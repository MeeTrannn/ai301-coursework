# Plan: issue #37, request bodies for `POST /profiles` and `POST /reviews`

- Issue: codepath/pathreview-ai301-fa26-s3#37, "API reference doc is
  missing the `POST /profiles` request body schema"
- Repro this plan builds on: my repro comment on #37 (posted as
  MeeTrannn), run at commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`
- Build branch: `fix/37-api-request-body-schemas` on
  `MeeTrannn/pathreview-ai301-fa26-s3`

## What the issue asks

Add request bodies to `docs/API.md` for `POST /profiles` and
`POST /reviews`, with a description and an example value for each field.
The issue also notes that `POST /profiles` takes multipart form data, not
JSON.

## Repro evidence I rely on

All of it is quoted from my repro comment. It was run against a live
server: macOS 26.2 (25C56), Python 3.11.4 venv, PostgreSQL 16 and Redis 7
from `docker-compose.yml`, `LLM_PROVIDER=mock`, authenticated as the
seeded `user1@example.com`.

**E1. The doc has one line per endpoint and nothing else.**

```
$ sed -n '18p;24p' docs/API.md
`POST /profiles` — Create a profile with resume and GitHub username.
`POST /reviews` — Request a new portfolio review for a profile.
```

**E2. A JSON body to `POST /profiles` is accepted with a 200, and every field is dropped.**

```
$ curl -s -X POST http://127.0.0.1:8000/profiles \
    -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
    -d '{"github_username":"meetrannn","portfolio_url":"https://example.com"}' \
    -w "\nHTTP %{http_code}\n"
{"id":"2a9c4e52-...","github_username":null,"portfolio_url":null,...,"resume_filename":null}
HTTP 200
```

**E3. The same data as multipart form data is stored correctly.**

```
$ curl -s -X POST http://127.0.0.1:8000/profiles \
    -H "Authorization: Bearer $TOK" \
    -F "github_username=meetrannn" -F "portfolio_url=https://example.com" \
    -F "resume_file=@resume.md;type=text/markdown" \
    -w "\nHTTP %{http_code}\n"
{"id":"ac1f5845-...","github_username":"meetrannn","portfolio_url":"https://example.com",...,"resume_filename":"resume.md"}
HTTP 200
```

**E4. `POST /reviews` takes a JSON body with one required `profile_id`.**

```
$ curl -s -X POST http://127.0.0.1:8000/reviews \
    -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
    -d "{\"profile_id\":\"$PID\"}" -w "\nHTTP %{http_code}\n"
{"id":"2911887d-...","profile_id":"d1194941-...","status":"pending",...}
HTTP 200
```

The same request as form data gets a 422 (`"Input should be a valid
dictionary or object to extract fields from"`).

**E5. Accepted resume types are PDF, Markdown, and plain text.** The
rejection message names only two of them.

```
$ curl ... -F "github_username=txtcase2" -F "resume_file=@resume.txt;type=text/plain"
{..."resume_filename":"resume.txt"}
HTTP 200

$ curl ... -F "resume_file=@resume.csv;type=text/csv"
{"detail":"Resume must be a PDF or Markdown file"}
HTTP 422
```

**E6. An over-long `github_username` returns 500, not 422.**

```
$ curl ... -F "github_username=$LONG"      # 300 characters
{"detail":"Failed to create profile"}
HTTP 500
```

## Diagnosis

The defect is in `docs/API.md` only. Lines 18 and 24 give each endpoint
one descriptive line, with no content type, field list, or example (E1).
That gap matters more for `POST /profiles` than the issue suggests. The
endpoint takes multipart form data (E3), but a reader who guesses JSON
gets no error at all. The request returns 200 and the profile is stored
empty (E2). `POST /reviews` takes JSON (E4) and rejects the wrong
content type with a 422, so that guess is at least visible.

The handlers behave consistently with their own signatures. Both E2 and
E3 match what `api/routes/profiles.py:23-27` declares, and E4 matches
`ReviewCreate` in `api/schemas/review.py:14-15`. So the fix is to
document them, not change them.

Why the JSON body is silently ignored is a hypothesis I haven't traced:
the `Form`/`File` parameters all default to `None`, so there is nothing
to reject. The plan doesn't depend on that answer, because the doc fix
is the same either way.

## Scope

**In scope: one file, `docs/API.md`.**

