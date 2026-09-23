# ProtoMatter contact form

A one-page contact-exchange form for NFC business cards. Static, so it runs on
GitHub Pages with no server.

Tap card → this page opens → their phone autofills their details → one tap sends
them to your inbox. Plus a **Save my contact** button that drops your vCard into
their phone.

## Why a web form and not a `mailto:` card

A `mailto:` link can only prefill fields *you* supply — it cannot fill in the
other person's name, email or phone, because a static tag has no idea who tapped
it. (You do get their address free in the From header when they send, which is
why `mailto:` is still a decent fallback.)

A web form is the only route where **their own phone volunteers their details**:
the `autocomplete` tokens on each field (`name`, `email`, `tel`,
`organization`) let the browser fill them from what it already stores. That is
the difference between them typing and them tapping.

## Setup

### 1. Turn on GitHub Pages

Settings → Pages → Source: **Deploy from a branch** → `main` / `root`.
It lands at `https://<org>.github.io/protomatter-contact-form/`.

### 2. Point it at a form relay

GitHub Pages only serves files — there is no server to send mail. A relay
fills that gap: the page POSTs to them, they forward to your inbox.

**Out of the box the form is in test mode.** `CONFIG.endpoint` is empty, so
submitting validates the fields and shows the success screen without sending
anything. Useful for checking the page works before wiring anything up.

**Formspree** (you already have an account): copy your form endpoint from the
dashboard into `CONFIG.endpoint` in `index.html`:

```js
endpoint: "https://formspree.io/f/xdoqwerty"
```

Free tier is 50 submissions/month.

**FormSubmit** if you would rather not use an account at all: put your address
straight in, submit once, click the confirmation email they send, then replace
your address with the random alias from that email so it is not sitting in
public HTML:

```js
endpoint: "https://formsubmit.co/ajax/abc123def456"
```

## Per-card tracking

Append `?c=<something>` to the URL you write to each tag:

```
https://protomatterlabs.github.io/protomatter-contact-form/?c=ada
```

The value rides along in the submission, so you know which physical card the
contact came from. It also shows faintly at the bottom of the page.

This pairs with the batch CSV in the NFC Card desktop app — give each row a
unique `?c=` and every card is individually traceable.

## What it collects

| Field | autocomplete | Required |
|---|---|---|
| Name | `name` | yes |
| Email | `email` | yes |
| Phone | `tel` | no |
| Company | `organization` | no |
| What are you working on? | — | no |

Five fields is already pushing it. Every field you add costs completions; the
two required ones are the only two that matter.

## Details worth knowing

- **Honeypot, not CAPTCHA.** A hidden `_honey` field catches bots. `_captcha`
  is off because making a real person solve a puzzle while standing in front of
  you defeats the point.
- **16px inputs.** Anything smaller makes iOS Safari zoom on focus, which
  yanks the layout around mid-typing.
- **The vCard is built in the browser**, not fetched. No second file to host and
  nothing that can fail while someone is waiting.
- **vCard uses CRLF line endings** — the spec requires it, and some parsers
  reject LF-only.
- **Failure falls back to `mailto:`.** If the relay is down, the error turns
  into a link that opens their mail app prefilled with everything they typed,
  so nobody is stranded mid-exchange.

## Swapping the relay

Any static-form service works; change one line. [Formspree](https://formspree.io)
(50/month free, needs an account) takes the same JSON POST:

```js
endpoint: "https://formspree.io/f/<your-id>"
```
