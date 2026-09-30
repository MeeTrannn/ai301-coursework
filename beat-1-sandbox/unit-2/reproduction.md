# Unit 2 — Claim and Reproduce

## Your identity upstream

### GitHub username

MyTran-GitHub

## Posted upstream

### Claim comment

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/37#issuecomment-5905202280

First time contributing here — I'd like to claim this one.

From the issue and a read of the source at `2f4e82f5`, the gap is in
`docs/API.md`: lines 18 and 24 give `POST /profiles` and `POST /reviews` a
one-line description each, with no content type, field list, or example body.
The two endpoints are shaped differently, which is what I expect to matter
when writing them up:

- `api/routes/profiles.py:23` declares `github_username` and `portfolio_url`
  as `Form()` parameters and `resume_file` as an `UploadFile` — multipart form
  data, and `resume_file` is a field that `ProfileCreate` in
  `api/schemas/profile.py` does not contain.
- `api/routes/reviews.py:24` takes `data: ReviewCreate`, which
  `api/schemas/review.py:14` defines as JSON with a single required
  `profile_id: UUID`.

What I plan to do next, in this order:

1. Verify both request bodies against a running instance rather than by
   reading alone: send an actual multipart request to `POST /profiles` and a
   JSON request to `POST /reviews`, and record what each accepts and returns.
2. Pin down the accepted resume types. The check at
   `api/routes/profiles.py:44` lists `application/pdf`, `text/markdown` and
   `text/plain`, while the handler docstring (line 33) and the 422 detail
   (line 52) both say "PDF or Markdown". I want to confirm by request which
   one is true before I document either.
3. Check what an over-length `github_username` actually returns.
   `ProfileCreate` sets `max_length=255`, but it is constructed at line 78
   inside the handler's `try`, so my reading is that a ValidationError there
   would be caught by the `except Exception` at line 101 and surface as a 500
   rather than a 422. That is a code reading, not a result — I will send the
   request and report what comes back.

I will post a reproduction report in this thread with the environment, the
commands, and the raw responses before I open a PR against
`docs/37-api-request-body-schemas`.

If 2 or 3 turn out to be bugs rather than documentation gaps, I will say so
here and ask whether you want them filed separately rather than folded into
the doc.

I have not looked at any endpoints in `docs/API.md` other than the two this
issue names.

### Reproduction comment

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/37#issuecomment-5905214062

Reproduced, and the doc gap has a sharper consequence than I expected when I
claimed this: sending a JSON body to `POST /profiles` does not fail. It
returns `200 OK` and silently discards every field.

**Environment**

- macOS 26.2 (build 25C56), Apple Silicon
- Python 3.11.4 in a venv (the repo requires `>=3.11`; my system Python is
  3.14, so I created the venv with 3.11 explicitly)
- PostgreSQL 16 and Redis 7 from the repo's `docker-compose.yml`, `.env`
  copied from `.env.example` unmodified (`LLM_PROVIDER=mock`)
- Code state: my fork `MeeTrannn/pathreview-ai301-fa26-s3` checked out at
  commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`, with no local changes
  relative to this repo:

```
$ git rev-parse HEAD
2f4e82f52efbcfcc57d65b3fa5348672163ca088
$ git rev-list --left-right --count upstream/main...HEAD
0	0
```

All line numbers below are that commit's.

**Setup** — followed `docs/SETUP.md` steps 1-4, with these deviations, none
of which touch the endpoints under test: I created the venv with Python 3.11
rather than the default `python`; I skipped `make setup`'s `npm install` and
`pre-commit install` (frontend and hooks are not involved); and the
`vector-db` container exits on startup in my environment, which neither
endpoint uses. `alembic upgrade head` and `scripts/seed_db.py` both
succeeded, and I authenticated as the seeded `user1@example.com`.

One note on `docs/SETUP.md` step 2, which I hit on the way in: it says to set
`OPENROUTER_API_KEY`, but `.env.example` has no such variable — it ships
`LLM_PROVIDER=mock` and `OPENAI_API_KEY`, and the mock default needs no key.
Not this issue; mentioning it in case it is worth its own.

**Steps to reproduce**

```
git clone https://github.com/MeeTrannn/pathreview-ai301-fa26-s3.git
cd pathreview-ai301-fa26-s3
git checkout 2f4e82f52efbcfcc57d65b3fa5348672163ca088
cp .env.example .env
docker compose up -d && sleep 15
python3.11 -m venv .venv && .venv/bin/pip install -e ".[dev]"
set -a && . ./.env && set +a
.venv/bin/alembic upgrade head && .venv/bin/python scripts/seed_db.py
.venv/bin/uvicorn api.main:app --host 127.0.0.1 --port 8000 &

