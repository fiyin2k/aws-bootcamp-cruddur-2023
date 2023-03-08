# Week 2 — Distributed Tracing

## Honeycomb
## Install Packages
Install these packages to instrument our Flask app with OTEL(OpenTelemetry):

``` sh
pip install opentelemetry-api
pip install opentelemetry-sdk
pip install opentelemetry-exporter-otlp-proto-http
pip install opentelemetry-instrumentation-flask
pip install opentelemetry-instrumentation-requests
```
These packages can be installed through a .txt file called requirements.txt. Go to terminal and run:
``` sh
pip install -r requirements.txt
```
## Export Environment Variables
```
export HONEYCOMB_API_KEY=""
export HONEYCOMB_SERVICE_NAME=""
```

```
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME=""
```
### Include environment variables within docker-compose.yml
```
OTEL_SERVICE_NAME="${HONEYCOMB_SERVICE_NAME}"
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
```

##### Import libraries and initialize
```py
# app.py updates
    
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.sdk.trace.export import ConsoleSpanExporter, SimpleSpanProcessor 

# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)

# Initialize automatic instrumentation with Flask
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```
## Initialize automatic instrumentation with Flask
```py
#app.py update
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```
### Configure OTEL
```py
export OTEL_EXPORTER_OTLP_ENDPOINT="https://api.honeycomb.io"
export OTEL_EXPORTER_OTLP_HEADERS="x-honeycomb-team=tG937G8HLMb0tgQEnoCRTB"
export OTEL_SERVICE_NAME="your-service-name"
python app.py
```
<img width="1440" alt="Screenshot 2023-03-04 at 2 10 55 AM" src="https://user-images.githubusercontent.com/50529535/223774359-ca1fc611-6a35-4b05-a5e5-99ee989530b7.png">

### CHALLENGE: Run custom queries in Honeycomb and save them:
<img width="993" alt="Screenshot 2023-03-06 at 7 59 31 PM" src="https://user-images.githubusercontent.com/50529535/223774757-f1f71512-be16-4e01-a245-68980ada0bbc.png">
<img width="1287" alt="Screenshot 2023-03-06 at 8 03 20 PM" src="https://user-images.githubusercontent.com/50529535/223774797-f269f93c-082e-485f-a622-b180949d1f1f.png">

## AWS X-Ray
### Install aws xray-sdk
Add to requirements.txt
```sh
aws-xray-sdk
```
Pip install requirements.txt
```sh
pip install -r requirements.txt
```
App.py Updates
Install dependencies: xray_recorder, XRayMiddleware
```py
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.ext.flask.middleware import XRayMiddleware

xray_url = os.getenv("AWS_XRAY_URL")
xray_recorder.configure(service='Cruddur', dynamic_naming=xray_url)
XRayMiddleware(app, xray_recorder)
```
<img width="1440" alt="Screenshot 2023-03-07 at 7 11 08 PM" src="https://user-images.githubusercontent.com/50529535/223776108-840b0bba-c9ba-433a-80ce-cba73f610aba.png">
<img width="1440" alt="Screenshot 2023-03-07 at 11 16 10 PM" src="https://user-images.githubusercontent.com/50529535/223776122-2cd1a358-0b35-479e-9dd4-5486019eb8df.png">
<img width="1440" alt="Screenshot 2023-03-07 at 11 17 42 PM" src="https://user-images.githubusercontent.com/50529535/223778015-543ae1c7-f80d-42c5-ae49-666d3454bfb1.png">
<img width="1440" alt="Screenshot 2023-03-08 at 2 19 44 PM" src="https://user-images.githubusercontent.com/50529535/223778030-2797cbbb-1265-4496-a3ec-909c42eac275.png">

## CloudWatch Logs

Add to the requirements.txt
```
watchtower
```
```sh
pip install -r requirements.txt
```
App.py updates
```
import watchtower
import logging
from time import strftime
```
### Configure LOGGER to utilize CloudWatch
```py
LOGGER = logging.getLogger(__name__)
LOGGER.setLevel(logging.DEBUG)
console_handler = logging.StreamHandler()
cw_handler = watchtower.CloudWatchLogHandler(log_group='cruddur')
LOGGER.addHandler(console_handler)
LOGGER.addHandler(cw_handler)
LOGGER.info("some message")
```
<img width="1440" alt="Screenshot 2023-03-08 at 12 19 39 PM" src="https://user-images.githubusercontent.com/50529535/223780386-710b7b65-c879-4801-b6f1-4fa430ebc6b5.png">

## Rollbar
Created a new project in Rollbar
Install Rollbar dependencies:
in requirements.txt
```sh
blinker
rollbar
```
in app.py
```py
import rollbar
import rollbar.contrib.flask
from flask import got_request_exception
```
Set and view Rollbar environment variables
```sh
export ROLLBAR_ACCESS_TOKEN=""
gp env ROLLBAR_ACCESS_TOKEN=""
env | grep ROLLBAR
```
Include an enpoint to app.py 
endpoint to app.py
```py
@app.route('/rollbar/test')
def rollbar_test():
    rollbar.report_message('Hello World!', 'warning')
    return "Hello World!"
```
<img width="835" alt="Screenshot 2023-03-08 at 5 00 04 PM" src="https://user-images.githubusercontent.com/50529535/223780283-e46b7e19-8bab-4b4b-8202-6508b595cc85.png">
<img width="645" alt="Screenshot 2023-03-08 at 5 02 49 PM" src="https://user-images.githubusercontent.com/50529535/223780333-638dfc27-1212-4d93-887b-d9c82a928750.png">
