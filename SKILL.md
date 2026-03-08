---
name: Infrastructure Weaver
description: Builds interconnected and resilient system architectures
tags:
  - weaving
  - architecture
  - resilience
  - networking
  - scalability
version: 2.1.0
author: Infrastructure Team
dependencies:
  - terraform >= 1.5.0
  - kubectl >= 1.28.0
  - helm >= 3.12.0
  - istioctl >= 1.19.0
  - jq >= 1.6
  - yq >= 4.30.0
required_tools:
  - aws-cli
  - az
  - gcloud
  - docker
min_claw_version: "2.0.0"
category: infrastructure
---

# Infrastructure Weaver

Builds interconnected and resilient system architectures with automated failover, load balancing, and service mesh integration.

## Purpose

Infrastructure Weaver automates the deployment and configuration of production-grade, interconnected system architectures. It handles the creation of multi-AZ/multi-region deployments, service mesh integration, circuit breakers, retry logic, auto-scaling policies, and network security groups.

### Real Use Cases

- **Multi-region active-active deployment**: Deploy application stacks across us-east-1 and us-west-2 with Route53 latency-based routing, database replication, and cross-region failover automation.
  
- **Service mesh with zero-trust security**: Install and configure Istio with mTLS, automatic sidecar injection, fine-grained RBAC policies, and telemetry integration with Prometheus/Grafana.

- **Resilient microservices architecture**: Deploy microservices with HPA/VPA, pod disruption budgets, readiness/liveness probes, circuit breakers (Envoy/Istio), and distributed tracing (Jaeger/Zipkin).

- **Hybrid cloud connectivity**: Establish VPN tunnels or VPC peering between AWS VPCs and Azure VNets, configure Transit Gateway, and set up cross-cloud load balancing.

- **Infrastructure as Code migration**: Convert existing manually-provisioned resources into Terraform modules, implement drift detection, and establish CI/CD pipelines for infrastructure changes.

## Scope

### Core Commands

`infrastructure-weaver weave <architecture-type> --environment <env> --region <region> [flags]`

Supported architecture types:
- `kubernetes-cluster` - Full K8s cluster with node pools, networking (Calico/Cilium), ingress (Nginx/ALB), storage classes
- `service-mesh` - Istio/Consul installation with gateway configurations, virtual services, destination rules
- `microservices-app` - Complete microservices stack with databases (PostgreSQL/Redis), message queues (RabbitMQ/Kafka), monitoring stack
- `multi-region` - Cross-region replication with global load balancer, database replication, and S3 cross-region sync
- `hybrid-cloud` - AWS-Azure/GCP connectivity with VPN, peering, and unified networking

### Command Flags

```bash
# Example: Deploy multi-region Kubernetes cluster with GitOps
infrastructure-weaver weave kubernetes-cluster \
  --environment production \
  --region us-east-1 \
  --multi-region \
  --gitops-repo https://github.com/org/flux-repo \
  --gitops-branch main \
  --node-count 5 \
  --machine-type m5.xlarge \
  --enable-autoscaling \
  --min-nodes 3 \
  --max-nodes 15 \
  --pod-cidr 10.244.0.0/16 \
  --service-cidr 10.96.0.0/12 \
  --dns-zone cluster.example.com \
  --tls-cert-arn arn:aws:acm:us-east-1:123:certificate/xxx \
  --vpc-id vpc-12345 \
  --private-subnets "subnet-abc,subnet-def" \
  --public-subnets "subnet-xyz,subnet-pqr" \
  --enable-irsa \
  --oidc-provider oidc.eks.us-east-1.amazonaws.com/id/xxx \
  --storage-class gp3 \
  --enable-monitoring \
  --enable-logging \
  --log-retention 30 \
  --tags Environment=production,Team=platform,Owner=team@example.com

# Example: Deploy service mesh with advanced traffic management
infrastructure-weaver weave service-mesh \
  --environment staging \
  --region eu-west-1 \
  --mesh-provider istio \
  --version 1.19.0 \
  --enable-mtls STRICT \
  --enable-telemetry \
  --enable-tracing \
  --tracing-collector jaeger-collector.jaeger.svc.cluster.local \
  --enable-metrics \
  --prometheus-url https://prometheus.example.com \
  --enable-access-logging \
  --access-log-format JSON \
  --gateway-namespaces istio-system \
  --gateway-label istio=ingressgateway \
  --gateway-type LoadBalancer \
  --gateway-replicas 2 \
  --gateway-service-type LoadBalancer \
  --gateway-annotations "service.beta.kubernetes.io/aws-load-balancer-type=nlb" \
  --enable-automatic-sidecar-injection \
  --pod-labels "sidecar.istio.io/inject=true" \
  --namespace-excludes kube-system,local-path-storage \
  --setup-timeout 600s

# Example: Deploy resilient microservices application with circuit breakers
infrastructure-weaver weave microservices-app \
  --namespace payment-processor \
  --image-registry registry.example.com \
  --image-tag v2.1.0 \
  --replicas 3 \
  --cpu-request 500m \
  --cpu-limit 2000m \
  --memory-request 1Gi \
  --memory-limit 4Gi \
  --enable-hpa \
  --hpa-min-replicas 3 \
  --hpa-max-replicas 20 \
  --hpa-target-cpu 70 \
  --hpa-target-memory 80 \
  --enable-vpa \
  --database-instance db.prometheus.r5.large \
  --database-version 14 \
  --redis-node-type cache.t4g.micro \
  --redis-replicas 2 \
  --enable-pdb \
  --pdb-min-available 2 \
  --circuit-breaker-max-connections 100 \
  --circuit-breaker-max-pending-requests 50 \
  --circuit-breaker-max-requests 100 \
  --circuit-breaker-max-retries 3 \
  --retry-attempts 3 \
  --retry-per-try-timeout 2s \
  --enable-metrics \
  --enable-logs \
  --log-level info \
  --enable-tracing \
  --jaeger-endpoint http://jaeger-collector:14268/api/traces

# Example: Establish multi-region replication with Route53 failover
infrastructure-weaver weave multi-region \
  --primary-region us-east-1 \
  --secondary-region us-west-2 \
  --enable-database-replication \
  --db-engine aurora-postgresql \
  --db-instance-class db.r5.large \
  --db-replica-count 2 \
  --enable-s3-replication \
  --s3-source-bucket app-data-primary \
  --s3-destination-bucket app-data-secondary \
  --replication-time-control 15m \
  --route53-zone example.com \
  --health-check-path /health \
  --health-check-port 443 \
  --failover-threshold 3 \
  --enable-cdn \
  --cdn-distribution-id E1234567890ABC \
  --cdn-origin primary-lb-123456.us-east-1.elb.amazonaws.com \
  --enable-waf \
  --waf-acl-arn arn:aws:wafv2:us-east-1:123:global/webacl/app-waf/abc123

# Example: Setup hybrid cloud connectivity between AWS and Azure
infrastructure-weaver weave hybrid-cloud \
  --cloud-provider aws \
  --peer-cloud-provider azure \
  --aws-vpc-id vpc-123456 \
  --aws-vpn-cidr 10.100.0.0/16 \
  --azure-vnet-id /subscriptions/xxx/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnet1 \
  --azure-vpn-cidr 10.200.0.0/16 \
  --vpn-connection-name aws-azure-conn \
  --vpn-type aws-site-to-site-vpn \
  --azure-local-network-gateway azure-onprem-gw \
  --enable-bgp \
  --asn 65001 \
  --tunnel-encryption aes256 \
  --tunnel-lifetime 3600 \
  --enable-transit-gateway \
  --transit-gateway-id tgw-12345 \
  --enable-vpc-sharing \
  --shared-account-ids 111111111111,222222222222 \
  --enable-centralized-logging \
  --log-group /aws/vpn/flow-logs \
  --enable-mirroring \
  --traffic-mirror-target-id tmt-12345 \
  --traffic-mirror-filter-id tmf-12345
```

