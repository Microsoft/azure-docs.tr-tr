---
title: PostgreSQL için Azure veritabanı-esnek sunucu (Önizleme) ile bölge yedekli yüksek kullanılabilirliğe genel bakış
description: PostgreSQL için Azure veritabanı-esnek sunucu ile bölge yedekli yüksek kullanılabilirlik kavramlarını öğrenin
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: c0d9b6042ae695caa73d926653f237b756bf4971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94366732"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL için Azure veritabanı 'nda yüksek kullanılabilirlik kavramları-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

PostgreSQL için Azure veritabanı-esnek sunucu, bölgesel olarak **yedekli** sunucu dağıtımı kullanarak otomatik yük devretme özelliği ile yüksek kullanılabilirlik yapılandırması sunar. Alanlar arası yedekli bir yapılandırmada dağıtıldığında, esnek sunucu farklı bir kullanılabilirlik alanında bekleyen çoğaltmayı otomatik olarak sağlar ve yönetir. PostgreSQL akış çoğaltmasını kullanarak, veriler bekleyen çoğaltma sunucusuna **zaman uyumlu** modda çoğaltılır. 

Bölgesel olarak yedekli yapılandırma, Kullanıcı tarafından başlatılan ölçek işlem işlemi gibi planlı olaylar sırasında ve ayrıca temel alınan donanım, yazılım hataları, ağ hataları ve kullanılabilirlik bölgesi hataları gibi planlanmamış olaylar sırasında sıfır veri kaybı ile otomatik yük devretme özelliği sunar. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="bölge yedekli yüksek kullanılabilirlik"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Bölge yedekli yüksek kullanılabilirlik mimarisi

Birincil veritabanı sunucunuzun dağıtılacağı bölgeyi ve kullanılabilirlik alanını seçebilirsiniz. Bekleyen çoğaltma sunucusu, birincil sunucuyla aynı yapılandırmaya (aynı işlem katmanı, işlem boyutu, depolama boyutu ve ağ yapılandırması) sahip olur ve farklı bir kullanılabilirlik alanında sağlanır. İşlem günlükleri, PostgreSQL akış çoğaltmasını kullanarak bekleme konumuna zaman uyumlu modda çoğaltılır. Otomatik yedeklemeler, birincil veritabanı sunucusundan düzenli olarak gerçekleştirilir, ancak işlem günlükleri sürekli olarak bekleme çoğaltmasından yedekleme depolamasına arşivlenir. 

Yüksek kullanılabilirlik yapılandırmasının sistem durumu, portalda sürekli olarak izlenir ve raporlanır. Bölge yedekli yüksek kullanılabilirlik durumları aşağıda listelenmiştir:

| **Durum** | **Açıklama** |
| ------- | ------ |
| <b> Başlatarak | Yeni bir hazır bekleyen sunucu oluşturma işleminde |
| <b> Veri çoğaltma | Bekleme oluşturulduktan sonra birincil ile birlikte yer alır. |
| <b> Sağlıklı | Çoğaltma kararlı durumda ve sağlıklı. |
| <b> Yük devrediliyor | Veritabanı sunucusu bekleme konumuna devrediliyor. |
| <b> Bekleme kaldırma kaldırılıyor | Bekleyen sunucuyu silme sürecinde. | 
| <b> Etkin değil | Bölge yedekli yüksek kullanılabilirliği etkin değil.  |

## <a name="steady-state-operations"></a>Sabit durum işlemleri

PostgreSQL istemci uygulamaları, VERITABANı sunucu adı kullanılarak birincil sunucuya bağlanır. Uygulama okumaları doğrudan birincil sunucudan sunulur, ancak işlemeler ve yazma işlemleri yalnızca birincil sunucuda ve bekleme çoğaltmasındaki veriler kalıcı olduktan sonra uygulamaya onaylanır. Bu ek gidiş dönüş gereksiniminden dolayı, uygulamalar yazma ve işlemeler için yükseltilmiş gecikme süresi bekleyebilir. Portalda yüksek kullanılabilirlik durumunu izleyebilirsiniz.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="bölge yedekli yüksek kullanılabilirlik-sabit durum"::: 

1. İstemciler esnek sunucuya bağlanır ve yazma işlemleri gerçekleştirir.
2. Değişiklikler, bekleme sitesine çoğaltılır.
3. Birincil bildirim alır.
4. Yazma/yürütmeler onaylanır.

## <a name="failover-process---planned-downtimes"></a>Yük devretme işlemi-planlı aşağı saatler

