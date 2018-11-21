---
title: 從 IoT 資料擷取可採取動作的見解概觀
description: 使用 Azure 服務從 IoT 資料擷取見解。 解決方案指南概觀。
author: ercenk
ms.author: ercenk
manager: gmarchet
ms.service: industry
ms.topic: article
ms.date: 09/26/2018
ms.openlocfilehash: 9528983a06602bd469a4d651bda8478aa758152b
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654115"
---
# <a name="extracting-actionable-insights-from-iot-data"></a><span data-ttu-id="7697c-104">從 IoT 資料擷取可採取動作的見解</span><span class="sxs-lookup"><span data-stu-id="7697c-104">Extracting actionable insights from IoT data</span></span>

<span data-ttu-id="7697c-105">製造商正在查看具有眼睛的現代化廠房，以使用數位工具改進生產與程序。</span><span class="sxs-lookup"><span data-stu-id="7697c-105">Manufacturers are looking at a modern factory floor with an eye to improving production and processes using digital tools.</span></span> <span data-ttu-id="7697c-106">最常見的分母是物聯網 (IoT)。</span><span class="sxs-lookup"><span data-stu-id="7697c-106">And the greatest common denominator is the Internet of Things (IoT).</span></span> <span data-ttu-id="7697c-107">機器現在已經發出一些資料了。</span><span class="sxs-lookup"><span data-stu-id="7697c-107">Machines have been emitting data for some time now.</span></span> <span data-ttu-id="7697c-108">新機器無疑會提供更多資料。</span><span class="sxs-lookup"><span data-stu-id="7697c-108">New machines will doubtlessly be providing even more data.</span></span>
<span data-ttu-id="7697c-109">但取得資料只是第一步。</span><span class="sxs-lookup"><span data-stu-id="7697c-109">But having the data is just the first step.</span></span> <span data-ttu-id="7697c-110">我們的解決方案指南提供一個路徑來使用資料來執行動作，例如預測性維護。</span><span class="sxs-lookup"><span data-stu-id="7697c-110">Our solution guide provides a path to using the data for actions such as predictive maintenance.</span></span>

<span data-ttu-id="7697c-111">簡而言之，解決方案著重在三個主要步驟：事項 (資料)、見解 (透過進階分析) 與動作。</span><span class="sxs-lookup"><span data-stu-id="7697c-111">In brief, the solution focuses on three major steps: things (data), insights (via advanced analytics) and actions.</span></span>

![事項到見解到動作。](assets/extracting-insights-from-iot/things-insights-actions.png)

<span data-ttu-id="7697c-113">擁有資料很好，但資料只是原始素材。</span><span class="sxs-lookup"><span data-stu-id="7697c-113">Having data is great, but data is just raw materials.</span></span> <span data-ttu-id="7697c-114">透過分析取得的見解可引導您執行動作。</span><span class="sxs-lookup"><span data-stu-id="7697c-114">Insights through analysis guides your actions.</span></span>

![Lambda 架構](assets/extracting-insights-from-iot/lambda-architecture.png)

<span data-ttu-id="7697c-116">架構很彈性，而且能讓您以高輸送量方式內嵌及分析資料。</span><span class="sxs-lookup"><span data-stu-id="7697c-116">The architecture is flexible and lets you ingest and analyze data at high throughput.</span></span> <span data-ttu-id="7697c-117">就像您需要成長一樣，元件也可以相應放大及相應縮小以符合需求或節省成本。</span><span class="sxs-lookup"><span data-stu-id="7697c-117">As your needs grow, the components can also scale up and down to meet demand or save cost.</span></span> <span data-ttu-id="7697c-118">指南也顯示實作架構的建議，例如，使用 Azure 事件中樞來內嵌，並針對分析用戶端使用 Power BI。</span><span class="sxs-lookup"><span data-stu-id="7697c-118">The guide also shows you recommendations for implementing the architecture—for example, using Azure Event Hubs to ingest, and Power BI for an analytics client.</span></span>

[<span data-ttu-id="7697c-119">從 IoT 資料擷取可採取動作的見解</span><span class="sxs-lookup"><span data-stu-id="7697c-119">Extracting actionable insights from IoT data</span></span>](./extracting-insights-from-iot-data.md)
