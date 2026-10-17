# 🚀 SQL Portfolio Project: Customer & Order Data Analysis

Bu projede, perakende satış verilerini (`CUSTOMERS` ve `ORDERS` tabloları) kullanarak uçtan uca bir veri analizi gerçekleştirdim. Proje boyunca temel SQL sorgularından orta seviye analitik sorgulara (`JOIN`, `GROUP BY`, `Subquery`, `CASE WHEN`) kadar pek çok yetkinliği pratik ettim.

---

## 📊 Kullanılan Teknolojiler & Beceriler
* **Veritabanı Yönetim Sistemi:** Microsoft SQL Server (MSSQL)
* **Temel & Orta Seviye SQL Komutları:**
  * `INNER JOIN` (Tabloları birleştirme)
  * `GROUP BY` & `SUM() / AVG()` (Özetleme ve gruplama)
  * `Subquery` (Dinamik alt sorgular ile filtreleme)
  * `CASE WHEN` (Koşullu sınıflandırma ve segmentasyon)
  * `WHERE` & `ORDER BY` (Filtreleme ve sıralama)

---

## 🔍 SQL Sorguları ve İş Analizleri (Insights)

### 1️⃣ Sorgu 1: Müşteri Sipariş ve Harcama Özeti
Müşterilerin toplam sipariş adetlerini ve toplam harcama tutarlarını listeleyen temel sorgu.
SQL
SELECT CUSTOMERS.CUSTOMER_NAME, CUSTOMERS.CITY, COUNT(ORDERS.ORDER_ID) AS TOTAL_ORDERS, SUM(CASE WHEN ORDERS.ORDER_STATUS = 'Delivered' THEN ORDERS.UNIT_PRICE ELSE 0 END) AS TOTAL_DELIVERED_SPEND FROM CUSTOMERS JOIN ORDERS ON CUSTOMERS.CUSTOMER_ID = ORDERS.CUSTOMER_ID GROUP BY CUSTOMERS.CUSTOMER_NAME, CUSTOMERS.CITY ORDER BY SUM(CASE WHEN ORDERS.ORDER_STATUS = 'Delivered' THEN ORDERS.UNIT_PRICE ELSE 0 END) DESC ;

"Sorgu 1 sonucuna göre İzmir bölgesinden Zeynep Demir ve Can Arslan en yüksek toplam harcamayı yapan müşterilerdir. Ayşe Yılmaz ve Mehmet Kaya en çok sipariş adedine (3 sipariş) sahip olmalarına rağmen harcama tutarları teslim edilen sipariş türlerine göre değişkenlik göstermektedir."

2️⃣ Sorgu 2: Şehir ve Kategori Bazlı Gelir Dağılımı
Sipariş durumlarına (Delivered vs Cancelled) göre şehir ve kategorilerin gelir performansının incelenmesi.
SQL
SELECT CUSTOMERS.CITY, ORDERS.PRODUCT_CATEGORY, COUNT(ORDERS.ORDER_ID) AS TOTAL_ORDERS, SUM(CASE WHEN ORDERS.ORDER_STATUS = 'Delivered' THEN ORDERS.UNIT_PRICE ELSE 0 END) AS TOTAL_DELIVERED_SPEND, SUM(CASE WHEN ORDERS.ORDER_STATUS = 'Cancelled' THEN ORDERS.UNIT_PRICE ELSE 0 END) AS TOTAL_CANCELLED_SPEND FROM CUSTOMERS JOIN ORDERS ON CUSTOMERS.CUSTOMER_ID = ORDERS.CUSTOMER_ID GROUP BY CUSTOMERS.CITY, ORDERS.PRODUCT_CATEGORY ORDER BY TOTAL_DELIVERED_SPEND DESC ;

Sorgu 2 sonucuna göre; Ankara bölgesinde Electronics kategorisi 5.500 TL teslim edilen ciro ile en yüksek satışı gerçekleştiren grup olmuştur. İzmir bölgesinde Electronics (4.700 TL) ve Home (1.300 TL) kategorilerindeki tüm siparişler başarıyla teslim edilirken, Antalya bölgesindeki Electronics siparişlerinde 1.600 TL ve Ankara Home kategorisinde 801 TL tutarında iptaller gerçekleşmiştir.

3️⃣ Sorgu 3: Ortalama Sipariş Tutarının Üzerindeki Siparişler
Teslim edilmiş siparişler arasında genel ortalama tutarın üzerinde harcama yapılan siparişlerin alt sorgu (Subquery) yardımıyla filtrelenmesi.
SQL
SELECT CUSTOMERS.CUSTOMER_NAME, ORDERS.PRODUCT_CATEGORY, ORDERS.UNIT_PRICE FROM ORDERS JOIN CUSTOMERS ON CUSTOMERS.CUSTOMER_ID = ORDERS.CUSTOMER_ID WHERE ORDERS.ORDER_STATUS = 'Delivered' AND ORDERS.UNIT_PRICE > (SELECT AVG(ORDERS.UNIT_PRICE) FROM ORDERS WHERE ORDER_STATUS = 'Delivered') ORDER BY ORDERS.UNIT_PRICE DESC ;

Sorgu 3 sonucuna göre; Teslim edilen siparişlerin genel ortalama tutarının üzerinde harcama yapan siparişler filtrelenmiş; Zeynep Demir (2.500 TL), Can Arslan (2.200 TL) ve Ece Kurt (2.100 TL) gibi müşteriler yüksek tutarlı siparişleriyle öne çıkan isimler olmuştur.

4️⃣ Sorgu 4: Müşteri Harcama Seviyelerinin Sınıflandırılması (Segmentasyon)
CASE WHEN yapısı kullanılarak müşterilerin toplam harcamalarına göre High Spender, Medium Spender ve Low Spender şeklinde segmentlere ayrılması.
SQL
SELECT CUSTOMERS.CUSTOMER_NAME, CUSTOMERS.CITY, SUM(ORDERS.UNIT_PRICE) AS TOTAL_SPENT, CASE WHEN SUM(ORDERS.UNIT_PRICE) >= 2000 THEN 'HIGH SPENDER' WHEN SUM(ORDERS.UNIT_PRICE) BETWEEN 1000 AND 2000 THEN 'MEDIUM SPENDER' ELSE 'LOW SPENDER' END AS SPENDER_LEVELS FROM CUSTOMERS JOIN ORDERS ON CUSTOMERS.CUSTOMER_ID = ORDERS.CUSTOMER_ID GROUP BY CUSTOMERS.CUSTOMER_NAME, CUSTOMERS.CITY ORDER BY SPENDER_LEVELS DESC ;

Sorgu 4 sonucuna göre; Müşterilerin teslim edilen sipariş toplamlarına göre yapılan segmentasyonda; Deniz Aksoy, Elif Sarı, Zeynep Demir ve Can Arslan gibi müşteriler yüksek harcama tutarlarıyla 'High Spender' kategorisinde yer alarak en değerli müşteri segmentini oluşturmuştur. Merve Celik ve Selin Öz gibi müşteriler ise 'Medium Spender' grubunda yer almıştır.

