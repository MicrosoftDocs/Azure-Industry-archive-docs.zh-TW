# <a name="enabling-the-financial-services-risk-lifecycle-with-azure-and-r"></a>使用 Azure 與 R 啟用財務服務風險生命週期


在關鍵金融服務作業中的生命週期中，風險計算在數個階段都非常關鍵。 例如，保險產品管理生命週期的一個簡化形式看起來可能像下列圖表一樣。 風險計算方面是以藍色文字顯示。

![](./assets/fsi-risk-modeling/image1.png)

資本市場公司的案例看起來可能像這個一樣：

![](./assets/fsi-risk-modeling/image2.png)

在這些程序中，風險模型建構的常見需求包括：

1.  對於特設風險相關實驗 (依風險分析) 的需求；保險公司中的精算師，或資本市場公司中的分析師。
    這些分析師一般都會處理程式碼與其領域中熱門的模型建構工具：R 與 Python。 許多大學履歷都包括數學金融與 MBA 課程中的 R 或 Python 訓練。
    這兩個語言都提供廣泛的開放原始碼程式庫，這支援熱門的風險計算。 搭配使用適當的工具之後，分析師通常需要存取下列項目：

    a.  精確的市場定價資料。

    b.  現有的原則與宣告資料。

    c.  現有的市場位置資料。

    d.  其他外部資料。 來源包括結構化資料，例如死亡率表與競爭定價資料。 可能也使用較少的傳統來源，例如天氣、新聞等。

    e.  計算功能以啟用快速的互動式資料調查。

2.  他們也可以使用特設機器學習演算法來定價或決定市場策略。

3.  視覺化並呈現資料以在產品規劃、交易策略與類似討論中使用的需求。

4.  快速執行由分析師所設定的已定義模型，以進行定價、估值與市場風險。 估值使用專屬風險模型建構、市場風險工具與自訂程式碼的組合。 會以批次方式搭配不同的每晚、每週、每月、每季與每年計算 (產生工作負載高峰) 來執行分析。

5.  將資料與其他企業內部風險評估整合，以取得合併的風險報告。 在較大的組織中，較低層級的風險估計可以傳輸到企業風險模型建構與報告工具。

6.  必須以必要間隔以已定義格式報告結果，以符合投資人與監管需求。

Microsoft 在 [Azure Marketplace](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) 中透過 Azure 服務與合作夥伴供應項目的結合來支援上面的顧慮。 在此文章中，我們顯示有關如何使用 R 執行特設實驗的範例。我們從說明如何在單一機器上執行實驗開始，接著顯示如何在 [Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely) 上執行相同的實驗，最後顯示如何在我們的模型建構中利用外部服務的優勢。 在 Azure 上執行已定義模型時的選項與考量項目可以在著重在[銀行](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)與[保險](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)的這些文章中找到。

## <a name="analyst-modelling-in-r"></a>R 中的分析師模型建構 

讓我們從查看分析師如何在簡化的代表性資本市場案例中使用 R 開始。 您可以透過針對計算參考現有的 R 程式庫或從頭開始撰寫程式碼來建置。 在我們的案例中，我們也必須擷取外部定價資料。 為讓範例維持簡單但仍具示範功能，我們計算股票遠期合約的潛在未來曝險 (PFE)。
此範例針對複雜導數的檢測避免複雜量化模型建構技術，並著重在單一風險因素以專注在風險生命週期。 我們的範例會執行下列動作：

1.  選取感興趣的檢測。

2.  檢測的來源歷史價格。

3.  透過簡單的蒙地卡羅 (MC) 計算 (它使用幾何布朗運動 (GBM)) 來建構權益價格模型：

    a.  預估預期的報酬 μ (mu) 與揮發性 σ (theta)。

    b.  校準模型至歷史資料。

4.  視覺化傳達結果的各種路徑。

5.  繪製 max(0,Stock Value) 以示範 PFE 的意義、風險價值 (VaR) 的差異

    a.  釐清：PFE = Share Price (T) -- 遠期合約價格 K

6.  取 0.95 百分位數以在每個時間步驟/模擬期間結尾取得 PFE 值

