---
title: 使用 Azure 服務的醫療保健藍圖實作 AI
author: dstarr
ms.author: dastarr
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 此文章提供適用於 AI 之 Microsoft Azure 藍圖的指導方針。
ms.openlocfilehash: 40919ffde2c2cac11339b40348cba7a5e0e0e16d
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77052632"
---
# <a name="implementing-the-azure-blueprint-for-ai"></a>實作適用於 AI 的 Azure 藍圖

## <a name="introduction"></a>簡介

醫療保健組織了解 AI (人工智慧) 與 ML (機器學習) 對其公司的各層面而言都是非常有價值的工具，從改進病患病情到讓每日作業程序變得更流暢。 通常，醫療保健組織沒有技術員工可實作 AI/ML 系統。 為改進此情況並快速讓 AI/ML 解決方案在 Azure 上運作，Microsoft 建立了 [Azure 醫療保健 AI 藍圖](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)。 使用該藍圖，我們顯示如何以安全、符合規範且可靠的方式快速開始使用 AI/ML。

適用於 AI 的醫療保健藍圖可使用 Azure 加快在您的組織中導入 AI/ML 的速度。 此文章說明如何安裝該藍圖、其元件，以及如何使用它來執行可預測病人住院天數的 AI/ML 實驗。

### <a name="benefits"></a>優點

該藍圖的建立目的旨在為醫療保健組織提供指導方針以及快速開始使用 PaaS (平台即服務) 架構的方式，以在高度符合規範的醫療保健環境中支援 AI/ML，包括確定系統上傳 HIPAA 與 HITRUST 合規性需求。

醫療保健組織中的技術員工，通常沒有很多時間可處理新專案，特別是對必須學習複雜的新技術之技術員工而言。 該藍圖可協助技術員工熟悉 Azure 與數個其服務，進而節省學習曲線成本。 技術員工可以使用該藍圖做為參考實作來學習 (在該藍圖安裝之後)，並使用該知識來擴充其功能或以該藍圖為模式建立新的 AI/ML 解決方案。

該藍圖可讓您的組織快速開始使用新的 AI/ML 功能。 有了 AI 與 ML 之後，技術員工就可以準備使用透過各種來源收集的資料來執行 AI/ML 實驗。 例如，可能已經有先前敗血症案例的資料，以及針對患有該疾病之個別病人所追蹤的附隨變數的資料。 以匿名格式使用此資料時，技術人員可以查看病人是否有潛在敗血症的徵兆，並協助變更作業程序以更有效地避免該疾病。

該藍圖提供資料與範例程式碼，可讓您了解如何預測病人的住院天數。 這是範例使用案例，可用於了解 AI/ML 解決方案的各元件。

### <a name="platform-or-infrastructure-as-a-service"></a>平台或基礎結構即服務

Microsoft Azure 同時提供 PaaS 與 SaaS 供應項目，而如何針對您的需求選擇要使用哪一個則視使用案例而異。 該藍圖的設計目的旨在使用可預測病人在醫院住院天數的 PaaS 服務。 Azure 醫療保健 AI 藍圖提供具現化安全且符合規範之 AI/ML 解決方案 (針對醫療保健組織所預先設定) 所需的所有項目。 此藍圖所使用的 PaaS 模型會安裝藍圖並將它設定為完整解決方案。

### <a name="paas-option"></a>PaaS 選項

使用 PaaS 服務模型有助於降低擁有權總成本 (TCO)，因為沒有必須管理的硬體。 組織不需要購買並維護硬體或 VM。 該藍圖會以獨佔方式使用 PaaS 服務。

這可降低維護內部部署解決方案的成本，並讓技術員工可以專注在策略性專案，而非基礎結構。 它也可以讓您將計算與儲存體的費用從資本費用預算轉為營運費用預算。 執行此藍圖案例的成本是由服務的使用加上資料儲存體成本所驅動。

### <a name="iaas-option"></a>IaaS 選項

