---
title: Restore-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı-Azure portal
description: Bu makalede, Azure portal aracılığıyla PostgreSQL için Azure veritabanı-hiper ölçek (Citus) içinde geri yükleme işlemlerinin nasıl gerçekleştirileceği açıklanır.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/14/2021
ms.openlocfilehash: 99e507fed35e5b74aa44bc2713550f7cf9d7b2e5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518890"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Hiper ölçek (Citus) sunucu grubunun zaman içindeki bir noktaya geri yüklemesi

Bu makalede, yedeklemeleri kullanarak bir hiper ölçek (Citus) sunucu grubu için [zaman içinde kurtarma](concepts-hyperscale-backup.md#restore) işlemleri gerçekleştirmeye yönelik adım adım yordamlar sunulmaktadır. En erken yedeklemeye ya da saklama döneminizin içindeki özel bir geri yükleme noktasına geri yükleyebilirsiniz.

## <a name="restoring-to-the-earliest-restore-point"></a>En erken geri yükleme noktasına geri yükleme

Hiperscale (Citus) sunucu grubunuzu mevcut olan en eski yedeklemeye geri yüklemek için aşağıdaki adımları izleyin.

1.  [Azure Portal](https://portal.azure.com/), geri yüklemek istediğiniz sunucu grubunu seçin.

2.  Sol panelde **Genel Bakış ' a** tıklayın ve **geri yükle**' ye tıklayın.

    > [!IMPORTANT]
    > Sunucu grubunuz için **geri yükleme** düğmesi henüz yoksa lütfen bir Azure destek isteği açın.

3.  Geri yükleme sayfası, **en erken** ve **özel** bir geri yükleme noktası arasında seçim yapmanız istenir ve en erken tarihi görüntüler.

4.  **En erken geri yükleme noktası** seçin.

5.  **Yeni sunucuya geri yükle** alanına yeni bir sunucu grubu adı girin. Diğer alanlar (abonelik, kaynak grubu ve konum) görüntülenir, ancak düzenlenemez.

6.  **Tamam**'a tıklayın.

7.  Geri yükleme işleminin başlatıldığı bir bildirim gösterilir.

Son olarak, [geri yükleme sonrası görevleri](#post-restore-tasks)izleyin.

## <a name="restoring-to-a-custom-restore-point"></a>Özel bir geri yükleme noktasına geri yükleme

Hiperscale (Citus) sunucu grubunuzu seçtiğiniz tarih ve saate geri yüklemek için aşağıdaki adımları izleyin.

1.  [Azure Portal](https://portal.azure.com/), geri yüklemek istediğiniz sunucu grubunu seçin.

2.  Sol panelde **Genel Bakış ' a** tıklayın ve **geri yükle** ' ye tıklayın

    > [!IMPORTANT]
    > Sunucu grubunuz için **geri yükleme** düğmesi henüz yoksa lütfen bir Azure destek isteği açın.

3.  Geri yükleme sayfası, **en erken** ve **özel** bir geri yükleme noktası arasında seçim yapmanız istenir ve en erken tarihi görüntüler.

4.  **Özel geri yükleme noktası** seçin.

5.  **Geri yükleme noktası (UTC)** için tarih ve saat ' i seçin ve **yeni sunucuya geri yükle** alanına yeni bir sunucu grubu adı belirtin. Diğer alanlar (abonelik, kaynak grubu ve konum) görüntülenir, ancak düzenlenemez.
 
6.  **Tamam**'a tıklayın.

7.  Geri yükleme işleminin başlatıldığı bir bildirim gösterilir.

Son olarak, [geri yükleme sonrası görevleri](#post-restore-tasks)izleyin.

## <a name="post-restore-tasks"></a>Post-restore tasks

Bir geri yüklemeden sonra kullanıcılarınızın ve uygulamalarınızın yedeklemesini ve çalışmasını sağlamak için aşağıdakileri yapmanız gerekir:

* Yeni sunucu özgün sunucunun yerini alacak şekilde, istemcileri ve istemci uygulamalarını yeni sunucuya yeniden yönlendirin
* Kullanıcıların bağlanabilmesi için uygun bir sunucu düzeyi güvenlik duvarının bulunduğundan emin olun. Bu kurallar özgün sunucu grubundan kopyalanmaz.
* PostgreSQL sunucu parametrelerini gereken şekilde ayarlayın. Parametreler, özgün sunucu grubundan kopyalanmaz.
* Uygun oturum açma ve veritabanı düzeyi izinlerinin yerinde olduğundan emin olun.
* Uyarıları uygun şekilde yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

* Hiper ölçekte [yedekleme ve geri yükleme](concepts-hyperscale-backup.md) hakkında daha fazla bilgi edinin (Citus).
* Hiperscale (Citus) sunucu gruplarında [Önerilen uyarıları](./howto-hyperscale-alert-on-metric.md#suggested-alerts) ayarlayın.
