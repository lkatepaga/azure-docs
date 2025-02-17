---
title: Monitor Azure Web PubSub
description: Learn how to monitor Azure Web PubSub with Azure Monitor
author: wanlwanl
ms.author: wanl
ms.service: azure-web-pubsub
ms.topic: how-to 
ms.date: 05/15/2023
---

# Monitor Azure Web PubSub

When you have critical applications and business processes that rely on Azure resources, you want to monitor those resources for availability, performance, and operation. This article describes the monitoring data generated by Azure Web PubSub and how you can use the features of Azure Monitor to analyze and alert on this data.

## Monitor overview

The **Overview** page in the Azure portal for each Azure Web PubSub includes a brief view of the resource usage, such as concurrent connections and outbound traffic. This information is helpful. It's only a small amount of the monitoring data is available from this pane. Some of this data is collected automatically. It's available for analysis as soon as you create the resource. You can enable other types of data collection after some configuration.

## What is Azure Monitor?

Azure Web PubSub creates monitoring data using [Azure Monitor](../azure-monitor/overview.md). Monitor is a full stack monitoring service in Azure that provides a complete set of features to monitor your Azure resources in addition to resources in other clouds and on-premises.

If you're not already familiar with monitoring Azure services, start with [Monitoring Azure resources with Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), which describes the following concepts:

- What is Azure Monitor?
- Costs associated with monitoring
- Monitoring data collected in Azure
- Configuring data collection
- Standard tools in Azure for analyzing and alerting on monitoring data

The following sections build on this article. They describe the specific data gathered from Azure Web PubSub and provide examples for configuring data collection and analyzing this data with Azure tools.

## Monitoring data

Azure Web PubSub collects the same kinds of monitoring data as other Azure resources that are described in [Azure Monitor data collection](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data-from-azure-resources).

See [Monitor Azure Web PubSub data reference](howto-monitor-data-reference.md) for detailed information on the metrics and logs metrics created by Azure Web PubSub.

## Collection and routing

Platform metrics and the Activity log are collected and stored automatically, but can be routed to other locations by using a diagnostic setting.

Resource Logs aren't collected and stored until you create a diagnostic setting and route them to one or more locations.

See [Create diagnostic setting to collect platform logs and metrics in Azure](../azure-monitor/essentials/diagnostic-settings.md) for the detailed process for creating a diagnostic setting using the Azure portal, CLI, or PowerShell. When you create a diagnostic setting, you specify which categories of logs to collect.

The metrics and logs you can collect are discussed in the following sections.

## Analyzing metrics

You can analyze metrics for Azure Web PubSub with metrics from other Azure services using metrics explorer by opening **Metrics** from the **Azure Monitor** menu. See [Getting started with Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) for details on using this tool.

For a list of the platform metrics collected for Azure Web PubSub, see [Metrics](concept-metrics.md).

For reference, you can see a list of [all resource metrics supported in Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## Analyzing logs

Data in Azure Monitor Logs is stored in tables where each table has its own set of unique properties.

All resource logs in Azure Monitor have the same fields followed by service-specific fields. The common schema is outlined in [Azure Monitor resource log schema](../azure-monitor/essentials/resource-logs-schema.md). 

Azure Web PubSub collects three types of resource logs: *Connectivity*, *Messaging*, and *HTTP requests*.
- **Connectivity** logs provide detailed information for Azure Web PubSub hub connections. For example, basic information (user ID, connection ID, and so on) and event information (connect, disconnect, and so on).
- **Messaging** logs provide tracing information for the Azure Web PubSub hub messages received and sent via Azure Web PubSub service. For example, tracing ID and message type of the message.
- **HTTP requests** logs provide tracing information for HTTP requests to the Azure Web PubSub service. For example, HTTP method and status code. Typically the HTTP request is recorded when it arrives at or leave from service.

### How to enable resource logs

Currently Azure Web PubSub supports integration with [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

1. Go to Azure portal.
1. On **Diagnostic settings** page of your Azure Web PubSub service instance, select **+ Add diagnostic setting**.
    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-list.png" alt-text="Screenshot of viewing diagnostic settings and create a new one.":::

1. In **Diagnostic setting name**, input the setting name.
1. In **Category details**, select any log category you need.
1. In **Destination details**, check **Archive to a storage account**.

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-details.png" alt-text="Screenshot of configuring diagnostic setting detail.":::

1. Select **Save** to save the diagnostic setting.
> [!NOTE]
> The storage account should be in the same region as Azure Web PubSub service.

### Archive to an Azure Storage Account

Logs are stored in the storage account that's configured in the **Diagnostics setting** pane. A container named `insights-logs-<CATEGORY_NAME>` is created automatically to store resource logs. Inside the container, logs are stored in the file `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. The path is combined by `resource ID` and `Date Time`. The log files are split by `hour`. The minute value is always `m=00`.

### Archive to Azure Log Analytics

To send logs to a Log Analytics workspace:
1. On the **Diagnostic setting** page, under **Destination details**, select **Send to Log Analytics workspace.
1. Select the **Subscription** you want to use.
1. Select the **Log Analytics workspace** to use as the destination for the logs.

To view the resource logs, follow these steps:

1. Select `Logs` in your target Log Analytics.

    :::image type="content" alt-text="Screenshot showing Log Analytics menu item." source="./media/howto-troubleshoot-diagnostic-logs/log-analytics-menu-item.png" lightbox="./media/howto-troubleshoot-diagnostic-logs/log-analytics-menu-item.png":::


1. Enter `WebPubSubConnectivity`, `WebPubSubMessaging` or `WebPubSubHttpRequest`, and then select the time range to query the log. For advanced queries, see [Get started with Log Analytics in Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).

    :::image type="content" alt-text="Screenshot showing query log in Log Analytics." source="./media/howto-troubleshoot-diagnostic-logs/query-log-in-log-analytics.png" lightbox="./media/howto-troubleshoot-diagnostic-logs/query-log-in-log-analytics.png":::


To use a sample query for SignalR service, follow the steps below.
1. Select `Logs` in your target Log Analytics.
1. Select `Queries` to open query explorer.
1. Select `Resource type` to group sample queries in resource type.
1. Select `Run` to run the script.
    :::image type="content" alt-text="Screenshot showing sample query in Log Analytics." source="./media/howto-troubleshoot-diagnostic-logs/log-analytics-sample-query.png" lightbox="./media/howto-troubleshoot-diagnostic-logs/log-analytics-sample-query.png":::


## Alerts

Azure Monitor alerts proactively notify you when important conditions are found in your monitoring data. They allow you to identify and address issues in your system before your customers notice them. You can set alerts on [metrics](../azure-monitor/alerts/alerts-metric-overview.md), [logs](../azure-monitor/alerts/alerts-unified-log.md), and the [activity log](../azure-monitor/alerts/activity-log-alerts.md). Different types of alerts have benefits and drawbacks.

The following table lists common and recommended alert rules for Azure Web PubSub.

| Alert type | Condition | Examples  |
|:---|:---|:---|
| Metric | Connection | When number of connections exceed a set value|
| Metric | Outbound traffic | When number of messages exceed a set value|
| Activity Log | Create or update service | When service is created or updated|
| Activity Log | Delete service | When service is deleted|
| Activity Log | Restart service| When service is restarted|

## Next steps

For more information about monitoring Azure Functions, see the following articles:

* [Monitor Azure Web PubSub data reference](howto-monitor-data-reference.md) - reference of the metrics, logs, and other important values created by your function app.
* [Monitoring Azure resources with Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) - details monitoring Azure resources.