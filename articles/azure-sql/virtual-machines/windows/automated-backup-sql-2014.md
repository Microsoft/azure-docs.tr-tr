---
title: SQL Server 2014 Azure sanal makineleri için otomatik yedekleme
description: Azure 'da çalışan SQL Server 2014 VM 'lerinin otomatik yedekleme özelliğini açıklar. Bu makale, Kaynak Yöneticisi kullanan VM 'lere özeldir.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41add54ce767413982ab0503f7263c58aed4d4e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359311"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>SQL Server 2014 sanal makineleri için otomatik yedekleme (Kaynak Yöneticisi)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

Otomatik yedekleme, SQL Server 2014 Standard veya Enterprise çalıştıran bir Azure VM üzerindeki tüm mevcut ve yeni veritabanları için [Microsoft Azure üzere yönetilen yedeklemeyi](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) otomatik olarak yapılandırır. Bu, dayanıklı Azure Blob depolama kullanan düzenli veritabanı yedeklerini yapılandırmanızı sağlar. Otomatik yedekleme [SQL Server hizmet olarak altyapı (IaaS) aracı uzantısına](sql-server-iaas-agent-extension-automate-management.md)bağlıdır.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Önkoşullar
Otomatik yedeklemeyi kullanmak için aşağıdaki önkoşulları göz önünde bulundurun:


**İşletim sistemi**:

- Windows Server 2012 ve üzeri 

**SQL Server sürüm/sürüm**:

- SQL Server 2014 standart
- SQL Server 2014 Enterprise

> [!NOTE]
> SQL 2016 ve üzeri için bkz. [SQL Server 2016 Için otomatik yedekleme](automated-backup.md).

**Veritabanı yapılandırması**:

