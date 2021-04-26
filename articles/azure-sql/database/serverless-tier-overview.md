---
title: Sunucusuz işlem katmanı
description: Bu makalede, yeni sunucusuz bilgi işlem katmanı açıklanmakta ve Azure SQL veritabanı için mevcut sağlanan işlem katmanıyla karşılaştırılır.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 4/16/2021
ms.openlocfilehash: d5b0c8e60632be5e058900680dc376b7f0761150
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781586"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL veritabanı sunucusuz
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sunucusuz işlem, Azure SQL Veritabanı'ndaki tek veritabanları için iş yükü talebine göre işlemi otomatik olarak ölçeklendiren ve saniyede kullanılan işlem miktarını faturalayan bir işlem katmanıdır. Ayrıca sunucusuz işlem katmanı etkinlik dışı dönemlerde veritabanını otomatik olarak duraklatır (bu dönemlerde yalnızca depolama faturalanır) ve etkinliğe dönüldüğünde veritabanlarını otomatik olarak sürdürür.

## <a name="serverless-compute-tier"></a>Sunucusuz işlem katmanı

Azure SQL veritabanı 'ndaki tek veritabanlarına yönelik sunucusuz işlem katmanı, bir işlem otomatik ölçeklendirme aralığı ve otomatik duraklatma gecikmesi tarafından parametrelenir. Bu parametrelerin yapılandırması, veritabanı performans deneyimini ve işlem maliyetini şekillendirilir.

