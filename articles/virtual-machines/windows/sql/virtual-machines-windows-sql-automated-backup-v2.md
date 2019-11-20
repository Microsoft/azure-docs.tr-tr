---
title: SQL Server 2016/2017 Azure VM 'Leri için otomatik yedekleme v2 | Microsoft Docs
description: Azure 'da çalışan SQL Server 2016/2017 VM 'lerinin otomatik yedekleme özelliğini açıklar. Bu makale, Kaynak Yöneticisi kullanan VM 'lere özeldir.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 452dfcc04d9fc9048493222ad2a82a5bcc8b78f4
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162872"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Azure sanal makineleri için otomatik yedekleme v2 (Kaynak Yöneticisi)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Otomatik yedekleme v2, SQL Server 2016/2017 Standard, Enterprise veya Developer Edition çalıştıran bir Azure VM üzerindeki tüm mevcut ve yeni veritabanları için [Microsoft Azure üzere yönetilen yedeklemeyi](https://msdn.microsoft.com/library/dn449496.aspx) otomatik olarak yapılandırır. Bu, dayanıklı Azure Blob depolama kullanan düzenli veritabanı yedeklerini yapılandırmanızı sağlar. Otomatik yedekleme v2, [SQL Server IaaS Aracısı uzantısına](virtual-machines-windows-sql-server-agent-extension.md)bağlıdır.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Önkoşullar
Otomatik yedekleme v2 'yi kullanmak için aşağıdaki önkoşulları gözden geçirin:

**Işletim sistemi**:

- Windows Server 2012 R2
- Windows Server 2016

**SQL Server sürüm/sürüm**:

- SQL Server 2016: geliştirici, standart veya kurumsal
- SQL Server 2017: geliştirici, standart veya kurumsal

> [!IMPORTANT]
> Otomatik yedekleme v2, SQL Server 2016 veya üzeri sürümlerle birlikte kullanılabilir. SQL Server 2014 kullanıyorsanız, veritabanlarınızı yedeklemek için otomatik yedekleme v1 kullanabilirsiniz. Daha fazla bilgi için bkz. [SQL Server 2014 Azure sanal makineleri Için otomatik yedekleme](virtual-machines-windows-sql-automated-backup.md).

**Veritabanı yapılandırması**:

