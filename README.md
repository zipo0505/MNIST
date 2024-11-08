# 詳解 Pytorch 實作 MNIST
南華大學跨領域-人工智慧期中報告
11024103 林仁楚 11024227 邱胤睿

參考網址:https://viatorsun.blog.csdn.net/article/details/107275868?spm=1001.2101.3001.6650.14&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-14-107275868-blog-125947800.235%5Ev43%5Econtrol&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-14-107275868-blog-125947800.235%5Ev43%5Econtrol&utm_relevant_index=24
MNIST雖然很簡單，但是值得我們學習的東西還是有很多的。
專案雖然簡單，但是個人建議還是將各個模組分開創建，特別是對於新人而言，模組化的創建會讓讀者更加清晰、易於理解。

CNN模組：卷積神經網路的組成；
train模組：利用CNN模型 對 MNIST資料集 進行訓練並儲存模型
test模組：載入訓練好的模型對測試集資料進行測試
cnn.pt ： train 的CNN模型

![image](https://github.com/zipo0505/MINIST/blob/main/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2%202024-11-03%20150849.png?raw=true)
# CNN 模組
MNIST的辨識演算法很多，在此提供的是 卷積神經網路CNN ，其他演算法也同樣可以取得很好的辨識效果，有興趣的小夥伴可以自己嘗試。

在此就不得不提 Pytorch的優勢了，而且都知道 Pytorch 是動態計算模型。但是何為動態運算模型呢？

在此對比 Tensorflow。在流行的神經網路架構中, Tensorflow 就是最典型的靜態運算架構。使用Tensorflow 就必須先搭建好這樣一個計算系統, 一旦搭建好了, 就不能改動了(也有例外), 所有的計算都會在這種圖中流動, 當然很多情況下這樣就夠了, 我們不需要改動什麼結構。
不動結構當然可以提高效率. 但是一旦計算流程不是靜態的, 計算圖要變動. 最典型的例子就是RNN, 有時候RNN 的time step 不會一樣, 或者在training 和testing 的時候, batch_size 和time_step 也不一樣, 這時, Tensorflow 就頭痛了。
如果用一個動態計算圖的 Pytorch, 我們就好理解多了, 寫起來也簡單多了. PyTorch 支持在運行過程中根據運行參數動態改變應用模型。可以簡單理解為：一種是先定義後使用，另一種是邊使用邊定義。動態運算圖模式是 PyTorch 的天然優勢之一，Google 2019年 3 月發布的 TensorFlow 2.0 Alpha 版本中的 Eager Execution，被認為是在動態計算圖模式上追趕 PyTorch 的舉措。

CNN 模組分析
CNN 模組主要分為兩個部分，一個是定義CNN模組，另一個是將各個模組組成前向傳播通道

super() 函數： 是用來呼叫父類別(超類別)的一個方法。
用來解決多重繼承問題的，直接用類別名稱呼叫父類別方法在使用單一繼承的時候沒問題，但是如果使用多重繼承，會涉及到查找順序（MRO）、重複呼叫（鑽石繼承）等種種問題。
super(SimpleCNN, self) 先找到 SimpleCNN 的父類別（就是類別 nn.Module ），然後把類別 SimpleCNN 的物件轉換為類別 nn.Module 的對象

nn.Sequential()： 是一個有順序的容器，將神經網路模組 依照傳入建構器的順序依序被加入計算圖中執行。由於每個神經網路模組都繼承於nn.Module，因此透過索引的方式利用add_module函數將 nn.Sequential()模組 加入現有模組中。

forward()： 是前向傳播函數，將先前定義好的每層神經網路模組串聯起來，同時也定義了模型的輸入參數

x.view() & x.reshape()： 其實兩者的作用並沒有太大差別，作用都是調整張量的型別大小，view() 出現的更早些，而reshape() 則是為了與Numpy對齊，在Pytorch 0.3版本之後添加的，兩者作用沒有太大區別；

![image](https://github.com/zipo0505/MINIST/blob/main/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2%202024-11-03%20150454.png?raw=true)
# train 模組
![image](https://github.com/zipo0505/MINIST/blob/main/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2%202024-11-03%20145607.png?raw=true)
# test 模組
![image](https://github.com/user-attachments/assets/cc17038c-c9d8-4b8c-971d-c34dd8e33832)

# MNIST 資料集
如果還沒有MNIST資料集，可以透過以下方式從 torchvision 下載，下載路徑為專案路徑下的 ‘./data’ 資料夾下，可以看到 MNIST 的資料是 ubyte
![image](https://github.com/zipo0505/MINIST/blob/main/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2%202024-11-03%20150435.png?raw=true)
![image](https://github.com/zipo0505/MINIST/blob/main/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2%202024-11-03%20150418.png?raw=true)

透過上述datasets.MNIST 指令將MNIST 資料讀取到記憶體中，並轉換為Tensor 格式儲存在train_dataset 變數中，透過Debug 我們可以看到MNIST 的資料是【10000,28,28】的數據，每個【28 ,28】的資料對應的標籤是targets
![image](https://github.com/user-attachments/assets/6cebf45d-797a-4686-9d8e-ff8deb8484cf)

然後我們視覺化其中一個【28,28】資料可以看出，其就是一個28x28 的單通道灰階圖，每個值表示一個像素點，其值範圍為【0-255】，像素值並不能直接傳入模型，需要透過transforms.ToTensor() 轉換為Tensor格式。
![image](https://github.com/zipo0505/MINIST/blob/main/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2%202024-11-03%20150315.png?raw=true)
