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

![Azure Portal - Arama çubuğunda "sql server licensing" yazılı, dropdown menüde seçenek görünüyor]

**SQL Server licensing** sayfası açılır.

![SQL Server licensing sayfası - "No sql server licenses to display" mesajı ve sağ altta mavi "Create" butonu]

Sağ altta veya üst menüde **"Create"** butonunu göreceksiniz.

---

### Adım 2: Lisans Oluştur ve Bilgileri Doldurun

1. **"Create"** butonuna tıklayın

2. Açılan pencerede iki seçenek göreceksiniz:
   - ⚪ SQL Server virtual core license
   - 🔘 **SQL Server physical core license** ← **BUNU SEÇİN**

![Lisans tipi seçim ekranı - "SQL Server physical core license" seçili]

3. **"Select"** butonuna tıklayın

---

### Adım 3: Bilgileri Doldurun ve Oluştur

Artık kurulum sihirbazı açıldı. Aşağıdaki alanları doldurun:

![Create SQL Server physical core license formu - Basics sekmesi]

#### 📂 Project Details

| Alan | Ne Yazmalı | Örnek |
|------|------------|-------|
| **Subscription** | Azure aboneliğinizi seçin | Azure Subscription 1 |
| **Resource group** | Mevcut grup seçin veya "Create new" | `rg-sql-licenses` |

#### 📝 Instance Details

| Alan | Ne Yazmalı | Örnek |
|------|------------|-------|
| **License name** | Lisans için benzersiz isim | `sql-enterprise-prod-001` |
| **Region** | Size en yakın bölge | West Europe |

#### ⚙️ License Configuration

| Alan | Ne Seçmeli | Açıklama |
|------|------------|----------|
| **Billing Plan** | **PAYG** | Pay-as-you-go seçin |
| **Physical Cores** | Core sayınız | Örn: `16` |
| **Edition** | **Enterprise** | Enterprise seçin |
| **Activation State** | ☑️ **Activated** | Hemen aktif et |

![Form doldurulmuş hali: PAYG, 16 cores, Enterprise, Activated]

**💡 İpucu:** Minimum 4 core girmelisiniz.

---

**Opsiyonel - Etiket Ekleyin:**

İsterseniz **"Next: Tags"** diyerek etiket ekleyin (Environment: Production gibi), veya direkt **"Review + create"** yapın.

**Review + Create:**

1. **"Review + create"** butonuna tıklayın
2. ✅ **Validation passed** mesajını görün
3. **Tahmini aylık maliyet** kontrol edin (örn: ~$1,140/month)
4. **"Create"** butonuna tıklayın

![Review + create sayfası - Validation passed, tahmini maliyet gösteriliyor]

---

### ✅ Tamamlandı!

Deployment **30-60 saniye** içinde tamamlanır.

![Deployment in progress... yükleme çubuğu]

✅ **Your deployment is complete** mesajını görünce **"Go to resource"** butonuna tıklayın.

![Deployment complete ekranı - Yeşil onay işareti]

**Lisansınızın detayları:**

![SQL Server license - Overview sayfası]

**Essentials** bölümünde göreceksiniz:
- **Status**: ✅ Activated
- **Billing Plan**: PAYG
- **Edition**: Enterprise
- **Physical Cores**: 16
- **Location**: West Europe

---

## 🎉 Kurulum Tamamlandı!

**Tebrikler!** SQL Server Enterprise lisansınız artık aktif.

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

### Örnek Maliyetler (Tahmini)

| Core Sayısı | Aylık Maliyet (USD) |
|-------------|---------------------|
| 8 cores | ~$570 |
| 16 cores | ~$1,140 |
| 32 cores | ~$2,280 |

**💡 Not:** Fiyatlar bölgeye göre değişir. Güncel fiyat için [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) kullanın.

---

## 🔧 Lisans Yönetimi

### Core Sayısını Değiştirmek

1. Lisans kaynağınıza gidin
2. **"Configure"** butonuna tıklayın
3. **Physical Cores** değerini değiştirin
4. **"Save"** yapın

![Configure sayfası - Core sayısı değiştirme]

**Değişiklik anında yürürlüğe girer.**

---

### Lisansı Durdurmak

**Geçici olarak durdurmak:**

1. Lisans kaynağına gidin
2. **Configure** > **Activation State** > ☐ Activated işaretini kaldırın
3. Save

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

![Cost Management - SQL Server PAYG maliyetleri grafik halinde]

Aylık harcamanızı gerçek zamanlı takip edebilirsiniz.

---

## 💬 Müşteriye Nasıl Açıklarsınız?

Denetim veya soru sorulduğunda:

> **"SQL Server Enterprise lisansımızı Azure üzerinden Pay-As-You-Go modeli ile alıyoruz. Kullandığımız kadar ödeme yapıyoruz. İşte Azure Portal'daki lisans kaydımız ve fatura detaylarımız."**

**Kanıt olarak gösterin:**
- ✅ Azure Portal'daki lisans kaynağı
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
C: **Evet**, Deactivated yapabilir veya silebilirsiniz.

**S: Core sayısını değiştirebilir miyim?**
C: **Evet**, istediğiniz zaman Configure'den değiştirebilirsiniz.

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
- 📚 [Microsoft Learn Dokümantasyonu](https://learn.microsoft.com/azure/azure-arc/)

---

**🎉 Başarılar dileriz!**

Azure SQL Server PAYG ile esnek, güvenilir ve şeffaf lisanslama deneyimi yaşayın.

---

**Doküman Versiyonu:** 3.0
**Tarih:** 19 Aralık 2024
