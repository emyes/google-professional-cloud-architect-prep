# Google Cloud AI & ML Ecosystem: PCA Exam Cheatsheet

## 1. Service Tiers & Selection
- **Pre-Trained APIs**: Vision, Natural Language, Speech-to-Text, Text-to-Speech, Translation, Document AI, Chirp, AML AI, Cloud Healthcare API, Imagen.
  - Use for rapid integration, common AI tasks, no ML expertise needed.
- **Vertex AI AutoML**: Custom models with minimal code, for unique datasets.
- **Vertex AI Custom Training**: Full control, any ML framework, for advanced teams.
- **BigQuery ML**: ML in SQL, integrated with data warehouse.
- **Ray on Vertex AI**: Distributed Python/ML workloads at scale.

## 2. Generative AI & Agents
- **Gemini Models**: Multimodal, foundation for generative/agentic services.
- **Gemini Enterprise**: Business workflow automation.
- **Vertex AI Agent Builder**: Custom enterprise conversational agents.
- **NotebookLM Enterprise**: Knowledge base Q&A from curated docs.
- **Generative AI Evaluation Service**: Model quality, human/automated eval, integrates with Vertex AI.

## 3. Data & Feature Management
- **Vertex AI Managed Datasets**: Centralized data for training.
- **Vertex AI Feature Store**: Feature reuse, offline-online consistency, real-time serving, latency monitoring.

## 4. Development & Experimentation
- **Colab Enterprise**: Fully managed, collaborative, integrated with Vertex AI.
- **User-Managed Notebooks**: Full control, custom environments.
- **Vertex AI Experiments**: Track, compare, manage training runs.

## 5. Model Training & Evaluation
- **Data Splits**: Training, validation, test sets.
- **Metrics**:
  - Classification: Precision, Recall, Average Precision, TP/FP/FN/TN.
  - Regression: Mean Absolute Error (MAE).

## 6. Deployment & Prediction
- **Online Prediction**: Real-time, low-latency, Vertex AI Endpoints.
- **Batch Prediction**: Offline, large data volumes.
- **Model Registry**: Version, track, manage models.
- **Custom Prediction Routines**: Deploy custom logic from Model Garden.

## 7. MLOps & Governance
- **Vertex AI Pipelines**: Orchestrate, automate, govern ML workflows.
- **Model Monitoring**: Detect drift/skew, configure sampling, alerting, monitor multiple models.
- **Explainable AI**: Feature attributions (Shapley, Integrated Gradients), example-based explanations.
- **Responsible AI**: Fairness, explainability, privacy, accountability.

## 8. Infrastructure
- **TPUs**: Custom ASICs for deep learning, high performance/efficiency.
- **GPUs**: Nvidia H100, B200, A100, T4 for ML/AI workloads.
- **AI Hypercomputer**: Jupiter OCS network, dynamic topology for distributed training.
- **Artifact Registry**: Secure package/image storage for ML pipelines.

## 9. Security & Compliance
- **VPC Service Controls**: Service perimeter, data exfiltration prevention.
- **Private Service Connect**: Private network to Google APIs.
- **Cloud NAT**: Secure outbound internet for private resources.
- **IAM**: Granular access control for AI resources.
- **Cloud DLP**: PII detection/redaction in data.
- **CMEK**: Customer-managed encryption keys.
- **Model Armor**: Prompt/response screening for generative AI.
- **Confidential Computing**: Memory encryption for sensitive workloads.

---

**Tip:** For PCA exam, focus on when to use each service, integration points, security controls, and how to architect for scale, governance, and compliance.