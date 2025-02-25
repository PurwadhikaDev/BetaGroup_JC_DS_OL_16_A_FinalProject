# BetaGroup_JC_DS_OL_16_A_FinalProject

<a id="readme-top"></a>


<h3 align="center">E-COMMERCE CUSTOMER CHURN ANALYSIS</h3>

  <p align="center">
    
    TEAM BETA:
      - MUHAMMAD MAHARDIKA RENALDI
      - MUHAMMAD DAUD
      - MUH FAIS HIDAYAH
  </p>
</div>


<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#1.-business-problem-and-data-understanding">Business Problem and Data Understanding</a>
      <ul>
        <li><a href="#1.1. context">Context</a></li>
        <li><a href="#1.2. Revenue-Impact-Analysis">Revenue Impact Analysis</a></li>
        <li><a href="#1.3. Problem-Statement">Problem Statement</a></li>
        <li><a href="#1.4. Goals">Goals</a></li>
        <li><a href="#1.5. analytic-approach">Analytic Approach</a></li>
        <li><a href="#1.6. metric-evaluation">Metric Evaluation</a></li>
        <li><a href="#1.7. data-understanding">Data Understanding</a></li>
        <li><a href="#1.8. Attribute-Information">Attribute Information</a></li>
      </ul>
    </li>
    <li>
      <a href="#2. exploratory-data-analysis">Exploratory Data Analysis</a>
      <ul>
        <li><a href="#summary-data">Summary Data</a></li>
        <li><a href="#data-analysis">Data Analysis</a></li>
      </ul>
    </li>
        <li>
      <a href="#3.data-preprocessing">Data Preprocessing</a>
    </li>
    <li>
      <a href="#tableau-dashboard">Tableau Dashboard</a>
    </li>
    <li>
      <a href="#modeling-and-evaluation">Modeling and Evaluation</a>
      <ul>
        <li><a href="#modeling">Modeling</a></li>
        <li><a href="#resampling">Resampling</a></li>
        <li><a href="#coefficient">Coefficient</a></li>
        <li><a href="#tuning">Tuning</a></li>
        <li><a href="#confusion-matrix">Confusion Matrix</a></li>
      </ul>
    </li>
    <li><a href="#conclusion-and-recommendation">Conclusion & Recommendation</a>
   <ul>
        <li><a href="#1.1. analisis-dampak-bisnis">Analisis Dampak Bisnis</a></li>
        <li><a href="#1.2. conclusion">Conclusion</a></li>
        <li><a href="#1.3. Recomendation">Recomendation</a></li>
      </ul>
    </li>
  </ol>
</details>


## __1. BUSSINES PROBLEM AND DATA UNDERSTANDING__
### __1.1 Context__

TechStyle merupakan platform e-commerce yang berfokus pada penjualan produk elektronik dan gadget, dengan model bisnis yang mengintegrasikan sistem membership dan marketplace. Didirikan pada tahun 2020, TechStyle telah berkembang menjadi salah satu pemain utama di segmen e-commerce teknologi di Indonesia, dengan lebih dari 5,600 pelanggan aktif (4,682 non-churn dan 948 churn) dan jaringan warehouse yang tersebar di berbagai kota besar.

Platform ini mengadopsi model bisnis yang mirip dengan Bhinneka, di mana kami memiliki kontrol langsung atas inventory melalui sistem warehouse terintegrasi, terutama untuk kategori produk elektronik premium. Namun, kami juga mengakomodasi seller pihak ketiga untuk kategori produk pendukung seperti aksesoris dan peripheral, menciptakan ekosistem marketplace yang komprehensif.

#### 1.1.1. Sistem Membership dan Revenue Stream

TechStyle mengoperasikan sistem membership berbasis subscription bulanan dengan biaya berlangganan yang harus dibayarkan setiap awal periode. Pendapatan tetap dari membership fee ini menjadi salah satu revenue stream utama yang kemudian dikonversi menjadi berbagai benefit member, termasuk program cashback yang rata-rata mencapai $180.635 per bulan untuk member aktif.

Sistem tier membership TechStyle didasarkan pada konsep "continuous membership tenure", dimana:

#### 1.1.2. Tier Structure:

1. Basic Member (0-3 bulan)
    - Cashback rate: 5%
    - Voucher bulanan senilai $10
    - Gratis ongkir minimal pembelian $100

2. Silver Member (3-6 bulan)
    - Cashback rate: 5%
    - Voucher bulanan senilai $15
    - Gratis ongkir minimal pembelian $75
    - Akses flash sale khusus

3.  Gold Member (6-12 bulan)
    - Cashback rate: 5%
    - Voucher bulanan senilai $25
    - Gratis ongkir minimal pembelian $50
    - Prioritas customer service
    - Exclusive early access untuk produk baru

4. Platinum Member (>12 bulan)
    - Cashback rate: 5%
    - Voucher bulanan senilai $50
    - Gratis ongkir tanpa minimal pembelian
    - Dedicated customer service
    - Priority waitlist untuk produk limited
    - Special birthday rewards


#### 1.1.3. Tenure Reset Policy:

- Tenure dihitung berdasarkan masa berlangganan kontinyu
- Jika member berhenti berlangganan (churn), maka ketika bergabung kembali tenure dimulai dari 0
- Tidak ada sistem "tenure freezing" - setiap putus berlangganan akan mereset hitungan
- Benefit tier tidak dapat diakumulasi dari periode membership yang terputus


#### 1.1.4. Kebijakan tenure reset ini dirancang untuk:

- Mendorong loyalitas jangka panjang
- Memaksimalkan customer lifetime value
- Meminimalisir churn dengan insentif progresif
- Memberikan nilai tambah yang signifikan untuk long-term members


Status churn bersifat binary dan final dalam satu periode - sekali seorang member terklasifikasi sebagai churn, mereka harus memulai dari Basic Member jika ingin bergabung kembali. Meski pelanggan yang churn masih dapat bertransaksi sebagai regular customer, mereka kehilangan akses ke benefit membership yang bernilai signifikan.


