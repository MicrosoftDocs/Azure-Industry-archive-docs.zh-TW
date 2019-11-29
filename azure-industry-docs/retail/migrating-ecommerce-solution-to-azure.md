---
title: 將電子商務解決方案移轉到 Azure 的概觀
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 本文章會說明將電子商務基礎結構從內部部署移轉到 Azure 的各個階段。
ms.openlocfilehash: e918f1157dc2bc42a6c4d0decfef95a8daa7ccf0
ms.sourcegitcommit: b8f9ccc4e4453d6912b05cdd6cf04276e13d7244
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74263350"
---
# <a name="migrating-your-e-commerce-solution-to-azure-overview"></a>電子商務解決方案 Azure 移轉概觀

## <a name="introduction"></a>簡介

將現有電子商務解決方案移動到雲端，能為企業帶來許多優點：如此可實現延展性、提供客戶全天候存取能力，而且更容易與雲端服務整合。 但首先，要將電子商務解決方案移動到雲端就是件大工程，決策人員務必得了解所需的成本。 本文件會說明 Azure 移轉的範圍，旨在告知您各種選項。 第一階段始於 IT 專業人員將元件移動到雲端。 移到 Azure 後，會再說明電子商務團隊可採取何種步驟來提高 ROI 並發揮雲端的功效。

**十字路口**

