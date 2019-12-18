---
title: 從 IoT 擷取製造業見解的架構
description: 使用 Azure 服務從 IoT 資料擷取見解。
author: ercenk
ms.author: ercenk
manager: gmarchet
ms.service: industry
ms.topic: article
ms.date: 11/28/2019
ms.openlocfilehash: c08e6bbb1da47084122dae1ed6a9e1cea0b59473
ms.sourcegitcommit: db3bee67c1467884af223a48a895715afba8e08c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2019
ms.locfileid: "75005307"
---
# <a name="extracting-actionable-insights-from-iot-data"></a>從 IoT 資料擷取可操作見解

如果您負責工廠的機器，應該已意識到物聯網 (IoT) 是改善流程和結果的下一步。 第一步是在機器或工廠裝設感應器。 下一步是使用資料 - 這是本文件的物件。 本指南提供從 IoT 資料分析中擷取可操作見解所需元件的技術概觀。

IoT 分析解決方案旨在將來自一組裝置之未經處理的 IoT 資料，轉換為更適合分析的形式。 一旦資料處於可分析形式，即可實際執行分析。 分析的一些範例包括：

- 遙測資料的簡單視覺效果，例如，顯示溫度隨時間變化的橫條圖
- KPI 計算，例如，整體設備效率 (OEE)
- 機器學習服務提供的預測性分析

接著，這些分析會提供動作參考資訊的見解。 動作範圍可從傳送簡單命令到機器、調整作業參數、在另一個軟體系統上執行操作，到實作整間公司的作業改善計畫。

下圖顯示工廠機器和最終結果之間發生的一系列動作，這是呈現「使用率」的儀表板，顯示圖表和標籤 "87.5%"。

![工廠到儀表板。](assets/extracting-insights-from-iot/factory-to-dashboard.png)

為了加以說明，我們將使用簡單 KPI 的計算：機器使用率。 *機器使用率*是機器實際*生產*組件的時間百分比。 例如，如果一個班次有 8 小時，而其中 7 小時是機器生產組件的時間，則該班次的機器使用率為 87.5% (7/8 x 100)。

## <a name="approach"></a>方法

IoT 應用程式有三個元件：**事項** (或裝置)，傳送用於產生**見解**的資料或事件，其用於產生**動作**以幫助改善業務或程序。

製造工廠 (事項) 中的設備會在運作時傳送各種類型的資料。 例如，銑床機傳送進給率和溫度資料；這用於評估機器是否正在運作 (見解)，用於讓工廠以最有效率的方式運作 (動作)。 我們將逐步執行擷取資料、在儀表板中對其進行視覺化、擷取新見解，以及採取進一步的動作。
![事項到見解到動作。](assets/extracting-insights-from-iot/things-insights-actions.png)

Microsoft 已針對 IoT 應用程式發佈高階參考架構，其逐步解說各種子系統，以及這些子系統的推薦方法。
IoT 應用程式是由下列的子系統所組成：

1. 設備或內部部署邊緣閘道，它們是一種特定類型的裝置，可安全地向雲端註冊訊息來源 (裝置)。 邊緣閘道也可以將訊息從本機通訊協定轉換為另一種格式 (例如 JSON)。
2. 雲端閘道服務或中樞 (例如 [Azure IoT 中樞](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk)或 [Azure 事件中樞](/azure/event-hubs/event-hubs-about?WT.mc_id=iotinsightssoln-docs-ercenk))，可安全地擷取資料，並提供裝置管理功能。 
3. 取用串流資料的串流處理器。 處理器也可以與商務程序整合，並將資料放入儲存體。
4. 使用者介面 (儀表板的形式) 可視覺化 IoT 資料，並使裝置管理更容易。

![解決方案的架構。](assets/extracting-insights-from-iot/architecture.png)

在本文中，我們會著重在擷取見解的程序。 以下是主要步驟：

1. 存取資料，並將其處理為資料流。
2. 處理並儲存資料。
3. 視覺化或呈現資料。 

下方圖表顯示資料流、從資料來源、到轉換、擷取、處理和儲存，再到呈現，以及最後動作。

![資料流：來源到擷取到呈現，再到動作。](assets/extracting-insights-from-iot/data-flow.png)

## <a name="converting-the-data-to-a-stream"></a>將資料轉換至串流

IoT 資料是時間序列資料：「事項」中的值會在一段時間內變得更有意義。 工廠設備隨著時間運作，並在此期間發生事件。 如果廠房資料未傳送至資料擷取服務，例如 [Azure IoT 中樞](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk)，我們必須定期輪詢其存放區的資料 - 例如，製造執行系統 (MES)，或 HTTP 端點)，並將資料傳送至擷取服務。  
若要將資料轉換至串流，我們通常會：

1. 存取資料來源。
2. 轉換及擴充資料。
3. 將資料貼到可擷取串流資料的端點。

我們在此不詳述資料來源的存取權，因為存取權取決於資料所在位置，而且變數太多。

## <a name="transforming-and-enriching-the-data"></a>轉換及擴充資料