Dengan model bisnis ini, prediksi dan pencegahan churn menjadi sangat kritis untuk mempertahankan:
- Revenue stream dari membership fee
- Tingkat engagement dan frekuensi transaksi yang lebih tinggi
- Nilai transaksi yang lebih besar dari member dengan tier tinggi
- Efisiensi program marketing dan retention

### 1.2. Revenue Impact Analysis

#### 1.2.1. Asumsi Dasar
1. **Membership Fee Structure:**
   - $199/bulan

2. **Transaction Pattern:**

         Non-Churn (Active Members):
         - Rata-rata CashbackAmount: $180.635
         - Rata-rata OrderCount: 3.047 orders/bulan

         Churn:
         - Rata-rata CashbackAmount: $160.371
         - Rata-rata OrderCount: 2.824 orders/bulan
#### __Per Customer Impact__
- Non_Churn_Revenue(Member) = $180.635 / 0.05 = $3,612.70/month
- Churn_Revenue(Non Member) = $160.371 / 0.05 = $3,207.42/month
- Revenue_Loss_per_Customer = $3,612.70 - $3,207.42 = $405.28/month
- Fee Membership = $199/month 

#### __Total Impact__
- Total_Monthly_Loss = $405.28 * 948 = $384,210.65/month
- Fee Membership = $199 * 948 = $188,652/month
- Annual_Revenue_Loss:
  - $384,210.65 * 12 = $4,610,527.80/year
  - $188,652 * 12 = $2,263,824/year
- Total Revenue Loss = $4,610,527.80 + $2,263,824 = $6,874,351.8/year

### __1.3. Problem Statement__
TechStyle, sebagai platform e-commerce yang berfokus pada penjualan produk elektronik dan gadget, menghadapi tantangan kritis dalam memprediksi dan mencegah churn pelanggan untuk mempertahankan aliran pendapatan dan keterlibatan pelanggan. Dengan model bisnis berbasis membership, TechStyle menawarkan berbagai tingkatan membership yang memberikan manfaat berbeda untuk mendorong loyalitas jangka panjang. Platform ini memiliki lebih dari 5.600 pelanggan aktif, dengan 4.682 pelanggan non-churn dan 948 pelanggan churn. Perusahaan bertujuan untuk meminimalkan churn guna mempertahankan pendapatan dari biaya keanggotaan dan nilai transaksi tinggi dari pelanggan loyal.

Fokus utama adalah mengembangkan model prediktif untuk mengidentifikasi potensi churner secara akurat. Model tersebut harus meminimalkan Type 2 errors (False Negatives), dimana pelanggan yang sebenarnya churn tidak diprediksi akan churn, karena hal ini memiliki dampak finansial jangka panjang yang signifikan. Sebaliknya, Type 1 errors (False Positives) menyebabkan biaya retensi yang tidak perlu tetapi memiliki dampak yang lebih kecil. Tujuannya adalah mengoptimalkan model untuk mengurangi False Negatives, sehingga lebih banyak pelanggan yang dapat dipertahankan dan memaksimalkan nilai pelanggan seumur hidup (Customer Lifetime Value/CLV).

### __1.4. Goals__
Sebagai seorang data scientist, kami akan mencoba untuk membantu perusahaan dalam:
1. Memprediksi seakurat mungkin apakah sebuah customer berpotensi untuk melakukan churn (Berhenti Membeship) atau tidak.
2. Mengetahui pola dari customer yang churn dan variabel apa saja yang mempengaruhi churn
3. Meningkatkan retensi pelanggan, Dengan mengetahui siapa pelanggan yang berpotensi untuk berhenti berlangganan membership sehingga perusahaan dapat merancang strategi retensi untuk mencegah churn dan mempertahankan member yang sudah ada.
4. Menurunkan churn rate hingga dibawah 10%

### __1.5. Analytical Approach__
langkah pertama yaitu dengan melakukan analysis data untuk menemukan pola dan karakteristik bagi customer yang berpotensi untuk churn. kemudian membuat model machine learning dengan metode klasifikasi berdasarkan fitur-fitur yang tersedia, dan melakukan evaluasi terhadap model tersebut

### __1.6. Metric Evaluation__
Karena Fokus Utama kita adalah customer yang berpotensi untuk churn, maka target yang akan kita tetapkan adalah sebagai berikut:

- 0 : customer tidak churn
- 1 : customer churn  

- Type 1 error : False Positive (customer yang aktualnya tidak melakukan churn tetapi di prediksi melakukan churn)
Konsekuensi : potensi pengeluaran biaya retensi yang sia-sia karena E Commerce akan mengeluarkan biaya untuk customer yang sebenarnya tidak churn

- Type 2 error : False Negative (customer yang aktualnya melakukan churn tetapi di prediksi tidak churn)
Konsekuensi : E Commerce kehilangan pelanggan dan berpotensi kehilangan keuntungan jangka panjang dari Customer(CLV)

#### 1.6.1. Asumsi Dasar dan Kalkulasi
Berdasarkan penelitian industri e-commerce dan subscription-based business, kita menggunakan asumsi:
- Customer Acquisition Cost (CAC): $300 (rata-rata dari range $200-400)
- Retention Cost: $60 (20% dari CAC)
- Cashback rate seragam untuk semua tier: 5%
- Membership fee: $199 per bulan

#### 1.6.2. Skenario Impact Type Error

1. Skenario 1: False Positive (Type 1 Error)
    - Customer A tidak berencana churn namun diprediksi akan churn:
        - Kondisi:
            - Customer masih aktif dan profitable
            - Model memprediksi akan churn
            - E-commerce melakukan tindakan retensi

        - Impact:
            1. Biaya Retensi yang Terbuang = $60 USD
            2. Tidak ada kehilangan revenue karena customer tetap aktif
            3. Potensi customer experience terganggu karena tindakan retensi yang tidak perlu

        - Total Loss = $60 USD (biaya retensi)

