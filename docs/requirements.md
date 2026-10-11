# Ujima Loan Desk: Requirements

These notes were written from the finished app, so they describe what it does today and what I would build next. Each story is marked Done or Backlog.

## 1. Problem and goal

A SACCO loan officer has to read every application, check it against lending policy and write a reason for the decision. That is slow and the quality varies from one officer to the next. The app gives the officer a consistent first recommendation with a written rationale. The officer keeps the final say.

**Success measures (proposed, not yet measured)**
- Time from submission to a decision in the queue is under one minute.
- Every decision carries a rationale and a list of risk factors.
- Every AI decision can be overridden and the override is visible in the figures.

## 2. Personas

| Persona | Goal | Pain today |
|---|---|---|
| Member (applicant) | Apply quickly and get a clear answer | Long waits, little explanation |
| Loan officer | Decide consistently and defend the decision | Manual policy checks, uneven rationale |
| SACCO manager | See approval rate and money going out | No quick view across applications |

## 3. Scope

**In scope:** application form, AI scoring against policy, officer queue, override, analytics, local storage.

**Out of scope:** real authentication, a database, repayment tracking, credit bureau checks, notifications.

## 4. Lending policy used by the AI

- Loan amount normally capped at 3 times the member's savings.
- Debt to income ratio of 0.40 or lower.
- More than 0 previous defaults means the best outcome is Review. More than 1 means Reject.
- Membership over 24 months and repaid loans count in the member's favour.

## 5. User stories

### US-01 Submit an application (Done)
As a member, I want to fill in one form with my income, expenses, savings and loan request, so that I get a decision without visiting the office.

**Acceptance criteria**
1. Given the name or member ID is empty, when I submit, then I see "Please enter the applicant's name and member ID." and nothing is sent.
2. Given every field is inside its allowed range, when I submit, then the application is sent for scoring.
3. Given I choose a sample profile, when the form loads it, then all fields are filled so I can submit straight away.

### US-02 Reject invalid inputs (Done)
As a loan officer, I want impossible values refused, so that the AI never scores bad data.

**Acceptance criteria**
1. Given age is below 18 or above 80, when the application is validated, then it is refused.
2. Given the loan amount is below KES 1,000 or above KES 20,000,000, then it is refused.
3. Given the loan term is outside 1 to 120 months, then it is refused.
4. Given an employment status or purpose outside the listed options, then it is refused.

### US-03 Get an AI recommendation (Done)
As a loan officer, I want a structured recommendation with a score and reasons, so that I can review it quickly.

**Acceptance criteria**
1. Given a valid application, when scoring completes, then the result has a decision (approve, review or reject), a risk level (low, medium, high) and a credit score from 0 to 100.
2. Given the result, then it also has approved amount, recommended term, interest rate, estimated monthly payment, debt to income ratio and a written rationale.
3. Given the result, then it lists positive factors, risk factors and recommendations.
4. Given the AI reply does not match the required structure, when it is checked, then an error is shown and nothing is stored.

### US-04 Handle gateway problems (Done)
As a member, I want a plain message when scoring fails, so that I know what to do next.

**Acceptance criteria**
1. Given the gateway is rate limited, then I see "Rate limit reached. Please try again in a moment."
2. Given credits are used up, then I see "AI credits exhausted. Please top up your workspace billing."
3. Given the API key is missing, then the server reports that the key is not configured.

### US-05 Work the officer queue (Done)
As a loan officer, I want to filter applications by decision and open the details, so that I can focus on the cases that need me.

**Acceptance criteria**
1. Given saved applications, when I open the dashboard, then I see tabs for All, Approve, Review and Reject with counts.
2. Given I select an application, then I see its rationale, factors and proposed terms.
3. Given there are no applications, then I see an empty state.
4. Given I click Clear queue, then I must confirm before anything is removed.

### US-06 Override the AI (Done)
As a loan officer, I want to replace the AI decision with mine, so that the final call stays with a person.

**Acceptance criteria**
1. Given an application, when I choose Approve, Review or Reject, then my choice is saved as the officer override.
2. Given an override exists, then the queue tab counts use the override instead of the AI decision.
3. Given an override exists, then the original AI decision is still stored.

### US-07 See portfolio analytics (Done, with one defect)
As a SACCO manager, I want totals and charts, so that I can see approval rate and money going out.

**Acceptance criteria**
1. Given saved applications, then the page shows applications, approval rate, amount disbursed and average AI score.
2. Given overrides, then approval rate and amount disbursed use the final decision.
3. Given the decision mix chart, then it uses the final decision.
4. Given the risk chart, then it uses the AI risk level.
5. Given the disbursed by purpose chart, then it should only add up approved applications. **This does not hold today, see D-01.**

## 6. Backlog

| ID | Item | Priority (MoSCoW) | Status |
|---|---|---|---|
| B-01 | Fix D-01 so disbursed by purpose counts approved loans only | Must | Open |
| B-02 | Reason field when an officer overrides, stored with the override | Should | Open |
| B-03 | Real sign in with member and officer roles | Should | Open |
| B-04 | Move storage from the browser to a database | Should | Open |
| B-05 | Export the queue to CSV for the manager | Could | Open |
| B-06 | Email or SMS the member when the final decision is made | Could | Open |
| B-07 | Credit bureau check before scoring | Won't (this release) | Parked |

## 7. Defect log

**D-01: Disbursed by purpose counts every application.**
The chart adds the AI approved amount for all applications, including ones that were rejected or overridden to reject. The headline disbursed figure is correct because it uses the final decision, so the two numbers disagree.

**Decision for B-01:** filter the chart by final decision equal to approve before summing. Separately, the risk distribution chart stays on the AI risk level on purpose, because risk describes the applicant and does not change when an officer overrides.

## 8. Data dictionary (application input)

| Field | Type | Rule |
|---|---|---|
| fullName | text | 2 to 80 characters |
| memberId | text | 2 to 40 characters |
| age | integer | 18 to 80 |
| employmentStatus | list | employed, self-employed, business-owner, farmer, retired |
| monthsAtJob | number | 0 to 600 |
| monthlyIncomeKES | number | 0 to 10,000,000 |
| monthlyExpensesKES | number | 0 to 10,000,000 |
| existingDebtKES | number | 0 to 50,000,000 |
| savingsBalanceKES | number | 0 to 50,000,000 |
| monthsAsMember | number | 0 to 600 |
| previousLoansRepaid | integer | 0 to 50 |
| previousDefaults | integer | 0 to 50 |
| loanAmountKES | number | 1,000 to 20,000,000 |
| loanTermMonths | integer | 1 to 120 |
| loanPurpose | list | business, education, agriculture, emergency, home-improvement, asset-purchase, personal |
| collateralValueKES | number | 0 to 100,000,000 |
| guarantors | integer | 0 to 10 |

## 9. Data dictionary (AI decision)

| Field | Type | Rule |
|---|---|---|
| decision | list | approve, review, reject |
| riskLevel | list | low, medium, high |
| creditScore | integer | 0 to 100 |
| approvedAmountKES | number | 0 or more |
| recommendedTermMonths | integer | 1 to 120 |
| interestRatePct | number | 0 to 60 |
| estimatedMonthlyPaymentKES | number | 0 or more |
| debtToIncomeRatio | number | 0 or more |
| rationale | text | required |
| positiveFactors, riskFactors, recommendations | list of text | required |
