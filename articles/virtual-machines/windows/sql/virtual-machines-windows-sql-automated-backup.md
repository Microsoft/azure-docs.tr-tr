---
title: SQL Server 2014 Azure sanal makineleri için otomatik yedekleme | Microsoft Docs
description: Azure 'da çalışan SQL Server 2014 VM 'lerinin otomatik yedekleme özelliğini açıklar. Bu makale, Kaynak Yöneticisi kullanan VM 'lere özeldir.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0cfcbdaee5a39a947bd89c677f49214c8c3cb98a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162855"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>SQL Server 2014 sanal makineleri için otomatik yedekleme (Kaynak Yöneticisi)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Otomatik yedekleme, SQL Server 2014 Standard veya Enterprise çalıştıran bir Azure VM üzerindeki tüm mevcut ve yeni veritabanları için [Microsoft Azure üzere yönetilen yedeklemeyi](https://msdn.microsoft.com/library/dn449496.aspx) otomatik olarak yapılandırır. Bu, dayanıklı Azure Blob depolama kullanan düzenli veritabanı yedeklerini yapılandırmanızı sağlar. Otomatik yedekleme [SQL Server IaaS Aracısı uzantısına](virtual-machines-windows-sql-server-agent-extension.md)bağlıdır.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Önkoşullar
Otomatik yedeklemeyi kullanmak için aşağıdaki önkoşulları göz önünde bulundurun:

**Işletim sistemi**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server sürüm/sürüm**:

- SQL Server 2014 standart
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Otomatik yedekleme SQL Server 2014 ile birlikte kullanılabilir. SQL Server 2016/2017 kullanıyorsanız, veritabanlarınızı yedeklemek için otomatik yedekleme v2 'yi kullanabilirsiniz. Daha fazla bilgi için bkz. [SQL Server 2016 Azure sanal makineleri Için otomatik yedekleme v2](virtual-machines-windows-sql-automated-backup-v2.md).

**Veritabanı yapılandırması**:

