---
title: 從 IoT 資料擷取可採取動作的見解摘要
description: 使用 Azure 服務從 IoT 資料擷取見解。 解決方案指南概觀。
author: ercenk
ms.author: ercenk
manager: gmarchet
ms.service: industry
ms.topic: article
ms.date: 11/28/2019
ms.openlocfilehash: 8ca31501d976e0b162735b7f55e2db7fae850776
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77052360"
---
# <a name="extracting-actionable-insights-from-iot-data"></a>從 IoT 資料擷取可採取動作的見解

製造商正在查看具有眼睛的現代化廠房，以使用數位工具改進生產與程序。 最常見的分母是物聯網 (IoT)。 機器現在已經發出一些資料了。 新機器無疑會提供更多資料。
但取得資料只是第一步。 我們的解決方案指南提供一個路徑來使用資料來執行動作，例如預測性維護。

簡而言之，解決方案著重在三個主要步驟：事項 (資料)、見解 (透過進階分析) 與動作。

![事項到見解到動作。](assets/extracting-insights-from-iot/things-insights-actions.png)

擁有資料很好，但資料只是原始素材。 透過分析取得的見解可引導您執行動作。

![Lambda 架構](assets/extracting-insights-from-iot/lambda-architecture.png)

架構很彈性，而且能讓您以高輸送量方式內嵌及分析資料。 就像您需要成長一樣，元件也可以相應放大及相應縮小以符合需求或節省成本。 指南也顯示實作架構的建議，例如，使用 Azure 事件中樞來內嵌，並針對分析用戶端使用 Power BI。

[從 IoT 資料擷取可採取動作的見解](./extracting-insights-from-iot-data.md)
