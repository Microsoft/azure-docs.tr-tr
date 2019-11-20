---
title: IaaS Aracısı Uzantısı ile yönetim görevlerini otomatikleştirme
description: Bu makalede, belirli SQL Server yönetim görevlerini otomatikleştiren SQL Server IaaS aracı uzantısının nasıl yönetileceği açıklanır. Bunlar otomatik yedekleme, otomatik düzeltme eki uygulama ve Azure Key Vault tümleştirmeyi içerir.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 9aae386e21df6711fc4984a7abfd34f418399f76
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034191"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS Aracısı uzantısını kullanarak Azure sanal makinelerinde yönetim görevlerini otomatikleştirme
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasik](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Aracı Uzantısı Extension (SqlIaasExtension) Azure sanal makinelerinde yönetim görevlerini otomatikleştirmek için çalıştırılır. Bu makale, uzantının desteklediği hizmetlere genel bir bakış sağlar. Bu makale ayrıca uzantının yüklenmesi, durumu ve kaldırılması için yönergeler sağlar.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Bu makalenin klasik sürümünü görüntülemek için, bkz. [SQL Server VM 'ler Için IaaS Aracısı uzantısı SQL Server (klasik)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Desteklenen hizmetler
SQL Server IaaS Aracısı uzantısı aşağıdaki yönetim görevlerini destekler:

| Yönetim özelliği | Açıklama |
| --- | --- |
| **Otomatik yedekleme SQL Server** |Tüm veritabanları için, varsayılan örnek veya VM üzerinde [doğru şekilde yüklenmiş](virtual-machines-windows-sql-server-iaas-faq.md#administration) bir SQL Server örneği için yedeklemelerin zamanlamasını otomatikleştirir. Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için otomatik yedekleme (Kaynak Yöneticisi)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server otomatik düzeltme eki uygulama** |VM 'niz için önemli Windows güncelleştirmelerinin gerçekleşmesi sırasında bir bakım penceresi yapılandırır, bu sayede iş yükünüz için yoğun zamanlarda güncelleştirmelerden kaçınabilirsiniz. Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için otomatik düzeltme eki uygulama (Kaynak Yöneticisi)](virtual-machines-windows-sql-automated-patching.md). |
| **Azure Key Vault tümleştirme** |SQL Server VM Azure Key Vault otomatik olarak yüklemenize ve yapılandırmanıza olanak sağlar. Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server için Azure Key Vault tümleştirmesini yapılandırma (Kaynak Yöneticisi)](virtual-machines-windows-ps-sql-keyvault.md). |

SQL Server IaaS Aracısı uzantısı yüklendikten ve çalıştırıldıktan sonra, yönetim özellikleri kullanılabilir hale gelir:

* Azure portal sanal makinenin SQL Server panelinde ve Azure Marketi 'ndeki SQL Server görüntüleri için Azure PowerShell üzerinden.
* Azure PowerShell aracılığıyla uzantının el ile yüklenmesi için. 

## <a name="prerequisites"></a>Önkoşullar
SANAL makinenizde SQL Server IaaS Aracısı uzantısını kullanmak için gerekenler şunlardır:

**İşletim sistemi**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server sürümü**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [En son Azure PowerShell komutlarını indirin ve yapılandırın](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Yükleme
SQL Server IaaS uzantısı, [SQL VM kaynak sağlayıcısı](virtual-machines-windows-sql-register-with-resource-provider.md)ile SQL Server VM kaydettiğinizde yüklenir. Gerekirse, aşağıdaki PowerShell komutunu kullanarak IaaS aracısını el ile SQL Server yükleyebilirsiniz: 

  ```powershell-interactive
    Set-AzVMExtension -ResourceGroupName "<ResourceGroupName>" `
    -Location "<VMLocation>" -VMName "<VMName>" `
    -Name "SqlIaasExtension" -Publisher "Microsoft.SqlServer.Management" `
    -ExtensionType "SqlIaaSAgent" -TypeHandlerVersion "2.0";  
  ```

> [!NOTE]
> Uzantının yüklenmesi SQL Server hizmetini yeniden başlatır. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Tek bir adlandırılmış SQL Server örneğine sahip bir VM 'ye yükler
SQL Server IaaS uzantısı, varsayılan örnek kaldırılırsa ve IaaS uzantısının yeniden yüklenmesi durumunda SQL Server bir adlandırılmış örnekle çalışır.

Adlandırılmış bir SQL Server örneğini kullanmak için şu adımları izleyin:
   1. Azure Marketi 'nden bir SQL Server VM dağıtın. 
   1. IaaS uzantısını [Azure Portal](https://portal.azure.com)kaldırın.
   1. SQL Server tamamen SQL Server VM içinde kaldırın.
   1. SQL Server VM içindeki adlandırılmış bir örnekle SQL Server yükler. 
   1. Azure portal IaaS uzantısını yükler.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>SQL Server IaaS uzantısının durumunu al
Uzantının yüklendiğini doğrulamak için bir yol, Azure portal aracı durumunu görüntülemek için kullanılır. Sanal makine penceresinde **Tüm ayarlar** ' ı seçin ve ardından **Uzantılar**' ı seçin. **Sqliaasextenma** uzantısının listelendiğini görmeniz gerekir.

![Azure portal IaaS Aracısı uzantısının durumu SQL Server](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzVMSqlServerExtension** Azure PowerShell cmdlet 'ini de kullanabilirsiniz:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Önceki komut, aracının yüklendiğini onaylar ve genel durum bilgilerini sağlar. Aşağıdaki komutları kullanarak otomatik yedekleme ve düzeltme eki uygulama hakkında belirli durum bilgilerini alabilirsiniz:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Kaldırılmasını
Azure portal, sanal makine özelliklerinin **Uzantılar** penceresinde üç nokta simgesini seçerek uzantıyı kaldırabilirsiniz. Ardından **Sil**’i seçin.

![Azure portal IaaS Aracısı uzantısı SQL Server kaldırılıyor](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzVMSqlServerExtension** PowerShell cmdlet 'ini de kullanabilirsiniz:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Sonraki adımlar
Uzantının desteklediği hizmetlerden birini kullanmaya başlayın. Daha fazla bilgi için, bu makalenin [desteklenen hizmetler](#supported-services) bölümünde başvurulan makalelere bakın.

Azure sanal makinelerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server nedir?](virtual-machines-windows-sql-server-iaas-overview.md).
