# Additional Components - LoongSuite Python Agent

## Additional GenAI Instrumentations

### VertexAI Instrumentation

#### Class: `opentelemetry.instrumentation.vertexai.VertexAIInstrumentor`

**Description**: Provides OpenTelemetry instrumentation for Google Cloud VertexAI services, enabling automatic tracing of AI model interactions.

**Installation**:
```bash
pip install ./instrumentation-genai/opentelemetry-instrumentation-vertexai
```

**Usage**:
```python
from opentelemetry.instrumentation.vertexai import VertexAIInstrumentor
from google.cloud import aiplatform

VertexAIInstrumentor().instrument()

# Your VertexAI code will be automatically instrumented
client = aiplatform.gapic.PredictionServiceClient()
response = client.generate_content(request=request)
```

**Instrumented Components**:
- `google.cloud.aiplatform_v1beta1.services.prediction_service.client.PredictionServiceClient.generate_content`
- `google.cloud.aiplatform_v1.services.prediction_service.client.PredictionServiceClient.generate_content`

### AGNO Instrumentation

#### Class: `opentelemetry.instrumentation.agno.AgnoInstrumentor`

**Description**: Provides instrumentation for AGNO framework applications.

**Location**: `instrumentation-genai/opentelemetry-instrumentation-agno/`

## Core Instrumentations

### HTTP Instrumentations

#### URLLib Instrumentation
```python
from opentelemetry.instrumentation.urllib import URLLibInstrumentor

URLLibInstrumentor().instrument()
```

#### URLLib3 Instrumentation
```python
from opentelemetry.instrumentation.urllib3 import URLLib3Instrumentor

URLLib3Instrumentor().instrument()
```

#### Requests Instrumentation
```python
from opentelemetry.instrumentation.requests import RequestsInstrumentor

RequestsInstrumentor().instrument()
```

#### HTTPX Instrumentation
```python
from opentelemetry.instrumentation.httpx import HTTPXClientInstrumentor

HTTPXClientInstrumentor().instrument()
```

### Web Framework Instrumentations

#### Flask Instrumentation
```python
from opentelemetry.instrumentation.flask import FlaskInstrumentor

FlaskInstrumentor().instrument_app(app)
```

#### FastAPI Instrumentation
```python
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor

FastAPIInstrumentor.instrument_app(app)
```

#### Django Instrumentation
```python
from opentelemetry.instrumentation.django import DjangoInstrumentor

DjangoInstrumentor().instrument()
```

#### Starlette Instrumentation
```python
from opentelemetry.instrumentation.starlette import StarletteInstrumentor

StarletteInstrumentor.instrument_app(app)
```

#### Tornado Instrumentation
```python
from opentelemetry.instrumentation.tornado import TornadoInstrumentor

TornadoInstrumentor().instrument()
```

#### AioHTTP Server Instrumentation
```python
from opentelemetry.instrumentation.aiohttp_server import AioHttpServerInstrumentor

AioHttpServerInstrumentor().instrument()
```

#### AioHTTP Client Instrumentation
```python
from opentelemetry.instrumentation.aiohttp_client import AioHttpClientInstrumentor

AioHttpClientInstrumentor().instrument()
```

### Database Instrumentations

#### SQLAlchemy Instrumentation
```python
from opentelemetry.instrumentation.sqlalchemy import SQLAlchemyInstrumentor

SQLAlchemyInstrumentor().instrument()
```

#### PostgreSQL Instrumentations
```python
# Psycopg2
from opentelemetry.instrumentation.psycopg2 import Psycopg2Instrumentor
Psycopg2Instrumentor().instrument()

# Psycopg (v3)
from opentelemetry.instrumentation.psycopg import PsycopgInstrumentor
PsycopgInstrumentor().instrument()

# AsyncPG
from opentelemetry.instrumentation.asyncpg import AsyncPGInstrumentor
AsyncPGInstrumentor().instrument()

# AioPG
from opentelemetry.instrumentation.aiopg import AiopgInstrumentor
AiopgInstrumentor().instrument()
```