未經處理資料通常必須進行轉換作業，將其標準化，並備妥供擷取使用。  特定的轉換取決於使用的分析類型而不同。  資料轉換的一些常見範例是時間序列資料，其中可能缺少測量，並且需要輸入測量，或者需要合理化不同機器的時幅。  我們希望有加上時間戳記的資料記錄 (如果來源包含時間戳記) 和成對的名稱和數值。 通常，資料會有階層式格式，且必須轉換成單層式結構。

下圖顯示具有不規則設定檔的階層式資料結構，轉換成標準化的資料行和資料列格式 (區塊)。

![資料圖形從階層式到單層式。](assets/extracting-insights-from-iot/hierarchy-to-flat.png)

通常，無法從網際網路存取資料。 常見的模式是使用邊緣閘道，將資料從工廠推送到擷取點。 [Azure IoT Edge](/azure/iot-edge?WT.mc_id=iotinsightssoln-docs-ercenk) 是建置在 IoT 中樞之外的服務。 IoT Edge 裝置作為閘道有三種[模式](/azure/iot-edge/iot-edge-as-gateway?WT.mc_id=iotinsightssoln-docs-ercenk)：透明閘道、通訊協定轉譯，以及身分識別轉譯。

如果資料在外部可用，並且可從網際網路存取，則可以使用多個 Azure 服務以存取、轉換和擴充資料。 這些選項包括：

