---
title: Hiper ölçek hizmeti katmanı nedir?
description: Bu makalede, Azure SQL veritabanı 'ndaki sanal çekirdek tabanlı satın alma modelinde hiper ölçek hizmeti katmanı açıklanmakta ve bu hizmetin Genel Amaçlı ve İş Açısından Kritik hizmet katmanlarından farklı olduğu açıklanır.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: e0982b4a43a931552574e447d5639d3fa92402d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773781"
---
# <a name="hyperscale-service-tier"></a>Hiper ölçekli hizmet katmanı

Azure SQL veritabanı, altyapı hatalarının durumlarında bile% 99,99 kullanılabilirlik sağlamak için bulut ortamı için ayarlanmış SQL Server veritabanı motoru mimarisini temel alır. Azure SQL veritabanı 'nda kullanılan üç mimari modeli vardır:

- Genel Amaçlı/standart
- Hiper Ölçek
- İş Açısından Kritik/Premium

Azure SQL veritabanı 'nda bulunan hiper ölçekli hizmet katmanı, sanal çekirdek tabanlı satın alma modelindeki en yeni hizmet katmandır. Bu hizmet katmanı, bir Azure SQL veritabanı için depolama ve işlem kaynaklarının, Genel Amaçlı ve İş Açısından Kritik hizmet katmanları için kullanılabilir limitlerin büyük ölçüde ötesinde ölçeğini genişletmek için Azure mimarisinden yararlanan, yüksek düzeyde ölçeklenebilir bir depolama ve işlem performansı katmandır.

> [!NOTE]
>
> - Sanal çekirdek tabanlı satın alma modelindeki Genel Amaçlı ve İş Açısından Kritik hizmet katmanlarına ilişkin ayrıntılar için bkz. [genel amaçlı](service-tier-general-purpose.md) ve [iş açısından kritik](service-tier-business-critical.md) hizmet katmanları. DTU tabanlı satın alma modeliyle sanal çekirdek tabanlı satın alma modeli karşılaştırması için bkz. [Azure SQL veritabanı satın alma modelleri ve kaynakları](purchasing-models.md).
> - Hiper ölçek hizmeti katmanı Şu anda yalnızca Azure SQL veritabanı için kullanılabilir ve Azure SQL yönetilen örneği değildir.

## <a name="what-are-the-hyperscale-capabilities"></a>Hiperscale özellikleri nelerdir?

Azure SQL veritabanı 'ndaki hiper ölçek hizmeti katmanı aşağıdaki ek özellikleri sağlar:

- 100 TB 'a kadar veritabanı boyutu desteği
- İşlem kaynaklarında GÇ etkisi olmayan, neredeyse anlık veritabanı yedeklemeleri (Azure Blob depolamada depolanan dosya anlık görüntülerine göre)  
- Kısa veritabanı geri yüklemeleri (dosya anlık görüntülerini temel alarak) saatler veya günler yerine dakikalar içinde (veri işlemi boyutu değil)
- Veri birimlerinden bağımsız olarak daha yüksek günlük verimlilik ve daha hızlı işlem işleme sürelerinden dolayı daha yüksek genel performans
- Hızlı genişleme-okuma iş yükünüzü boşaltma ve sık erişimli olarak kullanım için bir veya daha fazla salt okuma düğümü sağlayabilirsiniz
- Hızlı ölçeği artırma-sabit zamanlı olarak, gerektiğinde ağır iş yüklerine uyum sağlamak için işlem kaynaklarınızı ölçeklendirebilir ve ardından gerekli olmadığında işlem kaynaklarını geri doğru ölçeklendirdirebilirsiniz.

