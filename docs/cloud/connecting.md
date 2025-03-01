---
description: Configure a local execution environment to access Prefect Cloud.
tags:
    - Prefect Cloud
    - API keys
    - configuration
    - agents
    - workers
    - troubleshooting
    - connecting
search:
  boost: 2
---

# Connecting & Troubleshooting Prefect Cloud <span class="badge cloud"></span>

To create flow runs in a local or remote execution environment and use either Prefect Cloud or a Prefect server as the backend API server, you need to  

- Configure the execution environment with the location of the API. 
- Authenticate with the API, either by logging in or providing a valid API key (Prefect Cloud only).

## Log into Prefect Cloud from a terminal 

Configure a local execution environment to use Prefect Cloud as the API server for flow runs. In other words, "log in" to Prefect Cloud from a local environment where you want to run a flow.

1. Open a new terminal session.
2. [Install Prefect](/getting-started/installation/) in the environment in which you want to execute flow runs.

<div class="terminal">
```bash
$ pip install -U prefect
```
</div>

3. Use the `prefect cloud login` Prefect CLI command to log into Prefect Cloud from your environment.

<div class="terminal">
```bash
$ prefect cloud login
```
</div>

The `prefect cloud login` command, used on its own, provides an interactive login experience. Using this command, you can log in with either an API key or through a browser.

<div class="terminal">
```bash
$ prefect cloud login
? How would you like to authenticate? [Use arrows to move; enter to select]
> Log in with a web browser
    Paste an API key
Paste your authentication key:
? Which workspace would you like to use? [Use arrows to move; enter to select]
> prefect/terry-prefect-workspace
    g-gadflow/g-workspace
Authenticated with Prefect Cloud! Using workspace 'prefect/terry-prefect-workspace'.
```
</div>

You can also log in by providing a [Prefect Cloud API key](../users/api-keys/) that you create.

### Change workspaces

If you need to change which workspace you're syncing with, use the `prefect cloud workspace set` Prefect CLI command while logged in, passing the account handle and workspace name.

<div class="terminal">
```bash
$ prefect cloud workspace set --workspace "prefect/my-workspace"
```
</div>

If no workspace is provided, you will be prompted to select one.

**Workspace Settings** also shows you the `prefect cloud workspace set` Prefect CLI command you can use to sync a local execution environment with a given workspace.

You may also use the `prefect cloud login` command with the `--workspace` or `-w` option to set the current workspace.

<div class="terminal">
```bash
$ prefect cloud login --workspace "prefect/my-workspace"
```
</div>

## Manually configure Prefect API settings

You can also manually configure the `PREFECT_API_URL` setting to specify the Prefect Cloud API.

For Prefect Cloud, you can configure the `PREFECT_API_URL` and `PREFECT_API_KEY` settings to authenticate with Prefect Cloud by using an account ID, workspace ID, and API key.

<div class="terminal">
```bash
$ prefect config set PREFECT_API_URL="https://api.prefect.cloud/api/accounts/[ACCOUNT-ID]/workspaces/[WORKSPACE-ID]"
$ prefect config set PREFECT_API_KEY="[API-KEY]"
```
</div>

When you're in a Prefect Cloud workspace, you can copy the `PREFECT_API_URL` value directly from the page URL.

In this example, we configured `PREFECT_API_URL` and `PREFECT_API_KEY` in the default profile. You can use `prefect profile` CLI commands to create settings profiles for different configurations. For example, you could have a "cloud" profile configured to use the Prefect Cloud API URL and API key, and another "local" profile for local development using a local Prefect API server started with `prefect server start`. See [Settings](/concepts/settings/) for details.

