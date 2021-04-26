---
title: PowerShell kullanarak DPM iş yüklerini yedekleme
description: PowerShell kullanarak Data Protection Manager (DPM) için Azure Backup dağıtmayı ve yönetmeyi öğrenin
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 176cbffe5152462055c4ffdb2367cf9c0ab97c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90968306"
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

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

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

4. Kullanılacak depolama yedekliliği türünü belirtin. [Yerel olarak yedekli depolama (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), coğrafi olarak [yedekli depolama (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)veya [bölge yedekli depolama (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage)kullanabilirsiniz. Aşağıdaki örnek, *Testkasasının* **geoyedekli** olarak ayarlandığı **BackupStorageRedundancy** seçeneğini gösterir.

   > [!TIP]
   > Çoğu Azure Backup cmdlet’i, girdi olarak Kurtarma Hizmetleri kasasını gerektirir. Bu nedenle, yedekleme kurtarma hizmetleri Kasası nesnesinin bir değişkende depolanması uygundur.
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

Azure Backup aracısını yüklemeden önce, yükleyicinin Windows Server üzerinde indirilip mevcut olması gerekir. Yükleyicinin en son sürümünü [Microsoft Indirme merkezi](https://aka.ms/azurebackup_agent) ' nden veya kurtarma hizmetleri kasasının Pano sayfasından edinebilirsiniz. Yükleyiciyi, gibi kolay erişilebilen bir konuma kaydedin `C:\Downloads\*` .

Aracıyı yüklemek için **DPM sunucusundaki** yükseltilmiş bir PowerShell konsolunda aşağıdaki komutu çalıştırın:

```powershell
MARSAgentInstaller.exe /q
```

Bu, aracıyı tüm varsayılan seçeneklerle birlikte kurar. Yükleme arka planda birkaç dakika sürer. */Nu* seçeneğini belirtmezseniz, güncelleştirmeleri denetlemek için yüklemenin sonunda **Windows Update** penceresi açılır.

Aracı yüklü programlar listesinde görüntülenir. Yüklü programların listesini görmek için **Denetim Masası**  >  **Programlar**  >  **Programlar ve Özellikler**' e gidin.

![Aracı yüklendi](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Yükleme seçenekleri

Komut satırı aracılığıyla kullanılabilen tüm seçenekleri görmek için aşağıdaki komutu kullanın:

```powershell
MARSAgentInstaller.exe /?
```

Mevcut seçenekler şunlardır:

| Seçenek | Ayrıntılar | Varsayılan |
| --- | --- | --- |
| /q |Sessiz yükleme |- |
| /p: "konum" |Azure Backup aracısının yükleme klasörünün yolu. |C:\Program Files\Microsoft Azure kurtarma hizmetleri Aracısı |
| /s: "konum" |Azure Backup aracısına ait önbellek klasörünün yolu. |C:\Program Files\Microsoft Azure kurtarma hizmetleri, çalışma |
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

DPM sunucusunda, makineyi kasaya kaydetmek için [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) cmdlet 'ini çalıştırın.

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

DPM sunucusu, kurtarma hizmetleri kasasıyla kaydedildikten sonra varsayılan abonelik ayarlarıyla başlar. Bu abonelik ayarları ağ, şifreleme ve hazırlama alanını içerir. Abonelik ayarlarını değiştirmek için, önce [Get-Dpmcses Subscriptionsetting](/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting) cmdlet 'ini kullanarak var olan (varsayılan) ayarlar üzerinde bir tanıtıcı almanız gerekir:

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Tüm değişiklikler bu yerel PowerShell nesnesine yapılır ```$setting```  ve ardından tam nesne DPM 'ye kaydedilir ve [set-Dpmcses subscriptionsetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) cmdlet 'ini kullanarak bunları kaydetmek için Azure Backup. ```–Commit```Değişikliklerin kalıcı olduğundan emin olmak için bayrağını kullanmanız gerekir. Ayarlar uygulanmayacaksa Azure Backup uygulanmaz ve kullanılmaz.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Ağ

DPM makinesinin internet üzerindeki Azure Backup hizmetine bağlantısı bir ara sunucu üzerinden olduğunda, başarılı yedeklemeler için proxy sunucusu ayarları sağlanmalıdır. Bu, ```-ProxyServer``` ve ```-ProxyPort``` ```-ProxyUsername``` ```ProxyPassword``` parametreleri ve [set-dpmcses subscriptionsetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) cmdlet 'i ile parametreler kullanılarak yapılır. Bu örnekte, ara sunucu ile ilgili tüm bilgileri açık bir şekilde temizliyoruz.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Bant genişliği kullanımı, ```-WorkHourBandwidth``` ```-NonWorkHourBandwidth``` belirli bir gün kümesi için ve seçenekleri ile de denetlenebilir. Bu örnekte, hiçbir daraltma ayarlamayız.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Hazırlama alanını yapılandırma

DPM sunucusunda çalışan Azure Backup Aracısı, buluttan geri yüklenen veriler için geçici depolamaya ihtiyaç duyuyor (yerel hazırlama alanı). [Set-Dpmcses Subscriptionsetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) cmdlet 'ini ve parametresini kullanarak hazırlama alanını yapılandırın ```-StagingAreaPath``` .

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

Yukarıdaki örnekte, hazırlama alanı PowerShell nesnesinde *C:\stagingarea* olarak ayarlanır ```$setting``` . Belirtilen klasörün zaten var olduğundan emin olun, aksi takdirde abonelik ayarlarının son kaydedilmesi başarısız olur.

### <a name="encryption-settings"></a>Şifreleme ayarları

Azure Backup gönderilen yedekleme verileri verilerin gizliliğini korumak için şifrelenir. Şifreleme parolası, geri yükleme sırasında verilerin şifresini çözmek için "paroladır". Bu bilgilerin ayarlandığı bir kez güvenli ve güvenli tutulması önemlidir.

Aşağıdaki örnekte, ilk komut dizeyi ```passphrase123456789``` güvenli bir dizeye dönüştürür ve güvenli dizeyi adlı değişkene atar ```$Passphrase``` . İkinci komut, içindeki güvenli dizeyi, ```$Passphrase``` yedeklemeleri şifrelemek için parola olarak ayarlar.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Parola bilgilerini ayarladıktan sonra güvende tutun ve güvende tutun. Bu parola olmadan Azure 'dan verileri geri yükleyemezsiniz.
>
>

Bu noktada, nesnede gerekli tüm değişiklikleri yapmış olmanız gerekir ```$setting``` . Değişiklikleri kaydetmeyi unutmayın.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Azure Backup verileri koruma

Bu bölümde, DPM 'ye bir üretim sunucusu ekleyecek ve ardından verileri yerel DPM depolamasına ve sonra Azure Backup olacak şekilde koruyacaksınız. Örneklerde, dosya ve klasörleri nasıl yedekleyeceğiniz gösterilmektedir. Mantık, DPM tarafından desteklenen herhangi bir veri kaynağını yedeklemeye kolayca genişletilebilir. Tüm DPM yedeklemeleriniz, dört bölümden oluşan bir koruma grubu (PG) tarafından yönetilir:

1. **Grup üyeleri** , aynı koruma grubunda korumak istediğiniz tüm korunabilir nesneler (DPM 'de *veri kaynakları* olarak da bilinir) listesidir. Örneğin, bir koruma grubundaki üretim sanal makinelerini ve farklı yedekleme gereksinimlerine sahip olabilecek başka bir koruma grubundaki veritabanlarını SQL Server korumak isteyebilirsiniz. Bir üretim sunucusundaki herhangi bir veri kaynağını yedekleyebilmeniz için önce DPM aracısının sunucuda yüklü olduğundan ve DPM tarafından yönetildiğinden emin olmanız gerekir. [DPM aracısını yükleme](/system-center/dpm/deploy-dpm-protection-agent) ve uygun DPM sunucusuna bağlama adımlarını izleyin.
2. **Veri koruma yöntemi** , hedef yedekleme konumlarını belirtir-bant, disk ve bulut. Bizim örneğimizde, verileri yerel diske ve buluta koruyacağız.
3. Yedeklemelerin ne zaman alınması gerektiğini ve DPM sunucusu ile üretim sunucusu arasında verilerin ne sıklıkta eşitlenmesi gerektiğini belirten bir **yedekleme zamanlaması** .
4. Azure 'da kurtarma noktalarının ne kadar süreyle saklanacağını belirten bir **bekletme zamanlaması** .

### <a name="creating-a-protection-group"></a>Koruma grubu oluşturma

[Yeni-DPMProtectionGroup](/powershell/module/dataprotectionmanager/new-dpmprotectiongroup) cmdlet 'ini kullanarak yeni bir koruma grubu oluşturarak başlayın.

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Yukarıdaki cmdlet *ProtectGroup01* adlı bir koruma grubu oluşturacaktır. Mevcut bir koruma grubu daha sonra Azure bulutuna yedek eklemek için de değiştirilebilir. Ancak, koruma grubunda herhangi bir değişiklik yapmak için-yeni veya mevcut- [Get-DPMModifiableProtectionGroup](/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup) cmdlet 'ini kullanarak *değiştirilebilir* bir nesne üzerinde bir tanıtıcı edinmemiz gerekir.

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Grup üyelerini koruma grubuna ekleme

Her DPM Aracısı, üzerinde yüklü olduğu sunucudaki veri kaynaklarının listesini bilir. Koruma grubuna bir veri kaynağı eklemek için DPM aracısının öncelikle DPM sunucusuna veri kaynaklarının bir listesini geri gönderilmesi gerekir. Daha sonra bir veya daha fazla veri kaynağı seçilir ve koruma grubuna eklenir. Bunu elde etmek için gereken PowerShell adımları şunlardır:

1. DPM tarafından DPM Aracısı aracılığıyla yönetilen tüm sunucuların bir listesini getirir.
2. Belirli bir sunucu seçin.
3. Sunucudaki tüm veri kaynaklarının listesini getirir.
4. Bir veya daha fazla veri kaynağı seçin ve koruma grubuna ekleyin

DPM aracısının yüklü olduğu ve DPM sunucusu tarafından yönetilmekte olduğu sunucuların listesi [Get-Dpmbir Server](/powershell/module/dataprotectionmanager/get-dpmproductionserver) cmdlet 'i ile elde edilir. Bu örnekte, yalnızca yedekleme için *ProductionServer01* adıyla PowerShell 'i filtreleyecek ve yapılandıracağız.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Şimdi ```$server``` [Get-dpmdatasource](/powershell/module/dataprotectionmanager/get-dpmdatasource) cmdlet 'ini kullanarak veri kaynakları listesini getirin. Bu örnekte, `D:\` yedekleme için yapılandırmak istediğimiz birimle ilgili filtreliyoruz. Bu veri kaynağı daha sonra [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) cmdlet 'ı kullanılarak koruma grubuna eklenir. Eklemeleri yapmak için *değiştirilebilir* koruma grubu nesnesini kullanmayı unutmayın ```$MPG``` .

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Seçilen tüm veri kaynaklarını koruma grubuna ekleyinceye kadar bu adımı gereken sayıda tekrarlayın. Aynı zamanda yalnızca bir veri kaynağı ile başlayabilir ve koruma grubunu oluşturmak için iş akışını tamamlayabilirsiniz ve daha sonraki bir noktada koruma grubuna daha fazla veri kaynağı ekleyebilirsiniz.

### <a name="selecting-the-data-protection-method"></a>Veri koruma yöntemini seçme

Veri kaynakları koruma grubuna eklendikten sonra, bir sonraki adım, [set-DPMProtectionType](/powershell/module/dataprotectionmanager/set-dpmprotectiontype) cmdlet 'ini kullanarak koruma yöntemini belirtmektir. Bu örnekte, koruma grubu yerel disk ve bulut yedeklemesi için ayarlanır. Ayrıca,-online bayrağıyla [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) cmdlet 'ini kullanarak buluta korumak istediğiniz veri kaynağını belirtmeniz gerekir.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Bekletme aralığını ayarlama

[Set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) cmdlet 'ini kullanarak yedekleme noktaları için saklama süresini ayarlayın. Yedekleme zamanlaması tanımlanmadan önce bekletme ayarlamak tek görünebilir, ancak cmdlet 'i kullanmak, ```Set-DPMPolicyObjective``` daha sonra değiştirilebilecek varsayılan bir yedekleme zamanlaması otomatik olarak ayarlar. Önce yedekleme zamanlamasını ve bekletme ilkesini ayarlamak her zaman mümkündür.

Aşağıdaki örnekte cmdlet, Disk yedeklemeleri için bekletme parametrelerini ayarlar. Bu, yedeklemeleri 10 gün boyunca korur ve üretim sunucusu ile DPM sunucusu arasında her 6 saatte bir eşitleme verilerini eşitler. , ```SynchronizationFrequencyMinutes``` Bir yedekleme noktasının ne sıklıkta oluşturulduğunu, ancak VERILERIN DPM sunucusuna ne sıklıkta kopyalandığını tanımlamaz.  Bu ayar yedeklerin çok büyük hale gelmesini engeller.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Azure 'a giden yedeklemeler (DPM, çevrimiçi yedeklemeler olarak bunlara başvurur) için, bekletme aralıkları, [bir tam baba-son düzen (GFS) kullanılarak uzun süreli saklama](backup-azure-backup-cloud-as-tape.md)için yapılandırılabilir. Diğer bir deyişle, günlük, haftalık, aylık ve yıllık bekletme ilkeleri içeren bir birleştirilmiş bekletme ilkesi tanımlayabilirsiniz. Bu örnekte, istediğimiz karmaşık bekletme şemasını temsil eden bir dizi oluşturacağız ve sonra [set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) cmdlet 'ini kullanarak bekletme aralığını yapılandıracağız.

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Yedekleme zamanlamasını ayarlama

Cmdlet 'ini kullanarak koruma hedefini belirtirseniz DPM varsayılan bir yedekleme zamanlaması otomatik olarak ayarlar ```Set-DPMPolicyObjective``` . Varsayılan zamanlamaları değiştirmek için, [Get-DPMPolicySchedule](/powershell/module/dataprotectionmanager/get-dpmpolicyschedule) cmdlet 'ini ve ardından [set-DPMPolicySchedule](/powershell/module/dataprotectionmanager/set-dpmpolicyschedule) cmdlet 'ini kullanın.

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

Yukarıdaki örnekte, ```$onlineSch``` GFS şemasında koruma grubu için mevcut çevrimiçi koruma zamanlamasını içeren bir dizidir:

1. ```$onlineSch[0]``` günlük zamanlamayı içerir
2. ```$onlineSch[1]``` Haftalık zamanlamayı içerir
3. ```$onlineSch[2]``` aylık zamanlamayı içerir
4. ```$onlineSch[3]``` yıllık zamanlamayı içerir

Bu nedenle, haftalık zamanlamayı değiştirmeniz gerekiyorsa, ' a başvurmanız gerekir ```$onlineSch[1]``` .

### <a name="initial-backup"></a>İlk yedekleme

Bir veri kaynağı ilk kez yedeklenirken DPM, DPM çoğaltma biriminde korunacak şekilde veri kaynağının tam bir kopyasını oluşturan ilk çoğaltma oluşturması gerekir. Bu etkinlik belirli bir süre için zamanlanabilir veya parametresi ile [set-DPMReplicaCreationMethod](/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod) cmdlet 'i kullanılarak el ile tetiklenebilir ```-NOW``` .

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>DPM çoğaltma & kurtarma noktası biriminin boyutunu değiştirme

Aşağıdaki örnekte olduğu gibi [set-DPMDatasourceDiskAllocation](/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation) cmdlet 'INI kullanarak DPM çoğaltma birimi ve gölge kopya biriminin boyutunu da değiştirebilirsiniz: Get-DatasourceDiskAllocation-datasource $DS Set-DatasourceDiskAllocation-DataSource $DS-protectiongroup $MPG-Manual-replicaarea (2GB)-ShadowCopyArea (2 GB)

### <a name="committing-the-changes-to-the-protection-group"></a>Değişiklikler koruma grubuna uygulanıyor

Son olarak, yeni koruma grubu yapılandırması uyarınca DPM 'nin yedeklemeyi yapmadan önce değişikliklerin uygulanması gerekir. Bu, [set-DPMProtectionGroup](/powershell/module/dataprotectionmanager/set-dpmprotectiongroup) cmdlet 'i kullanılarak elde edilebilir.

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Yedekleme noktalarını görüntüleme

Bir veri kaynağı için tüm kurtarma noktalarının listesini almak için [Get-DPMRecoveryPoint](/powershell/module/dataprotectionmanager/get-dpmrecoverypoint) cmdlet 'ini kullanabilirsiniz. Bu örnekte şunları göndereceğiz:

* DPM sunucusundaki tüm PTE 'leri getirin ve bir dizide depolanan ```$PG```
* uygulamasına karşılık gelen veri kaynaklarını alın ```$PG[0]```
* bir veri kaynağı için tüm kurtarma noktalarını alın.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Azure 'da korunan verileri geri yükleme

Verilerin geri yüklenmesi, ```RecoverableItem``` nesne ve nesnenin bir birleşimidir ```RecoveryOption``` . Önceki bölümde, bir veri kaynağı için yedekleme noktalarının bir listesini aldık.

Aşağıdaki örnekte, kurtarma hedefi ile yedekleme noktalarını birleştirerek bir Hyper-V sanal makinesini Azure Backup 'dan nasıl geri yükleyeceğiniz gösterilmektedir. Bu örnek şunları içerir:

* [New-DPMRecoveryOption](/powershell/module/dataprotectionmanager/new-dpmrecoveryoption) cmdlet 'ini kullanarak bir kurtarma seçeneği oluşturuluyor.
* Cmdlet 'ini kullanarak yedekleme noktaları dizisi getiriliyor ```Get-DPMRecoveryPoint``` .
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
