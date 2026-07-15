# WeLoveMovies — Backend

An Express + Knex REST API for the WeLoveMovies application.

## Tech stack

- **Express** — HTTP routing and middleware
- **Knex** — query builder, migrations, and seeds
- **PostgreSQL** — development/production database
- **SQLite (in-memory)** — test database
- **Jest + SuperTest** — route/integration tests
- **cors** — cross-origin access for the frontend

## Database

Five tables, created via migrations in dependency order so foreign-key parents
exist before the tables that reference them:

- `movies`, `theaters`, `critics` (base tables)
- `reviews` — references `critics` and `movies`
- `movies_theaters` — join table referencing `movies` and `theaters`

## Routes

| Method | Path | Description |
| ------ | ---- | ----------- |
| GET | `/movies` | All movies (add `?is_showing=true` to filter to currently showing) |
| GET | `/movies/:movieId` | A single movie (404 if not found) |
| GET | `/movies/:movieId/theaters` | Theaters playing that movie |
| GET | `/movies/:movieId/reviews` | Reviews for the movie, each with its `critic` |
| GET | `/theaters` | All theaters, each with a nested `movies` array |
| PUT | `/reviews/:reviewId` | Update a review (404 if not found) |
| DELETE | `/reviews/:reviewId` | Delete a review, returns 204 (404 if not found) |

Unknown routes return `404`; existing routes hit with an unsupported HTTP method
return `405`. Successful responses use a `data` key; errors use an `error` key.

## Scripts

```bash
npm start      # run the server
npm test       # run the Jest test suite
npx knex migrate:latest
npx knex seed:run
```

---

# Post-Submission Reflection

## Describe your problem-solving approach while building this capstone.  What steps did you take to understand the requirements, plan your solution, and implement the features? ###

I started by reading the provided documentation before writing any code.

Database: `docs/tables/` files defined the exact schema for the tables.
Routes:  `docs/routes/` files described the expected request/response shape for every
endpoint, including edge cases.
Test: I reviewed the Jest test suite to understand the expected behavior for each route.

From there I worked in layers, bottom-up:

1. **Database first.** I created the migrations to match the documented schema and verified that the seed data loaded correctly.
2. **Services next.** I implemented the Knex queries each route.
3. **Controllers and routers.** I connected the request handlers to the services, handled 404 and 405 responses, and mounted the routers.
4. **Cross-cutting concerns last.** I added CORS, a not-found handler, and a centralized error handler.

## Explain one key technical decision you made during your implementation.  Why did you choose this approach over other possible solutions? ###

To avoid duplicating code, I reused the same theaters and reviews routers for both top-level and nested routes by using Express's mergeParams: true. This allowed the controllers to detect whether a movieId was provided and return either movie-specific data or the complete list as needed.

## AI use disclosure  Did you use any AI tools at any stage while preparing or developing this capstone?

**Yes.** I used an AI for documentation and debugging test.

## What is one area of backend engineering that you want to strengthen based on your experience developing this project?

I want to continue improving my backend development and database skills, especially relational data modeling and writing efficient SQL/Knex queries. Strengthening these areas will help me build more scalable applications and better understand how to structure and retrieve data effectively.

## Optional industry question  How would you use AI tools ethically and effectively in real backend engineering workflows (for example, for debugging, documentation lookups, or brainstorming improvements)?

I'd use AI as an accelerator, not an authority. Concretely:

- **Debugging:** paste an error and the relevant code to get hypotheses faster,
  but confirm the fix by reproducing the bug and re-running tests myself.
- **Documentation:** I would use AI to quickly understand framework documentation or APIs while confirming important details with the official documentation.
