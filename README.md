# cp1— Real-Time Fraud Detection System

## Set-Up Instructions

1. **Provision Resources on AWS**:
   - Create IAM Roles, Lambdas, DynamoDB tables (`Transactions`, `Users`, `UserFraudTransactionsMap`), and an SQS queue.
   - Set up API Gateway with a POST route for `/sms-response`.

2. **Configure Lambda Functions**:
   - Attach SQS trigger to `FraudDetectionLambda`.
   - Add EventBridge rule to invoke `ProcessTransactionLambda` periodically.
   - For `HandleUserResponseLambda`, connect it with API Gateway and provide Twilio credentials as environment variables.
   - Package and upload the Twilio Python library and any ML dependencies (e.g., `joblib`) as Lambda Layers especially for `FraudDetectionLambda`.

3. **Twilio Configuration**:
   - Set up a Twilio number for sending/receiving SMS.
   - Enable Twilio to POST responses to your API Gateway URL.
   - Ensure phone numbers used in testing are registered with your Twilio account (due to free-tier limits).

4. **Initialize Data**:
   - Use `PhishNetAddUser` to create five test users (phone-number-based).

---

## How It Works

1. **ProcessTransactionLambda**:
   - Simulates a fake transaction from a random user in the `Users` table.
   - Stores the transaction in DynamoDB and sends the transaction ID to SQS.

2. **FraudDetectionLambda**:
   - Triggered by SQS. Uses the transaction ID to fetch details.
   - Calculates a risk score using merchant, location, and amount heuristics.
   - Travel mode and user habits are factored in to reduce false positives.
   - If flagged as fraud, sends SMS using Twilio and stores a mapping in `UserFraudTransactionsMap`.

3. **HandleUserResponseLambda**:
   - Triggered when users respond to the fraud alert via SMS.
   - Can enable/disable travel mode via specific commands (e.g., `"travel - Tokyo"`, `"stop travel"`).
   - If replying to a fraud alert (`YES`/`NO`), updates the transaction and deletes the mapping.

4. **FraudTesterLambda (Optional)**:
   - A testing utility that allows you to run sample fraud transactions and validate your detection logic.