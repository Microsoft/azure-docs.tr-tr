---
title: Çok kiracılı SaaS öğreticisi
description: Tek başına uygulama modelini kullanarak yeni kiracılar sağlama ve kataloglayın
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/24/2018
ms.openlocfilehash: 02682a18f14e7ecbf5b42783ab84a1b55a4bb77b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133137"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Kiracı SaaS düzenine göre uygulama kullanarak yeni kiracılar sağlayın ve kataloglayın

Bu makalede, kiracı SaaS düzenine göre tek başına uygulama kullanılarak yeni kiracılar için sağlama ve kataloglandırma ele alınmaktadır.
Bu makalede iki ana bölüm bulunur:
* Yeni kiracılar sağlama ve kataloglandırma hakkında kavramsal tartışma
* Sağlamayı ve kataloglamaları gerçekleştiren örnek PowerShell kodunu vurgulayan bir öğretici
    * Öğretici, her kiracı için tek başına uygulama düzenine uyarlanan Wingtip bilet örnek SaaS uygulamasını kullanır.

## <a name="standalone-application-per-tenant-pattern"></a>Her kiracı için tek başına uygulama

Her kiracı deseni için tek başına uygulama, çok kiracılı SaaS uygulamalarına yönelik birkaç desenden biridir.  Bu düzende, her kiracı için tek başına bir uygulama sağlanır. Uygulama, uygulama düzeyi bileşenleri ve bir SQL veritabanı içerir.  Her kiracı uygulaması satıcının aboneliğine dağıtılabilir.  Alternatif olarak, Azure, bir uygulamanın kiracının aboneliğine dağıtılacağı ve kiracının adına satıcı tarafından yönetilen bir [yönetilen uygulamalar programı](https://docs.microsoft.com/azure/managed-applications/overview) sunmaktadır.

   ![Uygulama-kiracı başına desenler](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Bir kiracı için uygulama dağıttığınızda, uygulama ve veritabanı, kiracı için oluşturulan yeni bir kaynak grubunda sağlanır.  Ayrı kaynak gruplarının kullanılması, her kiracının uygulama kaynaklarını yalıtır ve bunların bağımsız olarak yönetilmesine olanak tanır. Her bir kaynak grubunda, her bir uygulama örneği kendisine karşılık gelen veritabanına doğrudan erişecek şekilde yapılandırılır.  Bu bağlantı modeli, uygulama ve veritabanı arasındaki bağlantıları aracısına yönelik bir katalog kullanan diğer desenlerle karşıttır.  Kaynak paylaşımı olmadığından, her kiracı veritabanına en yüksek yükü işlemek için yeterli kaynaklarla sağlanması gerekir. Bu model, kiracı yalıtımına yönelik güçlü bir vurgulama ve kaynak maliyetlerinde daha az vurgulamak gereken daha az kiracı içeren SaaS uygulamaları için kullanılmaya eğilimindedir.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Kiracı kataloğunu, kiracı başına uygulama başına bir model kullanma

Her kiracının uygulaması ve veritabanı tamamen yalıtılarak, kiracılar arasında çeşitli yönetim ve analiz senaryoları çalışabilir.  Örneğin, uygulamanın yeni bir sürümü için bir şema değişikliği uygulamak her kiracı veritabanının şemasında değişiklik yapılmasını gerektirir. Raporlama ve analiz senaryoları, dağıtıldığı yere bakılmaksızın tüm kiracı veritabanlarına erişim de gerektirebilir.

   ![Uygulama-kiracı başına desenler](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Kiracı kataloğu, kiracı tanımlayıcısı ile kiracı veritabanı arasında bir eşleme tutarken bir tanımlayıcının sunucu ve veritabanı adına çözümlenmesine izin verir.  Wingtip SaaS uygulamasında, kiracı tanıtıcısı, diğer şemalar kullanılabilse de kiracı adının karması olarak hesaplanır.  Tek başına uygulamaların bağlantıları yönetmesi için kataloğa ihtiyacı olmasa da, Katalog diğer eylemlerin bir kiracı veritabanı kümesine kapsamını atamak için kullanılabilir. Örneğin, elastik sorgu, çapraz kiracı raporlama için sorguların dağıtıldığı veritabanları kümesini tespit etmek üzere kataloğunu kullanabilir.

## <a name="elastic-database-client-library"></a>Elastik Veritabanı İstemci Kitaplığı

Wingtip Sample uygulamasında Katalog, [elastik veritabanı Istemci kitaplığı](sql-database-elastic-database-client-library.md) 'nın (edcl) parçalı yönetim özellikleri tarafından uygulanır.  Kitaplık, bir uygulamanın bir veritabanında depolanan parça haritasını oluşturmasını, yönetmesini ve kullanmasını sağlar. Wingtip bilet örneğinde, Katalog *kiracı kataloğu* veritabanında depolanır.  Parça, kiracı anahtarının, kiracının verilerinin depolandığı parça (veritabanı) ile eşlenir.  EDCL işlevleri, *kiracı kataloğu* veritabanındaki tablolarda depolanan küresel bir parça *haritasını* ve her parça içinde depolanan bir *yerel parça haritasını* yönetir.

EDCL işlevleri, parça eşlemesindeki girdileri oluşturmak ve yönetmek için uygulamalardan veya PowerShell betiklerinden çağrılabilir. Diğer EDCL işlevleri, parça kümesini almak veya verilen kiracı anahtarı için doğru veritabanına bağlanmak için kullanılabilir.

> [!IMPORTANT]
> Katalog veritabanındaki verileri veya kiracı veritabanlarındaki yerel parça haritasını doğrudan düzenleyemezsiniz. Yüksek veri bozulması riski nedeniyle doğrudan güncelleştirmeler desteklenmez. Bunun yerine, yalnızca EDCL API 'Leri kullanarak eşleme verilerini düzenleyin.

## <a name="tenant-provisioning"></a>Kiracı sağlama

Her kiracı, kaynakların içinde sağlanmasından önce oluşturulması gereken yeni bir Azure Kaynak grubu gerektirir. Kaynak grubu mevcut olduğunda, uygulama bileşenlerini ve veritabanını dağıtmak ve ardından veritabanı bağlantısını yapılandırmak için bir Azure Kaynak Yönetimi şablonu kullanılabilir. Veritabanı şemasını başlatmak için şablon bacpac dosyasını içeri aktarabilir.  Alternatif olarak, veritabanı ' şablon ' veritabanının bir kopyası olarak oluşturulabilir.  Veritabanı daha sonra ilk mekme verileriyle daha güncellenir ve kataloğa kaydedilir.

## <a name="tutorial"></a>Öğretici

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

* Katalog sağlama
* Daha önce katalogda dağıttığınız örnek kiracı veritabanlarını kaydedin
* Ek kiracı sağlama ve kataloğa kaydetme

Uygulamayı dağıtmak ve yapılandırmak için bir Azure Resource Manager şablonu kullanılır, kiracı veritabanını oluşturun ve ardından bir bacpac dosyasını içeri aktararak başlatın. İçeri aktarma isteği, eylem yapılmadan önce birkaç dakika sıraya alınmış olabilir.

Bu öğreticinin sonunda, her bir veritabanı katalogda kayıtlı olan bir tek başına kiracı uygulamaları kümesine sahip olursunuz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Üç örnek kiracı uygulaması dağıtılır. Bu uygulamaları beş dakikadan kısa bir süre içinde dağıtmak için bkz. [Wingtip biletleri SaaS tek başına uygulama modelini dağıtma ve araştırma](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Kataloğu sağlama

Bu görevde, tüm kiracı veritabanlarını kaydetmek için kullanılan kataloğu sağlamayı öğreneceksiniz. Yapacaklarınız:

* Azure Kaynak Yönetimi şablonu kullanarak **Katalog veritabanını sağlayın** . Veritabanı bacpac dosyası içeri aktarılmasıyla başlatılır.
* Daha önce dağıttığınız **örnek kiracı uygulamalarını kaydettirin** .  Her kiracı, kiracı adının karmasından oluşturulmuş bir anahtar kullanılarak kaydedilir.  Kiracı adı, katalogdaki bir uzantı tablosunda da depolanır.

1. PowerShell ıSE 'de *. ..\Learning Modules\userconfig.exe* dosyasını açın ve **\<User\>** değerini üç örnek uygulamayı dağıtmada kullandığınız değere güncelleştirin.  **Dosyayı kaydedin**.
1. PowerShell ıSE 'de *. ..\Learning Modules\provisiontenants\demo-provisionandcatalog.exe dosyasını* açın ve **$Scenario = 1**olarak ayarlayın. Kiracı kataloğunu dağıtın ve önceden tanımlanmış kiracılar 'ı kaydedin.

1. İmlecinizi, `& $PSScriptRoot\New-Catalog.ps1`belirten satıra yerleştirerek bir kesme noktası ekleyin ve ardından **F9**tuşuna basın.

    ![izleme için kesme noktası ayarlama](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. **F5**tuşuna basarak betiği çalıştırın.
1.  Komut dosyası yürütme kesme noktasında durduktan sonra, New-Catalog. ps1 betiğine dönmek için **F11** tuşuna basın.
1.  Komut dosyasının yürütülmesini, hata ayıklama menü seçenekleri, F10 ve F11 kullanarak, çağrılan işlevleri üzerinde veya içine adımla izleyin.
    *   PowerShell betiklerinde hata ayıklama hakkında daha fazla bilgi için bkz. [PowerShell betiklerinde çalışma ve hata ayıklama hakkında ipuçları](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Betik tamamlandıktan sonra, katalog var olur ve tüm örnek kiracılar kaydedilir.

Şimdi oluşturduğunuz kaynaklara bakın.

1. [Azure Portal](https://portal.azure.com/) açın ve kaynak gruplarına gözatamazsınız.  **Wingtip-sa-Catalog-\<kullanıcı\>** kaynak grubunu açın ve katalog sunucusunu ve veritabanını göz önünde edin.
1. Portalda veritabanını açın ve sol taraftaki menüden *Veri Gezgini* ' ni seçin.  Oturum aç komutuna tıklayın ve ardından Password = **P\@ssword1**girin.


1. *Tenantcatalog* veritabanının şemasını gezin.
   * `__ShardManagement` şemadaki nesneler, elastik veritabanı Istemci kitaplığı tarafından sağlanır.
   * `Tenants` tablosu ve `TenantsExtended` görünümü, ek değer sağlamak üzere kataloğun nasıl uzatılacağını gösteren, örneğe eklenen uzantılardır.
1. `SELECT * FROM dbo.TenantsExtended`sorguyu çalıştırın.

   ![Veri Gezgini](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Veri Gezgini kullanmaya alternatif olarak, SQL Server Management Studio veritabanına bağlanabilirsiniz. Bunu yapmak için, Wingtip-sunucusuna bağlanın-


    Verileri doğrudan katalogda düzenlememelisiniz-parça yönetimi API 'Lerini her zaman kullanın.

## <a name="provision-a-new-tenant-application"></a>Yeni bir kiracı uygulaması sağlama

Bu görevde, tek bir kiracı uygulamasını sağlamayı öğreneceksiniz. Yapacaklarınız:

* Kiracı için **Yeni bir kaynak grubu oluşturun** .
* Azure Kaynak Yönetimi şablonu kullanarak **uygulamayı ve veritabanını** yeni kaynak grubuna sağlayın.  Bu eylem, bir bacpac dosyasını içeri aktararak ortak şema ve başvuru verileriyle veritabanını başlatmayı içerir.
* **Veritabanını temel kiracı bilgileriyle başlatın**. Bu eylem, olay Web sitesinde arka plan olarak kullanılan fotoğrafı belirleyen mekan türünü belirtmeyi içerir.
* **Veritabanını Katalog veritabanına kaydedin**.

1. PowerShell ıSE 'de *. ..\Learning Modules\provisiontenants\demo-provisionandcatalog.exe* ve set **$Scenario = 2**' yi açın. Kiracı kataloğunu dağıtma ve önceden tanımlanmış kiracılar kaydetme

1. İmlecinizi, `& $PSScriptRoot\New-TenantApp.ps1`ve ardından **F9**tuşuna basarak satır 49 ' de istediğiniz yere yerleştirerek betiğe bir kesme noktası ekleyin.
1. **F5**tuşuna basarak betiği çalıştırın.
1.  Komut dosyası yürütme kesme noktasında durduktan sonra, New-Catalog. ps1 betiğine dönmek için **F11** tuşuna basın.
1.  Komut dosyasının yürütülmesini, hata ayıklama menü seçenekleri, F10 ve F11 kullanarak, çağrılan işlevleri üzerinde veya içine adımla izleyin.

Kiracı sağlandıktan sonra, yeni kiracının olaylar Web sitesi açılır.

   ![Kırmızı çaağaç yarış](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Daha sonra Azure portal oluşturulan yeni kaynakları inceleyebilirsiniz.

   ![Kırmızı çaağaç yarış kaynakları](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Faturalandırmayı durdurmak için kaynak gruplarını silin

Örneği araştırmayı tamamladıktan sonra, ilişkili faturalandırmayı durdurmak için oluşturduğunuz tüm kaynak gruplarını silin.

## <a name="additional-resources"></a>Ek kaynaklar

- Çok kiracılı SaaS veritabanı uygulamaları hakkında daha fazla bilgi edinmek için bkz. [çok kiracılı SaaS uygulamaları Için tasarım desenleri](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrendiniz:

> [!div class="checklist"]
> * Wingtip bilet SaaS tek başına uygulamasını dağıtma.
> * Uygulamayı oluşturan sunucular ve veritabanları hakkında.
> * İlgili faturalandırmayı durdurmak için örnek kaynakları silme.

Kataloğun, [Wingtip bilet SaaS uygulamasının](saas-dbpertenant-wingtip-app-overview.md)kiracı başına veritabanı sürümünü kullanarak çeşitli çapraz kiracı senaryolarını desteklemek için nasıl kullanıldığını keşfedebilirsiniz.
