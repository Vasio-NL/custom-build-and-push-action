## Image build and push action

This Github Action builds images and pushes them to a container registry.
It also creates/updates a configmap in a kubernetes cluster which keeps track of the latest version.
For pushing to Amazon Elastic Container Registry (ECR), see the [custom build and push ECR action](https://github.com/Vasio-NL/custom-build-and-push-ECR-action).

This action is intended to be used with the custom [kubernetes deploy action](https://github.com/Vasio-NL/custom-k8s-deploy-action).

Tags the image with the given name and pushes a new version to a kubernetes configmap.

### Inputs

| Name | Description                                                                     | Required |
| --- |---------------------------------------------------------------------------------| --- |
| `container-registry-url` | URL for the container registry                                                  | true |
| `container-registry-username` | Username for the container registry                                             | true |
| `container-registry-password` | Password for the container registry                                             | true |
| `container-repository-name` | The name of the container repository                                            | true |
| `dockerfile-path` | Path to the Dockerfile                                                          | true |
| `docker-image-name` | The name of the image                                                           | true |
| `kube-config-base64` | The base64 encoded kubeconfig needed to connect to the cluster                  | true |
| `docker-build-context` | The build context for the docker build. The default is the root directory. ('.') | false |
| `docker-build-args` | Optional additional build arguments for the docker build                        | false |

The container repository name is the name that is prefixed to the image name. An example image:

`registry.digitalocean.com/vasio/cool-project:latest`

In this example:
- The <b>registry url</b> is `registry.digitalocean.com`.
- The <b>container repository name</b> is `vasio`.
- The <b>image name</b> is `cool-project`.

### Example usage

The following is an example release job:

```
  build_app:
    runs-on: ubuntu-latest
    environment: ${{ github.ref_name }}
    steps:
      - uses: Vasio-NL/custom-build-and-push-action@v1
        with:
          docker-build-context: '.'
          docker-build-args: NODE_VERSION=18
          container-registry-url: ${{ vars.REGISTRY_URL }}
          container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
          container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
          container-repository-name: vasio
          docker-image-name: 'cool-project'
          dockerfile-path: './docker/Dockerfile'
          kube-config-base64: ${{ secrets.KUBE_CONFIG_B64 }}
```

### For developers: Updating the action
When making changes, make sure to tag new versions so they can be used in Github workflows. The action uses semantic versioning.

To tag the version (v1.0.3 in the example):

`git tag v1.0.3`

also update the major version tag (v1 in the example):

`git tag v1 -f`

When finished tagging, make sure to push the tags:

`git push --tags -f`