#### MySQL Instrumentations
```python
# MySQL
from opentelemetry.instrumentation.mysql import MySQLInstrumentor
MySQLInstrumentor().instrument()

# PyMySQL
from opentelemetry.instrumentation.pymysql import PyMySQLInstrumentor
PyMySQLInstrumentor().instrument()

# MySQLClient
from opentelemetry.instrumentation.mysqlclient import MySQLClientInstrumentor
MySQLClientInstrumentor().instrument()

# PyMSSQL
from opentelemetry.instrumentation.pymssql import PyMSSQLInstrumentor
PyMSSQLInstrumentor().instrument()
```

#### NoSQL Database Instrumentations
```python
# MongoDB
from opentelemetry.instrumentation.pymongo import PymongoInstrumentor
PymongoInstrumentor().instrument()

# Redis
from opentelemetry.instrumentation.redis import RedisInstrumentor
RedisInstrumentor().instrument()

# Cassandra
from opentelemetry.instrumentation.cassandra import CassandraInstrumentor
CassandraInstrumentor().instrument()

# Elasticsearch
from opentelemetry.instrumentation.elasticsearch import ElasticsearchInstrumentor
ElasticsearchInstrumentor().instrument()
```

#### SQLite Instrumentation
```python
from opentelemetry.instrumentation.sqlite3 import SQLite3Instrumentor

SQLite3Instrumentor().instrument()
```

### Message Queue Instrumentations

#### Kafka Instrumentations
```python
# Kafka Python
from opentelemetry.instrumentation.kafka import KafkaInstrumentor
KafkaInstrumentor().instrument()

# Confluent Kafka
from opentelemetry.instrumentation.confluent_kafka import ConfluentKafkaInstrumentor
ConfluentKafkaInstrumentor().instrument()

# AIOKafka
from opentelemetry.instrumentation.aiokafka import AIOKafkaInstrumentor
AIOKafkaInstrumentor().instrument()
```

#### RabbitMQ Instrumentations
```python
# Pika
from opentelemetry.instrumentation.pika import PikaInstrumentor
PikaInstrumentor().instrument()

# AIO-Pika
from opentelemetry.instrumentation.aio_pika import AioPikaInstrumentor
AioPikaInstrumentor().instrument()
```

### AWS Service Instrumentations

#### AWS Lambda Instrumentation
```python
from opentelemetry.instrumentation.aws_lambda import AwsLambdaInstrumentor

AwsLambdaInstrumentor().instrument()
```

#### Boto/Botocore Instrumentations
```python
# Boto
from opentelemetry.instrumentation.boto import BotoInstrumentor
BotoInstrumentor().instrument()

# Botocore
from opentelemetry.instrumentation.botocore import BotocoreInstrumentor
BotocoreInstrumentor().instrument()

# Boto3 SQS
from opentelemetry.instrumentation.boto3sqs import Boto3SQSInstrumentor
Boto3SQSInstrumentor().instrument()
```

### gRPC Instrumentation

```python
from opentelemetry.instrumentation.grpc import (
    GrpcInstrumentorClient,
    GrpcInstrumentorServer,
    GrpcAioInstrumentorClient,
    GrpcAioInstrumentorServer,
)

# Server instrumentation
GrpcInstrumentorServer().instrument()

# Client instrumentation
GrpcInstrumentorClient().instrument()

# Async server
GrpcAioInstrumentorServer().instrument()

# Async client
GrpcAioInstrumentorClient().instrument()
```

### Task Queue Instrumentations

#### Celery Instrumentation
```python
from opentelemetry.instrumentation.celery import CeleryInstrumentor

CeleryInstrumentor().instrument()
```

#### Remoulade Instrumentation
```python
from opentelemetry.instrumentation.remoulade import RemouladeInstrumentor

RemouladeInstrumentor().instrument()
```

### System and Runtime Instrumentations

