# Testing

Use synthetic data only. Do not include real client data or credentials in saved executions or screenshots.

## Scenario 1: new lead

Send `examples/valid-lead.json`.

Expected:

- HTTP `201`;
- `success: true`;
- `duplicate: false`;
- `created: true`;
- a contact and opportunity exist in HighLevel;
- Service Interest and External Lead ID are populated.

## Scenario 2: repeated delivery

Send the identical payload again, without changing `externalLeadId`.

Expected:

- HTTP `200`;
- `success: true`;
- `duplicate: true`;
- `created: false`;
- the returned opportunity ID matches the first request;
- no additional opportunity is created.

## Scenario 3: different enquiry from the same contact

Keep the email and phone from the valid payload but change `externalLeadId` and `serviceInterest`.

Expected:

- the existing contact is reused;
- HTTP `201`;
- a second, legitimate opportunity is created.

## Scenario 4: invalid payload

Send `examples/invalid-lead.json`.

Expected:

- HTTP `400`;
- `success: false`;
- `error: VALIDATION_ERROR`;
- no HighLevel API request is made.

## Scenario 5: HighLevel failure

Run only in a controlled test environment. A missing scope or unavailable upstream API should follow the error output and return:

- HTTP `502`;
- `success: false`;
- `error: HIGHLEVEL_API_ERROR`;
- the failing node name and a safe error message.

Do not publish screenshots that expose tokens, credential screens, webhook domains, production URLs, real contact data, or HighLevel resource IDs.