## Detailed Work Process

1. **Validation Phase**
   - Verify all required dependencies are installed and accessible
   - Validate input parameters against schema for the selected architecture type
   - Check for existing resources to prevent conflicts
   - Confirm IAM/cloud permissions for target account
   - Validate network CIDR ranges for overlaps

2. **Planning Phase**
   - Generate Terraform plan files with all required resources
   - Render Kubernetes manifests with Kustomize/Helm templates
   - Calculate resource requirements based on provided flags
   - Create execution order for dependent resources
   - Generate pre and post deployment hooks

3. **Infrastructure Provisioning**
   - Create VPCs, subnets, NAT gateways, internet gateways
   - Deploy EKS/AKS/GKE clusters with specified configurations
   - Configure managed node groups with auto-scaling
   - Setup storage classes, persistent volume claims
   - Create RDS/ElastiCache/Cloud SQL instances with replication
   - Establish VPC peering or Transit Gateway attachments

4. **Service Mesh Integration**
   - Install Istio operator with custom configuration
   - Configure gateway resources (LoadBalancer/NodePort)
   - Set up mTLS and authentication policies
   - Enable telemetry collection (Prometheus metrics)
   - Configure distributed tracing endpoints
   - Deploy Envoy filters for advanced traffic management

5. **Application Deployment**
   - Build and push container images to registry
   - Create Kubernetes namespaces with resource quotas
   - Deploy ConfigMaps and Secrets (from SOPS/SealedSecrets)
   - Deploy deployments with pod disruption budgets
   - Configure HPA/VPA for auto-scaling
   - Set up ingress resources with TLS termination

6. **Resilience Configuration**
   - Implement readiness and liveness probes
   - Configure circuit breakers at service mesh level
   - Set retry budgets and timeout policies
   - Configure connection pooling parameters
   - Deploy chaos engineering experiments (optional)

7. **Security Hardening**
   - Apply network policies to restrict pod communication
   - Configure Pod Security Standards (restricted/privileged)
   - Setup OIDC/IAM integration for service accounts
   - Enable audit logging to CloudWatch/Splunk
   - Apply security context constraints

8. **Monitoring and Alerting**
   - Deploy Prometheus Stack (Prometheus, Alertmanager, Grafana)
   - Configure metrics collection for all services
   - Create Grafana dashboards from templates
   - Setup Alertmanager routing rules and receiver configurations
   - Install logging stack (Loki/Fluentd/Elasticsearch)
   - Configure log aggregation and retention

9. **Verification Phase**
   - Run health checks on all deployed resources
   - Verify service mesh mTLS is functioning
   - Test failover scenarios for multi-region setups
   - Validate auto-scaling policies
   - Run connectivity tests between services
   - Check security group and network policy rules

10. **Output Generation**
    - Generate connection strings and endpoints
    - Create kubeconfig files for cluster access
    - Output Terraform state file location
    - Generate GitOps configuration for ongoing management
    - Create runbook with operational procedures
    - Provide cost estimation report

## Golden Rules