我們將以 MSFT 股票為基礎計算權益遠期的潛在未來曝險。 如前面所述，若要建構股票價格模型，需要 MSFT 股票的歷史價格，以便我們可以校準模型至歷史資料。 有許多方式可以取得歷史股票價格。 在我們的範例中，我們使用外部服務提供者 [Quandl](https://www.quandl.com/) 提供的免費股票價格服務。


> 注意：範例使用 [WIKI 價格資料集](https://www.quandl.com/databases/WIKIP)，這可用於學習概念。 針對美國型權益的產品使用，Quandl 建議使用 [每日收盤美國股票價格資料集](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices)。

若要處理資料並定義與權益關聯的風險，我們必須執行下列動作：

1.  從權益擷取歷史資料。

2.  從歷史資料判斷預期報酬 μ 與揮發性 σ。

3.  使用一些模擬方式，建構底層股票價格的模型。

4.  執行模型

5.  判斷未來權益的曝險。

我們一開始先從 Quandl 服務擷取股票，並繪製過去 180 天的收盤價格歷史。

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

擁有該資料之後，我們便開始校準 GBM 模型。

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

接著，我們建構底層股票價格的模型。 我們可以從頭開始實作各自的 GBM 程序，或利用提供此功能的許多 R 套件的其中一個。 我們使用 R 套件 [*sde* (隨機微分方程式的模擬與推斷)](https://cran.r-project.org/web/packages/sde/index.html)，它提供一個解決此問題的方法。 GBM 方法需要一組參數，它們被校準為歷史資料，或提供為模擬參數。 我們使用歷史資料，在模擬 (P0) 開始處提供 μ、σ 與股票價格。

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

我們現在可以開始蒙地卡羅模擬以針對一些模擬路徑建構潛在曝險模型。 我們將模擬限制為 50 個蒙地卡羅路徑與 256 個時間步驟。 為準備相應放大模擬並發揮 R 中的平行處理優勢， 蒙地卡羅模擬迴圈使用 foreach 陳述式。

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

我們現在已經模擬底層 MSFT 股票的價格。 為計算權益遠期的曝險，我們減去溢價並將曝險限制為僅正值。

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

下兩張凸顯示模擬的結果。 第一張圖顯示底層股票價格的蒙地卡羅模擬 (針對 50 個路徑)。 第二張圖示範將溢價從權益遠期減去之後，權益遠期的底層「信用曝險」。


|       |    |
|---    |--- |
| <img src="./assets/fsi-risk-modeling/image3.png" width="400px" alt="Figure 1 - 50 Monte Carlo Paths"/> | <img src="./assets/fsi-risk-modeling/image4.png" width="400px" alt="Figure 2 - Credit Exposure for Equity Forward"/> |
| 圖 1 - 50 個蒙地卡羅路徑 | 圖 2 - 權益遠期的信用曝險 |


在最後一個步驟中，1 個月 0.95 百分位數 PFE 是由下列程式碼所計算。

````R
# Calculate the PFE at each time step
df_pfe <- cbind(t,apply(pfe_mc,2,quantile,probs = instrument.pfeQuantile ))

resulting in the final PFE plot
plot(df_pfe, t = 'l', ylab = "Potential Future Exposure in USD", xlab = "time t in Years")
````

<img src="./assets/fsi-risk-modeling/image5.png" width="500px" alt="Potential Future Exposure for MSFT Equity Forward" /> 

圖 3 MSFT 權益遠期的潛在遠期曝險

## <a name="using-azure-batch-with-r"></a>搭配 R 使用 Azure Batch 

上面所述的 R 解決方案可以連結到 Azure Batch 並利用雲端來進行風險計算。 針對平行計算 (例如我們的範例)，這需要多一點心力。 [使用 Azure Batch 執行平行 R 模擬](https://docs.microsoft.com/en-us/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)教學課程提供有關如何將 R 連線到 Azure Batch 的詳細資訊。 我們在下面顯示連線到 Azure Batch 之程序的程式碼與摘要，以及如何在簡化的 PFE 計算中發揮雲端延伸模組的優勢。

此範例處理稍早所述的一些模型。 如果我們先前所看見的，此計算可以在我們的個人電腦上執行。 增加蒙地卡羅路徑數目或使用較小的時間步驟將會導致長許多的執行時間。 幾乎所有 R 程式碼都不需變更。 我們將在此節中反白顯示差異處。

在 Azure 中執行之每個蒙地卡羅模擬的路徑。 我們可以這樣做，因為每個路徑都與彼此獨立，這讓我們可以進行「使人窘迫的平行」計算。

為使用 Azure Batch，我們定義底層叢集並在程式碼中參考它，接著叢集才能在計算中使用。 為執行計算，我們使用下列 cluster.json 定義：

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

使用此叢集定義時，下列 R 程式碼會利用叢集：
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

最後，我們更新稍早的 foreach 陳述式以使用 doAzureParallel 套件。 這是輕微的變更，加入了對 sde 套件的參考，並將 %do% 變更為 %dopar%：

````R
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package and extend the computation to the cloud
exposure_mc <- foreach(i = 1:nt, .combine = rbind, .packages = 'sde') %dopar% GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()
````

每個蒙地卡羅模擬都是以工作形式提交到 Azure Batch。 該工作在雲端中執行。 結果在傳回給分析師工作台之前會先合併。 舉重與計算在雲端中執行，以發揮規模調整與底層基礎結構的完整優勢 (要求的計算所需要的)。

在完成計算之後，可以透過叫用下列單一指示以輕鬆關閉額外的資源：

````R
# Stop the Cloud cluster
stopCluster(cluster)
````


## <a name="use-a-saas-offering"></a>使用 SaaS 供應項目

前兩個範例顯示如何利用本機與雲端基礎結構來開發適當的估值模型。 此範例已經開始變更。 與內部部署基礎結構轉換為雲端式 IaaS 與 PaaS 服務的方式相同，相關風險數據的模型建構會轉換為服務導向程序。
今天的分析師面臨兩個主要挑戰：

1.  法規需求使用逐漸增加的計算功能來新增到模型建構需求。 監管者要求更頻繁且更新的風險數據。

2.  現有的風險基礎結構已隨著時間有機地成長，而且在以靈活方式實作新的需求與更進階的風險模型建構時會產生挑戰。

雲端式服務可提供必要的功能並支援風險分析。 此方法有一些優點：

-   監管者所要求的最常見風險計算必須在法規下由每個人實作。 透過使用來自特殊化服務提供者的服務，分析師可從能隨時使用、符合監管者規範的風險計算而獲益。 此類服務可能包括市場風險計算、對手風險計算、X 值調整 (XVA)，甚至是交易簿基本檢閱 (FRTB) 計算。

-   這些服務透過 Web 服務公開其介面。 現有的風險基礎結構可透過這些其他服務來加強。

在我們的範例中，我們想要針對 FRTB 計算叫用雲端式服務。 您可以在 [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) 找到許多這些項目。 針對此文章，我們選擇來自 [Vector Risk](http://www.vectorrisk.com/) 的試用版選項。 我們將繼續修改我們的系統。 這次，我們使用服務來計算感興趣的風險數據。 此程序由下列步驟組成：

1.  使用正確的參數呼叫相關風險服務。

2.  等候服務完成計算。

3.  擷取結果並納入到風險分析中。

已轉譯為 R 程式碼，我們的 R 程式碼可透過必要輸入值 (來自準備好的輸入範本) 的定義來加強。

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


接著，我們必須呼叫 Web 服務。 在此案例中，我們呼叫 StartCreditExposure 方法以觸發計算。 我們將 API 端點存放在名為 *endpoint* 的變數中。

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

一旦計算完成，我們就會擷取結果。

````R
# get back high level results
result <- POST( paste(endpoint, "GetCreditExposureResults", sep = ""), 
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
               body = sprintf('{"getCreditExposureResults": {"token":"DataSource=Production;Organisation=Microsoft", "ticket": "%s"}}', ticket), encode = "raw")

result <- content(result)
````

這可讓分析師繼續處理收到的結果。 感興趣相關風險數據是從結果擷取並繪製。

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


產生的繪圖看起來如下：

|       |     |
|----    |---- |
| <img src="./assets/fsi-risk-modeling/image6.png" width="400px" alt="Figure 4 - Credit Exposure for MSFT Equity Forward - Calculated with a Cloud Based Risk Engine"/> | <img src="./assets/fsi-risk-modeling/image7.png" width="400px" alt="Figure 5 - Potential Future Exposure for MSFT Equity Forward - Calculated with a Cloud  Based Risk Engine" /> |
| 圖 4 - MSFT 權益遠期的信用曝險 - <br/>使用雲端式風險引擎所計算 | 圖 5 - MSFT 權益遠期的潛在遠期曝險 - <br/> 使用雲端式風險引擎所計算 |



## <a name="next-steps"></a>後續步驟

透過計算基礎結構與 SaaS 型風險分析服務彈性存取雲端服務的功能，對於在資本市場與保險業中工作的風險分析師而且，在速度與靈活性方面都有進步。 在此文章中，我們逐步解說示範如何使用 Azure 與其他服務 (使用風險分析師知道的工具) 的範例。 在您建立並加強您的風險模型時，嘗試利用 Azure 的功能。

### <a name="tutorials"></a>教學課程


-   R 開發人員：[使用 Azure Batch 執行平行 R 模擬](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [基本 R 命令與 RevoScaleR 函式：25 個常見範例](https://docs.microsoft.com/machine-learning-server/r/tutorial-r-to-revoscaler?WT.mc_id=fsiriskmodelr-docs-scseely) \(英文\)

-   [使用 RevoScaleR 視覺化及分析資料](https://docs.microsoft.com/machine-learning-server/r/tutorial-revoscaler-data-model-analysis?WT.mc_id=fsiriskmodelr-docs-scseely) \(英文\)

-   [HDInsight 上的 ML 服務與開放原始碼 R 功能簡介](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview?WT.mc_id=fsiriskmodelr-docs-scseely)

此文章作者為 Dr.Darko Mocelj 與 Rupert Nicolay。