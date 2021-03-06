---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/20/2018
---
|**SKU**   | **S2S/VNet-to-VNet<br>通道** | **P2S<br>連線** | **彙總<br>輸送量基準測試** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| 最大 30                         | 最大 128               | 650 Mbps                    |
|**VpnGw2**| 最大 30                         | 最大 128               | 1 Gbps                      |
|**VpnGw3**| 最大 30                         | 最大 128               | 1.25 Gbps                   |
|**基本** | 最大 10                         | 最大 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- 「彙總輸送量基準測試」是以透過單一閘道彙總之多個通道的量值為基礎。 由於網際網路流量條件和您的應用程式行為，這不是保證的輸送量。

- 在 [價格](https://azure.microsoft.com/pricing/details/vpn-gateway) 頁面上可以找到價格資訊。

- 可以在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 頁面上找到 SLA (服務等級協定) 資訊。

- 只有使用資源管理員部署模型的 VPN 閘道支援 VpnGw1、VpnGw2 和 VpnGw3。