- **Always use Terraform state backend**: Never use local state. Configure S3/Azure Storage/GCS with state locking via DynamoDB.
- **Implement least-privilege IAM**: Every service account gets only permissions it requires. Use OpenID Connect federation instead of long-term keys.
- **Enforce mTLS everywhere**: All service-to-service communication must use mutual TLS enforced by service mesh policies.
- **Never expose databases publicly**: All databases must be in private subnets with security group restrictions.
- **Resource tagging is mandatory**: All provisioned resources must have standardized tags: Environment, Team, Owner, Project, ManagedBy.
- **Immutable infrastructure**: No in-place updates. All changes go through Terraform/Helm with version-controlled configurations.
- **Backup everything**: All stateful data must have automated backups with tested restore procedures.
- **Chaos engineering weekly**: Schedule weekly chaos experiments to validate resilience. Document all failure scenarios.
- **Multi-AZ by default**: All production workloads must span at least 3 availability zones unless cost-constrained.
- **Secrets management**: Never store secrets in Git. Use HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, or SOPS-encrypted files.

## Examples

### Example 1: Production Kubernetes Cluster with GitOps

**Input**:
```bash
infrastructure-weaver weave kubernetes-cluster \
  --environment production \
  --region us-east-1 \
  --gitops-repo https://github.com/company/k8s-manifests \
  --gitops-branch production \
  --node-count 6 \
  --machine-type m5.2xlarge \
  --enable-autoscaling \
  --min-nodes 3 \
  --max-nodes 20 \
  --enable-irsa \
  --enable-monitoring \
  --enable-logging
```

**Output**:
```
✓ Validating dependencies... OK
✓ Validating parameters... OK
✓ Checking for resource conflicts... OK
✓ Confirming IAM permissions... OK

PHASE 1: Terraform Initialization
→ Initializing Terraform backend in s3://company-terraform-state/production/us-east-1/eks
→ Terraform init completed successfully

PHASE 2: Network Infrastructure
→ Creating VPC (vpc-0a1b2c3d4e5f6g7h8) with CIDR 10.0.0.0/16
→ Creating 6 subnets across 3 AZs (subnet-aaa, subnet-bbb, subnet-ccc)
→ Creating NAT Gateways (nat-0a1b2c3d, nat-4e5f6g7h)
→ Creating Internet Gateway (igw-0a1b2c3d4e5f6g7h)

PHASE 3: EKS Cluster
→ Creating EKS cluster (production-us-east-1-eks) version 1.28
→ Creating managed node groups: core-nodes-1, core-nodes-2, core-nodes-3
→ Node group core-nodes-1: 2x m5.2xlarge in us-east-1a, us-east-1b
→ Node group core-nodes-2: 2x m5.2xlarge in us-east-1b, us-east-1c
→ Node group core-nodes-3: 2x m5.2xlarge in us-east-1c, us-east-1a
→ Cluster provisioning: [==========] 100% (6m42s)

PHASE 4: Service Mesh Installation
→ Installing Istio 1.19.0 via Istio Operator
→ Configuring mTLS STRICT mode
→ Enabling automatic sidecar injection
→ Creating ingress gateway with LoadBalancer service type
→ External IP assigned: a1b2c3d4e5f6g7h.elb.us-east-1.amazonaws.com

PHASE 5: Monitoring Stack
→ Deploying Prometheus Operator
→ Configuring Thanos sidecar for long-term storage
→ Deploying Grafana with pre-configured dashboards
→ Grafana URL: https://grafana.production.example.com
→ Login credentials stored in Secrets Manager: /production/monitoring/grafana

PHASE 6: Flux GitOps Configuration
→ Installing Flux v0.40.0
→ Configuring GitRepository source: https://github.com/company/k8s-manifests
→ Branch: production
→ Creating Kustomization for all namespaces
→ Flux reconciliation started (checking every 1m)

PHASE 7: Verification
→ Running cluster health checks: ✓ Control plane healthy, ✓ All nodes Ready
→ Testing node autoscaling: ✓ Pods scale as expected, ✓ Cluster Autoscaler functional
→ Verifying service mesh: ✓ mTLS enforced, ✓ Telemetry collecting
→ Testing monitoring: ✓ Prometheus targets all Up, ✓ Grafana dashboards accessible
→ Running connectivity tests: ✓ All services can communicate

PHASE 8: Output Generation
→ Kubeconfig saved to: ~/.kube/config-production-us-east-1
→ Terraform state saved to: s3://company-terraform-state/production/us-east-1/eks/terraform.tfstate
→ Cluster endpoint: https://API_ID.gr7.us-east-1.eks.amazonaws.com
→ Ingress gateway: a1b2c3d4e5f6g7h.elb.us-east-1.amazonaws.com
→ Grafana dashboard: https://grafana.production.example.com/d/cluster-overview
→ Cost estimate per month: $3,245.67 (compute) + $234.89 (network) + $456.12 (storage)

✓ Infrastructure deployment COMPLETE

Next steps:
1. Configure DNS: Create CNAME from *.apps.example.com to ingress gateway
2. Deploy applications: Use Flux to deploy workloads from git repo
3. Setup alerting: Review and customize Alertmanager rules in monitoring namespace
4. Run chaos experiments: kubectl apply -f experiments/network-latency.yaml
```

### Example 2: Service Mesh with Circuit Breakers and Retry Policies

**Input**:
```bash
infrastructure-weaver weave service-mesh \
  --environment production \
  --region us-east-1 \
  --mesh-provider istio \
  --enable-mtls STRICT \
  --enable-telemetry \
  --enable-tracing \
  --tracing-collector jaeger-collector.jaeger.svc.cluster.local:4317 \
  --circuit-breaker-max-connections 200 \
  --circuit-breaker-max-pending-requests 100 \
  --circuit-breaker-max-requests 200 \
  --circuit-breaker-max-retries 5 \
  --retry-attempts 4 \
  --retry-per-try-timeout 3s \
  --retry-connect-timeout 2s \
  --outlier-detection-consecutive-5xx 5 \
  --outlier-detection-interval 30s \
  --outlier-detection-base-ejection-time 3m \
  --outlier-detection-max-ejection-percent 50
```

