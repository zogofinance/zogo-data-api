# Zogo Data API

# Quick Start:

Here’s a postman collection that can be useful:

- ADD JSON postman collection

Some environment variables to set:

- BASIC_USERNAME - 343434dev
- BASIC_PASSWORD - mysecretdev

Sample curl with test credentials:

```jsx
curl --location 'https://api.zogofinance.com/production/v1/integration/analytics/integration/analytics/all-user' \
--header 'Authorization: Basic ZGF0YWFwaXRlc3Q6ZGF0YWFwaXRlc3Q='
```

# API Reference

## General

### Base URL

All API routes should be appended to the following base URL: `https://api.zogofinance.com/production/v1/integration/analytics`

### Authentication

All API routes use basic authentication.

**Production:**

- Please get your credentials from your Zogo representative.

**Testing/Development:**

- Username: `dataapitest`
- Password: `dataapitest`

### Query Parameters:

<details>
<summary><b>start_date</b></summary>

- `YYYY-MM-DD` format, query starts at 00:00:00 of the given day
- if empty, will search from the earliest data point
  </details>

<details>
<summary><b>end_date</b></summary>

- `YYYY-MM-DD` format, query ends at 23:59:59 of the given day
- if empty, will search from the most recent data point
</details>

<details>
<summary><b>platform</b></summary>

- one of the following:
  - `web`
  - `digital_banking`
  - `standalone`
  - `integration`
- if empty, will search data from all platforms
</details>

<details>
<summary><b>user_group</b></summary>

- only applicable for `web` platform
- one of the following:
  - `unregistered`
  - `registered`
- if empty, will search data from both unregistered and registered users
  </details>

<details>
<summary><b>age_group</b></summary>

- only applicable for `standalone` and `digital_banking` platforms
- if empty, will search data from all users
- one of the following:
  - `teen` (13-17 years old)
  - `young_adult` (18-24 years old)
  - `adult` (25-34 years old)
  - `old_adult` (35+ years old)
  - `unknown`
  </details>

<details>
<summary><b>zip_codes</b></summary>

- only applicable for `standalone` platform
- if empty, will search data from all zip codes
- Javascript example:

  ```jsx
  const zip_codes = ["11111", "22222", "33333"];

  // Convert the array to a JSON string and include it in the query parameter
  const query_string = `zip_codes=${encodeURIComponent(
    JSON.stringify(zip_codes)
  )}`;

  // Append the query string to the URL
  const url = `https://api.zogofinance.com/integration/data?${query_string}`;
  ```

</details>

## API Routes

### GET `/all-user`

<details>
<summary>Details:</summary>

**Description:**

Get users (and their zip codes) who were created between the start and end date.

**Parameters:**

Optional

- `start_date`
- `end_date`
- `platform`
- `user_group`
- `age_group`
- `zip_codes`

**Example 200 Response:**

```json
{
  "total_user_count": 100,
  "zip_codes": [
    {
      "zip_code": "11111",
      "total_user_count": 1
    }
  ]
}
```

</details>

### GET `/active-user`

<details>
<summary>Details:</summary>

**Description:**

Get users who have logged in at least once between the start and end date.

**Parameters:**

Optional

- `start_date`
- `end_date`
- `platform`
- `user_group`
- `age_group`
- `zip_codes`

**Example 200 Response:**

```json
{
  "active_user_count": 100
}
```

</details>

### GET `/skill-completion`

<details>
<summary>Details:</summary>

**Description:**

Get skill data for user activity within the given start and end date.

**Parameters:**

Optional

- `start_date`
- `end_date`
- `platform`
- `user_group`
- `age_group`
- `zip_codes`

**Example 200 Response:**

```json
{
  "skills": [
    {
      "skill_id": 1,
      "skill_name": "Buying a house",
      "skill_status": "active", // can be "inactive"
      "category_name": "Savings and Spending",
      "category_id": 1,
      "active_user_count": 200,
      "completed_skill_user_count": 100,
      "question_accuracy": 98.1
    }
  ]
}
```

</details>

### GET `/skill-review`

<details>
<summary>Details:</summary>

**Description:**

Get skill data for user activity within the given start and end date. Pre-test accuracy and confidence may only be available for some skills.

**Parameters:**

Optional

- `start_date`
- `end_date`
- `platform`
- `age_group`
- `zip_codes`

**Example 200 Response:**

```json
{
  "skills": [
    {
      "skill_id": 1,
      "skill_name": "Buying a house",
      "skill_status": "active", // can be "inactive"
      "category_name": "Savings and Spending",
      "category_id": 1,
      "pre_test_accuracy": 75.6,
      "post_test_accuracy": 99.0,
      "pre_test_confidence": 75.0,
      "post_test_confidence": 99.1
    }
  ]
}
```

</details>

### GET `/module-completion`

<details>
<summary>Details:</summary>

**Description:**

Get module data for user activity within the given start and end date.

**Parameters:**

Optional

- `start_date`
- `end_date`
- `platform`
- `user_group`
- `age_group`
- `zip_codes`

**Example 200 Response:**

```json
{
  "modules": [
    {
      "module_id": 1,
      "module_name": "The Buying Process",
      "skill_id": 1,
      "skill_name": "Buying a house",
      "skill_status": "active", // can be "inactive"
      "category_name": "Savings and Spending",
      "category_id": 1,
      "module_status": "active", // can be "inactive"
      "active_user_count": 200,
      "completed_user_count": 100
    }
  ]
}
```

</details>

### GET `/pineapple-party-engagement`

<details>
<summary>Details:</summary>

**Description:**

Get pineapple party data for parties started within the given start and end date.

**Parameters:**

Optional

- `start_date`
- `end_date`
- `platform`
  - only applicable for `standalone` and `digital_banking` platforms
- `age_group`
- `zip_codes`

**Example 200 Response:**

```json
{
  "parties": [
    {
      "party_start_time": "2023-01-01 02:00:00",
      "party_end_time": "2023-01-01 02:00:00",
      "active_user_count": 200,
      "completed_user_count": 100
    }
  ]
}
```

</details>

### GET `/survey-result`

<details>
<summary>Details:</summary>

**Description:**

Get survey data answered within the given start and end date.

**Parameters:**

Optional

- `start_date`
- `end_date`
- `platform`
- `user_group`
- `age_group`
- `zip_codes`

**Example 200 Response:**

```json
{
  "survey_results": [
    {
      "question_id": 1,
      "question": "Who is not involved in buying a house?",
      "secondary_text": "Please select an option",
      "total_answer_count": 100,
      "answer_option_data": [
        {
          "answer_value": "Realtor",
          "answer_count": 1
        },
        {
          "answer_value": "Lender",
          "answer_count": 1
        },
        {
          "answer_value": "Dr. Seuss",
          "answer_count": 98
        }
      ]
    }
  ]
}
```

</details>

## Rate Limits

No rate limits