2. Skenario 2: False Negative (Type 2 Error)
    - Customer B akan churn namun diprediksi tidak akan churn:
        - Kondisi:
            - Customer berencana churn
            - Model gagal memprediksi
            - Tidak ada tindakan retensi yang dilakukan

        - Impact:
            1. Kehilangan profit Bulanan = $405.28 + $199 = $604.28
            2. Kehilangan potential membership fee
            3. Penurunan customer base
            4. Potensi dampak word-of-mouth negatif

        - Total Loss = $604.28 USD (profit loss) + potential long-term impact


#### 1.6.3. Perbandingan Severity Error

Type 1 Error (False Positive):
- Impact: $60 loss per case
- Karakteristik: Short-term financial loss
- Risk Level: Medium

Type 2 Error (False Negative):
- Impact: $604.28 loss per case
- Karakteristik: Long-term revenue & customer loss
- Risk Level: High

#### 1.6.4. Kesimpulan
- Berdasarkan analisis di atas:

    - Type 2 Error (False Negative) memiliki dampak 10x lebih besar dari Type 1 Error(False Positive)
    - Fokus model seharusnya pada minimalisasi False Negative
    - Metrik evaluasi yang tepat adalah Recall untuk mengurangi False Negative

#### __1.7. Data Understanding__
__Dataset source__ : https://www.kaggle.com/datasets/ankitverma2010/ecommerce-customer-churn-analysis-and-prediction

__About Dataset__ :  Kumpulan data tersebut merupakan milik sebuah perusahaan e-commerce online terkemuka. Sebuah perusahaan ritel online (e-commerce) ingin mengetahui pelanggan yang akan berhenti berlangganan, sehingga mereka dapat mendekati pelanggan untuk menawarkan beberapa promo.

#### __1.8. Attribute Information__

| Data Type | Attribute | Description |
| --- | --- | --- |
int64 | CustomerID | Unique customer ID |
int64 | Churn | Churn Flag |
| float64 | Tenure | Tenure of customer in organization |
| object | PreferredLoginDevice | Preferred login device of customer |
| int64 | CityTier | City tier |
| float64 | WarehouseToHome | Distance in between warehouse to home of customer |
| object | PreferredPaymentMode | Preferred payment method of customer |
| object | Gender | Gender of customer |
| float64 | HourSpendOnApp | Number of hours spend on mobile application or website |
| int64 | NumberOfDeviceRegistered | Total number of deceives is registered on particular customer |
| object | PreferedOrderCat | Preferred order category of customer in last month |
| int64 | SatisfactionScore | Satisfactory score of customer on service |
| object | MaritalStatus	| Marital status of customer |
| int64 | NumberOfAddress | Total number of added added on particular customer |
| int64 | Complain | Any complaint has been raised in last month |
| float64 | OrderAmountHikeFromlastYear | Percentage increases in order from last year |
| float64 | CouponUsed | Total number of coupon has been used in last month |
| float64 | OrderCount | Total number of orders has been places in last month |
| float64 | DaySinceLastOrder | Day Since last order by customer |
| float64 | CashbackAmount | Average cashback in last month |             float64

## __2. EXPLORATORY DATA ANALYSIS (EDA)__

Pada Tahap EDA kami melakukan
- Pengecekan Data Duplikat
- Pengecekan Data Outlier
- Pengecekan Missing dan inconsistent Value
- Melukan descriptive statistic
- Data Analysis
  1. Pengecekan Hubungan antar variabel
  2. Analisis Hubungan Churn dengan setiap variabel baik numerik maupun categorical
  3. Pengecekan Dsitribusi Data


## __3. Data Pre-Processing__

Pada Tahap ini yang kami lakukan adalah
- Label Encoding
- One Hot Encoding
- Data Splitting (80:20)

## __4. Modelling and Evaluation__

<!-- Modeling and Tuning -->
### Modeling and Tuning
#### Modeling
Kami menggunakan beberapa model, yaitu: 
- logreg = LogisticRegression()
- knn = KNeighborsClassifier()
- dt = DecisionTreeClassifier()
- rf = RandomForestClassifier()
- xgb = XGBClassifier()
- lgbm = LGBMClassifier()

## __5. Hyperparameter Tuning__

Hyperparameter tuning adalah proses mencari nilai optimal dari hyperparameter suatu model machine learning untuk memperbaiki performa model machine learning Ini dilakukan dengan mencoba berbagai nilai hyperparameter dan membandingkan hasil mereka dengan metrik performa seperti akurasi, presisi, recall atau F1 score. Proses ini dapat menjadi rumit dan membutuhkan banyak waktu, tetapi hasilnya dapat meningkatkan performa model machine learning secara signifikan.

Pada model kali ini yang dilakukan tuning adalah 3 model dengan performa teratas yaitu
- lgbm = LGBMClassifier()
- xgb = XGBClassifier()
- dt = DecisionTreeClassifier()


## __6. Analisis Dampak Bisnis E-Commerce Customer Churn Prediction__

### __6.1 Pendahuluan__
Project churn prediction yang telah kita kembangkan menunjukkan hasil yang sangat menjanjikan dengan tingkat akurasi 98%. Model ini secara signifikan dapat membantu TechStyle dalam mengidentifikasi pelanggan yang berpotensi churn dengan presisi tinggi. Dibandingkan dengan praktik industri yang umumnya mencapai akurasi 70-80%, performa model kita jauh melampaui benchmark tersebut.

### __6.2 Analisis Model Performance__
Model LGBM yang telah kita tune menunjukkan performa yang exceptional dengan precision 0.95 dan recall 0.96 untuk kelas churn. Ini berarti dari 100 pelanggan yang diprediksi akan churn, 95 diantaranya merupakan prediksi yang akurat, dan model berhasil mengidentifikasi 96% dari total pelanggan yang benar-benar akan churn.

### __6.3 Analisis Dampak Finansial__

