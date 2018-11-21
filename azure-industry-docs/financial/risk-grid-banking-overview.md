---
title: 銀行業中的風險網格運算概觀
author: dstarr
ms.author: dastarr
ms.date: 04/12/2018
ms.topic: article
ms.service: industry
description: 提供在 Azure 上實作銀行業中的風險網格運算的商業考量。
ms.openlocfilehash: 49d3d5223bee85689043d84eb5236cca4f53fc03
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654175"
---
# <a name="risk-grid-computing-in-banking-overview"></a>銀行業中的風險網格運算概觀

## <a name="introduction"></a>簡介

在公司財務和投資銀行業中，其中一個最重要的工作是分析風險。

為了提供投資組合相關風險全面性的寫照，財務風險分析師會檢閱研究、監控經濟和社會狀況、持續掌握法規，並建立投資氣候的電腦模型。

影響投資組合的向量很多，而針對這些向量的風險分析足夠複雜，正好是電腦模型所需要的。 大多數分析師都花費相當多的時間處理電腦模型，為的是模擬和預測財務狀況會如何變化。 評估投資風險 (市場風險、信用風險和營運風險) 時，因為資料的數量和多樣性，處理預測性模型的運算負載可能會很大。

雲端運算可以讓分析師依需求存取大量的計算資源，而不需要花費資本成本或管理基礎結構，所以可以針對風險網格運算或風險模型化提供顯著的優點。 本篇文章審視運用 Microsoft Azure 擴充目前的風險網格運算資源，並且將風險網格運算工作負載的成本和速度最佳化。 涵蓋的主題包括安全且可靠的連線、批次處理，以及當內部部署的伺服器容量不足時根據需求擴充計算資源。

## <a name="grid-computing-services"></a>網格運算服務

分析師需要一個簡單又可靠的方式，將模型提供給批次處理管線，這個過程會先擷取資料，資料處理後進行分析，從產生的資料可以衍生出見解。

風險模型輸入資料有幾種形式，最常見的是 Excel 檔案或 .csv 檔案。 這些檔案通常會調整為更適合在風險運算管線後續階段處理風險模型的格式。 剖析和處理這些檔案的一個常用技巧是利用一組虛擬機器 (VM?WT.mc_id=gridbank-docs-dastarr) 一起合作進行批次處理來達成共同的目標。

[Azure Batch](/azure/batch/?WT.mc_id=gridbank-docs-dastarr) 是一項 Azure 服務，可以讓多個背景工作角色 VM 以平行方式執行，如下所示。 處理資料檔案並將結果送到機器學習系統或資料存放區，是背景工作角色節點的常見工作。 背景工作角色節點執行的應用程式程式碼是由客戶建立，因此幾乎所有動作都可以利用批次工作完成。

![內部部署批次](./assets/risk-grid-compute-assets/01-on-prem.png)

Azure 使用 Azure Batch 為風險網格運算提供絕佳的解決方案。 客戶可以使用 Azure Batch 擴充現有的風險運算網格，或者將內部部署的資源取代為完全雲端式的解決方案。

我們完全支援直接安全地連線至 Azure 雲端。 使用混合式網路連線到 Azure 時，Batch 的風險處理網格背景工作角色節點可以在連線到內部儲存的資料時存取模型資料。 客戶也可以將資料上傳到 Azure 中適當的儲存體，讓 Batch 可以直接存取資料。

## <a name="secure-connectivity-to-azure"></a>安全連線到 Azure

企業在 Azure 上建立風險網格運算解決方案時，通常會繼續使用現有的內部部署應用程式，例如交易系統、中台風險管理、風險分析等等。 Azure 變成是這些現有投資的擴充。

連線到雲端時，安全性是主要的考量。 直接連線到 Azure 時，第一步是考慮您目前的安全性模型。 如果客戶已經在內部使用 Active Directory (AD?WT.mc_id=gridbank-docs-dastarr)，可以利用現有的身分識別資源連線到 Azure。 服務帳戶可以位於內部的 AD。

