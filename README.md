# OCI Observability Lab - DevOps Use Case

Este repositório contém um **laboratório prático** baseado na introdução dos serviços de Observability and Management da Oracle Cloud Infrastructure (OCI). Ele simula um cenário DevOps integrando diversos serviços de observabilidade.

> **Baseado na transcrição do curso Oracle Cloud Infrastructure Observability Professional.**

---

## Objetivo

- Explorar:
  - **Monitoring**
  - **Logging**
  - **Events Service**
  - **Logging Analytics**
  - **Application Performance Monitoring (APM)**
  - **Database Management**
  - **Operations Insights** (opcional)

- Simular fluxo DevOps:
  - Upload de build artifact
  - Detectar eventos
  - Disparar ações automáticas
  - Criar alarmes
  - Visualizar logs
  - Analisar logs e métricas

---

## Pré-requisitos

- Tenancy Oracle Cloud (pode ser Free Tier)
- Permissões adequadas (ou tenancy admin)
- Compartments organizados (ex.: `ObservabilityLab`)
- (Opcional) OCI CLI instalado

---

## Passo a Passo

### 1. Criar Compartment

Organize tudo dentro de um Compartment dedicado:

- Acesse o Console → **Identity & Security → Compartments → Create Compartment**
  - Name: `ObservabilityLab`

---

### 2. Criar Bucket no Object Storage

Simule o build pipeline criando um bucket para armazenar “artifacts” do build.

- Console → **Object Storage → Create Bucket**
  - Name: `devops-build-artifacts`
  - Storage Tier: Standard
  - Compartment: `ObservabilityLab`

**Teste Upload:**  
Envie qualquer arquivo `.txt` ou `.zip` para simular um build artifact.

---

### 3. Configurar Event Service

Objetivo: **detectar automaticamente uploads no bucket.**

- Console → **Application Integration → Events Service → Create Rule**
  - Name: `ArtifactUploadEvent`
  - Compartment: `ObservabilityLab`
  - Condition:
    - Event Type: `ObjectCreated`
    - Service Name: `Object Storage`
    - Bucket Name: `devops-build-artifacts`

→ Action:
- Service: **Notifications**
- Topic: Crie um tópico (ex.: `ArtifactUploads`)
- Destination: Email (teu e-mail para testes)

**Teste:**  
- Suba outro arquivo → verifique se recebe email de notificação.

---

### 4. Configurar Logging

#### a) Service Logs

Ative logs do Object Storage:

- Console → **Logging → Log Groups → Create Log Group**
  - Name: `DevOpsLogGroup`
  - Compartment: `ObservabilityLab`

→ Crie Log:
- Name: `ObjectStorageLogs`
- Log Source: Object Storage
- Log Category: Object Read/Write

Teste:
- Faça novo upload → confira logs no console.

---

#### b) Custom Logs

Simule logs customizados (p. ex., logs do build pipeline):

- Logging → Create Log
  - Name: `BuildPipelineCustomLog`
  - Log Type: Custom
  - Log Group: `DevOpsLogGroup`

→ Para gravar logs via CLI:

```bash
oci logging log-entry put \
    --log-id <your_log_OCID> \
    --log-content '{"message":"Build Successful","severity":"INFO"}'
