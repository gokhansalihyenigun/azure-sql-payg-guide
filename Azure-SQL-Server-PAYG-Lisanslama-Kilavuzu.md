# Azure SQL Server Pay-As-You-Go (PAYG) Lisanslama Kılavuzu
### On-Premises SQL Server için Agent Kurmadan Lisans Yönetimi

---

## İçindekiler

1. [Genel Bakış](#1-genel-bakış)
2. [Lisanslama Modeli Nasıl Çalışır?](#2-lisanslama-modeli-nasıl-çalışır)
3. [Kimler İçin Bu Kılavuz?](#3-kimler-için-bu-kılavuz)
4. [Önkoşullar](#4-önkoşullar)
5. [Adım Adım Portal Kurulumu](#5-adım-adım-portal-kurulumu)
6. [Lisans Doğrulama ve Yönetim](#6-lisans-doğrulama-ve-yönetim)
7. [Fiyatlandırma ve Faturalandırma](#7-fiyatlandırma-ve-faturalandırma)
8. [Etik ve Uyumluluk Hususları](#8-etik-ve-uyumluluk-hususları)
9. [Sık Sorulan Sorular](#9-sık-sorulan-sorular)
10. [Sorun Giderme](#10-sorun-giderme)
11. [Kaynaklar](#11-kaynaklar)

---

## 1. Genel Bakış

Azure SQL Server Pay-As-You-Go (PAYG) lisanslama modeli, on-premises veya diğer ortamlarda çalışan SQL Server Enterprise Edition için **kullandığınız kadar ödeme** yapmanızı sağlayan bir Microsoft lisanslama seçeneğidir.

### Temel Özellikler

✅ **Agent Gerektirmez**: Azure Arc Connected Machine Agent kurmanıza gerek yoktur
✅ **Self-Declaration (Kendi Beyanı)**: Core sayısını Azure Portal'dan kendiniz bildirirsiniz
✅ **Güven Bazlı**: SPLA gibi güven temelli lisanslama modelidir
✅ **Enterprise Edition**: Yalnızca SQL Server Enterprise Edition için geçerlidir
✅ **Esnek Faturalandırma**: Saatlik bazda kullanım ücreti ödersiniz
✅ **Anında Aktivasyon**: Portal'dan dakikalar içinde aktif hale gelir

### Ne Zaman Kullanılır?

- Geçici veya dönemsel SQL Server ihtiyaçları
- Kısa vadeli projeler
- Upfront lisans maliyetinden kaçınmak istediğinizde
- Software Assurance veya toplu lisans anlaşmanız yoksa
- Esnek ödeme modeli tercih ettiğinizde

---

## 2. Lisanslama Modeli Nasıl Çalışır?

### Güven Bazlı Lisanslama (Trust-Based Licensing)

Bu model, **self-reported licensing** (kendi beyan ettiğiniz lisanslama) prensibiyle çalışır:

```
┌─────────────────────────────────────────────────────────┐
│  SİZ (Müşteri)                                          │
│  ↓                                                      │
│  1. Azure Portal'da "SQL Server licenses" oluşturursunuz│
│  2. Fiziksel core sayısını bildirirsiniz               │
│  3. "Activate immediately" seçeneğini işaretlersiniz    │
│  ↓                                                      │
│  AZURE (Microsoft)                                      │
│  ↓                                                      │
│  4. Beyan ettiğiniz core sayısına göre faturalandırır  │
│  5. Hiçbir sunucuya bağlantı kurmaz                    │
│  6. Güven ilişkisine dayanır                           │
└─────────────────────────────────────────────────────────┘
```

### SPLA ile Benzerlik

Bu lisanslama modeli, **SPLA (Services Provider License Agreement)** modeline çok benzer:

| Özellik | SPLA | Azure SQL PAYG |
|---------|------|----------------|
| Güven bazlı | ✅ Evet | ✅ Evet |
| Self-declaration | ✅ Evet | ✅ Evet |
| Aylık ödeme | ✅ Evet | ✅ Evet (saatlik bazda) |
| Denetim riski | ✅ Var | ✅ Var |
| Agent gereksinimi | ❌ Hayır | ❌ Hayır |

### Önemli Not

⚠️ **ETİK SORUMLULUK**: Bu model, doğru core sayısını bildirmenizi gerektirir. Yanlış veya eksik beyan etmek Microsoft lisans denetimlerinde sorun yaratabilir.

---

## 3. Kimler İçin Bu Kılavuz?

Bu doküman üç farklı kullanıcı grubu için hazırlanmıştır:

### 👨‍💻 Teknik Ekip

- Azure Portal'da nasıl yapılandırma yapılacağını öğreneceksiniz
- Hangi kaynakların oluşturulması gerektiğini göreceksiniz
- Faturalandırma mekanizmasını anlayacaksınız

### 📊 Lisans Yöneticileri

- Uyumluluk ve denetim süreçlerini yöneteceksiniz
- Maliyet optimizasyonu yapabileceksiniz
- Doğru beyanın önemini kavrayacaksınız

### 👤 Son Kullanıcılar / Yöneticiler

- Denetim sırasında "Lisansımı Azure'dan PAYG olarak ödüyorum" diyebileceksiniz
- Azure Portal'dan lisans kaydınızı gösterebileceksiniz
- Fatura detaylarıyla kanıt sunabileceksiniz

---

## 4. Önkoşullar

### Azure Tarafında Gereksinimler

✅ **Aktif Azure Aboneliği**
- Kredi kartı veya fatura bilgisi tanımlı olmalı
- Abonelik aktif durumda olmalı

✅ **Yeterli İzinler**
Aşağıdaki rollerden **en az birine** sahip olmalısınız:
- **Owner** (Sahip)
- **Contributor** (Katkıda Bulunan)
- **Cost Management Contributor**

### SQL Server Tarafında Gereksinimler

✅ **SQL Server Sürümü**
- SQL Server 2012 veya üzeri
- **Sadece Enterprise Edition** (Standard, Web, Express desteklenmez)

✅ **Ortam**
- On-premises sunucular
- Diğer bulut sağlayıcılar (AWS, GCP, vb.)
- Azure VM'ler (ancak bu durumda SQL IaaS Extension kullanmak daha mantıklı)
- Fiziksel veya sanal makineler

### Bilgi Toplama

Kuruluma başlamadan önce aşağıdaki bilgileri hazırlayın:

| Bilgi | Örnek | Nereden Bulunur |
|-------|-------|-----------------|
| **Fiziksel Core Sayısı** | 16 | Sunucu donanım spesifikasyonu |
| **SQL Server Edition** | Enterprise | SQL Server Management Studio |
| **SQL Server Versiyon** | SQL Server 2019 | `SELECT @@VERSION` |
| **Sunucu Konumu** | İstanbul Datacenter | Fiziksel konum |
| **En Yakın Azure Region** | West Europe | [Azure Regions](https://azure.microsoft.com/global-infrastructure/geographies/) |

**Core Sayısını Öğrenme:**

SQL Server'da çalıştırın:
```sql
SELECT
    cpu_count AS [Logical CPU Count],
    cpu_count / hyperthread_ratio AS [Physical CPU Count],
    hyperthread_ratio AS [Hyperthread Ratio]
FROM sys.dm_os_sys_info;
```

**Not**: `Physical CPU Count` değerini kullanın (Logical değil).

---

## 5. Adım Adım Portal Kurulumu

### Adım 1: Azure Portal'a Giriş

1. Tarayıcınızdan [https://portal.azure.com](https://portal.azure.com) adresine gidin
2. Azure hesabınızla oturum açın
3. Doğru abonelikte olduğunuzu doğrulayın (sağ üst köşe)

**📸 EKRAN GÖRÜNTÜsÜ 1**: Azure Portal ana sayfası, abonelik seçimi gösteriliyor

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-1]                                │
│                                                    │
│ Azure Portal - Ana Sayfa                          │
│ Sağ üst: Kullanıcı adı ve abonelik seçici        │
└────────────────────────────────────────────────────┘
```

---

### Adım 2: Azure Arc - SQL Server Licenses Bölümüne Gitme

Azure Portal'da **SQL Server licenses** bölümüne iki yoldan ulaşabilirsiniz:

#### Yöntem 1: Arama Çubuğu (Önerilen)

1. Portal üst kısmındaki **arama çubuğuna** tıklayın
2. **"Azure Arc"** yazın ve **Azure Arc** servisini seçin
3. Azure Arc sayfasında, sol menüden **"Data Services"** bölümüne gidin
4. **"SQL Server licenses"** seçeneğini tıklayın

**📸 EKRAN GÖRÜNTÜsÜ 2**: Azure Portal arama çubuğu, "Azure Arc" aranıyor

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-2]                                │
│                                                    │
│ Üst arama çubuğu: "Azure Arc"                     │
│ Dropdown: Azure Arc servisi görünüyor             │
└────────────────────────────────────────────────────┘
```

**📸 EKRAN GÖRÜNTÜsÜ 3**: Azure Arc sayfası, sol menü - Data Services altında SQL Server licenses

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-3]                                │
│                                                    │
│ Sol menü:                                         │
│   ├─ Overview                                     │
│   ├─ Data Services                                │
│   │   ├─ SQL Server instances                    │
│   │   └─ SQL Server licenses  ← BURAYA TIKLAYıN  │
│   └─ Infrastructure                               │
└────────────────────────────────────────────────────┘
```

#### Yöntem 2: Direkt Arama

1. Portal üst kısmındaki arama çubuğuna **"SQL Server licenses"** yazın
2. **"SQL Server licenses"** servisini doğrudan seçin

---

### Adım 3: Yeni Lisans Kaynağı Oluşturma

1. **SQL Server licenses** sayfasında **"+ Create"** butonuna tıklayın
2. Lisans tipi seçim ekranı açılacak
3. **"SQL Server physical core license"** seçeneğini seçin
   - **Not**: "SQL Server virtual core license" değil, **physical core** seçin

**📸 EKRAN GÖRÜNTÜsÜ 4**: SQL Server licenses sayfası, + Create butonu

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-4]                                │
│                                                    │
│ SQL Server licenses                               │
│ ┌──────────────────────────────────────────┐     │
│ │  + Create  │  Refresh  │  Manage view    │     │
│ └──────────────────────────────────────────┘     │
│                                                    │
│ Tabloda mevcut lisanslar görünür (varsa)         │
└────────────────────────────────────────────────────┘
```

**📸 EKRAN GÖRÜNTÜsÜ 5**: Lisans tipi seçim ekranı

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-5]                                │
│                                                    │
│ Select license type:                              │
│                                                    │
│ ┌──────────────────────────────────────────┐     │
│ │ ○ SQL Server virtual core license        │     │
│ │                                           │     │
│ │ ● SQL Server physical core license  ←    │     │
│ └──────────────────────────────────────────┘     │
│                                                    │
│ [ Cancel ]              [ Select ]                │
└────────────────────────────────────────────────────┘
```

---

### Adım 4: Temel Bilgileri Doldurma (Basics Tab)

Oluşturma sihirbazının **Basics** sekmesinde aşağıdaki bilgileri doldurun:

#### Project Details (Proje Detayları)

1. **Subscription**: Kullanmak istediğiniz Azure aboneliğini seçin
   - Dropdown'dan aboneliğinizi seçin

2. **Resource group**:
   - **Mevcut kaynak grubunu seçin** VEYA
   - **"Create new"** ile yeni kaynak grubu oluşturun
   - Örnek isim: `rg-sql-payg-licenses`

#### Instance Details (Kaynak Detayları)

3. **License name**: Lisans kaynağı için benzersiz bir isim verin
   - Örnek: `sql-enterprise-payg-prod-001`
   - İsimlendirme önerisi: `sql-[edition]-[environment]-[number]`

4. **Region**: Azure bölgesi seçin
   - SQL Server'ınızın bulunduğu konuma en yakın bölgeyi seçin
   - Türkiye için önerilen: **West Europe** veya **North Europe**
   - Örnek: `West Europe`

#### License Configuration (Lisans Yapılandırması)

5. **Billing Plan**:
   - **"PAYG" (Pay-As-You-Go)** seçin
   - Diğer seçenekler: "Paid" (mevcut lisansınız varsa)

6. **Physical Cores**:
   - SQL Server'ınızın çalıştığı sunucunun **fiziksel çekirdek sayısını** girin
   - **Minimum**: 4 core
   - **Maksimum**: Sınırsız (Enterprise için)
   - Örnek: `16`

7. **SQL Server Edition**:
   - **Enterprise** seçin (PAYG sadece Enterprise için çalışır)

8. **Activation State**:
   - **"Activated"** seçeneğini işaretleyin (activate immediately için)
   - Veya **"Deactivated"** seçip daha sonra aktif edebilirsiniz

**📸 EKRAN GÖRÜNTÜsÜ 6**: Basics tab, tüm alanlar doldurulmuş

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-6]                                │
│                                                    │
│ Create SQL Server physical core license           │
│                                                    │
│ Basics  │  Tags  │  Review + create               │
│ ───────                                           │
│                                                    │
│ Project Details                                   │
│  Subscription *        │ Azure Subscription 1 ▼   │
│  Resource group *      │ rg-sql-payg-licenses ▼   │
│                                                    │
│ Instance Details                                  │
│  License name *        │ sql-enterprise-payg-001  │
│  Region *              │ West Europe ▼            │
│                                                    │
│ License Configuration                             │
│  Billing Plan *        │ PAYG ▼                   │
│  Physical Cores *      │ 16                       │
│  Edition *             │ Enterprise ▼             │
│  Activation State *    │ ☑ Activated              │
│                                                    │
│ [ Previous ]            [ Next: Tags > ]          │
└────────────────────────────────────────────────────┘
```

---

### Adım 5: Etiketleme (Tags - Opsiyonel ama Önerilir)

Tags (etiketler), kaynakları organize etmek ve maliyet takibi yapmak için kullanılır.

1. **"Next: Tags"** butonuna tıklayın
2. Aşağıdaki etiketleri eklemenizi öneririz:

| Name (Anahtar) | Value (Değer) | Açıklama |
|----------------|---------------|----------|
| Environment | Production | Ortam (Production/Test/Dev) |
| Application | Database | Uygulama türü |
| CostCenter | IT-Database | Maliyet merkezi |
| Owner | db-admin@company.com | Sorumlu kişi |
| ManagedBy | Azure-PAYG | Yönetim şekli |

**📸 EKRAN GÖRÜNTÜsÜ 7**: Tags tab, örnek etiketler eklenmiş

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-7]                                │
│                                                    │
│ Create SQL Server physical core license           │
│                                                    │
│ Basics  │  Tags  │  Review + create               │
│         ───────                                   │
│                                                    │
│ Tags are name/value pairs that enable you to      │
│ categorize resources.                             │
│                                                    │
│  Name              │  Value                       │
│ ──────────────────────────────────────────────    │
│  Environment       │  Production                  │
│  Application       │  Database                    │
│  CostCenter        │  IT-Database                 │
│  Owner             │  db-admin@company.com        │
│  ManagedBy         │  Azure-PAYG                  │
│                                                    │
│ [ + Add ]                                         │
│                                                    │
│ [ Previous ]        [ Next: Review + create > ]   │
└────────────────────────────────────────────────────┘
```

---

### Adım 6: Gözden Geçirme ve Oluşturma (Review + Create)

1. **"Next: Review + create"** butonuna tıklayın
2. Azure, yapılandırmanızı doğrular (**Validation passed** mesajını görmelisiniz)
3. Aşağıdaki bilgileri gözden geçirin:

   - ✅ Subscription doğru mu?
   - ✅ Resource group doğru mu?
   - ✅ Core sayısı doğru mu?
   - ✅ Edition "Enterprise" olarak seçilmiş mi?
   - ✅ Activation state "Activated" mı?
   - ✅ Region uygun mu?

4. **Tahmini Aylık Maliyet** gösterilecek:
   - Örnek: ~$1,140/ay (16 core, Enterprise, West Europe için)

5. Tüm bilgiler doğruysa **"Create"** butonuna tıklayın

**📸 EKRAN GÖRÜNTÜsÜ 8**: Review + create tab, özet bilgiler

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-8]                                │
│                                                    │
│ Create SQL Server physical core license           │
│                                                    │
│ Basics  │  Tags  │  Review + create               │
│                  ────────────────                 │
│                                                    │
│ ✓ Validation passed                               │
│                                                    │
│ Basics                                            │
│  Subscription          Azure Subscription 1       │
│  Resource group        rg-sql-payg-licenses       │
│  License name          sql-enterprise-payg-001    │
│  Region                West Europe                │
│  Billing Plan          PAYG                       │
│  Physical Cores        16                         │
│  Edition               Enterprise                 │
│  Activation State      Activated                  │
│                                                    │
│ Estimated cost: ~$1,140.00/month                  │
│                                                    │
│ [ Previous ]                      [ Create ]      │
└────────────────────────────────────────────────────┘
```

---

### Adım 7: Deployment (Dağıtım) Süreci

**"Create"** butonuna tıkladıktan sonra:

1. **Deployment in progress** mesajı görünür
2. Azure kaynağı oluşturur (genellikle 30-60 saniye sürer)
3. **"Your deployment is complete"** mesajı gelir
4. **"Go to resource"** butonuna tıklayın

**📸 EKRAN GÖRÜNTÜsÜ 9**: Deployment tamamlandı ekranı

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-9]                                │
│                                                    │
│ ✓ Your deployment is complete                     │
│                                                    │
│ Deployment name: sql-enterprise-payg-001          │
│ Subscription:    Azure Subscription 1             │
│ Resource group:  rg-sql-payg-licenses             │
│                                                    │
│ Deployment details:                               │
│  ✓ sql-enterprise-payg-001  │  Succeeded          │
│                                                    │
│ Next steps:                                       │
│  [ Go to resource ]                               │
│  [ Download deployment details ]                  │
│                                                    │
│ [ Redeploy ]  [ Delete ]                          │
└────────────────────────────────────────────────────┘
```

---

### Adım 8: Lisans Kaynağını Görüntüleme

**"Go to resource"** butonuna tıkladıktan sonra, lisans kaynağınızın detay sayfasını göreceksiniz.

#### Overview (Genel Bakış) Sekmesi

Aşağıdaki bilgileri görüntüleyebilirsiniz:

| Bilgi | Açıklama |
|-------|----------|
| **Status** | Activated (Aktif) |
| **Billing Plan** | PAYG |
| **Edition** | Enterprise |
| **Physical Cores** | 16 |
| **Region** | West Europe |
| **Resource ID** | Tam kaynak kimliği |
| **Subscription ID** | Abonelik kimliği |

**📸 EKRAN GÖRÜNTÜsÜ 10**: SQL Server license resource - Overview sayfası

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-10]                               │
│                                                    │
│ sql-enterprise-payg-001                           │
│ SQL Server physical core license                  │
│                                                    │
│ [ Delete ]  [ Refresh ]  [ Configure ]            │
│                                                    │
│ Essentials                                        │
│ ┌──────────────────────────────────────────┐     │
│ │ Resource group    rg-sql-payg-licenses   │     │
│ │ Status            Activated              │     │
│ │ Location          West Europe            │     │
│ │ Subscription      Azure Subscription 1   │     │
│ │ Billing Plan      PAYG                   │     │
│ │ Edition           Enterprise             │     │
│ │ Physical Cores    16                     │     │
│ └──────────────────────────────────────────┘     │
│                                                    │
│ Left Menu:                                        │
│  ├─ Overview                                      │
│  ├─ Activity log                                  │
│  ├─ Access control (IAM)                          │
│  ├─ Tags                                          │
│  └─ Diagnose and solve problems                   │
└────────────────────────────────────────────────────┘
```

---

## 6. Lisans Doğrulama ve Yönetim

### Lisansın Aktif Olduğunu Doğrulama

Lisans kaynağı oluşturulduktan sonra:

1. **Status**: **"Activated"** olarak görünmelidir
2. **Billing Plan**: **"PAYG"** olmalıdır
3. Kaynak başarıyla oluşturulmuşsa, **faturalandırma otomatik başlar**

### Lisans Detaylarını Görüntüleme

Azure Portal'dan lisans kaynağınızı görüntüleyebilirsiniz:

1. **Azure Arc** > **Data Services** > **SQL Server licenses** sayfasına gidin
2. Oluşturduğunuz lisansı listeden seçin
3. **Overview** sekmesinde tüm detayları görün

### Lisans Yapılandırmasını Değiştirme

Oluşturulduktan sonra bazı özellikleri değiştirebilirsiniz:

1. Lisans kaynağının sayfasında **"Configure"** butonuna tıklayın
2. Değiştirebileceğiniz parametreler:
   - **Physical Cores**: Core sayısını artırabilir veya azaltabilirsiniz
   - **Activation State**: Aktif/Deaktif edebilirsiniz
   - **Tags**: Etiketleri güncelleyebilirsiniz

**Değiştirilemeyenler**:
- Billing Plan (PAYG'den değiştirilemez)
- Edition (Enterprise olarak sabitlenir)
- Region (oluşturulduktan sonra değiştirilemez)

**📸 EKRAN GÖRÜNTÜsÜ 11**: Configure sayfası

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-11]                               │
│                                                    │
│ Configure SQL Server license                      │
│                                                    │
│ License Configuration                             │
│  Physical Cores *      │ 16        [ - ]  [ + ]   │
│  Activation State *    │ ☑ Activated              │
│                                                    │
│ [ Save ]  [ Discard ]  [ Refresh ]                │
│                                                    │
│ NOTE: Changing the core count will affect your    │
│ monthly billing immediately.                      │
└────────────────────────────────────────────────────┘
```

### Denetim Sırasında Lisans Kanıtı Gösterme

Denetim (audit) sırasında lisansınızı kanıtlamak için:

1. Azure Portal'da lisans kaynağınıza gidin
2. **Overview** sayfasının ekran görüntüsünü alın
3. **Cost Management + Billing** > **Cost analysis** sayfasından fatura detaylarını gösterin
4. **Resource ID** ve **Subscription ID** bilgilerini paylaşın

Denetçilere söyleyebileceğiniz:
> "SQL Server Enterprise lisansımızı Azure Portal üzerinden Pay-As-You-Go modeli ile satın alıyoruz. İşte Azure kaynağımız ve fatura detaylarımız."

---

## 7. Fiyatlandırma ve Faturalandırma

### Fiyatlandırma Modeli

Azure SQL Server PAYG lisansı, **saatlik bazda** faturalandırılır:

- **Birim**: Fiziksel core başına saat
- **Minimum**: 4 core (Enterprise için)
- **Maksimum**: Sınırsız (Enterprise için)
- **Faturalandırma**: Saatlik, aylık faturaya yansır

### Fiyat Hesaplama Formülü

```
Aylık Maliyet = (Fiziksel Core Sayısı) × (Core Başına Saatlik Ücret) × (730 saat)
```

**Örnek (2024 West Europe Fiyatları - Tahmini)**:

- **Core başına saatlik ücret**: ~$0.10 (Enterprise Edition)
- **16 core için aylık maliyet**: 16 × $0.10 × 730 = **~$1,168/ay**

### Bölgelere Göre Fiyat Farklılıkları (Tahmini)

| Bölge | Core/Saat (USD) | 8 Core/Ay | 16 Core/Ay | 32 Core/Ay |
|-------|-----------------|-----------|------------|------------|
| **West Europe** | ~$0.10 | ~$584 | ~$1,168 | ~$2,336 |
| **North Europe** | ~$0.10 | ~$584 | ~$1,168 | ~$2,336 |
| **East US** | ~$0.09 | ~$525 | ~$1,050 | ~$2,100 |
| **Southeast Asia** | ~$0.11 | ~$642 | ~$1,284 | ~$2,568 |

**Not**: Güncel fiyatlar için [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) kullanın veya Azure Portal'da "Review + create" adımında tahmini maliyeti görüntüleyin.

### Normalized Core (NC) Hesaplaması

Microsoft, farklı edition'lar için **normalized core** konseptini kullanır:

- **Enterprise Edition**: 1 physical core = **4 NC**
- **Standard Edition**: 1 physical core = **1 NC**

Faturalandırma NC bazında yapılır.

**Örnek**:
- 16 physical core Enterprise = 16 × 4 = **64 NC**
- 16 physical core Standard = 16 × 1 = **16 NC**

### Faturalandırma Döngüsü

1. **Saatlik ölçüm**: Azure, her saat başı aktif core sayısını ölçer
2. **Aylık toplama**: Ay sonunda toplam kullanım hesaplanır
3. **Fatura**: Azure aboneliğinizin faturasına eklenir
4. **Ödeme**: Tanımlı ödeme yönteminizle otomatik tahsil edilir

### Faturaları Görüntüleme

1. Azure Portal'da **"Cost Management + Billing"** bölümüne gidin
2. **"Cost analysis"** sekmesini seçin
3. Filtreleme yapın:
   - **Resource type**: `Microsoft.AzureArcData/SqlServerLicenses`
   - **Resource name**: Lisans kaynağınızın adı
   - **Date range**: İstediğiniz tarih aralığı

**📸 EKRAN GÖRÜNTÜsÜ 12**: Cost Management - Cost Analysis sayfası

```
┌────────────────────────────────────────────────────┐
│ [Ekran Görüntüsü-12]                               │
│                                                    │
│ Cost analysis                                     │
│                                                    │
│ Filters:                                          │
│  Resource type: SqlServerLicenses                 │
│  Date range: Last 30 days                         │
│                                                    │
│ ┌──────────────────────────────────────────┐     │
│ │         Accumulated costs                │     │
│ │                                           │     │
│ │  $1,168.40                               │     │
│ │  ─────────────────────                   │     │
│ │         █████████████                     │     │
│ │                                           │     │
│ │  sql-enterprise-payg-001: $1,168.40      │     │
│ └──────────────────────────────────────────┘     │
│                                                    │
│ Resource breakdown:                               │
│  sql-enterprise-payg-001  │  $1,168.40            │
└────────────────────────────────────────────────────┘
```

### Maliyet Optimizasyon İpuçları

✅ **Doğru Core Sayısı Bildirin**
- Gerçekte kullandığınız core sayısını girin
- Eksik bildirirseniz audit riski, fazla bildirirseniz gereksiz maliyet

✅ **Kullanılmayan Lisansları Silin**
- Artık kullanılmayan SQL Server'lar için lisansları kaldırın
- Deactivate edebilir veya kaynağı silebilirsiniz

✅ **Deactivate Özelliğini Kullanın**
- Geçici olarak kullanılmayan sunucular için lisansı deaktive edin
- Faturalandırma durur, kaynak kalır

✅ **Azure Hybrid Benefit Değerlendirin**
- Mevcut Software Assurance lisansınız varsa
- PAYG yerine Azure Hybrid Benefit kullanarak tasarruf edin

✅ **Reserved Instances**
- Uzun vadeli kullanım için Azure Reserved Instances değerlendirin
- %30-70 tasarruf sağlayabilir

---

## 8. Etik ve Uyumluluk Hususları

### Güven Bazlı Lisanslama Nedir?

Azure SQL Server PAYG lisanslaması, **trust-based** (güven bazlı) bir modeldir:

- Microsoft, bildirdiğiniz bilgilere **güvenir**
- Sunucularınıza **bağlantı kurmaz**
- **Otomatik doğrulama yapmaz**
- **Denetim hakkını** saklı tutar

### Doğru Beyan Sorumluluğu

⚠️ **ETİK SORUMLULUK**: Doğru core sayısını bildirmek **sizin sorumluluğunuzdadır**.

**Yanlış beyan riskleri**:
- Microsoft lisans denetimleri (audit)
- Uyumsuzluk cezaları
- Geriye dönük ödeme yükümlülükleri
- Yasal sorunlar
- İtibar kaybı

### Microsoft Denetimleri (Audits)

Microsoft, müşterilerini denetleme hakkına sahiptir:

1. **Denetim bildirimi**: Microsoft, e-posta ile denetim başlatabilir
2. **Belge talebi**: SQL Server kurulumlarınızı, core sayılarınızı kanıtlamanız istenebilir
3. **Karşılaştırma**: Azure'da bildirdiğiniz core sayısı vs gerçek kurulum
4. **Uyumsuzluk durumunda**: Geriye dönük fatura + ceza ödenebilir

### Denetim Sırasında Yapılması Gerekenler

✅ **Hazırlıklı Olun**:
- Tüm SQL Server kurulumlarınızın dokümantasyonunu tutun
- Core sayısı kanıtlarını saklayın (donanım faturaları, sistem raporları)
- Azure Portal lisans kayıtlarınızın ekran görüntülerini arşivleyin

✅ **Şeffaf Olun**:
- Denetçilerle işbirliği yapın
- Tüm bilgileri eksiksiz paylaşın
- Hatalı beyan varsa düzeltin

✅ **Kayıt Tutun**:
- Lisans değişikliklerini belgeleyin
- Hangi tarihte kaç core bildirildiğini takip edin
- Fatura kayıtlarını saklayın

### SPLA ile Karşılaştırma

| Özellik | SPLA | Azure PAYG |
|---------|------|------------|
| **Güven bazlı** | ✅ Evet | ✅ Evet |
| **Self-declaration** | ✅ Evet | ✅ Evet |
| **Denetim riski** | ✅ Var | ✅ Var |
| **Aylık raporlama** | ✅ Zorunlu | ❌ Otomatik |
| **Microsoft ilişkisi** | 📝 SPLA sözleşmesi | 📝 Azure aboneliği |
| **Hosting sağlayıcılar için** | ✅ Uygun | ⚠️ Uygun değil |

### Tavsiyeler

1. **Gerçekçi Olun**: Gerçek core sayınızı bildirin
2. **Düzenli Kontrol**: 3-6 ayda bir Azure kayıtlarınızı gerçek kurulumla karşılaştırın
3. **Dokümantasyon**: Tüm değişiklikleri belgeleyin
4. **Eğitim**: Ekibinizi lisanslama kuralları hakkında eğitin
5. **Danışmanlık**: Belirsiz durumlarda Microsoft lisans uzmanlarına danışın

---

## 9. Sık Sorulan Sorular

### Genel Sorular

**S: PAYG lisansı hangi SQL Server sürümleri için geçerlidir?**

C: Sadece **SQL Server Enterprise Edition** için geçerlidir. Standard, Web, Express, Developer edition'ları için PAYG desteklenmez.

---

**S: On-premises SQL Server'ım için kullanabilir miyim?**

C: **Evet**, on-premises, AWS, GCP veya herhangi bir ortamda çalışan SQL Server Enterprise için kullanabilirsiniz. Azure Arc agent kurmanıza gerek yoktur.

---

**S: Azure Arc agent kurmam gerekiyor mu?**

C: **Hayır**, bu self-declaration modelinde Azure Arc Connected Machine Agent kurmanıza gerek yoktur. Sadece Azure Portal'dan core sayısını bildirirsiniz.

---

**S: SQL Server'ıma bir bağlantı kuruluyor mu?**

C: **Hayır**, Azure, SQL Server'ınıza hiçbir bağlantı kurmaz. Bu tamamen güven bazlı bir lisanslama modelidir.

---

**S: Birden fazla SQL Server instance'ı için tek lisans kullanabilir miyim?**

C: **Evet**, physical core lisansı oluşturduğunuzda, o fiziksel sunucudaki tüm SQL Server instance'ları kapsanır. Ancak doğru toplam core sayısını bildirmelisiniz.

---

**S: Lisansı durdurabilir miyim?**

C: **Evet**, iki yöntem var:
1. **Deactivate**: Lisans kaynağını "Deactivated" duruma alın → Faturalandırma durur
2. **Delete**: Kaynak grubundan lisans kaynağını silin → Kalıcı olarak kaldırılır

---

### Teknik Sorular

**S: Core sayısını nasıl belirliyorum?**

C: SQL Server'ın çalıştığı sunucunun **fiziksel çekirdek sayısını** kullanın. Hyperthreading dahil değildir.

SQL Server'da sorgu:
```sql
SELECT
    cpu_count AS [Logical CPU Count],
    cpu_count / hyperthread_ratio AS [Physical CPU Count]
FROM sys.dm_os_sys_info;
```

**Physical CPU Count** değerini kullanın.

---

**S: Sanal makinelerde nasıl hesaplanır?**

C: Eğer SQL Server bir VM'de çalışıyorsa:
- **Virtual core (v-core)** lisansı kullanabilirsiniz VEYA
- **Physical core (p-core)** lisansı: Host sunucunun fiziksel core sayısını bildirirsiniz (unlimited virtualization için)

---

**S: Lisansı oluşturduktan sonra core sayısını değiştirebilir miyim?**

C: **Evet**, lisans kaynağının **Configure** sayfasından core sayısını artırabilir veya azaltabilirsiniz. Değişiklik hemen geçerli olur ve faturalandırma güncellenir.

---

**S: PAYG lisansı aktif olduktan sonra SQL Server'ı yeniden başlatmam gerekiyor mu?**

C: **Hayır**, SQL Server'da hiçbir değişiklik yapmıyorsunuz. Sadece Azure tarafında beyanınızı yapıyorsunuz, dolayısıyla SQL Server'ı yeniden başlatmanıza gerek yok.

---

**S: Birden fazla Azure bölgesinde lisans oluşturabilir miyim?**

C: **Evet**, her bölge için ayrı lisans kaynağı oluşturabilirsiniz. Ancak her kaynak bağımsız faturalandırılır.

---

### Lisanslama ve Uyumluluk

**S: Audit sırasında lisansımı nasıl kanıtlarım?**

C:
1. Azure Portal'daki lisans kaynağınızın **Overview** sayfasının ekran görüntüsünü alın
2. **Cost Management + Billing** > **Cost analysis** sayfasından fatura detaylarını gösterin
3. **Resource ID** ve aylık fatura toplamınızı paylaşın
4. Denetçiye "Lisansımı Azure Portal üzerinden PAYG modeli ile ödüyorum" deyin

---

**S: Software Assurance'ım varsa PAYG'ye ihtiyacım var mı?**

C: **Hayır**, Software Assurance (SA) ile **Azure Hybrid Benefit** kullanarak daha uygun maliyetli lisanslama yapabilirsiniz. PAYG, SA'nız yoksa veya kısa vadeli ihtiyaçlar için uygundur.

---

**S: PAYG lisansı SQL Server Developer Edition için de geçerli mi?**

C: **Hayır**, Developer Edition ücretsizdir ve PAYG gerektirmez. PAYG sadece **Enterprise Edition** için geçerlidir.

---

**S: Unlimited virtualization avantajını kullanabilir miyim?**

C: **Evet**, physical core lisansı oluşturursanız ve host sunucunun tüm çekirdeklerini bildirirseniz, o host üzerindeki tüm VM'lerde çalışan SQL Server instance'ları kapsanır.

---

**S: High Availability (HA) ve Disaster Recovery (DR) için ekstra lisans gerekir mi?**

C: **Hayır**, PAYG lisansınız ile HA/DR için **passive (pasif) instance'lar ücretsizdir**. Ancak Microsoft lisanslama kurallarına uygun olarak yapılandırılmalıdır.

---

### Maliyet Soruları

**S: Fiyatlar sabit mi yoksa değişken mi?**

C: Azure fiyatları **bölgeye göre değişir** ve Microsoft tarafından güncellenebilir. Güncel fiyatlar için [Azure Pricing](https://azure.microsoft.com/pricing/) sayfasını kontrol edin.

---

**S: İlk ay tam mı ücretlendiriliyor?**

C: **Hayır**, faturalandırma **saatlik bazda pro-rated** (oransal) yapılır. Ayın ortasında oluştursanız bile sadece kullandığınız saatler için ödeme yaparsınız.

---

**S: Lisansı deaktive ettiğimde ne olur?**

C: Lisansı **Deactivated** duruma aldığınızda:
- Faturalandırma **hemen durur**
- Kaynak kalır (silmezsiniz)
- İstediğiniz zaman tekrar **Activated** duruma alabilirsiniz

---

**S: Minimum taahhüt süresi var mı?**

C: **Hayır**, PAYG modelinde minimum taahhüt yoktur. İstediğiniz zaman lisansı silebilir veya deaktive edebilirsiniz.

---

## 10. Sorun Giderme

### Sorun 1: "Create" Butonu Görünmüyor

**Belirtiler**: SQL Server licenses sayfasında "+ Create" butonu göremiyorum.

**Olası Nedenler**:
- Yetersiz Azure izinleri
- Abonelik durumu pasif
- Azure Arc servisi bölgenizde mevcut değil

**Çözümler**:

1. **İzinleri Kontrol Edin**:
   - Azure Portal > **Subscriptions** > Aboneliğiniz > **Access control (IAM)**
   - Rolünüzü kontrol edin: Owner, Contributor veya Cost Management Contributor olmalısınız
   - Yoksa yöneticinizden izin isteyin

2. **Abonelik Durumunu Kontrol Edin**:
   - Azure Portal > **Subscriptions**
   - Status: **Active** olmalı
   - Ödeme yöntemi tanımlı olmalı

3. **Farklı Tarayıcı Deneyin**:
   - Cache temizleyin veya incognito modda açın
   - Edge, Chrome veya Firefox kullanın

---

### Sorun 2: Lisans Kaynağı Oluşturulamıyor

**Hata Mesajı**: "Deployment failed" veya "Validation failed"

**Olası Nedenler**:
1. Geçersiz kaynak grubu
2. Bölge kısıtlaması
3. Core sayısı minimum şartını karşılamıyor
4. İsim çakışması

**Çözümler**:

1. **Kaynak Grubu**:
   - Yeni bir kaynak grubu oluşturun
   - Mevcut kaynak grubuna erişiminiz olduğunu doğrulayın

2. **Bölge Seçimi**:
   - Farklı bir Azure bölgesi seçin (örn: West Europe)
   - [Azure Products by Region](https://azure.microsoft.com/global-infrastructure/services/) sayfasından Azure Arc kullanılabilirliğini kontrol edin

3. **Core Sayısı**:
   - Minimum **4 core** girmeniz gerekir
   - Pozitif bir sayı girin

4. **İsim Çakışması**:
   - Benzersiz bir isim kullanın
   - Format: `sql-enterprise-payg-[unique-id]`

---

### Sorun 3: Lisans Durumu "Deactivated" Görünüyor

**Belirtiler**: Lisans oluşturdunuz ama status "Deactivated" gösteriyor.

**Neden**: Oluşturma sırasında "Activation State" seçeneğini işaretlemediniz.

**Çözüm**:

1. Lisans kaynağınıza gidin
2. **Configure** butonuna tıklayın
3. **"Activated"** seçeneğini işaretleyin
4. **Save** butonuna tıklayın
5. Status 1-2 dakika içinde güncellenecektir

---

### Sorun 4: Faturada PAYG Maliyeti Görünmüyor

**Belirtiler**: Lisans oluşturdum ama Cost Management'ta göremiyorum.

**Olası Nedenler**:
- Lisans yeni oluşturuldu (faturalandırma gecikebilir)
- Lisans "Deactivated" durumda
- Filtre yanlış ayarlanmış

**Çözümler**:

1. **Bekleyin**:
   - Faturalandırma verileri **2-4 saat** gecikebilir
   - Ertesi gün tekrar kontrol edin

2. **Lisans Durumunu Kontrol Edin**:
   - Lisans kaynağına gidin
   - Status: **Activated** olmalı

3. **Filtreyi Düzeltin**:
   - **Cost Management + Billing** > **Cost analysis**
   - Filtre:
     - Resource type: `Microsoft.AzureArcData/SqlServerLicenses`
     - Date range: Last 30 days
     - Resource name: Lisans kaynağınızın adı

4. **Abonelik Seçimini Kontrol Edin**:
   - Doğru abonelikte olduğunuzdan emin olun

---

### Sorun 5: Lisans Silinemiyor

**Hata Mesajı**: "Cannot delete resource" veya "Resource is locked"

**Olası Nedenler**:
- Kaynak kilidi (Resource lock) var
- Yetersiz izinler

**Çözümler**:

1. **Kaynak Kilidini Kontrol Edin**:
   - Lisans kaynağına gidin
   - Sol menüden **"Locks"** seçin
   - Varsa kilidi silin (Delete veya ReadOnly)

2. **İzinleri Kontrol Edin**:
   - Owner veya Contributor rolüne sahip olmalısınız
   - Yoksa yöneticinizden izin isteyin

3. **Önce Deactivate Edin**:
   - Lisansı **Deactivated** duruma alın
   - Birkaç dakika bekleyin
   - Sonra silmeyi deneyin

---

### Sorun 6: Core Sayısı Değiştirilemiyor

**Belirtiler**: Configure sayfasında core sayısını değiştiremiyorum.

**Olası Nedenler**:
- Lisans tipi "physical core" değil
- Yetersiz izinler
- Tarayıcı hatası

**Çözümler**:

1. **Lisans Tipini Kontrol Edin**:
   - Overview sayfasında "License Type" alanını kontrol edin
   - "Physical Core License" olmalı

2. **İzinleri Kontrol Edin**:
   - Contributor veya Owner rolüne sahip olmalısınız

3. **Tarayıcıyı Yenileyin**:
   - Sayfayı yenileyin (F5)
   - Cache temizleyin
   - Farklı tarayıcı deneyin

---

### Sorun 7: Azure Arc Sayfası Açılmıyor

**Belirtiler**: Azure Arc servisine erişemiyorum.

**Olası Nedenler**:
- Azure Arc servisi aboneliğinizde etkin değil
- Bölgesel kısıtlama

**Çözümler**:

1. **Servisi Aktifleştirin**:
   - Azure Portal arama çubuğunda "Azure Arc" arayın
   - İlk kez kullanıyorsanız "Get started" yapmanız istenebilir

2. **Abonelik Limitlerini Kontrol Edin**:
   - Azure Portal > **Subscriptions** > **Usage + quotas**
   - Azure Arc için limit olup olmadığını kontrol edin

---

### Azure Destek ile İletişim

Sorun devam ediyorsa Azure Support ile iletişime geçin:

1. **Azure Portal** > **Help + support** (Sağ üst köşe, soru işareti ikonu)
2. **New support request** seçin
3. Aşağıdaki bilgileri sağlayın:
   - **Issue type**: Technical
   - **Service type**: Azure Arc
   - **Problem type**: SQL Server licensing
   - **Subscription**: İlgili abonelik
   - **Problem**: Detaylı açıklama + ekran görüntüleri

**Destek Seviyeleri**:
- **Basic**: Ücretsiz, billing ve subscription desteği
- **Developer**: Teknik destek ($29/ay)
- **Standard**: 7/24 teknik destek ($100/ay)
- **Professional Direct**: Kritik destek ($1000/ay)

---

## 11. Kaynaklar

### Resmi Microsoft Dokümantasyonu

📚 **Azure Arc SQL Server**
- [Manage Licensing and Billing - SQL Server enabled by Azure Arc](https://learn.microsoft.com/en-us/sql/sql-server/azure-arc/manage-license-billing?view=sql-server-ver17)
- [Configure SQL Server - SQL Server enabled by Azure Arc](https://learn.microsoft.com/en-us/sql/sql-server/azure-arc/manage-configuration?view=sql-server-ver17)
- [Move to Pay-As-You-Go Subscription](https://learn.microsoft.com/en-us/sql/sql-server/azure-arc/manage-pay-as-you-go-transition?view=sql-server-ver17)

📚 **Azure Hybrid Benefit**
- [Create SQL Server license assignments](https://learn.microsoft.com/en-us/azure/cost-management-billing/scope-level/create-sql-license-assignments)
- [Centrally managed Azure Hybrid Benefit](https://learn.microsoft.com/en-us/azure/cost-management-billing/scope-level/overview-azure-hybrid-benefit-scope)

📚 **Fiyatlandırma**
- [Azure Arc Pricing](https://azure.microsoft.com/en-us/pricing/details/azure-arc/core-control-plane/)
- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

📚 **SQL Server Licensing**
- [SQL Server Licensing Guide](https://www.microsoft.com/licensing/docs/view/SQL-Server)
- [SQL Server on Azure VMs FAQ](https://learn.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/frequently-asked-questions-faq?view=azuresql)

### Videolar ve Eğitimler

🎥 **Microsoft Learn**
- [Save money with Arc SQL Server licensing](https://learn.microsoft.com/en-us/shows/data-exposed/save-money-with-arc-sql-server-licensing-what-you-need-to-know-data-exposed)
- [Azure Arc Overview](https://learn.microsoft.com/en-us/azure/azure-arc/overview)

### Topluluk Kaynakları

💬 **Forumlar**
- [Microsoft Q&A - Azure Arc](https://learn.microsoft.com/en-us/answers/tags/191/azure-arc)
- [Stack Overflow - Azure Arc](https://stackoverflow.com/questions/tagged/azure-arc)

📰 **Blog Yazıları**
- [Save money on SQL Server licensing with Azure Arc](https://www.microsoft.com/en-us/sql-server/blog/2024/11/06/save-money-on-microsoft-sql-server-licensing-with-microsoft-azure-arc/)
- [Azure Arc and SQL Pay-As-You-Go](https://4sight.cloud/blog/azure-arc-and-sql-pay-as-you-go-redefining-database-management-and-licensing)

### PowerShell ve CLI Komutları

Otomasyon için PowerShell örnekleri:

```powershell
# Azure'a bağlan
Connect-AzAccount

# Değişkenler
$resourceGroupName = "rg-sql-payg-licenses"
$location = "westeurope"
$licenseName = "sql-enterprise-payg-001"
$physicalCores = 16

# Kaynak grubu oluştur
New-AzResourceGroup -Name $resourceGroupName -Location $location

# SQL Server physical core lisansı oluştur (REST API ile)
$body = @{
    location = $location
    properties = @{
        billingPlan = "PAYG"
        physicalCores = $physicalCores
        activationState = "Activated"
        licenseCategory = "Core"
    }
} | ConvertTo-Json

Invoke-AzRestMethod `
    -Method PUT `
    -Path "/subscriptions/{subscriptionId}/resourceGroups/$resourceGroupName/providers/Microsoft.AzureArcData/SqlServerLicenses/$licenseName?api-version=2024-03-01-preview" `
    -Payload $body
```

Azure CLI örneği:

```bash
# Azure'a giriş
az login

# Değişkenler
RESOURCE_GROUP="rg-sql-payg-licenses"
LOCATION="westeurope"
LICENSE_NAME="sql-enterprise-payg-001"
PHYSICAL_CORES=16

# Kaynak grubu oluştur
az group create --name $RESOURCE_GROUP --location $LOCATION

# Lisans oluştur (REST API ile)
az rest --method put \
  --url "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.AzureArcData/SqlServerLicenses/$LICENSE_NAME?api-version=2024-03-01-preview" \
  --body "{
    \"location\": \"$LOCATION\",
    \"properties\": {
      \"billingPlan\": \"PAYG\",
      \"physicalCores\": $PHYSICAL_CORES,
      \"activationState\": \"Activated\",
      \"licenseCategory\": \"Core\"
    }
  }"
```

### İletişim ve Destek

📧 **Microsoft Licensing Uzmanları**
- Volume Licensing Service Center (VLSC): [https://www.microsoft.com/licensing/](https://www.microsoft.com/licensing/)
- Microsoft hesap temsilcinize ulaşın

📞 **Azure Destek**
- Azure Portal'dan support request açın
- Telefon: Bölgenize göre değişir ([İletişim bilgileri](https://azure.microsoft.com/support/options/))

---

## Ekran Görüntüleri Listesi

Bu dokümanda kullanılması önerilen ekran görüntüleri:

| # | Ekran Görüntüsü | Açıklama |
|---|-----------------|----------|
| **1** | Azure Portal Ana Sayfa | Abonelik seçimi gösteriliyor |
| **2** | Azure Portal Arama | "Azure Arc" aranıyor |
| **3** | Azure Arc - SQL Server Licenses | Sol menü navigasyonu |
| **4** | SQL Server Licenses Sayfası | + Create butonu |
| **5** | Lisans Tipi Seçimi | Physical core seçimi |
| **6** | Basics Tab | Tüm alanlar doldurulmuş |
| **7** | Tags Tab | Örnek etiketler |
| **8** | Review + Create | Özet ve tahmini maliyet |
| **9** | Deployment Complete | Başarılı dağıtım |
| **10** | License Resource - Overview | Lisans detayları |
| **11** | Configure Sayfası | Core sayısı değiştirme |
| **12** | Cost Management | Maliyet analizi |

---

## Özet: Hızlı Başlangıç Checklist

İşte adım adım yapılacaklar listesi:

- [ ] Azure hesabına giriş yap
- [ ] Doğru aboneliği seç
- [ ] SQL Server'ın fiziksel core sayısını öğren
- [ ] Azure Portal'da "Azure Arc" servisine git
- [ ] Data Services > SQL Server licenses > + Create
- [ ] "SQL Server physical core license" seç
- [ ] Basics tab: Subscription, Resource group, License name, Region
- [ ] License Configuration: PAYG, Core sayısı, Enterprise, Activated
- [ ] Tags tab: Environment, Application, vb. ekle (opsiyonel)
- [ ] Review + create: Tüm bilgileri doğrula
- [ ] Create butonuna tıkla
- [ ] Deployment tamamlandıktan sonra "Go to resource"
- [ ] Status: Activated olduğunu doğrula
- [ ] Cost Management'tan faturayı kontrol et

---

## Son Notlar

### Önemli Hatırlatmalar

✅ **Sadece Enterprise Edition** için geçerlidir
✅ **Agent kurmanıza gerek yoktur**
✅ **Güven bazlı lisanslama** - doğru beyan yapın
✅ **Saatlik faturalandırma** - kullandığınız kadar ödersiniz
✅ **Denetim riski vardır** - dokümantasyonunuzu saklayın

### Yasal Uyarı

⚠️ Bu doküman, Azure SQL Server PAYG lisanslaması hakkında bilgi vermek amacıyla hazırlanmıştır. **Resmi Microsoft lisanslama danışmanlığı yerine geçmez**. Karmaşık lisanslama senaryolarında mutlaka Microsoft lisans uzmanlarına danışın.

### Geri Bildirim

Bu dokümanda hata, eksik bilgi veya güncelleme ihtiyacı görürseniz lütfen belirtin.

---

**Doküman Bilgileri**

- **Versiyon**: 2.0 (Güncellenmiş)
- **Tarih**: 19 Aralık 2024
- **Yazar**: Claude (Anthropic AI)
- **Kapsam**: Azure SQL Server Pay-As-You-Go Lisanslama
- **Hedef Kitle**: Teknik ekip, Lisans yöneticileri, Son kullanıcılar

**Son Güncelleme**: Microsoft Learn dokümantasyonu (Aralık 2024) temel alınarak hazırlanmıştır.

---

**Başarılar Dileriz!** 🎉

Azure SQL Server PAYG lisanslaması ile esnek, güvenilir ve maliyet-etkin lisans yönetimi yapabilirsiniz. Sorularınız için Azure desteğine veya Microsoft lisans uzmanlarına başvurabilirsiniz.
