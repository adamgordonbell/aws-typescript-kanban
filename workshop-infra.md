# Workshop Infrastructure Notes

This repo uses a staged infrastructure layout. The application code is shared; the infrastructure changes by stage.

## Stage Folders

- `infra-basic/`
  - first public AWS deployment
  - public BFF
  - public backend
  - DynamoDB persistence
  - S3 website hosting for the frontend

- `infra-private/`
  - public BFF
  - backend moved behind internal HTTP
  - VPC, private subnets, internal ALB
  - same shared app code as the basic stage

- `infra-events/`
  - everything in the private stage
  - plus webhook ingestion
  - SQS + DLQ
  - worker Lambda

## Why This Shape

The repo is organized to teach architectural changes, not to maximize code reuse inside Pulumi itself.

The key principle is:
- shared app
- staged infrastructure
- minimal stage-specific runtime wiring

## Shared Runtime Assumptions

All AWS stages assume:
- `npm run build` has been run
- `dist/` exists
- `node_modules/` exists for Lambda packaging
- `data/` exists for seed state

## Notes On The Three AWS Stages

### `infra-basic`

This is intentionally imperfect. It is there to make the flaw visible:
- the backend is public
- the BFF can be bypassed

That gives the workshop a concrete reason to move to the next stage.

### `infra-private`

This is the security and architecture-hardening step:
- browser can only reach the BFF
- BFF calls backend over internal HTTP
- backend is no longer directly internet-accessible

This preserves the future path to ECS because the contract remains HTTP.

### `infra-events`

This is the async integration step:
- external webhooks post to a public ingress
- ingress pushes to SQS
- worker consumes from SQS
- failed messages can land in the DLQ

This makes the webhook story resilient and easier to reason about.
