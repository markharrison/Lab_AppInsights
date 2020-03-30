# Application Insights - Hands-on Lab Script - part 2

Mark Harrison : checked & updated 31 March 2020 - original 6 Aug 2018

![](Images/AppInsights.png)

- [Part 1 - Create AppInsights instance](appinsights-1.md)  
- [Part 2 - Develop and deploy AppInsights enabled webapp](appinsights-2.md) ... this document
- [Part 3 - Get insights on application](appinsights-3.md)
- [Part 4 - Advanced Analytics](appinsights-4.md)  
- [Part 5 - Availability Monitoring](appinsights-5.md)
- [Part 6 - Usage Behaviour Analysis](appinsights-6.md)
  
## Develop and Deploy AppInsights enabled webapp

### Create a .NET Core Web Application

- Use Visual Studio 2019 to create a .NET Core Web Application

![](Images/AppIns2VSProject1.png)

![](Images/AppIns2VSProject2.png)

![](Images/AppIns2VSproject3.png)

### Enable AppInsights

- Right click on project and select `Add` | `Application Insights Telemetry`

![](Images/AppIns2VSAddAI.png)

- There maybe a warning to update SDK - if so then accept
- Click `Get Started`  

![](Images/AppIns2VSAddAI2.png)

- Select the AppInsights instance we created earlier

![](Images/AppIns2VSAddAI3.png)

![](Images/APPins2VSAddAI4.png)

![](Images/APPins2VSAddAI5.png)

This process will insert some code into the WebApp to connect the app to the AppInsights instance and send the telemetry that is generated.

![](Images/APPins2VSAddAI6.png)

Also notice that the AppInsights IntrumentationKey has been added to the appsettings.json configuration file.

In real world development, different instrumentation keys should be used for dev / staging / production etc. to keep the telemetry separate across the different stages.  This can be done by setting the key in code from an environment variable.

- Amend the Key to match that used by the App Insights instance we created in part 1

![](Images/AppIns2Key.png)

```json
{
  "ApplicationInsights": {
    "InstrumentationKey": "007373e3-a964-48b3-a104-7d4e4f1d474f"
  }
}
```

Make sure the ApplicationInsights Nuget package is greater than 2.2.0 - upgrade if neccessary

![](Images/AppIns2Nuget1.png)

![](Images/AppIns2Nuget2.png)

Updated

![](Images/AppIns2Nuget3.png)

### Client Monitoring

The preceding steps are enough to start collecting server-side telemetry. Next we will enable collecting client usage telemetry - a JavaScript snippet must be added to our web pages.

In _ViewImports.cshtml, add injection:

```c#
@inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

In _Layout.cshtml, insert HtmlHelper at the end of the `<head>` section but before any other script. If you want to report any custom JavaScript telemetry from the page, inject it after this snippet:

```c#
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

![](Images/AppIns2ClientMonitoring.png)

When we run the application, and examine the source code - we can see the client monitoring javascript

![](Images/AppIns2ClientJS.png)

### Bad code

Add a new web page with some logic to the app to cause an exception - we will use this later.

![](Images/AppIns2BadCode1.png)

![](Images/AppIns2BadCode2.png)

![](Images/AppIns2BadCode3.png)

![](Images/AppIns2BadCode4.png)

Add the following code to send the telemetry on an exception to App Insights.  

Crash.cshtml

```c#
@page
@using Microsoft.ApplicationInsights
@model markharrisonweb.Pages.CrashModel
@{
    ViewData["Title"] = "Crash";

    string strError = "error";

    try
    {
        int x = 100;
        x = x / 0;
    }
    catch (Exception ex)
    {
        // Report the exception to Application Insights.
        Model.telemetryClient.TrackException(ex);

        strError = ex.ToString();
    }
}

<h1>Crash</h1>
@Html.Raw(strError)
```

![](Images/AppIns2BadCode5.png)

Also add the following code to the code-behind file

Crash.cshtml.cs

```c#
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.ApplicationInsights;

namespace markharrisonweb.Pages
{
    public class CrashModel : PageModel
    {
        public TelemetryClient telemetryClient;
        public CrashModel(TelemetryClient telemetry)
        {
            this.telemetryClient = telemetry;
        }

        public void OnGet()
        {

        }
    }
}
```

![](Images/AppIns2BadCode6.png)

### Call backend service

Next, add a new web page with some logic to call some backend API or database - we will use this later.

Below is example of how this could be done.