TOK=$(curl -s -X POST http://127.0.0.1:8000/auth/login \
  -d "username=user1@example.com&password=password1" \
  | python3 -c "import sys,json;print(json.load(sys.stdin)['access_token'])")

# the three upload fixtures the curls below use
printf '# Resume\n\nExperienced data engineer.\n' > resume.md
printf 'Plain text resume.\n' > resume.txt
printf 'not a resume\n' > resume.csv

# a profile id for the POST /reviews cases below
PID=$(curl -s -X POST http://127.0.0.1:8000/profiles \
  -H "Authorization: Bearer $TOK" -F "github_username=revtest" \
  | python3 -c "import sys,json;print(json.load(sys.stdin)['id'])")

$ echo $PID
d1194941-176d-4de7-8b3f-424fa7cc6ec7
```

**What the doc says**

```
$ sed -n '18p;24p' docs/API.md
`POST /profiles` — Create a profile with resume and GitHub username.
`POST /reviews` — Request a new portfolio review for a profile.
```

No content type, no fields, no example, for either endpoint.

**Observed output**

1. A JSON body — the shape a reader would assume from `ProfileCreate` in
   `api/schemas/profile.py`, since the doc does not say otherwise:

```
$ curl -s -X POST http://127.0.0.1:8000/profiles \
    -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
    -d '{"github_username":"meetrannn","portfolio_url":"https://example.com"}' \
    -w "\nHTTP %{http_code}\n"
{"id":"2a9c4e52-b4d0-4a95-9b58-8eeea22d5777","user_id":"2a93471c-1b8b-4d40-b5fb-a9818e3cba70","github_username":null,"portfolio_url":null,"created_at":"2026-09-30T13:20:18.086742Z","resume_filename":null}
HTTP 200
```

`200 OK`, and `github_username` and `portfolio_url` came back `null`. The
profile was created empty and the submitted values were dropped without any
error.

2. The same data as multipart form data, which is what the handler declares
   at `api/routes/profiles.py:23`:

```
$ curl -s -X POST http://127.0.0.1:8000/profiles \
    -H "Authorization: Bearer $TOK" \
    -F "github_username=meetrannn" -F "portfolio_url=https://example.com" \
    -F "resume_file=@resume.md;type=text/markdown" \
    -w "\nHTTP %{http_code}\n"
{"id":"ac1f5845-5f7d-4e05-9954-841a892de454","user_id":"2a93471c-1b8b-4d40-b5fb-a9818e3cba70","github_username":"meetrannn","portfolio_url":"https://example.com","created_at":"2026-09-30T13:20:18.113756Z","resume_filename":"resume.md"}
HTTP 200
```

3. `POST /reviews` with the `ReviewCreate` JSON body — one required
   `profile_id`, per `api/schemas/review.py:14`:

```
$ curl -s -X POST http://127.0.0.1:8000/reviews \
    -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
    -d "{\"profile_id\":\"$PID\"}" -w "\nHTTP %{http_code}\n"
{"id":"2911887d-6b52-4424-9868-423a3727fb12","profile_id":"d1194941-176d-4de7-8b3f-424fa7cc6ec7","status":"pending","sections":null,"overall_score":null,"error_message":null,"created_at":"2026-09-30T13:20:30.742777Z","updated_at":"2026-09-30T13:20:30.742778Z"}
HTTP 200
```

Worth noting the contrast with case 1. The same wrong-content-type mistake on
`POST /reviews` is rejected, not silently accepted:

```
$ curl -s -X POST http://127.0.0.1:8000/reviews \
    -H "Authorization: Bearer $TOK" -F "profile_id=$PID" \
    -w "\nHTTP %{http_code}\n"
{"detail":[{"type":"model_attributes_type","loc":["body"],"msg":"Input should be a valid dictionary or object to extract fields from","input":"--------------------------1WYoiFG9CaL3VIOKIYRBoY\r\nContent-Disposition: form-data; name=\"profile_id\"\r\n
HTTP 422
```

So one endpoint tells you and the other does not. My reading of why is that
`POST /reviews` declares a Pydantic model as its body, so FastAPI validates
at the request boundary, whereas `POST /profiles` declares `Form`/`File`
parameters that all default to `None` and so has nothing to reject. That is a
hypothesis from reading the two signatures — I have not traced FastAPI's
dependency resolution to confirm it.

**Expected behavior**: `docs/API.md` documents each POST endpoint's content
type, fields, and an example body.

**Actual behavior**: it documents none of them, as the `sed` output above
shows. The two endpoints take different content types, and for
`POST /profiles` guessing wrong is not a visible error — case 1 returns
`200` with the data discarded.

**The two answers I promised in my claim**

In my claim comment I said I would check the accepted resume types and what
an over-long `github_username` returns. Both are now run, not read:

Resume types — the code's three-type list is the truth, and both
human-readable messages are wrong. `text/plain` is accepted:

```
$ curl -s -X POST .../profiles -H "Authorization: Bearer $TOK" \
    -F "github_username=txtcase2" -F "resume_file=@resume.txt;type=text/plain" \
    -w "\nHTTP %{http_code}\n"
{"id":"19e94d18-8829-4381-98b3-5d35ad3f33b5","user_id":"2a93471c-1b8b-4d40-b5fb-a9818e3cba70","github_username":"txtcase2","portfolio_url":null,"created_at":"2026-09-30T13:26:25.377666Z","resume_filename":"resume.txt"}
HTTP 200

$ curl -s -X POST .../profiles -H "Authorization: Bearer $TOK" \
    -F "resume_file=@resume.csv;type=text/csv" -w "\nHTTP %{http_code}\n"
{"detail":"Resume must be a PDF or Markdown file"}
HTTP 422
```

The check at `api/routes/profiles.py:44` lists `application/pdf`,
`text/markdown` and `text/plain`, while the handler docstring (line 33) and
the rejection message (line 52) both say "PDF or Markdown". A `.txt` file
uploads fine; the error text a user sees when rejected still omits it.

Over-long `github_username` — returns `500`, which confirms the reading in my
claim comment rather than correcting it. I said there that a
`ValidationError` from `ProfileCreate` would be caught by the broad
`except Exception` and surface as a `500` rather than a `422`, and flagged
that as a code reading I had not run. Now run:

```
$ LONG=$(python3 -c "print('a'*300)")
$ curl -s -X POST .../profiles -H "Authorization: Bearer $TOK" \
    -F "github_username=$LONG" -w "\nHTTP %{http_code}\n"
{"detail":"Failed to create profile"}
HTTP 500
```

Server log for that request:

```
[error] profile_creation_error  error="1 validation error for ProfileCreate
github_username
  String should have at most 255 characters [type=string_too_long, input_value='aaaa...aaa', input_type=str]"
```

The `ProfileCreate(...)` construction at line 78 sits inside the handler's
`try`, so the `ValidationError` is caught by the broad `except Exception` at
line 101 and returned as a generic `500`. Had that model been the request
body, FastAPI would have validated it at the boundary and returned a `422`
naming the field and the limit.

**What I plan to document, and two questions**

For `POST /profiles`: `multipart/form-data`, with `github_username`
(string, optional), `portfolio_url` (string, optional), and `resume_file`
(file, optional — PDF, Markdown or plain text). For `POST /reviews`:
`application/json`, `{"profile_id": "<uuid>"}`, required. I plan to document
the handler's actual parameters rather than `ProfileCreate`, which is an
internal object and does not include `resume_file`.

1. The `text/plain` mismatch and the `500` are behaviors, not doc gaps. I can
   document what the API does today, but I would rather not write `500` into
   a reference as intended behavior. Do you want those filed as separate
   issues, or described as-is in the doc for now?
2. Should the doc mention the 255/500 character limits at all? They are real
   but only surface as a `500`, so documenting them as validation rules would
   overstate what a client can rely on.

**What I did not verify**: only the two endpoints this issue names. I did not
audit the rest of `docs/API.md`, though I noticed while authenticating that
`POST /auth/login` takes OAuth2 form fields (`username`, `password`) and is
also undocumented — a separate gap I am not claiming here. I did not test PDF
uploads, the `frontend/` client, or whether any existing caller relies on the
JSON-body behavior in case 1. All results are from a single run on one
machine.

---

*Edited to correct the environment block. The first version of this comment
named a personal fork of a different upstream; I have re-run every case above
on my course fork at `2f4e82f5` and replaced all output and line numbers with
that run's. Every result was the same, but the environment record was wrong
and the line numbers were off by one in two places.*

## Eval iterations

### Run history

Four runs, in order:

1. **12/20** — full run, my first revised rubric. Below the bar, and the
   category floor was unmet: `clear-accept 0/8`.
2. **10/12** — partial run (`--only` on the eight disagreements plus four
   canaries and `calib-03`). Partial runs print no bar.
3. **11/11** — partial run (`--only` on the two remaining disagreements plus
   five canaries).
4. **19/20** — full confirming run, written with `--save-run eval-run.txt`.

The last score matches the committed record: `eval-run.txt` reads
`agreement: 19/20 scored items  (bar: 18/20: PASS)`, with
`categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 3/4`.

### Package analysis

**pkg-16.** My rubric graded it **accept**; the gold label is **reject**,
category `wrong-target`. It is the one disagreement in my final run.

The package reproduces a pandas `rename_axis` crash and does it cleanly: the
trigger is copied verbatim from the issue, the pasted traceback ends in
`ValueError: Length of new names must be 1, got 3`, and the environment block
names Ubuntu 22.04, Python 3.10.12 and a pinned pandas 1.5.3. Every check
passed on its own terms, so my verdict rule returned accept.

It read it that way because of a clause I had written into `env-recorded`
during the revise loop:

> A difference the report's own result overrides — it reproduced the failure
> anyway, on another OS or a later version — is not a fail, because
> reproducing somewhere else widens the bug rather than weakening the report.

The issue targets the main branch: the bundle records that "the reporter
confirmed the bug on the latest version and on the main branch" with an
installed-versions block showing "python 3.14.6 and a main-branch pandas
build", and a thread comment "confirms on pandas 2.3.3 and current main". The
report ran 1.5.3 — *older* than everything the issue names.

My clause was written for the forward case, where reproducing on a newer
version than the issue names shows the bug survived and makes the report
stronger. Backwards it proves nothing: a crash on 1.5.3 says nothing about
whether current main still crashes, which is the only question the issue
asks. The grader applied the clause as written, and because the direction was
never specified it treated an older version as an overridden difference. That
asymmetry is the defect, and it is mine — the check is too permissive in one
direction because I wrote it thinking only about the other.

### Check rationale

The `next-step-named` row, quoted exactly as `tools/repro-check/rubric.md`
reads now (fenced so the pipes and emphasis appear as written):

```
| next-step-named | The package as a maintainer reads it — the claim comment *and* the repro report together, since both are posted to the same thread | Somewhere in the package, a next step is named in terms a maintainer can act on: the file or symbol a fix would touch, the direction that fix would take, or a specific question the maintainer has to answer. It does not matter which comment carries it; a claim that names the target and a report that stops at Expected/Actual is a complete package, because the reader has both. "I'll keep looking" and "will open a PR" name no target and are a fail. Never a date or an effort estimate | preferred |
```

It reads that way because the first version of it was the single worst change
I made. I had written it as `required` and scoped its evidence to the repro
report alone, reasoning that a report which proves a bug and then stops
leaves the maintainer to do the triage I claimed to be saving them. Two
classmates had independently asked for a conclusion check during the group
calibration, so I believed it was well motivated.

Run 1 returned 12/20 and rejected six of the eight packages the gold labels
call ready. In every one of those six, the next step was present — it was
just in the claim comment instead of the repro report. pkg-12's grade said it
plainly: the next-step language "lives in the claim comment, not the repro
report's closing lines."

So I changed two things and rejected a third. I widened the evidence to the
whole package, because both comments land in the same thread and a reader who
has one has both. I demoted it to `preferred`, because a package that names
its next step is stronger but a package that does not is still honest proof.
What I rejected was the tempting middle option of keeping it `required` and
merely widening the evidence — that would have passed the eval set too, but
it would mean holding back a correct, fully evidenced reproduction over a
missing sentence, and the rubric's job is to decide whether the proof is
ready to post, not whether the prose is complete.

### Trade-offs

Three concrete costs, one of which I accept permanently.

**A canary I re-ran with `--only`, and what it caught.** Every revision after
run 1 loosened a check, so per the canary rule I added one already-agreeing
package from each single-package category to the `--only` list. pkg-20, the
sole `disclosure` package, flipped from reject to accept. Investigating it
showed pkg-20 had been *agreeing for the wrong reason* in run 1: it only
rejected because of my too-strict `next-step-named`, while `claim-specific`
passed it in both runs on the reasoning that "no AI use is evidenced in the
text so the disclosure requirement is not shown violated." The repo's
AI_POLICY mandates disclosure in any form. That reasoning is backwards —
invisibility is the condition the rule exists to address — so my rubric was
blind to the entire category the eval set built that package to force.
Without the canary I would have shipped that blindness and only seen it on
the confirming full run. The fix now separates a disclosure mandate from a
condition of conduct, and honors the mandate's stated scope, which is why
pkg-09 (disclosure asked in pull requests, explicitly not in issue comments)
still correctly passes.

**A package whose result a check changed.** Loosening `output-matches` to
carry an explicit cannot-reproduce exemption moved pkg-09 and pkg-10 from
reject to accept, matching gold. Both report a careful, fully evidenced
non-reproduction, and the check had been failing them for not showing a
failure that by definition did not occur — while `claims-backed` already said
such a report passes. The rubric contradicted itself and the stricter half
was winning.

**A case I accept it will miss.** pkg-16, above. `env-recorded` is
direction-blind about version drift, and it will keep accepting any report
that reproduces a bug on a version *older* than the one the issue targets.
The fix is a one-line asymmetry — reproducing forward widens a report,
reproducing backward proves nothing — but applying it now would invalidate
the fingerprints in the committed `eval-run.txt`, which records the rubric
that actually produced the 19/20 run
(`rubric.md  sha256:e42ad724d4f2ce74`, verified against the committed file).
I would rather submit a rubric whose one known hole is documented than a
transcript that no longer matches the tool beside it.