### <a name="hybrid-network-solution"></a>混合式網路解決方案

混合式網路會將 Azure 直接與客戶的內部部署網路連線。 Azure 提供兩種模型，供您將目前的內部部署系統安全可靠地連線到 Azure：[Microsoft Azure ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbank-docs-dastarr) 與 [VPN 閘道](/azure/vpn-gateway/?WT.mc_id=gridbank-docs-dastarr)。 雖然實作方式、效能、成本與其他屬性各不相同，不過這兩種模型都是受信任的連線解決方案。

![Azure 連線能力](./assets/risk-grid-compute-assets/02-connectivity.png)

&quot;高載至雲端&quot;會在現有的資源突然增加時將運算作業卸載至雲端式機器，擴大客戶的資料中心或私人雲端資源。 因為雲端式風險運算網格是現有網路的一種簡單擴充，所以使用混合式網路模型可以輕鬆高載至雲端。

在上述邏輯架構中，除了簡單模型中的設定之外，還有幾種網路連線設定。 如需協助擬定如何將您的網路連線到 Azure 的決策及架構指引，請參閱[_將內部部署網路連線到 Azure_](/azure/architecture/reference-architectures/hybrid-networking/) 文章。

### <a name="rest-api-solution-over-internet"></a>透過網際網路的 REST API 解決方案

另一種建立混合式網路的方式是將資料上傳到 Azure 儲存體 (可以是檔案或 Blob 儲存體?WT.mc_id=gridbank-docs-dastarr)，然後讓 Batch 從儲存體讀取資料檔案。 這整個過程可以使用安全的 (SSL?WT.mc_id=gridbank-docs-dastarr) 連線來連線到 Azure，將文件儲存在 Azure 儲存體中，然後透過 [Batch 服務 REST API](/rest/api/batchservice/?WT.mc_id=gridbank-docs-dastarr) 或 SDK 搭配特定用途的應用程式管理風險網格運算作業，協調 Batch 執行。

### <a name="azure-data-factory"></a>Azure Data Factory

另一個適合您的解決方案是使用 [Azure Data Factory](/azure/data-factory/?WT.mc_id=gridbank-docs-dastarr)，這是一個雲端式的資料整合服務，用來撰寫大型儲存體、移動和處理管線。 需要時可以透過 Data Factory 管線上傳資料。 服務在 Azure 入口網站中提供視覺設計工具，供您在 Azure 中擷取、轉換和載入 (ETL?WT.mc_id=gridbank-docs-dastarr) 解決方案。 Data Factory 可以協助將資料擷取至 Azure，以便進一步處理。

## <a name="matching-processing-needs-with-demand"></a>讓處理需求與實際需求相符

運算風險時，無論是每天或是月底較重的負載，計算都會耗用大量運算資源。 這些計算不會全天候執行。 當風險計算不是在內部部署網格上執行時，雖然組織讓重要昂貴的伺服器處於沒有工作負載的狀態，但是還是需要電力、冷卻系統和資料中心空間的持續成本，以及其他固定成本。

### <a name="augmenting-on-premises-grid-with-azure-batch"></a>使用 Azure Batch 擴充內部部署網格

為了降低成本，企業可以選擇擁有和管理剛好足夠的背景工作角色節點，以滿足需求量較低時的需求。 然後將高需求的風險網格運算作業推送到 Azure 中的高效能伺服器，彈性地相應增加和減少工作負載需求。

Azure Batch 處理模型對於風險網格運算有幾項優點：

- 可以在各種內部部署系統中擴充現有的投資。
- 讓現有的基礎結構在需求很低時提供風險分析需求，解除配置以 Azure 為基礎的背景工作角色節點。
- 在需求很高時對風險運算網格提供額外的容量。
- 讓機器設定檔配合 Batch 工作負載所需的處理能力，即使負載需要[高效能運算 (HPC)](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) 設定時亦同。

