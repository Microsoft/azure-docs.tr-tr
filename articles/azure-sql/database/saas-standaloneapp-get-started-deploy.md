---
title: Tek kiracılı SaaS öğreticisi
description: Azure SQL veritabanı 'nı kullanan tek bir tek kiracılı SaaS uygulamasını dağıtın ve keşfedebilirsiniz.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793288"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Azure SQL veritabanı 'nı kullanan tek başına tek kiracılı bir uygulama dağıtma ve araştırma
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu öğreticide, tek başına uygulama veya kiracı başına uygulama, model kullanılarak geliştirilen Wingtip bilet SaaS örnek uygulamasını dağıtıp araştırın.  Uygulama, çok kiracılı SaaS senaryolarını etkinleştirmeyi kolaylaştıran Azure SQL veritabanı özelliklerini göstermek için tasarlanmıştır.

Tek başına uygulama veya kiracı başına uygulama, her kiracı için bir uygulama örneği dağıtır.  Her uygulama belirli bir kiracı için yapılandırılır ve ayrı bir Azure Kaynak grubunda dağıtılır. Birden çok kiracılı bir çözüm sağlamak için uygulamanın birden fazla örneğini temin edilir. Bu model, kiracı yalıtımının en üst önceliğe sahip olduğu kiracıların daha küçük numaralarına en uygun seçenektir. Azure, kaynakların kiracının aboneliğine dağıtılmasını ve kiracının adına bir hizmet sağlayıcı tarafından yönetilmesini sağlayan iş ortağı programları vardır. 

Bu öğreticide, Azure aboneliğinize üç kiracı için üç bağımsız uygulama dağıtırsınız.  Tek tek uygulama bileşenleriyle keşfetmeye ve bunlarla çalışmaya yönelik tam erişime sahip olursunuz.

