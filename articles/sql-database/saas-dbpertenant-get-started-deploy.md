---
title: Veritabanı başına kiracı SaaS öğreticisi
description: Azure SQL veritabanı 'nı kullanarak kiracı başına veritabanı desenini ve diğer SaaS desenlerini gösteren Wingtip biletleri SaaS çok kiracılı uygulamasını dağıtın ve araştırın.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 31f712f80ee2492e4bbaec99bd093d46f9d04e6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824005"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>SQL veritabanı ile kiracı başına veritabanı modelini kullanan çok kiracılı SaaS uygulamasını dağıtma ve araştırma

Bu öğreticide, Wingtip bilet SaaS veritabanı kiracı başına uygulaması (Wingtip) dağıtıp araştırdığınızda. Uygulama, birden çok kiracının verilerini depolamak için kiracı başına veritabanı modelini kullanır. Uygulama, Azure SQL veritabanı 'nın SaaS senaryolarını etkinleştirmeyi kolaylaştıran özelliklerini göstermek üzere tasarlanmıştır.

**Azure 'A dağıt**seçeneğini belirledikten beş dakika sonra, çok kiracılı bir SaaS uygulamanız vardır. Uygulama, bulutta çalışan bir SQL veritabanı içerir. Uygulama, her biri kendi veritabanına sahip olan üç örnek kiracıyla dağıtılır. Tüm veritabanları bir SQL elastik havuzuna dağıtılır. Uygulama, Azure aboneliğinize dağıtılır. Uygulamanın tek tek bileşenleriyle keşfetmeye ve bunlarla çalışmaya yönelik tam erişime sahip olursunuz. Uygulama C# kaynak kodu ve yönetim betikleri [Wingtipbilet ssaas-Dbpertenant GitHub][github-wingtip-dpt]deposunda mevcuttur.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> - Wingtip SaaS uygulamasını dağıtma.
> - Uygulama kaynak kodu ve yönetim betikleri nereden alınır?
> - Uygulamayı oluşturan sunucular, havuzlar ve veritabanları hakkında.
> - Kiracılar, *katalogla*verileri ile eşlenir.
> - Yeni bir kiracı sağlama.
> - Uygulamadaki kiracı etkinliğini izleme.