**Output**:
```
✓ Validating Istio installation... OK

PHASE 1: Service Mesh Configuration
→ IstioOperator spec generated for 1.19.0
→ Enabling global mTLS: STRICT mode
→ Configuring automatic sidecar injection for all namespaces (exclude: kube-system, istio-system)
→ Setting telemetry v2 enabled

PHASE 2: Gateway Configuration
→ Creating StandardGateway: istio-system/istio-ingressgateway
→ Service type: LoadBalancer
→ Replicas: 3 (spread across all AZs)
→ Annotations:
  - service.beta.kubernetes.io/aws-load-balancer-type=nlb
  - service.beta.kubernetes.io/aws-load-balancer-cross-zone-load-balancing-enabled=true
→ External IP allocated: nlb-abc123.elb.us-east-1.amazonaws.com

PHASE 3: Circuit Breaker Policies
→ Applying DestinationRule to all services with default retry policy:
  • Retry attempts: 4
  • Per-try timeout: 3s
  • Connect timeout: 2s
  • Retry-on: 5xx, gateway-error, connect-failure, refusals

→ Applying circuit breaker settings:
  • Max connections: 200
  • Max pending requests: 100
  • Max requests: 200
  • Max retries: 5

PHASE 4: Outlier Detection (Ejection)
→ Configuring outlier detection:
  • Eject after 5 consecutive 5xx errors
  • Detection interval: 30s
  • Base ejection time: 3m
  • Max ejection percent: 50%
  • Min ejection time: 3m

PHASE 5: Distributed Tracing
→ Enabling tracing in mesh config
→ Sampler type: probabilistic, param: 0.1 (10% sample rate)
→ Setting Jaeger collector endpoint: jaeger-collector.jaeger.svc.cluster.local:4317
→ Adding Zipkin exporter for legacy systems: zipkin.zipkin.svc.cluster.local:9411/api/v2/spans

PHASE 6: Telemetry
→ Enabling Prometheus metrics collection
→ Adding custom metrics for business KPIs
→ Configuring access logging with JSON format
→ Stream logs to Loki: http://loki.loki.svc.cluster.local:3100/loki/api/v1/push

PHASE 7: Verification
→ Envoy configuration check: ✓ All proxies updated with new cluster config
→ mTLS status: ✓ 100% of services have STRICT mTLS
→ Circuit breaker test: Service A → Service B failure simulation: ✓ Breaker tripped after threshold
→ Retry test: Simulated 503 error: ✓ 4 retries executed with 2s delay between attempts
→ Tracing test: Generated test span in sample-app: ✓ Span visible in Jaeger UI
→ Metrics check: istio_requests_total increasing: ✓ Prometheus scraping successfully

PHASE 8: Output
→ Ingress Gateway LoadBalancer: nlb-abc123.elb.us-east-1.amazonaws.com
→ Istio Dashboard: https://kiali.istio.svc.cluster.local:20001/kiali
→ Jaeger UI: https://jaeger-query.jaeger.svc.cluster.local:16686
→ Grafana dashboards:
  - Service Mesh Overview: https://grafana.istio.svc.cluster.local/d/istio-service-mesh
  - Circuit Breaker Metrics: https://grafana.istio.svc.cluster.local/d/istio-circuit-breaker
  - Retry Metrics: https://grafana.istio.svc.cluster.local/d/istio-retry-metrics

✓ Service mesh with resilience policies deployed successfully
```

### Example 3: Multi-Region Active-Active with Database Replication

**Input**:
```bash
infrastructure-weaver weave multi-region \
  --primary-region us-east-1 \
  --secondary-region us-west-2 \
  --enable-database-replication \
  --db-engine aurora-postgresql \
  --db-instance-class db.r5.large \
  --db-replica-count 2 \
  --enable-s3-replication \
  --s3-source-bucket company-app-data \
  --s3-destination-bucket company-app-data-replica \
  --replication-time-control 15m \
  --route53-zone example.com \
  --health-check-path /api/health \
  --health-check-port 443 \
  --failover-threshold 3 \
  --enable-cdn \
  --enable-waf \
  --enable-global-database \
  --enable-s3-global-transfer-acceleration
```

