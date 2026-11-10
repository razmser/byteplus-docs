# Best practices for handling burst traffic

This document explains how services experiencing sudden traffic spikes can make simple changes to application code to avoid rate limits caused by spikes and maintain request success rates.

<span id="2d1eb0be"></span>
# What are rate limits?

The ModelArk API enforces rate limits on requests per time window, token usage, usage growth rate, and concurrent requests to ensure stable operation and fair resource allocation. When a client's request triggers the API's rate limits, the API responds with a 429: 'Too Many Requests' error.

<span id="bb51679a"></span>
## Why is rate limiting necessary?

Rate limits are a common practice for API services, and configuring rate limits serves the following purposes:


* **Reduce API abuse or misuse**: malicious attacks send a large number of requests to the server, attempting to overload the service and cause service interruptions. ModelArk rate limit settings can effectively prevent such scenarios.

* **Ensure fair user access**: avoid individuals or organizations sending too many requests, which slows others' service access and response times. Limit per\-user request throughput (requests per unit time) to ensure that as many users as possible can use the API normally and reduce API service slowdowns.

* **Help manage overall infrastructure load**: if API service requests spike, they may put significant pressure on server\-side resources, causing performance issues. Rate limit settings can help all users maintain a smooth, stable experience.


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">Rate limiting defines an upper bound on service usage per unit time—it is not a hard guarantee of available capacity. When workload spikes (for example, burst traffic) or the system stays under sustained high load, rate limiting may still be triggered. To make the best use of your rate limit quota and avoid failed requests consuming it, we recommend smoothing traffic ramp\-up and/or distributing traffic across models.</div>


<span id="c683eb36"></span>
## Rate limit targets

ModelArk currently supports the following rate limit targets:


* **Model rate limits**: under the same primary account, limits are enforced per model (regardless of version). They are set by ModelArk and cannot be manually adjusted. For each model's default rate limit information, see [Model list](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310).


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">If you have traffic demands exceeding the default limits, submit a ticket to request a quota increase.</div>



* **Inference endpoint rate limits**: rate limits apply to inference endpoints (EP). Users can adjust them to flexibly control model service usage in applications.


<span id="667b1a4d"></span>
## Types of rate limit

ModelArk provides various rate limit metrics.


|Rate limit type |Description |Applies to |
|---|---|---|
|TPM |Per\-minute token limit, including the model's input and output. |Typically used as the rate limit metric for model services that bill per token in online inference. |
|TPD |Daily token limit, including the model's input and output. |Typically a metric for rate limits on Batch\-processing model services. |
|RPM |Limit on requests per minute to mitigate DoS attacks. |Almost all models have a corresponding RPM metric. |
|IPM |Limit on the number of pictures generated per minute for picture\-generation models. |Typically a metric for rate limits on picture\-generation model services. |
|Inflight Batchsize |Number of concurrent in\-flight requests: the limit on how many requests for the specified model can be processed simultaneously under the same primary account. An in\-flight request spans the entire lifecycle, including queuing, data transfer, computation, and response delivery. |Nearly all models have a corresponding concurrency metric. |


<span id="4d33ed0d"></span>
## Error codes for rate limits

> For detailed ModelArk error codes, see [Error codes](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1299023).


<span id="b52e7d46"></span>
# What is burst traffic?

Request volume can fluctuate sharply within a given time window, for example a sudden surge of requests, commonly seen in scheduled data processing jobs and trending events. Compared with the previous minute, an increase of 3 million TPM or a 20% increase within 3 minutes is recognized by ModelArk as burst traffic (the platform dynamically adjusts the threshold based on current load and capacity).

The TPM chart is shown below:


|Burst Traffic Example |Description |
|---|---|
|<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/77f12271fa144237ac9bb1ca92331b96~tplv-goo7wpa0wc-image.image) </span> |**Common patterns**<br><br><br>* Increased request frequency<br><br>* Sudden spikes in input or output length<br><br>   **Monitoring metrics (TPM, RPM)** <br><br>* In the console, go to [Online Inference](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint) \> **View details** \> **Monitoring** |


<span id="8c1934da"></span>
# Platform handling strategy

In this scenario, the platform quickly expands the service cluster for the specified model to progressively accommodate the load. At the same time, to protect the experience of most users, when burst traffic consumes too many resources and affects overall service utility, the platform prioritizes rate\-limiting or circuit\-breaking burst traffic to safeguard other users' utility. When rate limiting or circuit breaking is triggered, ModelArk responds to the surge in requests with the "429: 'Too Many Requests'" error and gradually recovers as resources expand to a certain level (on the order of minutes).

