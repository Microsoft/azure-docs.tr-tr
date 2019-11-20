---
title: Windows Server 'ı Azure 'a yedeklemek için PowerShell 'i kullanma
description: Bu makalede, PowerShell kullanarak Windows Server veya Windows istemcisinde Azure Backup ayarlama ve yedekleme ve kurtarmayı yönetme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 6285b7fc6493090ab0bead5f00124a6eaa02dc7e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172437"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>PowerShell kullanarak Windows Server/Windows İstemcisi için Azure’a yedekleme dağıtma ve yönetme

Bu makalede, PowerShell kullanarak Windows Server veya Windows istemcisinde Azure Backup ayarlama ve yedekleme ve kurtarmayı yönetme işlemlerinin nasıl yapılacağı gösterilir.

## <a name="install-azure-powershell"></a>Azure PowerShell'i yükleme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Başlamak için [en son PowerShell sürümünü yüklemelisiniz](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Kurtarma hizmetleri kasası oluşturma

Aşağıdaki adımlar, bir kurtarma hizmetleri Kasası oluşturma konusunda size yol açabilir. Kurtarma Hizmetleri Kasası, bir yedekleme kasasından farklı.

1. Azure Backup ilk kez kullanıyorsanız, Azure kurtarma hizmeti sağlayıcısını aboneliğinizle kaydettirmek için **register-AzResourceProvider** cmdlet 'ini kullanmanız gerekir.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Kurtarma Hizmetleri Kasası bir ARM kaynağıdır, bu nedenle onu bir kaynak grubuna yerleştirmeniz gerekir. Var olan bir kaynak grubunu kullanabilir veya yeni bir tane oluşturabilirsiniz. Yeni bir kaynak grubu oluştururken, kaynak grubu için adı ve konumu belirtin.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Yeni kasayı oluşturmak için **New-Azrecoveryserviceskasa** cmdlet 'ini kullanın. Kaynak grubu için kullanılan kasa için aynı konumu belirttiğinizden emin olun.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Kullanılacak depolama yedekliliği türünü belirtin; [yerel olarak yedekli depolama (LRS)](../storage/common/storage-redundancy-lrs.md) veya coğrafi olarak [yedekli depolama (GRS)](../storage/common/storage-redundancy-grs.md)kullanabilirsiniz. Aşağıdaki örnek, Testkasası için-BackupStorageRedundancy seçeneğinin Geoyedekli olarak ayarlandığını gösterir.

   > [!TIP]
   > Çoğu Azure Backup cmdlet’i, girdi olarak Kurtarma Hizmetleri kasasını gerektirir. Bu nedenle, Yedekleme Kurtarma Hizmetleri kasasının bir değişkende depolanması uygundur.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Bir abonelikteki kasaları görüntüleme

Geçerli abonelikteki tüm kasaların listesini görüntülemek için **Get-Azrecoveryserviceskasasını** kullanın. Bu komutu kullanarak yeni bir kasanın oluşturulduğunu denetleyebilir veya abonelikte hangi kasaların kullanılabilir olduğunu görebilirsiniz.

**Get-Azrecoveryserviceskasa**komutunu çalıştırın ve aboneliğin tüm kasaları listelenir.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Azure Backup aracısını yükleme

