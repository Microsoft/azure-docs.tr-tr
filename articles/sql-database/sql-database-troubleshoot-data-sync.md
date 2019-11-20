---
title: SQL Data Sync sorunlarını giderme
description: Azure SQL Data Sync ile ilgili sık karşılaşılan sorunları nasıl giderebileceğinizi öğrenin.
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
ms.openlocfilehash: 8e223d0c6243bfddc1e5a56867c4c69de5e2a62e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822447"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>SQL Data Sync sorunlarını giderme

Bu makalede, Azure SQL Data Sync ile ilgili bilinen sorunların nasıl giderileceği açıklanmaktadır. Bir sorun için çözüm varsa, burada sağlanır.

SQL Data Sync hizmetine genel bakış için bkz. [Azure SQL Data Sync ile birden fazla bulut ve şirket içi veritabanı arasında veri eşitleme](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync Şu anda Azure SQL veritabanı yönetilen **örneğini desteklemez.**

## <a name="sync-issues"></a>Eşitleme sorunları

- [İstemci aracısıyla ilişkili şirket içi veritabanları için Portal Kullanıcı arabiriminde eşitleme başarısız oluyor](#sync-fails)

- [Eşitleme grubum işleme durumunda takıldı](#sync-stuck)

- [Tablomda hatalı veri görüyorum](#sync-baddata)

- [Başarılı bir eşitlemeden sonra tutarsız birincil anahtar verileri görüyorum](#sync-pkdata)

- [Performans açısından önemli bir düşme görüyorum](#sync-perf)

- [Şu iletiyi görüyorum: "NULL değeri sütun \<sütununa eklenemiyor >. Sütun null değerlere izin vermiyor. " Bu ne anlama geliyor ve nasıl çözebilirim?](#sync-nulls)

- [Veri eşitleme, dairesel başvuruları nasıl işler? Diğer bir deyişle, aynı veriler birden çok eşitleme grubunda eşitlendiğinde ve sonuç olarak değişene devam ediyor mu?](#sync-circ)

### <a name="sync-fails"></a>İstemci aracısıyla ilişkili şirket içi veritabanları için Portal Kullanıcı arabiriminde eşitleme başarısız oluyor

İstemci aracısıyla ilişkili şirket içi veritabanları için SQL Data Sync Portal Kullanıcı arabiriminde eşitleme başarısız olur. Aracıyı çalıştıran yerel bilgisayarda, olay günlüğünde System. ıO. IOException hatalarını görürsünüz. Hatalar, diskte yeterli alan olduğunu söylüyor.

- **Neden**. Sürücüde yeterli alan yok.

- **Çözümleme**. % TEMP% dizininin bulunduğu sürücüde daha fazla alan oluşturun.

### <a name="sync-stuck"></a>Eşitleme grubum işleme durumunda takıldı

SQL Data Sync içindeki bir eşitleme grubu uzun süredir işleme durumunda. **Stop** komutuna yanıt vermez ve Günlükler yeni giriş göstermez.

Aşağıdaki koşullardan herhangi biri, bir eşitleme grubunun işleme durumunda takılmasına yol açabilir:

- **Neden**. İstemci aracısı çevrimdışı

- **Çözümleme**. İstemci aracısının çevrimiçi olduğundan emin olun ve işlemi yeniden deneyin.

- **Neden**. İstemci aracısı yüklü değil veya eksik.

- **Çözümleme**. İstemci aracısı yüklü değilse veya eksikse:

    1. Aracı XML dosyası mevcutsa, dosyayı SQL Data Sync yükleme klasöründen kaldırın.
    1. Aracıyı bir şirket içi bilgisayara yükleyin (aynı bilgisayar veya farklı bir bilgisayar olabilir). Ardından, portalda çevrimdışı olarak gösterilen aracı için oluşturulan aracı anahtarını gönderin.

- **Neden**. SQL Data Sync hizmeti durduruldu.

- **Çözümleme**. SQL Data Sync hizmetini yeniden başlatın.

    1. **Başlat** menüsünde, **Hizmetler**' i arayın.
    1. Arama sonuçlarında **Hizmetler**' i seçin.
    1. **SQL Data Sync** hizmetini bulun.
    1. Hizmet durumu **durdurulmuşsa**, hizmet adına sağ tıklayın ve ardından **Başlat**' ı seçin.

> [!NOTE]
> Yukarıdaki bilgiler eşitleme grubunuzu işleme durumundan taşımazsa, Microsoft Desteği eşitleme grubunuzun durumunu sıfırlayabilir. Eşitleme grubunuzun durumunu sıfırlamasını sağlamak için [Azure SQL veritabanı forumundaki](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)bir gönderi oluşturun. Gönderinizden, sıfırlanması gereken grubun abonelik KIMLIĞINI ve eşitleme grubu KIMLIĞINI dahil edin. Microsoft Desteği mühendis gönderinize yanıt verir ve durumun ne zaman sıfırlandığını size verir.

### <a name="sync-baddata"></a>Tablomda hatalı veri görüyorum

Aynı ada sahip olan ancak farklı veritabanı şemalarından gelen tablolar bir eşitlemeye dahil ise, eşitlemeden sonra tablolardaki hatalı verileri görürsünüz.

- **Neden**. SQL Data Sync sağlama işlemi, aynı ada sahip ancak farklı şemalarda bulunan tablolar için aynı izleme tablolarını kullanır. Bu nedenle, her iki tablodaki değişiklikler aynı izleme tablosuna yansıtılmıştır. Bu, eşitleme sırasında hatalı veri değişikliklerine neden olur.

- **Çözümleme**. Tablolar bir veritabanındaki farklı şemalara ait olsa bile, bir eşitlemede yer alan tabloların adlarının farklı olduğundan emin olun.

### <a name="sync-pkdata"></a>Başarılı bir eşitlemeden sonra tutarsız birincil anahtar verileri görüyorum

Eşitleme başarılı olarak bildirilir ve günlük başarısız veya Atlanan satırları gösterir, ancak birincil anahtar verilerinin eşitleme grubundaki veritabanları arasında tutarsız olduğunu gözlemleyebilirsiniz.

- **Neden**. Bu sonuç tasarıma göre yapılır. Birincil anahtar sütunundaki değişiklikler, birincil anahtarın değiştirildiği satırlarda tutarsız veriler oluşmasına neden oldu.

- **Çözümleme**. Bu sorunu engellemek için, birincil anahtar sütununda hiçbir veri değişmediğinden emin olun. Bu sorunu gerçekleştirdikten sonra onarmak için, eşitleme grubundaki tüm uç noktaların tutarsız verileri olan satırı silin. Sonra satırı yeniden ekleyin.

### <a name="sync-perf"></a>Performans açısından önemli bir düşme görüyorum

Performansı önemli ölçüde düşürür, muhtemelen veri eşitleme Kullanıcı arabirimini bile açamazsınız.

- **Neden**. En olası neden bir eşitleme döngüsüdür. Eşitleme grubu tarafından eşitleme, A eşitleme grubu B tarafından eşitleme tetiklediği zaman bir eşitleme döngüsü meydana gelir ve ardından eşitleme grubu A ile eşitleme tetikler. Asıl durum daha karmaşık olabilir ve döngüde ikiden fazla eşitleme grubu içerebilir. Bu sorun, bir eşitlemeden çakışan eşitleme gruplarının neden olduğu bir eşitlemenin dairesel olarak tetiklenmesi olabilir.

- **Çözümleme**. En iyi çözüm engeldir. Eşitleme gruplarında döngüsel başvurular olmadığından emin olun. Bir eşitleme grubu tarafından eşitlenen herhangi bir satır başka bir eşitleme grubu tarafından eşitlenemez.

### <a name="sync-nulls"></a>Şu iletiyi görüyorum: "NULL değeri sütun \<sütununa eklenemiyor >. Sütun null değerlere izin vermiyor. " Bu ne anlama geliyor ve nasıl çözebilirim? 
Bu hata iletisi aşağıdaki iki sorunlardan birinin oluştuğunu gösterir:
-  Tablonun birincil anahtarı yok. Bu sorunu onarmak için, eşitmekte olduğunuz tüm tablolara birincil bir anahtar ekleyin.
-  CREATE INDEX deyiminizde WHERE yan tümcesi vardır. Veri eşitleme bu koşulu işlemez. Bu sorunu onarmak için WHERE yan tümcesini kaldırın veya değişiklikleri tüm veritabanlarında el ile yapın. 
 
### <a name="sync-circ"></a>Veri eşitleme, dairesel başvuruları nasıl işler? Diğer bir deyişle, aynı veriler birden çok eşitleme grubunda eşitlendiğinde ve sonuç olarak değişene devam ediyor mu?
Veri eşitleme döngüsel başvuruları işlemez. Kaçındığınızdan emin olun. 

## <a name="client-agent-issues"></a>İstemci Aracısı sorunları

İstemci aracısıyla ilgili sorunları gidermek için bkz. [veri eşitleme Aracısı sorunlarını giderme](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Kurulum ve bakım sorunları

- ["Disk dışı alan" iletisi alıyorum](#setup-space)

- [Eşitleme grubumu silemiyorum](#setup-delete)

- [Şirket içi SQL Server veritabanının kaydını silemiyor](#setup-unreg)

- [Sistem hizmetlerini başlatmak için yeterli ayrıcalıklara sahip değil](#setup-perms)

- [Bir veritabanında "güncel olmayan" durumu vardır](#setup-date)

- [Bir eşitleme grubunda "güncel olmayan" durum bulunur](#setup-date2)

- [Bir eşitleme grubu, aracıyı kaldırmak veya durdurmak için üç dakika içinde silinemez](#setup-delete2)

- [Kayıp veya bozuk bir veritabanını geri yüklerken ne olur?](#setup-restore)

### <a name="setup-space"></a>"Disk dışı alan" iletisi alıyorum

- **Neden**. Soltover dosyalarının silinmesi gerekiyorsa "disk alanı yetersiz" iletisi görünebilir. Bu, virüsten koruma yazılımından kaynaklanıyor olabilir veya silme işlemleri denendiğinde dosyalar açık olur.

- **Çözümleme**. % Temp% klasöründe (`del \*sync\* /s`) bulunan eşitleme dosyalarını el ile silin. Ardından,% Temp% klasöründeki alt dizinleri silin.

> [!IMPORTANT]
> Eşitleme sürerken hiçbir dosyayı silmeyin.

### <a name="setup-delete"></a>Eşitleme grubumu silemiyorum

Bir eşitleme grubunu silme denemeniz başarısız olur. Aşağıdaki senaryolardan herhangi biri, bir eşitleme grubunun silinmesine neden olabilecek bir hata oluşabilir:

- **Neden**. İstemci aracısı çevrimdışı.

- **Çözümleme**. İstemci aracısının çevrimiçi olduğundan emin olup yeniden deneyin.

- **Neden**. İstemci aracısı yüklü değil veya eksik.

- **Çözümleme**. İstemci aracısı yüklü değilse veya eksikse:  
    a. Aracı XML dosyası mevcutsa, dosyayı SQL Data Sync yükleme klasöründen kaldırın.  
    b. Aracıyı bir şirket içi bilgisayara yükleyin (aynı bilgisayar veya farklı bir bilgisayar olabilir). Ardından, portalda çevrimdışı olarak gösterilen aracı için oluşturulan aracı anahtarını gönderin.

- **Neden**. Bir veritabanı çevrimdışı.

- **Çözümleme**. SQL veritabanlarınızın ve SQL Server veritabanlarının tümünün çevrimiçi olduğundan emin olun.

- **Neden**. Eşitleme grubu sağlanıyor veya eşitleniyor.

- **Çözümleme**. Sağlama veya eşitleme işlemi bitene kadar bekleyin ve ardından eşitleme grubunu silmeyi yeniden deneyin.

### <a name="setup-unreg"></a>Şirket içi SQL Server veritabanının kaydını silemiyor

- **Neden**. Büyük olasılıkla, zaten silinmiş olan bir veritabanının kaydını silmeye çalışıyorsunuz.

- **Çözümleme**. Şirket içi SQL Server veritabanının kaydını silmek için veritabanını seçin ve sonra **silmeyi zorla**' yı seçin.

  Bu işlem veritabanını eşitleme grubundan kaldıramazsa:

  1. İstemci Aracısı ana bilgisayar hizmetini durdurup yeniden başlatın:  
    a. **Başlat** menüsünü seçin.  
    b. Arama kutusuna **Services. msc**yazın.  
    c. Arama sonuçları bölmesinin **Programlar** bölümünde **Hizmetler**' e çift tıklayın.  
    d. **SQL Data Sync** hizmetine sağ tıklayın.  
    e. Hizmet çalışıyorsa, uygulamayı durdurun.  
    f. Hizmete sağ tıklayın ve ardından **Başlat**' ı seçin.  
    g. Veritabanının hala kayıtlı olup olmadığını denetleyin. Artık kayıtlı değilse işiniz yapılır. Aksi halde, bir sonraki adımla devam edin.
  1. İstemci Aracısı uygulamasını açın (SqlAzureDataSyncAgent).
  1. **Kimlik bilgilerini düzenle**' yi seçin ve ardından veritabanı için kimlik bilgilerini girin.
  1. Kayıt silme işlemine devam edin.

### <a name="setup-perms"></a>Sistem hizmetlerini başlatmak için yeterli ayrıcalıklara sahip değil

- **Neden**. Bu hata iki durumda oluşur:
  -   Kullanıcı adı ve/veya parola hatalı.
  -   Belirtilen kullanıcı hesabı, hizmet olarak oturum açmak için yeterli ayrıcalıklara sahip değil.

- **Çözümleme**. Kullanıcı hesabına hizmet olarak oturum açma kimlik bilgileri verin:

  1.  > **Denetim Masası** ' **na gidin** > **Yönetim araçları** > yerel **güvenlik ilkesi** > **yerel ilke** > **Kullanıcı Rights Management**.
  1. **Hizmet olarak oturum aç '** ı seçin.
  1. **Özellikler** iletişim kutusunda Kullanıcı hesabını ekleyin.
  1. **Uygula**’yı ve sonra **Tamam**’ı seçin.
  1. Tüm pencereleri kapatın.

### <a name="setup-date"></a>Bir veritabanında "güncel olmayan" durumu vardır

- **Neden**. SQL Data Sync, hizmetten 45 gün veya daha fazlası için çevrimdışı olan veritabanlarını kaldırır (veritabanının çevrimdışı olduğu zamandan itibaren sayılır). Bir veritabanı 45 gün veya daha uzun bir süre sonra yeniden çevrimiçi duruma gelirse, durumu **güncel**değildir.

- **Çözümleme**. Veritabanlarının hiçbirinin 45 gün veya daha uzun süre çevrimdışı çalışmamasını sağlayarak **güncel** olmayan bir durumu önleyebilirsiniz.

  Veritabanının durumu **güncel**değilse:

  1. Eşitleme grubundan **güncel** olmayan bir durumu olan veritabanını kaldırın.
  1. Veritabanını yeniden eşitleme grubuna ekleyin.

  > [!WARNING]
  > Çevrimdışıyken bu veritabanında yapılan tüm değişiklikleri kaybedersiniz.

### <a name="setup-date2"></a>Bir eşitleme grubunda "güncel olmayan" durum bulunur

- **Neden**. Bir veya daha fazla değişiklik 45 günlük bekletme dönemi boyunca uygulanmayabilir, bir eşitleme grubu güncel hale gelebilir.

- **Çözümleme**. Bir eşitleme grubunun **güncel** durumundan kaçınmak için, geçmiş görüntüleyicisinde eşitleme işlerinizin sonuçlarını düzenli olarak inceleyin. Uygulanmanıza neden olan değişiklikleri araştırın ve çözümleyin.

  Eşitleme grubunun durumu **güncel**değilse, eşitleme grubunu silip yeniden oluşturun.

### <a name="setup-delete2"></a>Bir eşitleme grubu, aracıyı kaldırmak veya durdurmak için üç dakika içinde silinemez

İlişkili SQL Data Sync istemci aracısını kaldırmak veya durdurmak için bir eşitleme grubunu üç dakika içinde silemezsiniz.

- **Çözümleme**.

  1. İlişkili eşitleme aracıları çevrimiçi iken bir eşitleme grubunu kaldırın (önerilir).
  1. Aracı çevrimdışıysa, ancak yüklüyse şirket içi bilgisayarda çevrimiçi duruma getirin. SQL Data Sync portalında aracının durumunu **çevrimiçi** olarak görünmesini bekleyin. Ardından, eşitleme grubunu kaldırın.
  1. Kaldırılmış olduğundan, aracı çevrimdışıysa:  
    a.  Aracı XML dosyası mevcutsa, dosyayı SQL Data Sync yükleme klasöründen kaldırın.  
    b.  Aracıyı bir şirket içi bilgisayara yükleyin (aynı bilgisayar veya farklı bir bilgisayar olabilir). Ardından, portalda çevrimdışı olarak gösterilen aracı için oluşturulan aracı anahtarını gönderin.  
    c. Eşitleme grubunu silmeyi deneyin.

### <a name="setup-restore"></a>Kayıp veya bozuk bir veritabanını geri yüklerken ne olur?

Bir yedeklemeden kayıp veya bozuk bir veritabanını geri yüklerseniz, veritabanının ait olduğu eşitleme gruplarında veri yakınsaması olabilir.

## <a name="next-steps"></a>Sonraki adımlar
SQL Data Sync hakkında daha fazla bilgi için bkz.:

-   Genel Bakış- [Azure SQL Data Sync ile birden çok bulut ve şirket içi veritabanı arasında veri eşitleme](sql-database-sync-data.md)
-   Veri eşitlemesini ayarlama
    - Portalda- [öğreticide, Azure SQL veritabanı ve şirket içi SQL Server arasında veri eşitlemek için SQL Data Sync ayarlama](sql-database-get-started-sql-data-sync.md)
    - PowerShell ile
        -  [PowerShell kullanarak birden çok Azure SQL veritabanı arasında eşitleme](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell kullanarak bir Azure SQL Veritabanı ile SQL Server şirket içi veritabanı arasında eşitleme](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Veri eşitleme Aracısı- [Azure SQL Data Sync Için veri eşitleme Aracısı](sql-database-data-sync-agent.md)
-   En iyi uygulamalar- [Azure SQL Data Sync Için en iyi yöntemler](sql-database-best-practices-data-sync.md)
-   İzleyici- [Azure izleyici günlükleri ile izleyici SQL Data Sync](sql-database-sync-monitor-oms.md)
-   Eşitleme şemasını güncelleştirme
    -   Transact-SQL- [Azure SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatikleştirin](sql-database-update-sync-schema.md)
    -   PowerShell ile- [varolan bir eşitleme grubundaki eşitleme şemasını güncelleştirmek Için PowerShell kullanın](scripts/sql-database-sync-update-schema.md)

SQL veritabanı hakkında daha fazla bilgi için bkz.

-   [SQL Veritabanı'na Genel Bakış](sql-database-technical-overview.md)
-   [Veritabanı Yaşam Döngüsü Yönetimi](https://msdn.microsoft.com/library/jj907294.aspx)
