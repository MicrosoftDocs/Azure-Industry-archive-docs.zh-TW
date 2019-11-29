---
title: 概觀 - 將精算風險分析移至 Azure 的指南
author: dstarr
ms.author: dastarr
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 本篇概觀會介紹精算開發人員是如何將其現有的解決方案加上支援的基礎結構移至 Azure。
ms.openlocfilehash: cb571d996ab34da9157e9e211f354d7ce3d5f720
ms.sourcegitcommit: b8f9ccc4e4453d6912b05cdd6cf04276e13d7244
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74263377"
---
# <a name="actuarial-risk-analysis-and-financial-modeling"></a><span data-ttu-id="ce3ff-103">精算風險分析與財務模型化</span><span class="sxs-lookup"><span data-stu-id="ce3ff-103">Actuarial risk analysis and financial modeling</span></span>

<span data-ttu-id="ce3ff-104">保險業仰賴精算師來分析風險及建立財務模型。</span><span class="sxs-lookup"><span data-stu-id="ce3ff-104">The insurance industry depends on risk analysis and financial modeling by actuaries.</span></span> <span data-ttu-id="ce3ff-105">保險業的專業人員必須針對長期保單與每日市場投資建立假設案例。</span><span class="sxs-lookup"><span data-stu-id="ce3ff-105">The professionals in that business need to create what-if scenarios for long term policies, and for daily market investments.</span></span> <span data-ttu-id="ce3ff-106">除此之外，像是「第 17 號國際財務報告準則」(IFRS 17) 等法規要求的報告也加重了肩上的負擔。</span><span class="sxs-lookup"><span data-stu-id="ce3ff-106">On top of that, regulations such as the International Financial Reporting Standard 17 (IFRS 17) add to their burden in the form of reports.</span></span> <span data-ttu-id="ce3ff-107">如果習慣使用網格運算，應可輕鬆適應 Azure 的虛擬運算世界。</span><span class="sxs-lookup"><span data-stu-id="ce3ff-107">If you are used to using grid computing, you can easily adapt to the world of virtual compute on Azure.</span></span> <span data-ttu-id="ce3ff-108">與其添購更多硬體，不如將錢投資在營運上，別花在立刻就會開始折舊的資產上。</span><span class="sxs-lookup"><span data-stu-id="ce3ff-108">Instead of buying more hardware, you'll spend your money on operations (not capital that begins to depreciate immediately).</span></span>

<span data-ttu-id="ce3ff-109">使用 Azure 計算的關鍵，在於將資料準備妥當，再發展出一套平行處理工作的方式。</span><span class="sxs-lookup"><span data-stu-id="ce3ff-109">The key to using Azure compute is to prepare your data and develop a way to parallelize the work.</span></span> <span data-ttu-id="ce3ff-110">而 Azure 計算在使用上的其他層面則涉及到了解如何運用 Azure Batch 或 Data Factory 等技術來處理工作。</span><span class="sxs-lookup"><span data-stu-id="ce3ff-110">The other aspects of using Azure compute comes from understanding how technologies such as Azure Batch or Data Factory can be used to process the work.</span></span> <span data-ttu-id="ce3ff-111">本指南最後也調查了使用資料科學 VM 或 Power BI 等報告選項。</span><span class="sxs-lookup"><span data-stu-id="ce3ff-111">Finally, the guide surveys options for reporting--such as using a Data Science VM, or Power BI.</span></span>

[<span data-ttu-id="ce3ff-112">精算風險分析與財務模型化</span><span class="sxs-lookup"><span data-stu-id="ce3ff-112">Actuarial risk analysis and financial modeling</span></span>](/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=overview-docs-dastarr)