---
title: Hyper-V 複寫至 Azure 的支援矩陣 | Microsoft Docs
description: 摘要說明使用 Azure Site recovery 將 Hyper-V 複寫至 Azure 的支援元件和需求
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: 9078b56ae9c5582aa21201c168974eb6cc7a4ed6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Hyper-V 複寫至 Azure 的支援矩陣


本文摘要說明將內部部署 Hyper-V VM 災害復原至 Azure (使用 [Azure Site Recovery](site-recovery-overview.md)) 所支援的元件和設定。


## <a name="supported-scenarios"></a>支援的案例

**案例** | **詳細資料**
--- | --- 
Hyper-V (含 Virtual Machine Manager) | 您可以針對在 System Center Virtual Machine Manager 網狀架構中管理的 Hyper-V 主機上執行之 VM 執行災害復原至 Azure。<br/><br/> 您可以在 Azure 入口網站中或使用 PowerShell 來部署此案例。<br/><br/> 當 Hyper-V 主機受 Virtual Machine Manager 管理時，您也可以執行災害復原至次要內部部署網站。 若要深入了解此案例，請閱讀[本教學課程](tutorial-vmm-to-vmm.md)。
Hyper-V (不含 Virtual Machine Manager) | 您可以針對在不是由 Virtual Machine Manager 管理的 Hyper-V 主機上執行之 VM 執行災害復原至 Azure。<br/><br/> 您可以在 Azure 入口網站中或使用 PowerShell 來部署此案例。 


## <a name="on-premises-servers"></a>內部部署伺服器

**伺服器** | **需求** | **詳細資料**
--- | --- | ---
Hyper-V (執行時不含 Virtual Machine Manager) | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2 | 當您在 Site Recovery 中設定 Hyper-V 站台時，不支援混用執行 Windows Server 2016 和 2012 R2 的主機。<br/><br/> 對於位在執行 Windows Server 2016 之主機上的 VM，不支援復原到替代位置。
Hyper-V (執行時含 Virtual Machine Manager) | Virtual Machine Manager 2016、Virtual Machine Manager 2012 R2 | 使用 Virtual Machine Manager 時，Windows Server 2016 主機應在 Virtual Machine Manager 2016 中管理。<br/><br/> 目前不支援混用 Windows Server 2016 和 2012 R2 上執行之 Hyper-V 主機的 Virtual Machine Manager 雲端。<br/><br/> 不支援包含將現有 Virtual Machine Manager 2012 R2 伺服器升級至 2016 的環境。


## <a name="replicated-vms"></a>複寫 VM


下表摘要說明 VM 支援。 Site Recovery 支援在受支援作業系統上執行的任何工作負載。 

 **元件** | **詳細資料**
