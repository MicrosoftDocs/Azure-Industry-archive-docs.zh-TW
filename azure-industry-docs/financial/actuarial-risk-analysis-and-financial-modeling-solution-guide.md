---
title: 將精算風險分析移至 Azure 的指南
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 精算開發人員如何將現有的解決方案加上支援的基礎結構移至 Azure。
ms.openlocfilehash: 456c054cf3a6165f160005ba8ea2c155637faa07
ms.sourcegitcommit: f030566b177715794d2ad857b150317e72d04d64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74234538"
---
# <a name="actuarial-risk-analysis-and-financial-modeling-solution-guide"></a>精算風險分析與財務模型化解決方案指南

過去幾年中，提供保險類產品的保險公司和公司已經看到一些新法規到位。 這些新法規要求對保險公司進行更廣泛的財務模型化。 歐盟頒佈了[償付能力 II](https://eur-lex.europa.eu/legal-content/EN/ALL/?uri=celex%3A32009L0138)，要求保險公司證明已完成適當分析，以驗證保險公司可在年底具備償付能力。 提供可變年金的保險公司必須遵循[精算指導方針 XLIII](https://eur-lex.europa.eu/legal-content/EN/ALL/?uri=celex%3A32009L0138)，並廣泛分析資產與負債現金流量。 所有類型的保險公司，包括散發保險類產品的保險公司，都必須在 2021 年前實施[國際財務報告準則 17](https://www.ifrs.org/supporting-implementation/supporting-materials-by-ifrs-standard/ifrs-17/)(IFRS 17)。 根據保險公司營運所在地的司法管轄區，存在其他法規。 這些標準和法規要求精算師在進行資產與負債模型化時使用計算密集型技術。 許多分析會利用隨機產生的情節資料，而不是資產和負債等的逐一輸入。 除了法規需求之外，精算師還會進行大量的財務模型化和計算，以產生用來產生法規報告的模型輸入資料表。 內部網格不能滿足運算需求，因此精算師正穩定地移至雲端。

精算師移至雲端，以便有更多時間檢閱、評估及驗證結果。 當管理機關對保險公司進行稽核時，精算師必須能夠說明其結果。 移至雲端可存取運算資源，透過平行處理的強大功能，在 24-120 小時的時鐘時間內執行 20000 小時的分析。 為了協助這項擴展需求，許多建立精算軟體的公司提供允許在 Azure 中執行計算的解決方案。 這些解決方案中，有些是以執行內部部署和 Azure 的技術為基礎的，例如 [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview?view=hpc16-ps&WT.mc_id=riskmodel-docs-scseely)。 其他解決方案是 Azure 原生的，並使用 [Azure Batch](https://docs.microsoft.com/azure/batch?WT.mc_id=riskmodel-docs-scseely)、[虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets?WT.mc_id=riskmodel-docs-scseely)，或自訂的擴充解決方案。

在本文中，我們將了解精算開發人員如何使用 Azure，結合模組化封裝，來分析風險。 本文會說明模組化封裝用來在 Azure 上大規模執行的一些 Azure 技術。 您可以使用相同技術進一步分析您的資料。 我們會查看以下項目：

- 在 Azure 中，以較少的時間執行較大的模型。
- 報告結果。
- 管理資料保留期。

無論是為生命、財產和災害、健康或其他保險服務，您都必須建立資產和負債的財務與風險模型，以調整您的投資和保費，以便保持身為保險公司的償付能力。 IFRS 17 報告對精算師建立的模型增加了變化，例如計算合約服務利潤 (CSM)，這改變了保險公司隨著時間管理其利潤的方式。

## <a name="running-more-in-less-time-in-azure"></a>在 Azure 中，以較少的時間執行更多

您相信雲端的承諾：它可以更快、更輕鬆地執行您的財務和風險模型。 對於許多保險公司而言，粗略計算顯示出一個問題：他們需要數年，甚至數十年的連續時間，從頭到尾執行這些計算。 您需要可解決執行時間問題的技術。 您的策略如下：

- 資料準備：某些資料變更的速度很慢。 政策或服務合約生效之後，索賠就會以可預測的速度進展。 您可以備妥模型在抵達時執行所需的資料，就不需要針對資料清理和準備而規劃大量時間。 您也可以使用叢集，透過加權表示法建立連續數據的替代物。 較少的記錄通常會使計算時間縮短。
- 平行處理：如果需要針對兩個或更多項目執行相同的分析，您可以同時執行分析。

讓我們分別看看這些項目。

### <a name="data-preparation"></a>資料準備

資料會從數個不同來源進入。 您的商業帳簿中有半結構化的政策資料。 有關被保險人、公司和出現在各種申請表單中的項目的資訊。 經濟情境產生器 (ESG) 會以各種不同格式產生資料，這些格式可能需要轉譯成您的模型可使用的表單。 目前有關資產價值的資料也需要正規化。 從來源轉移到模型時，股票市場資料、租賃現金流量、抵押貸款的支付資訊，以及其他資產資料都需要做一些準備。 最後，您應該根據最新的機厭資料更新任何假設。 為了加速模型執行，請提前準備資料。 執行階段發生時，您應執行任何必要更新以增加自上次排程更新後的變更。

那麼，您該如何準備資料？ 讓我們先看看通用位元，然後再看看如何處理資料出現的不同方式。 首先，您需要一個機制以獲得自上次同步處理之後的所有變更。 該機制應該包含可排序的值。 針對最近的變更，其值應大於任何先前的變更。 最常見的兩個機制是不斷增加的 ID 欄位或時間戳記。 如果記錄具有增加的 ID 金鑰，但記錄其餘部分包含可更新的欄位，則需要使用類似&quot;上次修改&quot;時間戳記以找出變更。 處理完記錄之後，要記錄最後已更新項目的可排序值。 此值 (可能是名為 _lastModified_ 的欄位上的時間戳記) 會變成您的浮水印，用於後續對資料存放區的查詢。 資料變更可以用多種方式加以處理。 以下是兩個使用最少資源的常見機制：

1. 如果您有數百或數千個變更要處理：將資料上傳至 Blob 儲存體。 使用 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory?WT.mc_id=riskmodel-docs-scseely)中的事件觸發程序處理變更集。
2. 如果您有少數變更要處理，或希望一有變更就立即更新資料，請將每個變更放進由[服務匯流排](https://docs.microsoft.com/azure/service-bus-messaging?WT.mc_id=riskmodel-docs-scseely)或[儲存體佇列](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction?WT.mc_id=riskmodel-docs-scseely)所裝載的佇列訊息。 [這篇文章](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted?WT.mc_id=riskmodel-docs-scseely)詳細說明兩種佇列技術之間的取捨。 當訊息在佇列中時，您可以使用 Azure Functions 或 Azure Data Factory 中的觸發程序來處理訊息。

下圖說明典型案例。 首先，排程的工作會收集一些資料集，並將檔案放入儲存體。 排程的工作可以是內部部署執行的 CRON 作業、[排程器工作](https://docs.microsoft.com/azure/scheduler?WT.mc_id=riskmodel-docs-scseely)[邏輯應用程式](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview?WT.mc_id=riskmodel-docs-scseely)，或任何在計時器上執行的項目。 上傳檔案之後，會觸發 [Azure Function](https://docs.microsoft.com/azure/azure-functions?WT.mc_id=riskmodel-docs-scseely) 或 **Data Factory** 執行個體以處理資料。 如果是可在短時間內處理的檔案，請使用 **Function**。 如果處理程序很複雜，需要 AI 或其他複雜的指令碼，您可能會發現 [HDInsight](https://docs.microsoft.com/azure/hdinsight?WT.mc_id=riskmodel-docs-scseely)[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks?WT.mc_id=riskmodel-docs-scseely)，或某些自訂項目的效果更好。 完成時，檔案會以新檔案或資料庫中的記錄的可用表單做為結束。

 ![](./assets/insurance-risk-assets/process-files.png)

當資料位於 Azure 中之後，您必須透過模型化應用程式使其可用。 您可以撰寫程式碼來進行自訂轉換，請透過 **HDInsight** 或 Azure **Databricks** 執行項目，以內嵌較大的項目，或將資料複製到正確的資料集內。 使用巨量資料工具也可以協助您執行動作，例如將非結構化資料轉換成結構化資料，以及針對接收的資料執行任何 AI 和 ML。 您也可以裝載虛擬機器、將資料從內部部署直接上傳到資料來源、直接呼叫 Azure Functions 等。

之後，資料必須由您的模型使用。 執行這項操作的方式，主要取決於計算需要存取資料的方式。 有些模型化系統要求所有資料檔案存留在執行計算的節點上。 其他系統可以使用 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/?WT.mc_id=riskmodel-docs-scseely)、[MySQL](https://docs.microsoft.com/azure/mysql/?WT.mc_id=riskmodel-docs-scseely)或 [PostgreSQL](https://docs.microsoft.com/azure/postgresql/?WT.mc_id=riskmodel-docs-scseely) 等的資料庫。 您可以使用這些項目中的任何一個低成本版本，然後在模型化執行期間擴充性能。 這為您提供了每日工作所需的價格，以及數千個核心要求資料時的額外速度。 一般來說，此資料在模型化執行期間會處於唯讀狀態。 如果您的計算跨多個區域進行，請考慮使用 [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/distribute-data-globally?WT.mc_id=riskmodel-docs-scseely) 或 [Azure SQL 異地複寫](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview?WT.mc_id=riskmodel-docs-scseely)。 兩者都提供了以低延遲跨區域自動複寫資料的機制。 您的選擇取決於您的開發人員所知道的工具、您如何模型化您的資料，以及您的模型化執行所使用的區域數目。

請務必花些時間思考您的資料要儲存在何處。 了解將會存在多少個相同資料的同時要求。 思考您將如何散發資訊：

- 是否每個計算節點都取得自己的複本？
- 副本是否透過某些高頻寬位置共用？

如果使用 Azure SQL 將資料集中存放，則可能會在大部分時間將資料庫保存在較低的價格層。 如果資料只在模型化執行期間使用，而且不會經常更新，則 Azure 客戶將盡可能備份資料，並在兩次執行之間關閉其資料庫執行個體。 這可能會省下大筆金額。 客戶也可以使用 [Azure SQL 彈性集區](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool?WT.mc_id=riskmodel-docs-scseely)。 它們是設計用來控制資料庫成本，尤其是當您不知道哪些資料庫會在不同時間承受大量負載時。 彈性集區允許資料庫集合使用所需電力，然後在需求轉移到系統中的其他位置時縮減。

您可能需要在模型化執行期間停用資料同步，以便此流程中稍後的計算使用相同的資料。 如果您使用佇列，請停用訊息處理器，但要允許佇列接收資料。

您也可以使用執行之前的時間來產生經濟情境、更新精算假設，以及廣泛更新其他靜態資料。 讓我們看看經濟情境產生 (ESG)。 [精算師協會](https://www.soa.org/)提供[學院利率產生器](https://www.soa.org/tables-calcs-tools/research-scenario/) (AIRG)，以及模擬美國債券殖利率 的 ESG 。 AIRG 是指定用於評價手冊 20 (VM-20) 計算等項目中的。 其他 ESG 可模擬股票市場、抵押貨款、商品價格等。

由於您的環境正在對資料進行前置處理，因此您也可以提早執行其他部分。 例如，您可能有用記錄代表更大族群進行模擬的項目。 這通常是透過叢集記錄來做到的。 如果資料集是偶爾更新，例如一天一次，則可以將記錄集減少為將在模型中使用的部分，做為擷取流程的一部分。

讓我們看看一個實際的範例。 使用 IFRS-17，您必須將合約分組在一起，讓任兩個合約的開始日期之間的最大距離不到一年。 讓我們假設您以簡單方式執行這件事，並使用合約年做為分組機制。 透過讀取檔案並將記錄移至適當的年份群組，可以在將資料載入到 Azure 時完成此分割。

專注於資料準備可減少執行模型元件所需的時間。 透過及早獲取數據，您可以節省執行模型的時鐘時間。

### <a name="parallelization"></a>平行處理

適當的平行處理步驟可以大幅減少執行時間。 透過簡化實作部分，並了解如何以允許兩個或多個活動同時執行的方式加速模型，藉此來實現加速。 訣竅是在工作要求的大小和單一節點的生產力之間找到平衡。 如果工作花在設定和清理上的時間比評估中的時間多，那就表示太小了。 如果工作太大，就無法改善執行時間。 您希望活動夠小以便分佈在多個節點上，並在經過的執行時間方面造成正面影響。

為了充分運用您的系統，您必須了解模型的工作流程，以及計算與擴充能力之間如何互動。您的軟體可能會有作業、工作或類似的概念。 請運用該知識來設計可以分割工作的項目。 如果在您的模型中有一些自訂的步驟，請將那些步驟設計成允許將輸入分割成較小群組以進行處理。 通常，這被稱為分散-集中模式。

- 分散：沿著自然的行分割輸入，並允許個別工作執行。
- 集中：當工作完成時，收集其輸出。

在分割項目時，也要知道處理程序在繼續進行之前必須同步處理的位置。 人們分割項目的常見位置有好幾個。 針對巢狀的隨機執行，您可能有一千個外部迴圈，其中一組轉折點執行一百個案例的內部迴圈。 每個外部迴圈可以同時執行。 您在轉折點停止，然後同時執行內部迴圈，將資訊帶回來調整外部迴圈的資料，然後再次前進。 下圖示範闡明工作流程。 有足夠的計算後，您可以在 100,000 個核心上運行100,000 個內部迴圈，將處理時間縮短到以下時間的總和：

![](./assets/insurance-risk-assets/timing.png)

根據完成散發的方式一點一點增加散發；它可能像使用正確參數建構小作業一樣簡單，也可能像將 100K 個檔案複製到正確位置一樣複雜。 如果可以使用來自 HD Insight 的 Apache Spark、Azure Databricks 或您自己的部署來散發結果會總，則甚至可以加快處理結果的速度。 例如，計算平均值是記住到目前為止所看到的項目數和總和的簡單問題。 其他計算可能在具有數千個核心的單一電腦上運作得更好。 對於那些計算，您可以在 Azure 中使用已啟用 GPU 的電腦。

大多數精算小組藉著將其模型移至 Azure 來開始這一段過程。 然後，他們會收集流程中各個步驟的計時資料。 接著，他們會將每個步驟的時鐘時間，從最長到最短的經過時間加以排序。 它們不會查看總執行時間，因為有些項目可能耗用數千個核心時數，但經過時間只有 20 分鐘。 對於每一個執行時間最長的作業步驟，精算開發人員都在尋找減少經過時間的方法，同時獲得正確的結果。 此程序會定期重複。 有些精算小組會設定目標執行時間，例如，一整晚的避險分析的目標是在 8 小時內執行。 一旦時間超過 8.25 小時，精算小組的某些部分會切換為改善分析中最長部分的時間。 一旦他們將時間縮短到 7.5 小時之後，就會切換回開發。 精算師返回和最佳化的啟發學習法各不相同。

若要執行這所有作業，您有幾種選擇。 多數精算軟體會使用計算網格。 在內部部署和 Azure 上運作的網格使用的是協力廠商封裝 [HPC Pack](https://docs.microsoft.com/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=riskmodel-docs-scseely) 或其他自訂封裝。 針對 Azure 最佳化的網格會使用[虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/?WT.mc_id=riskmodel-docs-scseely)、[Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=riskmodel-docs-scseely)，或其他自訂項目。 如果您選擇使用擴展集或 Batch，請務必看看他們對低優先順序 VM 的支援 ([擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?WT.mc_id=riskmodel-docs-scseely)低優先順序文件、[Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms?WT.mc_id=riskmodel-docs-scseely) 低優先順序文件)。 低優先順序 VM 是在只要一般價格一小部分即可租用的硬體上執行的 VM。 價格之所以較低，是因為當容量要求時，可能會佔用低優先順序 VM。 如果在時間預算中有一些彈性空間，那麼低優先順序 VM 是降低模型化執行價格的好方法。

如果您需要協調多部電腦上的執行和部署，也許有些是在不同區域中執行，那麼您可以善用 CycleCloud。 CycleCloud 不需要任何額外費用。 它會在必要時協調資料移動。 這包括配置、監視，以及關閉電腦。 它甚至可以處理低優先順序電腦，確認包含費用。 到目前為止，您已經可以說明您需要的電腦組合。 例如，也許您需要某一類電腦，但可以在任何具有 2 或多個核心的版本上順利執行。 週期可將核心配置到這些電腦類型上。

## <a name="reporting-on-the-results"></a>報告結果

執行精算套件並產生其結果之後，您將會有數個可提供給管理機關的報告。 您也會有大量的新資料，可用來加以分析，產生管理機關或稽核員不需要的深入解析。 您可能希望了解最佳客戶的概況。 使用深入解析，您可以告訴行銷部門，低成本客戶是什麼樣子，讓行銷和銷售部門可以更快找到他們。 同樣地，您可以使用這些資料來探索哪些族群從保險中獲益最多。 例如，您可能發現善用年度健康檢查的人會提早發現早期階段的健康問題。 這可為保險公司省下時間和金錢。 您可以使用該資料來促進客戶群中的行為。

若要做到這一點，您要存取大量的資料科學工具和某些視覺效果。 根據您想要進行多少調查，您可以從[資料科學 VM](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview?WT.mc_id=riskmodel-docs-scseely) 開始，這可從 Azure Marketplace 佈建。 這些 VM 都有 Windows 和 Linux 版本。 安裝之後，您會發現 Microsoft R Open、Microsoft ML Server、Anaconda、Jupyter 和其他工具都已準備就緒。 投入一些 R 或 Python 即可將資料視覺化，並與同事分享深入解析。

如果需要進行更多分析，您可以使用 Apache 資料科學工具，例如 Spark、Hadoop，以及其他透過 HDInsight 或 Databricks 的工具。 當需要進行定期分析，而且您希望將工作流程自動化時，請多多使用這些工具。 這對於大型資料集的即時分析也很有用。

一旦發現有趣的東西，您將需要呈現結果。 許多精算師會先取出範例結果，並將它們插入 Excel，以建立圖表、圖形和其他視覺效果。 如果您想要一些具有良好介面的工具來深入鑽研資料，可以看看 [Power BI](https://docs.microsoft.com/power-bi/?WT.mc_id=riskmodel-docs-scseely)。 Power BI 可以製作一些不錯的視覺效果，顯示來源資料，而且可透過新增[排序、標註書籤](https://docs.microsoft.com/power-bi/desktop-bookmarks?WT.mc_id=riskmodel-docs-scseely)，為讀者說明資料。

## <a name="data-retention"></a>資料保留

您放入系統的大部分資料都必須保留，供日後稽核。 資料保留一般需要 7 到 10 年，但需求各有差異。 最小保留包含：

- 針對模型的原始輸入的快照集。 這包括資產、負債、假設、ESG 和其他輸入。
- 最終輸出的快照集。 這包括用來建立呈給管理機關的報告的任何資料。
- 其他重要的中繼結果。 稽核員會詢問您的模型為什麼會得出某些結果。 您必須保留模型為什麼做出特定選擇或得出特定數字的相關證據。 許多保險公司會選擇保留用來從原始輸入產生最終輸出的二進位檔。 然後，當被問到時，他們會重新執行模型以取得中繼結果的全新複本。 如果輸出相符，中繼檔案應該還要包含所需的說明。

在模型執行期間，精算師會使用可處理從執行載入的要求的資料傳遞機制。 當執行完成且不再需要資料時，他們會保留部分資料。 保險公司至少應保留輸入和執行階段組態，以滿足任何重現性需求。 資料庫會保留在 Azure Blob 儲存體的備份中，而且伺服器會關閉。 放在高速儲存體上的資料也會移至成本較低的 Azure Blob 儲存體。 放進 Blob 儲存體之後，您可以選擇每個 Blob 所使用的資料層：經常性儲存層、非經常性儲存層、封存儲存層。 經常性儲存層儲存體非常適合經常存取的檔案。 非經常性儲存層儲存體已針對不常存取的資料存取最佳化。 封存儲存層儲存體最適合保留可稽核的檔案，但是節省的成本是延遲成本：封存層資料延遲的計算是以小時為單位。 請閱讀 [Azure Blob 儲存體：經常性儲存層、非經常性儲存層和封存儲存層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?WT.mc_id=riskmodel-docs-scseely)以充分了解不同的儲存層。 從資料建立到刪除的過程中，您可以使用生命週期管理來管理資料。 Blob 的 URI 保持為靜態，但是 Blob 的儲存位置會隨著時間越來越便宜。 這項功能可為 Azure 儲存體的許多使用者省下大量金錢和困擾。 您可以在[管理 Azure Blob 儲存體生命週期](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts?WT.mc_id=riskmodel-docs-scseely)中了解這些細節。 您可以自動刪除檔案這一點很棒：這表示您不會透過參考超出範圍的檔案而意外擴充稽核，因為可以自動移除檔案本身。

## <a name="next-steps"></a>後續步驟

如果您執行的精算系統具有內部部署網格實作，該網格實作也可能在 Azure 上執行。 某些廠商擁有執行超大規模的特製化 Azure 實作。 隨著移至 Azure 的過程，也請移動您的內部工具。 各地的精算師都發現，他們的資料科學技能在其膝上型電腦或大型環境中運作良好。 尋找你的小組已經做過的事情：也許你有些東西使用了深度學習，但在一個 GPU 上需要花費數小時或數天運行。 試試在具有 4 個高階 GPU 的電腦上執行相同的工作負載，並查看執行時間；有很高的機率您會看到已經有的項目有顯著的加速。

隨著情況改善，請確認您也建構了一些資料同步處理以便提供模型化資料。 除非資料已就緒，否則無法啟動模型執行。 這可能需要增加一些心力，讓您只傳送已變更的資料。 實際的方法也取決於資料大小：更新幾 MB 可能沒什麼大不了，但減少數 GB 上傳的數量將會大大加快速度。

### <a name="tutorials"></a>教學課程

- 適用於 R 開發人員：[使用 Azure Batch 執行平行 R 模擬](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=riskmodel-docs-scseely)
- 示範如何使用 Azure 函式與儲存體互動的教學課程：[使用 Azure Functions 將映像上傳到 Blob 儲存體](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database?tutorial-step=2&WT.mc_id=riskmodel-docs-scseely)
- 使用 Databricks 擷取、轉換和下載：[使用 Azure Databrick 擷取、轉換和載入資料](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?WT.mc_id=riskmodel-docs-scseely)
- 使用 HDInsight 擷取、轉換和下載：[使用 Azure HDInsight 上的 Apache Hive 來擷取、轉換和載入資料](https://docs.microsoft.com/azure/hdinsight/hdinsight-analyze-flight-delay-data-linux?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fhadoop%2FTOC.json&amp;bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&WT.mc_id=riskmodel-docs-scseely)
- 資料科學 VM 作法 (Linux)：[https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/linux-dsvm-walkthrough](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/linux-dsvm-walkthrough?WT.mc_id=riskmodel-docs-scseely)
- 資料科學 VM 作法 (Windows)：[https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/vm-do-ten-things](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/vm-do-ten-things?WT.mc_id=riskmodel-docs-scseely)
