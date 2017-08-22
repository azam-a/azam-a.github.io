---
layout: post
title: Indominus Tex - Serverless Pizza Tracker on AWS
image: /images/pizza_tracker/cloudcraft-codename_domini.png
---

![Indominus Tex logo]({{ site.baseurl }}/images/pizza_tracker/indominus_tex_logo.png){:height="300px"}

tl;dr: Stack - AWS Lambda (Python 3.6), SimpleDB, Twilio, Flask, Zappa. For tracking Domino's Pizza Malaysia orders.

<!--more-->

Once every week or two weeks, I order pizza from Domino's. Their website has an order tracker:

![Great Pizza Service (GPS) Tracker]({{ site.baseurl }}/images/pizza_tracker/gps.png)

It is good enough, however after a few minutes of tracking I always encountered session timeout message and the tracker stopped working. If I refresh the page, I had to log in and navigate to the tracker again, which requires more typing and clicking. And the cycle continues.

I noticed that the website does an API call every few seconds to get the order status, and decided to build my own tracker that does not need me to constantly babysit the browser session timing out. The tracker will send an SMS instead.

![CircleCI 2.0 - Workflows]({{ site.baseurl }}/images/pizza_tracker/sms.png){:height="500px"}

Previously, I did have an Ubuntu VPS instance on [DigitalOcean](https://www.digitalocean.com/), which was a good cheap alternative to an always-running AWS EC2, before LightSail came along. I used to have a few Django or Flask based apps running on the same VPS. Deploying a new app or updating the existing apps took too much effort, as I had to SSH in to pull a new repository, configure nginx, databases, etc. I was introduced to [Fabric](https://github.com/fabric/fabric) by [this book](https://www.obeythetestinggoat.com/book/chapter_automate_deployment_with_fabric.html) which could make the tasks more bearable, but until today there is no official Python 3 package.

![Deployment map by Full Stack Python](https://raw.githubusercontent.com/mattmakai/fullstackpython.com/master/static/img/visuals/full-stack-python-map.png)
[Deployment map by Full Stack Python](https://www.fullstackpython.com/deployment.html)

Directly handling servers feel like a hassle nowadays. At work, we use Docker a lot. But if I were to use it for this project, I still need to pay the always-running container instance. With the cost constraint, I decided to use [AWS Lambda](https://aws.amazon.com/lambda/pricing/). In order for me to quickly develop, build and deploy my ~~toy~~ projects with cost in mind, going serverless via Lambda seems like a logical decision.

I have used Lambda before, but the set up was not without its own problems. In order to expose the Lambda function to the internets, AWS API Gateway has to be configured and wired up with AWS IAM role to each function. Then I discovered [Zappa](https://github.com/Miserlou/Zappa), a framework to package, deploy Python WSGI app on Lambda and API Gateway, without manually going through the AWS dashboard nor composing lower level operations via AWS SDK.

With Zappa as the _deployer_, I chose [Flask](http://flask.pocoo.org/) as the WSGI app framework. Initially I considered [Django](https://www.djangoproject.com/) as well, but decided against it as it seems to be a little _overkill_ for this small project. Due to my familiarity with Django, I structured the Flask code to mimic Django, eg into _Models_, _Controllers_ (Django's Model Manager) and using Django terms such as _Views_.

On the storage side, [SimpleDB was chosen, instead of DynamoDB](http://www.masonzhang.com/2013/06/2-reasons-why-we-select-simpledb.html), even though DynamoDB looks to be more suited and scalable for larger projects. There is [flask-simple](https://github.com/rdegges/flask-simple) SimpleDB extension for Flask, but I decided to wrap the [SimpleDB boto3 API](http://boto3.readthedocs.io/en/latest/reference/services/sdb.html) myself to learn more about it. 

To send text messages, [Twilio](https://www.twilio.com/) is used. The REST API for sending SMS looks simple enough, so I opted for that instead of the official Python SDK provided by Twilio.

{% highlight python %}
import os
import requests

class TwilioClient(object):

    def __init__(self, from_=os.environ["TWILIO_FROM_NUMBER"],
                 account_sid=os.environ["TWILIO_ACCOUNT_SID"],
                 auth_token=os.environ["TWILIO_AUTH_TOKEN"]):
        self.from_ = from_
        self.account_sid = account_sid
        self.auth_token = auth_token

    def send_message(self, message, to):
        url = (f"https://{self.account_sid}:{self.auth_token}" +
               f"@api.twilio.com/2010-04-01/Accounts/" +
               f"{self.account_sid}/Messages")
        payload = {"From": self.from_, "To": to, "Body": message}
        return requests.post(url, data=payload)
{% endhighlight %}

In this project, I used [pytest](https://docs.pytest.org/) for the first time, after exclusively utilising the built-in unittest module in Python standard library since I picked up Python. pytest test runner makes executing tests easier, without resorting to trial and error in finding out the correct absolute/relative import statements in test files. For this project, I still maintain the traditional jUnit (xUnit) test classes style in test files, but using pytest as the test runner.

[![CircleCI 2.0 - Workflows]({{ site.baseurl }}/images/pizza_tracker/circleci-workflows.png)]({{ site.baseurl }}/images/pizza_tracker/circleci-workflows.png)

This project utilises [CircleCI](https://circleci.com/) for CI-CD pipeline. Initially I did not intend for this project's repository to be publicly available on GitHub, and [TravisCI](https://travis-ci.org/) has limited offering for private GitHub repositories. And since this project is now public, perhaps TravisCI could work as well as an alternative. Both of them do support definition of secret environment variables even for public projects.

For this project I also migrated DNS servers for the domain from the original registrar to [AWS Route 53](https://aws.amazon.com/route53/). This is because the registrar does not support [Let's Encrypt](https://letsencrypt.org/), and I do not want to host the project using the ugly non-human-readable pre-generated random AWS API Gateway endpoint URL. Moreover, Route 53 provides free SSL certificates via [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/). It even has wildcard certificates for subdomains, which Let's Encrypt does not have yet. Let's Encrypt's certificates also have to be renewed every few months.

At the time of writing, this is how the architecture looks like:
[![Architecture Diagram - Codename Domini]({{ site.baseurl }}/images/pizza_tracker/cloudcraft-codename_domini.png)]({{ site.baseurl }}/images/pizza_tracker/cloudcraft-codename_domini.png)


For future enhancements, maybe the architecture could be improved. Currently, most of the contents are generated and served by Flask, including the HTML responses. Next step could be that [s3_website](https://github.com/laurilehmijoki/s3_website) to be used to deploy static HTML contents (or even rich JavaScript SPA eg Angular client), whilst the Flask app could just serve as REST API service, passing JSON around. However this may complicate deployment as there are more moving parts, because s3_website is built on top of Ruby and Java. Although nothing is impossible if you try hard enough.

Other frameworks in the ecosystem look promising as well. [Serverless](https://serverless.com/) focuses more on JavaScript/Node.js ecosystem, and [AWS Chalice](https://aws.amazon.com/blogs/developer/chalice-1-0-0-ga-release/) was recently released as Generally Available.

![Serverless All the Things!]({{ site.baseurl }}/images/pizza_tracker/allthethings.png)

- Disclaimer: This article is not sponsored by AWS (I wish they do)
- P/S: Vendor lock-in is real. Most of my stacks are overly dependent on AWS nowadays :'(

The project can be accessed here: [Indominus Tex](https://indominus-tex.sidebox.es)

Project repository: [codename: domini](https://github.com/azam-a/domini)
