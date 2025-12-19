# Azure Arc SQL Server Pay-As-You-Go (PAYG) Lisanslama Kılavuzu

## İçindekiler
1. [Genel Bakış](#genel-bakış)
2. [Önkoşullar](#önkoşullar)
3. [Adım Adım Kurulum](#adım-adım-kurulum)
4. [Lisans Doğrulama](#lisans-doğrulama)
5. [Fiyatlandırma ve Faturalandırma](#fiyatlandırma-ve-faturalandırma)
6. [Sık Sorulan Sorular](#sık-sorulan-sorular)
7. [Sorun Giderme](#sorun-giderme)

---

## Genel Bakış

Azure Arc SQL Server Pay-As-You-Go (PAYG) özelliği, SQL Server Enterprise Edition için Azure üzerinden kullandığınız kadar ödeme yapmanıza olanak tanıyan bir lisanslama modelidir.

### Özellikler
- **Agent Gerektirmez**: Herhangi bir Azure Arc agent kurulumu gerekmez
- **Sadece Portal Üzerinden**: Azure Portal'dan basit aktivasyon
- **Enterprise Edition**: Yalnızca SQL Server Enterprise Edition için geçerlidir
- **Kullandığınız Kadar Öde**: Saatlik bazda faturalandırma
- **Anında Aktivasyon**: Dakikalar içinde aktif hale gelir

### Avantajlar
✅ Ön ödeme gerektirmez
✅ Yazılım Güvencesi (Software Assurance) gerekmez
✅ Uzun vadeli taahhüt gerektirmez
✅ Lisans yönetimi Azure'da otomatik yapılır
✅ Maliyet şeffaflığı ve kontrolü

---

## Önkoşullar

### Azure Abonelik Gereksinimleri
- Aktif bir Azure aboneliği
- Abonelik üzerinde aşağıdaki izinlerden en az biri:
  - **Owner** (Sahip)
  - **Contributor** (Katkıda Bulunan)
  - **SQL Server Contributor** rolü

### SQL Server Gereksinimleri
- **Sürüm**: SQL Server 2012 veya üzeri
- **Edition**: Enterprise Edition (PAYG sadece Enterprise için geçerlidir)
- **İşletim Sistemi**: Windows veya Linux
- **Konum**: On-premises, diğer bulut sağlayıcıları veya Azure VM

### Önemli Notlar
⚠️ SQL Server Standard veya diğer sürümler için PAYG desteklenmez
⚠️ SQL Server zaten lisanslıysa, PAYG'ye geçiş mevcut lisansınızı geçersiz kılabilir
⚠️ Azure Arc PAYG aktivasyonu Azure tarafında yapılır, SQL Server'a bağlantı gerekmez

---

## Adım Adım Kurulum

### Adım 1: Azure Portal'a Giriş Yapın

1. Tarayıcınızda [https://portal.azure.com](https://portal.azure.com) adresine gidin
2. Azure hesabınızla oturum açın
3. Doğru abonelikte olduğunuzdan emin olun

### Adım 2: SQL Server Licenses Sayfasına Gidin

1. Azure Portal'ın üst kısmındaki **arama çubuğuna** tıklayın
2. **"SQL Server licenses"** yazın
3. Arama sonuçlarından **"SQL Server licenses"** seçeneğini seçin

   **Alternatif Yöntem:**
   - Sol menüden **"All services"** (Tüm hizmetler) seçin
   - **"Databases"** kategorisine gidin
   - **"SQL Server licenses"** seçeneğini bulun

### Adım 3: Yeni Lisans Kaydı Oluşturun

1. **SQL Server licenses** sayfasında **"+ Create"** (Oluştur) butonuna tıklayın

2. **Basics (Temel Bilgiler)** sekmesinde aşağıdaki bilgileri doldurun:

   **Project Details (Proje Detayları):**
   - **Subscription**: Kullanmak istediğiniz Azure aboneliğini seçin
   - **Resource group**:
     - Mevcut bir kaynak grubu seçin VEYA
     - **"Create new"** ile yeni bir kaynak grubu oluşturun
     - Örnek: `rg-sql-payg-licenses`

   **Instance Details (Örnek Detayları):**
   - **License name**: Lisans kaydı için benzersiz bir isim verin
     - Örnek: `sql-enterprise-payg-prod`
   - **Region**: SQL Server'ınızın bulunduğu bölgeye en yakın Azure bölgesini seçin
     - Örnek: `West Europe`, `North Europe`, vb.

3. **License type (Lisans Türü)** bölümünde:
   - **"Pay-as-you-go"** seçeneğini seçin
   - **Edition**: **"Enterprise"** seçin (sadece bu seçenek PAYG için geçerlidir)

4. **Billing configuration (Faturalandırma Yapılandırması):**
   - **Cores (Çekirdek Sayısı)**: SQL Server'ınızın kullandığı fiziksel çekirdek sayısını girin
     - Minimum: 4 çekirdek
     - SQL Server'ınızın çalıştığı sunucunun toplam çekirdek sayısı
   - **Virtual Machines**: Kaç VM üzerinde SQL Server çalıştığını belirtin

### Adım 4: Etiketleme (Opsiyonel ama Önerilir)

1. **"Next: Tags"** butonuna tıklayın
2. Kaynak yönetimi için etiketler ekleyin:
   ```
   Environment: Production
   Application: Database
   CostCenter: IT-Database
   Owner: DBAdmin
   ```

### Adım 5: Gözden Geçirme ve Oluşturma

1. **"Review + create"** butonuna tıklayın
2. Tüm bilgileri kontrol edin:
   - Abonelik ve kaynak grubu doğru mu?
   - Çekirdek sayısı doğru mu?
   - Bölge uygun mu?
3. Aylık tahmini maliyeti gözden geçirin
4. **"Create"** butonuna tıklayın

### Adım 6: Aktivasyon Doğrulaması

1. Dağıtım tamamlandığında **"Go to resource"** butonuna tıklayın
2. Lisans kaydının durumunu kontrol edin:
   - **Status**: **Active** olmalıdır
   - **License type**: **Pay-as-you-go** görünmelidir
   - **Edition**: **Enterprise** görünmelidir
3. **Overview** sayfasında şunları görebilirsiniz:
   - Toplam çekirdek sayısı
   - Güncel faturalandırma durumu
   - Oluşturma tarihi

---

## Lisans Doğrulama

### SQL Server'da Lisans Kontrolü

SQL Server Management Studio (SSMS) üzerinden lisansınızın Azure PAYG olarak tanındığını doğrulayabilirsiniz:

```sql
-- SQL Server sürüm ve lisans bilgisi
SELECT
    SERVERPROPERTY('ProductVersion') AS Version,
    SERVERPROPERTY('ProductLevel') AS ProductLevel,
    SERVERPROPERTY('Edition') AS Edition,
    SERVERPROPERTY('LicenseType') AS LicenseType;
```

### Azure Portal'da Lisans Kontrolü

1. **SQL Server licenses** sayfasına gidin
2. Oluşturduğunuz lisans kaydını seçin
3. **"Monitoring"** > **"Metrics"** bölümünden kullanım metriklerini görüntüleyin

### Aktivasyon Süresi

- Lisans kaydı oluşturulduktan sonra **5-10 dakika** içinde aktif hale gelir
- SQL Server tarafında herhangi bir değişiklik gerekmez
- Yeniden başlatma gerekmez

---

## Fiyatlandırma ve Faturalandırma

### Fiyatlandırma Modeli

Azure Arc SQL Server PAYG lisansı **saatlik bazda** faturalandırılır:

- **Faturalandırma Birimi**: Fiziksel çekirdek başına saat
- **Minimum**: 4 çekirdek
- **Faturalandırma Periyodu**: Saatlik, aylık faturaya yansır

### Maliyet Tahmini

2024 fiyatları (bölgelere göre değişkenlik gösterir):

| Bölge | Çekirdek/Saat (USD) | 4 Çekirdek/Ay (USD) | 16 Çekirdek/Ay (USD) |
|-------|---------------------|---------------------|----------------------|
| West Europe | ~$0.95 | ~$684 | ~$2,736 |
| North Europe | ~$0.95 | ~$684 | ~$2,736 |
| East US | ~$0.85 | ~$612 | ~$2,448 |

**Not**: Güncel fiyatlar için [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) kullanın.

### Faturalandırma Detayları

1. **Azure Portal** > **Cost Management + Billing** > **Cost analysis** sayfasında maliyetlerinizi görüntüleyin
2. Filtre ekleyin:
   - **Resource type**: `Microsoft.AzureArcData/SqlServerLicenses`
   - **Tag**: Oluşturduğunuz etiketler

### Maliyet Optimizasyonu

✅ **Doğru Çekirdek Sayısı**: Sadece kullandığınız çekirdek sayısını bildirin
✅ **Kullanılmayan Lisansları Silin**: Artık kullanılmayan kayıtları kaldırın
✅ **Reserved Instances**: Uzun vadeli kullanım için Azure Reservations değerlendirin
✅ **Hybrid Benefit**: Mevcut Software Assurance lisanslarınız varsa Azure Hybrid Benefit kullanın

---

## Sık Sorulan Sorular

### Genel Sorular

**S: PAYG hangi SQL Server sürümleri için geçerlidir?**
C: Sadece **SQL Server Enterprise Edition** için geçerlidir. Standard, Web, Express sürümleri desteklenmez.

**S: On-premises SQL Server'ım için kullanabilir miyim?**
C: Evet, on-premises, diğer bulut sağlayıcıları (AWS, GCP) veya Azure VM'lerde çalışan SQL Server için kullanabilirsiniz.

**S: Azure Arc agent kurmam gerekiyor mu?**
C: Hayır, PAYG lisansı için Azure Arc agent kurulumu gerekmez. Sadece Azure Portal'dan aktivasyon yeterlidir.

**S: SQL Server'ıma bir bağlantı kuruluyor mu?**
C: Hayır, Azure PAYG lisansı Azure tarafında bir kayıt oluşturur. SQL Server'ınıza bağlantı veya agent kurulumu gerekmez.

**S: Mevcut lisansımdan PAYG'ye geçebilir miyim?**
C: Evet, ancak mevcut lisansınızı kullanmayı bırakmanız ve PAYG'ye geçmeniz gerekir. Bu durumda maliyet karşılaştırması yapmanız önerilir.

### Teknik Sorular

**S: PAYG lisansı aktif olduktan sonra SQL Server'ı yeniden başlatmam gerekiyor mu?**
C: Hayır, SQL Server'da herhangi bir değişiklik veya yeniden başlatma gerekmez.

**S: Çekirdek sayısını nasıl belirliyorum?**
C: SQL Server'ın çalıştığı sunucunun **fiziksel çekirdek sayısını** kullanın. Hyperthreading dahil değildir.

```sql
-- SQL Server'da çekirdek sayısını öğrenme
SELECT cpu_count AS [Logical CPU Count],
       cpu_count / hyperthread_ratio AS [Physical CPU Count]
FROM sys.dm_os_sys_info;
```

**S: Birden fazla SQL Server instance'ı için tek lisans kullanabilir miyim?**
C: Hayır, her SQL Server instance veya sunucu için ayrı PAYG lisansı oluşturmalısınız.

**S: Lisansı durdurabilir miyim?**
C: Evet, Azure Portal'dan lisans kaydını silebilirsiniz. Silme işlemi sonrası faturalandırma durur.

### Lisanslama ve Uyumluluk

**S: Audit sırasında lisansımı nasıl kanıtlarım?**
C: Azure Portal'daki lisans kaydınızı ve fatura detaylarınızı gösterebilirsiniz. Azure, Microsoft lisanslaması ile tamamen uyumludur.

**S: Software Assurance'ım varsa PAYG'ye ihtiyacım var mı?**
C: Hayır, Software Assurance ile **Azure Hybrid Benefit** kullanarak daha uygun maliyetli lisanslama yapabilirsiniz.

**S: PAYG lisansı SQL Server Developer Edition için de geçerli mi?**
C: Hayır, Developer Edition ücretsizdir ve PAYG gerektirmez. PAYG sadece Enterprise Edition için geçerlidir.

---

## Sorun Giderme

### "Create" Butonu Görünmüyor

**Neden**: Yetersiz izinler

**Çözüm**:
1. Abonelik üzerinde **Contributor** veya **Owner** rolüne sahip olduğunuzdan emin olun
2. Azure AD yöneticinizden gerekli izinleri isteyin
3. `az role assignment list --assignee <your-email>` komutu ile rollerinizi kontrol edin

### Lisans Kaydı Oluşturulamıyor

**Hata Mesajı**: "Deployment failed"

**Olası Nedenler ve Çözümler**:

1. **Kaynak grubu mevcut değil**:
   - Yeni bir kaynak grubu oluşturun veya mevcut birini seçin

2. **Bölge kısıtlaması**:
   - Farklı bir Azure bölgesi seçin
   - Aboneliğinizin seçili bölgede kaynak oluşturma iznine sahip olduğunu doğrulayın

3. **Çekirdek sayısı minimum şartı**:
   - En az 4 çekirdek girmeniz gerekmektedir

### Lisans Aktif Değil

**Durum**: Status "Pending" veya "Failed" görünüyor

**Çözüm**:
1. 10-15 dakika bekleyin (aktivasyon süresi)
2. Tarayıcınızı yenileyin (F5)
3. Hata devam ederse:
   - Lisans kaydını silin
   - Yeni bir kayıt oluşturun
   - Azure destek talebi açın

### Fatura Sorunları

**Durum**: Faturada PAYG lisans maliyeti görünmüyor

**Çözüm**:
1. **Cost Management + Billing** > **Cost analysis** sayfasına gidin
2. Tarih aralığını genişletin (son 30 gün)
3. Filtreyi kontrol edin:
   - Resource type: `Microsoft.AzureArcData/SqlServerLicenses`
4. Lisans kaydının **Active** durumda olduğunu doğrulayın

### Azure Support İletişim

Sorun devam ediyorsa:

1. **Azure Portal** > **Help + support** > **New support request**
2. **Issue type**: Technical
3. **Service type**: SQL Server on Azure Virtual Machines
4. **Problem type**: Licensing
5. Detaylı açıklama ve ekran görüntüleri ekleyin

---

## Ek Kaynaklar

### Resmi Microsoft Dokümantasyonu
- [Azure Arc SQL Server Overview](https://learn.microsoft.com/azure/azure-arc/data/overview)
- [SQL Server Licensing](https://learn.microsoft.com/sql/sql-server/sql-server-licensing)
- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

### PowerShell ile Otomasyon

Birden fazla lisans kaydı oluşturmanız gerekiyorsa PowerShell kullanabilirsiniz:

```powershell
# Azure'a bağlan
Connect-AzAccount

# Değişkenler
$resourceGroupName = "rg-sql-payg-licenses"
$location = "westeurope"
$licenseName = "sql-enterprise-payg-001"
$coreCount = 8

# Kaynak grubu oluştur (mevcut değilse)
New-AzResourceGroup -Name $resourceGroupName -Location $location

# SQL PAYG lisans kaydı oluştur
New-AzSqlServerLicense `
    -ResourceGroupName $resourceGroupName `
    -Name $licenseName `
    -Location $location `
    -Edition "Enterprise" `
    -LicenseType "PayAsYouGo" `
    -CoreCount $coreCount `
    -Tag @{
        Environment = "Production"
        Application = "Database"
    }

Write-Host "Lisans kaydı başarıyla oluşturuldu: $licenseName" -ForegroundColor Green
```

### Azure CLI ile Otomasyon

```bash
# Azure'a giriş yap
az login

# Değişkenler
RESOURCE_GROUP="rg-sql-payg-licenses"
LOCATION="westeurope"
LICENSE_NAME="sql-enterprise-payg-001"
CORE_COUNT=8

# Kaynak grubu oluştur
az group create --name $RESOURCE_GROUP --location $LOCATION

# SQL PAYG lisans kaydı oluştur
az sql server-license create \
    --resource-group $RESOURCE_GROUP \
    --name $LICENSE_NAME \
    --location $LOCATION \
    --edition Enterprise \
    --license-type PayAsYouGo \
    --core-count $CORE_COUNT \
    --tags Environment=Production Application=Database

echo "Lisans kaydı başarıyla oluşturuldu: $LICENSE_NAME"
```

---

## Sonuç

Azure Arc SQL Server PAYG lisansı, SQL Server Enterprise Edition'ı esneklik ve maliyet avantajı ile kullanmanızı sağlar. Agent kurulumu gerektirmeden, sadece Azure Portal üzerinden dakikalar içinde aktif hale getirebilir ve kullandığınız kadar ödeyebilirsiniz.

### Önemli Hatırlatmalar

✅ Sadece SQL Server Enterprise Edition için geçerlidir
✅ Agent veya bağlantı gerekmez
✅ Saatlik bazda faturalandırılır
✅ Doğru çekirdek sayısını bildirin
✅ Maliyet yönetimi için düzenli takip yapın

---

**Doküman Versiyonu**: 1.0
**Son Güncelleme**: 19 Aralık 2024
**Hazırlayan**: Claude (Anthropic)

**Not**: Azure Portal arayüzü ve özellikler zaman içinde güncellenebilir. En güncel bilgi için [Microsoft Learn](https://learn.microsoft.com) dokümantasyonunu kontrol edin.