Burst traffic rate\-limiting error codes are as follows:


|Status code |Error type |Error Code |Error message |Description |
|---|---|---|---|---|
|429 |TooManyRequests |ServerOverloaded |The service is currently unable to handle additional requests due to server overload. Please retry later. Request ID: |Service resources are insufficient. Please try again later. It often occurs when there is a sudden increase in calling traffic or when an inference endpoint is called after being idle for a long time.<br><br><div data-tips="true" data-tips-type="tip" data-tips-is-title="true">tip</div><br><br><br><div data-tips="true" data-tips-type="tip">If you hit the burst\-traffic limit when calling the <code>seed-1-8</code> models or earlier models, this error code is returned.</div><br> |
|429 |TooManyRequests |RequestBurstTooFast |System protection triggered by request burst. Please slow down traffic growth and increase requests gradually before retrying. |The surge in request volume has triggered system protection. Please slow down the ramp\-up rate, gradually increase traffic, and try again.<br><br><div data-tips="true" data-tips-type="tip" data-tips-is-title="true">tip</div><br><br><br><div data-tips="true" data-tips-type="tip">If you hit the burst\-traffic limit when calling the <code>seed-2-0</code> models or later models, this error code is returned.</div><br> |


<span id="3a0a8c71"></span>
# Client\-side handling strategies

Business assessment indicates the presence of burst traffic rate limits. The following proactive optimization strategies are recommended to further improve request success rates and mitigate the risk of rate limits.


* Client\-side smoothing \- proactively smooth the traffic ramp\-up curve: Use buffering mechanisms (such as message queues or local task queues) to convert instantaneous burst traffic into low\-fluctuation requests, thereby controlling the rate of traffic increase to the platform. Prevent `ServerOverloaded` errors at the source by avoiding excessively steep traffic increases.

* Client\-side traffic splitting \- load\-balance traffic across multiple models: Leverage ModelArk's independent burst tolerance across multiple models to distribute traffic to several models with redundant capacity, preventing resource exhaustion of a single model.


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>



* <div data-tips="true" data-tips-type="warning">Only cross\-model distribution is supported (for example, calling both "Model A" and "Model B" at the same time): the resource pools of different models are independent of each other, which can distribute traffic pressure;</div>


* <div data-tips="true" data-tips-type="warning">Distribution across multiple endpoints or accounts for the same model is ineffective: all inference endpoints and accounts of the same model share the same underlying computing resource pool, so traffic will ultimately converge to the same resource pool, making it impossible to avoid the rate limit threshold of that model.</div>


* Purchase Model Units \- proactively expand the resource pool: Purchase model units, forecast your own traffic fluctuation trends, place an order to expand and purchase model units 15 minutes in advance, and use the expanded model units to handle your burst increments.


Recommendations for strategy selection


<span aceTableMode="list" aceTableWidth="2,2,3"></span>
|Business Scenario |Recommended Combination Strategies |ModelArk Capabilities |
|---|---|---|
|Unpredictable instantaneous peaks (hot events) |Regulation + Load balancing |Dynamic limit tolerance curve + Multi\-model resource isolation |
|Predictable periodic bursts (scheduled tasks) |Load balancing + Purchase model units |Scale up 15 minutes in advance + Independent resource pool |
|Core business with high stability requirements |Primarily regulation + Purchase backup units |Stable load interval + Resource priority assurance |


For services with burst traffic, consider the following strategies when sending task requests to avoid rate limits/circuit breaking.

<span id="b0143313"></span>
## Practice 1: client\-side peak shaving and valley filling

<span id="0bdd018b"></span>
### Design

`Client-side burst peak requests → MQ receives without limitation (peak shaving) → Consumer-side consumption at a controlled rate and steady pace (valley filling) → ModelArk SDK calls the large model`


1. **Peak shaving**: RabbitMQ acts as a traffic buffer; the producer has no rate limitation and handles all instantaneous peak requests, completely isolating peaks from the large model service.

2. **Valley filling**: The consumer side processes MQ backlog requests at a steady rate, fully utilizing the large model service capacity without resource waste.

3. **Controlled ramp\-up**: The consumption rate starts from `5 QPS`, **increases by 2 QPS every 5 seconds**, up to a maximum of 30 QPS. The growth rate remains constant, with no sudden increases, ensuring the large model service is not overloaded.

4. **Reliability**: MQ queue plus message persistence and manual ACK confirmation ensure zero message loss; failed requests automatically return to the queue for retry.


Code example:

