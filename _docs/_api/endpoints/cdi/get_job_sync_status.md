---
nav_title: "GET: List Job Sync Status"
article_title: "GET: List Job Sync Status"
search_tag: Endpoint
page_order: 1
hidden: true
permalink: /api/cdi/get_job_sync/
layout: api_page
page_type: reference
description: "This article outlines details about the List job sync status Braze endpoint."

---
{% api %}
# List job sync status
{% apimethod get %}
/cdi/integrations/{integration_id}/job_sync_status
{% endapimethod %}

> Use this endpoint to return a list of past sync statuses for a given integration.

{% alert important %}
This endpoint is currently in early access. Contact your customer success manager if you're interested in participating in this early access.
{% endalert %}

{% alert note %}
To use this endpoint, you'll need to generate an API key with the `cdi.integration_job_status` permission.
{% endalert %}

## Rate limit

{% multi_lang_include rate_limits.md endpoint='cdi job sync status' %}

## Path parameters

| Parameter | Required | Data Type | Description |
|---|---|---|---|
| `integration_id` | Required | String | Integration ID. |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3 .reset-td-br-4}

## Query parameters

Each call to this endpoint will return 10 items. For an integration with more than 10 syncs, use the `Link` header to retrieve the data on the next page as shown in the following example response.

| Parameter | Required | Data Type | Description |
|---|---|---|---|
| `cursor` | Optional | String | Determines the pagination of the sync status. |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3 .reset-td-br-4}

## Example request

### Without cursor

```
curl --location --request GET 'https://rest.iad-03.braze.com/cdi/integrations/00000000-0000-0000-0000-000000000000/job_sync_status' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR-REST-API-KEY'
```

### With cursor

```
curl --location --request GET 'https://rest.iad-03.braze.com/cdi/integrations/00000000-0000-0000-0000-000000000000/job_sync_status?cursor=c2tpcDow' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR-REST-API-KEY'
```

## Response

### Example success response

The status code `200` could return the following response body.

{% alert note %}
The `Link` header won't exist if there are less than or equal to 10 syncs in total. For calls without a cursor, `prev` will not show. When looking at the last page of items, `next` will not show.
{% endalert %}

```
Link: </cdi/integrations/00000000-0000-0000-0000-000000000000/job_sync_status?cursor=c2tpcDow>; rel="prev",</cdi/integrations00000000-0000-0000-0000-000000000000/job_sync_status?cursor=c2tpcDoxMDA=>; rel="next"
```

```json
{
  "results": [
    {
        "job_status": (string) status of the sync, see below for explanation of different statuses,
        "sync_start_time": (string) time the sync started in ISO 8601,
        "sync_finish_time": (string) time the sync finished in ISO 8601,
        "last_timestamp_synced": (string) last UPDATED_AT timestamp processed by the sync in ISO 8601,
        "rows_synced": (integer) number of rows successfully synced to Braze,
        "rows_failed_with_errors": (integer) number of rows failed because of errors,
    },
  ],
  "message": "success"
}
```

| job_status | Explanation |
| --- | --- |
| `Running` | The job is currently running. |
| `Success` | All rows synced successfully. |
| `Partial` | Some rows failed to sync due to errors. |
| `Error` | No rows were synced. |
{: .reset-td-br-1 .reset-td-br-2}

## Troubleshooting

The following table lists possible returned errors and their associated troubleshooting steps.

| Error | Troubleshooting |
| --- | --- |
| `400 Invalid cursor` | Check that your `cursor` is valid. |
| `400 Invalid integration ID` | Check that your `integration_id` is valid. |
{: .reset-td-br-1 .reset-td-br-2}

For additional status codes and associated error messages, please refer to [Fatal errors & responses]({{site.baseurl}}/api/errors/#fatal-errors).

{% endapi %}
