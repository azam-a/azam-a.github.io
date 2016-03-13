---
layout: post
title: Email to Slack Notifier Using Python
---

![_config.yml]({{ site.baseurl }}/images/gocd2gmail2slack/gocddash.png)

In my current team at work, we use [GoCD](https://www.go.cd/) extensively. GoCD is used to compile, test, build, package, and deploy the artefacts to their intended environments. The lifecycle starts with a code check-in to TFS (we use TFS as SCM tool). Respective GoCD pipelines will kickstart automatically to fetch the latest version of the codebase, and it goes through the stages mentioned earlier.

Normally, the GoCD web dashboard is used to monitor the pipeline to see their status whether they are still building, stuck, passed, or failed. However in multi-team environments working on the same product, sometimes it is quite a hassle to constantly check the dashboard if there is any new changes to the codebase, especially for the shared upstream branches. We merge the upstream branch into the feature (team) branch regularly, to make sure that our feature branch also contains the latest version of the codebase.

Another place to check is the TFS web dashboard or its clients in Visual Studio for codebase change, but they do not contain the build information as well as the deployment information. And again, it is manual work to check the clients.

Initially, I started to search for existing solutions and found [gocd-slack-build-notifier](https://github.com/ashwanthkumar/gocd-slack-build-notifier). This requires installation of GoCD plugin and access to the GoCD server, which I did not have.

![_config.yml]({{ site.baseurl }}/images/gocd2gmail2slack/gocdnotify.png)

I played around the GoCD dashboard and found that it provides subscriptions of pipeline status via email notifications. Successfully getting emails sent to my inbox, I searched for email-to-Slack solution and found [gmail2slack](https://github.com/brooksc/gmail2slack). It didn't really suit my purpose as it will send everything sent to the Gmail inbox, and I require some kind of validation and filtering on which emails to be sent to Slack.

![_config.yml]({{ site.baseurl }}/images/gocd2gmail2slack/email.png)

Combining both of the ideas, I decided to write my own solution. 

Here it is ([gocd2gmail2slack](https://github.com/azam-a/gocd2gmail2slack))! It has some filtering rules (hardcoded at the moment) to only forward valid GoCD notification emails, skip some statuses, and can extract bits of information from both the email subject and body, particularly the TFS changeset information as well as the GoCD pipeline item. Our team can now just click at the pipeline name or changeset ID to directly go to GoCD server and TFS changeset, directly seeing build log and/or codebase diff.

![_config.yml]({{ site.baseurl }}/images/gocd2gmail2slack/slack.png)

I got it to work, and my team kind of like it (or maybe a bit annoyed) for the floods of Slack messages on our team channel. As an up side, we don't really look at GoCD dashboard anymore to monitor the build and deployment status or being surprised by broken builds and check-ins in upstream branches :)
