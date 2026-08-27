# Setup

## Prerequisites

- An n8n instance.
- A HighLevel sub-account.
- A HighLevel Private Integration Token with these scopes:
  - `contacts.readonly`
  - `contacts.write`
  - `opportunities.readonly`
  - `opportunities.write`
- An existing opportunity pipeline and stage.
- Two opportunity custom fields:
  - `Service Interest`
  - `External Lead ID`

`External Lead ID` should be a text field. If `Service Interest` is a dropdown, incoming values must match configured options.

## Import

Import `workflows/highlevel-lead-intake.json` into n8n. The public template is disabled and contains no credentials or real HighLevel resource IDs.

## Configure the HighLevel credential

Create an n8n **HTTP Header Auth** credential:

```text
Name: Authorization
Value: Bearer YOUR_PRIVATE_INTEGRATION_TOKEN
```

Attach it to all four HighLevel HTTP Request nodes:

- `Find Duplicate Contact`
- `Create Contact`
- `Search Opportunity by External ID`
- `Create Opportunity`

Never paste the token into a Code node, URL, sticky note, example, screenshot, or exported JSON.

## Configure resource IDs

In `Normalize and Validate`, replace:

```javascript
locationId: 'YOUR_GHL_LOCATION_ID',
pipelineId: 'YOUR_GHL_PIPELINE_ID',
stageId: 'YOUR_GHL_STAGE_ID',
serviceInterestFieldId: 'YOUR_SERVICE_INTEREST_FIELD_ID',
externalLeadIdFieldId: 'YOUR_EXTERNAL_LEAD_ID_FIELD_ID',
```

Use the custom field's actual `id`, not its `fieldKey` such as `opportunity.external_lead_id`.

## HighLevel opportunity setting

Enable **Allow Multiple Opportunities per Contact** in the HighLevel sub-account. The workflow will then allow different External Lead IDs for the same contact while rejecting repeated delivery of the same External Lead ID.

## Activate

Run the tests in `docs/testing.md` using the n8n test webhook. After all scenarios pass, activate the workflow and switch the caller to the production webhook URL.