#### __Scenario Tanpa Model__
Tanpa menggunakan model prediksi, TechStyle harus melakukan intervensi retensi kepada seluruh pelanggan yang berjumlah 1,126. Dengan biaya retensi sebesar $60 per pelanggan (berdasarkan standar industri dari HubSpot Research 2024), total biaya yang harus dikeluarkan adalah:
- Total Biaya = 1,126 × $60 = $67,560 per bulan
- Annual Cost = $810,720

#### __Scenario Dengan Model__
Dengan menggunakan model prediksi kita:
1. Jumlah pelanggan yang diprediksi churn: 192 pelanggan
   - True Positives: 182 pelanggan (96% dari 190 aktual churn)
   - False Positives: 10 pelanggan (1% dari 936 non-churn)
2. Biaya Intervensi = 192 × $60 = $11,520 per bulan
   - Annual Cost = $138,240
3. Penghematan Biaya = $67,560 - $11,520 = $56,040 per bulan
   - Annual Savings = $672,480

### __6.3 Analisis Revenue Impact__

#### __Potential Loss dari Churn__
Berdasarkan analisis data, kita mengetahui:
1. Rata-rata revenue per member loyal: $3,612.70 per bulan
2. Rata-rata revenue member yang churn: $3,207.42 per bulan
3. Delta revenue per member: $405.28
4. Membership fee: $199 per bulan

Potential monthly loss dari 190 member churn:
- Transaction Value Loss = $405.28 × 190 = $77,003.20
- Membership Fee Loss = $199 × 190 = $37,810.00
- Total Monthly Loss = $114,813.20
- Annual Loss = $1,377,758.40

#### __Revenue Saved dengan Model__
Dengan model, kita dapat mengintervensi 182 dari 190 member yang akan churn:
- Transaction Value Saved = $405.28 × 182 = $73,760.96
- Membership Fee Saved = $199 × 182 = $36,218.00
- Total Monthly Saved = $109,978.96
- Annual Saved = $1,319,747.52

#### __ROI Analysis__

| Scenario | Monthly Cost | Monthly Benefit | Net Monthly Gain | Annual ROI |
|----------|--------------|----------------|------------------|------------|
| Tanpa Model | $67,560 | $114,813.20 | $47,253.20 | 69.94% |
| Dengan Model | $11,520 | $109,978.96 | $98,458.96 | 854.68% |

### __6.4 Kesimpulan & Rekomendasi__

1. Model prediksi churn LGBM yang telah kita kembangkan menunjukkan performa exceptional dengan akurasi 98%, precision 0.95, dan recall 0.96.

2. Implementasi model akan memberikan efisiensi biaya yang signifikan:
   - Pengurangan biaya retensi sebesar 82.95% ($67,560 → $11,520)
   - Peningkatan net benefit sebesar 108.36% ($47,253.20 → $98,458.96)
   - ROI yang jauh lebih tinggi (854.68% vs 69.94%)

3. Rekomendasi Strategis:
   - Implementasikan model sebagai bagian dari sistem early warning churn
   - Kembangkan program retensi yang berbeda berdasarkan tier membership
   - Prioritaskan intervensi untuk Basic Members (0-3 bulan) yang memiliki churn rate tertinggi (41.86%)
   - Fokuskan pada penanganan komplain dan peningkatan cashback program

4. Next Steps:
   - Deploy model ke production environment
   - Integrasikan dengan sistem CRM
   - Develop dashboard monitoring real-time
   - Establish SOP untuk intervensi berdasarkan prediksi model

## __7. CONCLUSION__
### __7.1 Conclusion Data Analysis__

Berikut ini Faktor yang memiliki pengaruh kuat terhadap member untuk melakukan churn atau tidak melanjutkan membership:

- Pelanggan dengan tenure lebih singkat cenderung memiliki potensi yang lebih tinggi untuk melakukan churn dibandingkan dengan pelanggan dengan tenure lebih lama. Ini menunjukkan bahwa semakin lama pelanggan berlangganan, semakin kecil kemungkinan mereka untuk churn.
- Pelanggan yang mengajukan komplain lebih rentan untuk churn, yang bisa mengindikasikan bahwa keluhan mereka tidak ditangani dengan baik atau mereka kecewa dengan layanan yang diterima.
- Pelanggan yang menerima cashback lebih besar tampaknya lebih cenderung tetap bertahan. Pelanggan churn lebih umum di kelompok dengan cashback lebih rendah.
- Pelanggan dengan jarak lebih jauh dari gudang cenderung memiliki tingkat churn yang lebih tinggi dibandingkan dengan pelanggan yang lebih dekat ke gudang. Ini menunjukkan bahwa faktor jarak memiliki pengaruh signifikan terhadap churn pelanggan.
- Terdapat hubungan signifikan antara PreferredPaymentMode dan churn. Pelanggan yang menggunakan Cash on Delivery (COD) dan E-wallet memiliki tingkat churn yang lebih tinggi dibandingkan dengan pelanggan yang menggunakan metode pembayaran lain seperti Credit Card, Debit Card, atau UPI.
- Terdapat gap yang sangat besar antara churn rate di Basic Member dengan tier-tier lainnya, menunjukkan bahwa 3 bulan pertama adalah periode kritis dalam journey pelanggan TechStyle. Sementara itu, terdapat anomali kecil di Gold Member yang memiliki churn rate lebih tinggi dibandingkan Silver Member.
    * Basic Member: Churn rate tertinggi (41.9%) dengan 1,560 total member
    * Silver Member: Churn rate 7.5% dengan 590 total member
    * Gold Member: Churn rate 9.8% dengan 1,584 total member
    * Platinum Member: Churn rate terendah (5.0%) dengan 1,896 total member

### __7.2 Conclusion Model LGBM__
Accuracy (0.98)
- Dari total 1,126 member dalam data testing, model berhasil memprediksi dengan tepat sebanyak 1,108 kasus
- Terdiri dari 926 member yang tepat diprediksi tidak churn dan 182 member yang tepat diprediksi churn
- Ini menunjukkan tingkat akurasi keseluruhan model sebesar 98%

