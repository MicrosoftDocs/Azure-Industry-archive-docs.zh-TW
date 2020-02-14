---
title: 概觀 - Azure Batch 和 Azure Data Lake 的網格運算風險分析
author: dstarr
ms.author: dastarr
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 介紹銀行業實作 Azure Batch 來進行風險網格運算的技術層面。
ms.openlocfilehash: 542fb820870048ac2ec2cb67c2bbf13988588ea1
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77053176"
---
# <a name="risk-grid-computing-in-banking-solution-guide"></a>銀行業風險網格運算解決方案指南

本文會提供技術方面的概觀，解說如何使用 Microsoft Azure 來支援及強化銀行業的風險網格運算，包括建議的系統和高階架構。

本文件的目標讀者為解決方案架構師，在某些情況下，也適合想要深入探討風險運算解決方案提案的技術決策者閱讀。

## <a name="introduction"></a>簡介

財務風險分析模型通常會以批次工作形式處理，繁重的運算負載會對計算能力、資料存取及分析產生高度需求。 風險網格運算的計算需求通常會隨著時間增加，對於計算資源的需求也會相應增加。

Azure 提供了多樣化的產品與服務，即表示大部分的問題都不只有一種解決方案。 本文會概略介紹銀行業如何使用量身設計的技術、模式和實務做法，讓使用 [Microsoft Azure Batch](/azure/batch/batch-dotnet-get-started?WT.mc_id=gridbanksg-docs-dastarr) 的風險網格運算解決方案發揮最大效益。

Azure Batch 這項免費服務提供了符合成本效益且安全的解決方案，適用於風險網格運算模型常搭配使用的基礎結構和不同階段的批次處理。 Azure Batch 可以使用混合式網路或將整個 Batch 處理移往 Azure，藉此增強、擴充或甚至取代目前的內部部署計算資源投資。  資料可以上下周遊雲端或留在內部部署設施，而當內部部署資源不足時，計算節點能以高載到雲端的方式來處理其他資料。

## <a name="anatomy-of-a-batch-run"></a>Batch 執行剖析

Batch 執行通常涉及至少兩個應用程式。 在「前端節點」執行的應用程式通常會將作業提交到集區，有時候還會協調計算節點。 協調流程也可以透過 Azure 入口網站來設定。 另一個應用程式則是由計算節點以工作形式執行 (請參閱圖 1)。

計算節點應用程式負責執行平行處理風險模型檔案的工作。 可以在計算節點上安裝並執行一個以上的應用程式。

可以透過 [Batch API](/azure/batch/batch-apis-tools?WT.mc_id=gridbanksg-docs-dastarr) 上傳這些應用程式，也可以直接透過 Azure 入口網站或是[適用於 Batch 的 Azure CLI 命令](/azure/batch/cli-samples?WT.mc_id=gridbanksg-docs-dastarr)上傳。

![Azure Batch 網格運算](./assets/risk-grid-compute-assets/05-batch-grid-computing.png)

**圖 1：** Azure Batch 網格運算

Batch 執行由數個邏輯元素所組成。 圖 2 顯示的是批次工作的邏輯模型。 集區是用於容納涉及 Batch 執行之 VM 的容器，能佈建計算節點 VM。 集區也是用於容納計算節點上安裝之應用程式的容器。 作業會在集區內建立及執行。 工作則是由作業所執行。 工作是背景工作角色應用程式的執行動作，可以透過命令列指示叫用。

背景工作角色應用程式會在建立時安裝到計算節點。

![集區、作業和工作](./assets/risk-grid-compute-assets/06-pool-job-logical-model.png)

**圖 2：** 邏輯 Batch 概念模型

作業執行時，集區會佈建任何所需的背景工作角色 VM，並安裝背景工作角色應用程式。 作業會將工作指派給這些計算節點，計算節點會接著執行命令列指示，通常會呼叫已安裝的應用程式或指令碼。
使用 Batch 一般需要按照典型模式，如下所述：

