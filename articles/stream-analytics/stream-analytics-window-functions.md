---
title: Azure 串流分析視窗化函式簡介
description: 本文說明三個用於 Azure 串流分析作業中的視窗化函式 (輪轉、跳動、滑動)。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: c6f5dbe49cb60e3c7b2bc6562acf2d7fd79096ec
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-stream-analytics-window-functions"></a>串流分析時間範圍函式簡介
在許多即時資串流案例中，只必須對暫時時間範圍中內含的資料執行作業。 時間範圍函式的原生支援是 Azure 串流分析的重要功能，可對撰寫複雜串流處理作業中的開發人員產能造成重大影響。 串流分析可讓開發人員使用[**輪轉**](https://msdn.microsoft.com/library/dn835055.aspx)、[**跳動**](https://msdn.microsoft.com/library/dn835041.aspx)和[**滑動**](https://msdn.microsoft.com/library/dn835051.aspx)時間範圍對串流資料執行暫時作業。 值得注意的是，所有 [時間範圍](https://msdn.microsoft.com/library/dn835019.aspx) 作業都會在時間範圍 **結束** 時輸出結果。 時間範圍的輸出會是以使用的彙總函式為基礎的單一事件。 此事件會有時間範圍結束的時間戳記，所有時間範圍函式都是以固定長度定義。 最後務必注意，所有時間範圍函式都應使用於 [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) 子句中。

![串流分析時間範圍函式概念](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>輪轉時間範圍
輪轉時間範圍函式用於將資料串流分成不同的時間區段並對其執行函式，如下列範例所示。 輪轉時間範圍的主要差異在於它們會重複，不會重疊，而事件不能屬於一個以上的輪轉時間範圍。

![串流分析時間範圍函式輪轉簡介](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>跳動時間範圍
跳動時間範圍函式會在一段固定的時間向前跳動。 簡單將這類函式視為可以重疊的輪轉時間範圍，因此事件可以屬於一個以上的跳動時間範圍結果集。 若要讓跳動時間範圍與輪轉時間範圍一樣，您只要將躍點大小指定成與時間範圍大小相同。 

![串流分析時間範圍函式跳動簡介](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>滑動時間範圍
不同於輪轉或跳動時間範圍，滑動時間範圍函式**只**會在事件發生時產生輸出。 每個時間範圍都至少會有一個事件，而且時間範圍會持續依據 € (epsilon) 向前移動。 如同跳動時間範圍，事件可以屬於一個以上的滑動時間範圍。

![串流分析時間範圍函式滑動簡介](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>取得使用時間範圍函式的說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

