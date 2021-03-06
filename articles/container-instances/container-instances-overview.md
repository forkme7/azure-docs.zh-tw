---
title: Azure Container Instances 概觀
description: 了解 Azure Container Instances
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 03/29/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: e10d4046f543fbcc479421f4ca8f10221abc806f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="azure-container-instances"></a>Azure Container Instances

容器即將成為封裝、部署及管理雲端應用程式的慣用方法。 Azure Container Instances 提供更簡單快速的方法，讓您無需管理任何虛擬機器或採用更高層級的服務，就能在 Azure 中執行容器。

對於可在隔離容器中運作的任何情節，包括簡單的應用程式、工作自動化及建置工作，Azure Container Instances 是很棒的解決方案。 對於需要完整容器協調流程的情節，包括跨多個容器的服務探索、自動縮放及協調性應用程式升級，我們建議使用 [Azure Container Service (AKS)](../aks/index.yml)。

## <a name="fast-startup-times"></a>快速啟動時間

容器提供比虛擬機器更多的啟動優點。 Azure Container Instances 可在幾秒內啟動 Azure 中的容器，而不需要佈建和管理 VM。

## <a name="public-ip-connectivity-and-dns-name"></a>公用 IP 連線能力和 DNS 名稱

Azure Container Instances 可讓您利用 IP 位址和完整網域名稱 (FQDN)，直接向網際網路公開您的容器。 當您建立容器執行個體時，您可以指定自訂的 DNS 名稱標籤，讓系統可以在 *customlabel*.*azureregion*.azurecontainer.io 找到您的應用程式。

## <a name="hypervisor-level-security"></a>Hypervisor 等級安全性

在過去，雖然容器提供應用程式相依性隔離和資源控管，但並沒有足夠的能力防範惡意的多租用戶使用方式。 Azure Container Instances 保證會將您的應用程式隔離在容器中，就像在 VM 中一樣。

## <a name="custom-sizes"></a>自訂大小

容器通常已最適合只執行單一應用程式，但這些應用程式的確切需求可能明顯不同。 Azure Container Instances 允許使用明確的 CPU 核心和記憶體規格，以提供最佳的使用率。 您可根據所需的數量付費並採用以秒計費方式，因此能夠根據實際的需求來微調您的費用。

## <a name="persistent-storage"></a>永續性儲存體

為了擷取和保存 Azure Container Instances 的狀態，我們支援直接[裝載 Azure 檔案共用](container-instances-mounting-azure-files-volume.md)。

## <a name="linux-and-windows-containers"></a>Linux 和 Windows 容器

Azure Container Instances 可讓您使用相同的 API 來排程 Windows 和 Linux 容器。 當您建立[容器群組](container-instances-container-groups.md)時，只須指定 OS 類型。

某些功能目前僅限於 Linux 容器。 雖然我們致力於將類似的功能帶入 Windows 容器，但是您可以在 [Azure Container Instances 配額和區域可用性](container-instances-quotas.md)中找到目前的平台差異。

## <a name="co-scheduled-groups"></a>共同排程的群組

Azure Container Instances 支援排程共用主機、區域網路、儲存體和生命週期的[多個容器群組](container-instances-container-groups.md)。 這可讓您合併主要應用程式容器與其他支援角色容器，例如記錄 Sidecar。

## <a name="next-steps"></a>後續步驟

使用我們的快速入門指南，嘗試使用單一命令將容器部署至 Azure。

> [!div class="nextstepaction"]
> [Azure Container Instances 快速入門](container-instances-quickstart.md)
