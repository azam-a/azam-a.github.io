---
layout: post
title: Integrating JotForm Webhook with AWS API Gateway & Lambda (Python)
---

tl;dr: JotForm Webhook POSTs as multipart/form-data, which AWS API Gateway & Lambda can't process by default. My workaround - base64 encode at API Gateway, then pass to Lambda.

<!--more-->

Recently I was developing a solution to generate PDFs from an online form. The client uses JotForm, so I guess I could use API Gateway and Lambda to create a maintenance-free, serverless service (function) in the cloud. Moreover, JotForm has built-in Webhook to send the form data anywhere upon submission.

I faced a problem to wire things up end-to-end. From API Gateway logs:

{% highlight bash %}
Lambda invocation failed with status 400
Execution failed: Could not parse request body into json:
Unexpected character ('-' (code 45)) in numeric value:
expected digit (0-9) to follow minus sign,
for valid numeric value at...
{% endhighlight %}

Browsing through the internets, I found some discussions on handling multipart/form-data [[1]](http://stackoverflow.com/q/31645205) [[2]](http://stackoverflow.com/q/33981482) [[3]](https://aws.amazon.com/blogs/compute/amazon-api-gateway-mapping-improvements/).

After a few trial and error attempts, with inspecting the logs at both the API Gateway and Lambda, I managed to get it working.

First, select `Integration Request` in API Gateway console:
![_config.yml]({{ site.baseurl }}/images/jotform_lambda/api_gateway_method_execution.png)

Then, select `Body Mapping Templates`:
![_config.yml]({{ site.baseurl }}/images/jotform_lambda/api_gateway_integration_request.png)

After that, use the recommended request body passthrough option: 
![_config.yml]({{ site.baseurl }}/images/jotform_lambda/api_gateway_body_mapping_template.png)

Add a new Content-Type, `multipart/form-data`. Then define the mapping template:
{% highlight json %}
{"body_base64encoded": "$util.base64Encode($input.body)"}
{% endhighlight %}

And in my lambda_function.py, I decoded the payload and extracted the relevant information.

{% highlight python %}
import base64
import logging
import re

import orchestrator

logger = logging.getLogger()
logger.setLevel(logging.INFO)


def lambda_handler(event, context):
    logger.info("received Event: {}".format(event))
    decoded_event = decode(event)
    submission_id = extract_submission_id(decoded_event)
    orchestrator.main(submission_id)


def decode(event):
    bytestring = base64.b64decode(event["body_base64encoded"])
    return bytestring.decode("utf-8")


def extract_submission_id(string):
    expression = re.compile(
        "name\=\"submissionID\"\\r\\n\\r\\n(\d*)",
        flags=re.UNICODE)
    match = re.search(expression, string)
    return match.group(1)
{% endhighlight %}

In my case, I extracted JotForm's Submission ID, and used the ID to request a cleaner JSON using JotForm's API (GET /submission{id}).

Another alternative is to extract the relevant information in `Body Mapping Template` in API Gateway, thus can prevent redundant data pass-through.