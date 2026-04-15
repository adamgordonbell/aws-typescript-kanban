# Stage 0

Stage 0 is the local version of the workshop architecture. It keeps the same boundaries as the final AWS stage:

- static frontend
- public BFF
- private backend
- external events entering through a public webhook
- asynchronous processing via a local queue

## Commands

```bash
npm run build
npm run reset:data
npm run start:local
```

Then open `http://127.0.0.1:7070`.

`npm run test:local` verifies the same compiled board, queue, and async-processing logic without opening ports. That keeps verification runnable in restricted environments.

## Demo levers

- `CHAOS_SYNC_PROBABILITY=0.35 npm run start:local`
- `CHAOS_ASYNC_PROBABILITY=0.5 npm run start:local`

The BFF retries backend `5xx` responses. The backend worker retries queue messages and moves poison messages to `data/dead-letter-queue.json`.
