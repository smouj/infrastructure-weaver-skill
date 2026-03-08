---
name: Infrastructure Weaver
description: Construye arquitecturas de sistemas interconectadas y resilientes
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

Construye arquitecturas de sistemas interconectadas y resilientes con failover automatizado, balanceo de carga e integración de service mesh.

## Propósito

Infrastructure Weaver automatiza el despliegue y configuración de arquitecturas de sistemas interconectadas de nivel de producción. Maneja la creación de despliegues multi-AZ/multi-región, integración de service mesh, circuit breakers, lógica de reintentos, políticas de auto-escalado y grupos de seguridad de red.

### Casos de Uso Reales

- **Despliegue multi-región activo-activo**: Desplegar pilas de aplicación a través de us-east-1 y us-west-2 con Route53 enrutamiento basado en latencia, replicación de bases de datos y automatización de failover entre regiones.
  
- **Service mesh con seguridad zero-trust**: Instalar y configurar Istio con mTLS, inyección automática de sidecar, políticas RBAC granulares e integración de telemetría con Prometheus/Grafana.

- **Arquitectura de microservices resiliente**: Desplegar microservices con HPA/VPA, pod disruption budgets, probes de readiness/liveness, circuit breakers (Envoy/Istio) y distributed tracing (Jaeger/Zipkin).

- **Conectividad de nube híbrida**: Establecer túneles VPN o VPC peering entre AWS VPCs y Azure VNets, configurar Transit Gateway y configurar balanceo de carga entre nubes.

- **Migración de Infrastructure as Code**: Convertir recursos existentesaprovisionados manualmente en módulos de Terraform, implementar detección de drift y establecer pipelines CI/CD para cambios de infraestructura.

## Alcance

### Comandos Principales

`infrastructure-weaver weave <architecture-type> --environment <env> --region <region> [flags]`

Tipos de arquitectura soportados:
- `kubernetes-cluster` - K8s cluster completo con node pools, networking (Calico/Cilium), ingress (Nginx/ALB), storage classes
- `service-mesh` - Instalación Istio/Consul con configuraciones de gateway, virtual services, destination rules
- `microservices-app` - Stack completo de microservices con databases (PostgreSQL/Redis), message queues (RabbitMQ/Kafka), monitoring stack
- `multi-region` - Replicación entre regiones con global load balancer, replicación de bases de datos y S3 cross-region sync
- `hybrid-cloud` - Conectividad AWS-Azure/GCP con VPN, peering y unified networking

### Flags de Comando

