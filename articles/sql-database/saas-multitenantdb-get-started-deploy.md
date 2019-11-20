---
title: Parçalı bir çok kiracılı veritabanı SaaS uygulaması dağıtma
description: Azure SQL veritabanı 'nı kullanarak SaaS düzenlerini gösteren parçalı Wingtip bilet SaaS Multi-tenant veritabanı uygulamasını dağıtın ve araştırın.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
ms.date: 10/16/2018
ms.openlocfilehash: 3277318e01362df8fc21ff7ca769aaeb8006abc6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828003"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Parçalı bir çok kiracılı uygulamayı dağıtma ve araştırma

Bu öğreticide, Wingtip biletleri adlı örnek bir çok kiracılı SaaS uygulamasını dağıtıp araştırmış olursunuz. Wingtip bilet uygulaması, SaaS senaryolarının uygulanmasını kolaylaştıran Azure SQL veritabanı özelliklerini göstermek için tasarlanmıştır.

Wingtip bilet uygulamasının bu uygulaması, parçalı bir çok kiracılı veritabanı modelini kullanır. Parçalama, kiracı tanımlayıcısına göre yapılır. Kiracı verileri, kiracı tanımlayıcı değerlerine göre belirli bir veritabanına dağıtılır. 

Bu veritabanı, bir veya daha fazla kiracıyı her parça veya veritabanına depolamanıza olanak tanır. Her bir veritabanının birden çok kiracı tarafından paylaşılması için en düşük maliyetten en uygun şekilde iyileştirebilirsiniz. Ya da her bir veritabanı deposunu yalnızca bir kiracı ile ayırarak yalıtımda iyileştirede iyileştirebilirsiniz. İyileştirme seçiminiz, her bir kiracı için bağımsız olarak yapılabilir. Kiracı ilk kez depolandığında seçiminiz yapılabilir veya daha sonra fikrinizi değiştirebilirsiniz. Uygulama, her iki şekilde de çalışacak şekilde tasarlanmıştır.

## <a name="app-deploys-quickly"></a>Uygulama hızla dağıtılır

Uygulama, Azure bulutu 'nda çalışır ve Azure SQL veritabanı 'nı kullanır. Aşağıdaki dağıtım bölümünde, **Azure 'a mavi dağıtım** düğmesi sunulmaktadır. Düğmeye basıldığında, uygulama Azure aboneliğinize beş dakika içinde tam olarak dağıtılır. Bireysel uygulama bileşenleriyle çalışmak için tam erişiminiz vardır.

Uygulama, üç örnek kiracı için verilerle dağıtılır. Kiracılar bir çok kiracılı veritabanında birlikte depolanır.

Herhangi bir kişi, C# [GitHub deposundan][link-github-wingtip-multitenantdb-55g]Wingtip bilet için ve PowerShell kaynak kodunu indirebilir.

## <a name="learn-in-this-tutorial"></a>Bu öğreticide öğrenin

> [!div class="checklist"]
> - Wingtip bilet SaaS uygulamasını dağıtma.
> - Uygulama kaynak kodunun ve yönetim betiklerinin nereden alınacağı.
> - Uygulamayı oluşturan sunucular ve veritabanları hakkında.
> - Kiracılar, *katalogla*verileri ile eşlenir.
> - Yeni bir kiracı sağlama.
> - Uygulamadaki kiracı etkinliğini izleme.

Bu ilk dağıtımı üzerine inşa eden bir dizi ilgili öğretici vardır. Öğreticiler bir dizi SaaS tasarımı ve yönetim desenini keşfedebilir. Öğreticilerde çalışırken, farklı SaaS desenlerinin nasıl uygulandığını görmek için, belirtilen betiklerin içinde ilerletirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

- En son Azure PowerShell yüklendi. Ayrıntılar için bkz. [Azure PowerShell][link-azure-get-started-powershell-41q]kullanmaya başlama.

## <a name="deploy-the-wingtip-tickets-app"></a>Wingtip bilet uygulamasını dağıtma

### <a name="plan-the-names"></a>Adları planlayın

Bu bölümün adımlarında, kaynak adlarının genel olarak benzersiz olmasını sağlamak için kullanılan bir *Kullanıcı* değeri ve uygulamanın bir dağıtımı tarafından oluşturulan tüm kaynakları içeren *kaynak grubu* için bir ad sağlarsınız. *Ann Finley*adlı bir kişi için şunları öneririz:
- *Kullanıcı:* **AF1**  *(baş harfleri ve bir basamak. Uygulamayı ikinci kez dağıtırsanız farklı bir değer (ör. AF2) kullanın.)*
- *Kaynak grubu:* **Wingtip-MT-AF1** *(Wingtip-MT bunun parçalı çok kiracılı uygulama olduğunu gösterir. AF1 Kullanıcı adını eklemek, kaynak grubu adını içerdiği kaynakların adlarıyla ilişkilendirir.)*

