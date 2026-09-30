# Ujima SACCO: Ethical AI Loan Approval System

Capstone project for the AI for Professionals course. It is a working demo of how a Kenyan SACCO could use AI to speed up loan underwriting while a human loan officer stays in charge of every final decision.

## The problem

Loan officers at a SACCO read each application by hand, check it against rules like the savings limit and a debt-to-income ceiling, and write up their reasoning. That takes time and can be inconsistent from one officer to the next. This project tests whether an AI underwriter can do the first pass and explain itself, so the officer spends their time on judgment instead of arithmetic.

## How it works

1. **The member applies.** A guided form collects personal and employment details, income, expenses, existing debt, savings, membership history, the loan request, collateral and guarantors.
2. **The AI underwrites.** The application is scored against SACCO policy. The result comes back in a fixed structure: a decision (approve, review or reject), a risk level, a credit score from 0 to 100, a suggested amount, term and interest rate, the estimated monthly payment, the debt-to-income ratio, a short plain-language rationale, positive factors, risk factors and recommendations.
3. **The officer confirms.** Applications land in a queue. The officer opens any of them, reads the reasoning, and can override the AI with one click.
4. **The portfolio view updates.** An analytics page shows approval rate, risk distribution and amounts approved by loan purpose. It is calculated from the officer's final decision, not the AI's first one.

The form has two pre-filled sample applicants, one strong and one risky, so anyone testing the demo can see both ends of the range straight away.

## Policy rules the AI works from

- A member can borrow up to 3 times their savings with the SACCO.
- New monthly payment plus existing debt should not be more than 40% of income.
- One previous default caps the outcome at review. More than one means reject.
- Membership longer than 24 months and loans repaid on time count in the applicant's favour.

## Ethical design choices

- **A human decides.** The AI recommends and the loan officer has the final say. Overrides are respected everywhere, including the analytics.
- **Every decision is explained.** The officer sees the rationale and the factors for and against, in plain language, not just a score.
- **Output is checked, not trusted.** The AI's response has to match a strict schema. If it does not, the application fails loudly instead of showing a made-up decision.

## Limitations of the current demo

I would rather state these than have someone find them:

- The policy rules are given to the model in its instructions. They are not yet enforced by separate code, so the 3x savings limit and the debt-to-income target are not double-checked.
- Age is collected and passed to the model. That needs a fairness review before any real use, and there has been no bias testing yet.
- The model can give slightly different answers to the same application on different runs.
- Applications are stored in the browser only. There is no database, no login and no permanent audit trail.
- The decision card on the landing page is a fixed sample, not a real application. No speed or accuracy figures have been measured.
- All applicants are made-up sample data.

## What I would build next

1. Deterministic checks behind the model for the savings limit, debt-to-income and default rules.
2. Fairness testing across varied synthetic profiles, and a decision on whether age should be an input at all.
3. A proper audit log recording who overrode what, and when.
4. Repeatability tests: the same application run many times, with differences tracked.

## Stack

TanStack Start with React 19 and TypeScript, Tailwind CSS 4, shadcn/ui and Radix, Recharts for the analytics, and Zod for validation. Scaffolded with Lovable, with scoring done by a Gemini model through the Lovable AI Gateway.

## Source code

The source lives in a private repository. I am happy to share access or walk through it on request.

## About

Built by Collins Basuben, a fraud and risk analyst in Nairobi, Kenya. See also my [transaction monitoring project](https://github.com/Basuben/transaction-monitoring-fraud-detection).
