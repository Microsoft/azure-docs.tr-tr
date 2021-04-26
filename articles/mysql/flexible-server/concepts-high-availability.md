---
title: MySQL için Azure veritabanı esnek sunucusu ile bölge yedekli yüksek kullanılabilirliğe genel bakış
description: MySQL için Azure veritabanı esnek sunucusu ile bölge yedekli yüksek kullanılabilirlik kavramlarını öğrenin
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 5b5e1491d7f76cd4cff76d0c9a1af4daa49fa483
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813011"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>MySQL için Azure veritabanı esnek sunucusu 'nda yüksek kullanılabilirlik kavramları (Önizleme)

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

MySQL için Azure veritabanı esnek sunucu (Önizleme), **bölge yedekli** yüksek kullanılabilirlik seçeneği kullanılarak otomatik yük devretme ile yüksek kullanılabilirliğe izin verir. Alanlar arası yedekli bir yapılandırmada dağıtıldığında, esnek sunucu farklı bir kullanılabilirlik alanında bekleyen çoğaltmayı otomatik olarak sağlar ve yönetir. Depolama düzeyi çoğaltmasını kullanarak, veriler, yük devretmeden sonra sıfır veri kaybını etkinleştirmek için ikincil bölgedeki bekleme sunucusuna **eşzamanlı olarak çoğaltılır** . Yük devretme, istemci uygulamasından tamamen saydamdır ve herhangi bir kullanıcı eylemi gerektirmez. Bekleme sunucusu herhangi bir okuma veya yazma işlemi için kullanılamaz, ancak hızlı yük devretmeyi etkinleştirmek için pasif bir bekleme olur. Yük devretme süreleri genellikle 60-120 saniyeden fazla aralığındadır.

Bölgesel olarak yedekli yüksek kullanılabilirlik yapılandırması, Kullanıcı tarafından başlatılan ölçek işlem işlemleri gibi planlı olaylar ve temel alınan donanım ve yazılım hataları, ağ hataları ve hatta kullanılabilirlik bölgesi hataları gibi planlanmamış olaylar sırasında otomatik yük devretmeyi mümkün bir şekilde sunar.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="bölge yedekli yüksek kullanılabilirliği görünümü":::

## <a name="zone-redundancy-architecture"></a>Bölge artıklığı mimarisi

Birincil sunucu, bölgesinde ve belirli bir kullanılabilirlik bölgesinde dağıtılır. Yüksek kullanılabilirlik seçildiğinde, işlem katmanı, işlem boyutu, depolama boyutu ve ağ yapılandırması dahil olmak üzere, birincil sunucuyla aynı yapılandırmaya sahip bir yedek çoğaltma sunucusu otomatik olarak dağıtılır. Günlük verileri, herhangi bir hata durumunda sıfır veri kaybı sağlamak için zaman uyumlu çoğaltmaya eşzamanlı olarak çoğaltılır. Otomatik yedeklemeler, hem anlık görüntüler hem de günlük yedeklemeleri, birincil veritabanı sunucusundan gerçekleştirilir. 

HA 'nın sistem durumu sürekli olarak izlenir ve genel bakış sayfasında raporlanır.

Çeşitli çoğaltma durumları aşağıda listelenmiştir:

| **Durum** | **Açıklama** |
| :----- | :------ |
| <b>NotEnabled | Bölge yedekli HA etkin değil |
| <b>CreatingStandby | Yeni bir bekleme oluşturma işleminde |
| <b>ReplicatingData | Bekleme oluşturulduktan sonra birincil sunucu ile birlikte yer alır. |
| <b>Yük devretme | Veritabanı sunucusu birincil bilgisayardan bekleme moduna yük devretme sürecinde. |
| <b>Sağlam | Bölgesel olarak yedekli HA kararlı durumda ve sağlıklı. |
| <b>RemovingStandby | Kullanıcı eylemine bağlı olarak, bekleme çoğaltması silme sürecinde olur.| 

## <a name="advantages"></a>Avantajlar

Bölge artıklığı HA özelliğini kullanmanın bazı avantajları aşağıda verilmiştir: 

- Bekleme çoğaltması, sanal çekirdekler, depolama alanı, ağ ayarları (VNET, güvenlik duvarı) vb. gibi tam bir VM yapılandırmasında dağıtılır.
- Yüksek kullanılabilirliği devre dışı bırakarak bekleme çoğaltmasını kaldırma özelliği.
- Otomatik yedeklemeler, birincil veritabanı sunucusundan gerçekleştirilen ve bölge yedekli depolamada saklanan anlık görüntü tabanlıdır.
- Yük devretme durumunda, MySQL için Azure veritabanı esnek sunucu, yüksek kullanılabilirlik etkinse otomatik olarak yedek çoğaltmaya yük devreder. Yüksek kullanılabilirlik kurulumu birincil sunucuyu izler ve yeniden çevrimiçi duruma getirir.
- İstemciler her zaman birincil veritabanı sunucusuna bağlanır.
- Veritabanı kilitlenmesi veya düğüm hatası varsa, ilk olarak yeniden başlatma işlemi aynı düğümde denenir. Başarısız olursa, otomatik yük devretme tetiklenir.
- Herhangi bir statik sunucu parametresi değişikliğini seçmek için sunucuyu yeniden başlatma özelliği.