1. 建立包含 Batch 資產的資源群組。
2. 在新的資源群組中建立 Batch 帳戶。
3. 建立連結的儲存體帳戶。
4. 建立集區以在其中佈建背景工作角色 VM。
5. 將計算節點應用程式或指令碼上傳到集區。
6. 建立作業以將工作指派給集區中的 VM。
7. 將作業新增至集區。
8. 開始 Batch 執行。
9. 作業佇列工作會在計算節點上執行。
10. 計算節點會在 VM 可供使用時執行工作。

上述流程的圖例如圖 3 所示。

![Batch 執行流程](./assets/risk-grid-compute-assets/07-batch-run-process.png)

**圖 3：** 邏輯 Batch 概念模型

工作完成後，很適合用來移除計算節點，以免在不使用時產生費用。 若要刪除這些節點，使用者可透過程式碼或入口網站刪除容納集區，如此會移除背景工作角色 VM。

如需 Batch 使用方式的逐步入門說明，有多種語言的 [5 分鐘快速入門](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)可帶您認識流程或熟悉 Azure 入口網站。

## <a name="batch-process-scheduling"></a>Batch 處理排程

Azure Batch 有內建的排程器，因此您可到入口網站或透過 API 定義每次執行的排程。 Batch 作業排程器可以定義多個排程來引發多個作業。 每個作業都有自己的屬性，例如在工作開始及結束時應做的動作。 作業排程可以設定為按週期的間隔執行，或者單次執行。

許多銀行網格運算系統已經有專屬的排程服務， 因此將自己的排程器移動到 Azure 並非當務之急。 排程之所以能順暢運作，是因為 Azure Batch 能以手動方式或透過 SDK 叫用，使得排程仍然可以在內部部署中發生，而且在 Azure 中處理工作負載。

Batch 處理可以依照預先決定的排程或視需要執行，不過在任何情況下，只要不使用計算節點 VM，就不需要讓 VM 保持運作。  如果在使用數百個甚至數千個 VM 計算節點，只要在 VM 完成自身排入佇列的工作後，將伺服器取消佈建，就能大幅節約費用。

## <a name="compute-node-applications"></a>計算節點應用程式

在叫用工作時，計算節點需要有可執行的應用程式。 這類應用程式由企業撰寫，安裝在背景工作角色上後，可用於執行處理作業。 就銀行業的風險網格運算案例來看，這類應用程式通常負責將資料轉換成多種格式，特別是適合下游分析或其他處理的格式。

將應用程式提供到集區以便分發到計算節點時，會以應用程式封裝的形式上傳。 應用程式封裝可以是先前上傳之應用程式封裝的另一個版本。 一個計算節點可以安裝多個應用程式封裝。 作業包含了要載入到背景工作角色電腦的應用程式封裝。

也可以依版本來管理應用程式封裝部署。 如果已將多個應用程式封裝版本載入集區，您可以指定 Batch 執行時要使用哪個特定版本，如圖 4 所示。 當在稽核環境中，或是企業想要在重現前一次的執行時，這是必要的步驟。 如果有錯誤導入了背景工作角色應用程式，也能透過這個步驟復原。

![Batch 執行流程](./assets/risk-grid-compute-assets/08-versioning-worker-applications.png)

**圖 4：** 設定計算節點工作應用程式的版本

上傳到集區的應用程式封裝會採用 .zip 檔案形式，其中包含工作執行應用程式所需的應用程式二進位檔案和支援檔案。 應用程式封裝有 2 個範圍。 您可以在集區範圍或工作範圍內指定應用程式封裝。

## <a name="pool-application-packages"></a>集區應用程式封裝

這些封裝會部署到集區中的每個計算節點。 在佈建、重新啟動計算節點 VM 或重新安裝其映像時，如果有更新版本的應用程式存在，即會安裝新一份集區應用程式封裝。 您可以將一個或一個以上的應用程式封裝指派給集區，也就是計算節點會獲得所有指定的封裝。

## <a name="task-application-packages"></a>工作應用程式封裝

