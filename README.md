# ecr-secret-updater

🛠 A simple Kubernetes `CronJob` to generate AWS ECR image pull secrets for target namespaces, using only a simple alpine image.
 
This may be useful if you need to pull AWS ECR images from a non-AWS environment (e.g. a different cloud provider), and your only AWS IAM solution is access keys. If you can use AWS IAM OIDC to allow a Kubernetes service account to assume an IAM role, you should probably do that, and disregard this `CronJob`. If not, continue looking...


## Getting Started

This is intended to be as simple as possible, without having to rely on complex third party images or the need to build an image with `kubectl` and `aws` CLI. The simplicity also makes it easier to audit, so by all means, copy the code, and check it yourself before using it!

1. Create namespace: `ecr-secret-updater`
2. Create secret: `ecr-secret-updater-secrets`
    - Set `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_DEFAULT_REGION`
3. Update `TARGET_NAMESPACES` in [`k8s/job.yml`](./k8s/job.yml)
4. Apply manifests in [`k8s`](./k8s)
5. Use `aws-ecr` in `imagePullSecrets`


## Alternatives

Consider using [External Secrets](https://external-secrets.io/latest/api/generator/ecr/). Though, at the time of writing, their generators have very poor multi-namespace support, so you may have to generate the ECR secret in one namespace, and use another tool like [`reflector`](https://github.com/emberstack/kubernetes-reflector) to mirror it to other namespaces. In fact, you can use `reflector` for `ecr-secret-updater` too! Simply, set the `TARGET_NAMESPACES` to a single namespace, and use `reflector` to mirror it - allowing you to change the `CronJob`'s service account `ClusterRole` to a simple `Role`, which is more restrictive.