Azure Backup aracısını yüklemeden önce, yükleyicinin Windows Server üzerinde indirilip mevcut olması gerekir. Yükleyicinin en son sürümünü [Microsoft Indirme merkezi](https://aka.ms/azurebackup_agent) ' nden veya kurtarma hizmetleri kasasının Pano sayfasından edinebilirsiniz. Yükleyiciyi * C:\Downloads\*gibi kolay erişilebilen bir konuma kaydedin.

Alternatif olarak, indirme 'yi almak için PowerShell 'i kullanın:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Aracıyı yüklemek için, yükseltilmiş bir PowerShell konsolunda aşağıdaki komutu çalıştırın:

```powershell
MARSAgentInstaller.exe /q
```

Bu, aracıyı tüm varsayılan seçeneklerle birlikte kurar. Yükleme arka planda birkaç dakika sürer. */Nu* seçeneğini belirtmezseniz, tüm güncelleştirmeleri denetlemek için yüklemenin sonunda **Windows Update** penceresi açılır. Yüklendikten sonra, aracı yüklü programlar listesinde görünür.

Yüklü programların listesini görmek için **Denetim masası** > **Programlar** > **Programlar ve Özellikler**' e gidin.

![Aracı yüklendi](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Yükleme seçenekleri

Komut satırı aracılığıyla kullanılabilen tüm seçenekleri görmek için aşağıdaki komutu kullanın:

```powershell
MARSAgentInstaller.exe /?
```

Mevcut seçenekler şunlardır:

| Seçenek | Ayrıntılar | Varsayılan |
| --- | --- | --- |
| anahtarın |Sessiz yükleme |- |
| /p: "konum" |Azure Backup aracısının yükleme klasörünün yolu. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s: "konum" |Azure Backup aracısına ait önbellek klasörünün yolu. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Microsoft Update için kabul etme |- |
| /nu |Yükleme tamamlandıktan sonra güncelleştirmeleri denetleme |- |
| belirtilmediyse |Microsoft Azure Kurtarma Hizmetleri Aracısı 'nı kaldırır |- |
| /pH |Proxy ana bilgisayar adresi |- |
| /Po |Proxy ana bilgisayar bağlantı noktası numarası |- |
| /pu |Proxy konak Kullanıcı adı |- |
| /PW |Proxy parolası |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Windows Server veya Windows istemci makinesini bir kurtarma hizmetleri kasasına kaydetme

Kurtarma Hizmetleri kasasını oluşturduktan sonra, en son aracıyı ve kasa kimlik bilgilerini indirin ve C:\Downloads. gibi uygun bir konumda saklayın.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>PS az Module kullanarak kaydetme

En son az bir PowerShell modülünde, temeldeki platform sınırlamaları nedeniyle kasa kimlik bilgilerini indirmek kendinden imzalı bir sertifika gerektirir. Aşağıdaki örnek, kendinden imzalı bir sertifikayı nasıl sağlayabileceğiniz ve kasa kimlik bilgilerini nasıl indirecek gösterilmektedir.

```powershell
$Vault = Get-AzRecoveryServicesVault -ResourceGroupName $rgName -Name $VaultName
$cert = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname xxxxxxxxxxxxx
$certificate =[System.Convert]::ToBase64String($cert.RawData)
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Certificate $certificate -Vault $vault -Backup -Path $CredsPath
```

Windows Server veya Windows istemci makinesinde, makineyi kasaya kaydetmek için [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet 'ini çalıştırın.
Bu ve yedekleme için kullanılan diğer cmdlet 'ler, Mars Agentınstaller 'ın yükleme işleminin bir parçası olarak eklediği MSONLINE modülünden alınır.

Aracı yükleyicisi $Env:P SModulePath değişkenini güncelleştirmez. Bu, modülün otomatik yükünün başarısız olduğu anlamına gelir. Bu sorunu gidermek için aşağıdakileri yapabilirsiniz:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Alternatif olarak, aşağıdaki şekilde el ile modülünü komut dosyasına yükleyebilirsiniz:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Çevrimiçi Yedekleme cmdlet 'lerini yükledikten sonra, kasa kimlik bilgilerini kaydedersiniz:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Kasa kimlik bilgileri dosyasını belirtmek için göreli yollar kullanmayın. Cmdlet 'e giriş olarak mutlak bir yol sağlamanız gerekir.
>
>

## <a name="networking-settings"></a>Ağ ayarları

Windows makinenin Internet 'e bağlantısı bir ara sunucu üzerinden olduğunda, ara sunucu ayarları da aracıya temin edilebilir. Bu örnekte, proxy sunucusu yoktur, bu nedenle ara sunucu ile ilgili tüm bilgileri açıkça temizliyoruz.

Bant genişliği kullanımı Ayrıca, haftanın belirli bir gün kümesi için `work hour bandwidth` ve `non-work hour bandwidth` seçenekleriyle denetlenebilir.

Proxy ve bant genişliği ayrıntılarının ayarlanması, [set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) cmdlet 'i kullanılarak yapılır:

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Şifreleme ayarları

Azure Backup gönderilen yedekleme verileri verilerin gizliliğini korumak için şifrelenir. Şifreleme parolası, geri yükleme sırasında verilerin şifresini çözmek için "paroladır".

Azure portal **Kurtarma Hizmetleri Kasası** bölümünde **ayarlar** > **Özellikler** > **güvenlik PIN** 'i ' nin altında **Oluştur**' u seçerek bir güvenlik PIN 'i oluşturmanız gerekir. Ardından, bunu komutunda `generatedPIN` olarak kullanın:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Parola bilgilerini ayarladıktan sonra güvende tutun ve güvende tutun. Bu parola olmadan Azure 'daki verileri geri alamazsınız.
>
>

## <a name="back-up-files-and-folders"></a>Dosya ve klasörleri yedekleme

Windows Server ve istemcilerden Azure Backup tüm yedeklemeler bir ilkeye tabidir. İlke üç bölümden oluşur:

1. Yedeklemelerin ne zaman alınması ve hizmetle eşitlenmesi gerektiğini belirten bir **yedekleme zamanlaması** .
2. Azure 'da kurtarma noktalarının ne kadar süreyle saklanacağını belirten bir **bekletme zamanlaması** .
3. Neyin yedeklenmesi gerektiğini belirleyen bir **dosya ekleme/çıkarma belirtimi** .

Bu belgede, yedeklemeyi otomatikleştirdiğimiz için hiçbir şey yapılandırılmadığını varsayacağız. [Yeni-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) cmdlet 'ini kullanarak yeni bir yedekleme ilkesi oluşturarak başlayacağız.

```powershell
$NewPolicy = New-OBPolicy
```

Bu noktada, ilke boştur ve diğer cmdlet 'ler, yedeklemelerin ne zaman ekleneceğini, hangilerinin yedeklerin nerede depolanacağını belirlemek için gerekir.

### <a name="configuring-the-backup-schedule"></a>Yedekleme zamanlamasını yapılandırma

Bir ilkenin üç bölümünün ilki, [New-OBSchedule](https://technet.microsoft.com/library/hh770401) cmdlet 'i kullanılarak oluşturulan yedekleme zamanlamadır. Yedekleme zamanlaması, yedeklemelerin ne zaman alınması gerektiğini tanımlar. Bir zamanlama oluştururken iki giriş parametresi belirtmeniz gerekir:

* Yedeklemenin çalışması gereken **haftanın günleri** . Yedekleme işini yalnızca bir gün veya haftanın her gününde ya da arasında herhangi bir bileşim için çalıştırabilirsiniz.
* Yedeklemenin çalışması gereken **gün sayısı** . Yedeklemenin tetiklenmesi için günün en fazla üç farklı kez tanımlayabilirsiniz.

Örneğin, her Cumartesi ve Pazar için 4PM 'de çalışan bir yedekleme ilkesi yapılandırabilirsiniz.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Yedekleme zamanlamasının bir ilkeyle ilişkilendirilmesi gerekir ve bu, [set-OBSchedule](https://technet.microsoft.com/library/hh770407) cmdlet 'i kullanılarak elde edilebilir.

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Bekletme ilkesi yapılandırma

Bekletme ilkesi, yedekleme işlerinin oluşturduğu kurtarma noktalarının ne kadar süreyle korunacağını tanımlar. [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) cmdlet 'ini kullanarak yeni bir bekletme ilkesi oluştururken, yedekleme kurtarma noktalarının Azure Backup ile saklanması gereken gün sayısını belirtebilirsiniz. Aşağıdaki örnekte yedi günlük bir bekletme ilkesi ayarlanır.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Bekletme ilkesi, [set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405)cmdlet 'i kullanılarak ana ilkeyle ilişkilendirilmelidir:

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Yedeklenecek dosyaları dahil etme ve hariç tutma

Bir `OBFileSpec` nesnesi, bir yedeklemeye dahil edilecek ve dışlanacak dosyaları tanımlar. Bu, bir makinedeki korunan dosya ve klasörlerin kapsamını oluşturan bir kurallar kümesidir. Gereken birçok dosya ekleme veya dışlama kuralına sahip olabilir ve bunları bir ilkeyle ilişkilendirebilirsiniz. Yeni bir Obdosyabelirtimi nesnesi oluştururken şunları yapabilirsiniz:

* Dahil edilecek dosya ve klasörleri belirtin
* Dışlanacak dosya ve klasörleri belirtin
* Verilerin özyinelemeli yedeklemesini bir klasöre (veya) yalnızca belirtilen klasördeki en üst düzey dosyaların yedeklenip yedeklenmeyeceğini belirtin.

İkincisi, New-Obdosyabelirtimi komutunda-Nonözyinelemeli bayrağı kullanılarak elde edilir.

Aşağıdaki örnekte, C: ve D: birimi Yedeklenecek ve işletim sistemi ikili dosyalarını Windows klasöründe ve tüm geçici klasörlerde dışarıda bırakacağız. Bunu yapmak için, [New-Obdosyabelirtimi](https://technet.microsoft.com/library/hh770408) cmdlet 'ini kullanarak iki dosya belirtimi oluşturacağız. ekleme için bir tane ve dışlama için bir tane. Dosya belirtimleri oluşturulduktan sonra, [Add-Obdosyabelirtimi](https://technet.microsoft.com/library/hh770424) cmdlet 'i kullanılarak ilkeyle ilişkilendirilir.

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>MABS aracısında Windows Server sistem durumunu yedekleme

Bu bölüm, MABS aracısında sistem durumunu ayarlamak için PowerShell komutunu ele almaktadır

### <a name="schedule"></a>Zamanlama

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Bekletme

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Zamanlamayı ve bekletmeyi yapılandırma

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>İlke doğrulanıyor

```powershell
Get-OBSystemStatePolicy
 ```

### <a name="applying-the-policy"></a>İlke uygulanıyor

Artık ilke nesnesi tamamlanmıştır ve ilişkili bir yedekleme zamanlaması, bekletme ilkesi ve bir dosya içerme/dışlama listesi bulunur. Bu ilke artık kullanım Azure Backup için uygulanabilir. Yeni oluşturulan ilkeyi uygulamadan önce [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) cmdlet 'ini kullanarak sunucuyla ilişkili mevcut bir yedekleme ilkesi olmadığından emin olun. İlke kaldırıldığında onay istenir. Onayı atlamak için cmdlet 'iyle birlikte `-Confirm:$false` bayrağını kullanın.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

İlke nesnesini kaydetme, [set-OBPolicy](https://technet.microsoft.com/library/hh770421) cmdlet 'i kullanılarak yapılır. Bu ayrıca onay ister. Onayı atlamak için cmdlet 'iyle birlikte `-Confirm:$false` bayrağını kullanın.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Mevcut yedekleme ilkesinin ayrıntılarını [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) cmdlet 'ini kullanarak görüntüleyebilirsiniz. Yedekleme zamanlaması için [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) cmdlet 'ini ve bekletme Ilkelerine yönelik [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) cmdlet 'ini kullanarak daha fazla ayrıntıya gidebilirsiniz

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>İsteğe bağlı yedekleme gerçekleştirme

Yedekleme ilkesi ayarlandıktan sonra, her zamanlama için yedeklemeler gerçekleşir. İsteğe bağlı bir yedeklemenin tetiklenmesi, [Start-OBBackup](https://technet.microsoft.com/library/hh770426) cmdlet 'i kullanılarak da mümkündür:

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Azure Backup verileri geri yükleme

Bu bölüm, Azure Backup verilerin kurtarılmasını otomatikleştirme adımlarında size yol gösterir. Bunun yapılması aşağıdaki adımları içerir:

1. Kaynak birimini seçme
2. Geri yüklenecek bir yedekleme noktası seçin
3. Geri yüklenecek bir öğe seçin
4. Geri yükleme işlemini tetikleme

### <a name="picking-the-source-volume"></a>Kaynak birim seçme

Bir öğeyi Azure Backup geri yüklemek için önce öğenin kaynağını belirlemeniz gerekir. Komutları bir Windows Server veya Windows istemcisi bağlamında yürütüyoruz, makine zaten tanımlandı. Kaynağı tanımlamak için bir sonraki adım, kendisini içeren birimi tanımlamaktır. Bu makineden yedeklenen birimlerin veya kaynakların listesi, [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) cmdlet 'i yürütülerek alınabilir. Bu komut, bu sunucudan/istemciden yedeklenen tüm kaynakların dizisini döndürür.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Geri yüklenecek bir yedekleme noktası seçme

[Get-Obrecoverableıtem](https://technet.microsoft.com/library/hh770399.aspx) cmdlet 'ini uygun parametrelerle yürüterek bir yedekleme noktaları listesi alırsınız. Bizim örneğimizde, *D:* kaynak birimi için en son yedekleme noktasını seçeceğiz ve belirli bir dosyayı kurtarmak için kullanacaksınız.

```powershell
$Rps = Get-OBRecoverableItem -Source $Source[1]
```

```Output
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```

Nesne `$Rps`, yedekleme noktalarının bir dizisidir. İlk öğe en son noktasıdır ve nth öğesi en eski noktasıdır. En son noktayı seçmek için `$Rps[0]`kullanacağız.

### <a name="choosing-an-item-to-restore"></a>Geri yüklenecek bir öğe seçme

Geri yüklenecek tam dosyayı veya klasörü belirlemek için, [Get-Obrecoverableıtem](https://technet.microsoft.com/library/hh770399.aspx) cmdlet 'ini yinelemeli olarak kullanın. Bu şekilde, klasör hiyerarşisi yalnızca `Get-OBRecoverableItem`ile gözatılabilir.

Bu örnekte, *finans. xls* dosyasını geri yüklemek istiyoruz, nesne `$FilesFolders[1]`kullanarak başvuru yapabilir.

```powershell
$FilesFolders = Get-OBRecoverableItem $Rps[0]
$FilesFolders
```

```Output
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
```

```powershell
$FilesFolders = Get-OBRecoverableItem $FilesFolders[0]
$FilesFolders
```

```Output
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Ayrıca, ```Get-OBRecoverableItem``` cmdlet 'ini kullanarak geri yüklenecek öğeler için arama yapabilirsiniz. Bizim örneğimizde, *finans. xls* ' i aramak için bu komutu çalıştırarak dosyada bir tanıtıcı edinebilirsiniz:

```powershell
$Item = Get-OBRecoverableItem -RecoveryPoint $Rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Geri yükleme işlemi tetikleniyor

Geri yükleme işlemini tetiklemek için, önce kurtarma seçeneklerini belirtmemiz gerekir. Bu, [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) cmdlet 'i kullanılarak yapılabilir. Bu örnekte, dosyaları *C:\Temp*' e geri yüklemek istediğimiz varsayılmaktadır. Ayrıca, *C:\Temp*hedef klasöründe zaten var olan dosyaları atlamak istediğimiz de varsayalım. Böyle bir kurtarma seçeneği oluşturmak için aşağıdaki komutu kullanın:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Şimdi, `Get-OBRecoverableItem` cmdlet 'inin çıktısından seçili `$Item` [Başlat-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) komutunu kullanarak geri yükleme işlemini tetikleyin:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Azure Backup Aracısı kaldırılıyor

Azure Backup aracısını kaldırmak aşağıdaki komut kullanılarak yapılabilir:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Aracıdan aracı ikililerini kaldırmak göz önünde bulundurmanız gereken bazı sonuçlara sahiptir:

* Bu, makineden dosya filtresini kaldırır ve değişikliklerin izlenmesi durdurulur.
* Tüm ilke bilgileri makineden kaldırılır, ancak ilke bilgileri hizmette depolanmaya devam eder.
* Tüm yedekleme zamanlamaları kaldırılır ve başka yedeklemeler alınmaz.

Ancak, Azure 'da depolanan veriler kalır ve sizin tarafınızdan bekletme ilkesi kurulumuna göre saklanır. Eski noktaları otomatik olarak yaşlanır.

## <a name="remote-management"></a>Uzaktan yönetim

Azure Backup Aracısı, ilkeler ve veri kaynakları etrafındaki tüm yönetim, PowerShell aracılığıyla uzaktan gerçekleştirilebilir. Uzaktan yönetilecek makinenin doğru hazırlanması gerekir.

Varsayılan olarak, WinRM hizmeti el ile başlatma için yapılandırılmıştır. Başlangıç türü *Otomatik* olarak ayarlanmalıdır ve hizmetin başlatılması gerekir. WinRM hizmetinin çalıştığını doğrulamak için Status özelliğinin değeri *çalışıyor*olmalıdır.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Uzaktan iletişim için PowerShell yapılandırılmalıdır.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

Makine artık, aracının yüklenmesinden itibaren uzaktan yönetilebilir. Örneğin, aşağıdaki betik aracıyı uzak makineye kopyalar ve onu kurar.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Sonraki adımlar

Windows Server/Client Azure Backup hakkında daha fazla bilgi için:

* [Azure Backup'a giriş](backup-introduction-to-azure-backup.md)
* [Windows sunucularını yedekleme](backup-configure-vault.md)
