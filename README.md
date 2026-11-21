# JoobQ SDK for PHP

A thin worker and producer client for the JoobQ SDK gateway of a self-hosted JoobQUI server: register jobs and
workflow blueprints, run their tasks with retries, heartbeats and graceful shutdown, and enqueue jobs (up to 500 in one
call with `enqueueMany`) or start workflows. With JoobQ Pro on the server: batches with completion callbacks (`createBatch`,
`getBatch`) and a shared sliding-window rate limit (`rateLimit`).
JoobQUI version 0.8.0; an SDK talks to a server of its own version or later.

- Requirements: PHP 8.1 or newer with the `curl` and `json` extensions; `pcntl` for graceful shutdown.
- Install: `composer require azutoolkit/joobq`.
- Use: the namespace `JoobQ`, loaded by Composer's autoloader (`require 'vendor/autoload.php'`).
- Run: boot your application, then start `JoobQWorker` headless in a process of its own; handlers use the application's
  own services. With `pcntl` and `posix` loaded the worker always forks a pool (whatever `concurrency` is), each pool
  process a copy of the application made at the fork: open connections lazily or in `onProcessStart: fn () => ...`,
  which each pool process calls once before it takes tasks (like Resque's `after_fork`). When it throws, that process
  logs `joobq: onProcessStart failed: <message>` and ends without taking a task; its replacement starts after a pause
  that doubles from 0.25 s to 5 s, and after 5 such processes in a row `run()` throws. Without `pcntl` or `posix` the
  worker runs one task at a time in its own process and does not call `onProcessStart`.
- Connect: the server's origin and its SDK token, read by the examples from `JOOBQ_GATEWAY_URL` and `JOOBQ_SDK_TOKEN`.
- Documentation: https://www.joobq.io/docs/develop/ (workers, jobs, workflows, the gateway contract).

More about JoobQ: https://www.joobq.io

## License

MIT (see `LICENSE`).
