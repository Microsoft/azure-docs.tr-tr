---
title: Hiper ölçeğe genel bakış
description: Bu makalede, Azure SQL veritabanı 'ndaki sanal çekirdek tabanlı satın alma modelinde hiper ölçek hizmeti katmanı açıklanmakta ve bu hizmetin Genel Amaçlı ve İş Açısından Kritik hizmet katmanlarından farklı olduğu açıklanır.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: c71fb8a7e18439817023874146e22c29a5af3b12
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123695"
---
# <a name="hyperscale-service-tier"></a>Hiper ölçekli hizmet katmanı

Azure SQL veritabanı, altyapı hatalarının durumlarında bile% 99,99 kullanılabilirlik sağlamak için bulut ortamı için ayarlanmış SQL Server veritabanı motoru mimarisini temel alır. Azure SQL veritabanı 'nda kullanılan üç mimari modeli vardır:
- Genel Amaçlı/standart 
-  Hiper Ölçek
-  İş Açısından Kritik/Premium

Azure SQL veritabanı 'nda bulunan hiper ölçekli hizmet katmanı, sanal çekirdek tabanlı satın alma modelindeki en yeni hizmet katmandır. Bu hizmet katmanı, bir Azure SQL veritabanı için depolama ve işlem kaynaklarının, Genel Amaçlı ve Iş için kullanılabilen limitlerin büyük ölçüde ötesinde ölçeğini genişletmek için Azure mimarisinden yararlanan, yüksek düzeyde ölçeklenebilir bir depolama ve bilgi işlem performans katmanından yararlanır Kritik hizmet katmanları.

