---
title: 使用 REST API 存取 Azure Mobile Engagement 服務 API
description: 描述如何使用 Mobile Engagement REST API 存取 Azure Mobile Engagement 服務 API
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: ea7fe6a58d8173c410573e3aa978cb1975ab5da7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>使用 REST 存取 Azure Mobile Engagement 服務 API
> [!IMPORTANT]
> Azure Mobile Engagement 將於 2018 年 3 月 31 日停止服務。 此頁面將於不久之後刪除。
> 

Azure Mobile Engagement 提供 [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx)，讓您可以管理裝置、觸達/推送活動等。

> [!NOTE]
> Azure Mobile Engagement 服務將於 2018 年 3 月淘汰，目前僅供現有客戶使用。 如需詳細資訊，請參閱 [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/)。

如果您不想直接使用 REST API，我們也提供 [Swagger 檔案](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) ，可以與工具搭配使用來針對您慣用的語言產生 SDK。 我們建議使用 [AutoRest](https://github.com/Azure/AutoRest) 工具從我們的 Swagger 檔案產生 SDK。 我們已經以類似的方式建立 .NET SDK，可讓您使用 C# 包裝函式與這些 API 互動，且您不需要自行執行驗證權杖交涉和重新整理。 請參閱[服務 API .NET SDK 範例](mobile-engagement-dotnet-sdk-service-api.md)，以了解如何使用 .NET SDK for API。
