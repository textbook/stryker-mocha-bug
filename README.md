# Stryker Mocha Runner Bug

The v10 Mocha Runner declares support for Mocha up to but not including v13:

```shell
$ jq '{version, peerDependencies}' ./node_modules/@stryker-mutator/mocha-runner/package.json
{
  "version": "10.0.0",
  "peerDependencies": {
    "@stryker-mutator/core": "10.0.0",
    "mocha": ">= 7.2 < 13"
  }
}
```

## Mocha v11

With Mocha v11, tests and mutation both pass:

```shell
$ npx mocha --version
11.8.0
$ npm test

> @textbook/stryker-mocha-bug@1.0.0 test
> mocha



  ✔ has a passing test

  1 passing (0ms)

$ npm run mutate

> @textbook/stryker-mocha-bug@1.0.0 mutate
> stryker run

15:54:59 (37399) WARN ProjectReader Warning: No files found for mutation with the given glob expressions. As a result, a dry-run will be performed without actually modifying anything. If you intended to mutate files, please check and adjust the configuration. Current glob pattern(s) used: "{src,lib}/**/!(*.+(s|S)pec|*.+(t|T)est).+(cjs|mjs|js|ts|mts|cts|jsx|tsx|html|vue|svelte)" and "!{src,lib}/**/__tests__/**/*.+(cjs|mjs|js|ts|mts|cts|jsx|tsx|html|vue|svelte)". To enable file mutation, consider configuring the `mutate` property in your configuration file or using the --mutate option via the command line.
15:54:59 (37399) INFO Instrumenter Instrumented 0 source file(s) with 0 mutant(s)
15:54:59 (37399) INFO ConcurrencyTokenProvider Creating 17 test runner process(es).
15:55:00 (37399) INFO DryRunExecutor Starting initial test run (mocha test runner with "perTest" coverage analysis). This may take a while.
15:55:00 (37399) INFO DryRunExecutor Initial test run succeeded. Ran 1 tests in 0 seconds (net 0 ms, overhead 3 ms).

All tests/index.test.js
  ✘ has a passing test (covered 0)

Ran NaN tests per mutant on average.
----------|------------------|----------|-----------|------------|----------|----------|
          | % Mutation score |          |           |            |          |          |
File      |  total | covered | # killed | # timeout | # survived | # no cov | # errors |
----------|--------|---------|----------|-----------|------------|----------|----------|
All files |    n/a |     n/a |        0 |         0 |          0 |        0 |        0 |
----------|--------|---------|----------|-----------|------------|----------|----------|
15:55:00 (37399) INFO HtmlReporter Your report can be found at: file:///path/to/stryker-mocha-bug/reports/mutation/mutation.html
15:55:00 (37399) INFO MutationTestExecutor Done in 0 seconds.
```

## Mocha v12

With Mocha v12, while the tests themselves are fine, mutation fails:

