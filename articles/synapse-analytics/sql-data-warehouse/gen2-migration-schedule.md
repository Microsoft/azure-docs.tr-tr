---
title: Adanmış SQL havuzunuzu (eski adıyla SQL DW) Gen2 'e geçirin
description: Var olan adanmış bir SQL havuzunu (eski adıyla SQL DW) Gen2 ve bölgeye göre geçiş zamanlamalarına geçirmeye yönelik yönergeler.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0ce07ff3ca5fbcc9776792129d3bfb4ef54efe7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120130"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>Adanmış SQL havuzunuzu (eski adıyla SQL DW) Gen2 'e yükseltin

Microsoft, adanmış bir SQL Havuzu (eski adıyla SQL DW) çalıştırmanın giriş düzeyi maliyetini azaltmaya yardımcı oluyor.  Yoğun sorguları işleyebilen daha düşük işlem katmanları artık adanmış SQL Havuzu (eski adıyla SQL DW) için kullanılabilir. Gen2 için eksiksiz duyuru [daha düşük işlem katmanı desteğini](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)okuyun. Yeni teklif, aşağıdaki tabloda belirtilen bölgelerde kullanılabilir. Desteklenen bölgeler için, var olan Gen1 adanmış SQL Havuzu (eski adıyla SQL DW) şu iki şekilde Gen2 'e yükseltilebilir:

