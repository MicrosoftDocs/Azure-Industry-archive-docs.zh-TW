---
title: 使用 CosmosDB 在零售業中進行圖像式搜尋的入門
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 本文章會說明將電子商務基礎結構從內部部署移轉到 Azure 的各個階段。
ms.openlocfilehash: b43ea305e11ac32da58e4d0521d79f90d5c23d85
ms.sourcegitcommit: 2714a77488c413f01beb169a18acab45663bcfd7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308465"
---
# <a name="visual-search-overview"></a><span data-ttu-id="22644-103">圖像式搜尋概觀</span><span class="sxs-lookup"><span data-stu-id="22644-103">Visual Search Overview</span></span>

## <a name="executive-summary"></a><span data-ttu-id="22644-104">執行摘要</span><span class="sxs-lookup"><span data-stu-id="22644-104">Executive Summary</span></span>

<span data-ttu-id="22644-105">人工智慧提供轉換零售產業的潛能，就像我們今日所看到的。</span><span class="sxs-lookup"><span data-stu-id="22644-105">Artificial Intelligence offers the potential to transform retailing as we know it today.</span></span> <span data-ttu-id="22644-106">相信零售商會開發 AI 支援的客戶體驗架構是合理的。</span><span class="sxs-lookup"><span data-stu-id="22644-106">It is reasonable to believe that retailers will develop a customer experience architecture supported by AI.</span></span> <span data-ttu-id="22644-107">某些預期是使用 AI 來加強的平台將由於提供超個人化而使得營收增加。</span><span class="sxs-lookup"><span data-stu-id="22644-107">Some expectations are that a platform enhanced with AI will provide a revenue bump due to hyper personalization.</span></span> <span data-ttu-id="22644-108">數位商務持續提高客戶預期、喜好與行為。</span><span class="sxs-lookup"><span data-stu-id="22644-108">Digital commerce continues to heighten customer expectations, preferences and behavior.</span></span> <span data-ttu-id="22644-109">即時互動、相關建議與超個人化使得只要按一下按鈕就可以方便、快速地完成交易。</span><span class="sxs-lookup"><span data-stu-id="22644-109">Demands such as real-time engagement, relevant recommendations and hyper-personalization are driving speed and convenience at a click of a button.</span></span> <span data-ttu-id="22644-110">透過自然語音、視覺等在應用程式中啟用智慧可讓零售環境變得更好，這樣可提高零售商價值同時影響客戶購物的方式。</span><span class="sxs-lookup"><span data-stu-id="22644-110">Enabling intelligence in applications through natural speech, vision, etc. enables improvements in retail that will increase value while disrupting how customers shop.</span></span>

<span data-ttu-id="22644-111">此文件著重在**圖像式搜尋**的 AI  概念，並提供實作方面的關鍵考慮事項。</span><span class="sxs-lookup"><span data-stu-id="22644-111">This document focuses on the AI  concept of **visual search** and offers a few key considerations on its implementation.</span></span> <span data-ttu-id="22644-112">它提供工作流程範例，並將其階段對應到相關的 Azure 技術。</span><span class="sxs-lookup"><span data-stu-id="22644-112">It provides a workflow example and maps its stages to the relevant Azure technologies.</span></span> <span data-ttu-id="22644-113">概念是以能夠利用其行動裝置拍攝或位於網際網路上的影像來進行相關和/或類似項目搜尋 (視體驗的意圖而定) 的客戶為基礎。</span><span class="sxs-lookup"><span data-stu-id="22644-113">The concept is based on a customer being able to leverage an image taken with their mobile device or located on the internet to conduct a search of relevant and/or like items, depending upon the intention of the experience.</span></span> <span data-ttu-id="22644-114">因此，圖像式搜尋可利用多媒體中繼資料點提高從文字項目到影像的速度，以快速提供可用的適用項目。</span><span class="sxs-lookup"><span data-stu-id="22644-114">Thus, visual search improves speed from texted entry to an image with multiple meta-data points to quickly surface applicable items available.</span></span>

## <a name="visual-search-engines"></a><span data-ttu-id="22644-115">圖像式搜尋引擎</span><span class="sxs-lookup"><span data-stu-id="22644-115">Visual Search Engines</span></span>

<span data-ttu-id="22644-116">圖像式搜尋引擎可使用影像做為輸入，而且通常 (但不全然) 也使用影像做為輸出來擷取資訊。</span><span class="sxs-lookup"><span data-stu-id="22644-116">Visual search engines retrieve information using images as input and often—but not exclusively—as output too.</span></span>

<span data-ttu-id="22644-117">在零售產業中，引擎由於下列好原因而變得越來越常見：</span><span class="sxs-lookup"><span data-stu-id="22644-117">Engines are becoming more and more common in the retail industry, and for very good reasons:</span></span>

