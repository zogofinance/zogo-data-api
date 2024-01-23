# Zogo Data API

## Quick Start:

Here’s a postman collection that can be useful:

- ADD JSON postman collection

Some environment variables to set:

- BASIC_USERNAME - 343434dev
- BASIC_PASSWORD - mysecretdev

sample curl with test credentials:

```jsx
curl --location 'https://api.zogofinance.com/production/v1/integration/analytics/integration/analytics/all-user' \
--header 'Authorization: Basic ZGF0YWFwaXRlc3Q6ZGF0YWFwaXRlc3Q='
```

### Authentication

All API routes use basic authentication.

Production: please get your credentials from your Zogo representative.

Test credentials:

- username: dataapitest
- password: dataapitest

### Query Parameter Explanations:

<details>
start_date:
  - YYYY-MM-DD format, query starts at 00:00:00 of the given day
  - if empty, will search from the earliest data point
  </details>

- end_date
  - YYYY-MM-DD format, query ends at 23:59:59 of the given day
  - if empty, will search from the most recent data point
- platform
  - one of:
    - web
    - digital_banking
    - standalone
    - integration
  - if empty, will search data from all platforms
- user_group
  - only applicable for “web” platform
  - if empty, will search data from both unregistered and registered users
  - one of:
    - unregistered
    - registered
- age_group
  - only applicable for “standalone” and “digital_banking” platforms
  - if empty, will search data from all users
  - one of:
    - teen
      - 13-17 years old
    - young_adult
      - 18-24 years old
    - adult
      - 25-34 years old
    - old_adult
      - 35+
    - unknown
- zip_codes

  - only applicable for “standalone” platform
  - if empty, will search data from all zip codes
  - string that is `encodeURIComponent` and JSON.stringify of an array. js example:

  ```jsx
  const zip_codes = ["11111", "22222", "33333"];

  // Convert the array to a JSON string and include it in the query parameter
  const query_string = `zip_codes=${encodeURIComponent(
    JSON.stringify(zip_codes)
  )}`;

  // Append the query string to the URL
  const url = `https://api.zogofinance.com/integration/data?${query_string}`;
  ```

# API Reference

## General

### API Base URL

All API routes should be appended to the following base URL: `https://api.zogofinance.com/production/v1/integration/analytics`

## API Routes

### GET `/all-user`

<details>
<summary>Details:</summary>

**Description:**

Get users (and their zip codes) who were created between the start and end date.

User Access Tokens need to be generated and passed to the iframe to authenticate the user.

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

- GET /active-user
  Description - get users who have logged in at least once between the start and end date.
  Parameters:
  - Optional Parameters
    - `start_date`
    - `end_date`
    - `platform`
    - `user_group`
    - `age_group`
    - `zip_codes`
      Example 200 Response:
  ```jsx
  {
  	active_user_count: 100,
  }
  ```
- GET /skill-completion
  Description - get skill data for activity within the given start and end date
  Parameters:
  - Optional Parameters
    - `start_date`
    - `end_date`
    - `platform`
    - `user_group`
    - `age_group`
    - `zip_codes`
      Example 200 Response:
  ```jsx
  {
    skills: [
      {
        skill_id: 1,
        skill_name: "Buying a house",
        skill_status: "active", // can be "inactive"
        category_name: "Savings and Spending",
        category_id: 1,
        active_user_count: 200,
        completed_skill_user_count: 100,
        question_accuracy: 98.1,
      },
    ];
  }
  ```
- GET /skill-review
  Description - get skill data for activity within the given start and end date. test accuracy and confidence is only be available for some skills.
  Parameters:
  - Optional Parameters
    - `start_date`
    - `end_date`
    - `platform`
    - `age_group`
    - `zip_codes`
    ```jsx
    {
      skills: [
        {
          skill_id: 1,
          skill_name: "Buying a house",
          skill_status: "active", // can be "inactive"
          category_name: "Savings and Spending",
          category_id: 1,
          pre_test_accuracy: 75.6,
          post_test_accuracy: 99.0,
          pre_test_confidence: 75.0,
          post_test_confidence: 99.1,
        },
      ];
    }
    ```
    Example 200 Response:
- GET /module-completion
  Description - get module data for activity within the given start and end date
  Parameters:
  - Optional Parameters
    - `start_date`
    - `end_date`
    - `platform`
    - `user_group`
    - `age_group`
    - `zip_codes`
      Example 200 Response:
  ```jsx
  {
  	modules: [
  		{
  			module_id: 1,
  			module_name: "The Buying Process",
  			skill_id: 1,
  			skill_name: "Buying a house",
  			skill_status: "active", // can be "inactive"
  			category_name: "Savings and Spending",
  			category_id: 1,
  			module_status: "active", // can be "inactive"
  			active_user_count: 200
  			completed_user_count: 100
  		}
  	]
  }
  ```
- GET /pineapple-party-engagement
  Description - get pineapple party data for parties started within the given start and end date
  Parameters:
  - Optional Parameters
    - `start_date`
    - `end_date`
    - `platform`
      - only applicable for standalone and digital banking platforms
    - `age_group`
    - `zip_codes`
      Example 200 Response:
  ```jsx
  {
  	parties: [
  		{
  			party_start_time: “2023-01-01 02:00:00”,
  			party_end_time: “2023-01-01 02:00:00”,
  			active_user_count: 200
  			completed_user_count: 100
  		}
  	]
  }
  ```
- GET /survey-result
  Description - get survey data answered within the given start and end date
  Parameters:
  - Optional Parameters
    - `start_date`
    - `end_date`
    - `platform`
    - `user_group`
    - `age_group`
    - `zip_codes`
      Example 200 Response:
  ```jsx
  {
    survey_results: [
      {
        question_id: 1,
        question: "Who is not involved in buying a house?",
        secondary_text: "Please select an option",
        total_answer_count: 100,
        answer_option_data: [
          {
            answer_value: "Realtor",
            answer_count: 1,
          },
          {
            answer_value: "Lender",
            answer_count: 1,
          },
          {
            answer_value: "Dr. Seuss",
            answer_count: 98,
          },
        ],
      },
    ];
  }
  ```

### Rate Limits:

No rate limits
