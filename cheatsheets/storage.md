# Google Cloud Storage & Database Concepts Cheatsheet (Week 4)

## Cloud Storage
- **Cloud Storage - Intro**: Object storage for unstructured data (files, images, backups).
- **Bucket Setup**: Create globally unique buckets, choose location (region/multi-region), set storage class (Standard, Nearline, Coldline, Archive).
- **Data Imports**: gsutil, Storage Transfer Service, drag-and-drop in Console.
- **Data Management Features**: Object versioning, lifecycle management, retention policies.
- **Preventing Hotspotting**: Use random prefixes in object names to avoid write bottlenecks.
- **Integration with Cloud CDN**: Serve static content globally with low latency.
- **Signed URLs**: Grant temporary access to objects without making them public.
- **CORS**: Allow cross-origin requests for web apps.
- **Hosting Static Websites**: Serve HTML/CSS/JS directly from a bucket.
- **Encryption**: Data encrypted at rest by default; can use customer-managed keys.
- **Predefined Roles**: Storage Admin, Object Admin, Object Viewer, etc.

## Cloud SQL
- **Managed MySQL, PostgreSQL, SQL Server**
- **Use Cases**: Web apps, e-commerce, CRM, ERP.
- **Features**: Automated backups, high availability, read replicas, IAM integration.
- **Storage**: SSD/HDD, auto-resize, encryption.
- **Tiers**: Standard, High Memory, High CPU.
- **Backup Strategies**: Automated, on-demand, point-in-time recovery.
- **Availability**: Regional, failover support.
- **Replication Lag**: Monitor for async replica delays.
- **Auth Proxy**: Secure connections without public IP.

## Cloud Spanner
- **Relational at global scale**
- **Use Cases**: Global financial ledgers, gaming, inventory.
- **Features**: Horizontal scaling, strong consistency, 99.999% availability.
- **Schema**: Tables, interleaved tables, secondary indexes.
- **Backups**: Automated, on-demand, export/import.

## Cloud Bigtable
- **NoSQL wide-column store**
- **Use Cases**: IoT, time-series, analytics, real-time data.
- **Schema**: Rows, column families, cells.
- **Hotspotting**: Avoid sequential row keys; use hash/prefix.
- **Replication**: Multi-cluster for HA.

## BigQuery
- **Serverless data warehouse**
- **Use Cases**: Analytics, reporting, ad hoc queries.
- **Features**: Standard SQL, partitions, clustering, views, federated queries.
- **Schema**: Flexible, supports nested/repeated fields.
- **Security**: IAM, column/row-level security, encryption.
- **Cost**: Storage and query costs billed separately.

## Firestore & Firebase
- **Firestore**: NoSQL document DB for web/mobile apps.
- **Features**: Real-time sync, offline support, strong consistency.
- **Indexes**: Automatic and custom.
- **Deterministic Encryption**: For sensitive data.
- **Efficient Data Retrieval**: Structured queries, pagination.
- **Predefined Roles**: Owner, Editor, Viewer, Firestore roles.

## Memorystore
- **Managed Redis/Memcached**
- **Use Cases**: Caching, session storage, real-time analytics.

## AlloyDB
- **PostgreSQL-compatible, enterprise features**
- **Use Cases**: Modernize legacy databases, high performance OLTP.

---

## Decision Table: Google Cloud Storage & Database Services

| Service         | Data Model         | Best For... (Primary Use Case)                | Analogy                                      |
|-----------------|--------------------|-----------------------------------------------|----------------------------------------------|
| Cloud Storage   | Object (Blobs)     | Files, backups, static websites               | A global, secure file cabinet                |
| Cloud SQL       | Relational (Tables)| Web apps, e-commerce, CRM                     | A managed digital spreadsheet                |
| Cloud Spanner   | Relational (Global)| Global financial systems, inventory           | A globally-synced spreadsheet that never fails|
| Cloud Bigtable  | NoSQL (Wide-column)| IoT, analytics, time-series                   | An infinitely large, lightning-fast ledger   |
| BigQuery        | Analytical DW      | Analytics, reporting, business insights       | A powerful calculator for huge datasets      |
| Firestore       | NoSQL (Document)   | Mobile/web apps, real-time sync               | A real-time, cloud-based document store      |
| Memorystore     | Key-Value          | Caching, session storage                      | A super-fast, managed memory cache           |
| AlloyDB         | Relational (Postgres)| Modernize legacy DBs, high performance OLTP  | PostgreSQL with superpowers                  |

---

## Quick Rules of Thumb
- Use **Cloud SQL** for standard web/e-commerce apps.
- Use **Cloud Spanner** for global, high-availability needs.
- Use **Cloud Bigtable** for massive, fast, simple data (IoT, analytics).
- Use **BigQuery** for analytics and reporting on huge datasets.
- Use **Firestore** for real-time, mobile/web app data.
- Use **Memorystore** for caching and low-latency data.
- Use **AlloyDB** for PostgreSQL workloads needing enterprise features.
