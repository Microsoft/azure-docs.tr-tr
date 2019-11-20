---
title: SQL Server sanal makineler için otomatik yedekleme (klasik) | Microsoft Docs
description: 'Kaynak Yöneticisi kullanarak Azure sanal makinelerinde çalışan SQL Server için otomatik yedekleme özelliğini açıklar. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: da40b635b0fc094275d8d46b8c5ad6d3d90bea24
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101821"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Azure sanal makinelerinde SQL Server için otomatik yedekleme (klasik)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klasik](../classic/sql-automated-backup.md)
> 
> 

Otomatik yedekleme, SQL Server 2014 Standard veya Enterprise çalıştıran bir Azure VM üzerindeki tüm mevcut ve yeni veritabanları için [Microsoft Azure üzere yönetilen yedeklemeyi](https://msdn.microsoft.com/library/dn449496.aspx) otomatik olarak yapılandırır. Bu, dayanıklı Azure Blob depolama kullanan düzenli veritabanı yedeklerini yapılandırmanızı sağlar. Otomatik yedekleme [SQL Server IaaS Aracısı uzantısına](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)bağlıdır.

> [!IMPORTANT] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir. Bu makalenin Kaynak Yöneticisi sürümünü görüntülemek için bkz. [Azure sanal makineler 'de SQL Server Için otomatik yedekleme kaynak yöneticisi](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Önkoşullar
Otomatik yedeklemeyi kullanmak için aşağıdaki önkoşulları göz önünde bulundurun:

**Işletim sistemi**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server sürüm/sürüm**:

* SQL Server 2014 standart
* SQL Server 2014 Enterprise

> [!NOTE]
> SQL Server 2016 için otomatik yedekleme, Kaynak Yöneticisi sanal makinelerle desteklenir. Daha fazla bilgi için bkz. [SQL Server 2016 Azure sanal makineleri Için otomatik yedekleme v2 (Kaynak Yöneticisi)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Veritabanı yapılandırması**:

* Hedef veritabanlarının tam kurtarma modelini kullanması gerekir.

**Azure PowerShell**:

* [En son Azure PowerShell komutlarını yükler](/powershell/azure/overview).

**IaaS uzantısını SQL Server**:

* [SQL Server IaaS uzantısını yükler](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ayarlar
Aşağıdaki tabloda otomatik yedekleme için yapılandırılabilecek seçenekler açıklanmaktadır. Klasik VM 'Lerde bu ayarları yapılandırmak için PowerShell kullanmanız gerekir.

| Ayar | Aralık (varsayılan) | Açıklama |
| --- | --- | --- |
| **Otomatik Yedekleme** |Etkinleştir/devre dışı bırak (devre dışı) |SQL Server 2014 Standard veya Enterprise çalıştıran bir Azure VM için Otomatik yedeklemeyi etkinleştirilir veya devre dışı bırakır. |
| **Bekletme dönemi** |1-30 gün (30 gün) |Bir yedeklemenin saklanacağı gün sayısı. |
| **Depolama Hesabı** |Azure depolama hesabı (belirtilen VM için oluşturulan depolama hesabı) |Blob depolamada otomatik yedekleme dosyaları depolamak için kullanılacak bir Azure depolama hesabı. Tüm yedekleme dosyalarını depolamak için bu konumda bir kapsayıcı oluşturulur. Yedekleme dosyası adlandırma kuralı, tarih, saat ve makine adını içerir. |
| **Şifreleme** |Etkinleştir/devre dışı bırak (devre dışı) |Şifrelemeyi etkinleştirilir veya devre dışı bırakır. Şifreleme etkinleştirildiğinde, yedeği geri yüklemek için kullanılan sertifikalar aynı adlandırma kuralını kullanarak aynı automaticbackup kapsayıcısında belirtilen depolama hesabında bulunur. Parola değişirse, bu parolayla yeni bir sertifika oluşturulur, ancak eski sertifika önceki yedeklemeleri geri yüklemek için kalır. |
| **Parola** |Parola metni (yok) |Şifreleme anahtarları için parola. Bu yalnızca Şifreleme etkinse gereklidir. Şifrelenmiş bir yedeklemeyi geri yüklemek için, yedekleme sırasında kullanılan doğru parolaya ve ilgili sertifikaya sahip olmanız gerekir. |
| **Yedekleme sistemi veritabanları** | Etkinleştir/devre dışı bırak (devre dışı) | Ana, model ve MSDB 'nin tam yedeklemelerini al |
| **Yedekleme Zamanlamasını Yapılandır** | El ile/otomatik (otomatik) | Günlük büyümesini temel alarak otomatik olarak tam ve günlük yedeklemeleri almak için **Otomatik** ' i seçin. Tam ve günlük yedeklemeleri için zamanlamayı belirtmek üzere **el ile** ' yi seçin. |

## <a name="configuration-with-powershell"></a>PowerShell ile yapılandırma
Aşağıdaki PowerShell örneğinde, otomatik yedekleme, mevcut bir SQL Server 2014 VM için yapılandırılmıştır. **New-AzureVMSqlServerAutoBackupConfig** komutu, yedeklemeleri $storageaccount değişkeni tarafından belirtilen Azure depolama hesabında depolamak Için otomatik yedekleme ayarlarını yapılandırır. Bu yedeklemeler 10 gün boyunca tutulacaktır. **Set-AzureVMSqlServerExtension** komutu, belirtilen Azure VM 'yi bu ayarlarla güncelleştirir.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

SQL Server IaaS aracısının yüklenmesi ve yapılandırılması birkaç dakika sürebilir.

Şifrelemeyi etkinleştirmek için, önceki betiği, EnableEncryption parametresini CertificatePassword parametresi için bir parola (güvenli dize) ile birlikte geçecek şekilde değiştirin. Aşağıdaki betik, önceki örnekteki otomatik yedekleme ayarlarını sağlar ve şifreleme ekler.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Otomatik yedeklemeyi devre dışı bırakmak için, **-Enable** parametresi olmadan aynı betiği **New-AzureVMSqlServerAutoBackupConfig**' a çalıştırın. Yükleme sırasında olduğu gibi, Otomatik yedeklemeyi devre dışı bırakmak birkaç dakika sürebilir.

> [!NOTE]
> SQL Server IaaS aracısının devre dışı bırakılması ve kaldırılması, önceden yapılandırılmış yönetilen yedekleme ayarlarını kaldırmaz. SQL Server IaaS aracısını devre dışı bırakmadan veya kaldırmadan Otomatik yedeklemeyi devre dışı bırakmanız gerekir.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Otomatik yedekleme, Azure VM 'lerde yönetilen yedeklemeyi yapılandırır. Bu nedenle, davranışı ve etkilerini anlamak için [yönetilen yedekleme belgelerinin gözden geçirilmesi](https://msdn.microsoft.com/library/dn449496.aspx) önemlidir.

Aşağıdaki konuda, Azure VM 'lerinde SQL Server için ek yedekleme ve geri yükleme kılavuzu bulabilirsiniz: [Azure sanal makineler 'de SQL Server Için Yedekleme ve geri yükleme](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Diğer kullanılabilir otomasyon görevleri hakkında daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](../classic/sql-server-agent-extension.md).

Azure VM 'lerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

