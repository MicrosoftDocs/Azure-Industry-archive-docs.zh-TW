---
title: 銀行業中的資料管理概觀
author: dstarr
ms.author: dastarr
ms.date: 10/30/2019
ms.topic: article
ms.service: industry
description: 描述在受規範的銀行業環境中使用 Microsoft Azure 管理資料的技術。
ms.openlocfilehash: 1314054018c04e45b6450604febbf0142ead380d
ms.sourcegitcommit: f42a60539bec2a7769b42b6574f09eed4d1b6c79
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750540"
---
# <a name="data-management-in-banking-overview"></a>銀行業中的資料管理概觀

## <a name="introduction"></a>簡介

現今的銀行負責在其防火牆內保護和儲存大量寶貴資訊，包括客戶和多變的金融市場資訊。 在許多情況下並未使用該資訊，因為不易存取或搜尋，即使使用資料可改善跨多個銀行活動的決策也一樣。

有了這些資料，銀行可以更快找到誰的貸款可能違約或決定需要哪些市場投資組合評估調整的相關資訊。 銀行還可以更清楚了解其資料的儲存和管理方式，以滿足規範需求，從而可以運用、保留、封存或刪除資料以符合該需求。

為了滿足每日的銀行業務需求，必須完成上千個大小決策，使資料變得日益重要。 不僅如此，有鑑於嚴格的規範需求和防範金融犯罪義務，銀行必須有能力稽核任何資料分析過程的結果，一路追溯到存進資料存放庫的初始資訊。 可追蹤性要求從擷取到產生可操作性資料的透明度。

為了管理銀行服務的許多帳戶或業務，他們必須以快速又符合成本效益的方式了解所有這些資料。 隨著銀行數位化成熟，資料量和以新方式運用這些資料的機會呈指數方式增長，使銀行能夠尋求新的業務模式和以客戶為中心的商機領域。

具備適當的資料儲存策略，是提高營運效率、良好應用程式效能，以及法規合規性的關鍵。 資料儲存策略也是將資料轉換為可用於商業智慧和實用深入解析的格式的最初關鍵。

資料管理的常見模式如下：

![資料管理流程](./assets/data-mgmt-in-banking-overview/data-mgmt-00.png)

在此模型中，「資料服務」會描述任何轉換、資料連結，或封存以外的任何其他資料作業。 這是運用資料以協助做出更明智決策所需的關鍵活動。

所有的銀行與金融機構都在擷取、移動和儲存資料。 本文著重於將資料送進 Azure 中並從傳統內部部署資料存放區移出、處理、封存和刪除。 藉由將資料移至 Azure，銀行及金融機構可以充分利用基本優點，包括：

- 透過有效又無限的全球規模控制成本，僅在需要時使用計算資源和資料的容量。

- 透過淘汰實體伺服器內部部署，來降低資本支出和管理成本。

- 整合式的備份和災害復原，可降低資料保護的成本和複雜性。

- 將非經常儲存性資料封存至低成本的儲存體，同時仍能確保滿足合規性需求。

- 存取進階和整合式資料服務來處理資料，以便進行學習、預測、轉換或其他需求。

本文章提供建議的技術，以確保資料有效率地輸入 Azure，以及日後當資料移到雲端時要使用的基本資料管理技術。

## <a name="data-ingest"></a>資料擷取

金融機構將會擁有已收集的資料和目前應用程式正在使用的資料。 要將此資料移至 Azure 有數個選項。 在許多情況下，現有應用程式可以連線到 Azure 中的資料，就如同資料是內部部署一樣，幾乎不需要變更這些現有的應用程式。 使用 Microsoft [Azure SQL Database](/azure/sql-database/?WT.mc_id=bankdm-docs-dastarr) 時尤其如此，但是透過 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/databases?WT.mc_id=bankdm-docs-dastarr) 可以找到適用於 Oracle、TeraData MongoDB 等的解決方案。

資料移轉策略有許多種，可用來將資料從內部部署移至 Azure，並具有不同程度的延遲。 下面所參照的所有技術都提供了資料透明度和可靠的安全性。

### <a name="virtual-network-vnet-service-endpoints"></a>虛擬網路 (VNet) 服務端點

