# Reusable Appium TypeScript script template

Use this reference only when the user explicitly asks for a saved script, or when a successful flow should be preserved for reruns.

## File location and naming

- Put reusable flow scripts under `e2e/flows/`.
- Use a short feature name such as `e2e/flows/booking.ts`.
- Run the script with `npx ts-node e2e/flows/booking.ts`.
- The target project should provide `typescript`, `ts-node`, and `@types/node`.

## Environment setup

- Read optional local config from `e2e/environment.ts` before applying defaults.
- Create `e2e/environment.ts` with non-secret sample values when it does not already exist.
- Commit `e2e/environment.ts`; do not add it to `.gitignore`.
- Environment variables provided by the shell must override values from `e2e/environment.ts`.

Example local config shape:

```ts
const environment = {
  APPIUM_SERVER_URL: 'http://127.0.0.1:4723',
  APP_BUNDLE_ID: 'com.example.app',
  IOS_DEVICE_NAME: 'iPhone 15',
  IOS_PLATFORM_VERSION: '17.0',
};

export default environment;
```

## Session setup

Include these inputs when relevant:

- `APPIUM_SERVER_URL`
- `APP_BUNDLE_ID`
- `IOS_DEVICE_NAME`
- `IOS_UDID`
- `IOS_PLATFORM_VERSION`
- optional login credentials

## Shared helpers

Prefer small typed helpers for common operations. If the repo already has TypeScript scripts under `e2e/flows/`, copy their helper style. Otherwise implement thin wrappers around standard Appium WebDriver calls.

- `findFirst` to return the first matching element
- `maybeTap` to tap only when a matching element exists
- source lookup to capture page source for diagnostics
- explicit waits for expected elements or states
- `createBaseResult`
- `writeResult`

- create and delete session

## Flow shape

Structure the script in clear phases:

1. preconditions
2. navigation
3. action
4. assertion

## Result artifact

Every reusable E2E script should write a JSON result file under `e2e/results/`.

- Use `e2e/results/<flow>-result.json`, for example `e2e/results/booking-result.json`.
- Create the result object at script start with `createBaseResult(startedAt)`.
- Include at minimum: `flow`, `status`, `startedAt`, `completedAt`, Appium target metadata, flow input data, assertions, diagnostics, and `error`.
- Set `status` to `passed` only after the final app-state assertion succeeds.
- On failure, set `status` to `failed`, include `error.message`, `error.stack`, and best-effort diagnostics such as the latest page source.
- Always call `writeResult(resultPath, result)` from `finally` so a result file exists for passed and failed runs.
- Print one clear pass or fail line.

Use the repository's existing TypeScript scripts under `e2e/flows/` as examples when present.

## Result helper pattern

```ts
type FlowStatus = 'running' | 'passed' | 'failed';

interface FlowAssertion {
  name: string;
  passed: boolean;
  details?: string;
}

interface FlowDiagnostics {
  lastSource: string | null;
}

interface FlowError {
  message: string;
  stack?: string;
}

interface FlowResult {
  flow: string;
  status: FlowStatus;
  startedAt: string;
  completedAt: string | null;
  appiumServerUrl: string;
  platform: string;
  appBundleId: string;
  deviceName: string;
  platformVersion: string;
  udid: string | null;
  input: Record<string, unknown>;
  assertions: FlowAssertion[];
  diagnostics: FlowDiagnostics;
  error: FlowError | null;
}

function createBaseResult(startedAt: Date): FlowResult {
  return {
    flow: '<flow-name>',
    status: 'running',
    startedAt: startedAt.toISOString(),
    completedAt: null,
    appiumServerUrl: serverUrl,
    platform: 'iOS',
    appBundleId: process.env.APP_BUNDLE_ID || DEFAULT_BUNDLE_ID,
    deviceName: process.env.IOS_DEVICE_NAME || DEFAULT_DEVICE_NAME,
    platformVersion:
      process.env.IOS_PLATFORM_VERSION || DEFAULT_PLATFORM_VERSION,
    udid: process.env.IOS_UDID || null,
    input: {},
    assertions: [],
    diagnostics: {
      lastSource: null,
    },
    error: null,
  };
}

function writeResult(filePath: string, result: FlowResult): void {
  fs.mkdirSync(path.dirname(filePath), { recursive: true });
  fs.writeFileSync(filePath, `${JSON.stringify(result, null, 2)}\n`);
  console.log(`Result written: ${filePath}`);
}
```

Load `environment.ts` before applying defaults, while shell-provided environment variables remain the highest-priority source.