**Output**:
```
✓ Validating multi-region prerequisites... OK
✓ Route53 zone example.com found
✓ Confirming replication compatibility... OK

PHASE 1: Primary Region Infrastructure (us-east-1)
→ Creating Aurora PostgreSQL Global Database:
  • Primary cluster: company-db-prim
  • Writer endpoint: company-db-prim.cluster-ro.us-east-1.rds.amazonaws.com
  • Reader endpoint: company-db-prim.cluster-ro.us-east-1.rds.amazonaws.com
  • Global DB ID: global-abc123
→ Configuring replication to secondary regions (2 read replicas)
→ Creating S3 bucket: company-app-data
→ Enabling versioning, encryption (AWS-KMS), access logging
→ Creating KMS key: alias/company-app-data-primary

PHASE 2: Secondary Region Infrastructure (us-west-2)
→ Creating Aurora PostgreSQL read-only cluster:
  • Secondary cluster: company-db-sec
  • Reader endpoint: company-db-sec.cluster-ro.us-west-2.rds.amazonaws.com
  • Replicated from primary via Global Database
→ Creating S3 bucket: company-app-data-replica
→ Configuring S3 Cross-Region Replication (CRR):
  • Replication type: Same-Region Replication (SRR) within bucket for CRR
  • Replicate objects created after: today
  • Replicate delete markers: true
  • Replication time control: 15 minutes
→ Enabling S3 Transfer Acceleration

PHASE 3: Application Load Balancers
→ Primary region ALB: alb-primary-123456.us-east-1.elb.amazonaws.com
→ Secondary region ALB: alb-secondary-789012.us-west-2.elb.amazonaws.com
→ Configuring health checks: GET /api/health, port 443, timeout 5s, 3 failures threshold
→ Target groups registered with respective EKS clusters

PHASE 4: Route53 Configuration
→ Creating latency-based record set for *.apps.example.com:
  • Primary: alb-primary-123456.us-east-1.elb.amazonaws.com (weight 100)
  • Secondary: alb-secondary-789012.us-west-2.elb.amazonaws.com (weight 0 - failover)
→ Creating failover record sets:
  • Primary: health check id abc123, failover PRIMARY
  • Secondary: health check id def456, failover SECONDARY
→ Creating weighted routing for docroot.example.com → S3 static website endpoints

PHASE 5: CloudFront CDN Configuration
→ Creating CloudFront distribution:
  • Origin domain 1: alb-primary-123456.us-east-1.elb.amazonaws.com
  • Origin domain 2: alb-secondary-789012.us-west-2.elb.amazonaws.com (origin failover)
  • Default cache behavior: forward all query strings, cookies
  • Viewer protocol policy: Redirect HTTP to HTTPS
  • Allowed SSL methods: TLSv1.2, TLSv1.3
  • Price class: Price class 100 (North America, Europe)
→ Creating alternate domain: apps.example.com
→ ACM certificate: arn:aws:acm:us-east-1:123:certificate/xxx

PHASE 6: WAF Configuration
→ Creating WAFv2 Web ACL:
  • Rule: AWSManagedRulesCommonRuleSet
  • Rule: AWSManagedRulesAmazonIpReputationList
  • Rate limiting: 2000 requests per 5-minute period per IP
  • Block action for high risk IPs
  • Count action for moderate risk
→ Associating Web ACL with CloudFront distribution

PHASE 7: Cross-Region Networking
→ Creating VPC peering between us-east-1 and us-west-2
→ Updating route tables in both regions for inter-region traffic
→ Configuring security groups:
  • Primary app SG allows outbound to secondary DB on port 5432
  • Secondary DB SG allows inbound from primary app SG on port 5432
→ Testing replication lag: <100ms typical

PHASE 8: Verification
→ Aurora Global Database status: HEALTHY, replication lag: 32ms
→ S3 replication: 0 objects pending, replication enabled
→ Route53 latency probing: ✓ Primary healthy, latency 18ms (us-east-1), 72ms (us-west-2)
→ CloudFront distribution deployed, DNS: d1234abcdefg.cloudfront.net
→ WAF rules active, 0 blocked requests in last 24h
→ Failover simulation: Primary ALB health check failed 3/3 → Traffic shifted to secondary
  • Secondary ALB serving requests: ✓
  • RDS failover initiated: ✓ (60s to promote secondary)
  • Database connection strings updated automatically via Route53

PHASE 9: Output
→ Primary endpoints:
  • ALB: http://alb-primary-123456.us-east-1.elb.amazonaws.com
  • Database: company-db-prim.cluster-ro.us-east-1.rds.amazonaws.com:5432
  • S3: s3://company-app-data
→ Secondary endpoints:
  • ALB: http://alb-secondary-789012.us-west-2.elb.amazonaws.com
  • Database: company-db-sec.cluster-ro.us-west-2.rds.amazonaws.com:5432
  • S3: s3://company-app-data-replica
→ Global endpoints:
  • Apps: https://apps.example.com (Route53 latency-based)
  • CDN: https://d1234abcdefg.cloudfront.net
  • Database connection string: company-db-prim.cluster-ro.us-east-1.rds.amazonaws.com
    (Automatically fails over to secondary via Route53 when needed)
→ CloudWatch Alarms setup:
  • Aurora replica lag > 5 seconds
  • S3 replication objects > 1000 pending
  • Route53 health check failures > 0
  • CloudFront 5xx errors > 1%

✓ Multi-region infrastructure deployed, total time: 34m12s
Monthly estimated cost: Primary region $8,234.56 + Secondary region $6,123.45 = $14,358.01
```

## Environment

Required environment variables:
```bash
# AWS
export AWS_ACCESS_KEY_ID="AKIAIOSFODNN7EXAMPLE"
export AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
export AWS_DEFAULT_REGION="us-east-1"
export AWS_PROFILE="production"

# Azure (if hybrid cloud)
export AZURE_SUBSCRIPTION_ID="xxxx-xxxx-xxxx-xxxx"
export AZURE_TENANT_ID="xxxx-xxxx-xxxx-xxxx"
export AZURE_CLIENT_ID="xxxx-xxxx-xxxx-xxxx"
export AZURE_CLIENT_SECRET="secret"

# GCP (if using GKE)
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/gcp-key.json"
export GCP_PROJECT="my-project-id"

# Infrastructure Weaver specific
export INFRASTRUCTURE_WEAVER_STATE_BUCKET="company-terraform-state"
export INFRASTRUCTURE_WEAVER_STATE_REGION="us-east-1"
export INFRASTRUCTURE_WEAVER_DYNAMODB_TABLE="terraform-locks"
export INFRASTRUCTURE_WEAVER_GITOPS_TOKEN="ghp_xxxxx"  # GitHub token for Flux
export INFRASTRUCTURE_WEAVER_VAULT_ADDR="https://vault.example.com"
export INFRASTRUCTURE_WEAVER_VAULT_TOKEN="s.xxxxx"
export INFRASTRUCTURE_WEAVER_SENSITE                # Prompt for secrets if not in env
```

