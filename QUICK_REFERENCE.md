# LoongSuite Python Agent - Quick Reference Guide

## Installation

```bash
# Core OpenTelemetry
pip install opentelemetry-distro opentelemetry-exporter-otlp
opentelemetry-bootstrap -a install

# AgentScope Instrumentation  
pip install ./instrumentation-genai/opentelemetry-instrumentation-agentscope

# OpenAI v2 Instrumentation
pip install ./instrumentation-genai/opentelemetry-instrumentation-openai-v2
```

## Quick Start Commands

```bash
# Console output
opentelemetry-instrument --traces_exporter console --service_name myapp python app.py

# OTLP export
opentelemetry-instrument --traces_exporter otlp --exporter_otlp_endpoint http://localhost:4317 --service_name myapp python app.py

# With LoongCollector
opentelemetry-instrument --exporter_otlp_protocol grpc --traces_exporter otlp --exporter_otlp_insecure true --exporter_otlp_endpoint 127.0.0.1:6666 --service_name myapp python app.py
```

## Core API Classes

| Class | Purpose | Usage |
|-------|---------|-------|
| `BaseInstrumentor` | Base class for all instrumentors | `class MyInstrumentor(BaseInstrumentor):` |
| `AgentScopeInstrumentor` | AgentScope AI framework instrumentation | `AgentScopeInstrumentor().instrument()` |
| `OpenAIInstrumentor` | OpenAI API instrumentation | `OpenAIInstrumentor().instrument()` |
| `VertexAIInstrumentor` | Google VertexAI instrumentation | `VertexAIInstrumentor().instrument()` |

## Essential Functions

### Instrumentation Control
```python
# Enable instrumentation
instrumentor = AgentScopeInstrumentor()
instrumentor.instrument()

# Check status
if instrumentor.is_instrumented_by_opentelemetry:
    print("Instrumented successfully")

# Disable instrumentation  
instrumentor.uninstrument()
```

### Utility Functions
```python
from opentelemetry.instrumentation.utils import (
    extract_attributes_from_object,
    http_status_to_status_code,
    unwrap
)

# Extract object attributes
attrs = extract_attributes_from_object(obj, ["name", "value"])

# Convert HTTP status
status = http_status_to_status_code(404)  # Returns StatusCode.ERROR

# Remove wrapper
unwrap("module.path", "function_name")
```

### Custom Spans
```python
from opentelemetry import trace

tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("my-operation") as span:
    span.set_attribute("operation.type", "processing")
    result = do_work()
    span.set_attribute("operation.result", str(result))
```

## Environment Variables

### Core Configuration
```bash
export OTEL_SERVICE_NAME="my-ai-app"
export OTEL_RESOURCE_ATTRIBUTES="service.version=1.0.0,deployment.environment=prod"
export OTEL_TRACES_EXPORTER="otlp"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4317"
```

### GenAI Specific
```bash
export OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT="true"
```

### Disable Instrumentations
```bash
export OTEL_PYTHON_DISABLED_INSTRUMENTATIONS="urllib3,requests"
```

## Common Instrumentations

### Web Frameworks
```python
# Flask
from opentelemetry.instrumentation.flask import FlaskInstrumentor
FlaskInstrumentor().instrument_app(app)

# FastAPI  
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
FastAPIInstrumentor.instrument_app(app)

# Django
from opentelemetry.instrumentation.django import DjangoInstrumentor
DjangoInstrumentor().instrument()
```

### HTTP Clients
```python
# Requests
from opentelemetry.instrumentation.requests import RequestsInstrumentor
RequestsInstrumentor().instrument()

# HTTPX
from opentelemetry.instrumentation.httpx import HTTPXClientInstrumentor
HTTPXClientInstrumentor().instrument()
```

### Databases
```python
# SQLAlchemy
from opentelemetry.instrumentation.sqlalchemy import SQLAlchemyInstrumentor
SQLAlchemyInstrumentor().instrument()

# Redis
from opentelemetry.instrumentation.redis import RedisInstrumentor
RedisInstrumentor().instrument()

# PostgreSQL
from opentelemetry.instrumentation.psycopg2 import Psycopg2Instrumentor
Psycopg2Instrumentor().instrument()
```

## Response Propagation
```python
from opentelemetry.instrumentation.propagators import TraceResponsePropagator

# Inject trace context into response
propagator = TraceResponsePropagator()
response_headers = {}
propagator.inject(response_headers)
```

