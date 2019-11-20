---
title: Data Sync için en iyi deneyimler
description: Azure SQL Data Sync yapılandırmak ve çalıştırmak için en iyi yöntemler hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 75fe07dc9847ae32248688bc20fac01e74c7b26a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821866"
---
# <a name="best-practices-for-sql-data-sync"></a>SQL Data Sync için en iyi deneyimler 

Bu makalede, Azure SQL Data Sync için en iyi yöntemler açıklanmaktadır.

SQL Data Sync hizmetine genel bakış için bkz. [Azure SQL Data Sync ile birden fazla bulut ve şirket içi veritabanı arasında veri eşitleme](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync Şu anda Azure SQL veritabanı yönetilen **örneğini desteklemez.**

## <a name="security-and-reliability"></a>Güvenlik ve güvenilirlik

### <a name="client-agent"></a>İstemci Aracısı

-   İstemci aracısını, ağ hizmeti erişimi olan en az ayrıcalıklı kullanıcı hesabını kullanarak yükler.  
-   İstemci aracısını, şirket içi SQL Server bilgisayar olmayan bir bilgisayara yükler.  
-   Birden fazla aracıyla şirket içi bir veritabanını kaydetmeyin.    
    -   Farklı eşitleme grupları için farklı tabloları eşitleseniz bile bunu önleyin.  
    -   Birden çok istemci aracılarıyla şirket içi bir veritabanını kaydettirmek, eşitleme gruplarından birini sildiğinizde zorluk doğurur.

### <a name="database-accounts-with-least-required-privileges"></a>En az gerekli ayrıcalıklara sahip veritabanı hesapları

-   **Eşitleme kurulumu için**. Tablo Oluştur/Değiştir; Alter database; Yordam oluşturma; Şemayı Seç/Değiştir; Kullanıcı tanımlı tür oluşturun.

-   , **Devam eden eşitleme için**. Eşitleme için seçilen tablolarda ve eşitleme meta verileri ve izleme tablolarında seçme/ekleme/güncelleştirme/silme; Hizmet tarafından oluşturulan saklı yordamlarda yürütme izni; Kullanıcı tanımlı tablo türlerinde yürütme izni.

-   **Sağlaması için**. Eşitleme kapsamında tablolar üzerinde Değiştir; Eşitleme meta verileri tablolarında seçin/silin; Eşitleme izleme tablolarında, saklı yordamlarda ve Kullanıcı tanımlı türlerde denetim.

Azure SQL veritabanı yalnızca tek bir kimlik bilgileri kümesini destekler. Bu kısıtlama dahilinde bu görevleri gerçekleştirmek için aşağıdaki seçenekleri göz önünde bulundurun:

-   Farklı aşamalar için kimlik bilgilerini değiştirin (örneğin, *credentials1* for Setup ve *credentials2* for devam için).  
-   Kimlik bilgilerinin iznini değiştirin (yani, eşitleme ayarlandıktan sonra izni değiştirin).

## <a name="setup"></a>Kurulum

### <a name="database-considerations-and-constraints"></a>Veritabanı konuları ve kısıtlamaları

#### <a name="sql-database-instance-size"></a>SQL veritabanı örnek boyutu

Yeni bir SQL veritabanı örneği oluşturduğunuzda, en büyük boyutu, dağıttığınız veritabanından her zaman daha büyük olacak şekilde ayarlayın. En büyük boyutu dağıtılan veritabanından daha büyük olarak ayarlamazsanız eşitleme başarısız olur. SQL Data Sync otomatik büyüme sunmasa da, oluşturulduktan sonra veritabanının boyutunu artırmak için `ALTER DATABASE` komutunu çalıştırabilirsiniz. SQL veritabanı örneği boyut sınırları içinde kaltığınızdan emin olun.

> [!IMPORTANT]
> SQL Data Sync her veritabanıyla ek meta verileri depolar. Gerekli alanı hesaplarken bu meta veriler için hesap oluşturduğunuzdan emin olun. Eklenen ek yükün miktarı, tabloların genişliğiyle ilgilidir (örneğin, dar tablolar daha fazla ek yük gerektirir) ve trafik miktarı.

### <a name="table-considerations-and-constraints"></a>Tablo konuları ve kısıtlamaları

#### <a name="selecting-tables"></a>Tabloları seçme

Eşitleme grubundaki bir veritabanında bulunan tüm tabloları eklemeniz gerekmez. Bir eşitleme grubuna dahil ettiğiniz tablolar verimlilik ve maliyetleri etkiler. Tabloları ve bağımlı oldukları tabloları, yalnızca iş ihtiyacı olduğunda bir eşitleme grubunda ekleyin.

#### <a name="primary-keys"></a>Birincil anahtarlar

Bir eşitleme grubundaki her tablo bir birincil anahtara sahip olmalıdır. SQL Data Sync hizmeti, birincil anahtarı olmayan bir tabloyu eşitleyemiyor.

Üretimde SQL Data Sync kullanmadan önce, ilk ve devam eden eşitleme performansını test edin.

#### <a name="empty-tables-provide-the-best-performance"></a>Boş tablolar en iyi performansı sağlar

Boş tablolar, başlatma zamanında en iyi performansı sağlar. Hedef tablo boş ise, veri eşitleme verileri yüklemek için toplu ekleme kullanır. Aksi halde, veri eşitleme, çakışmaları denetlemek için satır içi bir karşılaştırma ve ekleme yapar. Ancak performans bir sorun değilse, zaten veri içeren tablolar arasında eşitleme ayarlayabilirsiniz.

### <a name="provisioning-destination-databases"></a>Hedef veritabanlarını sağlama

SQL Data Sync, temel veritabanı oto sağlaması sağlar.

Bu bölümde SQL Data Sync ' de sağlama kısıtlamaları anlatılmaktadır.

#### <a name="autoprovisioning-limitations"></a>Oto sağlama sınırlamaları

SQL Data Sync, oto sağlama için aşağıdaki sınırlamalara sahiptir:

-   Yalnızca hedef tabloda oluşturulan sütunları seçin. Eşitleme grubunun parçası olmayan tüm sütunlar hedef tablolarda sağlanmaz.
-   Dizinler yalnızca seçili sütunlar için oluşturulur. Kaynak tablo dizininde eşitleme grubunun parçası olmayan sütunlar varsa, bu dizinler hedef tablolarda sağlanmaz.  
-   XML türü sütunlarındaki dizinler sağlanmadı.  
-   DENETIM kısıtlamaları sağlanmadı.  
-   Kaynak tablolardaki mevcut Tetikleyiciler sağlanmadı.  
-   Görünümler ve saklı yordamlar hedef veritabanında oluşturulmaz.
-   GÜNCELLEŞTIRME basamakla ve yabancı anahtar kısıtlamalarındaki BASAMAKLı SILME eylemleri hedef tablolarda yeniden oluşturulmaz.
-   En fazla 28 ' den büyük olan ondalık veya sayısal sütunlarınız varsa SQL Data Sync eşitleme sırasında bir dönüştürme taşması sorunuyla karşılaşabilir. Ondalık veya sayısal sütunların duyarlılığını 28 veya daha az olacak şekilde kısıtlamanızı öneririz.

#### <a name="recommendations"></a>Öneriler

-   Yalnızca hizmeti denediğiniz sırada SQL Data Sync oto sağlama özelliğini kullanın.  
-   Üretim için veritabanı şemasını sağlayın.

### <a name="locate-hub"></a>Merkez veritabanının yerini nerede bulabileceğiniz

#### <a name="enterprise-to-cloud-scenario"></a>Kuruluşa buluta senaryo

Gecikme süresini en aza indirmek için, hub veritabanını eşitleme grubunun veritabanı trafiğinin en büyük yoğunluğu ' na yakın tutun.

#### <a name="cloud-to-cloud-scenario"></a>Buluttan buluta senaryo

-   Bir eşitleme grubundaki tüm veritabanları tek bir veri merkezinde olduğunda, hub aynı veri merkezinde bulunmalıdır. Bu yapılandırma, veri merkezleri arasındaki gecikme süresini ve veri aktarımı maliyetini azaltır.
-   Bir eşitleme grubundaki veritabanları birden çok veri merkezinde olduğunda, hub, veritabanlarının ve veritabanı trafiğinin çoğunluğu ile aynı veri merkezinde bulunmalıdır.

#### <a name="mixed-scenarios"></a>Karışık senaryolar

Önceki yönergeleri, Kurumsal-bulut ve buluttan buluta senaryolar gibi karmaşık eşitleme grubu yapılandırmalarına uygulayın.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a>Yavaş ve maliyetli ilk eşitlemeden kaçının

Bu bölümde, bir eşitleme grubunun ilk eşitlemesini tartıştık. İlk eşitlemenin daha uzun sürmesi ve gerekenden daha pahalı olmasını önlemeye nasıl yardımcı olabileceğinizi öğrenin.

#### <a name="how-initial-sync-works"></a>İlk eşitlemenin nasıl çalıştığı

Bir eşitleme grubu oluşturduğunuzda, verileri yalnızca bir veritabanında başlatın. Birden çok veritabanında verileriniz varsa SQL Data Sync her satırı çözülmesi gereken bir çakışma olarak değerlendirir. Bu çakışma çözümü, ilk eşitlemenin yavaş geçmesine neden olur. Birden çok veritabanında verileriniz varsa, ilk eşitleme, veritabanı boyutuna bağlı olarak birkaç gün ve birkaç ay sürebilir.

Veritabanları farklı veri merkezlerimizde, her satır farklı veri merkezleri arasında gezinmelidir. Bu, ilk eşitlemenin maliyetini artırır.

#### <a name="recommendation"></a>Öneri

Mümkünse, eşitleme grubunun veritabanlarından yalnızca birinde bulunan verilerle başlayın.

### <a name="design-to-avoid-synchronization-loops"></a>Eşitleme döngülerinin önüne geçmek için tasarım

Bir eşitleme grubu içinde döngüsel başvurular olduğunda bir eşitleme döngüsü oluşur. Bu senaryoda, bir veritabanındaki her değişiklik son olarak zaman içinde eşitlenir ve eşitleme grubundaki veritabanları aracılığıyla döngüsel olarak çoğaltılır.   

Performans düşüşüne neden olduğundan ve maliyetleri önemli ölçüde artırabileceğinden, eşitleme döngülerinden kaçıntığınızdan emin olun.

### <a name="handling-changes-that-fail-to-propagate"></a>Yayılmaya başarısız olan değişiklikler

#### <a name="reasons-that-changes-fail-to-propagate"></a>Değişikliklerin yayılmasının nedeni

Değişiklikler aşağıdaki nedenlerden biri için yayılamıyor olabilir:

-   Şema/veri türü uyumsuzluğu.
-   Null değer atanamaz sütunlara null ekleniyor.
-   Yabancı anahtar kısıtlamalarını ihlal ediyor.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Değişiklikler yayamadığında ne olur?

-   Eşitleme grubu, bir **Uyarı** durumunda olduğunu gösterir.
-   Ayrıntılar Portal Kullanıcı arabirimi günlük görüntüleyicisinde listelenmiştir.
-   Sorun 45 gün boyunca çözümlenmiyorsa, veritabanının tarihi eski olur.

> [!NOTE]
> Bu değişiklikler hiçbir şekilde yayılmaz. Bu senaryoya geri almanın tek yolu, eşitleme grubunu yeniden oluşturmaktır.

#### <a name="recommendation"></a>Öneri

Portal ve günlük arabirimi aracılığıyla eşitleme grubunu ve veritabanı sistem durumunu düzenli olarak izleyin.


## <a name="maintenance"></a>Bakım

### <a name="avoid-out-of-date-databases-and-sync-groups"></a>Güncel olmayan veritabanlarının ve eşitleme gruplarının olmaması

Bir eşitleme grubunun veya bir eşitleme grubundaki veritabanının tarihi eski olabilir. Eşitleme grubunun durumu **güncel**olmadığında, çalışmayı sonlandırır. Veritabanının durumu **güncel**olmadığında veriler kaybolabilir. Bu senaryodan kurtarmaya çalışmak yerine bu senaryonun önüne geçmek en iyisidir.

#### <a name="avoid-out-of-date-databases"></a>Güncel olmayan veritabanlarından kaçının

Bir veritabanının durumu, 45 gün veya daha uzun bir süre çevrimdışı olduğunda **güncel** değil olarak ayarlanır. Bir veritabanında **güncel** olmayan bir durumu önlemek için, veritabanlarının hiçbirinin 45 gün veya daha uzun bir süre çevrimdışı olduğundan emin olun.

#### <a name="avoid-out-of-date-sync-groups"></a>Güncel olmayan eşitleme gruplarından kaçının

Eşitleme grubundaki herhangi bir değişiklik, 45 gün veya daha uzun bir süre için eşitleme grubunun geri kalanına yayamazsa, eşitleme grubunun durumu **güncel** değil olarak ayarlanır. Bir eşitleme grubunda **güncel** olmayan bir durumu önlemek için, eşitleme grubunun geçmiş günlüğünü düzenli olarak kontrol edin. Tüm çakışmaların çözümlendiğinden ve değişikliklerin eşitleme grubu veritabanları genelinde başarıyla yayıldığından emin olun.

Bir eşitleme grubu, şu nedenlerden biri için değişiklik uygulayamayabilir:

-   Tablolar arasındaki şema uyumsuzluğu.
-   Tablolar arasında veri uyumsuzluğu.
-   Null değerlere izin veren bir sütunda null değeri olan bir satır ekleniyor.
-   Bir satır, yabancı anahtar kısıtlamasını ihlal eden bir değerle güncelleştiriliyor.

Güncel olmayan eşitleme gruplarını engellemek için:

-   Şemayı, başarısız satırlarda içerilen değerlere izin verecek şekilde güncelleştirin.
-   Yabancı anahtar değerlerini, başarısız satırlarda yer alan değerleri içerecek şekilde güncelleştirin.
-   Başarısız olan satırdaki veri değerlerini, hedef veritabanındaki şema veya yabancı anahtarlarla uyumlu olacak şekilde güncelleştirin.

### <a name="avoid-deprovisioning-issues"></a>Sağlamayı kaldırma sorunlarından kaçının

Bazı durumlarda, bir istemci aracısıyla bir veritabanının kaydını silmek eşitlemenin başarısız olmasına neden olabilir.

#### <a name="scenario"></a>Senaryo

1. A adlı eşitleme grubu, bir SQL veritabanı örneği ve yerel aracı 1 ile ilişkili bir şirket içi SQL Server veritabanı kullanılarak oluşturulmuştur.
2. Aynı şirket içi veritabanı yerel aracı 2 ile kaydedilir (Bu aracı herhangi bir eşitleme grubuyla ilişkili değildir).
3. Şirket içi veritabanının kaydını yerel aracı 2 ' den silmek, şirket içi veritabanı için bir eşitleme grubu için izleme ve meta tabloları kaldırır.
4. Eşitleme grubu A işlemleri başarısız oldu, bu hata: "veritabanı eşitleme için sağlanmadığından veya eşitleme yapılandırma tabloları için izinleriniz olmadığından geçerli işlem tamamlanamadı."

#### <a name="solution"></a>Çözüm

Bu senaryoyu önlemek için, birden fazla aracıyla bir veritabanını kaydetmeyin.

Bu senaryodan kurtarmak için:

1. Veritabanını ait olduğu her bir eşitleme grubundan kaldırın.  
2. Veritabanını kaldırdığınız her bir eşitleme grubuna geri ekleyin.  
3. Etkilenen her eşitleme grubunu dağıtın (Bu eylem veritabanını sağlar).  

### <a name="modifying-your-sync-group"></a>Eşitleme grubunu değiştirme

Bir veritabanını bir eşitleme grubundan kaldırmaya çalışmayın ve sonra, önce değişikliklerden birini dağıttıktan sonra eşitleme grubunu düzenleyin.

Bunun yerine, önce bir veritabanını bir eşitleme grubundan kaldırın. Ardından, değişikliği dağıtın ve sağlamayı kaldırma işleminin bitmesini bekleyin. Sağlamayı kaldırma tamamlandığında, eşitleme grubunu düzenleyebilir ve değişiklikleri dağıtabilirsiniz.

Bir veritabanını kaldırmaya ve sonra değişikliklerden birini dağıtmaya gerek kalmadan bir eşitleme grubunu düzenlemenize çalışırsanız, bir veya başka bir işlem başarısız olur. Portal arabirimi tutarsız hale gelebilir. Bu durumda, doğru durumu geri yüklemek için sayfayı yenileyin.

## <a name="next-steps"></a>Sonraki adımlar
SQL Data Sync hakkında daha fazla bilgi için bkz.:

-   Genel Bakış- [Azure SQL Data Sync ile birden çok bulut ve şirket içi veritabanı arasında veri eşitleme](sql-database-sync-data.md)
-   Veri eşitlemesini ayarlama
    - Portalda- [öğreticide, Azure SQL veritabanı ve şirket içi SQL Server arasında veri eşitlemek için SQL Data Sync ayarlama](sql-database-get-started-sql-data-sync.md)
    - PowerShell ile
        -  [PowerShell kullanarak birden çok Azure SQL veritabanı arasında eşitleme](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell kullanarak bir Azure SQL Veritabanı ile SQL Server şirket içi veritabanı arasında eşitleme](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Veri eşitleme Aracısı- [Azure SQL Data Sync Için veri eşitleme Aracısı](sql-database-data-sync-agent.md)
-   İzleyici- [Azure izleyici günlükleri ile izleyici SQL Data Sync](sql-database-sync-monitor-oms.md)
-   Sorun giderme- [Azure SQL Data Sync sorunlarını giderme](sql-database-troubleshoot-data-sync.md)
-   Eşitleme şemasını güncelleştirme
    -   Transact-SQL- [Azure SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatikleştirin](sql-database-update-sync-schema.md)
    -   PowerShell ile- [varolan bir eşitleme grubundaki eşitleme şemasını güncelleştirmek Için PowerShell kullanın](scripts/sql-database-sync-update-schema.md)

SQL veritabanı hakkında daha fazla bilgi için bkz.

-   [SQL veritabanına genel bakış](sql-database-technical-overview.md)
-   [Veritabanı yaşam döngüsü yönetimi](https://msdn.microsoft.com/library/jj907294.aspx)