- Under `POST /profiles`:
  - Content type `multipart/form-data`.
  - A field table: `github_username` (string, optional),
    `portfolio_url` (string, optional), and `resume_file` (file,
    optional; `application/pdf`, `text/markdown`, or `text/plain`).
  - Example values for each field.
  - An example `curl -F` request.
  - The 422 for an unsupported resume type.
  - One sentence saying the body must be form data, because JSON
    fields are not read (E2).
- Under `POST /reviews`:
  - Content type `application/json`.
  - A field table: `profile_id` (UUID, required).
  - An example JSON body.

The fields come from the handler parameters, not from `ProfileCreate`.
`ProfileCreate` is built inside the handler and has no `resume_file`.

**Out of scope:**

- Any change to `api/routes/profiles.py` or the schemas. That includes:
  - The 200-on-JSON behavior (E2).
  - The "PDF or Markdown" wording in the docstring (line 33) and the 422
    message (line 52), which omit plain text (E5).
  - The over-long `github_username` that returns 500 (E6).

  These are runtime behaviors, not doc gaps. My repro comment asked
  whether they should be filed separately, and that question is still
  open.
- The 255-character limits. I'm leaving them out of the doc for now.
  Today they only surface as a generic 500 (E6), so listing them as
  validation rules would promise a 422 that clients won't get.
- Other undocumented endpoints in `docs/API.md`, such as
  `POST /auth/login`'s OAuth2 form fields.
- The `OPENROUTER_API_KEY` mismatch in `docs/SETUP.md`.
- Response bodies for these two endpoints. The issue asks only for
  request bodies.

## Files I'll touch

- `docs/API.md`: the only file changed.
- Read only, to check the doc against the code:
  - `api/routes/profiles.py` (lines 23-27 and the type check at
    line 44)
  - `api/routes/reviews.py` (lines 22-24)
  - `api/schemas/review.py` (lines 14-15)

## Approach

1. Branch `fix/37-api-request-body-schemas` from `main` at `2f4e82f`.
2. In `docs/API.md`, put a short block directly under line 18
   (`POST /profiles`) with these parts, in order:
   - the content type;
   - the field table, giving each field's type, whether it's required,
     a description, and an example value;
   - the form-data sentence;
   - the accepted resume types and the 422;
   - a `curl -F` example built from the E3 request.
3. Under line 24 (`POST /reviews`), add the content type, the
   one-field table, and an example JSON body built from the E4 request.
4. Leave every other line of `docs/API.md` alone. The one-line endpoint
   list format stays as it is for the endpoints this issue doesn't name.
5. Check the field names in the doc character by character against
   `profiles.py:25-27` and `review.py:15`. Check the MIME list against
   `profiles.py:44`.
6. Commit as `docs(api): document request bodies for POST /profiles and
   POST /reviews`, with `Fixes #37` in the footer.

## Test plan

This is a docs-only change, so the test is my repro re-run, with the
doc now as the source of the requests. Same environment as the repro,
on the fix branch.

1. **The doc shows the bodies.** Run `sed -n '16,60p' docs/API.md`.
   - Before (E1): one line per endpoint.
   - After: under `POST /profiles`, the text `multipart/form-data` and
     the three field names `github_username`, `portfolio_url`,
     `resume_file`. Under `POST /reviews`, the text `application/json`
     and `profile_id`.
2. **The documented field names match the code.** Compare against
   `sed -n '25,27p' api/routes/profiles.py` and
   `sed -n '14,15p' api/schemas/review.py`. Expect every field the code
   declares to appear in the doc, and no field in the doc that the code
   lacks.
3. **The profiles example works as written.** Paste the `POST /profiles`
   example from the new doc, substituting only `$TOK`. Expect `HTTP 200`
   with `github_username` and `portfolio_url` echoed back non-null, plus
   `resume_filename` set, as in E3.
4. **The reviews example works as written.** Paste the `POST /reviews`
   example from the new doc, substituting only `$TOK` and `$PID`. Expect
   `HTTP 200` with `"status":"pending"`, as in E4.
5. **The documented 422 is real.** Re-run the `resume.csv` case from E5.
   Expect `HTTP 422`, matching what the doc says about unsupported types.
6. **Unchanged behavior stays unchanged.** Re-run E2 (the JSON body).
   Expect it still to return `HTTP 200` with null fields. This change
   does not alter the handler. The doc now tells readers not to send
   JSON, so this result is correct here and should not be read as a
   fix.
7. **CI-equivalent checks.** Run `make check` and `make test-unit`.
   Expect them to pass exactly as on `main`, because no Python file
   changes.

## Risks and unknowns