```Python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# pip install arkruntime
import time
import json
import os
import threading
import pika
from arkruntime import Ark

# ===================== Global simplest config [All parameters can be replaced]=====================
MQ_QUEUE = "client_ark_queue"
MQ_CONF = pika.ConnectionParameters('127.0.0.1', 5672, credentials=pika.PlainCredentials('guest', 'guest'))
ARK_API_KEY = os.getenv('ARK_API_KEY')
ARK_MODEL = "seed-2-0-lite-260228"
# Slope = step / interval = 2 / 5 = 0.4 QPS/s, ensuring no sudden spikes
FLOW = {"curr_qps": 5, "max_qps": 30, "step": 2, "interval": 5, "count": 0}
LOCK = threading.Lock()

# ===================== 1. Minimal slope-controlled throttling (Core) =====================
def flow_schedule():
    """Background thread: gradually increases the consumption rate at a steady pace to control the ramp-up slope."""
    while True:
        time.sleep(FLOW["interval"])
        if FLOW["curr_qps"] < FLOW["max_qps"]:
            FLOW["curr_qps"] += FLOW["step"]

def reset_count():
    """Background thread: resets the request counter every second for precise QPS control."""
    while True:
        time.sleep(1)
        with LOCK: FLOW["count"] = 0

def allow():
    """Throttle check: determines whether the current request is allowed."""
    with LOCK:
        if FLOW["count"] < FLOW["curr_qps"]:
            FLOW["count"] += 1
            return True
    return False

# ===================== 2. MQ producer (Client-side request enqueueing for peak shaving) =====================
def producer(client_req):
    """Enqueues client requests into the MQ without rate limits to absorb peak traffic."""
    conn = pika.BlockingConnection(MQ_CONF)
    ch = conn.channel()
    ch.queue_declare(queue=MQ_QUEUE, durable=True)
    ch.basic_publish(exchange='', routing_key=MQ_QUEUE, body=json.dumps(client_req),
                     properties=pika.BasicProperties(delivery_mode=2))
    conn.close()

# ===================== 3. Ark SDK invocation (Core LLM request) =====================
def call_ark(msg):
    """✅ Standard usage: call the LLM via the official Ark SDK with minimal code."""
    client = Ark(
        api_key=ARK_API_KEY,
        base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    )
    resp = client.chat.completions.create(model=ARK_MODEL, messages=[{"role": "user", "content": msg}])
    return resp.choices[0].message.content

# ===================== 4. MQ Consumer (Valley filling + slope-controlled rate + Ark invocation; core logic) =====================
def consumer():
    """Consumer core: consumes the peak-shaved traffic at a slope-controlled steady rate and calls the Ark model."""
    conn = pika.BlockingConnection(MQ_CONF)
    ch = conn.channel()
    ch.queue_declare(queue=MQ_QUEUE, durable=True)
    ch.basic_qos(prefetch_count=1)

    def callback(_ch, method, _, body):
        req = json.loads(body)["msg"]
        if not allow():
            _ch.basic_nack(delivery_tag=method.delivery_tag, requeue=True)
            return
        try:
            ark_resp = call_ark(req)
            print(f"request:{req[:15]} | response:{ark_resp[:20]}")
            _ch.basic_ack(delivery_tag=method.delivery_tag)
        except Exception as e:
            _ch.basic_nack(delivery_tag=method.delivery_tag, requeue=True)

    ch.basic_consume(queue=MQ_QUEUE, on_message_callback=callback, auto_ack=False)
    print(f"consumption launching | initial QPS:{FLOW['curr_qps']} max QPS:{FLOW['max_qps']} slope-controlled")
    ch.start_consuming()

# ===================== Launch entrance =====================
if __name__ == '__main__':
    threading.Thread(target=flow_schedule, daemon=True).start()
    threading.Thread(target=reset_count, daemon=True).start()

    consumer()
```


<span id="9294f1d8"></span>
## Practice 2: Implement the QPS ramp\-up strategy (recommended)

The client monitors server response status in real time, dynamically adjusts the request rate, gradually increases throughput, effectively avoids service rate limits, and ensures service stability.

<span id="e9790d0a"></span>
### Design

The QPS ramp\-up working principle is shown in the figure below: count errors caused by rate limits at fixed time intervals to assess online service load.


1. Gradually increase the request rate (QPS);

2. When the error rate rises, reduce or stabilize QPS per the strategy;

3. When the service is stable, continue increasing QPS and repeat 1 and 2;

4. It ultimately reaches the total rate limits (TPM, RPM) of the platform's corresponding model


<span id="daece59b"></span>
### Core mechanism 1: dynamic rate adjustment