!!! note "Environment variables"
    You can also set `PREFECT_API_URL` and `PREFECT_API_KEY` as you would any other environment variable. See [Overriding defaults with environment variables](/concepts/settings/#overriding-defaults-with-environment-variables) for more information.

See the [Flow orchestration with Prefect](/tutorial/flows/) tutorial for examples.

## Install requirements in execution environments

In local and remote execution environments &mdash; such as VMs and containers &mdash; you must make sure any flow requirements or dependencies have been installed before creating a flow run.

# Troubleshooting Prefect Cloud

This section provides tips that may be helpful if you run into problems using Prefect Cloud.

## Prefect Cloud and proxies

Proxies intermediate network requests between a server and a client. 

To communicate with Prefect Cloud, the Prefect client library makes HTTPS requests. These requests are made using the [`httpx`](https://www.python-httpx.org/) Python library. `httpx` respects accepted proxy environment variables, so the Prefect client is able to communicate through proxies. 

To enable communication via proxies, simply set the `HTTPS_PROXY` and `SSL_CERT_FILE` environment variables as appropriate in your execution environment and things should “just work.”

See the [Using Prefect Cloud with proxies](https://discourse.prefect.io/t/using-prefect-cloud-with-proxies/1696) topic in Prefect Discourse for examples of proxy configuration.

URLs that should be whitelisted for outbound-communication in a secure environment include the UI, the API, Authentication, and the current OCSP server:  

- app.prefect.cloud
- api.prefect.cloud
- auth.workos.com
- ocsp.pki.goog/s/gts1d4/OxYEb8XcYmo

## Prefect Cloud access via API

If the Prefect Cloud API key, environment variable settings, or account login for your execution environment are not configured correctly, you may experience errors or unexpected flow run results when using Prefect CLI commands, running flows, or observing flow run results in Prefect Cloud.

Use the `prefect config view` CLI command to make sure your execution environment is correctly configured to access Prefect Cloud.

<div class="terminal">
```bash
$ prefect config view
PREFECT_PROFILE='cloud'
PREFECT_API_KEY='pnu_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx' (from profile)
PREFECT_API_URL='https://api-beta.prefect.io/api/accounts/...' (from profile)
```
</div>

Make sure `PREFECT_API_URL` is configured to use `https://api-beta.prefect.io/api/...`.

Make sure `PREFECT_API_KEY` is configured to use a valid API key.

You can use the `prefect cloud workspace ls` CLI command to view or set the active workspace.

<div class="terminal">
```bash
$ prefect cloud workspace ls
┏━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃   Available Workspaces: ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━┩
│   g-gadflow/g-workspace │
│    * prefect/workinonit │
└─────────────────────────┘
    * active workspace
```
</div>

You can also check that the account and workspace IDs specified in the URL for `PREFECT_API_URL` match those shown in the URL bar for your Prefect Cloud workspace.

## Prefect Cloud login errors

If you're having difficulty logging in to Prefect Cloud, the following troubleshooting steps may resolve the issue, or will provide more information when sharing your case to the support channel.

- Are you logging into Prefect Cloud 2? Prefect Cloud 1 and Prefect Cloud 2 use separate accounts. Make sure to use the right Prefect Cloud 2 URL: https://app.prefect.cloud/
- Do you already have a Prefect Cloud account? If you’re having difficulty accepting an invitation, try creating an account first using the email associated with the invitation, then accept the invitation.
- Are you using a single sign-on (SSO) provider (Google or Microsoft) or just using a username and password login? 
- Did you utilize the “having trouble/forgot password” link on the login page? If so, did you receive the password reset email? Occasionally the password reset email can get filtered into your spam folder.

Other tips to help with login difficulties:

- Hard refresh your browser with Cmd+Shift+R.
- Try in a different browser. We actively test against the following browsers:
    - Chrome
    - Edge
    - Firefox
    - Safari
- Clear recent browser history/cookies

In some cases, logging in to Prefect Cloud results in a "404 Page Not Found" page or fails with the error: "Failed to load module script: Expected a JavaScript module script but the server responded with a MIME type of “text/html”. Strict MIME type checking is enforced for module scripts per HTML spec."

This error may be caused by a bad service worker. 

To resolve the problem, we recommend unregistering service workers. 

In your browser, start by opening the developer console. 

- In Chrome: **View > Developer > Developer Tools**
- In Firefox: **Tools > Browser Tools > Web Developer Tools**

Once the developer console is open:

1. Go to the **Application** tab in the developer console.
1. Select **Storage**.
1. Make sure **Unregister service workers** is selected.
1. Select **Clear site data**, then hard refresh the page with CMD+Shift+R (CTRL+Shift+R on Windows).

See the [Login to Prefect Cloud fails...](https://discourse.prefect.io/t/login-to-prefect-cloud-fails-with-an-error-failed-to-load-module-script-expected-a-javascript-module-script-but-the-server-responded-with-a-mime-type-of-text-html-strict-mime-type-checking-is-enforced-for-module-scripts-per-html-spec/1722/1) topic in Prefect Discourse for a video demonstrating these steps.

None of this worked?

Email us at help@prefect.io and provide answers to the questions above in your email to make it faster to troubleshoot and unblock you. Make sure you add the email address with which you were trying to log in, your Prefect Cloud account name, and, if applicable, the organization to which it belongs.
