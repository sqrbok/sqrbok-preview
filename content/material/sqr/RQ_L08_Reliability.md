---
title: "RQ L08: Software Reliability"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 16
redirect_from: /content/attributes/reliability/RQ_L08_Reliability.html
layout: default
page_type: question-set
---

# RQ L08: Software Reliability

**Format:** Explain in your own words, give brief examples, and show every calculation step. These questions test the lecture summarised in [SN L08: Software Reliability](SN_L08_Reliability.md).

---

## Part 1: Foundations

### Question 1: Fault, Error, Failure

A web server crashes on requests larger than 8 KB because of a missing length check. It ran for months without trouble.

1. Name the fault, the activation, the error and the failure.
2. During the months without trouble, which of the three existed, and why did nobody notice?
3. Musa and Avizienis use the word "error" differently. State both meanings.

### Question 2: Ariane 5

Trace the Ariane 5 flight 501 loss through the fault–error–failure chain. Then explain why the backup inertial reference computer did not help, and what this says about duplicating software.

### Question 3: Hardware vs Software

Explain why the bathtub curve describes hardware but not software. When does a program's failure rate change?

---

## Part 2: Measuring and Setting Reliability

### Question 4: From Failure Data to Reliability

A system logged 4 failures in 2,000 hours of stable operation.

1. Compute λ and the MTTF.
2. Compute the probability of no failure during a 24-hour run.
3. Its mean downtime per failure is 2 hours. Compute the availability.

### Question 5: Failure Intensity vs Failure Density

Describe a module that looks good by failure density but bad by failure intensity, and say which measure a user of the system would care about.

### Question 6: Severity Classes

Define four failure severity classes for a hospital patient portal (test results, appointments, messages to doctors). State which criterion you chose (cost, system capability, human life or environment), why, and give one failure per class.

### Question 7: FIO from Availability

A payment system must be available 99.9% of the time. Mean downtime per failure is 2 minutes.

1. Using the exact formula, compute the maximum failure intensity λ.
2. Compute the corresponding MTTF.
3. How many failures does this allow in 720 hours?
4. Compute λ with the approximation λ ≈ (1 − A)/t<sub>m</sub> and compare.

### Question 8: FIO from Reliability

A requirement says the probability of no failure during a 4-hour operation must be at least 0.995. What FIO follows?

---

## Part 3: Growth Models

### Question 9: When to Stop Testing

A Goel–Okumoto model fitted to test data gives a = 80 faults and b = 0.03 per week. The FIO is 1.0 failure per week.

1. What is λ(0)?
2. What is λ(20)?
3. After how many weeks is the FIO reached?
4. How many faults have been found by then, and how many remain?

### Question 10: Limits of Growth Models

A web shop deploys new features several times a day. Explain why a growth model fitted to its failure data would mislead, and name the approach used instead.

---

## Part 4: Fault Tolerance and Diversity

### Question 11: Recovery and the Domino Effect

Service A processes an order, sends "ship item" to Service B, and then detects an error and rolls back to a checkpoint taken before the message.

1. What must Service B do, and why?
2. How can this cascade further? Name the effect.
3. How would forward recovery handle A's error instead?

### Question 12: Acceptance Tests

Write an acceptance test for a function that returns the square root of a number, and one for a function that sorts a list. Why is the second harder to make complete?

### Question 13: Voting Reliability

Three versions each have reliability R<sub>c</sub> = 0.95.

1. Compute the reliability of a 2-of-3 majority system, assuming independent failures and a perfect voter.
2. Now assume some failures are shared: an input makes all three versions fail together with probability 0.01, and on all other inputs each version fails on its own with probability 0.04. Compute the system reliability.
3. Which assumption did Knight and Leveson's experiment test, and what did it find (setup, the two key numbers, the conclusion)?

---

## Part 5: From FIO to SLO

### Question 14: Error Budget

A streaming service has an SLO of 99.95% successful requests over a 30-day window and serves 50,000,000 requests in that window, spread evenly over time. This window it had three incidents:

- incident A: a full outage of 6 minutes;
- incident B: a full outage of 3 minutes;
- incident C: a degraded period in which 4,000 requests failed.

The team's policy, taken from the SRE Workbook's example: a postmortem is required for any single incident that uses *more than* 20% of the window's error budget; changes are frozen only when the budget is used up.

1. Compute the error budget in requests and in minutes of full outage.
2. Convert each outage to failed requests, and compute the share of the budget each incident used and the total.
3. Which incidents need a postmortem, and may the team keep shipping changes?

### Question 15: FIO and SLO

Explain why Musa's FIO and Google's SLO express the same idea, and what changed between them. Why is 100% the wrong target for both?

---

## Topics Covered

| Topic | Questions |
|---|---|
| Fault, error, failure; hardware vs software | 1, 2, 3 |
| Failure intensity, reliability, availability | 4, 5 |
| Severity classes and the FIO | 6, 7, 8 |
| Reliability growth models | 9, 10 |
| Fault tolerance, voting and diversity | 11, 12, 13 |
| SLO and error budgets | 14, 15 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