處理客戶的財務資訊時，安全性是最主要的考量。
Azure 中資源 (例如資料庫) 的保護通常取決於 Azure 內網路基礎結構本身的設定，然後透過特定端點存取該網路。

在將資料傳送到 Azure 之前，考慮保護 Azure 資源和從內部部署連接到它們的連線的網絡拓撲非常有用。
[虛擬網路 (VNet?WT.mc_id=bankdm-docs-dastarr) 服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview?WT.mc_id=bankdm-docs-dastarr) 提供連接到 Azure 定義 VNet 的安全連線。

VNet 在 Azure 資中定義為包含繫結 Vnet 內的 Azure 資源。 然後，該 VNet 的端點可以安全存取您重要的 Azure 服務資源，並且只能存取已定義 VNet 上的服務資源。

## <a name="database-lift-and-shift"></a>資料庫平移

資料庫移轉的「平移」模型是使用 Azure SQL Database 中最常見的案例。 平移只是表示取出現有的內部部署資料庫，然後將其直接移至雲端。 這樣做的原因包括下列各項：

- 從目前價格較高或有其他操作原因的資料中心移出
- 目前的內部部署 SQL Server 資料庫硬體即將過期或即將結束生命週期
- 為了支援公司的「移至雲端」一般策略
- 善用 SQL Azure 的可用性和災害復原功能

對於較小的資料庫，擷取資料的第一步驟通常是透過 Azure 入口網站、Azure CLI 或 Azure SDK 建立所需的資料存放區和結構 (例如資料表)。 針對這些較小的資料存放區，後續步驟可由撰寫的自訂應用程式執行，將正確資料複製到適當的 Azure 資料儲存體。 對於較大的資料移轉，在 Azure 中還原備份通常是最快的途徑。

將資料快速又安全地傳輸至 Azure 的方法有許多種。 [請參閱這篇文章](/azure/architecture/data-guide/scenarios/data-transfer?WT.mc_id=bankdm-docs-dastarr)了解某些標準技術的優缺點。

### <a name="azure-database-migration-service"></a>Azure 資料庫移轉服務

在平移 SQL Server 資料庫時，[Microsoft Azure 資料庫移轉服務](/azure/dms/dms-overview?WT.mc_id=bankdm-docs-dastarr)可用來將資料庫移至 Azure。 該服務使用[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?WT.mc_id=bankdm-docs-dastarr) 以確保在內部部署資料庫會與 Azure SQL 中所提供的功能相容。 移轉之前所需的任何資料庫變更都由您決定。 此外，使用此服務需要在內部部署網路與 Azure 之間的站對站網際網路連線。

### <a name="bulk-copy-program-for-sql-server"></a>適用於 SQL Server 的大量複製程式