常見的解決方案是當內部部署的背景工作角色全部都在使用時，自動在 Azure 中新增背景工作角色節點。 風險網格前端節點只會要求更多背景工作角色。 這會在 Azure 中自動調整網格背景工作角色節點的數目，並啟用彈性需求解決方案。

![混合式雲端](./assets/risk-grid-compute-assets/03-hybrid-cloud.png)

這種安排除了能夠有效率地使用資源之外，還提供其他好處。 如果是獨立的工作，增加更多背景工作角色可以線性調整負載。 Azure 也提供彈性，可以試用非常大型的 VM 執行個體或有幾張 GPU 卡的機器。 這樣的彈性適合進行實驗和創新。

如果需要更多計算容量，例如每季的估值，可以從 Azure Batch 的自動調整規模功能獲得額外的容量。 自動調整規模可以對 Batch 解決方案提供彈性。 比起擁有硬體，Azure 能夠調整資源，讓資源與需要的負載一致，以更低的成本提供明顯更大的容量。

大部分商用網格產品都支援某種形式的高載至雲端，讓風險分析負載的概念證明更簡單。 例如，[Microsoft HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) 可以在 Azure 中執行，而像是 TIBCO、Univa 以及其他公司的產品也可以。 透過 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/?WT.mc_id=gridbank-docs-dastarr) 可以取得這些協力廠商工具或系統。

### <a name="migrating-additional-resources-to-the-cloud"></a>將額外的資源移轉到雲端

隨著工作負載增加，或內部部署資料中心基礎結構變老舊，組織可以將風險網格運算的整個 Batch 處理移到 Azure。

### <a name="growing-into-azure"></a>進入 Azure

當內部部署機器的壽命已盡時，您可以進一步將背景工作角色節點分散到雲端。 Batch 前端節點也一樣。 這會讓內部部署網路與 Azure 之間的關係顛倒。 如果解除任何網路對網路產品 (例如 Azure ExpressRoute)，以及任何剩餘的內部部署背景工作節點，這可能會是一個減少成本的機會。

做了這個變更之後，使用各種不同的檔案輸入技術，就可以讓 Azure 使用這些資料。 Azure 有許多儲存體選項可供選擇，包括允許直接上傳資料的 REST 端點，而不是讓計算作業從內部部署網路選取資料。

 ![內部部署批次](./assets/risk-grid-compute-assets/04-batch-process.png)

在此模型中，所有的風險網格運算活動都可以在雲端中進行。 由背景工作角色處理的資料檔案可以儲存在 Azure 儲存體中，資料可以直接送入 Azure Data Lake，而 Azure HDInsight 可以處理機器學習需求。 最後，Power BI 和 Azure Analytics 是絕佳的資料分析工具，可以處理儲存在 Azure 中的所有資料。

### <a name="data-security-considerations-for-risk-grid-computing"></a>風險網格運算的資料安全性考量

雖然計算資料通常不包含任何個人識別資訊 (PII?WT.mc_id=gridbank-docs-dastarr)，但是大部分的銀行在將任何工作負載放入雲端之前，仍然會先進行安全性風險評估。 這項評估可能會需要 Microsoft 的投入，而且可能會產生安全性建議。

風險網格運算的重要考量是要[在 Azure VNet 內執行批次程序](/azure/batch/batch-virtual-network?WT.mc_id=gridbank-docs-dastarr)。 這可讓集區計算節點安全地與其他計算節點或內部部署網路進行通訊。 您應該建立適當的服務帳戶和網路服務群組 (NSG) 讓批次計算節點使用。 對於在 Azure 儲存體中傳輸中的資料和待用資料的資料加密，[Azure 也有解決方案](/azure/security/blueprints/financial-services-regulated-workloads?WT.mc_id=gridbank-docs-dastarr)。

