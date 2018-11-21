---
title: 圖像式搜尋概觀
author: scseely
ms.author: scseely, mazoroto
ms.date: 07/16/2018
ms.topic: article
ms.service: industry
description: 此文章說明將電子商務基礎結構從內部部署設施移轉到 Azure 的階段。
ms.openlocfilehash: 0c80e3068a1b23bf12d2468489fdd0b67c660dfa
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654205"
---
# <a name="visual-search-overview"></a>圖像式搜尋概觀

## <a name="executive-summary"></a>執行摘要

人工智慧提供轉換零售產業的潛能，就像我們今日所看到的。 相信零售商會開發 AI 支援的客戶體驗架構是合理的。 某些預期是使用 AI 來加強的平台將由於提供超個人化而使得營收增加。 數位商務持續提高客戶預期、喜好與行為。 即時互動、相關建議與超個人化使得只要按一下按鈕就可以方便、快速地完成交易。 透過自然語音、視覺等在應用程式中啟用智慧可讓零售環境變得更好，這樣可提高零售商價值同時影響客戶購物的方式。

此文件著重在**圖像式搜尋**的 AI  概念，並提供實作方面的關鍵考慮事項。 它提供工作流程範例，並將其階段對應到相關的 Azure 技術。 概念是以能夠利用其行動裝置拍攝或位於網際網路上的影像來進行相關和/或類似項目搜尋 (視體驗的意圖而定) 的客戶為基礎。 因此，圖像式搜尋可利用多媒體中繼資料點提高從文字項目到影像的速度，以快速提供可用的適用項目。

## <a name="visual-search-engines"></a>圖像式搜尋引擎

圖像式搜尋引擎可使用影像做為輸入，而且通常 (但不全然) 也使用影像做為輸出來擷取資訊。

在零售產業中，引擎由於下列好原因而變得越來越常見：