Planlanmış kapalı kalma süresi olayları, Azure zamanlanmış düzenli yazılım güncelleştirmeleri ve ikincil sürüm yükseltmeleri içerir. Yüksek kullanılabilirlik olarak yapılandırıldığında, uygulamalar birincil sunucuya erişmeye devam ederken bu işlemler ilk olarak bekleme çoğaltmaya uygulanır. Bekleme çoğaltması güncelleştirildikten sonra, birincil sunucu bağlantıları boşaltılır ve bekleme çoğaltmasını aynı veritabanı sunucusu adına sahip birincil olacak şekilde etkinleştiren bir yük devretme tetiklenir. İstemci uygulamalarının aynı veritabanı sunucusu adıyla yeni birincil sunucuya yeniden bağlanmasına ve işlemlerini sürdürmeleri gerekecektir. Eski birincil ile aynı bölgede yeni bir bekleme sunucusu kurulacaktır. 

Ölçek hesaplama veya ölçek depolama gibi diğer Kullanıcı tarafından başlatılan işlemler için, değişiklikler önce bekleme durumunda, ardından birincil tarafından uygulanır. Şu anda, bağlantılar bekleme konumuna devredilmez ve bu nedenle işlem birincil sunucuda yürütülürken kapalı kalma süresini doğurur.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Yönetilen bakım penceresi ile planlanan kapalı kalma süresini azaltma

 Azure tarafından başlatılan bakım etkinliklerini, tercih ettiğiniz bir günde 30 dakikalık bir pencere seçerek, veritabanlarının etkinliklerinin düşük olması beklenen şekilde zamanlayabilirsiniz. Düzeltme eki uygulama veya ikincil sürüm yükseltmeleri gibi Azure bakım görevleri bu pencere sırasında gerçekleşecektir.  Yüksek kullanılabilirliğe sahip esnek sunucular için, bu bakım etkinlikleri önce bekleme çoğaltma üzerinde gerçekleştirilir ve sonra etkinleştirilir. Uygulamalar daha sonra yeni birincil sunucuya yeniden bağlanır ve yeni bir bekleme sağlandığında işlemlerini sürdürür.

## <a name="failover-process---unplanned-downtimes"></a>Yük devretme işlemi-planlanmamış kapalı kalma süreleri

Planlanmamış kesintiler, yazılım hatalarını veya altyapı bileşeni başarısızlıklarını, veritabanının kullanılabilirliğini etkiler. Sunucu kullanılamamız, izleme sistemi tarafından algılanırsa, bekleme çoğaltmasındaki çoğaltma devre dışı olur ve bekleme çoğaltması birincil veritabanı sunucusu olarak etkinleştirilir. İstemciler aynı bağlantı dizesini kullanarak veritabanı sunucusuna yeniden bağlanabilir ve işlemlerini sürdürür. Genel yük devretme süresinin 60-120s olması beklenir. Ancak, yük devretme sırasında büyük işlemler ve kurtarma süresi gibi birincil veritabanı sunucusundaki etkinliğe bağlı olarak, yük devretme daha uzun sürebilir.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="bölge yedekli yüksek kullanılabilirlik-yük devretme"::: 

1. Birincil veritabanı sunucusu çalışmıyor ve istemciler veritabanı bağlantısını kaybeder. 
2. Bekleme sunucusu, yeni birincil sunucu olmak üzere etkinleştirilir. İstemci, aynı bağlantı dizesini kullanarak yeni birincil sunucuya bağlanır. İstemci uygulamasının birincil veritabanı sunucusuyla aynı bölgede olması gecikmeyi azaltır ve performansı geliştirir.
3. Bekleme sunucusu, eski birincil sunucuyla aynı bölgede oluşturulmuştur ve akış çoğaltması başlatılır. 
4. Sabit durum çoğaltma kurulduktan sonra, her iki sitede da veriler kalıcı olduktan sonra, istemci uygulaması işlemeleri ve yazmaları onaylanır.

## <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme 

