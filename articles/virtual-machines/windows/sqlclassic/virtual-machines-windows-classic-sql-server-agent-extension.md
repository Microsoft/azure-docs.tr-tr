---
title: SQL VM 'lerinde yönetim görevlerini otomatikleştirme (klasik) | Microsoft Docs
description: Bu konuda, belirli SQL Server yönetim görevlerini otomatikleştiren SQL Server Agent uzantısının nasıl yönetileceği açıklanmaktadır. Bunlar otomatik yedekleme, otomatik düzeltme eki uygulama ve Azure Key Vault tümleştirmeyi içerir. Bu konu klasik dağıtım modunu kullanır.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e58b3f651a7a8f65d3450cf4b069d4e3f252d928
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100275"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>SQL Server Agent uzantısı (klasik) ile Azure sanal makinelerinde yönetim görevlerini otomatikleştirme
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Klasik](../classic/sql-server-agent-extension.md)
> 
>
 
SQL Server IaaS Aracısı uzantısı (SQLIaaSAgent), yönetim görevlerini otomatikleştirmek için Azure sanal makinelerinde çalışır. Bu konu, uzantının desteklediği hizmetlere ve yükleme, durum ve kaldırma yönergelerine ilişkin genel bir bakış sağlar.

> [!IMPORTANT] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir. Bu makalenin Kaynak Yöneticisi sürümünü görüntülemek için, bkz. [SQL Server VM 'ler için SQL Server Agent uzantısı Kaynak Yöneticisi](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Desteklenen hizmetler
SQL Server IaaS Aracısı uzantısı aşağıdaki yönetim görevlerini destekler:

| Yönetim özelliği | Açıklama |
| --- | --- |
| **SQL otomatik yedekleme** |VM 'deki SQL Server varsayılan örneği için tüm veritabanları için yedeklemelerin zamanlamasını otomatikleştirir. Daha fazla bilgi için bkz. [Azure sanal makineler 'de SQL Server Için otomatik yedekleme (klasik)](../classic/sql-automated-backup.md). |
| **SQL otomatik düzeltme eki uygulama** |VM 'niz için önemli Windows güncelleştirmelerinin gerçekleşmesi sırasında bir bakım penceresi yapılandırır, bu sayede iş yükünüz için yoğun zamanlarda güncelleştirmelerden kaçınabilirsiniz. Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için otomatik düzeltme eki uygulama (klasik)](../classic/sql-automated-patching.md). |
| **Azure Anahtar Kasası Tümleştirmesi** |SQL Server VM Azure Key Vault otomatik olarak yüklemenize ve yapılandırmanıza olanak sağlar. Daha fazla bilgi için bkz. [Azure VM 'lerde SQL Server için Azure Key Vault tümleştirmesini yapılandırma (klasik)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Önkoşullar
SANAL makinenizde SQL Server IaaS Aracısı uzantısını kullanma gereksinimleri:

### <a name="operating-system"></a>İşletim Sistemi:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server sürümleri:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[En son Azure PowerShell komutlarını indirin ve yapılandırın](/powershell/azure/overview).

Windows PowerShell 'i başlatın ve **Add-AzureAccount** komutuyla Azure aboneliğinize bağlayın.

    Add-AzureAccount

Birden çok aboneliğiniz varsa, **Select-azuyeniden göndermeyi** kullanarak hedef klasik VM 'nizi içeren aboneliği seçin.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Bu noktada, **Get-AzureVM** komutuyla klasik sanal makinelerin ve bunların ilişkili hizmet adlarının bir listesini alabilirsiniz.

    Get-AzureVM

## <a name="installation"></a>Yükleme
Klasik VM 'Lerde, SQL Server IaaS Aracısı uzantısını yüklemek ve ilişkili hizmetlerini yapılandırmak için PowerShell kullanmanız gerekir. Uzantıyı yüklemek için **set-AzureVMSqlServerExtension** PowerShell cmdlet 'ini kullanın. Örneğin, aşağıdaki komut, uzantıyı bir Windows Server VM 'ye (klasik) yükleyip "Sqliaasextenma" olarak adlandırır.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

SQL IaaS aracı uzantısının en son sürümüne güncelleştirirseniz, uzantıyı güncelleştirdikten sonra sanal makinenizi yeniden başlatmanız gerekir.

> [!NOTE]
> Klasik sanal makinelerde, Portal üzerinden SQL IaaS Aracısı uzantısını yükleyip yapılandırma seçeneği yoktur.

> [!NOTE]
> SQL Server IaaS Aracısı uzantısı yalnızca [SQL Server VM Galeri görüntülerinde](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) desteklenir (Kullandıkça Öde veya kendi lisansını getir). Yalnızca bir işletim sistemi Windows Server sanal makinesine SQL Server el ile yüklüyorsanız veya özelleştirilmiş bir SQL Server VM VHD dağıtırsanız desteklenmez. Bu gibi durumlarda, PowerShell kullanarak uzantıyı el ile yüklemek ve yönetmek mümkün olabilir, ancak bunun yerine SQL Server VM Galeri görüntüsünü yükleyip özelleştirmek önemle önerilir.

## <a name="status"></a>Durum
Uzantının yüklendiğini doğrulamak için bir yol, Azure portalında aracı durumunu görüntülemek için kullanılır. Sanal makine dikey penceresinde listelenen bir sanal makineyi seçin ve ardından **Uzantılar**' a tıklayın. **Sqliaasagent** uzantısını listede görmeniz gerekir.

![Azure portalında IaaS Aracısı uzantısı SQL Server](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

**Get-AzureVMSqlServerExtension** Azure PowerShell cmdlet 'ini de kullanabilirsiniz.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Çıkarma
Azure portalında, sanal makine özelliklerinin **Uzantılar** dikey penceresinde üç nokta simgesine tıklayarak uzantıyı kaldırabilirsiniz. Ardından **Kaldır**' a tıklayın.

![Azure portalında SQL Server IaaS Aracısı uzantısını kaldırma](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

**Remove-AzureVMSqlServerExtension** PowerShell cmdlet 'ini de kullanabilirsiniz.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Sonraki Adımlar
Uzantı tarafından desteklenen hizmetlerden birini kullanmaya başlayın. Daha fazla ayrıntı için, bu makalenin [desteklenen hizmetler](#supported-services) bölümünde başvurulan konulara bakın.

Azure sanal makinelerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