```bash
# Ejemplo: Desplegar Kubernetes cluster multi-región con GitOps
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

# Ejemplo: Desplegar service mesh con gestión de tráfico avanzada
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

# Ejemplo: Desplegar aplicación de microservices resiliente con circuit breakers
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

# Ejemplo: Establecer replicación multi-región con Route53 failover
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

# Ejemplo: Configurar conectividad de nube híbrida entre AWS y Azure
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

## Proceso de Trabajo

1. **Fase de Validación**
   - Verificar que todas las dependencias requeridas estén instaladas y accesibles
   - Validar parámetros de entrada contra el esquema para el tipo de arquitectura seleccionado
   - Buscar recursos existentes para prevenir conflictos
   - Confirmar permisos IAM/de la nube para la cuenta objetivo
   - Validar rangos CIDR de red para superposiciones

2. **Fase de Planificación**
   - Generar archivos de plan Terraform con todos los recursos requeridos
   - Renderizar manifiestos de Kubernetes con plantillas Kustomize/Helm
   - Calcular requisitos de recursos basados en los flags proporcionados
   - Crear orden de ejecución para recursos dependientes
   - Generar hooks de pre y post despliegue

3. **Aprovisionamiento de Infraestructura**
   - Crear VPCs, subnets, NAT gateways, internet gateways
   - Desplegar clusters EKS/AKS/GKE con configuraciones especificadas
   - Configurar managed node groups con auto-escalado
   - Configurar storage classes, persistent volume claims
   - Crear instancias RDS/ElastiCache/Cloud SQL con replicación
   - Establecer VPC peering o Transit Gateway attachments

4. **Integración de Service Mesh**
   - Instalar operador Istio con configuración personalizada
   - Configurar recursos de gateway (LoadBalancer/NodePort)
   - Configurar mTLS y políticas de autenticación
   - Habilitar recolección de telemetría (métricas Prometheus)
   - Configurar endpoints de distributed tracing
   - Desplegar Envoy filters para gestión de tráfico avanzada

5. **Despliegue de Aplicación**
   - Construir y push de imágenes de contenedor al registry
   - Crear namespaces de Kubernetes con resource quotas
   - Desplegar ConfigMaps y Secrets (desde SOPS/SealedSecrets)
   - Desplegar deployments con pod disruption budgets
   - Configurar HPA/VPA para auto-escalado
   - Configurar recursos de ingress con terminación TLS

6. **Configuración de Resiliencia**
   - Implementar probes de readiness y liveness
   - Configurar circuit breakers a nivel de service mesh
   - Configurar retry budgets y políticas de timeout
   - Configurar parámetros de connection pooling
   - Desplegar experiments de chaos engineering (opcional)

7. **Hardening de Seguridad**
   - Aplicar network policies para restringir comunicación entre pods
   - Configurar Pod Security Standards (restricted/privileged)
   - Configurar integración OIDC/IAM para service accounts
   - Habilitar audit logging a CloudWatch/Splunk
   - Aplicar security context constraints

8. **Monitorización y Alertas**
   - Desplegar Prometheus Stack (Prometheus, Alertmanager, Grafana)
   - Configurar recolección de métricas para todos los servicios
   - Crear dashboards de Grafana desde plantillas
   - Configurar reglas de enrutamiento de Alertmanager y configuraciones de receivers
   - Instalar stack de logging (Loki/Fluentd/Elasticsearch)
   - Configurar agregación y retención de logs

9. **Fase de Verificación**
   - Ejecutar health checks en todos los recursos desplegados
   - Verificar que mTLS del service mesh está funcionando
   - Probar escenarios de failover para configuraciones multi-región
   - Validar políticas de auto-escalado
   - Ejecutar pruebas de conectividad entre servicios
   - Verificar reglas de security group y network policy

10. **Generación de Salida**
    - Generar connection strings y endpoints
    - Crear archivos kubeconfig para acceso al cluster
    - Output ubicación del archivo de estado Terraform
    - Generar configuración GitOps para gestión continua
    - Crear runbook con procedimientos operativos
    - Proporcionar reporte de estimación de costos

## Reglas de Oro

- **Siempre usar Terraform state backend**: Nunca usar estado local. Configurar S3/Azure Storage/GCS con state locking via DynamoDB.
- **Implementar IAM de menor privilegio**: Cada service account recibe solo los permisos que requiere. Usar federación OpenID Connect en lugar de claves de largo plazo.
- **Forzar mTLS en todos lados**: Toda comunicación servicio-a-servicio debe usar mutual TLS forzada por políticas de service mesh.
- **Nunca exponer bases de datos públicamente**: Todas las bases de datos deben estar en subnets privadas con restricciones de security group.
- **Resource tagging es obligatorio**: Todos los recursos aprovisionados deben tener tags estandarizados: Environment, Team, Owner, Project, ManagedBy.
- **Infraestructura inmutable**: Sin actualizaciones in-place. Todos los cambios van a través de Terraform/Helm con configuraciones versionadas.
- **Backup de todo**: Todos los datos stateful deben tener backups automatizados con procedimientos de restore probados.
- **Chaos engineering semanal**: Programar experiments de chaos semanalmente para validar resiliencia. Documentar todos los escenarios de fallo.
- **Multi-AZ por defecto**: Todas las cargas de trabajo de producción deben abarcar al menos 3 availability zones a menos que estén limitadas por costos.
- **Gestión de secrets**: Nunca almacenar secrets en Git. Usar HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, o archivos encriptados con SOPS.

## Ejemplos

### Ejemplo 1: Kubernetes Cluster de Producción con GitOps

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

### Ejemplo 2: Service Mesh con Circuit Breakers y Retry Policies

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

### Ejemplo 3: Multi-Región Activo-Activo con Replicación de Bases de Datos

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

## Entorno

Variables de entorno requeridas:
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

Archivo de configuración: `~/.infrastructure-weaver/config.yaml`
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

Permisos IAM Requeridos:
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

Pasos de Verificación:

1. **Health Check del Cluster**
```bash
# Verificar estado del cluster EKS
kubectl get ns -o wide
kubectl get nodes -o wide
kubectl cluster-info

