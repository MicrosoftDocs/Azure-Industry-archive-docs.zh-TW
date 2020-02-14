---
title: 適用於製造業的隨選、高功耗、Azure 計算服務
author: ercenk
ms.author: ercenk
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 製造業中的高功能計算需求概觀。
ms.openlocfilehash: fe5200a726b2a65efaed2bc7a8de01e97766d425
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77052530"
---
# <a name="on-demand-scalable-high-power-compute"></a>隨選、規模可調整、高功能計算

## <a name="introduction"></a>簡介

客戶期盼獲得具有下列屬性的產品：輕量型、強大、安全、可持續且可自訂。 因此，設計階段變得越來越複雜。 在該階段中，會使用電腦來視覺化、分析、模擬及最佳化。 那些工作將會變得越來越複雜，而且越來越需要更多計算資源。 加上產品連結程度越來越高，並產生大量需要處理及分析的資料。

這也產生單一需求：隨選大型計算資源。

在此文章中，我們將逐步解說工程與製造中需要大型計算資源的一些已知區域，並探索 Microsoft Azure 平台如何協助我們。

## <a name="cloud-design-workstations"></a>雲端設計工作站

產品設計師在產品開發生命週期中的設計與規劃階段使用各種軟體工具。 CAD 工具需要設計師工作站有強大的圖形功能，而且這些工作站的成本高昂。 這些經過增強的工作站通常位於設計師的辦公室，使得設計師被綁在一個位置。

雲端解決方案開始逐漸受歡迎，而且新的功能時常推出，雲端工作站的概念開始變得更可行。 使用裝載在雲端的工作站可讓設計師從任何位置存取它。 而且它可讓組織將成本模型從資本支出變成作業費用。

### <a name="remote-desktop-protocol"></a>遠端桌面通訊協定

Microsoft 的遠端桌面通訊協定 (RDP) 長久以來都只支援 TCP。 TCP 所產生的額外負荷比 UDP 高。 從 RDP 8.0 開始，執行 Microsoft 遠端桌面服務的伺服器可以使用 UDP。 若要使用此功能，虛擬機器 (VM) 必須有足夠的硬體資源，具體而言：CPU、記憶體與最關鍵的圖形處理單元 (GPU)。 （GPU 可說是高效能雲端工作站的最重要元件）。Windows Server 2016 提供數個存取基礎圖形功能的選項。 預設的 RDS GPU 解決方案 (亦稱為 Windows 進階點陣化平台 (WARP)) 是是一個適合知識工作者案例的解決方案，但它所提供的資源不適合雲端工作站案例。 RemoteFX vGPU 是 RemoteFX 的一個功能，它是針對遠端連線引進，為案例提供每部伺服器較高的使用者密度，允許較高的高載 GPU 使用率。 不過，當到了使用 GPU 功能的時候，需要離散裝置指派 (DDA) 才能完全發揮 GPU 功能。

我們提供具有單一或多 NVDIA GPU 的 NV 系列 VM，這是以 Azure N 系列供應項目的一部分提供。 這些 VM 已使用 OpenGL 與 DirectX 之類的架構針對遠端虛擬化與 VDI 案例最佳化。 往上增加到 4 個 GPU 時，您可以佈建能夠透過 Azure 上的 DDA 來發揮 GPU 完整優勢的工作站。

值得一提的狠重要一點是 Azure 平台的可程式設計性。 它為 VM 提供數個選項。 例如，您可以視需要佈建工作站。 您也可以透過進階儲存體上的 Azure 磁碟或 Azure 檔案在本機檔案上保留遠端機器的狀態。 這些選項可讓您控制成本。 Microsoft 與 Citrix 有合作關係，因此其 XenDesktop 與 XenApp 解決方案也提供桌面虛擬化解決方案的另一個替代選項。

## <a name="analysis-and-simulation"></a>分析及模擬

在電腦上分析及模擬實體系統這作法由來已久。 有限元素分析 (FEA) 是一個用於解決許多分析問題的數字型方法。 FEA 需要大量的計算能力來執行大型矩陣計算。 當我們從 2D 移到 3D，以及加入更多細微性到 FEA 網格時，牽涉到 FEA 模型解決方案的矩陣數目呈指數性成長。 這需要能依需要部署的計算能力。
用於解決問題的程式碼必須能平行執行，以發揮資源的規模可調整性優勢。

