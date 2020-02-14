---
title: 透過 Azure 和 R 啟用 Finserve 風險生命週期
description: ''
author: sseely
ms.author: sseely
ms.service: industry
ms.topic: overview
ms.date: 11/19/2019
ms.openlocfilehash: 03fea3996b62782c2b65e6d2edf841b5adaebcd2
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77052496"
---
# <a name="enabling-the-financial-services-risk-lifecycle-with-azure-and-r"></a><span data-ttu-id="4d07e-102">使用 Azure 與 R 啟用財務服務風險生命週期</span><span class="sxs-lookup"><span data-stu-id="4d07e-102">Enabling the financial services risk lifecycle with Azure and R</span></span>


<span data-ttu-id="4d07e-103">在關鍵金融服務作業中的生命週期中，風險計算在數個階段都非常關鍵。</span><span class="sxs-lookup"><span data-stu-id="4d07e-103">Risk calculations are pivotal at several stages in the lifecycle of key financial services operations.</span></span> <span data-ttu-id="4d07e-104">例如，保險產品管理生命週期的一個簡化形式看起來可能像下列圖表一樣。</span><span class="sxs-lookup"><span data-stu-id="4d07e-104">For example, a simplified form of the insurance product management lifecycle might look something like the diagram below.</span></span> <span data-ttu-id="4d07e-105">風險計算方面是以藍色文字顯示。</span><span class="sxs-lookup"><span data-stu-id="4d07e-105">The risk calculation aspects are shown in blue text.</span></span>

![](./assets/fsi-risk-modeling/image1.png)

<span data-ttu-id="4d07e-106">資本市場公司的案例看起來可能像這個一樣：</span><span class="sxs-lookup"><span data-stu-id="4d07e-106">A scenario in a capital markets firm might look like this:</span></span>

![](./assets/fsi-risk-modeling/image2.png)

<span data-ttu-id="4d07e-107">在這些程序中，風險模型建構的常見需求包括：</span><span class="sxs-lookup"><span data-stu-id="4d07e-107">Through these processes there are common needs around risk modelling including:</span></span>

1. <span data-ttu-id="4d07e-108">對於特設風險相關實驗 (依風險分析) 的需求；保險公司中的精算師，或資本市場公司中的分析師。</span><span class="sxs-lookup"><span data-stu-id="4d07e-108">The need for ad-hoc risk-related experimentation by risk analysts; actuaries in an insurance firm or quants in a capital markets firm.</span></span>
    <span data-ttu-id="4d07e-109">這些分析師一般都會處理程式碼與其領域中熱門的模型建構工具：R 與 Python。</span><span class="sxs-lookup"><span data-stu-id="4d07e-109">These analysts typically work with code and modelling tools popular in their domain: R and Python.</span></span> <span data-ttu-id="4d07e-110">許多大學履歷都包括數學金融與 MBA 課程中的 R 或 Python 訓練。</span><span class="sxs-lookup"><span data-stu-id="4d07e-110">Many university curriculums include training in R or Python in mathematical finance and in MBA courses.</span></span>
    <span data-ttu-id="4d07e-111">這兩個語言都提供廣泛的開放原始碼程式庫，這支援熱門的風險計算。</span><span class="sxs-lookup"><span data-stu-id="4d07e-111">Both languages offer a wide range of open source libraries which support popular risk calculations.</span></span> <span data-ttu-id="4d07e-112">搭配使用適當的工具之後，分析師通常需要存取下列項目：</span><span class="sxs-lookup"><span data-stu-id="4d07e-112">Along with appropriate tooling, analysts often require access to:</span></span>

    <span data-ttu-id="4d07e-113">a.</span><span class="sxs-lookup"><span data-stu-id="4d07e-113">a.</span></span>  <span data-ttu-id="4d07e-114">精確的市場定價資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-114">Accurate market pricing data.</span></span>

    <span data-ttu-id="4d07e-115">b.</span><span class="sxs-lookup"><span data-stu-id="4d07e-115">b.</span></span>  <span data-ttu-id="4d07e-116">現有的原則與宣告資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-116">Existing policy and claims data.</span></span>

    <span data-ttu-id="4d07e-117">c.</span><span class="sxs-lookup"><span data-stu-id="4d07e-117">c.</span></span>  <span data-ttu-id="4d07e-118">現有的市場位置資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-118">Existing market position data.</span></span>

    <span data-ttu-id="4d07e-119">d.</span><span class="sxs-lookup"><span data-stu-id="4d07e-119">d.</span></span>  <span data-ttu-id="4d07e-120">其他外部資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-120">Other external data.</span></span> <span data-ttu-id="4d07e-121">來源包括結構化資料，例如死亡率表與競爭定價資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-121">Sources include structured data such as mortality tables and competitive pricing data.</span></span> <span data-ttu-id="4d07e-122">可能也使用較少的傳統來源，例如天氣、新聞等。</span><span class="sxs-lookup"><span data-stu-id="4d07e-122">Less traditional sources such as weather, news and others may also be used.</span></span>

    <span data-ttu-id="4d07e-123">e.</span><span class="sxs-lookup"><span data-stu-id="4d07e-123">e.</span></span>  <span data-ttu-id="4d07e-124">計算功能以啟用快速的互動式資料調查。</span><span class="sxs-lookup"><span data-stu-id="4d07e-124">Computational capacity to enable quick interactive data investigations.</span></span>

2. <span data-ttu-id="4d07e-125">他們也可以使用特設機器學習演算法來定價或決定市場策略。</span><span class="sxs-lookup"><span data-stu-id="4d07e-125">They may also make use of ad-hoc machine learning algorithms for  pricing or determining market strategy.</span></span>

3. <span data-ttu-id="4d07e-126">視覺化並呈現資料以在產品規劃、交易策略與類似討論中使用的需求。</span><span class="sxs-lookup"><span data-stu-id="4d07e-126">The need to visualize and present data for use in product planning,  trading strategy, and similar discussions.</span></span>

