# helm-openclaw

Helm chart for deploying OpenClaw on Kubernetes.

## Chart structure

- `Chart.yaml`: Chart metadata
- `values.yaml`: Default configuration
- `templates/`: Kubernetes manifests (Deployment, Service, Ingress, HPA, ServiceAccount)

## Quick start

```bash
helm lint .
helm upgrade --install openclaw . \
	--namespace openclaw \
	--create-namespace \
	--set image.repository=ghcr.io/openclaw/openclaw \
	--set image.tag=latest
```

## Common overrides

- Set replicas: `--set replicaCount=2`
- Set gateway bind mode command: `--set-json 'command=["node","dist/index.js","gateway","--bind","lan","--port","18789"]'`
- Disable config bootstrap init container: `--set bootstrapConfig.enabled=false`
- Change bootstrap gateway mode: `--set bootstrapConfig.gatewayMode=local`
- Disable startup config-file gate probe: `--set startupProbe.enabled=false`
- Enable persisted state/workspace: `--set persistence.config.enabled=true --set persistence.workspace.enabled=true`
- Disable managed token secret (not recommended): `--set gatewayToken.enabled=false`
- Use existing token secret: `--set gatewayToken.existingSecret=openclaw-token --set gatewayToken.secretKey=OPENCLAW_GATEWAY_TOKEN`
- Enable ingress: `--set ingress.enabled=true --set ingress.hosts[0].host=openclaw.example.com`
- Enable autoscaling: `--set autoscaling.enabled=true --set autoscaling.maxReplicas=10`

By default the chart creates a Secret and injects `OPENCLAW_GATEWAY_TOKEN` into the gateway container. If no token value is provided, Helm generates one.
By default the chart also runs an init container that seeds `/home/node/.openclaw/openclaw.json` with `gateway.mode=local` when the file is missing.

You can also place overrides in a custom values file:

```bash
helm upgrade --install openclaw . -f my-values.yaml
```