--- | ---
VM 設定 | 複寫到 Azure 的 VM 必須符合 [Azure 需求](#failed-over-azure-vm-requirements)。
客體作業系統 | [Azure 支援的](https://technet.microsoft.com/library/cc794868.aspx) \(英文\) 的任何客體 OS。<br/><br/> 不支援 Windows Server 2016 Nano Server。




## <a name="hyper-v-network-configuration"></a>Hyper-V 網路組態

**元件** | **Hyper-V (有 Virtual Machine Manager)** | **Hyper-V (不含 Virtual Machine Manager)**
--- | --- | ---
主機網路：NIC 小組 | yes
主機網路：VLAN | yes
主機網路：IPv4 | yes
主機網路：IPv6 | 否
客體 VM 網路：NIC 小組 | 否
客體 VM 網路：IPv4 | yes
客體 VM 網路：IPv6 | 否
客體 VM 網路：靜態 IP (Windows) | yes
客體 VM 網路：靜態 IP (Linux) | 否
客體 VM 網路：多重 NIC | yes



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM 網路組態 (容錯移轉後)

**元件** | **Hyper-V (有 Virtual Machine Manager)** | **Hyper-V (不含 Virtual Machine Manager)**
--- | --- | ---
Azure ExpressRoute | yes | yes
ILB | yes | yes
ELB | yes | yes
Azure 流量管理員 | yes | yes
多個 NIC | yes | yes
保留的 IP | yes | yes
IPv4 | yes | yes
保留來源 IP 位址 | yes | yes
Azure 虛擬網路服務端點<br/><br/> (Azure 儲存體防火牆和虛擬網路) | 否 | 否


## <a name="hyper-v-host-storage"></a>Hyper-V 主機儲存體

**儲存體** | **Hyper-V (有 Virtual Machine Manager)** | **Hyper-V (不含 Virtual Machine Manager)**
--- | --- | --- | ---
NFS | NA | NA
SMB 3.0 | yes | yes
SAN (ISCSI) | yes | yes
多重路徑 (MPIO)。 測試工具：<br></br> Microsoft DSM、EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM for CLARiiON | yes | yes

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM 客體儲存體

**儲存體** | **Hyper-V (有 Virtual Machine Manager)** | **Hyper-V (不含 Virtual Machine Manager)**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | yes | yes
第 2 代 VM | yes | yes
EFI/UEFI| yes | yes
共用叢集磁碟 | 否 | 否
已加密磁碟 | 否 | 否
NFS | NA | NA
SMB 3.0 | 否 | 否
RDM | NA | NA
磁碟 > 1 TB | 是，最多 4,095 GB | 是，最多 4,095 GB
磁碟：4k 邏輯與實體磁區 | 不支援：第 1 代/第 2 代 | 不支援：第 1 代/第 2 代
磁碟：4K 邏輯與 512 位元組實體磁區 | yes |  yes
使用等量磁碟的磁碟區 > 1 TB<br/><br/> 邏輯磁碟區管理 (LVM) | yes | yes
儲存空間 | yes | yes
熱新增/移除磁碟 | 否 | 否
排除磁碟 | yes | yes
多重路徑 (MPIO) | yes | yes

## <a name="azure-storage"></a>Azure 儲存體

**元件** | **Hyper-V (有 Virtual Machine Manager)** | **Hyper-V (不含 Virtual Machine Manager)**
--- | --- | ---
本地備援儲存體 | yes | yes
異地備援儲存體 | yes | yes
讀取權限異地備援儲存體 | yes | yes
非經常性儲存體 | 否 | 否
經常性存取儲存體| 否 | 否
區塊 Blob | 否 | 否
待用加密 (SSE)| yes | yes
進階儲存體 | yes | yes
匯入/匯出服務 | 否 | 否
Azure 虛擬網路服務端點 (Azure 儲存體防火牆和虛擬網路)，在目標上快取用於儲存複寫資料的儲存體帳戶 | 否 | 否


## <a name="azure-compute-features"></a>Azure 計算功能

**功能** | **Hyper-V (有 Virtual Machine Manager)** | **Hyper-V (不含 Virtual Machine Manager)**
--- | --- | ---
可用性設定組 | yes | yes
中樞 | yes | yes  
受控磁碟 | 是，適用於容錯移轉。<br/><br/> 不支援受控磁碟的容錯回復。 | 是，適用於容錯移轉。<br/><br/> 不支援受控磁碟的容錯回復。

## <a name="azure-vm-requirements"></a>Azure VM 需求

您複寫到 Azure 的內部部署 VM 必須符合此表中摘要說明的 VM 需求。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | Site Recovery 支援 [Azure 支援](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有作業系統。  | 若不支援，則必要條件檢查會失敗。
客體作業系統架構 | 64 位元 | 若不支援，則必要條件檢查會失敗。
作業系統磁碟大小 | 第 1 代 VM 高達 2,048 G。<br/><br/> 第 2 代 VM 高達 300 GB。  | 若不支援，則必要條件檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則必要條件檢查會失敗。
資料磁碟計數 | 16 或以下  | 若不支援，則必要條件檢查會失敗。
資料磁碟 VHD 大小 | 最多 4,095 GB | 若不支援，則必要條件檢查會失敗。
網路介面卡 | 支援多個介面卡 |
共用 VHD | 不支援 | 若不支援，則必要條件檢查會失敗。
FC 磁碟 | 不支援 | 若不支援，則必要條件檢查會失敗。
硬碟格式 | VHD  <br/><br/> VHDX | Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。 當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
BitLocker | 不支援 | 為 VM 啟用複寫之前必須先停用 BitLocker。
VM 名稱 | 介於 1 到 63 個字元。 只能使用字母、數字和連字號。 VM 名稱必須以字母或數字為開頭或結尾。 | 更新 Site Recovery 中 VM 屬性的值。
VM 類型 | 第 1 代<br/><br/> 第 2 代--Windows | OS 磁碟基本類型的第 2 代 VM (其中包含一或兩個格式化為 VHDX 的資料磁碟區) 且支援小於 300 GB 的磁碟空間。<br></br>不支援 Linux 第 2 代 VM。 [深入了解](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。|

## <a name="recovery-services-vault-actions"></a>復原服務保存庫動作

**Action** |  **Hyper-V (有 Virtual Machine Manager)** | **Hyper-V (不含 Virtual Machine Manager)**
--- | --- | --- 
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否 
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否 


## <a name="provider-and-agent"></a>Provider 和代理程式

若要確定您的部署與本文中的設定相容，請確定您所執行的是最新的提供者和代理程式版本。

**名稱** | **說明** | **詳細資料**
--- | --- | --- | --- | ---
Azure Site Recovery 提供者 | 協調內部部署伺服器與 Azure 之間的通訊 <br/><br/> HYPER-V (含 Virtual Machine Manager)：安裝在 Virtual Machine Manager 伺服器上<br/><br/> HYPER-V (不含 Virtual Machine Manager)：安裝在 HYPER-V 主機上| 最新版本：5.1.2700.1 (可從 Azure 入口網站取得)<br/><br/> [最新功能和修正](https://aka.ms/latest_asr_updates)
Microsoft Azure 復原服務代理程式 | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/> 安裝在內部部署 Hyper-V 伺服器上 (無論是否有 Virtual Machine Manager) | 最新的代理程式可從入口網站取得






## <a name="next-steps"></a>後續步驟
了解如何[準備 Azure](tutorial-prepare-azure.md) 的內部部署 Hyper-V VM 災害復原。 
