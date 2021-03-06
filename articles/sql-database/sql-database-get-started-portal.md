---
title: Azure 入口網站：建立 SQL Database | Microsoft Docs
description: 在 Azure 入口網站中建立 SQL Database 邏輯伺服器、伺服器層級防火牆規則和資料庫，並對其進行查詢。
keywords: sql database 教學課程, 建立 sql database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.topic: quickstart
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: ea13126030bb7a2672dcd153b36f1d5d63623903
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure SQL Database

本快速入門教學課程將逐步解說如何使用[以 DTU 為基礎的購買模型](sql-database-service-tiers.md#vcore-based-purchasing-model-preview)在 Azure 中建立 SQL 資料庫。 Azure SQL Database 是可讓您在雲端中執行及調整高可用性 SQL Server 資料庫的「資料庫即服務」供應項目。 此快速入門說明如何使用 Azure 入口網站建立 SQL 資料庫以開始使用產品。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-sql-database"></a>建立 SQL 資料庫

Azure SQL Database 會使用一組定義的[計算和儲存體資源](sql-database-service-tiers.md)建立。 此資料庫建立於 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL Database 邏輯伺服器](sql-database-features.md)內。

請遵循下列步驟來建立包含 Adventure Works LT 範例資料的 SQL Database。

1. 按一下 Azure 入口網站左上角的 [建立資源]。

2. 從 [新增] 頁面中選取 [資料庫]，然後在 [新增] 頁面的 [SQL Database] 下選取 [建立]。

   ![建立資料庫-1](./media/sql-database-get-started-portal/create-database-1.png)

3. 使用下列資訊填寫 SQL Database 表單，如上圖所示︰   

   | 設定       | 建議的值 | 說明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **資料庫名稱** | mySampleDatabase | 如需有效的資料庫名稱，請參閱[資料庫識別碼](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)。 |
   | **訂用帳戶** | 您的訂用帳戶  | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組**  | myResourceGroup | 如需有效的資源群組名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 |
   | **選取來源** | 範例 (AdventureWorksLT) | 將 AdventureWorksLT 結構描述和資料載入至新的資料庫 |

   > [!IMPORTANT]
   > 您必須在此表單上選取範例資料庫，因為它會用於此快速入門的其餘部分。
   >

4. 在 [伺服器] 之下，按一下 [進行必要設定]，並使用下列資訊填妥 SQL Server (邏輯伺服器) 表單，如下圖所示︰   

   | 設定       | 建議的值 | 說明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **伺服器名稱** | 任何全域唯一名稱 | 如需有效的伺服器名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 |
   | **伺服器管理員登入** | 任何有效名稱 | 如需有效的登入名稱，請參閱[資料庫識別碼](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)。 |
   | **密碼** | 任何有效密碼 | 您的密碼至少要有 8 個字元，而且必須包含下列幾種字元的其中三種︰大寫字元、小寫字元、數字和非英數字元。 |
   | **訂用帳戶** | 您的訂用帳戶 | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組** | myResourceGroup | 如需有效的資源群組名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 |
   | **位置** | 任何有效位置 | 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |

   > [!IMPORTANT]
   > 必須要有您在此處指定的伺服器系統管理員登入和密碼，稍後在本快速入門中才能登入伺服器及其資料庫。 請記住或記錄此資訊，以供稍後使用。
   >  

   ![建立資料庫伺服器](./media/sql-database-get-started-portal/create-database-server.png)

5. 完成表單後，按一下 [選取]。