Adlarınızı şimdi seçin ve bunları aşağı yazın. 

### <a name="steps"></a>Adımlar

1. Aşağıdaki mavi **Azure 'A dağıt** düğmesine tıklayın.
   - Bu, Azure portal Wingtip bilet SaaS dağıtım şablonu ile açar.

     [![Azure 'a dağıtma düğmesi.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Dağıtım için gerekli parametre değerlerini girin.

    > [!IMPORTANT]
    > Bu gösterim için, önceden mevcut kaynak grupları, sunucular veya havuzlar kullanmayın. Bunun yerine, **Yeni kaynak grubu oluştur**' u seçin. İlgili faturalandırmayı durdurmak için uygulamayla işiniz bittiğinde bu kaynak grubunu silin.
    > Üretim için bu uygulamayı veya oluşturduğu kaynakları kullanmayın. Kimlik doğrulamanın bazı yönleri ve sunucu güvenlik duvarı ayarları, tanıtımı kolaylaştırmak için uygulamada bilinçli olarak güvenli değildir.

    - **Kaynak grubu** için, **Yeni oluştur**' u seçin ve kaynak grubu için bir **ad** belirtin (büyük/küçük harfe duyarlı).
        - Açılan listeden bir **konum** seçin.
    - **Kullanıcı** için, kısa bir **Kullanıcı** değeri seçmenizi öneririz.

1. **Uygulamayı dağıtın**.

    - Hüküm ve koşulları kabul etmek için tıklayın.
    - **Satın al**’a tıklayın.

1. Arama kutusunun sağındaki zil simgesi olan **Bildirimler**' e tıklayarak dağıtım durumunu izleyin. Wingtip App 'in dağıtımı yaklaşık beş dakika sürer.

   ![dağıtım başarılı](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Yönetim betiklerini indirme ve engellemesini kaldırma

Uygulama dağıtıldığında, uygulama kaynak kodunu ve yönetim betikleri ' ni indirin.

> [!NOTE]
> ZIP dosyaları bir dış kaynaktan indirilip ayıklandığında, yürütülebilir içerikler (betikler, dll 'Ler) Windows tarafından engellenebilir. Bir zip dosyasından betikleri ayıkladığınızda, Ayıklamadan önce. zip dosyasının engelini kaldırmak için aşağıdaki adımları kullanın. . Zip dosyasının engellemesini kaldırarak betiklerin çalışmasına izin verildiğinden emin olursunuz.

1. [Wingtipbilet ssaas-MultiTenantDb GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)deposuna gidin.
2. **Kopyala veya indir**' e tıklayın.
3. **ZIP 'ı indir** ' e tıklayın ve dosyayı kaydedin.
4. **WingtipTicketsSaaS-MultiTenantDb-Master. zip** dosyasına sağ tıklayın ve **Özellikler**' i seçin.
5. **Genel** sekmesinde **Engellemeyi kaldır**' ı seçin ve **Uygula**' ya tıklayın.
6. **Tamam** düğmesine tıklayın.
7. Dosyaları ayıklayın.

Betikler *..\\Wingtipbilet ssaas-MultiTenantDb-master\\Learning Modules\\* klasöründe bulunur.

## <a name="update-the-configuration-file-for-this-deployment"></a>Bu dağıtım için yapılandırma dosyasını güncelleştir

Herhangi bir komut dosyasını çalıştırmadan önce, **userconfig. psm1**içindeki *kaynak grubunu* ve *Kullanıcı* değerlerini ayarlayın. Bu değişkenleri, dağıtım sırasında ayarladığınız aynı değerlere ayarlayın.

1. *PowerShell ISE*'de\\...\\öğrenme modüllerini *userconfig. psm1* olarak açın.
2. *Resourcegroupname* ve *ad* 'yi dağıtımınızın belirli değerleriyle güncelleştirin (yalnızca 10 ve 11. satır).
3. Değişiklikleri kaydedin.

Bu dosyada ayarlanan değerler tüm betikler tarafından kullanılır, bu nedenle doğru olmaları önemlidir. Uygulamayı yeniden dağıtırsanız, Kullanıcı ve kaynak grubu için farklı değerler seçmeniz gerekir. Ardından, UserConfig. psm1 dosyasını yeni değerlerle yeniden güncelleştirin.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Wingtip App 'te kiracılar, kiracılar ' dir. Bir mekan konser salonu, spor kulübü ya da olayları barındıran başka bir konum olabilir. Havalandırma noktaları, müşteriler olarak Wingtip 'e, her bir mekan için de bir kiracı tanımlayıcısı oluşturulur. Her bir mekan yaklaşan olaylarını Wingtip olarak listeler, bu nedenle ortak, olaylara bilet satın alabilir.

Her bir mekan, olaylarını listelemek ve bilet satmak için kişiselleştirilmiş bir Web uygulaması alır. Her Web uygulaması, diğer kiracılardan bağımsızdır ve yalıtılmıştır. Azure SQL veritabanı 'nda dahili olarak her kiracının verileri, varsayılan olarak parçalı bir çok kiracılı veritabanında depolanır. Tüm veriler kiracı tanımlayıcısıyla etiketlenir.

Merkezi bir **Olay Hub 'ı** Web sayfası, belirli dağıtımınızdaki kiracılar için bağlantıların bir listesini sağlar. **Olay Hub 'ı** Web sayfası ve tek bir Web uygulaması için aşağıdaki adımları kullanın:

1. Web tarayıcınızda **Olay Hub 'ını** açın:
   - http://events.wingtip-mt.&lt; User&gt;. trafficmanager.net &nbsp; *(&lt;kullanıcı&gt;, dağıtımınızın Kullanıcı değeri Ile değiştirin.)*

     ![olay hub’ı](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. **Olay Hub’ında** **Fabrikam Caz Kulübü**’ne tıklayın.

   ![Olaylar](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Gelen isteklerin dağıtımını denetlemek için, Wingtip uygulaması [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)kullanır. Her kiracının Olaylar sayfasında, URL 'sindeki kiracı adı yer alır. Her URL ayrıca belirli kullanıcı değerini içerir. Her URL, aşağıdaki adımları kullanarak gösterilen biçime uyar:

- http://events.wingtip-mt.&lt; User&gt;. trafficmanager.net/*fabrikamjazzclub*

1. Olaylar uygulaması, kiracı adını URL 'den ayrıştırır. Kiracı adı, önceki örnek URL 'de *fabrikamjazzclub* .
2. Uygulama daha sonra, parça [eşleme yönetimi](sql-database-elastic-scale-shard-map-management.md)kullanarak bir kataloğa erişmek üzere bir anahtar oluşturmak için kiracı adını karma hale getirir.
3. Uygulama, katalogda anahtarı bulur ve kiracının veritabanının ilgili konumunu alır.
4. Uygulama, kiracı için tüm verileri içeren bir veritabanını bulmak ve veritabanına erişmek için konum bilgisini kullanır.

### <a name="events-hub"></a>Olay Hub 'ı

1. **Olaylar hub** 'ı, katalogda kayıtlı olan tüm kiracılar ve bunların havalandırma durumlarını listeler.
2. **Olay Hub 'ı** , URL 'leri oluşturmak için her eşlemeyle ilişkili kiracının adını almak üzere katalogda genişletilmiş meta verileri kullanır.

Bir üretim ortamında, genellikle [Şirket İnternet etki alanını](../traffic-manager/traffic-manager-point-internet-domain.md) Traffic Manager profiline işaret etmek IÇIN BIR CNAME DNS kaydı oluşturursunuz.

## <a name="start-generating-load-on-the-tenant-databases"></a>Kiracı veritabanları üzerinde yük oluşturmaya başlama

Artık uygulama dağıtıldığına göre, şimdi çalışacak şekilde koyalım! *Demo-LoadGenerator* PowerShell betiği, her kiracı için çalışan bir iş yükünü başlatır. Birçok SaaS uygulamasında gerçek dünyada yük, genellikle sporda ve öngörülemeyen bir şekilde yapılır. Bu tür yükün benzetimini yapmak için, Oluşturucu tüm kiracılara dağıtılmış bir yük oluşturur. Yük, rastgele aralıklarda oluşan her bir kiracıda rastgele artışlarıyla içerir. Yük deseninin ortaya geçmesi birkaç dakika sürer. bu nedenle, yüklemeyi izlemeden önce oluşturucunun en az üç veya dört dakika çalışmasını sağlamak en iyisidir.

1. *PowerShell ISE*'de,...\\öğrenme modüllerini\\yardımcı programları\\*demo-LoadGenerator. ps1* betiği ' ni açın.
2. Betiği çalıştırmak ve yük oluşturucuyu başlatmak için **F5**’e basın (şimdilik varsayılan parametre değerlerini bırakın).

*Demo-LoadGenerator. ps1* betiği, yük oluşturucunun çalıştığı başka bir PowerShell oturumu açar. Yük Oluşturucu bu oturumda, her kiracı için bir tane olmak üzere arka plan yük oluşturma işlerini çağıran bir ön plan görevi olarak çalışır.

Ön plan görevi başladıktan sonra, iş çağırma durumunda kalır. Görev, daha sonra sağlanan yeni kiracılar için ek arka plan işleri başlatır.

PowerShell oturumunun kapatılması tüm işleri durduruyor.

Farklı parametre değerlerini kullanmak için Load Generator oturumunu yeniden başlatmak isteyebilirsiniz. Varsa, PowerShell oluşturma oturumunu kapatın ve *demo-LoadGenerator. ps1*öğesini yeniden çalıştırın.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Parçalı veritabanına yeni bir kiracı sağlayın

İlk dağıtım, *Tenants1* veritabanında üç örnek kiracı içerir. Daha sonra başka bir kiracı oluşturalım ve bu uygulamayı dağıtılan uygulama üzerinde gözlemleyeceksiniz. Bu adımda, yeni bir kiracı oluşturmak için bir tuşa basın:

1. *PowerShell ISE*'de *Demo-ProvisionTenants. ps1*\\hazırlama ve Katalog\\...\\öğrenme modüllerini açın.
2. Betiği çalıştırmak için **F5** ( **F8**değil) tuşuna basın (şimdilik varsayılan değerleri bırakın).

   > [!NOTE]
   > Komut dosyasının seçili bir bölümünü çalıştırmak için **F8** tuşuna basarak, PowerShell betiklerini yalnızca **F5** tuşuna basarak çalıştırmanız gerekir. **F8** ile ilgili sorun *$PSScriptRoot* değişkeninin değerlendirilmemiştir. Bu değişken klasörlerde gezinmek, diğer betikleri çağırmak veya modülleri içeri aktarmak için birçok komut dosyası gereklidir.

Yeni Red çaağaç yarış kiracısı *Tenants1* veritabanına eklenir ve kataloğa kaydedilir. Yeni kiracının bilet satış **olayları** sitesi tarayıcınızda açılır:

![Yeni kiracı](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

**Olay Hub 'ını**yenileyin ve yeni kiracı listede görüntülenir.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Kendi veritabanında yeni bir kiracı sağlama

Parçalı çok kiracılı model, diğer kiracıların bulunduğu bir veritabanına veya kendi veritabanına yeni bir kiracı sağlamayı seçmenize olanak sağlar. Kendi veritabanında yalıtılmış bir kiracı aşağıdaki avantajları sağlar:

- Kiracının veritabanı performansı, diğer kiracıların gereksinimleriyle karşı uzlaşmaya gerek olmadan yönetilebilir.
- Gerekirse, başka bir kiracı etkilenmediğinden veritabanı daha önceki bir zaman noktasına geri yüklenebilir.

Ücretsiz deneme müşterileri veya ekonomisi müşterilerini çok kiracılı veritabanlarına yerleştirebilirsiniz. Her Premium kiracıyı kendi adanmış veritabanına yerleştirebilirsiniz. Yalnızca bir kiracı içeren çok sayıda veritabanı oluşturursanız, kaynak maliyetlerini iyileştirmek için bunları her bir elastik havuzda toplu olarak yönetebilirsiniz.

Daha sonra, bu kez kendi veritabanında başka bir kiracı sağladık:

1. .......\\öğrenme modülleri\\ *. ps1*\\sağlama ve *Katalog,* **salx salsa**'ya *$TenantName* , **dans** ve $VenueType ile **2** *' ye değiştirme* .

2. Betiği yeniden çalıştırmak için **F5** tuşuna basın.
    - Bu **F5** tuþuna, yeni kiracıyı ayrı bir veritabanında sağlar. Veritabanı ve kiracı kataloğa kaydedilir. Ardından tarayıcı, kiracının Olaylar sayfasında açılır.

   ![SALX salsa olayları sayfası](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Sayfanın en altına gidin. Başlıkta, kiracı verilerinin depolandığı veritabanı adını görürsünüz.

3. **Olay Hub 'ını** yenileyin ve iki yeni kiracı artık listede görüntülenir.

## <a name="explore-the-servers-and-tenant-databases"></a>Sunucuları ve kiracı veritabanlarını keşfet

Artık dağıtılan bazı kaynaklara göz atacağız:

1. [Azure Portal](https://portal.azure.com), kaynak grupları listesine gidin. Uygulamayı dağıtırken oluşturduğunuz kaynak grubunu açın.

   ![kaynak grubu](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. **Katalog-mt&lt;kullanıcı&gt;** sunucusu ' na tıklayın. Katalog sunucusu, *tenantcatalog* ve *basetenantdb*adlı iki veritabanı içerir. *Basetenantdb* veritabanı boş bir şablon veritabanıdır. Birçok kiracı veya yalnızca bir kiracı için kullanılan yeni bir kiracı veritabanı oluşturmak üzere kopyalanır.

   ![katalog sunucusu seçeneğine tıklayın](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Kaynak grubuna geri dönün ve kiracı veritabanlarını tutan *tenants1-MT* sunucusunu seçin.
    - Tenants1 veritabanı, özgün üç kiracının ve eklediğiniz ilk kiracının depolandığı çok kiracılı bir veritabanıdır. 50 DTU standart veritabanı olarak yapılandırılır.
    - **Salixsalsa** veritabanı, SALX salsa dans yetkisini tek kiracı olarak tutar. Varsayılan olarak 50 DTU ile standart bir sürüm veritabanı olarak yapılandırılmıştır.

   ![Kiracılar sunucusu](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Veritabanının performansını izleme

Yük Oluşturucu birkaç dakika boyunca çalışıyorsa, Azure portal yerleşik olarak bulunan veritabanı izleme özelliklerine bakmak için yeterli telemetri vardır.

1. **Tenants1-mt&lt;kullanıcı&gt;** sunucusuna gidin ve üzerinde dört kiracı olan veritabanının kaynak kullanımını görüntülemek için **tenants1** ' ye tıklayın. Her kiracı, yük oluşturucusdaki tek bir sporda ağır yüke tabidir:

   ![izleme tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   DTU kullanım grafiğinde çok kiracılı bir veritabanının birçok kiracı genelinde öngörülemeyen bir iş yükünü nasıl destekleyebileceği gösterilmektedir. Bu durumda, yük Oluşturucu her kiracıya kabaca 30 DTU bir çok sayıda yük uyguluyor. Bu yük, 50 DTU veritabanının %60 ' e karşılık gelir. Aynı anda birden fazla kiracıya uygulanan yükün sonucu %60 ' ü aşan en sivri noktaları.

2. **Tenants1-mt&lt;kullanıcı&gt;** sunucusuna gidin ve **salixsalsa** veritabanına tıklayın. Bu veritabanında yalnızca bir kiracı içeren kaynak kullanımını görebilirsiniz.

   ![salixsalsa veritabanı](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Yük Oluşturucu, her kiracının hangi veritabanına sahip olduğu bağımsız olarak her kiracıya benzer bir yük uyguluyor. **Salixsalsa** veritabanında yalnızca bir kiracı ile, veritabanının birkaç kiracısıyla veritabanına göre çok daha yüksek bir yük üzerinde bir yüke sahip olduğunu görebilirsiniz. 

### <a name="resource-allocations-vary-by-workload"></a>Kaynak ayırmaları iş yüküne göre farklılık gösterir

Bazen çok kiracılı bir veritabanı, tek kiracılı bir veritabanı değil, her zaman daha iyi performans için daha fazla kaynak gerektirir. Kaynakların en iyi şekilde ayrılması, sisteminizdeki kiracılar için belirli iş yükü özelliklerine bağlıdır.

Yük Oluşturucu betiği tarafından oluşturulan iş yükleri yalnızca çizim amaçlıdır.

## <a name="additional-resources"></a>Ek kaynaklar

- Çok kiracılı SaaS uygulamaları hakkında bilgi edinmek için bkz. [çok kiracılı SaaS uygulamaları Için tasarım desenleri](saas-tenancy-app-design-patterns.md).

- Elastik havuzlar hakkında bilgi edinmek için bkz.:

  - [Elastik havuzlar birden çok Azure SQL veritabanını yönetmenize ve ölçeklendirmenize yardımcı olur](sql-database-elastic-pool.md)
  - [Azure SQL Veritabanı ile ölçek genişletme](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrendiniz:

> [!div class="checklist"]
> - Wingtip bilet SaaS çok kiracılı veritabanı uygulamasını dağıtma.
> - Sunucular ve uygulamayı oluşturan veritabanları hakkında.
> - Kiracılar, *katalogla*verileri ile eşleştirilir.
> - Yeni kiracılar sağlama, çok kiracılı bir veritabanı ve tek kiracılı veritabanı.
> - Kiracı etkinliğini izlemek için havuz kullanımını görüntüleme.
> - İlgili faturalandırmayı durdurmak için örnek kaynakları silme.

Şimdi [sağlama ve Katalog öğreticisini](sql-database-saas-tutorial-provision-and-catalog.md)deneyin.


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Azure 'a dağıtma düğmesi."

