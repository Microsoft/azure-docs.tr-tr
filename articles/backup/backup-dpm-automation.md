---
title: PowerShell kullanarak DPM iş yüklerini yedekleme
description: PowerShell kullanarak Data Protection Manager (DPM) için Azure Backup dağıtmayı ve yönetmeyi öğrenin
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: d3a8b2ff95957b69bab4932ce8a7e5a1ab4bfa44
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172414"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>PowerShell kullanarak Data Protection Manager (DPM) sunucuları için Azure’a yedekleme dağıtma ve yönetme

Bu makalede, PowerShell kullanarak bir DPM sunucusunda Azure Backup ayarlama ve yedekleme ve kurtarmayı yönetme işlemlerinin nasıl yapılacağı gösterilir.

## <a name="setting-up-the-powershell-environment"></a>PowerShell ortamını ayarlama

Azure 'a Data Protection Manager yedeklemeleri yönetmek için PowerShell kullanmadan önce, PowerShell 'de doğru ortama sahip olmanız gerekir. PowerShell oturumunun başlangıcında, doğru modülleri içeri aktarmak ve DPM cmdlet 'lerine doğru bir şekilde başvurmak için aşağıdaki komutu çalıştırdığınıza emin olun:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Kurulum ve kayıt

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Başlamak için [en son Azure PowerShell indirin](/powershell/azure/install-az-ps).

Aşağıdaki kurulum ve kayıt görevleri PowerShell ile otomatikleştirilebilir:

* Kurtarma Hizmetleri kasası oluşturma
* Azure Backup aracısını yükleme
* Azure Backup hizmetiyle kaydetme
* Ağ ayarları
* Şifreleme ayarları

## <a name="create-a-recovery-services-vault"></a>Kurtarma hizmetleri kasası oluşturma

Aşağıdaki adımlar, bir kurtarma hizmetleri Kasası oluşturma konusunda size yol açabilir. Kurtarma Hizmetleri Kasası, bir yedekleme kasasından farklı.

1. Azure Backup ilk kez kullanıyorsanız, Azure kurtarma hizmeti sağlayıcısını aboneliğinizle kaydettirmek için **register-AzResourceProvider** cmdlet 'ini kullanmanız gerekir.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Kurtarma Hizmetleri Kasası bir ARM kaynağıdır, bu nedenle onu bir kaynak grubuna yerleştirmeniz gerekir. Var olan bir kaynak grubunu kullanabilir veya yeni bir tane oluşturabilirsiniz. Yeni bir kaynak grubu oluştururken, kaynak grubu için adı ve konumu belirtin.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Yeni bir kasa oluşturmak için **New-Azrecoveryserviceskasa** cmdlet 'ini kullanın. Kaynak grubu için kullanılan kasa için aynı konumu belirttiğinizden emin olun.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Kullanılacak depolama yedekliliği türünü belirtin; [yerel olarak yedekli depolama (LRS)](../storage/common/storage-redundancy-lrs.md) veya coğrafi olarak [yedekli depolama (GRS)](../storage/common/storage-redundancy-grs.md)kullanabilirsiniz. Aşağıdaki örnek, Testkasası için-BackupStorageRedundancy seçeneğinin Geoyedekli olarak ayarlandığını gösterir.

   > [!TIP]
   > Çoğu Azure Backup cmdlet’i, girdi olarak Kurtarma Hizmetleri kasasını gerektirir. Bu nedenle, Yedekleme Kurtarma Hizmetleri kasasının bir değişkende depolanması uygundur.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Bir abonelikteki kasaları görüntüleme

Geçerli abonelikteki tüm kasaların listesini görüntülemek için **Get-Azrecoveryserviceskasasını** kullanın. Bu komutu kullanarak yeni bir kasanın oluşturulduğunu denetleyebilir veya abonelikte hangi kasaların kullanılabilir olduğunu görebilirsiniz.

Get-Azrecoveryserviceskasa komutunu çalıştırın ve aboneliğin tüm kasaları listelenir.

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

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Azure Backup aracısını bir DPM sunucusuna yükleme

