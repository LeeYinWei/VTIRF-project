# VTIRF-project
除非有時間，否則CNN不是什麼好辦法呦
# 正文
## 問題敘述
設計一維多層結構， 能使用的材料有Ag， Al2O3， HfO2， SiO2， 總層數不超過16層，總厚度不超過800nm， 使得該結構的透射光譜的FOM最大。

FOM定義：

![主選單背景](https://thundering-capybara-3fb.notion.site/image/attachment%3A15ec713a-401d-4294-b49f-f04712312976%3AFOM_math.png?table=block&id=199b87fb-4849-80f5-a757-e0e2644e4687&spaceId=f06d0f0b-05f5-4e9e-b73d-9e0240c7f17a&width=580&userId=&cache=v2 "FOM intro")

## 解決方案
訓練一個能根據輸入光譜輸出相對應結構的模型， 訓練完成後， 輸入理想光譜，就能獲得理想結構。這裡使用的模型是CNN

本研究中使用的 1D-CNN (一維卷積神經網路) 模型是專門為處理 光譜序列數據 到 薄膜結構參數 的逆向設計任務而構建的。以下是該 CNN 模型的技術細節介紹：1. 模型架構設計 (Model Architecture)此 1D-CNN 採用了多層卷積結構，旨在從高維的光譜特徵中提取局部物理特徵：輸入層 (Input Layer)：接收維度為 (200, 1) 的數據，代表光譜在 400nm 至 2000nm 區間的 200 個採樣點。卷積層 (Conv1D Blocks)：第一層：32 個濾波器 (Filters)，卷積核大小 (Kernel Size) 為 3，使用 ReLU 激活函數。第二層：64 個濾波器，卷積核大小為 3，同樣使用 ReLU。池化層 (Pooling Layer)：使用 Average Pooling (Pool Size: 2) 進行降維，保留光譜特徵的全局平均資訊。正規化與防過擬合：包含 Batch Normalization 以增強梯度穩定性，以及 Dropout (0.2) 減少過擬合風險。輸出層 (Output Layer)：16 個單元，分別對應 16 層薄膜的厚度參數，使用 Sigmoid 或 Linear 激活函數進行數值預測。

![主選單背景](picture/CNNstructure "CNN structure")
## 資料集
先隨機生成結構，再用TMM(Transfer Matrix Method)計算出頻譜。
### 定義結構
#### 輸入頻譜(source)
又稱作source，簡稱src，為一連串的小數以逗號分隔，四捨五入到小數點後第三位，代表121個波長由小到大對應的穿透率，這121個波長來自[Ag.txt](https://drive.google.com/file/d/1C3TMoUacNziqRburjEdFepTVMDOVADnw/view?usp=drive_link)。

Example:`0.123, 0.002, 0.101...（略）`
#### 輸出結構(target)
又稱作target，簡稱tgt，為一連串字串以逗號分隔，字串的格式為：英文字母_數字，代表某一層的材料和厚度。a代表Ag，l代表Al2O3，h代表HfO2，s代表SiO2。數字的部分，單位是奈米，不同材料有不同的厚度範圍，精度到小數點後第一位。

Example:`a_20, l_108.3, h_104.3...（略）`

生成資料流程分為以下三個主要階段：
1. 參數空間採樣 (Parameter Space Sampling)隨機厚度生成：針對 16 層薄膜結構，隨機生成每層材料（$Ag, Al_{2}O_{3}, HfO_{2}, SiO_{2}$）的厚度參數。物理約束限制：確保單層厚度在合理範圍內，且 總厚度不超過 800 nm，以符合實際製程限制。樣本規模：總計生成了 1,010,000 組 獨特的結構組合，以覆蓋足夠大的設計空間。
2. 物理光譜模擬 (Physical Forward Simulation)傳輸矩陣法 (TMM)：利用 Transfer Matrix Method (TMM) 模擬每組結構在不同波長下的光學響應。波長取樣：採樣範圍涵蓋 200 nm 至 2000 nm，精確計算其穿透率 (Transmittance) 與反射率。高解析度處理：採用 200 點解析度以捕捉尖銳的切斷邊緣 (Cut-off edge)，這對於 VTIRF 的效能評估至關重要。
3. 資料預處理與正規化 (Preprocessing & Normalization)標籤正規化 (Label Scaling)：將 16 層薄膜的厚度數據進行正規化處理，這有助於增強神經網路梯度計算的穩定性並加速收斂。特徵編碼：將模擬出的光譜數據編碼為一維序列 (1D Sequence)，作為 CNN 與 Transformer 模型的輸入特徵。訓練集劃分：將數據集劃分為訓練集與驗證集，以評估模型對於未知光譜結構設計的泛化能力。

## 訓練程式碼
[cnn_vtirf.ipynb](folder/cnn_vtirf.ipynb)
`!pip install pytorch`

