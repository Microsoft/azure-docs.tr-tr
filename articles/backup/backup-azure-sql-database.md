---
title: SQL Server veritabanlarını Azure 'a yedekleme
description: Bu makalede SQL Server Azure 'a nasıl yedekleyeceğiniz açıklanmaktadır. Makalede kurtarma SQL Server de açıklanmaktadır.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 811f04edb4d5f0326d0af629146b7cee10424df8
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172643"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Azure VM'lerindeki SQL Server Backup hakkında

SQL Server veritabanları, düşük kurtarma noktası hedefi (RPO) ve uzun süreli saklama gerektiren kritik iş yükleridir. [Azure Backup](backup-overview.md)kullanarak Azure VM 'lerinde çalışan SQL Server veritabanlarını yedekleyebilirsiniz.

## <a name="backup-process"></a>Yedekleme işlemi

Bu çözüm SQL veritabanlarınızın yedeklerini almak için SQL Native API 'lerden yararlanır.

* Korumak istediğiniz SQL Server VM ve içindeki veritabanları için sorgulamak üzere belirttikten sonra, Azure Backup hizmet, VM 'ye ad `AzureBackupWindowsWorkload` uzantısı tarafından bir iş yükü yedekleme uzantısı yükler.
* Bu uzantı, bir düzenleyici ve SQL eklentisi içerir. Düzenleyici, yedekleme, yedekleme ve geri yükleme gibi çeşitli işlemler için iş akışlarını tetiklemeden sorumlu olsa da, eklenti gerçek veri akışından sorumludur.
* Bu VM 'deki veritabanlarını bulabilmek için Azure Backup hesabı `NT SERVICE\AzureWLBackupPluginSvc`oluşturur. Bu hesap yedekleme ve geri yükleme için kullanılır ve SQL sysadmin izinleri gerektirir. `NT SERVICE\AzureWLBackupPluginSvc` hesabı bir [sanal hizmet hesabıdır](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)ve bu nedenle herhangi bir parola yönetimi gerektirmez. Azure Backup, veritabanı bulma/sorgulama `NT AUTHORITY\SYSTEM` hesabını kullanır, bu nedenle bu hesabın SQL 'de genel oturum açması gerekir. Azure Marketi 'nden SQL Server VM oluşturmadıysanız **Usererrorsqlnosysadminmembership**hatası alabilirsiniz. Bu durum oluşursa, [Bu yönergeleri izleyin](#set-vm-permissions).
* Seçili veritabanlarında korumayı Yapılandır ' ı etkinleştirdikten sonra, yedekleme hizmeti düzenleyiciyi yedekleme zamanlamaları ve diğer ilke ayrıntıları ile ayarlar; bu da uzantının yerel olarak VM 'de önbelleğe alınır.
* Zamanlanan zamanda, düzenleyici eklenti ile iletişim kurar ve VDı kullanarak SQL Server 'dan yedekleme verilerini akışa başlar.  
* Eklenti, verileri doğrudan kurtarma hizmetleri kasasına gönderir ve böylece bir hazırlama konumu gereksinimini ortadan kaldırır. Veriler, depolama hesaplarında Azure Backup hizmeti tarafından şifrelenir ve depolanır.
* Veri aktarımı tamamlandığında, düzenleyici yedekleme hizmeti ile yürütmeyi onaylar.

  ![SQL yedekleme mimarisi](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Başlamadan önce

Başlamadan önce, aşağıdakileri doğrulayın:

1. Azure 'da çalışan bir SQL Server örneğine sahip olduğunuzdan emin olun. Market 'te [hızlıca bir SQL Server örneği oluşturabilirsiniz](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) .
2. [Özellik değerlendirmesi](#feature-consideration-and-limitations) ve [senaryo desteğini](#scenario-support)gözden geçirin.
3. Bu senaryoyla ilgili [sık sorulan soruları gözden geçirin](faq-backup-sql-server.md) .

## <a name="scenario-support"></a>Senaryo desteği

**Destek** | **Ayrıntılar**
--- | ---
**Desteklenen dağıtımlar** | SQL Market Azure VM 'Leri ve Market olmayan (SQL Server el ile yüklenmiş) VM 'Ler desteklenir.
**Desteklenen coğrafyalar** | Avustralya Güney Doğu (Ao), Doğu Avustralya (AE), Avustralya Orta (AC), Avustralya Orta 2 (AC) <br> Brezilya Güney (BRS)<br> Kanada Orta (CNC), Kanada Doğu (CE)<br> Güney Doğu Asya (SEA), Doğu Asya (EA) <br> Doğu ABD (EUS), Doğu ABD 2 (EUS2), Orta Batı ABD (WCUS), Batı ABD (WUS); Batı ABD 2 (WUS 2) Orta Kuzey ABD (NCUS) Orta ABD (cu DÜZEYINDE KAPSANıR) Orta Güney ABD (SCUS) <br> Hindistan Orta (ıNC), Hindistan Güney (INS), Hindistan Batı <br> Japonya Doğu (JPE), Japonya Batı (JPW) <br> Kore Orta (KRC), Kore Güney (KRS) <br> Kuzey Avrupa (NE), Batı Avrupa <br> UK Güney (UKS), UK Batı (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Teksas, US DoD Orta, US DoD Doğu <br> Almanya Kuzey, Almanya Orta Batı <br> İsviçre Kuzey, İsviçre Batı <br> Fransa Orta <br> Çin Doğu, Çin Doğu 2, Çin Kuzey, Çin Kuzey 2
**Desteklenen işletim sistemleri** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux Şu anda desteklenmiyor.
**Desteklenen SQL Server sürümleri** | SQL Server 2019, [ürün yaşam döngüsünü ara](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)sayfasında SQL Server 2017 SQL Server 2016 ve SPS 'yi [ürün yaşam döngüsünü ara sayfasında](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)ayrıntılı olarak SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, geliştirici, Express.
**Desteklenen .NET sürümleri** | VM 'de yüklü .NET Framework 4.5.2 veya üzeri

## <a name="feature-consideration-and-limitations"></a>Özellik değerlendirmesi ve sınırlamaları

* SQL Server yedekleme Azure portal veya **PowerShell**içinde yapılandırılabilir. CLı 'yi desteklemiyoruz.
* Çözüm her iki tür [dağıtım](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) için de desteklenir-Azure Resource Manager VM 'ler ve klasik VM 'ler.
* SQL Server çalıştıran VM, Azure genel IP adreslerine erişmek için internet bağlantısı gerektirir.
* SQL Server **Yük devretme kümesi örneği (FCı)** ve SQL Server her zaman yük devretme kümesi örneği desteklenmez.
* Yansıtma veritabanları ve veritabanı anlık görüntüleri için yedekleme ve geri yükleme işlemleri desteklenmez.
* Tek başına SQL Server örneğinizi veya SQL Always on kullanılabilirlik grubunu yedeklemek için birden fazla yedekleme çözümü kullanmak yedekleme hatasına neden olabilir; Bunu yapmaktan kaçının.
* Bir kullanılabilirlik grubunun iki düğümünü aynı veya farklı çözümlerle tek tek yedeklemek, yedekleme hatasına da neden olabilir.
* Azure Backup **salt okunurdur** veritabanları Için yalnızca tam ve salt kopya tam yedekleme türlerini destekler
* Çok sayıda dosya içeren veritabanları korunamaz. Desteklenen en fazla dosya sayısı **~ 1000**' dir.  
* Bir kasadaki **~ 2000** SQL Server veritabanlarını yedekleyebilirsiniz. Daha fazla veritabanınız olması durumunda birden çok kasa oluşturabilirsiniz.
* Yedeklemeyi tek bir go 'da en fazla **50** veritabanına yapılandırabilirsiniz; Bu kısıtlama, yedekleme yüklerini iyileştirmenize yardımcı olur.
* Boyutu **2 TB** 'a kadar olan veritabanlarını destekliyoruz; Bundan daha büyük boyutlarda performans sorunları çıkabilir.
* Sunucu başına kaç veritabanının korunduğuna ilişkin bir fikir sahibi olmak için, bant genişliği, VM boyutu, yedekleme sıklığı, veritabanı boyutu vb. gibi faktörleri göz önünde bulundurmanız gerekir. VM kaynaklarına ve yedekleme ilkesine göre sunucu başına sahip olabilirsiniz yaklaşık sayıda veritabanı sağlayan kaynak planlayıcısı 'nı [indirin](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) .
* Kullanılabilirlik grupları söz konusu olduğunda, yedeklemeler, birkaç etkene göre farklı düğümlerden alınır. Bir kullanılabilirlik grubu için yedekleme davranışı aşağıda özetlenmiştir.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Her zaman açık kullanılabilirlik grupları durumunda yedekleme davranışı

Yedeklemenin yalnızca bir AG düğümünde yapılandırılması önerilir. Yedekleme her zaman birincil düğümle aynı bölgede yapılandırılmalıdır. Diğer bir deyişle, her zaman, yedeklemeyi yapılandırdığınız bölgede bulunan birincil düğümün olması gerekir. AG 'nin tüm düğümleri yedeklemenin yapılandırıldığı bölgede yer alıyorsa sorun yoktur.

#### <a name="for-cross-region-ag"></a>Bölgeler arası AG için

* Yedekleme tercihinden bağımsız olarak yedeklemeler, yedeklemenin yapılandırıldığı bölgede yer alan düğümlerden gerçekleşmeyecek. Bunun nedeni, çapraz bölge yedeklemelerinin desteklenmemelidir. Yalnızca iki düğümünüz varsa ve ikincil düğüm diğer bölgedeyse; Bu durumda, yedeklemeler birincil düğümden gerçekleşmeye devam edecektir (yedekleme tercihiniz ' ikincil yalnızca ' değilse).
* Yük devretme işlemi, yedeklemenin yapılandırıldığı sunucudan farklı bir bölgede gerçekleşdiğinde, yedeklemeler başarısız olan bölgedeki düğümlerde başarısız olur.

Yedekleme tercihine ve yedeklemeler türlerine (tam/değişiklik/günlük/salt kopya) bağlı olarak, yedeklemeler belirli bir düğümden alınır (birincil/ikincil).

* **Yedekleme tercihi: birincil**

**Yedekleme türü** | **Node**
    --- | ---
    Tam | Birincil
    Di | Birincil
    Günlük |  Birincil
    Salt kopya tam |  Birincil

* **Yedekleme tercihi: yalnızca Ikincil**

**Yedekleme türü** | **Node**
--- | ---
Tam | Birincil
Di | Birincil
Günlük |  İkincil
Salt kopya tam |  İkincil

* **Yedekleme tercihi: Ikincil**

**Yedekleme türü** | **Node**
--- | ---
Tam | Birincil
Di | Birincil
Günlük |  İkincil
Salt kopya tam |  İkincil

* **Yedekleme tercihi yok**

**Yedekleme türü** | **Node**
--- | ---
Tam | Birincil
Di | Birincil
Günlük |  İkincil
Salt kopya tam |  İkincil

## <a name="set-vm-permissions"></a>VM izinlerini ayarla

  SQL Server bulma işlemini çalıştırdığınızda şunları Azure Backup:

* AzureBackupWindowsWorkload uzantısını ekler.
* Sanal makinedeki veritabanlarını bulacak bir NT SERVICE\AzureWLBackupPluginSvc hesabı oluşturur. Bu hesap, yedekleme ve geri yükleme için kullanılır ve SQL sysadmin izinleri gerektirir.
* Bir VM üzerinde çalışan veritabanlarını bulur Azure Backup NT AUTHORITY\SYSTEM hesabını kullanır. Bu hesap, SQL 'de ortak bir oturum açma olmalıdır.

Azure Marketi 'nde SQL Server VM oluşturmadıysanız veya SQL 2008 ve 2008 R2 kullanıyorsanız bir **Usererrorsqlnosysadminmembership** hatası alabilirsiniz.

Windows 2008 R2 'de çalışan **SQL 2008** ve **2008 R2** durumunda izinler vermek için [buraya](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)bakın.

Diğer tüm sürümler için aşağıdaki adımlarla izinleri onarın:

  1. SQL Server Management Studio (SSMS) oturum açmak için SQL Server sysadmin izinlerine sahip bir hesap kullanın. Özel izinlere ihtiyacınız yoksa, Windows kimlik doğrulamasının çalışması gerekir.
  2. SQL Server, **güvenlik/oturum açma** klasörünü açın.

      ![Hesapları görmek için güvenlik/oturum açmalar klasörünü açın](./media/backup-azure-sql-database/security-login-list.png)

  3. **Oturumlar** klasörüne sağ tıklayıp **yeni oturum açma**' yı seçin. **Oturum aç-yeni**' de **Ara**' yı seçin.

      ![Oturum aç-yeni iletişim kutusunda ara ' yı seçin.](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows sanal hizmet hesabı **NT SERVICE\AzureWLBackupPluginSvc** , sanal makine kaydı ve SQL bulma aşaması sırasında oluşturulmuştur. **Seçilecek nesne adını girin**bölümünde gösterildiği gibi hesap adını girin. Adı çözümlemek için **adları denetle** ' yi seçin. **OK (Tamam)** düğmesine tıklayın.

      ![Bilinmeyen hizmet adını çözümlemek için adları denetle ' yi seçin](./media/backup-azure-sql-database/check-name.png)

  5. **Sunucu rolleri**' nde **sysadmin** rolünün seçildiğinden emin olun. **OK (Tamam)** düğmesine tıklayın. Gerekli izinler artık var olmalıdır.

      ![Sysadmin sunucu rolünün seçildiğinden emin olun](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Şimdi veritabanını kurtarma hizmetleri kasasıyla ilişkilendirin. Azure portal, **korumalı sunucular** listesinde, bir hata durumunda olan sunucuya sağ tıklayıp veritabanlarını **yeniden keşfet**>.

      ![Sunucunun uygun izinlere sahip olduğunu doğrulayın](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. **Bildirimler** alanındaki ilerlemeyi denetleyin. Seçilen veritabanları bulunduğunda, bir başarı iletisi görüntülenir.

      ![Dağıtım başarılı iletisi](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> SQL Server birden çok SQL Server yüklüyse, **NT Service\AzureWLBackupPluginSvc** hesabı için sysadmin IZNINI tüm SQL örneklerine eklemeniz gerekir.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL 2008 ve SQL 2008 R2 için SQL sysadmin izinleri verme

SQL Server örneğine **NT AUTHORITY\SYSTEM** ve **NT Service\AzureWLBackupPluginSvc** oturum açmaları ekleyin:

1. Nesne Gezgini 'ndeki SQL Server örneğine gidin.
2. Güvenlik-> oturum açma bilgilerine gidin
3. Oturum açma Işlemlerine sağ tıklayıp *yeni oturum açma...* öğesine tıklayın.

    ![SSMS kullanarak yeni oturum açma](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Genel sekmesine gidin ve oturum açma adı olarak **NT AUTHORITY\SYSTEM** girin.

    ![SSMS için oturum açma adı](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. *Sunucu rolleri* ' ne gidin ve *genel* ve *sysadmin* rolleri ' ni seçin.

    ![SSMS 'de rol seçme](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. *Duruma*git. Veritabanı altyapısına bağlanma ve oturum açma Iznini *etkin*olarak *verin* .

    ![SSMS 'de izin verme](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Tamam'a tıklayın.
8. SQL Server örneğine NT Service\AzureWLBackupPluginSvc LOGIN eklemek için aynı adım dizisini yineleyin (yukarıdaki 1-7). Oturum açma zaten varsa, bunun sysadmin sunucu rolüne sahip olduğundan ve durum ' un altında, veritabanı motoruna bağlanma ve oturum açma Iznini etkin olarak vermiş olduğundan emin olun.
9. İzin verdikten sonra, portalda **DB 'Yi yeniden keşfet** : kasa **->** yedekleme altyapısı Azure VM 'de iş yükünü **->** :

    ![Azure portal 'de DBs 'yi yeniden keşfet](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Alternatif olarak, yönetici modunda aşağıdaki PowerShell komutlarını çalıştırarak izinleri vermeyi otomatik hale getirebilirsiniz. Örnek adı varsayılan olarak MSSQLSERVER olarak ayarlanır. Gerekirse, komut dosyasındaki örnek adı bağımsız değişkenini değiştirin:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Sonraki adımlar

* SQL Server veritabanlarını yedekleme [hakkında bilgi edinin](backup-sql-server-database-azure-vms.md) .
* Yedeklenen SQL Server veritabanlarını geri yükleme [hakkında bilgi edinin](restore-sql-database-azure-vm.md) .
* Yedeklenen SQL Server veritabanlarını yönetme [hakkında bilgi edinin](manage-monitor-sql-database-backup.md) .
