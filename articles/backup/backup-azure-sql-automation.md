---
title: Azure VM yedekleme 'de SQL DB Yedekleme & PowerShell aracılığıyla geri yükleme
description: Azure Backup ve PowerShell kullanarak Azure VM 'lerinde SQL veritabanlarını yedekleyin ve geri yükleyin.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 115eef3619f76f83f63c5e69e86393c032e0844e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172634"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>PowerShell ile Azure VM 'lerinde SQL veritabanlarını yedekleme ve geri yükleme

Bu makalede, [Azure Backup](backup-overview.md) kurtarma hizmetleri kasasını kullanarak BIR Azure VM 'de SQL DB 'yi yedeklemek ve kurtarmak için Azure PowerShell nasıl kullanılacağı açıklanır.

Bu makalede nasıl yapılacağı açıklanmaktadır:

> [!div class="checklist"]
>
> * PowerShell 'i ayarlayın ve Azure kurtarma hizmetleri sağlayıcısını kaydedin.
> * Kurtarma Hizmetleri kasası oluşturun.
> * Azure VM 'de SQL DB için yedeklemeyi yapılandırın.
> * Bir yedekleme işi çalıştırın.
> * Yedeklenen SQL DB 'yi geri yükleyin.
> * Yedekleme ve geri yükleme işlerini izleyin.

## <a name="before-you-start"></a>Başlamadan önce

