---
title: İş sürekliliği-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, PostgreSQL için Azure veritabanı kullanılırken iş sürekliliği (zaman içinde geri yükleme, veri merkezi kesintisi, coğrafi geri yükleme, çoğaltmalar) açıklanmaktadır.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: f8d8547c2d4900e6258f7360c50d8dfc17157832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100517253"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı ile iş sürekliliği 'ne genel bakış-tek sunucu

Bu genel bakışta, PostgreSQL için Azure veritabanı 'nın iş sürekliliği ve olağanüstü durum kurtarma için sağladığı özellikler açıklanmaktadır. Veri kaybına neden olabilecek veya veritabanınızın ve uygulamanızın kullanılamaz hale gelmesine neden olabilecek kurtarmayan olaylardan kurtarmaya yönelik seçenekler hakkında bilgi edinin. Bir kullanıcı veya uygulama hatası veri bütünlüğünü etkiliyorsa, bir Azure bölgesinin kesintiye neden olması veya uygulamanızın bakım gerektirmesi durumunda ne yapılacağını öğrenin.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>İş sürekliliği sağlamak için kullanabileceğiniz özellikler

İş sürekliliği planınızı geliştirdikçe, kurtarma süresi hedefiniz (RTO) bu şekilde kesintiye uğradıktan sonra uygulama tamamen kurtarmadan önce kabul edilebilir maksimum süreyi anlamanız gerekir. Ayrıca, kurtarma noktası hedefiniz (RPO) olduğundan, uygulamanın, kesintiye uğratan sonra kurtarma sırasında kaybedilmesi için en yüksek veri güncelleştirme miktarını (zaman aralığı) anlamanız gerekir.

PostgreSQL için Azure Veritabanı coğrafi geri yükleme başlatma olanağıyla coğrafi olarak yedekli yedeklemeleri ve farklı bir bölgeye okuma amaçlı çoğaltmalar dağıtmayı içeren iş sürekliliği özellikleri sağlar. Her birinin, kurtarma zamanı ve olası veri kaybı için farklı özellikleri vardır. [Coğrafi geri yükleme](concepts-backup.md) özelliği ile, başka bir bölgeden çoğaltılan yedekleme verileri kullanılarak yeni bir sunucu oluşturulur. Geri yükleme ve kurtarma için gereken toplam süre, veritabanının boyutuna ve kurtarılacak günlüklerin miktarına bağlıdır. Sunucu oluşturmak için genel süre, birkaç dakikadan birkaç saate kadar farklılık gösterir. [Okuma çoğaltmalarıyla](concepts-read-replicas.md), birincil işlemden alınan işlem günlükleri zaman uyumsuz olarak çoğaltmaya akışla kaydedilir. Bölge düzeyi veya bölge düzeyinde bir hata nedeniyle birincil veritabanı kesintisi durumunda çoğaltmaya yük devretmek, daha kısa bir RTO ve azaltılmış veri kaybı sağlar.

> [!NOTE]
> Birincil ve çoğaltma arasındaki gecikme, siteler arasındaki gecikmeye, aktarılan veri miktarına ve birincil sunucunun yazma iş yükünde en önemlisi ne kadar önemli olduğuna bağlıdır. Ağır yazma iş yükleri önemli bir gecikme oluşturabilir. 
>
> Okuma çoğaltmaları için kullanılan zaman uyumsuz bir çoğaltma doğası nedeniyle, yüksek bir kullanılabilirlik (HA) çözümü olarak **değerlendirilmemelidir,** çünkü daha yüksek olan bir daha yüksek RTO ve RPO anlamına gelebilir. Yalnızca gecikmeleri iş yükünün yoğun ve yoğun olmayan süreleriyle daha küçük kaldığı iş yükleri için okuma çoğaltmaları bir HA alternatifi görevi görebilir. Aksi takdirde, okuma çoğaltmaları, kullanıma yönelik ağır iş yükleri ve (olağanüstü durum kurtarma) DR senaryolarında doğru okuma ölçeği için tasarlanmıştır.

Aşağıdaki tabloda **tipik bir iş yükü** senaryosunda RTO ve RPO karşılaştırılır:

| **Özellik** | **Temel** | **Genel Amaçlı** | **Bellek için iyileştirilmiş** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Yedekten belirli bir noktaya geri yükleme | Bekletme dönemi içinde herhangi bir geri yükleme noktası <br/> RTO-değişecek <br/>RPO < 15 dk| Bekletme dönemi içinde herhangi bir geri yükleme noktası <br/> RTO-değişecek <br/>RPO < 15 dk | Bekletme dönemi içinde herhangi bir geri yükleme noktası <br/> RTO-değişecek <br/>RPO < 15 dk |
| Coğrafi olarak çoğaltılan yedeklerden coğrafi geri yükleme | Desteklenmez | RTO-değişecek <br/>RPO < 1 h | RTO-değişecek <br/>RPO < 1 h |
| Okuma amaçlı çoğaltmalar | RTO-dakika * <br/>RPO < 5 dk * | RTO-dakika * <br/>RPO < 5 dk *| RTO-dakika * <br/>RPO < 5 dk *|

 \* RTO ve RPO, siteler arasında gecikme, aktarılacak veri miktarı ve önemli birincil veritabanı yazma iş yükü gibi çeşitli faktörlere bağlı olarak bazı durumlarda **çok daha yüksek** olabilir. 

## <a name="recover-a-server-after-a-user-or-application-error"></a>Bir kullanıcı veya uygulama hatasından sonra bir sunucuyu kurtarma