Recall
1. Recall untuk Non-Churn (0.99)
- Dari 936 member yang sebenarnya tidak churn, 926 member berhasil diprediksi dengan tepat sebagai non-churn
- Artinya 99% member loyal berhasil diidentifikasi dengan benar
- Hanya 1% (10 member) yang salah dikategorikan sebagai churn
2. Recall untuk Churn (0.96)
- Dari 190 member yang sebenarnya churn, 182 member berhasil diprediksi dengan tepat sebagai churn
- Artinya 96% member yang berpotensi churn berhasil diidentifikasi dengan benar
- Hanya 4% (8 member) yang tidak terdeteksi sebagai churn

Precision
1. Precision untuk Non-Churn (0.99)
- Dari 934 member yang diprediksi tidak churn, 926 member benar-benar tidak churn
- Tingkat ketepatan 99% dalam mengidentifikasi member loyal
- Hanya 8 member yang seharusnya churn tapi diprediksi tidak churn
2. Precision untuk Churn (0.95)
- Dari 192 member yang diprediksi churn, 182 member benar-benar churn
- Tingkat ketepatan 95% dalam mengidentifikasi member yang berpotensi churn
- Hanya 10 member yang seharusnya tidak churn tapi diprediksi churn

Model LGBM yang dikembangkan menunjukkan performa yang sangat baik dalam mengidentifikasi potensi churn di TechStyle. Dengan akurasi 98%, model ini memberikan dasar yang sangat kuat untuk program retensi pelanggan.

Aspek paling kritis dalam prediksi churn adalah kemampuan model untuk menangkap member yang benar-benar akan churn (recall untuk churn = 96%). Dari perspektif bisnis, ini berarti:
- Dari setiap 100 member yang berpotensi churn, model berhasil mengidentifikasi 96 member
- Hanya 4 member yang 'lolos' dari radar program retensi
- Ini memungkinkan tim Retensi untuk fokus pada hampir semua member yang berisiko

Sama pentingnya, model memiliki precision 95% untuk prediksi churn, yang berarti:
- Dari setiap 100 member yang ditandai untuk program retensi, 95 memang benar-benar berisiko churn
- Hanya 5 member yang mungkin mendapat intervensi retensi yang tidak diperlukan
- Ini menunjukkan efisiensi tinggi dalam alokasi sumber daya untuk program retensi

Model juga sangat akurat dalam mengidentifikasi member loyal (recall dan precision 99% untuk non-churn), memastikan bahwa:
- Resources tidak terbuang untuk intervensi yang tidak diperlukan
- Member loyal tidak terganggu dengan program retensi yang tidak relevan
- Tim dapat fokus pada segmen yang benar-benar membutuhkan perhatian

Dengan performa ini, model memberikan dasar yang solid untuk implementasi program retensi yang efektif dan efisien, memungkinkan TechStyle untuk memaksimalkan ROI dari upaya retensi pelanggan.

## __8. Recommendation__
### __8.1 Berdasarkan Data Analysis yang dilakukan, diberikan rekomendasi agar dapat meningkatkan conversion rate dan keuntungan perusahaan__ :

__Distribusi Churn berdasarkan Tier__
Berdasarkan analisis tier membership yang telah dilakukan, terdapat pola churn yang bervariasi:
- Basic Member: 41.9% churn rate (tertinggi)
- Silver Member: 7.5% churn rate
- Gold Member: 9.8% churn rate
- Platinum Member: 5.0% churn rate (terendah)

#### __1. Implementasi Threshold Berbeda per Tier__

- **Basic Member: Intervensi pada probability >40%**
     * Justifikasi: Dengan churn rate tinggi (41.9%), tier ini membutuhkan pendekatan agresif dalam penanganan churn
     * ROI Calculation: Meskipun intervensi pada threshold rendah berisiko menghasilkan lebih banyak false positive, cost of inaction (kehilangan pelanggan yang baru bergabung) jauh lebih tinggi dibandingkan cost of intervention
     * Dampak Finansial: Early intervention di Basic Member sangat kritis karena merupakan pintu masuk ke tier yang lebih tinggi dan profitable
     * Nilai threshold 40% menyeimbangkan antara cakupan intervensi yang luas dengan efisiensi resource

- **Silver Member: Intervensi pada probability >60%**
     * Justifikasi: Churn rate relatif rendah (7.5%) menunjukkan bahwa mayoritas Silver Member sudah memiliki loyalitas yang baik
     * ROI Calculation: Threshold yang lebih tinggi memungkinkan fokus pada member yang benar-benar berisiko, menghemat resources untuk tier yang sudah relatif stabil
     * Dampak Finansial: Kehilangan nilai dari false negative di tier ini lebih dapat ditoleransi dibandingkan Basic Member
     * Nilai 60% mencerminkan pendekatan konservatif yang sesuai dengan karakteristik tier yang stabil

- **Gold Member: Intervensi pada probability >50%**
     * Justifikasi: Churn rate 9.8% menunjukkan risiko menengah, namun lebih tinggi dari Silver Member (anomali)
     * ROI Calculation: Balance antara cost of intervention dan potential loss dari churned Gold Member
     * Dampak Finansial: Gold Member memiliki nilai transaksi yang lebih signifikan, sehingga membutuhkan pendekatan yang lebih agresif dibandingkan Silver Member
     * Threshold 50% merupakan titik keseimbangan yang memungkinkan cakupan memadai tanpa terlalu banyak false positive

