---
title: Olağanüstü durum kurtarma ayrıntılarına ls
description: Olağanüstü durum kurtarma detaylarını gerçekleştirmek için Azure SQL veritabanı 'nı kullanmaya yönelik kılavuzluk ve en iyi uygulamaları öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 3ca00a03976ae38b7956616b8287220a7bc5998c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825845"
---
# <a name="performing-disaster-recovery-drill"></a>Olağanüstü durum kurtarma ayrıntısı gerçekleştiriliyor

Kurtarma iş akışı için uygulama hazırlığını doğrulamanın düzenli aralıklarla gerçekleştirilmesi önerilir. Uygulama davranışının ve veri kaybı etkilerine ve/veya yük devretmenin kapsadığı kesintiye karşı doğrulama iyi bir mühendislik uygulamasıdır. Ayrıca, iş sürekliliği sertifikasının bir parçası olarak birçok sektör standardı için de bir gereksinimdir.

Olağanüstü durum kurtarma detayının gerçekleştirilmesi aşağıdakilerden oluşur:

* Veri katmanı kesintisi benzetimi yapma
* Kurtar
* Uygulama bütünlüğünü doğrulama sonrası kurtarma

[Uygulamanızı iş sürekliliği için nasıl tasarlamış](sql-database-business-continuity.md)olduğunuza bağlı olarak, detaya gitmeyi yürütmek için iş akışı farklılık gösterebilir. Bu makalede, Azure SQL veritabanı bağlamında olağanüstü durum kurtarma detayına yönelik en iyi yöntemler açıklanmaktadır.

## <a name="geo-restore"></a>Coğrafi Geri Yükleme

Olağanüstü durum kurtarma detayını gerçekleştirirken olası veri kaybını engellemek için, üretim ortamının bir kopyasını oluşturarak ve uygulamanın yük devretme iş akışını doğrulamak üzere kullanarak bir test ortamı kullanarak detaya gitmeyi gerçekleştirin.

### <a name="outage-simulation"></a>Kesinti simülasyonu

Kesinti benzetimini yapmak için kaynak veritabanını yeniden adlandırabilirsiniz. Bu ad değişikliği uygulama bağlantı hatalarıyla neden olur.

### <a name="recovery"></a>Kurtarma

* Veritabanının coğrafi geri yüklemesini [burada](sql-database-disaster-recovery.md)açıklandığı gibi farklı bir sunucuda gerçekleştirin.
* Kurtarılan veritabanına bağlanacak uygulama yapılandırmasını değiştirin ve kurtarmayı tamamladıktan [sonra kurtarma işleminden sonra veritabanını Yapılandır](sql-database-disaster-recovery.md) kılavuzunu izleyin.

### <a name="validation"></a>Doğrulama

Uygulama bütünlüğü gönderisini (bağlantı dizeleri, oturum açmalar, temel işlevsellik testi veya standart uygulama sonuçlandır yordamlarının diğer doğrulamaları dahil) doğrulayarak detaya gitmeyi doldurun.

## <a name="failover-groups"></a>Yük devretme grupları

Yük devretme grupları kullanılarak korunan bir veritabanı için, detaya gitme işlemi ikincil sunucuya planlı yük devretme işlemini içerir. Planlı Yük devretme, roller geçildiğinde, yük devretme grubundaki birincil ve ikincil veritabanlarının eşitlenmiş durumda kalmasını sağlar. Planlanmamış yük devretmenin aksine, bu işlem veri kaybına neden olmaz; bu nedenle, detaya gitme üretim ortamında gerçekleştirilebilir.

### <a name="outage-simulation"></a>Kesinti simülasyonu

Kesinti benzetimi yapmak için, Web uygulamasını veya veritabanına bağlı sanal makineyi devre dışı bırakabilirsiniz. Bu kesinti simülasyonu, Web istemcilerinin bağlantı arızalarına neden olur.

### <a name="recovery"></a>Kurtarma

* DR bölgesindeki uygulama yapılandırmasının, tümüyle erişilebilen yeni birincil haline gelen eski ikinciye işaret ettiğini doğrulayın.
* İkincil sunucudan yük devretme grubunun [planlı yük devretmesini](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) başlatın.
* Kurtarmayı tamamladıktan [sonra kurtarma işleminden sonra veritabanını Yapılandır](sql-database-disaster-recovery.md) kılavuzunu izleyin.

### <a name="validation"></a>Doğrulama

Uygulama bütünlüğü sonrası kurtarma (bağlantı, temel işlevsellik testi veya detaya gitme için gereken diğer doğrulamalar dahil) doğrulayarak detaya gitmeyi doldurun.

## <a name="next-steps"></a>Sonraki adımlar

* İş sürekliliği senaryoları hakkında bilgi edinmek için bkz. [süreklilik senaryoları](sql-database-business-continuity.md).
* Azure SQL veritabanı otomatik yedeklemeleri hakkında bilgi edinmek için bkz. [SQL veritabanı otomatik yedeklemeleri](sql-database-automated-backups.md)
* Kurtarmaya yönelik otomatik yedeklemeleri kullanma hakkında bilgi edinmek için bkz. [hizmet tarafından başlatılan yedeklemelerden bir veritabanını geri yükleme](sql-database-recovery-using-backups.md).
* Daha hızlı kurtarma seçenekleri hakkında daha fazla bilgi edinmek için bkz. [etkin coğrafi çoğaltma](sql-database-active-geo-replication.md) ve [otomatik yük devretme grupları](sql-database-auto-failover-group.md).