> 
> [!NOTE]
> Sanal çekirdek tabanlı satın alma modelindeki Genel Amaçlı ve İş Açısından Kritik hizmet katmanlarına ilişkin ayrıntılar için bkz. [genel amaçlı](sql-database-service-tier-general-purpose.md) ve [iş açısından kritik](sql-database-service-tier-business-critical.md) hizmet katmanları. DTU tabanlı satın alma modeliyle sanal çekirdek tabanlı satın alma modeli karşılaştırması için bkz. [Azure SQL veritabanı satın alma modelleri ve kaynakları](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Hiperscale özellikleri nelerdir?

Azure SQL veritabanı 'ndaki hiper ölçek hizmeti katmanı aşağıdaki ek özellikleri sağlar:

- 100 TB 'a kadar veritabanı boyutu desteği
- İşlem kaynaklarında GÇ etkisi olmayan, neredeyse anlık veritabanı yedeklemeleri (Azure Blob depolamada depolanan dosya anlık görüntülerine göre)  
- Kısa bir süre içinde (dosya anlık görüntülerine göre), saat veya gün yerine dakikalar içinde hızlı veritabanı geri yüklemeleri (veri işleme boyutu değil)
- Veri birimlerinden bağımsız olarak daha yüksek günlük verimlilik ve daha hızlı işlem işleme sürelerinden dolayı daha yüksek genel performans
- Hızlı genişleme-okuma iş yükünüzü boşaltma ve sık erişimli olarak kullanım için bir veya daha fazla salt okuma düğümü sağlayabilirsiniz
- Hızlı ölçek artırma-sabit zamanlı olarak, yoğun iş yüklerinizi uygun olduğunda ve gerektiğinde işlem kaynaklarını geriye doğru ölçeklendirerek, işlem kaynaklarını ölçeklendirebilmeniz gerekir.

Hiper ölçek hizmeti katmanı, bulut veritabanlarında geleneksel olarak görülen pratik limitlerin çoğunu ortadan kaldırır. Diğer birçok veritabanının tek bir düğümde kullanılabilen kaynaklarla sınırlı olduğu yerlerde, hiper ölçek hizmet katmanındaki veritabanları bu tür sınırlara sahip değildir. Esnek depolama mimarisi sayesinde, depolama gerektiği gibi artar. Aslında, hiper ölçek veritabanları tanımlanmış en büyük boyutla oluşturulmaz. Hiper ölçekli bir veritabanı gerektiğinde büyür ve yalnızca kullandığınız kapasite için faturalandırılırsınız. Okuma yoğunluklu iş yükleri için, hiper ölçek hizmeti katmanı, okuma iş yüklerini boşaltma için gerektiğinde ek okuma çoğaltmaları sağlayarak hızlı genişleme sağlar.

Ayrıca, veritabanı yedeklemeleri oluşturmak için gereken süre ve ölçeği artırma veya azaltma, artık veritabanındaki veri hacmine bağlı değildir. Hiper ölçekli veritabanları neredeyse anında yedeklenebilir. Ayrıca, dakikada on terabaytlık bir veritabanını yukarı veya aşağı doğru bir şekilde ölçekleyebilirsiniz. Bu özellik, sizi ilk yapılandırma seçimlerinizde kutulandığında kaygıları ortadan boşaltır.

Hiper ölçek hizmet katmanının işlem boyutları hakkında daha fazla bilgi için bkz. [hizmet katmanı özellikleri](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Hiper ölçek hizmet katmanını kimler kabul etmelidir

Hiper ölçekli hizmet katmanı, bağımsız olarak ölçeklenebilir işlem ve depolama kaynaklarıyla harika esneklik ve yüksek performans sağladığından, çoğu iş yükü için tasarlanmıştır. Depolama alanını 100 TB 'a kadar otomatik olarak ölçeklendirebilme olanağı sunan bu, şunları yapan müşteriler için harika bir seçimdir:

- Şirket içinde büyük veritabanlarına sahip olmak ve buluta geçerek uygulamalarını modernleştirin etmek istiyorsunuz
- Zaten bulutta ve diğer hizmet katmanlarındaki en büyük veritabanı boyutu kısıtlamalarına göre sınırlandırılmıştır (1-4 TB)
- Daha küçük veritabanlarına sahiptir, ancak hızlı dikey ve yatay işlem ölçekleme, yüksek performans, anlık yedekleme ve hızlı veritabanı geri yükleme gerekir.

Hyperscale hizmet katmanı, saf OLTP 'dan saf Analize kadar çok sayıda SQL Server iş yüklerini destekler, ancak özellikle OLTP ve karma işlem ve analitik işleme (HTAP) iş yükleri için iyileştirilmiştir.

> [!IMPORTANT]
> Elastik havuzlar hiper ölçek hizmet katmanını desteklemez.

## <a name="hyperscale-pricing-model"></a>Hiper ölçek fiyatlandırma modeli

Hiper ölçek hizmeti katmanı yalnızca [Vcore modelinde](sql-database-service-tiers-vcore.md)kullanılabilir. Yeni mimariye uyum sağlamak için, fiyatlandırma modeli Genel Amaçlı veya İş Açısından Kritik hizmet katmanlarından biraz farklıdır:

- **İşlem**:

  Hiper ölçek işlem birimi fiyatı çoğaltma başına. [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) fiyatı, otomatik olarak okuma ölçeği çoğaltmalarına uygulanır. Varsayılan olarak hiper ölçekli veritabanı başına bir birincil çoğaltma ve bir salt okuma çoğaltması oluşturacağız.  Kullanıcılar, 1-5 'den birincil dahil olmak üzere toplam çoğaltma sayısını ayarlayabilir.

- **Depolama alanı**:

  Hiper ölçekli bir veritabanını yapılandırırken en büyük veri boyutunu belirtmeniz gerekmez. Hiper ölçeklendirme katmanında, veritabanınıza yönelik depolama alanı için gerçek kullanıma göre ücret alınır. Depolama, 10 GB ila 40 GB arasında dinamik olarak ayarlanan artışlarla otomatik olarak 10 GB ve 100 TB arasında ayrılır.  

Hiperscale fiyatlandırması hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Dağıtılmış işlevler mimarisi

Tek bir konum/işlem içindeki tüm veri yönetimi işlevlerinin merkezi olan geleneksel veritabanı altyapılarından farklı olarak (günümüzde üretimde dağıtılmış veritabanlarının tek parçalı veri altyapısının birden çok kopyasına sahip olması durumunda), hiper ölçekli bir veritabanı, çeşitli veri altyapılarının semantiğinin, veriler için uzun süreli depolama ve dayanıklılık sağlayan bileşenlerden, sorgu işleme altyapısını ayırır. Bu şekilde, depolama kapasitesi gerektiği kadar düzgün şekilde ölçeklendirilebilir (başlangıç hedefi 100 TB). Salt okunur çoğaltmalar aynı depolama bileşenlerini paylaşır, böylece yeni bir okunabilir çoğaltmayı çalıştırmak için veri kopyalama gerekmez. 

Aşağıdaki diyagramda, bir hiper ölçek veritabanındaki farklı düğüm türleri gösterilmektedir:

![architecture](./media/sql-database-hyperscale/hyperscale-architecture2.png)

Hiper ölçekli bir veritabanı aşağıdaki farklı bileşen türlerini içerir:

### <a name="compute"></a>Bilgi İşlem

İşlem düğümü, ilişkisel altyapının bulunduğu yerdir, bu nedenle tüm dil öğeleri, sorgu işleme vb. oluşur. Hiper ölçekli veritabanı olan tüm kullanıcı etkileşimleri, bu işlem düğümleri aracılığıyla gerçekleşir. İşlem düğümlerinde, veri sayfasını getirmek için gereken ağ gidiş dönüşlerin sayısını en aza indirmek için SSD tabanlı önbellekler (önceki diyagramda RBPEX-dayanıklı arabellek havuzu uzantısı olarak etiketlenir) vardır. Tüm okuma/yazma iş yüklerinin ve işlemlerinin işlendiği bir birincil işlem düğümü vardır. Yük devretme amaçlarıyla etkin bekleme düğümleri görevi gören bir veya daha fazla ikincil işlem düğümü vardır ve okuma iş yüklerini boşaltma için salt okunurdur işlem düğümleri görevi görür (Bu işlev isteniyorsa).

### <a name="page-server"></a>Sayfa sunucusu

Sayfa sunucuları, ölçeği genişletilmiş bir depolama altyapısını temsil eden sistemlerdir.  Her sayfa sunucusu, veritabanındaki sayfaların bir alt kümesinden sorumludur.  Aday olarak, her sayfa sunucusu 128 GB ve 1 TB veri arasında denetler. Birden fazla sayfa sunucusunda (artıklık ve kullanılabilirlik için tutulan çoğaltmalar dışında) hiç veri paylaşılmıyor. Bir sayfa sunucusunun işi, veritabanı sayfalarını istek üzerine işlem düğümlerine sunmaya ve sayfaların işlem güncellik olarak güncelleştirilmesini sağlamak için kullanılır. Sayfa sunucuları günlük hizmetinden günlük kayıtlarını oynatarak güncel tutulur. Sayfa sunucuları, performansı geliştirmek için SSD tabanlı önbellekleri de korur. Veri sayfalarının uzun süreli depolaması, ek güvenilirlik sağlamak için Azure depolama 'da tutulur.

### <a name="log-service"></a>Günlük hizmeti

Günlük hizmeti, birincil işlem çoğaltmasındaki günlük kayıtlarını kabul eder, bunları dayanıklı bir önbellekte devam ettirir ve günlük kayıtlarını, verilerin güncelleştirilebilmesi için ilgili sayfa sunucuları ve ilgili sayfa sunucuları gibi işlem çoğaltmalarının geri kalanına iletir. vardır. Bu şekilde, birincil işlem çoğaltmasındaki tüm veriler, tüm ikincil işlem çoğaltmaları ve sayfa sunucularına günlük hizmeti aracılığıyla dağıtılır. Son olarak, günlük kayıtları, Azure Storage 'daki uzun süreli depolamaya gönderilir ve bu, neredeyse sonsuz bir depolama deposudur. Bu mekanizma, sık kullanılan günlük kesilmesi gereksinimini ortadan kaldırır. Günlük hizmeti 'nin günlük kayıtlarına erişimi hızlandırmak için yerel önbelleği de vardır.

### <a name="azure-storage"></a>Azure Storage

Azure depolama, bir veritabanındaki tüm veri dosyalarını içerir. Sayfa sunucuları veri dosyalarını Azure depolama 'da güncel tutar. Bu depolama, yedekleme amaçları için ve Azure bölgeleri arasında çoğaltma için kullanılır. Yedeklemeler, veri dosyalarının depolama anlık görüntüleri kullanılarak uygulanır. Anlık görüntüleri kullanarak geri yükleme işlemleri, veri boyutundan bağımsız olarak hızlıdır. Veriler, veritabanının yedekleme saklama süresi içinde herhangi bir noktaya geri yüklenebilir.

## <a name="backup-and-restore"></a>Yedekleme ve geri yükleme

Yedeklemeler dosya anlık görüntüsü tabanlıdır ve bu nedenle neredeyse anında yapılır. Depolama ve işlem ayrımı, birincil işlem çoğaltmasındaki işleme yükünü azaltmak için yedekleme/geri yükleme işleminin depolama katmanına itimmesini sağlar. Sonuç olarak, veritabanı yedeklemesi birincil işlem düğümünün performansını etkilemez; benzer şekilde, geri yüklemeler dosya anlık görüntülerine geri dönerek yapılır, bu nedenle de veri işlemi boyutu değildir. Geri yükleme, sabit zamanlı bir işlemdir ve hatta birden çok terabaytlık veritabanları saat veya gün değil dakikalar içinde geri yüklenebilir. Mevcut bir yedeklemeyi geri yükleyerek yeni veritabanlarının oluşturulması, bu özellikten de yararlanır: aynı mantıksal sunucu içindeki geliştirme veya test amaçları için veritabanı kopyaları oluşturma, terabayttan boyutlu veritabanları bile, dakikalar içinde dolanabilir.

## <a name="scale-and-performance-advantages"></a>Ölçek ve performans avantajları

Ek salt okuma işlem düğümlerini hızlı bir şekilde artırma/azaltma özelliği sayesinde, hiper ölçek mimarisi önemli okuma ölçeği özelliklerine izin verir ve ayrıca, daha fazla yazma isteği sunmak için birincil işlem düğümünü de serbest bırakabilirsiniz. Ayrıca, hiper ölçek mimarisinin paylaşılan depolama mimarisi nedeniyle işlem düğümleri hızlı bir şekilde ölçeklenebilir/aşağı ölçeklendirilebilir.

## <a name="create-a-hyperscale-database"></a>Hiper ölçekli veritabanı oluşturma

[Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) veya [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)kullanılarak bir hiper ölçek veritabanı oluşturulabilir. Hiper ölçekli veritabanları yalnızca [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md)kullanılarak kullanılabilir.

Aşağıdaki T-SQL komutu, hiper ölçekli bir veritabanı oluşturur. `CREATE DATABASE` bildiriminde hem sürüm hem de hizmet hedefini belirtmeniz gerekir. Geçerli hizmet amaçları listesinin [kaynak sınırlarına](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen5) bakın.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Bu, 4 çekirdek içeren 5. nesil donanımında bir hiper ölçek veritabanı oluşturur.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Mevcut bir Azure SQL veritabanını hiper ölçekli hizmet katmanına geçirme

Mevcut Azure SQL veritabanlarınızı [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) veya [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)kullanarak hiper ölçeğe taşıyabilirsiniz. Bu sırada, tek yönlü geçişdir. Verileri dışa ve içe aktararak farklı bir hizmet katmanına Hyperscale 'den başka bir hizmet katmanına taşıyamazsınız. Kavram provaları (POCs) için üretim veritabanlarınızın bir kopyasını oluşturmanızı ve kopyayı hiper ölçeğe geçirmeyi öneririz. Mevcut bir Azure SQL veritabanının hiper ölçek katmanına geçirilmesi veri işleme boyutudur.

Aşağıdaki T-SQL komutu bir veritabanını hiper ölçek hizmeti katmanına taşıdır. `ALTER DATABASE` bildiriminde hem sürüm hem de hizmet hedefini belirtmeniz gerekir.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Hiper ölçekli bir veritabanının okuma ölçeğinde bir çoğaltmasını bağlama

Hiper ölçekli veritabanlarında, istemci tarafından belirtilen bağlantı dizesindeki `ApplicationIntent` bağımsız değişkeni bağlantının yazma çoğaltmasına mı yoksa salt bir ikincil çoğaltmayla mi yönlendirildiğini belirler. `ApplicationIntent` `READONLY` ve veritabanının ikincil bir çoğaltması yoksa, bağlantı birincil çoğaltmaya yönlendirilir ve varsayılan olarak `ReadWrite` davranışa ayarlanır.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Hiper ölçek ikincil çoğaltmaları, birincil çoğaltmayla aynı hizmet düzeyi hedefi kullanılarak aynıdır. Birden fazla ikincil çoğaltma varsa, iş yükü tüm kullanılabilir ikincil öğeler arasında dağıtılır. Her ikincil çoğaltma bağımsız olarak güncellenir, bu nedenle farklı çoğaltmaların birincil çoğaltmaya göre farklı veri gecikmesi olabilir.

## <a name="database-high-availability-in-hyperscale"></a>Hiper ölçekte veritabanı yüksek kullanılabilirlik

Diğer tüm hizmet katmanlarında olduğu gibi, hiper ölçek, işlem çoğaltma kullanılabilirliğine bakılmaksızın, kaydedilmiş işlemler için veri dayanıklılığını garanti eder. Birincil çoğaltmanın kullanılamamasından kaynaklanan kesinti süresi, yük devretme türüne (planlı vs. plansız) ve en az bir ikincil çoğaltmanın varlığına bağlıdır. Planlanmış bir yük devretmede (bir bakım olayı), sistem, yük devretmeyi başlatmadan önce yeni birincil çoğaltmayı oluşturur ya da yük devretme hedefi olarak var olan bir ikincil çoğaltmayı kullanır. Planlanmamış bir yük devretmede (örneğin, birincil çoğaltmada bir donanım arızası), sistem varsa bir yük devretme hedefi olarak ikincil bir çoğaltma kullanır veya kullanılabilir işlem kapasitesi havuzundan yeni bir birincil çoğaltma oluşturur. İkinci durumda, yeni birincil çoğaltmayı oluşturmak için gereken ek adımlar nedeniyle kesinti süresi daha uzun olur.

Hiperscale SLA için bkz. [Azure SQL veritabanı Için SLA](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Hiper ölçekli veritabanları için olağanüstü durum kurtarma

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Hiper ölçek veritabanını farklı bir Coğrafya 'ya geri yükleme
Bir Azure SQL veritabanı hiper ölçek veritabanını, bir olağanüstü durum kurtarma işleminin veya detaya gitme, yeniden konumlandırma ya da başka bir nedenden dolayı barındırmakta olduğu bir bölgeye geri yüklemeniz gerekiyorsa, birincil yöntem, veritabanının coğrafi olarak geri yüklenmesini sağlar.  Bu, başka bir AZURE SQL DB 'yi farklı bir bölgeye geri yüklemek için kullandığınız adımlarla tam olarak aynı adımları içerir:
1. Henüz uygun bir sunucunuz yoksa hedef bölgede bir SQL veritabanı sunucusu oluşturun.  Bu sunucunun özgün (kaynak) sunucuyla aynı aboneliğe sahip olması gerekir.
2. Azure SQL veritabanlarını otomatik yedeklemelerden geri yükleme sayfasındaki [coğrafi geri yükleme](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) konusundaki yönergeleri izleyin.

> [!NOTE]
> Kaynak ve hedef ayrı bölgelerde olduğundan, veritabanı coğrafi olarak son derece hızlı bir şekilde tamamlanan, coğrafi olmayan geri yüklemeler halinde kaynak veritabanıyla birlikte anlık görüntü depolamayı paylaşamaz.  Hiper ölçekli bir veritabanının coğrafi geri yüklemesi söz konusu olduğunda, hedef coğrafi olarak çoğaltılan depolamanın eşleştirilmiş bölgesinde olsa bile, veri boyutu bir işlem olur.  Bu, coğrafi geri yükleme yapmanın, geri yüklenmekte olan veritabanının boyutuyla orantılı bir zaman alabileceği anlamına gelir.  Hedef eşleştirilmiş bölgedeyse, kopya bir veri merkezinde yer alacak ve bu, internet üzerinden uzun mesafe kopyasından önemli ölçüde daha hızlı olacaktır, ancak yine de tüm bitleri kopyalayacaktır.

## <a name=regions></a>Kullanılabilir bölgeler

Azure SQL veritabanı hiper ölçek katmanı Şu anda aşağıdaki bölgelerde kullanılabilir:

- Avustralya Doğu
- Avustralya Güneydoğu
- Güney Brezilya
- Orta Kanada
- Orta ABD
- Çin Doğu 2
- Çin Kuzey 2
- Doğu Asya
- Doğu ABD
- Doğu ABD 2
- Fransa Orta
- Japonya Doğu
- Japonya Batı
- Kore Orta
- Kore Güney
- Orta Kuzey ABD
- Kuzey Avrupa
- Güney Afrika Kuzey
- Orta Güney ABD
- Güneydoğu Asya
- Birleşik Krallık Güney
- Birleşik Krallık Batı
- Batı Avrupa
- Batı ABD
- Batı ABD 2

Desteklenen olarak listelenmeyen bir bölgede hiper ölçekli veritabanı oluşturmak istiyorsanız, Azure portal aracılığıyla bir ekleme isteği gönderebilirsiniz. Desteklenen bölgelerin listesini genişletmek için çalışıyoruz. bu nedenle lütfen en son bölge listesini inceleyin.

Listelenmeyen bölgelerde hiper ölçekli veritabanları oluşturma özelliğini istemek için:

1. [Azure yardım ve destek dikey penceresine](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) gidin

2. [ **Yeni destek isteğine** tıklayın](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Azure yardım ve destek dikey penceresi](media/sql-database-service-tier-hyperscale/request-screen-1.png)

3. **Sorun türü**için **hizmet ve abonelik sınırları (kotalar)** seçeneğini belirleyin

4. Veritabanlarını oluşturmak için kullanacağınız aboneliği seçin

5. **Kota türü**için **SQL veritabanı** ' nı seçin.

6. Ileri ' ye tıklayın **: çözümler**

1. **Ayrıntıları sağla** ' ya tıklayın

    ![Sorun ayrıntıları](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. **SQL veritabanı kota türünü**seçin: **diğer kota isteği**

9. Aşağıdaki şablonu doldur:

    ![Kota ayrıntıları](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    Şablonda, aşağıdaki bilgileri sağlayın

    > Yeni bir bölgede Azure hiper ölçek SQL veritabanı oluşturma isteği<br/> Bölge: [istediğiniz bölgeyi doldur]  <br/>
    > Okunabilir çoğaltmalar dahil olmak üzere SKU/toplam çekirdekleri hesaplama <br/>
    > Tahmini TB sayısı 
    >

10. **Önem Derecesi C**’yi seçin

11. Uygun iletişim yöntemini seçin ve ayrıntıları girin.

12. **Kaydet** ve **devam et** 'e tıklayın

## <a name="known-limitations"></a>Bilinen sınırlamalar
Bunlar, GA ile aynı hiper ölçek hizmet katmanına yönelik geçerli sınırlamalardır.  Mümkün olduğunca bu sınırlamaların çoğunu kaldırmak için etkin bir şekilde çalışıyoruz.

| Sorun | Açıklama |
| :---- | :--------- |
| Bir mantıksal sunucu için yedeklemeleri Yönet bölmesi, hiper ölçek veritabanlarını göstermez ve SQL Server 'dan filtrelenecektir  | Hiper ölçek, yedeklemeleri yönetmek için ayrı bir yönteme sahiptir ve bu nedenle uzun süreli bekletme ve zaman içinde yedekleme bekletme ayarları uygulanmaz/geçersiz kılınır. Buna uygun olarak, hiper ölçekli veritabanları yedekleme bölmesini yönetme bölmesinde görünmez. |
| Belirli bir noktaya geri yükleme | Bir veritabanı hiper ölçek hizmeti katmanına geçirildikten sonra, geçiş işleminden önce bir zaman noktasına geri yükleme desteklenmez.|
| Hiperzölçekli DB 'yi Hypserscale 'e geri yükleme ve tam tersi | Hiper ölçekli bir veritabanını hiper olmayan bir veritabanına geri yükleyemezsiniz veya hiper ölçekli bir veritabanını hiper ölçekli bir veritabanına geri yükleyemezsiniz.|
| Bir veritabanında 1 TB 'den büyük bir veya daha fazla veri dosyası varsa, geçiş başarısız olur | Bazı durumlarda, büyük dosyaları 1 TB 'tan küçük olacak şekilde küçülterek Bu soruna geçici bir çözüm uygulanabilir. Geçiş işlemi sırasında kullanılan bir veritabanını geçiriyorsanız, hiçbir dosyanın 1 TB 'den büyük olmadığından emin olun. Veritabanı dosyalarının boyutunu anlamak için aşağıdaki sorguyu kullanın. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Yönetilen Örnek | Azure SQL veritabanı yönetilen örneği şu anda hiper ölçekli veritabanları ile desteklenmiyor. |
| Esnek Havuzlar |  Esnek havuzlar Şu anda SQL veritabanı Hyperscale ile desteklenmemektedir.|
| Hyperscale 'e geçiş Şu anda tek yönlü bir işlemdir | Bir veritabanı hiper ölçeğe geçirildiğinde doğrudan Hyperscale olmayan bir hizmet katmanına geçirilemez. Mevcut olduğunda, bir veritabanını Hiperscale 'den hiper olmayan ölçeğe geçirmenin tek yolu BACPAC dosyasını veya diğer veri taşıma teknolojilerini (toplu kopyalama, Azure Data Factory, Azure Databricks, SSIS, vb.) kullanarak dışarı ve içeri aktarmaya yönelik bir yoldur.|
| Kalıcı bellek içi nesneleri olan veritabanlarının geçirilmesi | Hiper ölçek yalnızca kalıcı olmayan bellek nesnelerini destekler (tablo türleri, yerel SPs 'ler ve işlevler).  Kalıcı bellek Içi tablolar ve diğer nesneler, bir veritabanını hiper ölçek hizmet katmanına geçirmeden önce, bellek içi nesneler olarak bırakılmalıdır ve yeniden oluşturulmalıdır.|
| Değişiklik İzleme | Azure SQL Hyperscale veritabanları ile Değişiklik İzleme henüz yapılandırıp kullanamazsınız. |
| Coğrafi Çoğaltma  | Azure SQL veritabanı hiper ölçek için Coğrafi çoğaltmayı henüz yapılandıramazsınız. |
| Veritabanı kopyalama | Azure SQL Hyperscale 'de yeni bir veritabanı oluşturmak için henüz veritabanı kopyasını kullanamazsınız. |
| TDE/AKV tümleştirmesi | Azure Key Vault kullanılarak Saydam veritabanı şifrelemesi (genellikle kendi anahtarını getir veya BYOK olarak adlandırılır), Azure SQL veritabanı Hyperscale için henüz desteklenmemiştir, ancak hizmet tarafından yönetilen anahtarlarla birlikte TDE desteklenir. |
|Akıllı veritabanı özellikleri | "Plan zorla" seçeneğinin dışında, diğer tüm otomatik ayarlama seçenekleri hiper ölçekte henüz desteklenmemiştir: seçenekler etkin gibi görünebilir, ancak hiçbir öneri veya eylem yapılmaz. |

## <a name="next-steps"></a>Sonraki adımlar

- Hyperscale hakkında bir SSS için bkz. [hyperscale hakkında sık sorulan sorular](sql-database-service-tier-hyperscale-faq.md).
- Hizmet katmanları hakkında daha fazla bilgi için bkz. [hizmet katmanları](sql-database-service-tiers.md)
- Sunucu ve abonelik düzeylerindeki sınırlar hakkında bilgi için bkz. [mantıksal sunucudaki kaynak sınırlarına genel bakış](sql-database-resource-limits-logical-server.md) .
- Tek bir veritabanı için model sınırları satın almak için, [tek bir veritabanı için bkz. Azure SQL veritabanı sanal çekirdek tabanlı satın alma modeli sınırları](sql-database-vcore-resource-limits-single-databases.md).
- Özellikler ve karşılaştırma listesi için bkz. [SQL ortak özellikleri](sql-database-features.md).
