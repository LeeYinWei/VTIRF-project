# VTIRF-project
除非有時間，否則CNN不是什麼好辦法呦
# 正文
1. 問題敘述
設計一維多層結構， 能使用的材料有Ag， Al2O3， HfO2， SiO2， 總層數不超過16層，總厚度不超過800nm， 使得該結構的透射光譜的FOM最大。
FOM定義：
![主選單背景](https://thundering-capybara-3fb.notion.site/image/attachment%3A15ec713a-401d-4294-b49f-f04712312976%3AFOM_math.png?table=block&id=199b87fb-4849-80f5-a757-e0e2644e4687&spaceId=f06d0f0b-05f5-4e9e-b73d-9e0240c7f17a&width=580&userId=&cache=v2 "FOM intro")

2.解決方案
訓練一個能根據輸入光譜輸出相對應結構的模型， 訓練完成後， 輸入理想光譜，就能獲得理想結構。這裡使用的模型是CNN