Çeşitli SaaS tasarım ve yönetim düzenlerini araştırmak için bir [dizi ilgili öğretici](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) sunulmaktadır. Öğreticiler, bu ilk dağıtımın ötesinde derleme içerir. Öğreticileri kullandığınızda, farklı SaaS desenlerinin nasıl uygulandığını görmek için, belirtilen betikleri inceleyebilirsiniz. Betikler, SQL Database özelliklerinin SaaS uygulamalarının geliştirilmesini nasıl basitleştireceğinizi gösterir.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlayabilmeniz için Azure PowerShell yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya başlayın](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wingtip bilet SaaS uygulamasını dağıtma

### <a name="plan-the-names"></a>Adları planlayın

Bu bölümün adımlarında, kaynak adlarının genel olarak benzersiz olduğundan emin olmak için kullanılan bir kullanıcı değeri sağlarsınız. Ayrıca, uygulamanın bir dağıtımı tarafından oluşturulan tüm kaynakları içeren kaynak grubu için bir ad sağlarsınız. Ann Finley adlı kurgusal bir kişi için şunları öneririz:

- **User**: *AF1* , Ann 'ın baş harflerinden ve bir basamağdan oluşur. Uygulamayı ikinci kez dağıtırsanız, farklı bir değer kullanın. Örnek olarak AF2.
- **Kaynak grubu**: *Wingtip-DPT-AF1* , bu, kiracı başına veritabanı uygulaması olduğunu gösterir. Kaynak grubu adını içerdiği kaynakların adlarıyla ilişkilendirmek için AF1 Kullanıcı adını ekleyin.

Adlarınızı şimdi seçin ve bunları aşağı yazın.

### <a name="steps"></a>Adımlar

1. Azure portal, Wingtip bilet SaaS veritabanının kiracı başına Dağıtım şablonunu açmak için, **Azure 'A dağıt**' ı seçin.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Gerekli parametreler için şablondaki değerleri girin.

    > [!IMPORTANT]
    > Bazı kimlik doğrulama ve sunucu güvenlik duvarları, tanıtım amacıyla güvenli şekilde güvenli değildir. Yeni bir kaynak grubu oluşturmanızı öneririz. Mevcut kaynak gruplarını, sunucuları veya havuzları kullanmayın. Bu uygulamayı, betikleri veya üretim için dağıtılan kaynakları kullanmayın. İlgili faturalandırmayı durdurmak için uygulamayla işiniz bittiğinde bu kaynak grubunu silin.

    - **Kaynak grubu**: **Yeni oluştur**' u seçin ve kaynak grubu için daha önce seçtiğiniz benzersiz adı belirtin.
    - **Konum**: açılan listeden bir konum seçin.
    - **Kullanıcı**: daha önce seçtiğiniz Kullanıcı adı değerini kullanın.

1. Uygulamayı dağıtın.

    a. Hüküm ve koşulları kabul etmek için seçin.

    b. **Satın al**'ı seçin.

1. Dağıtım durumunu izlemek için **Bildirimler** ' i (arama kutusunun sağındaki zil simgesi) seçin. Wingtip bilet SaaS uygulamasının dağıtımı yaklaşık beş dakika sürer.

   ![Dağıtım başarılı oldu](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Wingtip bilet yönetim betiklerini indirme ve engellemesini kaldırma

Uygulama dağıtıldıktan sonra, kaynak kodu ve yönetim betikleri ' ni indirin.

> [!IMPORTANT]
> . Zip dosyaları bir dış kaynaktan indirilip ayıklandığında, yürütülebilir dosyalar (betikler ve DLL 'Ler) Windows tarafından engelleniyor olabilir. Betikleri Ayıklamadan önce. zip dosyasının engelini kaldırma adımlarını izleyin. Engellemeyi kaldırma, betiklerin çalışmasına izin verilmesini sağlar.

1. [Wingtipbilet ssaas-DbPerTenant GitHub][github-wingtip-dpt]deposuna göz atabilirsiniz.
1. **Clone or download**'u (Kopyala veya indir) seçin.
1. **ZIP 'ı indir**' i seçin ve dosyayı kaydedin.
1. **WingtipTicketsSaaS-DbPerTenant-Master. zip** dosyasına sağ tıklayın ve ardından **Özellikler**' i seçin.
1. **Genel** sekmesinde **Engellemeyi kaldır** > **Uygula**' yı seçin.
1. **Tamam**' ı seçin ve dosyaları ayıklayın

Betikler...\\Wingtipbilet ssaas-DbPerTenant-Master\\öğrenme modülleri klasöründe bulunur.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Bu dağıtım için Kullanıcı yapılandırma dosyasını güncelleştir

Herhangi bir komut dosyasını çalıştırmadan önce, Kullanıcı yapılandırma dosyasındaki kaynak grubunu ve Kullanıcı değerlerini güncelleştirin. Bu değişkenleri, dağıtım sırasında kullandığınız değerlere ayarlayın.

1. PowerShell ıSE 'de,...\\öğrenme modüllerini açın\\**userconfig. psm1**
1. **Resourcegroupname** ve **ad** 'yi dağıtımınızın belirli değerleriyle güncelleştirin (yalnızca 10 ve 11. satır).
1. Değişiklikleri kaydedin.

Bu değerlere neredeyse her betikte başvurulur.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulama Vitrini olayları barındıran olaylar. Mekan türleri konser Halls, cakulüler ve spor sinek 'leri içerir. Wingtip biletlerinde, venler kiracılar olarak kaydedilir. Bir kiracıya, olayları listelemek ve müşterilerine bilet satmak için kolay bir yol sunar. Her bir mek, olaylarını listelemek ve bilet satmaya yönelik kişiselleştirilmiş bir Web sitesi alır.

Uygulamada dahili olarak, her kiracı bir SQL elastik havuzuna dağıtılan bir SQL veritabanı alır.

Merkezi bir **Olay Hub 'ı** sayfası, dağıtımınızdaki kiracılar için bağlantıların bir listesini sağlar.

1. Web tarayıcınızda Olay Hub 'ını açmak için URL 'YI kullanın: http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net. &lt;Kullanıcı&gt; dağıtımınızın Kullanıcı değeriyle değiştirin.

    ![Olay Hub 'ı](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Olaylar hub 'ında **fabrikam Cakkulübü** seçin.

    ![Olaylar](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Wingtip Application, gelen isteklerin dağıtımını denetlemek için [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) kullanır. Belirli bir kiracının Olaylar sayfasına erişim URL 'SI aşağıdaki biçimi kullanır:

- http://events.wingtip-dpt.&lt; Kullanıcı&gt;. trafficmanager.net/fabrikamjazzclub

    Önceki biçimin parçaları aşağıdaki tabloda açıklanmıştır.

    | URL bölümü        | Açıklama       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Wingtip uygulamasının olaylar bölümü.<br /><br /> *-DPT* , Wingtip bilet 'nin *kiracı başına veritabanı* uygulamasını diğer uygulamalardan ayırır. Bu örnek, *tek* kiracılı kiracı ( *-sa*) veya *çok kiracılı veritabanı* ( *-MT*) uygulamalarından örnektir. |
    | . *&lt;kullanıcı&gt;* | örnekteki *AF1* . |
    | . trafficmanager.net/ | Traffic Manager, temel URL. |
    | fabrikamjazzclub | Fabrikam Cakulüler adlı kiracıyı tanımlar. |
    | &nbsp; | &nbsp; |

- Kiracı adı, olaylar uygulamasına göre URL 'den ayrıştırılır.
- Kiracı adı bir anahtar oluşturmak için kullanılır.
- Anahtar, kiracının veritabanının konumunu almak için kataloğa erişmek üzere kullanılır.
  - Katalog, parça *eşleme yönetimi*kullanılarak uygulanır.
- Olay Hub 'ı, her kiracı için olaylar listesi sayfa URL 'Lerini oluşturmak üzere katalogda genişletilmiş meta verileri kullanır.

Bir üretim ortamında, genellikle [*Şirket İnternet etki alanı*](../traffic-manager/traffic-manager-point-internet-domain.md) Traffic Manager DNS adına işaret eden BIR CNAME DNS kaydı oluşturursunuz.

> [!NOTE]
> Traffic Manager 'ın kullanılması bu öğreticide hemen açık olmayabilir. Bu öğretici serisinin amacı, karmaşık bir üretim ortamının ölçeğini işleyebilen desenleri göstermektir. Böyle bir durumda, örneğin, dünya genelinde dağıtılmış birden çok Web uygulamanız vardır ve veritabanları ile birlikte yer alır ve Traffic Manager 'ın bu örnekler arasında yönlendirme yapması gerekir.
Traffic Manager 'ın kullanımını gösteren başka öğreticiler kümesi, [coğrafi geri yükleme](saas-dbpertenant-dr-geo-restore.md) ve [coğrafi çoğaltma](saas-dbpertenant-dr-geo-replication.md) öğreticilerinde de bulunur. Bu öğreticilerde Traffic Manager, bölgesel bir kesinti olması durumunda SaaS uygulamasının bir kurtarma örneğine geçiş yapmak için kullanılır.

## <a name="start-generating-load-on-the-tenant-databases"></a>Kiracı veritabanları üzerinde yük oluşturmaya başlama

Artık uygulama dağıtıldığına göre, şimdi çalışmaya bakalım.

*Demo-LoadGenerator* PowerShell betiği, tüm kiracı veritabanlarına karşı çalışan bir iş yükünü başlatır. Birçok SaaS uygulamasında gerçek dünyada yük, tek tek ve tahmin edilemez. Bu tür yükün benzetimini yapmak için Oluşturucu, her bir kiracıda rastgele ani artışlar veya etkinlik içeren bir yük oluşturur. Artışlarıyla rastgele aralıklarda oluşur. Yük deseninin ortaya geçmesi birkaç dakika sürer. Yüklemeyi izlemeye başlamadan önce oluşturucunun en az üç veya dört dakika çalışmasına izin verin.

1. PowerShell ıSE 'de,...\\öğrenme modüllerini\\yardımcı programları\\*demo-LoadGenerator. ps1* betiği ' ni açın.
2. Betiği çalıştırmak için F5 tuşuna basın ve yük oluşturucuyu başlatın. Varsayılan parametre değerlerini Şu anda bırakın.
3. Azure hesabınızda oturum açın ve gerekirse kullanmak istediğiniz aboneliği seçin.

Yük Oluşturucu betiği, katalogdaki her veritabanı için bir arka plan işi başlatır ve sonra duraklar. Yük Oluşturucu betiğini yeniden çalıştırırsanız, bu, yeni bir tane başlamadan önce çalıştıran tüm arka plan işlerini sonlandırır.

### <a name="monitor-the-background-jobs"></a>Arka plan işlerini izleme

Arka plan işlerini denetlemek ve izlemek isterseniz, aşağıdaki cmdlet 'leri kullanın:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator. ps1 eylemleri

*Demo-LoadGenerator. ps1* , müşteri işlemlerinin etkin iş yükünü taklit eder. Aşağıdaki adımlarda, *demo-LoadGenerator. ps1* ' nin başlattığı eylemlerin sırası açıklanır:

1. *Demo-LoadGenerator. ps1* ön planda *loadgenerator. ps1* başlatır.

    - Her iki. ps1 dosyası de\\yardımcı programlar\\klasörler öğrenme modülleri altında depolanır.

2. , Katalogdaki tüm kiracı veritabanlarında *Loadgenerator. ps1* döngüleri.

3. *Loadgenerator. ps1* her kiracı veritabanı için bir arka plan PowerShell işi başlatır:

    - Varsayılan olarak, arka plan işleri 120 dakika boyunca çalışır.
    - Her iş, *sp_CpuLoadGenerator*yürüterek bir KIRACı veritabanında CPU tabanlı yüke neden olur. Yükün yoğunluğu ve süresi `$DemoScenario`göre değişir.
    - yüksek CPU yüküne neden olan bir SQL SELECT ifadesinin etrafında döngüler *sp_CpuLoadGenerator* . SEÇIM sorunları arasındaki zaman aralığı, denetlenebilir bir CPU yükü oluşturmak için parametre değerlerine göre değişir. Yük düzeyleri ve aralıklar daha gerçekçi yükün benzetimini yapmak için rasgeledir.
    - Bu. SQL dosyası *Wingtiptenantdb\\dbo\\StoredProcedures\\* altında depolanır.

4. `$OneTime = $false`, yük Oluşturucu arka plan işlerini başlatır ve çalışmaya devam eder. Her 10 saniyede, sağlanan tüm yeni kiracılar için izler. `$OneTime = $true`ayarlarsanız, LoadGenerator arka plan işlerini başlatır ve ardından ön planda çalışmayı sonlandırır. Bu öğretici için `$OneTime = $false`bırakın.

   Yük oluşturucuyu durdurmak veya yeniden başlatmak istiyorsanız CTRL-C veya Durdur Işlemini CTRL-BREAK kullanın.

   Yük oluşturucuyu ön planda çalışır durumda bırakırsanız, diğer PowerShell betiklerini çalıştırmak için başka bir PowerShell ıSE örneği kullanın.

&nbsp;

Sonraki bölüme geçmeden önce, iş çağırma durumunda yük oluşturucuyu çalışır durumda bırakın.

## <a name="provision-a-new-tenant"></a>Yeni bir kiracı sağlama

İlk dağıtım üç örnek kiracı oluşturur. Şimdi, dağıtılan uygulamanın etkisini görmek için başka bir kiracı oluşturursunuz. Wingtip App 'te yeni kiracılar sağlamak için iş akışı, [sağlama ve Katalog öğreticisinde](saas-dbpertenant-provision-and-catalog.md)açıklanmaktadır. Bu aşamada, bir dakikadan kısa süren yeni bir kiracı oluşturacaksınız.

1. Yeni bir PowerShell ıSE açın.
2. Açık...\\öğrenimi Modules\Provision ve Catalog\\*demo-ProvisionAndCatalog. ps1*.
3. Betiği çalıştırmak için F5 tuşuna basın. Şimdilik varsayılan değerleri bırakın.

   > [!NOTE]
   > Birçok Wingtip SaaS komut dosyası, diğer betiklerdeki işlevleri çağırmak için Klasörlere gözatıp *$PSScriptRoot* kullanır. Bu değişken yalnızca, tam komut dosyası F5 tuşuna basılarak yürütüldüğünde değerlendirilir. Bir seçimi F8 ile vurgulama ve çalıştırma, hatalara neden olabilir. Betikleri çalıştırmak için F5 'e basın.

Yeni kiracı veritabanı:

- Bir SQL elastik havuzunda oluşturulur.
- Başlatıldığını.
- Kataloğa kaydedilir.

Hazırlama başarılı olduktan sonra, yeni kiracının *Olaylar* sitesi tarayıcınızda görüntülenir.

![Yeni kiracı](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Yeni kiracının listede görünmesini sağlamak için olaylar hub 'ını yenileyin.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Sunucuları, havuzları ve kiracı veritabanlarını öğrenme

Kiracıların koleksiyonuna karşı bir yük çalıştırmaya başladığınıza göre, dağıtılmış kaynaklara göz atalım.

1.  [Azure Portal](https://portal.azure.com), SQL Server listenize gidin. Ardından, **Katalog-DPT-&lt;kullanıcı&gt;** sunucusu ' nu açın.
    - Katalog sunucusu, **tenantcatalog** ve **basetenantdb** olmak üzere iki veritabanı içerir (yeni kiracılar oluşturmak için kopyalanmış bir şablon veritabanı).

   ![Veritabanları](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. SQL Server listenize geri dönün.

3. Kiracı veritabanlarını tutan **tenants1-DPT-&lt;USER&gt;** sunucusunu açın.

4. Aşağıdaki öğelere bakın:

    - Her kiracı veritabanı, 50 eDTU standart havuzundaki **Esnek standart** bir veritabanıdır.
    - Kırmızı çaağaç yarış veritabanı, daha önce sağladığınız kiracı veritabanıdır.

   ![Veritabanları içeren sunucu](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Havuzu izleme

*Loadgenerator. ps1* birkaç dakika çalıştıktan sonra, bazı izleme özelliklerine bakmak için yeterli veri kullanılabilir olmalıdır. Bu yetenekler havuzlar ve veritabanları içinde yerleşik olarak bulunur.

**Tenants1-DPT-&lt;user&gt;** sunucusuna gidin ve havuzun kaynak kullanımını görüntülemek için **Pool1** ' yi seçin. Aşağıdaki grafiklerde, yük Oluşturucu bir saat boyunca çalışır.

   ![Havuzu izle](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- **Kaynak kullanımı**etiketli ilk grafik, havuz eDTU kullanımını gösterir.
- İkinci grafik, havuzdaki beş etkin veritabanının eDTU kullanımını gösterir.

İki grafik, elastik havuzların ve SQL veritabanının öngörülemeyen SaaS uygulaması iş yüklerine iyi uygun olduğunu gösterir. Grafiklerde dört veritabanının her biri 40 eDTU 'ya kadar olan her şey olduğunu ve ancak tüm veritabanlarının 50-eDTU havuzu tarafından rahat bir şekilde desteklenecek olduğunu gösterir. 50-eDTU havuzu daha ağır iş yüklerini destekleyebilir. Veritabanları tek veritabanları olarak sağlandıysa, her birinin bursts 'yi desteklemek için S2 (50 DTU) olması gerekir. Dört adet tek S2 veritabanının maliyeti, havuzun fiyatının neredeyse üç katı olur. Gerçek dünyada durumlarda SQL veritabanı müşterileri, 200 eDTU havuzlarında en fazla 500 veritabanı çalıştırır. Daha fazla bilgi için bkz. [performans izleme öğreticisi](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ek kaynaklar

- Daha fazla bilgi için bkz. [Wingtip bilet SaaS veritabanı kiracı başına uygulama üzerinde derleme yapan ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Elastik havuzlar hakkında bilgi edinmek için bkz. [Azure SQL elastik havuzu nedir?](sql-database-elastic-pool.md).
- Elastik işler hakkında bilgi edinmek için bkz. [ölçekli bulut veritabanlarını yönetme](elastic-jobs-overview.md).
- Çok kiracılı SaaS uygulamaları hakkında bilgi edinmek için bkz. [çok kiracılı SaaS uygulamaları Için tasarım desenleri](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrendiniz:

> [!div class="checklist"]
> - Wingtip bilet SaaS uygulamasını dağıtma.
> - Uygulamayı oluşturan sunucular, havuzlar ve veritabanları hakkında.
> - Kiracılar, *katalogla*verileri ile eşlenir.
> - Yeni kiracılar sağlama.
> - Kiracı etkinliğini izlemek için havuz kullanımını görüntüleme.
> - İlgili faturalandırmayı durdurmak için örnek kaynakları silme.

Ardından, [sağlama ve Katalog öğreticisini](saas-dbpertenant-provision-and-catalog.md)deneyin.

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
