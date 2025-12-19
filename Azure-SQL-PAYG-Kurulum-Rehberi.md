# Azure SQL Server Pay-As-You-Go (PAYG) Kurulum Rehberi
### 5 Dakikada SQL Server Enterprise Lisansı - Kullandığınız Kadar Ödeyin

---

## 🎯 Neden Azure SQL PAYG?

✅ **Upfront maliyet yok** - İlk yatırım gerektirmez
✅ **Esnek ödeme** - Kullandığınız kadar ödersiniz
✅ **Anında aktivasyon** - 5 dakikada kurulum
✅ **Enterprise Edition** - Tam özellikli SQL Server
✅ **Agent gerektirmez** - Sadece portal üzerinden
✅ **İptal garantisi** - İstediğiniz zaman iptal edebilirsiniz

---

## 📋 Başlamadan Önce

İhtiyacınız olanlar:

| Gereksinim | Açıklama |
|------------|----------|
| ✅ Azure Hesabı | Aktif bir Azure aboneliği |
| ✅ Core Sayısı | SQL Server'ınızın kaç core kullandığını bilin |
| ✅ 5 Dakika | Kurulum süresi |

**Core sayınızı öğrenin:**

SQL Server Management Studio'da çalıştırın:
```sql
SELECT cpu_count / hyperthread_ratio AS [Physical Cores]
FROM sys.dm_os_sys_info;
```

---

## 🚀 Kurulum Adımları (Sadece 3 Adım!)

### Adım 1: Azure Portal'a Giriş ve SQL Server Licensing Sayfasına Git