針對工作層級的應用程式封裝，只會安裝到排定要執行某項工作的計算節點上。 工作應用程式封裝適用於當一個集區中有多個工作要執行的情況。

在彙總集區層級作業產生的資料時，工作應用程式就能派上用場，這類資料可能與風險網格運算案例息息相關。 舉例而言，工作應用程式可能會執行一組風險計算，稍後再將產生的資料用於風險計算工作流程。

## <a name="scaling-batch-jobs"></a>調整批次作業

銀行通常會在計算資源使用率較低的週末或夜間進行風險分析批次執行。 雖然這個模型適用於部分銀行，不過很快便會不敷使用，而需要投入更多資金，將更多背景工作角色電腦加入網格。

如果 Batch 作業的執行時間太長，或是想要為 Batch 執行增設更多運算能力，Azure 提供了幾個選項。

1. 配置更多計算節點電腦加以擴充。
2. 配置能力較強的計算節點電腦以相應增加。 可以佈建 Azure 電腦來滿足對核心、記憶體甚至 GPU 運算能力的高效能需求。

> 請注意：Microsoft HPC Pack 搭配 Batch 是較複雜的模型，本文不予討論。

在 Batch 處理叢集中，少至兩部處理 VM，多至在數千個 VM 計算節點上以上萬顆核心處理數千個並行工作，都有可能。 每部 VM 負責每次執行一件工作。 集區中的 VM 數目能以手動方式調整，抑或是設定為隨著負載增減而自動調整。

### <a name="burst-to-cloud"></a>高載至雲端

當內部部署網格中的計算資源因執行大型分析作業而不足時，「高載至雲端」能在 Azure 中新增更多計算節點，藉此補強這些資源。 高載至雲端是一種模型，當對於內部部署資源的需求過高時，私人雲端或基礎結構會將工作負載分散到雲端伺服器上。

可以預先將這些計算節點設為要在 Azure 的 IaaS 平台上佈建的 Linux 或 Windows 虛擬機器。 此外，可以將伺服器佈建並自動設定為使用 Tibco Gridserver 和 IBM Symphony 等現有投資。

### <a name="automatic-scaling-formulas"></a>自動調整公式

這項彈性功能可以在 Azure 入口網站設定，或使用[自動調整公式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)設定。 自動調整公式是上傳到 Batch 處理排程器的指令碼，能精密控制 Batch 的行為。 計算節點集區的自動調整，是由建立自動調整公式與節點之間的關聯來實現。

以下是自動調整公式的範例，自動調整會由一部 VM 開始，視需要相應增加至 50 部。 隨著工作完成，VM 會逐一閒置，而自動調整公式會將集區縮小。

```Formula
startingNumberOfVMs = 1;
maxNumberofVMs = 50;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

### <a name="other-scaling-techniques"></a>其他調整技術

自動調整也可以透過 Enable-AzureBatchAutoScale PowerShell Cmdlet 來啟用。 Enable-AzureBatchAutoScale Cmdlet 可讓您自動調整指定的集區。 範例如下。

1. 第一個命令負責定義公式，再將之儲存到 `$Formula` 變數。
2. 第二個命令會使用 `RiskGridPool` 中的公式，替名為 `$Formula` 的集區啟用自動調整。

```console
C:\> $Formula = ‘startingNumberOfVMs = 1;
maxNumberofVMs = 50;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second?WT.mc_id=gridbanksg-docs-dastarr);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);’;

