---
title: 將 SQL Server 執行個體遷移至 Azure SQL Database 受控執行個體 | Microsoft Docs
description: 了解如何將 SQL Server 執行個體遷移至 Azure SQL Database 受控執行個體。
keywords: database migration,sql server database migration,database migration tools,migrate database,migrate sql database,資料庫移轉,sql server 資料庫移轉,資料庫移轉工具,移轉資料庫,移轉 sql database
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: 5b8a2ec7e0401ac239acdefdd77a13b522f73960
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>將 SQL Server 遷移至 Azure SQL Database 受控執行個體

在本文中，您將了解用來將 SQL Server 2005 或更新版本執行個體遷移到 Azure SQL Database 受控執行個體 (預覽) 的方法。 

SQL Database 受控執行個體是現有 SQL Data 服務的擴充項目，提供單一資料庫與彈性集區以外的第三種部署選項。  其設計用來讓資料庫可隨即轉移至完全受控的 PaaS，而不必重新設計應用程式。 SQL Database 受控執行個體對內部部署 SQL Server 程式設計模型提供極高的相容性，並為大多數的 SQL Server 功能，提供立即可用的支援，同時隨附工具與服務。

整體而言，應用程式移轉程序看起來就如下圖所示：

![移轉程序](./media/sql-database-managed-instance-migration/migration-process.png)

