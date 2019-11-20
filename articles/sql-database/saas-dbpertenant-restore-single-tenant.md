---
title: Çok kiracılı bir SaaS uygulamasında bir veritabanını geri yükleme
description: Verileri yanlışlıkla sildikten sonra tek bir kiracının SQL veritabanını geri yüklemeyi öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 12/04/2018
ms.openlocfilehash: a54e8e5629f6f8ad688b6fe11bbf50fc038095bf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818521"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Tek bir kiracıyı kiracı başına veritabanı SaaS uygulamasıyla geri yükleme

Kiracı başına veritabanı modeli, tek bir kiracıyı diğer kiracıları etkilemeden önceki bir zaman noktasına geri yüklemeyi kolaylaştırır.

Bu öğreticide, iki veri kurtarma deseni öğrenirsiniz:

> [!div class="checklist"]
> * Bir veritabanını bir paralel veritabanına geri yükleme (yan yana).
> * Var olan veritabanını değiştirerek bir veritabanını yerinde geri yükleyin.

|||
|:--|:--|
| Paralel veritabanına geri yükleme | Bu model, bir kiracının verilerini önceki bir noktadan incelemeye izin vermek için İnceleme, denetim ve uyumluluk gibi görevler için kullanılabilir. Kiracının geçerli veritabanı çevrimiçi ve değişmemiş durumda kalır. |
| Yerinde geri yükleme | Bu model genellikle, bir kiracı yanlışlıkla verileri sildikten veya bozduktan sonra bir kiracıyı daha önceki bir noktaya kurtarmak için kullanılır. Özgün veritabanı satır dışı alınır ve geri yüklenen veritabanıyla değiştirilmiştir. |
|||

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Wingtip SaaS uygulaması dağıtıldı. Beş dakikadan kısa bir süre içinde dağıtmak için bkz. [Wingtip SaaS uygulamasını dağıtma ve araştırma](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell kullanmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>SaaS kiracısı geri yükleme desenlerine giriş

Tek bir kiracının verilerini geri yüklemek için iki basit desen vardır. Kiracı veritabanları birbirinden yalıtılmış olduğundan, bir kiracının geri yüklenmesi diğer kiracının verilerini etkilemez. Azure SQL veritabanı 'nın zaman içinde geri yükleme (ıNR) özelliği her iki desende kullanılır. SÜR her zaman yeni bir veritabanı oluşturur.

* **Paralel olarak geri yükle**: ilk düzende, kiracının geçerli veritabanı ile birlikte yeni bir paralel veritabanı oluşturulur. Daha sonra kiracıya geri yüklenen veritabanına salt okuma erişimi verilir. Geri yüklenen veriler incelenebilir ve geçerli veri değerlerinin üzerine yazmak için kullanılır. Bu, kiracının geri yüklenen veritabanına nasıl eriştiğini ve kurtarmaya yönelik seçeneklerin nasıl sağlandığını belirlemek için uygulama tasarımcısına sahiptir. Daha önceki bir noktada, kiracının verilerini incelemeye izin vermek, bazı senaryolarda gerekli olan tek bir işlem olabilir.

* **Yerinde geri yükleme**: ikinci model, verilerin kaybolması veya bozulması ve kiracının önceki bir noktaya geri dönmesi istiyorsa kullanışlıdır. Veritabanı geri yüklenirken kiracı, satır dışı alınır. Özgün veritabanı silinir ve geri yüklenen veritabanı yeniden adlandırılır. Özgün veritabanının yedekleme zinciri silme işleminden sonra erişilebilir kalır, bu nedenle veritabanını gerekirse daha önceki bir noktaya geri yükleyebilirsiniz.

Veritabanı [etkin coğrafi çoğaltma](sql-database-active-geo-replication.md) kullanıyorsa ve paralel olarak geri yüklenirse, geri yüklenen kopyadan gerekli verileri özgün veritabanına kopyalamanızı öneririz. Özgün veritabanını geri yüklenen veritabanıyla değiştirirseniz, Coğrafi çoğaltmayı yeniden yapılandırmanız ve yeniden eşitlenmesi gerekir.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Her kiracı uygulama komut dosyası için Wingtip bilet SaaS veritabanı 'nı alın

Wingtip biletleri SaaS Multitenant veritabanı betikleri ve uygulama kaynak kodu [Wingtipbilet ssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub deposunda mevcuttur. Wingtip bilet SaaS betikleri indirme ve engellemesini kaldırma adımları için bkz. [genel rehberlik](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Başlamadan önce

Bir veritabanı oluşturulduğunda, ilk tam yedeklemenin içinden geri yükleme için kullanılabilir hale gelmesi 10 ila 15 dakika sürebilir. Uygulamayı yeni yüklediyseniz, bu senaryoyu denemeden önce birkaç dakika beklemeniz gerekebilir.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Yanlışlıkla verileri silerek kiracının benzetimini yapın

Bu kurtarma senaryolarını göstermek için, ilk olarak "yanlışlıkla" Kiracı veritabanlarından birindeki bir olayı silin. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Geçerli olayları gözden geçirmek için olaylar uygulamasını açın

1. Olay Hub 'ını (http://events.wtp.&lt; User&gt;. trafficmanager.net) açın ve **contoso Concert Salı**' yi seçin.

   ![Olay Hub 'ı](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Olay listesini kaydırın ve listedeki son olayı bir yere iade edin.

   ![Son olay görünür](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>Son olayı "yanlışlıkla" Sil

1. PowerShell ıSE 'de,...\\öğrenme modüllerini\\Iş sürekliliği ve olağanüstü durum kurtarma\\RestoreTenant\\*demo-RestoreTenant. ps1*' i açın ve aşağıdaki değeri ayarlayın:

   * **$DemoScenario** = **1**, *son olayı Sil (Bilet satışları olmadan)* .
2. Betiği çalıştırmak için F5 tuşuna basın ve son olayı silin. Aşağıdaki onay iletisi görüntülenir:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Contoso olayları sayfası açılır. Aşağı kaydırın ve olayın kaybolduğunu doğrulayın. Olay hala listede ise, **Yenile** ' yi seçin ve onun devam ettiğini doğrulayın.
   ![son olay kaldırıldı](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Bir kiracı veritabanını üretim veritabanıyla paralel olarak geri yükleme

Bu alıştırma, contoso Concert Salı veritabanını, olay silinmeden önceki bir zamana geri yükler. Bu senaryo, bir paralel veritabanında silinen verileri gözden geçirmek istediğinizi varsayar.

 *Restore-TenantInParallel. ps1* betiği, paralel bir katalog girişi ile *ContosoConcertHall\_Old*adlı bir paralel Kiracı veritabanı oluşturur. Bu geri yükleme deseninin küçük bir veri kaybını kurtarmak için idealdir. Bu kalıbı, uyumluluk veya denetim amaçlarıyla verileri gözden geçirmeniz gerekiyorsa de kullanabilirsiniz. [Etkin coğrafi çoğaltma](sql-database-active-geo-replication.md)kullandığınızda önerilen yaklaşım önerilir.

1. [Yanlışlıkla verileri silme bir kiracının benzetimini](#simulate-a-tenant-accidentally-deleting-data) gerçekleştirin bölümü.
2. PowerShell ıSE 'de,...\\öğrenme modüllerini\\Iş sürekliliği ve olağanüstü durum kurtarma\\RestoreTenant\\_demo-RestoreTenant. ps1_.
3. **$DemoScenario** = **2**' i ayarlayın, *kiracıyı paralel olarak geri yükleyin*.
4. Betiği çalıştırmak için F5 tuşuna basın.

Betik, olayı silmeden önce, kiracı veritabanını zaman içinde bir noktaya geri yükler. Veritabanı, _ContosoConcertHall\_eski_adlı yeni bir veritabanına geri yüklendi. Bu geri yüklenen veritabanında bulunan katalog meta verileri silinir ve sonra *ContosoConcertHall\_eski* adından oluşturulan bir anahtar kullanılarak veritabanı kataloğa eklenir.

Demo betiği, tarayıcınızdaki bu yeni kiracı veritabanının Olaylar sayfasını açar. URL 'den, bu sayfanın *_old* adına eklendiği geri yüklenen veritabanından verileri gösterdiğine ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```.

Önceki bölümde silinen olayın geri yüklendiğini doğrulamak için tarayıcıda listelenen olayları kaydırın.

Geri yüklenen kiracıyı, kendi olay uygulamasıyla ek bir kiracı olarak ortaya çıkarmak, geri yüklenen verilere kiracı erişimi sağlama olasılığının düşüktür. Geri yükleme modelini göstermek için kullanılır. Genellikle, eski verilere salt okuma erişimi verirsiniz ve geri yüklenen veritabanını tanımlı bir süre için koruyabilirsiniz. Örnekte, _geri yüklenen kiracıyı kaldır_ senaryosunu çalıştırarak, geri yüklenen kiracı girişini silebilirsiniz.

1. **$DemoScenario** = **4**' ü ayarlayın, *geri yüklenen kiracıyı kaldırın*.
2. Betiği çalıştırmak için F5 tuşuna basın.
3. *ContosoConcertHall\_eski* girdisi artık katalogdan silinir. Tarayıcınızda bu kiracının Olaylar sayfasını kapatın.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Bir kiracıyı yerinde geri yükleyin, var olan kiracı veritabanını değiştirin

Bu alıştırma, contoso Concert salonu kiracısını olay silinmeden önceki bir noktaya geri yükler. *Restore-TenantInPlace* betiği, bir kiracı veritabanını yeni bir veritabanına geri yükler ve orijinali siler. Bu geri yükleme düzeninin önemli verilerin bozulmasını kurtarmak için idealdir ve kiracının önemli veri kaybına uyum sağlaması gerekebilir.

1. PowerShell ıSE 'de, **demo-RestoreTenant. ps1** dosyasını açın.
2. **$DemoScenario** = **5**' i ayarlayın, *kiracı 'yı yerinde geri yükleyin*.
3. Betiği çalıştırmak için F5 tuşuna basın.

Betik, kiracı veritabanını olay silinmeden önceki bir noktaya geri yükler. Daha fazla güncelleştirme yapılmasını engellemek için ilk olarak contoso Concert salonu kiracıyı bir satır alır. Ardından, geri yükleme noktasından geri yükleyerek paralel bir veritabanı oluşturulur. Geri yüklenen veritabanı, veritabanı adının mevcut Kiracı veritabanı adıyla çakışmayacağından emin olmak için zaman damgasıyla adlandırılır. Ardından, eski kiracı veritabanı silinir ve geri yüklenen veritabanı özgün veritabanı adı olarak yeniden adlandırılır. Son olarak, contoso Concert salonu, uygulamanın geri yüklenen veritabanına erişmesine izin verecek şekilde çevrimiçi hale getirilir.

Veritabanını, olay silinmeden önceki bir zaman noktasına başarıyla geri yükledi. **Olaylar** sayfası açıldığında, son olayın geri yüklendiğini onaylayın.

Veritabanını geri yükledikten sonra, ilk tam yedeklemenin yeniden geri yükleme için kullanılabilir hale gelmeden önce bir 10 ila 15 dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir veritabanını bir paralel veritabanına geri yükleme (yan yana).
> * Bir veritabanını yerinde geri yükleyin.

[Kiracı veritabanı şemasını yönet](saas-tenancy-schema-management.md) öğreticisini deneyin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Wingtip SaaS uygulaması üzerinde derleme yapan ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure SQL veritabanı ile iş sürekliliği 'ne genel bakış](sql-database-business-continuity.md)
* [SQL veritabanı yedeklemeleri hakkında bilgi edinin](sql-database-automated-backups.md)