1. Tarayıcınızdan **[portal.azure.com](https://portal.azure.com)** adresine gidin
2. Azure hesabınızla giriş yapın
3. Portal üst kısmındaki **🔍 arama çubuğuna** tıklayın
4. **"sql server licensing"** yazın
5. Açılan listeden **"SQL Server licensing"** seçeneğini seçin

![Azure Portal - Arama çubuğunda "sql server licensing" yazılı]

**SQL Server licensing** sayfası açılır. "No sql server licenses to display" mesajı ve sağ altta mavi **"Create"** butonu görürsünüz.

![SQL Server licensing sayfası - Boş liste ve Create butonu]

---

### Adım 2: Lisans Bilgilerini Doldurun

**"Create"** butonuna tıklayın. **"Create a SQL Server physical core license"** formu açılır.

![Create a SQL Server physical core license - Form açıldı]

Form **5 sekmeden** oluşur:
1. ✅ **Basics**
2. **License details**
3. **License Activation**
4. **Tags**
5. **Review + submit**

---

#### 📋 Sekme 1: Basics

**Project details:**

| Alan | Ne Yazmalı | Örnek |
|------|------------|-------|
| **Subscription** | Azure aboneliğinizi seçin | MCPP Subscription |
| **Resource group** | Mevcut grup seçin veya yeni oluşturun | `rg-sql-licenses` |

**Instance details:**

| Alan | Ne Yazmalı | Örnek |
|------|------------|-------|
| **Name** | Lisans için benzersiz isim | `sql-payg-prod-001` |
| **Region** | Size en yakın bölge | East US veya West Europe |

![Basics sekmesi - Tüm alanlar doldurulmuş]

**"Next: License details >"** butonuna tıklayın.

---

#### 📋 Sekme 2: License details

| Alan | Ne Seçmeli | Açıklama |
|------|------------|----------|
| **License category** | `Core` | Otomatik seçili (değiştirmeyin) |
| **Scope type** | `Single Resource group` | Tek kaynak grubu için |
| **Billing plan** | **`Pay-as-you-go ("PAYG")`** | ← **Bunu seçin** |
| **Physical cores** | Core sayınız | Örn: `16` |

![License details - Billing plan: Pay-as-you-go, Physical cores: 16]

**💡 İpucu:**
- **Scope type** seçenekleri: Single Resource group, Subscription, Tenant
- **Billing plan** seçenekleri:
  - **Pay-as-you-go ("PAYG")** ← Kullandığınız kadar öde
  - Paid (Mevcut lisansınız varsa)

**"Next: License Activation >"** butonuna tıklayın.

---

#### 📋 Sekme 3: License Activation

**Activation options:** (Ne zaman aktif etmek istiyorsunuz?)

⚪ **I will activate the license later** (Sonra aktif ederim)
🔘 **I want to activate the license immediately** ← **BUNU SEÇİN**

![License Activation - "I want to activate the license immediately" seçili]

**Ekranda 2 bilgilendirme mesajı görürsünüz:**

🟠 **Turuncu uyarı:**
> "To activate the license, you must have connected machines in the license's scope that are configured to use the physical core license."

🔵 **Mavi bilgi:**
> "Once the license is activated, it will take effect on the eligible resources in the license scope immediately and the hourly billing using an Enterprise edition meter will start today."

**"Next: Tags >"** butonuna tıklayın.

---

#### 📋 Sekme 4: Tags (Opsiyonel)

İsterseniz etiket ekleyin (maliyet takibi için faydalı):

| Name | Value |
|------|-------|
| Environment | Production |
| Application | Database |

Etiket eklemek istemiyorsanız **"Next: Review + submit >"** butonuna tıklayın.

---

### Adım 3: Gözden Geçir ve Oluştur

#### 📋 Sekme 5: Review + submit

1. Tüm bilgilerinizi gözden geçirin:
   - ✅ Subscription doğru mu?
   - ✅ Core sayısı doğru mu?
   - ✅ Billing plan PAYG seçili mi?
   - ✅ Activation "immediately" seçili mi?

2. ✅ **Validation passed** mesajını görürsünüz

3. **Tahmini aylık maliyet** gösterilir
   - Örnek: ~$4,380/month (16 core için)

4. **"Submit"** veya **"Create"** butonuna tıklayın

![Review + submit - Validation passed, özet bilgiler]

---

## ✅ Kurulum Tamamlandı!

Deployment **30-60 saniye** içinde tamamlanır.

![Deployment in progress]

✅ **"Your deployment is complete"** mesajını görünce **"Go to resource"** butonuna tıklayın.

![Deployment complete - Yeşil onay işareti]

---

## 📊 Lisansınız Aktif!

Lisans kaynağınızın Overview sayfası açılır:

![SQL Server license - Overview sayfası]

**Essentials** bölümünde göreceksiniz:
- **Status**: ✅ Activated
- **Billing Plan**: PAYG
- **Physical Cores**: 16
- **Location**: East US
- **Resource ID**: Benzersiz kaynak kimliği

---

## 🎉 Tebrikler!

**SQL Server Enterprise lisansınız artık aktif ve faturalandırma başladı.**

### 🎯 Bundan Sonra Ne Olacak?

✅ **Faturalandırma otomatik başladı** - Saatlik bazda
✅ **Hiçbir şey yapmanıza gerek yok** - SQL Server'ınız çalışmaya devam edebilir
✅ **Aylık faturanız Azure aboneliğinize yansıyacak**

---

## 💰 Fiyatlandırma

### Nasıl Faturalandırılır?

- **Birim**: Core başına saat
- **Ödeme**: Aylık, Azure faturanızda görünür
- **Minimum**: 4 core

### Örnek Maliyetler

**Enterprise Edition PAYG Fiyatı:** $273.75/core/ay

| Core Sayısı | Aylık Maliyet (USD) |
|-------------|---------------------|
| 4 cores (minimum) | $1,095 |
| 8 cores | $2,190 |
| 16 cores | $4,380 |
| 32 cores | $8,760 |

**💡 Not:** Fiyatlar bölgeye göre değişebilir. Güncel fiyat için [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) kullanın.

---

## 🔧 Lisans Yönetimi

### Core Sayısını Değiştirmek

1. Lisans kaynağınıza gidin
2. **"Configure"** butonuna tıklayın
3. **Physical Cores** değerini değiştirin
4. **"Save"** yapın

**Değişiklik anında yürürlüğe girer.**

---

### Lisansı Durdurmak

**Geçici olarak durdurmak:**

1. Lisans kaynağına gidin
2. **"Configure"** tıklayın
3. **License Activation** bölümünde **"Deactivate"** seçin
4. Save

**Faturalandırma durur, kaynak kalır.**

**Kalıcı olarak silmek:**

1. Lisans kaynağına gidin
2. Üst menüden **"Delete"** butonuna tıklayın
3. Onaylayın

**Kaynak silinir, faturalandırma durur.**

---

## 📊 Maliyet Takibi

### Faturanızı Görüntüleyin

1. Azure Portal'da **"Cost Management + Billing"** bölümüne gidin
2. **"Cost analysis"** sekmesini seçin
3. Filtre ekleyin:
   - Resource type: `SqlServerLicenses`
   - Date range: Last 30 days

Aylık harcamanızı gerçek zamanlı takip edebilirsiniz.

---

## 💬 Müşteriye Nasıl Açıklarsınız?

Denetim veya soru sorulduğunda:

> **"SQL Server Enterprise lisansımızı Azure üzerinden Pay-As-You-Go modeli ile alıyoruz. Kullandığımız kadar ödeme yapıyoruz. İşte Azure Portal'daki lisans kaydımız ve fatura detaylarımız."**

**Kanıt olarak gösterin:**
- ✅ Azure Portal'daki lisans kaynağı (Overview sayfası)
- ✅ Cost Management'tan fatura detayları
- ✅ Aylık ödeme kayıtları

---

## ❓ Sık Sorulan Sorular

**S: Hangi SQL Server sürümleri için geçerli?**
C: Sadece **SQL Server Enterprise Edition** için.

**S: Agent kurmam gerekiyor mu?**
C: **Hayır**, hiçbir agent kurmanıza gerek yok.

**S: On-premises SQL Server için kullanabilir miyim?**
C: **Evet**, her ortam için kullanabilirsiniz (on-prem, AWS, GCP, vb.)

**S: SQL Server'a bağlantı kuruluyor mu?**
C: **Hayır**, hiçbir bağlantı kurulmaz.

**S: Minimum taahhüt var mı?**
C: **Hayır**, istediğiniz zaman iptal edebilirsiniz.

**S: İlk ay tam mı ücretlendirilir?**
C: **Hayır**, saatlik bazda ödeme yaparsınız (pro-rated).

**S: Lisansı durdurabilir miyim?**
C: **Evet**, Deactivate yapabilir veya silebilirsiniz.

**S: Core sayısını değiştirebilir miyim?**
C: **Evet**, istediğiniz zaman Configure'den değiştirebilirsiniz.

**S: "I will activate the license later" seçersem ne olur?**
C: Lisans kaydı oluşturulur ama faturalandırma başlamaz. İstediğiniz zaman Activate edebilirsiniz.

**S: Scope type ne anlama geliyor?**
C:
- **Single Resource group**: Sadece bir kaynak grubu için
- **Subscription**: Abonelik genelinde
- **Tenant**: Tüm tenant için

---

## 🎁 Avantajlar Özeti

| Özellik | Açıklama |
|---------|----------|
| 💰 **Maliyet Esnekliği** | Kullandığınız kadar ödeyin |
| ⚡ **Hızlı Kurulum** | 5 dakikada aktif |
| 🚫 **Agent Yok** | Sadece portal üzerinden |
| 🔄 **Esnek İptal** | İstediğiniz zaman durdurun |
| 📊 **Şeffaf Fatura** | Gerçek zamanlı maliyet takibi |
| ✅ **Tam Özellikli** | Enterprise Edition |
| 🌍 **Her Ortam** | On-prem, cloud, hybrid |

---

## 📞 Destek

Sorularınız için:
- 📧 Azure Portal > Help + support
- 📚 [Microsoft Learn Dokümantasyonu](https://learn.microsoft.com/sql/sql-server/azure-arc/manage-license-billing)

---

**🎉 Başarılar dileriz!**

Azure SQL Server PAYG ile esnek, güvenilir ve şeffaf lisanslama deneyimi yaşayın.

---

**Doküman Versiyonu:** 4.0 (Gerçek ekran görüntülerine göre güncellendi)
**Tarih:** 19 Aralık 2024
