Project name:

FinSentry AI

Project description:

Financial institutions process large volumes of digital transactions every day, making it difficult to investigate potentially fraudulent activity using only static rules or isolated fraud-detection models. Fraud investigation often requires combining transaction anomalies, customer behavioral patterns, historical records, and contextual evidence before a reliable decision can be made.

FinSentry AI is a multi-agent financial fraud detection and investigation system in which multiple specialized AI agents collaboratively analyze suspicious transactions and produce evidence-based fraud assessments.

The system consists of five distinct coordinating agents:

1. Supervisor Agent – Acts as the central orchestrator. It receives a transaction, creates an investigation plan, delegates analysis to the appropriate agents, maintains investigation state, and combines their findings into the final decision.

2. Transaction Risk Agent – Examines transaction-level characteristics such as transaction amount, time, frequency, merchant information, location, and payment method to identify abnormal or high-risk transaction patterns.

3. Customer Behavior Agent – Analyzes the customer's historical transaction behavior and compares the current transaction against their normal spending patterns, transaction frequency, preferred locations, devices, and merchants.

4. Fraud Intelligence Agent – Investigates historical fraud records and related entities to determine whether the merchant, recipient account, device, location, or other transaction attributes are associated with previously suspicious activity.

5. Decision & Explanation Agent – Receives evidence collected by the other agents, evaluates the combined findings, generates an overall fraud-risk assessment with supporting reasons, and determines whether the transaction should be cleared, flagged for investigation, or escalated for human review.

The agents operate collaboratively rather than independently. For example, if the Transaction Risk Agent identifies an unusually large transaction, the Supervisor Agent can request the Customer Behavior Agent to determine whether such spending is abnormal for that particular customer. If abnormal behavior is confirmed, the Fraud Intelligence Agent can then investigate whether the recipient, device, or other entities have appeared in previous suspicious cases. These findings are passed to the Decision & Explanation Agent, whose assessment changes according to the evidence produced by the preceding agents. Conflicting findings can also be returned to the Supervisor Agent for additional investigation before a final decision is made.

The final output provides the predicted fraud risk, confidence level, evidence collected by each agent, reasoning behind the assessment, and recommended action. High-risk or uncertain cases are escalated for human review, ensuring that critical financial decisions are not performed autonomously without appropriate oversight.

By combining multi-agent coordination, transaction analysis, behavioral profiling, fraud intelligence, evidence aggregation, explainable decision-making, and human-in-the-loop validation, FinSentry AI aims to provide a more transparent and investigative approach to financial fraud detection than a conventional single-model fraud classifier.