Azure Backup aracısını yüklemeden önce, yükleyicinin Windows Server üzerinde indirilip mevcut olması gerekir. Yükleyicinin en son sürümünü [Microsoft Indirme merkezi](https://aka.ms/azurebackup_agent) ' nden veya kurtarma hizmetleri kasasının Pano sayfasından edinebilirsiniz. Yükleyiciyi * C:\Downloads\*gibi kolay erişilebilen bir konuma kaydedin.

Aracıyı yüklemek için **DPM sunucusundaki**yükseltilmiş bir PowerShell konsolunda aşağıdaki komutu çalıştırın:

```powershell
MARSAgentInstaller.exe /q
```

Bu, aracıyı tüm varsayılan seçeneklerle birlikte kurar. Yükleme arka planda birkaç dakika sürer. */Nu* seçeneğini belirtmezseniz, güncelleştirmeleri denetlemek için yüklemenin sonunda **Windows Update** penceresi açılır.

Aracı yüklü programlar listesinde görüntülenir. Yüklü programların listesini görmek için **Denetim masası** > **Programlar** > **Programlar ve Özellikler**' e gidin.

![Aracı yüklendi](./media/backup-dpm-automation/installed-agent-listing.png)

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>DPM 'yi bir kurtarma hizmetleri kasasına kaydetme

Kurtarma Hizmetleri kasasını oluşturduktan sonra, en son aracıyı ve kasa kimlik bilgilerini indirin ve C:\Downloads. gibi uygun bir konumda saklayın.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

DPM sunucusunda, makineyi kasaya kaydetmek için [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet 'ini çalıştırın.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>İlk yapılandırma ayarları

DPM sunucusu, kurtarma hizmetleri kasasıyla kaydedildikten sonra varsayılan abonelik ayarlarıyla başlar. Bu abonelik ayarları ağ, şifreleme ve hazırlama alanını içerir. Abonelik ayarlarını değiştirmek için, önce [Get-Dpmcses Subscriptionsetting](https://technet.microsoft.com/library/jj612793) cmdlet 'ini kullanarak var olan (varsayılan) ayarlar üzerinde bir tanıtıcı almanız gerekir:

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Tüm değişiklikler bu yerel PowerShell nesnesi ```$setting``` yapılır ve ardından tam nesne DPM 'ye kaydedilir ve [set-Dpmcses Subscriptionsetting](https://technet.microsoft.com/library/jj612791) cmdlet 'ini kullanarak bunları kaydetmek için Azure Backup. Değişikliklerin kalıcı olduğundan emin olmak için ```–Commit``` bayrağını kullanmanız gerekir. Bu ayarlar, uygulanmamışsa Azure Backup uygulanmaz ve kullanılmaz.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Ağ

DPM makinesinin internet üzerindeki Azure Backup hizmetine bağlantısı bir ara sunucu üzerinden olduğunda, başarılı yedeklemeler için proxy sunucusu ayarları sağlanmalıdır. Bu işlem, ```-ProxyServer```ve ```-ProxyPort```, ```-ProxyUsername``` ve ```ProxyPassword``` parametreleri [set-Dpmcses Subscriptionsetting](https://technet.microsoft.com/library/jj612791) cmdlet 'i kullanılarak yapılır. Bu örnekte, ara sunucu ile ilgili tüm bilgileri açık bir şekilde temizliyoruz.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Bant genişliği kullanımı aynı zamanda, belirli bir gün kümesi için ```-WorkHourBandwidth``` ve ```-NonWorkHourBandwidth``` seçenekleriyle denetlenebilir. Bu örnekte, hiçbir daraltma ayarlamayız.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Hazırlama alanını yapılandırma

DPM sunucusunda çalışan Azure Backup Aracısı, buluttan geri yüklenen veriler için geçici depolamaya ihtiyaç duyuyor (yerel hazırlama alanı). [Set-Dpmcses Subscriptionsetting](https://technet.microsoft.com/library/jj612791) cmdlet 'ini ve ```-StagingAreaPath``` parametresini kullanarak hazırlama alanını yapılandırın.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

Yukarıdaki örnekte, hazırlama alanı ```$setting```PowerShell nesnesinde *C:\stagingarea* olarak ayarlanır. Belirtilen klasörün zaten var olduğundan emin olun, aksi takdirde abonelik ayarlarının son kaydedilmesi başarısız olur.

### <a name="encryption-settings"></a>Şifreleme ayarları

Azure Backup gönderilen yedekleme verileri verilerin gizliliğini korumak için şifrelenir. Şifreleme parolası, geri yükleme sırasında verilerin şifresini çözmek için "paroladır". Bu bilgilerin ayarlandığı bir kez güvenli ve güvenli tutulması önemlidir.

Aşağıdaki örnekte, ilk komut dizeyi ```passphrase123456789``` güvenli bir dizeye dönüştürür ve güvenli dizeyi ```$Passphrase```adlı değişkene atar. İkinci komut, ```$Passphrase``` güvenli dizeyi, yedeklemeleri şifrelemek için parola olarak ayarlar.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Parola bilgilerini ayarladıktan sonra güvende tutun ve güvende tutun. Bu parola olmadan Azure 'dan verileri geri yükleyemezsiniz.
>
>

Bu noktada, ```$setting``` nesnesi için gerekli tüm değişiklikleri yapmış olmanız gerekir. Değişiklikleri kaydetmeyi unutmayın.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Azure Backup verileri koruma

Bu bölümde, DPM 'ye bir üretim sunucusu ekleyecek ve ardından verileri yerel DPM depolamasına ve sonra Azure Backup olacak şekilde koruyacaksınız. Örneklerde, dosya ve klasörleri nasıl yedekleyeceğiniz gösterilmektedir. Mantık, DPM tarafından desteklenen herhangi bir veri kaynağını yedeklemeye kolayca genişletilebilir. Tüm DPM yedeklemeleriniz, dört bölümden oluşan bir koruma grubu (PG) tarafından yönetilir:

1. **Grup üyeleri** , aynı koruma grubunda korumak istediğiniz tüm korunabilir nesneler (DPM 'de *veri kaynakları* olarak da bilinir) listesidir. Örneğin, bir koruma grubundaki üretim sanal makinelerini ve farklı yedekleme gereksinimlerine sahip olabilecek başka bir koruma grubundaki veritabanlarını SQL Server korumak isteyebilirsiniz. Bir üretim sunucusundaki herhangi bir veri kaynağını yedekleyebilmeniz için önce DPM aracısının sunucuda yüklü olduğundan ve DPM tarafından yönetildiğinden emin olmanız gerekir. [DPM aracısını yükleme](https://technet.microsoft.com/library/bb870935.aspx) ve uygun DPM sunucusuna bağlama adımlarını izleyin.
2. **Veri koruma yöntemi** , hedef yedekleme konumlarını belirtir-bant, disk ve bulut. Bizim örneğimizde, verileri yerel diske ve buluta koruyacağız.
3. Yedeklemelerin ne zaman alınması gerektiğini ve DPM sunucusu ile üretim sunucusu arasında verilerin ne sıklıkta eşitlenmesi gerektiğini belirten bir **yedekleme zamanlaması** .
4. Azure 'da kurtarma noktalarının ne kadar süreyle saklanacağını belirten bir **bekletme zamanlaması** .

### <a name="creating-a-protection-group"></a>Koruma grubu oluşturma

[Yeni-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) cmdlet 'ini kullanarak yeni bir koruma grubu oluşturarak başlayın.

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Yukarıdaki cmdlet *ProtectGroup01*adlı bir koruma grubu oluşturacaktır. Mevcut bir koruma grubu daha sonra Azure bulutuna yedek eklemek için de değiştirilebilir. Ancak, koruma grubunda herhangi bir değişiklik yapmak için-yeni veya mevcut- [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) cmdlet 'ini kullanarak *değiştirilebilir* bir nesne üzerinde bir tanıtıcı edinmemiz gerekir.

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Grup üyelerini koruma grubuna ekleme

Her DPM Aracısı, üzerinde yüklü olduğu sunucudaki veri kaynaklarının listesini bilir. Koruma grubuna bir veri kaynağı eklemek için DPM aracısının öncelikle DPM sunucusuna veri kaynaklarının bir listesini geri gönderilmesi gerekir. Daha sonra bir veya daha fazla veri kaynağı seçilir ve koruma grubuna eklenir. Bunu elde etmek için gereken PowerShell adımları şunlardır:

1. DPM tarafından DPM Aracısı aracılığıyla yönetilen tüm sunucuların bir listesini getirir.
2. Belirli bir sunucu seçin.
3. Sunucudaki tüm veri kaynaklarının listesini getirir.
4. Bir veya daha fazla veri kaynağı seçin ve koruma grubuna ekleyin

DPM aracısının yüklü olduğu ve DPM sunucusu tarafından yönetilmekte olduğu sunucuların listesi [Get-Dpmbir Server](https://technet.microsoft.com/library/hh881600) cmdlet 'i ile elde edilir. Bu örnekte, yalnızca yedekleme için *ProductionServer01* adlı PS 'yi filtreleyecek ve yapılandıracağız.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Şimdi [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) cmdlet 'ini kullanarak ```$server``` veri kaynakları listesini getirin. Bu örnekte, yedekleme için yapılandırmak istediğimiz *D:\\* birimi için filtreliyoruz. Bu veri kaynağı daha sonra [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) cmdlet 'ı kullanılarak koruma grubuna eklenir. Eklemeleri yapmak için *değiştirilebilir* koruma grubu nesnesini ```$MPG``` kullanmayı unutmayın.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Seçilen tüm veri kaynaklarını koruma grubuna ekleyinceye kadar bu adımı gereken sayıda tekrarlayın. Aynı zamanda yalnızca bir veri kaynağı ile başlayabilir ve koruma grubunu oluşturmak için iş akışını tamamlayabilirsiniz ve daha sonraki bir noktada koruma grubuna daha fazla veri kaynağı ekleyebilirsiniz.

### <a name="selecting-the-data-protection-method"></a>Veri koruma yöntemini seçme

Veri kaynakları koruma grubuna eklendikten sonra, bir sonraki adım, [set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) cmdlet 'ini kullanarak koruma yöntemini belirtmektir. Bu örnekte, koruma grubu yerel disk ve bulut yedeklemesi için ayarlanır. Ayrıca,-online bayrağıyla [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) cmdlet 'ini kullanarak buluta korumak istediğiniz veri kaynağını belirtmeniz gerekir.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Bekletme aralığını ayarlama

[Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) cmdlet 'ini kullanarak yedekleme noktaları için saklama süresini ayarlayın. Yedekleme zamanlaması tanımlanmadan önce bekletme ayarlamak tek görünebilir, ancak ```Set-DPMPolicyObjective``` cmdlet 'i kullanmak, daha sonra değiştirilebilecek varsayılan bir yedekleme zamanlaması otomatik olarak ayarlar. İlk olarak yedekleme zamanlamasını ve sonrasında bekletme ilkesini ayarlamak her zaman mümkündür.

Aşağıdaki örnekte cmdlet, Disk yedeklemeleri için bekletme parametrelerini ayarlar. Bu, yedeklemeleri 10 gün boyunca korur ve üretim sunucusu ile DPM sunucusu arasında her 6 saatte bir eşitleme verilerini eşitler. ```SynchronizationFrequencyMinutes```, bir yedekleme noktasının ne sıklıkta oluşturulduğunu, ancak verilerin DPM sunucusuna ne sıklıkta kopyalandığını tanımlamaz.  Bu ayar yedeklerin çok büyük hale gelmesini engeller.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Azure 'a giden yedeklemeler (DPM, çevrimiçi yedeklemeler olarak bunlara başvurur) için, bekletme aralıkları, [bir tam baba-son düzen (GFS) kullanılarak uzun süreli saklama](backup-azure-backup-cloud-as-tape.md)için yapılandırılabilir. Diğer bir deyişle, günlük, haftalık, aylık ve yıllık bekletme ilkeleri içeren bir birleştirilmiş bekletme ilkesi tanımlayabilirsiniz. Bu örnekte, istediğimiz karmaşık bekletme şemasını temsil eden bir dizi oluşturacağız ve sonra [set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) cmdlet 'ini kullanarak bekletme aralığını yapılandıracağız.

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Yedekleme zamanlamasını ayarlama

```Set-DPMPolicyObjective``` cmdlet 'ini kullanarak koruma hedefini belirtirseniz DPM varsayılan bir yedekleme zamanlaması otomatik olarak ayarlar. Varsayılan zamanlamaları değiştirmek için, [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) cmdlet 'ini ve ardından [set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) cmdlet 'ini kullanın.

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

Yukarıdaki örnekte ```$onlineSch```, GFS şemasında koruma grubu için var olan çevrimiçi koruma zamanlamasını içeren dört öğe içeren bir dizidir:

1. ```$onlineSch[0]``` günlük zamanlamayı içerir
2. ```$onlineSch[1]``` haftalık zamanlamayı içerir
3. ```$onlineSch[2]``` aylık zamanlamayı içerir
4. ```$onlineSch[3]``` yıllık zamanlamayı içerir

Bu nedenle, haftalık zamanlamayı değiştirmeniz gerekiyorsa ```$onlineSch[1]```başvurmanız gerekir.

### <a name="initial-backup"></a>İlk yedekleme

Bir veri kaynağı ilk kez yedeklenirken DPM, DPM çoğaltma biriminde korunacak şekilde veri kaynağının tam bir kopyasını oluşturan ilk çoğaltma oluşturması gerekir. Bu etkinlik belirli bir zaman için zamanlanabilir veya ```-NOW```parametresiyle [set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) cmdlet 'i kullanılarak el ile tetiklenebilir.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>DPM çoğaltma & kurtarma noktası biriminin boyutunu değiştirme

Aşağıdaki örnekte olduğu gibi [set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) cmdlet 'INI kullanarak DPM çoğaltma birimi ve gölge kopya biriminin boyutunu da değiştirebilirsiniz: Get-DatasourceDiskAllocation-DataSource $DS set-DatasourceDiskAllocation-DataSource $DS-protectiongroup $MPG-Manual-replicaarea (2GB)-ShadowCopyArea (2 GB)

### <a name="committing-the-changes-to-the-protection-group"></a>Değişiklikler koruma grubuna uygulanıyor

Son olarak, yeni koruma grubu yapılandırması uyarınca DPM 'nin yedeklemeyi yapmadan önce değişikliklerin uygulanması gerekir. Bu, [set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) cmdlet 'i kullanılarak elde edilebilir.

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Yedekleme noktalarını görüntüleme

Bir veri kaynağı için tüm kurtarma noktalarının listesini almak için [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) cmdlet 'ini kullanabilirsiniz. Bu örnekte şunları göndereceğiz:

* DPM sunucusundaki tüm PTE 'leri getirin ve bir dizide depolanan ```$PG```
* ```$PG[0]``` karşılık gelen veri kaynaklarını alın
* bir veri kaynağı için tüm kurtarma noktalarını alın.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Azure 'da korunan verileri geri yükleme

Verilerin geri yüklenmesi ```RecoverableItem``` nesnesinin ve ```RecoveryOption``` nesnesinin bir birleşimidir. Önceki bölümde, bir veri kaynağı için yedekleme noktalarının bir listesini aldık.

Aşağıdaki örnekte, kurtarma hedefi ile yedekleme noktalarını birleştirerek bir Hyper-V sanal makinesini Azure Backup 'dan nasıl geri yükleyeceğiniz gösterilmektedir. Bu örnek şunları içerir:

* [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) cmdlet 'ini kullanarak bir kurtarma seçeneği oluşturuluyor.
* ```Get-DPMRecoveryPoint``` cmdlet 'ini kullanarak yedekleme noktaları dizisi getiriliyor.
* Geri yüklenecek bir yedekleme noktası seçme.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Komutlar, tüm veri kaynağı türleri için kolayca genişletilebilir.

## <a name="next-steps"></a>Sonraki adımlar

* DPM hakkında daha fazla bilgi için Azure Backup bkz. [DPM yedeklemeye giriş](backup-azure-dpm-introduction.md)