![Sunucusuz faturalandırma](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Performans yapılandırması

- **Minimum sanal çekirdekler** ve **maksimum sanal çekirdekler** , veritabanı için kullanılabilir işlem kapasitesi aralığını tanımlayan yapılandırılabilir parametrelerdir. Bellek ve GÇ sınırları belirtilen vCore aralığıyla orantılıdır.  
- **Otomatik duraklatma gecikmesi** , veritabanının otomatik olarak duraklatılmadan önce devre dışı olması gereken süreyi tanımlayan yapılandırılabilir bir parametredir. Sonraki oturum açma veya diğer etkinlik gerçekleştiğinde veritabanı otomatik olarak sürdürülür.  Alternatif olarak, Otomatik duraklatma devre dışı bırakılabilir.

### <a name="cost"></a>Maliyet

- Sunucusuz bir veritabanının maliyeti, işlem maliyeti ve depolama maliyetinin özetidir.
- İşlem kullanımı, yapılandırılan minimum ve maksimum limitlerin arasında olduğunda, işlem maliyeti sanal çekirdeği ve kullanılan belleği temel alır.
- İşlem kullanımı, yapılandırılan minimum limitlerin altındaysa, işlem maliyeti en düşük sanal çekirdekleri ve en az belleğe göre yapılandırılır.
- Veritabanı duraklatıldığında, işlem maliyeti sıfırdır ve yalnızca depolama maliyetleri tahakkuk edilir.
- Depolama maliyeti, sağlanan işlem katmanının ile aynı şekilde belirlenir.

Daha fazla maliyet ayrıntısı için bkz. [faturalandırma](serverless-tier-overview.md#billing).

## <a name="scenarios"></a>Senaryolar

Sunucusuz model aralıklı, tahmin edilemez kullanım düzenleri olan ve boşta kullanım dönemlerinden sonra işlemin ısınması için biraz beklemeyi kaldırabilen tek veritabanları için fiyat-performans açısından iyileştirilmiştir. Buna karşılık sağlanan işlem katmanı, ortalama kullanımı yüksek olan ve işlem ısınması için beklemeyi kaldıramayan tek veritabanları veya elastik havuzlardaki birden çok veritabanı için fiyat-performans açısından iyileştirilmiştir.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Sunucusuz işlem için uygun senaryolar

- Kesintili kullanım desenlerine sahip tek veritabanları, zaman içinde etkinlik dışı ve düşük ortalama işlem kullanımı dönemleriyle birlikte oluşmuştur.
- Sağlanan işlem katmanındaki tek veritabanları, genellikle ölçeklendirildi ve hizmet için işlem yeniden oluşturmayı tercih eden müşteriler.
- SQL veritabanı 'nda dağıtımdan önce işlem boyutlandırmanın zor veya tahmin edilmesi mümkün olmayan kullanım geçmişi olmayan yeni tek veritabanları.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Sağlanan işlem için uygun senaryolar

- Daha düzenli, öngörülebilir kullanım desenleri ve zaman içinde daha fazla ortalama işlem kullanımı olan tek veritabanları.
- Daha sık bellek kırpmasını veya duraklatılmış bir durumdan devam eden gecikmelerden kaynaklanan performans dengelerini karşılamaz veritabanları.
- Daha iyi fiyat performansı iyileştirmesi için esnek havuzlarda birleştirilemeyen, kesintili kullanım desenlerine sahip birden çok veritabanı.

## <a name="comparison-with-provisioned-compute-tier"></a>Sağlanan işlem katmanıyla karşılaştırma

Aşağıdaki tabloda sunucusuz bilgi işlem katmanı ve sağlanan işlem katmanı arasındaki farklılıklar özetlenmektedir:

| | **Sunucusuz işlem** | **Sağlanan işlem** |
|:---|:---|:---|
|**Veritabanı kullanım deseninin**| Zaman içinde daha düşük ortalama işlem kullanımı ile öngörülemeyen kullanım | Zamana göre daha fazla ortalama işlem kullanımı veya elastik havuzlar kullanan birden çok veritabanı içeren daha düzenli kullanım düzenleri.|
| **Performans yönetimi çabaları** |Daha az|Daha fazla|
|**İşlem ölçekleme**|Automatic|El ile|
|**İşlem yanıtlama hızı**|Etkin olmayan dönemlerden sonra düşük|Hemen|
|**Faturalandırma ayrıntı düzeyi**|/Saniye|/Saat|

## <a name="purchasing-model-and-service-tier"></a>Model ve hizmet katmanı satın alma

SQL veritabanı sunucusuz, şu anda yalnızca sanal çekirdek satın alma modelinde 5. nesil donanımda Genel Amaçlı katmanında desteklenir.

## <a name="autoscaling"></a>Otomatik ölçeklendirme

### <a name="scaling-responsiveness"></a>Ölçeklendirme yanıt hızı

Genel olarak sunucusuz veritabanları, en fazla Vçekirdekler değeri tarafından ayarlanan limitlerde istenen herhangi bir işlem için kesintiye uğramadan kaynak talebini karşılamak üzere yeterli kapasiteye sahip bir makine üzerinde çalıştırılır. Bazen, makine kaynak talebini birkaç dakika içinde karşılayamaz, Yük Dengeleme otomatik olarak gerçekleşir. Örneğin, kaynak talebi 4 sanal çekirdektir, ancak yalnızca 2 sanal çekirdek varsa, 4 sanal çekirdek sağlanmadan önce yük dengelenmesi birkaç dakika sürebilir. Veritabanı, bağlantı kesildiğinde işlemin sonunda kısa bir dönem haricinde yük dengelemesi sırasında çevrimiçi kalır.

### <a name="memory-management"></a>Bellek yönetimi

Sunucusuz veritabanları için bellek, sağlanan işlem veritabanlarından daha sık geri kazanılır. Bu davranış sunucusuz 'de maliyetleri denetlemek için önemlidir ve performansı etkileyebilir.

#### <a name="cache-reclamation"></a>Önbellek geri kazanma

Sağlanan işlem veritabanlarının aksine, CPU veya etkin önbellek kullanımı düşük olduğunda SQL önbelleğinden alınan bellek sunucusuz bir veritabanından geri kazanılır.

- En son kullanılan önbellek girişlerinin toplam boyutu bir süre eşiğinin altına düştüğünde etkin önbellek kullanımı düşük kabul edilir.
- Cache geri kazanma tetiklendiğinde, hedef önbellek boyutu artımlı olarak önceki boyutunun bir kesirine düşürülür ve geri kazanma yalnızca kullanım düşük kalırsa devam eder.
- Cache geri kazanma gerçekleştiğinde, çıkarmak için önbellek girdileri seçme ilkesi, bellek baskısı yüksek olduğunda sağlanan işlem veritabanları için aynı seçim ilkesidir.
- Önbellek boyutu, yapılandırılmış minimum sanal çekirdekler tarafından tanımlanan en düşük bellek sınırının altına hiçbir şekilde indirgenmez.

Hem sunucusuz hem de sağlanan işlem veritabanlarında, kullanılabilir tüm bellek kullanılıyorsa önbellek girdileri çıkartılamayabilir.

CPU kullanımı düşük olduğunda, etkin önbellek kullanımının kullanım düzenine bağlı olarak yüksek kalabileceğini ve bellek geri kazanma engel olabileceğini unutmayın.  Ayrıca, düzenli arka plan işlemlerinin önceki Kullanıcı etkinliğine yanıt vermemesi nedeniyle, bellek geri kazanma gerçekleşmeden önce Kullanıcı etkinliği durdurulmadan daha fazla gecikme olabilir.  Örneğin, silme işlemleri ve QDS temizleme görevleri, silinmek üzere işaretlenmiş hayalet kayıtlar oluşturur, ancak veri sayfalarını önbelleğe okumayı gerektirebilecek hayalet temizleme işlemi çalıştırılıncaya kadar fiziksel olarak silinmez.

#### <a name="cache-hydration"></a>Önbellek hidrasyonu

Veriler diskten aynı şekilde ve sağlanan veritabanları için aynı hızda getirilirken, SQL önbelleği artar. Veritabanı meşgulse, önbelleğin en yüksek bellek sınırına kadar kısıtlı olarak büyümesine izin verilir.

## <a name="auto-pausing-and-auto-resuming"></a>Otomatik duraklatma ve otomatik sürdürme

### <a name="auto-pausing"></a>Otomatik duraklatma

Otomatik duraklatma gecikmesi süresi için aşağıdaki koşulların tümü doğru olursa otomatik duraklatma tetiklenir:

- Sayı oturumu = 0
- Kullanıcı havuzunda çalışan Kullanıcı iş yükü için CPU = 0

İsterseniz otomatik duraklamayı devre dışı bırakmak için bir seçenek sağlanır.

Aşağıdaki özellikler otomatik duraklamayı desteklemez, ancak otomatik ölçeklendirmeyi destekler.  Aşağıdaki özelliklerden herhangi biri kullanılırsa, Otomatik duraklatma devre dışı bırakılmalıdır ve veritabanı eylemsizlik süresi ne olursa olsun veritabanı çevrimiçi olarak kalır:

- Coğrafi çoğaltma (etkin coğrafi çoğaltma ve otomatik yük devretme grupları).
- Uzun süreli yedek saklama (LTR).
- SQL Data Sync 'de kullanılan eşitleme veritabanı.  Eşitleme veritabanlarının aksine, hub ve üye veritabanları otomatik duraklamayı destekler.
- DNS diğer ad
- Elastik Işlerde kullanılan iş veritabanı (Önizleme).

Otomatik duraklatma, veritabanının çevrimiçi olmasını gerektiren bazı hizmet güncelleştirmelerinin dağıtımı sırasında geçici olarak engellenir.  Bu gibi durumlarda, hizmet güncelleştirmesi tamamlandıktan sonra otomatik duraklatma yeniden kullanılabilir duruma gelir.

### <a name="auto-resuming"></a>Otomatik olarak yeniden sürdürülüyor

Aşağıdaki koşullardan herhangi biri doğruysa, otomatik olarak yeniden başlatma tetiklenir:

|Özellik|Tetikleyiciyi otomatik olarak yeniden başlatma|
|---|---|
|Kimlik doğrulaması ve yetkilendirme|Oturum aç|
|Tehdit algılama|Veritabanı veya sunucu düzeyinde tehdit algılama ayarlarını etkinleştirme/devre dışı bırakma.<br>Tehdit algılama ayarlarını veritabanı veya sunucu düzeyinde değiştirme.|
|Veri bulma ve sınıflandırma|Duyarlılık etiketlerini ekleme, değiştirme, silme veya görüntüleme|
|Denetim|Denetim kayıtlarını görüntüleme.<br>Denetim ilkesini güncelleştirme veya görüntüleme.|
|Veri maskeleme|Veri maskeleme kuralları ekleme, değiştirme, silme veya görüntüleme|
|Saydam veri şifrelemesi|Saydam veri şifrelemesinin durumunu veya durumunu görüntüleme|
|Güvenlik açığı değerlendirmesi|Etkinse geçici taramalar ve düzenli taramalar|
|Sorgu (performans) veri deposu|Sorgu deposu ayarlarını değiştirme veya görüntüleme|
|Performans önerileri|Performans önerilerini görüntüleme veya uygulama|
|Otomatik ayarlama|Otomatik Dizin oluşturma gibi otomatik ayarlama önerilerini uygulama ve doğrulama|
|Veritabanı kopyalama|Kopya olarak veritabanı oluşturun.<br>BACPAC dosyasına dışarı aktarın.|
|SQL Data Sync|Yapılandırılabilir bir zamanlamaya göre çalışan ya da el ile gerçekleştirilen merkez ve üye veritabanları arasında eşitleme|
|Belirli veritabanı meta verilerini değiştirme|Yeni veritabanı etiketleri ekleniyor.<br>Maksimum sanal çekirdekler, en az sanal çekirdek veya oto duraklatma gecikmesi değiştiriliyor.|
|SQL Server Management Studio (SSMS)|18,1 'den önceki SSMS sürümlerinin kullanılması ve sunucudaki herhangi bir veritabanı için yeni bir sorgu penceresinin açılması, aynı sunucuda otomatik olarak duraklatılan tüm veritabanlarını sürdürür. SSMS sürüm 18,1 veya üzeri kullanılıyorsa bu davranış oluşmaz.|

Yukarıda listelenen işlemlerden herhangi birini gerçekleştirerek izleme, yönetim veya diğer çözümler otomatik olarak devam ettirmeyi tetikler.

Otomatik sürdürme, veritabanının çevrimiçi olmasını gerektiren bazı hizmet güncelleştirmelerinin dağıtımı sırasında da tetiklenir.

### <a name="connectivity"></a>Bağlantı

Sunucusuz bir veritabanı duraklatıldığında, ilk oturum açma işlemi veritabanını sürdürür ve 40613 hata koduyla veritabanının kullanılamadığını belirten bir hata döndürür. Veritabanı devam ettirdikten sonra, bağlantı kurmak için oturum açma yeniden denenmelidir. Bağlantı yeniden deneme mantığının bulunduğu veritabanı istemcilerinin değiştirilmesi gerekmez.

### <a name="latency"></a>Gecikme süresi

Otomatik olarak devam etmeyi ve otomatik olarak duraklatma gecikmesi, otomatik olarak devam etmek için 1 dakikalık ve otomatik olarak duraklatılacağı 1-10 dakikadır.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Müşteri tarafından yönetilen saydam veri şifrelemesi (BYOK)

[Müşteri tarafından yönetilen saydam veri şifrelemesi](transparent-data-encryption-byok-overview.md) (bYok) kullanılıyorsa ve anahtar silme veya iptal gerçekleştiğinde sunucusuz veritabanı otomatik olarak duraklatılmışsa, veritabanı otomatik duraklatılmış durumda kalır.  Bu durumda, veritabanının bir sonraki sürdürülme sonrasında veritabanı yaklaşık 10 dakika içinde erişilemez hale gelir.  Veritabanı erişilemez duruma geldikten sonra kurtarma işlemi, sağlanan işlem veritabanları ile aynı olur.  Anahtar silme veya iptal gerçekleştiğinde sunucusuz veritabanı çevrimiçiyse, veritabanı, sağlanan işlem veritabanları ile aynı şekilde yaklaşık 10 dakika içinde erişilemez hale gelir.

## <a name="onboarding-into-serverless-compute-tier"></a>Sunucusuz işlem katmanına ekleme

Yeni bir veritabanı oluşturmak veya var olan bir veritabanını sunucusuz bir işlem katmanına taşımak, sağlanan işlem katmanında yeni bir veritabanı oluşturma ile aynı kalıbı izler ve aşağıdaki iki adımı içerir.

1. Hizmet hedefini belirtin. Hizmet hedefi, hizmet katmanını, donanım üretimini ve maks. sanal çekirdekleri bir bütün olarak kullanıma önerir. Hizmet hedefi seçenekleri için bkz. [sunucusuz kaynak limitleri](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. İsteğe bağlı olarak, varsayılan değerlerini değiştirmek için en düşük sanal çekirdekleri ve oto duraklatma gecikmesini belirtin. Aşağıdaki tabloda bu parametreler için kullanılabilir değerler gösterilmektedir.

   |Parametre|Değer seçimleri|Varsayılan değer|
   |---|---|---|---|
   |En düşük sanal çekirdekler|Yapılandırılan en fazla sanal çekirdek sayısını gösterir- [kaynak sınırlarına](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)bakın.|0,5 sanal çekirdekler|
   |Oto duraklatma gecikmesi|En az: 60 dakika (1 saat)<br>Maksimum: 10080 dakika (7 gün)<br>Artımlar: 10 dakika<br>Oto duraklamayı devre dışı bırak:-1|60 dakika|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Sunucusuz işlem katmanında yeni bir veritabanı oluşturun

Aşağıdaki örnekler sunucusuz işlem katmanında yeni bir veritabanı oluşturur.

#### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Bkz. [hızlı başlangıç: Azure SQL veritabanı 'nda Azure Portal kullanarak tek bir veritabanı oluşturma](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>PowerShell kullanma

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Azure CLI kullanma

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) kullanma

T-SQL kullanırken, en düşük sanal çekirdekler ve oto duraklatma gecikmesi için varsayılan değerler uygulanır.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Ayrıntılar için bkz. [veritabanı oluşturma](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Sağlanan işlem katmanından bir veritabanını sunucusuz işlem katmanına taşıma

Aşağıdaki örneklerde, bir veritabanı sağlanan işlem katmanından sunucusuz işlem katmanına taşınır.

#### <a name="use-powershell"></a>PowerShell kullanma


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Azure CLI kullanma

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) kullanma

T-SQL kullanırken, varsayılan değerler en düşük sanal çekirdekler ve otomatik duraklatma gecikmesi için uygulanır.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Ayrıntılar için bkz. [alter database](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Bir veritabanını sunucusuz işlem katmanından sağlanan işlem katmanına taşıma

Bir sunucusuz veritabanı, sağlanan bir işlem veritabanını sunucusuz bir işlem katmanına taşıma ile aynı şekilde, sağlanan bir işlem katmanına taşınabilir.

## <a name="modifying-serverless-configuration"></a>Sunucusuz yapılandırmayı değiştirme

### <a name="use-powershell"></a>PowerShell kullanma

Maksimum veya en düşük sanal çekirdekleri ve oto duraklatma gecikmesini değiştirmek,, ve bağımsız değişkenleri kullanılarak PowerShell 'deki [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) komutu kullanılarak gerçekleştirilir `MaxVcore` `MinVcore` `AutoPauseDelayInMinutes` .

### <a name="use-the-azure-cli"></a>Azure CLI kullanma

Maksimum veya en düşük sanal çekirdekleri ve oto duraklatma gecikmesini değiştirmek,, ve bağımsız değişkenlerini kullanarak Azure CLı 'deki [az SQL DB Update](/cli/azure/sql/db#az_sql_db_update) komutu kullanılarak gerçekleştirilir `capacity` `min-capacity` `auto-pause-delay` .


## <a name="monitoring"></a>İzleme

### <a name="resources-used-and-billed"></a>Kullanılan ve faturalandırılan kaynaklar

Sunucusuz bir veritabanının kaynakları uygulama paketi, SQL örneği ve Kullanıcı kaynak havuzu varlıkları tarafından kapsüllenir.

#### <a name="app-package"></a>Uygulama paketi

Uygulama paketi, veritabanının sunucusuz veya sağlanmış bir işlem katmanında olup olmamasına bakılmaksızın bir veritabanı için en dıştaki kaynak yönetimi sınırıdır. Uygulama paketi, hepsi bir arada SQL veritabanındaki bir veritabanı tarafından kullanılan tüm Kullanıcı ve sistem kaynaklarının kapsamını kapsayan tam metin araması gibi SQL örneğini ve dış hizmetleri içerir. SQL örneği genellikle uygulama paketi genelinde genel kaynak kullanımını ayırır.

#### <a name="user-resource-pool"></a>Kullanıcı kaynak havuzu

Kullanıcı kaynak havuzu, veritabanının sunucusuz veya sağlanmış bir işlem katmanında olup olmamasından bağımsız olarak bir veritabanı için en çok kaynak yönetimi sınırıdır. Kullanıcı kaynak havuzu kapsamları, SELECT, INSERT, UPDATE ve DELETE gibi DDL sorguları tarafından oluşturulan kullanıcı iş yükü için CPU ve g/ç sorguları. Bu sorgular genellikle uygulama paketindeki kullanım oranının en önemli oranını temsil eder.

### <a name="metrics"></a>Ölçümler

Bir sunucusuz veritabanının uygulama paketinin ve Kullanıcı havuzunun kaynak kullanımını izlemeye yönelik ölçümler aşağıdaki tabloda listelenmiştir:

|Varlık|Metric|Açıklama|Birimler|
|---|---|---|---|
|Uygulama paketi|app_cpu_percent|Uygulama tarafından, uygulama için izin verilen en fazla Vçekirdelere göre kullanılan sanal çekirdekler yüzdesi.|Yüzde|
|Uygulama paketi|app_cpu_billed|Raporlama döneminde uygulama için faturalandırılan işlem miktarı. Bu süre boyunca ödenen miktar, bu ölçümün ve vCore birim fiyatının ürünüdür. <br><br>Bu ölçümün değerleri, en fazla CPU kullanımı ve her saniye kullanılan bellek için toplanan zamana göre belirlenir. Kullanılan miktar, en düşük sanal çekirdekler ve minimum bellek tarafından ayarlanan şekilde sağlanan minimum miktardan azsa, sağlanan minimum miktar faturalandırılır.İşlemci amacıyla CPU 'yu bellek ile karşılaştırmak için, bellek miktarı GB cinsinden vCore başına 5 GB olarak yeniden ayarlayarak sanal çekirdek birimlerine normalleştirilmelidir.|Sanal çekirdek Saniyeler|
|Uygulama paketi|app_memory_percent|Uygulama tarafından uygulama için izin verilen en fazla belleğe göre kullanılan bellek yüzdesi.|Yüzde|
|Kullanıcı havuzu|cpu_percent|Kullanıcı iş yükü tarafından Kullanıcı iş yükü için izin verilen en fazla sanal çekirdeğe göre kullanılan sanal çekirdekler yüzdesi.|Yüzde|
|Kullanıcı havuzu|data_IO_percent|Kullanıcı iş yükü tarafından Kullanıcı iş yükü için izin verilen en fazla veri ıOPS 'ye göre kullanılan veri ıOPS yüzdesi.|Yüzde|
|Kullanıcı havuzu|log_IO_percent|Kullanıcı iş yükü tarafından Kullanıcı iş yükü için izin verilen en fazla günlük MB/sn 'ye göre kullanılan günlük MB/sn yüzdesi.|Yüzde|
|Kullanıcı havuzu|workers_percent|Kullanıcı iş yükü tarafından Kullanıcı iş yükü için izin verilen en fazla çalışanlara göre kullanılan çalışanların yüzdesi.|Yüzde|
|Kullanıcı havuzu|sessions_percent|Kullanıcı iş yükü tarafından Kullanıcı iş yükü için izin verilen en fazla oturumlara göre kullanılan oturumların yüzdesi.|Yüzde|

### <a name="pause-and-resume-status"></a>Durumu duraklatma ve devam etmeyi

Azure portal veritabanı durumu, içerdiği veritabanlarını listeleyen sunucunun Genel Bakış bölmesinde görüntülenir. Veritabanı durumu, veritabanının genel bakış bölmesinde de görüntülenir.

Bir veritabanının duraklatma ve devam durumunu sorgulamak için aşağıdaki komutları kullanma:

#### <a name="use-powershell"></a>PowerShell kullanma

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Azure CLI kullanma

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Kaynak sınırları

Kaynak sınırları için bkz. [sunucusuz işlem katmanı](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Faturalandırma

Faturalandırılan işlem miktarı, her saniye kullanılan en yüksek CPU ve bellek sayısıdır. Kullanılan CPU miktarı ve kullanılan bellek miktarı her biri için sağlanan minimum tutardan azsa, sağlanan miktar faturalandırılır. İşlemci amacıyla CPU 'yu bellek ile karşılaştırmak için, bellek miktarı GB cinsinden vCore başına 5 GB olarak yeniden ayarlayarak sanal çekirdek birimlerine normalleştirilmelidir.

- **Faturalandırılan kaynak**: CPU ve bellek
- **Faturalandırılan miktar**: Vcore birim fiyatı * Max (en az sanal çekirdek, sanal çekirdek, en az bellek gb * 1/3, bellek GB kullanıldı * 1/3) 
- **Faturalama sıklığı**: saniye başına

VCore birim fiyatı, saniye başına sanal çekirdek başına maliyettir. Belirli bir bölgedeki belirli birim fiyatları için [Azure SQL Veritabanı fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/sql-database/single/) bakın.

Faturalandırılan işlem miktarı aşağıdaki ölçüm tarafından sunulur:

- **Ölçüm**: App_cpu_billed (sanal çekirdek saniye)
- **Tanım**: Max (min sanal çekirdekler, sanal çekirdekler, en az bellek gb * 1/3, bellek GB kullanılan * 1/3)
- **Raporlama sıklığı**: dakika başına

Bu miktar saniyede hesaplanır ve 1 dakikadan fazla toplanır.

### <a name="minimum-compute-bill"></a>Minimum işlem faturanız

Sunucusuz bir veritabanı duraklatılmışsa, işlem faturanız sıfırdır.  Sunucusuz bir veritabanı duraklatılmadığı takdirde, en düşük işlem faturanız en fazla (en düşük sanal çekirdek, en az bellek GB * 1/3) temel alan sanal çekirdek miktarından daha az olmaz.

Örnekler:

- Sunucusuz bir veritabanının duraklatılmadığını ve 3,0 GB dakikalık belleğe karşılık gelen 8 maks sanal çekirdek ve 1 dakikalık sanal çekirdek ile yapılandırıldığını varsayalım.  Daha sonra minimum işlem faturanız en fazla (1 sanal çekirdek, 3,0 GB * 1 sanal çekirdek/3 GB) = 1 sanal çekirdek tabanlıdır.
- Sunucusuz bir veritabanının duraklatılmadığını ve 6 GB dakikalık belleğe karşılık 2,1 gelen 4 maksimum sanal çekirdek ve 0,5 minimum Vcore ile yapılandırıldığını varsayalım.  Daha sonra minimum işlem faturanız en yüksek (0,5 sanal çekirdek, 2,1 GB * 1 sanal çekirdek/3 GB) = 0,7 sanal çekirdekleri temel alır.

Sunucusuz için [Azure SQL Veritabanı Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?service=sql-database) , yapılandırılan en fazla ve en düşük sanal çekirdek sayısına göre yapılandırılabilir en düşük belleği tespit etmek için kullanılabilir.  Kural olarak, yapılandırılan en düşük sanal çekirdekler 0,5 sanal çekirdekten fazlaysa, en düşük işlem faturanız yapılandırılan en düşük bellekten ve yalnızca yapılandırılan minimum sanal çekirdek sayısına göre belirlenir.

### <a name="example-scenario"></a>Örnek senaryo

1 dakikalık sanal çekirdek ve 4 maks sanal çekirdeklerle yapılandırılmış sunucusuz bir veritabanını göz önünde bulundurun.  Bu, 3 GB ve daha fazla bellek ve 12 GB maksimum bellek ile aynıdır.  Otomatik duraklatma gecikmesini 6 saat olarak, veritabanı iş yükünün ise 24 saatlik bir dönemde ilk 2 saat boyunca etkin olduğunu ve aksi takdirde devre dışı olduğunu varsayalım.    

Bu durumda, veritabanı ilk 8 saat boyunca işlem ve depolama için faturalandırılır.  Veritabanı, ikinci saatten sonra devre dışı bırakılsa bile, veritabanı çevrimiçi olduğunda sağlanan minimum işlem temelinde sonraki 6 saat içinde işlem için faturalandırılır.  Veritabanı duraklatıldıktan sonra yalnızca depolama, 24 saatlik sürenin geri kalanı üzerinden faturalandırılır.

Daha kesin olarak, bu örnekteki işlem faturanız aşağıdaki gibi hesaplanır:

|Zaman aralığı|her saniye kullanılan sanal çekirdekler|Her saniye kullanılan GB|Faturalandırılan işlem boyutu|zaman aralığı içinde faturalandırılan sanal çekirdek Saniyeler|
|---|---|---|---|---|
|0:00-1:00|4|9|kullanılan sanal çekirdekler|4 sanal çekirdek * 3600 saniye = 14400 sanal çekirdek saniye|
|1:00-2:00|1|12|Kullanılan bellek|12 GB * 1/3 * 3600 saniye = 14400 sanal çekirdek saniye|
|2:00-8:00|0|0|Sağlanan minimum bellek|3 GB * 1/3 * 21600 saniye = 21600 sanal çekirdek saniye|
|8:00-24:00|0|0|Durakladığında faturalandırılan işlem yok|0 sanal çekirdek saniye|
|24 saat üzerinden faturalandırılan toplam vCore saniye||||50400 sanal çekirdek saniye|

İşlem birimi fiyatının $0.000145/vCore/Second olduğunu varsayalım.  Ardından bu 24 saatlik dönem için faturalandırılan işlem, faturalandırılan işlem birimi fiyatının ve sanal çekirdek saniyenin ürünüdür: $0.000145/vCore/Second * 50400 sanal çekirdek saniyesi ~ $7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hibrit Avantajı ve ayrılmış kapasite

Azure Hibrit Avantajı (AHB) ve ayrılmış kapasite iskontoları sunucusuz işlem katmanına uygulanmaz.

## <a name="available-regions"></a>Kullanılabilir bölgeler

Sunucusuz bilgi işlem katmanı, aşağıdaki bölgeler dışında Dünya çapında kullanılabilir: Çin Doğu, Çin Kuzey, Almanya Orta, Almanya Kuzeydoğu ve US Gov Orta (Iowa).

## <a name="next-steps"></a>Sonraki adımlar

- Başlamak için bkz. [hızlı başlangıç: Azure SQL veritabanı 'nda Azure Portal kullanarak tek bir veritabanı oluşturma](single-database-create-quickstart.md).
- Kaynak sınırları için bkz. [sunucusuz işlem katmanı kaynak sınırları](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
