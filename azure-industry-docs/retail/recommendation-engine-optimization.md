---
title: 重複使用建議系統，以及 R 與 Azure 的演算法
author: kbaroni
ms.author: kbaroni
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 如何重複使用並最佳化以 R 語言撰寫的建議應用程式。 仰賴 Azure VM 上的 Machine Learning Server。
ms.openlocfilehash: c5c35de681abc52641952f8bc9e95095b9d99d97
ms.sourcegitcommit: b8f9ccc4e4453d6912b05cdd6cf04276e13d7244
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74263494"
---
# <a name="optimize-and-reuse-an-existing-recommendation-system"></a>將現有的建議系統最佳化並重複使用  

此文件說明成功重複使用並改進以 R 撰寫之現有建議系統的程序。此程序的關鍵之處在於是 MicrosoftML 與 RevoScaleR 程式庫 (建置到 Microsoft Machine Learning Server) 的平行處理原則。

## <a name="recommendation-systems-and-r"></a>建議系統與 R

針對經銷商，了解消費者喜好與購買記錄是一個競爭優勢。 經銷商已使用此類資料數年，並結合機器學習以指出與消費者相關的產品並提供個人化的購物體驗。 此方式稱為**產品建議**，而且它會為經銷商產生大幅的營收串流。 建議系統會協助回答下列問題，例如：*接下來他會看哪部電影？此客戶可能對哪些額外服務感興趣？此客戶想要到哪裡度假？*
客戶最近會想知道：*客戶 (訂閱者) 會更新合約嗎？* 客戶有現有的建議模型，此模型可預測訂閱者針對合約續約的機率。 一旦產生預測，就會套用額外的處理以將回應分類為是、否或可能。 模型回應接著會整合到話務中心商業程序。 該程序可讓服務專員提供個人化的建議給消費者。  
有許多這個客戶早期分析產品已內建在[程式設計語言 R](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server) 中，包括其建議系統核心的機器學習模型。 因為其訂閱者基底已成長，因此有資料與運算需求。 在這種程度上，建議工作負載處理速度現在異常慢且難以處理。 現在 Python 逐漸成為其分析產品策略的一部分。 但在近期，他們必須保留其 R 投資並尋找更有效的開發與部署程序。 所面臨的挑戰是使用 Azure 中的功能最佳化現有的方法。 我們著手進行一個工作來提供並驗證建議工作負載的概念證明技術堆疊。 在這裡，我們摘述了可為類似專案使用的一般方法。  

## <a name="design-goals"></a>設計目標

關鍵優先順序是重新設計並自動化模型工作流程以提供數個優點：

- 較快的模型開發循環與較快的創新速度。 當資料準備與模型開發循環都有效率時，實驗量就可以增加。  
- 透過更頻繁地針對全新資料執行重新定型循環，可獲得更精確的模型結果與更好的建議。
- 更能調整規模的概念證明 (POC) 實作，這在各種生產環境中都能運作。
- 較快的生產時間 (透過自動化開發、測試與部署之間的程序)。 自動化也能減少作業錯誤與延遲時間。  

## <a name="requirements"></a>需求

工作流程重新設計有下列需求：

- 利用小組的現有 R 技術與專業。
- 在適當的情況下重複使用程式碼。
- 將訂閱者資料存放在資料庫中，以輕鬆並快速地整合到模型定型與重新定型程序。
- 透過 Web 應用程式介面叫用模型重新定型與評分。
- 在 Web 前端使用 Azure Active Directory 來進行驗證。

## <a name="technology-stack"></a>技術堆疊

此專案的管線需要多種技術與工具，而且以四個區域為中心：

1. 訂閱者資料的持續性與可存取性。
2. 模型的開發、定型與選擇。  
3. 模型部署與作業化。
4. 使用 Web 應用程式來叫用評分與模型重新定型。

下面將更詳細地討論管線圖中的技術元件。

![最佳化架構](assets/recommendation-engine-optimization/recommendation-architecture.png)

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