# Esperado:
# - Todos los nodos en estado Ready
# - Todos los pods del sistema en estado Running
# - La información del cluster muestra URL del servidor y ubicación
```

2. **Verificación de Service Mesh**
```bash
# Verificar pods de Istio
kubectl get pods -n istio-system
istioctl proxy-status
istioctl authn tls-check $(kubectl get pod -l app=app -o jsonpath='{.items[0].metadata.name}').default

# Esperado:
# - Todos los pods de istio-system en Running
# - Todos los proxies conectados a Istiod
# - Modo mTLS: STRICT para todos los servicios
```

3. **Prueba de Circuit Breaker**
```bash
# Enviar requests para activar circuit breaker
for i in {1..50}; do
  kubectl exec -it deploy/frontend -- curl -s -o /dev/null -w "%{http_code}" http://backend:8080/api
done

# Verificar estado del breaker
kubectl exec -it deploy/frontend -- curl -s http://localhost:15000/clusters | jq '.["outbound|8080||backend.default.svc.cluster.local"].circuit_breakers'
```

4. **Verificación de Auto-escalado**
```bash
# Load test para activar HPA
kubectl run -it load-generator --image=busybox --rm -it -- /bin/sh -c "while true; do wget -q -O- http://frontend; done"

# En otro terminal, observar HPA
kubectl get hpa -w

# Esperado: CPU > 70% activa scale-up, nuevos pods dentro de 2 minutos
```

5. **Prueba de Failover Multi-región**
```bash
# Simular fallo del ALB primario
aws elb deregister-instances-from-load-balancer \
  --load-balancer-name alb-primary-123456 \
  --instances i-xxxxxxx

# Monitorear health checks de Route53
aws route53 get-health-check --health-check-id abc123

# Esperado: Después de 3 fallos, failover a secundario dentro del intervalo de health check (30s)
# Tráfico servido desde región secundaria, base de datos promociona read replica
```

6. **Verificación de Replicación S3**
```bash
# Subir archivo de prueba al bucket primario
echo "test-$(date)" | aws s3 cp - s3://company-app-data/replication-test.txt

# Verificar estado de replicación
aws s3api list-objects-v2 \
  --bucket company-app-data-replica \
  --prefix replication-test.txt

# Esperado: Archivo aparece en bucket secundario dentro de 15 minutos (RTC)
```

7. **Verificación de Políticas de Seguridad**
```bash
# Verificar network policies
kubectl get networkpolicy -A

# Probar comunicación pod-to-pod
kubectl exec -it deploy/frontend -- nc -zv backend 8080

# Esperado:
# - Network policies aplicadas, conexiones no autorizadas bloqueadas
# - Solo comunicaciones explícitamente permitidas entre pods tienen éxito
```

8. **Verificación de Costos**
```bash
# Generar reporte de costos
aws ce get-cost-and-usage \
  --time-period Start=$(date -d '1 day ago' +%Y-%m-%d),End=$(date +%Y-%m-%d) \
  --granularity DAILY \
  --filter '{"Dimensions":{"Key":"SERVICE","Values":["Amazon Elastic Kubernetes Service","Amazon RDS","AmazonCloudFront","AmazonS3"]}}' \
  --metrics "BlendedCost"