QPS Climber (per\-second query rate climber) uses a trial\-and\-error adjustment strategy. It dynamically adjusts the request rate by periodically evaluating the request success rate. The core implementation is in the `adjustQPS()` method:

```Go
func (q *QPSClimber) adjustQPS() {
// Calculate total requests and error rate
    acceptCount := float64(q.counter.Count(Accept))
    serverOverloadedCount := float64(q.counter.Count(ServerOverloadedCode))
    unknownCount := float64(q.counter.Count(UnknownCode))
    errorAll := serverOverloadedCount + unknownCount
    errorRatio := errorAll / acceptCount

    // Adjust QPS dynamically based on the error rate
    oldRate, newRate := float64(q.limiter.Limit()), float64(0)
    switch {
    case errorRatio >= q.options.StrictErrorRatio:
// High error rate scenario: reduce the request rate
        newRate = oldRate - oldRate*q.options.ProbeRatio
    case errorRatio >= q.options.RelaxErrorRatio:
// Moderate error rate scenario: maintain the current request rate
        newRate = oldRate
    case errorRatio > 0:
// Low error rate scenario: slightly increase the request rate to probe
        newRate = oldRate + oldRate*q.options.ProbeRatio
    case errorRatio == 0:
// Zero-error scenario: increase the request rate more aggressively
        newRate = oldRate + oldRate*q.options.FastProbeRatio

        // Prevent unbounded rate growth when the request volume is insufficient
        if acceptCount*1.5 < oldRate*q.options.AdjustInterval.Seconds() {
            newRate = oldRate
        }
    }

    // Apply rate adjustment and boundary limit settings
    finalRate := minMax(1, float64(q.options.MaxRate), newRate)
    if finalRate != oldRate {
        q.limiter.SetLimit(rate.Limit(finalRate))
        q.limiter.SetBurst(int(finalRate))
    }
}
```


<span id="6dfa3af8"></span>
### Core mechanism 2: client rate limits and retry strategy

QPS Climber implements two layers of protection:


1. **Client token bucket rate limits**: it uses the `golang.org/x/time/rate` package to implement the token bucket algorithm and ensures that requests do not exceed the configured QPS limit

2. **Exponential backoff retries**: for failed requests, apply an exponential backoff strategy to retry and avoid repeatedly hitting the server in a short period.


The core implementation is in the `DoRequest()` method:

```Go
func (q *QPSClimber) DoRequest(ctx context.Context, req *model.CreateChatCompletionRequest) (model.ChatCompletionResponse, error) {
// Initialize the exponential backoff
    backOff := newExponentialBackoff(q.options.RetryBaseDelay, q.options.RetryMaxDelay)

    for {
        select {
        case <-ctx.Done():
            return model.ChatCompletionResponse{}, ctx.Err()
        default:
        }

        // Client token bucket rate limits
        if !q.limiter.Allow() {
            backOff.wait()
            continue
        }

        // Log accepted requests and execute
        q.Record(Accept)
        resp, err := q.client.CreateChatCompletion(ctx, req)
        if err == nil {
            return resp, nil
        }

        // Error handling and retry decision
        statusCode, retry := needRetryError(err)
        q.Record(convertToCode(statusCode))

        if !retry {
            return resp, err
        }

        // Exponential backoff wait
        backOff.wait()
    }
}
```


<span id="3295a511"></span>
### Core mechanism 3: intelligent routing after error classification

Classify server\-side response errors into the following categories:


* `ServerOverloadedCode`: service overload (e.g., HTTP 429)

* `InternalServerError`: internal server error (e.g., HTTP 500)

* `UnknownCode`: other request errors (such as HTTP 400)


Use `convertToCode()` to implement intelligent error classification, providing decision support for dynamic rate adjustment:

```Go
func convertToCode(statusCode int) Code {
  switch {
  case statusCode >= http.StatusInternalServerError:
    return InternalServerError
  case statusCode == http.StatusTooManyRequests:
    return ServerOverloadedCode
  case statusCode == http.StatusOK:
    return SuccessCode
  default:
    return UnknownCode
  }
}
```


<span id="8b27df6d"></span>
### Configuration

```Go
func defaultOptions() *Options {
  return &Options{
    AdjustInterval:   10 * time.Second,
    InitialRate:      10,
    MaxRate:          1000,
    StrictErrorRatio: 0.05,
    RelaxErrorRatio:  0.01,
    ProbeRatio:       0.05,
    FastProbeRatio:   0.1,
    RetryBaseDelay: 100 * time.Millisecond,
    RetryMaxDelay:  3 * time.Second,
  }
}
```


