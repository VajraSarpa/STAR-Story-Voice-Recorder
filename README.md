# STAR-Story-Voice-Recorder

# 🎙️ STAR Story Voice Recorder

**A serverless AWS portfolio project demonstrating cloud engineering competency while solving real interview preparation needs.**

[![AWS](https://img.shields.io/badge/AWS-Serverless-FF9900?style=flat&logo=amazon-aws)](https://aws.amazon.com/)
[![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=flat&logo=python)](https://www.python.org/)
[![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=flat&logo=terraform)](https://www.terraform.io/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Deployment Guide](#deployment-guide)
- [Cost Analysis](#cost-analysis)
- [Well-Architected Framework](#well-architected-framework)
- [Security](#security)
- [Interview Talking Points](#interview-talking-points)
- [Future Enhancements](#future-enhancements)
- [Contributing](#contributing)
- [License](#license)

---

## 🎯 Overview

**STAR Story Voice Recorder** is a mobile-first Progressive Web Application (PWA) that helps AWS Cloud Institute students and Amazon employees practice Leadership Principle stories during their commute. This project serves dual purposes:

1. **Practical Tool**: Record, transcribe, and organize STAR stories for interview preparation
2. **Portfolio Project**: Demonstrate AWS serverless architecture, IaC, and Well-Architected Framework compliance

### Key Metrics

- **Monthly Cost**: <$3.00 (under $5 budget)
- **Development Time**: 3-4 weeks (2-3 hours/week)
- **Tech Stack**: 100% Serverless AWS
- **Deployment**: Fully automated with Terraform IaC

---

## 🏗️ Architecture

### High-Level Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        User Layer                            │
│  Mobile Browser (PWA) - Progressive Web Application          │
└─────────────────────┬───────────────────────────────────────┘
                      │ HTTPS
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                    API Layer                                 │
│  API Gateway (HTTP API) + Lambda Authorizer                 │
└───┬─────────────┬───────────────┬─────────────┬─────────────┘
    │             │               │             │
    ▼             ▼               ▼             ▼
┌─────────┐  ┌─────────┐    ┌──────────┐  ┌──────────┐
│ Lambda  │  │ Lambda  │    │  Lambda  │  │  Lambda  │
│  Upload │  │  List   │    │ Process  │  │ Playback │
│   URL   │  │Records  │    │Recording │  │   (TTS)  │
└────┬────┘  └────┬────┘    └────┬─────┘  └────┬─────┘
     │            │              │             │
     ▼            ▼              ▼             ▼
┌─────────────────────────────────────────────────────────────┐
│                    Storage Layer                             │
├──────────────────┬──────────────────┬────────────────────────┤
│   S3 Bucket      │   DynamoDB       │    Transcribe/Polly    │
│   (Audio Files)  │   (Metadata)     │    (AI Services)       │
└──────────────────┴──────────────────┴────────────────────────┘
```

### Component Breakdown

#### **1. Frontend (PWA)**
- **Technology**: Vanilla JavaScript, HTML5, CSS3
- **Features**: 
  - MediaRecorder API for audio capture
  - Responsive design (mobile-first)
  - Service Worker for offline capability
  - IndexedDB for local caching (future)
- **Hosting**: S3 + CloudFront (Phase 2)

#### **2. API Gateway (HTTP API)**
- **Endpoints**:
  - `POST /recordings/upload-url` - Generate presigned S3 URL
  - `GET /recordings` - List recordings with filters
  - `POST /recordings/{id}/playback` - Get audio/TTS playback
- **Features**: CORS enabled, CloudWatch logging

#### **3. Lambda Functions**
| Function | Runtime | Memory | Timeout | Trigger |
|----------|---------|---------|---------|---------|
| get_upload_url | Python 3.12 | 256MB | 30s | API Gateway |
| process_recording | Python 3.12 | 512MB | 300s | S3 Event |
| list_recordings | Python 3.12 | 256MB | 30s | API Gateway |
| get_playback | Python 3.12 | 512MB | 60s | API Gateway |

#### **4. Storage Services**
- **S3 Bucket**:
  - Versioning enabled
  - SSE-S3 encryption
  - Lifecycle policies (Glacier after 90 days, delete after 365 days)
  - Organized structure: `uploads/{userId}/{recordingId}.webm`
- **DynamoDB Table**:
  - PAY_PER_REQUEST billing
  - Global Secondary Indexes (GSI):
    - `LeadershipPrincipleIndex` - Filter by LP
    - `CreatedAtIndex` - Sort by date
  - TTL enabled for auto-expiration
  - Point-in-time recovery enabled

#### **5. AI Services**
- **AWS Transcribe**: Convert speech to text (~$0.024/minute)
- **Amazon Polly**: Text-to-speech playback (~$4/1M characters)

---

## 💻 Technology Stack

### **AWS Services**
- **Compute**: AWS Lambda (Python 3.12)
- **API**: API Gateway (HTTP API)
- **Storage**: S3, DynamoDB
- **AI/ML**: AWS Transcribe, Amazon Polly
- **Monitoring**: CloudWatch Logs, Metrics, Alarms
- **Security**: IAM, encryption at rest/transit

### **Infrastructure as Code**
- **Terraform**: v1.0+
- **Provider**: AWS Provider ~> 5.0

### **Development Tools**
- **Language**: Python 3.12
- **Frontend**: JavaScript (ES6+), HTML5, CSS3
- **Version Control**: Git, GitHub
- **Testing**: Manual (unit tests Phase 2)

---

## ✨ Features

### Phase 1 (MVP) ✅
- [x] Voice recording from mobile browser
- [x] Automatic speech-to-text transcription
- [x] Organized storage by Leadership Principle
- [x] Searchable recording library
- [x] Original audio + TTS playback
- [x] Cost-optimized serverless architecture
- [x] Infrastructure as Code deployment

### Phase 2 (Enhanced) 🚀
- [ ] AWS Bedrock integration for STAR structure analysis
- [ ] Web dashboard (React)
- [ ] Multi-user authentication (Cognito)
- [ ] Sharing capability with mentors
- [ ] CloudFront CDN for global access
- [ ] CI/CD pipeline (GitHub Actions)

---

## 📦 Prerequisites

### Required Software
```bash
# AWS CLI
aws --version  # AWS CLI 2.x

# Terraform
terraform --version  # v1.0+

# Python
python3 --version  # Python 3.12+

# Git
git --version
```

### AWS Account Setup
1. **AWS Account**: Free tier eligible account
2. **IAM User**: Admin permissions or PowerUserAccess
3. **AWS CLI**: Configured with credentials
   ```bash
   aws configure
   ```
4. **Region**: us-east-1 (or modify `terraform/variables.tf`)

### Cost Estimate
- **Free Tier**: Most services covered for first 12 months
- **Estimated Monthly Cost**: $2-3 after free tier
- **Cost Breakdown**: See [Cost Analysis](#cost-analysis)

---

## 🚀 Deployment Guide

### **Step 1: Clone Repository**
```bash
git clone https://github.com/YOUR_USERNAME/star-recorder.git
cd star-recorder
```

### **Step 2: Package Lambda Functions**
```bash
chmod +x scripts/package-lambdas.sh
./scripts/package-lambdas.sh
```

This creates ZIP files for all Lambda functions in `lambda/functions/`.

### **Step 3: Deploy Infrastructure with Terraform**

```bash
cd terraform

# Initialize Terraform
terraform init

# Review execution plan
terraform plan -var 'owner_email=YOUR_EMAIL@example.com'

# Deploy infrastructure
terraform apply -var 'owner_email=YOUR_EMAIL@example.com'
```

**Important**: Save the outputs!
```bash
# Example outputs:
# api_endpoint = "https://abc123xyz.execute-api.us-east-1.amazonaws.com/dev"
# recordings_bucket = "star-recorder-recordings-dev"
# dynamodb_table = "star-recorder-recordings-dev"
```

### **Step 4: Configure Frontend**

1. Edit `frontend/app.js`:
   ```javascript
   const API_BASE_URL = 'YOUR_API_GATEWAY_URL';  // From Terraform output
   ```

2. (Optional) Upload frontend to S3 for hosting:
   ```bash
   aws s3 mb s3://star-recorder-frontend-YOUR_NAME
   aws s3 sync frontend/ s3://star-recorder-frontend-YOUR_NAME --acl public-read
   aws s3 website s3://star-recorder-frontend-YOUR_NAME --index-document index.html
   ```

   Or host locally:
   ```bash
   cd frontend
   python3 -m http.server 8000
   # Visit http://localhost:8000
   ```

### **Step 5: Test the Application**

1. Open the frontend URL in a **mobile browser**
2. Click "Record" tab
3. Enter a title and select Leadership Principle
4. Click the microphone button to record
5. Speak for 30-60 seconds
6. Click stop, then "Save Recording"
7. Wait 1-2 minutes for transcription
8. Switch to "Library" tab to view your recording

---

## 💰 Cost Analysis

### Monthly Cost Breakdown (30 recordings)

| Service | Free Tier | Usage | Cost |
|---------|-----------|-------|------|
| **API Gateway** | 1M requests | 100 requests/month | $0.00 |
| **Lambda** | 1M requests, 400,000 GB-sec | ~50 invocations | $0.00 |
| **S3 Storage** | 5GB | 90MB audio + transcripts | $0.00 |
| **DynamoDB** | 25GB, 25 WCU/RCU | Minimal usage | $0.00 |
| **AWS Transcribe** | None | 90 minutes @ $0.024/min | **$2.16** |
| **Amazon Polly** | None | ~5,000 chars @ $4/1M | **$0.02** |
| **CloudWatch Logs** | 5GB | <100MB | $0.00 |
| **Data Transfer** | 100GB out | <1GB | $0.00 |
| **TOTAL** | | | **$2.18/month** |

### Cost Optimization Strategies

1. **Transcribe is the primary cost driver** (~97% of total)
   - Use it only when needed
   - Consider batching recordings
   - Alternative: AWS Batch for bulk processing

2. **S3 Lifecycle Policies**
   - Move to Glacier IR after 90 days: Saves 68%
   - Delete after 1 year: Avoid accumulation

3. **DynamoDB On-Demand**
   - Pay only for actual reads/writes
   - No idle costs for unused capacity

4. **Lambda Memory Optimization**
   - Right-sized: 256-512MB
   - Faster execution = lower cost

5. **Future Enhancements**
   - CloudFront caching: Reduce API calls
   - Cognito free tier: 50,000 MAUs

### Scaling to 100 Users

| Metric | MVP (1 user) | 100 Users | Cost Impact |
|--------|--------------|-----------|-------------|
| Recordings | 30/month | 3,000/month | $216/month (Transcribe) |
| API Calls | 100/month | 10,000/month | Still free tier |
| Storage | 90MB | 9GB | $0.21/month |
| **TOTAL** | $2.18 | **$216.21/month** |

**Note**: At scale, consider:
- Reserved capacity for DynamoDB (35% savings)
- S3 Intelligent-Tiering
- AWS Budgets alerts

---

## 🏛️ Well-Architected Framework

### **1. Operational Excellence**

**Design Principles Applied:**
- ✅ **Infrastructure as Code**: 100% Terraform
- ✅ **Observability**: CloudWatch Logs, Metrics, Alarms
- ✅ **Evolution**: Git version control, modular architecture
- ✅ **Failure Anticipation**: Lambda retries, S3 versioning

**Specific Implementations:**
- Terraform state management with remote backend (recommend S3 + DynamoDB)
- CloudWatch alarms for Lambda errors (threshold: 5 errors/5 min)
- API Gateway access logs for debugging
- Tagged resources for cost allocation

### **2. Security**

**Design Principles Applied:**
- ✅ **Least Privilege**: IAM roles per Lambda function
- ✅ **Encryption**: SSE-S3 (at rest), HTTPS (in transit)
- ✅ **Traceability**: X-Ray tracing enabled
- ✅ **Defense in Depth**: VPC endpoints (Phase 2)

**Specific Implementations:**
```hcl
# Example: Lambda execution role with minimal permissions
resource "aws_iam_role_policy" "lambda_policy" {
  policy = jsonencode({
    Statement = [
      {
        Effect = "Allow"
        Action = ["s3:GetObject", "s3:PutObject"]
        Resource = "${aws_s3_bucket.recordings.arn}/*"
      },
      # No S3:* or DynamoDB:* wildcards
    ]
  })
}
```

**Security Enhancements (Phase 2):**
- AWS Cognito for authentication
- API Gateway Lambda authorizer
- S3 bucket policies (block public access)
- AWS WAF for DDoS protection

### **3. Reliability**

**Design Principles Applied:**
- ✅ **Recover from Failure**: Lambda automatic retries
- ✅ **Scale Horizontally**: Serverless auto-scaling
- ✅ **Stop Guessing Capacity**: On-demand pricing
- ✅ **Manage Change**: IaC enables rollback

**Specific Implementations:**
- S3 versioning for recovery
- DynamoDB point-in-time recovery (PITR)
- CloudWatch alarms for proactive monitoring
- Multi-AZ deployment (inherent in DynamoDB, Lambda)

**Availability Calculation:**
- API Gateway: 99.95% SLA
- Lambda: 99.95% SLA
- S3: 99.99% durability, 99.9% availability
- **Combined**: ~99.75% monthly availability

### **4. Performance Efficiency**

**Design Principles Applied:**
- ✅ **Serverless Architectures**: No server management
- ✅ **Experiment Often**: Easy to test different Lambdas
- ✅ **Mechanical Sympathy**: Right service for the job

**Specific Implementations:**
| Component | Choice | Justification |
|-----------|--------|---------------|
| Compute | Lambda | Event-driven, auto-scaling |
| Database | DynamoDB | NoSQL, millisecond latency |
| Storage | S3 | Unlimited scalability, 99.99% durability |
| CDN | CloudFront (Phase 2) | Edge caching, low latency |

**Performance Optimization:**
- Lambda: Provisioned concurrency (if needed)
- DynamoDB: DAX caching layer (future)
- S3 Transfer Acceleration (global users)
- API Gateway caching (reduce Lambda calls)

### **5. Cost Optimization**

**Design Principles Applied:**
- ✅ **Adopt Consumption Model**: Pay only for usage
- ✅ **Measure Efficiency**: CloudWatch metrics
- ✅ **Stop Spending on Undifferentiated Heavy Lifting**: Managed services

**Specific Implementations:**
- DynamoDB on-demand (no idle costs)
- S3 lifecycle policies (Glacier, deletion)
- Lambda right-sized memory (256-512MB)
- API Gateway HTTP API (cheaper than REST)

**Cost Tracking:**
```bash
# AWS Cost Explorer tags
Project: STARRecorder
Environment: dev
ManagedBy: Terraform
```

### **6. Sustainability**

**Design Principles Applied:**
- ✅ **Minimize Idle Resources**: Serverless, auto-scaling
- ✅ **Maximize Utilization**: Right-sized resources
- ✅ **Use Managed Services**: AWS handles optimization

**Specific Implementations:**
- No idle EC2 instances
- S3 Intelligent-Tiering (future)
- Lambda Graviton2 processors (future, 20% lower energy)
- CloudWatch Logs retention (7 days, not indefinite)

---

## 🔒 Security

### **IAM Policies**

Each Lambda function has a dedicated IAM role with minimal permissions:

```python
# get_upload_url Lambda permissions:
- s3:PutObject (limited to uploads/ prefix)
- dynamodb:PutItem (recordings table only)

# process_recording Lambda permissions:
- s3:GetObject
- transcribe:StartTranscriptionJob
- dynamodb:UpdateItem

# list_recordings Lambda permissions:
- dynamodb:Query (read-only)

# get_playback Lambda permissions:
- dynamodb:GetItem
- s3:GetObject
- polly:SynthesizeSpeech
```

### **Encryption**

- **At Rest**:
  - S3: SSE-S3 (AES-256)
  - DynamoDB: Encryption enabled (AWS-managed keys)
- **In Transit**:
  - API Gateway: HTTPS only
  - Presigned URLs: HTTPS enforced

### **Data Privacy**

- **Single User MVP**: No authentication, single `userId: "default-user"`
- **Phase 2**: AWS Cognito for user isolation
- **Data Retention**: S3 lifecycle deletes after 365 days
- **GDPR Compliance**: User can delete recordings anytime

---

## 🎤 Interview Talking Points

Use these talking points when presenting this project:

### **1. Architecture Decision: Why Serverless?**
*"I chose serverless because the workload is unpredictable—I might record 5 times one week, zero the next. With Lambda and DynamoDB on-demand, I only pay for actual usage. This keeps my costs under $3/month while maintaining production-ready architecture."*

### **2. Trade-Off: API Gateway HTTP API vs. REST API**
*"I selected HTTP API over REST API because it's 70% cheaper and met all my requirements. I traded features like API keys and usage plans for cost efficiency—appropriate for an MVP with a single user."*

### **3. Cost Optimization: S3 Lifecycle Policies**
*"Transcribe was my primary cost driver at $2.16/month. I implemented S3 lifecycle policies to transition recordings to Glacier IR after 90 days, which would save 68% if I scaled to 100 users. This demonstrates proactive cost management."*

### **4. Security: Least Privilege IAM**
*"Each Lambda function has a dedicated IAM role with only the permissions it needs. For example, `get_upload_url` can write to S3 but can't read, and `list_recordings` can query DynamoDB but can't write. This follows AWS security best practices."*

### **5. Scalability: DynamoDB GSI Design**
*"I designed the DynamoDB schema with a composite key (userId, recordingId) and two GSIs for filtering by Leadership Principle and creation date. This allows efficient queries at scale without table scans."*

### **6. Reliability: Error Handling**
*"I implemented CloudWatch alarms to notify me if Lambda errors exceed 5 in a 5-minute window. The process_recording function also updates DynamoDB with error states, allowing users to see failed transcriptions instead of silently failing."*

### **7. Operational Excellence: Infrastructure as Code**
*"The entire infrastructure is defined in Terraform. This means I can recreate the environment in any AWS account with a single command, making it easy to set up dev/staging/prod environments or recover from disasters."*

### **8. Future Enhancements: Bedrock Integration**
*"For Phase 2, I'm planning to integrate AWS Bedrock to analyze STAR story structure—checking if the recording includes Situation, Task, Action, and Result components. This shows how traditional workloads can be augmented with generative AI."*

---

## 🚀 Future Enhancements

### **Phase 2: Production-Ready Features**
- [ ] **Authentication**: AWS Cognito for multi-user support
- [ ] **Frontend Hosting**: S3 + CloudFront CDN
- [ ] **CI/CD**: GitHub Actions for automated deployment
- [ ] **Testing**: Unit tests (pytest), integration tests
- [ ] **Monitoring**: CloudWatch dashboards, X-Ray tracing

### **Phase 3: Advanced Features**
- [ ] **AI Analysis**: Bedrock for STAR structure validation
- [ ] **Collaboration**: Share recordings with mentors (presigned URLs)
- [ ] **Quiz Mode**: Random story playback for practice
- [ ] **Web Dashboard**: React admin panel for desktop
- [ ] **Mobile App**: React Native for native mobile experience

### **Infrastructure Improvements**
- [ ] **Multi-Region**: DynamoDB global tables, S3 replication
- [ ] **Disaster Recovery**: Automated backups, cross-region failover
- [ ] **Performance**: Lambda provisioned concurrency, DAX caching
- [ ] **Cost**: Reserved capacity for high-volume users

---

## 📝 Project Structure

```
star-recorder/
├── terraform/                   # Infrastructure as Code
│   ├── main.tf                 # Main Terraform configuration
│   ├── variables.tf            # Input variables
│   ├── outputs.tf              # Output values (API endpoint, etc.)
│   └── terraform.tfvars.example
├── lambda/
│   └── functions/              # Lambda function code
│       ├── get_upload_url/
│       │   └── index.py
│       ├── process_recording/
│       │   └── index.py
│       ├── list_recordings/
│       │   └── index.py
│       └── get_playback/
│           └── index.py
├── frontend/                    # Progressive Web App
│   ├── index.html
│   ├── app.js
│   ├── manifest.json
│   └── sw.js                    # Service worker
├── scripts/
│   └── package-lambdas.sh       # Deployment helper
├── docs/
│   ├── ARCHITECTURE.md          # Detailed architecture diagrams
│   ├── COST_ANALYSIS.md         # Cost breakdown
│   └── SECURITY.md              # Security considerations
├── README.md                    # This file
└── LICENSE
```

---

## 🤝 Contributing

Contributions are welcome! This is a portfolio project, but improvements are always appreciated.

### How to Contribute
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **AWS Cloud Institute**: For the cloud engineering curriculum
- **Amazon Leadership Principles**: Inspiration for the project
- **AWS Well-Architected Framework**: Guiding architecture decisions

---

## 📞 Contact

**Your Name**  
AWS Cloud Institute Student | Amazon Operations Employee  
[GitHub](https://github.com/YOUR_USERNAME) | [LinkedIn](https://linkedin.com/in/YOUR_PROFILE) | [Email](mailto:YOUR_EMAIL)

---

**Built with ☁️ by an aspiring AWS Cloud Engineer**
