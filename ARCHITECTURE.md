# Supporting Layers — Refactor Notes

This project was refactored to separate three concerns that were previously
scattered across route files, services, and middleware.

## validators/
Holds express-validator chains, declared once and exported by name.
`routes/articles.js` imports these chains instead of defining its own —
`createArticle` and `updateArticle` in `validators/article.validator.js`.

## utils/
Pure, reusable helpers with no dependency on routes, controllers, or services:
- `AppError.js` — custom Error subclass carrying `statusCode` and `isOperational`.
- `asyncHandler.js` — wraps an async route handler so rejected promises are
  forwarded to `next()`, removing repetitive try/catch blocks.
- `validateRequest.js` — reads `validationResult(req)` and calls
  `next(new AppError('Validation failed', 422))` when a validator chain fails.

## config/
`config/index.js` is the single place in the project that reads `process.env`.
It exports `{ port, nodeEnv, jwtSecret, maxArticles }`, with sane defaults
for values that are safe to omit locally. `app.js` and
`services/articlesService.js` import from `config` instead of reading
`process.env` directly.

## Environment variables
See `.env.example` for the full list of variables `config/index.js` reads.
Copy it to `.env` for local development — `.env` itself is gitignored and
must never be committed.
