# Application Insights - Hands-on Lab Script - part 5

Mark Harrison : 6 Aug 2018

![](Images/AppInsights.png)

- [Part 1 - Create AppInsights instance](appinsights-1.md)  
- [Part 2 - Develop and deploy AppInsights enabled webapp](appinsights-2.md)
- [Part 3 - Get insights on application](appinsights-3.md)
- [Part 4 - Advanced Analytics](appinsights-4.md)  
- [Part 5 - Availability Monitoring](appinsights-5.md) ... this document
- [Part 6 - Usage Behaviour Analysis](appinsights-6.md)

## Availability Monitoring

### Overview

App Insights can use tests to monitor the web apps availability and responsiveness. IT will web requests to your application at regular intervals from points around the world.

### Define Test

- Select the `Availability` menu option

- Click on `Add Test`

![](Images/AppIns501.png)

- Define the test parameters, including:
  - Test Type: Ping
  - Specify URL of web app
  - Test frequency 5 minutes
  - Regions from where  the request will originate
  - Alerts - email address / web hook for error notifications
  
![](Images/AppIns502.png)

### Test results

After a period of time, the results of the tests will be displayed.  Green dots are successful - red dots are failures.

![](Images/AppIns503.png)

Clicking on a green/red dot will give detailed information.

![](Images/AppIns504.png)

![](Images/AppIns505.png)

---
[Home](appinsights-0.md) | [Prev](appinsights-4.md)  | [Next](appinsights-6.md)