# Esperado: Costos dentro del 10% de la estimación
```

## Solución de Problemas

### Issue: Terraform plan falla con "Error: no matches for kind"
**Causa**: Configuración del provider Kubernetes faltante o incorrecta versión API.
**Fix**: Asegurar que el contexto kubectl esté configurado correctamente antes de ejecutar:
```bash
aws eks update-kubeconfig --region us-east-1 --name production-us-east-1-eks
kubectl config use-context production-us-east-1-eks
```

### Issue: EKS node groups atascados en estado "CREATING"
**Causa**: Capacidad EC2 insuficiente en los AZs seleccionados o tipo de instancia no disponible.
**Fix**: Verificar capacidad EC2:
```bash
aws ec2 describe-spot-placement-groups --filters Name=group-name,Values=eks-node-group-name
# Ya sea distribuir en más AZs o elegir tipo de instancia diferente
```

### Issue: Pods de Istio CrashLoopBackOff con "Failed to pull image"
**Causa**: Repositorio ECR privado sin permisos IAM apropiados.
**Fix**: Asegurar que el role IAM de los nodos EKS tenga política AmazonEC2ContainerRegistryReadOnly:
```bash
aws iam attach-role-policy --role-name NodeInstanceRole --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly
```

### Issue: Circuit breaker no se activa
**Causa**: DestinationRule no aplicada o selector de pods no coincide.
**Fix**: Verificar que DestinationRule apunta al servicio correcto:
```bash
kubectl get destinationrule -o yaml | grep -A 10 "circuitBreaker"
# Verificar config Envoy: istioctl proxy-config cluster <pod-name>.default
```

### Issue: Replicación S3 no funciona
**Causa**: Role IAM para replicación faltante o versioning S3 no habilitado en fuente.
**Fix**:
```bash
# Habilitar versioning en bucket fuente
aws s3api put-bucket-versioning --bucket company-app-data --versioning-configuration Status=Enabled

# Verificar role de replicación
aws iam get-role --role-name aws-route53-replicator
# Asegurar que trust relationship incluye s3.amazonaws.com
```

### Issue: Failover multi-región no se activa
**Causa**: Health check path incorrecta o target group mal configurada.
**Fix**:
```bash
aws route53 get-health-check --health-check-id abc123
aws elb describe-target-groups --load-balancer-arns $(aws elb describe-load-balancers --names alb-primary-123456 --query 'LoadBalancers[].LoadBalancerArn' --output text)
# Verificar que health check path coincida con endpoint /health de la aplicación
```

### Issue: Prometheus no recolecta métricas
**Causa**: ServiceMonitor CRD no instalado o labels faltantes.
**Fix**:
```bash
kubectl get servicemonitor -A
# Asegurar que servicio tiene label: prometheus.io/scrape: "true"
# Verificar targets Prometheus: kubectl --namespace monitoring port-forward svc/prometheus-operated 9090:9090
# Visitar http://localhost:9090/targets
```

### Issue: Flux falla en reconcile con "Git error"
**Causa**: Token de GitHub expirado o acceso al repositorio revocado.
**Fix**: Actualizar GitRepository CR con nuevo token:
```bash
kubectl -n flux-system edit gitrepository cluster-repo
# Actualizar secretRef:
#   - name: github-auth
#     namespace: flux-system
```

### Issue: Replicación entre regiones con lag muy alta (>15 minutos)
**Causa**: Objetos grandes, limitaciones de ancho de banda, o RTO excedido.
**Fix**:
- Habilitar S3 Transfer Acceleration para transferencias entre regiones más rápidas
- Implementar S3 Batch Operations para sync inicial grande
- Aumentar ventana de replicación time control
- Verificar métricas CloudWatch para ReplicationLatency

### Issue: Failover de base de datos no completa
**Causa**: Aplicación no maneja cambios DNS TTL o problemas de connection pooling.
**Fix**:
- Reducir DNS TTL del endpoint de DB a 60 segundos
- Implementar lógica de retry de connection string en aplicación
- Usar Amazon RDS Proxy para manejar failover transparentemente:
```bash
aws rds create-db-proxy --db-proxy-name app-proxy \
  --engine-family POSTGRESQL \
  --auth "SecretArn=arn:aws:secretsmanager:...:secret:db-password, IAMAuth=DISABLED, Username=postgres" \
  --role-arn arn:aws:iam::123:role/RDSProxyRole \
  --vpc-subnet-ids subnet-abc123, subnet-def456 \
  --vpc-security-group-ids sg-0123456789abcdef0
