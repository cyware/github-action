# Cyware Github Action

[![GitHub License](https://img.shields.io/badge/license-MIT-lightgrey.svg)](https://raw.githubusercontent.com/cyware/github-action/master/LICENSE) [![Github Action Community](https://img.shields.io/badge/community-Github%20Actions%20Discuss-343434.svg)](https://github.community/c/github-ecosystem/github-apps/64)

Use Github Action to run jobs on ephemeral environments automatically deployed by Cyware, authenticating into them via a bypass token.
This job connects with Cyware during a Github Action job, fetching necessary environment variables in order to run e2e tests where authentication via OAuth is normally required.

## How to use

In your Github Workflow file located in `.github/workflows/`, you can use the Cyware's Github Action as per the following example:

```
on: [pull_request]

jobs:
  cypress-e2e-tests:
    runs-on: ubuntu-latest
    name: Collect the bypass token and URL for an authenticated ephemeral environment attached to this PR in order to run e2e tests on it.
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Fetch Cyware Tokens
        uses: cyware/github-action/fetch-cyware-env@1.0.0
        env:
          CYWARE_API_TOKEN: ${{ secrets.CYWARE_API_TOKEN }}
      - name: Run the e2e tests on the ephemeral environment
        run: npm run test
        shell: bash
        env:
            CYPRESS_BASE_URL: $CYWARE_ENVIRONMENT_URL
            CYPRESS_BYPASS_TOKEN: $CYWARE_BYPASS_TOKEN
        
```

The Github Action can be configured by passing inputs or environment variables:

**Inputs**
```
  - name: Fetch Cyware Tokens
    uses: cyware/github-action/fetch-cyware-env@1.0.0
    with:
        api-token: ${{ secrets.CYWARE_API_TOKEN }}
        timeout-minutes: 30
```

| Input name | Description | Default Value |
| --------------- | --------------- |--------------- |
| `api-token` | Token required to connect to Cyware's APIs. Can be obtained from your Organization's setting page | -|
| `timeout-minutes` | Number of minutes to wait for Cyware environment before timing out. | 60|
| `app-name` | Filter the environments by name of the application on the Cyware app. | -|


**Environment Variables**
```
  - name: Fetch Cyware Tokens
    uses: cyware/github-action/fetch-cyware-env@1.0.0
    env:
      CYWARE_API_TOKEN: ${{ secrets.CYWARE_API_TOKEN }}
      CYWARE_TIMEOUT: 30
      INPUT_APP_NAME: 'react-app'
```

| Environment Variable | Description | Default Value |
| --------------- | --------------- |--------------- |
| `CYWARE_API_TOKEN` | Token required to connect to Cyware's APIs. Can be obtained from your Organization's setting page  |-|
| `CYWARE_TIMEOUT` | Number of minutes to wait for Cyware environment before timing out. |60|
| `CYWARE_APP_NAME` | Filter the environments by name of the application on the Cyware app. |-|

**NOTE**: Inputs are given precedence over environment variables.

If input `api-token` or environment variable `CYWARE_API_TOKEN` is not provided, error is raised.

On successful run, the following environment variables are set, which can then be passed on to other actions in the same workflow.

| Parameter Name | Description |
| --------------- | --------------- |
|`CYWARE_ENVIRONMENT_URL` | URL of the ephemeral environment |
|`CYWARE_ENVIRONMENT_ID`  | ID of the ephemeral environment  |
|`CYWARE_BYPASS_TOKEN`    | Token to bypass authentication   |


## Resources

[Cyware Documentation](https://docs.cyware.build/docs/)