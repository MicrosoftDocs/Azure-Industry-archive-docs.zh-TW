---
title: 零售產業中的巨量資料管理入門
author: dstarr
ms.author: dastarr
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 零售商擁有大量未使用資料的資料存放區，從中可以獲得有價值的見解。 本文討論 Microsoft Azure 如何協助有效地使用該資料。
ms.openlocfilehash: 198e0f609889eee86e005c5ee56090006ae2a413
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77054111"
---
# <a name="data-management-in-retail-overview"></a>零售業的資料管理概觀

## <a name="introduction"></a>簡介

資料是開發和提供更好的零售體驗的基礎。 資料存在於零售組織的各個面向，可用來擷取出整個價值鏈的見解，並在運營績效和客戶行為中加以應用，從而改善服務體驗。 從線上瀏覽至社交參與，再到店內購買，存在各種豐富的資料。 不過，擷取資料只是資料管理的一部分。 整個組織內的資料都要正確地處理，才能將不同的資料拼接在一起分析，進而使零售商更能在經營時做出有影響力的決策。

例如，隨著行動購物逐漸成長，客戶期望零售商對於相關的購物習慣具有適當的資料量，用於改善體驗。 使用案例是在實體零售商店內的特定位置購物時，直接將個人化產品和促銷商品發送至客戶的行動裝置。 利用有關內容、地點、方式、數量和頻率的資料，以及商店產品可供量等其他輸入資料，可以在客戶位於目標產品附近購物時，向客戶的裝置發送即時促銷資訊。 

有效地使用資料可以幫助零售商提供更多適切的體驗來推動客戶購買；例如，零售商可以向客戶發送通知，並將自家電商網站的折扣代碼包含於其中。  此外，這項資料將提供可操作的見解，讓領導者在帶領公司時不會進退失據。

進行促銷的動作是由資料點的組合所構成，並由進入商店的客戶觸發。 要能將上述各點彼此串聯並產生效果，還需以下列資料管理模型為依據。

![程序流程](./assets/retail-data-management-assets/process-flow.png)

圖 1

將資料帶入 Azure 時，請考慮資料來源的 3P，以及資料本身對廠商想啟用的方案是否適用。 資料來源的 3P 是購買 (Purchased)、公用 (Public) 及專用 (Proprietary)。

> **購買**資料通常會擴大並強化組織的現有資料，通常包含市場和人口統計資料，可補充組織的資料擷取觸達。 例如，零售商可以購買額外的人口統計資料，以擴大主要的客戶記錄，確保記錄準確且完整。 
>
> **公用**資料是免費提供的，可以從社交媒體、政府資源 (例如地理位置) 及其他線上資源中獲取。 這項資料可以推斷出各種見解，例如哪種天氣模式與購買模式相關，或者哪種社交參與能指出某樣產品在特定地區的流行程度。 公用資料通常可透過 API 取得。
>
> **專用**資料位於組織內。 它可能是零售商的內部部署系統、SaaS 應用程式，或雲端提供者。 為能存取 SaaS 應用程式提供者中的資料，和其他廠商的資料，API 通常用於溝通廠商系統。 這包括電子商務網站記錄、POS 銷售資料，以及庫存管理系統等資料。

資料管理管線所提供的各種見解，都來自上述這幾類資料。

## <a name="ingest"></a>擷取

起初，資料會以原生格式載入 Azure，並視情況儲存。 接收及管理不同的資料來源可能令人怯步，但 Microsoft Azure 提供的服務可以快速、輕鬆地將資料載入雲端，使其可在資料管理管線中處理。 