- <span data-ttu-id="22644-118">根據 [Emarketer](https://www.emarketer.com/Report/Visual-Commerce-2017-How-Image-Recognition-Augmentation-Changing-Retail/2002059) 在 2017 年發表的報告，大約 75% 的網際網路使用者在購買產品之前會搜尋圖片或影片。</span><span class="sxs-lookup"><span data-stu-id="22644-118">Around 75% of internet users search for pictures or videos of a product before making a purchase, according to an [Emarketer](https://www.emarketer.com/Report/Visual-Commerce-2017-How-Image-Recognition-Augmentation-Changing-Retail/2002059) report published in 2017.</span></span>
- <span data-ttu-id="22644-119">根據 [Slyce](https://slyce.it/wp-content/uploads/2015/11/Visual_Search_Technology_and_Market.pdf) (一家圖像式搜尋公司) 在 2015 的報告，有 74% 的客戶也會發現文字搜尋無效率。</span><span class="sxs-lookup"><span data-stu-id="22644-119">74% of consumers also find text searches inefficient, according to [Slyce](https://slyce.it/wp-content/uploads/2015/11/Visual_Search_Technology_and_Market.pdf) (a visual search company) 2015 report.</span></span>

<span data-ttu-id="22644-120">因此，根據 [Markets &amp; Markets](https://www.marketsandmarkets.com/PressReleases/image-recognition.asp) 研究，影像辨識市場到 2019 年的產值將超過 250 億美元。</span><span class="sxs-lookup"><span data-stu-id="22644-120">Therefore, the image recognition market will be worth more than $25 billion by 2019, according to research by [Markets &amp; Markets](https://www.marketsandmarkets.com/PressReleases/image-recognition.asp).</span></span>

<span data-ttu-id="22644-121">有許多領先的電子商務品牌都使用此技術，因此對此技術的發展也做出相當的貢獻。</span><span class="sxs-lookup"><span data-stu-id="22644-121">The technology has already taken hold with major e-commerce brands, who have also contributed significantly to its development.</span></span> <span data-ttu-id="22644-122">最早期的採用者包括：</span><span class="sxs-lookup"><span data-stu-id="22644-122">The most prominent early adopters are probably:</span></span>

- <span data-ttu-id="22644-123">eBay，該公司提供其「影像搜尋」功能，並在其應用程式中「在 eBay 上搜尋」工具 目前只支援行動裝置)。</span><span class="sxs-lookup"><span data-stu-id="22644-123">eBay with their Image Search and "Find It on eBay" tools in their app (this is currently only a mobile experience).</span></span>
- <span data-ttu-id="22644-124">Pinterest，該公司使用其鏡頭視覺化探索工具。</span><span class="sxs-lookup"><span data-stu-id="22644-124">Pinterest with their Lens visual discovery tool.</span></span>
- <span data-ttu-id="22644-125">Microsoft，該公司提供 Bing 圖像式搜尋。</span><span class="sxs-lookup"><span data-stu-id="22644-125">Microsoft with Bing Visual Search.</span></span>

## <a name="adopt-and-adapt"></a><span data-ttu-id="22644-126">採用並適應</span><span class="sxs-lookup"><span data-stu-id="22644-126">Adopt and Adapt</span></span>

<span data-ttu-id="22644-127">幸運的是，您不需要擁有強大的運算能力就能從圖像式搜尋獲益。</span><span class="sxs-lookup"><span data-stu-id="22644-127">Fortunately, you don't need vast amounts of computing power to profit from visual search.</span></span> <span data-ttu-id="22644-128">具有影像型錄的任何公司都可以利用 Microsoft 建置在其 Azure 服務中的的 AI 專業。</span><span class="sxs-lookup"><span data-stu-id="22644-128">Any business with an image catalog can take advantage of Microsoft's AI expertise built into its Azure services.</span></span>

<span data-ttu-id="22644-129">[Bing 圖像式搜尋](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/?WT.mc_id=vsearchgio-article-gmarchet) API 提供一種方式來從影像擷取脈絡資訊，例如，透過辨識家具、時尚、各種產品等。</span><span class="sxs-lookup"><span data-stu-id="22644-129">[Bing Visual Search](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/?WT.mc_id=vsearchgio-article-gmarchet) API provides a way to extract context information from images, identifying—for instance—home furnishings, fashion, several kinds of products, etc.</span></span>

<span data-ttu-id="22644-130">它也會以視覺化方式從其自有型錄、具有相對購物來源的產品、相關搜尋傳回類似的影像。</span><span class="sxs-lookup"><span data-stu-id="22644-130">It will also return visually similar images out of its own catalog, products with relative shopping sources, related searches.</span></span> <span data-ttu-id="22644-131">雖然這很有趣，但若您的公司不是那些來源的其中一個，則用途可能也有限。</span><span class="sxs-lookup"><span data-stu-id="22644-131">While interesting, this will be of limited use if your company is not one of those sources.</span></span>

<span data-ttu-id="22644-132">Bing 也將提供：</span><span class="sxs-lookup"><span data-stu-id="22644-132">Bing will also provide:</span></span>

- <span data-ttu-id="22644-133">允許您探索影像中發現之物件或概念的標籤。</span><span class="sxs-lookup"><span data-stu-id="22644-133">Tags that allow you to explore objects or concepts found in the image.</span></span>
- <span data-ttu-id="22644-134">影像中感興趣區域 (例如，服飾、家具項目) 的週框方塊。</span><span class="sxs-lookup"><span data-stu-id="22644-134">Bounding boxes for regions of interest in the image (e.g. clothing, furniture items).</span></span>

<span data-ttu-id="22644-135">您可以納入該資訊來將搜尋空間 (與時間) 大幅縮小為公司的產品型錄，進而將其限制為僅顯示類似區域與感興趣類別的物件。</span><span class="sxs-lookup"><span data-stu-id="22644-135">You can take that information to reduce the search space (and time) into a company's product catalog significantly, restricting it to objects like those in the region and category of interest.</span></span>

## <a name="implement-your-own"></a><span data-ttu-id="22644-136">實作您自己的</span><span class="sxs-lookup"><span data-stu-id="22644-136">Implement Your Own</span></span>

<span data-ttu-id="22644-137">實作圖像式搜尋時有一些重要的元件必須考慮：</span><span class="sxs-lookup"><span data-stu-id="22644-137">There are a few key components to consider when implementing visual search:</span></span>

- <span data-ttu-id="22644-138">內嵌及篩選影像</span><span class="sxs-lookup"><span data-stu-id="22644-138">Ingesting and filtering images</span></span>
- <span data-ttu-id="22644-139">儲存及擷取技術</span><span class="sxs-lookup"><span data-stu-id="22644-139">Storage and retrieval techniques</span></span>
- <span data-ttu-id="22644-140">功能化、編碼或「雜湊處理」</span><span class="sxs-lookup"><span data-stu-id="22644-140">Featurization, encoding or "hashing"</span></span>
- <span data-ttu-id="22644-141">相似度度量或距離與排名</span><span class="sxs-lookup"><span data-stu-id="22644-141">Similarity measures or distances and ranking</span></span>

 ![](./assets/visual-search-use-case-overview/visual-search-pipeline.png)

<span data-ttu-id="22644-142">*圖 1：* 圖像式搜尋管線範例</span><span class="sxs-lookup"><span data-stu-id="22644-142">*Figure 1: Example of Visual Search Pipeline*</span></span>

### <a name="sourcing-the-pictures"></a><span data-ttu-id="22644-143">尋找圖片來源</span><span class="sxs-lookup"><span data-stu-id="22644-143">Sourcing the Pictures</span></span>

<span data-ttu-id="22644-144">若您並未擁有圖片型錄，您可能需要在可供開始用的資料集上將演算法定型，例如流行的 [MNIST](https://www.kaggle.com/zalando-research/fashionmnist)、深度 [fashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion.html) 等。</span><span class="sxs-lookup"><span data-stu-id="22644-144">If you do not own a picture catalog, you may need to train the algorithms on openly available data sets, such as fashion [MNIST](https://www.kaggle.com/zalando-research/fashionmnist), deep [fashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion.html) and similar.</span></span> <span data-ttu-id="22644-145">它們包含數種產品類別，而且常用來為影像分類與搜尋演算法進行效能評定。</span><span class="sxs-lookup"><span data-stu-id="22644-145">They contain several categories of products and are commonly used to benchmark image categorization and search algorithms.</span></span>

 ![](./assets/visual-search-use-case-overview/deep-fashion-dataset.png)

<span data-ttu-id="22644-146">*圖 2：* 來自 The Deep Fashion 資料集的範例</span><span class="sxs-lookup"><span data-stu-id="22644-146">*Figure 2: An Example from The Deep Fashion Dataset*</span></span>

### <a name="filtering-the-images"></a><span data-ttu-id="22644-147">篩選影像</span><span class="sxs-lookup"><span data-stu-id="22644-147">Filtering the Images</span></span>

<span data-ttu-id="22644-148">大部分的效能評定資料集 (例如前面所提到的) 都已預先處理。</span><span class="sxs-lookup"><span data-stu-id="22644-148">Most benchmark datasets - such as those mentioned before - have already been pre-processed.</span></span>

<span data-ttu-id="22644-149">若您要建置自己的項目，您至少希望影像都有相同的大小，這大部分是由您的模型要針對其定型的輸入所控制。</span><span class="sxs-lookup"><span data-stu-id="22644-149">If you are building your own, at a minimum you will want the images to all have the same size, mostly dictated by the input that your model is trained for.</span></span>

<span data-ttu-id="22644-150">在許多案例中，最好也將影像的亮度正常化。</span><span class="sxs-lookup"><span data-stu-id="22644-150">In many cases, it is best also to normalize the luminosity of the images.</span></span> <span data-ttu-id="22644-151">視您搜尋的詳細層級而定，色彩也可能是多餘的資訊，因此降低為黑白兩色將有助於縮短處理時間。</span><span class="sxs-lookup"><span data-stu-id="22644-151">Depending on the detail level of your search, color may also be redundant information, so reducing to black and white will help with processing times.</span></span>

<span data-ttu-id="22644-152">最後 (但並非最不重要) 一點是，影像資料集在它代表的不同類別之間應該要平衡。</span><span class="sxs-lookup"><span data-stu-id="22644-152">Last but not least, the image dataset should be balanced across the different classes it represents.</span></span>

### <a name="image-database"></a><span data-ttu-id="22644-153">影像資料庫</span><span class="sxs-lookup"><span data-stu-id="22644-153">Image Database</span></span>

<span data-ttu-id="22644-154">資料層是您架構中最需要小心處理的元件。</span><span class="sxs-lookup"><span data-stu-id="22644-154">The data layer is a particularly delicate component of your architecture.</span></span> <span data-ttu-id="22644-155">它將會包含：</span><span class="sxs-lookup"><span data-stu-id="22644-155">It will contain:</span></span>

- <span data-ttu-id="22644-156">映像</span><span class="sxs-lookup"><span data-stu-id="22644-156">Images</span></span>
- <span data-ttu-id="22644-157">任何有關影像的中繼資料 (大小、標籤、產品 SKU、描述)</span><span class="sxs-lookup"><span data-stu-id="22644-157">Any metadata about the images (size, tags, product SKUs, description)</span></span>
- <span data-ttu-id="22644-158">由機器學習模型產生的資料 (例如，每個影像的 4096 個元素的數字向量)</span><span class="sxs-lookup"><span data-stu-id="22644-158">Data generated by the machine learning model (for instance a 4096-element numerical vector  per image)</span></span>

<span data-ttu-id="22644-159">當您不同來源或使用數個機器學習模型來擷取影像以獲得最大化效能時，資料結構將會變更。</span><span class="sxs-lookup"><span data-stu-id="22644-159">As you retrieve images from different sources or use several machine learning models for optimal performance, the structure of the data will change.</span></span> <span data-ttu-id="22644-160">因此，選擇一個可以處理半結構化資料與非固定結構描述的技術或組合非常重要。</span><span class="sxs-lookup"><span data-stu-id="22644-160">It is therefore important to choose a technology or combination that can deal with semi-structured data and no fixed schema.</span></span>

<span data-ttu-id="22644-161">您可能也想要要求數目最少的實用資料點 (例如，金鑰、產品 SKU、描述、標籤欄位的影像識別碼)。</span><span class="sxs-lookup"><span data-stu-id="22644-161">You may also want to require a minimum number of useful data points (e.g. an image identifier or key, a product sku, a description, a tag field).</span></span>

<span data-ttu-id="22644-162">[Azure CosmosDB](https://azure.microsoft.com/services/cosmos-db/?WT.mc_id=vsearchgio-article-gmarchet) 針對建置在其上的應用程式提供您所需的彈性與各種存取機制 (有助於您的型錄搜尋)。</span><span class="sxs-lookup"><span data-stu-id="22644-162">[Azure CosmosDB](https://azure.microsoft.com/services/cosmos-db/?WT.mc_id=vsearchgio-article-gmarchet) offers the required flexibility and a variety of access mechanisms for applications built on top of it (which will help with your catalog search).</span></span> <span data-ttu-id="22644-163">不過，追求最佳價格/效能時務必小心。</span><span class="sxs-lookup"><span data-stu-id="22644-163">However, one has to be careful to drive the best price/performance.</span></span> <span data-ttu-id="22644-164">CosmosDB 允許存放文件附件，但每個帳戶都有總限制，而且它的成本可能很高。</span><span class="sxs-lookup"><span data-stu-id="22644-164">CosmosDB allows document attachments to be stored, but there is a total limit per account and it may be a costly proposition.</span></span> <span data-ttu-id="22644-165">通常建議將實際影像檔存放在 Blob 中，並在資料庫中插入其連結。</span><span class="sxs-lookup"><span data-stu-id="22644-165">It is common practice to store the actual image files in blobs and insert a link to them in the database.</span></span> <span data-ttu-id="22644-166">在 CosmosDB 的案例中，這意指建立包含與該影像關聯之型錄屬性 (SKU、標籤等) 的文件，以及包含影像檔案 (例如，在 Azure Blob 儲存體、OneDrive 等上) 之 URL 的附件。</span><span class="sxs-lookup"><span data-stu-id="22644-166">In the case of CosmosDB this implies creating a document that contains the catalog properties associated to that image (sku, tag etc.) and an attachment that contains the URL of the image file (e.g. on Azure blob storage, OneDrive etc).</span></span>

 ![](./assets/visual-search-use-case-overview/cosmosdb-data-model.png)

<span data-ttu-id="22644-167">*圖 3：* CosmosDB 階層式資源模型</span><span class="sxs-lookup"><span data-stu-id="22644-167">*Figure 3: CosmosDB Hierarchical Resource Model*</span></span>

<span data-ttu-id="22644-168">若計畫利用 Cosmos DB 全球分布的優點，請注意，它將會複寫文件與附件，但不會複寫連結的檔案。</span><span class="sxs-lookup"><span data-stu-id="22644-168">If you plan to take advantage of the global distribution of Cosmos DB, note that it will replicate the documents and attachments, but not the linked files.</span></span> <span data-ttu-id="22644-169">您可能想要考慮適用於那些的內容發佈網路。</span><span class="sxs-lookup"><span data-stu-id="22644-169">You may want to consider a content distribution network for those.</span></span>

<span data-ttu-id="22644-170">其他適用的技術是 Azure SQL Database (若可接受固定結構描述) 與 Blob 或甚至是 Azure 資料表與 Blob 的組合，可讓您以實惠的價格取得儲存及擷取功能。</span><span class="sxs-lookup"><span data-stu-id="22644-170">Other applicable technologies are a combination of Azure SQL Database (if fixed schema is acceptable) and blobs, or even Azure Tables and blobs for inexpensive and fast storage and retrieval.</span></span>

### <a name="feature-extraction-amp-encoding"></a><span data-ttu-id="22644-171">特色擷取與編碼</span><span class="sxs-lookup"><span data-stu-id="22644-171">Feature Extraction &amp; Encoding</span></span>

<span data-ttu-id="22644-172">編碼程序會從資料庫中的圖片擷取突出的特色，並將它們每一個都對應到疏鬆「特色」向量 (具有許多零的向量)，此向量可有數千個元件。</span><span class="sxs-lookup"><span data-stu-id="22644-172">The encoding process extracts salient features from pictures in the database and maps each of them to a sparse "feature" vector (a vector with many zeros) that can have thousands of components.</span></span> <span data-ttu-id="22644-173">此向量是特色 (例如，邊緣、形狀) 的數字型表示法，可將圖片分類 (類似於代碼)。</span><span class="sxs-lookup"><span data-stu-id="22644-173">This vector is a numerical representation of the features (e.g. edges, shapes) that characterize the picture – akin to a code.</span></span>

<span data-ttu-id="22644-174">特色擷取技術通常使用「移轉學習機制」  。</span><span class="sxs-lookup"><span data-stu-id="22644-174">Feature extraction techniques typically use _transfer learning mechanisms_.</span></span> <span data-ttu-id="22644-175">此機制的運作方式為：您選取預先定型的類神經網路、使用此網路執行每個影像，並將產生的特色向量存放回您的影像資料庫。</span><span class="sxs-lookup"><span data-stu-id="22644-175">This occurs when you select a pre-trained neural network, run each image through it and store the feature vector  produced back in your image database.</span></span> <span data-ttu-id="22644-176">透過該方式，您可以從將該網路定型的人「移轉」學習。</span><span class="sxs-lookup"><span data-stu-id="22644-176">In that way, you "transfer" the learning from whoever trained the network.</span></span> <span data-ttu-id="22644-177">Microsoft 已開發並發行數個預先定型的網路，這些網路廣為使用於影像辨識工作，例如 [ResNet50](https://www.kaggle.com/keras/resnet50)。</span><span class="sxs-lookup"><span data-stu-id="22644-177">Microsoft has developed and published several pre-trained networks that have been widely used for image recognition tasks, such as [ResNet50](https://www.kaggle.com/keras/resnet50).</span></span>

<span data-ttu-id="22644-178">視類神經網路而定，特色向量可能更長且更疏鬆，或更短且更不疏鬆，因此記憶體與儲存體需求將會不同。</span><span class="sxs-lookup"><span data-stu-id="22644-178">Depending on the neural network, the feature vector will be more or less long and sparse, hence the memory and storage requirements will vary.</span></span>

<span data-ttu-id="22644-179">此外，您可能會發現不同的網路適用於不同的類別，因此圖像式搜尋的實作實際可能會產生大小不同的特色向量。</span><span class="sxs-lookup"><span data-stu-id="22644-179">Also, you may find that different networks are applicable to different categories, hence an implementation of visual search may actually generate feature vectors of varying size.</span></span>

<span data-ttu-id="22644-180">預先定型的類神經網路非常容易使用，但可能不如在您的影像型錄上進行定型的自訂模型來得有效率。</span><span class="sxs-lookup"><span data-stu-id="22644-180">Pre-trained neural networks are relatively easy to use but may not be as efficient a custom model trained on your image catalog.</span></span> <span data-ttu-id="22644-181">那些預先定型的網路通常是針對效能測試資料集的分類 (而非在您的特定影像集合上搜尋) 所設計。</span><span class="sxs-lookup"><span data-stu-id="22644-181">Those pre-trained networks are typically designed for classification of benchmark datasets rather than search on your specific collection of images.</span></span>

<span data-ttu-id="22644-182">您可以修改它們並將其重新定型，讓它們可以同時產生類別預測與稠密 (亦即，較小、非疏鬆) 向量，這對於限制搜尋空間、降低記憶體與儲存體需求很有用。</span><span class="sxs-lookup"><span data-stu-id="22644-182">You may want to modify and retrain them so they produce both a category prediction and a dense (i.e. smaller, not sparse) vector, which will be very useful to restrict the search space, reduce memory and storage requirements.</span></span> <span data-ttu-id="22644-183">您可以使用二進位向量，它通常被稱為[語意雜湊](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)，這個詞彙是從文件編碼與擷取技術所衍生。</span><span class="sxs-lookup"><span data-stu-id="22644-183">Binary vectors can be used and are often referred to as " [semantic hash](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)" – a term derived from document encoding and retrieval techniques.</span></span> <span data-ttu-id="22644-184">按進位表示法可簡化進一步的計算。</span><span class="sxs-lookup"><span data-stu-id="22644-184">The binary representation simplifies further calculations.</span></span>

 ![](./assets/visual-search-use-case-overview/resnet-modifications.png)

<span data-ttu-id="22644-185">*圖 4：* 對適用於圖像式搜尋之 ResNet 的修改 – F. Yang et al.，2017</span><span class="sxs-lookup"><span data-stu-id="22644-185">*Figure 4: Modifications to ResNet for Visual Search – F. Yang et al., 2017*</span></span>

<span data-ttu-id="22644-186">不論您是選擇預先定型的模型或開發您自己的模型，您仍必須決定要在哪執行模型本身的特色化和/ 或定型。</span><span class="sxs-lookup"><span data-stu-id="22644-186">Whether you choose pre-trained models or to develop your own, you will still need to decide where to run the featurization and/or training of the model itself.</span></span>

<span data-ttu-id="22644-187">Azure 提供數個選項：VM、Azure Batch、[Batch AI](https://azure.microsoft.com/services/batch-ai/?WT.mc_id=vsearchgio-article-gmarchet)、Databricks 叢集。</span><span class="sxs-lookup"><span data-stu-id="22644-187">Azure offers several options: VMs, Azure Batch, [Batch AI](https://azure.microsoft.com/services/batch-ai/?WT.mc_id=vsearchgio-article-gmarchet), Databricks clusters.</span></span> <span data-ttu-id="22644-188">不過，在所有案例中，最佳價格/效能是以 GPU 使用率來給定。</span><span class="sxs-lookup"><span data-stu-id="22644-188">In all cases, however, the best price/performance is given by the use of GPUs.</span></span>

<span data-ttu-id="22644-189">Microsoft 最近也宣布推出 FPGA，讓您能以 GPU 成本的數分之一快速執行計算 ([Brainwave](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/?WT.mc_id=vsearchgio-article-gmarchet) 專案)。</span><span class="sxs-lookup"><span data-stu-id="22644-189">Microsoft has also recently announced the availability of FPGAs for fast computation at a fraction of the GPU cost (project [Brainwave](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/?WT.mc_id=vsearchgio-article-gmarchet)).</span></span> <span data-ttu-id="22644-190">不過，在本文撰寫時，此供應項目受限於特定網路架構，因此您必須密切評估其效能。</span><span class="sxs-lookup"><span data-stu-id="22644-190">However, at the time of writing, this offering is limited to certain network architectures, so you will need to evaluate their performance closely.</span></span>

### <a name="similarity-measure-or-distance"></a><span data-ttu-id="22644-191">相似度度量或距離</span><span class="sxs-lookup"><span data-stu-id="22644-191">Similarity Measure or Distance</span></span>

<span data-ttu-id="22644-192">當影像在特色向量空間中呈現時，尋找相似度就成為定義此類空間中各點距離度量的問題。</span><span class="sxs-lookup"><span data-stu-id="22644-192">When the images are represented in the feature vector space, finding similarities becomes a question of defining a distance measure between points in such space.</span></span> <span data-ttu-id="22644-193">一旦定義距離，您就可以計算類似影像叢集和/或定義相似度矩陣。</span><span class="sxs-lookup"><span data-stu-id="22644-193">Once a distance is defined, you can compute clusters of similar images and/or define similarity matrices.</span></span> <span data-ttu-id="22644-194">視選取的距離度量而定，結果可能會不同。</span><span class="sxs-lookup"><span data-stu-id="22644-194">Depending on the distance metric selected, the results may vary.</span></span> <span data-ttu-id="22644-195">例如，最常見的歐幾里德距離會度量實數向量，這很容易了解：它會擷取距離的大小量。</span><span class="sxs-lookup"><span data-stu-id="22644-195">The most common Euclidean distance measure over real-number vectors, for instance, is easy to understand: it captures the magnitude of the distance.</span></span> <span data-ttu-id="22644-196">不過，從計算的觀點來看，它很沒有效率。</span><span class="sxs-lookup"><span data-stu-id="22644-196">However, it is rather inefficient in terms of computation.</span></span>

<span data-ttu-id="22644-197">[餘弦](https://en.wikipedia.org/wiki/Cosine_similarity)距離通常用來擷取向量的方向，而不是其大小量。</span><span class="sxs-lookup"><span data-stu-id="22644-197">[Cosine](https://en.wikipedia.org/wiki/Cosine_similarity) distance is often used to capture the orientation of the vector, rather than its magnitude.</span></span>

<span data-ttu-id="22644-198">其他替代項目，例如以二進位表示法表示的 [Hamming](https://en.wikipedia.org/wiki/Hamming_distance) 距離精確度比較沒有那麼高，但效率比較好且速度比較快。</span><span class="sxs-lookup"><span data-stu-id="22644-198">Alternatives such as [Hamming](https://en.wikipedia.org/wiki/Hamming_distance) distance over binary representations trade some accuracy for efficiency and speed.</span></span>

<span data-ttu-id="22644-199">向量大小與距離度量的組合將決定搜尋所需的計算資源與記憶體資源需求為何。</span><span class="sxs-lookup"><span data-stu-id="22644-199">The combination of vector size and distance measure will determine how computationally intensive and memory intensive the search will be.</span></span>

### <a name="search-amp-ranking"></a><span data-ttu-id="22644-200">搜尋與排名</span><span class="sxs-lookup"><span data-stu-id="22644-200">Search &amp; Ranking</span></span>

<span data-ttu-id="22644-201">一旦定義相似度，我們就必須設計不同的有效率方法來擷取最接近傳遞為輸入之項目的 N 個項目，然後傳回識別碼清單。</span><span class="sxs-lookup"><span data-stu-id="22644-201">Once similarity is defined, we need to devise an efficient method to retrieve the closest N items to the one passed as input, then return a list of identifiers.</span></span> <span data-ttu-id="22644-202">這也稱為「影像排名」。</span><span class="sxs-lookup"><span data-stu-id="22644-202">This is also known as "image ranking".</span></span> <span data-ttu-id="22644-203">在大型資料集上，計算每個距離的時間通常很長，因此我們使用近似最接近像素演算法。</span><span class="sxs-lookup"><span data-stu-id="22644-203">On a large data set, the time to compute every distance is prohibitive, so we use approximate nearest-neighbor algorithms.</span></span> <span data-ttu-id="22644-204">針對那些用途，有數個開放原始碼程式庫可用，因此您不需要從頭開始撰寫程式碼。</span><span class="sxs-lookup"><span data-stu-id="22644-204">Several open source libraries exist for those, so you won't have to code them from scratch.</span></span>

<span data-ttu-id="22644-205">最後，記憶體與計算需求將決定已定型模型的部署技術，以及高可用性。</span><span class="sxs-lookup"><span data-stu-id="22644-205">Finally, memory and computation requirements will determine the choice of deployment technology for the trained model, as well high availability.</span></span> <span data-ttu-id="22644-206">一般而言，搜尋空間將會分割，而且數個排名演算法執行個體將會同時執行。</span><span class="sxs-lookup"><span data-stu-id="22644-206">Typically, the search space will be partitioned, and several instances of the ranking algorithm will run in parallel.</span></span> <span data-ttu-id="22644-207">允許延展性與可用性的其中一個選項是 [Azure Kubernetes](https://azure.microsoft.com/services/container-service/kubernetes/?WT.mc_id=vsearchgio-article-gmarchet) 叢集。</span><span class="sxs-lookup"><span data-stu-id="22644-207">One option that allows for scalability and availability is [Azure Kubernetes](https://azure.microsoft.com/services/container-service/kubernetes/?WT.mc_id=vsearchgio-article-gmarchet) clusters.</span></span> <span data-ttu-id="22644-208">在該案例中，建議您跨數個容器 (每個容器都負責處理一個搜尋空間分割區) 與數個節點 (以達成高可用性) 部署排名模型。</span><span class="sxs-lookup"><span data-stu-id="22644-208">In that case it is advisable to deploy the ranking model across several containers (handling a partition of the search space each) and several nodes (for high availability).</span></span>

## <a name="next-steps"></a><span data-ttu-id="22644-209">後續步驟</span><span class="sxs-lookup"><span data-stu-id="22644-209">Next steps</span></span>

<span data-ttu-id="22644-210">實作圖像式搜尋不見得很複雜。</span><span class="sxs-lookup"><span data-stu-id="22644-210">Implementing visual search need not be complex.</span></span> <span data-ttu-id="22644-211">您可以使用 Bing 或使用 Azure 建置您自己的解決方案，同時獲益於 Microsoft 的 AI 研究與工具。</span><span class="sxs-lookup"><span data-stu-id="22644-211">You can use Bing or build your own with Azure services, while benefiting from Microsoft's AI research and tools.</span></span>

### <a name="trial"></a><span data-ttu-id="22644-212">試用版</span><span class="sxs-lookup"><span data-stu-id="22644-212">Trial</span></span>

- <span data-ttu-id="22644-213">試用[圖像式搜尋 API 測試主控台](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="22644-213">Try out the [Visual Search API Testing Console](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac)</span></span>

### <a name="develop"></a><span data-ttu-id="22644-214">開發</span><span class="sxs-lookup"><span data-stu-id="22644-214">Develop</span></span>

- <span data-ttu-id="22644-215">若要開始建立自訂服務，請參閱 [Bing 圖像式搜尋 API 概觀](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview/?WT.mc_id=vsearchgio-article-gmarchet)</span><span class="sxs-lookup"><span data-stu-id="22644-215">To begin creating a customized service, see [Bing Visual Search API Overview](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview/?WT.mc_id=vsearchgio-article-gmarchet)</span></span>
- <span data-ttu-id="22644-216">若要建立您的第一個要求，請參閱快速入門：[C#](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/csharp) | [Java](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/java) | [node.js](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/nodejs) | [Python](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/python)</span><span class="sxs-lookup"><span data-stu-id="22644-216">To create your first request, see the quickstarts: [C#](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/csharp) | [Java](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/java) | [node.js](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/nodejs) | [Python](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/python)</span></span>
- <span data-ttu-id="22644-217">熟悉[圖像式搜尋 API](https://aka.ms/bingvisualsearchreferencedoc) 參考。</span><span class="sxs-lookup"><span data-stu-id="22644-217">Familiarize yourself with the [Visual Search API Reference](https://aka.ms/bingvisualsearchreferencedoc).</span></span>

### <a name="background"></a><span data-ttu-id="22644-218">背景</span><span class="sxs-lookup"><span data-stu-id="22644-218">Background</span></span>

- <span data-ttu-id="22644-219">[深度學習影像分離](https://www.microsoft.com/developerblog/2018/04/18/deep-learning-image-segmentation-for-ecommerce-catalogue-visual-search/?WT.mc_id=vsearchgio-article-gmarchet)：Microsoft 白皮書描述從背景分離影像的程序</span><span class="sxs-lookup"><span data-stu-id="22644-219">[Deep Learning Image Segmentation](https://www.microsoft.com/developerblog/2018/04/18/deep-learning-image-segmentation-for-ecommerce-catalogue-visual-search/?WT.mc_id=vsearchgio-article-gmarchet): Microsoft paper describes the process of separating images from backgrounds</span></span>
- <span data-ttu-id="22644-220">[Ebay 圖像式搜尋](https://arxiv.org/abs/1706.03154)：康乃爾大學研究</span><span class="sxs-lookup"><span data-stu-id="22644-220">[Visual Search at Ebay](https://arxiv.org/abs/1706.03154): Cornell University research</span></span>
- <span data-ttu-id="22644-221">[Pinterest 的圖像式搜尋](https://arxiv.org/abs/1702.04680) \(英文\)：康乃爾大學研究</span><span class="sxs-lookup"><span data-stu-id="22644-221">[Visual Discovery at Pinterest](https://arxiv.org/abs/1702.04680) Cornell University research</span></span>
- <span data-ttu-id="22644-222">[S語意式雜湊處理](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)：多倫多大學研究</span><span class="sxs-lookup"><span data-stu-id="22644-222">[Semantic Hashing](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf) University of Toronto research</span></span>

<span data-ttu-id="22644-223">_此文章是由 Giovanni Marchetti 與 Mariya Zorotovich 所撰寫。_</span><span class="sxs-lookup"><span data-stu-id="22644-223">_This article was authored by Giovanni Marchetti and Mariya Zorotovich._</span></span>