---
title: SQL Data Sync sorunlarını giderme
description: Azure 'da SQL Data Sync ile ilgili sık karşılaşılan sorunları belirleme, sorun giderme ve çözümleme hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: 02eaec4c86c934e8d2638de1b60aa9267babf7a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790177"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>SQL Data Sync ile ilgili sorunları giderme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, Azure 'da SQL Data Sync ile ilgili bilinen sorunların nasıl giderileceği açıklanmaktadır. Bir sorun için çözüm varsa, burada sağlanır.

SQL Data Sync genel bir bakış için bkz. [Azure 'da SQL Data Sync ile birden çok bulutta ve şirket içi veritabanlarında veri eşitleme](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> SQL Data Sync Şu anda Azure SQL yönetilen **örneğini desteklemez.**

## <a name="sync-issues"></a>Eşitleme sorunları

- [İstemci aracısıyla ilişkili şirket içi veritabanları için Portal Kullanıcı arabiriminde eşitleme başarısız oluyor](#sync-fails)

- [Eşitleme grubum işleme durumunda takıldı](#sync-stuck)

- [Tablomda hatalı veri görüyorum](#sync-baddata)

- [Başarılı bir eşitlemeden sonra tutarsız birincil anahtar verileri görüyorum](#sync-pkdata)

- [Performans açısından önemli bir düşme görüyorum](#sync-perf)

- [Şu iletiyi görüyorum: "sütuna NULL değer eklenemiyor \<column> . Sütun null değerlere izin vermiyor. " Bu ne anlama geliyor ve nasıl çözebilirim?](#sync-nulls)

- [Veri eşitleme, dairesel başvuruları nasıl işler? Diğer bir deyişle, aynı veriler birden çok eşitleme grubunda eşitlendiğinde ve sonuç olarak değişene devam ediyor mu?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> İstemci aracısıyla ilişkili şirket içi veritabanları için Portal Kullanıcı arabiriminde eşitleme başarısız oluyor

İstemci aracısıyla ilişkili şirket içi veritabanları için SQL Data Sync Portal Kullanıcı arabiriminde eşitleme başarısız olur. Aracıyı çalıştıran yerel bilgisayarda, olay günlüğünde System. ıO. IOException hatalarını görürsünüz. Hatalar, diskte yeterli alan olduğunu söylüyor.

- **Neden**. Sürücüde yeterli alan yok.

- **Çözümleme**. % TEMP% dizininin bulunduğu sürücüde daha fazla alan oluşturun.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> Eşitleme grubum işleme durumunda takıldı

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
> Yukarıdaki bilgiler eşitleme grubunuzu işleme durumundan taşımazsa, Microsoft Desteği eşitleme grubunuzun durumunu sıfırlayabilir. Eşitleme grubunuz durumunu sıfırlamanıza sahip olmak için, [Azure SQL veritabanı Için Microsoft Q&soru sayfasında](/answers/topics/azure-sql-database.html)bir gönderi oluşturun. Gönderinizden, sıfırlanması gereken grubun abonelik KIMLIĞINI ve eşitleme grubu KIMLIĞINI dahil edin. Microsoft Desteği mühendis gönderinize yanıt verir ve durumun ne zaman sıfırlandığını size verir.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> Tablomda hatalı veri görüyorum

Aynı ada sahip olan ancak farklı veritabanı şemalarından gelen tablolar bir eşitlemeye dahil ise, eşitlemeden sonra tablolardaki hatalı verileri görürsünüz.

- **Neden**. SQL Data Sync sağlama işlemi, aynı ada sahip ancak farklı şemalarda bulunan tablolar için aynı izleme tablolarını kullanır. Bu nedenle, her iki tablodaki değişiklikler aynı izleme tablosuna yansıtılmıştır. Bu, eşitleme sırasında hatalı veri değişikliklerine neden olur.

- **Çözümleme**. Tablolar bir veritabanındaki farklı şemalara ait olsa bile, bir eşitlemede yer alan tabloların adlarının farklı olduğundan emin olun.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a> Başarılı bir eşitlemeden sonra tutarsız birincil anahtar verileri görüyorum

Eşitleme başarılı olarak bildirilir ve günlük başarısız veya Atlanan satırları gösterir, ancak birincil anahtar verilerinin eşitleme grubundaki veritabanları arasında tutarsız olduğunu gözlemleyebilirsiniz.

- **Neden**. Bu sonuç tasarıma göre yapılır. Birincil anahtar sütunundaki değişiklikler, birincil anahtarın değiştirildiği satırlarda tutarsız veriler oluşmasına neden oldu.

- **Çözümleme**. Bu sorunu engellemek için, birincil anahtar sütununda hiçbir veri değişmediğinden emin olun. Bu sorunu gerçekleştirdikten sonra onarmak için, eşitleme grubundaki tüm uç noktaların tutarsız verileri olan satırı silin. Sonra satırı yeniden ekleyin.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> Performans açısından önemli bir düşme görüyorum

Performansı önemli ölçüde düşürür, muhtemelen veri eşitleme Kullanıcı arabirimini bile açamazsınız.

- **Neden**. En olası neden bir eşitleme döngüsüdür. Eşitleme grubu tarafından eşitleme, A eşitleme grubu B tarafından eşitleme tetiklediği zaman bir eşitleme döngüsü meydana gelir ve ardından eşitleme grubu A ile eşitleme tetikler. Asıl durum daha karmaşık olabilir ve döngüde ikiden fazla eşitleme grubu içerebilir. Bu sorun, bir eşitlemeden çakışan eşitleme gruplarının neden olduğu bir eşitlemenin dairesel olarak tetiklenmesi olabilir.

- **Çözümleme**. En iyi çözüm engeldir. Eşitleme gruplarında döngüsel başvurular olmadığından emin olun. Bir eşitleme grubu tarafından eşitlenen herhangi bir satır başka bir eşitleme grubu tarafından eşitlenemez.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a> Şu iletiyi görüyorum: "sütuna NULL değer eklenemiyor \<column> . Sütun null değerlere izin vermiyor. " Bu ne anlama geliyor ve nasıl çözebilirim? 
Bu hata iletisi aşağıdaki iki sorunlardan birinin oluştuğunu gösterir:
-  Tablonun birincil anahtarı yok. Bu sorunu onarmak için, eşitmekte olduğunuz tüm tablolara birincil bir anahtar ekleyin.
-  CREATE INDEX deyiminizde WHERE yan tümcesi vardır. Veri eşitleme bu koşulu işlemez. Bu sorunu onarmak için WHERE yan tümcesini kaldırın veya değişiklikleri tüm veritabanlarında el ile yapın. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> Veri eşitleme, dairesel başvuruları nasıl işler? Diğer bir deyişle, aynı veriler birden çok eşitleme grubunda eşitlendiğinde ve sonuç olarak değişene devam ediyor mu?
Veri eşitleme döngüsel başvuruları işlemez. Kaçındığınızdan emin olun. 

## <a name="client-agent-issues"></a>İstemci Aracısı sorunları

İstemci aracısıyla ilgili sorunları gidermek için bkz. [veri eşitleme Aracısı sorunlarını giderme](sql-data-sync-agent-overview.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Kurulum ve bakım sorunları

- ["Disk dışı alan" iletisi alıyorum](#setup-space)

- [Eşitleme grubumu silemiyorum](#setup-delete)

- [SQL Server veritabanının kaydını silemiyor](#setup-unreg)

- [Sistem hizmetlerini başlatmak için yeterli ayrıcalıklara sahip değil](#setup-perms)

- [Bir veritabanında "güncel olmayan" durumu vardır](#setup-date)

- [Bir eşitleme grubunda "güncel olmayan" durum bulunur](#setup-date2)

- [Bir eşitleme grubu, aracıyı kaldırmak veya durdurmak için üç dakika içinde silinemez](#setup-delete2)

- [Kayıp veya bozuk bir veritabanını geri yüklerken ne olur?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a> "Disk dışı alan" iletisi alıyorum

- **Neden**. Soltover dosyalarının silinmesi gerekiyorsa "disk alanı yetersiz" iletisi görünebilir. Bu, virüsten koruma yazılımından kaynaklanıyor olabilir veya silme işlemleri denendiğinde dosyalar açık olur.

- **Çözümleme**. % Temp% klasöründe () bulunan eşitleme dosyalarını el ile silin `del \*sync\* /s` . Ardından,% Temp% klasöründeki alt dizinleri silin.

> [!IMPORTANT]
> Eşitleme sürerken hiçbir dosyayı silmeyin.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> Eşitleme grubumu silemiyorum

Bir eşitleme grubunu silme denemeniz başarısız olur. Aşağıdaki senaryolardan herhangi biri, bir eşitleme grubunun silinmesine neden olabilecek bir hata oluşabilir:

- **Neden**. İstemci aracısı çevrimdışı.

- **Çözümleme**. İstemci aracısının çevrimiçi olduğundan emin olup yeniden deneyin.

- **Neden**. İstemci aracısı yüklü değil veya eksik.

- **Çözümleme**. İstemci aracısı yüklü değilse veya eksikse:  
    a. Aracı XML dosyası mevcutsa, dosyayı SQL Data Sync yükleme klasöründen kaldırın.  
    b. Aracıyı bir şirket içi bilgisayara yükleyin (aynı bilgisayar veya farklı bir bilgisayar olabilir). Ardından, portalda çevrimdışı olarak gösterilen aracı için oluşturulan aracı anahtarını gönderin.

- **Neden**. Bir veritabanı çevrimdışı.

- **Çözümleme**. Veritabanlarının çevrimiçi olduğundan emin olun.

- **Neden**. Eşitleme grubu sağlanıyor veya eşitleniyor.

- **Çözümleme**. Sağlama veya eşitleme işlemi bitene kadar bekleyin ve ardından eşitleme grubunu silmeyi yeniden deneyin.

### <a name="i-cant-unregister-a-sql-server-database"></a><a name="setup-unreg"></a> SQL Server veritabanının kaydını silemiyor

- **Neden**. Büyük olasılıkla, zaten silinmiş olan bir veritabanının kaydını silmeye çalışıyorsunuz.

- **Çözümleme**. SQL Server veritabanının kaydını silmek için veritabanını seçin ve sonra **silmeyi zorla**' yı seçin.

  Bu işlem veritabanını eşitleme grubundan kaldıramazsa:

  1. İstemci Aracısı ana bilgisayar hizmetini durdurup yeniden başlatın:  
    a. **Başlat** menüsünü seçin.  
    b. Arama kutusuna **Services. msc** yazın.  
    c. Arama sonuçları bölmesinin **Programlar** bölümünde **Hizmetler**' e çift tıklayın.  
    d. **SQL Data Sync** hizmetine sağ tıklayın.  
    e. Hizmet çalışıyorsa, uygulamayı durdurun.  
    f. Hizmete sağ tıklayın ve ardından **Başlat**' ı seçin.  
    örneğin: Veritabanının hala kayıtlı olup olmadığını denetleyin. Artık kayıtlı değilse işiniz yapılır. Aksi halde, bir sonraki adımla devam edin.
  1. İstemci Aracısı uygulamasını açın (SqlAzureDataSyncAgent).
  1. **Kimlik bilgilerini düzenle**' yi seçin ve ardından veritabanı için kimlik bilgilerini girin.
  1. Kayıt silme işlemine devam edin.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a> Sistem hizmetlerini başlatmak için yeterli ayrıcalıklara sahip değil

- **Neden**. Bu hata iki durumda oluşur:
  -   Kullanıcı adı ve/veya parola hatalı.
  -   Belirtilen kullanıcı hesabı, hizmet olarak oturum açmak için yeterli ayrıcalıklara sahip değil.

- **Çözümleme**. Kullanıcı hesabına hizmet olarak oturum açma kimlik bilgileri verin:

  1. **Başlat**  >  **Denetim Masası**  >  **Yönetim Araçları**  >  **yerel güvenlik ilkesi**  >  **yerel ilke**  >  **Kullanıcı Rights Management**' ye gidin.
  1. **Hizmet olarak oturum aç '** ı seçin.
  1. **Özellikler** iletişim kutusunda Kullanıcı hesabını ekleyin.
  1. **Uygula**’yı ve sonra **Tamam**’ı seçin.
  1. Tüm pencereleri kapatın.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a> Bir veritabanında "güncel olmayan" durumu vardır

- **Neden**. SQL Data Sync, hizmetten 45 gün veya daha fazlası için çevrimdışı olan veritabanlarını kaldırır (veritabanının çevrimdışı olduğu zamandan itibaren sayılır). Bir veritabanı 45 gün veya daha uzun bir süre sonra yeniden çevrimiçi duruma gelirse, durumu **güncel** değildir.

- **Çözümleme**. Veritabanlarının hiçbirinin 45 gün veya daha uzun süre çevrimdışı çalışmamasını sağlayarak **güncel** olmayan bir durumu önleyebilirsiniz.

  Veritabanının durumu **güncel** değilse:

  1. Eşitleme grubundan **güncel** olmayan bir durumu olan veritabanını kaldırın.
  1. Veritabanını yeniden eşitleme grubuna ekleyin.

  > [!WARNING]
  > Çevrimdışıyken bu veritabanında yapılan tüm değişiklikleri kaybedersiniz.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> Bir eşitleme grubunda "güncel olmayan" durum bulunur

- **Neden**. Bir veya daha fazla değişiklik 45 günlük bekletme dönemi boyunca uygulanmayabilir, bir eşitleme grubu güncel hale gelebilir.

- **Çözümleme**. Bir eşitleme grubunun **güncel** durumundan kaçınmak için, geçmiş görüntüleyicisinde eşitleme işlerinizin sonuçlarını düzenli olarak inceleyin. Uygulanmanıza neden olan değişiklikleri araştırın ve çözümleyin.

  Eşitleme grubunun durumu **güncel** değilse, eşitleme grubunu silip yeniden oluşturun.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> Bir eşitleme grubu, aracıyı kaldırmak veya durdurmak için üç dakika içinde silinemez

İlişkili SQL Data Sync istemci aracısını kaldırmak veya durdurmak için bir eşitleme grubunu üç dakika içinde silemezsiniz.

- **Çözümleme**.

  1. İlişkili eşitleme aracıları çevrimiçi iken bir eşitleme grubunu kaldırın (önerilir).
  1. Aracı çevrimdışıysa, ancak yüklüyse şirket içi bilgisayarda çevrimiçi duruma getirin. SQL Data Sync portalında aracının durumunu **çevrimiçi** olarak görünmesini bekleyin. Ardından, eşitleme grubunu kaldırın.
  1. Kaldırılmış olduğundan, aracı çevrimdışıysa:  
    a.  Aracı XML dosyası mevcutsa, dosyayı SQL Data Sync yükleme klasöründen kaldırın.  
    b.  Aracıyı bir şirket içi bilgisayara yükleyin (aynı bilgisayar veya farklı bir bilgisayar olabilir). Ardından, portalda çevrimdışı olarak gösterilen aracı için oluşturulan aracı anahtarını gönderin.  
    c. Eşitleme grubunu silmeyi deneyin.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a> Kayıp veya bozuk bir veritabanını geri yüklerken ne olur?

Bir yedeklemeden kayıp veya bozuk bir veritabanını geri yüklerseniz, veritabanının ait olduğu eşitleme gruplarında veri yakınsaması olabilir.

## <a name="next-steps"></a>Sonraki adımlar
SQL Data Sync hakkında daha fazla bilgi için bkz.:

-   Genel Bakış- [Azure 'da SQL Data Sync birden çok bulutta ve şirket içi veritabanlarında veri eşitleme](sql-data-sync-data-sql-server-sql-database.md)
-   Veri eşitlemesini ayarlama
    - Portalda- [öğretici: Azure SQL veritabanı ile SQL Server arasında veri eşitlemek için SQL Data Sync ayarlama](sql-data-sync-sql-server-configure.md)
    - PowerShell ile
        -  [Azure SQL veritabanı 'nda birden çok veritabanı arasında eşitleme yapmak için PowerShell 'i kullanma](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell kullanarak Azure SQL veritabanındaki bir veritabanı ile SQL Server örneğindeki bir veritabanı arasında eşitleme](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Veri eşitleme Aracısı- [Azure 'da SQL Data Sync Için veri eşitleme Aracısı](sql-data-sync-agent-overview.md)
-   En iyi uygulamalar- [Azure 'da SQL Data Sync Için en iyi yöntemler](sql-data-sync-best-practices.md)
-   İzleyici- [Azure izleyici günlükleri ile izleyici SQL Data Sync](./monitor-tune-overview.md)
-   Eşitleme şemasını güncelleştirme
    -   Transact-SQL- [Azure 'da SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatikleştirin](sql-data-sync-update-sync-schema.md)
    -   PowerShell ile- [varolan bir eşitleme grubundaki eşitleme şemasını güncelleştirmek Için PowerShell kullanın](scripts/update-sync-schema-in-sync-group.md)

SQL veritabanı hakkında daha fazla bilgi için bkz.

-   [SQL Veritabanı'na Genel Bakış](sql-database-paas-overview.md)
-   [Veritabanı Yaşam Döngüsü Yönetimi](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))