```

### Issue: HPA no escala a pesar de CPU alta
**Causa**: Métricas no disponibles o resource requests no configurados en pods.
**Fix**:
```bash
kubectl describe hpa frontend
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/namespaces/default/pods/*" | jq .
# Asegurar que containerspec tiene resources.requests.cpu definido
```

## Comandos de Rollback

Infrastructure Weaver mantiene estado completo de Terraform e historial de GitOps para rollbacks seguros.

### 1. Rollback Terraform (Infraestructura)

```bash
# Listar Terraform workspaces disponibles (cada despliegue es un workspace)
cd /path/to/infrastructure-weaver/terraform/environments/production/us-east-1/eks
terraform workspace list
# Output:
#* current
#  weaver-2024-01-15-143022
#  weaver-2024-01-22-091555

# Seleccionar workspace anterior para rollback
terraform workspace select weaver-2024-01-15-143022

# Crear terraform.tfvars desde despliegue anterior (guardado en S3)
aws s3 cp s3://company-terraform-state/production/us-east-1/eks/terraform.tfstate.backup-2024-01-15-143022 terraform.tfstate.backup

# Opción 1: Restaurar estado por fuerza (inmediato, sin validación)
terraform apply -auto-approve -state=terraform.tfstate.backup

# Opción 2: Planificar primero para ver cambios
terraform plan -state=terraform.tfstate.backup
# Si cambios aceptables:
terraform apply -state=terraform.tfstate.backup

# Rollback a workspace actual después de verificación
terraform workspace select current
```

### 2. Rollback Kubernetes via GitOps (Aplicación)

Infrastructure Weaver usa Flux para GitOps. Cada despliegue es un commit.

```bash
# Ver historial Git de manifiestos del cluster
cd /path/to/k8s-manifests
git log --oneline --all -- graph

# Encontrar hash de commit antes del despliegue problemático
git log --oneline -10
# Output:
# abc1234 Deploy payment-service v2.1.0 with circuit breakers
# def5678 Fix HPA for payment-service
# ghi9012 Rollback payment-service to v2.0.3

# Revertir ese commit específico
git revert --no-commit abc1234
git commit -m "Rollback payment-service to v2.0.3 (accidental rollout v2.1.0 breaks payments)"
git push origin production

# Flux detecta cambios automáticamente y los aplica
kubectl get kustomization -n flux-system -o wide
# Observar reconciliation
kubectl get pods -n payment-processor -w
# Pods reinician con versión antigua dentro de 2-5 minutos
```

### 3. Rollback Helm (Si no se usa GitOps)

```bash
# Listar historial de releases
helm list -A | grep payment-processor
# O
helm history release/payment-processor -n payment-processor

# Rollback a revisión específica
helm rollback release/payment-processor 5 -n payment-processor

# Verificar rollback
helm status release/payment-processor -n payment-processor
kubectl get pods -n payment-processor -l app.kubernetes.io/instance=release
```

### 4. Rollback de Base de Datos (Aurora PostgreSQL)

```bash
# Promover una read replica a primary (para failback después de failover multi-región)
aws rds promote-read-replica \
  --db-instance-identifier company-db-sec \
  --region us-west-2

# Actualizar Route53 para apuntar de vuelta a primary
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

### 5. Rollback de Bucket S3 (Nivel Objeto)

```bash
# Listar versiones de objetos
aws s3api list-object-versions \
  --bucket company-app-data \
  --prefix config/

# Restaurar objeto específico a versión anterior
aws s3api copy-object \
  --bucket company-app-data \
  --copy-source company-app-data/config/app-config.json?versionId=abc123def456 \
  --key config/app-config.json

# O rollback bucket completo a estado anterior via S3 Batch Operations
# (Crear manifest de todas las versiones actuales, copiar desde backup prefix)
```

### 6. Rollback Completo de Región

Si región entera necesita rollback:

```bash
# Destroy stack completo de Terraform para región
cd /path/to/terraform/environments/production/us-west-2
terraform workspace select weaver-2024-01-22-091555
terraform destroy -auto-approve

# Remover cluster de Route53
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

# Deshabilitar replicación S3
aws s3api put-bucket-replication \
  --bucket company-app-data \
  --replication-configuration '{"Role": "arn:aws:iam::123:role/s3-replication-role", "Rules": []}'
```

### 7. Recuperación Point-in-Time de Base de Datos (PITR)

```bash
# Restaurar base de datos a tiempo específico
aws rds restore-db-cluster-to-point-in-time \
  --db-cluster-identifier company-db-prim \
  --target-db-cluster-identifier company-db-prim-restored \
  --restore-to-time 2024-01-20T14:30:00Z \
  --region us-east-1

# Una vez verificado, cambiar aplicaciones a nuevo endpoint
# Actualizar ConfigMap/Secret en Kubernetes:
kubectl -n payment-processor create secret generic db-connection \
  --from-literal=host=company-db-prim-restored.cluster-ro.us-east-1.rds.amazonaws.com \
  --dry-run=client -o yaml | kubectl apply -f -
```

### 8. Rollback de Seguridad de Red

```bash
# Rollback de cambios en security groups
aws ec2 describe-security-groups --group-ids sg-12345678 > security-groups-backup.json

# Para restaurar: Eliminar SG modificado y recrear desde backup
aws ec2 revoke-security-group-ingress --group-id sg-12345678 \
  --protocol tcp --port 5432 --cidr 0.0.0.0/0  # Remover regla peligrosa abierta

# O reemplazar SG completo (crea nuevo SG, requiere actualizar ENIs)
# Mejor: Usar Terraform para rollback de estado que gestiona SGs
```

### 9. Rollback de Chaos Experiment

```bash
# Listar experiments en ejecución
kubectl get chaosexperiments -A

# Terminar experiment específico
kubectl delete chaosexperiment network-chaos -n production

# O deshabilitar Chaos Mesh completamente si hay problemas
kubectl patch cm chaos-mesh-config -n chaos-mesh -p '{"data":{"enable":"false"}}'
```

### 10. Checklist de Verificación Post-Rollback

Después de cualquier rollback, SIEMPRE verificar:

```bash
# 1. Health del cluster
kubectl get ns,k8sevents,csinodes

# 2. Estado de pods
kubectl get pods -A | grep -v Running

# 3. Endpoints de servicio
kubectl get endpoints -A

# 4. Conectividad de base de datos
kubectl exec -it deploy/frontend -- nc -zv database 5432

# 5. Health checks de Route53
aws route53 get-health-check --health-check-id abc123 | grep StatusReport

# 6. Smoke test de aplicación
for service in payment user notification analytics; do
  curl -s -o /dev/null -w "%{http_code}" https://$service.example.com/health || echo "$service FAILED"
done

# 7. Métricas y logs
kubectl logs -l app=frontend --tail=50 --since=5m | grep -i error | wc -l
```

Estimaciones de Tiempo de Rollback:
- Rollback de infraestructura Terraform: 15-30 minutos
- Rollback de aplicación GitOps: 2-5 minutos (reinicio de pods)
- Base de datos PITR: 10-20 minutos a nuevo cluster + actualización de configuración de aplicación
- Failback multi-región: 5-10 minutos (dependiente de TTL DNS)
- Rollback de objeto S3: Instantáneo (a menos que lag de sync de replicación entre regiones)

Todas las operaciones de rollback están logueadas en CloudTrail y requieren aprobación IAM para entornos de producción via workflow de aprobación de Infrastructure Weaver.
```

## Requisitos

Dependencias del sistema:
```bash
# Verificar versiones mínimas
terraform version    # >= 1.5.0
kubectl version --client   # >= 1.28.0
helm version --short       # >= 3.12.0
istioctl version           # >= 1.19.0
jq --version              # >= 1.6
yq --version              # >= 4.30.0

# Herramientas cloud requeridas
aws --version
az version
gcloud version
docker --version
```

Requisitos de red:
- Acceso a APIs de nubes (AWS, Azure, GCP) desde ejecutor
- Conectividad saliente a repositorios de imágenes de contenedor
- Resolución DNS para endpoints de servicio
- Ancho de banda suficiente para transferencia de datos entre regiones

Requisitos de IAM:
- Permisos de administrador o roles específicos listados en sección de permisos IAM
- Acceso a buckets S3 para estado Terraform
- Permisos para crear/modificar recursos en todas las cuentas objetivo
- Capacidad de crear roles IAM y políticas

Requisitos de Kubernetes:
- Cluster existente o capacidad de crear nuevo cluster
- CNI instalado (Calico, Cilium, o similar)
- Storage classes definidas
- Métricas server habilitado para HPA
- CRDs de service mesh (Istio/Consul) Disponibles

Requisitos de Storage:
- Backend state Terraform configurado (S3/Azure Storage/GCS)
- DynamoDB/Tabla de locks para Terraform state locking
- Buckets S3 con versioning habilitado para artefactos
- KMS keys para encriptación de datos sensibles

Requisitos de Seguridad:
- Vault/SOPS para gestión de secrets
- Certificados TLS para ingress
- Policies de seguridad definidas (Pod Security Standards, Network Policies)
- Audit logging habilitado en todos los servicios

## Formato de Salida Requerido

Infrastructure Weaver produce output estructurado en fases:

```
✓ [Validación] Nombre de la comprobación... ESTADO

PHASE N: Nombre de Fase
→ Descripción de acción específica
→ Otra acción con detalles

PHASE N+1: Siguiente Fase
...

✓ [Resumen] Mensaje final de completamiento

Next steps:
1. Lista de acciones posteriores al despliegue
```

Formato de output JSON para integración con CI/CD:
```json
{
  "deployment_id": "weaver-2024-01-15-143022",
  "status": "COMPLETED",
  "phases": [
    {
      "name": "Validation",
      "checks": [
        {"name": "Dependencies", "status": "OK"},
        {"name": "Parameters", "status": "OK"}
      ]
    },
    {
      "name": "Infrastructure Provisioning",
      "resources_created": 42,
      "duration_seconds": 1205
    }
  ],
  "outputs": {
    "kubeconfig_path": "~/.kube/config-production-us-east-1",
    "terraform_state": "s3://bucket/path/terraform.tfstate",
    "endpoints": {
      "cluster": "https://api.example.com",
      "ingress": "alb-123.elb.amazonaws.com"
    }
  },
  "cost_estimate_monthly": 12456.78,
  "warnings": [],
  "errors": []
}
```

Logs estructurados en formato JSON Lines:
```json
{"timestamp":"2024-01-15T10:30:00Z","level":"INFO","phase":"Validation","message":"Checking dependencies"}
{"timestamp":"2024-01-15T10:30:05Z","level":"INFO","phase":"Terraform","message":"Initializing backend","details":{"backend":"s3","bucket":"company-terraform-state"}}
{"timestamp":"2024-01-15T10:35:00Z","level":"SUCCESS","phase":"Verification","message":"All health checks passed"}
```

Metricas Prometheus exportadas:
```
infrastructure_weaver_deployment_duration_seconds{architecture="kubernetes-cluster",region="us-east-1",status="success"} 1205.34
infrastructure_weaver_resources_total{type="vpc"} 1
infrastructure_weaver_resources_total{type="eks_cluster"} 1
infrastructure_weaver_deployment_success_total 1
```

Toda la salida debe incluir:
- Timestamps en UTC
- Correlación IDs para tracer debugging
- Niveles de log apropiados (DEBUG, INFO, WARN, ERROR, SUCCESS)
- Duración de cada fase
- IDs de recursos creados/modificados
- Costos estimados cuando aplica
- Links a dashboards y UIs relevantes

```