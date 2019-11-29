---
title: 使用 Azure ML 和分析數據，針對消費者品牌進行 SKU 最佳化
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 零售產業種類最佳化。 透過來自 AI 與 ML 的見解的 SKU 最佳化。
ms.openlocfilehash: 22411776e830bb3c71f8c1277b30ec4331a3ef17
ms.sourcegitcommit: 2714a77488c413f01beb169a18acab45663bcfd7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308502"
---
# <a name="sku-optimization-for-consumer-brands-solution-guide"></a>消費者品牌的 SKU 最佳化解決方案指南

## <a name="introduction"></a>簡介

零售商與消費者品牌著重在確保他們有消費者在市場中尋求購買的適當產品與服務。 我們也可以說，當看待最大化銷售時，產品 (或產品組合) 是購物體驗的主要部分。 供應項目 (也就是庫存) 的可用性一直是消費者品牌的顧慮。

產品庫存 (亦稱為 SKU 種類) 是一個複雜的主題，它涵蓋供應鏈與後勤價值鏈。 針對此文件，我們特別著重在最佳化 SKU 種類的問題，最大化來自消費者良好觀點的營收。 SKU 種類最佳化的難題是透過開發演算法來回答下列問題：

- 哪些 SKU 再給定市場或市集表現得最好？ 
- 哪些 SKU 應該根據其表現配置到給定市場或市集？
- 哪些 SKU 的表現不如預期而且應該由其他表現較好 SKU 取代？
- 我們可以衍生有關我們客戶與市場區隔的哪些其他見解？

## <a name="automate-decision-making"></a>自動化決策制訂

傳統上，消費者品牌會透過增加 SKU 組合中的 SKU 來更接近消費者需求。 當SKU 數目激增且競爭增加時，預估單是組合中 SKU 百分之 10 的產品 SKU 就貢獻了百分之 90 的營收。 一般而言，百分之 80 的營收來自百分之 20 的 SKU。 而此比例是改進獲利性的候選指標。 

傳統靜態回報方法利用歷史資料，這會限制見解。 至少，決策仍是人工制訂並實行的。 這表示人為介入與處理時間。 由於人工智慧 (AI) 與雲端運算的發展，使得可以使用進階分析來提供廣大範圍的選擇與預測。 此類型的自動化改進了結果與提供給客戶的速度。

## <a name="sku-assortment-optimization"></a>SKU 種類最佳化

SKU 種類解決方案必須透過將銷售資料區非為有意義的詳細比較，來處理數百萬種 SKU。 解決方案的目標是透過使用進階分析來調整產品種類，來最大化在每間免稅店或店面的銷售。 第二個目標是減少缺貨情況並改進種類。 財務目標是 5到 10 個百分比的銷售額增加。 為了達成該目的，見解可人：

- 了解 SKU 組合績效並管理績效不符預期的項目。
- 最佳化 SKU 的分配以減少缺貨情況。
- 了解新的 SKU 如何支援短期與長期策略。
- 從現有資料建立可重複、可調整與可採取行動的見解。

## <a name="descriptive-analytics"></a>描述性分析

描述性模型會彙總資料點並探索可能會影響產品銷售之因素之間的關係。 該資訊可以透過一些外部資料點來增強，例如位置、天氣與人口普查資料等。視覺化有助於一個人透過解譯資料來衍生見解。 不過，在這樣做時，該人將受限於了解先前銷售循環中發生什麼事，或目前期間發生什麼事 (取決於資料重新整理頻率)。

傳統資料倉儲與回報方法在此案例中足敷使用，例如，可讓您了解哪些 SKU 在一段時間內績效最好或最差。

下圖顯示典型的歷史資料 (銷售資料) 報告。 它具有數個含有核取方塊的區塊，以選取準則來篩選結果。 中心顯示兩個橫條圖，其中顯示一段時間的銷售。 第一個圖表顯示每週的平均銷售額，而第二個圖表則顯示每週的數量。

 ![顯示歷史銷售資料的儀表板範例。](assets/sku-optimization-solution-guide/sku-max-model.png)
  
## <a name="predictive-analytics"></a>預測性分析

歷史報告對於了解發生什麼事而言非常實用。 最終，我們希望取得將會發生什麼事的預測。 過去的資訊對於該目的而言可能很實用。 例如，我們可以指出季節性趨勢。 但它不能涵蓋「模擬分析」案例，例如建構導入新產品的模型。 若要執行該動作，我們必須將我們的焦點移到建構客戶行為模型，因為那是決定銷售額的最終因素。