6. 按一下 [定價層] 可指定服務層、DTU 數目和儲存體數量。 瀏覽 DTU 數量的選項，以及可供您每個服務層使用的儲存體。

   > [!IMPORTANT]
   > \* 大於內含儲存體數量的儲存體大小尚在預覽中，而且會產生額外成本。 如需詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。
   >
   >\* 在進階層，目前於下列區域中提供超過 1 TB 的儲存體：澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、美國中部、法國中部、德國中部、日本東部、日本西部、韓國中部、美國中北部、北歐、美國中南部、東南亞、英國南部、英國西部、美國東部 2、美國西部、美國維吉尼亞州政府及西歐。 請參閱 [P11-P15 目前限制](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
   >

7. 在此快速入門教學課程中，選取 [標準] 服務層，然後使用滑桿選取 **10 DTU (S0)** 和 **1** GB 的儲存體。

   ![建立資料庫-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. 若要使用 [附加元件儲存體] 選項，請接受預覽條款。

   > [!IMPORTANT]
   > \* 大於內含儲存體數量的儲存體大小尚在預覽中，而且會產生額外成本。 如需詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。
   >
   >\* 在進階層，目前於下列區域中提供超過 1 TB 的儲存體：巴西南部、加拿大中部、加拿大東部、美國中部、法國中部、德國中部、日本東部、日本西部、韓國中部、美國中北部、北歐、美國中南部、東南亞、英國南部、英國西部、美國東部 2、美國西部、美國維吉尼亞州政府及西歐。 請參閱 [P11-P15 目前限制](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
   >

9. 在選取伺服器層、DTU 數目和儲存體數量之後，按一下 [套用]。  

10. 您現在已完成 SQL Database 表單，請按一下 [建立] 來佈建資料庫。 佈建需要幾分鐘的時間。

11. 在工具列上，按一下 [通知] 以監視部署程序。

     ![通知](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>建立伺服器層級防火牆規則

SQL Database 服務會在伺服器層級建立防火牆，防止外部應用程式和工具連線到伺服器或伺服器上的任何資料庫，除非建立防火牆規則以針對特定的 IP 位址開啟防火牆。 請遵循下列步驟來為您用戶端的 IP 位址建立 [SQL Database 伺服器層級防火牆規則](sql-database-firewall-configure.md)，並讓外部連線僅能夠穿過您 IP 位址的 SQL Database 防火牆。

> [!NOTE]
> SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若情況如此，除非 IT 部門開啟連接埠 1433，否則您無法連線至 Azure SQL Database 伺服器。
>

1. 部署完成之後，按一下左側功能表中的 [SQL Database]，然後按一下 [SQL Database] 頁面上的 [mySampleDatabase]。 資料庫的概觀頁面隨即開啟，其中會顯示完整伺服器名稱 (例如 **mynewserver-20170824.database.windows.net**)，並提供進一步的組態選項。

2. 在後續的快速入門教學課程中，請複製此完整伺服器名稱，以用來連線至您的伺服器及其資料庫。

   ![伺服器名稱](./media/sql-database-get-started-portal/server-name.png)

3. 如先前映像所示，按一下工具列上的 [設定伺服器防火牆]。 SQL Database 伺服器的 [防火牆設定] 頁面隨即開啟。

   ![伺服器防火牆規則](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. 按一下工具列上的 [新增用戶端 IP]，將目前的 IP 位址新增至新的防火牆規則。 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

5. 按一下 [檔案] 。 系統便會為目前的 IP 位址建立伺服器層級防火牆規則，以便在邏輯伺服器上開啟連接埠 1433。

6. 依序按一下 [確定]，然後關閉 [防火牆設定] 頁面。

您現在可以利用 SQL Server Management Studio 或選擇的其他工具，使用先前建立的伺服器管理帳戶從這個 IP 位址連線至 SQL Database 伺服器及其資料庫。

> [!IMPORTANT]
> 根據預設，已對所有 Azure 服務啟用透過 SQL Database 防火牆存取。 按一下此頁面上的 [關閉] 即可對所有 Azure 服務停用。
>

## <a name="query-the-sql-database"></a>查詢 SQL Database

您現在已在 Azure 中建立範例資料庫，讓我們使用 Azure 入口網站內建的查詢工具來確認您可以連線到資料庫並查詢資料。

1. 在資料庫的 [SQL Database] 頁面上，按一下左側功能表中的 [查詢編輯器 (預覽)]，然後按一下 [登入] 。

   ![登入](./media/sql-database-get-started-portal/query-editor-login.png)

2. 選取 SQL Server 驗證、提供必要的登入資訊，然後按一下 [確定] 進行登入。

3. 以 **ServerAdmin** 身分進行驗證後，在查詢編輯器窗格中輸入下列查詢。

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. 按一下 [執行]，然後在 [結果] 窗格中檢閱查詢結果。

   ![查詢編輯器結果](./media/sql-database-get-started-portal/query-editor-results.png)

5. 關閉 [查詢編輯器] 頁面，按一下 [確定] 以捨棄未儲存的編輯。

## <a name="clean-up-resources"></a>清除資源

如果您想移至[後續步驟](#next-steps)並了解如何使用各種不同方法來連線及查詢您的資料庫，請儲存這些資源。 不過，如果您要刪除在此快速入門中建立的資源，請使用下列步驟。


1. 從 Azure 入口網站的左側功能表中，依序按一下 [資源群組] 和 [myResourceGroup]。
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入 **myResourceGroup**，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

- 您現在具有資料庫，您可使用任何一個慣用工具或語言進行[連線和查詢](sql-database-connect-query.md)。 
- 若要了解如何設計您的第一個資料庫、建立資料表及插入資料，請參閱下列其中一個教學課程：
 - [使用 SSMS 設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database.md)
  - [設計 Azure SQL Database 並連接 C# 和 ADO.NET](sql-database-design-first-database-csharp.md)
