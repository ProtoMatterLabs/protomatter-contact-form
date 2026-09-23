# Making it easy to reply

Three paths, from "already works" to "costs money". Start at the top.

---

## 1. The form already auto-replies. Nothing to set up.

When someone uses **Send me your details**, they are automatically mailed your
details straight back. No action from you, no Gmail rules, no service.

It is the `_autoresponse` field, and it substitutes from the profile that was
tapped — so Ada's card replies with *Ada's* details, not the admin's.

Edit the wording in `edit.html` under **Automatic reply sent back to them**.
Placeholders: `{{name}}` `{{role}}` `{{company}}` `{{email}}` `{{site}}`

**This already covers the case you care about.** The rest of this file is for
the paths that bypass the form.

---

## 2. Replying to a submission: one tap, already built.

Every notification you receive contains two extra rows:

| | |
|---|---|
| `reply_by_email` | `mailto:` addressed to them, prefilled with your details |
| `reply_by_text` | `sms:` addressed to them, prefilled with your details |

Tap either and your mail or messaging app opens **already written**. You press
send. That is the whole workflow.

`reply_by_text` is only filled in if they gave a phone number. Blank phone,
no link — nothing is broken.

**These are links, not automation.** They do not send anything on their own.
That is deliberate: an auto-sent text from your personal number is not
something any phone will do (see §4).

---

## 3. Someone emails you directly (they tapped "Email me")

The form is not involved, so `_autoresponse` cannot fire. Set up a Gmail rule.

This takes about two minutes and only needs doing once.

### Why it can be targeted

Your card prefills the subject line — by default **"Nice meeting you"**. That
exact string is the hook, so the rule fires on card contacts and nothing else.
Change it per person in `admin.html` if you want per-person rules.

### Create the reply template

1. Gmail → ⚙ → **See all settings** → **Advanced**
2. Set **Templates** to *Enable*, then **Save Changes**
3. **Compose**, write the reply you want to send (your details, a line of
   greeting — leave the To and Subject blank)
4. In the compose window: **⋮** → **Templates** → **Save draft as template** →
   **Save as new template**. Name it *Card reply*.

### Create the rule

1. Gmail search box → **Show search options** (the sliders icon)
2. **Subject:** `Nice meeting you`
3. **Create filter**
4. Tick **Send template** and choose *Card reply*
5. **Create filter**

Done. Anyone who emails you from a card gets your details back automatically.

### Worth knowing

- Gmail sends a template reply **once per conversation**, not on every message
  in a thread. That is usually what you want.
- Make the subject distinctive. A generic one will fire on unrelated mail.
- The "Vacation responder" is the blunt alternative — it replies to
  *everything*, which is almost never what you want here.

---

## 4. Automatic text replies

**Not possible from your own number.** Android and iOS both refuse to auto-send
SMS outside Driving Mode, and no app can work around it.

It needs a service such as Twilio with a **dedicated number** (roughly $1/month
plus a fraction of a cent per message). Inbound text triggers an automatic
reply. That is a real second phone number on your card, not your own.

### The idea of texting people a link

You asked whether we could text someone a link that opens their email
prefilled. We can — but look at what it costs against what it adds:

| | Taps for you | Setup | Cost |
|---|---|---|---|
| Reply link in the email (§2) | 1 | none | free |
| Text them a link | 1 | Twilio, a number, a webhook | monthly + per message |

Both end with them tapping a prefilled message. The SMS version adds a paid
service, a phone number and a server to trigger it, and lands in a channel
people check less carefully than email.

**The reply link in §2 is the same workflow with none of that.** Worth revisiting
only if you specifically want inbound texts to a business number.
