---
type: decision
status: stable
updated: 2026-08-21
date: 2026-08-21
decided_by: Matheus Silva
source: "Planning discussion with msilva, 2026-08-21"
tags: [proxy, gcp, cloud-run, networking, pulumi]
aliases: [private Airtable Proxy infrastructure, proxy.livemode.com]
---

# Deploy Airtable Proxy privately behind VPN

**Decision.** [[Airtable Proxy]] will be deployed as a private service reachable
through Livemode's VPN at `https://proxy.livemode.com`. The VPN/network is the
initial trust boundary. Calling services identify themselves through the first
URL path segment, per [[2026-08-19 Identify proxy apps by URL path, not header]],
but do not authenticate with a separate app key in the first production phase.
An app key will be layered on later without removing the private-network boundary.

## Request path and credential boundary

A caller uses an endpoint such as:

```text
https://proxy.livemode.com/livescript/v0/...
```

The proxy resolves `livescript` in `PROXY_APPS`, verifies the configured base
allow-list, removes the app-id segment, replaces any incoming authorization with
the corresponding Airtable PAT, and forwards the canonical `/v0/...` request.
The PAT is never returned to the caller or written to telemetry.

In this phase, the app ID is an identifier, not proof of identity: any workload
that can reach the proxy through the trusted network could claim another known
app ID. This is an explicit first-phase trade-off, mitigated by private ingress,
least-privilege PATs, and per-app base allow-lists.

## Planned private request path

```text
Internal application
  -> corporate VPN
  -> GCP VPC
  -> private DNS: proxy.livemode.com
  -> regional internal HTTPS Application Load Balancer
  -> serverless NEG
  -> Cloud Run (internal ingress)
  -> api.airtable.com
```

`proxy.livemode.com` uses split-horizon DNS: inside the VPN it resolves to the
load balancer's private address; outside the private network it has no usable
public route. TLS must cover that hostname. The existing private DNS zone,
certificate/PKI, VPN, VPC, and shared subnets remain central-infrastructure
concerns rather than resources owned by this application repository.

The initial deployment is regional. Cross-region private load balancing is a
future availability upgrade, not first-slice scope.

## Pulumi ownership

The Go Pulumi program for this repository should own:

- required GCP API enablement;
- an Artifact Registry Docker repository;
- the Cloud Run runtime service account and least-privilege IAM;
- Secret Manager secret metadata and access policy for `PROXY_APPS`;
- the Cloud Run v2 service (`internal` ingress, `min=1`, `max>1`, `/healthz`);
- the serverless NEG;
- the internal load balancer's backend service, URL map, HTTPS proxy, private
  address, and forwarding rule;
- the `proxy.livemode.com` record in the existing private DNS zone, if the
  application stack is authorized to manage that record;
- production monitoring resources after the OTLP backend is chosen.

The stack should receive, rather than create, the existing project/network
context: GCP project, region, VPC, frontend subnet, proxy-only subnet, private
DNS zone, and certificate resource identifiers.

Container build and publication stay in CI/release tooling. Cloud Run should
reference an immutable image tag or digest.

## Secrets

Pulumi should create Secret Manager containers and IAM, but PAT values should be
inserted by an administrator or secure delivery pipeline rather than committed
to this repository or placed in clear-text stack configuration. Rotation creates
a new secret version and a new Cloud Run revision. A single `PROXY_APPS` secret
matches the current code; per-app secrets can be considered when rotation or
ownership pressure justifies the code change.

## Egress

The first slice uses Cloud Run's normal outbound path to `api.airtable.com`.
Direct VPC egress plus Cloud NAT is added only if Livemode policy requires a
static source IP, centralized firewalling, or all-traffic VPC routing.

## Future app-key phase

The private network remains in place. Each app will additionally present an
app key; the proxy will validate it before selecting/injecting the Airtable PAT.
The transport and rotation representation for the app key are not decided here.
The rollout should allow per-app migration rather than requiring every consumer
to switch at once.

## Still open before implementation

- GCP project and region.
- Exact VPC, frontend subnet, and existing proxy-only subnet identifiers.
- Whether the VPN routes directly into that VPC and how VPN clients resolve the
  private DNS zone.
- Ownership of the `livemode.com` private DNS zone and TLS certificate.
- Pulumi state backend (Pulumi Cloud or GCS).
- Secret-value delivery and rotation procedure.
- Production OTLP backend and monitoring/notification destination.
