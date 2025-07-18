# LoongSuite Python Agent - Comprehensive API Documentation

## Table of Contents

1. [Overview](#overview)
2. [Core Instrumentation Framework](#core-instrumentation-framework)
3. [GenAI Instrumentation](#genai-instrumentation)
4. [Utility Functions](#utility-functions)
5. [Response Propagators](#response-propagators)
6. [Bootstrap and Auto-Instrumentation](#bootstrap-and-auto-instrumentation)
7. [Environment Variables](#environment-variables)
8. [Examples and Usage](#examples-and-usage)

## Overview

LoongSuite Python Agent is a customized distribution of upstream OpenTelemetry Python Agent, enhanced with support for popular AI agent frameworks. It provides comprehensive observability for Python applications, with special focus on GenAI instrumentations following the latest semantic conventions.

## Core Instrumentation Framework

### BaseInstrumentor

The foundation class for all instrumentors in the LoongSuite ecosystem.

#### Class: `opentelemetry.instrumentation.instrumentor.BaseInstrumentor`

**Description**: Abstract base class that all instrumentors must inherit from. Provides a standardized interface for instrumenting third-party libraries.

**Key Methods**:

```python
class BaseInstrumentor(ABC):
    def instrument(self, **kwargs: Any) -> Any:
        """
        Instrument the library.
        
        Args:
            **kwargs: Optional configuration parameters
            
        Returns:
            Result of instrumentation (implementation-specific)
            
        Example:
            instrumentor = MyInstrumentor()
            instrumentor.instrument()
        """
    
    def uninstrument(self, **kwargs: Any) -> Any:
        """
        Remove instrumentation from the library.
        
        Args:
            **kwargs: Optional configuration parameters
            
        Returns:
            Result of uninstrumentation (implementation-specific)
            
        Example:
            instrumentor.uninstrument()
        """
    
    @abstractmethod
    def instrumentation_dependencies(self) -> Collection[str]:
        """
        Return a list of python packages with versions that will be instrumented.
        
        Returns:
            Collection[str]: List of package dependencies in requirements.txt format
            
        Example:
            def instrumentation_dependencies(self) -> Collection[str]:
                return ['requests ~= 1.0', 'agentscope >= 0.1.0']
        """
    
    @property
    def is_instrumented_by_opentelemetry(self) -> bool:
        """
        Check if the library is currently instrumented.
        
        Returns:
            bool: True if instrumented, False otherwise
        """
```

**Usage Example**:
```python
from opentelemetry.instrumentation.instrumentor import BaseInstrumentor

class MyLibraryInstrumentor(BaseInstrumentor):
    def instrumentation_dependencies(self):
        return ["mylibrary>=1.0.0"]
    
    def _instrument(self, **kwargs):
        # Implementation-specific instrumentation logic
        pass
    
    def _uninstrument(self, **kwargs):
        # Implementation-specific uninstrumentation logic
        pass

# Usage
instrumentor = MyLibraryInstrumentor()
instrumentor.instrument()
```

## GenAI Instrumentation

### AgentScope Instrumentation

#### Class: `opentelemetry.instrumentation.agentscope.AgentScopeInstrumentor`

**Description**: Provides OpenTelemetry instrumentation for AgentScope applications, enabling automatic tracing of AI agent interactions.

**Installation**:
```bash
pip install ./instrumentation-genai/opentelemetry-instrumentation-agentscope
```

**Usage**:
```python
from opentelemetry.instrumentation.agentscope import AgentScopeInstrumentor

# Automatic instrumentation
AgentScopeInstrumentor().instrument()

# Your AgentScope code
import agentscope
agentscope.init(model_configs={...})
```

**Instrumented Components**:
- `agentscope.models.model.ModelWrapperBase.__init__`: Model initialization
- `agentscope.service.service_toolkit.ServiceToolkit._execute_func`: Tool execution

**Trace Attributes**:
- `gen_ai.prompt.{index}.message.role`: Role of the message (system, user, assistant)
- `gen_ai.prompt.{index}.message.content`: Content of the message
- `gen_ai.response.finish_reasons`: Completion reason codes

### OpenAI v2 Instrumentation

#### Class: `opentelemetry.instrumentation.openai_v2.OpenAIInstrumentor`

**Description**: Provides instrumentation for OpenAI API calls with enhanced GenAI semantic conventions support.

**Usage**:
```python
from opentelemetry.instrumentation.openai_v2 import OpenAIInstrumentor
from openai import OpenAI

OpenAIInstrumentor().instrument()

client = OpenAI()
response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

**Environment Variables**:
- `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT`: Set to "true" to capture message content in traces

### VertexAI Instrumentation

**Description**: Provides instrumentation for Google Cloud VertexAI services.

**Location**: `instrumentation-genai/opentelemetry-instrumentation-vertexai/`

## Utility Functions

### Core Utilities

#### Module: `opentelemetry.instrumentation.utils`

**Function**: `extract_attributes_from_object`
```python
def extract_attributes_from_object(
    obj: Any, 
    attributes: Sequence[str], 
    existing: Dict[str, str] | None = None
) -> Dict[str, str]:
    """
    Extract attributes from an object and convert them to strings.
    
    Args:
        obj: Object to extract attributes from
        attributes: List of attribute names to extract
        existing: Existing attributes dictionary to update
        
    Returns:
        Dict[str, str]: Dictionary of extracted attributes
        
    Example:
        class MyObject:
            def __init__(self):
                self.name = "test"
                self.value = 42
        
        obj = MyObject()
        attrs = extract_attributes_from_object(obj, ["name", "value"])
        # Returns: {"name": "test", "value": "42"}
    """
```

**Function**: `http_status_to_status_code`
```python
def http_status_to_status_code(
    status: int,
    allow_redirect: bool = True,
    server_span: bool = False,
) -> StatusCode:
    """
    Convert HTTP status code to OpenTelemetry canonical status code.
    
    Args:
        status: HTTP status code
        allow_redirect: Whether to treat 3xx as success
        server_span: Whether this is a server span
        
    Returns:
        StatusCode: OpenTelemetry status code
        
    Example:
        status = http_status_to_status_code(404)
        # Returns: StatusCode.ERROR
    """
```

**Function**: `unwrap`
```python
def unwrap(obj: object, attr: str) -> None:
    """
    Remove instrumentation wrapper from a function.
    
    Args:
        obj: Object containing the wrapped function or dotted import path
        attr: Name of the wrapped function
        
    Example:
        unwrap("requests.api", "request")
        # Or
        import requests.api
        unwrap(requests.api, "request")
    """
```

## Response Propagators

### TraceResponsePropagator

#### Class: `opentelemetry.instrumentation.propagators.TraceResponsePropagator`

**Description**: Experimental propagator that injects trace context into HTTP responses for client-side trace continuation.

**Methods**:
```python
class TraceResponsePropagator(ResponsePropagator):
    def inject(
        self,
        carrier: textmap.CarrierT,
        context: Optional[Context] = None,
        setter: textmap.Setter = default_setter,
    ) -> None:
        """
        Inject SpanContext into HTTP response carrier.
        
        Args:
            carrier: Response object to inject trace context into
            context: OpenTelemetry context (uses current if None)
            setter: Function to set headers in carrier
            
        Example:
            from opentelemetry.instrumentation.propagators import TraceResponsePropagator
            
            propagator = TraceResponsePropagator()
            response_headers = {}
            propagator.inject(response_headers)
            # Adds 'traceresponse' header with trace context
        """
```

**Custom Setters**:
```python
# For dictionary-like objects
from opentelemetry.instrumentation.propagators import DictHeaderSetter
setter = DictHeaderSetter()

# For function-based setting
from opentelemetry.instrumentation.propagators import FuncSetter
setter = FuncSetter(response.set_header)  # For frameworks like Falcon

# Usage
propagator.inject(carrier, setter=setter)
```

## Bootstrap and Auto-Instrumentation

### Bootstrap Functionality

#### Module: `opentelemetry.instrumentation.bootstrap`

**Description**: Provides automatic installation and management of instrumentation packages.

**Command Line Usage**:
```bash
# Install all available instrumentations for detected packages
opentelemetry-bootstrap -a install

# List available instrumentations
opentelemetry-bootstrap --list

# Install specific instrumentations
opentelemetry-bootstrap -a install --package requests urllib3
```

**Programmatic Usage**:
```python
from opentelemetry.instrumentation.bootstrap import run_bootstrap

# Install instrumentations for detected packages
run_bootstrap(action="install", packages=None)
```

### Auto-Instrumentation

**Command Line Usage**:
```bash
# Run with automatic instrumentation
opentelemetry-instrument \
    --traces_exporter console \
    --service_name my-service \
    python my_app.py

# With OTLP exporter
opentelemetry-instrument \
    --traces_exporter otlp \
    --exporter_otlp_endpoint http://localhost:4317 \
    --service_name my-service \
    python my_app.py
```

## Environment Variables

### Core Configuration

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `OTEL_SERVICE_NAME` | Service name for traces | `unknown_service:python` | `my-application` |
| `OTEL_RESOURCE_ATTRIBUTES` | Resource attributes | Empty | `service.version=1.0.0,deployment.environment=prod` |
| `OTEL_TRACES_EXPORTER` | Trace exporter type | `otlp` | `console,otlp` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | OTLP endpoint URL | `http://localhost:4317` | `https://api.honeycomb.io` |

### GenAI Specific

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT` | Capture message content | `false` | `true` |

### Instrumentation Control

| Variable | Description | Default | Example |
|----------|-------------|---------|---------|
| `OTEL_PYTHON_DISABLED_INSTRUMENTATIONS` | Disable specific instrumentations | Empty | `requests,urllib3` |
| `OTEL_PYTHON_LOG_CORRELATION` | Enable log correlation | `false` | `true` |

## Examples and Usage

### Basic AgentScope Integration

```python
import agentscope
from agentscope.agents import DialogAgent
from opentelemetry.instrumentation.agentscope import AgentScopeInstrumentor

# Enable instrumentation
AgentScopeInstrumentor().instrument()

# Initialize AgentScope
agentscope.init(
    model_configs={
        "config_name": "my-qwen-max",
        "model_name": "qwen-max", 
        "model_type": "dashscope_chat",
        "api_key": "YOUR-API-KEY",
    }
)

# Create agents (automatically instrumented)
angel = DialogAgent(
    name="Angel",
    sys_prompt="You're a helpful assistant named Angel.",
    model_config_name="my-qwen-max",
)

# Agent interactions will be automatically traced
msg = angel("Hello, how are you?")
```

### Manual Instrumentation with Custom Spans

```python
from opentelemetry import trace
from opentelemetry.instrumentation.agentscope import AgentScopeInstrumentor

# Enable instrumentation
AgentScopeInstrumentor().instrument()

tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("custom-operation") as span:
    span.set_attribute("operation.type", "data-processing")
    
    # Your custom logic here
    result = process_data()
    
    span.set_attribute("operation.result", str(result))
```

### Advanced Configuration with Multiple Exporters

```python
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.exporter.jaeger.thrift import JaegerExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Configure tracer provider
trace.set_tracer_provider(TracerProvider())

# Add OTLP exporter
otlp_exporter = OTLPSpanExporter(
    endpoint="http://localhost:4317",
    insecure=True
)
trace.get_tracer_provider().add_span_processor(
    BatchSpanProcessor(otlp_exporter)
)

# Add Jaeger exporter
jaeger_exporter = JaegerExporter(
    agent_host_name="localhost",
    agent_port=6831,
)
trace.get_tracer_provider().add_span_processor(
    BatchSpanProcessor(jaeger_exporter)
)
```

### Integration with LoongCollector

```yaml
# LoongCollector configuration (otlp.yaml)
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

```bash
# Run application with LoongCollector
opentelemetry-instrument \
    --exporter_otlp_protocol grpc \
    --traces_exporter otlp \
    --exporter_otlp_insecure true \
    --exporter_otlp_endpoint 127.0.0.1:6666 \
    --service_name demo \
    python my_app.py
```

### Error Handling and Debugging

```python
import logging
from opentelemetry.instrumentation.agentscope import AgentScopeInstrumentor

# Enable debug logging
logging.basicConfig(level=logging.DEBUG)

try:
    # Instrument with error handling
    AgentScopeInstrumentor().instrument()
except Exception as e:
    logging.error(f"Failed to instrument: {e}")
    
# Check instrumentation status
instrumentor = AgentScopeInstrumentor()
if instrumentor.is_instrumented_by_opentelemetry:
    print("Successfully instrumented")
else:
    print("Instrumentation failed")
```

### Custom Instrumentor Development

```python
from opentelemetry.instrumentation.instrumentor import BaseInstrumentor
from opentelemetry.instrumentation.utils import unwrap
from wrapt import wrap_function_wrapper

class MyLibraryInstrumentor(BaseInstrumentor):
    def instrumentation_dependencies(self):
        return ["mylibrary>=1.0.0"]
    
    def _instrument(self, **kwargs):
        tracer_provider = kwargs.get("tracer_provider")
        tracer = trace.get_tracer(__name__, tracer_provider=tracer_provider)
        
        def wrapper(wrapped, instance, args, kwargs):
            with tracer.start_as_current_span("mylibrary.operation") as span:
                span.set_attribute("mylibrary.method", wrapped.__name__)
                return wrapped(*args, **kwargs)
        
        wrap_function_wrapper("mylibrary.module", "function_name", wrapper)
    
    def _uninstrument(self, **kwargs):
        unwrap("mylibrary.module", "function_name")
```

## Best Practices

### 1. Resource Attribution
Always set meaningful resource attributes:
```python
from opentelemetry.sdk.resources import Resource

resource = Resource.create({
    "service.name": "my-ai-application",
    "service.version": "1.0.0",
    "deployment.environment": "production",
})
```

### 2. Span Naming
Use descriptive span names that follow semantic conventions:
```python
with tracer.start_as_current_span("llm.chat.completion") as span:
    span.set_attribute("gen_ai.system", "openai")
    span.set_attribute("gen_ai.request.model", "gpt-4")
```

### 3. Error Handling
Always handle exceptions in custom spans:
```python
with tracer.start_as_current_span("custom.operation") as span:
    try:
        result = risky_operation()
        span.set_status(trace.Status(trace.StatusCode.OK))
    except Exception as e:
        span.set_status(trace.Status(
            trace.StatusCode.ERROR, 
            description=str(e)
        ))
        span.record_exception(e)
        raise
```

### 4. Performance Considerations
Use sampling to reduce overhead in high-throughput applications:
```python
from opentelemetry.sdk.trace.sampling import TraceIdRatioBasedSampler

# Sample 10% of traces
sampler = TraceIdRatioBasedSampler(rate=0.1)
```

## Support and Community

- **User Group**: [DingTalk User Group](https://qr.dingtalk.com/action/joingroup?code=v1,k1,VaFSqbGiRY0iAL3GGd18DRWDyb1HpgOuyfDzsX3Drng=&_dt_no_comment=1&origin=11)
- **Developer Group**: [DingTalk Developer Group](https://qr.dingtalk.com/action/joingroup?code=v1,k1,mexukXI88tZ1uiuLYkKhdaETUx/K59ncyFFFG5Voe9s=&_dt_no_comment=1&origin=11)
- **GitHub**: [loongsuite-python-agent](https://github.com/alibaba/loongsuite-python-agent)
- **Observability Community**: [https://observability.cn](https://observability.cn)