Configuration file: `~/.infrastructure-weaver/config.yaml`
```yaml
terraform:
  backend: s3
  state_bucket: "company-terraform-state"
  state_region: "us-east-1"
  dynamodb_table: "terraform-locks"
  workspace_prefix: "weaver-"

kubernetes:
  kubeconfig_path: "~/.kube/config"
  context_prefix: "weaver-"
  wait_timeout: "600s"
  resource_timeout: "30m"

gitops:
  flux_version: "0.40.0"
  manifests_dir: "./gitops/clusters"
  suspend_during_deploy: true

monitoring:
  prometheus_retention: "30d"
  grafana_admin_password_secret: "monitoring/grafana-admin"
  alertmanager_receivers:
    - slack: "#infrastructure-alerts"
    - pagerduty: "platform-team"
    - email: "ops@example.com"

chaos:
  enabled: true
  schedule: "0 2 * * 0"  # Every Sunday at 2am
  experiments_dir: "./chaos/experiments"
  allowed_namespaces: ["staging", "testing"]
```

Required IAM Permissions:
- `AmazonEKSClusterPolicy`
- `AmazonEKSWorkerNodePolicy`
- `AmazonEKS_CNI_Policy`
- `AmazonEC2ContainerRegistryReadOnly`
- `AmazonS3FullAccess` (for specific buckets only)
- `AmazonRDSFullAccess` (for Aurora clusters)
- `AmazonRoute53FullAccess`
- `IAMFullAccess` (for service account roles)
- `CloudWatchFullAccess`
- `AWSWAFFullControlPolicy`

Verification Steps:

1. **Cluster Health Check**
```bash
# Check EKS cluster status
kubectl get ns -o wide
kubectl get nodes -o wide
kubectl cluster-info

# Expected:
# - All nodes in Ready state
# - All system pods in Running state
# - Cluster info shows server URL and location
```

2. **Service Mesh Verification**
```bash
# Check Istio pods
kubectl get pods -n istio-system
istioctl proxy-status
istioctl authn tls-check $(kubectl get pod -l app=app -o jsonpath='{.items[0].metadata.name}').default

# Expected:
# - istio-system pods all Running
# - All proxies connected to Istiod
# - mTLS mode: STRICT for all services
```

3. **Circuit Breaker Test**
```bash
# Send requests to trigger circuit breaker
for i in {1..50}; do
  kubectl exec -it deploy/frontend -- curl -s -o /dev/null -w "%{http_code}" http://backend:8080/api
done

# Check breaker status
kubectl exec -it deploy/frontend -- curl -s http://localhost:15000/clusters | jq '.["outbound|8080||backend.default.svc.cluster.local"].circuit_breakers'
```

4. **Auto-scaling Verification**
```bash
# Load test to trigger HPA
kubectl run -it load-generator --image=busybox --rm -it -- /bin/sh -c "while true; do wget -q -O- http://frontend; done"

# In another terminal, watch HPA
kubectl get hpa -w

# Expected: CPU > 70% triggers scale-up, new pods within 2 minutes
```

5. **Multi-region Failover Test**
```bash
# Simulate primary ALB failure
aws elb deregister-instances-from-load-balancer \
  --load-balancer-name alb-primary-123456 \
  --instances i-xxxxxxx

# Monitor Route53 health checks
aws route53 get-health-check --health-check-id abc123

# Expected: After 3 failures, failover to secondary within health check interval (30s)
# Traffic served from secondary region, database promotes read replica
```

6. **S3 Replication Check**
```bash
# Upload test file to primary bucket
echo "test-$(date)" | aws s3 cp - s3://company-app-data/replication-test.txt

# Check replication status
aws s3api list-objects-v2 \
  --bucket company-app-data-replica \
  --prefix replication-test.txt

# Expected: File appears in secondary bucket within 15 minutes (RTC)
```

7. **Security Policies Verification**
```bash
# Check network policies
kubectl get networkpolicy -A

# Test pod-to-pod communication
kubectl exec -it deploy/frontend -- nc -zv backend 8080

# Expected:
# - Network policies enforced, unauthorized connections blocked
# - Only explicitly allowed pod communications succeed
```

8. **Cost Verification**
```bash
# Generate cost report
aws ce get-cost-and-usage \
  --time-period Start=$(date -d '1 day ago' +%Y-%m-%d),End=$(date +%Y-%m-%d) \
  --granularity DAILY \
  --filter '{"Dimensions":{"Key":"SERVICE","Values":["Amazon Elastic Kubernetes Service","Amazon RDS","AmazonCloudFront","AmazonS3"]}}' \
  --metrics "BlendedCost"

# Expected: Costs within 10% of estimate
```

## Troubleshooting

### Issue: Terraform plan fails with "Error: no matches for kind"
**Cause**: Kubernetes provider configuration missing or incorrect API version.
**Fix**: Ensure kubectl context is set correctly before running:
```bash
aws eks update-kubeconfig --region us-east-1 --name production-us-east-1-eks
kubectl config use-context production-us-east-1-eks
```

### Issue: EKS node groups stuck in "CREATING" state
**Cause**: Insufficient EC2 capacity in selected AZs or instance type not available.
**Fix**: Check EC2 capacity:
```bash
aws ec2 describe-spot-placement-groups --filters Name=group-name,Values=eks-node-group-name
# Either spread across more AZs or choose different instance type
```

