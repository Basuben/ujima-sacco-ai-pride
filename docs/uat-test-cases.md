# Ujima Loan Desk: UAT Test Cases

**Status: none of these have been run.** Scoring needs a live AI gateway key and I have not run the app against one. Every case below is written and ready, and the result column stays at Not run until someone executes it.

| ID | Story | Steps | Expected result | Result |
|---|---|---|---|---|
| UAT-01 | US-01 | Leave name empty, click submit | Toast: "Please enter the applicant's name and member ID." Nothing is sent | Not run |
| UAT-02 | US-01 | Load the Amina Wanjiku sample, submit | Decision appears, success toast, app opens the dashboard | Not run |
| UAT-03 | US-01, US-03 | Load the Brian Otieno sample (savings 12,000, loan 350,000, 1 default), submit | Decision is review or reject, never approve, with risk factors listed | Not run |
| UAT-04 | US-02 | Enter age 17, submit | Application refused | Not run |
| UAT-05 | US-02 | Enter loan amount 500, submit | Application refused | Not run |
| UAT-06 | US-03 | Submit a valid application | Result has decision, risk level, score 0 to 100, terms, rationale and factor lists | Not run |
| UAT-07 | US-04 | Run with the API key removed | Server reports the key is not configured | Not run |
| UAT-08 | US-05 | Save three applications, open each tab | Counts match and each tab lists the right rows | Not run |
| UAT-09 | US-05 | Click Clear queue, cancel the prompt | Nothing is removed | Not run |
| UAT-10 | US-06 | Override an Approve to Reject | Queue moves it to Reject, AI decision still stored | Not run |
| UAT-11 | US-07 | Override one Approve to Reject, open analytics | Approval rate, disbursed total and decision mix reflect the override | Not run |
| UAT-12 | US-07 | Reject one application, open the disbursed by purpose chart | Rejected amount should not appear. **Expected to fail today, see D-01** | Not run |