### <a name="an-in-depth-look-at-the-problem-choice-models"></a>深入檢視問題：選擇模型

讓我們從定義我們在追求什麼以及我們有哪些資料開始：

種類最佳化表示尋找產品子集以推出銷售，而這會使得預期營收最大化。 這是我們在追求的。

**交易資料**每日都會針對財務目的而收集。 

**種類資料**包括與 SKU 相關的潛在所有項目：以下是我們想要的範例： 

- SKU 的數目
- SKU 描述
- 配置的數量
- SKU 與購買數量
- 活動 (例如，購買) 的時間戳記
- SKU 價格
- POS 的 SKU 價格
- 每個 SKU 在任何時間點的庫存水準

不幸的是，此類資料並未可靠地收集為交易資料。 

在此文件中，為了簡化，我們將只會考慮交易資料與 SKU 資料，而不會考慮外部因素。

即便如此，請注意，在給定一組產品的情況下，會有 2n 個可能的種類。 這讓最佳化問題成為需要密集計算的程序。 在有大量產品的情況下，評估所有可能的組合不切實際。 因此，一般而言，種類會依類別 (例如，穀類)、位置與其他準則區分，以減少變數數目。 最佳化模型嘗試「修剪」最佳化數目至可行子集。

問題的癥結在於如何有效地**建構客戶行為模型**。 在完美的世界中，為他們提供的產品將會符合他們想購買的產品。

在過去幾十年，就已發展出可預測客戶選擇的數學模型。 模型的選擇最終會決定最適合的實作技術；因此，我們將會摘述它們並提供新的考量。

## <a name="parametric-models"></a>參數化模型

