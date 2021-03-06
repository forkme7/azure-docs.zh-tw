---
title: Microsoft Azure 和 Azure 監視器中的傳統警示概觀 | Microsoft Docs
description: 警示可讓您監視 Azure 資源度量、事件或記錄檔，並在您所指定條件符合時收到通知。
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>什麼是 Microsoft Azure 中的傳統警示？

> [!NOTE]
> 本文說明如何建立舊版傳統計量警示。 「Azure 監視器」現已支援[新版的近乎即時計量警示](monitoring-overview-unified-alerts.md)
>

本文說明 Microsoft Azure 中的各種警示來源、這些警示的目的和優點，以及如何開始使用這些警示。 本文特別適用於「Azure 監視器」傳統警示。 警示是在 Azure 中進行監視的一種方法，可讓您對資料設定條件，並在最近的監視資料符合條件時收到通知。


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Azure 監視器傳統警示的分類法
Azure 使用下列詞彙來描述傳統警示及其功能：
* **警示** - 符合時會啟動之準則 (一或多個規則或條件) 的定義。
* **作用中** - 符合傳統警示所定義準則時的狀態。
* **已解決** - 先前符合傳統警示所定義的準則，但之後已不再符合時的狀態。
* **通知** - 根據變成作用中之傳統警示而採取的動作。
* **動作** - 傳送給通知接收者的特定呼叫 (例如以電子郵件傳送位址或張貼到 Webhook URL)。 通知通常可觸發多個動作。


## <a name="alerts-on-azure-monitor-data"></a>Azure 監視器資料的相關警示
Azure 監視器中可用資料的警示類型有三種：計量警示、近乎即時計量警示與活動記錄警示。

* **傳統計量警示**：當指定的計量值超出您指派的閾值時，就會觸發此警示。 當警示為「已啟動」時 (超出閾值且符合警示條件時)，以及當警示為「已解決」時 (再次超出閾值且不再符合條件時)，警示會產生通知。 這些是舊的計量警示。 如需較新的計量警示，請參閱下文。

* **近乎即時的計量警示** (新版警示體驗) - 與先前的計量警示相比，這些是新一代的計量警示，具有改良的功能。 這些警示可以 1 分鐘的頻率執行。它們也支援監視多個 (目前是兩個) 計量。  當警示「啟用」時 (每個計量的閾值同時超過且符合警示條件)，或當警示「解決」時 (當至少有一個計量再次超過閾值且已不符合條件)，警示都會產生通知。

* **傳統活動記錄警示** - 產生符合您所指派篩選準則之「活動記錄」事件時會觸發的資料流記錄警示。 這些警示只有「已啟動」這個狀態，因為警示引擎只會將篩選準則套用至任何新的事件。 您可以使用這些警示，在發生新的服務健康狀態事件時，或是在使用者或應用程式於您的訂用帳戶中執行作業時 (例如「刪除虛擬機器」)，收到通知。

針對透過 Azure 監視器提供的診斷記錄資料，建議將資料路由傳送至 Log Analytics 並使用 Log Analytics 警示。 下圖摘要說明 Azure 監視器中的資料來源，以及就概念而言如何發出該資料的警示。

![警示的說明](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>如何收到 Azure 監視器傳統警示的通知？
在過去，Azure 的警示來自不同的服務，各自使用其專屬的內建通知方法。 從現在開始，Azure 監視器提供可重複使用的通知群組，稱為動作群組。 動作群組會指定一組通知接收者 (任何數目的電子郵件地址、電話號碼 (簡訊) 或 Webhook URL)，每當啟動參考此動作群組的警示時，所有接收者都會收到該通知。 這可讓您在許多警示物件之間重複使用一組接收者 (例如您隨時待命的工程師清單)。 目前，只有活動記錄警示可以使用動作群組，但未來將有數個其他 Azure 警示類型也可以使用動作群組。

除了電子郵件地址和簡訊號碼，動作群組的通知支援還包括張貼到 Webhook URL。 如此即可啟用自動化和修復，例如使用：
    - Azure 自動化 Runbook
    - Azure Function
    - Azure 邏輯應用程式
    - 第三方服務

近乎即時計量警示與活動記錄警示都使用動作群組。

警示 (傳統) 下可用的舊計量警示不使用動作群組。 在個別度量警示上，您可以設定通知：
* 將電子郵件通知傳送至服務管理員、共同管理員或您指定的其他電子郵件。
* 呼叫 webhook，可讓您啟動其他自動化動作。

## <a name="next-steps"></a>後續步驟
使用下列項目取得有關警示規則和設定這些規則的資訊：

* 深入了解[計量](monitoring-overview-metrics.md)
* [透過 Azure 入口網站設定傳統的計量警示](insights-alerts-portal.md)
* 設定[傳統的計量警示 PowerShell](insights-alerts-powershell.md)
* 設定[傳統的計量警示命令列介面 (CLI)](insights-alerts-command-line-interface.md)
* 設定[傳統的計量警示 Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 深入了解[活動記錄](monitoring-overview-activity-logs.md)
* [透過 Azure 入口網站設定活動記錄警示](monitoring-activity-log-alerts.md)
* [透過 Resource Manager 設定活動記錄警示](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 檢閱[活動記錄警示 webhook 結構描述](monitoring-activity-log-alerts-webhook.md)
* 深入了解[較新的計量警示](monitoring-near-real-time-metric-alerts.md)
* 深入了解[服務通知](monitoring-service-notifications.md)
* 深入了解[動作群組](monitoring-action-groups.md)
* 設定[警示](monitor-alerts-unified-usage.md)
