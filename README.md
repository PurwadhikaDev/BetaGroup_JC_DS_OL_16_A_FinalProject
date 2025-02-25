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
      <a href="#1. business-problem-and-data-understanding">Business Problem and Data Understanding</a>
      <ul>
        <li><a href="#1.1. context">Context</a></li>
        <li><a href="#1.2. Revenue-Impact-Analysis">Problem Statement</a></li>
        <li><a href="#1.3. Problem-Statement">Stakeholder In-charge</a></li>
        <li><a href="#1.4. Goals">Goals</a></li>
        <li><a href="#1.5. analytic-approach">Analytic Approach</a></li>
        <li><a href="#1.6. metric-evaluation">Metric Evaluation</a></li>
      </ul>
    </li>
    <li>
      <a href="#data-understanding">Data Understanding</a>
      <ul>
        <li><a href="#features">Features</a></li>
        <li><a href="#data-preprocessing">Data Preprocessing</a></li>
      </ul>
    </li>
    <li>
      <a href="#data-analytics">Data Analytics</a>
    </li>
    <li>
      <a href="#tableau-dashboard">Tableau Dashboard</a>
    </li>
    <li>
      <a href="#modeling-and-tuning">Modeling & Tuning</a>
      <ul>
        <li><a href="#modeling">Modeling</a></li>
        <li><a href="#resampling">Resampling</a></li>
        <li><a href="#coefficient">Coefficient</a></li>
        <li><a href="#tuning">Tuning</a></li>
        <li><a href="#confusion-matrix">Confusion Matrix</a></li>
      </ul>
    </li>
    <li><a href="#conclusion-and-recommendation">Conclusion & Recommendation</a></li>
  </ol>
</details>



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