Azure 有數個實用的服務可移轉資料。 選擇取決於要移轉的資料類型。 [Azure 資料移轉](/azure/dms/dms-overview?WT.mc_id=retaildm-docs-dastarr)服務適用於 SQL Server，而 [Azure 匯入/匯出服務](/azure/storage/common/storage-import-export-service?WT.mc_id=retaildm-docs-dastarr)是幫助將資料帶入 Azure 的服務。 要考慮的其他資料輸入服務包括 [Azure Data Factory](/azure/data-factory?WT.mc_id=retaildm-docs-dastarr) 與 [Azure Logic Apps](/azure/logic-apps/?WT.mc_id=retaildm-docs-dastarr) 連接器。 每個服務都有自己的功能，並且應該加以調查，看看何種技術最適合特定的情況。

資料擷取並不限於 Microsoft 技術。 透過 [Azure Marketplace](https://azuremarketplace.microsoft.com?WT.mc_id=retaildm-docs-dastarr)，零售商可在 Azure 中設定許多不同的供應商資料庫，以便與現有的內部部署系統搭配使用。 

並非所有資料都得在 Azure 中維護。 例如，銷售點 (POS) 資料可能會保留在內部部署中，讓網際網路中斷不會影響銷售交易。 這項資料可以按照排程 (可能是每晚或每週) 排入佇列並上傳到 Azure，以供分析使用，但前提是一律將內部部署資料視為事實來源。

## <a name="prepare"></a>準備

開始分析之前，必須備妥資料。 資料的成形對於確保預測模型的品質、報告 KPI 和資料相關性非常重要。

準備資料進行分析時有兩種資料類型需要處理，結構化和非結構化。 結構化資料較容易處理，因為它已形成，並經過格式化。 它可能只需要簡單的轉換，即可從來源格式的結構化資料轉變為可用於分析作業的結構化資料。 非結構化資料通常會帶來更多挑戰。 非結構化資料不會以固定的記錄長度格式儲存。 範例包括文件、社交媒體摘要，以及數位映像和影片。 這些資料與結構化資料必須使用不同的方式來管理，而且通常需要專門的流程，以確保這些資料透過可用的方式儲存在正確的資料存放區中。

資料成形發生在準備階段的「擷取-轉換-載入 (ETL)」程序中。 資料擷取自匯入 Azure 的未變更資料來源，並視需要「整理」或重新格式化，最後再以更為結構化的全新格式來儲存。 常見的 ETL 資料準備作業是將 .csv 或 Excel 檔案轉換為 parquet 檔案，以便 Apache Spark 等機器學習系統更容易快速讀取和處理。 另一個常見案例是從 .csv 檔案或其他格式建立 XML 檔案或 JSON。 產生的格式更容易與其他分析引擎搭配使用。

在 Azure 中，有數種轉換技術可作為 ETL 服務，以調整資料。 選擇包括 [Azure Databricks](/azure/azure-databricks?WT.mc_id=retaildm-docs-dastarr)、[Azure Functions](/azure/azure-functions/?WT.mc_id=retaildm-docs-dastarr) 或 Logic Apps。 Databricks 是 Apache Spark 的完全受控執行個體，用於將資料從一種形式轉換為另一種形式。 Azure Functions 是無狀態 (或「無伺服器 」) 函式，具有啟用這些函式和執行程式碼的觸發程序。 Logic Apps 整合服務。

## <a name="store"></a>儲存

在處理之前儲存資料需要審慎考慮。 資料可以採用結構化或非結構化格式，資料形式通常會決定其儲存目的地。 例如，高度結構化的資料可能適用於 Azure SQL。 低結構化資料可能會保留在 Blob 儲存體、檔案儲存體或表格儲存體中。

儲存在 Azure 中的資料具有卓越的效能，可透過可靠的服務等級協定 (SLA) 進行備份。 資料服務提供更易於管理的解決方案、高可用性、跨多個地理位置的複寫，最重要的是，Azure 提供推動機器學習所需的資料存放區和服務。

結構化和非結構化資料可以儲存在 [Azure Data Lake](/azure/data-lake-store/data-lake-store-overview?WT.mc_id=retaildm-docs-dastarr) 中，並使用 [U-SQL](/azure/data-lake-analytics/data-lake-analytics-u-sql-get-started?WT.mc_id=retaildm-docs-dastarr) (Azure Data Lake 專用的查詢語言) 以排入佇列。 Data Lake 中可能包含的資料範例包括如下，其分為常見的結構化和非結構化資料來源。

### <a name="structured-data"></a>結構化資料

- CRM 資料與其他企業營運應用程式
- POS 交易日期
- 感應器資料
- 關聯式資料
- 電子商務交易資料

### <a name="unstructured-data"></a>非結構化資料

- 社交摘要
- 影片
- 數位映像
- 網站點選流分析

有越來越多支援非結構化資料以產生值的使用案例。 這是透過資料驅動決策的需求和 AI 等技術的進步所推動的，以便能夠擷取和處理大規模資料。 例如，資料可以包括相片或串流影片。 例如，可利用串流影片來偵測客戶購物選擇，以進行流暢的結帳程序；或可以將產品目錄資料與客戶喜歡的衣服相片自然地整合，以提供類似或推薦品項的檢視。 

結構化資料的範例包括關聯式資料庫的資料摘要、感應器資料、Apache Parquet 檔案，以及電子商務資料。 這些資料的固有結構使其適用於機器學習管線。

Azure Data Lake 服務也可以使用 [Data Lake Analytics](/azure/data-lake-analytics/data-lake-analytics-overview?WT.mc_id=retaildm-docs-dastarr)，啟用批次處理和互動式查詢，以及即時分析。 此外，Data Lake 特別適用於非常大量的資料分析工作負載。 最後，Data Lake 中的資料是永續性的，沒有時間限制。

其他資料存放區 (例如關聯式資料庫、Blob 儲存體、Azure 檔案儲存體，以及 Cosmos DB 文件儲存體) 也可以保留整理後的資料，以便在資料管理管線中，進行下游分析。 沒有規定必須使用 Data Lake。

## <a name="analyze"></a>分析

對於降低庫存成本等問題，零售商可以使用機器學習程序執行的分析。

資料分析會透過機器學習引擎，備妥資料以進行處理，深入了解客戶體驗。 此程序會產生「學習」模型，並且可套用至未來資料以預測結果。 模型定義要檢查的資料，以及如何透過各種演算法分析資料。 將分析的輸出資料和資料視覺效果搭配使用，可以觸發見解 - 例如，從零售商電子商務平台中的客戶願望清單中，提供商品的店內優惠券。

藉由提供學習生態系統進行處理所儲存的資料，來進行資料分析。 通常，這是由 Hadoop、Databricks 或在虛擬機器上運行之自我管理 Spark 執行個體所執行的機器學習。 這也可以藉由查詢資料來完成。 KPI 的見解通常可以在整理後的資料中找到，而無需透過機器學習管線。

[Hadoop](/azure/hdinsight/hdinsight-hadoop-architecture?WT.mc_id=retaildm-docs-dastarr) 是完全受控之 Azure 服務 ([HDInsight](/azure/hdinsight/?WT.mc_id=retaildm-docs-dastarr)) 的一部分。 HDInsight 是一組資料學習工具，用於定型資料模型、將資料輸出到資料倉儲，以及透過 Hive 查詢語言對 Hadoop 執行查詢。 HDInsight 可以分析資料流或歷史資料。

各種不同的學習演算法可套用至資料，作為定型和維護資料模型的一部分。 資料模型會明確決定針對分析人員所產生的資料結構。

首先，會適當地整理並形成資料。 然後由機器學習系統 (例如 HDInsight 或 Apache Spark) 處理。 為了完成此操作，現有資料用於定型模型，而模型用於分析資料。 經過定型的模型會定期更新新的已知良好資料，以在分析期間提高準確性。 機器學習服務會使用模型來執行正在處理的資料分析。

在模型定型和執行資料分析程序之後，從機器學習分析衍生的資料可以儲存在資料倉儲或標準化儲存體資料庫中，以用於分析資料。 Microsoft 提供 [Power BI](/power-bi/?WT.mc_id=retaildm-docs-dastarr)，這是一項功能完整的資料分析工具，可深入分析資料倉儲中的資料。

## <a name="action"></a>動作

零售資料不斷變化，處理資料的系統必須及時進行。 例如，電子商務購物者的資料必須快速地處理。 如此一來，買家購物車中的商品可用於在結帳過程中，提供額外服務或附加商品。 這種形式的資料處理和分析必須幾乎立即發生，而且通常由進行「微批次」交易的系統執行。 也就是說，在可存取已處理資料並透過模型執行的系統中分析資料。

其他「批次」作業可能會定期發生，但不需要幾近即時發生。 當批次分析發生內部部署時，這些作業通常在夜間、週末執行，或在不使用資源時執行。 透過 Azure，可以隨時擴展大型批次作業及支援這些作業所需的虛擬機器。

使用下列步驟開始使用。

1. 為資料存放區建立資料擷取計劃，為要執行的分析提供值。 透過詳細的資料同步或移轉計劃，可以將資料以原始格式帶入 Azure。 

2. 確定所需的可操作見解，並選擇資料處理管線以配合資料處理活動。 

3. 記住這些資料功能，運用適當的演算法建立資料處理管線，以獲得所需的見解。

4. 如果可能，請使用一般資料模型以輸出到資料倉儲；這可以展現最有趣的資料功能。 這通常表示在原始的 Azure 儲存體系統中讀取資料，並將整理後的版本寫入另一個資料存放區。

5. 透過 Spark 或 Hadoop 提供的機器學習管線處理資料。 然後將輸出提供給資料倉儲。 有許多預設演算法可處理資料，或零售商可各自實作。 除了機器學習案例以外，還要將資料載入標準資料儲存體中，並強制執行一般資料模型，然後查詢 KPI 資料。 例如，資料可能會儲存在星型結構描述或其他資料存放區。

由於資料現在可供資料分析師使用，也許會發現可操作的見解，並採取行動以利用這些新知識。 例如，客戶的購買喜好可以載入回零售商的系統中，並用於改善數個客戶接觸點，如下所示。

- 藉由搭售產品，增加平均電子商務或 POS 交易
- CRM 中的購買記錄可支援客戶呼叫中心查詢
- 由電子商務推薦引擎量身打造的產品建議
- 根據客戶資料的目標與相關廣告
- 根據供應鏈中的產品移動更新庫存可供量

可能出現的另一種見解是以前未受質疑的模式。 例如，您可能發現在下午 3:00 到 7:00 之間，發生更多庫存損失。 這可能表示需要額外的資料，以判斷根本原因和行動方案 - 例如，改善安全性或標準作業程序。

## <a name="conclusion"></a>結論

零售業中的資料管理是很複雜的。 但是，它提供傳遞相關和已改善客戶體驗的寶貴能力。 使用本文中的技術，可以獲得見解，以優化客戶體驗、推動營利的業務成果，並找出可能會促使經營改善的趨勢。

若要繼續了解更多與實作資料管理管線相關的 Azure 功能，請參閱下列內容：

- 請參閱 [Azure Data Factory](/azure/data-factory/?WT.mc_id=retaildm-docs-dastarr) 如何幫助將內部部署資料存放區中的資料擷取至 Azure。
- 深入了解 [Azure Data Lake](/azure/data-lake-store/data-lake-store-overview?WT.mc_id=retaildm-docs-dastarr) 如何儲存所有資料，包括結構化和非結構化資料。
- 請參閱實際零售報表，說明 [Power BI](https://powerbi.microsoft.com/en-us/industries/retail/?WT.mc_id=retaildm-docs-dastarr) 如何更深入了解已知問題，並啟用趨勢分析。
- 請造訪 [Azure Marketplace](https://azuremarketplace.microsoft.com/?WT.mc_id=retaildm-docs-dastarr)，查找與內部部署現有解決方案相容的解決方案。

_本文由 David Starr 和 Mariya Zorotovich 撰寫。_