Hiper ölçek hizmeti katmanı, bulut veritabanlarında geleneksel olarak görülen pratik limitlerin çoğunu ortadan kaldırır. Diğer birçok veritabanının tek bir düğümde kullanılabilen kaynaklarla sınırlı olduğu yerlerde, hiper ölçek hizmet katmanındaki veritabanları bu tür sınırlara sahip değildir. Esnek depolama mimarisi sayesinde, depolama gerektiği gibi artar. Aslında, hiper ölçek veritabanları tanımlanmış en büyük boyutla oluşturulmaz. Hiper ölçekli bir veritabanı gerektiğinde büyür ve yalnızca kullandığınız kapasite için faturalandırılırsınız. Okuma yoğunluklu iş yükleri için, hiper ölçek hizmeti katmanı, okuma iş yüklerini boşaltma için gerektiğinde ek okuma çoğaltmaları sağlayarak hızlı genişleme sağlar.

Ayrıca, veritabanı yedeklemeleri oluşturmak için gereken süre ve ölçeği artırma veya azaltma, artık veritabanındaki veri hacmine bağlı değildir. Hiper ölçekli veritabanları neredeyse anında yedeklenebilir. Ayrıca, dakikada on terabaytlık bir veritabanını yukarı veya aşağı doğru bir şekilde ölçekleyebilirsiniz. Bu özellik, sizi ilk yapılandırma seçimlerinizde kutulandığında kaygıları ortadan boşaltır.