## <a name="steady-state-operations"></a>Sabit durum işlemleri

Uygulamalar, veritabanı sunucusu adı kullanılarak birincil sunucuya bağlanır. Bekleme çoğaltma bilgileri doğrudan erişim için gösterilmez. Yürütmeler ve yazma işlemleri yalnızca, günlük dosyaları hem birincil sunucunun diskinde hem de bekleme çoğaltmasında zaman uyumlu bir şekilde kalıcı olduktan sonra uygulamaya onaylanır. Bu ek gidiş dönüş gereksiniminden dolayı, uygulamalar yazma ve işlemeler için yükseltilmiş gecikme süresi bekleyebilir. Portalda yüksek kullanılabilirlik durumunu izleyebilirsiniz.

## <a name="failover-process"></a>Yük devretme işlemi 
İş sürekliliği için planlanmış ve planlanmamış olaylar için bir yük devretme işlemi yapmanız gerekir. 

### <a name="planned-events"></a>Planlı olaylar

Planlanmış kapalı kalma süresi olayları, Azure tarafından zamanlanan etkinlikleri, düzenli yazılım güncelleştirmeleri, alt sürüm yükseltmeleri veya ölçek işlem ve ölçek depolama işlemleri gibi müşteriler tarafından başlatılan etkinlikler içerir. Tüm bu değişiklikler ilk olarak bekleme çoğaltmaya uygulanır. Bu süre boyunca, uygulamalar birincil sunucuya erişmeye devam eder. Bekleme çoğaltması güncelleştirildikten sonra, birincil sunucu bağlantıları kaldırılır, DNS kaydını güncelleştirerek, bekleme çoğaltmasını aynı veritabanı sunucusu adına sahip birincil olacak şekilde etkinleştiren bir yük devretme tetiklenir. İstemci bağlantılarının bağlantısı kesilir ve bunlara yeniden bağlanmaları ve işlemlerini sürdürmeleri gerekir. Eski birincil ile aynı bölgede yeni bir bekleme sunucusu oluşturulur. Genel yük devretme zamanının% 60-120 olması beklenir. 

>[!NOTE]
> İşlem ölçekleme işlemi söz konusu olduğunda, ikincil çoğaltma sunucusunu ve ardından birincil sunucuyu ölçeklendiririz. Dahil edilen yük devretme yok.

### <a name="failover-process---unplanned-events"></a>Yük devretme işlemi-planlanmamış olaylar
Planlanmamış hizmet alt süreleri, işlem, ağ, depolama hataları veya güç kesintileri gibi altyapı hatalarının veritabanının kullanılabilirliğini etkilediği yazılım hatalarını içerir. Veritabanının kullanım dışı kalması durumunda, bekleyen çoğaltmaya çoğaltma yok olur ve bekleme çoğaltması birincil veritabanı olarak etkinleştirilir. DNS güncellenir ve istemciler daha sonra veritabanı sunucusuna yeniden bağlanır ve işlemlerini sürdürür. Genel yük devretme süresinin% 60-120 olması beklenir. Ancak, yük devretme sırasında büyük işlemler ve kurtarma süresi gibi birincil veritabanı sunucusundaki etkinliğe bağlı olarak, yük devretme daha uzun sürebilir.

### <a name="forced-failover"></a>Zorlamalı yük devretme
MySQL için Azure veritabanı zorla yük devretme işlemi, uygulama senaryolarınız ile işlevselliği test etmenize olanak tanıyan bir yük devretmeyi el ile zorlamanıza olanak sağlar ve herhangi bir kesinti oluşması durumunda size hazırlanmanıza yardımcı olur. Zorlamalı yük devretme, bekleme çoğaltmasını, DNS kaydını güncelleştirerek aynı veritabanı sunucusu adına sahip birincil sunucu olmak üzere bir yük devretme tetikleyerek birincil sunucu haline gelir. Özgün birincil sunucu yeniden başlatılır ve bekleme çoğaltmaya geçiş yapılır. İstemci bağlantılarının bağlantısı kesilir ve işlemleri sürdürülmek için yeniden bağlanması gerekir. Geçerli iş yüküne ve en son denetim noktasına bağlı olarak genel yük devretme süresi ölçülecektir. Genel olarak, 60-120s arasında olması beklenir.

## <a name="schedule-maintenance-window"></a>Bakım penceresini zamanla 

Esnek sunucular, tercih ettiğiniz günde 30 dakikalık bir pencere seçebileceğiniz, düzeltme eki uygulama veya ikincil sürüm yükseltmeleri gibi Azure bakım işlemlerinin gerçekleşmesine neden olabilir. Yüksek kullanılabilirlik ile yapılandırılmış esnek sunucularınız için, bu bakım etkinlikleri önce bekleme çoğaltma üzerinde gerçekleştirilir. 