雖然全球電子商務交易只佔零售總銷售額的一小部分，這個通路每年都呈現穩定成長的趨勢。 2017 年，電子商務佔零售總銷售額的 10.2%，相較於 2016 年只有 8.6% ([資料來源](https://www.emarketer.com/Report/Worldwide-Retail-Ecommerce-Sales-eMarketers-Updated-Forecast-New-Mcommerce-Estimates-20162021/2002182))。 隨著電子商務的日益成熟，加上雲端運算的問世，零售商走到了十字路口。  這是必須抉擇的時刻。 不斷演進的技術讓新功能得以實現，零售商因此能預見未來運用新功能的業務模式，並且根據當前能力的情況開始規劃通往現代化的途徑。

**改善客戶旅程**

電子商務主要著重於客戶旅程，而具有許多不同的屬性。 這些屬性可以分成四大類：探索、評估、購買及購買後。

會將客戶行為擷取為資料。 購物漏斗圖是一系列應用程式連接點的集合，使用者使用這些應用程式來檢視產品資料、交易、庫存、運送、訂單履行、客戶個人檔案、購物車及推薦產品等資訊。

典型的零售業仰賴大量的軟體解決方案，種類範圍從面向客戶的應用程式，一路向下延伸到底層的基礎應用程式。  下圖可一窺典型零售業採用的功能。

 ![](./assets/migrating-ecommerce-solution-to-azure/ecommerce-system-sketch.png)

雲端帶來了轉變的契機，可改變公司組織取得、使用及管理技術的方式。  其他效益還包括能降低資料中心的維護成本、提高可靠性與效能，而且能具備增設其他服務的彈性。 在這個使用案例中，要來看看零售業能夠採取何種途徑，將現有基礎結構移轉到 Azure。 也會使用重新裝載、重構及重建等階段式方法，發揮出新環境的優點。 儘管有許多公司組織會依此步驟逐步實行現代化，不過在大多數情況下，任何一個階段都可以當成起點。  組織可以選擇放棄將目前的應用程式重新裝載於 Azure 上，直接跳到重構甚至重建。  這樣的決策需視應用程式與組織而定，才能充分滿足其現代化需求。

## <a name="rehost"></a>重新裝載

亦稱為「原形轉移」，這個階段必須將實體伺服器與 VM 按原樣移轉到雲端。 光是將目前的伺服器環境直接移轉到 IaaS，就能獲得節約成本、保障安全性、提高可靠性等優點。 節約效益來自於技術的運用，例如在適當大小的 VM 上執行工作負載。 目前內部部署 VM 與實體機器的能力經常超出零售商的日常需求。 VM 必須能夠處理一年僅發生幾次的季節性業務尖峰。 因此，在離峰的淡季期間，還是得支付未使用的功能。 透過 Azure，就可以根據目前業務週期的需求選擇大小適中的 VM。

在 Azure 中重新裝載分為三個階段：

- **分析**：識別並清查內部部署資源，如應用程式、工作負載、網路及安全性。 在這個階段結束時，將能獲得現有系統的完整文件記錄。
- **移轉**：將每個子系統從內部部署設施移動到 Azure。 在這個階段中，會將 Azure 當做資料中心的延伸來使用，同時保持應用程式的通訊。
- **最佳化**：將系統移動到 Azure 後，確保各個項目的大小設定適當。 如果環境顯示某些 VM 分配到的資源太多，請變更 VM 類型，調整為更加合適的 CPU、記憶體和本機儲存體組合。

### <a name="analyze"></a>分析

執行下列動作：

1. 列出內部部署伺服器和應用程式。 這個動作仰賴代理程式或管理工具來收集伺服器、伺服器上執行的應用程式、目前伺服器使用量，以及伺服器及其應用程式設定方式的中繼資料。 此結果是環境中所有伺服器和應用程式的報告。
1. 識別相依性。 您可以使用工具來識別哪些伺服器會彼此溝通，又有哪些應用程式會互相通訊。 此結果是所有應用程式與工作負載的對應。 這些對應會饋送至移轉計劃。
1. 分析設定。 目標是要得知在 Azure 中執行 VM 後，需要哪些類型的 VM。 此結果是一份列出所有可移動到 Azure 之應用程式的報告。 設定可以進一步分類為：
      1. 不需要修改
      1. 需要基本修改，如命名變更
      1. 需要稍微修改，如少量程式碼變更
      1. 需要額外處理才能移動的不相容工作負載
1. 編列預算。 您現在有一份列舉出各個 CPU 還有記憶體等項目的清單，其中也列出了每個應用程式的需求。 請將這些工作負載置放在適當大小的 VM 上。 雲端平台的帳單計費以使用量為準。 有工具可協助將需求對應到適當大小的 Azure VM。 如果要移轉 Windows VM 或 SQL Server，也建議您參考 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/?WT.mc_id=retailecomm-docs-scseely)，以利減少花費在 Azure 上的費用。

Microsoft 提供了多種工具來分析系統及編列目錄。 如果執行的是 VMWare，可以使用 [Azure Migrate](/azure/migrate/migrate-overview?WT.mc_id=retailecomm-docs-scseely) 來協助探索及評量。 這款工具可以找出能移動到 Azure 的機器、針對應執行的 VM 類型提出建議，以及預估工作負載的成本。 若是 Hyper-V 環境，請使用 [Azure Site Recovery 部署規劃工具](/azure/site-recovery/hyper-v-deployment-planner-overview?WT.mc_id=retailecomm-docs-scseely)。 若是要移動數百部或更多 VM 的大規模移轉，建議與 [Azure 移轉合作夥伴](https://azure.microsoft.com/migration/partners/?WT.mc_id=retailecomm-docs-scseely)合作。 這些合作夥伴具備移動工作負載的專業知識和經驗。

### <a name="migrate"></a>移轉

開始規劃要將哪些服務移動到雲端，以及移轉的順序。 由於這個階段涉及到工作負載的移動，因此請遵循以下順序：

1. 建置網路。
2. 納入身分識別系統 (Azure Active Directory)。
3. 在 Azure 中佈建儲存體項目。

在移轉期間，Azure 環境是內部部署網路的延伸。 您可以將邏輯網路與 [Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview?WT.mc_id=retailecomm-docs-scseely)連接。 至於絕不會接觸到網際網路的私人連線，則可選擇使用 [Azure ExpressRoute](/azure/expressroute/?WT.mc_id=retailecomm-docs-scseely) 來維持網路與 Azure 之間的通訊。 也可以使用站對站 VPN，讓 Azure VPN 閘道與內部部署 VPN 裝置通訊，同時透過 Azure 與網路之間的加密通訊安全傳送所有流量。 我們在[這裡](/azure/architecture/reference-architectures/hybrid-networking/vpn?WT.mc_id=retailecomm-docs-scseely)發佈了一個參考架構，詳細解說了如何設定混合式網路。

網路設定完畢後，請開始規劃業務持續性。 建議做法是使用即時複寫，將內部部署資料移動到雲端，並確保雲端與現有資料相同。 電子商務商店永遠不打烊：以複製的方式既能從內部部署切換到 Azure，且能將對客戶的影響降到最低。

開始將資料、應用程式和相關伺服器移動到 Azure。 許多公司使用 [Azure Site Recovery](/azure/site-recovery/site-recovery-overview?WT.mc_id=retailecomm-docs-scseely) 服務來移轉到 Azure。 這項服務著重於商務持續性和災害復原 (BCDR)， 最適合用於從內部部署設施移轉到 Azure。 您的實作小組可以在[這裡](/azure/site-recovery/migrate-tutorial-on-premises-azure?WT.mc_id=retailecomm-docs-scseely)閱讀詳細資訊，了解如何將內部部署 VM 和實體伺服器移轉到 Azure。

將子系統移動到 Azure 之後，請進行測試，以確保一切正常。 等到所有問題都解決了，再將工作負載移動到 Azure。

### <a name="optimize"></a>最佳化

現階段，您會繼續監視環境並變更基礎計算選項，配合環境變更調整工作負載。 監視環境健康情況的人員需注意各項資源的使用情況。 目標應設定在大部分的 VM 都有 75-90% 的使用率。 對於使用率極低的 VM，請考慮在其上裝載更多應用程式，或是移轉到 Azure 上費用最低的 VM，以期維持在適當的效能等級。

Azure 也會提供環境最佳化工具。 [Azure Advisor](/azure/advisor/advisor-overview?WT.mc_id=retailecomm-docs-scseely) 會監視環境的元件，並提供符合最佳做法的個人化建議。 這些建議可協助改善您應用程式所用資源的效能、安全性及可用性。 Azure 入口網站也會公開應用程式健康情況的相關資訊。 您的 VM 應利用 [Linux 及 Windows 的 Azure 虛擬機器延伸模組](/azure/virtual-machines/extensions/overview?WT.mc_id=retailecomm-docs-scseely)。 這些延伸模組會提供部署後設定、防毒、應用程式監視和更多功能。 您也可以利用其他許多 Azure 服務來診斷網路和使用服務，並透過[網路監看員](/azure/network-watcher/network-watcher-monitoring-overview?WT.mc_id=retailecomm-docs-scseely)、[服務對應](/azure/monitoring/monitoring-walkthrough-servicemap?WT.mc_id=retailecomm-docs-scseely)、[Application Insights](/azure/application-insights/app-insights-overview?WT.mc_id=retailecomm-docs-scseely) 及 [Log Analytics](/azure/log-analytics/log-analytics-overview?WT.mc_id=retailecomm-docs-scseely) 等服務發出警示。

雖然有部分組織團隊持續在將 Azure 中的系統最佳化，不過開發小組已可開始進入移轉後階段：重構。

## <a name="refactor"></a>重構

完成移轉後，您的電子商務應用程式就可以開始利用 Azure 中的新家。 不必等到整個環境都移轉完成後再開始重構階段。 如果您的 CMS 小組已經移轉，但 ERP 小組尚未進行，也沒關係， CMS 小組仍可開始進行重構工作。 這個階段關係到使用其他 Azure 服務來重構應用程式，以求將成本、可靠性和效能最佳化。 採用原形移轉不僅能使用提供者管理的硬體和作業系統，在這個模式下，還可以利用雲端服務來降低成本。 您可以僅變更少量應用程式程式碼或設定，而繼續沿用目前的應用程式，還可以將應用程式連接到容器、資料庫和身分識別管理系統等新的基礎結構服務。

重構工作僅需變更極少量的程式碼和設定。 您之所以會投入較多時間來實現自動化，多半是因為這個階段採用的技術要仰賴指令碼來建置和部署資源，而部署指示是指令碼。

雖然有許多 Azure 服務可以使用，不過我們要著重於重構階段中最常使用的服務：容器、應用程式服務和資料庫服務。 為什麼要探討重構？ 重構可提供紮實的程式碼基礎，將程式碼債務保持在合理範圍內，藉此降低長期成本。

容器可以用來組合應用程式。 由於容器將作業系統虛擬化的方式，您可以將多個容器裝載到同一部 VM 上。 您可以在不變更或變更少量程式碼的情況下，將應用程式移動到容器，不過可能需要變更設定。 這項作業也需要撰寫指令碼，用指令碼將多個應用程式組合在一個容器中。 重構時，開發團隊需要花費時間撰寫和測試這些指令碼。 Azure 可透過 [Azure Kubernetes Service](/azure/aks/?WT.mc_id=retailecomm-docs-scseely) (AKS) 和相關的 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/?WT.mc_id=retailecomm-docs-scseely) 來支援容器化，協助您管理容器映像。

至於應用程式服務，則可利用各種 Azure 服務。 比方說，您現有的基礎結構可能會將訊息放入佇列來處理客戶訂單，就像 [RabbitMQ](https://www.rabbitmq.com/) 一樣。 (例如，一則訊息用來向客戶收費，第二則訊息用來出貨。)重新裝載時，則會將 RabbitMQ 放在不同的 VM 中。 重構期間，您會將[服務匯流排](/azure/service-bus-messaging/service-bus-queues-topics-subscriptions?WT.mc_id=retailecomm-docs-scseely)佇列或主題加入解決方案中、重寫 RabbitMQ 程式碼，並停止使用提供佇列功能的 VM。 這項變更會以永遠可用的訊息佇列服務來取代一組 VM，進而可降低成本。 Azure 入口網站另有提供其他應用程式服務。

對於資料庫，則可將資料庫從 VM 移動到服務。 Azure 的 [Azure SQL Database](/azure/sql-database/sql-database-cloud-migrate?WT.mc_id=retailecomm-docs-scseely) 和 [Azure SQL Database 受控執行個體](/azure/sql-database/sql-database-managed-instance?WT.mc_id=retailecomm-docs-scseely)支援 SQL Server 工作負載。 [資料移轉服務](https://azure.microsoft.com/services/database-migration/?WT.mc_id=retailecomm-docs-scseely)會評估您的資料庫，通知您在移轉之前需要完成哪些工作，然後將資料庫從 VM 移動到服務。 Azure 也支援 [MySQL](https://azure.microsoft.com/services/mysql/?WT.mc_id=retailecomm-docs-scseely)、[PostgreSQL](https://azure.microsoft.com/services/postgresql/?WT.mc_id=retailecomm-docs-scseely) 和[其他資料庫](https://azure.microsoft.com/services/#databases?WT.mc_id=retailecomm-docs-scseely)引擎服務。

## <a name="rebuild"></a>重建

到目前為止，我們在嘗試盡量減少電子商務系統的變更，也就是不變動目前使用中的系統。 現在要開始討論如何真正發揮雲端的效益。 這個階段的意義在於積極採用 PaaS 甚至 SaaS 服務和架構，藉此修改現有應用程式。 這個流程程序需要大幅修改應用程式，以求新增功能，或配合雲端的需求來重新建立應用程式的架構。  _受控 API_ 是充分發揮雲端系統效益的新概念。 我們可以建立 API 讓服務彼此通訊，簡化系統的更新作業。  第二個優點是能夠取得手邊資料的深入解析。 為了達到這個目的，我們可以移動到_微服務加 API_ 架構，並使用機器學習和其他工具來分析資料。

### <a name="microservices--apis"></a>微服務 + API

微服務能透過對外開放的 API 進行通訊。 每個服務均各自獨立，且需實作一項商務功能，例如向客戶推薦商品、維護購物車等。 將應用程式分解成微服務需要時間和規劃。 雖然微服務的定義並無硬性規定，不過基本概念包括將可部署的單位減少為一組幾乎總是同時變更的元件。 微服務可讓您以所需的頻率來部署變更，同時降低整體應用程式的測試負擔。 有些服務的規模可能很小。 這些服務適合以 [Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=retailecomm-docs-scseely) 來實現無伺服器化，以便在需要時擴充，滿足多個呼叫者的需求，未使用時也不會消耗資源。 其他服務則會依照商務功能細分，例如管理產品、擷取客戶訂單等。

無伺服器的機制確實有缺點：在輕微負載下，回應速度可能會變慢，因為某些雲端上的伺服器需要幾秒鐘的時間來設定並執行您的程式碼。 至於環境中客戶頻繁使用的部分，則需確保客戶能快速輕鬆地尋找產品、下訂單、要求退貨等。 每當效能低落時，都必須承受在購物漏斗圖中失去客戶的風險。 如果有功能必須快速做出回應，請在 [Azure Kubernetes Service](/azure/aks/?WT.mc_id=retailecomm-docs-scseely) 中重建該功能，使其成為獨立的可部署單位。  至於其他情況 (例如有些服務需要大量記憶體、數個 CPU 及大量本機儲存空間的組合)，將微服務裝載於專屬 VM 是合理的做法。

各項服務都需要使用 API 來進行互動。 您可以將至 API 的存取導向到微服務，不過與服務通訊的人需要知道應用程式的拓撲。 [API 管理](/azure/api-management/?WT.mc_id=retailecomm-docs-scseely)之類的服務可集中發佈 API。 所有應用程式只需要連接 API 管理服務即可。 開發人員可以探索有哪些 API 可供使用。 API 管理服務也會提供保持零售環境順暢運作的功能。 該服務可以根據不同的應用程式組件限制對 API 的存取 (以避免出現瓶頸)、將緩慢變更值的回應加入快取、將 JSON 轉換為 XML 等。 如需完整的原則清單，請前往[這裡](/azure/api-management/api-management-policies?WT.mc_id=retailecomm-docs-scseely)。

### <a name="make-use-of-your-data-and-the-azure-marketplace"></a>善用資料與 Azure Marketplace

由於您的資料和系統全都在 Azure 中，所以可輕鬆地將其他 SaaS 解決方案納入業務。 您馬上就可以採取一些行動。 例如可使用 [Power BI](https://powerbi.microsoft.com/?WT.mc_id=retailecomm-docs-scseely) 拼接各種資料來源，建立視覺效果和報告，進而獲得深入解析。

接下來，請看看 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?WT.mc_id=retailecomm-docs-scseely) 中的供應項目，了解其可如何協助您將庫存最佳化、根據客戶屬性管理行銷活動，以及根據每位客戶的喜好與記錄呈現適當產品等。 請花一些時間設定您的資料，以便在 Marketplace 供應項目中發揮效益。

## <a name="next-steps"></a>後續步驟

許多開發團隊都想要同時進行重新裝載與重構，以求解決技術債務並充分利用產能。 在貿然進入後續步驟之前，先進行重新裝載，會有一些優勢。  在部署到新環境時出現的任何問題，都比較容易診斷及修正。 開發和支援小組可以獲得足夠時間熟悉 Azure 帶來的新環境。 要開始重構及重建系統時，您已經是以穩定運作的應用程式做為基礎。 如此就能實施較小規模的針對性變更，也能頻繁更新。

我們發佈了更為通用的白皮書，來解說如何移轉到雲端：[雲端移轉基本資訊](https://azure.microsoft.com/resources/cloud-migration-essentials-e-book/?_lrsc=9618a836-9f81-4087-901f-51058783c3a8&WT.mc_id=retailecomm-docs-scseely) \(英文\)。 在規劃移轉作業時，推薦您詳加閱讀此份白皮書。

## <a name="technologies-presented"></a>使用的技術

用於重新裝載：

- [Azure Advisor](/azure/advisor/advisor-overview?WT.mc_id=retailecomm-docs-scseely) 是個人化的雲端顧問，可協助您依最佳做法來最佳化您的 Azure 部署。
- [Azure Migrate](/azure/migrate/migrate-overview?WT.mc_id=retailecomm-docs-scseely) 服務會評估要移轉至 Azure 的內部部署工作負載。
- [Azure Site Recovery](/azure/site-recovery/site-recovery-overview?WT.mc_id=retailecomm-docs-scseely) 可協調和管理 Azure VM 以及內部部署 VM 和實體伺服器的災害復原。
- [Azure 虛擬網路](/azure/virtual-network/virtual-networks-overview?WT.mc_id=retailecomm-docs-scseely)可讓多種類型的 Azure 資源 (例如 Azure 虛擬機器 (VM)) 安全地彼此通訊，以及與網際網路和內部部署網路通訊。
- [Azure ExpressRoute](/azure/expressroute/?WT.mc_id=retailecomm-docs-scseely) 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。

用於重構：

- [Azure Kubernetes Service](/azure/aks/?WT.mc_id=retailecomm-docs-scseely) 可以管理裝載 Kubernetes 的環境；因此，您不需具備容器協調流程專業知識，就能快速、輕鬆地部署及管理容器化應用程式。
- [Azure SQL Database](/azure/sql-database/sql-database-technical-overview?WT.mc_id=retailecomm-docs-scseely) 是 Microsoft Azure 中的通用關聯式資料庫受控服務。 可支援關聯式資料、JSON、空間和 XML 等結構。 SQL Database 提供受控的單一 SQL 資料庫、彈性集區中的受控 SQL 資料庫，以及 SQL 受控執行個體。

用於重建：

- Azure [API 管理](/azure/api-management/?WT.mc_id=retailecomm-docs-scseely)可協助組織將 API 發佈給外部、合作夥伴及內部開發人員，以發揮其資料與服務的潛力。
- [Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=retailecomm-docs-scseely) 是可在雲端輕鬆執行程式碼片段或「函數」的解決方案。
- [Power BI](https://powerbi.microsoft.com/?WT.mc_id=retailecomm-docs-scseely) 是商務分析工具套件，可將見解提供給整個組織。

## <a name="conclusion"></a>結論

若要將電子商務系統移動到 Azure ，就需要分析、規劃和定義好的方法。 本文中討論了重新裝載、重構和重建等三階段做法。 如此能讓組織從某個工作狀態移動到另一個工作狀態，同時將每個步驟的改變程度降到最低。 零售商也可以選擇重構或甚至重建元件，完全略過重新裝載。 假以時日，您會獲得走向現代化的明確路徑，請勇敢邁開步伐。 隨著您逐漸累積使用 Azure 的經驗，您將會發現更多機會，能加入新功能、降低成本並改善整體系統。


_本文由 Scott Seely 和 Mariya Zorotovich 撰寫。_