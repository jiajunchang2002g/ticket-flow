# Cinema Ticket Booking v2

This project demonstrates safe seat booking under high concurrency.

## Frontend

The UI is built with [Vite](https://vite.dev/) (static webpage framework). Source files live in `frontend/src/`. Run `npm run build` inside `frontend/` to compile them into the `static/` directory, which the Go server serves.

For local UI development with hot-module replacement, run `npm run dev` inside `frontend/` and proxy API calls to the Go server (default `:8080`).

## Feature

- Supports concurrent booking requests.

![Booking demo](image.png)

## Problem

When multiple users try to book the same seat at the same time, race conditions can lead to duplicate bookings.

## Solution

Use Redis Lua scripts to atomically check and reserve seats with TTL.

- Hold script: checks that all requested seats are free, then holds all of them together.
- Confirm script: finalizes the held seats together for the same session.
- Result: supports many concurrent users and multi-seat requests without double-booking or partial success.

## Quick Start

1. Start Redis:

```bash
docker compose up -d redis
```

2. Build the frontend:

```bash
cd frontend
npm install
npm run build
cd ..
```

3. Run the app:

```bash
go run ./cmd
```

4. Open the app:

- UI: http://localhost:8080
- Redis Commander (optional): http://localhost:8081

5. Stop services when done:

```bash
docker compose down
```

## Demo

- Single-seat race: many goroutines try to book the same seat, and only 1 booking succeeds.
- Overlapping multi-seat race: many goroutines request overlapping seat groups, so some bookings succeed and some fail, with no partial success.

Notes: all credit(s) go to https://github.com/SelfMadeEngineerCode 