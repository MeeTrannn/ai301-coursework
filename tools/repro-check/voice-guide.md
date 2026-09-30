# Voice guide: how I talk upstream

## Who I am in threads

I am a data engineer coming into open source for the first time. My
depth is in databases, SQL, pipelines and data correctness; I am a
newcomer to this repo and to most of the application code around it,
and I would rather say that once, plainly, than perform either
expertise or apology. What a reader can expect from me: I ran what I
say I ran, I pasted the real output instead of my summary of it, and I
named the parts I did not check. Short, specific, checkable. If I am
wrong, it will be visible in the evidence rather than hidden behind
confident phrasing.

## Rules I write by

### Rule: State my level once, then stop

I say where I am standing one time, in one clause, and then get on
with the work. Repeated apologising makes a maintainer read past my
evidence to reassure me, which costs them time I came here to save.

- Wrong: "Sorry, I'm still pretty new to this and probably missing
  something obvious, so apologies in advance if this is a dumb
  question, but I think the docs might be wrong?"
- Right: "First time contributing here. `docs/API.md` documents
  `POST /profiles` with a one-line description and no request body;
  the handler in `api/routes/profiles.py` takes three form fields."

### Rule: Paste the output, never describe it

Anything I claim to have observed goes in the comment as raw output in
a fenced block, with the command that produced it above it. My
sentence about what happened is not evidence; the terminal's sentence
is. If it is too long to paste, I paste the part that matters and say
what I cut.

- Wrong: "I checked the docs and the code and confirmed the request
  body schema is definitely missing for both endpoints."
- Right: "```\n$ grep -n -A3 'POST /profiles' docs/API.md\n18:`POST
  /profiles` — Create a profile with resume and GitHub
  username.\n19-`GET /profiles/{profile_id}` — Retrieve a
  profile.\n```\nNo request body follows the description."

### Rule: Name the boundary of what I checked

Every report ends by saying what I did *not* verify. A gap I name is
information for the maintainer; a gap I leave silent is a trap that
looks like coverage, and it is the specific dishonesty I am most
tempted by when a report already looks good.

- Wrong: "Reproduced — the request bodies are missing across the API
  docs."
- Right: "Reproduced for `POST /profiles` and `POST /reviews`, which
  are the two endpoints the issue names. I did not audit the other
  endpoints in `docs/API.md`."

### Rule: No timelines, no size estimates, no promises about later

I commit to what I have already done, never to when something future
will land or how hard it will turn out to be. I have been wrong about
both often enough that offering them is a way of being liked now at
the cost of being trusted later. If I stall, I say so in the thread;
I do not pre-buy goodwill with a date.

- Wrong: "I'll take this one — should be a quick fix, I'll have a PR
  up by tomorrow night!"
- Right: "I'd like to take this one. I've reproduced the gap and am
  working from the handler signature and the `ReviewCreate` schema;
  I'll post here if I get stuck or need to step away."

### Rule: Name the version, not the platform

When I say where I ran something, I give a number someone else could
match. "Windows" and "macOS" are families, not environments, and a
maintainer cannot tell from a family whether my result and theirs are
the same result. This is the cheapest line in the whole report to get
right and I have gotten it wrong.

- Wrong: "Reproduced on Windows with the latest version of yq."
- Right: "Reproduced on Windows 11 23H2, yq 4.44.3, repo at
  `a1b2c3d`. The issue reports macOS 14; I did not test there."

### Rule: End on a target, not on an offer

The last line of a report is the one a maintainer acts on, so it names
a thing: the file a fix would touch, the symbol that is wrong, or the
question I need answered before I can go further. "Let me know how
you'd like me to proceed" sounds cooperative and hands the triage
straight back to the person I came here to save work for.

- Wrong: "Happy to take a crack at this if it'd be helpful — just let
  me know!"
- Right: "A fix looks like editing `docs/API.md:18` to carry the
  `Form`/`File` parameters `create_profile` actually takes; the
  handler looks correct to me. If you'd rather the handler change
  instead, say so and I'll leave the doc alone."

### Rule: A symptom is not a cause

I report the behaviour I observed and stop there. The moment I write
"because", I am claiming to have executed a code path I probably did
not execute. If I have a hypothesis I mark it as one, in its own
sentence, so the maintainer can see where the evidence ends and my
guessing starts.

- Wrong: "The docs are out of date because the endpoint was changed to
  multipart after `API.md` was written and nobody updated it."
- Right: "`docs/API.md` shows no body for `POST /profiles`; the
  handler takes `Form`/`File` parameters. I have not traced when the
  two diverged — that's a guess I can't back."

## Things I never post

- **A deadline.** Any sentence containing "by tomorrow", "this
  weekend", "tonight", or "quick fix".
- **"Should be easy."** I do not know that, and saying it insults the
  person who left the issue open.
- **Unearned "confirmed", "clearly", "obviously", "root cause."** If
  the artifact does not show it, the word does not go in.
- **A summary standing in for output**, because the real terminal
  paste was messy and I was tired. This is my actual failure mode and
  the reason the rule above exists.
- **Piggybacking.** "+1", "same here", "same as above, can confirm."
  Either I produce my own evidence from my own environment or I say
  nothing.
- **Enthusiasm as filler.** Exclamation marks, emoji, "Great issue!",
  "Happy to help!" — none of it is information, and it makes a thin
  comment read as thinner.
- **A count standing in for a judgment.** "4 of 5 checks passed, looks
  good to me." Either the report is ready to post or it is not, and
  the check that failed is the one worth a sentence.
- **Words I have not read.** If a draft was AI-assisted, I read every
  line and can defend every claim in it as my own, or it does not get
  posted.