4. <span data-ttu-id="4d07e-127">快速執行由分析師所設定的已定義模型，以進行定價、估值與市場風險。</span><span class="sxs-lookup"><span data-stu-id="4d07e-127">The rapid execution of defined models, configured by the analysts, for pricing, valuations, and market risk.</span></span> <span data-ttu-id="4d07e-128">估值使用專屬風險模型建構、市場風險工具與自訂程式碼的組合。</span><span class="sxs-lookup"><span data-stu-id="4d07e-128">The valuations use a combination of dedicated risk modelling, market risk tools, and custom code.</span></span> <span data-ttu-id="4d07e-129">會以批次方式搭配不同的每晚、每週、每月、每季與每年計算 (產生工作負載高峰) 來執行分析。</span><span class="sxs-lookup"><span data-stu-id="4d07e-129">The analysis is executed in a batch with varying nightly, weekly, monthly, quarterly, and annual calculations generating spikes in workloads.</span></span>

5. <span data-ttu-id="4d07e-130">將資料與其他企業內部風險評估整合，以取得合併的風險報告。</span><span class="sxs-lookup"><span data-stu-id="4d07e-130">The integration of data with other enterprise wide risk measures for consolidated risk reporting.</span></span> <span data-ttu-id="4d07e-131">在較大的組織中，較低層級的風險估計可以傳輸到企業風險模型建構與報告工具。</span><span class="sxs-lookup"><span data-stu-id="4d07e-131">In larger organizations lower level risk estimates may be transferred to an enterprise risk modelling and reporting tool.</span></span>

6. <span data-ttu-id="4d07e-132">必須以必要間隔以已定義格式報告結果，以符合投資人與監管需求。</span><span class="sxs-lookup"><span data-stu-id="4d07e-132">Results must be reported in a defined format at the required  interval to meet investor and regulatory requirements.</span></span>