```shell
$ npx mocha --version
12.0.3
$ npm test

> @textbook/stryker-mocha-bug@1.0.0 test
> mocha



  ✔ has a passing test

  1 passing (1ms)

$ npm run mutate

> @textbook/stryker-mocha-bug@1.0.0 mutate
> stryker run

15:56:12 (38555) WARN PluginLoader Error during loading "@stryker-mutator/mocha-runner" plugin:
  Cannot find module '/path/to/stryker-mocha-bug/node_modules/mocha/lib/cli/run-helpers'
Require stack:
- /path/to/stryker-mocha-bug/package.json
15:56:12 (38555) WARN ProjectReader Warning: No files found for mutation with the given glob expressions. As a result, a dry-run will be performed without actually modifying anything. If you intended to mutate files, please check and adjust the configuration. Current glob pattern(s) used: "{src,lib}/**/!(*.+(s|S)pec|*.+(t|T)est).+(cjs|mjs|js|ts|mts|cts|jsx|tsx|html|vue|svelte)" and "!{src,lib}/**/__tests__/**/*.+(cjs|mjs|js|ts|mts|cts|jsx|tsx|html|vue|svelte)". To enable file mutation, consider configuring the `mutate` property in your configuration file or using the --mutate option via the command line.
15:56:12 (38555) INFO Instrumenter Instrumented 0 source file(s) with 0 mutant(s)
15:56:12 (38555) INFO ConcurrencyTokenProvider Creating 17 test runner process(es).
15:56:13 (38555) ERROR Stryker Unexpected error occurred while running Stryker StrykerError: Error: Could not inject [class ChildProcessTestRunnerWorker]. Cause: Cannot find TestRunner plugin "mocha". In fact, no TestRunner plugins were loaded. Did you forget to install it?
Error: Could not inject [class ChildProcessTestRunnerWorker]. Cause: Cannot find TestRunner plugin "mocha". In fact, no TestRunner plugins were loaded. Did you forget to install it?
    at InjectionError.create (file:///path/to/stryker-mocha-bug/node_modules/typed-inject/dist/src/errors.js:53:20)
    at ClassProvider.injectClass (file:///path/to/stryker-mocha-bug/node_modules/typed-inject/dist/src/InjectorImpl.js:45:34)
    at ChildProcessProxyWorker.handleInit (file:///path/to/stryker-mocha-bug/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy-worker.js:75:41)
    at ChildProcess.<anonymous> (file:///path/to/stryker-mocha-bug/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy.js:132:39)
    at ChildProcess.emit (node:events:509:28)
    at emit (node:internal/child_process:972:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:91:21) {
  innerError: undefined
}
15:56:13 (38555) INFO Stryker This might be a known problem with a solution documented in our troubleshooting guide.
15:56:13 (38555) INFO Stryker You can find it at https://stryker-mutator.io/docs/stryker-js/troubleshooting/
15:56:13 (38555) INFO Stryker Still having trouble figuring out what went wrong? Try `npx stryker run --fileLogLevel trace --logLevel debug` to get some more info.
15:56:13 (38555) WARN ChildProcessTestRunnerProxy An unexpected error occurred during test runner disposal. This might be worth looking into. Stryker will ignore this error. StrykerError: Error: Could not inject [class ChildProcessTestRunnerWorker]. Cause: Cannot find TestRunner plugin "mocha". In fact, no TestRunner plugins were loaded. Did you forget to install it?
Error: Could not inject [class ChildProcessTestRunnerWorker]. Cause: Cannot find TestRunner plugin "mocha". In fact, no TestRunner plugins were loaded. Did you forget to install it?
    at InjectionError.create (file:///path/to/stryker-mocha-bug/node_modules/typed-inject/dist/src/errors.js:53:20)
    at ClassProvider.injectClass (file:///path/to/stryker-mocha-bug/node_modules/typed-inject/dist/src/InjectorImpl.js:45:34)
    at ChildProcessProxyWorker.handleInit (file:///path/to/stryker-mocha-bug/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy-worker.js:75:41)
    at ChildProcess.<anonymous> (file:///path/to/stryker-mocha-bug/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy.js:132:39)
    at ChildProcess.emit (node:events:509:28)
    at emit (node:internal/child_process:972:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:91:21) {
  innerError: undefined
}
15:56:13 (38555) WARN ChildProcessTestRunnerProxy An unexpected error occurred during test runner disposal. This might be worth looking into. Stryker will ignore this error. StrykerError: Error: Could not inject [class ChildProcessTestRunnerWorker]. Cause: Cannot find TestRunner plugin "mocha". In fact, no TestRunner plugins were loaded. Did you forget to install it?
Error: Could not inject [class ChildProcessTestRunnerWorker]. Cause: Cannot find TestRunner plugin "mocha". In fact, no TestRunner plugins were loaded. Did you forget to install it?
    at InjectionError.create (file:///path/to/stryker-mocha-bug/node_modules/typed-inject/dist/src/errors.js:53:20)
    at ClassProvider.injectClass (file:///path/to/stryker-mocha-bug/node_modules/typed-inject/dist/src/InjectorImpl.js:45:34)
    at ChildProcessProxyWorker.handleInit (file:///path/to/stryker-mocha-bug/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy-worker.js:75:41)
    at ChildProcess.<anonymous> (file:///path/to/stryker-mocha-bug/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy.js:132:39)
    at ChildProcess.emit (node:events:509:28)
    at emit (node:internal/child_process:972:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:91:21) {
  innerError: undefined
}
node:internal/process/promises:394
    triggerUncaughtException(err, true /* fromPromise */);
    ^

StrykerError: Error: Could not inject [class ChildProcessTestRunnerWorker]. Cause: Cannot find TestRunner plugin "mocha". In fact, no TestRunner plugins were loaded. Did you forget to install it?
Error: Could not inject [class ChildProcessTestRunnerWorker]. Cause: Cannot find TestRunner plugin "mocha". In fact, no TestRunner plugins were loaded. Did you forget to install it?
    at InjectionError.create (file:///path/to/stryker-mocha-bug/node_modules/typed-inject/dist/src/errors.js:53:20)
    at ClassProvider.injectClass (file:///path/to/stryker-mocha-bug/node_modules/typed-inject/dist/src/InjectorImpl.js:45:34)
    at ChildProcessProxyWorker.handleInit (file:///path/to/stryker-mocha-bug/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy-worker.js:75:41)
    at ChildProcess.<anonymous> (file:///path/to/stryker-mocha-bug/node_modules/@stryker-mutator/core/dist/src/child-proxy/child-process-proxy.js:132:39)
    at ChildProcess.emit (node:events:509:28)
    at emit (node:internal/child_process:972:14)
    at process.processTicksAndRejections (node:internal/process/task_queues:91:21) {
  innerError: undefined
}

Node.js v24.18.0
```