- Hedef veritabanlarının tam kurtarma modelini kullanması gerekir. Yedeklemelerdeki tam kurtarma modelinin etkileri hakkında daha fazla bilgi için, bkz. [yedekleme tam kurtarma modeli altında](https://technet.microsoft.com/library/ms190217.aspx).
- Hedef veritabanlarının varsayılan SQL Server örneğinde olması gerekir. SQL Server IaaS uzantısı adlandırılmış örnekleri desteklemiyor.

> [!NOTE]
> Otomatik yedekleme SQL Server IaaS Aracısı uzantısına dayanır. Geçerli SQL sanal makine galeri görüntüleri varsayılan olarak bu uzantıyı ekler. Daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Ayarlar

Aşağıdaki tabloda otomatik yedekleme için yapılandırılabilecek seçenekler açıklanmaktadır. Gerçek yapılandırma adımları Azure portal veya Azure Windows PowerShell komutlarını kullanıp kullanmayacağınızı bağlı olarak değişir.

| Ayar | Aralık (varsayılan) | Açıklama |
| --- | --- | --- |
| **Otomatik Yedekleme** | Etkinleştir/devre dışı bırak (devre dışı) | SQL Server 2014 Standard veya Enterprise çalıştıran bir Azure VM için Otomatik yedeklemeyi etkinleştirilir veya devre dışı bırakır. |
| **Bekletme dönemi** | 1-30 gün (30 gün) | Bir yedeklemenin saklanacağı gün sayısı. |
| **Depolama Hesabı** | Azure depolama hesabı | Blob depolamada otomatik yedekleme dosyaları depolamak için kullanılacak bir Azure depolama hesabı. Tüm yedekleme dosyalarını depolamak için bu konumda bir kapsayıcı oluşturulur. Yedekleme dosyası adlandırma kuralı, tarih, saat ve makine adını içerir. |
| **Şifreleme** | Etkinleştir/devre dışı bırak (devre dışı) | Şifrelemeyi etkinleştirilir veya devre dışı bırakır. Şifreleme etkinleştirildiğinde, yedeği geri yüklemek için kullanılan sertifikalar aynı `automaticbackup` kapsayıcısında aynı adlandırma kuralına göre belirtilen depolama hesabında bulunur. Parola değişirse, bu parolayla yeni bir sertifika oluşturulur, ancak eski sertifika önceki yedeklemeleri geri yüklemek için kalır. |
| **Parola** | Parola metni | Şifreleme anahtarları için parola. Bu yalnızca Şifreleme etkinse gereklidir. Şifrelenmiş bir yedeklemeyi geri yüklemek için, yedekleme sırasında kullanılan doğru parolaya ve ilgili sertifikaya sahip olmanız gerekir. |

## <a name="configure-in-the-portal"></a>Portalda yapılandırma

Sağlama sırasında veya mevcut SQL Server 2014 VM 'Ler için Otomatik yedeklemeyi yapılandırmak üzere Azure portal kullanabilirsiniz.

## <a name="configure-new-vms"></a>Yeni VM 'Leri yapılandırma

Kaynak Yöneticisi dağıtım modelinde yeni bir SQL Server 2014 sanal makinesi oluşturduğunuzda otomatik yedeklemeyi yapılandırmak için Azure portal kullanın.

**SQL Server ayarları** sekmesinde, **otomatik yedekleme** ' ye kaydırın ve **Etkinleştir**' i seçin. Ayrıca, saklama süresini ve depolama hesabını da belirtebilir, şifrelemeyi etkinleştirir, sistem veritabanlarını yedekleyebilir ve bir yedekleme zamanlaması yapılandırabilirsiniz.  Aşağıdaki Azure portal ekran görüntüsü **SQL otomatik yedekleme** ayarlarını gösterir.

![Azure portal 'de SQL otomatik yedekleme yapılandırması](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Mevcut VM 'Leri yapılandırma

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Mevcut SQL Server sanal makineler için [SQL sanal makineler kaynağına](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) gidin ve **yedeklemeler**' i seçin. 

![Mevcut VM 'Ler için SQL otomatik yedekleme](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

İşiniz bittiğinde, değişiklikleri kaydetmek için **yedeklemeler** sayfasının alt kısmındaki **Uygula** düğmesini seçin.

Otomatik yedeklemeyi ilk kez etkinleştirirseniz Azure SQL Server IaaS aracısını arka planda yapılandırır. Bu süre boyunca, Azure portal otomatik yedeklemenin yapılandırıldığını gösteremeyebilir. Aracının yüklenmesi, yapılandırılması için birkaç dakika bekleyin. Azure portal sonra yeni ayarları yansıtacaktır.

> [!NOTE]
> Ayrıca, bir şablon kullanarak Otomatik yedeklemeyi yapılandırabilirsiniz. Daha fazla bilgi için bkz. [otomatik yedekleme Için Azure hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>PowerShell ile yapılandırma

Otomatik yedeklemeyi yapılandırmak için PowerShell kullanabilirsiniz. Başlamadan önce şunları yapmanız gerekir:

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

> [!IMPORTANT]
> Uzantı zaten yüklü değilse, uzantıyı yüklemek SQL Server hizmetini yeniden başlatır.

### <a id="verifysettings"></a>Geçerli ayarları doğrulama

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

Çıktılarınız **etkin** ' in **false**olarak ayarlandığını gösteriyorsa, Otomatik yedeklemeyi etkinleştirmeniz gerekir. İyi haber, Otomatik yedeklemeyi aynı şekilde etkinleştirmenizi ve yapılandırmanızı sağlar. Bu bilgi için sonraki bölüme bakın.

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
> **New-Azvmsqlserverotomatikbackupconfig** için yalnızca SQL Server 2016 ve otomatik yedekleme v2 için uygulanan başka ayarlar vardır. SQL Server 2014 şu ayarları desteklemez: **Backupsystemdbs**, **backupscheduletype**, **fullbackupfrequency**, **fullbackupstarthour**, **fullbackupwindowınhours**ve  **LogBackupFrequencyInMinutes**. Bu ayarları SQL Server 2014 sanal makinesinde yapılandırmaya çalışırsanız, bir hata yoktur, ancak ayarlar uygulanmaz. Bu ayarları SQL Server 2016 sanal makinesinde kullanmak istiyorsanız, [SQL Server 2016 Azure sanal makineleri Için otomatik yedekleme v2](virtual-machines-windows-sql-automated-backup-v2.md)bölümüne bakın.

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>İzleme

SQL Server 2014 ' de Otomatik yedeklemeyi izlemek için iki ana seçeneğiniz vardır. Otomatik yedekleme SQL Server yönetilen yedekleme özelliğini kullandığından, aynı izleme teknikleri her ikisi için de geçerlidir.

İlk olarak, [msdb. smart_admin. sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)öğesini çağırarak durumu yoklayabilmeniz gerekir. Veya [msdb. smart_admin. fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tablo değerli işlevini sorgulayın.

> [!NOTE]
> SQL Server 2014 ' deki yönetilen yedekleme şeması **msdb. smart_admin**' dir. SQL Server 2016 ' de **msdb. managed_backup**olarak değiştirilmiştir ve başvuru konuları bu yeni şemayı kullanır. Ancak SQL Server 2014 için, tüm yönetilen yedekleme nesneleri için **smart_admin** şemasını kullanmaya devam etmeniz gerekir.

Diğer bir seçenek de bildirimler için yerleşik Veritabanı Postası özelliğinden yararlanabilmenizi sağlar.

1. **SSMBackup2WANotificationEmailIds** parametresine bir e-posta adresi atamak için [msdb. smart_admin. sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) saklı yordamını çağırın. 
1. Azure VM 'den e-postaları göndermek için [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) 'i etkinleştirin.
1. Veritabanı Postası yapılandırmak için SMTP sunucusunu ve Kullanıcı adını kullanın. SQL Server Management Studio veya Transact-SQL komutlarıyla Veritabanı Postası yapılandırabilirsiniz. Daha fazla bilgi için bkz. [veritabanı postası](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [SQL Server Agent veritabanı postası kullanacak şekilde yapılandırın](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. SMTP bağlantı noktasına hem yerel VM Güvenlik Duvarı hem de VM için ağ güvenlik grubu aracılığıyla izin verildiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Otomatik yedekleme, Azure VM 'lerde yönetilen yedeklemeyi yapılandırır. Bu nedenle [SQL Server 2014 ' de yönetilen yedekleme belgelerinin gözden geçirilmesi](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx)önemlidir.

Azure VM 'lerinde SQL Server için ek yedekleme ve geri yükleme kılavuzunu aşağıdaki makaleye bulabilirsiniz: [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](virtual-machines-windows-sql-backup-recovery.md).

Diğer kullanılabilir otomasyon görevleri hakkında daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Azure VM 'lerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](virtual-machines-windows-sql-server-iaas-overview.md).
