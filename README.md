# docker-run-action

Inspiration from https://github.com/addnab/docker-run-action/releases/tag/v3, converted to a composite action.
Run a docker image pulled from a repository or built by a previous step, allowing options and arguments to be set for the `docker run` command.
See `action.yml` for available inputs.

## Pull and run an image

```yaml
- name: Checkout
  uses: actions/checkout@v3 # Required to mount the Github Workspace to a volume
- uses: open-reading-frame/docker-run-action@main
  with:
    username: ${{ github.repository_owner }}
    password: ${{ secrets.GHCR_TOKEN }}
    registry: ghcr.io
    image: ghcr.io/my-organization/private-image:latest
    options: -v ${{ github.workspace }}:/workspace -e ENV_VAR="env_var"
    args: --arg-1 value_1 --arg-2 value_2
```

This would roughly translate to:
```bash
echo ${{ secrets.GHCR_TOKEN }} | docker login ghcr.io -u ${{ github.repository_owner }} --password-stdin
docker run -v ${{ github.workspace }}:/workspace -e ENV_VAR="env_var" ghcr.io/my-organization/private-image:latest --arg-1 value_1 --arg-2 value_2
```

## Run an image built by a previous step
```yaml
- uses: open-reading-frame/docker-build-push-action@main
  with:
    context: .
    dockerfile: path/to/Dockerfile
    tags: test-image:latest
    load: true
- uses: open-reading-frame/docker-run-action@main
  with:
    image: test-image:latest
```