**Parameter description**


|Parameter Name |Default Value |Recommended Value Range |Tuning Recommendations |
|---|---|---|---|
|**InitialRate** |10 |5\-50 |Start new services with a low value, based on the service's initial capacity settings |
|**MaxRate** |1000 |Estimate based on the service quota |Estimate using request volume (TPM) and request rate (RPM). Target 80%\-90% of the service's rated QPS to allow headroom |
|**AdjustInterval** |10s |5\-15s |Shorten the interval when traffic fluctuates significantly |
|**StrictErrorRatio** |0.05 |0.03\-0.1 |Error rate threshold 1. When the error rate exceeds this threshold, the request rate decreases. When stability requirements are high, lower this value. |
|**RelaxErrorRatio** |0.01 |0.005\-0.05 |Increase this value moderately when a small number of errors is acceptable. |
|**ProbeRatio** |0.05 |0.03\-0.1 |Rate adjustment step size; reduce it when fluctuations are large. |
|**RetryBaseDelay** |100 ms |50ms\-500ms |Unit: ms. Initial retry delay; adjust based on service response speed. |
|**RetryMaxDelay** |3 s |3s\-10s |Maximum retry wait time. |


<span id="17fc2d03"></span>
### Usage recommendations


* **Use the singleton pattern**: create a single `QPSClimber` instance in your application, and have all API calls share this instance to avoid wasting resources

* **Centralize error handling**: use the `needRetryError()` method to handle all errors and keep retry decisions consistent

* **Control concurrent requests**: use Go's `sync.WaitGroup` to implement efficient concurrent requests while controlling the overall rate with QPS Climber

   ```Go
   // Concurrent request example
   var wg sync.WaitGroup
   concurrency := 1000
   wg.Add(concurrency)
   
   for i := 0; i < concurrency; i++ {
       go func() {
           defer wg.Done()
           for total.Add(-1) >= 0 {
   // Business request code
               req := &model.CreateChatCompletionRequest{...}
               _, err := qpsClimber.DoRequest(ctx, req)
   // Error handling
           }
       }()
   }
   wg.Wait()
   ```
   

* **Warm\-up mechanism**: start the application with a low initial QPS and gradually increase it via `adjustInterval` to avoid a traffic surge during cold start that could trigger circuit breaking

* **Adaptive adjustment**: use QPS Climber's dynamic tuning so the system automatically adapts to traffic changes

* **Alerts and monitoring**: review the QPS adjustment logs and set alerts and monitoring for key metrics such as error rate and success rate

* **Fallback plan**: in extreme cases, consider implementing request fallback or a circuit\-breaking mechanism to keep core functionality available


<span id="f6bbf34f"></span>
## Practice 3: backoff and retry strategy

Using a backoff algorithm for request retries is a simple and effective way to avoid rate limits (not only in burst traffic scenarios). A randomized exponential backoff algorithm automates request retries.

<span id="5e2dc1ab"></span>
### Design

When a rate limit error occurs, pause briefly, then retry the failed request. If the request still fails, the system increases the wait duration and tries again. This process repeats until the request succeeds or reaches the maximum number of retries.

Advantages:


* Automatic retries allow the system to recover from rate limit errors, preventing crashes and data loss.

* Exponential backoff enables a fast first retry. If early retries fail, longer delays help avoid triggering rate limits.

* Add random jitter to the wait time to avoid a large number of requests retrying simultaneously.


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">Failed requests count toward the per\-minute request limit, so frequent retries will hit the rate limit threshold faster and lead to more request failures.</div>


<div data-tips="true" data-tips-type="warning">Using an exponential backoff algorithm for retries increases the likelihood of success while adhering to rate limits and helps ensure stable system operations.</div>


Code example:

Use Tenacity, a third\-party Python library, to simplify implementing retries. The following example shows how to add randomized exponential backoff to a request.

```Python
import os
# Upgrade the ModelArk SDK to the latest version. Run the following command to install: pip install -U arkruntime
from arkruntime import Ark
# Import the exponential backoff algorithm package
from tenacity import retry, stop_after_attempt, wait_random_exponential

client = Ark(
# Read your ModelArk API key from the environment variable.
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

@retry(wait=wait_random_exponential(min=1, max=60), stop=stop_after_attempt(6))
def completion_with_backoff(**kwargs):
    return client.chat.completions.create(**kwargs)

resp = completion_with_backoff(
    model="seed-1-6-250615", messages=[{"role": "user", "content": "hello"}]
)

print(resp.choices[0].message.content)
```