- [評估受控執行個體的相容性](sql-database-managed-instance-migrate.md#assess-managed-instance-compatibility)
- [選擇應用程式連線選項](sql-database-managed-instance-migrate.md#choose-app-connectivity-option)
- [部署到最佳大小的受控執行個體](sql-database-managed-instance-migrate.md#deploy-to-an-optimally-sized-managed-instance)
- [選取移轉方法並進行移轉](sql-database-managed-instance-migrate.md#select-migration-method-and-migrate)
- [監視應用程式](sql-database-managed-instance-migrate.md#monitor-applications)

> [!NOTE]
> 若要將單一資料庫遷移至單一資料庫或彈性集區，請參閱[將 SQL Server 資料庫遷移至 Azure SQL Database](sql-database-cloud-migrate.md)。

## <a name="assess-managed-instance-compatibility"></a>評估受控執行個體的相容性

首先，判斷受控執行個體是否與您應用程式的資料庫需求相容。 受控執行個體旨在為大部分內部部署或虛擬機器上使用 SQL Server 的現有應用程式，提供簡單的隨即轉移。 不過，有時候您可能需要尚不支援的功能，而且實作因應措施的成本非常高。 

使用[資料移轉小幫手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview)，可偵測影響 Azure SQL Database 資料庫功能的潛在相容性問題。 DMA 尚不支援將受控執行個體作為移轉目的地，但建議您針對 Azure SQL Database 執行評估，並針對產品文件，仔細檢閱提報的功能同位和相容性問題清單。 大部分導致無法移轉至 Azure SQL Database 的執行問題，已透過受控執行個體加以解決。 例如跨資料庫查詢、相同執行個體內的跨資料庫交易、其他 SQL 來源的連結伺服器、CLR、全域暫存資料表、執行個體層級檢視、Service Broker 等功能皆可在受控執行個體中使用。 

不過，有些狀況下，您須考慮替代選項，例如 Azure 中的[「虛擬機器上的 SQL Server」](https://azure.microsoft.com/services/virtual-machines/sql-server/)。 這裡有一些範例：

- 如果您需要直接存取作業系統或檔案系統，例如在具有 SQL Server 的相同虛擬機器上安裝第三方或自訂代理程式。
- 如果您的執行個體與尚不支援的功能有緊密相依性，例如 FileStream / FileTable、PolyBase 及跨執行個體交易等功能。
- 如果您極必須維持在特定版本的 SQL Server (例如 2012 版)。
- 如果您的計算需求遠低於受控執行個體在公開預覽中提供的功能 (例如，只需要一個虛擬核心)，而且資料庫彙總不是可接受的選項。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到最佳大小的受控執行個體

受控執行個體專為打算移至雲端的內部工作負載量身訂做。 其引進新的採購模型，提供更大的彈性來選取適合您工作負載的正確資源層級。 在內部部署的環境中，您可能習慣使用實體核心數目來調整這些工作負載大小。 受控執行個體的新採購模型是以虛擬核心 (VCore) 為基礎，再個別加上額外儲存體與可用 IO。 相對於目前使用的內部部署方案，VCore 模型可讓您較簡單地了解雲端中的計算需求。 這個新模型可讓您在雲端中具有正確大小的目的地環境。

您可以在部署期間選取計算和儲存體資源，並在之後進行變更，而這不會導致應用程式產生停機時間。

![受控執行個體大小](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

若要了解如何建立 VNet 基礎結構和受控執行個體，請參閱[建立受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。

> [!IMPORTANT]
> 請務必讓您目的地 VNet 和子網路永遠符合[受控執行個體的 VNET 需求](sql-database-managed-instance-vnet-configuration.md#requirements)。 任何相容性問題都可能會讓您無法建立新的執行個體，或使用已經建立的執行個體。

## <a name="select-migration-method-and-migrate"></a>選取移轉方法並進行遷移

受控執行個體鎖定的是需要將大量資料庫從內部部署或 IaaS 資料庫實作移轉的使用者案例。 如果您需要隨即轉移定期使用執行個體層級和/或跨資料庫功能的應用程式後端，那麼這是最佳選擇。 如果這是您的情況，您可以將整個執行個體移動至 Azure 中對應的環境，而不需要重新建構您的應用程式。 

若要移動 SQL 執行個體，您需要謹慎規劃下列作業：

-   移轉必須共置的所有資料庫 (在相同執行個體上執行)
-   移轉您應用程式依賴的執行個體層級物件，包括登入、認證、SQL Agent 作業和運算子，以及伺服器層級觸發程序。 

受控執行個體是完全受控的服務，可讓您將一些固定的 DBA 活動委派至平台，因為這些活動已內建。 因此，某些執行個體層級的資料就不需要遷移，例如，定期備份的維護作業或 Alwayson 組態，因為已內建[高可用性](sql-database-high-availability.md)。

受控執行個體支援下列資料庫移轉選項 (這些是目前唯一支援的移轉方法)：

- Azure 資料庫移轉服務 - 幾乎零停機時間的移轉
- 從 URL 原生還原 - 從 SQL Server 使用原生備份且需要一些停機時間
- 使用 BACPAC 檔案進行遷移 - 從 SQL Server 或 SQL Database 使用 BACPAC 檔案且需要一些停機時間

### <a name="azure-database-migration-service"></a>Azure 資料庫移轉服務

[Azure 資料庫移轉服務 (DMS)](../dms/dms-overview.md) 是一個完全受控的服務，能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。 此服務可簡化將現有第三方和 SQL Server 資料庫移動至 Azure 時所需的工作。 公開預覽中的部署選項包括 Azure SQL Database、受控執行個體，以及 Azure 虛擬機器中的 SQL Server。 DMS 是移轉企業工作負載的建議方法。 

若要深入了解 DMS 的此案例和組態步驟，請參閱[使用 DMS 將內部部署資料庫遷移至受控執行個體](../dms/tutorial-sql-server-to-managed-instance.md)。  

### <a name="native-restore-from-url"></a>從 URL 原生還原

從 SQL Server 內部部署環境或[虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 產生的原生備份 (.bak 檔案) (可從 [Azure 儲存體](https://azure.microsoft.com/services/storage/)取得) 進行還原，是 SQL DB 受控執行個體上的重要功能之一，讓您可以快速且輕鬆地進行離線資料庫移轉。 

下圖概略說明此程序：

![移轉流程](./media/sql-database-managed-instance-migration/migration-flow.png)

下表詳述根據您執行的來源 SQL Server 版本，您可以使用的方法：

|步驟|SQL 引擎和版本|備份 / 還原方法|
|---|---|---|
|將備份放至 Azure 儲存體|SQL 2012 SP1 CU2 之前的版本|直接將 .bak 檔案上傳到 Azure 儲存體|
||2012 SP1 CU2 - 2016|使用已被取代的 [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 語法直接備份|
||2016 和更新版本|使用 [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 直接備份|
|從 Azure 儲存體還原至受控執行個體|[使用 SAS 認證從 URL 還原](sql-database-managed-instance-restore-from-backup-tutorial.md)|

> [!IMPORTANT]
> 不支援系統資料庫還原。 若要移轉執行個體層級物件 (儲存在 master 或 msdb 資料庫中)，我們建議透過指令碼來找出這些物件，並在目的地執行個體上執行 T-SQL 指令碼。

如需包含使用 SAS 認證將資料庫備份還原至受控執行個體的完整教學課程，請參閱[從備份還原至受控執行個體](sql-database-managed-instance-restore-from-backup-tutorial.md)。

### <a name="migrate-using-bacpac-file"></a>使用 BACPAC 檔案進行遷移

您可以在 BACPAC 檔案中建立原始資料庫的副本 (包含資料)，並從此 BACPAC 檔案匯入 Azure SQL Database 和受控執行個體。 請參閱[將 BACPAC 檔案匯入到新的 Azure SQL Database](sql-database-import.md)。

## <a name="monitor-applications"></a>監視應用程式

追蹤移轉之後的應用程式行為和效能。 在受控執行個體中，某些變更只會在[變更資料庫相容性層級](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)時啟用。 在大部分的情況中，資料庫移轉到 Azure SQL Database 時會留其原始的相容性層級。 如果使用者資料庫在移轉之前的相容性層級為 100 或以上，則移轉後相容性層級維持不變。 如果使用者資料庫在移轉之前的相容性層級為 90，那在升級後的資料庫中，相容性層級會設定為 100，這是受控執行個體中能支援的最低相容性層級。 系統資料庫的相容性層級是 140。

若要減少移轉的風險，請只在進行效能監視後變更資料庫相容性層級。 在資料庫相容性層級變更之前和之後，請使用查詢資料存放區作為取得工作負載效能相關資訊的最佳工具，如[在升級到較新 SQL Server 版本期間保持效能穩定性](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)中所述。

如果您已在完全受控的平台上，請充分運用屬於 SQL Database 服務的功能。 比方說，您不需要在受控執行個體上建立備份 (服務會自動為您執行備份)。 您無法再擔心如何排程、使用及管理備份。 受控執行個體使用[時間點復原 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)，讓您能夠還原到此保留期限內的任何時間點。 公用預覽期間，保留期限固定為七天。
此外，您不需要擔心如何設定為高可用性，因為[高可用性](sql-database-high-availability.md)已內建。

若要加強安全性，請考量使用一些可用功能：
- 資料庫層級的 Azure Active Directory 驗證
- 監視活動所用的稽核與威脅偵測
- 控制對於敏感和特殊權限資料 ([資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)和[動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)) 的存取權。

## <a name="next-steps"></a>後續步驟

- 如需受控執行個體的詳細資訊，請參閱[受控執行個體是什麼？](sql-database-managed-instance.md)
- 如需包含從備份進行還原的教學課程，請參閱[建立受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。
- 如需示範使用 DMS 進行移轉的教學課程，請參閱[使用 DMS 將內部部署資料庫遷移到受控執行個體](../dms/tutorial-sql-server-to-managed-instance.md)。  