### Issue: Istio pods CrashLoopBackOff with "Failed to pull image"
**Cause**: Private ECR repository without proper IAM permissions.
**Fix**: Ensure EKS node IAM role has AmazonEC2ContainerRegistryReadOnly policy:
```bash
aws iam attach-role-policy --role-name NodeInstanceRole --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly
```

### Issue: Circuit breaker not activating
**Cause**: DestinationRule not applied or pod selector mismatch.
**Fix**: Verify DestinationRule targets correct service:
```bash
kubectl get destinationrule -o yaml | grep -A 10 "circuitBreaker"
# Check Envoy config: istioctl proxy-config cluster <pod-name>.default
```

### Issue: S3 replication not working
**Cause**: IAM role for replication missing or S3 versioning not enabled on source.
**Fix**:
```bash
# Enable versioning on source bucket
aws s3api put-bucket-versioning --bucket company-app-data --versioning-configuration Status=Enabled

# Check replication role
aws iam get-role --role-name aws-route53-replicator
# Ensure role trust relationship includes s3.amazonaws.com
```

### Issue: Multi-region failover not triggering
**Cause**: Health check path incorrect or target group misconfigured.
**Fix**:
```bash
aws route53 get-health-check --health-check-id abc123
aws elb describe-target-groups --load-balancer-arns $(aws elb describe-load-balancers --names alb-primary-123456 --query 'LoadBalancers[].LoadBalancerArn' --output text)
# Verify health check path matches application /health endpoint
```

### Issue: Prometheus not scraping metrics
**Cause**: ServiceMonitor CRD not installed or missing labels.
**Fix**:
```bash
kubectl get servicemonitor -A
# Ensure service has label: prometheus.io/scrape: "true"
# Check Prometheus target: kubectl --namespace monitoring port-forward svc/prometheus-operated 9090:9090
# Visit http://localhost:9090/targets
```

### Issue: Flux fails to reconcile with "Git error"
**Cause**: GitHub token expired or repository access revoked.
**Fix**: Update GitRepository CR with new token:
```bash
kubectl -n flux-system edit gitrepository cluster-repo
# Update secretRef:
#   - name: github-auth
#     namespace: flux-system
```

### Issue: Cross-region replication lag too high (>15 minutes)
**Cause**: Large objects, bandwidth constraints, or RTO exceeded.
**Fix**:
- Enable S3 Transfer Acceleration for faster cross-region transfers
- Implement S3 Batch Operations for large initial sync
- Increase replication time control window
- Check CloudWatch metrics for ReplicationLatency

### Issue: Database failover does not complete
**Cause**: Application not handling DNS TTL changes or connection pooling issues.
**Fix**:
- Reduce DB endpoint DNS TTL to 60 seconds
- Implement connection string retry logic in application
- Use Amazon RDS Proxy to handle failover transparently:
```bash
aws rds create-db-proxy --db-proxy-name app-proxy \
  --engine-family POSTGRESQL \
  --auth "SecretArn=arn:aws:secretsmanager:...:secret:db-password, IAMAuth=DISABLED, Username=postgres" \
  --role-arn arn:aws:iam::123:role/RDSProxyRole \
  --vpc-subnet-ids subnet-abc123, subnet-def456 \
  --vpc-security-group-ids sg-0123456789abcdef0
```

### Issue: HPA not scaling despite high CPU
**Cause**: Metrics not available or resource requests not set on pods.
**Fix**:
```bash
kubectl describe hpa frontend
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/namespaces/default/pods/*" | jq .
# Ensure containerspec has resources.requests.cpu defined
```

## Rollback Commands

Infrastructure Weaver maintains full Terraform state and GitOps history for safe rollbacks.

### 1. Terraform Rollback (Infrastructure)

```bash
# List available Terraform workspaces (each deployment is a workspace)
cd /path/to/infrastructure-weaver/terraform/environments/production/us-east-1/eks
terraform workspace list
# Output:
#* current
#  weaver-2024-01-15-143022
#  weaver-2024-01-22-091555

# Select previous workspace to rollback
terraform workspace select weaver-2024-01-15-143022

# Create terraform.tfvars from previous deployment (saved in S3)
aws s3 cp s3://company-terraform-state/production/us-east-1/eks/terraform.tfstate.backup-2024-01-15-143022 terraform.tfstate.backup

# Option 1: Force restore state (immediate, no validation)
terraform apply -auto-approve -state=terraform.tfstate.backup

# Option 2: Plan first to see changes
terraform plan -state=terraform.tfstate.backup
# If changes acceptable:
terraform apply -state=terraform.tfstate.backup

# Rollback to current workspace after verification
terraform workspace select current
```

### 2. Kubernetes Rollback via GitOps (Application)

Infrastructure Weaver uses Flux for GitOps. Every deployment is a commit.

```bash
# View Git history of cluster manifests
cd /path/to/k8s-manifests
git log --oneline --all -- graph

# Find commit hash before problematic deployment
git log --oneline -10
# Output:
# abc1234 Deploy payment-service v2.1.0 with circuit breakers
# def5678 Fix HPA for payment-service
# ghi9012 Rollback payment-service to v2.0.3

# Revert that specific commit
git revert --no-commit abc1234
git commit -m "Rollback payment-service to v2.0.3 (accidental rollout v2.1.0 breaks payments)"
git push origin production

# Flux automatically detects changes and applies them
kubectl get kustomization -n flux-system -o wide
# Watch reconciliation
kubectl get pods -n payment-processor -w
# Pods restart with old version within 2-5 minutes
```

