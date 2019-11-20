---
title: Çok kiracılı bir uygulamada yeni kiracılar sağlama
description: Azure SQL veritabanı çok kiracılı SaaS uygulamasında yeni kiracılar sağlamayı ve kataloglarını öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 6ec8f8835e925663fc6ac21a6eb1df09d6927109
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132112"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Yeni kiracılar sağlamayı ve bunları kataloğa kaydetmeyi öğrenin

Bu öğreticide, SaaS düzenlerini sağlamayı ve kataloglayacağınızı öğreneceksiniz. Ayrıca, Wingtip bilet SaaS veritabanı kiracı başına uygulamasında nasıl uygulandığını de öğreneceksiniz. Yeni kiracı veritabanları oluşturup başlatır ve bunları uygulamanın kiracı kataloğuna kaydedersiniz. Katalog, SaaS uygulamasının birçok kiracılar ve verileri arasındaki eşlemeyi tutan bir veritabanıdır. Katalog, uygulama ve yönetim isteklerini doğru veritabanına yönlendiren önemli bir rol oynar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Tek bir yeni kiracı sağlayın.
> * Ek kiracılar için bir toplu iş sağlayın.


Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Her kiracı için Wingtip bilet SaaS veritabanı uygulaması dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz. [Wingtip bilet SaaS veritabanı-Kiracı uygulaması dağıtma ve araştırma](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell’in yüklendiğinden. Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya başlayın](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS Katalog düzenine giriş

Veritabanı tarafından desteklenen çok kiracılı bir SaaS uygulamasında, her kiracının bilgisinin nerede depolandığını öğrenmek önemlidir. SaaS Katalog modelinde, her kiracı ve verilerinin depolandığı veritabanı arasındaki eşlemeyi tutmak için bir Katalog veritabanı kullanılır. Bu model, kiracı verileri birden çok veritabanı arasında dağıtıldığında geçerlidir.

Her kiracı, katalogdaki bir anahtarla tanımlanır ve bu, veritabanlarının konumuyla eşlenir. Wingtip bilet uygulamasında, anahtar, kiracının adının bir karmasından oluşturulur. Bu düzen, uygulamanın, uygulama URL 'sinde bulunan kiracı adından anahtar oluşturmasına izin verir. Diğer kiracı anahtar şemaları kullanılabilir.

Katalog, veritabanının adının veya konumunun uygulama üzerinde en az etkiyle değiştirilmesini sağlar. Çok kiracılı bir veritabanı modelinde, bu özellik aynı zamanda bir kiracıyı veritabanları arasında taşımayı de karşılar. Katalog, bir kiracının veya veritabanının bakım veya diğer eylemler için çevrimdışı olup olmadığını göstermek için de kullanılabilir. Bu yetenek, [tek kiracılı geri yükleme öğreticisinde](saas-dbpertenant-restore-single-tenant.md)araştırılabilir.

Katalog Ayrıca kiracılar için sunulan şema sürümü, hizmet planı veya SLA 'Lar gibi ek kiracı veya veritabanı meta verilerini de saklayabilir. Katalog, uygulama yönetimi, müşteri desteği veya DevOps sağlayan diğer bilgileri de saklayabilir.

SaaS uygulamasının ötesinde, Katalog veritabanı araçlarını etkinleştirebilir. Wingtip bilet SaaS veritabanı-Kiracı örneği ' nde, katalog, geçici [Raporlama öğreticisinde](saas-tenancy-cross-tenant-reporting.md)araştırılan çapraz kiracı sorgusunu etkinleştirmek için kullanılır. Veritabanları arası iş yönetimi, [şema yönetimi](saas-tenancy-schema-management.md) ve [kiracı Analizi](saas-tenancy-tenant-analytics.md) öğreticilerinde araştırılabilir.

Wingtip bilet SaaS örneklerinde, katalog, [elastik veritabanı istemci kitaplığı 'nın (EDCL)](sql-database-elastic-database-client-library.md)parça yönetimi özellikleri kullanılarak uygulanır. EDCL, Java ve .NET Framework kullanılabilir. EDCL, bir uygulamanın veritabanı tarafından desteklenen bir parça eşlemesi oluşturmasına, yönetmesine ve kullanmasına olanak sağlar.

Parça Haritası, parçaların (veritabanlarının) bir listesini ve anahtarlar (kiracılar) ile parçalar arasındaki eşlemeyi içerir. EDCL işlevleri, parça eşlemesinde girdileri oluşturmak için kiracı sağlama sırasında kullanılır. Bunlar, uygulamalar tarafından doğru veritabanına bağlanmak için çalışma zamanında kullanılır. EDCL, Katalog veritabanı trafiğini en aza indirmek ve uygulamayı hızlandırmak için bağlantı bilgilerini önbelleğe alır.

> [!IMPORTANT]
> Eşleme verilerine Katalog veritabanından erişilebilir, ancak *düzenleyemezsiniz*. Yalnızca elastik veritabanı Istemci kitaplığı API 'Lerini kullanarak eşleme verilerini düzenleyin. Eşleme veri risklerini doğrudan işlemek kataloğu bozuyor ve desteklenmez.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>SaaS sağlama düzenine giriş

Tek kiracılı veritabanı modeli kullanan bir SaaS uygulamasına yeni bir kiracı eklediğinizde, yeni bir kiracı veritabanı sağlamalısınız. Veritabanının uygun konum ve hizmet katmanında oluşturulması gerekir. Ayrıca, uygun şema ve başvuru verileriyle birlikte başlatılmalıdır. Ve uygun kiracı anahtarı altındaki katalogda kayıtlı olmalıdır.

Veritabanı sağlamaya yönelik farklı yaklaşımlar kullanılabilir. SQL betiklerini yürütebilir, bacpac dağıtabilir veya bir şablon veritabanını kopyalayabilirsiniz.

Veritabanı sağlama, şema yönetimi stratejinizin bir parçası olmalıdır. Yeni veritabanlarının en son şemayla sağlandığından emin olmanız gerekir. Bu gereksinim, [şema yönetimi öğreticisinde](saas-tenancy-schema-management.md)araştırılabilir.

Her kiracı için Wingtip bilet veritabanı uygulaması, katalog sunucusuna dağıtılan _basetenantdb_adlı bir şablon veritabanını kopyalayarak yeni kiracılar sağlar. Sağlama, kayıt deneyiminin bir parçası olarak uygulamayla tümleştirilebilir. Ayrıca, betikler kullanılarak çevrimdışı de desteklenebilir. Bu öğretici, PowerShell kullanarak sağlamayı araştırır.

Sağlama betikleri, bir elastik havuzda yeni bir kiracı veritabanı oluşturmak için _basetenantdb_ veritabanını kopyalar. Kiracı veritabanı, _newtenant_ DNS diğer adına eşlenmiş kiracı sunucusunda oluşturulur. Bu diğer ad, yeni kiracılar sağlamak için kullanılan sunucuya yönelik bir başvuru tutar ve olağanüstü durum kurtarma öğreticilerinde bir kurtarma kiracı sunucusunu işaret etmek üzere güncelleştirilir ([coğrafi geri yükleme kullanarak](saas-dbpertenant-dr-geo-restore.md)Dr, [coğrafi çoğaltma kullanarak](saas-dbpertenant-dr-geo-replication.md)). Betikler daha sonra, kiracıya özel bilgilerle veritabanını başlatır ve Katalog parça haritasına kaydeder. Kiracı veritabanlarına, kiracı adına göre adlar verilir. Bu adlandırma düzeni, deseninin önemli bir parçası değildir. Katalog, kiracı anahtarını veritabanı adıyla eşleştirir, bu nedenle herhangi bir adlandırma kuralı kullanılabilir.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Her kiracı uygulama komut dosyası için Wingtip bilet SaaS veritabanı 'nı alın

Wingtip bilet SaaS betikleri ve uygulama kaynak kodu [Wingtipbilet ssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub deposunda mevcuttur. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Sağlama ve kataloğa kaydetme ile ilgili ayrıntılı kılavuz

Wingtip bilet uygulamasının yeni kiracı sağlaması uygulayıp uygulamadığını anlamak için bir kesme noktası ekleyin ve bir kiracı sağladığınızda iş akışını izleyin.

1. PowerShell ıSE 'de,...\\Learning modülleri\\ProvisionAndCatalog\\_demo-ProvisionAndCatalog. ps1_ ' yi açın ve aşağıdaki parametreleri ayarlayın:

   * **$TenantName** = yeni mekanın adı (örneğin, *Bushwillow Blues*).
   * **$VenueType** = önceden tanımlanmış mekan türlerinden biri: _maves, classicalmusic, dans, CAI, judo, motor yarış, çok amaçlı, Opera, rockmusic, futbol_.
   * **$DemoScenario** = **1**, *tek bir kiracı sağlayın*.

2. Kesme noktası eklemek için imlecinizi *Yeni-kiracı '* ı belirten satıra yerleştirin. Ardından F9 tuşuna basın.

   ![Ilı](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Betiği çalıştırmak için F5 tuşuna basın.

4. Komut dosyası yürütme kesme noktasında durduktan sonra, koda dönmek için F11 tuşuna basın.

   ![Hata ayıklama](media/saas-dbpertenant-provision-and-catalog/debug.png)



**Hata ayıklama** menü seçeneklerini kullanarak betiğin yürütülmesini izleyin. Çağrılan işlevlerin üzerinde veya içine gitmek için F10 ve F11 tuşlarına basın. PowerShell betiklerinde hata ayıklama hakkında daha fazla bilgi için bkz. [PowerShell betiklerinde çalışma ve hata ayıklama hakkında ipuçları](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Bu iş akışını açık bir şekilde izlemeniz gerekmez. Betikte hata ayıklama yapılacağını açıklar.

* **CatalogAndDatabaseManagement. psm1 modülünü içeri aktarın.** Parça [yönetimi](sql-database-elastic-scale-shard-map-management.md) işlevleri üzerinde bir katalog ve kiracı düzeyinde bir soyutlama sağlar. Bu modül, Katalog deseninin çoğunu kapsüller ve araştırmayı kapsar.
* **SubscriptionManagement. psm1 modülünü içeri aktarın.** Azure 'da oturum açma ve birlikte çalışmak istediğiniz Azure aboneliğini seçme işlevlerini içerir.
* **Yapılandırma ayrıntılarını alın.** F11 kullanarak Get-Configuration içine geçin ve uygulama yapılandırmasının nasıl belirtilme şeklini görün. Kaynak adlarına ve uygulamaya özgü diğer değerler burada tanımlanmıştır. Betikleri öğrenene kadar bu değerleri değiştirmeyin.
* **Katalog nesnesini alın.** Daha yüksek düzeyde betikte kullanılan bir katalog nesnesini oluşturan ve döndüren Get-Catalog ' a adımla. Bu işlev, **Azureshardmanagement. psm1**'dan içeri aktarılan parça yönetim işlevlerini kullanır. Katalog nesnesi aşağıdaki öğelerden oluşur:

   * $catalogServerFullyQualifiedName, standart gövde ve Kullanıcı adınız ile oluşturulur: _Katalog-\<user\>. Database. Windows .net_.
   * $catalogDatabaseName, *tenantcatalog* yapılandırmasından alınır.
   * $shardMapManager nesnesi, katalog veritabanından başlatılır.
   * $shardMap nesnesi, katalog veritabanındaki _tenantcatalog_ parça eşlemesinden başlatılır. Bir katalog nesnesi oluşur ve döndürülür. Bu, üst düzey betikte kullanılır.
* **Yeni kiracı anahtarını hesaplayın.** Kiracı adından kiracı anahtarı oluşturmak için bir karma işlevi kullanılır.
* **Kiracı anahtarının zaten var olup olmadığını denetleyin.** Anahtarın kullanılabilir olduğundan emin olmak için Katalog denetlenir.
* **Kiracı veritabanına New-TenantDatabase öğesi sağlanır.** Veritabanının bir [Azure Resource Manager şablonu](../azure-resource-manager/resource-manager-template-walkthrough.md)kullanarak nasıl sağlandığını gösteren F11 kullanın.

    Veritabanı adı, hangi parçanın hangi kiracıya ait olduğunu netleştirmek üzere kiracı adından oluşturulur. Diğer veritabanı adlandırma kurallarını da kullanabilirsiniz. Kaynak Yöneticisi şablonu, Katalog sunucusundaki bir şablon veritabanını (_Basetenantdb_) kopyalayarak bir kiracı veritabanı oluşturur. Alternatif olarak, bir veritabanı oluşturup bir bacpac içeri aktararak onu başlatabilirsiniz. Alternatif olarak bilinen bir konumdan başlatma betiği de çalıştırabilirsiniz.

    Kaynak Yöneticisi şablonu. ..\Learning Modules\Common\ klasöründe bulunur: *tenantdatabasecopytemplate. JSON*

* **Kiracı veritabanı daha fazla başlatılmış.** Mekan (kiracı) adı ve mekan türü eklenir. Ayrıca, burada başka başlatma da yapabilirsiniz.

* **Kiracı veritabanı kataloğa kaydedilir.** Bu, kiracı anahtarı kullanılarak *Add-TenantDatabaseToCatalog* ile kaydedilir. Ayrıntılara geçmek için F11 kullanın:

    * Katalog veritabanı, parça eşlemesine eklenir (bilinen veritabanları listesi).
    * Anahtar değerini parçaya bağlayan eşleme oluşturulur.
    * Kiracı hakkında ek meta veriler (mekan adı), katalogdaki kiracılar tablosuna eklenir. Kiracılar tablosu, parça yönetimi şemasının bir parçası değildir ve EDCL tarafından yüklenmez. Bu tabloda, Katalog veritabanının uygulamaya özgü ek verileri destekleyecek şekilde nasıl genişletilebileceği gösterilmektedir.


Sağlama tamamlandıktan sonra, yürütme özgün *demo-ProvisionAndCatalog* betiğine geri döner. Tarayıcıdaki yeni kiracı için **Olaylar** sayfası açılır.

   ![Olaylar sayfası](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Kiracılar toplu iş sağlama

Bu alıştırma, 17 kiracılar toplu işi sağlar. Diğer Wingtip bilet SaaS veritabanı-Kiracı öğreticilerine başlamadan önce Bu kiracı toplu iş örneğini sağlamanızı öneririz. Birlikte çalışmak için birkaç veritabanı daha vardır.

1. PowerShell ıSE 'de,...\\öğrenme modüllerini\\ProvisionAndCatalog\\*demo-ProvisionAndCatalog. ps1*' yi açın. *$DemoScenario* parametresini 3 olarak değiştirin:

   * **$DemoScenario** = **3**, *kiracı grubu sağlayın*.
2. Betiği çalıştırmak için F5 tuşuna basın.

Betik, ek kiracı grubu dağıtır. Toplu işi denetleyen [Azure Resource Manager şablonu](../azure-resource-manager/resource-manager-template-walkthrough.md) kullanır ve her bir veritabanının bağlı bir şablona sağlamasını destekler. Şablonların bu şekilde kullanılması, Azure Resource Manager’ın betiğinizin sağlama işlemine aracılık etmesine olanak tanır. Şablonlar, veritabanlarını paralel olarak sağlayın ve gerekirse yeniden denemeleri işler. Betik ıdempotent, bu nedenle herhangi bir nedenle başarısız olursa veya durdurulduğunda yeniden çalıştırın.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Başarıyla dağıtılan kiracılar toplu işlemini doğrulama

* [Azure Portal](https://portal.azure.com), sunucu listenize gidin ve *tenants1* sunucusunu açın. **SQL veritabanları**' nı seçin ve 17 ek veritabanı toplu işleminin şu anda listede olduğunu doğrulayın.

   ![Veritabanı listesi](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Diğer sağlama düzenleri

Bu öğreticide bulunmayan diğer sağlama desenleri:

**Ön sağlama veritabanları**: önceden sağlama, elastik havuzdaki veritabanlarının ek maliyet eklememesinden yararlanır. Faturalandırma, veritabanlarına değil, elastik havuza yöneliktir. Boştaki veritabanları hiçbir kaynak tüketmez. Bir havuzdaki veritabanlarının ön sağlamasını yaparak ve gerektiğinde ayırarak, kiracılar ekleme süresini azaltabilirsiniz. Önceden sağlanan veritabanlarının sayısı, bir arabelleğin beklenen sağlama oranına uygun tutulması için gerektiği şekilde ayarlanabilir.

**Otomatik sağlama**: otomatik sağlama modelinde, bir sağlama hizmeti gerektiğinde sunucuları, havuzları ve veritabanlarını otomatik olarak sağlar. İsterseniz, elastik havuzlarda önceden sağlama veritabanlarını dahil edebilirsiniz. Veritabanları kullanımdan çıkarıldı ve silinirse, elastik havuzlardaki boşluklar sağlama hizmeti tarafından doldurulabilir. Bu tür bir hizmet, birden çok coğrafi sistem genelinde sağlamayı işleme ve olağanüstü durum kurtarma için coğrafi çoğaltma ayarlama gibi basit veya karmaşık olabilir.

Otomatik sağlama düzeniyle, bir istemci uygulaması veya betiği, sağlama hizmeti tarafından işlenmek üzere bir sıraya sağlama isteği gönderir. Ardından, tamamlanma sağlamak için hizmeti yoklar. Ön sağlama kullanılıyorsa, istekler hızla işlenir. Hizmet, arka planda bir değiştirme veritabanı sağlar.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Tek bir yeni kiracı sağlayın.
> * Ek kiracılar için bir toplu iş sağlayın.
> * Kiracılar sağlama ve bunları kataloğa kaydetme ayrıntılarının ayrıntılarına geçin.

[Performans izleme öğreticisini](saas-dbpertenant-performance-monitoring.md)deneyin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Her kiracı Için Wingtip bilet SaaS veritabanı uygulaması üzerinde derleme yapan ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastik veritabanı istemci kitaplığı](sql-database-elastic-database-client-library.md)
* [Windows PowerShell ISE hata ayıklama betikleri](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