<span data-ttu-id="4d07e-133">Microsoft 在 [Azure Marketplace](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) 中透過 Azure 服務與合作夥伴供應項目的結合來支援上面的顧慮。</span><span class="sxs-lookup"><span data-stu-id="4d07e-133">Microsoft supports the above concerns through a combination of Azure services and partner offerings in the [Azure Marketplace](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span> <span data-ttu-id="4d07e-134">在此文章中，我們顯示有關如何使用 R 執行特設實驗的範例。我們從說明如何在單一機器上執行實驗開始，接著顯示如何在 [Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely) 上執行相同的實驗，最後顯示如何在我們的模型建構中利用外部服務的優勢。</span><span class="sxs-lookup"><span data-stu-id="4d07e-134">In this article, we show practical examples of how to perform ad-hoc experimentation using R. We begin by explaining how to run the experiment on a single machine, then show how to run the same experiment on [Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely), and close by showing how to take advantage of external services in our modelling.</span></span> <span data-ttu-id="4d07e-135">在 Azure 上執行已定義模型時的選項與考量項目可以在著重在[銀行](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)與[保險](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)的這些文章中找到。</span><span class="sxs-lookup"><span data-stu-id="4d07e-135">Options and considerations for the execution of defined models on Azure are described in these articles focused on [banking](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely) and [insurance](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span>

## <a name="analyst-modelling-in-r"></a><span data-ttu-id="4d07e-136">R 中的分析師模型建構</span><span class="sxs-lookup"><span data-stu-id="4d07e-136">Analyst Modelling in R</span></span>

<span data-ttu-id="4d07e-137">讓我們從查看分析師如何在簡化的代表性資本市場案例中使用 R 開始。</span><span class="sxs-lookup"><span data-stu-id="4d07e-137">Let's start by looking at how R may be used by an analyst in a simplified, representative capital markets scenario.</span></span> <span data-ttu-id="4d07e-138">您可以透過針對計算參考現有的 R 程式庫或從頭開始撰寫程式碼來建置。</span><span class="sxs-lookup"><span data-stu-id="4d07e-138">You can build this either by referencing an existing R library for the calculation or by writing code from scratch.</span></span> <span data-ttu-id="4d07e-139">在我們的案例中，我們也必須擷取外部定價資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-139">In our example, we also must fetch external pricing data.</span></span> <span data-ttu-id="4d07e-140">為讓範例維持簡單但仍具示範功能，我們計算股票遠期合約的潛在未來曝險 (PFE)。</span><span class="sxs-lookup"><span data-stu-id="4d07e-140">To keep the example simple but illustrative, we calculate the potential future exposure (PFE) of an equity stock forward contract.</span></span>
<span data-ttu-id="4d07e-141">此範例針對複雜導數的檢測避免複雜量化模型建構技術，並著重在單一風險因素以專注在風險生命週期。</span><span class="sxs-lookup"><span data-stu-id="4d07e-141">This example avoids complex quantitative modelling techniques for instruments like complex derivatives and focuses on a single risk factor to concentrate on the risk life cycle.</span></span> <span data-ttu-id="4d07e-142">我們的範例會執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="4d07e-142">Our example does the following:</span></span>

1. <span data-ttu-id="4d07e-143">選取感興趣的檢測。</span><span class="sxs-lookup"><span data-stu-id="4d07e-143">Select an instrument of interest.</span></span>

2. <span data-ttu-id="4d07e-144">檢測的來源歷史價格。</span><span class="sxs-lookup"><span data-stu-id="4d07e-144">Source historic prices for the instrument.</span></span>

3. <span data-ttu-id="4d07e-145">透過簡單的蒙地卡羅 (MC) 計算 (它使用幾何布朗運動 (GBM)) 來建構權益價格模型：</span><span class="sxs-lookup"><span data-stu-id="4d07e-145">Model equity price by simple Monte Carlo (MC) calculation, which  uses Geometric Brownian Motion (GBM):</span></span>

    <span data-ttu-id="4d07e-146">a.</span><span class="sxs-lookup"><span data-stu-id="4d07e-146">a.</span></span>  <span data-ttu-id="4d07e-147">預估預期的報酬 μ (mu) 與揮發性 σ (theta)。</span><span class="sxs-lookup"><span data-stu-id="4d07e-147">Estimate expected return μ (mu) and volatility σ (theta).</span></span>

    <span data-ttu-id="4d07e-148">b.</span><span class="sxs-lookup"><span data-stu-id="4d07e-148">b.</span></span>  <span data-ttu-id="4d07e-149">校準模型至歷史資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-149">Calibrate the model to historic data.</span></span>

4. <span data-ttu-id="4d07e-150">視覺化傳達結果的各種路徑。</span><span class="sxs-lookup"><span data-stu-id="4d07e-150">Visualize the various paths to communicate the results.</span></span>

5. <span data-ttu-id="4d07e-151">繪製 max(0,Stock Value) 以示範 PFE 的意義、風險價值 (VaR) 的差異</span><span class="sxs-lookup"><span data-stu-id="4d07e-151">Plot max(0,Stock Value) to demonstrate the meaning of PFE, the  difference to Value at Risk (VaR)</span></span>

    <span data-ttu-id="4d07e-152">a.</span><span class="sxs-lookup"><span data-stu-id="4d07e-152">a.</span></span>  <span data-ttu-id="4d07e-153">釐清：PFE = Share Price (T) -- 遠期合約價格 K</span><span class="sxs-lookup"><span data-stu-id="4d07e-153">To clarify: PFE = Share Price (T) -- Forward Contract Price K</span></span>

6. <span data-ttu-id="4d07e-154">取 0.95 百分位數以在每個時間步驟/模擬期間結尾取得 PFE 值</span><span class="sxs-lookup"><span data-stu-id="4d07e-154">Take the 0.95 Quantile to get the PFE value at each time step / end  of simulation period</span></span>

<span data-ttu-id="4d07e-155">我們將以 MSFT 股票為基礎計算權益遠期的潛在未來曝險。</span><span class="sxs-lookup"><span data-stu-id="4d07e-155">We will calculate the potential future exposure for an equity forward based on MSFT stock.</span></span> <span data-ttu-id="4d07e-156">如前面所述，若要建構股票價格模型，需要 MSFT 股票的歷史價格，以便我們可以校準模型至歷史資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-156">As mentioned above, to model the stock prices, historic prices for the MSFT stock are required so we can calibrate the model to historical data.</span></span> <span data-ttu-id="4d07e-157">有許多方式可以取得歷史股票價格。</span><span class="sxs-lookup"><span data-stu-id="4d07e-157">There are many ways to acquire historical stock prices.</span></span> <span data-ttu-id="4d07e-158">在我們的範例中，我們使用外部服務提供者 [Quandl](https://www.quandl.com/) 提供的免費股票價格服務。</span><span class="sxs-lookup"><span data-stu-id="4d07e-158">In our example, we use a free version of a stock price service from an external service provider, [Quandl](https://www.quandl.com/).</span></span>


> <span data-ttu-id="4d07e-159">注意：範例使用 [WIKI 價格資料集](https://www.quandl.com/databases/WIKIP)，這可用於學習概念。</span><span class="sxs-lookup"><span data-stu-id="4d07e-159">Note: The example uses the [WIKI Prices dataset](https://www.quandl.com/databases/WIKIP) which can be used for learning concepts.</span></span> <span data-ttu-id="4d07e-160">針對美國型權益的產品使用，Quandl 建議使用 [每日收盤美國股票價格資料集](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices)。</span><span class="sxs-lookup"><span data-stu-id="4d07e-160">For production usage of US based equities, Quandl recommends using the [End of Day US Stock Prices dataset](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices).</span></span>

<span data-ttu-id="4d07e-161">若要處理資料並定義與權益關聯的風險，我們必須執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="4d07e-161">To process the data and define the risk associated with the equity, we need to do the following things:</span></span>

1. <span data-ttu-id="4d07e-162">從權益擷取歷史資料。</span><span class="sxs-lookup"><span data-stu-id="4d07e-162">Retrieve history data from the equity.</span></span>

2. <span data-ttu-id="4d07e-163">從歷史資料判斷預期報酬 μ 與揮發性 σ。</span><span class="sxs-lookup"><span data-stu-id="4d07e-163">Determine the expected return μ and volatility σ from the historic  data.</span></span>

3. <span data-ttu-id="4d07e-164">使用一些模擬方式，建構底層股票價格的模型。</span><span class="sxs-lookup"><span data-stu-id="4d07e-164">Model the underlying stock prices using some simulation.</span></span>

4. <span data-ttu-id="4d07e-165">執行模型</span><span class="sxs-lookup"><span data-stu-id="4d07e-165">Run the model</span></span>

5. <span data-ttu-id="4d07e-166">判斷未來權益的曝險。</span><span class="sxs-lookup"><span data-stu-id="4d07e-166">Determine the exposure of the equity in the future.</span></span>

<span data-ttu-id="4d07e-167">我們一開始先從 Quandl 服務擷取股票，並繪製過去 180 天的收盤價格歷史。</span><span class="sxs-lookup"><span data-stu-id="4d07e-167">We start by retrieving the stock from the Quandl service and plotting the closing price history over the last 180 days.</span></span>

````R
# Lubridate package must be installed
if (!require(lubridate)) install.packages('lubridate')
library(lubridate)

# Quandl package must be installed
if (!require(Quandl)) install.packages('Quandl')
library(Quandl)

# Get your API key from quandl.com
quandl_api = "enter your key here"

# Add the key to the Quandl keychain
Quandl.api_key(quandl_api)

quandl_get <-
    function(sym, start_date = "2018-01-01") {
        require(devtools)
        require(Quandl)
        # Retrieve the Open, High, Low, Close and Volume Column for a given Symbol
        # Column Indices can be deduced from this sample call
        # data <- Quandl(c("WIKI/MSFT"), rows = 1)

        tryCatch(Quandl(c(
        paste0("WIKI/", sym, ".8"),    # Column 8 : Open
        paste0("WIKI/", sym, ".9"),    # Column 9 : High
        paste0("WIKI/", sym, ".10"),   # Column 10: Low
        paste0("WIKI/", sym, ".11"),   # Column 11: Close
        paste0("WIKI/", sym, ".12")),  # Column 12: Volume
        start_date = start_date,
        type = "raw"
        ))
    }

# Define the Equity Forward
instrument.name <- "MSFT"
instrument.premium <- 100.1
instrument.pfeQuantile <- 0.95

# Get the stock price for the last 180 days
instrument.startDate <- today() - days(180)

# Get the quotes for an equity and transform them to a data frame
df_instrument.timeSeries <- quandl_get(instrument.name,start_date = instrument.startDate)

#Rename the columns
colnames(df_instrument.timeSeries) <- c()
colnames(df_instrument.timeSeries) <- c("Date","Open","High","Low","Close","Volume")

# Plot the closing price history to get a better feeling for the data
plot(df_instrument.timeSeries$Date, df_instrument.timeSeries$Close)
````

<span data-ttu-id="4d07e-168">擁有該資料之後，我們便開始校準 GBM 模型。</span><span class="sxs-lookup"><span data-stu-id="4d07e-168">With the data in hand, we calibrate the GBM model.</span></span>

````R
# Code inspired by the book Computational Finance, An Introductory Course with R by 
#    A. Arratia.

# Calculate the daily return in order to estimate sigma and mu in the Wiener Process
df_instrument.dailyReturns <- c(diff(log(df_instrument.timeSeries$Close)), NA)

# Estimate the mean of std deviation of the log returns to estimate the parameters of the Wiener Process

estimateGBM_Parameters <- function(logReturns,dt = 1/252) {

    # Volatility
    sigma_hat = sqrt(var(logReturns)) / sqrt(dt)

    # Drift
    mu_hat = mean(logReturns) / dt + sigma_hat**2 / 2.0

    # Return the parameters
    parameter.list <- list("mu" = mu_hat,"sigma" = sigma_hat)

    return(parameter.list)
}

# Calibrate the model to historic data
GBM_Parameters <- estimateGBM_Parameters(df_instrument.dailyReturns[1:length(df_instrument.dailyReturns) - 1])
````

<span data-ttu-id="4d07e-169">接著，我們建構底層股票價格的模型。</span><span class="sxs-lookup"><span data-stu-id="4d07e-169">Next, we model the underlying stock prices.</span></span> <span data-ttu-id="4d07e-170">我們可以從頭開始實作各自的 GBM 程序，或利用提供此功能的許多 R 套件的其中一個。</span><span class="sxs-lookup"><span data-stu-id="4d07e-170">We can either implement the discrete GBM process from scratch or utilize one of many R packages which provide this functionality.</span></span> <span data-ttu-id="4d07e-171">我們使用 R 套件 [*sde* (隨機微分方程式的模擬與推斷)](https://cran.r-project.org/web/packages/sde/index.html)，它提供一個解決此問題的方法。</span><span class="sxs-lookup"><span data-stu-id="4d07e-171">We use the R package [*sde* (Simulation and Inference for Stochastic Differential Equations)](https://cran.r-project.org/web/packages/sde/index.html) which provides a method of solving this problem.</span></span> <span data-ttu-id="4d07e-172">GBM 方法需要一組參數，它們被校準為歷史資料，或提供為模擬參數。</span><span class="sxs-lookup"><span data-stu-id="4d07e-172">The GBM method requires a set of parameters which are either calibrated to historic data or given as simulation parameters.</span></span> <span data-ttu-id="4d07e-173">我們使用歷史資料，在模擬 (P0) 開始處提供 μ、σ 與股票價格。</span><span class="sxs-lookup"><span data-stu-id="4d07e-173">We use the historic data, providing μ, σ and the stock prices at the beginning of the simulation (P0).</span></span>

````R
if (!require(sde)) install.packages('sde')
library(sde)

sigma <-  GBM_Parameters$sigma
mu <- GBM_Parameters$mu
P0 <- tail(df_instrument.timeSeries$Close, 1)

# Calculate the PFE looking one month into the future
T <- 1 / 12

# Consider nt MC paths
nt=50

# Divide the time interval T into n discrete time steps
n = 2 ^ 8 

dt <- T / n
t <- seq(0,T,by=dt)
````

<span data-ttu-id="4d07e-174">我們現在可以開始蒙地卡羅模擬以針對一些模擬路徑建構潛在曝險模型。</span><span class="sxs-lookup"><span data-stu-id="4d07e-174">We are now ready to start a Monte Carlo simulation to model the potential exposure for some number of simulation paths.</span></span> <span data-ttu-id="4d07e-175">我們將模擬限制為 50 個蒙地卡羅路徑與 256 個時間步驟。</span><span class="sxs-lookup"><span data-stu-id="4d07e-175">We will limit the simulation to 50 Monte Carlo paths and 256 time steps.</span></span> <span data-ttu-id="4d07e-176">為準備相應放大模擬並發揮 R 中的平行處理優勢， 蒙地卡羅模擬迴圈使用 foreach 陳述式。</span><span class="sxs-lookup"><span data-stu-id="4d07e-176">In preparation for scaling out the simulation and taking advantage of parallelization in R, the Monte Carlo simulation loop uses a foreach statement.</span></span>

````R
# Track the start time of the simulation
start_s <- Sys.time()

# Instead of a simple for loop to execute a simulation per MC path, call the
# simulation with the foreach package
# in order to demonstrate the similarity to the AzureBatch way to call the method.

library(foreach)
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package
exposure_mc <- foreach (i=1:nt, .combine = rbind ) %do%  GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()

# Track the end time of the simulation
end_s <- Sys.time()

# Duration of the simulation

difftime(end_s, start_s) 
````

<span data-ttu-id="4d07e-177">我們現在已經模擬底層 MSFT 股票的價格。</span><span class="sxs-lookup"><span data-stu-id="4d07e-177">We have now simulated the price of the underlying MSFT stock.</span></span> <span data-ttu-id="4d07e-178">為計算權益遠期的曝險，我們減去溢價並將曝險限制為僅正值。</span><span class="sxs-lookup"><span data-stu-id="4d07e-178">To calculate the exposure of the equity forward, we subtract the premium and limit the exposure to only positive values.</span></span>

````R
# Calculate the total Exposure as V_i(t) - K, put it to zero for negative exposures
pfe_mc <- pmax(exposure_mc - instrument.premium ,0)

ymax <- max(pfe_mc)
ymin <- min(pfe_mc)
plot(t, pfe_mc[1,], t = 'l', ylim = c(ymin, ymax), col = 1, ylab="Credit Exposure in USD", xlab="time t in Years")
for (i in 2:nt) {
    lines(t, pfe_mc[i,], t = 'l', ylim = c(ymin, ymax), col = i)
}
````

<span data-ttu-id="4d07e-179">下兩張凸顯示模擬的結果。</span><span class="sxs-lookup"><span data-stu-id="4d07e-179">The next two pictures show the result of the simulation.</span></span> <span data-ttu-id="4d07e-180">第一張圖顯示底層股票價格的蒙地卡羅模擬 (針對 50 個路徑)。</span><span class="sxs-lookup"><span data-stu-id="4d07e-180">The first picture shows the Monte Carlo simulation of the underlying stock price for 50 paths.</span></span> <span data-ttu-id="4d07e-181">第二張圖示範將溢價從權益遠期減去之後，權益遠期的底層「信用曝險」。</span><span class="sxs-lookup"><span data-stu-id="4d07e-181">The second picture illustrates the underlying Credit Exposure for the equity forward after subtracting the premium of the equity forward and limiting the exposure to positive values.</span></span>


|       |    |
|---    |--- |
| <img src="./assets/fsi-risk-modeling/image3.png" width="400px" alt="Figure 1 - 50 Monte Carlo Paths"/> | <img src="./assets/fsi-risk-modeling/image4.png" width="400px" alt="Figure 2 - Credit Exposure for Equity Forward"/> |
| <span data-ttu-id="4d07e-182">圖 1 - 50 個蒙地卡羅路徑</span><span class="sxs-lookup"><span data-stu-id="4d07e-182">Figure 1 - 50 Monte Carlo Paths</span></span> | <span data-ttu-id="4d07e-183">圖 2 - 權益遠期的信用曝險</span><span class="sxs-lookup"><span data-stu-id="4d07e-183">Figure 2 - Credit Exposure for Equity Forward</span></span> |


<span data-ttu-id="4d07e-184">在最後一個步驟中，1 個月 0.95 百分位數 PFE 是由下列程式碼所計算。</span><span class="sxs-lookup"><span data-stu-id="4d07e-184">In the last step, the 1-Month 0.95 Quantile PFE is calculated by the following code.</span></span>

````R
# Calculate the PFE at each time step
df_pfe <- cbind(t,apply(pfe_mc,2,quantile,probs = instrument.pfeQuantile ))

resulting in the final PFE plot
plot(df_pfe, t = 'l', ylab = "Potential Future Exposure in USD", xlab = "time t in Years")
````

<img src="./assets/fsi-risk-modeling/image5.png" width="500px" alt="Potential Future Exposure for MSFT Equity Forward" /> 

<span data-ttu-id="4d07e-185">圖 3 MSFT 權益遠期的潛在遠期曝險</span><span class="sxs-lookup"><span data-stu-id="4d07e-185">Figure 3 Potential Future Exposure for MSFT Equity Forward</span></span>

## <a name="using-azure-batch-with-r"></a><span data-ttu-id="4d07e-186">搭配 R 使用 Azure Batch</span><span class="sxs-lookup"><span data-stu-id="4d07e-186">Using Azure Batch with R</span></span> 

<span data-ttu-id="4d07e-187">上面所述的 R 解決方案可以連結到 Azure Batch 並利用雲端來進行風險計算。</span><span class="sxs-lookup"><span data-stu-id="4d07e-187">The R solution described above can be connected to Azure Batch and leverage the cloud for risk calculations.</span></span> <span data-ttu-id="4d07e-188">針對平行計算 (例如我們的範例)，這需要多一點心力。</span><span class="sxs-lookup"><span data-stu-id="4d07e-188">This takes little extra effort for a parallel calculation such as ours.</span></span> <span data-ttu-id="4d07e-189">[使用 Azure Batch 執行平行 R 模擬](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)教學課程提供有關如何將 R 連線到 Azure Batch 的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="4d07e-189">The tutorial, [Run a parallel R simulation with Azure Batch](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely), provides detailed information on connecting R to Azure Batch.</span></span> <span data-ttu-id="4d07e-190">我們在下面顯示連線到 Azure Batch 之程序的程式碼與摘要，以及如何在簡化的 PFE 計算中發揮雲端延伸模組的優勢。</span><span class="sxs-lookup"><span data-stu-id="4d07e-190">Below we show the code and summary of the process to connect to Azure Batch and how to take advantage of the extension to the cloud in a simplified PFE calculation.</span></span>

<span data-ttu-id="4d07e-191">此範例處理稍早所述的一些模型。</span><span class="sxs-lookup"><span data-stu-id="4d07e-191">This example tackles the same model described earlier.</span></span> <span data-ttu-id="4d07e-192">如果我們先前所看見的，此計算可以在我們的個人電腦上執行。</span><span class="sxs-lookup"><span data-stu-id="4d07e-192">As we have seen before, this calculation can run on our personal computer.</span></span> <span data-ttu-id="4d07e-193">增加蒙地卡羅路徑數目或使用較小的時間步驟將會導致長許多的執行時間。</span><span class="sxs-lookup"><span data-stu-id="4d07e-193">Increases to the number of Monte Carlo paths or use of smaller time steps will result in much longer execution times.</span></span> <span data-ttu-id="4d07e-194">幾乎所有 R 程式碼都不需變更。</span><span class="sxs-lookup"><span data-stu-id="4d07e-194">Almost all of the R code will remain unchanged.</span></span> <span data-ttu-id="4d07e-195">我們將在此節中反白顯示差異處。</span><span class="sxs-lookup"><span data-stu-id="4d07e-195">We will highlight the differences in this section.</span></span>

<span data-ttu-id="4d07e-196">在 Azure 中執行之每個蒙地卡羅模擬的路徑。</span><span class="sxs-lookup"><span data-stu-id="4d07e-196">Each path of the Monte Carlo simulation runs in Azure.</span></span> <span data-ttu-id="4d07e-197">我們可以這樣做，因為每個路徑都與彼此獨立，這讓我們可以進行「使人窘迫的平行」計算。</span><span class="sxs-lookup"><span data-stu-id="4d07e-197">We can do this because each path is independent of the others, giving us an "embarrassingly parallel" calculation.</span></span>

<span data-ttu-id="4d07e-198">為使用 Azure Batch，我們定義底層叢集並在程式碼中參考它，接著叢集才能在計算中使用。</span><span class="sxs-lookup"><span data-stu-id="4d07e-198">To use Azure Batch, we define the underlying cluster and reference it in the code before the cluster can be used in the calculations.</span></span> <span data-ttu-id="4d07e-199">為執行計算，我們使用下列 cluster.json 定義：</span><span class="sxs-lookup"><span data-stu-id="4d07e-199">To run the calculations, we use the following cluster.json definition:</span></span>

````JavaScript
{
  "name": "myMCPool",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 4,
  "poolSize": {
    "dedicatedNodes": {
      "min": 1,
      "max": 1
    },
    "lowPriorityNodes": {
      "min": 3,
      "max": 3
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": [],
  "subnetId": ""
}
````

<span data-ttu-id="4d07e-200">使用此叢集定義時，下列 R 程式碼會利用叢集：</span><span class="sxs-lookup"><span data-stu-id="4d07e-200">With this cluster definition, the following R code makes use of the cluster:</span></span>
````R
# Define the cloud burst environment
library(doAzureParallel)

# set your credentials
setCredentials("credentials.json")

# Create your cluster if not exist
cluster <- makeCluster("cluster.json")

# register your parallel backend
registerDoAzureParallel(cluster)

# check that your workers are up
getDoParWorkers()
````

<span data-ttu-id="4d07e-201">最後，我們更新稍早的 foreach 陳述式以使用 doAzureParallel 套件。</span><span class="sxs-lookup"><span data-stu-id="4d07e-201">Finally, we update the foreach statement from earlier to use the doAzureParallel package.</span></span> <span data-ttu-id="4d07e-202">這是輕微的變更，加入了對 sde 套件的參考，並將 %do% 變更為 %dopar%：</span><span class="sxs-lookup"><span data-stu-id="4d07e-202">It's a minor change, adding a reference to the sde package and changing the %do% to %dopar%:</span></span>

````R
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package and extend the computation to the cloud
exposure_mc <- foreach(i = 1:nt, .combine = rbind, .packages = 'sde') %dopar% GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()
````

<span data-ttu-id="4d07e-203">每個蒙地卡羅模擬都是以工作形式提交到 Azure Batch。</span><span class="sxs-lookup"><span data-stu-id="4d07e-203">Each Monte Carlo simulation is submitted as a task to Azure Batch.</span></span> <span data-ttu-id="4d07e-204">該工作在雲端中執行。</span><span class="sxs-lookup"><span data-stu-id="4d07e-204">The task executes in the cloud.</span></span> <span data-ttu-id="4d07e-205">結果在傳回給分析師工作台之前會先合併。</span><span class="sxs-lookup"><span data-stu-id="4d07e-205">Results are merged before being sent back to the analyst workbench.</span></span> <span data-ttu-id="4d07e-206">舉重與計算在雲端中執行，以發揮規模調整與底層基礎結構的完整優勢 (要求的計算所需要的)。</span><span class="sxs-lookup"><span data-stu-id="4d07e-206">The heavy lifting and computations execute in the cloud to take full advantage of scaling and the underlying infrastructure required by the requested calculations.</span></span>

<span data-ttu-id="4d07e-207">在完成計算之後，可以透過叫用下列單一指示以輕鬆關閉額外的資源：</span><span class="sxs-lookup"><span data-stu-id="4d07e-207">After the calculations have finished, the additional resources can easily be shut-down by invoking the following a single instruction:</span></span>

````R
# Stop the Cloud cluster
stopCluster(cluster)
````


## <a name="use-a-saas-offering"></a><span data-ttu-id="4d07e-208">使用 SaaS 供應項目</span><span class="sxs-lookup"><span data-stu-id="4d07e-208">Use a SaaS offering</span></span>

<span data-ttu-id="4d07e-209">前兩個範例顯示如何利用本機與雲端基礎結構來開發適當的估值模型。</span><span class="sxs-lookup"><span data-stu-id="4d07e-209">The first two examples show how to utilize local and cloud infrastructure for developing an adequate valuation model.</span></span> <span data-ttu-id="4d07e-210">此範例已經開始變更。</span><span class="sxs-lookup"><span data-stu-id="4d07e-210">This paradigm has begun to shift.</span></span> <span data-ttu-id="4d07e-211">與內部部署基礎結構轉換為雲端式 IaaS 與 PaaS 服務的方式相同，相關風險數據的模型建構會轉換為服務導向程序。</span><span class="sxs-lookup"><span data-stu-id="4d07e-211">In the same way that on-premises infrastructure has transformed into cloud-based IaaS and PaaS services, the modelling of relevant risk figures is transforming into a service-oriented process.</span></span>
<span data-ttu-id="4d07e-212">今天的分析師面臨兩個主要挑戰：</span><span class="sxs-lookup"><span data-stu-id="4d07e-212">Today's analysts face two major challenges:</span></span>

1. <span data-ttu-id="4d07e-213">法規需求使用逐漸增加的計算功能來新增到模型建構需求。</span><span class="sxs-lookup"><span data-stu-id="4d07e-213">The regulatory requirements use increasing compute capacity to add to modeling requirements.</span></span> <span data-ttu-id="4d07e-214">監管者要求更頻繁且更新的風險數據。</span><span class="sxs-lookup"><span data-stu-id="4d07e-214">The regulators are asking for more frequent and up-to date risk figures.</span></span>

2.  <span data-ttu-id="4d07e-215">現有的風險基礎結構已隨著時間有機地成長，而且在以靈活方式實作新的需求與更進階的風險模型建構時會產生挑戰。</span><span class="sxs-lookup"><span data-stu-id="4d07e-215">The existing risk infrastructure has grown organically with time and creates challenges when implementing new requirements and more advanced risk modeling in an agile manner.</span></span>

<span data-ttu-id="4d07e-216">雲端式服務可提供必要的功能並支援風險分析。</span><span class="sxs-lookup"><span data-stu-id="4d07e-216">Cloud-based services can deliver the required functionality and support risk analysis.</span></span> <span data-ttu-id="4d07e-217">此方法有一些優點：</span><span class="sxs-lookup"><span data-stu-id="4d07e-217">This approach has some advantages:</span></span>

-  <span data-ttu-id="4d07e-218">監管者所要求的最常見風險計算必須在法規下由每個人實作。</span><span class="sxs-lookup"><span data-stu-id="4d07e-218">The most common risk calculations required by the regulator must be implemented by everyone under the regulation.</span></span> <span data-ttu-id="4d07e-219">透過使用來自特殊化服務提供者的服務，分析師可從能隨時使用、符合監管者規範的風險計算而獲益。</span><span class="sxs-lookup"><span data-stu-id="4d07e-219">By utilizing services from a specialized service provider, the analyst benefits from ready to use, regulator-compliant risk calculations.</span></span> <span data-ttu-id="4d07e-220">此類服務可能包括市場風險計算、對手風險計算、X 值調整 (XVA)，甚至是交易簿基本檢閱 (FRTB) 計算。</span><span class="sxs-lookup"><span data-stu-id="4d07e-220">Such services may include market risk calculations, counterparty risk calculations, X-Value Adjustment (XVA), and even Fundamental Review of Trading Book (FRTB) calculations.</span></span>

- <span data-ttu-id="4d07e-221">這些服務透過 Web 服務公開其介面。</span><span class="sxs-lookup"><span data-stu-id="4d07e-221">These services expose their interfaces through web services.</span></span> <span data-ttu-id="4d07e-222">現有的風險基礎結構可透過這些其他服務來加強。</span><span class="sxs-lookup"><span data-stu-id="4d07e-222">The existing risk infrastructure can be enhanced by these other services.</span></span>

<span data-ttu-id="4d07e-223">在我們的範例中，我們想要針對 FRTB 計算叫用雲端式服務。</span><span class="sxs-lookup"><span data-stu-id="4d07e-223">In our example, we want to invoke a cloud-based service for FRTB calculations.</span></span> <span data-ttu-id="4d07e-224">您可以在 [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) 找到許多這些項目。</span><span class="sxs-lookup"><span data-stu-id="4d07e-224">Several of these can be found on [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span> <span data-ttu-id="4d07e-225">針對此文章，我們選擇來自 [Vector Risk](http://www.vectorrisk.com/) 的試用版選項。</span><span class="sxs-lookup"><span data-stu-id="4d07e-225">For this article we chose a trial option from [Vector Risk](http://www.vectorrisk.com/).</span></span> <span data-ttu-id="4d07e-226">我們將繼續修改我們的系統。</span><span class="sxs-lookup"><span data-stu-id="4d07e-226">We will continue to modify our system.</span></span> <span data-ttu-id="4d07e-227">這次，我們使用服務來計算感興趣的風險數據。</span><span class="sxs-lookup"><span data-stu-id="4d07e-227">This time, we use a service to calculate the risk figure of interest.</span></span> <span data-ttu-id="4d07e-228">此程序由下列步驟組成：</span><span class="sxs-lookup"><span data-stu-id="4d07e-228">This process consists of the following steps:</span></span>

1. <span data-ttu-id="4d07e-229">使用正確的參數呼叫相關風險服務。</span><span class="sxs-lookup"><span data-stu-id="4d07e-229">Call the relevant risk service and with the right parameters.</span></span>

2. <span data-ttu-id="4d07e-230">等候服務完成計算。</span><span class="sxs-lookup"><span data-stu-id="4d07e-230">Wait until the service finishes the calculation.</span></span>

3. <span data-ttu-id="4d07e-231">擷取結果並納入到風險分析中。</span><span class="sxs-lookup"><span data-stu-id="4d07e-231">Retrieve and incorporate the results into the risk analysis.</span></span>

<span data-ttu-id="4d07e-232">已轉譯為 R 程式碼，我們的 R 程式碼可透過必要輸入值 (來自準備好的輸入範本) 的定義來加強。</span><span class="sxs-lookup"><span data-stu-id="4d07e-232">Translated into R code, our R code can be enhanced by the definition of the required input values from a prepared input template.</span></span>

````R
Template <- readLines('RequiredInputData.json')
data <- list(
# drilldown setup
  timeSteps = seq(0, n, by = 1),
  paths = as.integer(seq(0, nt, length.out = min(nt, 100))),
# calc setup
  calcDate = instrument.startDate,
  npaths = nt,
  price = P0,
  vol = sigma,
  drift = mu,
  premium = instrument.premium,
  maturityDate = today()
  )
body <- whisker.render(template, data)
````


<span data-ttu-id="4d07e-233">接著，我們必須呼叫 Web 服務。</span><span class="sxs-lookup"><span data-stu-id="4d07e-233">Next, we need to call the web service.</span></span> <span data-ttu-id="4d07e-234">在此案例中，我們呼叫 StartCreditExposure 方法以觸發計算。</span><span class="sxs-lookup"><span data-stu-id="4d07e-234">In this case, we call the StartCreditExposure method to trigger the calculation.</span></span> <span data-ttu-id="4d07e-235">我們將 API 端點存放在名為 *endpoint* 的變數中。</span><span class="sxs-lookup"><span data-stu-id="4d07e-235">We store the endpoint for the API in a variable named *endpoint*.</span></span>

````R
# make the call
result <- POST( paste(endpoint, "StartCreditExposure", sep = ""),  
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
                body = body, encode = "raw"
               )

result <- content(result)
````

<span data-ttu-id="4d07e-236">一旦計算完成，我們就會擷取結果。</span><span class="sxs-lookup"><span data-stu-id="4d07e-236">Once the calculations have finished, we retrieve the results.</span></span>

````R
# get back high level results
result <- POST( paste(endpoint, "GetCreditExposureResults", sep = ""), 
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
               body = sprintf('{"getCreditExposureResults": {"token":"DataSource=Production;Organisation=Microsoft", "ticket": "%s"}}', ticket), encode = "raw")

result <- content(result)
````

<span data-ttu-id="4d07e-237">這可讓分析師繼續處理收到的結果。</span><span class="sxs-lookup"><span data-stu-id="4d07e-237">This leaves the analyst to continue with the results received.</span></span> <span data-ttu-id="4d07e-238">感興趣相關風險數據是從結果擷取並繪製。</span><span class="sxs-lookup"><span data-stu-id="4d07e-238">The relevant risk figures of interest are extracted from the results and plotted.</span></span>

````R
if (!is.null(result$error)) {
    cat(result$error$message)
} else {
    # plot PFE
    result <- result$getCreditExposureResultsResponse$getCreditExposureResultsResult
    df <- do.call(rbind, result$exposures)
    df <- as.data.frame(df)
    df <- subset(df, term <= n)   
}

plot(as.numeric(df$term[df$statistic == 'PFE']) / 365, df$result[df$statistic == 'PFE'], type = "p", xlab = ("time t in Years"), ylab = ("Potential Future Exposure in USD"), ylim = range(c(df$result[df$statistic == 'PFE'], df$result[df$statistic == 'PFE'])), col = "red")
````


<span data-ttu-id="4d07e-239">產生的繪圖看起來如下：</span><span class="sxs-lookup"><span data-stu-id="4d07e-239">The resulting plots look like this:</span></span>

|       |     |
|----    |---- |
| <img src="./assets/fsi-risk-modeling/image6.png" width="400px" alt="Figure 4 - Credit Exposure for MSFT Equity Forward - Calculated with a Cloud Based Risk Engine"/> | <img src="./assets/fsi-risk-modeling/image7.png" width="400px" alt="Figure 5 - Potential Future Exposure for MSFT Equity Forward - Calculated with a Cloud  Based Risk Engine" /> |
| <span data-ttu-id="4d07e-240">圖 4 - MSFT 權益遠期的信用曝險 -</span><span class="sxs-lookup"><span data-stu-id="4d07e-240">Figure 4 - Credit Exposure for MSFT Equity Forward -</span></span> <br/><span data-ttu-id="4d07e-241">使用雲端式風險引擎所計算</span><span class="sxs-lookup"><span data-stu-id="4d07e-241">Calculated with a Cloud Based Risk Engine</span></span> | <span data-ttu-id="4d07e-242">圖 5 - MSFT 權益遠期的潛在遠期曝險 -</span><span class="sxs-lookup"><span data-stu-id="4d07e-242">Figure 5 - Potential Future Exposure for MSFT Equity Forward -</span></span> <br/> <span data-ttu-id="4d07e-243">使用雲端式風險引擎所計算</span><span class="sxs-lookup"><span data-stu-id="4d07e-243">Calculated with a Cloud  Based Risk Engine</span></span> |



## <a name="next-steps"></a><span data-ttu-id="4d07e-244">後續步驟</span><span class="sxs-lookup"><span data-stu-id="4d07e-244">Next Steps</span></span>

<span data-ttu-id="4d07e-245">透過計算基礎結構與 SaaS 型風險分析服務彈性存取雲端服務的功能，對於在資本市場與保險業中工作的風險分析師而且，在速度與靈活性方面都有進步。</span><span class="sxs-lookup"><span data-stu-id="4d07e-245">Flexible access to the cloud through compute infrastructure and SaaS-based risk analysis services can deliver improvements in speed and agility for risk analysts working in capital markets and insurance.</span></span> <span data-ttu-id="4d07e-246">在此文章中，我們逐步解說示範如何使用 Azure 與其他服務 (使用風險分析師知道的工具) 的範例。</span><span class="sxs-lookup"><span data-stu-id="4d07e-246">In this article we worked through an example which illustrates how to use Azure and other services using tools risk analysts know.</span></span> <span data-ttu-id="4d07e-247">在您建立並加強您的風險模型時，嘗試利用 Azure 的功能。</span><span class="sxs-lookup"><span data-stu-id="4d07e-247">Try taking advantage of Azure's capabilities as you create and enhance your risk models.</span></span>

### <a name="tutorials"></a><span data-ttu-id="4d07e-248">教學課程</span><span class="sxs-lookup"><span data-stu-id="4d07e-248">Tutorials</span></span>

- <span data-ttu-id="4d07e-249">R 開發人員：[使用 Azure Batch 執行平行 R 模擬](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)</span><span class="sxs-lookup"><span data-stu-id="4d07e-249">R Developers: [Run a parallel R simulation with Azure   Batch](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)</span></span>

- [<span data-ttu-id="4d07e-250">基本 R 命令和 RevoScaleR 函式：25個常見範例</span><span class="sxs-lookup"><span data-stu-id="4d07e-250">Basic R commands and RevoScaleR functions: 25 common   examples</span></span>](https://docs.microsoft.com/machine-learning-server/r/tutorial-r-to-revoscaler?WT.mc_id=fsiriskmodelr-docs-scseely)

- <span data-ttu-id="4d07e-251">[使用 RevoScaleR 視覺化及分析資料](https://docs.microsoft.com/machine-learning-server/r/tutorial-revoscaler-data-model-analysis?WT.mc_id=fsiriskmodelr-docs-scseely) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="4d07e-251">[Visualize and analyze data using   RevoScaleR](https://docs.microsoft.com/machine-learning-server/r/tutorial-revoscaler-data-model-analysis?WT.mc_id=fsiriskmodelr-docs-scseely)</span></span>

- [<span data-ttu-id="4d07e-252">HDInsight 上的 ML 服務與開放原始碼 R 功能簡介</span><span class="sxs-lookup"><span data-stu-id="4d07e-252">Introduction to ML Services and open-source R capabilities on   HDInsight</span></span>](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview?WT.mc_id=fsiriskmodelr-docs-scseely)

<span data-ttu-id="4d07e-253">_本文是由 Dr. Darko Mocelj 和 Rupert Nicolay 所撰寫。_</span><span class="sxs-lookup"><span data-stu-id="4d07e-253">_This article was authored by Dr. Darko Mocelj and Rupert Nicolay._</span></span>