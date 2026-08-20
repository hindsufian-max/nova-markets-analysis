# Data Scientist — Home Task

Thanks for taking the time. This should take **about an hour**. It is not a test of
whether you already know the payments industry — you almost certainly don't, and
neither did we when we started. It is a test of how you work with unfamiliar,
messy data and an underspecified question, which is most of this job.

**Use AI.** Claude Code, ChatGPT, Copilot, whatever you normally use. We use these
tools every day and we would rather see how you drive them than pretend you work
without them. One of the questions below asks you about that directly.

---

## The situation

Northwind Payments processes online deposits and withdrawals on behalf of client
businesses (we call them merchants). This landed in the team inbox this morning:

> From: Account Management
> Subject: FW: Nova Markets — urgent
>
> Nova Markets (merchant id `NOVA-FX`) is escalating. They pulled their own
> dashboard export and say their approval rate "fell off a cliff" after 1 June.
> They're threatening to move volume to a competitor unless we explain it this
> week, and they want to know how much it cost them.
>
> Can someone look into this and tell us what to say to them?

That's the whole brief. Figure out what is actually going on and tell us what to
do about it.

---

## What you have

Everything is in `data/`. It is a snapshot pulled on 30 June 2026.

| File | What it is |
|---|---|
| `orders.csv` | One row per payment attempt, all merchants, 1 Apr – 30 Jun 2026 |
| `routes.json` | Reference data describing each route |
| `fx_rates.csv` | Daily exchange rates |
| `merchant_dashboard_export.csv` | The export Nova Markets sent us — what *they* see |
| `ops_log.md` | The payments ops team's change log for the period |

### A few terms

- **Merchant** — our client. The business collecting the payment.
- **Attempt / order** — one try at moving money. `order_type` tells you which
  direction: `SALE` is money in from the merchant's customer (a deposit),
  `PAYOUT` is money out, `REFUND` is money returned.
- **Processor** — an external payment company we connect to.
- **Route** — the specific path an attempt is sent down to reach a processor. A
  merchant usually has several, and which one an attempt uses can change over
  time. `routes.json` describes them.
- **Approval rate** — the share of attempts that succeed. Deciding exactly how to
  compute it is part of your job here, not something we're going to hand you.

Statuses in `orders.csv`:

| Status | Meaning |
|---|---|
| `APPROVED` | Succeeded. |
| `DECLINED` | The processor rejected it. |
| `FILTERED` | Stopped by our own risk screening. Never reached a processor. |
| `PENDING` | Still in flight. No final outcome yet. |

Anything not described above, you'll have to work out or look up — that's normal
here. If you can't work something out, say so; it's a better answer than a guess
presented as fact.

---

## What to send us

**A link to a public GitHub repo** containing:

> **Important:** This task uses a fictionalized company ("Northwind Payments").
> Do not mention Zota's name anywhere in your public repo — README, code,
> commit history, commit messages, or repo name.

**1. `README.md` — your memo.** Maximum one page. Written for the account manager,
who is smart but not technical. It must have these five sections:

- **What's going on** — what you found, and is Nova Markets right?
- **What we should do** — what we tell the merchant, and what we should change
  internally.
- **Assumptions and open questions** — what you had to assume, and what you'd want
  to ask before anyone acts on this.
- **How to reproduce** — how we run your code.
- **How you used AI** — which tools, and one specific place where the tool got
  something wrong or led you astray, and how you caught it.

**2. Your code.** A notebook or scripts. It needs to actually produce the numbers
in your memo — we will run it.

Charts are welcome but not required. We care much more about whether the
conclusion is right than about how it looks.

---

## How we'll read it

We're looking for whether you questioned the data before trusting it, whether your
conclusion follows from your numbers, and whether you were clear about what you
don't know.

**On the hour:** it's a real limit, not a suggestion. We would much rather have a
sharp partial answer with the gaps clearly marked than a complete-looking answer
we can't trust. If you run out of time, write down what you'd do next and send it.
Telling us what you chose *not* to do is a good answer, not an excuse.

Good luck.