- **Platinum Member: Intervensi pada probability >30%**
     * Justifikasi: Meskipun memiliki churn rate terendah (5%), Platinum Member mewakili nilai pelanggan tertinggi dengan rata-rata transaksi dan frequency order tertinggi
     * ROI Calculation: Cost of losing satu Platinum Member sangat tinggi, sehingga pendekatan ultra-konservatif adalah yang paling sesuai
     * Dampak Finansial: Rata-rata revenue Platinum Member yang tinggi ($4,177.17/bulan) menjustifikasi threshold rendah
     * Nilai 30% mencerminkan low risk tolerance untuk kehilangan member di tier tertinggi

#### __2. Alokasi Budget Retensi__

Strategi alokasi budget retensi harus berdasarkan hasil prediksi model ML untuk setiap tier. Dengan asumsi distribusi member yang diprediksi churn merata di seluruh tier, alokasi dapat dilakukan dengan pertimbangan:

- **Basic Member**: 
     * Proporsi terbesar karena volume tinggi dan nilai strategis sebagai pipeline untuk tier lebih tinggi
     * Expected churn rate yang tinggi tanpa intervensi
     * Kebutuhan investasi signifikan untuk meningkatkan onboarding dan early engagement

- **Silver Member**: 
     * Proporsi lebih kecil karena churn rate terendah dan volumenya paling sedikit
     * Fokus pada stabilisasi dan persiapan transisi ke Gold tier

- **Gold Member**: 
     * Alokasi moderat untuk mengatasi anomali churn yang lebih tinggi dibanding Silver tier
     * Investasi dalam experience enhancement untuk meningkatkan konversi ke Platinum

- **Platinum Member**: 
     * Proporsi sesuai meskipun churn rate rendah karena nilai pelanggan sangat tinggi
     * Fokus pada personalisasi dan value maximization

#### __3. Restrukturisasi Program Cashback dan Benefit Tier__

Analisis menunjukkan pola cashback dan order yang bervariasi per tier:
- Basic Member: $155.66 cashback, 2.32 order/bulan, revenue $3,113.28/bulan
- Silver Member: $169.91 cashback, 2.97 order/bulan, revenue $3,398.12/bulan
- Gold Member: $163.31 cashback, 2.75 order/bulan, revenue $3,266.28/bulan
- Platinum Member: $208.86 cashback, 3.67 order/bulan, revenue $4,177.17/bulan

##### __A. Basic Member (0-3 bulan)__
- **Cashback**: 3% cashback dasar, bonus 1% untuk order ke-3 dalam sebulan
     * Justifikasi: Dengan rata-rata revenue $3,113.28/bulan, cashback 3% menghasilkan $93.40, lebih rendah dari current state ($155.66) namun memberikan insentif untuk meningkatkan frekuensi order
     * Bonus 1% untuk order ke-3 bertujuan mendorong peningkatan frequency dari current 2.32 order/bulan
     * Proporsi cashback terhadap membership fee ($93.40/$199 = 47%) masih memberikan marjin sehat untuk perusahaan

- **Benefit Tambahan**:
     * Gratis ongkir untuk pembelian >$100. Justifikasi: Threshold ditetapkan di ~1/13 dari monthly revenue untuk mendorong pembelian namun tetap achievable
     * Voucher selamat datang $10. Justifikasi: 5% dari membership fee, sebagai welcome gesture yang affordable
     * Akses ke flash sale reguler. Justifikasi: Low-cost benefit yang memberikan perceived value tinggi
     * Prioritas customer service 12 jam. Justifikasi: Standar dasar dengan mempertimbangkan cost staffing dan resource

##### __B. Silver Member (3-6 bulan)__
- **Cashback**: 5% cashback dasar, bonus 2% untuk order ke-4 dalam sebulan
     * Justifikasi: Dengan rata-rata revenue $3,398.12/bulan, cashback 5% menghasilkan $169.91 (sama dengan current state)
     * Bonus 2% untuk order ke-4 mendorong peningkatan dari current 2.97 order/bulan
     * Proporsi cashback terhadap membership fee ($169.91/$199 = 85%) menunjukkan value proposition yang kuat

- **Benefit Tambahan**:
     * Gratis ongkir untuk pembelian >$75. Justifikasi: ~1/17 dari monthly revenue, lebih rendah dari Basic menunjukkan upgrade benefit
     * Voucher bulanan $15. Justifikasi: 7.5% dari membership fee, peningkatan 50% dari Basic
     * Akses ke ekslusif weekend sale. Justifikasi: Differentiated access menciptakan tier separation yang jelas
     * Prioritas customer service 8 jam. Justifikasi: 33% peningkatan dari Basic, menunjukkan prioritas yang lebih tinggi
     * Birthday discount 10%. Justifikasi: Low-frequency high-perceived value benefit dengan cost manageable

##### __C. Gold Member (6-12 bulan)__
- **Cashback**: 7% cashback dasar, bonus 3% untuk order ke-5 dalam sebulan
     * Justifikasi: Dengan rata-rata revenue $3,266.28/bulan, cashback 7% menghasilkan $228.64 (lebih tinggi dari current $163.31)
     * Peningkatan signifikan ini mengatasi anomali churn di Gold tier (9.8%)
     * Bonus 3% untuk order ke-5 bertujuan meningkatkan frequency dari current 2.75 order/bulan
     * Proporsi cashback-to-fee yang lebih tinggi ($228.64/$199 = 115%) menjustifikasi tenure yang lebih panjang

- **Benefit Tambahan**:
     * Gratis ongkir untuk pembelian >$50. Justifikasi: ~1/32 dari monthly revenue, signifikan lebih rendah menunjukkan tier value
     * Voucher bulanan $25. Justifikasi: 12.5% dari membership fee, hampir double Silver benefit
     * Akses ke pre-sale event. Justifikasi: High perceived value dengan low actual cost
     * Prioritas customer service 4 jam. Justifikasi: 50% peningkatan dari Silver, menunjukkan tier premium
     * Birthday discount 15%. Justifikasi: Peningkatan 50% dari Silver menunjukkan tier value
     * Dedicated account manager. Justifikasi: Investasi personalized service untuk retention di tier yang lebih tinggi

