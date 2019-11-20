---
title: SQL Server VM 'Ler için otomatik düzeltme eki uygulama (klasik) | Microsoft Docs
description: Klasik dağıtım modunu kullanarak Azure 'da çalışan SQL Server sanal makinelere yönelik otomatik düzeltme eki uygulama özelliğini açıklar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9fabccd477883750c1aecb5493fdb64ddf5ab2c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100287"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Azure sanal makinelerinde SQL Server için otomatik düzeltme eki uygulama (klasik)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klasik](../classic/sql-automated-patching.md)
> 
> 

Otomatik düzeltme eki uygulama SQL Server çalıştıran bir Azure sanal makinesine yönelik bir bakım penceresi oluşturur. Otomatik Güncelleştirmeler yalnızca bu bakım penceresi sırasında yüklenebilir. SQL Server için, bu, sistem güncelleştirmelerinin ve ilişkili tüm yeniden başlatmalarının veritabanı için mümkün olan en iyi zamanda gerçekleşmesini sağlar. 

> [!IMPORTANT]
> Yalnızca **Önemli** olarak işaretlenmiş Windows güncelleştirmeleri yüklenir. Toplu Güncelleştirmeler gibi diğer SQL Server güncelleştirmelerinin el ile yüklenmesi gerekir. 

Otomatik Yama Uygulama [SQL Server IaaS Aracı Uzantısı](../classic/sql-server-agent-extension.md)'na bağımlıdır.

> [!IMPORTANT] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir. Bu makalenin Kaynak Yöneticisi sürümünü görüntülemek için bkz. [Azure sanal makineler 'de SQL Server Için otomatik düzeltme eki uygulama kaynak yöneticisi](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Önkoşullar
Otomatik düzeltme eki kullanmak için aşağıdaki önkoşulları göz önünde bulundurun:

**Işletim sistemi**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server sürümü**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [En son Azure PowerShell komutlarını yükler](/powershell/azure/overview).

**IaaS uzantısını SQL Server**:

* [SQL Server IaaS uzantısını yükler](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ayarlar
Aşağıdaki tabloda otomatik düzeltme eki uygulama için yapılandırılabilecek seçenekler açıklanmaktadır. Klasik VM 'Lerde bu ayarları yapılandırmak için PowerShell kullanmanız gerekir.

| Ayar | Olası değerler | Açıklama |
| --- | --- | --- |
| **Otomatik Düzeltme Eki Uygulama** |Etkinleştir/devre dışı bırak (devre dışı) |Bir Azure sanal makinesi için otomatik düzeltme eki uygulamayı etkinleştirilir veya devre dışı bırakır. |
| **Bakım zamanlaması** |Günlük, Pazartesi, Salı, Çarşamba, Perşembe, Cuma, Cumartesi, Pazar |Sanal makineniz için Windows, SQL Server ve Microsoft güncelleştirmelerini indirme ve yükleme zamanlaması. |
| **Bakım başlangıç saati** |0-24 |Sanal makineyi güncelleştirmek için yerel başlangıç saati. |
| **Bakım penceresi süresi** |30-180 |Güncelleştirmelerin indirilmesini ve yüklenmesini tamamlamaya izin verilen dakika sayısı. |
| **Düzeltme Eki kategorisi** |Önemli |İndirilecek ve yüklenecek güncelleştirmeler kategorisi. |

## <a name="configuration-with-powershell"></a>PowerShell ile yapılandırma
Aşağıdaki örnekte, PowerShell, mevcut bir SQL Server VM otomatik düzeltme eki yapılandırmak için kullanılır. **New-AzureVMSqlServerAutoPatchingConfig** komutu otomatik güncelleştirmeler için yeni bir bakım penceresi yapılandırır.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Bu örneğe bağlı olarak, aşağıdaki tabloda hedef Azure VM üzerinde pratik etki açıklanmaktadır:

| Parametre | Etki |
| --- | --- |
| **DayOfWeek** |Her Perşembe için düzeltme eki yüklendi. |
| **MaintenanceWindowStartingHour** |Güncelleştirmeler 11:00:00:00 ile başlatılır. |
| **MaintenanceWindowDuration** |Düzeltme eklerinin 120 dakika içinde yüklü olması gerekir. Başlangıç zamanına bağlı olarak, 1:00pm ile tamamlanmaları gerekir. |
| **PatchCategory** |Bu parametre için olası tek ayar "önemli" dır. |

SQL Server IaaS aracısının yüklenmesi ve yapılandırılması birkaç dakika sürebilir.

Otomatik Düzeltme Eki uygulamayı devre dışı bırakmak için,-enable parametresi olmadan aynı betiği New-AzureVMSqlServerAutoPatchingConfig ' a çalıştırın. Yükleme sırasında olduğu gibi, otomatik düzeltme eki uygulamayı devre dışı bırakmak birkaç dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar
Diğer kullanılabilir otomasyon görevleri hakkında daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](../classic/sql-server-agent-extension.md).

Azure VM 'lerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