#### System Metrics Instrumentation
```python
from opentelemetry.instrumentation.system_metrics import SystemMetricsInstrumentor

SystemMetricsInstrumentor().instrument()
```

#### Threading Instrumentation
```python
from opentelemetry.instrumentation.threading import ThreadingInstrumentor

ThreadingInstrumentor().instrument()
```

#### Asyncio Instrumentation
```python
from opentelemetry.instrumentation.asyncio import AsyncioInstrumentor

AsyncioInstrumentor().instrument()
```

#### Logging Instrumentation
```python
from opentelemetry.instrumentation.logging import LoggingInstrumentor

LoggingInstrumentor().instrument()
```

### Template Engine Instrumentations

#### Jinja2 Instrumentation
```python
from opentelemetry.instrumentation.jinja2 import Jinja2Instrumentor

Jinja2Instrumentor().instrument()
```

### Caching Instrumentations

#### Pymemcache Instrumentation
```python
from opentelemetry.instrumentation.pymemcache import PymemcacheInstrumentor

PymemcacheInstrumentor().instrument()
```

### CLI Framework Instrumentations

#### Click Instrumentation
```python
from opentelemetry.instrumentation.click import ClickInstrumentor

ClickInstrumentor().instrument()
```

### ORM Instrumentations

#### TortoiseORM Instrumentation
```python
from opentelemetry.instrumentation.tortoiseorm import TortoiseORMInstrumentor

TortoiseORMInstrumentor().instrument()
```

#### Pyramid Instrumentation
```python
from opentelemetry.instrumentation.pyramid import PyramidInstrumentor

PyramidInstrumentor().instrument()
```

### Falcon Instrumentation
```python
from opentelemetry.instrumentation.falcon import FalconInstrumentor

FalconInstrumentor().instrument()
```

## Custom Exporters

### Rich Console Exporter

**Description**: Provides rich, formatted console output for traces using the Rich library.

**Location**: `exporter/opentelemetry-exporter-richconsole/`

**Usage**:
```python
from opentelemetry.exporter.richconsole import RichConsoleSpanExporter
from opentelemetry.sdk.trace.export import BatchSpanProcessor

exporter = RichConsoleSpanExporter()
processor = BatchSpanProcessor(exporter)
```

### Prometheus Remote Write Exporter

**Description**: Exports metrics to Prometheus remote write endpoints.

**Location**: `exporter/opentelemetry-exporter-prometheus-remote-write/`

## Utility Components

### HTTP Client Instrumentor (Utility)

**Location**: `util/opentelemetry-util-http/`

**Description**: Provides utility classes for HTTP client instrumentation.

```python
from opentelemetry.util.http.httplib import HttpClientInstrumentor

HttpClientInstrumentor().instrument()
```

## Environment Variables for Specific Instrumentations

### Database Instrumentations
```bash
# SQLAlchemy
export OTEL_PYTHON_SQLALCHEMY_CAPTURE_PARAMETERS=true

# Redis
export OTEL_PYTHON_REDIS_CAPTURE_STATEMENT=true
```

### HTTP Instrumentations
```bash
# HTTP Client instrumentations
export OTEL_PYTHON_REQUESTS_CAPTURE_HEADERS=true
export OTEL_PYTHON_URLLIB_CAPTURE_HEADERS=true
export OTEL_PYTHON_HTTPX_CAPTURE_HEADERS=true
```

### Web Framework Instrumentations
```bash
# Flask
export OTEL_PYTHON_FLASK_CAPTURE_HEADERS=true

# Django
export OTEL_PYTHON_DJANGO_CAPTURE_HEADERS=true

# FastAPI
export OTEL_PYTHON_FASTAPI_CAPTURE_HEADERS=true
```

## Programmatic Configuration Examples

### Multiple Database Instrumentations
```python
from opentelemetry.instrumentation.sqlalchemy import SQLAlchemyInstrumentor
from opentelemetry.instrumentation.redis import RedisInstrumentor
from opentelemetry.instrumentation.pymongo import PymongoInstrumentor

# Instrument multiple database libraries
SQLAlchemyInstrumentor().instrument()
RedisInstrumentor().instrument()
PymongoInstrumentor().instrument()
```

