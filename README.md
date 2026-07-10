# Automated Inventory ATS Load Tester
A Python-based ETL and API orchestration tool built within Google Colab designed to validate, stress-test, and update downstream inventory systems using production-grade catalog structures. The script extracts real-world catalog keys, synthesizes isolated test scenarios, coordinates OAuth2 authentication, and securely synchronizes data to final endpoints with strict error handling.

## Purpose
Before shipping major data syncs to a live ecosystem, it is critical to observe how systems perform under continuous bulk mutations. This utility queries dynamic upstream catalog data to extract structural identifiers (UPCs), maps them against controlled mock variables (locations and quantities), and builds a standardized testing suite to safely evaluate the target system's performance, constraints, and operational bounds.

## Key Features & Architecture
Deep JSON Extraction (Task 1): Connects to an upstream catalog export endpoint using secure API key credentials pulled from Google Colab's secrets management. It parses a deeply nested response structure (ApiDocument -> Response -> Styles -> Items) to compile a flat list of actual catalog production UPCs.

Data Synthesis & Formatting (Task 2): Leverages pandas to stitch together the real UPCs with a localized, random sample pool of location IDs and randomized delta quantities ranging from -10 to 10. The output is formatted and stored as a clean, local testing artifact (inventory_data.csv).

OAuth2 Credentials Flow (Task 3): Handles server-to-server token requests using an application/x-www-form-urlencoded POST layout, retrieving short-lived Bearer tokens safely to authorize the final push.

Controlled Synchronization Loop (Task 4): Reads the testing artifact row-by-row and transforms each record into a structured payload required by the target system.

Fail-Fast Circuit Breaker Rule: Features strict condition monitoring. If the target system yields a critical failure code (400 Bad Request or 444 Connection Closed Without Response), the synchronizer immediately aborts the pipeline to protect system health and prevent cascading errors.

Rate Limit Pacing: Features a deliberate sleep cycle delay (0.2s) to ensure consistent, stable throughput that avoids triggering server-side DDoS/Overload boundaries.

## Technologies Used
Python 3

Pandas: For structural dataframe creation and CSV compilation.

Requests: For synchronous API communication (POST methods for both JSON payloads and Form URL-encoded headers).

Google Colab Secrets: Used to securely isolate sensitive production credentials (MY_API_KEY) from version control.