- **Otomatik yükseltme işlemi:** Hizmet bir bölgede kullanılabilir duruma geldiğinde otomatik yükseltmeler başlatılmaz.  Belirli bir bölgede otomatik yükseltmeler başlatıldığında, seçtiğiniz bakım zamanlamasında tek tek veri ambarı yükseltmeleri gerçekleşmeyecektir.
- [**Gen2 'e kendi kendine yükseltme:**](#self-upgrade-to-gen2) Gen2 'e kendi kendine yükseltme yaparak ne zaman yükselteceğiniz kontrol edebilirsiniz. Bölgeniz henüz desteklenmiyorsa, bir geri yükleme noktasından doğrudan desteklenen bir bölgedeki bir Gen2 örneğine geri yükleyebilirsiniz.

## <a name="automated-schedule-and-region-availability-table"></a>Otomatik zamanlama ve bölge kullanılabilirlik tablosu

Aşağıdaki tabloda, alt Gen2 işlem katmanı kullanılabilir olduğunda ve otomatik yükseltmeler başlatıldığında bölge tarafından özetlenmektedir. Tarihler değişebilir. Bölgenizin ne zaman kullanılabilir hale geldiğini görmek için yeniden denetleyin.

\* bölge için belirli bir zamanlamanın Şu anda kullanılamadığını belirtir.

| **Bölge** | **Düşük Gen2 kullanılabilir** | **Otomatik yükseltmeler başlar** |
|:--- |:--- |:--- |
| Doğu Kanada |1 Haziran 2020 |1 Temmuz 2020 |
| Doğu Çin |\* |\* |
| Kuzey Çin |\* |\* |
| Orta Almanya |\* |\* |
| Almanya Orta Batı |Kullanılabilir |1 Mayıs 2020 |
| Hindistan Batı |Kullanılabilir |1 Mayıs 2020  |

## <a name="automatic-upgrade-process"></a>Otomatik yükseltme işlemi

Yukarıdaki kullanılabilirlik grafiğine bağlı olarak, Gen1 örneklerinizin otomatik yükseltmelerini planlıyoruz. Adanmış SQL havuzunun (eski adıyla SQL DW) kullanılabilirliğine yönelik herhangi bir beklenmedik kesintiden kaçınmak için, bakım zamanlamanız sırasında otomatik yükseltmeler planlanacaktır. Yeni bir Gen1 örneği oluşturma özelliği, Gen2 'ye otomatik olarak yükseltme yapılmakta olan bölgelerde devre dışı bırakılacak. Otomatik yükseltmeler tamamlandıktan sonra Gen1 kullanımdan kalkacaktır. Zamanlamalar hakkında daha fazla bilgi için bkz. [bakım zamanlaması görüntüleme](maintenance-scheduling.md#view-a-maintenance-schedule)

Özel SQL havuzunuzu (eski adıyla SQL DW) yeniden başlatdığımızda yükseltme işlemi, bağlantı (yaklaşık 5 dakika) ile kısa bir yer içerir.  Adanmış SQL havuzunuz (eski adıyla SQL DW) yeniden başlatıldıktan sonra, kullanım için tam olarak kullanılabilir olacaktır. Ancak, yükseltme işlemi arka planda veri dosyalarını yükseltmeye devam ederken performans düşüklüğüne karşılaşabilirsiniz. Performans düşüşünün toplam süresi veri dosyalarınızın boyutuna bağlı olarak değişiklik gösterir.

Ayrıca, yeniden başlatmanın ardından daha büyük bir SLO ve kaynak sınıfı kullanarak tüm birincil columnstore tablolarında [alter INDEX REBUILD](sql-data-warehouse-tables-index.md) ' i çalıştırarak veri dosyası yükseltme işlemini de hızlandırabilir.

> [!NOTE]
> Alter INDEX REBUILD çevrimdışı bir işlemdir ve tablolar yeniden oluşturma tamamlanana kadar kullanılamaz.

## <a name="self-upgrade-to-gen2"></a>Gen2 'e kendi kendine yükseltme

Mevcut bir Gen1 adanmış SQL havuzunda (eski adıyla SQL DW) bu adımları izleyerek kendi kendine yükseltmeyi seçebilirsiniz. Kendi kendine yükseltmeyi seçerseniz, bölgenizde otomatik yükseltme işlemi başlamadan önce bunu tamamlamalısınız. Bunun yapılması, bir çakışmaya neden olan otomatik yükseltmelerin riskini önlemenize olanak sağlar.

Kendi kendine yükseltme yaparken iki seçenek vardır.  Geçerli adanmış SQL havuzunuzu (eski adıyla SQL DW) yerinde yükseltebilir veya Gen1 adanmış bir SQL havuzunu (eski adıyla SQL DW) bir Gen2 örneğine geri yükleyebilirsiniz.

- [Yerinde yükseltme](upgrade-to-latest-generation.md) -Bu seçenek, var olan Gen1 adanmış SQL havuzdan (eskı ADıYLA SQL DW) Gen2 'e yükseltilir. Özel SQL havuzunuzu (eski adıyla SQL DW) yeniden başlatdığımızda yükseltme işlemi, bağlantı (yaklaşık 5 dakika) ile kısa bir yer içerir.  Yeniden başlatıldıktan sonra, tam kullanıma açık olacaktır. Yükseltme sırasında sorunlarla karşılaşırsanız, olası neden olarak bir [destek isteği](sql-data-warehouse-get-started-create-support-ticket.md) ve "Gen2 Upgrade" başvurusu açın.
- [Geri yükleme noktasından Yükselt](sql-data-warehouse-restore-points.md) -geçerli Gen1 adanmış SQL havuzunuza (eskı ADıYLA SQL DW) Kullanıcı tanımlı bir geri yükleme noktası oluşturun ve ardından doğrudan bir Gen2 örneğine geri yükleyin. Mevcut Gen1 adanmış SQL Havuzu (eski adıyla SQL DW) yerinde kalır. Geri yükleme tamamlandıktan sonra, Gen2 adanmış SQL havuzunuz (eski adıyla SQL DW) tam kullanıma açık olacaktır.  Geri yüklenen Gen2 örneğinde tüm test ve doğrulama süreçlerini çalıştırdıktan sonra, özgün Gen1 örneği silinebilir.

  - 1. Adım: Azure portal, [Kullanıcı tanımlı bir geri yükleme noktası oluşturun](sql-data-warehouse-restore-active-paused-dw.md).
  - 2. Adım: Kullanıcı tanımlı bir geri yükleme noktasından geri yükleme yaparken, "performans düzeyi" ni tercih ettiğiniz Gen2 katmanına ayarlayın.

Yükseltme işlemi arka planda veri dosyalarını yükseltmeye devam ederken bir süre için performansta düşüş yaşayabilirsiniz. Performans düşüşünün toplam süresi veri dosyalarınızın boyutuna bağlı olarak değişiklik gösterir.

Arka plan veri geçiş sürecini hızlandırmak için, daha büyük bir SLO ve kaynak sınıfında sorguladığınız tüm birincil columnstore tablolarında [alter INDEX REBUILD](sql-data-warehouse-tables-index.md) ' i çalıştırarak veri hareketini hemen zorlayabilirsiniz.

> [!NOTE]
> Alter INDEX REBUILD çevrimdışı bir işlemdir ve tablolar yeniden oluşturma tamamlanana kadar kullanılamaz.

Adanmış SQL havuzunuz (eski adıyla SQL DW) ile ilgili herhangi bir sorunla karşılaşırsanız, olası neden olarak bir [destek isteği](sql-data-warehouse-get-started-create-support-ticket.md) oluşturun ve "Gen2 Upgrade" başvurusu yapın.

Daha fazla bilgi için bkz. [Gen2 sürümüne yükseltme](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Geçiş hakkında sık sorulan sorular

**S: Gen2 maliyeti Gen1 ile aynı mı?**

- C: Evet.

**S: yükseltmeler Otomasyon betiklerimi nasıl etkiler?**

- Y: bir hizmet düzeyi hedefine başvuran tüm otomasyon betikleri, Gen2 eşdeğerine karşılık olacak şekilde değiştirilmelidir.  [Ayrıntılara bakın](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal).

**S: kendi kendini yükseltme ne kadar sürer?**

- Y: bir geri yükleme noktasından yerinde yükseltme yapabilir veya yükseltebilirsiniz.

  - Yerinde yükseltme, adanmış SQL havuzunuzun (eski adıyla SQL DW) geçici olarak duraklamasını ve sürdürülmesine neden olur.  Adanmış SQL Havuzu (eski adıyla SQL DW) çevrimiçi olduğu için bir arka plan işlemi devam edecektir.  
  - Bir geri yükleme noktası üzerinden yükseltiyorsanız, yükseltme tam geri yükleme işlemini yapacağı için daha uzun sürer.

**S: otomatik yükseltme ne kadar sürer?**

- Y: yükseltmenin gerçek kapalı kalma süresi yalnızca hizmeti duraklatma ve devam etmek için gereken süredir ve 5 ila 10 dakika arasındadır. Kısa bir kapalı kalma süresinin ardından arka plan işlemi depolama geçişini çalıştırır. Arka plan işlemi için zaman uzunluğu, adanmış SQL havuzunuzun boyutuna bağlıdır (eski adıyla SQL DW).

**S: Bu otomatik yükseltme gerçekleşsin mi?**

- A: bakım zamanlamanız sırasında. Seçtiğiniz bakım zamanlamalarınızın yararlanmak, işletmenizin kesintiye uğramasını en aza indirir.

**S: arka plan yükseltme sürecim takılı görünüyorsa ne yapmam gerekir?**

- Y: columnstore tablolarınızın bir yeniden kümesini başlatın. Bu işlem sırasında tablonun yeniden dizin oluşturma işleminin çevrimdışı olacağını unutmayın.

**S: Gen1 üzerinde Gen2 hizmet düzeyi hedefi yoksa ne olursa?**

- Y: Gen1 üzerinde DW600 veya DW1200 çalıştırıyorsanız, DW1000c daha fazla bellek, kaynak ve daha yüksek performans olduğundan Gen2 sırasıyla DW500c veya Gen1 kullanılması önerilir.

**S: coğrafi yedeklemeyi devre dışı bırakabilir miyim?**

- C: Hayır. Coğrafi yedekleme, bir bölge kullanılamaz duruma geldiğinde adanmış SQL havuzunuzu (eski adıyla SQL DW) korumak için bir kurumsal özelliktir. Daha fazla endişeniz varsa bir [destek isteği](sql-data-warehouse-get-started-create-support-ticket.md) açın.

**S: T-SQL sözdiziminde gen1 ve Gen2 arasında bir fark var mı?**

- Y: T-SQL dil sözdiziminde Gen1 ile Gen2 arasında bir değişiklik yoktur.

**S: Gen2, bakım pencerelerini destekliyor mu?**

- C: Evet.

**S: bölgem yükseltildikten sonra yeni bir Gen1 örneği oluşturabileceksiniz mi?**

- C: Hayır. Bir bölge yükseltildikten sonra, yeni Gen1 örneklerinin oluşturulması devre dışı bırakılır.

## <a name="next-steps"></a>Sonraki adımlar

- [Yükseltme adımları](upgrade-to-latest-generation.md)
- [Bakım pencereleri](maintenance-scheduling.md)
- [Kaynak sistem durumu İzleyicisi](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Geçişe başlamadan önce gözden geçirin](upgrade-to-latest-generation.md#before-you-begin)
- [Bir geri yükleme noktasından yerinde yükseltme ve yükseltme](upgrade-to-latest-generation.md)
- [Kullanıcı tanımlı geri yükleme noktası oluşturma](sql-data-warehouse-restore-points.md)
- [Gen2 'e geri yüklemeyi öğrenin](sql-data-warehouse-restore-active-paused-dw.md)
- [Bir Azure SYNAPSE Analytics destek isteği açın](./sql-data-warehouse-get-started-create-support-ticket.md)