# protomatter-contact-form — read this first

The page an NFC business card opens when somebody taps it. Three standalone
HTML files on GitHub Pages. No server, no build step, no dependencies.

| File | Who it is for |
|---|---|
| `index.html` | whoever tapped the card — `?c=<id>` picks the profile |
| `edit.html` | one person editing their own card — `?c=<id>` |
| `admin.html` | whoever runs the cards: profiles, invites, publishing |
| `config.json` | settings shared by every card |
| `profiles.json` | every profile, keyed by card id |

## How a page is configured

Three layers, each overriding the one before:

```
DEFAULTS        built into index.html, so the page is never blank
config.json     shared by every card: relay endpoint, wording, copy rules
profiles.json   the one profile matching ?c=<id>
```

A profile carries only what makes it different. That is why the endpoint and
the auto-reply wording are written once, and why the editor shows which values
are inherited rather than pretending a box is empty.

## Rules that are not yours to change

1. **No API tokens in any page.** These files are public. Publishing is
   deliberately manual: download `profiles.json` from the admin page, commit
   it. Do not automate it with a GitHub token.
2. **No build step, no `package.json`, no framework.** A non-developer has to
   be able to open these files and read them. Everything is inline on purpose.
3. **`edit.html` must never mention that an admin page exists.** People editing
   their own card are not administrators.
4. **Nothing is saved implicitly.** Saved, unsaved and published must always be
   visibly different states.
5. **`robots.txt` disallows crawling.** This page is meant to be reached from a
   card, not from a search engine.

## Things that broke before — keep them fixed

* **`<meta name="referrer" content="no-referrer">` breaks the form relay.** It
  reads `Referer` to tell a hosted page from a local file. The policy is
  `strict-origin-when-cross-origin` for that reason.
* **An empty `_cc` makes the relay answer HTTP 500.** Empty underscore-prefixed
  fields are stripped before the POST.
* **The relay answers HTTP 200 with `success:"false"`.** Read the body, not the
  status code.
* **Never address a form field through the form object.** `<form name="...">`
  makes `form.name` return a string, which once caused a valid email address to
  be rejected. Use `getElementById`.
* **Contacts are vCard, not MECARD.** MECARD has no `TITLE`, so job titles
  silently vanished from saved contacts.

## Testing

The tests live in the companion repository (`NFC_Card`), which also holds the
desktop Studio and the phone app. They execute these pages' real JavaScript
against a small DOM stub — config layering, the vCard, the submission body,
version numbers, the admin CSV export, the profile rename path.

```
python tests\run_all.py --layer web
```

Point it at this checkout with the `PROTOMATTER_WEB` environment variable, or
clone the two repositories side by side and it will find this one by itself.

Anything that needs a real browser — the photo cropper's drag and zoom, phone
autofill, whether an email actually arrives — is in that repository's
`tests/manual/checklist.json` and is recorded by hand.

## The card link

```
https://<host>/<repo>/?c=a7f3k9
```

That shape is shared with the desktop Studio, the printed QR and the NFC tag
itself. Changing how the id is read here breaks cards that are already printed
and already in people's wallets. There is no redirect layer.