選取 R 工作負載的主要原因： **[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)** 和 **Microsoft ML**。 這些套件中包含的功能會在程式碼中廣泛使用，以匯入資料、建立分類模型，以及將它們部署到生產環境。
MLS 已部署在 Azure 中的兩部 Linux 虛擬機器上：一部設定為「開發」，而另一部設定為「作業」。 開發 VM 是使用更大量的記憶體與處理能力來佈建，以進行數百個模型的定型及測試。 它也裝載 RStudio Server 以為遠端使用者提供輕鬆存取 RStudio IDE 的方式。 作業伺服器使在較小的 VM 上設定，此 VM 具有裝載 R 模型 (可透過 REST API 從 Web 應用程式呼叫) 所需的額外延伸模組。

### <a name="rstudio-server"></a>RStudio Server

**[RStudio Server](https://www.rstudio.com/products/rstudio/#Server)** 是 Linux 應用程式，它為遠端或膝上型電腦用戶端提供瀏覽器型介面。 它在連接埠 8787 上執行，而且一旦在 Azure VM 上建立網路安全性規則之後，即可供遠端連線使用。 針對偏好使用 RStudio IDE 的分析師與資料科學家，提供對具有大量運算與記憶體功能之虛擬機器的存取是一個有效率的選項。 它同時以開放原始碼版本與商業版本提供下載。

### <a name="azure-sql-database"></a>Azure SQL Database

一開始，訂閱者資料是存放在一個很大的 .csv 檔案中，該檔案有 500 個訂閱者的 6 百萬列購買與喜好設定資訊。 將該資料存放在資料庫中表示可以在 R 內以更快的速度存取資料，而且也可以進行記錄篩選。 再也不需要匯入整個資料集進行定型或重新定型：可在資料庫來源依訂閱者篩選資料，這樣可大幅減少匯入及處理資料所需的資源。  
Azure 中有數個受控雲端資料庫選項。 我們選擇了 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)，因為客戶熟悉 SQL Server，而且更重要的是，未來我們計畫大規模引進 SQL Server 機器學習服務到 Azure SQL Database。 [SQL Server 機器學習服務](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017)是資料庫內功能，可用於透過預存程序執行 R 與 Python 工作負載。

### <a name="nodejs-and-reactjs"></a>Node.js 與 React.js

已建立 Web 介面以叫用 R 指令碼並保護網站。 我們選擇了 Node.js 做為網頁伺服器架構，因為它讓您在分散式環境內為 Web 應用程式提供輕量型、有效率的執行階段環境。 React 是用來建置使用者介面並位於前端，它可以呼叫裝載在網頁伺服器上的 Web 服務。 據信 Node + React 可提供為模型管線進行 Web 服務原型設計的最快路徑。  

## <a name="infrastructure-implementation"></a>基礎結構實作

下一節說明如何為此專案部署伺服器基礎結構。 進行正確的開發及部署就和決定要套用的模型建構方法與技術一樣重要。

### <a name="initial-database-load"></a>初始資料庫負載

