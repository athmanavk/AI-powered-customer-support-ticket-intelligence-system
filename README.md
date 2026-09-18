# AI-powered-customer-support-ticket-intelligence-system
AI-powered customer support ticket intelligence system with natural-language querying, anomaly detection, FastAPI REST API, and Streamlit UI.

## 1. Project Overview

This project is an AI-powered customer support ticket intelligence system built for the DOTMappers IT Pvt. Ltd. AI Engineer Technical Assessment.

The system processes customer support ticket data from a CSV file and provides:

* Natural-language querying of support ticket data
* AI-powered interpretation of user questions using an LLM
* Deterministic data analysis using Python and Pandas
* Anomaly detection for support tickets
* REST API access through FastAPI
* A minimal interactive UI using Streamlit
* Health and system statistics endpoints

The goal is to provide a simple and reliable interface for analyzing customer support operations using natural-language questions.

## 2. Problem Statement

The system is designed to work with a customer support ticket dataset and perform the following tasks:

1. Ingest the CSV dataset and make it queryable.
2. Answer natural-language questions about the ticket data.
3. Detect and flag anomalies such as:

   * Abnormally long resolution times.
   * Unresolved high-priority tickets older than 24 hours.
4. Expose the functionality through both:

   * A REST API.
   * A minimal user interface.

An LLM is used for natural-language understanding, while the actual data analysis is performed using deterministic Python/Pandas operations.

## 3. Dataset

The project uses `support_tickets.csv`.

The dataset contains 500 customer support tickets with the following columns:

| Column                | Description                                                  |
| --------------------- | ------------------------------------------------------------ |
| `ticket_id`           | Unique ticket identifier                                     |
| `created_at`          | Ticket creation timestamp                                    |
| `category`            | Billing, Technical, or General                               |
| `priority`            | Low, Medium, High, or Critical                               |
| `status`              | Open, Resolved, or Escalated                                 |
| `response_time_hrs`   | Hours from ticket creation to first agent response           |
| `resolution_time_hrs` | Hours from ticket creation to resolution; null if unresolved |
| `agent_id`            | Assigned support agent identifier                            |
| `customer_rating`     | Post-resolution customer rating from 1–5; null if unresolved |
| `issue_summary`       | Brief description of the reported issue                      |

## 4. System Architecture

```text
                    User
                      |
              +-------+-------+
              |               |
         Streamlit UI      REST API
              |               |
              +-------+-------+
                      |
                  FastAPI
                      |
              Natural Language
                   Query
                      |
                     LLM
                      |
             Structured Intent
                      |
             Query Validation
                      |
              Query Engine
                      |
                  Pandas
                      |
             support_tickets.csv
                      |
                  Result
                      |
              Natural Language
                   Response
```

For anomaly detection, the system uses a separate deterministic analysis flow:

```text
support_tickets.csv
        |
   Data Loader
        |
 Anomaly Detector
        |
 +------+------------------+
 |                         |
Long resolution       Unresolved
time anomalies        high-priority
                      tickets >24h
 |
Results
```

## 5. LLM Integration

The LLM is used for natural-language understanding.

Instead of allowing the LLM to directly execute Python or Pandas code, the system converts the user's question into a structured query intent.

Example:

```text
User:
"How many critical tickets are unresolved?"

        ↓

LLM

        ↓

Structured intent

{
    "operation": "count",
    "filters": {
        "priority": "Critical",
        "status": "Open"
    }
}

        ↓

Validated query engine

        ↓

Pandas

        ↓

Final answer
```

This approach separates natural-language understanding from data execution and keeps the data operations deterministic.

## 6. Natural-Language Querying

The system is designed to handle questions such as:

* How many tickets are currently open?
* How many critical tickets are unresolved?
* Which agent has the lowest average customer rating?
* What is the average customer rating for Technical category tickets?
* Show me all Critical tickets not resolved within 12 hours.
* Which agent resolved the most tickets?

The evaluator may use additional natural-language questions during the walkthrough.

## 7. Anomaly Detection

