---
title: SQL Server veritabanlarını Azure 'a yedekleme
description: Bu makalede SQL Server Azure 'a nasıl yedekleyeceğiniz açıklanmaktadır. Makalede kurtarma SQL Server de açıklanmaktadır.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b6daf631248958948e799b20284d84a1e59e5dfe
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518873"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Azure VM'lerindeki SQL Server Backup hakkında

[Azure Backup](backup-overview.md) , Azure VM 'lerinde çalışan SQL Server yedeklemek için akış tabanlı, özel bir çözüm sunar. Bu çözüm, Azure Backup sıfır altyapı yedeklemesi, uzun süreli saklama ve merkezi yönetim avantajlarıyla birlikte hizalanır. Buna ek olarak, SQL Server için aşağıdaki avantajları da sağlar:

1. Tüm Yedekleme türlerini destekleyen iş yükü uyumlu yedeklemeler-tam, değişiklik ve günlük
2. sık kullanılan günlük yedeklemeleri ile 15 dakikalık RPO (kurtarma noktası hedefi)
3. Bir saniyede en çok bir noktaya kurtarma
4. Ayrı veritabanı düzeyinde yedekleme ve geri yükleme

Günümüzde destekdiğimiz yedekleme ve geri yükleme senaryolarını görüntülemek için [destek matrisine](sql-support-matrix.md#scenario-support)bakın.

## <a name="backup-process"></a>Yedekleme işlemi

Bu çözüm SQL veritabanlarınızın yedeklerini almak için SQL Native API 'lerden yararlanır.

* Korumak istediğiniz SQL Server VM ve içindeki veritabanları için sorgulamak üzere belirttikten sonra, Azure Backup hizmet, VM 'ye ad uzantısı tarafından bir iş yükü yedekleme uzantısı yükler `AzureBackupWindowsWorkload` .
* Bu uzantı, bir düzenleyici ve SQL eklentisi içerir. Düzenleyici, yedekleme, yedekleme ve geri yükleme gibi çeşitli işlemler için iş akışlarını tetiklemeden sorumlu olsa da, eklenti gerçek veri akışından sorumludur.
* Bu VM 'deki veritabanlarını bulabilmek için Azure Backup hesabı oluşturur `NT SERVICE\AzureWLBackupPluginSvc` . Bu hesap yedekleme ve geri yükleme için kullanılır ve SQL sysadmin izinleri gerektirir. `NT SERVICE\AzureWLBackupPluginSvc`Hesap bir [sanal hizmet hesabıdır](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)ve bu nedenle herhangi bir parola yönetimi gerektirmez. Azure Backup `NT AUTHORITY\SYSTEM` , veritabanı bulma/sorgulama için hesabı kullanır, bu nedenle bu HESABıN SQL 'de genel oturum açması gerekir. SQL Server sanal makinesini Azure Market'ten oluşturmadıysanız **UserErrorSQLNoSysadminMembership** hatasıyla karşılaşabilirsiniz. Bu durumda [buradaki yönergeleri izleyin](#set-vm-permissions).
* Seçili veritabanlarında korumayı Yapılandır ' ı etkinleştirdikten sonra, yedekleme hizmeti düzenleyiciyi yedekleme zamanlamaları ve diğer ilke ayrıntıları ile ayarlar; bu da uzantının yerel olarak VM 'de önbelleğe alınır.
* Zamanlanan zamanda, düzenleyici eklenti ile iletişim kurar ve VDı kullanarak SQL Server 'dan yedekleme verilerini akışa başlar.  
* Eklenti, verileri doğrudan kurtarma hizmetleri kasasına gönderir ve böylece bir hazırlama konumu gereksinimini ortadan kaldırır. Veriler, depolama hesaplarında Azure Backup hizmeti tarafından şifrelenir ve depolanır.
* Veri aktarımı tamamlandığında, düzenleyici yedekleme hizmeti ile yürütmeyi onaylar.

  ![SQL yedekleme mimarisi](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Başlamadan önce

Başlamadan önce, aşağıdaki gereksinimleri doğrulayın:

1. Azure 'da çalışan bir SQL Server örneğine sahip olduğunuzdan emin olun. Market 'te [hızlıca bir SQL Server örneği oluşturabilirsiniz](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) .
2. [Özellik konularını](sql-support-matrix.md#feature-considerations-and-limitations) ve [senaryo desteğini](sql-support-matrix.md#scenario-support)gözden geçirin.
3. Bu senaryoyla ilgili [sık sorulan soruları gözden geçirin](faq-backup-sql-server.yml) .

## <a name="set-vm-permissions"></a>Sanal makine izinlerini ayarlama

  SQL Server bulma işlemini çalıştırdığınızda şunları Azure Backup:

* AzureBackupWindowsWorkload uzantısını ekler.
* Sanal makinedeki veritabanlarını bulacak bir NT SERVICE\AzureWLBackupPluginSvc hesabı oluşturur. Bu hesap, yedekleme ve geri yükleme için kullanılır ve SQL sysadmin izinleri gerektirir.
* Bir VM üzerinde çalışan veritabanlarını bulur Azure Backup NT AUTHORITY\SYSTEM hesabını kullanır. Bu hesap, SQL 'de ortak bir oturum açma olmalıdır.

Azure Market 'te SQL Server VM oluşturmadıysanız veya SQL 2008 veya 2008 R2 kullanıyorsanız bir **Usererrorsqlnosysadminmembership** hatası alabilirsiniz.

Windows 2008 R2 'de çalışan **SQL 2008** ve **2008 R2** durumunda izin vermek için [buraya](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)bakın.

Diğer tüm sürümler için aşağıdaki adımlarla izinleri onarın:

  1. SQL Server Management Studio (SSMS) oturum açmak için SQL Server sysadmin izinlerine sahip bir hesap kullanın. Özel izinlere ihtiyacınız yoksa, Windows kimlik doğrulamasının çalışması gerekir.
  2. SQL Server, **güvenlik/oturum açma** klasörünü açın.

      ![Hesapları görmek için güvenlik/oturum açmalar klasörünü açın](./media/backup-azure-sql-database/security-login-list.png)

  3. **Oturumlar** klasörüne sağ tıklayıp **yeni oturum açma**' yı seçin. **Oturum aç-yeni**' de **Ara**' yı seçin.

      ![Oturum aç-yeni iletişim kutusunda ara ' yı seçin.](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows sanal hizmet hesabı **NT SERVICE\AzureWLBackupPluginSvc** , sanal makine kaydı ve SQL bulma aşaması sırasında oluşturulmuştur. **Seçilecek nesne adını girin** bölümünde gösterildiği gibi hesap adını girin. Adı çözümlemek için **adları denetle** ' yi seçin. **Tamam**’ı seçin.

      ![Bilinmeyen hizmet adını çözümlemek için adları denetle ' yi seçin](./media/backup-azure-sql-database/check-name.png)

  5. **Sunucu rolleri**' nde **sysadmin** rolünün seçildiğinden emin olun. **Tamam**’ı seçin. Gerekli izinler artık var olmalıdır.

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
3. Oturum açma işlemlerine sağ tıklayıp *yeni oturum açma...* seçeneğini belirleyin.

    ![SSMS kullanarak yeni oturum açma](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Genel sekmesine gidin ve oturum açma adı olarak **NT AUTHORITY\SYSTEM** girin.

    ![SSMS için oturum açma adı](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. *Sunucu rolleri* ' ne gidin ve *genel* ve *sysadmin* rolleri ' ni seçin.

    ![SSMS 'de rol seçme](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. *Duruma* git. Veritabanı altyapısına bağlanma ve oturum açma Iznini *etkin* olarak *verin* .

    ![SSMS 'de izin verme](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Tamam'ı seçin.
8. SQL Server örneğine NT Service\AzureWLBackupPluginSvc LOGIN eklemek için aynı adım dizisini yineleyin (yukarıdaki 1-7). Oturum açma zaten varsa, bunun sysadmin sunucu rolüne sahip olduğundan ve durum ' un altında, veritabanı motoruna bağlanma ve oturum açma Iznini etkin olarak vermiş olduğundan emin olun.
9. İzin verdikten sonra portalda **DB 'Yi yeniden keşfet** : **->** Azure VM 'de kasa yedekleme altyapısı **->** iş yükü:

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
