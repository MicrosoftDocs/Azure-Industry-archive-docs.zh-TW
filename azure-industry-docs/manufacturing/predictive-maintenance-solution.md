---
title: 預測性維護解決方案
author: ercenk
ms.author: ercenk
ms.date: 05/03/2018
ms.topic: article
ms.service: industry
description: 有關如何為 Azure 上的製造業客戶開發預測性維護解決方案的解決方案描述。
ms.openlocfilehash: 1c7b95e2da21df46465ccaf21827ae97597206a2
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654315"
---
# <a name="predictive-maintenance-in-manufacturing-solution-guide"></a>製造業解決方案指南中的預設性維護


## <a name="introduction"></a>簡介

預設性維護使用感應器、人工智慧、與資料科學的組合來最佳化設備維護。 設備最小化維護成本並最大化運作時間的設備維護需求為製造商提供顯著價值。

資料是解決方案的核心。 資料必須有充足的失敗指示器，以及描述內容的其他方面。 它可以來自多個來源，例如感應器、機器記錄與製造應用程式記錄。

此文章提供建置預測性維護解決方案的選項。 它提供不同的觀點並參考現有的資料以讓您快速上手。 預測性維護解決方案的需求視設備、環境、程序與組織而異；我們正嘗試提供替代方法與技術來在發想符合您需求之解決方案的過程中引導您。

讓我們從預測性維護解決方案的高階元件開始。


![高階解決方案](./assets/pdm-assets/highlevelsolution.png)

在明細中，下列高階活動會發生：

1. 收集定型資料，包括失敗資料

2. 使用此定型資料將 Machine Learning (ML) 模型定型，以在給定條件組的情況下預測未來資產失敗

3. 繼續收集資料

4. 將收集的資料輸入到 ML 模型，此模型將會預測失敗，結果通常具有某種層級的信賴度 (例如：「機器有 85%  的機率會在接下來 24 小時內故障」)

5. 提供預測失敗案例

6. 規劃並針對在資料中看到的見解採取動作

## <a name="training-the-ml-model"></a>將 ML 模型定型

建置 ML 模型需要足夠、正確且完整的資料。 此外，預測性維護會面臨獨特的挑戰，其中一個最大的挑戰就是失敗資料的可用性。 失敗是相當罕見的事件 (特別是在高價設備，例如 CNC 機器或煉油廠元件) 中；因此，即使我們已收集長時間的感應器資料，我們可能也沒有足夠的失敗資料。 考慮「失敗」如何定義；到底什麼會造成失敗？ 是裝置未預期停止運作嗎？
是裝置效能降低到再也不符合預期的層級？ 失敗是否導致裁切機損毀 (由於金屬疲勞所導致的元件故障)，或其他指出失敗的指示器 (在災難發生前)？

## <a name="considering-the-data-needed-for-ml"></a>考慮 ML 所需的資料

一併考慮我們是否已擷取足夠的資料來正確地記錄這些失敗？ 在許多案例中，光是感應器資料可能不足以識別失敗。 有時候，我們可能需要外部資料以將機器狀態「標示」為失敗狀態，或需要第二個資訊來源 (例如，透過不同的系統擷取失敗案例的操作員)。 此資料可以位於外部系統 (例如 ERP、製造執行系統 (MES)、歷史學家等) 並跨越惡名昭彰的 IT/OT 裝置，因此在製造工廠中非常受歡迎，讓保護必要資料特別具挑戰性。

在本質上，預測性維護是動態問題，因此必須持續重新整理 (或重新定型) 關聯的機器學習模型。 若妥善執行，預測性維護應該可減少失敗執行個體，這很好，但會導致較少的失敗資料。 此外，影響失敗的功能可能會變更，而使得先前的機器學習模型變成無效。 我們建議定期使用錯誤狀況中的任何變更來將模型定型。

「全新」資料也表示模型中發生新狀況，與先前用來將模型定型的狀況不同。 換句話說，我們可能將失敗建構為變數函式模型 _x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>n</sub>, f(x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>n</sub>)_，但最終我們可能會發現變數 _x<sub>(n+1)</sub>,⋯,x<sub>(m+n)</sub>_ 也會影響失敗，因此我們可能必須針對 _f(x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>(m+n)</sub>)_ 修改我們的模型定型。 模型在偵測失敗時可能表現不如預期，可以建置新的模型，包括來自機器 MES 記錄以及針對下一個反覆項目的資料點。