##### __D. Platinum Member (>12 bulan)__
- **Cashback**: 10% cashback dasar, bonus 5% untuk order ke-6 dalam sebulan
     * Justifikasi: Dengan rata-rata revenue $4,177.17/bulan, cashback 10% menghasilkan $417.72 (double dari current $208.86)
     * Peningkatan dramatic ini sesuai dengan nilai strategis Platinum member
     * Bonus 5% untuk order ke-6 mendorong peningkatan dari current 3.67 order/bulan
     * Proporsi cashback yang sangat tinggi ($417.72/$199 = 210%) mengkompensasi loyalty jangka panjang

- **Benefit Tambahan**:
     * Gratis ongkir tanpa minimal pembelian. Justifikasi: Ultimate convenience, menghilangkan friction dalam pembelian
     * Voucher bulanan $50. Justifikasi: 25% dari membership fee, menunjukkan value proposition premium
     * Akses ke exclusive limited-edition products. Justifikasi: Status symbol dengan high perceived value
     * Priority customer service 1 jam. Justifikasi: 75% peningkatan dari Gold, menunjukkan service level tertinggi
     * Birthday discount 20% + special gift. Justifikasi: Combination benefit yang menciptakan memorable experience
     * Dedicated premium account manager. Justifikasi: Personalized service untuk member dengan lifetime value tertinggi
     * Invitation to annual VIP event. Justifikasi: High-cost benefit namun menghasilkan engagement dan brand loyalty yang sangat tinggi

#### __4. Strategi Intervensi Spesifik per Tier__

##### __A. Basic Member (Critical Period Intervention)__
Data menunjukkan 41.9% Basic Member churn dengan 33.5% persentase complain. Strategi intervensi:

1. **Onboarding Fast Track**:
    - Welcome call dalam 24 jam setelah signup
        * Justifikasi: First impression kritis dalam 24 jam pertama, dan data menunjukkan Basic Member membutuhkan engagement awal yang kuat
        * Implementasi: Sistem automated call scheduling dengan script terstruktur
        * Pengukuran: First-call resolution rate, signup-to-first-purchase time
        
    - Video tutorial personalized berdasarkan kategori produk pertama
        * Justifikasi: 33.5% complain rate menunjukkan potential knowledge gap dalam penggunaan platform
        * Implementasi: Library tutorial video pre-recorded dengan personalization based on first browsing behavior
        * Pengukuran: Video completion rate, post-video engagement metrics
    - Follow-up email otomatis di hari ke-7, 14, dan 21
        * Justifikasi: Critical period intervention di minggu-minggu awal dengan jeda waktu yang strategis
        * Implementasi: Templated email sequence dengan dynamic content
        * Pengukuran: Email open rate, click-through rate, conversion rate

2. **Early Warning Response**:
    - Alert sistem jika tidak ada order dalam 14 hari
        * Justifikasi: Dengan rata-rata 2.32 order/bulan, tidak ada aktivitas selama 14 hari merupakan red flag
        * Implementasi: Automated alert system dengan escalation path
        * Pengukuran: Alert-to-intervention time, intervention success rate
    - Proactive outreach jika terjadi complain dengan resolusi <24 jam
        * Justifikasi: Complain dari Basic Member perlu respons cepat karena tingginya churn risk
        * Implementasi: Dedicated team untuk Basic Member complaint handling
        * Pengukuran: Complaint resolution time, post-resolution satisfaction
    - Special win-back offer jika menunjukkan tanda-tanda churn
        * Justifikasi: Investasi khusus diperlukan untuk member yang terdeteksi pada threshold churn prediction
        * Implementasi: Tiered offer based on customer value
        * Pengukuran: Offer acceptance rate, post-offer retention

3. **Value Acceleration Program**:
    - Double cashback untuk pembelian kedua dalam 30 hari
        * Justifikasi: Incentivize repeat purchase dalam window kritis 30 hari
        * Implementasi: Automated cashback multiplier
        * Pengukuran: Time-to-second-purchase, average order value change
    - "Fast Track to Silver" dengan 3 pembelian dalam 60 hari
        * Justifikasi: Mempercepat transisi ke tier dengan churn rate lebih rendah (7.5%)
        * Implementasi: Accelerated tier progression program
        * Pengukuran: Tier progression rate, post-progression retention
    - Personalized recommendation berdasarkan browsing behavior
        * Justifikasi: Relevance meningkatkan engagement dan reduces decision paralysis
        * Implementasi: ML-based recommendation engine
        * Pengukuran: Recommendation click-through rate, conversion rate

##### __B. Silver Member (Stability Enhancement)__
Tier dengan churn rate terendah (7.5%) dan persentase complain terendah (23.2%):

1. **Engagement Reinforcement**:
    - Monthly "Silver Benefits" reminder email
        * Justifikasi: Memperkuat value perception di tier dengan satisfaction tertinggi
        * Implementasi: Benefit utilization reminder dengan success stories
        * Pengukuran: Benefit utilization rate, engagement lift post-reminder
    - Mid-tier milestone celebration pada bulan ke-4.5
        * Justifikasi: Menjembatani gap psikologis di tengah tenure Silver (3-6 bulan)
        * Implementasi: Special offer/recognition di pertengahan journey
        * Pengukuran: Milestone engagement, post-milestone retention
    - Category-specific promotions berdasarkan purchase history
        * Justifikasi: Leveraging existing preference untuk meningkatkan frequency 2.97 order/bulan
        * Implementasi: Targeted promotion engine dengan purchase data
        * Pengukuran: Category penetration depth, cross-category conversion

