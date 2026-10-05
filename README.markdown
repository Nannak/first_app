
# First App

This repository contains the original static Rails welcome page. The staging
container serves the contents of `public/` with nginx, so the legacy Rails 3
dependencies are not installed in the production image.

## Fly.io staging

The included `fly.toml` is configured for a small, scale-to-zero staging app.
Before the first deployment, change the `app` value if `first-app-staging` is
already in use in your Fly.io organization, then run:

```sh
fly auth login
fly launch --copy-config --no-deploy
fly deploy
```

### Deploying from automation or a hosted workspace

Docker is not required locally. Fly can build the image with its remote builder;
install `flyctl`, provide a Fly access token through the environment, and force a
remote build:

```sh
export FLY_API_TOKEN="<token supplied by your secret store>"
fly deploy --remote-only --config fly.toml
```

Do not add the token to this repository. The app named in `fly.toml` must already
exist and the token must be authorized to deploy it. For the first deployment,
create the app from an authenticated workstation or CI job if necessary:

```sh
fly apps create first-app-staging
fly deploy --remote-only --config fly.toml
```

Check the deployment with:

```sh
fly status
fly checks list
curl "https://$(fly status --json | jq -r .Hostname)/healthz"
```

The health endpoint returns `ok`. Staging Machines stop while idle and restart
automatically on the next request.
