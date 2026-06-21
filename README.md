第11組 二手物品交換平台
---
[期末報告ppt](https://canva.link/gnlxj48w45zk4ok) 。
---
[期末報告word](二手物品交換平台word.pdf) 。
---

| 姓名 | 學號 |心得|
|------|------|------|
| 余家豪 | 41243116 |  資料庫系統對我來說是一個從會寫程式的學生到軟體工程師的第一部也是非常重要的一步，其實生活中常常會使用到資料庫但我們卻渾然不覺，經過這一學期的課程以及這次期末專題，我從一開始的一竅不知到如今已略懂資料庫系統運作，可以與組員一同設計一個二手物品交換平台的資料庫以及它的運作邏輯，我想這對我未來面對更大型的專案有更多自信可以完成，感謝老師一學期的教導。
| 楊承哲 | 41243147 |在這學期學到了很多設計、管理資料庫時要注意的事情，資料庫沒有我想的那麼難，但有很多眉眉角角要注意，透過這學期的課程讓我在以後再遇到類似的情況可以避免再犯下一樣的錯誤。
| 黃培峰 | 41243145 |
| 温博鈞 | 41243144 |

---


## 應用情境
  小華為了活化家中閒置的物品，登入二手交換平台，透過分類搜尋找到感興趣的二手相機。他瀏覽商品詳情並與賣家透過內建訊息功能確認物品狀況後，發起交換請求。系統自動記錄雙方的交換意願與物品狀態，並將請求通知賣家。賣家收到通知後，透過平台確認並同意交換。雙方隨即約定時間完成面交，並在確認物品無誤後於系統上完成交易。
  
---

## 使用案例
### 使用者：
  1. **管理員**
      - 分類管理:管理員可以新增或刪除商品分類
      - 違規處理:管理員可以下架違規商品或停權違規帳號。
  2. **接收者(賣家)**
      - 上架商品:接收者可以上傳商品照片、設定價格、描述商品，下架商品。
      - 交換管理:接收者可以查看發起端提供的物品資訊，並更新交換狀態。
      - 回覆訊息:接收者可以針對發起方的問題進行答覆。
   3. **發起者(買家)**
      - 商品瀏覽: 系統須提供讓發起端選擇「自身一件或多件物品」與「接收方物品」進行配對的功能 。
      - 商品狀態追蹤: 使用者可查看交換狀態。
---

## 資料庫設計圖(ERDIAGRAM)

 ![ERDIAGRAM](NEWER.jpg)

### `users` -使用者資料表

  ```sql
CREATE TABLE Users (
    UserID INT PRIMARY KEY AUTO_INCREMENT,
    Name VARCHAR(50) NOT NULL,
    Email VARCHAR(100) NOT NULL UNIQUE,
    Role VARCHAR(10) NOT NULL,

    CONSTRAINT chk_user_email_format
        CHECK (Email REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+[.][A-Za-z]{2,}$'),

    CONSTRAINT chk_user_role
        CHECK (Role IN ('admin', 'user'))
) ;
  ```
| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|---------|-----------|----|--------------|
| `UserID` |   int   | 使用者編號 | 否 | PK |
| `Name`   | string | 使用者名字 | 否 | 使用者姓名格式 |
| `Email`  | string | 使用者電子信箱   | 否 | 唯一且符合電子郵件格式 |
| `Role` |  string   | 角色 | 否 | 只會是admin or user |

---

### `Category` -分類資料表

 ```sql
CREATE TABLE Category (
    CategoryID INT PRIMARY KEY AUTO_INCREMENT,
    CategoryName VARCHAR(50) NOT NULL UNIQUE
) ;
  ```
| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|---------|-----------|----|--------------|
| `CategoryID` |   int   | 分類編號 | 否 | PK |
| `CategoryName`   | string | 分類名稱 | 否 | 唯一(Unique) |

---
### `Product` -商品資料表

 ```sql
CREATE TABLE Product (
    ProductID INT PRIMARY KEY AUTO_INCREMENT,
    OwnerID INT NOT NULL,
    CategoryID INT NOT NULL,
    Title VARCHAR(100) NOT NULL,
    Description TEXT,
    Price DECIMAL(10, 2) NOT NULL,
    Status VARCHAR(20) NOT NULL DEFAULT '可交換',
    CreatedAt DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT chk_product_price
        CHECK (Price >= 0),

    CONSTRAINT chk_product_status
        CHECK (Status IN ('可交換', '交換中', '已交換', '已下架')),

    FOREIGN KEY (OwnerID) REFERENCES Users(UserID),
    FOREIGN KEY (CategoryID) REFERENCES Category(CategoryID)
) ;
 ```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|---------|-----------|----|--------------|
| `ProductID` |   int   | 商品編號 | 否 | PK |
| `OwnerID`  | int | 擁有者編號   | 否 | FK(關聯至User表) |
| `CategoryID` |   int  | 分類編號 | 否 | FK(關聯至Category表) |
| `Title` |   string   | 產品名稱 | 否 | 長度上限100個字 |
| `Description`   | string | 產品描述 | 否 | 無 |
| `Price` |  decimal   | 產品價格 | 否 | >=0 |
| `Status` |  string   | 商品狀態 | 否 | 可交換，已交換，已下架 |
| `CreatedAt` | DATETIME  | 建立時間 | 否 | 預設為系統當前時間 |

---
### `ProductImages` -商品圖片資料表

  ```sql
CREATE TABLE ProductImages (
    ImageID INT PRIMARY KEY AUTO_INCREMENT,
    ProductID INT NOT NULL,
    ImageURL VARCHAR(255) NOT NULL,

    FOREIGN KEY (ProductID) REFERENCES Product(ProductID)
) ;
  ```
| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|---------|-----------|----|--------------|
| `ImageID` |   int   | 圖片編號 | 否 | PK |
| `ProductID`   | int | 產品編號 | 否 | FK(關聯至Product表) |
| `ImageURL`  | varchar | 圖片路徑  | 否 | 無 |

---
### `Message` -訊息資料表
  ```sql
CREATE TABLE Message (
    MessageID INT PRIMARY KEY AUTO_INCREMENT,
    SenderID INT NOT NULL,
    ReceiverID INT NOT NULL,
    ProductID INT NOT NULL,
    Content TEXT NOT NULL,
    SentTime DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (SenderID) REFERENCES Users(UserID),
    FOREIGN KEY (ReceiverID) REFERENCES Users(UserID),
    FOREIGN KEY (ProductID) REFERENCES Product(ProductID)
);

  ```
| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|---------|-----------|----|--------------|
| `MessageID` |   int   | 訊息編號 | 否 | PK |
| `SenderID`   | int | 發送者編號 | 否 | FK(關聯至User表) |
| `ReceiverID`  | int | 接收者編號   | 否 | FK(關聯至User表) |
| `ProductID` |   int  | 產品邊號 | 否 | FK(關聯至Product表) |
| `Content` |   text   | 訊息內容 | 否 | 須包含文字不可為空 |
| `SentTime` |  datetime   | 發送時間 | 否 | 系統當前時間 |
---
### `Exchanges` -交換資料表
  ```sql
CREATE TABLE Exchanges (
    ExchangeID INT PRIMARY KEY AUTO_INCREMENT,
    ProposerUserID INT NOT NULL,
    ReceiverUserID INT NOT NULL,
    OrderDate DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    Status VARCHAR(20) NOT NULL DEFAULT '待確認',

    CONSTRAINT chk_exchange_status
        CHECK (Status IN ('待確認', '已同意', '已拒絕', '已完成')),

    CONSTRAINT chk_exchange_different_users
        CHECK (ProposerUserID <> ReceiverUserID),

    FOREIGN KEY (ProposerUserID) REFERENCES Users(UserID),
    FOREIGN KEY (ReceiverUserID) REFERENCES Users(UserID)
);

  ```
| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|---------|-----------|----|--------------|
| `ExchangesID` |   int   | 交換編號 | 否 | PK |
| `ProposerUserID`   |  int | 發起者編號 | 否 | FK(關聯至User表) |
| `ReceiverID` |   int  | 對方物品編號 | 否 | FK(關聯至Product表) |
| `OrderDate` |   datetime   | 訂單日期 | 否 | 預設為系統當前時間 |
| `Status` |  varchar   | 交易狀態 | 否 | 例如：待確認、已同意、已拒絕、已完成 |
---
### `ExchangeItems` -交換物品資料表
  ```sql
CREATE TABLE ExchangeItems (
    ExchangeItemID INT PRIMARY KEY AUTO_INCREMENT,
    ExchangeID INT NOT NULL,
    ProductID INT NOT NULL,
    Side VARCHAR(20) NOT NULL,

    CONSTRAINT chk_exchange_item_side
        CHECK (Side IN ('發起方', '接收方')),

    CONSTRAINT uq_exchange_product
        UNIQUE (ExchangeID, ProductID),

    FOREIGN KEY (ExchangeID) REFERENCES Exchanges(ExchangeID),
    FOREIGN KEY (ProductID) REFERENCES Product(ProductID)
) ;

  ```
| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|---------|-----------|----|--------------|
| `exchangeItemID` |   int   | 物品交換編號 | 否 | PK |
| `exchangeID`   | int | 交換編號 | 否 | FK(關聯至exchange表) |
| `ProductID` |   int  | 商品編號 | 否 | FK(關聯至Product表) |
| `side` |   varchar   | 角色 | 否 | 例如:發起方，接收方 |

---
## 關係介紹

  ![系統關係圖](NEWER112.jpg)

- category 1 —— N product：歸類
- users 1 —— N product：擁有
- product 1 —— N productimages：擁有圖片
- users 1 —— N exchanges：發起
- users 1 —— N exchanges：接收
- exchanges 1 —— N exchangeitems：包含
- product 1 —— N exchangeitems：出現在
- exchanges 1 —— N message：包含訊息
- users 1 —— N message：發送
- users 1 —— N message：接收


---

## 實際建置
user資料表建置
  ```sql
INSERT INTO Users (UserID, Name, Email, Role)
VALUES
(1, '王小明', 'ming@example.com', 'user'),
(2, '李小美', 'mei@example.com', 'user'),
(3, '黃曉風', 'feng@example.com', 'user'),
(4, '陳志豪', 'hao@example.com', 'user'),
(5, '林怡君', 'yijun@example.com', 'user'),
(6, '張家瑋', 'jiawei@example.com', 'user'),
(7, '吳佩珊', 'peishan@example.com', 'user'),
(8, '劉冠宇', 'guanyu@example.com', 'user'),
(9, '蔡雅婷', 'yating@example.com', 'user'),
(10, '管理員', 'admin@example.com', 'admin');
```
<img width="304" height="204" alt="image" src="https://github.com/user-attachments/assets/66a1910d-e8fa-4cb1-8dba-4e4fb63aa050" />

---
Category資料表建置
  ```sql
INSERT INTO Category (CategoryID, CategoryName)
VALUES
(101, '電子產品'),
(102, '玩具'),
(103, '書籍'),
(104, '衣服'),
(105, '運動用品'),
(106, '餐具'),
(107, '家具'),
(108, '生活用品'),
(109, '文具'),
(110, '收藏品');
```
<img width="197" height="212" alt="image" src="https://github.com/user-attachments/assets/5326cb8f-8647-4673-8372-9046d8ae3546" />


---
Product資料表建置
  ```sql
INSERT INTO Product (ProductID, OwnerID, CategoryID, Title, Description, Price, Status, CreatedAt)
VALUES
(1001, 1, 101, '二手相機', '功能正常，外觀有些微使用痕跡', 5000.00, '交換中', '2026-06-16 09:00:00'),
(1002, 2, 102, '小孩玩具', '保存良好，適合幼兒使用', 100.00, '交換中', '2026-06-16 09:10:00'),
(1003, 2, 103, '資料庫課本', '二手書，有少量筆記', 250.00, '交換中', '2026-06-16 09:20:00'),
(1004, 3, 104, '外套', '尺寸 M，九成新', 300.00, '交換中', '2026-06-16 09:30:00'),
(1005, 4, 101, '機械鍵盤', '按鍵正常，附 USB 線', 1200.00, '交換中', '2026-06-16 09:40:00'),
(1006, 5, 108, '後背包', '容量大，適合上課或旅行使用', 600.00, '交換中', '2026-06-16 09:50:00'),
(1007, 6, 107, '木椅', '椅腳穩固，表面有些刮痕', 800.00, '已交換', '2026-06-16 10:00:00'),
(1008, 7, 108, '檯燈', '亮度可調整，功能正常', 450.00, '已交換', '2026-06-16 10:10:00'),
(1009, 8, 105, '羽球拍', '握把稍舊，但可正常使用', 700.00, '可交換', '2026-06-16 10:20:00'),
(1010, 9, 108, '馬克杯', '全新未使用', 150.00, '可交換', '2026-06-16 10:30:00');
```
<img width="724" height="221" alt="image" src="https://github.com/user-attachments/assets/78f35e6b-1d7c-410b-af11-4c91293368ac" />

---
ProductImage資料表建置
  ```sql
INSERT INTO ProductImages (ImageID, ProductID, ImageURL)
VALUES
(2001, 1001, 'images/product_1001_camera.jpg'),
(2002, 1002, 'images/product_1002_toy.jpg'),
(2003, 1003, 'images/product_1003_book.jpg'),
(2004, 1004, 'images/product_1004_jacket.jpg'),
(2005, 1005, 'images/product_1005_keyboard.jpg'),
(2006, 1006, 'images/product_1006_bag.jpg'),
(2007, 1007, 'images/product_1007_chair.jpg'),
(2008, 1008, 'images/product_1008_lamp.jpg'),
(2009, 1009, 'images/product_1009_racket.jpg'),
(2010, 1010, 'images/product_1010_cup.jpg');

```
<img width="344" height="210" alt="image" src="https://github.com/user-attachments/assets/8c1ce60e-4da5-4e19-9624-34b0d1de5ddb" />

---
 Exchanges資料表建置
  ```sql
INSERT INTO Exchanges (ExchangeID, ProposerUserID, ReceiverUserID, OrderDate, Status)
VALUES
(5001, 2, 1, '2026-06-16 11:00:00', '待確認'),
(5002, 3, 2, '2026-06-16 11:20:00', '待確認'),
(5003, 4, 5, '2026-06-16 11:40:00', '已同意'),
(5004, 6, 7, '2026-06-16 12:00:00', '已完成'),
(5005, 8, 9, '2026-06-16 12:20:00', '已拒絕'),
(5006, 1, 2, '2026-06-16 12:40:00', '已拒絕'),
(5007, 5, 4, '2026-06-16 13:00:00', '已同意'),
(5008, 7, 6, '2026-06-16 13:20:00', '已完成'),
(5009, 9, 8, '2026-06-16 13:40:00', '已拒絕'),
(5010, 4, 1, '2026-06-16 14:00:00', '待確認');
```
<img width="464" height="210" alt="image" src="https://github.com/user-attachments/assets/1c6309e3-6207-4af5-8e2f-5a200caa9604" />

---
 ExchangeItems資料表建置
  ```sql
INSERT INTO ExchangeItems (ExchangeItemID, ExchangeID, ProductID, Side)
VALUES
(6001, 5001, 1002, '發起方'),
(6002, 5001, 1003, '發起方'),
(6003, 5001, 1001, '接收方'),
(6004, 5002, 1004, '發起方'),
(6005, 5002, 1002, '接收方'),
(6006, 5002, 1003, '接收方'),
(6007, 5003, 1005, '發起方'),
(6008, 5003, 1006, '接收方'),
(6009, 5004, 1007, '發起方'),
(6010, 5004, 1008, '接收方');
```
<img width="321" height="215" alt="image" src="https://github.com/user-attachments/assets/64ca36d2-2d34-42c0-8aed-5be7d199a4ad" />

---
Message資料表建置
  ```sql
INSERT INTO Message (MessageID, ExchangeID, SenderID, ReceiverID, Content, SentTime)
VALUES
(8001, 5001, 2, 1, '請問可以用小孩玩具和資料庫課本交換你的相機嗎？', '2026-06-16 11:02:00'),
(8002, 5001, 1, 2, '可以，請問玩具和課本狀況如何？', '2026-06-16 11:05:00'),
(8003, 5002, 3, 2, '我想用外套交換你的玩具和課本，可以嗎？', '2026-06-16 11:22:00'),
(8004, 5002, 2, 3, '可以討論，外套尺寸是 M 嗎？', '2026-06-16 11:25:00'),
(8005, 5003, 4, 5, '我想用機械鍵盤交換你的後背包。', '2026-06-16 11:42:00'),
(8006, 5003, 5, 4, '可以，鍵盤目前使用狀況如何？', '2026-06-16 11:45:00'),
(8007, 5004, 6, 7, '木椅想交換你的檯燈，可以嗎？', '2026-06-16 12:02:00'),
(8008, 5004, 7, 6, '可以，我們約時間面交。', '2026-06-16 12:05:00'),
(8009, 5005, 8, 9, '請問羽球拍可以交換馬克杯嗎？', '2026-06-16 12:22:00'),
(8010, 5005, 9, 8, '不好意思，目前不想交換馬克杯。', '2026-06-16 12:25:00');
```
<img width="673" height="203" alt="image" src="https://github.com/user-attachments/assets/3ebf342c-b6fe-4472-a66c-9a9a4838f5b0" />

---
## view
view查看某筆交換包含哪些商品
  ```sql
CREATE VIEW View_User_AvailableProducts AS
SELECT
    p.ProductID,
    p.Title,
    p.Description,
    p.Price,
    p.Status,
    p.CreatedAt,
    u.UserID AS OwnerID,
    u.Name AS OwnerName,
    c.CategoryName,
    pi.ImageURL
FROM Product p
JOIN Users u ON p.OwnerID = u.UserID
JOIN Category c ON p.CategoryID = c.CategoryID
LEFT JOIN ProductImages pi ON p.ProductID = pi.ProductID
WHERE p.Status = '可交換';
```


<img width="633" height="360" alt="image" src="https://github.com/user-attachments/assets/57f5d2d9-f7b9-435d-bdd3-d611d4c30c64" />


這裡只查USER1跟2的交易
可以看到李小美想要用小孩玩具與資料庫課本向王小明交換相機

---
view後台統計
  ```sql
CREATE VIEW View_Admin_DashboardSummary AS
SELECT
    (SELECT COUNT(*) FROM Users) AS TotalUsers,
    (SELECT COUNT(*) FROM Product) AS TotalProducts,
    (SELECT COUNT(*) FROM Category) AS TotalCategories,
    (SELECT COUNT(*) FROM Exchanges) AS TotalExchanges,
    (SELECT COUNT(*) FROM Message) AS TotalMessages,
    (SELECT COUNT(*) FROM Product WHERE Status = '可交換') AS AvailableProducts,
    (SELECT COUNT(*) FROM Product WHERE Status = '交換中') AS ExchangingProducts,
    (SELECT COUNT(*) FROM Exchanges WHERE Status = '待確認') AS PendingExchanges,
    (SELECT COUNT(*) FROM Exchanges WHERE Status = '已完成') AS CompletedExchanges;
```


<img width="906" height="50" alt="image" src="https://github.com/user-attachments/assets/41cde18a-acca-45db-abbf-a653fb9ad94c" />

可以看到各項數據統計，例如使用者數量 商品數量。

---
view查看可交換物品(user) 
 ```sql
CREATE VIEW View_User_AvailableProducts AS
SELECT
    p.ProductID,
    p.Title,
    p.Description,
    p.Price,
    p.Status,
    p.CreatedAt,
    u.UserID AS OwnerID,
    u.Name AS OwnerName,
    c.CategoryName,
    pi.ImageURL
FROM Product p
JOIN Users u ON p.OwnerID = u.UserID
JOIN Category c ON p.CategoryID = c.CategoryID
LEFT JOIN ProductImages pi ON p.ProductID = pi.ProductID
WHERE p.Status = '可交換';
```
<img width="921" height="68" alt="image" src="https://github.com/user-attachments/assets/0cfb6653-4c2a-45ea-b991-5f215a4cc746" />

---