Bir sunucuyu çeşitli kesintiye uğratan kurtarmak için hizmetin yedeklemelerini kullanabilirsiniz. Bir kullanıcı yanlışlıkla bazı verileri silebilir, yanlışlıkla önemli bir tabloyu bırakabilir, hatta tüm veritabanını bile bırakabilir. Bir uygulama, bir uygulama hatası nedeniyle yanlışlıkla hatalı verilerle iyi verilerin üzerine yazabilir ve bu şekilde devam edebilir.

Sunucunuzun bir kopyasını zaman içinde bilinen iyi bir noktada oluşturmak için bir **zaman içinde geri yükleme** gerçekleştirebilirsiniz. Bu nokta, sunucunuz için yapılandırdığınız yedekleme saklama süresi içinde olmalıdır. Veriler yeni sunucuya geri yüklendikten sonra, özgün sunucuyu yeni geri yüklenen sunucuyla değiştirebilir veya gerekli verileri geri yüklenen sunucudan özgün sunucuya kopyalayabilirsiniz.

> [!IMPORTANT]
> Silinen sunucular **geri yüklenemez.** Sunucuyu silerseniz, sunucuya ait olan tüm veritabanları da silinir ve kurtarılamaz. Sunucunuzun yanlışlıkla silinmesini önlemeye yardımcı olmak için [Azure Kaynak kilidini](../azure-resource-manager/management/lock-resources.md) kullanın.

## <a name="recover-from-an-azure-data-center-outage"></a>Azure veri merkezi kesintisinden kurtarma

Çok sık olmasa da Azure veri merkezlerinde kesintiler yaşanabilir. Bir kesinti oluştuğunda, en son birkaç dakika içinde bir iş kesintilerine neden olabilir, ancak saat için en son zaman alabilir.

Bir seçenek, veri merkezi kesintisi olduğunda sunucunuzun çevrimiçi duruma gelmesini bekleyeyöneliktir. Bu, örneğin bir geliştirme ortamı gibi belirli bir süre için sunucunun çevrimdışı olmasını sağlayan uygulamalar için geçerlidir. Bir veri merkezinde kesinti olduğunda, kesintiden en son ne kadar süre sürdüğünü bilemezsiniz, bu nedenle bu seçenek yalnızca sunucunuza bir süredir ihtiyacınız yoksa işe yarar.

## <a name="geo-restore"></a>Coğrafi geri yükleme

Coğrafi geri yükleme özelliği, coğrafi olarak yedekli yedeklemeleri kullanarak sunucuyu geri yükler. Yedeklemeler, sunucunuzun [eşleştirilmiş bölgesinde](../best-practices-availability-paired-regions.md)barındırılır. Bu yedeklerden diğer bölgelere geri yükleme yapabilirsiniz. Coğrafi geri yükleme, yedeklemelerdeki verilerle yeni bir sunucu oluşturur. [Yedekleme ve geri yükleme kavramları makalesindeki](concepts-backup.md)coğrafi geri yükleme hakkında daha fazla bilgi edinin.

> [!IMPORTANT]
> Coğrafi geri yükleme yalnızca, sunucuyu coğrafi olarak yedekli yedekleme depolama alanı ile sağladıysanız mümkündür. Var olan bir sunucu için yerel olarak yedekli yedekten coğrafi olarak yedekli yedeklemelere geçiş yapmak istiyorsanız, var olan sunucunuzun pg_dump kullanarak bir döküm almanız ve coğrafi olarak yedekli yedeklemelerle yapılandırılmış yeni oluşturulan bir sunucuya geri yüklemeniz gerekir.

## <a name="cross-region-read-replicas"></a>Bölgeler arası okuma çoğaltmaları
İş sürekliliği ve olağanüstü durum kurtarma planlamasını iyileştirmek için bölgeler arası okuma çoğaltmalarını kullanabilirsiniz. Okuma çoğaltmaları PostgreSQL 'in fiziksel çoğaltma teknolojisi kullanılarak zaman uyumsuz olarak güncelleştirilir ve birincil görevi erteleyebilirsiniz. Okuma çoğaltmaları, kullanılabilir bölgeler ve [okuma çoğaltmaları kavramları makalesindeki](concepts-read-replicas.md)yük devretme hakkında daha fazla bilgi edinin. 

## <a name="faq"></a>SSS
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>PostgreSQL için Azure veritabanı müşteri verilerini nerede depolar?
Varsayılan olarak, PostgreSQL için Azure veritabanı, müşteri verilerini dağıtıldığı bölgeden taşımaz veya depolamaz. Ancak, müşteriler isteğe bağlı olarak coğrafi olarak [yedekli yedeklemeleri](concepts-backup.md#backup-redundancy-options) etkinleştirmeyi veya farklı bir bölgedeki verileri depolamak için [çapraz bölge okuma çoğaltması](concepts-read-replicas.md#cross-region-replication) oluşturmayı tercih edebilir.


## <a name="next-steps"></a>Sonraki adımlar
- [PostgreSQL Için Azure veritabanı 'nda otomatikleştirilmiş yedeklemeler](concepts-backup.md)hakkında daha fazla bilgi edinin. 
- [Azure Portal](howto-restore-server-portal.md) veya [Azure CLI](howto-restore-server-cli.md)kullanarak nasıl geri yükleyeceğinizi öğrenin.
- [PostgreSQL Için Azure veritabanı 'nda okuma çoğaltmaları](concepts-read-replicas.md)hakkında bilgi edinin.