第一個步驟是將訂閱者資料從非常大的 .csv 檔案匯入到 Azure SQL Database。 有多種選項可用來將資料匯入到 Azure SQL Database，如此[參考](https://blogs.msdn.microsoft.com/sqlcat/2010/07/30/loading-data-to-sql-azure-the-fast-way/) \(英文\) 所述。 下面說明我們是如何辦到的：

1. 透過 Azure 入口網站依照[這裡](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)的步驟建立資料庫。
2. 下載並使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) 從 VM 連線到資料庫。
3. 選取 [SQL 匯入/匯出精靈](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard?view=sql-server-2017) (若您的時間有限，有其他效能更好的匯入選項)。 請記住，匯入/匯出精靈會將資料類型從資料來源對應到目標目的地；而且在我們的案例中，所有資料元素都對應到 varchar(max) 資料類型 (在可接受的情況下)。 若您的案例需要不同的對應，您可以在精靈中修改資料類型 ([參考](https://docs.microsoft.com/sql/integration-services/import-export-data/data-type-mapping-in-the-sql-server-import-and-export-wizard?view=sql-server-2017))。  
4. 因為提交到資料庫的大部分查詢都會篩選 *subscriber_id* 欄位，我們已在該欄位上建立索引。

### <a name="web-application"></a>Web 應用程式

Web 應用程式負責三個功能：

- 驗證：Web 使用者透過 *React* 前端向 *Azure Active Directory* 驗證。
- 模型評分：針對特定訂閱者，接受來自使用者的輸入資料、使用 REST API 將訂閱者資料提交到 Web 服務，該服務接著會傳回預測回應。  
- 模型重新定型：接受訂閱者識別碼做為輸入，並叫用開發伺服器上的 R 指令碼以針對該訂閱者將模型重新定型。

搭配 *Azure Active Directory* 實作單一登入 (SSO) 比預期更有挑戰性。 這是單頁應用程式 (SPA) 架構使然。 有一個特定 Azure Active Directory 程式庫是成功關鍵：[react-adal](https://github.com/salvoravida/react-adal)。 下列參考提供實作驗證的實用指導方針：

- [Azure AD 的驗證案例](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)
- [單頁應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios#single-page-application-spa)

### <a name="development-vm-mls-930"></a>開發 VM (MLS 9.3.0)

開發 VM 裝載模型開發、定型與重新定型，以及分類模型的部署。 Azure VM (DS13 V2) 隨著 Linux/Ubuntu 16.10 提供，而且基底 VM 中已安裝下列項目：

- Machine Learning Server 9.3.0 (使用[這裡](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-install)\(英文\) 提供的指示)。 請務必徹底執行安裝程式驗證步驟，以確認安裝成功。 因為這是開發 VM，您可以略過*啟用 Web 服務部署與遠端連線*一節。
- [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/) (開放原始碼版本)。 請勿重新安裝 R/r-base (它先前是隨 MLS 9.3.0 安裝)。  
- [新增網路安全性群組](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal)到 VM，以針對 RStudio Server 允許連接埠 8787 上的連入連線。  
- ODBC 伺服器 (負責處理開發 VM 與 Azure SQL Database 之間的通訊)。 VM 上已安裝下列 ODBC 驅動程式：  
- 與 Linux/ubuntu 16.10 相容的[適用於 SQL Server 17 的 ODBC 驅動程式](https://www.microsoft.com/download/details.aspx?id=56567)  
- 開放原始碼 ODBC 驅動程式 unixodbc (安裝指示：[使用 ODBC 匯入關聯式資料](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-data-odbc) \(英文\))。 注意：在此文章中， Ubuntu 指示中有兩個錯字。  
- 檢查 unixodbc 是否已安裝：````Apt list –installed | grep unixODBC (should be unixodbc)````
      - 安裝驅動程式：````sudo apt-get install unixodbc-devel unixodbc-bin unixodbc (should be unixodbc-dev)````

### <a name="operations-vm-mls-930"></a>作業 VM (MLS 9.3.0)

模型 Web 服務與端點上裝載的作業 VM 存放了 Swagger 檔案，並存放了分類模型的序列化版本。 設定與 MLS 開發伺服器非常像。 不過，它是針對作業化所設計，這表示已安裝為 REST 端點提供服務所需的 Web 服務。 若要部署作業 VM，可以利用 ARM 範本來加快部署速度。 請參閱：[使用 ARM 設定 Microsoft Machine Learning Server 9.3 以將分析操作化](https://blogs.msdn.microsoft.com/mlserver/2018/02/27/configuring-microsoft-machine-learning-server-9-3-to-operationalize-analytics-using-arm-templates/) \(英文\)。 針對四個專案，已使用此 [ARM 範本](https://github.com/Microsoft/microsoft-r/tree/master/mlserver-arm-templates/one-box-configuration/linux)部署 *One-Box* 設定。  
在此情況下，支援模型管線所需的伺服器元件已啟動並執行。

## <a name="model-implementation"></a>模型實作

有一個關鍵決策影響了此專案最終模型設計，而且那是要移至「許多模型」設計 (而非單一整合型模型)。 差異：每個訂閱者都有自己的分類模型，而不是以一個大型分類模型為所有訂閱者提供服務。 針對此客戶，這是慣用方法，因為較小的模型有較小的記憶體需求，而且在生產環境中比較容易水平調整規模。

### <a name="data-import"></a>資料匯入

模型部署所需的所有資料都存在於 *Azure SQL Database* 中。 針對模型定型與重新定型，資料匯入是在兩個階段完成：

1. 提交查詢到資料庫，以擷取特定 *subscriber_id* 的資料，以傳回結果集。 針對資料庫的查詢存取，有兩個選項要考慮：

- 稱為 [RxSQLServerData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqlserverdata) 的 RevoScaleR 函式
- R odbc 套件

我們決定使用 R “odbc” 程式庫，它可讓我們在資料庫層級進行資料篩選。 篩選資料庫資料表，以只顯示特定訂閱者模型中，要讀入到 R 並處理所需的最小資料列數目。 針對每個模型進行定型與重新定型所需的記憶體、運算資源與整體時間將能減少。  

1. 結果集會根據分類演算法的要求轉換為 R 資料框架，而且一些資料類型會明確從 varchar 轉換為 integer 或 numeric。 針對此功能，我們使用 RevoScaleR 函式 [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport)。 *rxImport* 函式隨附於 RevoScaleR 與 MicrosoftML 中，而且設計為支援多執行緒。 以下是我們使用它的範例：

````r

# Populate the data frame and modify column types as needed
input_data <- rxImport(sqlServerDataDS, colClasses = c(  
Approved="integer",
      OnTimeArrivalRate="numeric",
      Amount="numeric",
      IsInformed="numeric",
      <continue with list of columns> )
# View the characteristics of the variables in the data source
rxGetInfo (input_data, getVarInfo = TRUE)
````

## <a name="unbalanced-data"></a>不對稱的資料

因為建議模型的目標是預測客戶針對合約續約的機率並將機率分類為「是」、「否」或「可能」，我們將使用分類演算法。 一個可能會嚴重影響分類演算法精確度與效能的問題不對稱的資料集。  
若資料集中某個「類別」的樣本數目遠大於另一個「類別」中的樣本數目，我們將它稱為「不對稱的資料集」。 在此案例中，可供每個訂閱者使用的資料列數目不對稱：在高的那一端。一個訂閱者有 1 百萬個以上的資料列；在低的那一端， 330 個客戶的資料少於 100 列。 下圖顯示每個訂閱者的資料列 (樣本) 數不對稱的情況：![imbalance-chart](assets/recommendation-engine-optimization/recommendaton_engine_chart.png)

一個可用於處理不對稱資料集的技巧是變更資料集並為代表性不足的類別進行過度取樣，或為代表性過高的類別進行低度取樣。 另一個技巧是使用資料擁有者對資料所具有的精確知識與其屬性，以合成方式產生額外的資料。 客戶為訂閱者建立了最小樣本大小的閾值。 針對低於閾值的訂閱者，必須處理資料。 針對此專案，我們同時探索了這兩個方法。

## <a name="algorithm-selection"></a>演算法選擇

已評估三個不同的分類演算法實作：*rxDForest*、*rxFastTrees* 與 *rxFastForest*。 這三個演算法都能發揮多執行緒處理與平行處理原則的優點。 此外，Microsoft ML 將使用多顆 CPU 或 GPU (如果可用的話)。 用於評估模型的準則包括：

- 新模型是否比原始模型更精確？
- 在生產環境中執行之模型對記憶體資源的需求度如何？ 在不影響精確度的情況下，作業環境是否能支援以近乎即時的預測回應模式同時執行許多模型？
- 演算法處理不對稱資料集的完善程度為何？ 是否應該透過產生合成資料來處理不對稱問題？

下表摘述差異我們發現的結果：

| 演算法 | 說明 | 結果 | 注意 |
| :--------- | :------------ | :--------- | :--------------- |
| [rxFastTrees](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfasttrees) | 推進式決策樹的平行處理原則實作 (它實作 FastRank 的多執行緒版本)。 | 精確、最快的效能。 | 沒有不對稱資料的特殊功能。 必須提供預先處理的資料做為輸入 |
| [rxFastForest](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfastforest) | 隨機樹系的平行處理原則實作，並使用 rxFastTrees 來建置決策樹的整體 學習人員。 | 使用預先處理的資料時，精確度比原始模型好。 所需記憶體體資源較少，比 rxDForest 快 |沒有不對稱資料的特殊功能。 提供預先處理的資料做為輸入。 |
| [rxDForest](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdforest) | 隨機樹系的平行處理原則實作。 包括在 RevoScaleR 中。 可以完全在函式呼叫內處理不對稱資料 (移除缺少的資料、使資料符合需求、處理樣本的階層)。 | 比原始模型更快。 精確度等於或高於原始模型。 使用各種技巧來重新取樣及合成，以處理不對稱的資料集。 高記憶體使用量。  | 高記憶體使用量，因為它在函式內包括條件式資料。   針對資料進行處理很好，但沒有比由資料擁有者所提供的自訂轉換還好。 |

最後，客戶選擇 *rxFastForest* 演算法並決定處理不對稱的資料 (透過使用 [vtreat](https://cran.r-project.org/web/packages/vtreat/index.html) 程式庫並針對代表性不足的訂閱者新增自訂資料預先處理步驟以合成方式產生資料。

## <a name="model-deployment--web-services"></a>模型部署與 Web 服務

發行模型到作業 VM 部署是一個很直覺的動作，而且記載在此快速入門文件中：[使用 mrsdeploy 將 R 模型部署為 Web 服務](https://docs.microsoft.com/machine-learning-server/operationalize/quickstart-publish-r-web-service) \(英文\)。
在我們的案例中，一旦在開發 VM 上建立模型，就會使用下列步驟在作業 VM 上發行它們：

1. 使用 mrsdeploy 套件中兩個函式的其中一個進行驗證，以建立從開發人員 VM 連線到作業 VM 的遠端登入：remoteLogin() - 使用本機系統管理員名稱與密碼，或 remoteLoginAAD() - 使用 Azure Active Directory。 ＜使用 mrsdeploy 並開啟遠端工作階段以登入 Machine Learning Server 或 R Server＞參考中說明上述兩個選項。  
2. 一旦將模型定型，使用 mrsdeploy 套件中的 publishService 或 updateService 函式將它發行到作業 VM。 針對此專案，我們使用多個部署方法，而且視所使用的方法而定，我們發行了新模型或更新現有的模型。 已實作下列程式碼以處理這兩個案例：

````r
# If the service does not exist, publish it and if it does exist, update it.  
# No service by this name so publish one
 api <- publishService(serviceName, code =sc_predict, model = model,  
      inputs = list(prop_data="data.frame"),  
      outputs = list(answer = "numeric"), v = "v1.0.0" )
 print("=========== Model Created =============")
} else {
# A service by this name already exists, update it  
 api <- updateService(serviceName, model = model,
     inputs = list(prop_data="data.frame"), v = "v1.0.0" )
 print("=========== Model Updated =============")
}
 ````

當部署時，模型會序列化並存放在作業伺服器上，而且可透過 Web 服務以「標準」  或「即時」  模式取用。 每次以標準模式呼叫 Web 服務時，都會載入 R 與任何必要程式庫，並透過每個呼叫來卸載。 相反地，使用「即時」  模式時，R 與程式庫只會載入一次，並由後續 Web 服務呼叫重複使用。 因為 Web 服務呼叫的大部分額外負擔是載入 R 與程式庫，即時模式提供有關模型評分的較短延遲，而且回應時間可以在 10 毫秒以下。 針對標準與即時選項，請參閱[這裡](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)的文件與參考範例。 即時可用於進行單次預測，但您也可以傳入輸入資料框架以進行評分。 如同本參考所述：[透過批次處理的非同步 Web 服務 (使用 mrsdeploy)](https://docs.microsoft.com/machine-learning-server/operationalize/how-to-consume-web-service-asynchronously-batch) \(英文\)。

## <a name="conclusion"></a>結論

利用建置在 Microsoft Machine Learning Server 中的 MicrosoftML 與 RevoScaleR 程式庫的平行處理原則來加快為數百個訂閱者開發、部署個別分類模型及為其評分的速度。 模型精確度已改進，而且定型及重新定型時間已縮短，這所有一切都是在對現有 R 程式碼基底進行最少變更的情況下完成的。
實作基礎結構以支援模型管線並正確設定端對端技術元件可能很複雜。 以下是一些能協助您以自己的方式開始的參資料考：

- [Machine Learning Server 文件](https://docs.microsoft.com/machine-learning-server/) \(英文\)
- [Machine Learning Server 的 R 教學課程](https://docs.microsoft.com/advanced-analytics/tutorials/sql-server-r-tutorials?view=sql-server-2017) \(英文\)
- [Machine Learning Server 的 R 範例](https://docs.microsoft.com/machine-learning-server/r/r-samples) \(英文\)
- [R 函式程式庫參考](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) \(英文\)

## <a name="references"></a>參考

若您對於為您的零售業務建置其他預測性解決方案有興趣，請瀏覽 Azure [AI Gallery](https://gallery.azure.ai/) \(英文\) 的[零售驅動](https://gallery.azure.ai/industries/retail)。  