Yüksek kullanılabilirlik ile yapılandırılan esnek sunucular, güncel tutmak için verileri gerçek zamanlı olarak bekleme sunucusuna çoğaltır. Birincil sunucudaki (örneğin, bir tablonun yanlışlıkla düşürülme veya yanlış veri güncelleştirmelerinin) herhangi bir kullanıcı hatası, bekleme çoğaltmaya tam olarak çoğaltılır. Bu nedenle, bu tür mantıksal hatalardan kurtarmak için bekleme kullanamazsınız. Bu tür hatalardan kurtulmak için yedeklerden zaman içinde geri yükleme gerçekleştirmeniz gerekir.  Esnek sunucunun zaman içindeki bir noktaya geri yükleme özelliğini kullanarak, hata olmadan önceki zamana geri yükleme yapabilirsiniz. Yüksek kullanılabilirlik ile yapılandırılmış veritabanları için, yeni bir veritabanı sunucusu, Kullanıcı tarafından sağlanmış bir ada sahip tek bir esnek sunucu olarak geri yüklenecektir. Daha sonra nesneyi veritabanı sunucusundan dışarı aktarabilir ve üretim veritabanı sunucunuza aktarabilirsiniz. Benzer şekilde, veritabanı sunucunuzu test ve geliştirme amaçlarıyla kopyalamak istiyorsanız veya başka amaçlar için geri yüklemek istiyorsanız, zaman içinde yapılan geri yüklemeler gerçekleştirebilirsiniz.

## <a name="zone-redundant-high-availability---features"></a>Bölge yedekli yüksek kullanılabilirlik-Özellikler

-   Bekleme çoğaltması, sanal çekirdekler, depolama, ağ ayarları (VNET, güvenlik duvarı) vb. dahil olmak üzere birincil sunucuyla aynı tam bir VM yapılandırmasında dağıtılır.

-   Var olan bir veritabanı sunucusu için yüksek kullanılabilirlik ekleme özelliği.

-   Yüksek kullanılabilirliği devre dışı bırakarak bekleme çoğaltmasını kaldırma özelliği.

-   Birincil veritabanı sunucunuz için kullanılabilirlik bölgenizi seçme özelliği.

-   Birincil ve hazır bekleyen veritabanı sunucularını durdurma, başlatma ve yeniden başlatma özelliği.

-   Otomatik yedeklemeler, birincil veritabanı sunucusundan gerçekleştirilir ve bölgesel olarak yedekli depolama alanında depolanır.

-   İstemciler her zaman birincil veritabanı sunucusuna bağlanır.

-   Herhangi bir statik sunucu parametresi değişikliğini seçmek için sunucuyu yeniden başlatma özelliği.
  
-   Küçük sürüm yükseltmeleri gibi düzenli bakım etkinlikleri, ilk başta bekleme durumunda gerçekleşir ve kapalı kalma süresini azaltmak için hizmetin yük devretmesine neden olur.  

## <a name="zone-redundant-high-availability---limitations"></a>Bölge yedekli yüksek kullanılabilirlik-sınırlamalar

-   Hızla artırılabilir işlem katmanında yüksek kullanılabilirlik desteklenmez.
-   Yüksek kullanılabilirlik yalnızca birden çok alanın kullanılabildiği bölgelerde desteklenir.
-   Farklı bir kullanılabilirlik bölgesine zaman uyumlu çoğaltma nedeniyle, uygulamalar yükseltilmiş yazma ve tamamlama gecikmesi ile karşılaşabilir.

-   Bekleme çoğaltması, okuma sorguları için kullanılamaz.

-   Birincil sunucudaki iş yüküne ve etkinliğe bağlı olarak, yük devretme işlemi 120 saniyeden daha uzun sürebilir.

-   Birincil veritabanı sunucusunu yeniden başlatmak, bekleme çoğaltmasını de yeniden başlatır. 

-   Ek okuma çoğaltmalarının yapılandırılması desteklenmez.

-   Yönetilen bakım penceresi sırasında müşterinin başlattığı yönetim görevlerinin yapılandırılması zamanlanamaz.

-   İşlemi ölçeklendirme ve depolamayı ölçeklendirme gibi planlı olaylar önce bekleyen çoğaltmada ve ardından da birincil sunucuda gerçekleşir. Bu planlı işlemler için sunucu yük devretmiyor. 

-  Mantıksal kod çözme veya mantıksal çoğaltma, bir HA yapılandırılmış esnek sunucu ile yapılandırıldıysa, bekleme sunucusuna yük devretme durumunda mantıksal çoğaltma yuvaları, bekleme sunucusuna kopyalanmaz.  

## <a name="next-steps"></a>Sonraki adımlar

-   [İş sürekliliği](./concepts-business-continuity.md) hakkında bilgi edinin
-   [Yüksek kullanılabilirliği yönetme](./how-to-manage-high-availability-portal.md) hakkında bilgi edinin
-   [Yedekleme ve kurtarma](./concepts-backup-restore.md) hakkında bilgi edinin