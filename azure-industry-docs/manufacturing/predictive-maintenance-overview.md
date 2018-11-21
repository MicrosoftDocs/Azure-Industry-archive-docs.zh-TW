---
title: 製造業中的預測性維護概觀
author: scseely
ms.author: scseely
ms.date: 05/02/2018
ms.topic: article
ms.service: industry
description: 有關如何為 Azure 上的製造業客戶開發預測性維護 (PdM) 的概觀。
ms.openlocfilehash: c5f3737c29c1d16f5d343a1fd56ed924684a3a1d
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654155"
---
# <a name="predictive-maintenance-in-manufacturing-overview"></a>製造業中的預測性維護概觀

## <a name="introduction"></a>簡介

預測性維護 (PdM) 會預測維護需求，以避免與未排定之停機關聯的成本。 透過連結裝置並監視裝置產生的資料，您可以找出導致潛在故障問題的模式。 您接著可以使用這些見解在問題發生之前即予以解決。 這個預測設備或資產需要維護之時間的能力可讓您最大化設備生命週期並最小化停機時間。

PdM 可從工廠設備產生的資料擷取見解，並根據這些見解採取動作。 PdM 的概念最早可追溯到 1990 年代，而且會增強正常排定的預測性維護。 在早期，缺少感應器可產生資料，而且缺少計算資源可收集並分析資料，使得很難實作 PdM。 今天，物聯網 (IoT)、雲端運算、資料分析與 Machine Learning (ML) 的進展讓預測性分析成為主流。

PdM 需要設備從監視設備的感應器提供資料以及其他作業資料。 PdM 系統會分析資料並存放結果。 人會根據分析來採取動作。

