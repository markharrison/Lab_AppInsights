# Application Insights - Hands-on Lab Script - part 3

Mark Harrison : checked & updated 31 March 2020 - original 6 Aug 2018

![](Images/AppInsights.png)

- [Part 1 - Create AppInsights instance](appinsights-1.md)  
- [Part 2 - Develop and deploy AppInsights enabled webapp](appinsights-2.md)
- [Part 3 - Get insights on application](appinsights-3.md) ... this document
- [Part 4 - Advanced Analytics](appinsights-4.md)  
- [Part 5 - Availability Monitoring](appinsights-5.md)
- [Part 6 - Usage Behaviour Analysis](appinsights-6.md)

## Get insights on application

### AppInsights Overview

Check the AppInsights overview page.

Note the response times, server requests, failed requests.

![](Images/AppIns2AIBlade.png)

### Live Metrics

Live Metrics Stream provides a powerful and non-invasive diagnostic tool to watch what is happening on the web application in real time.

- Select the `Live Metrics Stream` menu item

![](Images/AppIns3LiveMetricsStream.png)

### Smart Detection - Performance Anomalies

Smart Detection is a machine learning feature that automatically analyzes the performance of a web application, and can warn about anomalties / potential problems.

- Select the `Smart Detection` menu item.

![](Images/AppIns3SmartDetection1.png)

Currently no issues, but select Settings to see the rules available and how to configure

![](Images/AppIns3SmartDetection2.png)

![](Images/AppIns3SmartDetection3.png)

### Application Map

Application Map helps you spot performance bottlenecks or failure hotspots across all components of your distributed application. Each node on the map represents an application component or its dependencies; and has health KPI and alerts status. You can click through from any component to more detailed diagnostics, such as Application Insights events.

![](Images/AppIns3ApplicationMap1.png)

![](Images/AppIns3ApplicationMap2.png)

- Click on the WebApp Component
- Investigate failures (need to access an invalid URL on the web application to generate the appropriate telemetry)

![](Images/AppIns3ApplicationMapFailures.png)

- Investigate performance

![](Images/AppIns3ApplicationMapPerf.png)

- Click on the client component
  - Investigate performance

  ![](Images/AppIns3ApplicationMapClient.png)

  ![](Images/AppIns3ApplicationMapClientPerf.png)

- Click on the backend component
- Investigate performance

![](Images/AppIns3ApplicationMapBackEnd.png)

![](Images/AppIns3ApplicationMapBackEndPerf.png)

### Profiler

**Profiling for ASP.NET Core 3.x applications isn't supported yet - jump over this section**
(Current workaround <https://github.com/microsoft/ApplicationInsights-Profiler-AspNetCore>)

The Application Insights Profiler tool displays detailed profiles of live requests that were served by your app. Profiler highlights the hot path that uses the most time. Requests with various response times are profiled on a sampling basis. By using a variety of techniques, you can minimize the overhead that's associated with the application.

- The WebApp needs to be upscaled to Basic or above to run the Profile

- For Profiler to upload traces, your application must be actively handling requests

- Select the `Enable profiler` button from the Performance blade

![](Images/AppIns3Profiler1.png)

- Select the `Profile Now` menu option

![](Images/AppIns3Profiler2.png)

![](Images/AppIns3Profiler3.png)

- It is also possible to configure Triggers for then the Profiling starts e.g. if CPU goes above 80%

![](Images/AppIns3ProfilerTriggers.png)

- Once the profile has run, the traces can be inspected.  Select a sample to display a code-level breakdown of time spent executing the request.

![](Images/AppIns3Profiler4.png)

## Exceptions

- Invoke the bad code / exception in the Web App

![](Images/AppIns3Crash1.png)

- Select the `Failures` menu option

![](Images/AppIns3Crash2.png)

![](Images/AppIns3Crash3.png)

- Inspect details of the exception - including the exception message and code stack

![](Images/AppIns3Crash4.png)

There is an option to create a work Item in either VSTS / Github to report / manage the error.  The first time this is accessed, configuration / authentication is required.

- On the Exception details blade, click on `Create Work Item`

![](Images/appIns3ConfigWorkItem.png)

![](Images/AppIns3NewWorkItem.png)

- Inspect the item in VSTS (or Github)

![](Images/AppIns3MyWorkItem.png)

![](Images/AppIns3MyWorkItem2.png)

### Snapshot Debugger

Application Insights Snapshot Debugger allows you to automatically collect a debug snapshot from live web applications. The snapshot shows the state of source code and variables at the moment an exception was thrown.

The first time an exception happens, Snapshot debugger remembers it and adds it to the collection plan. It doesn’t take a snapshot. The second time it happens, then it checks against the collection and take the snapshot. This is because it needs to deoptimize the IL first, and then next time it happens, it can capture the snapshot with variables deoptimized.  Furthermore, it may be too much noise to act on every exception. If it happens a second time, then it’s probably something worth taking note of.

- On the Exception details blade, click on the [`]Open debug snapshot]
- The snapshot can be downloaded and opened in Visual Studio

![](Images/AppIns3DebugSnapShot.png)

- Subscription owners should assign the Application Insights Snapshot Debugger role to users who will inspect snapshots.  There will be a prompt for this.

![](Images/AppIns3DebugSnapShot2.png)

Download Snapshot and Open in Visual Studio

![](Images/AppIns3DebugSnapShot3.png)

![](Images/AppIns3DebugSnapShot4.png)

### Metrics Explorer

Metrics in Application Insights are measured values and counts of events that are sent in telemetry from your application. They help  detect performance issues and watch trends in how the application is being used. There's a wide range of standard metrics, and one can also create custom metrics and events.

Metrics and event counts are displayed in charts of aggregated values such as sums, averages, or counts.

Options available are documented here:

- <https://docs.microsoft.com/en-us/azure/azure-monitor/app/metrics-explorer>
- <https://docs.microsoft.com/en-us/azure/azure-monitor/app/api-custom-events-metrics>

![](Images/AppIns3Metrics1.png)

![](Images/AppIns3Metrics2.png)

### Search

Search is a feature of Application Insights that you use to find and explore individual telemetry items, such as page views, exceptions, or web requests.

Can filter on Event Types (e.g. Exceptions) or Properties (e.g. City = Dundee).  Can also filter on Time ranges.

![](Images/AppIns3Search1.png)

- Filter on Request & Exceptions
  - See the End to End Transaction

![](Images/AppIns3Search2.png)

![](Images/AppInsSearch3.png)

- Filter on Customer Event ... look for our Color page for Green events

![](Images/AppIns3Search4.png)

![](Images/AppIns3Search5.png)

![](Images/AppIns3Search6.png)

For more complex search queriesand reporting, we can use AppInsights Analytics ... covered in the next section (part 4).

---
[Home](appinsights-0.md) | [Prev](appinsights-2.md) | [Next](appinsights-4.md)
