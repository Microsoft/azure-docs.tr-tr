---
title: Veri Ambarınızı Gen2 'e geçirin
description: Var olan bir veri ambarını bölgeye göre Gen2 ve geçiş zamanlaması 'na geçirmeye yönelik yönergeler.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.custom: seo-lt-2019
ms.openlocfilehash: 888f50d645c9b3babf95335e434db65423108ccb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693026"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Veri Ambarınızı Gen2 'ye yükseltme

Microsoft, veri ambarı çalıştırmanın giriş düzeyi maliyetinin altına inmesine yardımcı oluyor.  Talep edilen sorguları işleyebilen daha düşük işlem katmanları artık Azure SQL veri ambarı için kullanılabilir. Gen2 için eksiksiz duyuru [daha düşük işlem katmanı desteğini](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)okuyun. Yeni teklif, aşağıdaki tabloda belirtilen bölgelerde kullanılabilir. Desteklenen bölgeler için, mevcut Gen1 veri ambarları şu iki şekilde Gen2 'e yükseltilebilir:

- **Otomatik yükseltme işlemi:** Hizmet bir bölgede kullanılabilir duruma geldiğinde otomatik yükseltmeler başlatılmaz.  Belirli bir bölgede otomatik yükseltmeler başlatıldığında, seçtiğiniz bakım zamanlamalarınız sırasında tek tek DW yükseltmeleri gerçekleşmeyecektir.
- [**Gen2 'e kendi kendine yükseltme:** ](#self-upgrade-to-gen2) Gen2 'e kendi kendine yükseltme yaparak ne zaman yükselteceğiniz kontrol edebilirsiniz. Bölgeniz henüz desteklenmiyorsa, bir geri yükleme noktasından doğrudan desteklenen bir bölgedeki bir Gen2 örneğine geri yükleyebilirsiniz.

## <a name="automated-schedule-and-region-availability-table"></a>Otomatik zamanlama ve bölge kullanılabilirlik tablosu

Aşağıdaki tabloda, alt Gen2 işlem katmanı kullanılabilir olduğunda ve otomatik yükseltmeler başlatıldığında bölge tarafından özetlenmektedir. Tarihler değişebilir. Bölgenizin ne zaman kullanılabilir hale geldiğini görmek için yeniden denetleyin.

\* Şu anda bölge için belirli bir zamanlamanın kullanılamadığını gösterir.

| **Bölge** | **Düşük Gen2 kullanılabilir** | **Otomatik yükseltmeler başlar** |
|:--- |:--- |:--- |
| Avustralya Doğu |Kullanılabilir |Tamamlama |
| Avustralya Güneydoğu |Kullanılabilir |Tamamlama |
| Güney Brezilya |Kullanılabilir |Tamamlama |
| Orta Kanada |Kullanılabilir |Tamamlama |
| Doğu Kanada |1 Haziran 2020 |1 Temmuz 2020 |
| Orta ABD |Kullanılabilir |Tamamlama |
| Çin Doğu |\* |\* |
| Çin Doğu 2 |Kullanılabilir |Tamamlama |
| Çin Kuzey |\* |\* |
| Çin Kuzey 2 |Kullanılabilir |Tamamlama |
| Doğu Asya |Kullanılabilir |Tamamlama |
| Doğu ABD |Kullanılabilir |Tamamlama |
| Doğu ABD 2 |Kullanılabilir |Tamamlama |
| Fransa Orta |Kullanılabilir |Devam eden |
| Almanya Orta |\* |\* |
| Almanya Orta Batı |1 Eylül 2019|1 Ekim 2019 |
| Hindistan Orta |Kullanılabilir |Tamamlama |
| Hindistan Güney |Kullanılabilir |Tamamlama |
| Hindistan Batı |1 Temmuz 2019 |Devam eden |
| Japonya Doğu |Kullanılabilir |Tamamlama |
| Japonya Batı |Kullanılabilir |Tamamlama |
| Kore Orta |Kullanılabilir |Tamamlama |
| Kore Güney |Kullanılabilir |Tamamlama |
| Orta Kuzey ABD |Kullanılabilir |Tamamlama |
| Kuzey Avrupa |Kullanılabilir |Tamamlama |
| Güney Afrika Kuzey |12 Temmuz 2019 |Tamamlama |
| Orta Güney ABD |Kullanılabilir |Tamamlama |
| Güneydoğu Asya |Kullanılabilir |Tamamlama |
| BAE Kuzey |20 Temmuz 2019 |Tamamlama |
| Birleşik Krallık Güney |Kullanılabilir |Devam eden |
| Birleşik Krallık Batı |Kullanılabilir |Devam eden |
| Batı Orta ABD |1 Kasım 2019 |1 Aralık 2019|
| Batı Avrupa |Kullanılabilir |Tamamlama |
| Batı ABD |Kullanılabilir |Tamamlama |
| Batı ABD 2 |Kullanılabilir |Tamamlama |

## <a name="automatic-upgrade-process"></a>Otomatik yükseltme işlemi

Yukarıdaki kullanılabilirlik grafiğine bağlı olarak, Gen1 örneklerinizin otomatik yükseltmelerini planlıyoruz. Veri ambarının kullanılabilirliğine ilişkin beklenmeyen kesintilere engel olmak için, otomatik yükseltmeler bakım zamanlamanız sırasında zamanlanır. Yeni bir Gen1 örneği oluşturma özelliği, Gen2 'ye otomatik olarak yükseltme yapılmakta olan bölgelerde devre dışı bırakılacak. Otomatik yükseltmeler tamamlandıktan sonra Gen1 kullanımdan kalkacaktır. Zamanlamalar hakkında daha fazla bilgi için bkz. [bakım zamanlaması görüntüleme](viewing-maintenance-schedule.md)

Yükseltme işlemi, veri Ambarınızı yeniden başlatdığımızda bağlantı (yaklaşık 5 dakika) ile kısa bir yer içerir.  Veri ambarınız yeniden başlatıldıktan sonra, tam kullanıma açık olacaktır. Ancak, yükseltme işlemi arka planda veri dosyalarını yükseltmeye devam ederken performans düşüklüğüne karşılaşabilirsiniz. Performans düşüşünün toplam süresi veri dosyalarınızın boyutuna bağlı olarak değişiklik gösterir.

Ayrıca, yeniden başlatmanın ardından daha büyük bir SLO ve kaynak sınıfı kullanarak tüm birincil columnstore tablolarında [alter INDEX REBUILD](sql-data-warehouse-tables-index.md) ' i çalıştırarak veri dosyası yükseltme işlemini de hızlandırabilir.

> [!NOTE]
> Alter INDEX REBUILD çevrimdışı bir işlemdir ve tablolar yeniden oluşturma tamamlanana kadar kullanılamaz.

## <a name="self-upgrade-to-gen2"></a>Gen2 'e kendi kendine yükseltme

Mevcut bir Gen1 veri ambarında bu adımları izleyerek kendi kendine yükseltmeyi seçebilirsiniz. Kendi kendine yükseltmeyi seçerseniz, bölgenizde otomatik yükseltme işlemi başlamadan önce bunu tamamlamalısınız. Bunun yapılması, bir çakışmaya neden olan otomatik yükseltmelerin riskini önlemenize olanak sağlar.

Kendi kendine yükseltme yaparken iki seçenek vardır.  Geçerli veri Ambarınızı yerinde yükseltebilir ya da bir Gen1 veri ambarını bir Gen2 örneğine geri yükleyebilirsiniz.

- [Yerinde yükseltme](upgrade-to-latest-generation.md) -Bu seçenek, var olan Gen1 veri Ambarınızı Gen2 'e yükseltir. Yükseltme işlemi, veri Ambarınızı yeniden başlatdığımızda bağlantı (yaklaşık 5 dakika) ile kısa bir yer içerir.  Veri ambarınız yeniden başlatıldıktan sonra, tam kullanıma açık olacaktır. Yükseltme sırasında sorunlarla karşılaşırsanız, olası neden olarak bir [destek isteği](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) ve "Gen2 Upgrade" başvurusu açın.
- [Geri yükleme noktasından Yükselt](sql-data-warehouse-restore.md) -geçerli Gen1 veri Ambarınızda Kullanıcı tanımlı bir geri yükleme noktası oluşturun ve ardından doğrudan bir Gen2 örneğine geri yükleyin. Mevcut Gen1 veri ambarı yerinde kalır. Geri yükleme tamamlandıktan sonra, Gen2 veri ambarınızın kullanımı tamamen kullanılabilir olacaktır.  Geri yüklenen Gen2 örneğinde tüm test ve doğrulama süreçlerini çalıştırdıktan sonra, özgün Gen1 örneği silinebilir.

   - 1\. Adım: Azure portal, [Kullanıcı tanımlı bir geri yükleme noktası oluşturun](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal).
   - 2\. Adım: Kullanıcı tanımlı bir geri yükleme noktasından geri yükleme yaparken, "performans düzeyi" ni tercih ettiğiniz Gen2 katmanına ayarlayın.

Yükseltme işlemi arka planda veri dosyalarını yükseltmeye devam ederken bir süre için performansta düşüş yaşayabilirsiniz. Performans düşüşünün toplam süresi veri dosyalarınızın boyutuna bağlı olarak değişiklik gösterir.

Arka plan veri geçiş sürecini hızlandırmak için, daha büyük bir SLO ve kaynak sınıfında sorguladığınız tüm birincil columnstore tablolarında [alter INDEX REBUILD](sql-data-warehouse-tables-index.md) ' i çalıştırarak veri hareketini hemen zorlayabilirsiniz.

> [!NOTE]
> Alter INDEX REBUILD çevrimdışı bir işlemdir ve tablolar yeniden oluşturma tamamlanana kadar kullanılamaz.

Veri Ambarınızla ilgili herhangi bir sorunla karşılaşırsanız, olası neden olarak bir [destek isteği](sql-data-warehouse-get-started-create-support-ticket.md) oluşturun ve "Gen2 Upgrade" başvurusu yapın.

Daha fazla bilgi için bkz. [Gen2 sürümüne yükseltme](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Geçiş hakkında sık sorulan sorular

**S: Gen2 maliyeti Gen1 ile aynı mı?**

- C: Evet.

**S: yükseltmeler Otomasyon betiklerimi nasıl etkiler?**

- Y: bir hizmet düzeyi hedefine başvuran tüm otomasyon betikleri, Gen2 eşdeğerine karşılık olacak şekilde değiştirilmelidir.  [Ayrıntılara bakın](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**S: kendi kendini yükseltme ne kadar sürer?**

- Y: bir geri yükleme noktasından yerinde yükseltme yapabilir veya yükseltebilirsiniz.  
   - Yerinde yükseltme, veri ambarınızın geçici olarak duraklamasına ve sürdürülmesine neden olur.  Veri ambarı çevrimiçi olduğunda bir arka plan işlemi devam edecektir.  
   - Bir geri yükleme noktası üzerinden yükseltiyorsanız, yükseltme tam geri yükleme işlemini yapacağı için daha uzun sürer.

**S: otomatik yükseltme ne kadar sürer?**

- Y: yükseltmenin gerçek kapalı kalma süresi yalnızca hizmeti duraklatma ve devam etmek için gereken süredir ve 5 ila 10 dakika arasındadır. Kısa bir kapalı kalma süresinin ardından arka plan işlemi depolama geçişini çalıştırır. Arka plan işleminin süresi veri ambarınızın boyutuna bağlıdır.

**S: Bu otomatik yükseltme gerçekleşsin mi?**

- A: bakım zamanlamanız sırasında. Seçtiğiniz bakım zamanlamalarınızın yararlanmak, işletmenizin kesintiye uğramasını en aza indirir.

**S: arka plan yükseltme sürecim takılı görünüyorsa ne yapmam gerekir?**

 - Y: columnstore tablolarınızın bir yeniden kümesini başlatın. Bu işlem sırasında tablonun yeniden dizin oluşturma işleminin çevrimdışı olacağını unutmayın.

**S: Gen1 üzerinde Gen2 hizmet düzeyi hedefi yoksa ne olursa?**
- Y: Gen1 üzerinde DW600 veya DW1200 çalıştırıyorsanız, DW1000c daha fazla bellek, kaynak ve daha yüksek performans olduğundan Gen2 sırasıyla DW500c veya Gen1 kullanılması önerilir.

**S: coğrafi yedeklemeyi devre dışı bırakabilir miyim?**
- Y: Hayır. Coğrafi yedekleme, bir bölgenin kullanılamaz duruma gelmesi durumunda veri ambarı kullanılabilirliğinin korunmasının bir kurumsal özelliğidir. Daha fazla endişeniz varsa bir [destek isteği](sql-data-warehouse-get-started-create-support-ticket.md) açın.

**S: T-SQL sözdiziminde gen1 ve Gen2 arasında bir fark var mı?**

- Y: T-SQL dil sözdiziminde Gen1 ile Gen2 arasında bir değişiklik yoktur.

**S: Gen2, bakım pencerelerini destekliyor mu?**

- C: Evet.

**S: bölgem yükseltildikten sonra yeni bir Gen1 örneği oluşturabileceksiniz mi?**

- Y: Hayır. Bir bölge yükseltildikten sonra, yeni Gen1 örneklerinin oluşturulması devre dışı bırakılır.

## <a name="next-steps"></a>Sonraki adımlar

- [Yükseltme adımları](upgrade-to-latest-generation.md)
- [Bakım pencereleri](maintenance-scheduling.md)
- [Kaynak sistem durumu İzleyicisi](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Geçişe başlamadan önce gözden geçirin](upgrade-to-latest-generation.md#before-you-begin)
- [Bir geri yükleme noktasından yerinde yükseltme ve yükseltme](upgrade-to-latest-generation.md)
- [Kullanıcı tanımlı geri yükleme noktası oluşturma](sql-data-warehouse-restore-points.md)
- [Gen2 'e geri yüklemeyi öğrenin](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)
- [Bir SQL veri ambarı destek isteği açın](https://go.microsoft.com/fwlink/?linkid=857950)
