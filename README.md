# comment-worker

`comment-worker` is a Cloudflare worker that receives user-generated content and uploads it as data files to a GitHub and/or GitLab repository. In practice, this allows you to have dynamic content (e.g. blog post comments) as part of a fully static website, as long as your site automatically deploys on every push to GitHub and/or GitLab, as seen on GitHub Pages, GitLab Pages, Netlify and others.

Essentially it is a direct replacement for [staticman](https://github.com/eduardoboucas/staticman) and is mostly API compatible.

Please see [Issue #1](https://github.com/zanechua/comment-worker/issues/1) for more info

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/zanechua/comment-worker)

# Usage

You need to declare the following variables

| key                      | example                                     |
|--------------------------|---------------------------------------------|
| GITHUB_APP_ID            | 123456                                      |
| GITHUB_APP_PRIVATE_KEY   | -----BEGIN PRIVATE KEY-----...              |
| GITHUB_ORGANIZATION_SLUG | org                                         |
| GITHUB_REPOSITORY_SLUG   | repo-name                                   |
| CW_ALLOWED_ORIGINS       | https://example.com,https://www.example.com |

You can do this in the variables settings of the Cloudflare worker.

The `GITHUB_APP_PRIVATE_KEY` needs to be in pkcs8 format.

Use `openssl` to convert the default key from the GitHub app

```bash
openssl pkcs8 -topk8 -inform PEM -outform PEM -nocrypt -in private-key.pem -out private-key-pkcs8.key
```

### Setup

Create a `.dev.vars` at the root of the repository and the format should follow the `dotenv` format of `KEY=VALUE` and a new line for each of the environment variables

Create a GitHub app by going to [https://github.com/settings/apps](https://github.com/settings/apps) with the permissions of the following:

![GHA Permissions](https://github.com/zanechua/comment-worker/assets/4265429/a5b7e22d-fc15-4828-8289-b9de3958ee24)

Make sure to generate the private key and download the pem file. Follow the conversion to the pkcs8 key and input the private key with `\n` as the separators for the line breaks. 

E.g. `GITHUB_APP_PRIVATE_KEY=-----BEGIN PRIVATE KEY-----\nMIIEvQ...`

Run the following to load up the app
```bash
pnpm install
pnpm dev
```

### Deploy

We're using Cloudflare workers + wrangler to handle the deployments, we have to use wrangler due to the features we are using that requires wrangler to package the bundle up.

You need to run the deploy command with the `CLOUDFLARE_ACCOUNT_ID` and `CLOUDFLARE_API_TOKEN` variables before running the command.

```bash
pnpm build
CLOUDFLARE_ACCOUNT_ID=abc CLOUDFLARE_API_TOKEN=abc pnpm deploy
```