在此文章中引進一些背景之後，我們討論如何使用內部部署資料、Azure 機器學習與機器學習模型使用組合，來實作各種 PdM 解決方案。 PdM 高度仰賴資料來制訂決策，因此我們從查看資料收集開始。 必須收集資料並使用資料來評估目前發生什麼事，並在未來使用資料來建置更好的預測性模型。 最後，我們說明分析解決方案看起來像什麼樣子，包括在 [Power BI](https://docs.microsoft.com/en-us/power-bi/) 之類的報告工具中將分析結果視覺化。

## <a name="maintenance-strategies"></a>維護策略

在製造業的歷史中，發展出數個維護策略。 「反應式維護」可在問題發生之後予以修正。 「預防性維護」會依照根據先前故障經驗建立的的維護排程，在問題發生之前即予以修正。 PdM 也可以在問題發生之前即予以修正，但請考慮設備的實際使用，而非依照固定排程工作。 在三者之中，由於資料收集、處理與資料視覺化之故，PdM 是最難達成的。 讓我們深入看看看看每個選項。

「反應式維護」奉行「沒壞就不修理」原則。 只要在資產損壞時才予以修復。 例如，您的 5 軸 CNC 加工中心機馬達只會在壞掉時才會進行修理。 「反應式維護」會最大化元件的生命週期，雖然元件終究會故障。 「反應式維護」也會有未知的停機時間、正常元件由於受故障元件影響的非預期附隨損害，以及其他問題。

「預防性維護」要求您根據預先定義的間隔來維護資產。 此間隔通常是以資產的故障頻率歷史記錄為基礎。 這些間隔是以歷史表現、模擬、統計式模型建構等為基礎。此策略的優點是它可以提高運作時間、降低故障次數，因此能讓您排定維護計畫。 在許多案例中，缺點是資產上更換的元件可能都還可以使用。 這會導致過度維護與浪費。 另一方面，某些零件可能也會在排定的維護時間之前故障。 您或許已經熟知預防性維護：在每個設定的作業時數 (或一些其他計量) 之後，停止使用機器並檢查機器。 更換即將需要更換的每個零件。

「預測性維護」會使用模型來預測資產可能會遇到元件故障的時間，以監視資產的使用狀況。 接著系統會為該元件排定它自己的「Just-In-Time 維護」。 PdM 透過同時最大化運作時間與資產生命週期，在先前的策略上進行改進。 既然您是在快接近設備的元件最大生命週期時維護零件，您花比較少的錢來更換可運作的零件。 缺點是 PdM 的 Just-In-Time 本質比較難執行，因為它需要更多更具回應性與彈性的服務組織。 回到 5 軸 CNC 加工中心機的馬達，若預測性模型表示馬達在接下來 24 小時內有 75% 的可能性會故障 (以來自機器中之感應器的資訊為基礎)，您會排定合理的維護排程 (亦即，以已規劃方式，而且不會對生產造成影響)。

 ![](./assets/pdm-assets/maintenancestrategies.png)


## <a name="different-ways-pdm-can-be-offered"></a>PdM 可提供的不同方式

PdM 解決方案可由製造商直接使用，以監視來自自己之製造作業的資料。 有其他方式存在，這代表其他組織的商業機會與營收串流。 例如︰

- 製造商透過為其產品提供預測性維護服務，以提高其客戶的價值。
- 製造商在產品即服務模型下提供其產品，在此模型中，客戶會「訂閱」產品，而非購買產品。 在此模型下，製造商想要最大化產品運作時間；當產品不在運作時，將無法產生營收。
- 公司為由一或多個造商製造的產品提供預測性維護產品與服務。

## <a name="building-a-predictive-maintenance-solution"></a>建置預測性維護解決方案

若要建置 PdM 解決方案，我們從資料開始；找出顯示正常作業的資料以及顯示設備在故障之前、故障時與故障後看起來的樣子的資料。 資料來自感應器、由設備操作者維護的備註、執行資訊、設備資料與機器規格等。記錄系統可能包括歷史學家、製造執行系統與 ERP 等。資料以各種方式提供以進行分析。 [小組資料科學程序](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/) (圖解如下且已針對製造業自訂) 能有效說明建置及執行機器學習模型時可能會遇到的各種顧慮。

 ![](./assets/pdm-assets/DataScienceDiagram.png)


您的第一個工作將會是找出您想要預測的故障類型。 記住這點之後，您接著就可以找出有關於故障類型之有趣資料的資料來源。 管線會從您的環境將資料讀入到系統中。 資料科學家將會使用其最愛的機器學習工具來準備資料。 此時，他們已準備好建立模型並定型，以找出各種問題類型。 模型會回答如下問題：

- 針對資產，接下來 X 小時內發生故障的機率為何？ 答：0-100%
- 資產的剩餘使用年限為何？ 答：X 小時
- 資產的行為是否異常？ 答：是或否
- 哪些資產需要最緊急的維護？ 答：資產 X

一旦開發之後，模型能部署在設備本身進行自我診斷、部署在製造環境某處的邊緣裝置中，或部署在 Azure 中。 您將也能繼續將資料從主要來源傳送到中央存放區，以便您可以繼續建置並維護 PdM 解決方案。

Azure 的功能允許您在您選擇的技術上定型並測試模型。 您可以使用 GPU、FPGA、CPU、大型記憶體機器等。 Azure 中的其中一個絕佳功能是平台完全採用由資料科學家 (例如 R 與 Python) 所使用的開放原始碼工具。 當分析完成時，結果可以顯示在儀表板的其他面或其他報告中。 這些報告可能會出現在自訂工具中，或您可以利用 [Power BI](https://docs.microsoft.com/en-us/power-bi/) 或[時間序列深入解析](https://docs.microsoft.com/en-us/azure/time-series-insights/)等報告工具的優勢。

不論您的 PdM 需要什麼，Azure 都提供工具、規模與您所需的功能來建置穩固的解決方案。

## <a name="getting-started"></a>開始使用

工廠中的許多設備都會收集資料並提供機制來從裝置收集資料。 儘快開始收集該資料。 當故障發生時，讓資料科學家分析資料以建立可偵測未來故障的模型。 當有關故障偵測的知識建立時，您將移到預測模式，以在規劃的停機時間修正元件。 [預測性維護模型建構指南](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) \(英文\) 提供詳盡的逐步解說，說明如何建置解決方案的 Machine Learning 部分。

若要查看範例解決方案，請檢閱[航太中的 PdM](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace) 的解決方案、指南與劇本。 若需要準備建置模型，我們建議瀏覽 [AI School](https://aischool.microsoft.com/)。 [搭配 Azure ML 進行 Machine Learning 的簡介](https://aischool.microsoft.com/learning-paths/4ZYo4wHJVCsUSAKa2EoAk8) \(英文\) 課程將協助提供熟悉我們工具的內容。

## <a name="technologies-presented"></a>呈現的技術

[Azure Blob 儲存體](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction)能依所需的資料存取頻率，將數百到數十億個物件存放在經常性、非經常性或封存層中。

[Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/) 是全球範圍中延遲極低的資料庫且可大規模調整規模的應用程式，且原生支援 NoSQL。

[Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/) 的功能可讓開發人員、資料科學家以及分析師的工作更加輕鬆，範圍包括儲存任何大小、形狀及速率的資料，以及跨各平台和語言進行任何類型的處理及分析。

[Azure 事件中樞](https://docs.microsoft.com/en-us/azure/event-hubs/)是規模超大的遙測內嵌服務，它可以收集、轉換及存放數百萬個事件。 這個分散式串流平台提供低延遲和可設定的保留期，讓您能夠將大量遙測資料輸入雲端，並使用發佈/訂閱語意從多個應用程式讀取資料。

[Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/) 是一個物聯網 (IoT) 服務，建置在 IoT 中樞之上。 這項服務提供客戶在裝置上分析資料的服務，也稱為 位於邊緣，而非在雲端中。 將部份工作負載移至 Edge，您的裝置就能以較少的時間將訊息傳送至雲端，並更快速地變更狀態。

[Azure IoT 中樞](https://docs.microsoft.com/en-us/azure/iot-hub/)是一項完全受控的服務，可在數百萬個 IoT 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。

[Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/) 可讓電腦從資料與經驗當中學習並做出回應，而不需要明確的程式設計。 客戶可以建置利用智慧方式來感應、處理及操作資訊的人工智慧 (AI) 應用程式，以增強人力、增加速度和效率，並協助組織完成更多作業。

[Azure 服務匯流排](https://docs.microsoft.com/en-us/azure/service-bus/)是一個代理通訊機制。 服務匯流排傳訊基礎結構的核心元件，就是佇列、主題和訂用帳戶。

[Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/) 是完全受控的智慧型關聯式雲端資料庫服務，提供最廣泛的 SQL Server 引擎相容性，讓您不必更改應用程式，即可移轉 SQL Server 資料庫。

[Power BI](https://docs.microsoft.com/en-us/power-bi/) 是商務分析工具套件，可將見解提供給整個組織。 連線到數百種資料來源，簡化資料準備工作，立即獲取特定的分析。

[時間序列深入解析](https://docs.microsoft.com/en-us/azure/time-series-insights/)是完全受控的分析、儲存及視覺化服務，可讓您在雲端上管理 IoT 規模的時間序列資料。

## <a name="conclusion"></a>結論

PdM 需要機器有一些層級的檢測設備與連線，以允許我們建置可預測問題並允許我們在故障發生之前採取動作的系統。 PdM 會透過找出要檢查及修復或更換的元件以增強預測性維護排程。 有許多資源可協助您開始。 Microsoft 的基礎結構可協助您建置可在裝置、邊緣及在雲端中執行的解決方案。 

若要開始，請挑選您想要預防的前 1-3 個故障，並透過那些項目開始您的探索程序。 接著，找出有助於找出故障的資料所在處。 結合資料與您從[搭配 Azure ML 進行 Machine Learning 的簡介](https://aischool.microsoft.com/learning-paths/4ZYo4wHJVCsUSAKa2EoAk8) \(英文\) 課程挑選的技巧來建置您的 PdM 模型。