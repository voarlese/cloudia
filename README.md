# Cloudia

##### 建立資料表
```sql
create database cloudia;
```

##### 支援UTF-8
```sql
alter database cloudia character set UTF8;
```

##### 選擇資料庫
```sql
use cloudia;
```
##### 表格規劃
* 每次啟動時編號 用於區分每次auto
* 單次抽卡紀錄 (抽到哪幾張、LEVEL統計)

###### 卡片資料
```mysql
create table card(
   card_no BIGINT,
   name VARCHAR(20),
   img_Name CHAR(10),
   level_No BIGINT,
   type_No BIGINT,
   PRIMARY KEY ( card_no )
);
```
| 型態 | 名稱 |
| ------------- | ------------- |
| long | card_no |
|char(10)|img_name|
| varcher | name |
| int | level_no |
| int | type_no |
---
###### 等級表
```mysql
create table cardLevel(
   level_no BIGINT PRIMARY KEY,
   level_name CHAR(4)
);
```

|型態 | 名稱 |
| ------------ | ------------ |
| int | level_no |
| char(4) | level_name |
---
###### 卡片類別表
```mysql
create table cardType(
   type_no bigint PRIMARY KEY,
   type_name CHAR(4)
);
```
|型態 | 名稱 |
| ------------ | ------------ |
| int | typeNo |
| char(4) | typeName |
---
###### 單次(pick)抽卡表
```mysql
create table pick(
   pick_no BIGINT PRIMARY KEY,
   order_no BIGINT,
   card_no BIGINT,
   pick_time DATETIME
);
```
|型態 | 名稱 |
| ------------ | ------------ |
| bigint | pick_no |
| bigint | order_no|
| bigint | card_no |
| timestamp | time |
---
###### 訂單(auto)表
```mysql
create table autoOrder(
   order_no BIGINT PRIMARY KEY,
   order_time DATETIME
);
```
|型態 | 名稱 |
| ------------ | ------------ |
| long | order_no |
| DATETIME | order_time |
---
###### 卡片圖片表
```mysql
create table cardImage(
   id int auto_increment PRIMARY KEY,
   card_no bigint,
   img_name varchar(25),
   coordinate_x DOUBLE,
   coordinate_y DOUBLE
create table cardImage(
   id int auto_increment PRIMARY KEY,
   card_no bigint,
   img_name varchar(25),
   coordinate_x DOUBLE,
   coordinate_y DOUBLE
);
```
|型態 | 名稱 |
| ------------ | ------------ |
| INT | id |
|bigint(20)|card_no|
|varchar(25)|img_name|
|double|coordinate_x|
|double|coordinate_y|

### 查詢語法
#### 指定ORDER 抽卡總數
```sql
select count(A.pick_count) from (select pick_no, count(pick_no) as pick_count, order_no from PICK where order_no = '1611649909449' group by pick_no) as A;
```
#### 查詢 每個pick 包含多少個 3 聖物 
```sql
select C.pick_no, count(C.pick_no), C.pick_time from (select A.pick_no, A.pick_time, B.name, B.card_no from PICK as A inner join CARD as B on A.card_no = B.card_no where A.order_no = '1610817121701' and (B.card_no = '26' or B.card_no ='17' or B.card_no = '4')) as C group by C.pick_no;
```
#### 查詢全部SSR每十抽出現次數
```sql
select C.pick_no, count(C.pick_no) from (select A.*, B.name, B.img_name from PICK as A inner join CARD as B on A.card_no = B.card_no where A.order_no = '1610817121701' and B.card_no in('4', '9', '17', '26', '31', '38', '41', '55', '60')) as C group by C.pick_no;
```
#### 查詢指定ORDER 每次十抽SSR出現次數
```sql
select C.pick_no, count(C.pick_no) from (select A.*, B.name from PICK as A inner join CARD as B on A.card_no = B.card_no where A.order_no = '1611721712574' and B.card_no in('4', '9', '17', '26', '31', '38', '41', '55', '60')) as C group by C.pick_no;
```
#### 單次ORDER 每次十抽 判斷出來卡片數量
```sql
select A.pick_count, A.pick_no from (select pick_no, count(pick_no) as pick_count, order_no from PICK where order_no = '1611721712574' group by pick_no) as A;
```

#### 統計單卡出現機率
```sql
select A.card_no, B.name, count(A.card_no) as 出現次數, 30400 as Total, (count(A.card_no)/33040) * 100 as 機率, B.level_name, B.type_name from PICK as A inner join (select A.*, B.level_name, C.type_name from CARD as A, CARD_LEVEL as B, CARD_TYPE as C where A.level_no = B.level_no and A.type_no = C.type_no) as B on A.card_no = B.card_no where A.order_no = '1610817121701' group by A.card_no order by 機率;
```
#### 查詢單次十抽抽中卡片
```sql
select A.*, B.level_name, C.type_name, D.pick_no from CARD as A, CARD_LEVEL as B, CARD_TYPE as C, PICK as D where A.level_no = B.level_no and A.type_no = C.type_no and A.card_no = D.card_no and D.pick_no = '1611723910413';
```

#### 卡片資料
```sql
select A.*, B.level_name, C.type_name, D.img_name, D.coordinate_x, D.coordinate_y from CARD as A, CARD_LEVEL as B, CARD_TYPE as C, cardImage as D where A.level_no = B.level_no and A.type_no = C.type_no and A.card_no and A.card_no = D.card_no order by A.card_no;
```