* Kurtarma Hizmetleri kasaları hakkında [daha fazla bilgi edinin](backup-azure-recovery-services-vault-overview.md) .
* [Azure VM 'leri IÇINDEKI SQL veritabanlarını yedeklemeye](backup-azure-sql-database.md#before-you-start)yönelik özellik özellikleri hakkında bilgi edinin.
* Kurtarma Hizmetleri için PowerShell nesne hiyerarşisini gözden geçirin.

### <a name="recovery-services-object-hierarchy"></a>Kurtarma Hizmetleri nesne hiyerarşisi

Nesne hiyerarşisi aşağıdaki diyagramda özetlenir.

![Kurtarma Hizmetleri nesne hiyerarşisi](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure kitaplığı 'ndaki **az. RecoveryServices** [cmdlet başvuru](/powershell/module/az.recoveryservices) başvurusunu gözden geçirin.

### <a name="set-up-and-install"></a>Ayarlama ve kurma

PowerShell 'i aşağıdaki şekilde ayarlayın:

1. [Az PowerShell ' in en son sürümünü indirin](/powershell/azure/install-az-ps). Gerekli en düşük sürüm 1.5.0 ' dir.

2. Bu komutla Azure Backup PowerShell cmdlet 'lerini bulun:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup ve kurtarma hizmetleri Kasası için diğer adları ve cmdlet 'leri inceleyin. İşte neleri görebileceğiniz hakkında bir örnek. Cmdlet 'lerin tamamen bir listesi değildir.

    ![Kurtarma Hizmetleri cmdlet 'lerinin listesi](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount**ile Azure hesabınızda oturum açın.
5. Görüntülenen Web sayfasında hesap kimlik bilgilerinizi girmeniz istenir.

    * Alternatif olarak, hesap kimlik bilgilerinizi **-Credential**ile **Connect-azaccount** cmdlet 'ine bir parametre olarak dahil edebilirsiniz.
    * Bir kiracı için çalışan bir CSP iş ortağıysanız, kiracının veya kiracının birincil etki alanı adını kullanarak müşteriyi kiracı olarak belirtin. **Connect-AzAccount-Tenant** fabrikam.com bir örnektir.

6. Hesapla birlikte kullanmak istediğiniz aboneliği ilişkilendirin, çünkü bir hesap birden fazla aboneliğe sahip olabilir.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Azure Backup ilk kez kullanıyorsanız, Azure kurtarma hizmetleri sağlayıcısını aboneliğinize kaydetmek için **register-AzResourceProvider** cmdlet 'ini kullanın.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Sağlayıcıların başarıyla kaydedildiğini doğrulayın:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Komut çıkışında, **Registrationstate** ' ın **kayıtlı**olarak değiştiğini doğrulayın. Değilse, **register-AzResourceProvider** cmdlet 'ini tekrar çalıştırın.

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kurtarma Hizmetleri Kasası oluşturmak için bu adımları izleyin.

Kurtarma Hizmetleri Kasası bir Kaynak Yöneticisi kaynağıdır, bu nedenle onu bir kaynak grubuna yerleştirmeniz gerekir. Var olan bir kaynak grubunu kullanabilir veya **New-AzResourceGroup** cmdlet 'i ile bir kaynak grubu oluşturabilirsiniz. Bir kaynak grubu oluşturduğunuzda, kaynak grubu için adı ve konumu belirtin.

1. Kasa, bir kaynak grubuna yerleştirilir. Mevcut bir kaynak grubunuz yoksa, [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)ile yeni bir tane oluşturun. Bu örnekte, Batı ABD bölgesinde yeni bir kaynak grubu oluşturacağız.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Kasayı oluşturmak için [New-Azrecoveryserviceskasa](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet 'ini kullanın. Kaynak grubu için kullanılan kasa için aynı konumu belirtin.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Kasa depolaması için kullanılacak artıklık türünü belirtin.

    * [Yerel olarak yedekli depolama](../storage/common/storage-redundancy-lrs.md) veya coğrafi olarak [yedekli depolama](../storage/common/storage-redundancy-grs.md)kullanabilirsiniz.
    * Aşağıdaki örnek, **testkasasının** [set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd Için **-BackupStorageRedundancy** seçeneğini **geoyedekli**olarak ayarlar.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Bir abonelikteki kasaları görüntüleme

Abonelikteki tüm kasaları görüntülemek için [Get-Azrecoveryserviceskasasını](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)kullanın.

```powershell
Get-AzRecoveryServicesVault
```

Çıktı aşağıdakine benzer. İlişkili kaynak grubu ve konumu sağlanır.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Kasa bağlamını ayarla

Kasa nesnesini bir değişkende depolayın ve kasa bağlamını ayarlayın.

* Birçok Azure Backup cmdlet 'i, giriş olarak kurtarma hizmetleri Kasası nesnesini gerektirir, bu nedenle kasa nesnesini bir değişkende depolamak yararlıdır.
* Kasa bağlamı, kasada korunan veri türüdür. [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)ile ayarlayın. Bağlam ayarlandıktan sonra, sonraki tüm cmdlet 'ler için geçerli olur.

Aşağıdaki örnek, **testkasası**için kasa bağlamını ayarlar.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Kasa KIMLIĞINI getir

Azure PowerShell yönergelerine uygun olarak kasa bağlamı ayarını kullanımdan kaldırmayı planlıyoruz. Bunun yerine, kasa KIMLIĞINI depolayıp alabilir ve ilgili komutlara aşağıdaki gibi geçirebilirsiniz:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Yedekleme ilkesi yapılandırma

Yedekleme ilkesi yedeklemeler için zamanlamayı ve yedekleme kurtarma noktalarının ne kadar süreyle tutulacağını belirtir:

* Yedekleme ilkesi en az bir bekletme ilkesiyle ilişkilendirilir. Bir bekletme ilkesi, bir kurtarma noktasının silinmeden önce ne kadar süreyle saklanacağını tanımlar.
* [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)kullanarak varsayılan yedekleme ilkesi bekletmesini görüntüleyin.
* [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)kullanarak varsayılan yedekleme ilkesi zamanlamasını görüntüleyin.
* Yeni bir yedekleme ilkesi oluşturmak için [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet 'ini kullanırsınız. Zamanlama ve bekletme ilkesi nesnelerini girin.

Varsayılan olarak, bir başlangıç saati zamanlama Ilkesi nesnesinde tanımlanmıştır. Başlangıç saatini istenen başlangıç saatine dönüştürmek için aşağıdaki örneği kullanın. İstenen başlangıç saati UTC biçiminde de olmalıdır. Aşağıdaki örnek, günlük yedeklemeler için istenen başlangıç zamanının 01:00. UTC olduğunu varsayar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Başlangıç saatini yalnızca 30 dakikalık katları olarak sağlamanız gerekir. Yukarıdaki örnekte, yalnızca "01:00:00" veya "02:30:00" olabilir. Başlangıç saati "01:15:00" olamaz

Aşağıdaki örnek, zaman çizelgesi ilkesini ve bekletme ilkesini değişkenler halinde depolar. Daha sonra bu değişkenleri yeni bir ilke (**Newsqlpolicy**) için parametre olarak kullanır. **Newsqlpolicy** günlük "Full" yedeklemesi alır, 180 gün boyunca saklar ve 2 saatte bir günlük yedeklemesi alır

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Çıktı aşağıdakine benzer.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Yedeklemeyi etkinleştir

### <a name="registering-the-sql-vm"></a>SQL VM 'yi kaydetme

Azure VM yedeklemeleri ve Azure dosya paylaşımları için yedekleme hizmeti bu Azure Resource Manager kaynaklarına bağlanabilir ve ilgili ayrıntıları getirir. SQL bir Azure VM içindeki bir uygulama olduğundan, yedekleme hizmeti uygulamaya erişmek ve gerekli ayrıntıları getirmek için izne ihtiyaç duyuyor. Bunu yapmak için, bir kurtarma hizmetleri kasasıyla SQL uygulamasını içeren Azure VM 'yi *' kaydetmeniz '* gerekir. Bir SQL VM 'yi bir kasaya kaydettikten sonra, SQL veritabanlarını yalnızca bu kasaya koruyabilirsiniz. VM 'yi kaydettirmek için [register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet 'ini kullanın.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Komut bu kaynağın bir ' yedekleme kapsayıcısı ' döndürür ve durum ' kaydedildi ' olarak değişir

> [!NOTE]
> Zorla parametresi verilmezse, kullanıcıdan ' Bu kapsayıcı için korumayı devre dışı bırakmak istiyor musunuz ' metnini bir metinle onaylamasını istenir. Lütfen bu metni yoksayın ve onaylamak için "Y" deyin. Bu bilinen bir sorundur ve, zorla parametresinin metnini ve gereksinimini kaldırmak için çalışıyoruz.

### <a name="fetching-sql-dbs"></a>SQL DB 'Leri getiriliyor

Kayıt tamamlandığında, yedekleme hizmeti VM 'deki tüm kullanılabilir SQL bileşenlerini listeleyebilir. Henüz bu kasaya yedeklenmek üzere tüm SQL bileşenlerini görüntülemek için [Get-Azrecoveryservicesbackupkorunabilir TableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 'ini kullanın

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

Çıktı, bu kasaya kayıtlı tüm SQL VM 'lerde, öğe türü ve ServerName olan tüm korumasız SQL bileşenlerini gösterir. '-Container ' parametresini geçirerek belirli bir SQL VM 'ye daha fazla filtre uygulayabilir veya benzersiz bir SQL öğesine ulaşmak için ItemType bayrağıyla birlikte ' name ' ve ' ServerName ' birleşimini kullanın.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Yedeklemeyi yapılandırma

Gerekli SQL DB ve bu ilkenin yedeklenmesi gereken ilkeye sahip olduğumuz için, bu SQL VERITABANı için yedeklemeyi yapılandırmak üzere [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet 'ini kullanabilirsiniz.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Komut, yapılandırma yedeklemesi tamamlanana kadar bekler ve aşağıdaki çıktıyı döndürür.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Yeni SQL DB 'Leri getiriliyor

Makine kaydedildikten sonra Backup hizmeti, daha sonra kullanılabilir olan veritabanları ayrıntılarını getirir. Kullanıcı daha sonra kayıtlı makineye SQL DBs/SQL örnekleri eklerse, tüm korumasız veritabanlarını (yeni eklenen olanlar dahil) yeniden almak için yedekleme hizmetini el ile yeni bir ' sorgulama ' gerçekleştirecek şekilde tetiklemeniz gerekir. SQL VM üzerinde [Initialize-Azrecoveryservicesbackupıtem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 'ini kullanarak yeni bir sorgu gerçekleştirin. Komut işlem tamamlanana kadar bekler. Daha sonra, en son korumasız SQL bileşenlerinin listesini almak için [Get-Azrecoveryservicesbackupkorunabilir öğe](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 'ini kullanın

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

İlgili korunabilir öğeler alındıktan sonra, [yukarıdaki bölümde](#configuring-backup)belirtildiği gibi yedeklemeleri etkinleştirin.
Bunlardan biri yeni DBs 'yi el ile algılamak istemiyor, [aşağıda](#enable-autoprotection)açıklandığı gibi, tekrar korumayı kabul edebilirler.

## <a name="enable-autoprotection"></a>Oto korumayı etkinleştir

Kullanıcı, gelecekte eklenen tüm veritabanları belirli bir ilkeyle otomatik olarak korunduğu için yedeklemeyi yapılandırabilir. Oto korumayı etkinleştirmek için [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet 'ini kullanın.

Yönerge gelecek tüm veritabanlarını yedekleyebileceğinizden, işlem bir SQLInstance düzeyinde yapılır.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Oto Koruma amacı verildiğinde, yeni eklenen veritabanlarını getirmek için makineye yapılan sorgu her 8 saatte bir zamanlanmış arka plan görevi olarak gerçekleşir.

## <a name="restore-sql-dbs"></a>SQL veritabanlarını geri yükle

Azure Backup, Azure VM 'lerinde çalışan SQL Server veritabanlarını şu şekilde geri yükleyebilir:

* İşlem günlüğü yedeklerini kullanarak belirli bir tarih veya saate (ikinci olarak) geri yükleyin. Azure Backup, uygun tam değişiklik yedeklemesini ve seçilen saate göre geri yüklemek için gereken günlük yedeklemeleri zincirini otomatik olarak belirler.
* Belirli bir kurtarma noktasına geri yüklemek için belirli bir tam veya değişiklik yedeklemesini geri yükleyin.

SQL DBs 'yi geri yüklemeden önce [burada](restore-sql-database-azure-vm.md#prerequisites) bahsedilen önkoşulları denetleyin.

İlk olarak [Get-Azrecoveryservicesbackupıtem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet 'ini kullanarak ılgılı yedeklenen SQL DB 'yi getirin.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>İlgili geri yükleme süresini getir

Yukarıda özetlenen Kullanıcı, yedeklenen SQL VERITABANıNı tam/fark kopyasına **veya** bir günlük noktasına geri yükleyebilir.

#### <a name="fetch-distinct-recovery-points"></a>Ayrı kurtarma noktalarını getir

Yedeklenen SQL DB için farklı (tam/fark) kurtarma noktaları getirmek üzere [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) komutunu kullanın.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

Çıktı aşağıdaki örneğe benzer

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

İlgili kurtarma noktasını getirmek için ' Recoverypointıd ' filtresini veya dizi filtresini kullanın.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Fetch-In-Time kurtarma noktası

Kullanıcı VERITABANıNı belirli bir noktaya geri yüklemek isterse [Get-Azrecoveryservicesbackuprecoverylogzincirps](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) cmdlet 'ini kullanın. Cmdlet 'i, bu SQL yedekleme öğesi için bozuk ve sürekli bir günlük zincirinin başlangıç ve bitiş zamanlarını temsil eden tarihlerin listesini döndürür. İstenen zaman noktası bu Aralık içinde olmalıdır.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Çıktı aşağıdaki örneğe benzer olacaktır.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Yukarıdaki çıktı, kullanıcının görüntülenen başlangıç saati ve bitiş saati arasındaki herhangi bir zaman noktasına geri yükleme olabileceği anlamına gelir. Süreler UTC olarak. Yukarıda gösterilen aralığın içinde olan, PS 'de herhangi bir zaman noktası oluşturun.

> [!NOTE]
> Geri yükleme için bir günlük noktası seçildiğinde, Kullanıcı, veritabanının geri yüklendiği başlangıç noktasını, tam yedeklemeyi belirtmemelidir. Azure Backup hizmeti kurtarma planının tamamını, yani seçim yapılacak tam yedeklemenin, hangi günlük yedeklemelerin uygulanacağını vb. ele alır.

### <a name="determine-recovery-configuration"></a>Kurtarma yapılandırmasını belirleme

SQL DB geri yükleme durumunda aşağıdaki geri yükleme senaryoları desteklenir.

* Yedeklenen SQL DB 'yi başka bir kurtarma noktasındaki verilerle geçersiz kılma-Originalworkloadresstore
* SQL DB 'yi aynı SQL örneğine yeni bir VERITABANı olarak geri yükleme-Alternateworkloadresstore
* SQL DB 'yi başka bir SQL VM 'deki başka bir SQL örneğine yeni bir VERITABANı olarak geri yükleme-Alternateworkloadresstore

İlgili kurtarma noktasını (farklı veya günlük zaman) geçirdikten sonra, kurtarma yapılandırma nesnesini istenen kurtarma planına göre getirmek için [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet 'ini kullanın.

#### <a name="original-workload-restore"></a>Özgün iş yükü geri yükleme

Yedeklenen VERITABANıNı kurtarma noktasındaki verilerle geçersiz kılmak için, aşağıdaki örnekte gösterildiği gibi doğru bayrağı ve ilgili kurtarma noktasını belirtmeniz yeterlidir.

##### <a name="original-restore-with-distinct-recovery-point"></a>Ayrı kurtarma noktasıyla orijinal geri yükleme

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Günlük noktasındaki özgün geri yükleme zamanı

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Alternatif iş yükü geri yükleme

> [!IMPORTANT]
> Yedeklenen SQL DB, yeni bir VERITABANı olarak yalnızca başka bir SQLInstance 'a geri yüklenebilir ve bu kasaya bir Azure VM ' kaydı ' kaydedilir.

Yukarıda özetlenen şekilde, hedef SQLInstance başka bir Azure VM içinde yer alıyorsa, [Bu kasaya kaydedildiğinden](#registering-the-sql-vm) ve Ilgili SQLInstance 'ın korunabilir bir öğe olarak göründüğünden emin olun.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Ardından, aşağıda gösterildiği gibi yalnızca ilgili kurtarma noktasını, hedef SQL örneğini sağ bayrağıyla geçirin.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Farklı kurtarma noktasıyla alternatif geri yükleme

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Günlük noktası ile alternatif geri yükleme zamanı

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

[Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet 'inden elde edilen son kurtarma noktası yapılandırma nesnesi, geri yükleme için tüm ilgili bilgileri içerir ve aşağıda gösterildiği gibi.

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

Geri yüklenen VERITABANı adını, Overwritewlifvar, NoRecoveryMode ve targetPhysicalPath alanlarını düzenleyebilirsiniz. Aşağıda gösterildiği gibi, hedef dosya yolları için daha fazla ayrıntı alın.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

İlgili PS özelliklerini dize değerleri olarak aşağıda gösterildiği gibi ayarlayın.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> Geri yükleme işlemi yapılandırma nesnesini temel alacak olduğundan, son kurtarma yapılandırma nesnesinin gerekli ve doğru değerlere sahip olduğundan emin olun.

### <a name="restore-with-relevant-configuration"></a>İlgili yapılandırmayla geri yükleme

İlgili kurtarma yapılandırma nesnesi alındıktan ve doğrulandıktan sonra geri yükleme işlemini başlatmak için [restore-Azrecoveryservicesbackupıtem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet 'ini kullanın.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Geri yükleme işlemi izlenecek bir iş döndürür.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>SQL yedeklemelerini yönetme

### <a name="on-demand-backup"></a>İsteğe bağlı yedekleme

Bir VERITABANı için yedekleme etkinleştirildikten sonra, Kullanıcı [Backup-Azrecoveryservicesbackupıtem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet 'INI kullanarak veritabanı için isteğe bağlı bir yedekleme da tetiklenebilir. Aşağıdaki örnek, bir SQL DB 'de sıkıştırma etkinken tam yedeklemeyi tetikler ve tam yedekleme 60 gün boyunca korunur.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

İsteğe bağlı yedekleme komutu izlenecek bir iş döndürür.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Çıkış kaybolursa veya ilgili Iş KIMLIĞINI almak isterseniz, Azure Backup hizmetten [işlerin listesini alın](#track-azure-backup-jobs) ve ardından ayrıntılarını ve ayrıntılarını izleyin.

### <a name="change-policy-for-backup-items"></a>Yedekleme öğeleri için ilkeyi değiştirme

Kullanıcı var olan ilkeyi değiştirebilir veya Policy1 ' den Policy2 ' ye yedeklenmiş öğenin ilkesini değiştirebilir. Yedeklenen bir öğeye yönelik ilkeleri değiştirmek için ilgili ilkeyi ve yedekleme öğesini getirin ve parametresi olarak Backup öğesiyle [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) komutunu kullanın.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Komut, yapılandırma yedeklemesi tamamlanana kadar bekler ve aşağıdaki çıktıyı döndürür.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>SQL VM 'lerini yeniden kaydetme

> [!WARNING]
> Yeniden kayıt denemeden önce hata belirtilerini ve nedenleri anlamak için bu [belgeyi](backup-sql-server-azure-troubleshoot.md#re-registration-failures) okuduğunuzdan emin olun

SQL VM 'nin yeniden kaydedilmesini tetiklemek için ilgili yedekleme kapsayıcısını getirin ve Register cmdlet 'ine geçirin.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Korumayı Durdur

#### <a name="retain-data"></a>Verileri tutma

Kullanıcı korumayı durdurmayı istiyorsa, [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet 'ini kullanabilirler. Bu, zamanlanmış yedeklemeleri durdurur, ancak şu anda yedeklenene kadar yedeklenen veriler sürekli olarak korunur.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Yedekleme verilerini silme

Kasadaki depolanan yedekleme verilerini tamamen kaldırmak için '-RemoveRecoveryPoints ' bayrağını [' devre dışı bırak](#retain-data)' seçeneğini eklemeniz yeterlidir.

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Otomatik korumayı devre dışı bırak

Bir SQLInstance üzerinde otomatik koruma yapılandırıldıysa, Kullanıcı [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet 'ini kullanarak bunu devre dışı bırakabilir.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>SQL VM kaydını sil

Bir SQL Server 'ın tüm veritabanları [artık korunmuyorsa ve yedekleme verisi](#delete-backup-data)yoksa, kullanıcı SQL sanal makinesinin kaydını bu kasadan silmeyi sağlayabilir. Yalnızca Kullanıcı, veritabanlarını başka bir kasaya koruyabilir. SQL VM kaydını silmek için [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet 'ini kullanın.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Azure Backup işleri izleme

Azure Backup yalnızca SQL Backup 'daki Kullanıcı tarafından tetiklenen işleri izlediğine dikkat edin. Zamanlanan yedeklemeler (günlük yedeklemeleri dahil) portalda/PowerShell 'de görünmez. Ancak, herhangi bir zamanlanan iş başarısız olursa, portalda bir [yedekleme uyarısı](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) oluşturulur ve gösterilir. Tüm zamanlanmış işleri ve ilgili diğer bilgileri izlemek için [Azure izleyici 'Yi kullanın](backup-azure-monitoring-use-azuremonitor.md) .

Kullanıcılar, yedekleme gibi zaman uyumsuz işlerin [çıkışında](#on-demand-backup) döndürülen JobId ile isteğe bağlı/kullanıcı tarafından tetiklenen işlemleri izleyebilir. İşi ve ayrıntılarını izlemek için [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet 'ini kullanın.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

İsteğe bağlı işlerin ve bunların durumlarının listesini Azure Backup hizmetten almak için [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet 'ini kullanın. Aşağıdaki örnek, tüm sürmekte olan SQL işlerini döndürür.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Devam eden bir işi iptal etmek için [stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet 'ini kullanın.

## <a name="managing-sql-always-on-availability-groups"></a>SQL Always on kullanılabilirlik gruplarını yönetme

SQL Always on kullanılabilirlik grupları için, kullanılabilirlik grubunun (AG) [Tüm düğümlerini](#registering-the-sql-vm) kaydettiğinizden emin olun. Tüm düğümler için kayıt yapıldıktan sonra, bir SQL kullanılabilirlik grubu nesnesi korunabilir öğeler altında mantıksal olarak oluşturulur. SQL AG kapsamındaki veritabanları ' SQLDatabase ' olarak listelenecektir. Düğümler tek başına örnekleri olarak görünür ve bunların altındaki varsayılan SQL veritabanları da SQL veritabanları olarak listelenecektir.

Örneğin, bir SQL AG 'nin iki düğüm olduğunu varsayalım: ' SQL-Server-0 ' ve ' SQL-Server-1 ' ve 1 SQL AG DB. Her iki düğüm de kaydedildikten sonra, Kullanıcı [korunabilir öğeleri listelerse](#fetching-sql-dbs), aşağıdaki bileşenleri listeler

* SQLAvailabilityGroup olarak bir SQL AG nesnesi-korunabilir öğe türü
* SQLDatabase olarak SQL AG DB korumalı tablo öğesi türü
* SQL-Server-0-korunabilir öğe türü SQLInstance olarak
* SQL-Server-1-korunabilir öğe türü SQLInstance olarak
* SQL-Server-0-korunabilir öğe türü altındaki varsayılan SQL DBs (Master, model, msdb), SQLDatabase olarak
* SQL-Server-1-korunabilir öğe türü altındaki herhangi bir varsayılan SQL DBs (Master, model, msdb), SQLDatabase olarak

[yedekleme kapsayıcıları listelendiğinde](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)SQL-Server-0, SQL-Server-1 da "AzureVMAppContainer" olarak listelenecektir.

[Yedeklemeyi etkinleştirmek](#configuring-backup) IÇIN ilgili SQL veritabanını yapmanız yeterlidir ve isteğe bağlı [yedekleme](#on-demand-backup) ve [geri yükleme PS cmdlet 'leri](#restore-sql-dbs) aynı.