- 根據 [Emarketer](https://www.emarketer.com/Report/Visual-Commerce-2017-How-Image-Recognition-Augmentation-Changing-Retail/2002059) 在 2017 年發表的報告，大約 75% 的網際網路使用者在購買產品之前會搜尋圖片或影片。
- 根據 [Slyce](https://slyce.it/wp-content/uploads/2015/11/Visual_Search_Technology_and_Market.pdf) (一家圖像式搜尋公司) 在 2015 的報告，有 74% 的客戶也會發現文字搜尋無效率。

因此，根據 [Markets &amp; Markets](https://www.marketsandmarkets.com/PressReleases/image-recognition.asp) 研究，影像辨識市場到 2019 年的產值將超過 250 億美元。

有許多領先的電子商務品牌都使用此技術，因此對此技術的發展也做出相當的貢獻。 最早期的採用者包括：

- eBay，該公司提供其「影像搜尋」功能，並在其應用程式中「在 eBay 上搜尋」工具 目前只支援行動裝置)。
- Pinterest，該公司使用其鏡頭視覺化探索工具。
- Microsoft，該公司提供 Bing 圖像式搜尋。

## <a name="adopt-and-adapt"></a>採用並適應

幸運的是，您不需要擁有強大的運算能力就能從圖像式搜尋獲益。 具有影像型錄的任何公司都可以利用 Microsoft 建置在其 Azure 服務中的的 AI 專業。

[Bing 圖像式搜尋](https://azure.microsoft.com/en-us/services/cognitive-services/bing-visual-search/?WT.mc_id=vsearchgio-article-gmarchet) API 提供一種方式來從影像擷取脈絡資訊，例如，透過辨識家具、時尚、各種產品等。

它也會以視覺化方式從其自有型錄、具有相對購物來源的產品、相關搜尋傳回類似的影像。 雖然這很有趣，但若您的公司不是那些來源的其中一個，則用途可能也有限。

Bing 也將提供：

- 允許您探索影像中發現之物件或概念的標籤。
- 影像中感興趣區域 (例如，服飾、家具項目) 的週框方塊。

您可以納入該資訊來將搜尋空間 (與時間) 大幅縮小為公司的產品型錄，進而將其限制為僅顯示類似區域與感興趣類別的物件。

## <a name="implement-your-own"></a>實作您自己的

實作圖像式搜尋時有一些重要的元件必須考慮：

- 內嵌及篩選影像
- 儲存及擷取技術
- 功能化、編碼或「雜湊處理」
- 相似度度量或距離與排名

 ![](./assets/visual-search-use-case-overview/visual-search-pipeline.png)

*圖 1：圖像式搜尋管線範例*

### <a name="sourcing-the-pictures"></a>尋找圖片來源

若您並未擁有圖片型錄，您可能需要在可供開始用的資料集上將演算法定型，例如流行的 [MNIST](https://www.kaggle.com/zalando-research/fashionmnist)、深度 [fashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion.html) 等。 它們包含數種產品類別，而且常用來為影像分類與搜尋演算法進行效能評定。

 ![](./assets/visual-search-use-case-overview/deep-fashion-dataset.png)

*圖 2：來自 The Deep Fashion 資料集的範例*

### <a name="filtering-the-images"></a>篩選影像

大部分的效能評定資料集 (例如前面所提到的) 都已預先處理。

若您要建置自己的項目，您至少希望影像都有相同的大小，這大部分是由您的模型要針對其定型的輸入所控制。

在許多案例中，最好也將影像的亮度正常化。 視您搜尋的詳細層級而定，色彩也可能是多餘的資訊，因此降低為黑白兩色將有助於縮短處理時間。

最後 (但並非最不重要) 一點是，影像資料集在它代表的不同類別之間應該要平衡。

### <a name="image-database"></a>影像資料庫

資料層是您架構中最需要小心處理的元件。 它將會包含：

- 影像
- 任何有關影像的中繼資料 (大小、標籤、產品 SKU、描述)
- 由機器學習模型產生的資料 (例如，每個影像的 4096 個元素的數字向量)

當您不同來源或使用數個機器學習模型來擷取影像以獲得最大化效能時，資料結構將會變更。 因此，選擇一個可以處理半結構化資料與非固定結構描述的技術或組合非常重要。

您可能也想要要求數目最少的實用資料點 (例如，金鑰、產品 SKU、描述、標籤欄位的影像識別碼)。

[Azure CosmosDB](https://azure.microsoft.com/en-us/services/cosmos-db/?WT.mc_id=vsearchgio-article-gmarchet) 針對建置在其上的應用程式提供您所需的彈性與各種存取機制 (有助於您的型錄搜尋)。 不過，追求最佳價格/效能時務必小心。 CosmosDB 允許存放文件附件，但每個帳戶都有總限制，而且它的成本可能很高。 通常建議將實際影像檔存放在 Blob 中，並在資料庫中插入其連結。 在 CosmosDB 的案例中，這意指建立包含與該影像關聯之型錄屬性 (SKU、標籤等) 的文件，以及包含影像檔案 (例如，在 Azure Blob 儲存體、OneDrive 等上) 之 URL 的附件。

 ![](./assets/visual-search-use-case-overview/cosmosdb-data-model.png)

*圖 3：CosmosDB 階層式資源模型*

若計畫利用 Cosmos DB 全球分布的優點，請注意，它將會複寫文件與附件，但不會複寫連結的檔案。 您可能想要考慮適用於那些的內容發佈網路。

其他適用的技術是 Azure SQL Database (若可接受固定結構描述) 與 Blob 或甚至是 Azure 資料表與 Blob 的組合，可讓您以實惠的價格取得儲存及擷取功能。

### <a name="feature-extraction-amp-encoding"></a>特色擷取與編碼

編碼程序會從資料庫中的圖片擷取突出的特色，並將它們每一個都對應到疏鬆「特色」向量 (具有許多零的向量)，此向量可有數千個元件。 此向量是特色 (例如，邊緣、形狀) 的數字型表示法，可將圖片分類 (類似於代碼)。

特色擷取技術通常使用「移轉學習機制」。 此機制的運作方式為：您選取預先定型的類神經網路、使用此網路執行每個影像，並將產生的特色向量存放回您的影像資料庫。 透過該方式，您可以從將該網路定型的人「移轉」學習。 Microsoft 已開發並發行數個預先定型的網路，這些網路廣為使用於影像辨識工作，例如 [ResNet50](https://www.kaggle.com/keras/resnet50)。

視類神經網路而定，特色向量可能更長且更疏鬆，或更短且更不疏鬆，因此記憶體與儲存體需求將會不同。

此外，您可能會發現不同的網路適用於不同的類別，因此圖像式搜尋的實作實際可能會產生大小不同的特色向量。

預先定型的類神經網路非常容易使用，但可能不如在您的影像型錄上進行定型的自訂模型來得有效率。 那些預先定型的網路通常是針對效能測試資料集的分類 (而非在您的特定影像集合上搜尋) 所設計。

您可以修改它們並將其重新定型，讓它們可以同時產生類別預測與稠密 (亦即，較小、非疏鬆) 向量，這對於限制搜尋空間、降低記憶體與儲存體需求很有用。 您可以使用二進位向量，它通常被稱為[語意雜湊](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)，這個詞彙是從文件編碼與擷取技術所衍生。 按進位表示法可簡化進一步的計算。

 ![](./assets/visual-search-use-case-overview/resnet-modifications.png)

*圖 4：對適用於圖像式搜尋之 ResNet 的修改 – F. Yang et al.，2017*

不論您是選擇預先定型的模型或開發您自己的模型，您仍必須決定要在哪執行模型本身的特色化和/ 或定型。

Azure 提供數個選項：VM、Azure Batch、[Batch AI](https://azure.microsoft.com/en-us/services/batch-ai/?WT.mc_id=vsearchgio-article-gmarchet)、Databricks 叢集。 不過，在所有案例中，最佳價格/效能是以 GPU 使用率來給定。

Microsoft 最近也宣布推出 FPGA，讓您能以 GPU 成本的數分之一快速執行計算 ([Brainwave](https://www.microsoft.com/en-us/research/blog/microsoft-unveils-project-brainwave/?WT.mc_id=vsearchgio-article-gmarchet) 專案)。 不過，在本文撰寫時，此供應項目受限於特定網路架構，因此您必須密切評估其效能。

### <a name="similarity-measure-or-distance"></a>相似度度量或距離

當影像在特色向量空間中呈現時，尋找相似度就成為定義此類空間中各點距離度量的問題。 一旦定義距離，您就可以計算類似影像叢集和/或定義相似度矩陣。 視選取的距離度量而定，結果可能會不同。 例如，最常見的歐幾里德距離會度量實數向量，這很容易了解：它會擷取距離的大小量。 不過，從計算的觀點來看，它很沒有效率。

[餘弦](https://en.wikipedia.org/wiki/Cosine_similarity)距離通常用來擷取向量的方向，而不是其大小量。

其他替代項目，例如以二進位表示法表示的 [Hamming](https://en.wikipedia.org/wiki/Hamming_distance) 距離精確度比較沒有那麼高，但效率比較好且速度比較快。

向量大小與距離度量的組合將決定搜尋所需的計算資源與記憶體資源需求為何。

### <a name="search-amp-ranking"></a>搜尋與排名

一旦定義相似度，我們就必須設計不同的有效率方法來擷取最接近傳遞為輸入之項目的 N 個項目，然後傳回識別碼清單。 這也稱為「影像排名」。 在大型資料集上，計算每個距離的時間通常很長，因此我們使用近似最接近像素演算法。 針對那些用途，有數個開放原始碼程式庫可用，因此您不需要從頭開始撰寫程式碼。

最後，記憶體與計算需求將決定已定型模型的部署技術，以及高可用性。 一般而言，搜尋空間將會分割，而且數個排名演算法執行個體將會同時執行。 允許延展性與可用性的其中一個選項是 [Azure Kubernetes](https://azure.microsoft.com/en-us/services/container-service/kubernetes/?WT.mc_id=vsearchgio-article-gmarchet) 叢集。 在該案例中，建議您跨數個容器 (每個容器都負責處理一個搜尋空間分割區) 與數個節點 (以達成高可用性) 部署排名模型。

## <a name="next-steps"></a>後續步驟

實作圖像式搜尋不見得很複雜。 您可以使用 Bing 或使用 Azure 建置您自己的解決方案，同時獲益於 Microsoft 的 AI 研究與工具。

### <a name="trial"></a>試用版

- 試用[圖像式搜尋 API 測試主控台](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac) \(英文\)

### <a name="develop"></a>開發

- 若要開始建立自訂服務，請參閱 [Bing 圖像式搜尋 API 概觀](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview/?WT.mc_id=vsearchgio-article-gmarchet)
- 若要建立您的第一個要求，請參閱快速入門：[C#](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/csharp) | [Java](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/java) | [node.js](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/nodejs) | [Python](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/python)
- 熟悉[圖像式搜尋 API](https://aka.ms/bingvisualsearchreferencedoc) 參考。

### <a name="background"></a>背景

- [深度學習影像分離](https://www.microsoft.com/developerblog/2018/04/18/deep-learning-image-segmentation-for-ecommerce-catalogue-visual-search/?WT.mc_id=vsearchgio-article-gmarchet)：Microsoft 白皮書描述從背景分離影像的程序
- [Ebay 的圖像式搜尋](https://arxiv.org/abs/1706.03154) \(英文\)：康乃爾大學研究
- [Pinterest 的圖像式搜尋](https://arxiv.org/abs/1702.04680) \(英文\)：康乃爾大學研究
- [S語意式雜湊處理](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)：多倫多大學研究

_此文章是由 Giovanni Marchetti 與 Mariya Zorotovich 所撰寫。_