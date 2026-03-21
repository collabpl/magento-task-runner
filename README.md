# magento-task-runner

Docker images for running Magento 2 background tasks and queue workers. Each image
extends the official Magento Cloud Docker PHP CLI base and bundles a compiled
`magento-worker-daemon` binary that manages consumer/queue worker processes.

Available on:
- **Docker Hub**: [`collabpl/magento-task-runner`](https://hub.docker.com/r/collabpl/magento-task-runner)
- **GitLab Registry**: `gitlab-registry.collab.pl/collab/docker/magento-task-runner`

## Available tags

| Tag | PHP | Base image |
|-----|-----|-----------|
| `8.3` | 8.3 | `magento/magento-cloud-docker-php:8.3-cli-1.4.7` |
| `8.4` | 8.4 | `magento/magento-cloud-docker-php:8.4-cli-1.4.7` |
| `8.5` | 8.5 | `magento/magento-cloud-docker-php:8.5-cli-1.4.7` |

## What's included

- **`magento-worker-daemon`** — compiled binary installed at `/usr/local/bin/`, set as
  the default `CMD`. Manages the lifecycle of Magento queue consumer processes.
- **Non-root user**: runs as `www` (inherited from the base image)
- **`MAGENTO_ROOT`**: `/var/www/html` (default working directory)

## Usage

### Docker Hub

```bash
docker pull collabpl/magento-task-runner:8.4
```

### GitLab Registry

```bash
docker pull gitlab-registry.collab.pl/collab/docker/magento-task-runner:8.4
```

### docker-compose example

```yaml
services:
  worker:
    image: collabpl/magento-task-runner:8.4
    volumes:
      - ./src:/var/www/html
    restart: unless-stopped
```

### Kubernetes deployment example

```yaml
containers:
  - name: magento-worker
    image: collabpl/magento-task-runner:8.4
    volumeMounts:
      - name: magento-src
        mountPath: /var/www/html
```

## Repository structure

```
.
├── bin/
│   └── magento-worker-daemon    # compiled daemon binary
├── 8.3/
│   └── Dockerfile
├── 8.4/
│   └── Dockerfile
├── 8.5/
│   └── Dockerfile
├── .github/
│   └── workflows/               # GitHub Actions — build & push to Docker Hub
└── .gitlab-ci.yml               # GitLab CI — build via Kaniko
```

## CI/CD

**GitHub Actions** builds for `linux/amd64` and pushes to Docker Hub on every push to
`main` and on published releases. Requires `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN`
repository secrets.

**GitLab CI** uses the internal
[`kaniko-build`](https://gitlab.collab.pl/collab/components/kaniko-build)
component to build all three variants in parallel and push to the GitLab Container Registry.

## Adding a new PHP version

1. Create `{version}/Dockerfile` (copy an existing one and update the `FROM` line).
2. Add the new tag to the `.gitlab-ci.yml` parallel matrix.
3. Create a new GitHub Actions workflow `.github/workflows/build-php-{version}.yml`.
4. Push — both pipelines will build and publish the new image automatically.

## Updating the daemon binary

Replace `bin/magento-worker-daemon` with the newly compiled binary and push a new tag.
Both CI pipelines will rebuild and publish all image variants.

## License

MIT