雖然該藍圖與此文章著重在 PaaS 實作，但是也有該藍圖的[開放原始碼延伸模組](https://github.com/Azure/Azure-Health-Extension)可用，它可讓您在基礎結構即服務 (IaaS) 環境中使用。

在 IaaS 裝載模型中，客戶只需要針對 Azure 裝載 VM 的運作時間與其處理能力支付費用。 IaaS 提供高層級的控制，因為客戶負責管理其自有 VM，但但通常這樣做成本會比較高，因為 VM 是依運作時間與使用量來計費。 此外，客戶負責管理 VM，並套用補充程式及保護 VM 免於惡意程式碼威脅等。

IaaS 模型不再此文章的討論範圍內，此文章著重在藍圖的 PaaS 部署。

## <a name="the-healthcare-aiml-blueprint"></a>醫療保健 AI/ML 藍圖

該藍圖會建立在醫療保健環境中使用此技術的起點。 當藍圖安裝到 Azure 時，會建立所有資源、服務與數個使用者帳戶來支援具有適當執行者、權限與服務的 AI/ML 案例。

該藍圖包括 AI/ML 實驗，可預測病人的住院天數，這有助於預測人力、床位與其他後勤支援需求。 套件包括安裝指令碼、範例程式碼、測試資料、安全性與隱私權支援等。

## <a name="blueprint-technical-resources"></a>藍圖技術資源

下列資源全部都可在此 GitHub 存放庫中找到。

主要資源為：

1. 用於部署、設定與其他工作的 [PowerShell](https://docs.microsoft.com/powershell/scripting/powershell-scripting?WT.mc_id=ms-docs-dastarr) 指令碼。
2. 包括如何使用安裝指令碼的[詳細安裝指示](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)。
3. [詳盡的常見問題集](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/faq.md)。

此模型的交叉剪輯顧慮包括身分識別與安全性，這兩者在處理病人資料時特別重要。 下圖顯示 ML 管線的元件。

![ML 管線](assets/sg-healthcare-ai-blueprint-assets/ml-pipeline.png)

下圖顯示已安裝的 Azure 產品。 q每個資源或服務都提供 AI/ML 處理解決方案的元件，包括身分識別與安全性的交叉剪輯顧慮。

![元件區域](assets/sg-healthcare-ai-blueprint-assets/component-zones.png)

在符合規範的醫療保健環境中實作新系統非常複雜。 例如，確保系統的各層面都符合 HIPAA 規範並取得 HITRUST 認證所需的心力遠比開發輕量型解決方案還多。 該藍圖會安裝身分識別與資源權限，以協助解決這些複雜問題。

該藍圖也提供額外的指令碼與資料，以模擬並研究收病人與讓病人自動出院的結果。 這些指令碼可讓員工立即開始了解如何在安全、隔離的案例中使用該解決方案來實作 AI 與 ML。

### <a name="additional-blueprint-resources"></a>額外藍圖資源

該藍圖提供適用於技術員工的實用指導方針與指示，而且也包括成品，有助於建立完全可運作的安裝。 這些其他成品包括：

1. [威脅模型](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=01828de2-9555-4bac-a2a0-44e9ed2eeeaf&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)，可搭配 [Microsoft Threat Modeling Tool](https://www.microsoft.com/download/details.aspx?id=49168&WT.mc_id=ms-docs-dastarr) 使用。 此威脅模型顯示解決方案的各元件、各元件之間的資料流程，以及信任邊界。 想要延伸基底藍圖或想要從安全性觀點了解系統架構的人，可以將該工具用於威脅模型建構。

2. Excel 活頁簿中的 [HITRUST 客戶責任矩陣](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=eab85244-b9ab-490a-9e2a-611153f7d3af&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)。  這顯示您 (客戶) 必須為矩陣中的每個需求提供 Microsoft 所提供的項目。 在此文章中，有關此責任矩陣的詳細資訊包含在此文件的＜安全性與合規性 > 藍圖責任矩陣＞一節中。

3. [HITRUST 健康資料與 AI 檢閱](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=ffc32e44-665e-46c5-b753-163d55a17d27&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr) 白皮書 \(英文\) 會針對通過 HITRUST 認證的需求來檢查藍圖。

4. [HIPAA 健康資料與 AI 檢閱](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=d5ce675c-3e83-45db-98a6-ae77fc439436&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr) 白皮書 \(英文\) 會檢閱架構是否符合 HIPAA 規範。

這些資源位於 [GitHub 上的此位置](https://github.com/Azure/Health-Data-and-AI-Blueprint)。

## <a name="installing-the-blueprint"></a>安裝藍圖

讓此藍圖解決方案開始運作需要一些時間。 您可能必須稍微熟悉 PowerShell 指令碼，不過我們提供逐步指示來協助完成安裝，因此技術員工將能成功部署此藍圖，不論其對指令碼的熟悉程度為何。

技術員工只需要 Azure 30 分鐘到 1 小時的使用經驗，就能安裝該藍圖。

### <a name="the-installation-script"></a>安裝指令碼

該藍圖提供有關如何安裝的實用指導方針與指示。 它也提供用於安裝及解除安裝藍圖服務與資源的指令碼。 呼叫 PowerShell 部署指令碼非常簡單。 在安裝該藍圖之前，必須收集特定資料，並使用該資料做為 deploy.ps1 指令碼的引數，如下所示。

```powershell
.\deploy.ps1 -deploymentPrefix <prefix> `
            -tenantId <tenant id> ` # also known as the AAD directory
            -tenantDomain <tenant domain> `
            -subscriptionId <subscription id> `
            -globalAdminUsername <user id> ` # ID from your AAD account
            -deploymentPassword <universal password> ` # applied to all new users and service accounts
            -appInsightsPlan 1 # we want app insights set up
```

### <a name="the-installation-environment"></a>安裝環境

**重要！** 請勿從 Azure 以外機器安裝該藍圖。 若您在 Azure 中建立乾淨的 Windows 10 (或其他 Windows VM) 並從該處執行安裝指令碼，將比較有可能成功安裝。 此技術使用雲端式 VM 將延遲降到最低，並協助建立順暢的安裝體驗。

在安裝期間，指令碼會呼翹其他套件以載入並使用。 從 Azure 中的 VM 安裝時，安裝機器與目標資源之間的延遲將大幅降低。 不過，下載的某些指令碼套件仍像 Azure 環境外部的指令碼套件一樣容易發生延遲。

### <a name="install-failure-dont-panic"></a>安裝失敗！ (別慌張)

安裝程式會在安裝期間下載一些外部套件。 有時候，指令碼資源要求將會因為安裝機器與套件之間的延遲而逾時。 發生此情況時，您有兩個選擇：

1. 不對安裝指令碼進行任何變更，再次執行安裝指令碼。 安裝程式會檢查已配置的資源，並指安裝需要的項目。 雖然此方式有用，但仍有安裝指令碼嘗試配置已配置之資源的風險。 這可能導致錯誤，而且安裝將會失敗。

2. 您仍然必須執行 deploy.ps1 指令碼，但傳遞不同的引數以解除安裝藍圖服務。

```powershell
.\deploy.ps1 -clearDeploymentPrefix <prefix> `
             -tenantId <value> `
             -subscriptionId <value> `
             -tenantDomain <value> `
             -globalAdminUsername <value> `
             -clearDeployment
```

解除安裝完成之後，變更安裝指令碼中的前置詞，並嘗試重新安裝。 延遲問題可能不會再發生。 下載指令碼套件時，若安裝失敗，請執行解除安裝程式指令碼，然後重新執行安裝程式。

執行解除安裝指令碼之後，下列項目將會消失。

- 由安裝程式指令碼安裝的使用者
- 資源群組與其對應的服務已消失，包括資料儲存體
- 應用程式向 AAD (Azure Active Directory) 註冊

請注意，Key Vault 維持為「虛刪除」，而且您無法在入口網站中看到它時，它並不會解除配置 30 天。 這樣可讓您在需要時重構該 Key Vault。 若要深入了解這的意涵與如何處理，請參閱此文章的＜技術問題 > Key Vault＞一節。

### <a name="reinstall-after-an-uninstall"></a>在解除安裝之後重新安裝

若需要在解除安裝藍圖之後重新安裝，您必須變更下一個部署中的前置詞，因為如果您沒有變更前置詞，解除安裝的 Key Vault 將會導致錯誤。 此文章的＜技術問題 > Key Vault＞一節中有詳細的說明。

### <a name="required-administrator-roles"></a>必要系統管理員角色

要安裝藍圖的人必須是 AAD 中的全域系統管理員角色。 安裝帳戶也必須是要使用之訂用帳戶的 Azure 訂用帳戶系統管理員。 若要執行安裝的人沒有擁有上述兩個角色，安裝將會失敗。

![藍圖安裝程式](assets/sg-healthcare-ai-blueprint-assets/blueprint-installer.png)

此外，由於與 AAD 的緊密整合，安裝並非設計為搭配 MSDN 訂閱運作。 必須使用標準 Azure 帳戶。 如果有需要，使用[取得免費試用版](https://azure.microsoft.com/free/?WT.mc_id=ms-docs-dastarr)以獲得可花在安裝藍圖解決方案並執行其示範的點數。

## <a name="adding-other-resources"></a>新增其他資源

Azure 藍圖安裝只包括實作 AI/ML 使用案例所需的服務。 不過，您可以新增更多資源或服務到 Azure 環境，讓它成為可執行其他工作的測試平台，或成為生產系統的起點。 例如，您可以在相同的訂用帳戶與 AAD 中新增其他 PaaS 福ˋ或 IaaS 資源。

需要更多 Azure 功能時，可以新增資源 (例如 [Cosmos DB](/azure/cosmos-db/introduction?WT.mc_id=ms-docs-dastarr) 或新的 [Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=ms-docs-dastarr)) 到解決方案。 新增資源或服務時，請確定它們設定為符合法規與原則的安全性與隱私權原則。

您可以使用 [Azure REST API](https://docs.microsoft.com/rest/api/?view=Azure&WT.mc_id=ms-docs-dastarr)、[Azure PowerShell 指令碼](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-6.6.0&WT.mc_id=ms-docs-dastarr)或使用 [Azure 入口網站](https://portal.azure.com/?WT.mc_id=ms-docs-dastarr)來建立新資源與服務。

## <a name="using-machine-learning-with-the-blueprint"></a>搭配藍圖使用機器學習

藍圖是建立來透過模型中使用的迴歸演算法來示範 ML 案例，此演算法會預測 [病人的住院天數](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr#example-use-case)。 這是衛生保健提供者常執行的預測，因為這有助於排定人力與其他作業決策。 此外，可以偵測特定疾病患者增加或減少時平均住院天數的異常情況。

### <a name="ingesting-training-data"></a>內嵌定型資料

安裝藍圖且所有服務都正常運作之後，就可以內嵌要分析的資料。 我們提供 100,000 筆[可內嵌的病人記錄](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr#ingest)，這些記錄可搭配模型使用。 內嵌病人記錄是使用 [Azure Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio?WT.mc_id=ms-docs-dastarr) 來執行病人住院天數實驗的第一個步驟，如下所示。

![擷取](assets/sg-healthcare-ai-blueprint-assets/ingest.png)

藍圖包括一個實驗，以及在 Machine Learning Studio (MLS) 中執行 ML 作業所需的資料。 該範例在實驗中使用已定型的模型來根據許多變數預測病人住院天數。

在此示範環境中，內嵌到 Azure SQL 資料庫的資料沒有任何缺失或缺少資料元素。 此資料是乾淨的。 通常，會內嵌不乾淨的資料，而且在使用它來進行機器學習定型演算法或在 MD 作業中使用它之前，必須先「清理」它。 缺少資料或資料中有不正確的值將會對 ML 分析的結果造成負面影響。

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

許多醫療保健組織都沒有技術員工可專注在 ML 專案。 這通常表示建立 ML 解決方案時未能有效利用有價值的資料，或花許多錢聘請顧問來協助建立 ML 解決方案。

AI/ML 專家以及正在學習 AI/ML 的人都可以使用 Azure Machine Learning Studio 來設計實驗。 MLS 是可用來建立 ML 實驗的網頁型設計環境。 使用 MLS 時，您可以建立模型、將模型定型、針對模型進行評估以及為模型評分，進而在使用不同的工具來開發模型時節省寶貴的時間。

MLS 提供適用於 ML 工作負載的完整工具組。 這表示相較於其他 ML 工具，ML 新手能以較快的速度使用工具並產生結果。 這可讓您的 IT 員工著重在於其他各方面提供價值，而不是引進 ML 專家。 若您自己的醫療保健組織中能擁有此功能，就表示可以測驗任何假說，並分析產生的資料以獲得可採取行動的見解，如病人干預主義提供[預先撰寫的模組](/azure/machine-learning/studio-module-reference/index?WT.mc_id=ms-docs-dastarr#help-for-machine-learning-modules)以用於拖放畫布、以視覺化方式撰寫端點對端點資料科學工作流程為實驗。

您可以發現預先撰寫的模組，其中封裝特定演算法，例如決策樹、決策預測、叢集處理、時間序列、異常偵測等。

自訂模組可新增到任何實驗。 這些是以 [R 語言](/azure/machine-learning/studio/extend-your-experiment-with-r?WT.mc_id=ms-docs-dastarr)或以 [Python](/azure/machine-learning/studio/execute-python-scripts?WT.mc_id=ms-docs-dastarr) 寫的。 這可讓您使用預先建置的模組自訂邏輯來建立更複雜的實驗。

MLS 可讓您[建立並使用](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model?WT.mc_id=ms-docs-dastarr)學習模組，以及佈建一組預先設計的實驗以在通用應用程式中使用。 此外，可以將新的實驗新增到 MLS，而不需要變更任何藍圖資源。

為節省時間，請瀏覽 [Azure AI 資源庫](https://gallery.azure.ai/industries/healthcare?WT.mc_id=ms-docs-dastarr) \(英文\) 以尋找適用於特定產業 (包括醫療保健) 且可立即使用的 ML 解決方案。 例如，資源庫包括乳癌偵測與心臟病預測的解決方案與實驗。

## <a name="security-and-compliance"></a>安全性與合規性

安全性與合規性是在醫療保健環境中建立、安裝或管理軟體系統時必須謹記在心的兩個最重要項目。 若不符合必要安全性原則與認證需求，在採用軟體系統方面的投資可能會受到影響。

雖然此文章與醫療保健藍圖著重在技術安全性，但其他類型的安全性也一樣重要，包括實體安全與系統管理安全性。 這些安全性主題不在此文章的討論範圍內，此文章著重在藍圖的技術安全性。

### <a name="principle-of-least-privilege"></a>最低權限原則

藍圖會安裝具有支援角色的具名使用者，並限制其只能存取解決方案中的資源。 此模型稱為「最低權限原則」，這是系統設計中的一種資源存取方式。 原則會說明服務與使用者帳戶只應該擁有法定用途所需之系統與服務存取權。

此安全性模型可確保系統的合規性符合 HIPAA 與 HITRUST 需求，因此可去除組織所面臨的風險。

### <a name="defense-in-depth"></a>深層防禦

使用多個安全性抽象層的系統設計是使用深層防禦。 深層防禦提供多種層級的安全性備援。 它表示您不會只仰賴單一的防禦層級。 它可確保使用者與服務帳戶擁有對資源、服務與資料的適當存取權。 Azure 在系統架構的各層級提供安全性與監視資源，以針對整個技術範疇提供深層防禦。

在軟體系統 (例如藍圖所安裝的軟體系統) 中，使用者可以登入，但並未獲得特定資源的權限。 此深層防禦範例是由 RBAC (角色型存取控制) 與 AAD 所提供，而且支援最低權限原則。

雙因素驗證也是一種技術深層防禦的形式，而且可以在安裝藍圖時選擇包括在內。

### <a name="azure-key-vault"></a>Azure 金鑰保存庫

Azure Key Vault 服務是一個容器 (亦稱為「保存庫」)，可用來存放祕密、憑證與應用程式所使用的其他資料。 這些包括資料庫字串、REST 端點 URL、API 金鑰，以及開發人員不想要以硬式編碼方式撰寫到應用程式中或在 .config 檔案中散佈的任何其他項目。

此外，保存庫可由應用程式服務身分識別或具有 AAD 權限的其他帳戶存取。 這樣可讓需要保存庫內容的應用程式在執行階段存取祕密。

存放在保存庫中的金鑰可能已加密或簽署，而且可以根據任何安全性顧慮監視金鑰用途。

當您刪除 Key Vault 時，系統並不會立即將它從 Azure 清除。 此文章的＜技術問題 > Key Vault＞一節中說明關於這個的意涵。

### <a name="application-insights"></a>Application Insights

醫療保健組織通常有任務以及必須穩定並以具備備援機制之方式執行的維生系統。 必須偵測服務異常或中斷並即時修正。 [Application Insights](/azure/application-insights/app-insights-proactive-application-security-detection-pack?WT.mc_id=ms-docs-dastarr) 是一個 Application Performance Management (APM) 技術，它可以監視應用程式並在發生問題時傳送警示。 它會在執行階段監視應用程式是否發生錯誤或應用程式異常狀況。 它是設計來搭配多種程式設計語言使用，並提供豐富的功能集以協助確保應用程式健康情況良好並順暢地執行。

例如，應用程式可能發生記憶體流失。 Application Insights 可透過豐富的報告功能與它所監視的 KPI，來協助您尋找此類問題並進行診斷。 Application Insights 是適用於應用程式開發人員的強固 APM 服務。

此[互動式示範](https://analytics.applicationinsights.io/demo#/discover/home?WT.mc_id=ms-docs-dastarr)顯示 Application Insights 的主要特色與功能，包括可由醫療保健組織中的技術員工用來監視應用程式狀態與健康情況的全方位監視儀表板。

#### <a name="azure-security-center"></a>Azure 資訊安全中心

即時安全性與 KPI 監視在任務關鍵應用程式中是必要的。 Azure 資訊安全中心 (ASC) 有助於確保您的 Azure 資源安全且受保護。 ASC 是一個安全性管理與進階威脅防護服務。 它可以用來跨您的工作負載套用安全性原則、限制暴露於威脅的部分，以及偵測及回應攻擊。

資訊安全中心標準版提供下列服務。

- **混合式安全性** – 取得您所有內部部署和雲端工作負載中安全性的統一檢視。 在由使用 Azure 之醫療保健組織所使用的混合式雲端網路中，這特別實用。
- **進階威脅偵測** - ASC 使用進階分析與 [Microsoft Intelligent Security Graph](http://cloud-platform-assets.azurewebsites.net/intelligent-security-graph/?WT.mc_id=ms-docs-dastarr) 勝過不斷進化的網路攻擊並立即將其移除。
- **存取與應用程式控制** - 透過套用適用於您特定工作負載並由機器學習服務提供支援的允許清單建議，來封鎖惡意程式碼與垃圾應用程式。

在健康情況 AI 藍圖的環境中，ASC 會分析系統元件並提供儀表板，顯示訂用帳戶中之服務與資源中的弱點。 不同的儀表板元件提供解決方案顧慮的詳細資訊，如下所示。

- 原則與合規性
- 資源安全性檢查
- 威脅保護

下面是已找出 13 個改進系統威脅弱點建議的範例儀表板。 它也顯示僅有 46% 的 HIPAA 與原則合規性。

![威脅保護](assets/sg-healthcare-ai-blueprint-assets/threat_protection.png)

向下切入高嚴重性安全性問題會顯示受影響的資源與必須針對每個資源採取的補救措施，如下所示。

嘗試手動保護所有資源與網路的 IT 員工可能需要數小時才能完成。 ASC 可找出給定系統中的弱點，因此 IT 員工可以將多出來的時間花在其他策略性專案。 針對許多找出的弱點，ASC 都可以自動套用補救動作並保護資源，而不需要系統管理員深入研究問題。

![高風險](assets/sg-healthcare-ai-blueprint-assets/high_risks.png)

ACS 甚至會透過其威脅偵測與警示功能執行更多工作。 使用 ACS 來監視網路、機器與雲端服務中是否有外來攻擊與入侵後活動，以保護您的環境。
ASC 會自動從各種 Azure 資源收集、分析及整合安全性資訊與記錄。 

ASC 中的 ML 功能可讓它偵測人工方法無法找到的威脅。 ASC 中會顯示優先安全性警示清單，以及快速調查問題所需的資訊，和如何修復攻擊行為的建議。

### <a name="rbac-security"></a>RBAC 安全性

[角色型存取控制](/azure/role-based-access-control/role-assignments-portal?WT.mc_id=ms-docs-dastarr) (RBAC) 會提供或拒絕對受保護資源的存取權，有時候會為每個資源會使用不同的特定權限。 這可確保只有適當的使用者可以存取其受指派系統元件。 例如，資料庫系統管理員可以存取包含加密病人資料的資料庫，而衛生保健提供者只能透過顯示資料的應用程式存取適當病人的記錄。 這通常是電子醫療記錄或電子健康資料系統。 護士必須存取資料庫，而資料庫系統管理員不需要查看病人的健康記錄資料。

RBAC 是 Azure 安全性系統的一部分，而且可讓您進行精準的 Azure 資源系統管理，因此可協助您完成上述設定。 為每個使用者進行精細設定的功能可讓安全性與系統管理員以非常精細的設定方式指派權限給每個使用者。

### <a name="blueprint-responsibility-matrix"></a>藍圖責任矩陣

HITRUST 客戶責任矩陣是一個 Excel 文件，它支援針對建置在 Azure 上的系統實作並記錄安全性控制措施的客戶。 該活頁簿會列出相關的 HITRUST 需求，並說明 Microsoft 與客戶如何負責符合每一個需求。

了解在雲端環境中實作安全性控制措施的共同責任對在 Azure 上建置系統的客戶而言是基本要求。 實作特定安全性控制措施可能是 Microsoft 的責任、客戶的責任或 Microsoft 與客戶之間的共同責任。 不同的雲端實作會影響責任在 Microsoft 與客戶之間分攤的方式。

請參閱下面的責任表以取得範例。

|Azure 責任  |客戶責任  |
|---------|---------|
|Azure 負責實作、管理及監視與其服務佈建環境相關的資訊保護計畫方法與機制。 | 客戶負責實作、設定、管理及監視用於存取及取用 Azure 服務之客戶控制資產的資訊保護計畫方法與機制。 |
|Azure 負責實作、管理及監視與其服務佈建環境相關的帳戶管理方法與機制。 |客戶也負責已部署之 Azure 虛擬機器執行個體與常駐應用程式元件的帳戶管理。|

這只是部署雲端系統時必須考慮之許多責任中的其中兩個範例。 HITRUST 客戶責任矩陣是設計來支援具有 Azure 系統實作之組織的 HITRUST 合規性。

## <a name="customization"></a>自訂

在安裝藍圖之後自訂它是很常見的。 自訂環境的原因與技術各有不同。

您可以透過修改安裝指令碼，在安裝之前自訂藍圖。 雖然您可以這樣做，但建議您建立獨立的 PowerShell 指令碼來在初始安裝完成之後執行。 初始安裝完成之後，也可以透過入口網站將服務新增到系統中。

自訂可能包括下列任一項或更多項。

- 新增實驗到 Machine Learning Studio
- 新增額外的不相關服務到環境中
- 修改資料內嵌與 ML 實驗輸出以使用 Azure SQL patientdb 資料庫以外的資料來源
- 提供生產資料給 ML 實驗
- 清除任何要內嵌以符合實驗要求的專屬資料

自訂安裝與使用任何 Azure 解決方案沒有不同。 您可以新增或移除服務或資源，以佈建新功能。 自訂藍圖時請注意，不要修改整體 ML 管線以確保實作繼續運作。

## <a name="technical-issues"></a>技術問題

下列問題會導致藍圖安裝失敗或以不想要的設定安裝。

### <a name="key-vault"></a>Key Vault

在刪除 Azure 資源時，金鑰保存庫是獨特的。 保存庫會由 Azure 針對復原目的保留。 因此，每次執行安裝指令碼時，都必須將不同的前置詞傳遞到安裝指令碼，否則安裝將會因為與舊的值名稱衝突而失敗。 金鑰保存庫與所有其他資源都是使用您提供到安裝指令碼的前置詞來命名。

由安裝指令碼所建立的金鑰保存庫會維持「虛刪除」狀態 30 天。 雖然目前無法透過入口網站存取，但虛刪除的[金鑰保存庫可從 PowerShell 管理](/azure/key-vault/key-vault-soft-delete-powershell?WT.mc_id=ms-docs-dastarr)，甚至也可以手動刪除。

### <a name="azure-active-directory"></a>Azure Active Directory

強烈建議您在空的 AAD 中安裝藍圖，而不要安裝到生產系統中。 建立新 AAD 執行個體並在安裝期間使用其租用戶識別碼，以避免新增藍圖帳戶到您的即時 AAD 執行個體。

## <a name="technologies-presented"></a>使用的技術

- 深入了解 [Azure 健康資料與 AI 藍圖](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)。
- 下載、複製或將 [這裡的 GitHub 保存庫分岔](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)。
- [Machine Learning Studio](/azure/machine-learning/?WT.mc_id=ms-docs-dastarr) 是用來建立 Machine Learning 實驗的工作區與工具資料科學。 它可讓您使用內建演算法、特殊用途小工具，以及 Python 與 R 指令碼。
- 祕密、憑證與其他私人資料是存放在 [Azure Key Vault](/azure/key-vault/key-vault-whatis?WT.mc_id=ms-docs-dastarr) 中。
- 指令碼語言 PowerShell 是用來設定藍圖，雖然所需的命令是在安裝指示中提供。
- [Azure AI 資源庫](https://gallery.azure.ai/) \(英文\) 依客戶的產業提供對客戶而言非常實用的 AI/ML 解決方案食譜盒。 有一些由資料科學家與其他醫療保健專家發佈的解決方案。
- [Azure 資訊安全中心](/azure/security-center/?WT.mc_id=ms-docs-dastarr)提供對您應用程式行為、弱點與減緩技術的見解。

## <a name="wrapping-up"></a>總結

[Azure 健康資料 AI 藍圖](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)是完整的 ML 解決方案，而且可做為學習工具使用，讓技術人員更了解 Azure 以及如何確保系統符合醫療保健法規需求。 它也可以用為使用 Azure Machine Learning Studio 做為焦點之生產系統的起點。

[下載藍圖](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)以在數小時內 (而非數天或數週) 開始使用您的實作。 若您有關於安裝或藍圖的問題，請瀏覽[常見問題集頁面](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/faq.md) \(英文\)。

下載支援附隨品以更深入了解安裝與 ML 實驗背後的藍圖實作。 此附隨品包括下列項目。

1. [HITRUST 客戶責任矩陣](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=eab85244-b9ab-490a-9e2a-611153f7d3af&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
2. [全方位的威脅模型](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=01828de2-9555-4bac-a2a0-44e9ed2eeeaf&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
3. [HITRUST 健康資料與 AI 檢閱白皮書](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=ffc32e44-665e-46c5-b753-163d55a17d27&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
4. [HIPAA 健康資料與 AI 檢閱](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=d5ce675c-3e83-45db-98a6-ae77fc439436&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics?WT.mc_id=ms-docs-dastarr)

不論您是將藍圖用於學習用途或用於為您的組織培育 AI/ML 解決方案的種子，它都提供在 Azure 中使用 AI/ML 的起點，而且焦點在於醫療保健。