如果目前的 SQL Server 是內部部署，而目標是要移至 SQL Azure，另一個很棒的技術是使用 SQL Server Management Studio[ 和 BCP 公用程式將資料移動](https://azure.microsoft.com/blog/bcp-and-sql-azure/?WT.mc_id=bankdm-docs-dastarr)到 SQL Azure。 再從原始內部部署伺服器撰寫指令碼並建立 Azure SQL 資料庫之後，可以使用 BCP 將資料快速傳送到 SQL Azure。

### <a name="blob-and-file-storage"></a>Blob 和檔案儲存體

個別的銀行分行通常會在本機內部部署伺服器上有自己的檔案存放區。 這可能造成分行之間的檔案共用問題，並導致指定檔案沒有單一的真實來源。 更糟的是，機構內可能有「正式」檔案存放區供分行存取，但有間歇性連線或存取檔案共用的其他問題。

Azure 提供可協助減輕這些問題的服務。 將此資料移至 Azure，可為所有資料和使用集中式權限和存取控制的通用存取儲存體提供單一的真實來源。

針對特定資料格式，不同的資料儲存體解決方案可能更加適用。
例如，在 SQL Server 中內部部署儲存的資料可能最適合 Azure SQL。 儲存在 .csv 或 Excel 檔案中的資料可能最適合 [Azure Blob](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=bankdm-docs-dastarr) 儲存體或[Azure 檔案](/azure/storage/files/storage-files-introduction?WT.mc_id=bankdm-docs-dastarr)儲存體，這是在 Blob 服務上實作的。

幾乎所有送進和送出 Azure Blob 儲存體的資料都通過 Blob 儲存體，做為資料移動的一部分。 Blob 儲存體具有下列要素。

- 持久和可用
- 安全和規範
- 可管理和符合成本效益
- 可調整和高效能
- 開放和可交互作用

將所有分行連接至 Azure 中相同檔案共用，通常是透過銀行現有的資料中心來完成，如圖 1 所示。 公司資料中心透過 SMB (伺服器訊息區) 連線連接到檔案儲存體。
邏輯上來說，並從站台網路的觀點來看，檔案共用可以位於公司資料中心內，而且可以裝載為任何其他網路的檔案共用。
當使用這項技術時，資料在待用時和在資料中心與 Azure 之間傳輸過程中會加密。

![邏輯檔案共用](./assets/data-mgmt-in-banking-overview/data-mgmt-01.png)

圖 1

企業通常會使用檔案儲存體來彙總及保護大量檔案。 這可淘汰舊的檔案伺服器或重新安排硬體用途。
移至檔案儲存體的另一個優點是集中的資料管理和復原服務。

### <a name="azure-data-box"></a>Azure 資料箱

通常，銀行要帶入 Azure 的資訊就算沒有數 PB，也會有數 TB。 幸好，Azure 中的資料存放區非常有彈性且高度可擴充。

著重於將超大量資料移轉至 Azure 的服務是 [Azure 資料箱](https://azure.microsoft.com/services/storage/databox/?WT.mc_id=bankdm-docs-dastarr)。 此服務的設計宗旨是在不透過 Azure 連線傳送資料或備份的情況下移轉資料。 Azure 資料箱適用於數 TB 的資料，此設備可從 Azure 入口網站訂購。 它會運送到您的位置，它在那裡會連線到您的網路並透過標準 NAS 通訊協定載入資料，並透過 standard256-AES 加密提供保護。 當資料位於設備上時，該設備會送回 Azure 資料中心，接著資料會在 Azure 中進行水合。
然後，會安全地清除該裝置。

## <a name="azure-information-protection"></a>Azure 資訊保護

Azure 資訊保護 (AIP) 是雲端式解決方案，可協助組織將其文件及電子郵件分類、加註標籤及進行保護。 定義規則和條件的系統管理員可自動完成此動作，使用者也可以手動方式完成，或在提供使用者建議的情況下組合完成。

## <a name="data-services"></a>資料服務

銀行遭遇主要資料管理問題、因不同核心銀行系統而造成中繼資料衝突，以及來自來源系統、上線系統的資料，提供管理系統，CRM系統等。 Azure 擁有可協助緩解這些及其他經常發生的資料問題的工具。

金融服務組織需要用來在其資料上執行作業有許多種。 在將資料寫入 Azure 資料存放區時，可能必須轉換該資料，或將它與其他資料連結以增強所擷取之資料。

### <a name="azure-data-factory"></a>Azure Data Factory

[Microsoft Azure Data Factory](/azure/data-factory/introduction?WT.mc_id=bankdm-docs-dastarr) 是完全受控的服務，可協助輸入、處理和監視 Data Factory 管線中的資料移動。 Data Factory 活動會形成資料管理管線的結構。

當資料送進 Azure 時，以及在其他 Azure 服務之間傳送時，Data Factory 可將資料轉換或增強。 Azure Data Factory 是一個針對複雜的混合式擷取-轉換-載入 (ETL)、擷取-載入-轉換 (ELT) 及資料整合專案建置的受控雲端服務。

例如，資料可送入分析管線或工具，進而造可操作的深入解析。 資料可能會送進機器學習解決方案，或轉換成另一種格式以供後續的下游處理。 一個例子是將 .csv 檔案轉換成較適合機器學習系統的 PARQUET 檔案，並將這些 PARQUET 檔案儲存在 Blob 儲存體中。

資料也可能提交給下游計算服務，例如 [Azure HDInsight](/azure/hdinsight/hadoop/apache-hadoop-introduction?WT.mc_id=bankdm-docs-dastarr)[Spark](/azure/hdinsight/spark/apache-spark-overview?WT.mc_id=bankdm-docs-dastarr)[Azure Data Lake Analytics](/azure/data-lake-analytics/data-lake-analytics-overview?WT.mc_id=bankdm-docs-dastarr) 和 [Azure Machine Learning](/azure/machine-learning/?WT.mc_id=bankdm-docs-dastarr)。 這樣可直接送進系統，進而產生分析和智慧型報告。 資料輸入的其中一個常見模型如下方的圖 2 所示。 資料會保存於通用的 [Data Lake](/azure/data-lake-store/data-lake-store-overview?WT.mc_id=bankdm-docs-dastarr) 中，以供下游分析服務使用。

![Data Lake 擷取模型](./assets/data-mgmt-in-banking-overview/data-mgmt-02.png)

圖 2

Data Factory 管線是由活動組成，這些活動參與及輸出資料集。 活動可以組合到一個管線中，該管線定義了您要獲取資料的位置、處理方式，以及儲存結果的位置。 建置管線與活動是 Data Factory 的核心，在 Azure 入口網站中撰寫視覺化工作流程可輕鬆建立管線。 [請參閱此處查看完整的活動清單](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=bankdm-docs-dastarr)。

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/?WT.mc_id=bankdm-docs-dastarr) 是在 Azure 中受控的 Apache Spark 型分析平台。 它具有可高度擴充姓，並可視需要在電腦叢集上執行大型 Spark 作業。 Databricks 在 Notebook 上使用，它在資料科學家、資料工程師和商務分析師之間提供共同作業的單一位置。

當需要資料轉換或分析時，Databricks 可成為邏輯處理管線。 它可以由 Data Factory 直接提供給分析時間至關重要的機器學習情境，或提供簡單的檔案轉換。

![Databricks](./assets/data-mgmt-in-banking-overview/data-mgmt-03.png)

## <a name="archiving-data"></a>封存資料

當活動資料存放區中不再需要資料時，根據狀態和當地銀行法規，可以基於合規性或稽核記錄目的而封存資料。 針對不常存取的資料，Azure 有儲存體選項可用。 資料經常會有隱私權問題，需要將資料保留在儲存體中許多年。

儲存資料的成本可能很高，尤其是儲存在內部部署資料庫中更是如此。 這些資料庫有時很少存取，只需要寫入新封存的資料或刪除封存中不再需要的資料的資料庫。
內部部署機器的非經常性存取，意味著硬體的擁有權總成本較高。

### <a name="azure-archive-storage"></a>Azure 封存儲存體

對於非結構化資料 (例如檔案或映像)，Azure 為 Blob 儲存體提供了[數個儲存體儲存層](/azure/storage/blobs/storage-blob-storage-tiers?WT.mc_id=bankdm-docs-dastarr)，包括經常性儲存層、非經常性儲存層和封存儲存層。 經常性儲存層適用於使用中且預期效能最高並在應用程式中使用的資料。 非經常性儲存層適用於短期備份和災害復原資料集，以及應用程式可使用但很少存取的資料。 封存儲存層的成本最低，適用於已離線的資料。

封存儲存層資料可以再次水合到非經常性或經常性儲存層，但此動作可能需要數小時才能完成。 如果資料至少 180 天不會存取，封存儲存體可能很適合。 當 Blob 位於封存儲存體時是無法讀取的，但可執行其他現有的作業，例如列出清單、刪除和擷取中繼資料。 封存資料層是 Blob 儲存體成本最低的資料層。

### <a name="azure-sql-database-long-term-retention"></a>SQL Database 長期保留

當使用 Azure SQL 時，有[長期備份保留服務](/azure/sql-database/sql-database-long-term-retention?WT.mc_id=bankdm-docs-dastarr)可用來儲存備份長達十年。 使用者可以排程備份以長期保留儲存體，讓備份保留數週、數月或甚至幾年。

若要從長期儲存體還原資料庫，請依據時間戳記選取特定備份。 可將資料庫還原至原始資料庫相同訂用帳戶底下的現有伺服器。

## <a name="deleting-unwanted-data"></a>刪除不必要的資料

為了遵守有關資料保留的銀行法規或政策，通常必須在不再需要時刪除資料。 在針對這類不必要資料實作技術解決方案之前，務必制訂清除計畫，才不會違反經過同意的政策。 任何時間皆可從封存或 Azure 中的其他資料存放區刪除資料。

有一個刪除不必要資料的有效策略，就是固定間隔時間執行，最常見的是每晚或每週。 您可以撰寫[時間觸發的 Azure Function](/azure/azure-functions/functions-bindings-timer?WT.mc_id=bankdm-docs-dastarr) 來正確執行此作業。 如果您刪除任何資料，Microsoft Azure 即會刪除該資料，包括任何快取或備份的複本。

## <a name="getting-started"></a>開始使用

根據目前的使用狀況和現今使用的資料模型成熟度，有數種方法可以開始使用。 不論是什麼情況，現在都是檢閱資料儲存體、處理，以及每個資料存放區所需之保留模型的最佳時機。 這對於在法規合規性情境中建置資料管理系統至關重要。
雲端在此提供的是新商機，而且目前在內部部署無法使用。 這可能意味著要更新您現有的資料模型。

當您熟悉新的資料模型之後，請決定您的資料擷取策略。 資料來源有哪些？ 資料存放在 Azure 的何處？ 資料如何及何時會移至 Azure？ 根據內容類型、大小和更多項目，此處有許多資源可用來協助移轉。 Azure 資料移轉服務就是其中一個例子。

當您的資料裝載於 Azure 中之後，請針對已超過其實用性或有效期的資料建立資料清除計畫。 雖然長期 (非經常性) 儲存體通常是封存的最佳選擇，不過清理過期的資料可降低使用量和整體儲存成本。 備份及封存 [Azure 解決方案架構](https://azure.microsoft.com/solutions/architecture/?solution=backup-archive?WT.mc_id=bankdm-docs-dastarr)是協助規劃您整體策略的理想資源。

## <a name="relevant-technologies"></a>相關技術

- [Azure Functions](/azure/azure-functions/functions-bindings-timer?WT.mc_id=bankdm-docs-dastarr) 是無伺服器的指令碼和小型程式，可回應系統事件或根據計時器來執行。

- [Azure 儲存體用戶端工具](/azure/storage/common/storage-explorers?WT.mc_id=bankdm-docs-dastarr)是用來存取資料存放區的工具，其包含的內容遠超過 Azure 入口網站。

- [Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=bankdm-docs-dastarr)適合用來儲存文字或影像和其他類型的非結構化資料的資料。

- [Databricks](/azure/azure-databricks/?WT.mc_id=bankdm-docs-dastarr) 是完全受控的服務，提供 Spark 叢集的簡單實作。

- [Data Factory](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=bankdm-docs-dastarr) 是雲端資料整合服務，用來將資料儲存、傳輸及處理服務組合成自動化資料管線

## <a name="conclusion"></a>結論

隨著銀行與金融產業的數位化狀況快速改變，客戶越來越希望找到自己可以立即採用且加速時間很快的解決方案和合作夥伴。 隨著資料擷取以指數方式增加，銀行需要快速、創新且安全的方式來儲存、分析及使用其重要資料。

Azure 可使用數種技術和策略協助資料擷取、處理、封存和刪除的需求。 將資料擷取至 Azure 很簡單，而且根據資料類型、結構等，有各種資料存放區可用來儲存資料。資料解決方案除了適用於 SQL Server 和 SQL Azure，還包含協力廠商資料庫。

使用 Databricks 和 Data Factory 等 Azure 服務，可以簡單地操作及處理該資料。 封存儲存體適用於不常存取的資料的長期儲存，而且可以視需要依循環週期加以刪除。

請瀏覽 Azure 解決方案程式庫，了解[備份和封存儲存體](https://azure.microsoft.com/solutions/architecture/?solution=backup-archive?WT.mc_id=bankdm-docs-dastarr)，以開始設計您的資料管理計畫。

**作者：** Howard Bush 和 David Starr