即使沒有現代化 IoT 環境串流資料到雲端，將機器學習模型定型所需的資料可能已在您的 MES、歷史學家或其他生產系統中。 只需要準備資料，以便它可以用來將機器學習模型定型。

下圖說明將 ML 模型定型的典型工作流程。 標示為「重複」的箭頭建議這是反覆性程序，在此程序中，當有新的資料進入且情況變更時，我們將持續重新定型模型。 此迴圈必須重複的時機與方式取決於實作的特定情況，而且需要仔細的監視先前建置之模型在預測失敗方面的表現，以偵測模型是否「過時」或「表現變差」。

持續將新模型定型並部署模型也帶來管理模型的挑戰。 Microsoft 為模型的 CI/CD (持續整合與持續部署) 提供 Azure Machine Learning 模型管理服務。

![ML 模型建置階段](assets/pdm-assets/mlmodelbuildingstages.png)

Microsoft 也發行了[詳細指南](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk)，說明如何準備資料並將機器學習模型定型。 有三個典型的維護問題，以及相關的機器學習演算法：

- _針對資產，接下來 X 小時內發生失敗的機率為何？_ 答：0-100%
  - **二元分類：** 二元分類是一個機器學習方法，它使用資料來判斷項目或資料列的種類、類型或類別是否屬於兩種類別其中一種的成員。 有多種類型的分類演算法，Microsoft 已發行一組演算法，並以 [Machine Learning Studio 模組](https://docs.microsoft.com/en-us/azure/machine-learning/studio-module-reference/machine-learning-initialize-model-classification?WT.mc_id=pdmsolution-docs-ercenk)的形式提供。
- _資產的剩餘使用年限為何？_ 答：X 小時
  - **迴歸：** 迴歸是一種機器學習演算法類別，它可以在給定一組其他變數的情況下預測變數的值。 Machine Learning Studio 以[模組](https://docs.microsoft.com/en-us/azure/machine-learning/studio-module-reference/machine-learning-initialize-model-regression?WT.mc_id=pdmsolution-docs-ercenk)的形式包括一組迴歸演算法。
    - **長期短期記憶 (LSTM)：** [LSTM](http://colah.github.io/posts/2015-08-Understanding-LSTMs/?WT.mc_id=pdmsolution-docs-ercenk) 網路是一種深度類神經網路 (DNN)。 DNN 的靈感來自建構大腦中個別神經元行為的模型。 Microsoft 已發行[逐步指南](https://docs.microsoft.com/en-us/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk)，說明如何針對預測性維護使用 LSTM
- _哪些資產需要最緊急的維護？_ 答：資產 X
  - **多元分類：** 多元分類是一個機器學習方法，它使用資料來判斷項目或資料列的種類、類型或類別是否屬於兩種類別以上其中一種的成員。

再提一次，帶入資料可能表示利用多種通道，先大量將它初始化，然後繼續接收用於預測失敗的串流資料，並針對後續的模型建置作業使用它。

## <a name="bringing-data-to-azure"></a>將資料帶入 Azure

Microsoft Azure 提供數種服務可讓您內嵌即存放資料。 我們建議使用批次方法來將資料傳輸到 Azure (若資料還不在 Azure 中)。 若您可以將您的資料匯出為已知格式的檔案 (例如 csv、json、xml 等)，這些是好的選項。 您也可以選擇在上傳前先將資料壓縮，然後進一步在雲端中處理它們。

- 使用 [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy?WT.mc_id=pdmsolution-docs-ercenk) 上傳到 Blob 儲存體 (Windows 與 Linux)

- 在 Linux 上[將 Blob 儲存體掛接](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-how-to-mount-container-linux?WT.mc_id=pdmsolution-docs-ercenk)為檔案系統

- 若資料大小比較大，且需要太長的時間上傳，請使用[匯入/匯出服務](https://docs.microsoft.com/en-us/azure/storage/common/storage-import-export-service?WT.mc_id=pdmsolution-docs-ercenk)

- 在 Windows、Linux 與 MacOS 上[掛接 Azure 檔案](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-windows?WT.mc_id=pdmsolution-docs-ercenk)共用

若資料位於 SQL Server 資料庫中，您也可以使用 [Data Migration Assistant](https://docs.microsoft.com/en-us/sql/dma/dma-overview?WT.mc_id=pdmsolution-docs-ercenk) 將資料上傳到 Azure SQL Database 中。

Azure 平台上有數種工具與服務可用於擷取、轉換及上傳 (ETL) 作業。 最著名的服務是 [Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/?WT.mc_id=pdmsolution-docs-ercenk) ，它提供可用於操控資要的完整功能集。 許多可從 Azure 透過開放原始碼程式庫取得的 ML 服務中也提供可用於操控資料的其他選項。

至於將 ML 模型定型，Microsoft Azure 提供許多選項，它們全都可以在不同的組合中使用。

- [Azure Machine Learning  服務](https://docs.microsoft.com/en-us/azure/machine-learning/preview/?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure Machine Learning Studio](https://docs.microsoft.com/en-us/azure/machine-learning/studio/?WT.mc_id=pdmsolution-docs-ercenk)

- [資料科學虛擬機器](https://docs.microsoft.com/en-us/azure/machine-learning/data-science-virtual-machine/?WT.mc_id=pdmsolution-docs-ercenk)

- [HDInsight 中的 Spark MLLib](https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-machine-learning-mllib-ipython?WT.mc_id=pdmsolution-docs-ercenk)

- [Batch AI 定型服務](https://docs.microsoft.com/en-us/azure/batch-ai/?WT.mc_id=pdmsolution-docs-ercenk)

決定要使用哪個工具取決於作業的複雜度、小組的經驗，以及資料的大小。

除了雲端服務成本之外，雲端解決方案中的成本方程式還包括許多變數，例如額外的工程、系統管理與資料傳輸成本。在評估成本時使用這些變數，然後制訂明智的決策。 服務並不是構成總成本方程式的唯一要素。

設計資料分析與模型發行程序是複雜的主題，而且視使用的技術而異。 那些主題不在此文章的範圍內。 有一系列的文章說明該程序，而且我們提供可用來產生該模型的 Azure 服務。 Microsoft 也提供系統性方法，可讓您用於建置解決方案以讓資料科學小組有效率地在資料生命週期內共同作業。

Microsoft 的 [Machine Learning 文件](https://docs.microsoft.com/en-us/azure/machine-learning?WT.mc_id=pdmsolution-docs-ercenk)是探索建置 ML 與 AI 模型並將其部署到雲端並進行管理的好起點。

Microsoft Azure 平台提供豐富的選擇，供您大規模處理資料並建置 ML 模型。 雲端平台幾乎無窮盡的可用性、可調整的運算與儲存體能力，讓您可以建置 ML 與 AI 模型。 因此，使用 Azure 服務來建置模型是實作此資料流程最符合邏輯的選項。

## <a name="using-the-model"></a>使用模型

一旦我們擁有 ML 模型，我們就需要一個機制來取用它 (或「使用」它)，以預測設備維護需求。 從設備收到此資料之後，它會在一個處理層中移動，以預測未來的失敗並提供可供維護小組採取動作的各種方式。

![使用模型](assets/pdm-assets/usingthemodel.png)

您可以在線上透過串流感應器資料到解決方案，或定期將感應器資料以離線方式匯入到解決方案中，以內嵌資料。

Microsoft Azure 平台提供各種服務供您內嵌、處理及存放資料，例如：

- [Azure 事件中樞](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-what-is-event-hubs?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure 服務匯流排](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure IoT 中樞](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-what-is-iot-hub?WT.mc_id=pdmsolution-docs-ercenk)

- [Apache Kafka for   HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=pdmsolution-docs-ercenk)

不像建置 ML 模型的程序，取用它不需要許多運算資源。 視您的需求而定，模型可以部署到雲端中的服務，或部署在近端內部部署環境中。

有兩個可用於執行 ML 模型的替代位置，亦即本機或僅限雲端。

## <a name="local-execution"></a>本機執行

ML 模型會在本機被取用，同時資料會傳送到雲端待內嵌，儲存及進一步處理。 此選項也非常適用於早期偵測非常重要的案例。

![本機執行](assets/pdm-assets/localandcloud.png)

## <a name="cloud-execution"></a>雲端執行

ML 模型的內嵌、處理及儲存以及執行可在 Azure 雲端中發生。 此選項可能較適用於在多個租用戶或地理位置 (且延遲不嚴重) 中共用 ML 模組執行結果的案例。 您可以從本機新增一個選擇性元件 (通常稱為「邊緣閘道」)，以依照稱為 [「大使」模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/ambassador?WT.mc_id=pdmsolution-docs-ercenk)的模式執行一些工作 (例如資料彙總與投影、串流分析等)。

在 Azure 上使用模型的方式有數種。 [Azure Machine Learning Web 服務](https://docs.microsoft.com/en-us/azure/machine-learning/studio/consume-web-services?WT.mc_id=pdmsolution-docs-ercenk)是最直覺化的方式，而且使用 [Azure Machine Learning Studio](https://docs.microsoft.com/en-us/azure/machine-learning/studio/what-is-ml-studio?WT.mc_id=pdmsolution-docs-ercenk) 做為建立模型的選擇。 或者，您也可以選擇 [Azure Machine Learning 模型管理](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview?WT.mc_id=pdmsolution-docs-ercenk)，它提供可用於管理模型的全方位服務集合，並提供具有驗證、負載平衡、自動相應放大與加密功能的 REST API 端點。 模型可以部署到單一機器 (例如，資料科學虛擬機器、IoT 裝置、本機 PC) 或 [Azure Container Service](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes?WT.mc_id=pdmsolution-docs-ercenk)。 一旦透過 REST API 公開模型之後，使用它的機會就無窮無盡，從客戶應用程式到企業解決方案整合。

![僅限雲端](assets/pdm-assets/cloudonly.png)

僅限雲端部署不一定表示只會有即時資料串流。 可能的策略是定期將資料從本機系統 (例如，歷史學家或 MES) 匯出並將它匯入到雲端系統，然後呈現結果。 當裝置無法直接將資料推送到系統、現有的系統已準備好收集資料，或不需要任何近乎即時資料處理時，此選項可能是一個可行的方式。 在這些案例中，不需要考慮邊緣閘道。

## <a name="predictive-maintenance-in-the-iot-context"></a>IoT 環境中的預測性維護

許多 IoT 解決方案都內嵌並存放資料做為其功能集的一部分。 此外，預測性維護解決方案通常仰賴 IoT 資料，它們可以是新增到 IoT 解決方案的自然功能。 在此環境中要強調的一點是，在現有資料中擁有已記錄失敗，以為失敗定型預測性模型的重要性。

某些使用案例需要近乎即時的資料處理。 在這些案例中，我們需要具有高度資料內嵌速率功能，且規模可調整的 IoT 解決方案。 Microsoft Azure 平台提供許多服務，這些服務能讓解決方案支援高度可調整的 IoT 需求。 Azure 平台上的 [Microsoft 的 IoT 解決方案架構](https://docs.microsoft.com/en-us/azure/iot-suite/iot-suite-what-is-azure-iot?WT.mc_id=pdmsolution-docs-ercenk) 具有三個階段上的邏輯元件：

- 裝置連線能力

- 資料處理和分析

- 展示

![IoT 解決方案架構](assets/pdm-assets/iot.png)

您可以在[線上](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf?WT.mc_id=pdmsolution-docs-ercenk)找到 Azure IoT 解決方案架構的詳細資料。
不過，由於連線到後端服務的潛在實質裝置數目，可能必須面臨獨特的挑戰。

## <a name="data-ingestion-and-stream-processing"></a>資料內嵌與串流處理

從裝置帶入資料是兩個不同服務之間的通訊問題；亦即產生資料的系統 (裝置) 與處理此資料的系統 (亦即，將 ML 模型定型、針對已定型模型執行內送資料點以預測剩餘使用年限)。

根據定義，分散式系統是由具有與彼此通訊之需求的不同元件所組成。 一個用於啟用通訊的選項可以是擁有與彼此直接對話的相關元件。 這會建立一個難以維護及調整規模的系統。 當元件數目增加時，它將會建立通訊連結的 _O(n<sup>2</sup>)_ 複雜性。 一個較好的方式是，資料張貼到通用中樞，以及從通用中樞讀取資料。

![子元件通訊](./assets/pdm-assets/subcomponentcommunication.png)

針對資料內嵌插入新元件可讓通訊的規模更容易調整。 此元件需要可調整規模、安全而且最可能更容易從全球各地存取 (透過全球分割與資料內嵌程序的協助)。 

考慮預測性維護是 IoT 解決方案的其中一個功能。 當資料透過閘道串流時，它必須被路由傳送到與預測性維護功能相關的服務。
另一個要考慮的模式是[閘道路由傳送](https://docs.microsoft.com/en-us/azure/architecture/patterns/gateway-routing?WT.mc_id=pdmsolution-docs-ercenk)。

這兩種模式都可以使用 Azure 服務、[IoT 中樞](https://azure.microsoft.com/en-us/services/iot-hub/?WT.mc_id=pdmsolution-docs-ercenk)與 [Azure 串流分析](https://azure.microsoft.com/en-us/services/stream-analytics/?WT.mc_id=pdmsolution-docs-ercenk)來實作。

## <a name="edge-and-cloud-processing-cooperation"></a>邊緣與雲端處理合作

並非所有裝置與設備都能直接且一致地存取網際網路。
有時候，您必須從通用閘道提取其資料。 例如，[MTConnect](http://www.mtconnect.org/) 代理程式只提供用於提取資料的 REST 介面。

可能有其他必須考慮的事項，例如延遲、在將裝置資料傳送到雲端 (多組織用戶共享案例) 之前先在本機將它清理的需求，以及執行在裝置資料上執行投影或彙總的需求。 [「大使」模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/ambassador?WT.mc_id=pdmsolution-docs-ercenk)是滿足這些需求的好方式。 [Microsoft Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-iot-edge-works?WT.mc_id=pdmsolution-docs-ercenk) 這個實作可以做為 [Microsoft Azure IoT 中樞](https://azure.microsoft.com/en-us/services/iot-hub/?WT.mc_id=pdmsolution-docs-ercenk)的 Proxy，並透過遠端管理功能提供本機處理功能。

常見的部署可能包括在工廠的近乎即時的警示，同時清理資料並將它張貼到雲端中的多組織用戶共享解決方案進行封存、模型定型與非時間關鍵報告。 使用 Azure IoT Edge 與 IoT Hub 的功能時， 客戶可以控制邊緣裝置上的資料篩選選項，以及與其他工廠系統互動以提供警示。

![多組織用戶共享](assets/pdm-assets/multitenant.png)

## <a name="multitenant-perspective"></a>多組織用戶共享觀點

如同稍早所述，某些製造商或協力廠商可能想要提供預測性維護服務給其客戶。 這些服務很可能會以多組織用戶共享雲端部署來提供，這會有自己的挑戰要處理：

### <a name="data-security-and-isolation"></a>資料安全性與隔離

提供服務的一方必須確定能識別來自其客戶的機密資訊並適當地保護或清理。Microsoft Azure 提供根據所使用儲存體服務來加密資料的功能。

您也必須使用已知方法 (例如個別裝置憑證、個別裝置啟用/停用、TLS 安全性、X.509 支援、IP 白名單/黑名單與共用存取原則) 來保護裝置產生並提交資料的方式。 提供服務的一方必須確定能識別來其客戶的機密資訊並適當地保護或清理。[Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-encryption?WT.mc_id=pdmsolution-docs-ercenk)、[Azure 儲存體](https://docs.microsoft.com/en-us/azure/storage/common/storage-service-encryption?WT.mc_id=pdmsolution-docs-ercenk)、[Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/database-encryption-at-rest?WT.mc_id=pdmsolution-docs-ercenk) 與 [Azure SQL Database](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?WT.mc_id=pdmsolution-docs-ercenk) 是可用來進行待用資料加密的服務範例。 解決方案提供者也應該考慮如何在相同的資源 (例如，資料庫) 或多個資源之間分割資料。 

### <a name="geographical-considerations"></a>地理位置的考量

最可能的情況是，產生資料的裝置將分散在不同的地理位置。 解決方案必須提供最接近資料來源的內嵌點。 有時候，持續連線也可能是一個問題，而且資料可能必須以大量方式內嵌，或必須有本機儲存並轉寄機制。

### <a name="scalability"></a>延展性

建置 ML 模型需要可彈性調整規模的運算資源。
解決方案提供者必須設計可有效使用運算資源的程序，並視需要使用該程序來調整解決方案規模。

### <a name="provisioning-tenants-and-secure-access"></a>佈建租用戶並保護存取

服務提供者必須設計可有效讓新租用戶上線的方法，並為其提供可自行管理其帳戶的方式。 這也是決定讓部署使用專屬或共用資源的程序。


## <a name="pillars-of-software-quality-review"></a>軟體品質要素檢閱 

複雜的系統需要與滿足功能需求不同的額外審查。 成功的雲端解決方案會將焦點放在這五大要素：延展性、可用性、復原能力、管理性與安全性。 除了五大要素之外，我們也想要帶來解決方案的成本效益。

如需詳細資訊，請參閱[軟體品質的要素](https://docs.microsoft.com/en-us/azure/architecture/guide/pillars?WT.mc_id=pdmsolution-docs-ercenk)一文。

| 要素                      |                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 延展性                 | Most Azure 服務支援垂直與水平規模調整選項。 利用 Azure 平台上資源隨選部署的優點，並透過自動化服務控制其規模 (執行個體大小與數目)。                                                                                                                                                                   |
| 可用性與復原能力 | 您可以使用許多 Azure 服務，在需要時彈性佈建運算與儲存體資源。 所有 Azure 服務都提供多種等級的 SLA，不過，解決方案必須考慮 SLA 等級並使用適當的設計原則來加以利用。                                                                                                                    |
| 管理性                  | 您可以透過不同的方式 (例如 ARM 範本、命令列工具與 PowerShell Cmdlet，以及 Azure Management API) 來部署及管理 Azure 資源。 管理 Azure 資源時請考慮建置自動化解決方案，而非搭配 UI 來使用工具。                                                                                                                                |
| 安全性                    | Azure IoT 中樞支援在 TLS 上使用對稱式與非對稱式金鑰 (X509 憑證與 TPM)。 資料存放區是使用身分識別與存取管理 (IAM) 設定來保護，而且它們也支援待用資料加密。 做為一般快速安全性檢查清單，請考慮檢閱授權、驗證、傳輸與待用加密，以及稽核機制。 |
| 成本效益              | 考慮以自動化方式在需要時佈建資源，並在它們未使用時予以捨棄。                                                                                                                                                                                                                                                                                                  |

## <a name="conclusion"></a>結論

預測性維護是一個長久以來就討論的主題。 最近在雲端平台 (例如 Microsoft Azure) 中的發展讓預測性維護的實作者能克服以往在處理資料時會面臨的許多挑戰。 由於在運算與儲存體容量方面的彈性規模調整能力，雲端平台可提供新的機會來實作預測性維護並帶來新的營收商機。 Microsoft 的 Azure 平台提供許多功能不同的服務，可協助您達成預測性維護解決方案的商業目標。

此文章提供如何收集資料並將資料模型定型的方式，並說明如何利用已定型的模型來針對先前各節中預測的結果採取動作。

## <a name="further-reading"></a>進階閱讀

1. [著重未來：停止過去的思考方式並透過 IoT 走在前端](https://blogs.microsoft.com/iot/2017/02/28/future-focused-stop-thinking-in-the-past-and-get-ahead-of-the-unexpected-with-iot-2/?WT.mc_id=pdmsolution-docs-ercenk) \(英文\)

2. [利用由 IoT 提供支援的預測性維護增加設備的可靠性](https://www.microsoft.com/en-us/internet-of-things/predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk) \(英文\)

3. [從物聯網獲取價值：如何採用預測性維護專案](http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF?WT.mc_id=pdmsolution-docs-ercenk)

4. [合作夥伴觀點：前線的預測性維護](https://blogs.microsoft.com/iot/2017/03/21/partner-perspectives-predictive-maintenance-on-the-frontlines/?WT.mc_id=pdmsolution-docs-ercenk) \(英文\)

5. [從商品化到服務化：將您的公司轉型以透過 IoT 在新的現場服務年代中佔有競爭優勢](https://blogs.microsoft.com/iot/2016/11/07/from-commodization-to-servitization-transforming-your-business-to-compete-in-the-new-age-of-field-service-with-iot/?WT.mc_id=pdmsolution-docs-ercenk) \(英文\)