### Web Stack Instrumentation
```python
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.instrumentation.sqlalchemy import SQLAlchemyInstrumentor
from opentelemetry.instrumentation.redis import RedisInstrumentor

# Complete web application stack
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
SQLAlchemyInstrumentor().instrument()
RedisInstrumentor().instrument()
```

### Microservices Communication Stack
```python
from opentelemetry.instrumentation.grpc import GrpcInstrumentorClient, GrpcInstrumentorServer
from opentelemetry.instrumentation.kafka import KafkaInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor

# Microservices communication
GrpcInstrumentorServer().instrument()
GrpcInstrumentorClient().instrument()
KafkaInstrumentor().instrument()
RequestsInstrumentor().instrument()
```

### AWS Stack Instrumentation
```python
from opentelemetry.instrumentation.aws_lambda import AwsLambdaInstrumentor
from opentelemetry.instrumentation.botocore import BotocoreInstrumentor
from opentelemetry.instrumentation.boto3sqs import Boto3SQSInstrumentor

# AWS services
AwsLambdaInstrumentor().instrument()
BotocoreInstrumentor().instrument()
Boto3SQSInstrumentor().instrument()
```

## Best Practices for Multi-Instrumentation

### 1. Selective Instrumentation
Use environment variables to disable unnecessary instrumentations:
```bash
export OTEL_PYTHON_DISABLED_INSTRUMENTATIONS="urllib3,requests"
```

### 2. Custom Span Processors
Configure different span processors for different types of spans:
```python
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor, SimpleSpanProcessor

# Fast processor for high-volume spans
fast_processor = BatchSpanProcessor(
    exporter,
    max_queue_size=2048,
    max_export_batch_size=512,
    export_timeout_millis=5000,
)

# Detailed processor for important spans
detailed_processor = SimpleSpanProcessor(detailed_exporter)
```

### 3. Resource Attribution by Service
```python
from opentelemetry.sdk.resources import Resource, SERVICE_NAME, SERVICE_VERSION

# Different resources for different services
web_resource = Resource.create({
    SERVICE_NAME: "web-frontend",
    SERVICE_VERSION: "1.0.0",
    "service.instance.id": "web-01",
})

api_resource = Resource.create({
    SERVICE_NAME: "api-backend", 
    SERVICE_VERSION: "2.1.0",
    "service.instance.id": "api-03",
})
```

## Complete Stack Examples

### AI Application Stack
```python
# GenAI instrumentations
from opentelemetry.instrumentation.agentscope import AgentScopeInstrumentor
from opentelemetry.instrumentation.openai_v2 import OpenAIInstrumentor
from opentelemetry.instrumentation.vertexai import VertexAIInstrumentor

# Supporting instrumentations
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.instrumentation.redis import RedisInstrumentor
from opentelemetry.instrumentation.sqlalchemy import SQLAlchemyInstrumentor

# Instrument the complete AI stack
AgentScopeInstrumentor().instrument()
OpenAIInstrumentor().instrument()
VertexAIInstrumentor().instrument()
RequestsInstrumentor().instrument()
RedisInstrumentor().instrument()
SQLAlchemyInstrumentor().instrument()
```

### High-Performance Web Application
```python
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
from opentelemetry.instrumentation.httpx import HTTPXClientInstrumentor
from opentelemetry.instrumentation.asyncpg import AsyncPGInstrumentor
from opentelemetry.instrumentation.redis import RedisInstrumentor
from opentelemetry.instrumentation.celery import CeleryInstrumentor

# Async web stack
FastAPIInstrumentor.instrument_app(app)
HTTPXClientInstrumentor().instrument()
AsyncPGInstrumentor().instrument()
RedisInstrumentor().instrument()
CeleryInstrumentor().instrument()
```

This comprehensive instrumentation coverage enables complete observability across the entire application stack, from AI model interactions to database queries and message queue operations.