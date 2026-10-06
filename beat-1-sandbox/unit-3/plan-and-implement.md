# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

MeeTrannn

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/37#issuecomment-6009624852

Text as posted:

> Plan for this one, built from my repro above (commit `2f4e82f5`, live
> server, Python 3.11.4, macOS 26.2).
>
> **What's wrong.** `docs/API.md` gives each endpoint one line and nothing
> more:
>
> ```
> $ sed -n '18p;24p' docs/API.md
> `POST /profiles` — Create a profile with resume and GitHub username.
> `POST /reviews` — Request a new portfolio review for a profile.
> ```
>
> For `POST /profiles` that gap hides a silent failure. Case 1 of my repro
> sent the fields as JSON:
>
> ```
> $ curl -s -X POST http://127.0.0.1:8000/profiles \
>     -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
>     -d '{"github_username":"meetrannn","portfolio_url":"https://example.com"}' \
>     -w "\nHTTP %{http_code}\n"
> {"id":"2a9c4e52-b4d0-4a95-9b58-8eeea22d5777","user_id":"2a93471c-1b8b-4d40-b5fb-a9818e3cba70","github_username":null,"portfolio_url":null,"created_at":"2026-09-30T13:20:18.086742Z","resume_filename":null}
> HTTP 200
> ```
>
> The profile was created empty. The same fields sent as multipart form
> data (case 2) were stored correctly. The handlers
> match their own signatures (`api/routes/profiles.py:23-27`, `ReviewCreate`
> at `api/schemas/review.py:14`), so I'm treating this as a doc fix, not a
> code fix.
>
> **Change.** One file, `docs/API.md`. Under `POST /profiles`, I'll add:
> - the content type, `multipart/form-data`;
> - a field table: `github_username` and `portfolio_url` (strings,
>   optional), and `resume_file` (file, optional; `application/pdf`,
>   `text/markdown`, or `text/plain`, per the check at line 44);
> - an example value for each field;
> - the 422 for unsupported types;
> - one sentence saying JSON fields are not read;
> - a `curl -F` example.
>
> Under `POST /reviews`: `application/json`, `profile_id` (UUID, required),
> and an example body. The fields come from the handler parameters, not
> `ProfileCreate`, which has no `resume_file`.
>
> **Out of scope:**
> - Any handler or schema change, including the 200-on-JSON behavior, the
>   "PDF or Markdown" message that omits plain text, and the 500 on an
>   over-long `github_username`.
> - The 255-character limits.
> - Other endpoints, `POST /auth/login` included.
>
> @oherna25: your note asked for the MIME types and the 422, and both are
> in. I'm keeping the server run in the test plan, though, because a
> source read alone wouldn't have caught case 1.
>
> **Test.** I'll re-run my repro on the fix branch:
> 1. `sed -n '16,60p' docs/API.md` should show both content types and all
>    four field names, where today it shows none.
> 2. The `POST /profiles` and `POST /reviews` examples, pasted from the new
>    doc with only `$TOK` and `$PID` filled in, should return `HTTP 200`.
>    The profile should come back with non-null fields, and the review with
>    `"status":"pending"`.
> 3. The `.csv` upload should still return `HTTP 422`.
> 4. `make check` and `make test-unit` should pass as they do on `main`.
>
> Case 1 will still return 200, because this change doesn't touch the
> handler.
>
> **Not verified:** PDF uploads (I haven't run one), and whether anything in
> `frontend/` relies on the JSON behavior. Building on
> `fix/37-api-request-body-schemas`, not the `docs/` branch name from my
> claim, to follow the course branch rule.
>
> Two questions from my repro are still open, and they decide two lines of
> the doc. Should the plain-text mismatch and the 500 be filed as their own
> issues? And should the doc mention the 255-character limits? Until I hear
> otherwise, I'll treat it as "file separately, limits left out".

---

## Your branch

**Branch**

`fix/37-api-request-body-schemas`

It's on my fork at https://github.com/MeeTrannn/pathreview-ai301-fa26-s3/tree/fix/37-api-request-body-schemas, and the issue number 37 is the issue I claimed (codepath/pathreview-ai301-fa26-s3#37). The commit is `e40ad04` `docs(api): document request bodies for POST /profiles and POST /reviews`, and it changes only `docs/API.md` (+36 lines).

**Evidence**

My unit 2 repro sent requests to a live server and read the real `docs/API.md`, so it runs against the real change as-is. No stand-in check was needed. Both runs used the same environment: macOS 26.2, Python 3.11.4 venv, Postgres 16 and Redis 7 from `docker-compose.yml`, `.env` copied from `.env.example` (`LLM_PROVIDER=mock`), and the seeded `user1@example.com`.

### Before: my unit 2 repro, as posted on #37 at `2f4e82f`

Source: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/37#issuecomment-5905615109

> Reproduced, and the doc gap has a sharper consequence than I expected when I
> claimed this: sending a JSON body to `POST /profiles` does not fail. It
> returns `200 OK` and silently discards every field.
>
> **Environment**
>
> - macOS 26.2 (build 25C56), Apple Silicon
> - Python 3.11.4 in a venv (the repo requires `>=3.11`; my system Python is
>   3.14, so I created the venv with 3.11 explicitly)
> - PostgreSQL 16 and Redis 7 from the repo's `docker-compose.yml`, `.env`
>   copied from `.env.example` unmodified (`LLM_PROVIDER=mock`)
> - Code state: my fork `MeeTrannn/pathreview-ai301-fa26-s3` checked out at
>   commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`, with no local changes
>   relative to this repo:
>
> ```
> $ git rev-parse HEAD
> 2f4e82f52efbcfcc57d65b3fa5348672163ca088
> $ git rev-list --left-right --count upstream/main...HEAD
> 0	0
> ```
>
> All line numbers below are that commit's.
>
> **Setup** — followed `docs/SETUP.md` steps 1-4, with these deviations, none
> of which touch the endpoints under test: I created the venv with Python 3.11
> rather than the default `python`; I skipped `make setup`'s `npm install` and
> `pre-commit install` (frontend and hooks are not involved); and the
> `vector-db` container exits on startup in my environment, which neither
> endpoint uses. `alembic upgrade head` and `scripts/seed_db.py` both
> succeeded, and I authenticated as the seeded `user1@example.com`.
>
> One note on `docs/SETUP.md` step 2, which I hit on the way in: it says to set
> `OPENROUTER_API_KEY`, but `.env.example` has no such variable — it ships
> `LLM_PROVIDER=mock` and `OPENAI_API_KEY`, and the mock default needs no key.
> Not this issue; mentioning it in case it is worth its own.
>
> **Steps to reproduce**
>
> ```
> git clone https://github.com/MeeTrannn/pathreview-ai301-fa26-s3.git
> cd pathreview-ai301-fa26-s3
> git checkout 2f4e82f52efbcfcc57d65b3fa5348672163ca088
> cp .env.example .env
> docker compose up -d && sleep 15
> python3.11 -m venv .venv && .venv/bin/pip install -e ".[dev]"
> set -a && . ./.env && set +a
> .venv/bin/alembic upgrade head && .venv/bin/python scripts/seed_db.py
> .venv/bin/uvicorn api.main:app --host 127.0.0.1 --port 8000 &
>
> TOK=$(curl -s -X POST http://127.0.0.1:8000/auth/login \
>   -d "username=user1@example.com&password=password1" \
>   | python3 -c "import sys,json;print(json.load(sys.stdin)['access_token'])")
>
> # the three upload fixtures the curls below use
> printf '# Resume\n\nExperienced data engineer.\n' > resume.md
> printf 'Plain text resume.\n' > resume.txt
> printf 'not a resume\n' > resume.csv
>
> # a profile id for the POST /reviews cases below
> PID=$(curl -s -X POST http://127.0.0.1:8000/profiles \
>   -H "Authorization: Bearer $TOK" -F "github_username=revtest" \
>   | python3 -c "import sys,json;print(json.load(sys.stdin)['id'])")
>
> $ echo $PID
> d1194941-176d-4de7-8b3f-424fa7cc6ec7
> ```
>
> **What the doc says**
>
> ```
> $ sed -n '18p;24p' docs/API.md
> `POST /profiles` — Create a profile with resume and GitHub username.
> `POST /reviews` — Request a new portfolio review for a profile.
> ```
>
> No content type, no fields, no example, for either endpoint.
>
> **Observed output**
>
> 1. A JSON body — the shape a reader would assume from `ProfileCreate` in
>    `api/schemas/profile.py`, since the doc does not say otherwise:
>
> ```
> $ curl -s -X POST http://127.0.0.1:8000/profiles \
>     -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
>     -d '{"github_username":"meetrannn","portfolio_url":"https://example.com"}' \
>     -w "\nHTTP %{http_code}\n"
> {"id":"2a9c4e52-b4d0-4a95-9b58-8eeea22d5777","user_id":"2a93471c-1b8b-4d40-b5fb-a9818e3cba70","github_username":null,"portfolio_url":null,"created_at":"2026-09-30T13:20:18.086742Z","resume_filename":null}
> HTTP 200
> ```
>
> `200 OK`, and `github_username` and `portfolio_url` came back `null`. The
> profile was created empty and the submitted values were dropped without any
> error.
>
> 2. The same data as multipart form data, which is what the handler declares
>    at `api/routes/profiles.py:23`:
>
> ```
> $ curl -s -X POST http://127.0.0.1:8000/profiles \
>     -H "Authorization: Bearer $TOK" \
>     -F "github_username=meetrannn" -F "portfolio_url=https://example.com" \
>     -F "resume_file=@resume.md;type=text/markdown" \
>     -w "\nHTTP %{http_code}\n"
> {"id":"ac1f5845-5f7d-4e05-9954-841a892de454","user_id":"2a93471c-1b8b-4d40-b5fb-a9818e3cba70","github_username":"meetrannn","portfolio_url":"https://example.com","created_at":"2026-09-30T13:20:18.113756Z","resume_filename":"resume.md"}
> HTTP 200
> ```
>
> 3. `POST /reviews` with the `ReviewCreate` JSON body — one required
>    `profile_id`, per `api/schemas/review.py:14`:
>
> ```
> $ curl -s -X POST http://127.0.0.1:8000/reviews \
>     -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
>     -d "{\"profile_id\":\"$PID\"}" -w "\nHTTP %{http_code}\n"
> {"id":"2911887d-6b52-4424-9868-423a3727fb12","profile_id":"d1194941-176d-4de7-8b3f-424fa7cc6ec7","status":"pending","sections":null,"overall_score":null,"error_message":null,"created_at":"2026-09-30T13:20:30.742777Z","updated_at":"2026-09-30T13:20:30.742778Z"}
> HTTP 200
> ```
>
> Worth noting the contrast with case 1. The same wrong-content-type mistake on
> `POST /reviews` is rejected, not silently accepted:
>
> ```
> $ curl -s -X POST http://127.0.0.1:8000/reviews \
>     -H "Authorization: Bearer $TOK" -F "profile_id=$PID" \
>     -w "\nHTTP %{http_code}\n"
> {"detail":[{"type":"model_attributes_type","loc":["body"],"msg":"Input should be a valid dictionary or object to extract fields from","input":"--------------------------1WYoiFG9CaL3VIOKIYRBoY\r\nContent-Disposition: form-data; name=\"profile_id\"\r\n
> HTTP 422
> ```
>
> So one endpoint tells you and the other does not. My reading of why is that
> `POST /reviews` declares a Pydantic model as its body, so FastAPI validates
> at the request boundary, whereas `POST /profiles` declares `Form`/`File`
> parameters that all default to `None` and so has nothing to reject. That is a
> hypothesis from reading the two signatures — I have not traced FastAPI's
> dependency resolution to confirm it.
>
> **Expected behavior**: `docs/API.md` documents each POST endpoint's content
> type, fields, and an example body.
>
> **Actual behavior**: it documents none of them, as the `sed` output above
> shows. The two endpoints take different content types, and for
> `POST /profiles` guessing wrong is not a visible error — case 1 returns
> `200` with the data discarded.
>
> **The two answers I promised in my claim**
>
> In my claim comment I said I would check the accepted resume types and what
> an over-long `github_username` returns. Both are now run, not read:
>
> Resume types — the code's three-type list is the truth, and both
> human-readable messages are wrong. `text/plain` is accepted:
>
> ```
> $ curl -s -X POST .../profiles -H "Authorization: Bearer $TOK" \
>     -F "github_username=txtcase2" -F "resume_file=@resume.txt;type=text/plain" \
>     -w "\nHTTP %{http_code}\n"
> {"id":"19e94d18-8829-4381-98b3-5d35ad3f33b5","user_id":"2a93471c-1b8b-4d40-b5fb-a9818e3cba70","github_username":"txtcase2","portfolio_url":null,"created_at":"2026-09-30T13:26:25.377666Z","resume_filename":"resume.txt"}
> HTTP 200
>
> $ curl -s -X POST .../profiles -H "Authorization: Bearer $TOK" \
>     -F "resume_file=@resume.csv;type=text/csv" -w "\nHTTP %{http_code}\n"
> {"detail":"Resume must be a PDF or Markdown file"}
> HTTP 422
> ```
>
> The check at `api/routes/profiles.py:44` lists `application/pdf`,
> `text/markdown` and `text/plain`, while the handler docstring (line 33) and
> the rejection message (line 52) both say "PDF or Markdown". A `.txt` file
> uploads fine; the error text a user sees when rejected still omits it.
>
> Over-long `github_username` — returns `500`, which confirms the reading in my
> claim comment rather than correcting it. I said there that a
> `ValidationError` from `ProfileCreate` would be caught by the broad
> `except Exception` and surface as a `500` rather than a `422`, and flagged
> that as a code reading I had not run. Now run:
>
> ```
> $ LONG=$(python3 -c "print('a'*300)")
> $ curl -s -X POST .../profiles -H "Authorization: Bearer $TOK" \
>     -F "github_username=$LONG" -w "\nHTTP %{http_code}\n"
> {"detail":"Failed to create profile"}
> HTTP 500
> ```
>
> Server log for that request:
>
> ```
> [error] profile_creation_error  error="1 validation error for ProfileCreate
> github_username
>   String should have at most 255 characters [type=string_too_long, input_value='aaaa...aaa', input_type=str]"
> ```
>
> The `ProfileCreate(...)` construction at line 78 sits inside the handler's
> `try`, so the `ValidationError` is caught by the broad `except Exception` at
> line 101 and returned as a generic `500`. Had that model been the request
> body, FastAPI would have validated it at the boundary and returned a `422`
> naming the field and the limit.
>
> **What I plan to document, and two questions**
>
> For `POST /profiles`: `multipart/form-data`, with `github_username`
> (string, optional), `portfolio_url` (string, optional), and `resume_file`
> (file, optional — PDF, Markdown or plain text). For `POST /reviews`:
> `application/json`, `{"profile_id": "<uuid>"}`, required. I plan to document
> the handler's actual parameters rather than `ProfileCreate`, which is an
> internal object and does not include `resume_file`.
>
> 1. The `text/plain` mismatch and the `500` are behaviors, not doc gaps. I can
>    document what the API does today, but I would rather not write `500` into
>    a reference as intended behavior. Do you want those filed as separate
>    issues, or described as-is in the doc for now?
> 2. Should the doc mention the 255/500 character limits at all? They are real
>    but only surface as a `500`, so documenting them as validation rules would
>    overstate what a client can rely on.
>
> **What I did not verify**: only the two endpoints this issue names. I did not
> audit the rest of `docs/API.md`, though I noticed while authenticating that
> `POST /auth/login` takes OAuth2 form fields (`username`, `password`) and is
> also undocumented — a separate gap I am not claiming here. I did not test PDF
> uploads, the `frontend/` client, or whether any existing caller relies on the
> JSON-body behavior in case 1. All results are from a single run on one
> machine.

### After: the same repro steps re-run on `fix/37-api-request-body-schemas`

These are the same commands, run as a script that prints each command before its output. The only change is that the `.../profiles` shorthand in the posted version is spelled out as the full URL. I recorded it at `c35d2d0`, which is the same tree as the pushed `e40ad04`; only the commit author changed.

The repro's exact command, `sed -n '18p;24p' docs/API.md`, still runs. But the new block pushes the `POST /reviews` line down, so line 24 is now the profiles table header. The transcript therefore also prints both endpoints by heading.

````text
## Code state
$ git rev-parse --abbrev-ref HEAD
fix/37-api-request-body-schemas

$ git log --oneline -1
c35d2d0 docs(api): document request bodies for POST /profiles and POST /reviews

$ git rev-list --left-right --count upstream/main...HEAD
0	1

$ git diff --stat upstream/main...HEAD
 docs/API.md | 36 ++++++++++++++++++++++++++++++++++++
 1 file changed, 36 insertions(+)

## Setup (same as the repro)
$ echo $PID
b15c46e6-de5c-4334-a4cf-768cda59d22a

## What the doc says
# The repro's command, verbatim (line numbers are the old file's):
$ sed -n '18p;24p' docs/API.md
`POST /profiles` — Create a profile with resume and GitHub username.
| Field | Type | Required | Description | Example |

# The same two endpoints, found by heading instead of line number:
$ sed -n '/^### Profiles/,/^## Interactive Docs/p' docs/API.md
### Profiles

`POST /profiles` — Create a profile with resume and GitHub username.

Request body: `multipart/form-data`. Send the fields as form data, not
JSON: a JSON body is not read, and the profile is created with every
field empty.

| Field | Type | Required | Description | Example |
|---|---|---|---|---|
| `github_username` | string | No | GitHub username to analyze. | `octocat` |
| `portfolio_url` | string | No | URL of a portfolio site. | `https://example.com` |
| `resume_file` | file | No | Resume upload. Accepted types: `application/pdf`, `text/markdown`, `text/plain`. | `resume.md` |

An upload of any other type returns `422` with
`{"detail": "Resume must be a PDF or Markdown file"}`.

```bash
curl -X POST http://localhost:8000/profiles \
  -H "Authorization: Bearer $TOKEN" \
  -F "github_username=octocat" \
  -F "portfolio_url=https://example.com" \
  -F "resume_file=@resume.md;type=text/markdown"
```

`GET /profiles/{profile_id}` — Retrieve a profile.
`DELETE /profiles/{profile_id}` — Delete a profile and associated data.

### Reviews

`POST /reviews` — Request a new portfolio review for a profile.

Request body: `application/json`.

| Field | Type | Required | Description | Example |
|---|---|---|---|---|
| `profile_id` | UUID | Yes | ID of the profile to review, as returned by `POST /profiles`. | `d1194941-176d-4de7-8b3f-424fa7cc6ec7` |

```bash
curl -X POST http://localhost:8000/reviews \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"profile_id": "d1194941-176d-4de7-8b3f-424fa7cc6ec7"}'
```

`GET /reviews/{review_id}` — Retrieve a completed review.
`GET /reviews` — List reviews for the authenticated user (paginated).

## Interactive Docs

## Case 1: JSON body to POST /profiles
$ curl -s -X POST http://127.0.0.1:8000/profiles \
    -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
    -d '{"github_username":"meetrannn","portfolio_url":"https://example.com"}' \
    -w "\nHTTP %{http_code}\n"
{"id":"921e6d19-ee7f-4b59-8df1-d58999dfc957","user_id":"f4a4d39b-0ebe-400a-ae96-cf7183124d7a","github_username":null,"portfolio_url":null,"created_at":"2026-10-06T12:14:44.829094Z","resume_filename":null}
HTTP 200

## Case 2: multipart form data to POST /profiles
$ curl -s -X POST http://127.0.0.1:8000/profiles \
    -H "Authorization: Bearer $TOK" \
    -F "github_username=meetrannn" -F "portfolio_url=https://example.com" \
    -F "resume_file=@resume.md;type=text/markdown" \
    -w "\nHTTP %{http_code}\n"
{"id":"5297f12c-43ca-4f90-8c79-7c6b2289402c","user_id":"f4a4d39b-0ebe-400a-ae96-cf7183124d7a","github_username":"meetrannn","portfolio_url":"https://example.com","created_at":"2026-10-06T12:14:44.845863Z","resume_filename":"resume.md"}
HTTP 200

## Case 3: POST /reviews with the ReviewCreate JSON body
$ curl -s -X POST http://127.0.0.1:8000/reviews \
    -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
    -d "{\"profile_id\":\"$PID\"}" -w "\nHTTP %{http_code}\n"
{"id":"bb1cb344-4524-461c-ba63-14a6f86beadf","profile_id":"b15c46e6-de5c-4334-a4cf-768cda59d22a","status":"pending","sections":null,"overall_score":null,"error_message":null,"created_at":"2026-10-06T12:14:44.858797Z","updated_at":"2026-10-06T12:14:44.858798Z"}
HTTP 200

## Case 3 contrast: POST /reviews with form data
$ curl -s -X POST http://127.0.0.1:8000/reviews \
    -H "Authorization: Bearer $TOK" -F "profile_id=$PID" \
    -w "\nHTTP %{http_code}\n" | cut -c1-260
{"detail":[{"type":"model_attributes_type","loc":["body"],"msg":"Input should be a valid dictionary or object to extract fields from","input":"--------------------------Dl4yyvSSGxXtT8CQ4u99Wu\r\nContent-Disposition: form-data; name=\"profile_id\"\r\n\r\nb15c46
HTTP 422

## Resume types
$ curl -s -X POST http://127.0.0.1:8000/profiles -H "Authorization: Bearer $TOK" \
    -F "github_username=txtcase2" -F "resume_file=@resume.txt;type=text/plain" \
    -w "\nHTTP %{http_code}\n"
{"id":"4fd0ddf2-e7bd-49de-b3fa-53c144047824","user_id":"f4a4d39b-0ebe-400a-ae96-cf7183124d7a","github_username":"txtcase2","portfolio_url":null,"created_at":"2026-10-06T12:14:44.886786Z","resume_filename":"resume.txt"}
HTTP 200

$ curl -s -X POST http://127.0.0.1:8000/profiles -H "Authorization: Bearer $TOK" \
    -F "resume_file=@resume.csv;type=text/csv" -w "\nHTTP %{http_code}\n"
{"detail":"Resume must be a PDF or Markdown file"}
HTTP 422

## Over-long github_username
$ LONG=$(python3 -c "print('a'*300)")

$ curl -s -X POST http://127.0.0.1:8000/profiles -H "Authorization: Bearer $TOK" \
    -F "github_username=$LONG" -w "\nHTTP %{http_code}\n"
{"detail":"Failed to create profile"}
HTTP 500

# Server log for that request:
2026-10-05 22:14:44 [error    ] profile_creation_error         error="1 validation error for ProfileCreate\ngithub_username\n  String should have at most 255 characters [type=string_too_long, input_value='aaaaaaaaaaaaaaaaaaaaaaaa...aaaaaaaaaaaaaaaaaaaaaaa', in
````

**What changed between before and after:**
- **The doc.** Before, each endpoint had one line and no request body. After, `POST /profiles` shows `multipart/form-data`, the three fields, the accepted resume types, the 422, and a `curl -F` example. `POST /reviews` shows `application/json`, `profile_id`, and a JSON example.
- **The API.** Every live case returns the same status and body shape before and after. That's expected, because the fix changes only the doc. Case 1 (a JSON body) still returns `200` with null fields. The doc now tells readers to send form data, and the runtime behavior stays out of scope, as the plan says.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. **Calibration-only partial run** of the 4 calib packages (`--include-calibration --only calib-01,calib-02,calib-03,calib-04`). All 4 agreed with the gold labels. Calibration packages are never scored, so the agreement line read:
   > agreement: 0/0 scored items
2. **First full run:**
   > agreement: 19/20 scored items  (bar: 18/20: PASS)

   The only miss was pkg-14 (`failed: executable_by_stranger`).
3. **Second full run, the committed `eval-run.txt`:**
   > agreement: 20/20 scored items  (bar: 18/20: PASS)

   Between runs 2 and 3, the rubric and the evidence guide did not change. Only `procedure.md` did: I added two live-mode rules that my live plan-check runs on #37 flagged as gaps (how to treat a peer's comment, and a house rule versus a repo convention). I re-ran so that the fingerprints in `eval-run.txt` match the files in `tools/plan-check/`.

**Package analysis**

**pkg-14** (zellij-org/zellij#5174, category `clear-accept`). The gold label is **accept**. My rubric said **reject** in run 2 and **accept** in run 3, and nothing in `executable_by_stranger`'s rubric row or the evidence guide changed between the two.

Both times, the deciding check was `executable_by_stranger`. Its pass condition requires that the plan "names where the change starts (file, function, or module) and what changes there". The plan's Files paragraph reads:

> Files: the client attach/reattach path in `zellij-server` (session connection handling) and `zellij-client`'s terminal query issuance; exact functions to be pinned in the PR after tracing the query issuance with debug logs, which I have working

In run 2, the grader fixed on the second half of that sentence:

> Plan states 'exact functions to be pinned in the PR after tracing the query issuance with debug logs' — the concrete file/function to start editing is not named, only a general area, leaving a real decision for build time.

In run 3, it weighed the first half and the Scope paragraph, which states the action ("consuming or draining pending OSC query responses in the client attach path ... before pane input is wired"):

> Names zellij-server's client/session connection handling and zellij-client's query issuance, and the concrete action (drain responses before wiring input).

So the package sits right on my check's line. The check accepts a module as the starting point, and this plan names modules and an action, but it defers the functions. My evidence guide lists "somewhere" and "whichever is easier" as unbuildable signals. "To be pinned after tracing" looks like those, but it's different: the author has the tracing working and has already chosen the approach. The gold label reads it as ready, and my rubric only agrees with it some of the time.

**Check rationale**

From `tools/plan-check/rubric.md`, exactly as it reads now:

| diagnosis_grounded | The plan's stated cause, read against every step of the repro-evidence block: observed and expected behavior, artifacts (logs, output, debug traces), and especially any control run, timing matrix, or isolating step that varies one factor. | The stated cause explains the behavior the repro evidence shows, and no step in the repro evidence rules it out. Fail if a control or isolating step shows the symptom without the blamed component, or shows the blamed component working correctly. Also fail if the cause explains a different behavior from the one reproduced, or the plan ignores the repro. The repro evidence outranks the thread: adopting a confident diagnosis from the thread does not pass if the repro contradicts it. | required |

**Why it reads this way.** My first draft of this row judged the cause against "the repro-evidence block's observed behavior, expected behavior, and artifacts". It passed when "no fact in the repro evidence contradicts it". I revised it before my first eval run, after reading the four calibration packages and the category descriptions in `gold-labels.json`.

calib-03 showed me the draft was too weak. It's a polished plan that adopts the thread's confident key-binding diagnosis. Its repro never contradicts that diagnosis directly, but its timing matrix includes a row "(no pager in the loop at all): 25.8 s wall time". A grader checking only for a direct contradiction could pass that plan. Three things got added:
- an explicit pointer to "any control run, timing matrix, or isolating step that varies one factor", because that is where wrong causes get ruled out;
- two concrete fail patterns: the symptom without the blamed component, and the blamed component working correctly;
- the sentence "The repro evidence outranks the thread". I added it because calib-03's wrong cause came from the thread, not from the author.

I rejected an alternative that would make the plan cite a specific repro step by number. A plan can be grounded without citing step numbers (calib-01's Cause line cites none, and it is a gold accept), and a check about citation format would grade the write-up's shape, not the diagnosis.

**Trade-offs**

The check that gives up the most is `executable_by_stranger`, quoted exactly:

| executable_by_stranger | The plan's approach and order of work, read with the repo-facts block (layout, build and test commands). | A contributor who has not read the thread could start the first step without asking the author anything: the plan names where the change starts (file, function, or module) and what changes there, and any open decision comes with how it will be settled. Fail if the first concrete action is missing or depends on something only the author knows. | required |

**What it costs: pkg-14, a case I accept it will sometimes miss.** Run 2 rejected that gold-accept package on this check, and run 3 accepted it, with the check unchanged. A plan that names the modules and the action but defers the exact functions sits on the boundary, and the grader can land on either side.

I did not loosen the check to make pkg-14 pass reliably. Doing so would mean accepting plans that leave the code site to be found at build time. That is exactly the pattern the `unbuildable` category is built on:
- pkg-17: "no chosen layer ('gocui? tcell? not sure')";
- pkg-18: "recover() 'somewhere' ... every real decision deferred to build time".

Wording loose enough to pass pkg-14 every time would risk passing pkg-18, a reject. With only 3 `unbuildable` packages, a flip there costs more than a miss in the 7-package `clear-accept` category.

I didn't run a `--only` canary for this, because I didn't change the check. The evidence that nothing else moved comes from comparing my two full runs. Every package other than pkg-14 got the same verdict both times, including all 3 `unbuildable` packages (rejected in both runs) and the other 6 `clear-accept` packages (accepted in both). Between runs 2 and 3, the only edits were two live-mode rules in `procedure.md`, which eval mode doesn't exercise. So I read pkg-14's flip as grader variance, not as an effect of those edits.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