參數模型會使用具有一組有限參數的函數來逼近客戶行為。 我們依我們的方式預估最適合資料的參數集合。 最舊的一個已知方法是[多元邏吉斯迴歸](https://en.wikipedia.org/wiki/Multinomial_logistic_regression) (亦稱為 MNL、多類別邏輯或 Softmax 迴歸)。 它是用來計算分類問題中數個可能結果的機率。 在此案例中，您可以使用 MNL 來計算：

- 在給定某個種類 (a) 中一組類別中的項目，而且它對客戶 (v) 有一個已知效用時，某個客戶 (c) 在特定時間 (t) 選擇某個項目 (i) 的機率。 

我們也假設某項目的效用能以其特色的功能來定義。 測量效用時也能包快外部資訊 (例如，下雨時，雨傘比較有用)。

我們通常使用 MNL 做為其他模型的效能評定，因為在預估參數及評估結果方面，它很容易使用。 換句話說，若您的方式比 MNL 還糟糕，則您的演算法沒有用。
有數個模型從 MNL 衍生，但這不在此白皮書的討論範圍內。 

有適用於 R 與 Python 程式設計語言的程式庫。 針對 R，您可以使用 glm (與導數)。  針對 Python，有 [scikit-learn](http://scikit-learn.org/stable/)、[biogeme](http://biogeme.epfl.ch/) 與 [larch](https://pypi.org/project/larch/) 可供使用。 這些程式庫在各種平台上提供工具來指定 MNL 問題，並提供平行求解器來尋找解答。

最近，已收到使用 GPU 上 MNL 模型的實作透過一些參數來計算複雜模型的要求，若未使用此模型，在計算時將會非常棘手。

具有 Softmax 輸出層的類神經網路已在大型多類別問題中有效地使用。 這些網路會產生輸出向量，它們代表不同結果的機率分配。 相較於其他實作，它們在定型時非常慢，但是它們可以處理大量的類別與參數。 

## <a name="non-parametric-models"></a>非參數化模型

撇開 MNL 的熱門程度不講，MNL 在人類行為上進行了一些重大的假設，這可能限制了其實用性。 特別的是，它假設在兩個選項中選擇之某人的相對機率與稍後在該集合中引進額外的替代選擇無關。 這在大部分的案例中都不切實際。

例如，若您對產品 "A” 與產品 “B” 的喜好程度一樣，您有 50% 的時間會選擇某個產品，而不選擇另一個產品。 讓我們在矩陣中引進產品 “C”。 您可能在 50% 的時間仍選擇 “A”，但現在您將喜好程度 25% 分給 “B”，並將另外 25% 分給 “C”。 相對機率已變更。

此外，由於缺貨或種類多樣性 (也就是說，當您沒有特別的想法，而從貨架上隨機選擇其中之一)，MNL 與導數沒有簡單的方式可考慮替代項。
非參數化模型是設計來考慮替代項並對客戶行為加諸較少的限制式。 

它們引進了「排名」的概念，其中消費者表現對種類中不同產品的嚴格喜好。 因此，可以透過以喜好程度遞減方式將產品排序，以建構其購買行為的模型。 

種類最佳化問題能以營收最大化方式表示：

<center><img style="float: center;" src="assets/sku-optimization-solution-guide/assortment-optimization-problem.png" width="150"/></center>
 
- $r_i$ 表示產品 i 的營收 
- 若在排名 k 中選擇產品 i，則 $y_i^k$ 為 1，否則為 0  
- $\lambda_k$ 是客戶根據排名 k 進行選擇的機率
- 若產品包括在種類中，則 $x_i$ 為 1，否則為 0
- K 是排名數目 
- n 是產品數目

受限制式約束：

- 針對每個排名，只能有 1 個選擇
- 在排名 k 下，只有當產品 i 是種類的一部分時，才能選取它
- 若芷品 i 包括在種類中，不能選擇排名 k 中喜好程度較低的任何選項 
- 不購買也是一種選項，而且因此不能選擇排名 k 中喜好程度較低的任何選項

在此類組成中，問題可被視為[混合整數最佳化](https://en.wikipedia.org/wiki/Integer_programming)。

讓我們考慮若有 n 個產品，最大可能排名數目 (包括不選擇選項) 是階層：(n+1)!

雖然組成中的限制式允許相當有效率的可能選項修剪 (例如，選擇了最喜愛的選項並設定為 1，然後將其餘設定為 0)，您可以想像，在給定的可能替代項目數下，該實作的延展性將很重要。

### <a name="the-importance-of-data"></a>資料的重要性

我們先前曾提到過銷售資料很容易取得。 我們想要使用它們來通知我們的種類最佳化模型。 特別是，我們想要找出我們的機率分配 λ。

來是銷售點系統的銷售資料將由具有時間戳記與一組當時與該位置顯示給客戶之產品的交易組成。 從那些資料，我們可以建構一個實際資料向量，其元素 vi,m 代表將項目 i 銷售給客戶的機率 (給定種類 $S_m$)

我們也可以建構一個矩陣：

<center><img style="float: center;" src="assets/sku-optimization-solution-guide/matrix-construction.png" width="300"/></center>

在給我們銷售資料的情況下尋找我們機率分配 λ 會成為另一個最佳化問題。 我們想要尋找向量 λ 以最小化我們的銷售預估錯誤：

$$min_\lambda|\Lambda\lambda - v|$$

請注意，該計算也能以迴歸表示，因此，可以使用如多變量決策樹的模型。 

## <a name="implementation-details"></a>實作詳細資料

當我們從上面的公式減去時，最佳化模型是資料驅動且運算密集的。

Microsoft 合作夥伴 (例如 Neal Analytics) 已開發強固的架構來滿足那些情況。 請參閱 [SKU Max](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview?WT.mc_id=invopt-article-gmarchet) \(英文\)。 我們將使用那些架構做為範例並提供新的考慮事項。

- 首先，它們依賴 (1) 強固且規模可調整的資料管線來提供給模型，並使用 (2) 強固且規模可調整的執行基礎結構來執行它們。
- 第二，結果可輕鬆由規劃者透過儀表板取用。

圖 2 顯示範例架構。 它包括四個主要區塊：擷取、處理、模型與操作化。 每個區塊都包含主要程序。 「擷取」包括「資料預先處理」；「處理」包括「存放資料」功能；「模型」包括「將機器學習模型定型」功能；而「操作化」包括「存放資料」與報告選項 (例如儀表板)。

![四個部分中的架構：擷取、處理、模型與操作化。](assets/sku-optimization-solution-guide/architecture-sku-optimization.png)<center><font size="1">_圖 2：SKU 最佳化的架構 (由 Neal Analytics 提供)_ </font></center>

## <a name="the-data-pipeline"></a>資料管線

架構強調同時針對模型的定型與作業建立資料管線的重要性。 我們使用 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction?WT.mc_id=invopt-article-gmarchet) 這個可讓您執行整合工作流程的受控擷取-轉換-載入 (ETL) 服務來協調管線中的活動。

Azure Data Factory 是具有稱為「活動」之元件的受控服務，該元件可以取用和/或產生資料庫。

活動可以分割為：

- 資料移動 (例如，從來源複製到目的地)
- 資料轉換 (例如，使用 SQL 查詢來彙總，或執行預存程序)

可以排定連結活動集合的工作流程，並由資料處理站服務監視及管理。 完整工作流程稱為「管線」。

在「擷取」階段中，我們可以利用複製活動 (內建在 Data Factory 中) 來將資料從各種來源 (不論是內部部署或位於雲端) 轉換為 Azure SQL 資料倉儲。 文件中提供作法範例：

- [將資料複製到 Azure SQL DW 或從該處複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse?WT.mc_id=invopt-article-gmarchet)
- [將資料載入到 Azure SQL DW](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?WT.mc_id=invopt-article-gmarchet)

下圖顯示管線的定義。 它是由三個大小相同的連續區塊組成。 前兩個區塊是由箭頭連接以指出資料流程的資料集與活動。 第三個區塊標示為「管線」，而且只是指向前兩個區塊以指出封裝。 

 ![Azure Data Factory 概念：資料集由活動的管線取用。](assets/sku-optimization-solution-guide/azure-data-factory.png)<center><font size="1">_圖 3：Azure Data Factory 的基本概念_</font></center>

您可以在 Microsoft 的 Appsource 頁面上找到由 Neal Analytics 解決方案使用的其中一個資料格式範例。 該解決方案包括下列資料集：

- 每個店面與 SKU 組合的銷售歷史資料
- 店面與消費者記錄
- SKU 代碼與描述
- 擷取產品特色 (例如，大小、材質) 的 SKU 屬性。 這些通常都是在參數型模型中使用以在產品變化之間區分。

若資料來源未以特定格式表示，Data Factory 提供一系列的轉換活動。 

在「處理」階段中，SQL 資料倉儲是要儲存體引擎。 因此，您能以 SQL 預存程序方式表示此類轉換活動，這能以管線的一部分自動叫用。 文件提供詳細指示：

- [使用 SQL 預存程序轉換資料](https://docs.microsoft.com/azure/data-factory/transform-data-using-stored-procedure?WT.mc_id=invopt-article-gmarchet)

請注意，Data Factory 不會限制您必須使用 SQL 資料倉儲與 SQL 預存程序。 事實上，它與各種平台整合。 例如，您可以選擇使用 Databricks 並針對轉換執行 Python 指令碼。 這是一個優點，因為您可以使用一個平台來進行儲存及轉換，並在下列「模型」階段為機器學習演算法定型。

## <a name="training-the-ml-algorithm"></a>為 ML 演算法定型

有數種工具可協助您實作參數型與非參數型模型。 您的選擇視您的延展性與效能需求而定。

[Azure ML Studio](https://studio.azureml.net/?WT.mc_id=invopt-article-gmarchet) 是可用來進行原型設計的絕佳工具。 它提供簡單的方式讓您以自己的程式碼模組 (以 R 或 Python 撰寫) 建置並執行定型工作流程，或在圖形化環境中使用預先定義的 ML 元件 (例如多類別分類器、擴大的決策樹迴歸)。 它也可以讓您以 Web 服務形式發行定型的模型，以進一步供您按幾下滑鼠來取用，為您產生 REST 介面。 

不過，它可以處理的資料大小受限於 10 GB (目前)，而且可供每個元件使用的核心數目也受限於 2 個 (目前)。

下圖顯示使用中 ML Studio 的範例。 它是機器學習實驗的圖形化表示。 此圖顯示數個區塊群組。 每組區塊都代表實驗中的某個階段，而且每個區塊都連結到一或多個區塊以指出資料輸入與輸出。

![使用中 Machine Learning Studio 的範例。](assets/sku-optimization-solution-guide/ml-training-pipeline-example.png)<center><font size="1">_圖 4：使用 R 與預先建置元件的 ML 定型管線範例_</font></center>

若需要進一步調整規模但仍然想要使用 Microsoft 的一些快速、平行通用機器學習演算法實作 (例如多元邏吉斯迴歸)，您可以看看在 Azure 資料科學虛擬機器上執行的 Microsoft ML Server。

針對每個大詳資料大小 (TB 等級)，選擇儲存體與運算元素能符合下列需求的平台是合理的：

- 獨立調整規模，以限制不需要為模型定型時的成本。
- 將運算散佈在多個核心執行。
- 在「接近」儲存體的位置執行運算，以限制資料移動。

Azure [HDInsight](https://azure.microsoft.com/services/hdinsight/?WT.mc_id=invopt-article-gmarchet) 與 [Databricks](https://azure.microsoft.com/services/databricks/?WT.mc_id=invopt-article-gmarchet) 都滿足這些需求。 此外，它們都在 Azure Data Factory 編輯器內執行支援的平台。 在工作流程中整合兩者其中之一相當簡單。

ML Server 與其程式庫可部署在 HDInsight 上，但若要發揮平台功能的完整優勢，您可以使用 SparkML、Python 中的 Microsoft ML Spark 程式庫或其他專家線性程式設計解析器 (例如 TFoCS、Spark-LP 或 SolveDF) 實作想要的 ML 演算法。 

從定型程序開始，然後成為從 Data Factory 工作流程叫用適當的 pySpark 指令碼或 Nnotebook 的問題。 圖形化編輯器中完全支援此功能。 如需詳細資訊，請參閱[在 Azure Data Factory 中使用 Databricks Notebook 活動執行 Databricks Notebook](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook?WT.mc_id=invopt-article-gmarchet)。

下圖顯示透過 Azure 入口網站存取的 Data Factory 使用者介面。 它包括適用於工作流程中各程序的區塊。 

![顯示 Databricks Notebook 活動的 Data Factory 介面。](assets/sku-optimization-solution-guide/data-factory-pipeline-databricks.png)<center><font size="1">_圖 5：具有 Databricks Notebook 活動的 Data Factory 管線範例_</font></center>

此外，請注意，在我們的[庫存最佳化解決方案](https://gallery.azure.ai/Solution/Inventory-Optimization-3?WT.mc_id=invopt-article-gmarchet)中，我們建議一個容器型解析器實作，它是透過 [Azure Batch](https://azure.microsoft.com/services/batch/?WT.mc_id=invopt-article-gmarchet) 來調整規模的。 專家最佳化程式庫 (例如 [pyomo](http://www.pyomo.org/about/)) 可讓您以 Python 程式設計語言表示最佳化問題，然後叫用獨立解析器，例如 [bonmin](https://projects.coin-or.org/Bonmin) (開放原始碼版本) 或 [gurobi](http://www.gurobi.com/) (商業版本) 以尋找解決方案。

庫存最佳化文件可處理與種類最佳化不同的問題 (訂單數量)，而且 Azure 中的解析器實作同樣適用。

雖然此技術比到目前為止所建議技術的還複雜，但此技術允許最大化延展性，幾乎只受您可提供的核心數限制。

## <a name="running-the-model-operationalize"></a>執行模型 (操作化)

將模型定型之後，執行它通常需要與用於部署之基礎結構不同的基礎結構。 若要讓它可供輕鬆取用，您可以選擇將它部署為具有 REST 介面的 Web 服務。 Azure ML Studio 與 ML Server 兩者都可以將建立此類服務的程序自動化。 在 ML Server 的案例中，我們提供範本供您部署支援的基礎結構。 請參閱相關[4文件](https://docs.microsoft.com/machine-learning-server/what-is-operationalization?WT.mc_id=invopt-article-gmarchet) \(英文 \)。

下圖顯示部署的架構。 它包括執行 R 語言與 Python 之伺服器的表示。 這兩種伺服器都會與執行運算之 Web 節點的子區段通訊。 有一個大型的資料存放區會連線到運算區塊。

![ML server 部署圖表。 要執行之多個節點前的負載平衡器。](assets/sku-optimization-solution-guide/ml-server-deployment-example.png)<center><font size="1">_圖 6：ML Server 部署的範例_</font></center>


針對在 HDInsight 或 Databricks 上建立而因此相依於 Spark 環境 (程式庫、平行功能等) 的模型，您可以考慮在叢集上執行它們。 [這裡](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/spark-model-consumption?WT.mc_id=invopt-article-gmarchet)提供指導方針。

這有優點，那就是作業模型本身可透過用於評分的 Data Factory 管線活動來叫用。

若要使用容器，您可以封裝您的模型並將它們部署在 Azure Kubernetes Service 上。 原型將將要求使用 [Azure 資料科學 VM](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/?WT.mc_id=invopt-article-gmarchet)；您也必須在 VM 上安裝 Azure ML [命令列](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-service-deploy?WT.mc_id=invopt-article-gmarchet)工具。

## <a name="data-output-and-reporting"></a>資料輸出與報告

一旦部署，模型就能處理財務交易工作流程與庫存讀數，以產生最佳化種類預測。 因此產生的資料可以存放回 Azure SQL 資料倉儲以進行進一步的分析。 特別是，它將可以研究各 SKU 的過去績效，進而找出帶來最多營收的 SKU 與帶來最少營收的 SKU。 您將可以將那些資料與由模型所建議的種類比較，並評估績效與重新定型的需求。

[PowerBI](https://powerbi.microsoft.com/get-started/?&OCID=AID719832_SEM_uhlWLg3x&lnkd=Google_PowerBI_Brand&gclid=CjwKCAjw5ZPcBRBkEiwA-avvkyOLMJCrhqH8iac84aLX7EcUQIirSSqUCostzGi8y_XntJTCD73ZixoCQ4sQAvD_BwE?WT.mc_id=invopt-article-gmarchet) 提供一種方式來分析並顯示該程序中產生的資料。 

下圖顯示典型 Power BI 儀表板。 它包括顯示 SKU 庫存資訊的兩張圖表。 

![顯示超過 12 個月之結果的儀表板的範例。](assets/sku-optimization-solution-guide/sku-max-model.png)<center><font size="1">_圖 7：模型結果報告的範例 (由 Neal Analytics 提供)_ </font></center>

## <a name="security-considerations"></a>安全性考量

處理敏感性資料的解決方案包括財務記錄、庫存水準與價格資訊。 您必須保護此類敏感性資料。 若要消除對資料安全性與隱私權的顧慮，請注意：

- 您可以使用 Azure Integration Runtime 在內部部署環境中執行一些 Azure Data Factory 管線。 執行階段會執行資料移動活動到來源內部部署環境，或將資料從其中移出的活動。 它也會分配活動以在內部部署環境中執行。
  - 特別是，您可以開發自訂活動以將要傳輸到 Azure 的資料匿名化 (並在內部部署環境中執行它)。
- 提到的所有服務都支援傳輸中資料加密與待用資料加密。 若選擇將資料存放在Azure Data Lake 上，預設會啟用加密。 若您使用 Azure SQL 資料倉儲，您可以啟用透明資料加密 (TDE)。
- 提到的所有服務 (ML studio 除外) 都支援與 Azure Active Directory 整合以進行驗證及授權。 若您撰寫自己的程式碼，您必須將該整合建置到您的應用程式中。

如需有關 GDPR 的詳細資訊，請參閱我們的[合規性](https://www.microsoft.com/trustcenter?WT.mc_id=invopt-article-gmarchet) \(英文\) 頁面。

## <a name="technologies-mentioned"></a>提到的技術

- [Azure Batch](https://azure.microsoft.com/services/batch/?WT.mc_id=invopt-article-gmarchet)
- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/?&OCID=AID719825_SEM_w1MNAVjn&lnkd=Google_Azure_Brand&gclid=CjwKCAjw5ZPcBRBkEiwA-avvk4bGtyQo11KBY-u2skor1SydsSl1vrYUmhyGhhwyJhDlAYpnMmIcRRoCTfsQAvD_BwE&dclid=CMn6lvfRkd0CFRwBrQYdtIoJOA?WT.mc_id=invopt-article-gmarchet)
- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction?WT.mc_id=invopt-article-gmarchet)
- [Azure Integration Runtime](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime?WT.mc_id=invopt-article-gmarchet)
- [HDInsight](https://azure.microsoft.com/services/hdinsight/?WT.mc_id=invopt-article-gmarchet)
- [Databricks](https://azure.microsoft.com/services/databricks/?WT.mc_id=invopt-article-gmarchet)
- [Azure SQL 資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is?WT.mc_id=invopt-article-gmarchet)
- [Azure ML Studio](https://studio.azureml.net/?WT.mc_id=invopt-article-gmarchet)
- [Microsoft ML Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server?WT.mc_id=invopt-article-gmarchet)
- [Azure 資料科學 VM](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/?WT.mc_id=invopt-article-gmarchet)
- [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/?WT.mc_id=invopt-article-gmarchet)
- [Microsoft PowerBI](https://powerbi.microsoft.com/?WT.mc_id=invopt-article-gmarchet)
- [Pyomo 最佳化模型建構語言](http://www.pyomo.org/)
- [Bonmin 解析器](https://projects.coin-or.org/Bonmin)
- [適用於 Spark 的 TFoCS 解析器](https://github.com/databricks/spark-tfocs)
