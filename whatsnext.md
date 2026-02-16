---

## Yes & No

- **Yes, it Works:**
  - Real-time fraud detection with rule-based scoring.
  - SMS-based fraud alert and response system.
  - Travel mode implementation with location-based sensitivity.
  - Mapping table (`UserFraudTransactionsMap`) tracks open fraud investigations.
  - User-triggered travel mode toggles via text messages.

- **No, it Doesn’t (Yet):**
  - The machine learning fraud detection logic exists in the code, but it’s not functional — the models and encoders (`.pkl` files) are referenced but not uploaded to Lambda.
  - `joblib` is required for loading models, but not currently packaged via Lambda Layers.
  - Twilio is restricted to verified numbers on the free tier.
  - UserID must be a phone number since DynamoDB cannot enforce uniqueness on non-key attributes.

---

## What to Work on Next

- Integrate and test the ML-based detection by:
  - Training models on realistic datasets.
  - Uploading them using Lambda Layers.
- Migrate user data to an RDS/SQL database for better integrity and relational access. 
  - This would allow enforcing unique constraints on fields like phone numbers without making them the primary key.
- Use NLP to parse user responses more flexibly (e.g., typos, slang).
- Add fuzzy matching for travel mode locations (e.g., "Tokoyo" still maps to "Tokyo").
- Build a lightweight frontend or dashboard where users can review alerts, toggle travel mode, and view history and also get more details on the transactions to verify if it is fraud or not.

