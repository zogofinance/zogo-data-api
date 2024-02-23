# Zogo Data API Reference

## General

### Base URL

All API routes should be appended to the following base URL: `https://api.zogofinance.com/production/v1/integration/analytics`

### Authentication

All API routes require a basic auth token.

To generate the Basic token, you will take the client_id and client_secret in “client_id:client_secret” format and encode it using base64 encoding. For testing you can also use the Basic Auth with your credentials in apps like Postman.

> 💡 This encoding can be done automatically with [this tool](https://www.debugbear.com/basic-auth-header-generator)

For testing/development you can use the following basic token: “Basic ZGF0YWFwaXRlc3Q6ZGF0YWFwaXRlc3Q=” (Note: there will be very little user data for this testing client.)

- client_id “dataapitest” and client_secret “dataapitest”

For production, please reach out to Zogo for production credentials.

### Query Parameters
The following query parameters can be appended to the API requests.  See the details of the individual API endpoint to see whether or not a particular query parameter is applicaple for that endpoint.

<details>
<summary><b>start_date<b/></summary>

- `YYYY-MM-DD` format, query starts at 00:00:00 UTC of the given day
- if empty, will search from the earliest data point
</details>

<details>
<summary><b>end_date<b/></summary>

- `YYYY-MM-DD` format, query ends at 23:59:59 UTC of the given day
- if empty, will search from the most recent data point
</details>

<details>
<summary><b>platform<b/></summary>

- one of the following:
  - `web`
  - `digital_banking`
  - `standalone`
  - `integration`
- if specified, will only include data for users of that platform
- if empty, will search data from all platforms
</details>

<details>
<summary><b>user_group<b/></summary>

- only applicable when platform is set to `web`, otherwise will error
- one of the following:
  - `unregistered`
  - `registered`
- if specified, will only include data for registered or unregistered users
- if empty, will search data from both unregistered and registered users
  </details>

<details>
<summary><b>age_group<b/></summary>

- only applicable when platform is set to either `standalone` or `digital_banking`, otherwise will error (no age data is collected for the other platforms)
- if empty, will search data from all users
- one of the following:
  - `teen` (13-17 years old)
  - `young_adult` (18-24 years old)
  - `adult` (25-40 years old)
  - `old_adult` (40+ years old)
  - `unknown` (this will include digital_banking users that have no age data)
  </details>

<details>
<summary><b>zip_codes<b/></summary>

- only applicable when platform is set to `standalone`, otherwise will error (zip code data is only collected for standalone)
- if empty, will search data from all zip codes
- JavaScript example:

  ```js
  const zip_codes = ["11111", "22222", "33333"];

  // Convert the array to a JSON string and include it in the query parameter
  const query_string = `zip_codes=${encodeURIComponent(
    JSON.stringify(zip_codes)
  )}`;

  // Append the query string to the URL
  const url = `https://api.zogofinance.com/integration/data?${query_string}`;
  ```

</details>


<details>
<summary><b>skill_id<b/></summary>

- for endpoints that return skill data, this allows you to specify returning data for a specific skill.
- see the content library in your Partner Portal to get the skill id for a specific skill.
- if empty, will search data for all skills.

</details>

<details>
<summary><b>module_id<b/></summary>

- for endpoints that return module data, this allows you to specify returning data for a specific module.
- see the content library in your Partner Portal to get the module id for a specific module.
- if empty, will search data for all modules.

</details>

<details>
<summary><b>page<b/></summary>

- required for any endpoint that supports pagination
- see the `page_count` property on the return object to see how many pages of data exist

</details>

## API Routes

### GET `/all-user`

<details>
<summary>Details:</summary>

**Description:**

Get users (and their zip codes) who were created between the start and end date.  There will only be zip code data if there are standalone users in the returned data set.

**Parameters:**

Optional:

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
  "zip_codes": [ // will be an empty array if no standalone users returned
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

Optional:

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

Get skill data for user activity within the given start and end date. If a `skill_id` is sent in the request, the data for that single skill will be returned. If there is no user activity for a particular skill, it will not be returned in the response.

**Parameters:**

Required:

- `page` (3 skills per page)

Optional:

- `skill_id`
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
      "completed_user_count": 100,
      "question_accuracy": 98.1
    },
    ...
  ],
  "page_count": 5
}
```

</details>

### GET `/skill-review`

<details>
<summary>Details:</summary>

**Description:**

Get skill data for users who completed skills within the given start and end date. If a `skill_id` is sent in the request, the data for that single skill will be returned. Only returns data for skills that have pre/post-tests and have at least one user who completed the skill. The `confidence_increase` reflects the percentage of users whose confidence increased between the pre-test and post test for that skill.

**Parameters:**

Required:

- `page` (~3 skills per page (could be less if some of the skills have no engagement) )

Optional:

- `skill_id`
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
      "pre_test_accuracy": 75.6,
      "post_test_accuracy": 99.0,
      "confidence_increase": 75.0
    }
  ],
  "page_count": 5
}
```

</details>

### GET `/module-completion`

<details>
<summary>Details:</summary>

**Description:**

Get module data for user activity within the given start and end date. If a `module_id` is sent in the request, the data for that single module will be returned. If there is no user activity for a partiuclar module, it will not be returned in the response.

**Parameters:**

Required:

- `page` (50 modules per page)

Optional:

- `module_id`
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
  ],
  "page_count": 5
}
```

</details>

### GET `/points-party-engagement`

<details>
<summary>Details:</summary>

**Description:**

Get points party data for parties started within the given start and end date.

**Parameters:**

Optional:

- `start_date`
- `end_date`
- `platform` (only applicable for `standalone` and `digital_banking` platforms)
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

Get survey data answered within the given start and end date. Response exludes any surveys that have no engagement.

**Parameters:**

Required:

- `page` (~10 surveys per page (could be less if surveys have no engagement))

Optional:

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
  ],
  "page_count": 5
}
```

</details>
