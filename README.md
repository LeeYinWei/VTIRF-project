# VTIRF-project
除非有時間，否則CNN不是什麼好辦法呦
# 正文
## 問題敘述
設計一維多層結構， 能使用的材料有Ag， Al2O3， HfO2， SiO2， 總層數不超過16層，總厚度不超過800nm， 使得該結構的透射光譜的FOM最大。

FOM定義：

![主選單背景](https://thundering-capybara-3fb.notion.site/image/attachment%3A15ec713a-401d-4294-b49f-f04712312976%3AFOM_math.png?table=block&id=199b87fb-4849-80f5-a757-e0e2644e4687&spaceId=f06d0f0b-05f5-4e9e-b73d-9e0240c7f17a&width=580&userId=&cache=v2 "FOM intro")

## 解決方案
訓練一個能根據輸入光譜輸出相對應結構的模型， 訓練完成後， 輸入理想光譜，就能獲得理想結構。這裡使用的模型是CNN

本研究中使用的 1D-CNN (一維卷積神經網路) 模型是專門為處理 光譜序列數據 到 薄膜結構參數 的逆向設計任務而構建的。以下是該 CNN 模型的技術細節介紹：1. 模型架構設計 (Model Architecture)此 1D-CNN 採用了多層卷積結構，旨在從高維的光譜特徵中提取局部物理特徵：輸入層 (Input Layer)：接收維度為 (200, 1) 的數據，代表光譜在 400nm 至 2000nm 區間的 200 個採樣點。卷積層 (Conv1D Blocks)：第一層：32 個濾波器 (Filters)，卷積核大小 (Kernel Size) 為 3，使用 ReLU 激活函數。第二層：64 個濾波器，卷積核大小為 3，同樣使用 ReLU。池化層 (Pooling Layer)：使用 Average Pooling (Pool Size: 2) 進行降維，保留光譜特徵的全局平均資訊。正規化與防過擬合：包含 Batch Normalization 以增強梯度穩定性，以及 Dropout (0.2) 減少過擬合風險。輸出層 (Output Layer)：16 個單元，分別對應 16 層薄膜的厚度參數，使用 Sigmoid 或 Linear 激活函數進行數值預測。2. 訓練與優化機制 (Training & Optimization)損失函數 (Loss Function)：以 均方誤差 (MSE) 作為指標，衡量預測光譜與目標光譜之間的物理擬合精確度。學習率調度 (Learning Rate Scheduling)：採用 ReduceLROnPlateau 策略。當驗證損失停止下降時，自動將學習率乘以 0.5，以進行更細微的參數調整 (Fine-grained tuning)。數據規模：基於 1,010,000 組 透過傳輸矩陣法 (TMM) 生成的物理真實樣本進行訓練，確保模型具備強健的物理映射能力。3. 模型表現分析 (Performance Analysis)優勢：紅外抑制：該 CNN 模型在紅外線區段 ($>700\text{ nm}$) 展現了極佳的反射抑制能力，使其 FOM (品質因數) 達到 0.9813。局限：可見光穿透：由於卷積層傾向於捕捉局部光譜轉折，在優化 16 層膜干涉的整體相位耦合時較為吃力，導致其可見光峰值穿透率僅約 0.6，低於 Transformer 架構的 0.8。4. 在技術文件中的定位在您的 GitHub README 或技術文件中，可以將此 CNN 定位為 「基於局部特徵提取的快速設計基準 (Baseline)」，並強調其在特定頻段抑制（如紅外線）的高效率。