- Hedef veritabanlarının tam kurtarma modelini kullanması gerekir. Yedeklemelerdeki tam kurtarma modelinin etkileri hakkında daha fazla bilgi için, bkz. [yedekleme tam kurtarma modeli altında](https://technet.microsoft.com/library/ms190217.aspx).
- Sistem veritabanlarının tam kurtarma modelini kullanması gerekmez. Ancak, model veya MSDB için günlük yedeklemelerinin alınması gerekiyorsa tam kurtarma modeli kullanmanız gerekir.
- Hedef veritabanları, varsayılan SQL Server örneği ya da [doğru şekilde yüklenmiş](virtual-machines-windows-sql-server-iaas-faq.md#administration) bir adlandırılmış örnek üzerinde olmalıdır. 

> [!NOTE]
> Otomatik yedekleme **SQL Server IaaS Aracısı uzantısına**dayanır. Geçerli SQL sanal makine galeri görüntüleri varsayılan olarak bu uzantıyı ekler. Daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Ayarlar
Aşağıdaki tabloda, otomatik yedekleme v2 için yapılandırılabilecek seçenekler açıklanmaktadır. Gerçek yapılandırma adımları Azure portal veya Azure Windows PowerShell komutlarını kullanıp kullanmayacağınızı bağlı olarak değişir.

### <a name="basic-settings"></a>Temel ayarlar

| Ayar | Aralık (varsayılan) | Açıklama |
| --- | --- | --- |
| **Otomatik Yedekleme** | Etkinleştir/devre dışı bırak (devre dışı) | SQL Server 2016/2017 Developer, Standard veya Enterprise çalıştıran bir Azure VM için Otomatik yedeklemeyi etkinleştirilir veya devre dışı bırakır. |
| **Bekletme dönemi** | 1-30 gün (30 gün) | Yedeklemelerin saklanacağı gün sayısı. |
| **Depolama Hesabı** | Azure depolama hesabı | Blob depolamada otomatik yedekleme dosyaları depolamak için kullanılacak bir Azure depolama hesabı. Tüm yedekleme dosyalarını depolamak için bu konumda bir kapsayıcı oluşturulur. Yedekleme dosyası adlandırma kuralı, tarih, saat ve veritabanı GUID 'sini içerir. |
| **Şifreleme** |Etkinleştir/devre dışı bırak (devre dışı) | Şifrelemeyi etkinleştirilir veya devre dışı bırakır. Şifreleme etkinleştirildiğinde, yedeği geri yüklemek için kullanılan sertifikalar belirtilen depolama hesabında bulunur. Aynı adlandırma kuralına sahip aynı **otomatik yedekleme** kapsayıcısını kullanır. Parola değişirse, bu parolayla yeni bir sertifika oluşturulur, ancak eski sertifika önceki yedeklemeleri geri yüklemek için kalır. |
| **Parola** |Parola metni | Şifreleme anahtarları için parola. Bu parola yalnızca Şifreleme etkinse gereklidir. Şifrelenmiş bir yedeklemeyi geri yüklemek için, yedekleme sırasında kullanılan doğru parolaya ve ilgili sertifikaya sahip olmanız gerekir. |

### <a name="advanced-settings"></a>Gelişmiş ayarlar

| Ayar | Aralık (varsayılan) | Açıklama |
| --- | --- | --- |
| **Sistem veritabanı yedeklemeleri** | Etkinleştir/devre dışı bırak (devre dışı) | Bu özellik etkinleştirildiğinde, sistem veritabanlarını da yedekler: Master, MSDB ve model. MSDB ve model veritabanları için, günlük yedeklerinin alınmasını istiyorsanız bunların tam kurtarma modunda olduğunu doğrulayın. Günlük yedeklemeleri hiçbir şekilde ana için alınmaz. Ve TempDB için hiçbir yedekleme yapılmaz. |
| **Yedekleme zamanlaması** | El ile/otomatik (otomatik) | Varsayılan olarak, yedekleme zamanlaması günlük büyümeye göre otomatik olarak belirlenir. El ile yedekleme zamanlaması, kullanıcının yedeklemeler için zaman penceresini belirtmesini sağlar. Bu durumda, yedeklemeler yalnızca belirtilen sıklıkta ve belirli bir günün belirtilen zaman penceresinde gerçekleşirken gerçekleşir. |
| **Tam yedekleme sıklığı** | Günlük/haftalık | Tam yedekleme sıklığı. Her iki durumda da, tam yedeklemeler bir sonraki zamanlanmış zaman penceresinde başlar. Haftalık seçildiğinde, tüm veritabanları başarılı bir şekilde yedeklenene kadar yedeklemeler birden çok güne yayılamaz. |
| **Tam yedekleme başlangıç zamanı** | 00:00 – 23:00 (01:00) | Tam yedeklemelerin gerçekleşmesi için verilen günün başlangıç saati. |
| **Tam yedekleme zamanı penceresi** | 1 – 23 saat (1 saat) | Belirli bir günün zaman penceresinin, tam yedeklemelerin gerçekleşmesi için geçen süre. |
| **Günlük yedekleme sıklığı** | 5 – 60 dakika (60 dakika) | Günlük yedeklemeleri sıklığı. |

## <a name="understanding-full-backup-frequency"></a>Tam yedekleme sıklığını anlama
Günlük ve haftalık tam yedeklemeler arasındaki farkı anlamak önemlidir. Aşağıdaki iki örnek senaryoyu göz önünde bulundurun.

### <a name="scenario-1-weekly-backups"></a>Senaryo 1: haftalık yedeklemeler
Çok sayıda büyük veritabanı içeren bir SQL Server VM vardır.

Pazartesi günü, otomatik yedekleme v2 'yi aşağıdaki ayarlarla etkinleştirirsiniz:

- Yedekleme zamanlaması: **el ile**
- Tam yedekleme sıklığı: **haftalık**
- Tam yedekleme başlangıç zamanı: **01:00**
- Tam yedekleme zamanı penceresi: **1 saat**

Bu, bir sonraki kullanılabilir yedekleme penceresinin 1 saat 1 saat olarak Salı olması anlamına gelir. Bu sırada, otomatik yedekleme, veritabanlarınızı tek seferde yedeklemeye başlar. Bu senaryoda, veritabanlarınız, ilk iki veritabanı için tam yedeklemelerin tamamlandığı kadar büyük. Ancak, bir saatten sonra veritabanlarının hepsi yedeklenmeden sonra.

Bu durumda, otomatik yedekleme sonraki günde kalan veritabanlarını yedeklemeye başlar, 1 saat boyunca bir saat olarak Çarşamba. Bu süre içinde tüm veritabanları yedeklenmemişse, bir sonraki güne aynı anda yeniden dener. Bu, tüm veritabanları başarılı bir şekilde yedeklenene kadar devam eder.

Yeniden Salı 'a ulaştıktan sonra otomatik yedekleme tüm veritabanlarını yeniden yedeklemeye başlar.

Bu senaryo otomatik yedeklemenin yalnızca belirtilen zaman penceresinde çalıştığını ve her bir veritabanının haftada bir kez yedeklenmekte olduğunu gösterir. Bu Ayrıca, yedeklemelerin tek bir günde tüm yedeklemeleri tamamlamamak mümkün olmadığı durumlarda birden çok güne yayılabilmesini sağlar.

### <a name="scenario-2-daily-backups"></a>Senaryo 2: günlük yedeklemeler
Çok sayıda büyük veritabanı içeren bir SQL Server VM vardır.

Pazartesi günü, otomatik yedekleme v2 'yi aşağıdaki ayarlarla etkinleştirirsiniz:

- Yedekleme zamanlaması: El Ile
- Tam yedekleme sıklığı: günlük
- Tam yedekleme başlangıç zamanı: 22:00
- Tam yedekleme zamanı penceresi: 6 saat

Bu, bir sonraki kullanılabilir yedekleme penceresinin Pazartesi günü, 6 saat için 10 PM olarak olduğu anlamına gelir. Bu sırada, otomatik yedekleme, veritabanlarınızı tek seferde yedeklemeye başlar.

Ardından, 6 saat boyunca Salı günü, tüm veritabanlarının tam yedeklemeleri yeniden başlar.

> [!IMPORTANT]
> Günlük yedeklemeler zamanlarken, tüm veritabanlarının bu süre içinde yedeklendiğinden emin olmak için geniş bir zaman penceresi zamanlamanız önerilir. Bu, yedeklenecek büyük miktarda veriniz olduğunda özellikle önemlidir.

## <a name="configure-in-the-portal"></a>Portalda yapılandırma

Sağlama sırasında veya mevcut SQL Server 2016/2017 VM 'Ler için otomatik yedekleme v2 'yi yapılandırmak üzere Azure portal kullanabilirsiniz.

## <a name="configure-for-new-vms"></a>Yeni VM 'Ler için yapılandırma

Kaynak Yöneticisi dağıtım modelinde yeni bir SQL Server 2016 veya 2017 sanal makine oluştururken otomatik yedekleme v2 'yi yapılandırmak için Azure portal kullanın.

**SQL Server ayarları** sekmesinde **otomatik yedekleme**altında **Etkinleştir** ' i seçin. Aşağıdaki Azure portal ekran görüntüsü **SQL otomatik yedekleme** ayarlarını gösterir.

![Azure portal 'de SQL otomatik yedekleme yapılandırması](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Otomatik yedekleme v2 varsayılan olarak devre dışıdır.

## <a name="configure-existing-vms"></a>Mevcut VM 'Leri yapılandırma

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Mevcut SQL Server sanal makineler için [SQL sanal makineler kaynağına](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) gidin ve ardından otomatik yedeklemelerinizi yapılandırmak için **yedeklemeler** ' i seçin.

![Mevcut VM 'Ler için SQL otomatik yedekleme](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


İşiniz bittiğinde, değişiklikleri kaydetmek için **yedeklemeler** ayarları sayfasının alt kısmındaki **Uygula** düğmesine tıklayın.

Otomatik yedeklemeyi ilk kez etkinleştirirseniz Azure SQL Server IaaS aracısını arka planda yapılandırır. Bu süre boyunca, Azure portal otomatik yedeklemenin yapılandırıldığını gösteremeyebilir. Aracının yüklenmesi, yapılandırılması için birkaç dakika bekleyin. Bundan sonra Azure portal yeni ayarları yansıtacaktır.

## <a name="configure-with-powershell"></a>PowerShell ile yapılandırma

Otomatik yedekleme v2 'yi yapılandırmak için PowerShell 'i kullanabilirsiniz. Başlamadan önce şunları yapmanız gerekir:

- [En son Azure PowerShell indirin ve yükleyin](https://aka.ms/webpi-azps).
- Windows PowerShell 'i açın ve **Connect-AzAccount** komutuyla hesabınızla ilişkilendirin.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>SQL IaaS uzantısını yükler
Azure portal bir SQL Server sanal makine sağladıysanız, SQL Server IaaS uzantısının zaten yüklü olması gerekir. **Get-AzVM** komutunu çağırarak ve **Uzantılar** özelliğini inceleyerek, VM 'niz için yüklenip yüklenmediğini belirleyebilirsiniz.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

SQL Server IaaS Aracısı uzantısı yüklüyse, "SqlIaaSAgent" veya "Sqliaasextenma" olarak listelenmiş olduğunu görmeniz gerekir. Uzantının **Provisioningstate** 'i de "başarılı" olarak gösterilmelidir. 

Yüklü değilse veya sağlanmadıysa, aşağıdaki komutla yükleyebilirsiniz. VM adı ve kaynak grubuna ek olarak, sanal makinenizin bulunduğu bölgeyi ( **$Region**) de belirtmeniz gerekir.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a>Geçerli ayarları doğrulama
Sağlama sırasında otomatik yedeklemeyi etkinleştirdiyseniz, geçerli yapılandırmanızı denetlemek için PowerShell kullanabilirsiniz. **Get-AzVMSqlServerExtension** komutunu çalıştırın ve **oto backupsettings** özelliğini inceleyin:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Aşağıdakine benzer bir çıktı almalısınız:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Çıktılarınız **etkin** ' in **false**olarak ayarlandığını gösteriyorsa, Otomatik yedeklemeyi etkinleştirmeniz gerekir. İyi haber, Otomatik yedeklemeyi aynı şekilde etkinleştirmenizi ve yapılandırmanızı sağlar. Bu bilgi için sonraki bölüme bakın.

> [!NOTE] 
> Değişiklik yaptıktan hemen sonra ayarları denetledikten sonra eski yapılandırma değerlerini geri almanız mümkündür. Birkaç dakika bekleyin ve değişikliklerinizin uygulandığından emin olmak için ayarları yeniden denetleyin.

### <a name="configure-automated-backup-v2"></a>Otomatik yedekleme v2 'yi yapılandırma
PowerShell 'i Otomatik yedeklemeyi etkinleştirmek için ve ayrıca yapılandırma ve davranışını dilediğiniz zaman değiştirmek için kullanabilirsiniz. 

İlk olarak, yedekleme dosyaları için bir depolama hesabı seçin veya oluşturun. Aşağıdaki betik bir depolama hesabı seçer veya yoksa oluşturur.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Otomatik yedekleme, Premium depolamada yedeklemelerin depolanmasını desteklemez, ancak Premium depolama kullanan VM disklerinden yedeklemeler alabilir.

Ardından, Azure depolama hesabında yedeklemeleri depolamak için otomatik yedekleme v2 ayarlarını etkinleştirmek ve yapılandırmak üzere **New-Azvmsqlserverotomatikbackupconfig** komutunu kullanın. Bu örnekte, yedeklemeler 10 gün boyunca tutulacak şekilde ayarlanır. Sistem veritabanı yedeklemeleri etkindir. Tam yedeklemeler, iki saat boyunca 20:00 ile başlayan bir zaman penceresiyle haftalık olarak zamanlanır. Günlük yedeklemeleri, her 30 dakikada bir zamanlanır. İkinci komut, **set-AzVMSqlServerExtension**, BELIRTILEN Azure VM 'yi bu ayarlarla güncelleştirir.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

SQL Server IaaS aracısının yüklenmesi ve yapılandırılması birkaç dakika sürebilir. 

Şifrelemeyi etkinleştirmek için, önceki betiği, **Enableencryption** parametresini **CertificatePassword** parametresi için bir parola (güvenli dize) ile birlikte geçecek şekilde değiştirin. Aşağıdaki betik, önceki örnekteki otomatik yedekleme ayarlarını sağlar ve şifreleme ekler.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Ayarlarınızın uygulandığını doğrulamak için [Otomatik Yedekleme yapılandırmasını doğrulayın](#verifysettings).

### <a name="disable-automated-backup"></a>Otomatik yedeklemeyi devre dışı bırak
Otomatik yedeklemeyi devre dışı bırakmak için, **New-Azvmsqlserverotomatikbackupconfig** komutuna **-Enable** parametresi olmadan aynı betiği çalıştırın. **-Enable** parametresinin yokluğu, özelliği devre dışı bırakma komutuna işaret eder. Yükleme sırasında olduğu gibi, Otomatik yedeklemeyi devre dışı bırakmak birkaç dakika sürebilir.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Örnek betik
Aşağıdaki betik, sanal ağınız için Otomatik yedeklemeyi etkinleştirmek ve yapılandırmak üzere özelleştirebileceğiniz bir değişken kümesi sağlar. Bu durumda, gereksinimlerinize göre betiği özelleştirmeniz gerekebilir. Örneğin, sistem veritabanlarının yedeklenmesini devre dışı bırakmak veya şifrelemeyi etkinleştirmek istiyorsanız değişiklikler yapmanız gerekir.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>İzleme

SQL Server 2016/2017 ' de Otomatik yedeklemeyi izlemek için iki ana seçeneğiniz vardır. Otomatik yedekleme SQL Server yönetilen yedekleme özelliğini kullandığından, aynı izleme teknikleri her ikisi için de geçerlidir.

İlk olarak, [msdb. managed_backup. sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)öğesini çağırarak durumu yoklayabilmeniz gerekir. Veya [msdb. managed_backup. fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tablo değerli işlevini sorgulayın.

Diğer bir seçenek de bildirimler için yerleşik Veritabanı Postası özelliğinden yararlanabilmenizi sağlar.

1. **SSMBackup2WANotificationEmailIds** parametresine bir e-posta adresi atamak için [msdb. managed_backup. sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) saklı yordamını çağırın. 
1. Azure VM 'den e-postaları göndermek için [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) 'i etkinleştirin.
1. Veritabanı Postası yapılandırmak için SMTP sunucusunu ve Kullanıcı adını kullanın. SQL Server Management Studio veya Transact-SQL komutlarıyla Veritabanı Postası yapılandırabilirsiniz. Daha fazla bilgi için bkz. [veritabanı postası](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [SQL Server Agent veritabanı postası kullanacak şekilde yapılandırın](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. SMTP bağlantı noktasına hem yerel VM Güvenlik Duvarı hem de VM için ağ güvenlik grubu aracılığıyla izin verildiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar
Otomatik yedekleme v2, Azure VM 'lerde yönetilen yedeklemeyi yapılandırır. Bu nedenle, davranışı ve etkilerini anlamak için [yönetilen yedekleme belgelerinin gözden geçirilmesi](https://msdn.microsoft.com/library/dn449496.aspx) önemlidir.

Azure VM 'lerinde SQL Server için ek yedekleme ve geri yükleme kılavuzunu aşağıdaki makaleye bulabilirsiniz: [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](virtual-machines-windows-sql-backup-recovery.md).

Diğer kullanılabilir otomasyon görevleri hakkında daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Azure VM 'lerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](virtual-machines-windows-sql-server-iaas-overview.md).

