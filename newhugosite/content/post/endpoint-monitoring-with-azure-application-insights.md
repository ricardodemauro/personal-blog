+++
categories = ["Azure", "Application Insights", "en", "devops", "Monitoring"]
date = 2020-09-28T15:14:09Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1591696205602-2f950c417cb9?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=MnwxMTc3M3wwfDF8c2VhcmNofDV8fGNoYXJ0c3xlbnwwfHx8fDE2NjczMDgzMDA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "endpoint-monitoring-with-azure-application-insights"
tags = ["Azure", "Application Insights", "en", "devops", "Monitoring"]
title = "Endpoint Monitoring with Azure Application Insights"

+++


Let's explore the use of Microsoft Azure Application Insights to monitor the health of our application using the endpoint _/healthcheck_.

Applications Insights it's a great tool for monitoring, error logging, performance monitoring, dependency mapping, and other things. In other words, it's a full APM - Application Performance Management - ready for you to use in Production Environments.

> Application Insights, a feature of [Azure Monitor](https://docs.microsoft.com/pt-br/azure/azure-monitor/overview), is an extensible Application Performance Management (APM) service for developers and DevOps professionals.[https://docs.microsoft.com/pt-br/azure/azure-monitor/app/app-insights-overview](https://docs.microsoft.com/pt-br/azure/azure-monitor/app/app-insights-overview)

## 💬 In this Issue

- [Recap of Previous Articles](#recap-of-previous-articles)
- [Check the Prices Before Use in Production](#check-the-prices-before-use-in-production)
- [One Simple Requirement](#one-simple-requirement)
- [Creating the Application Insights Resource](#creating-application-insights-resource)
- [Conclusion - The Results](#conclusion-the-results)

## Recap of Previous Articles

This is the third article about _Health Checks_ and _Application Monitoring_.

1.  [Adding Health Check endpoint](__GHOST_URL__/adding-health-checks-to-net-core-application/)
2. [Adding UI Health Check](__GHOST_URL__/adding-health-checks-ui/)
3. **Using Application Insights Endpoint Monitoring** --> this article
4. Using Azure App Services Endpoint Monitoring

## Check the Prices Before Use in Production

Check out the prices before adopt in your work project.

{{< figure src="https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/ai-pricing.png" caption="Prices from https://azure.microsoft.com/en-us/pricing/details/monitor/" >}}

Check the latest updated prices at [https://azure.microsoft.com/en-us/pricing/details/monitor/](https://azure.microsoft.com/en-us/pricing/details/monitor/).

## One Simple Requirement

Our API should be available over the internet (Application Insights should have access to hit it).

This article assumes that you have a **Health Checks** endpoint up and running available over the internet.

> Go back to this [article](__GHOST_URL__/adding-health-checks-to-net-core-application/) to set up and then return here._The Health Check UI is not necessary for this article, only the API endpoint._

I published mine at [https://webapphealthchecks.azurewebsites.net/](https://webapphealthchecks.azurewebsites.net/)You can use it for testing purposes if you like.

## Creating the Application Insights Resource

Access the Azure Portal at [_https://portal.azure.com_](https://portal.azure.com) and access the **Application Insights** blade and click **Add** to create a new resource.

{{< figure src="https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/portal-azure.png" caption="Create Application Insights Resource" >}}

Enter all the required information to create the resource, such as _subscription_, _resource group_, etc.When asked for the _Resource Mode_ choose the `Classic`.

{{< figure src="https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/ai-create-1.png" caption="Creating the AI" >}}

Once the Application Insights it's created, go to the `Availability` section.This is where the monitoring tool is configured to watch our endpoint.

{{< figure src="https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/ai-created.png" caption="Go to Availability" >}}

Now click on the `+Add Test` button.

{{< figure src="https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/AI-create-test.png" caption="Adding Test" >}}

Fill in the required information.

* Test name: _AI-Ping-WebApp_
* Test Type: _URL ping test_
* URL: [_https://webapphealthchecks.azurewebsites.net/healthcheck_](https://webapphealthchecks.azurewebsites.net/healthcheck)
* Parse dependent requests: _unchecked_
* Enable retries for availability test failures: _checked_
* Test frequency: _5 minutes_
* Test locations: _default values_
* Test Timeout: _120 seconds_
* HTTP Response: _checked_
* Status code must equal: _200_
* Content match: _checked_
* Content must contain: _Healthy_
* Alerts: _leave as default_

And then **Create**.

{{< figure src="https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/ai-ping-test-full-2.png" caption="Configure Application Insights Web Test" >}}

## The Results

Now just wait for the tests to begin and show the results. After a few minutes, you should get something like this.

{{< figure src="https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/ai-ping-test-result.png" caption="Line View Test Results" >}}

{{< figure src="https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/ai-ping-test-result-2.png" caption="Scatter Plot Test Results" >}}

## **😎 Final thoughts**

Wow, finally we made it. That is enough!

Of course, we can keep going adding more monitoring and making more use of Application Insights.

Share in the comments section what you would do to make it even better or different.

## **❤️ Enjoy this article?**

****Forward to a friend**** and let them know.

