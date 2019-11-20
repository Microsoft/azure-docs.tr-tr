---
title: Azure Izleyici günlükleriyle SQL Data Sync izleme
description: Azure Izleyici günlüklerini kullanarak Azure SQL Data Sync izlemeyi öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 0ed0bd3544fff89c8230267e3d6d8826c5ae3c7c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114606"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Azure Izleyici günlükleriyle SQL Data Sync izleme 

SQL Data Sync etkinlik günlüğünü denetlemek ve hataları ve uyarıları algılamak için, daha önce Azure portal SQL Data Sync el ile kontrol etmeniz veya PowerShell ya da REST API kullanmanız gerekiyordu. Veri eşitleme izleme deneyimini geliştiren özel bir çözüm yapılandırmak için bu makaledeki adımları izleyin. Bu çözümü, senaryonuza uyacak şekilde özelleştirebilirsiniz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

SQL Data Sync hizmetine genel bakış için bkz. [Azure SQL Data Sync ile birden fazla bulut ve şirket içi veritabanı arasında veri eşitleme](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync Şu anda Azure SQL veritabanı yönetilen **örneğini desteklemez.**

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Tüm eşitleme gruplarınız için panoyu izleme 

Sorunları bulmak için, her bir eşitleme grubunun günlüklerine tek tek bakmamız gerekmez. Özel bir Azure Izleyici görünümü kullanarak aboneliklerinizden herhangi birindeki tüm eşitleme gruplarınızı tek bir yerde izleyebilirsiniz. Bu görünüm, müşterileri SQL Data Sync için önemli olan bilgileri yüzeyler.

![Veri eşitleme izleme panosu](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Otomatik e-posta bildirimleri

Artık Azure portal veya PowerShell ya da REST API aracılığıyla günlüğü el ile denetlemeniz gerekmez. [Azure izleyici günlükleri](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)ile, bir hata oluştuğunda onları görmeniz gereken kişilerin e-posta adreslerine doğrudan gidecek uyarılar oluşturabilirsiniz.

![Veri eşitleme e-posta bildirimleri](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Bu izleme özelliklerini nasıl ayarlayabilirim? 

Aşağıdaki işlemleri gerçekleştirerek bir saatten daha az SQL Data Sync için özel bir Azure Izleyici günlüğü izleme çözümü uygulayın:

Üç bileşeni yapılandırmanız gerekir:

-   Azure Izleyici günlüklerine SQL Data Sync günlük verilerini akışa almak için bir PowerShell runbook 'u.

-   E-posta bildirimleri için bir Azure Izleyici uyarısı.

-   İzleme için bir Azure Izleyici görünümü.

### <a name="samples-to-download"></a>İndirilecek örnekler

Aşağıdaki iki örneği indirin:

-   [Veri eşitleme günlüğü PowerShell runbook 'U](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Veri eşitleme Azure Izleyici görünümü](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki şeyleri ayarladığınızdan emin olun:

-   Bir Azure Otomasyonu hesabı

-   Log Analytics çalışma alanı

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>SQL Data Sync günlüğü almak için PowerShell runbook 'U 

Azure Otomasyonu 'nda barındırılan bir PowerShell runbook 'u kullanarak SQL Data Sync günlük verilerini çekin ve Azure Izleyici günlüklerine gönderin. Örnek bir betik dahildir. Bir önkoşul olarak, bir Azure Otomasyonu hesabına sahip olmanız gerekir. Ardından bir runbook oluşturmanız ve çalışacak şekilde zamanlamanız gerekir. 

### <a name="create-a-runbook"></a>Runbook oluşturma

Runbook oluşturma hakkında daha fazla bilgi için bkz. [Ilk PowerShell runbook 'Um](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Azure Otomasyonu hesabınız altında, Işlem Otomasyonu altında **runbook 'lar** sekmesini seçin.

2.  Runbook 'Lar sayfasının sol üst köşesindeki **runbook Ekle** ' yi seçin.

3.  **Mevcut bir runbook 'U Içeri aktar**' ı seçin.

4.  **Runbook dosyası**altında, verilen `DataSyncLogPowerShellRunbook` dosyasını kullanın. **Runbook türünü** `PowerShell`olarak ayarlayın. Runbook 'a bir ad verin.

5.  **Oluştur**'u seçin. Artık bir runbook 'a sahipsiniz.

6.  Azure Otomasyonu hesabınız altında, paylaşılan kaynaklar ' ın altındaki **değişkenler** sekmesini seçin.

7.  Değişkenler sayfasında **değişken Ekle** ' yi seçin. Runbook 'un son yürütme süresini depolamak için bir değişken oluşturun. Birden çok runbook 'unuz varsa her runbook için bir değişkene ihtiyacınız vardır.

8.  Değişken adını `DataSyncLogLastUpdatedTime` olarak ayarlayın ve türünü DateTime olarak ayarlayın.

9.  Runbook 'u seçin ve sayfanın üst kısmındaki Düzenle düğmesine tıklayın.

10. Hesabınız ve SQL Data Sync yapılandırmanız için gerekli değişiklikleri yapın. (Daha ayrıntılı bilgi için bkz. örnek komut dosyası.)

    1.  Azure bilgileri.

    2.  Eşitleme grubu bilgileri.

    3.  Azure Izleyici günlük bilgileri. Bu bilgileri Azure portalında bulun | Ayarlar | Bağlı kaynaklar. Azure Izleyici günlüklerine veri gönderme hakkında daha fazla bilgi için bkz. [http veri toplayıcı API 'si (Önizleme) Ile Azure izleyici günlüklerine veri gönderme](../azure-monitor/platform/data-collector-api.md).

11. Runbook 'u test bölmesinde çalıştırın. Başarılı olduğundan emin olmak için denetleyin.

    Hatalar varsa, en son PowerShell modülünün yüklü olduğundan emin olun. En son PowerShell modülünü Otomasyon hesabınızdaki **modüller galerisine** yükleyebilirsiniz.

12. **Yayımla** ' ya tıklayın

### <a name="schedule-the-runbook"></a>Runbook 'u zamanlama

Runbook 'u zamanlamak için:

1.  Runbook altında kaynaklar ' ın altında **zamanlamalar** sekmesini seçin.

2.  Zamanlamalar sayfasında **zamanlama Ekle ' yi** seçin.

3.  **Runbook 'a bir zamanlama bağla**' yı seçin.

4.  **Yeni zamanlama oluştur** ' u seçin.

5.  **Yinelemeyi** yineleme olarak ayarlayın ve istediğiniz aralığı ayarlayın. Burada, komut dosyasında ve Azure Izleyici günlüklerinde aynı aralığı kullanın.

6.  **Oluştur**'u seçin.

### <a name="check-the-automation"></a>Otomasyonu denetleyin

Otomasyonunun beklendiği gibi çalışıp çalışmadığını izlemek için, Otomasyon hesabınız için **genel bakış** ' ın altında, **Izleme**altında **iş istatistikleri** görünümünü bulun. Kolay görüntülenmek üzere bu görünümü panonuza sabitleyin. Runbook 'un başarılı çalıştırmaları "tamamlandı" olarak gösterilir ve başarısız çalıştırmalar "başarısız" olarak gösterilir.

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>E-posta bildirimleri için bir Azure Izleyici okuyucusu uyarısı oluşturun

Azure Izleyici günlüklerini kullanan bir uyarı oluşturmak için aşağıdaki işlemleri yapın. Bir önkoşul olarak, Azure Izleyici günlüklerinin bir Log Analytics çalışma alanıyla bağlantılı olması gerekir.

1.  Azure portal **günlük araması**' nı seçin.

2.  Seçtiğiniz Aralık dahilinde eşitleme grubuna göre hataları ve uyarıları seçmek için bir sorgu oluşturun. Örneğin:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Sorguyu çalıştırdıktan sonra, **Uyarı**yazan zili seçin.

4.  **Uyarı oluştur**' un altında **ölçüm ölçümü**' ni seçin.

    1.  Birleşik değeri **değerinden büyük**olarak ayarlayın.

    2.  **Şundan büyük**olduktan sonra, bildirim almadan önce geçmesi gereken eşiği girin. Veri eşitlemede geçici hatalar beklenmektedir. Paraziti azaltmak için eşiği 5 olarak ayarlayın.

5.  **Eylemler**altında, **e-posta bildirimini** "Evet" olarak ayarlayın. İstenen e-posta alıcılarını girin.

6.  **Kaydet**’e tıklayın. Belirtilen alıcılar artık hata oluştuğunda e-posta bildirimleri alıyor.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Izleme için bir Azure Izleyici görünümü oluşturma

Bu adım, belirtilen tüm eşitleme gruplarını görsel olarak izlemek için bir Azure Izleyici görünümü oluşturur. Görünüm çeşitli bileşenler içerir:

-   Tüm eşitleme gruplarının kaç hata, başarı ve uyarı olduğunu gösteren bir genel bakış kutucuğu.

-   Eşitleme grubu başına hata ve uyarı sayısını gösteren tüm eşitleme grupları için bir kutucuk. Sorun olmayan gruplar bu kutucukta görünmez.

-   Her bir eşitleme grubu için, hata, başarı ve uyarı sayısını ve en son hata iletilerini gösteren bir kutucuk.

Azure Izleyici görünümünü yapılandırmak için aşağıdaki işlemleri yapın:

1.  Log Analytics çalışma alanı giriş sayfasında sol taraftaki artı ' yi seçerek **Görünüm tasarımcısını**açın.

2.  Görünüm tasarımcısının üst çubuğunda **Içeri aktar** ' ı seçin. Ardından "DataSyncLogOMSView" örnek dosyasını seçin.

3.  Örnek görünüm iki eşitleme grubunu yönetmek içindir. Bu görünümü senaryonuza uyacak şekilde düzenleyin. **Düzenle** ' ye tıklayın ve aşağıdaki değişiklikleri yapın:

    1.  Galeriden gerektiği şekilde yeni "halka & listesi" nesneleri oluşturun.

    2.  Her kutucukta sorguları bilgilerinizi kullanarak güncelleştirin.

        1.  Her kutucukta TimeStamp_t aralığını istediğiniz gibi değiştirin.

        2.  Her bir eşitleme grubunun kutucukları üzerinde, eşitleme grubu adlarını güncelleştirin.

    3.  Her kutucukta başlığı gerektiği şekilde güncelleştirin.

4.  **Kaydet** ' e tıklayın ve görünüm hazırlayın.

## <a name="cost-of-this-solution"></a>Bu çözümün maliyeti

Çoğu durumda bu çözüm ücretsizdir.

**Azure Otomasyonu:** Kullanımınıza bağlı olarak, Azure Otomasyonu hesabının bir maliyeti olabilir. Ayda ilk 500 dakikalık iş çalıştırma süresi ücretsizdir. Çoğu durumda, bu çözümün ayda 500 dakikadan kısa bir süre kullanması beklenir. Ücretlerden kaçınmak için, runbook 'u iki saat veya daha uzun bir zaman aralığında çalışacak şekilde zamanlayın. Daha fazla bilgi için bkz. [Otomasyon Fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).

**Azure izleyici günlükleri:** Kullanımınıza bağlı olarak Azure Izleyici günlükleriyle ilişkili bir maliyet olabilir. Ücretsiz katman, günde 500 MB/saniye veri içerir. Çoğu durumda, bu çözümün günde 500 MB 'tan az olması beklenir. Kullanımı azaltmak için, runbook 'a dahil edilen hata filtrelemeyi kullanın. Günde 500 MB 'tan fazla kullanıyorsanız, sınırlamaya ulaşıldığında analiz durdurma riskini önlemek için ücretli katmana yükseltin. Daha fazla bilgi için bkz. [Azure izleyici günlükleri fiyatlandırması](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Kod örnekleri

Bu makalede açıklanan kod örneklerini aşağıdaki konumlardan indirin:

-   [Veri eşitleme günlüğü PowerShell runbook 'U](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Veri eşitleme Azure Izleyici görünümü](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Sonraki adımlar
SQL Data Sync hakkında daha fazla bilgi için bkz.:

-   Genel Bakış- [Azure SQL Data Sync ile birden çok bulut ve şirket içi veritabanı arasında veri eşitleme](sql-database-sync-data.md)
-   Veri eşitlemesini ayarlama
    - Portalda- [öğreticide, Azure SQL veritabanı ve şirket içi SQL Server arasında veri eşitlemek için SQL Data Sync ayarlama](sql-database-get-started-sql-data-sync.md)
    - PowerShell ile
        -  [PowerShell kullanarak birden çok Azure SQL veritabanı arasında eşitleme](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell kullanarak bir Azure SQL Veritabanı ile SQL Server şirket içi veritabanı arasında eşitleme](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Veri Eşitleme Aracısı - [veri Aracısı Azure SQL Data Sync için eşitleme](sql-database-data-sync-agent.md)
-   En iyi uygulamalar- [Azure SQL Data Sync Için en iyi yöntemler](sql-database-best-practices-data-sync.md)
-   Sorun giderme- [Azure SQL Data Sync sorunlarını giderme](sql-database-troubleshoot-data-sync.md)
-   Eşitleme şemasını güncelleştirme
    -   Transact-SQL- [Azure SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatikleştirin](sql-database-update-sync-schema.md)
    -   PowerShell ile- [varolan bir eşitleme grubundaki eşitleme şemasını güncelleştirmek Için PowerShell kullanın](scripts/sql-database-sync-update-schema.md)

SQL Veritabanı hakkında daha fazla bilgi için bkz.:

-   [SQL Veritabanı'na Genel Bakış](sql-database-technical-overview.md)
-   [Veritabanı Yaşam Döngüsü Yönetimi](https://msdn.microsoft.com/library/jj907294.aspx)