```c#
@page
@using System.Text;
@using System.Net;
@using System.IO;
@model markharrisonweb.Pages.RSSFeedModel
@{
    ViewData["Title"] = "RSSFeed";

    string RSSFeedURL = "https://feeds.feedburner.com/azure1news";
    int bufSize = 65536;
    int length;
    byte[] buf = new byte[bufSize];
    StringBuilder sb = new StringBuilder(bufSize);

    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(RSSFeedURL);
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();
    Stream responseStream = response.GetResponseStream();

    // Read response stream until end
    while ((length = responseStream.Read(buf, 0, buf.Length)) != 0)
    {
        sb.Append(Encoding.UTF8.GetString(buf, 0, length));
    }
}

<h1>RSSFeed</h1>

@Html.Raw(sb.ToString())
```

![](Images/AppIns2RSSFeed.png)

### Snapshot Debugging

Optional step needed for Snapshot Debugging

Add SnapshotCollector Nuget package

![](Images/AppIns2SnapshotNuget.png)

Add the following code to startup.cs

```c#
using Microsoft.ApplicationInsights.SnapshotCollector;
```

AAdd the following at the end of the ConfigureServices method in the Startup class in Startup.cs.

```c#
    services.AddSnapshotCollector((configuration) =>
        Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));

```

Add the following settings to the appsettings.json file

```json
{
  "ApplicationInsights": {
    "InstrumentationKey": "<your instrumentation key>"
  },
  "SnapshotCollectorConfiguration": {
    "IsEnabledInDeveloperMode": false,
    "ThresholdForSnapshotting": 1,
    "MaximumSnapshotsRequired": 3,
    "MaximumCollectionPlanSize": 50,
    "ReconnectInterval": "00:15:00",
    "ProblemCounterResetInterval":"1.00:00:00",
    "SnapshotsPerTenMinutesLimit": 1,
    "SnapshotsPerDayLimit": 30,
    "SnapshotInLowPriorityThread": true,
    "ProvideAnonymousTelemetry": true,
    "FailedRequestLimit": 3
  }
}

```

![](Images/AppIns2SnapshotCollector.png)

### Custom Events

We can instrument application code by augmenting the captured telemetry with custom events and custom metrics (continuous measurement).

This could also be used for behaviour monitoring, and identify what users do you your site.

Below is example of how this could be done.

The Web page will generate a random color and send the result to the AppInsights telemetry.

Color.cshtml

```c#
@page
@using Microsoft.ApplicationInsights
@model markharrisonweb.Pages.ColorModel
@{
    ViewData["Title"] = "Color";

    string[] strColors = { "red", "blue", "yellow", "green" };
    Random r = new Random();
    int rInt = r.Next(strColors.Length);
    string strRandColor = strColors[rInt];

    var properties = new Dictionary<string, string> { { "Color", strRandColor } };
    Model.telemetryClient.TrackEvent("ColorEvent", properties, null);
}

<h1>Color</h1>
<svg height="200" width="200">
    <circle cx="100" cy="100" r="95" stroke="black" stroke-width="3" fill="@Html.Raw(strRandColor)" />
</svg>
```

![](Images/AppIns2Color1.png)

Also add the following code to the code-behind file

Color.cshtml.cs

```c#
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.ApplicationInsights;

namespace markharrisonweb.Pages
{
    public class ColorModel : PageModel
    {
        public TelemetryClient telemetryClient;
        public ColorModel(TelemetryClient telemetry)
        {
            this.telemetryClient = telemetry;
        }

        public void OnGet()
        {

        }
    }
}
```

![](Images/AppIns2Color2.png)

### Deploy Application

Our application is now complete - lets deploy it.

- Publish application to Azure
  - Select the WebApp resource created earlier

  ![](Images/AppIns2Deploy1.png)

  ![](Images/AppIns2Deploy2.png)

  ![](Images/AppIns2Deploy3.png)

  ![](Images/AppIns2Deploy4.png)

  ![](Images/AppIns2Deploy5.png)

  ![](Images/AppIns2Deploy6.png)

- Once published, we can access our web application

  ![](Images/AppIns2Deploy7.png)

  ![](Images/AppIns2Deploy8.png)

### Test web site  

Put the website under a load.  The following PowerShell may be useful, amend to the WebApp Url

```PowerShell
for ($i = 0 ; $i -lt 100; $i++)
{
 Invoke-WebRequest -uri https://markharrisonapp.azurewebsites.net/
}
```

### AppInsights Overview

Check the AppInsights overview page.

Note the response times, server requests, failed requests.

![](Images/AppIns2AIBlade.png)

---
[Home](appinsights-0.md) | [Prev](appinsights-1.md) | [Next](appinsights-3.md)