## <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme 
Esnek sunucu yüksek kullanılabilirlik zaman uyumlu olarak verileri çoğaltarak yapılandırılmışsa, bekleme sunucusu birincil ile güncel olur. Birincil (örneğin, bir tablonun yanlışlıkla düşürülme veya yanlış güncelleştirmeler gibi) herhangi bir kullanıcı hatası, bekleme durumuna tamamen çoğaltılır. Bu nedenle, bu tür mantıksal hatalardan kurtarmak için bekleme kullanamazsınız. Bu mantıksal hatalardan kurtarmak için, hata oluşmadan hemen önce zaman içindeki zamana geri yükleme gerçekleştirmeniz gerekir. Esnek sunucu 'nın zaman içinde geri yükleme özelliğini kullanarak, yüksek kullanılabilirliğe sahip veritabanını geri yüklediğinizde, yeni bir veritabanı sunucusu, Kullanıcı tarafından sağlanmış bir ada sahip yeni bir esnek sunucu olarak geri yüklenir. Daha sonra nesneyi veritabanı sunucusundan dışarı aktarabilir ve üretim veritabanı sunucunuza aktarabilirsiniz. Benzer şekilde, veritabanı sunucunuzu test ve geliştirme amaçlarıyla kopyalamak istiyorsanız veya başka amaçlar için geri yüklemek istiyorsanız en son geri yükleme noktasını veya özel bir geri yükleme noktasını seçebilirsiniz. Geri yükleme işlemi tek bölgede esnek bir sunucu oluşturur.

## <a name="mitigate-downtime"></a>Kapalı kalma süresini azaltma 
Bölge artıklığı HA özelliğini kullanmıyorsanız, uygulamanız için kapalı kalma süresini azaltmaya devam etmeniz gerekir. Zamanlanan düzeltme ekleri, ikincil sürüm yükseltmeleri veya Kullanıcı tarafından başlatılan işlemler gibi planlama hizmeti, zamanlanmış bakım pencerelerinin bir parçası olarak gerçekleştirilebilir. Planlanmış düzeltme ekleri, ikincil sürüm yükseltmeleri veya ölçek işlemi gibi kullanıcı tarafından başlatılan işlemler gibi planlı hizmet, kapalı kalma süresini doğurur. Azure tarafından başlatılan bakım görevleri için uygulama etkisini azaltmak üzere, bunları haftanın günü boyunca zamanlamayı ve en az uygulamayı etkileyen süreyi zamanlamayı tercih edebilirsiniz. 

Veritabanı kilitlenmesi veya sunucu arızası gibi planlanmamış kapalı kalma süresi olayları sırasında, etkilenen sunucu aynı bölge içinde yeniden başlatılır. Nadir olarak, tüm bölge etkilenirse, veritabanını bölge içindeki başka bir bölgeye geri yükleyebilirsiniz. 

## <a name="things-to-know-with-zone-redundancy"></a>Bölge artıklığına sahip olmak için gerekenler 

Aşağıda, bölge artıklığı yüksek kullanılabilirliğini kullanırken göz önünde bulundurmanız gereken bazı noktalar verilmiştir: 

- Yüksek kullanılabilirlik **yalnızca** esnek sunucu oluşturma zamanı sırasında ayarlanabilir.
- Yüksek kullanılabilirlik, Burstable işlem katmanında desteklenmez.
- Farklı bir kullanılabilirlik bölgesine zaman uyumlu çoğaltma nedeniyle, birincil veritabanı sunucusu yükseltilmiş yazma ve tamamlama gecikmesi ile karşılaşabilir.
- Bekleme çoğaltması salt okuma sorguları için kullanılamaz.
- Yük devretme sırasında birincil sunucudaki etkinliğe bağlı olarak, yük devretmenin tamamlanması 60-120 saniye veya daha uzun sürebilir.
- Statik parametre değişikliklerini seçmek için birincil veritabanı sunucusunu yeniden başlatmak, bekleme çoğaltmasını de yeniden başlatır.
- Bölge yedekli yüksek kullanılabilirlik sunucuları için okuma çoğaltmalarının yapılandırılması desteklenmez.
- Yönetilen bakım penceresi sırasında müşterinin başlattığı yönetim görevlerinin yapılandırılması otomatikleştirilemez.
- Ölçek işlem ve küçük sürüm yükseltmeleri gibi planlı olaylar hem birincil hem de bekleme sırasında aynı anda gerçekleşir. 


## <a name="next-steps"></a>Sonraki adımlar

- [İş sürekliliği](./concepts-business-continuity.md) hakkında bilgi edinin
- [Bölge yedekli yüksek kullanılabilirlik](./concepts-high-availability.md) hakkında bilgi edinin
- [Yedekleme ve kurtarma](./concepts-backup-restore.md) hakkında bilgi edinin