要考量的某些區域會是：Active Directory (AD) 或非加入 AD 的計算節點 (適用於 Windows Server 節點?WT.mc_id=gridbank-docs-dastarr)、[VM 磁碟加密](/azure/security/azure-security-disk-encryption?WT.mc_id=gridbank-docs-dastarr)、計算輸入和輸出待用資料和傳輸中資料的安全性、Azure 網路設定、使用權限等等。 驗證也可能透過祕密金鑰在 REST API 層級處理。

## <a name="getting-started"></a>開始使用

許多客戶在內部已經使用風險運算網格。 如果貴公司是內部自行開發網格，請考慮使用 Azure Batch 擴充網格。 一個開始使用 Azure Batch 的好方法是複寫目前的處理應用程式邏輯，然後在 Azure 中以 Batch 作業執行，來擴充任何目前的內部部署解決方案。 根據我們應用程式的功能，這可能需要一個網路解決方案，將 Azure Batch 計算節點加入內部部署網路。

若要減少任何安全性、速度和連線可靠性的問題，請考慮使用 Azure ExpressRoute 或 VPN 閘道，將您的內部部署網路連線到 Azure。 接下來，您可能要讓您的內部部署前端節點佈建一個以 Azure 為基礎的背景工作角色節點叢集，視需要向上和向下微調。

最後，您可能已經準備好將風險運算基礎結構完整移轉到 Azure。 如果是，[這篇文章](/azure/batch/?WT.mc_id=gridbank-docs-dastarr)可以讓您立即開始。

## <a name="technologies-presented"></a>呈現的技術

[Azure Batch](/azure/batch/?WT.mc_id=gridbank-docs-dastarr) 可以擴充內部部署風險運算背景工作角色節點，根據需求動態提供計算資源。

[Azure DataLake](/azure/data-lake-store?WT.mc_id=gridbank-docs-dastarr) 為您的風險分析資料提供儲存空間、處理和分析。

[Azure ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbank-docs-dastarr) 可透過連線提供者所提供的私人連線，將您的內部部署網路擴充到 Azure。

[Azure HDInsight](/azure/hdinsight/?WT.mc_id=gridbank-docs-dastarr) 是完全受控的開放原始碼分析服務，可處理大量資料，例如月底批次執行所提供的資料。

[Microsoft HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) 可以佈建進行批次處理的高效能運算叢集。

[Power BI](/power-bi/?WT.mc_id=gridbank-docs-dastarr) 是一套風險分析師用來獲取和分享見解的商務分析工具。

[VPN 閘道](/azure/vpn-gateway/?WT.mc_id=gridbank-docs-dastarr)可以透過網際網路，將您的內部部署網路擴充到 Azure 雲端。

## <a name="conclusion"></a>結論

本文章涵蓋的解決方案是銀行業中的風險網格運算方式。 如果提供 Azure 產品和服務的豐富功能，以及各種現有的用戶端系統架構，也可以使用其他架構。 即便如此，只要提供本篇文章所呈現的優點，Batch 還是能為風險網格運算提供合理的模型。

[將內部部署網路擴充到 Azure](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbank-docs-dastarr) 可以讓 Azure 輕鬆存取網路資源和內部部署網路中已有的其他處理系統。 當內部部署機器的壽命已盡時，在 Azure 中完全使用 Batch 計算，會比支援混合式模型來得更適合。

另一種方式是在 Batch 作業開始之前先將檔案上傳到 Azure 儲存體，這樣就可以利用 Batch 的優點卻不需要混合式網路。 這可以透過漸進的方式完成，或是做為使用 Batch 執行的起點。

選取連線策略之後，一個開始使用風險運算的邏輯方式是將您現有的作業放入 Azure 計算背景工作角色節點，然後在測試環境中執行，看看是否需要變更任何程式碼。 [本篇文章提供一個起點](/azure/batch/batch-virtual-network?WT.mc_id=gridbank-docs-dastarr)，讓您以選擇的語言或工具開始使用 Azure Batch。

[銀行業風險網格運算解決方案指南](/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=banking-docs-dastarr)