解決模擬問題需要大規模的計算資源。 高效能運算 (HPC) 是一個大規模運算級別。 HPC 需要低後端網路延遲，該網路必須具有遠端直接記憶體存取 (RDMA) 功能以進行快速平行運算。 Azure 平台提供針對高效能運算所建置的 VM。 它們具有搭配 DDR4 記憶體的特殊處理器，因此可允許需要大量運算資源的解決方案有效地執行，不論是在 Linux 或 Windows 安裝上。 此外，它們以數種大小提供。 請參閱[高效能運算 VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json?WT.mc_id=computeinmanufacturing-docs-ercenk)。
若要查看 Azure 如何以其他方式支援 HPC，請參閱 Big Compute：[HPC 與批次](https://azure.microsoft.com/solutions/big-compute/?WT.mc_id=computeinmanufacturing-docs-ercenk)。

Azure 平臺可讓解決方案相應增加和相應放大。其中一個常見的模擬軟體套件是星形-CCM +，從 CD cd-adapco。 [已發佈的研究](https://azure.microsoft.com/blog/availability-of-star-ccm-on-microsoft-azure/?WT.mc_id=computeinmanufacturing-docs-ercenk)示範執行 “Le Mans 100 million cell” 計算流體力學 (CFD) 模型的 STAR-CCM+ 提供平台的延展性。 下列圖表示範當在執行模擬期間加入更多核心時觀察到的延展性：

![https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/34f1873b-4db5-4c62-b963-8bdf3966cf60.png](assets/bigcompute-assets/starccm.png)

另一個受歡迎的工程分析軟體套件是 ANSYS CFD。 它可讓工程師執行多物理分析，包括流體力、熱效應、結構完整性與電磁輻射。 [已發佈的研究](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/?WT.mc_id=computeinmanufacturing-docs-ercenk)示範 Azure 上該解決方案的延展性，顯示類似的結果。

![https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/77129585-f25c-4c29-b22b-80c627d03daa.png](assets/bigcompute-assets/fluent.png)

不需要投資近端計算叢集，需要平行執行的軟體套件可部署在 Azure 虛擬機器上，或使用 [HPC 與 GPU VM](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 系列部署在[虛擬機器擴展集 (VMSS)](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json?WT.mc_id=computeinmanufacturing-docs-ercenk) 上，以取得全雲端解決方案。

### <a name="burst-to-azure"></a>將暴增的工作負載移至 Azure

若近端叢集可用，另一個選項是將它延伸到 Azure，因此可卸載尖峰工作負載 (亦稱為「將暴增的工作負載移至 Azure」)。 若要這樣做，您需要使用支援 Azure 的其中一個內部部署工作負載管理員 (例如 [Alces Flight Compute](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview?WT.mc_id=computeinmanufacturing-docs-ercenk)、[TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/?WT.mc_id=computeinmanufacturing-docs-ercenk)、[Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)、[IBM Spectrum Symphony 與 Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/?WT.mc_id=computeinmanufacturing-docs-ercenk)、[PBS Pro](http://pbspro.org/)、[Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx?WT.mc_id=computeinmanufacturing-docs-ercenk))。

另一個選項是 Azure Batch，此服務可用來有效率地執行大規模平行與 HPC 批次作業。 Azure Batch 允許使用訊息傳遞介面 (MPI) API 的作業。 Batch 透過 [HPC](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx?WT.mc_id=computeinmanufacturing-docs-ercenk) 與 [GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc?WT.mc_id=computeinmanufacturing-docs-ercenk) 最佳化 VM 系列同時支援 [Microsoft MPI](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu?WT.mc_id=computeinmanufacturing-docs-ercenk) 與 MPI。 Microsoft 也併購了 [Cycle Computing](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/?WT.mc_id=computeinmanufacturing-docs-ercenk)， 它提供的解決方案提供較高層級的抽象化，以在 Azure 上執行叢集。 另一個選項是透過可無縫存取補充性 Azure 服務 (例如 [Azure 儲存體](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure)與 [Azure Data Lake](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage?WT.mc_id=computeinmanufacturing-docs-ercenk)) 的方式在 Azure 上執行 [Cray 超級電腦](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview?WT.mc_id=computeinmanufacturing-docs-ercenk)。

## <a name="generative-design"></a>有生產力的設計

設計程序總是反覆的。 設計師是從是從目標設計的一組限制式與參數開始，並逐一查看數個設計替代項，最終在最滿足限制式的項目上完成設定。
不過，當計算能力幾乎無限時，您應該評估數千個或甚至數百萬個設計替代項，而不是只評估幾個。 這個旅程從參數型模型，以及它們在 CAD 工具中的使用開始。 現在有了雲端平台上大量的計算資源，產業即將邁出下一步。 有生產力的設計這個詞彙描述提供參數與限制式到軟體工具的設計程序。 接著，工具會產生設計替代項，進而建立解決方案的數個置換項目。
有一些方法可用來進行有生產力的設計：拓撲最佳化、辨識格最佳化、介面最佳化與表單合成。 那些方法的詳細資料不在此文章的討論範圍內。 不過，那些方法的常用模式是存取需要大量計算資源之環境的需求。

有生產力的設計的起點是定義必須逐一查看哪些演算法的設計參數，以及合理的增加與值範圍。 演算法接著會建立每個有效參數組合的替代項。 這會產生大量的設計替代項。 建立這些替代項需要許多計算資源。
您也必須為每個設計替代項執行所有模擬與分析工作。 最後結果是您需要大量計算環境。

Azure 用於計算需求隨選相應增加的多個選項，從 [Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 到 [VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 都是那些工作負載的自然目的地。

## <a name="machine-learning-ml"></a>Machine Learning (ML)

在相當簡單化的層級中，我們能以如下方式將 ML 系統一般化：在給定資料點或一組資料點的情況下，系統會傳回相互關聯的結果。 透過此方式，ML 系統會用於解決問題，例如：

- 在給定過去房屋價格與房屋情況的情況下，給定房屋堆出到市場時的預期價格為何？

- 給定來自各感應器的讀數，以及機器過去故障案例，該機器在下一段期間故障的可能性為何？

- 給定一組影像，哪一隻是家貓？

- 給定輸油管道的影片摘要，其上是否有因為嚴重凹痕要成損壞的部分？

使用人工智慧 (AI) 與機器學習 (ML) 新增進階分析功能從使用如下程序開發模型開始。

![](assets/bigcompute-assets/aipipeline.png)

[選取演算法](https://docs.microsoft.com/azure/machine-learning/studio/algorithm-choice?WT.mc_id=computeinmanufacturing-docs-ercenk)取決於資料的大小、品質與本質，以及預期的答案類型。 根據輸入大小與選取的演算法，以及計算環境，此步驟通常需要較大的計算資源而且可能需要不同的時間才能完成。 下列圖表來是[技術文章](https://blogs.technet.microsoft.com/machinelearning/2017/07/25/lessons-learned-benchmarking-fast-machine-learning-algorithms/?WT.mc_id=computeinmanufacturing-docs-ercenk)此文章說明如何針對 ML 演算法的定型進行效能測試；它顯示在給定不同演算法、資料大小與計算目標 (GPU 或 CPU) 的情況下完成定型循環的時間。

![](assets/bigcompute-assets/vmsizes.png)

決策的主要驅使者是商業問題。 若問題需要使用適當的演算法處理大型資料集，關鍵因素是用於將演算法定型的雲端規模計算資源。
[Azure Batch AI](https://azure.microsoft.com/services/batch-ai/?WT.mc_id=computeinmanufacturing-docs-ercenk) 是一個服務，它可以平行、大規模地將 AI 模型定型。

使用 Azure Batch AI 時，資料科學家可以在工作站上使用 [Azure 資料科學虛擬機器 (DSVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 或 [Azure 深度學習虛擬機器 (DLVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/deep-learning-dsvm-overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 開發解決方案，並將定型作業推送到叢集。 DSVM 與 DLVM 是使用一組豐富的預先安裝工具與範例特別設定的 VM 映像。

![](assets/bigcompute-assets/azurebatchai.png)

## <a name="conclusion"></a>結論

製造產需要大量的數學計算，因此需要存取高階硬體元件，包括圖形處理單元 (GPU)。 裝載資源之平台的延展性與彈性非常重要。 若要控制成本，它必須能視需要使用，同時提供最佳速度。

Microsoft Azure 平台提供滿足這些需求的廣泛選擇。 您可以從頭開始、控制每個資源與其每個方面，以建置您自己的解決方案。 或者，您可以尋找 Microsoft 合作夥伴來協助您建立解決方案。 我們的合作夥伴知道如何利用 Azure 功能的優勢。

## <a name="next-steps"></a>後續步驟

- 透過開發 [NV 系列 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu?WT.mc_id=computeinmanufacturing-docs-ercenk) 以設定雲端工作站

- 檢閱為您的設計需求部署工具的[選項](https://docs.microsoft.com/azure/virtual-machines/linux/high-performance-computing?WT.mc_id=computeinmanufacturing-docs-ercenk)，以利用 Azure HPC 功能的優勢

- 透過 [Azure Machine  Learning](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=computeinmanufacturing-docs-ercenk) 了解可能性