- Hedef _Kullanıcı_ veritabanlarının tam kurtarma modelini kullanması gerekir. Sistem veritabanlarının tam kurtarma modelini kullanması gerekmez. Ancak, model veya MSDB için günlük yedeklemelerinin alınması gerekiyorsa, tam kurtarma modelini kullanmanız gerekir. Yedeklemelerdeki tam kurtarma modelinin etkileri hakkında daha fazla bilgi için, bkz. [yedekleme tam kurtarma modeli altında](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- SQL Server VM, SQL IaaS Aracısı uzantısına [tam yönetim modunda](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)kaydedilir. 
-  Otomatik yedekleme, tam [SQL Server IaaS Aracısı uzantısına](sql-server-iaas-agent-extension-automate-management.md)dayanır. Bu nedenle, otomatik yedekleme yalnızca varsayılan örnekten hedef veritabanlarında veya tek bir adlandırılmış örnekten desteklenir. Varsayılan örnek yoksa ve birden çok adlandırılmış örnek varsa, SQL IaaS uzantısı başarısız olur ve otomatik yedekleme çalışmaz. 

## <a name="settings"></a>Ayarlar

Aşağıdaki tabloda otomatik yedekleme için yapılandırılabilecek seçenekler açıklanmaktadır. Gerçek yapılandırma adımları Azure portal veya Azure Windows PowerShell komutlarını kullanıp kullanmayacağınızı bağlı olarak değişir.

| Ayar | Aralık (varsayılan) | Description |
| --- | --- | --- |
| **Otomatik Yedekleme** | Etkinleştir/devre dışı bırak (devre dışı) | SQL Server 2014 Standard veya Enterprise çalıştıran bir Azure VM için Otomatik yedeklemeyi etkinleştirilir veya devre dışı bırakır. |
| **Bekletme dönemi** | 1-30 gün (30 gün) | Bir yedeklemenin saklanacağı gün sayısı. |
| **Depolama hesabı** | Azure depolama hesabı | Blob depolamada otomatik yedekleme dosyaları depolamak için kullanılacak bir Azure depolama hesabı. Tüm yedekleme dosyalarını depolamak için bu konumda bir kapsayıcı oluşturulur. Yedekleme dosyası adlandırma kuralı, tarih, saat ve makine adını içerir. |
| **Şifreleme** | Etkinleştir/devre dışı bırak (devre dışı) | Şifrelemeyi etkinleştirilir veya devre dışı bırakır. Şifreleme etkinleştirildiğinde, yedeği geri yüklemek için kullanılan sertifikalar aynı adlandırma kuralını kullanarak aynı kapsayıcıda belirtilen depolama hesabında bulunur `automaticbackup` . Parola değişirse, bu parolayla yeni bir sertifika oluşturulur, ancak eski sertifika önceki yedeklemeleri geri yüklemek için kalır. |
| **Parola** | Parola metni | Şifreleme anahtarları için parola. Bu yalnızca Şifreleme etkinse gereklidir. Şifrelenmiş bir yedeklemeyi geri yüklemek için, yedekleme sırasında kullanılan doğru parolaya ve ilgili sertifikaya sahip olmanız gerekir. |


## <a name="configure-new-vms"></a>Yeni VM 'Leri yapılandırma

Kaynak Yöneticisi dağıtım modelinde yeni bir SQL Server 2014 sanal makinesi oluşturduğunuzda otomatik yedeklemeyi yapılandırmak için Azure portal kullanın.

**SQL Server ayarları** sekmesinde, **otomatik yedekleme** ' ye kaydırın ve **Etkinleştir**' i seçin. Aşağıdaki Azure portal ekran görüntüsü **SQL otomatik yedekleme** ayarlarını gösterir.

![Azure portal SQL otomatik yedekleme yapılandırması](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Mevcut VM 'Leri yapılandırma

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Mevcut SQL Server VM 'Ler için otomatik yedeklemeleri etkinleştirebilir ve devre dışı bırakabilir, saklama süresini değiştirebilir, depolama hesabını belirtebilir ve Azure portal şifrelemeyi etkinleştirebilirsiniz. 

SQL Server 2014 sanal makinenizin [SQL sanal makineler kaynağına](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) gidin ve **yedeklemeler**' i seçin. 

![Mevcut VM 'Ler için SQL otomatik yedekleme](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

İşiniz bittiğinde, değişiklikleri kaydetmek için **yedeklemeler** sayfasının alt kısmındaki **Uygula** düğmesini seçin.

Otomatik yedeklemeyi ilk kez etkinleştirirseniz Azure SQL Server IaaS aracısını arka planda yapılandırır. Bu süre boyunca, Azure portal otomatik yedeklemenin yapılandırıldığını gösteremeyebilir. Aracının yüklenmesi ve yapılandırılması için birkaç dakika bekleyin. Bundan sonra Azure portal yeni ayarları yansıtacaktır.

> [!NOTE]
> Ayrıca, bir şablon kullanarak Otomatik yedeklemeyi yapılandırabilirsiniz. Daha fazla bilgi için bkz. [otomatik yedekleme Için Azure hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>PowerShell ile yapılandırma

Otomatik yedeklemeyi yapılandırmak için PowerShell kullanabilirsiniz. Başlamadan önce şunları yapmanız gerekir:

- [En son Azure PowerShell indirin ve yükleyin](https://aka.ms/webpi-azps).
- Windows PowerShell 'i açın ve **Connect-AzAccount** komutuyla hesabınızla ilişkilendirin. 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>SQL Server IaaS uzantısını yükler
Azure portal bir SQL Server VM sağladıysanız, SQL Server IaaS uzantısının zaten yüklü olması gerekir. **Get-AzVM** komutunu çağırarak ve **Uzantılar** özelliğini inceleyerek, VM 'niz için yüklü olup olmadığını belirleyebilirsiniz.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

SQL Server IaaS Aracısı uzantısı yüklüyse, "SqlIaaSAgent" veya "Sqliaasextenma" olarak listelenmiş olduğunu görmeniz gerekir. Uzantının **Provisioningstate** 'i de "başarılı" olarak gösterilmelidir.

Bu yüklü değilse veya sağlanmadıysa, aşağıdaki komutla yükleyebilirsiniz. VM adı ve kaynak grubuna ek olarak, sanal makinenizin bulunduğu bölgeyi (**$Region**) de belirtmeniz gerekir. SQL Server VM için lisans türünü belirtin, [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)aracılığıyla Kullandıkça Öde veya kendi lisansını getir seçeneklerinden birini belirleyin. Lisanslama hakkında daha fazla bilgi için bkz. [lisans modeli](licensing-model-azure-hybrid-benefit-ahb-change.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Uzantı zaten yüklü değilse, uzantıyı yüklemek SQL Server yeniden başlatır.

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Geçerli ayarları doğrulama

Sağlama sırasında otomatik yedeklemeyi etkinleştirdiyseniz, geçerli yapılandırmanızı denetlemek için PowerShell kullanabilirsiniz. **Get-AzVMSqlServerExtension** komutunu çalıştırın ve **oto backupsettings** özelliğini inceleyin:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Aşağıdakine benzer bir çıktı almalısınız:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Çıktılarınız **etkin** ' in **false** olarak ayarlandığını gösteriyorsa, Otomatik yedeklemeyi etkinleştirmeniz gerekir. İyi haber, Otomatik yedeklemeyi aynı şekilde etkinleştirmenizi ve yapılandırmanızı sağlar. Bu bilgi için sonraki bölüme bakın.

> [!NOTE] 
> Değişiklik yaptıktan hemen sonra ayarları denetledikten sonra eski yapılandırma değerlerini geri almanız mümkündür. Birkaç dakika bekleyin ve değişikliklerinizin uygulandığından emin olmak için ayarları yeniden denetleyin.

### <a name="configure-automated-backup"></a>Otomatik yedeklemeyi yapılandırma
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

Ardından, Azure depolama hesabında yedeklemeleri depolamak için otomatik yedekleme ayarlarını etkinleştirmek ve yapılandırmak üzere **New-Azvmsqlserverotomatikbackupconfig** komutunu kullanın. Bu örnekte, yedeklemeler 10 gün boyunca tutulur. İkinci komut, **set-AzVMSqlServerExtension**, BELIRTILEN Azure VM 'yi bu ayarlarla güncelleştirir.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

SQL Server IaaS aracısının yüklenmesi ve yapılandırılması birkaç dakika sürebilir.

> [!NOTE]
> **New-Azvmsqlserverotomatikbackupconfig** için yalnızca SQL Server 2016 ve otomatik yedekleme v2 için uygulanan başka ayarlar vardır. SQL Server 2014 şu ayarları desteklemez: **Backupsystemdbs**, **backupscheduletype**, **fullbackupfrequency**, **fullbackupstarthour**, **fullbackupwindowınhours** ve **LogBackupFrequencyInMinutes**. Bu ayarları SQL Server 2014 sanal makinesinde yapılandırmaya çalışırsanız, bir hata yoktur, ancak ayarlar uygulanmaz. Bu ayarları SQL Server 2016 sanal makinesinde kullanmak istiyorsanız, [SQL Server 2016 Azure sanal makineleri Için otomatik yedekleme v2](automated-backup.md)bölümüne bakın.

Şifrelemeyi etkinleştirmek için, önceki betiği, **Enableencryption** parametresini **CertificatePassword** parametresi için bir parola (güvenli dize) ile birlikte geçecek şekilde değiştirin. Aşağıdaki betik, önceki örnekteki otomatik yedekleme ayarlarını sağlar ve şifreleme ekler.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

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

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>İzleme

SQL Server 2014 ' de Otomatik yedeklemeyi izlemek için iki ana seçeneğiniz vardır. Otomatik yedekleme SQL Server yönetilen yedekleme özelliğini kullandığından, aynı izleme teknikleri her ikisi için de geçerlidir.

İlk olarak, [msdb. smart_admin. sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)öğesini çağırarak durumu yoklayabilmeniz gerekir. Veya [msdb. smart_admin. fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tablo değerli işlevini sorgulayın.

> [!NOTE]
> SQL Server 2014 ' de yönetilen yedekleme şeması **msdb.smart_admin**. SQL Server 2016 ' de **msdb.managed_backup** olarak değiştirilmiştir ve başvuru konuları bu yeni şemayı kullanır. Ancak SQL Server 2014 için, tüm yönetilen yedekleme nesneleri için **smart_admin** şemasını kullanmaya devam etmeniz gerekir.

Diğer bir seçenek de bildirimler için yerleşik Veritabanı Postası özelliğinden yararlanabilmenizi sağlar.

1. **SSMBackup2WANotificationEmailIds** parametresine bir e-posta adresi atamak için [msdb. smart_admin. sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) saklı yordamını çağırın. 
1. Azure VM 'den e-postaları göndermek için [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) 'i etkinleştirin.
1. Veritabanı Postası yapılandırmak için SMTP sunucusunu ve Kullanıcı adını kullanın. SQL Server Management Studio veya Transact-SQL komutlarıyla Veritabanı Postası yapılandırabilirsiniz. Daha fazla bilgi için bkz. [veritabanı postası](/sql/relational-databases/database-mail/database-mail).
1. [SQL Server Agent veritabanı postası kullanacak şekilde yapılandırın](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. SMTP bağlantı noktasına hem yerel VM Güvenlik Duvarı hem de VM için ağ güvenlik grubu aracılığıyla izin verildiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Otomatik yedekleme, Azure VM 'lerde yönetilen yedeklemeyi yapılandırır. Bu nedenle [SQL Server 2014 ' de yönetilen yedekleme belgelerinin gözden geçirilmesi](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)önemlidir.

Azure VM 'lerinde SQL Server için ek yedekleme ve geri yükleme kılavuzunu aşağıdaki makaleye bulabilirsiniz: [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](backup-restore.md).

Diğer kullanılabilir otomasyon görevleri hakkında daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](sql-server-iaas-agent-extension-automate-management.md).

Azure VM 'lerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md).