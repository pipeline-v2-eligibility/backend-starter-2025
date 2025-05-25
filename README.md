# 2025 Backend Assessment Starter

## Tracey Logging Backend API

This document describes the Tracey Logging API, designed for capturing and retrieving log entries. This API serves as a challenge for mid-level developers looking to enhance their skills in building robust backend services.

## Getting Started

To begin this challenge, please go to the following GitHub repository: [https://github.com/pipeline-v2-eligibility/backend-starter-2025](https://github.com/pipeline-v2-eligibility/backend-starter-2025). **Instead of cloning or forking, use the green **Use this template** button** to create your own repository. This is super importan. It will provide you with a necessary starting point for your solution to be evaluated.

## Features

Your implementation of the Tracey Logging API should support the following features:

* **Log Ingestion:** Allow new log entries to be created via a `POST` request to the `/logs` endpoint.
* **Log Retrieval:** Enable fetching existing log entries via a `GET` request to the `/logs` endpoint.
* **Filtering:** Support filtering logs by `action`, `userId`, `severity`, `source`, and a `timestamp` range (from and to).
* **Sorting:** Allow sorting logs by one or more fields: `timestamp`, `severity`, and `latency`. Sorting should support both ascending (`+`) and descending (`-`) order. The default sort order for `GET /logs` should be `timestamp` descending.
* **Data Validation:** Implement comprehensive data validation for all incoming requests, including adherence to `maxLength`, `enum`, `pattern`, and `format` constraints as defined in the OpenAPI schema.
* **Edge Case Handling:** Specifically, ensure that `POST` requests with future timestamps are rejected with a `400 Bad Request` error.
* **Rate Limiting:** The API should implement rate limiting, allowing a configurable number of requests per second for both `GET` and `POST` operations. Exceeding the rate limit should result in a `429 Too Many Requests` response. We expect you to rate limit to a **max of 5 requests per second**.

## API Endpoints

### 1. `POST /logs`

This endpoint allows for the creation of new log entries.

**Request:**

* **Method:** `POST`
* **Path:** `/logs`
* **Content-Type:** `application/json`
* **Body:** A JSON object representing a log entry.

    ```json
    {
        "action": "ORDER_SHIPPED",
        "timestamp": "{{$datetime iso8601}}",
        "userId": "u_{{$randomInt 100 1000}}",
        "severity": "info",
        "source": "backend",
        "meta": {
            "ip": "192.168.55.1",
            "latencyMs": {{randomInt 50 500}}
        }
    }
    ```

**Expected Responses:**

* **`201 Created`**: Log entry successfully created.
    ```json
    {
        "message": "Log created successfully."
    }
    ```
* **`400 Bad Request`**: Invalid payload or missing required fields. This also applies to future timestamps.
    ```json
    {
        "message": "Invalid request payload.",
        "code": "200001"
    }
    ```
* **`429 Too Many Requests`**: Rate limit exceeded.
* **`500 Internal Server Error`**: Unexpected server-side failure.

### 2. `GET /logs`

This endpoint allows for retrieving log entries with various filtering and sorting options.

**Request:**

* **Method:** `GET`
* **Path:** `/logs`
* **Content-Type:** `application/json`
* **Query Parameters:**
    * `action` (string, optional): Filter logs by a specific action.
        ```bash
        GET {{baseUrl}}/logs?action=ORDER_SHIPPED
        ```
    * `userId` (string, optional): Filter logs by a specific user ID.
    * `severity` (string, optional): Filter logs by severity level (`info`, `warning`, `error`, `critical`).
    * `source` (string, optional): Filter logs by source (`frontend`, `backend`, `IoT`, `cli`).
    * `timestamp[from]` (string, optional): Start of the timestamp range (inclusive), in ISO 8601 format.
    * `timestamp[to]` (string, optional): End of the timestamp range (inclusive), in ISO 8601 format.
    * `sort` (string, optional): Comma-separated list of sort expressions. Each field must be prefixed with `+` (ascending) or `-` (descending). Allowed fields are: `timestamp`, `severity`, and `latency`. Duplicate fields are not allowed. By default, logs are sorted by `timestamp` in descending order.
        ```bash
        GET {{baseUrl}}/logs?sort=%2Btimestamp%2C-latency
        ```
        This decodes to `+timestamp,-latency` which means sort by timestamp in ascending order then by latency in descending order.

**Expected Responses:**

* **`200 OK`**: Returns a list of log entries.
    ```json
    {
        "logs": [
            {
                "action": "ACTION_TWO",
                "timestamp": "2025-05-04T13:20:00Z",
                "userId": "u_2",
                "severity": "info",
                "source": "backend",
                "meta": {
                    "ip": "192.168.0.2",
                    "latencyMs": 200
                }
            }
        ]
    }
    ```
* **`400 Bad Request`**: Invalid query parameters or sort expression.
* **`429 Too Many Requests`**: Rate limit exceeded.
* **`500 Internal Server Error`**: Unexpected server-side failure.

## How to Submit

Your submission will be evaluated based on the code in your main branch or any pull requests raised against it.

* **Complete `about.json`**: You **are REQUIRED** to fill in your personal details in the `about.json` file located in the root of your repository. This file is crucial for identifying your submission.

## Some Hints
* **Deployment Hints**: For deployment, Google Cloud Run offers an "Always Free" tier that is suitable for this project. You can set up continuous deployment by creating a Dockerfile and configuring it to redeploy when you push code to your repository's main branch. A valuable resource to guide you through this process is the Google Cloud documentation's "Quickstart: Deploy a service to Cloud Run" which you can find by searching online.
* **Database Choice**: You have the flexibility to choose your database; SQLite is a viable option if you prefer.