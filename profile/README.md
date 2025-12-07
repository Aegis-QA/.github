# Aegis QA

**Intelligent test case generation from requirements documents and UI screenshots using AI/LLM technology**

![Status](https://img.shields.io/badge/status-active-success)
![License](https://img.shields.io/badge/license-MIT-blue)

## Overview

AI-Based Test Case Generation is a comprehensive system that automatically generates detailed test cases from Software Requirements Specification (SRS) documents and UI screenshots. The system leverages cutting-edge LLM technology to understand requirements and create comprehensive, well-structured test cases.

### Key Features

- **AI-Powered Generation**: Uses OpenRouter LLM for intelligent test case creation
- **Multi-Format Support**: Process PDF, DOCX, TXT documents and PNG/JPG images
- **Real-Time Processing**: Kafka-based background worker with live status updates
- **Modern Architecture**: Microservices-based design with separate frontend, backend, and infrastructure
- **Production-Ready**: Docker containerized with monitoring and observability built-in
- **Scalable**: Horizontal scaling support for high-volume processing

## Architecture

```
┌─────────────┐      ┌──────────────┐      ┌─────────────┐
│   Next.js   │─────▶│   FastAPI    │─────▶│  PostgreSQL │
│  Frontend   │      │   Backend    │      │  Database   │
│ (Port 3001) │      │ (Port 8000)  │      │ (Port 5432) │
└─────────────┘      └──────────────┘      └─────────────┘
                            │
                            ▼
                     ┌──────────────┐
                     │    Kafka     │
                     │   Message    │
                     │    Queue     │
                     │ (Port 19093) │
                     └──────────────┘
                            │
                            ▼
                     ┌──────────────┐      ┌──────────────┐
                     │  Background  │─────▶│    MinIO     │
                     │   Worker     │      │   Storage    │
                     │  (Docker)    │      │ (Port 9000)  │
                     └──────────────┘      └──────────────┘
```

## Repositories

This project is organized into three main repositories:

### 1. [Backend](https://github.com/AI-based-Test-Case-Generation/backend)
FastAPI-based REST API and background worker for test case generation.

**Tech Stack:** Python, FastAPI, SQLAlchemy, Kafka, OpenRouter

**Key Components:**
- REST API for file uploads and job management
- Background worker for async processing
- LLM integration for test case generation
- Database models and migrations

### 2. [Frontend](https://github.com/AI-based-Test-Case-Generation/frontend)
Modern Next.js web application with intuitive UI for managing test case generation.

**Tech Stack:** Next.js, React, TypeScript, Tailwind CSS

**Key Features:**
- Drag-and-drop file upload
- Real-time job status tracking
- Interactive test case display
- Export functionality

### 3. [Infrastructure](https://github.com/AI-based-Test-Case-Generation/infrastructure)
Docker Compose configuration and monitoring stack for all services.

**Includes:**
- PostgreSQL database
- Kafka + Zookeeper
- MinIO object storage
- Prometheus + Grafana monitoring
- Kafka UI tools

## Quick Start

### Prerequisites

- Docker (20.10+) and Docker Compose (2.0+)
- Python 3.10+ (for backend development)
- Node.js 18+ and npm (for frontend development)
- OpenRouter API key ([get one here](https://openrouter.ai/))

### Full System Setup

#### 1. Clone All Repositories

```bash
# Create a workspace directory
mkdir testcase-generator
cd testcase-generator

# Clone all repositories
git clone https://github.com/AI-based-Test-Case-Generation/infrastructure.git
git clone https://github.com/AI-based-Test-Case-Generation/backend.git
git clone https://github.com/AI-based-Test-Case-Generation/frontend.git
```

#### 2. Start Infrastructure Services

```bash
cd infrastructure
docker compose up -d
```

Wait for all services to be healthy (~30-60 seconds):
```bash
docker compose ps
```

#### 3. Setup Backend

```bash
cd ../backend

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp .env.example .env
# Edit .env and add your OPENROUTER_API_KEY

# Initialize database
python migrate_db.py

# Start API server
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

#### 4. Setup Frontend

```bash
cd ../frontend

# Install dependencies
npm install

# Configure environment
cp .env.example .env.local
# Edit .env.local if needed

# Start development server
npm run dev
```

#### 5. Access the Application

- **Frontend:** http://localhost:3001
- **Backend API:** http://localhost:8000/docs
- **MinIO Console:** http://localhost:9001
- **Kafka UI:** http://localhost:8080
- **Grafana:** http://localhost:3000

## Documentation

Each repository contains detailed documentation:

- **[Backend README](https://github.com/AI-based-Test-Case-Generation/backend/blob/main/README.md)** - API endpoints, worker setup, database schema
- **[Frontend README](https://github.com/AI-based-Test-Case-Generation/frontend/blob/main/README.md)** - Component docs, styling guide, configuration
- **[Infrastructure README](https://github.com/AI-based-Test-Case-Generation/infrastructure/blob/main/README.md)** - Service management, monitoring, troubleshooting

## Usage

### Via Web Interface

1. Navigate to http://localhost:3001
2. Upload an SRS document (PDF, DOCX, or TXT)
3. Optionally add UI screenshots
4. Click "Generate Test Cases"
5. Monitor job status in real-time
6. View and export generated test cases

### Via API

```bash
# Upload document
curl -X POST \
  -F "file=@requirements.pdf" \
  -F "images=@screenshot.png" \
  http://localhost:8000/api/v1/upload

# Check job status
curl http://localhost:8000/api/v1/jobs/1

# Get test cases
curl http://localhost:8000/api/v1/jobs/1/testcases
```

## Configuration

### Backend Environment Variables

```bash
DATABASE_URL=postgresql://user:password@localhost:5432/testcase_db
KAFKA_BOOTSTRAP_SERVERS=localhost:19093
MINIO_URL=http://localhost:9000
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin
OPENROUTER_API_KEY=sk-or-v1-your-key-here
USE_LLM=true
```

### Frontend Environment Variables

```bash
NEXT_PUBLIC_API_URL=http://localhost:8000
```

## Monitoring & Observability

The system includes comprehensive monitoring:

- **Prometheus** (http://localhost:19090) - Metrics collection
- **Grafana** (http://localhost:3000) - Visualization dashboards
- **Kafka UI** (http://localhost:8080) - Message queue monitoring
- **MinIO Console** (http://localhost:9001) - Storage management

## Testing

### Testing the Complete Flow

1. Create a test requirements file:
```bash
echo "User should be able to login with valid username and password" > test.txt
```

2. Upload via API:
```bash
curl -X POST \
  -F "file=@test.txt" \
  http://localhost:8000/api/v1/upload
```

3. Monitor worker logs:
```bash
docker logs -f worker
```

4. Check generated test cases:
```bash
curl http://localhost:8000/api/v1/jobs/1/testcases | jq
```

## Troubleshooting

### Services Not Starting

```bash
# Check Docker status
docker compose ps

# View logs
docker compose logs -f

# Restart services
docker compose restart
```

### Backend Connection Issues

```bash
# Verify backend is running
curl http://localhost:8000/health

# Check backend logs
cd backend
tail -f logs/app.log
```

### Worker Not Processing Jobs

```bash
# Check worker logs
docker logs -f worker

# Verify Kafka connection
docker exec kafka kafka-topics --list --bootstrap-server localhost:9092

# Restart worker
docker compose restart worker
```

## Production Deployment

For production deployment:

1. **Use managed services** for PostgreSQL, Kafka, and object storage
2. **Enable SSL/TLS** for all connections
3. **Change default credentials** for all services
4. **Set up proper logging** and monitoring
5. **Configure autoscaling** for workers
6. **Implement rate limiting** on API endpoints
7. **Use environment-specific** configuration

See individual repository documentation for detailed production deployment guides.

## Security

> **WARNING**: Default configuration is for DEVELOPMENT ONLY!

For production:
- Change all default credentials
- Enable authentication on all services
- Use HTTPS/SSL for all connections
- Implement proper secrets management
- Set up network security and firewalls
- Enable audit logging

## Contributing

We welcome contributions! Please:

1. Fork the relevant repository
2. Create a feature branch
3. Make your changes with tests
4. Submit a pull request

See individual repository CONTRIBUTING.md files for specific guidelines.

## License

MIT License - See LICENSE file in each repository for details.

## Team

Built by the AI-Based Test Case Generation team.

## Links

- **Organization:** https://github.com/AI-based-Test-Case-Generation
- **Backend:** https://github.com/AI-based-Test-Case-Generation/backend
- **Frontend:** https://github.com/AI-based-Test-Case-Generation/frontend
- **Infrastructure:** https://github.com/AI-based-Test-Case-Generation/infrastructure
- **OpenRouter:** https://openrouter.ai/

---

**Version:** 1.0.0  
**Last Updated:** December 2025

For issues and support, please open an issue in the relevant repository.