The system detects support-ticket anomalies using deterministic rules.

### Long Resolution Time

Resolution-time anomalies are identified using statistical outlier detection on resolved tickets.

The system examines `resolution_time_hrs` and flags unusually high values.

### Unresolved High-Priority Tickets

The system also identifies unresolved High and Critical priority tickets that are older than 24 hours.

For the static dataset, the age calculation is based on the latest timestamp available in the dataset.

## 8. REST API

The application provides REST API endpoints through FastAPI.

### Health Check

```text
GET /health
```

Used to verify that the API is running.

### Natural-Language Query

```text
POST /query
```

Accepts a natural-language question and returns the corresponding result.

Example:

```json
{
    "question": "How many critical tickets are unresolved?"
}
```

### Anomaly Detection

```text
GET /anomalies
```

Returns detected support-ticket anomalies.

### Statistics

```text
GET /stats
```

Returns basic dataset and system statistics.

## 9. Minimal UI

A Streamlit interface is provided for interactive use.

The UI allows the user to:

* Enter natural-language questions.
* View the LLM interpretation.
* View query results.
* Run anomaly detection.
* View dataset statistics.
* Check API health.

## 10. Technology Stack

* Python
* Pandas
* NumPy
* Scikit-learn
* FastAPI
* Uvicorn
* Streamlit
* LLM API / free-tier LLM
* Requests

## 11. Project Structure

```text
ai_support_system/
│
├── app/
│   ├── main.py
│   ├── data_loader.py
│   ├── query_engine.py
│   ├── anomaly_detector.py
│   ├── llm.py
│   └── streamlit_app.py
│
├── data/
│   └── support_tickets.csv
│
├── requirements.txt
├── README.md
└── .gitignore
```

## 12. Setup

### Clone the Repository

```bash
git clone <your-github-repository-url>
cd ai_support_system
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

### Configure the LLM

The LLM API key should be provided through an environment variable and should not be hard-coded in the source code.

Example:

```text
GROQ_API_KEY=your_api_key_here
```

Do not commit the actual API key to GitHub.

### Start the FastAPI Server

```bash
uvicorn app.main:app --reload
```

The API will be available locally.

### Start the Streamlit UI

```bash
streamlit run app/streamlit_app.py
```

## 13. Example Workflow

A typical request follows this process:

```text
User Question
      ↓
Streamlit / REST API
      ↓
LLM
      ↓
Structured Query Intent
      ↓
Validation
      ↓
Query Engine
      ↓
Pandas DataFrame
      ↓
Result
      ↓
User
```

This design keeps the LLM responsible for language understanding while keeping data processing controlled and reproducible.

## 14. Known Limitations

* The provided dataset does not contain a separate `resolved_at` timestamp.
* Therefore, questions that specifically require the calendar month of resolution cannot be determined exactly from the available data.
* For the static dataset, unresolved-ticket age is calculated relative to the latest `created_at` timestamp available in the dataset.
* The current system is designed as a prototype for the assessment and can be extended with a database, authentication, caching, logging, and additional query operations.

## 15. Future Improvements

Potential improvements include:

* Database integration for larger datasets.
* More comprehensive natural-language query coverage.
* Additional anomaly detection techniques.
* Authentication and authorization for the API.
* Structured logging and monitoring.
* Automated testing and CI/CD.
* Containerized deployment.
* More advanced LLM evaluation and fallback handling.

## 16. Assessment Requirements Covered

| Requirement                | Implementation                           |
| -------------------------- | ---------------------------------------- |
| CSV ingestion              | Pandas data loader                       |
| Natural-language questions | LLM + structured query intent            |
| Queryable data             | Query engine                             |
| Anomaly detection          | Statistical and rule-based detection     |
| REST API                   | FastAPI                                  |
| Minimal UI                 | Streamlit                                |
| LLM integration            | Free-tier/local LLM                      |
| Documentation              | README.md                                |
| Dependencies               | requirements.txt                         |
| Python                     | Entire application implemented in Python |