- **Open questions from my repro comment.** I asked two things that
  nobody has answered yet:
  - Should the plain-text mismatch and the 500 be filed separately?
  - Should the doc mention the character limits?

  The plan assumes "file separately" and "leave limits out". If a
  maintainer answers differently, the doc may need a sentence added or
  removed. I'll record that under Deviations.
- **Documenting the 422 message.** The message a rejected upload gets
  says "PDF or Markdown" (E5). The doc will list all three accepted
  types, as the code at line 44 does, so a reader will see the doc
  disagree with the error text. I'll quote the message as it is rather
  than correct it, since the message is out of scope.
- **PDF uploads were never tested.** My repro skipped them. The doc will
  list `application/pdf` because line 44 accepts it, but I haven't
  observed a PDF upload succeed. The PDF branch uses `PyPDF2`, so if that
  import or parse fails, a PDF would get the "Failed to parse PDF
  resume" 422. If I test a PDF during the build, I'll record the result
  under Deviations.
- **Possible callers relying on the JSON behavior.** I haven't checked
  whether `frontend/` or anything else depends on E2's 200-on-JSON. This
  change doesn't alter that behavior, so it can't break such a caller,
  but I can't say none exists.
- **Shared issue.** Classmates bbdevelops, oherna25, and vanthuynh have
  also posted plans or reviews on #37. Under the house rules this
  doesn't block mine. oherna25 suggested documenting the MIME types and
  the 422, which the plan does. They also said no server run was needed.
  My repro shows the server run is what found E2, so steps 3-6 of the
  test plan keep it.
- **Branch name.** My claim comment named the branch
  `docs/37-api-request-body-schemas`, the type CONTRIBUTING.md suggests
  for docs. The course house rule requires `fix/<issue>-<slug>`, so I'm
  using `fix/37-api-request-body-schemas`. The comment says so, so the
  thread isn't left pointing at the old name.

## Deviations

None that change the posted plan. The build kept the same scope (only
`docs/API.md` changed), the same field list, and the same out-of-scope
list as the comment on #37, so I am not posting a follow-up. As of the
build, nobody had answered my two open questions, so the defaults
stand: the bugs get filed separately, and the doc leaves out the
character limits.

Three small differences in how the work was carried out, none of which
changes what the plan promised:

- **Placeholder names.** The doc examples use `$TOKEN` where the test
  plan said `$TOK`. The `POST /reviews` example uses a literal UUID
  (`d1194941-...`, the profile ID from my repro) instead of `$PID`, so
  a reader sees a real value. For test 4 I swapped that UUID for a
  profile ID created in the same run. Otherwise the example ran
  verbatim.
- **Blank lines around the new blocks.** The new blocks sit between
  lines that used to be adjacent, so `GET /profiles/{profile_id}` and
  `GET /reviews/{review_id}` now start new paragraphs. Their text is
  unchanged.
- **Health endpoint.** `/health` reported Postgres and Redis as
  unhealthy during the run, even though both were serving requests (the
  seed script read users, and every request below hit the database).
  CONTRIBUTING lists `api/routes/health.py` as a seeded bug (#62). This
  is not part of this issue and did not affect any result.

Test results, on `fix/37-api-request-body-schemas`, with the same
environment as the repro (macOS 26.2, Python 3.11.4, compose Postgres
16 and Redis 7, `LLM_PROVIDER=mock`, seeded `user1@example.com`):

1. `docs/API.md` now shows `multipart/form-data` and `github_username`,
   `portfolio_url`, `resume_file` under `POST /profiles`, and
   `application/json` and `profile_id` under `POST /reviews`.
2. The documented fields match `profiles.py:25-27` and `review.py:15`
   exactly, and the MIME list matches `profiles.py:44`.
3. The `POST /profiles` example, extracted from the doc and run as
   written:

   ```
   {"id":"abc3f31f-...","github_username":"octocat","portfolio_url":"https://example.com",...,"resume_filename":"resume.md"}
   HTTP 200
   ```
4. The `POST /reviews` example:

   ```
   {"id":"fcbb5444-...","profile_id":"ba78d7f0-...","status":"pending",...}
   HTTP 200
   ```
5. A `.csv` upload returned
   `{"detail":"Resume must be a PDF or Markdown file"}` with `HTTP 422`,
   as the doc says.
6. A JSON body still returned `"github_username":null,
   "portfolio_url":null` with `HTTP 200`. That's unchanged, as expected,
   because the handler wasn't touched.
7. `make check` passed: ruff, black (no files changed), and mypy.
   `make test-unit` gave 375 passed and 53 xfailed.

Still not verified: a PDF upload, and whether `frontend/` relies on the
JSON behavior.
