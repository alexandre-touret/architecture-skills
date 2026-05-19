# Preferred Target Solutions

When designing architectures using the `methodological-architecture-designer` skill, prioritize the following solutions unless the CCTP explicitly mandates an alternative:

## Architecture & Development
- **Language/Ecosystem**: Java First.
- **Compute Strategy**: Kubernetes/Docker First.
- **Service Model**: Managed solutions first, to reduce operational overhead.

## Cloud Infrastructure
- **Primary Provider**: Google Cloud Platform (GCP).
- **Compute**: GKE (Google Kubernetes Engine) for complex orchestration (preferred for Kubernetes first approach), Cloud Run for stateless containerized workloads.
- **Storage/Databases**: 
  - **Relational**: PostgreSQL First (e.g., Cloud SQL for PostgreSQL).
  - **NoSQL**: If you propose MongoDB, you MUST explicitly state why it is chosen over PostgreSQL or other alternatives like Firestore/Datastore. Cloud Storage for object storage.
- **Messaging/Events**: Google Cloud Pub/Sub.

## Security and Identity
- **IAM / Authentication**: Keycloak.
- **Protocols**: OIDC (OpenID Connect) for authentication, OAuth 2.0 for authorization.
- **Secrets Management**: Google Secret Manager.

## Integration & APIs
- **API Management**: Apigee or native GCP API Gateway.
- **External Interfaces**: Standardize on REST/JSON or gRPC for internal microservices communications.

## Justification
These solutions (Java, Kubernetes/Docker, Managed Services, PostgreSQL, GCP, Keycloak) are the preferred standard to maximize security, scalability, and maintainability while minimizing operational toil. They form the baseline architecture unless specific constraints in the CCTP (e.g., regulatory reasons or existing enterprise agreements) require deviation.