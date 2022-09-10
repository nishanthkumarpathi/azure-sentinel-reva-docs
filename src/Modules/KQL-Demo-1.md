# KQL Demo

All queries in this tutorial use the [Log Analytics demo environment](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Monitoring\_Logs/DemoLogsBlade). You can use your own environment, but you might not have some of the tables that are used here. Because the data in the demo environment isn't static, the results of your queries might vary slightly from the results shown here.

### Count rows <a href="#count-rows-1" id="count-rows-1"></a>

The [InsightsMetrics](https://docs.microsoft.com/en-us/azure/azure-monitor/reference/tables/insightsmetrics) table contains performance data that's collected by insights such as Azure Monitor for VMs and Azure Monitor for containers. To find out how large the table is, we'll pipe its content into an operator that counts rows.

A query is a data source (usually a table name), optionally followed by one or more pairs of the pipe character and some tabular operator. In this case, all records from the `InsightsMetrics` table are returned and then sent to the [count operator](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/countoperator). The `count` operator displays the results because the operator is the last command in the query.

KustoCopy

```kusto
InsightsMetrics | count
```

Here's the output:

| Count     |
| --------- |
| 1,263,191 |

### Filter by Boolean expression: _where_

The [AzureActivity](https://docs.microsoft.com/en-us/azure/azure-monitor/reference/tables/azureactivity) table has entries from the Azure activity log, which provides insight into subscription-level or management group-level events occuring in Azure. Let's see only `Critical` entries during a specific week.

The [where](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/whereoperator) operator is common in the Kusto Query Language. `where` filters a table to rows that match specific criteria. The following example uses multiple commands. First, the query retrieves all records for the table. Then, it filters the data for only records that are in the time range. Finally, it filters those results for only records that have a `Critical` level.

&#x20;Note

In addition to specifying a filter in your query by using the `TimeGenerated` column, you can specify the time range in Log Analytics. For more information, see [Log query scope and time range in Azure Monitor Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/scope).

KustoCopy

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

[![Screenshot that shows the results of the where operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-where-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-where-results.png#lightbox)

### Select a subset of columns: _project_

Use [project](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/projectoperator) to include only the columns you want. Building on the preceding example, let's limit the output to certain columns:

KustoCopy

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

[![Screenshot that shows the results of the project operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-project-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-project-results.png#lightbox)

### Show _n_ rows: _take_

[NetworkMonitoring](https://docs.microsoft.com/en-us/azure/azure-monitor/reference/tables/networkmonitoring) contains monitoring data for Azure virtual networks. Let's use the [take](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/takeoperator) operator to look at 10 random sample rows in that table. The [take](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/takeoperator) shows some rows from a table in no particular order:

KustoCopy

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![Screenshot that shows the results of the take operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-take-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-take-results.png#lightbox)

### Order results: _sort_, _top_

Instead of random records, we can return the latest five records by first sorting by time:

KustoCopy

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

You can get this exact behavior by instead using the [top](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/topoperator) operator:

KustoCopy

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![Screenshot that shows the results of the top operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-top-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-top-results.png#lightbox)

### Compute derived columns: _extend_

The [extend](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/projectoperator) operator is similar to [project](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/projectoperator), but it adds to the set of columns instead of replacing them. You can use both operators to create a new column based on a computation on each row.

The [Perf](https://docs.microsoft.com/en-us/azure/azure-monitor/reference/tables/perf) table has performance data that's collected from virtual machines that run the Log Analytics agent.

KustoCopy

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

[![Screenshot that shows the results of the extend operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-extend-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-extend-results.png#lightbox)

### Aggregate groups of rows: _summarize_

The [summarize](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/summarizeoperator) operator groups together rows that have the same values in the `by` clause. Then, it uses an aggregation function like `count` to combine each group in a single row. A range of [aggregation functions](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/aggregation-functions) are available. You can use several aggregation functions in one `summarize` operator to produce several computed columns.

The [SecurityEvent](https://docs.microsoft.com/en-us/azure/azure-monitor/reference/tables/securityevent) table contains security events like logons and processes that started on monitored computers. You can count how many events of each level occurred on each computer. In this example, a row is produced for each computer and level combination. A column contains the count of events.

KustoCopy

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

[![Screenshot that shows the results of the summarize count operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-summarize-count-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-summarize-count-results.png#lightbox)

### Summarize by scalar values

You can aggregate by scalar values like numbers and time values, but you should use the [bin()](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/binfunction) function to group rows into distinct sets of data. For example, if you aggregate by `TimeGenerated`, you'll get a row for most time values. Use `bin()` to consolidate values per hour or day.

The [InsightsMetrics](https://docs.microsoft.com/en-us/azure/azure-monitor/reference/tables/insightsmetrics) table contains performance data that's organized according to insights from Azure Monitor for VMs and Azure Monitor for containers. The following query shows the hourly average processor utilization for multiple computers:

KustoCopy

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

[![Screenshot that shows the results of the avg operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-summarize-avg-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-summarize-avg-results.png#lightbox)

### Display a chart or table: _render_

The [render](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) operator specifies how the output of the query is rendered. Log Analytics renders output as a table by default. You can select different chart types after you run the query. The `render` operator is useful to include in queries in which a specific chart type usually is preferred.

The following example shows the hourly average processor utilization for a single computer. It renders the output as a timechart.

KustoCopy

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

[![Screenshot that shows the results of the render operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-render-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-render-results.png#lightbox)

### Work with multiple series

If you use multiple values in a `summarize by` clause, the chart displays a separate series for each set of values:

KustoCopy

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

[![Screenshot that shows the results of the render operator with multiple series example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-render-multiple-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-render-multiple-results.png#lightbox)

### Join data from two tables

What if you need to retrieve data from two tables in a single query? You can use the [join](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) operator to combine rows from multiple tables in a single result set. Each table must have a column that has a matching value so that the join understands which rows to match.

[VMComputer](https://docs.microsoft.com/en-us/azure/azure-monitor/reference/tables/vmcomputer) is a table that Azure Monitor uses for VMs to store details about virtual machines that it monitors. [InsightsMetrics](https://docs.microsoft.com/en-us/azure/azure-monitor/reference/tables/insightsmetrics) contains performance data that's collected from those virtual machines. One value collected in _InsightsMetrics_ is available memory, but not the percentage memory that's available. To calculate the percentage, we need the physical memory for each virtual machine. That value is in `VMComputer`.

The following example query uses a join to perform this calculation. The [distinct](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/distinctoperator) operator is used with `VMComputer` because details are regularly collected from each computer. As result, the table contains multiple rows for each computer. The two tables are joined using the `Computer` column. A row is created in the resulting set that includes columns from both tables for each row in `InsightsMetrics`, where the value in `Computer` has the same value in the `Computer` column in `VMComputer`.

KustoCopy

```kusto
VMComputer
| distinct Computer, PhysicalMemoryMB
| join kind=inner (
    InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val
) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

[![Screenshot that shows the results of the join operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-join-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-join-results.png#lightbox)

### Assign a result to a variable: _let_

Use [let](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/letstatement) to make queries easier to read and manage. You can use this operator to assign the results of a query to a variable that you can use later. By using the `let` statement, the query in the preceding example can be rewritten as:

KustoCopy

```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
let AvailableMemory = InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

[![Screenshot that shows the results of the let operator example.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-let-results.png)](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/tutorial/azure-monitor-let-results.png#lightbox)





The following sections give examples of how to work with charts when using the Kusto Query Language.

#### Chart the results

Begin by reviewing the number of computers per operating system during the past hour:

KustoCopy

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

By default, the results display as a table:

![Screenshot that shows query results in a table.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/query-results-table.png)

For a more useful view, select **Chart**, and then select the **Pie** option to visualize the results:

![Screenshot that shows query results in a pie chart.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/query-results-pie-chart.png)

#### Timecharts

Show the average and the 50th and 95th percentiles of processor time in bins of one hour.

The following query generates multiple series. In the results, you can choose which series to show in the timechart.

KustoCopy

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Select the **Line** chart display option:

![Screenshot that shows a multiple-series line chart.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/multiple-series-line-chart.png)

**Reference line**

A reference line can help you easily identify whether the metric exceeded a specific threshold. To add a line to a chart, extend the dataset by adding a constant column:

KustoCopy

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Screenshot that shows a multiple-series line chart with a threshold reference line.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/multiple-series-threshold-line-chart.png)

#### Multiple dimensions

Multiple expressions in the `by` clause of `summarize` create multiple rows in the results. One row is created for each combination of values.

KustoCopy

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

When you view the results as a chart, the chart uses the first column from the `by` clause. The following example shows a stacked column chart that's created by using the `EventID` value. Dimensions must be of the `string` type. In this example, the `EventID` value is cast to `string`:

![Screenshot that shows a bar chart based on the EventID column.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/select-column-chart-type-eventid.png)

You can switch between columns by selecting the drop-down arrow for the column name:

![Screenshot that shows a bar chart based on AccountType column, with the column selector visible.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/select-column-chart-type-accounttype.png)

### Smart analytics

This section includes examples that use smart analytics functions in Azure Log Analytics to analyze user activity. You can use these examples to analyze your own applications that are monitored by Azure Application Insights, or use the concepts in these queries for similar analysis on other data.

#### Cohorts analytics

Cohort analysis tracks the activity of specific groups of users, known as _cohorts_. Cohort analytics attempts to measure how appealing a service is by measuring the rate of returning users. Users are grouped by the time they first used the service. When analyzing cohorts, we expect to find a decrease in activity over the first tracked periods. Each cohort is titled by the week its members were observed for the first time.

The following example analyzes the number of activities users completed during five weeks after their first use of the service:

KustoCopy

```kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user, we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity.
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time.
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after.
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample, we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```

Here's the output:

![Screenshot that shows a table of cohorts based on activity.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/cohorts-table.png)

#### Rolling monthly active users and user stickiness

The following example uses time-series analysis with the [series\_fir](https://docs.microsoft.com/en-us/azure/kusto/query/series-firfunction) function. You can use the `series_fir` function for sliding window computations. The sample application being monitored is an online store that tracks users' activity through custom events. The query tracks two types of user activities: `AddToCart` and `Checkout`. It defines an active user as a user who completed a checkout at least once on a specific day.

KustoCopy

```kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they completed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked out in our website.
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user.
| make-series UserClicks=count() default=0 on timestamp 
	in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column that contains a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin.
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day).
| mv-expand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity.
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// Render as timechart.
| render timechart
```

Here's the output:

![Screenshot of a chart that shows rolling active users by day over a month.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/rolling-monthly-active-users-chart.png)

The following example turns the preceding query into a reusable function. The example then uses the query to calculate rolling user stickiness. An active user in this query is defined as a user who completed a checkout at least once on a specific day.

KustoCopy

```kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
		in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mv-expand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

Here's the output:

![Screenshot of a chart that shows user stickiness over time.](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/images/samples/user-stickiness-chart.png)

#### Regression analysis

This example demonstrates how to create an automated detector for service disruptions based exclusively on an application's trace logs. The detector seeks abnormal, sudden increases in the relative amount of error and warning traces in the application.

Two techniques are used to evaluate the service status based on trace logs data:

* Use [make-series](https://docs.microsoft.com/en-us/azure/kusto/query/make-seriesoperator) to convert semi-structured textual trace logs into a metric that represents the ratio between positive and negative trace lines.
* Use [series\_fit\_2lines](https://docs.microsoft.com/en-us/azure/kusto/query/series-fit-2linesfunction) and [series\_fit\_line](https://docs.microsoft.com/en-us/azure/kusto/query/series-fit-linefunction) for advanced step-jump detection by using time-series analysis with a two-line linear regression.



```kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day.
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total.
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series.
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series.
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down.
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease).
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```