Hiper ölçek hizmet katmanının işlem boyutları hakkında daha fazla bilgi için bkz. [hizmet katmanı özellikleri](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Hiper ölçek hizmet katmanını kimler kabul etmelidir

Hiper ölçekli hizmet katmanı, bağımsız olarak ölçeklenebilir işlem ve depolama kaynaklarıyla harika esneklik ve yüksek performans sağladığından, çoğu iş yükü için tasarlanmıştır. Depolama alanını 100 TB 'a kadar otomatik ölçeklendirme olanağı sunan bu, şunları yapan müşteriler için harika bir seçimdir:

- Şirket içinde büyük veritabanlarına sahip olmak ve buluta geçerek uygulamalarını modernleştirin etmek istiyorsunuz
- Zaten bulutta ve diğer hizmet katmanlarındaki en büyük veritabanı boyutu kısıtlamalarına göre sınırlandırılmıştır (1-4 TB)
- Daha küçük veritabanlarına sahiptir, ancak hızlı dikey ve yatay işlem ölçekleme, yüksek performans, anlık yedekleme ve hızlı veritabanı geri yükleme gerekir.

Hyperscale hizmet katmanı, saf OLTP 'dan saf Analize kadar çok sayıda SQL Server iş yükünü destekler, ancak özellikle OLTP ve karma işlem ve analitik işleme (HTAP) iş yükleri için iyileştirilmiştir.

> [!IMPORTANT]
> Elastik havuzlar hiper ölçek hizmet katmanını desteklemez.

## <a name="hyperscale-pricing-model"></a>Hiper ölçek fiyatlandırma modeli

Hiper ölçek hizmeti katmanı yalnızca [Vcore modelinde](service-tiers-vcore.md)kullanılabilir. Yeni mimariye uyum sağlamak için, fiyatlandırma modeli Genel Amaçlı veya İş Açısından Kritik hizmet katmanlarından biraz farklıdır:

- **İşlem**:

  Hiper ölçek işlem birimi fiyatı çoğaltma başına. [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) fiyatı, otomatik olarak okuma ölçeği çoğaltmalarına uygulanır. Varsayılan olarak hiper ölçekli veritabanı başına bir birincil çoğaltma ve bir salt okuma çoğaltması oluşturacağız.  Kullanıcılar, 1-5 'den birincil dahil olmak üzere toplam çoğaltma sayısını ayarlayabilir.

- **Depolama alanı**:

  Hiper ölçekli bir veritabanını yapılandırırken en büyük veri boyutunu belirtmeniz gerekmez. Hiper ölçek katmanında, gerçek ayırmayı temel alarak veritabanınızın depolama alanı için ücretlendirilirsiniz. Depolama, 10 GB 'lik artışlarla otomatik olarak 40 GB ve 100 TB arasında ayrılır. Gerekirse, birden çok veri dosyası aynı anda büyüyebilir. Bir hiper ölçek veritabanı, 10 GB başlangıç boyutuyla oluşturulur ve 40 GB boyutuna ulaşana kadar 10 dakikada bir artmaya başlar.

Hiperscale fiyatlandırması hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Dağıtılmış işlevler mimarisi

Tek bir konum/işlem içindeki tüm veri yönetimi işlevlerinin merkezi olan geleneksel veritabanı altyapılarından farklı olarak (günümüzde üretimde dağıtılmış veritabanlarının tek parçalı veri altyapısının birden çok kopyasına sahip olması durumunda), hiper ölçekli bir veritabanı, çeşitli veri altyapılarının semantiğinin, veriler için uzun süreli depolama ve dayanıklılık sağlayan bileşenlerden, sorgu işleme altyapısını ayırır. Bu şekilde, depolama kapasitesi gerektiği kadar düzgün şekilde ölçeklendirilebilir (başlangıç hedefi 100 TB). Salt okunur çoğaltmalar aynı depolama bileşenlerini paylaşır, böylece yeni bir okunabilir çoğaltmayı çalıştırmak için veri kopyalama gerekmez.

Aşağıdaki diyagramda, bir hiper ölçek veritabanındaki farklı düğüm türleri gösterilmektedir:

![architecture](./media/service-tier-hyperscale/hyperscale-architecture.png)

Hiper ölçekli bir veritabanı aşağıdaki farklı bileşen türlerini içerir:

### <a name="compute"></a>İşlem

İşlem düğümü, ilişkisel altyapının nerede yaşar. Burada dil, sorgu ve işlem işleme meydana gelir. Hiper ölçekli veritabanı olan tüm kullanıcı etkileşimleri, bu işlem düğümleri aracılığıyla gerçekleşir. İşlem düğümlerinde, veri sayfasını getirmek için gereken ağ gidiş dönüşlerin sayısını en aza indirmek için SSD tabanlı önbellekler (önceki diyagramda RBPEX-dayanıklı arabellek havuzu uzantısı olarak etiketlenir) vardır. Tüm okuma/yazma iş yüklerinin ve işlemlerinin işlendiği bir birincil işlem düğümü vardır. Yük devretme amaçlarıyla etkin bekleme düğümleri görevi gören bir veya daha fazla ikincil işlem düğümü vardır ve okuma iş yüklerini boşaltma için salt okunurdur işlem düğümleri görevi görür (Bu işlev isteniyorsa).

Hiper ölçekli işlem düğümlerinde çalışan veritabanı altyapısı, diğer Azure SQL veritabanı hizmet katmanlarıyla aynıdır. Kullanıcılar, hiper ölçek işlem düğümlerinde veritabanı altyapısı ile etkileşdiğinde, desteklenen yüzey alanı ve altyapı davranışı, [bilinen sınırlamalar](#known-limitations)dışında diğer hizmet katmanlarıyla aynıdır.

### <a name="page-server"></a>Sayfa sunucusu

Sayfa sunucuları, ölçeği genişletilmiş bir depolama altyapısını temsil eden sistemlerdir.  Her sayfa sunucusu, veritabanındaki sayfaların bir alt kümesinden sorumludur.  Aday olarak, her sayfa sunucusu en fazla 128 GB veya en fazla 1 TB veri denetler. Birden fazla sayfa sunucusunda (artıklık ve kullanılabilirlik için tutulan sayfa sunucusu çoğaltmalarının dışında) hiç veri paylaşılmıyor. Bir sayfa sunucusunun işi, veritabanı sayfalarını istek üzerine işlem düğümlerine sunmaya ve sayfaların işlem güncellik olarak güncelleştirilmesini sağlamak için kullanılır. Günlük kaydı hizmetinden günlük kayıtları yürütüleyerek sayfa sunucuları güncel tutulur. Sayfa sunucuları, performansı geliştirmek için SSD tabanlı önbellekleri de kapsamasını sağlar. Veri sayfalarının uzun süreli depolaması, ek güvenilirlik sağlamak için Azure depolama 'da tutulur.

### <a name="log-service"></a>Günlük hizmeti

Günlük hizmeti, birincil işlem çoğaltmasındaki günlük kayıtlarını kabul eder, bunları dayanıklı bir önbellekte devam ettirir ve günlük kayıtlarını, verilerin güncelleştirilebilmesi için ilgili sayfa sunucuları ve ilgili sayfa sunucuları gibi işlem çoğaltmalarının geri kalanına iletir. Bu şekilde, birincil işlem çoğaltmasındaki tüm veriler, tüm ikincil işlem çoğaltmaları ve sayfa sunucularına günlük hizmeti aracılığıyla dağıtılır. Son olarak, günlük kayıtları, Azure Storage 'daki uzun süreli depolamaya gönderilir ve bu, neredeyse sonsuz bir depolama deposudur. Bu mekanizma, sık kullanılan günlük kesilmesi gereksinimini ortadan kaldırır. Günlük hizmeti Ayrıca, günlük kayıtlarına erişimi hızlandırmak için yerel belleğe ve SSD önbelleklerine sahiptir.

### <a name="azure-storage"></a>Azure depolama

Azure depolama, bir veritabanındaki tüm veri dosyalarını içerir. Sayfa sunucuları veri dosyalarını Azure depolama 'da güncel tutar. Bu depolama, yedekleme amaçları için ve Azure bölgeleri arasında çoğaltma için kullanılır. Yedeklemeler, veri dosyalarının depolama anlık görüntüleri kullanılarak uygulanır. Anlık görüntüleri kullanarak geri yükleme işlemleri, veri boyutundan bağımsız olarak hızlıdır. Veriler, veritabanının yedekleme saklama süresi içinde herhangi bir noktaya geri yüklenebilir.

## <a name="backup-and-restore"></a>Yedekleme ve geri yükleme

Yedeklemeler dosya anlık görüntüsü tabanlıdır ve bu nedenle neredeyse anında yapılır. Depolama ve işlem ayrımı, birincil işlem çoğaltmasındaki işleme yükünü azaltmak için yedekleme/geri yükleme işleminin depolama katmanına itimmesini sağlar. Sonuç olarak, veritabanı yedeklemesi birincil işlem düğümünün performansını etkilemez. Benzer şekilde, dosya anlık görüntülerine geri dönerek bir zaman noktası kurtarma (sür) yapılır ve bu da veri bir işlem boyutu değildir. Aynı Azure bölgesindeki bir hiper ölçek veritabanının geri yüklenmesi, sabit zamanlı bir işlemdir ve hatta birden fazla terabaytlık veritabanları saat veya gün değil dakikalar içinde geri yüklenebilir. Mevcut bir yedeklemeyi geri yükleyerek yeni veritabanlarının oluşturulması bu özellikten yararlanır: Multi-terabaytlık veritabanları da dahil olmak üzere geliştirme veya test amaçları için veritabanı kopyaları oluşturma, dakikalar içinde dolanabilir.

Hiper ölçekli veritabanlarının coğrafi geri yüklenmesi için bkz. [bir hiper ölçek veritabanını farklı bir bölgeye geri yükleme](#restoring-a-hyperscale-database-to-a-different-region).

## <a name="scale-and-performance-advantages"></a>Ölçek ve performans avantajları

Ek salt okuma işlem düğümlerini hızlı bir şekilde artırma/azaltma özelliği sayesinde, hiper ölçek mimarisi önemli okuma ölçeği özelliklerine izin verir ve ayrıca, daha fazla yazma isteği sunmak için birincil işlem düğümünü de serbest bırakabilirsiniz. Ayrıca, hiper ölçek mimarisinin paylaşılan depolama mimarisi nedeniyle işlem düğümleri hızlı bir şekilde ölçeklenebilir/aşağı ölçeklendirilebilir.

## <a name="create-a-hyperscale-database"></a>Hiper ölçekli veritabanı oluşturma

[Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/create-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)veya [CLI](/cli/azure/sql/db#az_sql_db_create)kullanılarak bir hiper ölçek veritabanı oluşturulabilir. Hiper ölçekli veritabanları yalnızca [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md)kullanılarak kullanılabilir.

Aşağıdaki T-SQL komutu, hiper ölçekli bir veritabanı oluşturur. Bildiriminde hem sürüm hem de hizmet hedefi belirtmeniz gerekir `CREATE DATABASE` . Geçerli hizmet amaçları listesinin [kaynak sınırlarına](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) bakın.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Bu, dört çekirdekli 5. nesil donanımında hiper ölçekli bir veritabanı oluşturur.

## <a name="upgrade-existing-database-to-hyperscale"></a>Mevcut veritabanını hiper ölçeğe yükselt

Azure SQL veritabanı 'ndaki mevcut veritabanlarınızı [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)veya [CLI](/cli/azure/sql/db#az_sql_db_update)kullanarak hiper ölçeğe taşıyabilirsiniz. Bu sırada, tek yönlü geçişdir. Verileri dışa ve içe aktararak farklı bir hizmet katmanına Hyperscale 'den başka bir hizmet katmanına taşıyamazsınız. Kavram provaları (POCs) için üretim veritabanlarınızın bir kopyasını oluşturmanızı ve kopyayı hiper ölçeğe geçirmeyi öneririz. Azure SQL veritabanı 'nda var olan bir veritabanını hiper ölçek katmanına geçirmek veri işleme boyutudur.

Aşağıdaki T-SQL komutu bir veritabanını hiper ölçek hizmeti katmanına taşıdır. Bildiriminde hem sürüm hem de hizmet hedefi belirtmeniz gerekir `ALTER DATABASE` .

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Hiper ölçekli bir veritabanının okuma ölçeğinde bir çoğaltmasını bağlama

Hiper ölçekli veritabanlarında, `ApplicationIntent` istemci tarafından belirtilen bağlantı dizesindeki bağımsız değişken bağlantının yazma çoğaltmasına mı yoksa salt bir ikincil çoğaltmayla mi yönlendirildiğini belirler. `ApplicationIntent`Olarak ayarlanan `READONLY` ve veritabanının ikincil bir çoğaltması yoksa, bağlantı birincil çoğaltmaya yönlendirilir ve varsayılan olarak `ReadWrite` davranış olur.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Hiper ölçek ikincil çoğaltmaları, birincil çoğaltmayla aynı hizmet düzeyi hedefi kullanılarak aynıdır. Birden fazla ikincil çoğaltma varsa, iş yükü tüm kullanılabilir ikincil öğeler arasında dağıtılır. Her ikincil çoğaltma bağımsız olarak güncelleştirilir. Bu nedenle, farklı çoğaltmaların birincil çoğaltmaya göre farklı veri gecikmesi olabilir.

## <a name="database-high-availability-in-hyperscale"></a>Hiper ölçekte veritabanı yüksek kullanılabilirlik

Diğer tüm hizmet katmanlarında olduğu gibi, hiper ölçek, işlem çoğaltma kullanılabilirliğine bakılmaksızın, kaydedilmiş işlemler için veri dayanıklılığını garanti eder. Birincil çoğaltmanın kullanılamamasından kaynaklanan kesinti süresi, yük devretme türüne (planlı vs. plansız) ve en az bir ikincil çoğaltmanın varlığına bağlıdır. Planlanmış bir yük devretmede (bir bakım olayı), sistem, yük devretmeyi başlatmadan önce yeni birincil çoğaltmayı oluşturur ya da yük devretme hedefi olarak var olan bir ikincil çoğaltmayı kullanır. Planlanmamış bir yük devretmede (örneğin, birincil çoğaltmada bir donanım arızası), sistem varsa bir yük devretme hedefi olarak ikincil bir çoğaltma kullanır veya kullanılabilir işlem kapasitesi havuzundan yeni bir birincil çoğaltma oluşturur. İkinci durumda, yeni birincil çoğaltmayı oluşturmak için gereken ek adımlar nedeniyle kesinti süresi daha uzun olur.

Hiperscale SLA için bkz. [Azure SQL veritabanı Için SLA](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Hiper ölçekli veritabanları için olağanüstü durum kurtarma

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Hiper ölçek veritabanını farklı bir bölgeye geri yükleme

Azure SQL veritabanındaki bir hiper ölçek veritabanını, bir olağanüstü durum kurtarma işleminin veya detaya gitme, yeniden konumlandırma ya da başka bir nedenden dolayı üzerinde barındırdığı bir bölgeye geri yüklemeniz gerekiyorsa, birincil yöntem, veritabanının coğrafi olarak geri yüklenmesini sağlar. Bu, SQL veritabanı 'ndaki diğer veritabanlarını farklı bir bölgeye geri yüklemek için kullandığınız adımlarla tam olarak aynı adımları içerir:

1. Henüz uygun bir sunucunuz yoksa hedef bölgede bir [sunucu](logical-servers.md) oluşturun.  Bu sunucunun özgün (kaynak) sunucuyla aynı aboneliğe sahip olması gerekir.
2. Azure SQL veritabanı 'ndaki bir veritabanını otomatik yedeklemelerden geri yükleme sayfasındaki [coğrafi geri yükleme](./recovery-using-backups.md#geo-restore) konusundaki yönergeleri izleyin.

> [!NOTE]
> Kaynak ve hedef ayrı bölgelerde olduğundan, veritabanı, veritabanı boyutundan bağımsız olarak hemen tamamlanmış olan coğrafi olmayan geri yüklemeler gibi kaynak veritabanıyla anlık görüntü depolamayı paylaşamaz. Hiper ölçekli bir veritabanının coğrafi geri yüklemesi söz konusu olduğunda, hedef coğrafi olarak çoğaltılan depolamanın eşleştirilmiş bölgesinde olsa bile, veri boyutu bir işlem olur. Bu nedenle, coğrafi geri yükleme, geri yüklenen veritabanının boyutuyla orantılı olarak zaman alır. Hedef, eşleştirilmiş bölgedeyse, veri aktarımı bir bölge içinde olacaktır, bu da çapraz bölge veri aktarımından önemli ölçüde daha hızlıdır, ancak veri boyutu bir işlem olmaya devam edecektir.

## <a name="available-regions"></a><a name=regions></a>Kullanılabilir bölgeler

Azure SQL veritabanı hiper ölçek katmanı, tüm bölgelerde kullanılabilir ancak aşağıda listelenen aşağıdaki bölgelerde varsayılan olarak etkindir. Hiperscale 'nin varsayılan olarak etkinleştirilmediği bir bölgede hiper ölçekli veritabanı oluşturmak istiyorsanız, Azure portal aracılığıyla bir ekleme isteği gönderebilirsiniz. Yönergeler için bkz. [Azure SQL veritabanı Için istek kotası artışlarına](quota-increase-request.md) bakın. İsteğiniz gönderilirken aşağıdaki yönergeleri kullanın:

- [Bölge erişimi](quota-increase-request.md#region) SQL veritabanı kota türünü kullanın.
- Açıklamada, okunabilir çoğaltmalar dahil işlem SKU 'SU/toplam çekirdekleri ekleyin ve hiper ölçek kapasitesi istediğinizi belirtin.
- Ayrıca, TB olarak zaman içinde tüm veritabanlarının toplam boyutunun projeksiyonu de belirtin.

Etkin bölgeler:
- Doğu Avustralya
- Güneydoğu Avustralya
- Orta Avustralya
- Güney Brezilya
- Orta Kanada
- Doğu Kanada
- Central US
- Çin Doğu 2
- Çin Kuzey 2
- Doğu Asya
- Doğu ABD
- Doğu ABD 2
- Orta Fransa
- Almanya Orta Batı
- Doğu Japonya
- Batı Japonya
- Güney Kore - Orta
- Güney Kore - Güney
- Orta Kuzey ABD
- Kuzey Avrupa
- Norveç Doğu
- Norveç Batı
- Güney Afrika - Kuzey
- Orta Güney ABD
- Güneydoğu Asya
- İsviçre Batı
- Güney Birleşik Krallık
- Batı Birleşik Krallık
- Orta US DoD
- Doğu US DoD
- ABD devleti Arizona
- ABD devleti Texas
- Orta Batı ABD
- West Europe
- Batı ABD
- Batı ABD 2

## <a name="known-limitations"></a>Bilinen sınırlamalar

Bunlar, GA ile aynı hiper ölçek hizmet katmanına yönelik geçerli sınırlamalardır.  Mümkün olduğunca bu sınırlamaların çoğunu kaldırmak için etkin bir şekilde çalışıyoruz.

| Sorun | Description |
| :---- | :--------- |
| Bir sunucunun Yedeklemeleri Yönet bölmesi, hiper ölçekli veritabanlarını göstermez. Bunlar görünümden filtrelenecektir.  | Hiper ölçek, yedeklemeleri yönetmek için ayrı bir yönteme sahiptir, bu nedenle Long-Term bekletme ve zaman içinde yedekleme bekletme ayarları uygulanmaz. Buna uygun olarak, hiper ölçekli veritabanları yedekleme bölmesini yönetme bölmesinde görünmez.<br><br>Diğer Azure SQL veritabanı hizmet katmanlarından hiper ölçeğe geçirilmiş veritabanları için, geçiş öncesi yedeklemeler kaynak veritabanının [yedekleme bekletme](automated-backups-overview.md#backup-retention) süresi boyunca tutulur. Bu yedeklemeler, kaynak veritabanını geçişten önceki bir zaman noktasına [geri yüklemek](recovery-using-backups.md#programmatic-recovery-using-automated-backups) için kullanılabilir.|
| Belirli bir noktaya geri yükleme | Hiper olmayan bir veritabanı hiper ölçekli bir veritabanı olarak geri yüklenemez ve hiper ölçek veritabanı hiper olmayan bir veritabanı olarak geri yüklenemez. Hizmet katmanını değiştirerek hiper ölçeğe geçirilmiş hiper olmayan bir veritabanı için, geçiş işleminden önce ve veritabanının yedekleme saklama süresi içinde bir zaman noktasına geri [yükleyin.](recovery-using-backups.md#programmatic-recovery-using-automated-backups) Geri yüklenen veritabanı hiper olmayan ölçek olacaktır. |
| Azure SQL veritabanı hizmet katmanını Hyperscale olarak değiştirirken, veritabanında 1 TB 'den büyük bir veri dosyası varsa işlem başarısız olur | Bazı durumlarda, hizmet katmanını Hyperscale olarak değiştirmeye çalışmadan önce büyük dosyaları 1 TB 'tan küçük olacak şekilde [küçülterek](file-space-manage.md#shrinking-data-files) Bu soruna geçici bir çözüm uygulanabilir. Veritabanı dosyalarının geçerli boyutunu öğrenmek için aşağıdaki sorguyu kullanın. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL Yönetilen Örnek | Azure SQL yönetilen örneği şu anda hiper ölçekli veritabanları ile desteklenmiyor. |
| Esnek Havuzlar |  Esnek havuzlar Şu anda Hyperscale ile desteklenmemektedir.|
| Hyperscale 'e geçiş Şu anda tek yönlü bir işlemdir | Bir veritabanı hiper ölçeğe geçirildiğinde doğrudan Hyperscale olmayan bir hizmet katmanına geçirilemez. Mevcut olduğunda, bir veritabanını Hiperscale 'den hiper olmayan ölçeğe geçirmenin tek yolu bacpac dosyasını veya diğer veri taşıma teknolojilerini (toplu kopyalama, Azure Data Factory, Azure Databricks, SSIS, vb.) kullanarak dışarı ve içeri aktarmaya yönelik bir yoldur. [Yeni-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) veya [New-Azsqldatabaseımport](/powershell/module/az.sql/new-azsqldatabaseimport)kullanılarak PowerShell 'den, [az SQL DB Export](/cli/azure/sql/db#az_sql_db_export) ve [az SQL DB import](/cli/azure/sql/db#az_sql_db_import)kullanarak Azure CLI 'dan bacpac dışarı [REST API](/rest/api/sql/) ve içeri aktarma Azure Portal. Daha küçük hiper ölçekli veritabanları için bacpac içeri/dışarı aktarma (200 GB 'a kadar) SSMS ve [SqlPackage](/sql/tools/sqlpackage) sürüm 18,4 ve üzeri kullanılarak desteklenir. Daha büyük veritabanları için bacpac dışarı aktarma/içeri aktarma uzun sürebilir ve çeşitli nedenlerle başarısız olabilir.|
| In-Memory OLTP nesneleriyle veritabanlarının geçirilmesi | Hiper ölçek, bellek için iyileştirilmiş tablo türleri, tablo değişkenleri ve yerel koda derlenmiş modüller dahil In-Memory OLTP nesnelerinin bir alt kümesini destekler. Ancak, geçirilmekte olan veritabanında herhangi bir In-Memory OLTP nesneleri varsa, Premium ve İş Açısından Kritik hizmet katmanlarından hiper ölçeğe geçiş desteklenmez. Bu tür bir veritabanını hiper ölçeğe geçirmek için, tüm In-Memory OLTP nesnelerinin ve bağımlılıklarının bırakılması gerekir. Veritabanı geçirildikten sonra, bu nesneler yeniden oluşturulabilir. Dayanıklı ve dayanıklı olmayan bellek için iyileştirilmiş tablolar, şu anda hiper ölçekte desteklenmez ve disk tablolarına değiştirilmelidir.|
| Coğrafi Çoğaltma  | Azure SQL veritabanı hiper ölçek için Coğrafi çoğaltmayı henüz yapılandıramazsınız. |
| Veritabanı kopyalama | Hiperscale üzerinde veritabanı kopyalama işlemi artık genel önizlemeye sunuldu. |
| Akıllı veritabanı özellikleri | "Plan zorla" seçeneğinin dışında, diğer tüm otomatik ayarlama seçenekleri henüz hiper ölçekte desteklenmez: seçenekler etkin gibi görünebilir, ancak hiçbir öneri veya eylem yapılmaz. |
| Sorgu Performansı İçgörüleri | Sorgu performansı öngörüleri Şu anda hiper ölçekli veritabanları için desteklenmiyor. |
| Veritabanını Küçült | DBCC SHRINKDATABASE veya DBCC SHRINKFILE Şu anda hiper ölçekli veritabanları için desteklenmiyor. |
| Veritabanı bütünlük denetimi | DBCC CHECKDB Şu anda hiper ölçekli veritabanları için desteklenmiyor. DBCC CHECKFıLEGROUP ve DBCC CHECKTABLE, geçici çözüm olarak kullanılabilir. Azure SQL veritabanı 'nda veri bütünlüğü yönetimiyle ilgili ayrıntılı bilgi için bkz. [Azure SQL veritabanı 'Nda veri bütünlüğü](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) . |

## <a name="next-steps"></a>Sonraki adımlar

- Hyperscale hakkında bir SSS için bkz. [hyperscale hakkında sık sorulan sorular](service-tier-hyperscale-frequently-asked-questions-faq.md).
- Hizmet katmanları hakkında daha fazla bilgi için bkz. [hizmet katmanları](purchasing-models.md)
- Sunucu ve abonelik düzeylerindeki sınırlar hakkında bilgi için bkz. [bir sunucudaki kaynak sınırlarına genel bakış](resource-limits-logical-server.md) .
- Tek bir veritabanı için model sınırları satın almak için, [tek bir veritabanı için bkz. Azure SQL veritabanı sanal çekirdek tabanlı satın alma modeli sınırları](resource-limits-vcore-single-databases.md).
- Özellikler ve karşılaştırma listesi için bkz. [SQL ortak özellikleri](features-comparison.md).
