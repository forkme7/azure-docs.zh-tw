---
title: Azure Service Fabric - 使用 Log Analytics 來監視效能 | Microsoft Docs
description: 了解如何設定 OMS 代理程式，以監視 Azure Service Fabric 叢集的容器和效能計數器。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 6e1c870458f43bcc5d6d40f0e40e2b2a95bee2af
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="performance-monitoring-with-log-analytics"></a>使用 Log Analytics 來監視效能

本文會逐步說明如何將 Log Analytics (又稱為 OMS 代理程式) 作為虛擬機器擴展集延伸模組，新增至您的叢集，並將它連接到現有 Azure Log Analytics 工作區。 如此即可收集容器、應用程式和效能監控的相關診斷資料。 透過將它新增為虛擬機器擴展集資源的延伸模組，Azure Resource Manager 可確保其本身在每個節點上安裝，即使在調整叢集規模時也是如此。

> [!NOTE]
> 本文假設您已設定好 Azure Log Analytics 工作區。 如果尚未完成，請前往[設定 Azure Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>透過 Azure CLI 新增代理程式延伸模組

要將 OMS 代理程式加入叢集，最佳方法是透過 Azure CLI 提供的虛擬機擴展集 API。 如果您尚未設定 Azure CLI，請前往 Azure 入口網站，並開啟 [Cloud Shell](../cloud-shell/overview.md) 執行個體，或[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 一旦要求了 Cloud Shell，請確定用於作業的訂用帳戶與資源的訂用帳戶相同。 請使用 `az account show` 進行檢查，並確保「名稱」值符合叢集訂用帳戶的名稱。

2. 在入口網站中，瀏覽至您的 Log Analytics 工作區所在的資源群組。 按一下 Log Analytics 資源 (資源類型會是 Log Analytics)。 當您在資源概觀頁面時，在左側功能表的 [設定] 區段下方，按一下 [進階設定]。

    ![Log Analytics 屬性頁面](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. 如果您要建立 Windows 叢集，請按一下 [Windows 伺服器]，如果您要建立 Linux 叢集，請按一下 [Linux 伺服器]。 此頁面會顯示您的 `workspace ID` 和 `workspace key` (列為入口網站中的主索引鍵)。 您在下一個步驟需要上述兩項。

4. 執行命令，以使用 Cloud Shell 中的 `vmss extension set` API，將 OMS 代理程式安裝到您的叢集：

    若為 Windows 叢集：
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    若為 Linux 叢集：

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    以下是將 OMS 代理程式新增至 Windows 叢集的範例。

    ![OMS 代理程式 cli 命令](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. 應該在 15 分鐘內即可成功地將代理程式新增至您的節點。 您可以使用 `az vmss extension list` API 驗證是否已新增代理程式：

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>透過 Resource Manager 範本新增代理程式

部署 Azure Log Analytics 工作區，並將代理程式新增至每個節點的範例 Resource Manager 範本可用於 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 或 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)。

您可以下載並修改此範本，以部署最適合您需求的叢集。

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>在 Log Analytics 入口網站中檢視效能計數器

現在您已新增 OMS 代理程式，請前往 Log Analytics 入口網站來選擇您要收集的效能計數器。 

1. 在 Azure 入口網站中，移至您建立 Service Fabric 分析解決方案所在的資源群組。 選取 **ServiceFabric\<nameOfOMSWorkspace\>** 並移至其概觀頁面。 在頂端，按一下連結來移至 OMS 入口網站。

2. 進入入口網站後，您會看到圖格並用圖形代表每一個啟用的解決方案，其中有一個是 Service Fabric 的解決方案。 按一下這個圖形來繼續 Service Fabric 分析解決方案。 

3. 現在您會在操作通道和 Reliable Services 事件上，看到一些圖格與圖形。 在右側按一下齒輪圖示來移至設定頁面。

    ![OMS 設定](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. 在設定頁面中，按一下 [資料] 並選擇 Windows 或 Linux 效能計數器。 您可以選擇啟用預設的效能計數器，也可以設定收集的間隔。 您也可以新增想收集的[其他效能計數器](service-fabric-diagnostics-event-generation-perf.md)。 [本文](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)參照適當的格式。

設定好計數器後，返回解決方案頁面，然後您很快就會在 [節點計量] 下方的圖形中看到資料滾動顯示。 您也可以使用 Kusto 查詢語言來查詢效能計數器資料 (與節點上的叢集事件和篩選條件類似)、效能計數器名稱以及值。 

![OMS 效能計數器查詢](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>後續步驟

* 收集相關[效能計數器](service-fabric-diagnostics-event-generation-perf.md)。 若要設定 OMS 代理程式以收集特定的效能計數器，請檢閱[設定資料來源](../log-analytics/log-analytics-data-sources.md#configuring-data-sources)。
* 設定 Log Analytics 以設定[自動化警示](../log-analytics/log-analytics-alerts.md)，來協助偵測與診斷
* 或者，您可以透過 [Azure 診斷延伸模組來收集效能計數器，並將它們傳送至 Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)