## AgentScope Integration Example

```python
import agentscope
from agentscope.agents import DialogAgent
from opentelemetry.instrumentation.agentscope import AgentScopeInstrumentor

# Enable instrumentation
AgentScopeInstrumentor().instrument()

# Your AgentScope application
agentscope.init(model_configs={
    "config_name": "my-model",
    "model_name": "qwen-max",
    "model_type": "dashscope_chat", 
    "api_key": "YOUR-API-KEY"
})

agent = DialogAgent(
    name="Assistant",
    sys_prompt="You are a helpful assistant.",
    model_config_name="my-model"
)

# This will be automatically traced
response = agent("Hello, how are you?")
```

## Error Handling

```python
from opentelemetry import trace
from opentelemetry.trace import Status, StatusCode

with tracer.start_as_current_span("risky-operation") as span:
    try:
        result = risky_function()
        span.set_status(Status(StatusCode.OK))
    except Exception as e:
        span.set_status(Status(StatusCode.ERROR, str(e)))
        span.record_exception(e)
        raise
```

## Multiple Exporters Setup

```python
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor, ConsoleSpanExporter

# Setup tracer
trace.set_tracer_provider(TracerProvider())

# Console exporter for debugging
console_exporter = ConsoleSpanExporter()
trace.get_tracer_provider().add_span_processor(
    BatchSpanProcessor(console_exporter)
)

# OTLP exporter for production
otlp_exporter = OTLPSpanExporter(endpoint="http://localhost:4317")
trace.get_tracer_provider().add_span_processor(
    BatchSpanProcessor(otlp_exporter)
)
```

## Resource Configuration

```python
from opentelemetry.sdk.resources import Resource

resource = Resource.create({
    "service.name": "my-ai-application",
    "service.version": "1.0.0", 
    "deployment.environment": "production",
    "service.instance.id": "instance-01"
})
```

## Complete AI Stack Setup

```python
# All GenAI instrumentations
from opentelemetry.instrumentation.agentscope import AgentScopeInstrumentor
from opentelemetry.instrumentation.openai_v2 import OpenAIInstrumentor  
from opentelemetry.instrumentation.vertexai import VertexAIInstrumentor

# Supporting stack
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.instrumentation.redis import RedisInstrumentor
from opentelemetry.instrumentation.sqlalchemy import SQLAlchemyInstrumentor

# Enable all instrumentations
AgentScopeInstrumentor().instrument()
OpenAIInstrumentor().instrument()
VertexAIInstrumentor().instrument()
RequestsInstrumentor().instrument()
RedisInstrumentor().instrument()
SQLAlchemyInstrumentor().instrument()
```

## Troubleshooting

### Check Instrumentation Status
```python
instrumentor = AgentScopeInstrumentor()
print(f"Is instrumented: {instrumentor.is_instrumented_by_opentelemetry}")
print(f"Dependencies: {instrumentor.instrumentation_dependencies()}")
```

### Enable Debug Logging
```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

### Skip Dependency Checks
```python
instrumentor.instrument(skip_dep_check=True)
```

## LoongCollector Integration

### Configuration File (otlp.yaml)
```yaml
enable: true
global:
  StructureType: v2
inputs:
  - Type: service_otlp
    Protocols:
      GRPC:
        Endpoint: 0.0.0.0:6666
flushers:
  - Type: flusher_otlp
    Traces:
      Endpoint: http://127.0.0.1:4317
```

### Run with LoongCollector
```bash
opentelemetry-instrument \
    --exporter_otlp_protocol grpc \
    --traces_exporter otlp \
    --exporter_otlp_insecure true \
    --exporter_otlp_endpoint 127.0.0.1:6666 \
    --service_name myapp \
    python app.py
```

## Performance Tuning

### Batch Processing Configuration
```python
from opentelemetry.sdk.trace.export import BatchSpanProcessor

processor = BatchSpanProcessor(
    exporter,
    max_queue_size=2048,
    max_export_batch_size=512,
    export_timeout_millis=5000,
    schedule_delay_millis=500
)
```

### Sampling Configuration  
```python
from opentelemetry.sdk.trace.sampling import TraceIdRatioBasedSampler

# Sample 10% of traces
sampler = TraceIdRatioBasedSampler(rate=0.1)
```

This quick reference covers the most commonly used APIs and configuration patterns for LoongSuite Python Agent.