### 3. Helm Rollback (If not using GitOps)

```bash
# List release history
helm list -A | grep payment-processor
# OR
helm history release/payment-processor -n payment-processor

# Rollback to specific revision
helm rollback release/payment-processor 5 -n payment-processor

# Verify rollback
helm status release/payment-processor -n payment-processor
kubectl get pods -n payment-processor -l app.kubernetes.io/instance=release
```

### 4. Database Rollback (Aurora PostgreSQL)

```bash
# Promote a read replica to primary (for failback after multi-region failover)
aws rds promote-read-replica \
  --db-instance-identifier company-db-sec \
  --region us-west-2

# Update Route53 to point back to primary
aws route53 change-resource-record-sets --hosted-zone-id Z123456ABC \
  --change-batch '{
    "Comment": "Failback to primary region",
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "apps.example.com.",
        "Type": "A",
        "SetIdentifier": "primary",
        "Weight": 100,
        "Failover": "PRIMARY",
        "AliasTarget": {
          "HostedZoneId": "Z35SXDOTRQ7X7K",
          "DNSName": "alb-primary-123456.us-east-1.elb.amazonaws.com."
        }
      }
    }]
  }'
```

### 5. S3 Bucket Rollback (Object Level)

```bash
# List object versions
aws s3api list-object-versions \
  --bucket company-app-data \
  --prefix config/

# Restore specific object to previous version
aws s3api copy-object \
  --bucket company-app-data \
  --copy-source company-app-data/config/app-config.json?versionId=abc123def456 \
  --key config/app-config.json

# Or rollback entire bucket to previous state via S3 Batch Operations
# (Create manifest of all current versions, copy from backup prefix)
```

### 6. Complete Region Rollback

If entire region deployment needs to be rolled back:

```bash
# Destroy entire Terraform stack for region
cd /path/to/terraform/environments/production/us-west-2
terraform workspace select weaver-2024-01-22-091555
terraform destroy -auto-approve

# Remove cluster from Route53
aws route53 change-resource-record-sets --hosted-zone-id Z123456ABC \
  --change-batch '{
    "Comment": "Remove failed region",
    "Changes": [{
      "Action": "DELETE",
      "ResourceRecordSet": {
        "Name": "apps.example.com.",
        "Type": "A",
        "SetIdentifier": "secondary",
        "Weight": 0,
        "Failover": "SECONDARY",
        "AliasTarget": {
          "HostedZoneId": "Z987654DEF",
          "DNSName": "alb-secondary-789012.us-west-2.elb.amazonaws.com."
        }
      }
    }]
  }'

# Disable S3 replication
aws s3api put-bucket-replication \
  --bucket company-app-data \
  --replication-configuration '{"Role": "arn:aws:iam::123:role/s3-replication-role", "Rules": []}'
```

### 7. Database Point-in-Time Recovery (PITR)

```bash
# Restore database to specific time
aws rds restore-db-cluster-to-point-in-time \
  --db-cluster-identifier company-db-prim \
  --target-db-cluster-identifier company-db-prim-restored \
  --restore-to-time 2024-01-20T14:30:00Z \
  --region us-east-1

# Once verified, switch applications to new endpoint
# Update ConfigMap/Secret in Kubernetes:
kubectl -n payment-processor create secret generic db-connection \
  --from-literal=host=company-db-prim-restored.cluster-ro.us-east-1.rds.amazonaws.com \
  --dry-run=client -o yaml | kubectl apply -f -
```

### 8. Network Security Rollback

```bash
# Rollback security group changes
aws ec2 describe-security-groups --group-ids sg-12345678 > security-groups-backup.json

# To restore: Delete modified SG and recreate from backup
aws ec2 revoke-security-group-ingress --group-id sg-12345678 \
  --protocol tcp --port 5432 --cidr 0.0.0.0/0  # Remove dangerous open rule

# Or replace entire SG (creates new SG, requires updating ENIs)
# Best: Use Terraform to rollback state which manages SGs
```

### 9. Chaos Experiment Rollback

```bash
# List running experiments
kubectl get chaosexperiments -A

# Terminate specific experiment
kubectl delete chaosexperiment network-chaos -n production

# Or disable Chaos Mesh entirely if troubleshooting
kubectl patch cm chaos-mesh-config -n chaos-mesh -p '{"data":{"enable":"false"}}'
```

### 10. Rollback Verification Checklist

After any rollback, ALWAYS verify:

```bash
# 1. Cluster health
kubectl get ns,k8sevents,csinodes

# 2. Pod status
kubectl get pods -A | grep -v Running

# 3. Service endpoints
kubectl get endpoints -A

# 4. Database connectivity
kubectl exec -it deploy/frontend -- nc -zv database 5432

# 5. Route53 health checks
aws route53 get-health-check --health-check-id abc123 | grep StatusReport

# 6. Application smoke test
for service in payment user notification analytics; do
  curl -s -o /dev/null -w "%{http_code}" https://$service.example.com/health || echo "$service FAILED"
done

# 7. Metrics and logs
kubectl logs -l app=frontend --tail=50 --since=5m | grep -i error | wc -l
```

Rollback Time Estimates:
- Terraform infrastructure rollback: 15-30 minutes
- GitOps application rollback: 2-5 minutes (pods restart)
- Database PITR: 10-20 minutes to new cluster + application config update
- Multi-region failback: 5-10 minutes (DNS TTL dependent)
- S3 object rollback: Instant (unless cross-region replication sync lag)

All rollback operations are logged in CloudTrail and require IAM approval for production environments via Infrastructure Weaver's approval workflow.
```