2. **Loyalty Loop Development**:
    - Implementasi sistem poin loyalitas khusus Silver
        * Justifikasi: Mekanisme engagement tambahan untuk tier yang sudah stabil
        * Implementasi: Points accrual system dengan meaningful redemption
        * Pengukuran: Points earning rate, redemption rate
    - Preview benefit Gold setelah 5 bulan
        * Justifikasi: Menciptakan aspirational value untuk transisi ke tier berikutnya
        * Implementasi: Limited-time trial access ke Gold benefits
        * Pengukuran: Preview engagement, upgrade conversion rate
    - Referral program dengan double reward
        * Justifikasi: Memanfaatkan tingkat kepuasan tinggi di Silver tier untuk akuisisi
        * Implementasi: Enhanced referral structure untuk Silver members
        * Pengukuran: Referral send rate, conversion rate, quality of referred customers

##### __C. Gold Member (Anomaly Treatment)__
Menunjukkan anomali dengan churn rate (9.8%) lebih tinggi dari Silver:

1. **Experience Assessment**:
    - Proactive satisfaction survey di bulan ke-8
        * Justifikasi: Mengidentifikasi root cause anomali churn di tier ini
        * Implementasi: Targeted survey dengan incentive for completion
        * Pengukuran: Survey completion rate, insight actionability
    - Dedicated session dengan account manager untuk evaluasi pengalaman
        * Justifikasi: Personalized approach untuk high-value member di risk tier
        * Implementasi: Structured conversation dengan focused questions
        * Pengukuran: Session completion rate, issue identification rate
    - Focus group untuk Gold member dengan tenure 9-11 bulan
        * Justifikasi: Periode kritis menjelang potential upgrade/churn decision
        * Implementasi: Small group discussion dengan specific agenda
        * Pengukuran: Insight generation, implementasi rekomendasi

2. **Value Enhancement**:
    - Gold-exclusive product bundles dengan harga preferensial
        * Justifikasi: Meningkatkan value perception di tier dengan slight cashback anomaly
        * Implementasi: Curated bundles dengan margin contribution yang dioptimalkan
        * Pengukuran: Bundle adoption rate, margin impact
    - Privilege access ke new product testing
        * Justifikasi: Creates exclusivity dan sense of importance
        * Implementasi: Beta testing program dengan feedback mechanism
        * Pengukuran: Participation rate, NPS dari program
    - Customized shopping experience berdasarkan preference
        * Justifikasi: Personalisasi untuk meningkatkan relevance dan satisfaction
        * Implementasi: Customized UI/UX berdasarkan shopping history
        * Pengukuran: Engagement metrics, conversion rate lift

3. **Platinum Transition Path**:
    - Clear visualization transition path to Platinum
        * Justifikasi: Menciptakan aspirational clarity untuk progress
        * Implementasi: Interactive progress visualization di dashboard
        * Pengukuran: Visualization engagement, progress acceleration  
    - "Almost Platinum" preview benefit di bulan ke-11
        * Justifikasi: Menciptakan compelling reason untuk complete transition
        * Implementasi: Limited time access ke selected Platinum benefits
        * Pengukuran: Preview utilization, conversion impact
    - Transition coaching call menjelang upgrade ke Platinum
        * Justifikasi: High-touch approach di decision point kritis
        * Implementasi: Scheduled consultation dengan focus pada value maximization
        * Pengukuran: Call completion rate, conversion rate

##### __D. Platinum Member (Value Maximization)__
Tier dengan nilai transaksi tertinggi dan churn rate terendah (5.0%):

1. **White Glove Service**:
    - Dedicated concierge team dengan penanganan prioritas
        * Justifikasi: Service premium untuk member dengan LTV tertinggi
        * Implementasi: Specialized team dengan training khusus Platinum service
        * Pengukuran: Response time, resolution rate, satisfaction score
    
    - Quarterly review call dengan account manager
        * Justifikasi: Proactive relationship management untuk preventing churn
        * Implementasi: Structured review dengan focus pada enhancing experience
        * Pengukuran: Call completion rate, upsell success, issue resolution
    
    - Proactive product recommendation berdasarkan past purchase
        * Justifikasi: Meningkatkan AOV dan frequency dari current 3.67 order/bulan
        * Implementasi: AI-driven recommendation dengan human curation
        * Pengukuran: Recommendation acceptance rate, incremental revenue

2. **Exclusive Experiences**:
    - Invitation-only virtual events dengan brand partners
        * Justifikasi: Creating unique value yang tidak tersedia di marketplace lain
        * Implementasi: Curated events dengan brand partners premium
        * Pengukuran: Attendance rate, post-event satisfaction, purchase correlation
    
    - Early access ke new product launches 7 hari sebelum publik
        * Justifikasi: Memberikan competitive advantage dan status ke highest tier
        * Implementasi: Automated early access system dengan communication plan
        * Pengukuran: Early access utilization, conversion rate vs. general launch
    
    - Personalized shopping experience dengan curated selection
        * Justifikasi: Ultimate personalization untuk member dengan data engagement terlengkap
        * Implementasi: AI-powered curation dengan expert human oversight
        * Pengukuran: Engagement dengan curated content, conversion lift

3. **Community Building**:
    - Platinum member community forum
        * Justifikasi: Creating network effect dan additional switching cost
        * Implementasi: Exclusive platform dengan moderasi aktif
        * Pengukuran: Engagement rate, content generation, influence on purchasing
    - Brand ambassador program untuk selected members
        * Justifikasi: Leveraging loyal customers sebagai brand advocates
        * Implementasi: Structured program dengan clear benefits dan responsibilities
        * Pengukuran: Ambassador activity, influenced sales, social media impact
    - Advisory board membership untuk feedback produk dan layanan
        * Justifikasi: Co-creation approach yang meningkatkan loyalty dan relevance
        * Implementasi: Quarterly advisory sessions dengan product team
        * Pengukuran: Idea implementation rate, member satisfaction, retention impact

## Tableau Dashboard
[Link to Tableau](https://public.tableau.com/shared/27BMXGT27?:display_count=n&:origin=viz_share_link)
![image](https://github.com/PurwadhikaDev/BetaGroup_JC_DS_OL_16_A_FinalProject/blob/306c0b1df35b16cb2d40757c7bc2d07d1c4e5b09/BETA%20A%20DASHBOARD.JPG)