C:\> Enable-AzureBatchAutoScale -Id "RiskGridPool" -AutoScaleFormula $Formula -BatchContext $Context
```

也可以使用 Azure CLI 搭配 `az batch pool resize` 命令，並使用 Azure 入口網站來進行調整。

## <a name="data-storage-and-retention"></a>資料儲存和保留

等到資料內嵌並經過計算節點處理後，產生的輸出資料可以儲存在資料庫中，以供進一步處理與分析，或是在內嵌時先行轉換後再儲存，確保格式適合下游處理。 Microsoft Azure 提供幾個儲存選項。 [要使用哪一項資料存放區技術](/azure/architecture/data-guide/?WT.mc_id=gridbanksg-docs-dastarr)的選擇，主要取決於下游處理的分析和報告需求。

使用混合式網路時，資料儲存目標可能為內部部署。 透過混合式網路使用 Batch 時，計算節點可以將資料寫回內部部署資料存放區，而不使用 Azure 儲存位置。 背景工作角色也可以寫入 Azure 檔案儲存體，該儲存體能以磁碟形式掛接在內部部署電腦上，以便會使用到內部部署檔案的流程存取。

## <a name="monitoring-and-logging"></a>監視和記錄

為了將未來 Batch 作業的執行最佳化，請記錄資料以找出最佳化區域。 舉例而言，如果背景工作角色執行時接近 CPU 的能力極限，增加計算節點的核心有助於避免過度佔用 CPU 資源，而且還可以更快完成作業。 在 Batch 作業中，每個應用程式的執行都有自己的特性，因此若要針對 Batch 執行中的 VM 進行最佳化，方法也不盡相同。 若是記憶體密集的工作，可在下一次執行時以不同的方式設定電腦，藉以配置更多記憶體。

可以由計算節點和格線前端應用程式負責記錄，或使用 [Batch 診斷記錄](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)來進行。 您可以設定 Batch 執行效能的相干記錄資訊，找出有哪些區域可加以改善，以提高效能並縮短工作完成時間。

### <a name="custom-batch-monitoring-and-logging"></a>自訂 Batch 監視和記錄

控制用的應用程式和計算節點應用程式可以產生及儲存這些資料，以供進一步分析。 找到的資料有助於將 Batch 作業最佳化，其中包括：

- 每件工作的開始和結束時間
- 每個計算節點保持運作和執行工作的時間
- 每個計算節點保持運作但未執行工作的時間
- 整體的批次作業執行時間

### <a name="batch-diagnostic-logging"></a>Batch 診斷記錄

若不使用控制器和計算節點應用程式來發出檢測資料，還有一種替代方案。 [Batch 診斷記錄](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)可以擷取大量的執行資料。 Batch 診斷記錄預設為不啟用，而且必須針對 Batch 帳戶啟用。

Batch 診斷記錄能提供大量資料，有助於排解疑難問題及最佳化批次執行。 包括作業和工作的開始與結束時間、核心計數、節點總數和其他許多計量。

批次記錄會針對發出的記錄要求儲存目的地，並儲存批次執行所產生的事件，例如集區建立、作業執行、工作執行等。除了將診斷記錄事件儲存在 Azure 儲存體帳戶中，Batch 服務記錄事件也可以串流處理至[Azure 事件中樞](/azure/event-hubs/event-hubs-what-is-event-hubs?WT.mc_id=gridbanksg-docs-dastarr)，並傳送至[azure log Analytics](/azure/log-analytics/log-analytics-overview?WT.mc_id=gridbanksg-docs-dastarr)。

這些資料能用來將核心計算和前端節點的應用程式最佳化。 這樣的做法能縮短背景工作角色 VM 不再需要時的取消佈建時間，不必等到 Batch 執行結束後再進行，因此能降低成本。

### <a name="batch-management-tools"></a>Batch 管理工具

Azure 入口網站提供 Batch 監視儀表板，能在作業執行時顯示 Batch 的相關資訊，甚至能顯示帳戶配額。 這樣的功能足以滿足許多 Batch 作業應用程式的需求。

Azure 入口網站除了提供 Batch 管理和視覺化工具之外，還有免費的開放原始碼 Batch 管理工具 [BatchLabs](https://github.com/Azure/BatchLabs)。 這是一款獨立的用戶端工具，可以協助您建立、偵錯及監視 Azure Batch 應用程式。 下載適用於 Mac、Linux 或 Windows 的安裝套件。

## <a name="network-models"></a>網路模型

風險分析通常需要將數百甚至數千份文件內嵌到風險網格運算流程中。 這些檔案通常是位於內部部署檔案存放區、網路共用或其他存放庫。 若使用以 Azure 為基礎的 VM 來存取及處理這些檔案，讓內部部署網路與 Azure 網路緊密連接，便能簡單又快速地存取檔案。 這種方法甚至不必變更在計算節點上進行處理工作的程式碼。

Azure 提供兩種模型，供您將目前的內部部署系統安全可靠地連線到 Azure：[Microsoft Azure ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbanksg-docs-dastarr) 與 [VPN 閘道](/azure/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr)。 雖然實作方式、效能與[其他屬性](/azure/networking/networking-overview?WT.mc_id=gridbanksg-docs-dastarr)各不相同，不過這兩種模型都能提供安全可靠的連線能力。

若不使用前述做法，風險網格運算前端節點也可以存留在內部部署設施，並透過以 .NET 和其他語言撰寫的 REST API 或 SDK 執行 Batch 作業。

還有其他技術能不透過混合式網路解決方案，就橋接起 Azure 與內部部署資源之間的隔閡。 如需詳細資訊，請參閱下文。

### <a name="expressroute"></a>ExpressRoute

ExpressRoute 能透過連線合作夥伴 (目前網際網路服務提供者 (ISP?WT.mc_id=gridbanksg-docs-dastarr)) 促成的私人連線，將內部部署或資料中心網路與 Azure 相連。 這個做法能讓兩個網路彼此視為相同的網路執行個體，實現網路之間的無縫存取。 如果想要整合現有的內部部署系統與 Azure 網路，網路整合必不可少，而 ExpressRoute 提供最快的連線速度。

如需 Azure ExpressRoute 的定價資訊，請[參閱這個頁面](https://azure.microsoft.com/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr)。

### <a name="vpn-gateway"></a>VPN 閘道

VPN 閘道將是將您的網路與 Azure 相連的另一種方法。 這個模型的缺點是流量會經由網際網路來傳送， 如此會導致連線較不具有彈性，而且網路速度也不及 ExpressRoute。不過這不會對風險網格運算案例造成任何阻礙，因為資料檔案的讀取通常不會花費太多時間。

如需 VPN 閘道的定價資訊，請[參閱這個頁面](https://azure.microsoft.com/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr)。

### <a name="choices-for-connectivity-details"></a>連線選擇詳細資料

基本上，若要將您的網路延伸到 Azure，可以採用如圖 5 所示的兩種模式。

1. 虛擬閘道：站對站
2. ExpressRoute：Exchange 或 ISP 提供者

![站對站與 ExpressRoute](./assets/risk-grid-compute-assets/10-s2s-expressroute.png)

**圖 5：** 站對站與 ExpressRoute

#### <a name="virtual-gateway-site-to-site-integration"></a>虛擬閘道站對站整合

[站對站 VPN 閘道](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal?WT.mc_id=gridbanksg-docs-dastarr)能將您的內部部署網路與 Azure VNet 連接。 這個做法能橋接網路之間的隔閡，在本質上讓雙方成為同一個網路的一部分，允許雙向存取資源、伺服器和成品。 至於執行風險網格運算批次作業的 Azure 背景工作角色 VM，您可以直接存取其中的資料檔案。

#### <a name="expressroute-integration"></a>ExpressRoute 整合

由 Azure 合作夥伴網路提供者促成的 ExpressRoute 連線不僅能帶來與站對站連線相同的優點，還提供更優異的速度和可靠性。

取得 [ExpressRoute 連線模型] (/azure/expressroute/expressroute-connectivity-models?WT.mc_id=gridbanksg-docs-dastarr) 的詳細資訊。

### <a name="batch-processing-without-an-azure-hybrid-network"></a>不使用 Azure 混合式網路的 Batch 處理

另一種 Batch 案例是將所有資料檔案上傳到 Azure 儲存體，之後再使用以 Azure 為基礎的計算電腦加以處理。 檔案儲存體和 Blob 儲存體是存放風險網格運算資料的可行選擇。

在此案例中，作業控制器與所有計算節點都存留在 Azure 中，如圖 6 所示。 處理後的資料可以存放在 Azure 資料存放區，準備供 Azure Machine Learning 解決方案或其他系統進行後續處理。 額外處理工作不在本文章的討論範圍內。

![站對站與 ExpressRoute](./assets/risk-grid-compute-assets/09-batch-upload-process.png)

**圖 6：** Batch 上傳到執行生命週期

### <a name="hybrid-network-connectivity-resources"></a>混合式網路連線資源

對您的情況而言，可能有好幾種合適的設定。 為了協助您擬定有關 Azure 網路連線的決策及提供架構指引，請參閱由模式與做法小組所撰寫的 _[將內部部署網路連線到 Azure](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbanksg-docs-dastarr)_ 一文。

- 如需 VPN 閘道設定的替代方案，[請參閱這篇文章](/azure/vpn-gateway/vpn-gateway-about-vpngateways?WT.mc_id=gridbanksg-docs-dastarr)。
- 深入了解 [ExpressRoute 連線模型](/azure/expressroute/expressroute-connectivity-models?WT.mc_id=gridbanksg-docs-dastarr)。
- 計算 [ExpressRoute 價格](https://azure.microsoft.com/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr)。
- 計算 [VPN 閘道價格](https://azure.microsoft.com/pricing/details/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr)。

## <a name="security-considerations"></a>安全性考量

您可以建立 Azure [虛擬網路 (VNet)](/azure/virtual-network/virtual-networks-overview?WT.mc_id=gridbanksg-docs-dastarr)，並在虛擬網路中建立集區的計算節點。 這個方法能為 Batch 執行提供額外的隔離措施，同時允許使用 [Azure Active Directory (AAD)](/azure/active-directory/active-directory-whatis?WT.mc_id=gridbanksg-docs-dastarr) 驗證。 如需詳細資訊，請參閱[集區網路組態](/azure/batch/batch-api-basics#pool-network-configuration?WT.mc_id=gridbanksg-docs-dastarr)一節。

使用 AAD 驗證 Batch 應用程式的方法有兩種：

1. **整合式驗證**。 使用 AAD 帳戶的批次應用程式，可以使用帳戶來取得資料存放區的資源和其他資源。

2. **服務主體**。 AAD 服務主體能定義使用者和應用程式的存取原則與使用權限。 服務主體能使用與該應用程式相關聯的祕密金鑰來提供使用者驗證。 這個方法可允許使用祕密金鑰驗證自動執行的應用程式。 服務主體能定義應用程式的原則和使用權限，以便在執行階段存取資源時代表應用程式。 [在此深入了解](/azure/active-directory/develop/active-directory-application-objects?WT.mc_id=gridbanksg-docs-dastarr)。

若要深入了解與 AAD 相關的批次處理安全性，[請參閱這篇文章](/azure/batch/batch-aad-auth?WT.mc_id=gridbanksg-docs-dastarr)。

Batch 服務也可以使用共用金鑰來進行驗證。 根據驗證服務的規定，您必須將兩個標頭值加入 HTTP 要求、資料和授權。 如需共用金鑰驗證的詳細資訊，[請參閱這個頁面](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service?WT.mc_id=gridbanksg-docs-dastarr)。

## <a name="cost-considerations"></a>成本考量

使用 Azure Batch 完全免費。 您只需要針對取用的基礎資源付費，例如虛擬機器運作時間、儲存體和網路。 然而，計算節點 VM 即使閒置也必須付費，因此若不再需要，建議您取消佈建。 刪除包含 VM 的集區是相當常用的方法。
>
建立集區時，您可以指定您想要的計算節點類型及每個類型的數目。 計算節點有以下兩種類型︰
>
>**專用計算節點**會保留給您的工作負載使用。 它們比低優先順序的節點昂貴，但保證永遠不會遭到佔用。
>
>**低優先順序計算節點**會利用 Azure 中剩餘的容量來執行 Batch 工作負載。 低優先順序的節點每小時比專用節點更便宜，並可啟用需要大量計算能力的工作負載。 如需詳細資訊，請參閱[使用低優先順序的 VM 搭配 Batch](/azure/batch/batch-low-pri-vms?WT.mc_id=gridbanksg-docs-dastarr)。
>
>專用和低優先順序節點可以共存在同一個集區。
>
>如需低優先順序和專用計算節點的價格資訊，請參閱 [Batch 價格](https://azure.microsoft.com/pricing/details/batch/?WT.mc_id=gridbanksg-docs-dastarr)。

使用 Batch 診斷記錄服務時，發送到 Azure 儲存體的資料會產生費用。 這是儲存體資料，就像其他所有資料一樣，而且價格會受到保留的診斷資料數量影響。

## <a name="getting-started"></a>開始使用

複雜如 Batch 風險網格運算這樣的領域，若想要深入了解，有許多切入點，以下是幾個協助您進一步了解 Batch 技術的合理起點。

使用程式碼和 Azure 入口網站範例是開始使用 Batch 的[好方法](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)。 [GitHub](https://github.com/Azure/azure-batch-samples) 另有免費使用的 Azure Batch 範例應用程式。

以下是一些簡短的教學課程，協助您建置簡單的應用程式來建立和執行批次計算作業。 建置應用程式的選項如下：

- [Batch .NET API](/azure/batch/batch-dotnet-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [Batch SDK for Python](/azure/batch/batch-python-tutorial?WT.mc_id=gridbanksg-docs-dastarr)
- [Batch SDK for Node.js](/azure/batch/batch-nodejs-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [使用 PowerShell 管理 Batch](/azure/batch/batch-powershell-cmdlets-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [使用 Azure CLI 管理 Batch](/azure/batch/batch-cli-get-started?WT.mc_id=gridbanksg-docs-dastarr)

請考慮啟動概念證明計劃。 要使用什麼方法將資料內嵌到 Azure？ 要使用混合式網路，還是透過 SDK 或 REST 介面上傳資料？ 如果有意使用混合式網路，請考慮進行試驗將網路準備妥當。

評估 Batch 計算作業的大小，然後選取合適的調整解決方案。 [自動調整公式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)能進行複雜的排程案例，而且能使用 Azure 入口網站實現較簡單的案例。

## <a name="technologies-presented"></a>使用的技術

[Azure Batch](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr) 提供在雲端執行大規模平行處理作業的能力。

[Azure Active Directory](/azure/active-directory/active-directory-whatis?WT.mc_id=gridbanksg-docs-dastarr) 是多租用戶雲端式目錄，也是身分識別管理服務，將核心目錄服務、應用程式存取管理、身分識別保護結合在單一解決方案中。

[自動調整公式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)是上傳到 Batch 處理排程器的指令碼，能精密控制 Batch 的調整行為。

[Batch 診斷記錄](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)是 Azure Batch 的功能，能從 Batch 執行和產生的事件當中建立詳細記錄。 記錄會儲存在 Azure 儲存體。

[BatchLabs](https://github.com/Azure/BatchLabs) 是一款適用於 Windows、MacOS 和 Linux 的獨立應用程式，能用來監視及管理 Batch。

[ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbanksg-docs-dastarr) 是快速而可靠的混合式網路解決方案，能將內部部署網路與 Azure 網路連接在一起。

[VPN 閘道](/azure/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr)是一款混合式網路解決方案，能使用網際網路將內部部署網路與 Azure 網路連接在一起。

## <a name="conclusion"></a>結論

本文件概述銀行業使用 Azure Batch 進行風險網格運算時的技術解決方案與考量。 本文涉及的主題甚廣，包括 Azure Batch 的定義或[網路選項](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbanksg-docs-dastarr)，甚至成本考量都涵蓋在內。

如果您在考慮進一步評估以 Azure Batch 進行風險網格運算，[本頁面](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)是入門的實用資源。 其透過範例引導式的教學課程解說平行檔案處理，也就是風險網格運算固有的特色。 提供的教學課程使用 Azure 入口網站、Azure CLI、NET 和 Python。