Uygulama kaynak kodu ve yönetim betikleri [Wingtipbilet ssaas-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub deposunda bulunur. Uygulama Visual Studio 2015 kullanılarak oluşturulmuştur ve güncelleştirme yapılmadan Visual Studio 2019 ' de başarıyla açılamadı ve derlenemiyor.


Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> * Wingtip bilet SaaS tek başına uygulamasını dağıtma.
> * Uygulama kaynak kodunun ve yönetim betiklerinin nereden alınacağı.
> * Uygulamayı oluşturan sunucular ve veritabanları hakkında.

Ek öğreticiler yayımlanacak. Bu uygulama düzenine göre bir dizi yönetim senaryosunu araştırmanıza olanak sağlar.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wingtip bilet SaaS tek başına uygulamasını dağıtma

Uygulamayı belirtilen üç kiracı için dağıtın:

1. [Azure Portal](https://portal.azure.com)Dağıtım şablonunu açmak için her mavi **Azure 'a dağıt** düğmesine tıklayın. Her şablon iki parametre değeri gerektirir; Yeni bir kaynak grubu için bir ad ve bu dağıtımı uygulamanın diğer dağıtımlarından ayırt eden Kullanıcı adı. Sonraki adımda, bu değerleri ayarlamaya ilişkin ayrıntılar sağlanmaktadır.

   **Contoso Concert salonu**   
   [!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood dojo**   
   [!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Fabrikam Cakulüsinek**   
   [!["Azure 'a dağıt" etiketli bir düğmeyi gösteren resim.](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Her dağıtım için gerekli parametre değerlerini girin.

    > [!IMPORTANT]
    > Bazı kimlik doğrulama ve sunucu güvenlik duvarları, tanıtım amacıyla güvenli şekilde güvenli değildir. Her uygulama dağıtımı için **Yeni bir kaynak grubu oluşturun** .  Mevcut bir kaynak grubunu kullanmayın. Üretim için bu uygulamayı veya oluşturduğu kaynakları kullanmayın. İlgili faturalandırmayı durdurmak için uygulamalarla işiniz bittiğinde tüm kaynak gruplarını silin.

    Kaynak adlarınızdan yalnızca küçük harf, sayı ve kısa çizgi kullanmak en iyisidir.
    * **Kaynak grubu** Için yeni oluştur ' u seçin ve ardından kaynak grubu için küçük harfli bir ad sağlayın. **Wingtip-sa- \<venueName\> - \<user\>** Önerilen modeldir.  İçin \<venueName\> , mekan adını boşluk yok ile değiştirin. İçin \<user\> , Kullanıcı değerini aşağıdan değiştirin.  Bu düzende, kaynak grubu adları *Wingtip-sa-contosoconcerthall-AF1*, *Wingtip-sa-dogwooddojo-AF1*, *Wingtip-sa-fabrikamjazzclub-AF1* olabilir.
    * Açılan listeden bir **konum** seçin.

    * **Kullanıcı** için-adınızın baş ve bir basamak gibi kısa bir kullanıcı değeri önerilir: Örneğin, *AF1*.


3. **Uygulamayı dağıtın**.

    * Hüküm ve koşulları kabul etmek için tıklayın.
    * **Satın al**’a tıklayın.

4. **Bildirimler** ' e tıklayarak üç dağıtımın durumunu izleyin (arama kutusunun sağındaki zil simgesi). Uygulamaları dağıtmak yaklaşık beş dakika sürer.


## <a name="run-the-applications"></a>Uygulamaları çalıştırma

Uygulama Vitrini olayları barındıran olaylar.  Havalandırma noktaları, uygulamanın kiracılarıdır. Her bir mekan, olaylarını listelemek ve bilet satmak için kişiselleştirilmiş bir Web sitesi alır. Mekan türleri konser Halls, cakulüler ve spor sinek 'leri içerir. Örnekte, mekan türü, mekanın Web sitesinde gösterilen arka plan fotoğrafı belirler.   Tek başına uygulama modelinde her bir mek'un tek başına Azure SQL veritabanı ile ayrı bir uygulama örneği vardır.

1. Her üç kiracının olay sayfasını ayrı tarayıcı sekmelerinde açın:

   - http://events.contosoconcerthall.&lt; user &gt; . trafficmanager.net
   - http://events.dogwooddojo.&lt; user &gt; . trafficmanager.net
   - http://events.fabrikamjazzclub.&lt; user &gt; . trafficmanager.net

     (Her URL 'de, &lt; kullanıcıyı &gt; dağıtımınızın Kullanıcı değeriyle değiştirin.)

   ![Ekinlikler](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Uygulama, gelen isteklerin dağıtımını denetlemek için [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md)kullanır. Kiracıya özgü her uygulama örneği, URL 'deki etki alanı adının bir parçası olarak kiracı adını içerir. Tüm kiracı URL 'Leri, belirli **Kullanıcı** değerini içerir. URL 'Ler aşağıdaki biçimi izler:
- http://events.&lt; venuename &gt; . &lt; user &gt; . trafficmanager.net

Her kiracının veritabanı **konumu** , ilgili dağıtılan uygulamanın uygulama ayarlarına dahildir.

Bir üretim ortamında, genellikle [*Şirket İnternet etki alanını*](../../traffic-manager/traffic-manager-point-internet-domain.md) Traffic Manager profilinin URL 'sine işaret etmek IÇIN BIR CNAME DNS kaydı oluşturursunuz.


## <a name="explore-the-servers-and-tenant-databases"></a>Sunucuları ve kiracı veritabanlarını keşfet

Dağıtılan kaynaklara göz atalım:

1. [Azure Portal](https://portal.azure.com), kaynak grupları listesine gidin.
2. Üç kiracı kaynak grubunu görmeniz gerekir.
3. Fabrikam Cap Kulükünün dağıtımına yönelik kaynakları içeren **Wingtip-sa-fabrikam- &lt; user &gt;** kaynak grubunu açın.  **Fabrikamjazzclub- &lt; &gt; User** sunucusu **fabrikamjazzclub** veritabanını içerir.

Her kiracı veritabanı 50 DTU *tek başına* veritabanıdır.

## <a name="additional-resources"></a>Ek kaynaklar

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Çok kiracılı SaaS uygulamaları hakkında bilgi edinmek için bkz. [çok kiracılı SaaS uygulamaları Için tasarım desenleri](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Faturalandırmayı durdurmak için kaynak gruplarını silme ##

Örneği kullanmayı bitirdiğinizde, ilişkili faturalandırmayı durdurmak için oluşturduğunuz tüm kaynak gruplarını silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrendiniz:

> [!div class="checklist"]
> * Wingtip bilet SaaS tek başına uygulamasını dağıtma.
> * Uygulamayı oluşturan sunucular ve veritabanları hakkında.
> * İlgili faturalandırmayı durdurmak için örnek kaynakları silme.

Daha sonra, şema yönetimi ve kiracı analizi gibi çeşitli çapraz kiracı senaryolarına izin veren kiracılar kataloğunun kullanımını keşfedebileceğiniz [sağlama ve Katalog](saas-standaloneapp-provision-and-catalog.md) öğreticisini deneyin.
