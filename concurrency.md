# Concurrency & Rate Limiting

Understanding how API v3 handles concurrent requests and how to implement proper client-side handling.

## Overview

All API v3 endpoints are configured with **reserved concurrency = 1**. This means each Lambda function processes requests **sequentially, one at a time**.

This configuration ensures data consistency, prevents race conditions, and maintains predictable behavior across all operations.

## Your Plan's Rate Limit

Every account carries an **API rate limit**: how many calls it may make in any
sixty second window, across every endpoint. It is part of your subscription
plan and it is reported as `MaxApiCallsPerMinute` by `POST /account/plan`.
Unless your plan says otherwise, it is **150 calls per minute**.

It is a **rate, not an allowance**. It never resets, it is never "spent", and
it is a different limit from `MaxApiCallsAllowed`, which is the monthly total.
Both apply: staying inside your monthly total does not let you spend it all in
one minute.

Going over it returns **`429 Too Many Requests`** with a `Retry-After: 60`
header and a message naming your limit:

```json
{
  "Response": false,
  "Message": "Rate limit exceeded. Your plan allows 150 API calls per minute. Wait a few seconds and try again.",
  "Code": 429
}
```

**A refused call is not charged to you.** It is never written to the API log,
so it does not consume your monthly `MaxApiCallsAllowed` allowance.

This limit applies **per account**, so spreading your traffic across several
servers or addresses does not raise it. It also sits alongside the platform
firewall, which limits by source address instead and answers separately — one
does not replace the other.

If your integration genuinely needs a higher rate, contact support: it is a
plan setting and can be raised.

## HTTP Status Codes

When implementing your client, handle these status codes appropriately:

| Status Code | Meaning |
|-------------|---------|
| `200 OK` | Request processed successfully |
| `429 Too Many Requests` | Your plan's per-minute rate limit was exceeded — back off and retry. Not charged against your monthly allowance |
| `503 Service Unavailable` | Lambda is processing another request — retry with backoff |
| `401 Unauthorized` | Invalid or missing API key |

## Implementation Guidelines

### 1. Implement Retry Logic with Exponential Backoff

When you receive a 429 or 503 response, implement exponential backoff retry logic.

```javascript
/* JavaScript Example */

const delay = (_ms) => new Promise(resolve => setTimeout(resolve, _ms));

async function apiCallWithRetry(_url, _options, _maxRetries = 3) {

	for (let i = 0; i < _maxRetries; i++) {

		try {

			const response = await fetch(_url, _options);

			/* Handle Rate Limiting */
			if (response.status === 429 || response.status === 503) {

				const waitTime = Math.pow(2, i) * 1000; // 1s, 2s, 4s...

				await delay(waitTime);

				continue;
			}

			return response;

		} catch (_error) {

			if (i === _maxRetries - 1) throw _error;

			await delay(Math.pow(2, i) * 1000);
		}
	}
}
```

### 2. Process Requests Sequentially

For bulk operations, process requests one at a time instead of sending them in parallel.

```javascript
/* JavaScript Example */

const results = [];

for (const item of items) {

	const response = await apiCallWithRetry('https://api-v3.sweeppea.com/participants/add', {
		method  : 'POST',
		headers : {
			'Authorization' : `Bearer ${apiKey}`,
			'Content-Type'  : 'application/json'
		},
		body : JSON.stringify(item)
	});

	const data = await response.json();

	results.push(data);
}
```

### 3. Python Implementation

```python
import time
import requests

def api_call_with_retry(url, headers, method='GET', data=None, max_retries=3):

	for attempt in range(max_retries):

		try:

			if method == 'GET':
				response = requests.get(url, headers=headers)
			else:
				response = requests.post(url, headers=headers, json=data)

			# Handle Rate Limiting
			if response.status_code in [429, 503]:

				wait_time = 2 ** attempt  # 1s, 2s, 4s...

				time.sleep(wait_time)

				continue

			return response

		except Exception as error:

			if attempt == max_retries - 1:
				raise error

			time.sleep(2 ** attempt)
```

## Best Practices

- Use exponential backoff for retries (1s, 2s, 4s, 8s...)
- Handle 429 and 503 status codes gracefully
- Process bulk operations sequentially, not in parallel
- Avoid sending multiple simultaneous requests to the same endpoint
- Implement request queuing on the client side
- Monitor response times and adjust your retry strategy