- 部署在各種 Azure 計算服務中的自訂程式碼，例如 [App Service](/azure/app-service/?WT.mc_id=iotinsightssoln-docs-ercenk)、[Azure Kubernetes Service](/azure/aks/?WT.mc_id=iotinsightssoln-docs-ercenk) (AKS)、[Container Instances](/azure/container-instances/?WT.mc_id=iotinsightssoln-docs-ercenk)，或 [Service Fabric](/azure/service-fabric/service-fabric-overview?WT.mc_id=iotinsightssoln-docs-ercenk)。
- [Azure Logic Apps](/azure/logic-apps/?WT.mc_id=iotinsightssoln-docs-ercenk)
- [Azure Data Factory 中的管線及活動](/azure/data-factory/copy-activity-overview/?WT.mc_id=iotinsightssoln-docs-ercenk)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [BizTalk 服務](https://azure.microsoft.com/services/biztalk-services/)

上述每項服務都有各自的優點和成本，取決於具體情況而定。 例如，Logic Apps 提供[轉換 XML 文件](/azure/logic-apps/logic-apps-enterprise-integration-transform?WT.mc_id=iotinsightssoln-docs-ercenk)的方法。 但是，資料可能是過於複雜的 XML 文件，因此，開發一個大型 XSLT 指令碼以轉換資料可能不切實際。 在這種情況下，可以使用來自不同 Azure 服務的多個微服務，來開發混合式解決方案。 例如，在 Azure Logic Apps 中實作的微服務可以輪詢 HTTP 端點、暫時儲存未經處理的結果，並通知另一個微服務。 另一個轉換訊息的微服務可自訂裝載在 [Azure Functions 主機](https://github.com/Azure/azure-functions-host)上的程式碼。  

![Https 會輪詢資料，並透過函式處理。](assets/extracting-insights-from-iot/poll-logic-process.png)

或者，您可以選擇由 Azure Data Factory 協調的工作流程，其中一系列活動會執行轉換。 如需可用活動類型的詳細資料，請參閱 [Azure Data Factory 中的管線與活動](/azure/data-factory/concepts-pipelines-activities)。
訊息可以在接收時加上時間戳記，或者可以包含時間戳記，以便我們重建多個測量值的時間序列。 因此，可忽略的擷取延遲和高輸送量是保證資訊完整性和最終回應時效性的基礎。 若要將延遲降至最低，我們會將時間戳記標準化為盡可能接近工廠。

## <a name="ingesting-the-data-stream"></a>擷取資料流

若要以串流分析資料，我們可以根據時間範圍，對資料進行查詢，以識別模式和關聯性。 Azure 平台上有各種服務，可利用高輸送量擷取資料。
在以下服務之間進行選擇，取決於專案的需求，例如裝置管理、通訊協定支援、延展性、團隊對程式設計模型的偏好等。例如，團隊可能會先前經驗而優先使用 Kafka，或者需要針對解決方案擁有 Kafka 訊息代理程式。 或者，另一種情況是，專案可能需要資料擷取系統，以利用 [IoT 中樞裝置佈建服務的 TPM 金鑰證明](/azure/iot-dps/?WT.mc_id=iotinsightssoln-docs-ercenk)來保護裝置對擷取點的存取權。

- [Azure IoT 中樞](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk)是 IoT 應用程式和裝置之間的雙向通訊中樞。 它是一種可擴充的服務，藉由提供安全通訊、訊息路由、與其他 Azure 服務的整合，以及用於控制和設定裝置的管理功能，實現全功能的 IoT 解決方案。

- [Azure 事件中樞](/azure/event-hubs/event-hubs-about?WT.mc_id=iotinsightssoln-docs-ercenk)是大規模的僅擷取服務，以極高的輸送率從並行來源收集遙測資料。
- [HDInsight 上的 Apache Kafka](/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=iotinsightssoln-docs-ercenk) 是裝載 [Apache Kafka](https://kafka.apache.org/) 的受控服務。 Apache Kafka 是開放原始碼分散式串流平台，其同時提供訊息代理程式功能。 託管服務的 Kafka 運作時間服務等級協定 (SLA) 為 99.9%。

## <a name="processing-and-storing-the-data"></a>處理並儲存資料

IoT 應用程式帶來挑戰，因為它們是事件驅動的系統，同時需要保留歷史資料，並在該資料上運作。 傳入資料是附加的資料類型，而且可能會變大。 需要將資料保留更長時間，主要是基於以下原因：封存、批次分析，以及建置機器學習 (ML) 模型。 相反地，如果值超過或低於閾值，事件資料流對於近乎即時地分析以偵測異常、識別累積時間範圍上的模式，或觸發警報，是至關重要的。
Microsoft 的 Azure IoT 參考架構為使用 Lambda 架構 之 IoT 解決方案的裝置到雲端訊息和事件，提供建議的資料流程。 Lambda 架構可以分析近乎即時的串流資料，以及封存資料，使其成為處理傳入資料的最佳選擇。

## <a name="lambda-architecture"></a>Lambda 架構

Lambda 架構會藉由建立兩個資料流程路徑，來解決此問題。 進入系統的所有資料會經過這兩個路徑：

- 批次層 (非經常性路徑) 會以原始格式儲存所有傳入資料，並對資料執行批次處理。 此處理的結果會儲存為批次檢視。 它是執行複雜分析的緩慢處理管線，例如，來自多個來源以及更長時間內 (數小時，數天或更長時間) 的組合資料，並產生新的資訊，例如報告、機器學習模型等。
- 速度層 (經常性路徑) 會即時分析資料。 這一層的設計目的是要犧牲精確度來實現低延遲。 它是更快的處理管線，可以封存和顯示傳入訊息，並分析這些記錄，產生短期的重要資訊和警示等動作。
- 批次層會饋送至「服務層」，其可回應查詢。 批次層會為批次檢視編制索引，以便能有效率地查詢。 速度層會根據最新的資料，使用累加式更新來更新服務層。

下圖顯示代表轉換階段的五個區塊。 第一個區塊是資料流，其並行饋送速度層和批次層。 這兩個層級會饋送服務層，速度層和服務層都會饋送分析用戶端。
![Lambda 架構。](assets/extracting-insights-from-iot/lambda-schematic.png)

 Azure 平台提供各種服務，可用於實作架構。 下圖顯示如何對應這些服務，以進行實作。 該圖顯示轉換的五個階段，每個階段都包含相關的 Azure 技術。 深色方塊代表執行這些工作之多個選項的可用性。

![Lambda 架構。](assets/extracting-insights-from-iot/lambda-architecture-all.png)

上一節「擷取資料流」中，介紹速度層上資料擷取服務的選項。

[HDInsight 上的 Apache Kafka](/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=iotinsightssoln-docs-ercenk) 可以是針對資料擷取服務和串流處理實作資料流的服務選項。

如果您使用事件中樞進行資料擷取服務，請使用 [Azure 串流分析](/azure/stream-analytics?WT.mc_id=iotinsightssoln-docs-ercenk) (ASA)。 Azure 串流分析是事件處理引擎，可讓您檢查來自裝置的大量資料流。 傳入的資料可能來自裝置、感應器、網站、社交媒體摘要和應用程式等。 它也支援從資料流擷取資訊，識別模式和關聯性。

要進行串流分析查詢必須先有串流資料的來源，這包括了擷取於 Azure 事件中樞或Azure IoT 中樞的資料，以及來自 Azure Blob 儲存體等資料存放區的資料。 若要檢查資料流，請建立串流分析作業，以指定串流資料的輸入來源。 此作業也會指定轉換查詢，以定義如何尋找資料、模式或關聯性。 轉換查詢會利用類似 SQL 的查詢語言，以便篩選、排序、彙總和聯結一段時間內的串流資料。

## <a name="warm-path"></a>經常性路徑

此文件的範例案例是機器使用率 KPI (在指南的開頭有介紹)。 我們可以選擇以機率解譯資料，並假設如果機器正在傳送資料，則表示正使用該機器。 不過，機器可能會在未真正產生任何東西的情況下傳送資料 (例如，可能處於閒置狀態，或正在維護中)。 這突顯出嘗試從 IoT 資料中擷取見解時所面臨的一項常見挑戰：您正在尋找的資料不在您取得的資料中。 所以，在本例中，我們並未得到能清楚、明確地告訴我們機器是否正在生產的資料。  因此，我們需要將獲得的資料與其他資料來源結合，以推斷使用率，並套用規則來確定機器是否正在生產。 此外，這些規則可能會因公司而異，因其對「生產」可能有不同的解釋。 經常性路徑是在資料流經系統時進行分析。 我們會近乎即時處理此資料流，將其儲存到經常性儲存，然後將其推送到分析用戶端。

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 事件中樞非常適合作為資料流程之經常性路徑中的第一步。

下圖顯示速度層階段。 它是由事件中樞、串流分析執行個體，以及經常性儲存的資料存放區所組成。

![Lambda 架構：速度層反白顯示。](assets/extracting-insights-from-iot/lambda-2.png)
  
Azure 平台提供許多選項來處理事件中樞上的事件，不過，我們建議使用串流分析。 串流分析也可以將資料推送至 Power BI 服務，以視覺化串流資料。

串流分析可執行大規模的複雜分析，例如，輪轉/滑動/跳動視窗、資料流彙總，以及外部資料來源聯結。 對於更複雜的處理，可以藉由串聯事件中樞、串流分析作業，以及 Azure Functions 的多個執行個體以擴充效能，如下圖所示。

![事件中樞到分析到 Power BI。](assets/extracting-insights-from-iot/event-hubs-to-power-bi.png)
  
可使用 Azure 平台上的各種服務 (例如 Azure SQL Database) 實作經常性儲存。 建議使用 [Azure Cosmos DB](/azure/cosmos-db/introduction?WT.mc_id=iotinsightssoln-docs-ercenk)。 它是 Microsoft 全球發行的多模型資料庫。 適用於資料集，其可從彈性的、不使用結構描述、自動編制索引，以及豐富的查詢介面中受益。 Cosmos DB 允許多區域的讀取/寫入，除了自動容錯移轉以外，也支援手動容錯移轉。 此外，Cosmos DB 可讓使用者在其資料上設定存留時間 (TTL)，這會使舊資料自動到期。 建議使用此功能來控制記錄存留在資料庫中的時間，進而控制資料庫的大小。

Cosmos DB 的價格是根據使用的儲存體和佈建的[要求單位](/azure/cosmos-db/request-units)。 Cosmos DB 適用於不需要涉及大型資料集的查詢案例，因為這些查詢需要比基本查詢 (例如裝置的最後一個事件) 更多的要求單位。  

[Microsoft Power BI](/power-bi/power-bi-overview?WT.mc_id=iotinsightssoln-docs-ercenk) 是軟體服務、應用程式和連接器的集合，它們協同運作，將不相關的資料來源轉換為一致、視覺逼真，且互動式的見解。 Power BI 可協助您掌握重要資訊。 您可以利用 [Power BI 中的即時串流](/power-bi/service-real-time-streaming?WT.mc_id=iotinsightssoln-docs-ercenk)，將資料推送至服務。 此即時串流可作為 Power BI 儀表板上各種視覺效果的即時串流資料來源。

## <a name="cold-path"></a>非經常性路徑

經常性路徑是隨著時間為了探索模式而發生串流處理的地方。 不過，我們也想透過不同的樞軸和彙總，計算一段時間內的使用率，例如機器、生產線、工廠、生產部件等。 我們希望將這些結果與經常性路徑結果合併，以向使用者呈現統一的檢視。 非經常性路徑包括批次層和服務層。 該組合提供長期的系統檢視。

非經常性路徑包含解決方案的長期資料存放區。 它也包含批次層，該層會建立預先計算的彙總檢視，以便在很長一段時間內提供快速查詢回應。 Azure 平台上此層可用的技術選項非常多樣化。

![Lambda 架構：批次層反白顯示。](assets/extracting-insights-from-iot/lambda-3.png)
  
[Azure 時間序列深入解析](/azure/time-series-insights/?WT.mc_id=iotinsightssoln-docs-ercenk) (TSI) 是時間序列資料的分析、儲存體及視覺效果服務。 它提供類似 SQL 的篩選和彙總，減少了對使用者定義函式的需求。 TSI 可以接收來自事件中樞、IoT 中樞或 Azure Blob 儲存體的資料。 TSI 中的所有資料都會儲存在記憶體內部和 SSD 中，以確保資料一律可用於互動式分析。 例如，數百萬個事件的一般彙總會以毫秒為單位傳回。 它也提供視覺效果，例如不同時間序列的重疊、儀表板比較、可存取的表格式檢視，以及熱度圖。 TSI 的主要功能包括：

- 內建的視覺效果服務，適用於無需立即報告資料的解決方案。 TSI 有約略 30-60 秒的查詢資料記錄延遲時間。 
- 可查詢大型資料集。
- 使用者不限數量皆可進行無限量的查詢，且無需額外費用。

TSI 的最大保留期為 400 天，最大儲存限制為 3TB。 如果您需要較長的保留期範圍或更多容量，請使用非經常性儲存體資料庫 (視需要交換資料至 TSI 查詢)。

IoT 應用程式的非經常性儲存體一定會隨著時間增長。 這是資料長期儲存的位置，並在批次檢視中彙總，以供分析使用。 ML 模型的資料也會儲存在這裡。 建議針對非經常性儲存體使用 [Azure 儲存體](/azure/storage/?WT.mc_id=iotinsightssoln-docs-ercenk)。 它是 Microsoft 管理的服務，提供高度可用、安全、耐用、可擴充和備援的雲端儲存體。 Azure 儲存體包含 Azure Blob (物件)、Azure Data Lake Storage Gen2、Azure 檔案服務、Azure 佇列和 Azure 資料表。 非經常性儲存體可以是 Blob、Data Lake Storage Gen2，或 Azure 表格，或上述的組合。

[Azure 表格儲存體](/azure/cosmos-db/table-storage-overview?WT.mc_id=iotinsightssoln-docs-ercenk)是可將結構化的 NoSQL 資料儲存在雲端中的服務，並提供具有無結構描述設計的索引鍵/屬性存放區。 由於表格儲存體並無結構描述，因此可輕易隨著應用程式發展需求改寫資料。 相較於類似資料量的傳統 SQL，對許多類型的應用程式而言，表格儲存體資料可快速存取且符合成本效益，通常可降低成本。 我們將一個資料表作為樣本，並將另一個資料表用於從資料流接收的事件。 資料分割索引鍵的設計是一個特別重要的概念；兩個表格皆使用事件或樣本上的時間戳記 (小時)。 如需詳細資訊，請參閱 [了解表格服務資料模型](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?WT.mc_id=iotinsightssoln-docs-ercenk)。

若要儲存大量非結構化資料，例如 JSON，或包含 IoT 應用程式接收之未經處理資料的 XML 文件，[Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=iotinsightssoln-docs-ercenk)、[Azure 檔案](/azure/storage/files/storage-files-introduction?WT.mc_id=iotinsightssoln-docs-ercenk)，或 [Azure Data Lake Storage Gen2](/azure/storage/data-lake-storage/introduction?WT.mc_id=iotinsightssoln-docs-ercenk) 是最適合的選項。

使用者可從世界各地透過 HTTP 或 HTTPS 安全地存取 Azure Blob 儲存體。 必須使用服務使用的其中一種[授權機制](/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json?WT.mc_id=iotinsightssoln-docs-ercenk)，授權存取 Blob 儲存體。 此服務提供多個複寫[選項](/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json?WT.mc_id=iotinsightssoln-docs-ercenk)：本地備援、區域備援、異地備援，以及讀取存取異地備援。 另外還有三個[存取層](/azure/storage/blobs/storage-blob-storage-tiers?WT.mc_id=iotinsightssoln-docs-ercenk)，可提供最具成本效益的解決方案。

一旦資料位於非經常性儲存體之後，必須建立 Lambda 架構之服務層上的批次檢視。 [Azure Data Factory](/azure/data-factory/introduction?WT.mc_id=iotinsightssoln-docs-ercenk) 是在服務層上建立批次檢視的絕佳解決方案。 這是一項雲端式受控資料整合服務，可讓您在雲端建立資料驅動工作流程，以便協調及自動進行資料移動和資料轉換。 使用 Azure Data Factory，可以建立並排程[資料驅動的工作流程](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=iotinsightssoln-docs-ercenk) (稱為管線)，它可以從不同的資料存放區擷取資料。 使用服務 (例如，[Azure HDInsight Hadoop](/azure/hdinsight/?WT.mc_id=iotinsightssoln-docs-ercenk)[Spark](/azure/hdinsight/?WT.mc_id=iotinsightssoln-docs-ercenk) 和 [Azure Databricks](/azure/azure-databricks/?WT.mc_id=iotinsightssoln-docs-ercenk)) 可以處理和轉換資料。 這可讓您建置機器學習模型，並將其與分析用戶端搭配使用。

例如，如下圖所示，Data Factory 管線會從主要資料存放區中讀取資料。 其中一個管線會總結並彙總資料以填入 Azure 資料倉儲的執行個體。 Data Factory 管線也包含 [Azure Databricks 筆記本活動](/azure/data-factory/transform-data-using-databricks-notebook?WT.mc_id=iotinsightssoln-docs-ercenk)，用於建置機器學習服務模型。

![Lambda 架構：批次層反白顯示。](assets/extracting-insights-from-iot/master-data-to-ml-analytics.png)
  
[Azure SQL Database](/azure/sql-database/?WT.mc_id=iotinsightssoln-docs-ercenk) 或 [Azure SQL 資料倉儲](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is?WT.mc_id=iotinsightssoln-docs-ercenk)是主控批次檢視的最佳選項。 這些服務可以為主要資料提供預先計算和彙總的檢視。 

Azure SQL Database (SQL DB) 是關聯式資料庫即服務，它是根據最新版本的 Microsoft SQL Server 資料庫引擎而建置。 SQL DB 是高效能、可靠且安全的資料庫，您可以使用該資料庫來建置資料驅動應用程式和網站。 作為 Azure 服務，無需管理其基礎結構。 隨著資料量不斷增加，解決方案可以開始使用彙總和儲存資料的技術，以加快查詢速度。 預先計算彙總是眾所皆知的技術，特別適用於僅附加資料。 它對於管理成本也相當有幫助。

Azure SQL 資料倉儲提供許多其他功能，在某些情況下非常有用。 它是雲端式企業資料倉儲，利用大量平行處理，快速地在數以 PB 計的資料中執行複雜的查詢。 如果您需要保留 PB 規模的資料，並且希望快速執行查詢，我們建議使用 SQL 資料倉儲。

## <a name="visualizing-the-data"></a>將資料視覺化

在此層中，我們希望合併兩個資料管線 (經常性路徑和非經常性路徑)，以呈現資料的緊密檢視。 在此範例中，我們會使用多個計量來推論經常性和非經常性路徑上的機器的使用量。 在分析階段中，我們提供從這些路徑合併資料的視覺效果。

![Lambda 架構：分析用戶端層級反白顯示。](assets/extracting-insights-from-iot/lambda-4.png)

[Microsoft Power BI](/power-bi/?WT.mc_id=iotinsightssoln-docs-ercenk) 和 [Azure 時間序列深入解析](/azure/time-series-insights/?WT.mc_id=iotinsightssoln-docs-ercenk)提供現成的資料視覺效果。 Power BI 是一個商務分析解決方案，可讓您將資料視覺化，並在整個組織共用見解，或將其嵌入您的應用程式或網站。 [Power BI Desktop](https://powerbi.microsoft.com/desktop/?WT.mc_id=iotinsightssoln-docs-ercenk) 是免費且功能強大的工具，用於模型化報表，及其基礎資料來源。  內嵌 Power BI 視覺效果的應用程式使用桌上型電腦工具所撰寫，並裝載於 Power BI 服務上的報表。

時間序列深入解析具有資料總管，以視覺化並查詢資料以及 REST 查詢 API。 此外，它也公開 JavaScript 控制項程式庫，可讓您將 TSI 驅動的圖表嵌入自訂應用程式中。 以下是 TSI 上的基本熱度圖檢視，用於顯示接近現場機器使用率的傳入資料，只需查看觀察到的樣品數量即可。

![Lambda 架構：批次層反白顯示。](assets/extracting-insights-from-iot/client-screen.png)

如果您需要以瀏覽器為基礎的使用者介面來彙總多個來源的資料，TSI 和 Power BI 服務都允許嵌入視覺效果控制項。 兩者都提供 REST API ([Power BI Rest API](/rest/api/power-bi/?WT.mc_id=iotinsightssoln-docs-ercenk)[TSI REST API](/rest/api/time-series-insights/time-series-insights-reference-queryapi?WT.mc_id=iotinsightssoln-docs-ercenk)) 和 JavaScript SDKs ([Power BI JavaScript SDK](https://github.com/Microsoft/PowerBI-JavaScript?WT.mc_id=iotinsightssoln-docs-ercenk)、[TSI JavaScript SDK](/azure/time-series-insights/tutorial-explore-js-client-lib?WT.mc_id=iotinsightssoln-docs-ercenk))，允許大規模的自訂。

## <a name="next-steps"></a>後續步驟

我們介紹許多概念，並希望提供讀者一套起點，以深入了解，並將這些技術應用於他們自己的需求。 以下是我們認為對此用途相當有用的一些教學課程。

- 將資料轉換至串流
  - [建立定期執行的 Logic App](/azure/logic-apps/tutorial-build-schedule-recurring-logic-app-workflow?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [Logic Apps 上資料作業的程式碼範例](/azure/logic-apps/logic-apps-data-operations-code-samples?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [在容器中執行 Azure Functions](/azure/azure-functions/functions-create-function-linux-custom-image?WT.mc_id=iotinsightssoln-docs-ercenk)以裝載您的 Azure Function，這涵蓋在多項課程中。 包括使用自訂映像在 Linux 上建立函式、在任何平台上執行函式，以及Azure Functions 執行階段的 Docker 映像
  - [在 Azure Functions 使用各種繫結](/azure/azure-functions/functions-triggers-bindings?WT.mc_id=iotinsightssoln-docs-ercenk)

- 最忙碌路徑
  - 端到端教學課程示範如何使用事件中樞、Azure 串流分析，以及 Power BI。 請參閱[教學課程：在傳送至 Azure 事件中樞的即時事件中視覺化資料異常](/azure/event-hubs/event-hubs-tutorial-visualize-anomalies?WT.mc_id=iotinsightssoln-docs-ercenk)，以及[建立串流分析作業來分析通話資料，並且在 Power BI 儀表板中將結果視覺化](/azure/stream-analytics/stream-analytics-manage-job?WT.mc_id=iotinsightssoln-docs-ercenk)。
  -[將 Azure Cosmos DB 與與 .NET 搭配使用](/azure/cosmos-db/sql-api-get-started?WT.mc_id=iotinsightssoln-docs-ercenk)
- 非經常性路徑
  - [使用 Azure Data Factory 中的 Spark 活動來轉換雲端中的資料](/azure/data-factory/tutorial-transform-data-spark-portal?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [教學課程：建立 Azure 時間序列深入解析環境](/azure/time-series-insights/tutorial-create-populate-tsi-environment?WT.mc_id=iotinsightssoln-docs-ercenk)
- 分析用戶端
  - [了解 Power BI](/power-bi/guided-learning/?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [建立時間序列深入解析 SPA](/azure/time-series-insights/tutorial-create-tsi-sample-spa?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [探索時間序列深入解析 JAVA 指令碼用戶端程式庫](/azure/time-series-insights/tutorial-explore-js-client-lib?WT.mc_id=iotinsightssoln-docs-ercenk)
  - 請參閱 [TSI 示範](https://insights.timeseries.azure.com/demo)和 [Power BI 示範](https://microsoft.github.io/PowerBI-JavaScript/demo/v2-demo/index.html).

## <a name="appendix-pillars-of-software-quality-posq"></a>附錄：軟體品質的要素 (PoSQ)

成功的雲端應用程式著重於[軟體品質的這幾項支柱](/azure/architecture/guide/pillars?WT.mc_id=iotinsightssoln-docs-ercenk)：延展性、可用性、復原、管理和安全性。 在本節中，我們將視需要簡要討論每個元件的要素。 我們未涵蓋可用性、彈性、管理和 DevOps，因為大部分是在實作層級處理，而且我們想提到的是 Azure 平台提供廣泛的方法，透過 API、工具、診斷和記錄來達成這些目標。 除了上述要件，我們也會提及成本效益。

讓我們快速檢閱這些要素：

- **延展性**是系統處理負載增加的能力。 應用程式有兩種主要的調整方式。 垂直調整 (「相應增加」) 代表增加資源的容量，例如使用較大的 VM 大小。 水平調整 (「相應放大」) 則會為資源 (例如 VM 或資料庫複本) 新增執行個體。 延展性要素也包括效能和處理負載的能力。
- **可用性**是系統正常運作並執行作業的時間比例。 通常以運作時間百分比加以測量。 應用程式錯誤、基礎結構問題和系統負載全都有可能會降低可用性。 Microsoft Azure 服務的服務等級協定已發佈，並且可在[服務等級協定](https://azure.microsoft.com/support/legal/sla/?WT.mc_id=iotinsightssoln-docs-ercenk)。 可用性是系統層級唯一有意義的計量。 個別元件有助於系統的整體可用性。
- **災害復原**是指系統從失敗中復原並繼續運作的能力。 災害復原的目標是使應用程式在發生失敗後，能夠恢復到完全正常運作的狀態。 災害復原與可用性緊密相關。
- **管理性和 DevOps**。 此要素涵蓋讓應用程式在生產環境中執行的作業流程。 部署必須可靠且可預測。 應將部署程序自動化，以避免發生人為錯誤。 部署程序應迅速且成為例行工作，以免拖累新功能或錯誤修正的發行速度。 同樣重要的是，您必須能夠在更新發生問題時快速復原或向前復原。
- **安全性**應該是解決方案之整個生命週期 (從設計和實作到部署與作業) 的主要重點。 身分識別、保護您的基礎結構、應用程式安全性、授權、資料主權和加密、稽核都是需要解決的廣泛領域。

## <a name="posq-converting-the-data-to-a-stream"></a>PoSQ：將資料轉換至串流

**延展性**：我們可以從兩個層面來達到延展性。 首先，從元件的觀點來看，第二，從提供來源資料的系統觀點來看。

每個 Azure 服務皆提供垂直和水平縮放的選項。 我們強烈建議在設計解決方案時考量延展性需求。

至於提供來源資料的系統，需注意不要對系統造成衝擊，過於頻繁地查詢系統，基本上會導致系統的拒絕服務 (DoS) 攻擊。 如果要輪詢系統，應該記住調整輪詢頻率有兩個影響，即資料的粒度 (查詢次數越多，越接近即時)，以及遠端系統上建立的負載。

**安全性**：如果藉由對稱或非對稱金鑰存取遠端系統，建議將密碼保存在 [Azure Key Vault](/azure/key-vault/?WT.mc_id=iotinsightssoln-docs-ercenk) 中。

## <a name="posq-warm-path"></a>PoSQ：經常性路徑

**延展性**：如果 Azure 事件中樞用於擷取子系統，則主要延展性機制為[輸送量單位](/azure/event-hubs/event-hubs-features#throughput-units?WT.mc_id=iotinsightssoln-docs-ercenk)。 事件中樞提供以靜態方式或透過[自動擴充功能](/azure/event-hubs/event-hubs-auto-inflate?WT.mc_id=iotinsightssoln-docs-ercenk)，設定輸送量單位的功能。

串流分析中的 [串流單位](/azure/stream-analytics/stream-analytics-streaming-unit-consumption?WT.mc_id=iotinsightssoln-docs-ercenk) (SU) 代表配置用來執行作業的計算資源。 SU 的數目愈大，為您的作業配置的 CPU 和記憶體資源就愈多。 這個容量可讓您專注於查詢邏輯，並以及時的方式摘要出管理硬體以執行串流分析作業的需求。 除了 SU 以外，透過[正確地平行處理查詢](/azure/stream-analytics/stream-analytics-scale-jobs?WT.mc_id=iotinsightssoln-docs-ercenk)以有效運用非常重要。

Azure Cosmos DB 實作需要佈建正確的輸送量參數和適當的資料分割設計。 可以在容器或資料庫層級佈建輸送量，並以[要求單位](/azure/cosmos-db/request-units?WT.mc_id=iotinsightssoln-docs-ercenk) (RU) 表示。 Cosmos DB 提供估算要求單位的工具。 除了佈建輸送量以外，[有效分割資料庫](/azure/cosmos-db/partition-data?WT.mc_id=iotinsightssoln-docs-ercenk)至關重要。

**安全性**：用戶端對 Azure 事件中樞的存取權是透過共用存取簽章 (SAS) 權杖和事件發行者組合，進行用戶端身份驗證。 後端應用程式的安全性遵循與服務匯流排主題相同的概念。 有關事件中樞安全性模型的詳細說明，請參閱[事件中樞驗證和安全性模型概觀](/azure/event-hubs/event-hubs-authentication-and-security-model-overview?WT.mc_id=iotinsightssoln-docs-ercenk)。

保護 Cosmos DB 資料庫可以提供對資料的受控存取權，以及加密待用資料。 如需詳細資訊，請參閱 [Azure Cosmos DB 資料庫安全性](/azure/cosmos-db/database-security?WT.mc_id=iotinsightssoln-docs-ercenk)。

**成本效益**：事件中樞的價格是 SKU (標準或進階) 的函式，以及收到的數百萬個事件，加上輸送量單位。 藉由查看由傳入訊息指示的資料擷取率，可以實現最佳組合。

使用 Cosmos DB 時，建議透過 RU 使用率，觀察存放區的最佳使用情況。 Cosmos DB 也有控制資料保留的功能，如先前所述，建議使用該功能來控制記錄存留在資料庫中的時間，進而控制資料庫的大小。

## <a name="posq-cold-path"></a>PoSQ：非經常性路徑

**延展性**：Azure 時間序列深入解析 (TSI) 使用名為「容量」的計量進行調整，該計量是套用至輸入速率、儲存體容量，以及與 SKU 相關之成本的乘數。 

Azure 時間序列深入解析有多個 SKU，這些 SKU 對其垂直調整也有直接影響。 如需調整的相關詳細資料，請參閱文件[規劃 Azure 時間序列深入解析環境](/azure/time-series-insights/time-series-insights-environment-planning?WT.mc_id=iotinsightssoln-docs-ercenk)。 如同其他許多 Azure 服務一樣，TSI 也受限於節流，以避免「吵雜芳鄰」問題。 吵雜芳鄰 (Noisy Neighbor) 是共用環境中的應用程式 /azure/sql-database/sql-database-service-tiers-vcore?WT.mc_id=iotinsightssoln-docs-ercenk 會獨佔資源並讓其他使用者無法使用的現象。 如需管理節流的詳細資訊，請參閱 [TSI 說明文件](/azure/time-series-insights/time-series-insights-environment-mitigate-latency?WT.mc_id=iotinsightssoln-docs-ercenk)。 

儲存體帳戶的延展性目標記錄在 [Azure 儲存體延展性和效能目標](/azure/storage/common/storage-scalability-targets?WT.mc_id=iotinsightssoln-docs-ercenk)中。 儲存資料超出單一儲存帳戶容量的常用技術是，跨多個儲存體帳戶進行分割。

Azure SQL Database 有許多選項可垂直和水平管理延展性，取決於購買模型 ([以 DTU 為基礎](/azure/sql-database/sql-database-service-tiers-dtu?WT.mc_id=iotinsightssoln-docs-ercenk)和以虛擬核心為基礎)。 我們建議進一步研究，以使用關於該主題的 [SQL Database 說明文件](/azure/sql-database/sql-database-scale-resources?WT.mc_id=iotinsightssoln-docs-ercenk)，找出未來解決方案的最佳選項。

**安全性**：TSI 環境為彼此獨立的管理存取和資料存取提供[存取原則](/azure/time-series-insights/time-series-insights-data-access?WT.mc_id=iotinsightssoln-docs-ercenk)。 除了定義的資料來源以外，沒有直接的方法可將資料新增到 TSI 環境。 管理存取原則可授與環境設定相關權限。 資料存取原則可授與下列權限：發出資料查詢、在環境中操作參考資料，以及共用與環境相關聯的已儲存查詢和檢視方塊。

Azure Data Factory 服務提供多種方法來保護資料存放區認證，無論是在其受管存放區中，或在 Azure Key Vault 中。 傳輸中資料加密取決於資料存放區的傳輸 (例如 HTTPS 或 TLS)。 待用資料加密也取決於資料存放區。 如需進一步的詳細資料，請參閱[在 Azure Data Factory 中資料移動的安全性考量](/azure/data-factory/data-movement-security-considerations?WT.mc_id=iotinsightssoln-docs-ercenk)。

SQL Database 提供一套廣泛的安全性功能，用於資料存取、監視，以及加密待用資料。 如需詳細資料，請參閱 [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database?WT.mc_id=iotinsightssoln-docs-ercenk)。

**成本效益**：任何分析解決方案的核心都是儲存體。 分析引擎需要速度、效率、安全性和輸送量，以便在合理的時間內處理大量資料。 藉由彙總和摘錄資料，並有效地使用多語言存放區，制定充分利用基礎平台的機制，是有效管理成本的方法。 由於 Azure 是雲端平台，因此，有一些方法可利用程式設計的方式來解除委任、重新委任，以及調整資源大小。 例如，[建立或更新作業](/rest/api/sql/databases/createorupdate?WT.mc_id=iotinsightssoln-docs-ercenk)可用於變更 Azure SQL Database 的資料庫大小。