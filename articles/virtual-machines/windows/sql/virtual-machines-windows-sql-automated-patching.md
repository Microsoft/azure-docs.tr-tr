---
title: SQL Server VM 'Ler için otomatik düzeltme eki uygulama (Kaynak Yöneticisi) | Microsoft Docs
description: Kaynak Yöneticisi kullanarak Azure 'da çalışan SQL Server sanal makinelere yönelik otomatik düzeltme eki uygulama özelliğini açıklar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0900dd1809ecb1e93906b57483f334d0f12f6582
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102071"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Azure Virtual Machines’de (Resource Manager) SQL Server için Otomatik Düzeltme Eki Uygulama
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Klasik](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

Otomatik düzeltme eki uygulama SQL Server çalıştıran bir Azure sanal makinesine yönelik bir bakım penceresi oluşturur. Otomatik Güncelleştirmeler yalnızca bu bakım penceresi sırasında yüklenebilir. SQL Server için bu kısıtlama, sistem güncelleştirmelerinin ve ilişkili tüm yeniden başlatmaların veritabanı için mümkün olan en uygun zamanda yapılmasını sağlar. 

> [!IMPORTANT]
> Yalnızca **Önemli** olarak işaretlenmiş Windows güncelleştirmeleri yüklenir. Toplu Güncelleştirmeler gibi diğer SQL Server güncelleştirmelerinin el ile yüklenmesi gerekir. 

Otomatik Yama Uygulama [SQL Server IaaS Aracı Uzantısı](virtual-machines-windows-sql-server-agent-extension.md)'na bağımlıdır.

## <a name="prerequisites"></a>Önkoşullar
Otomatik düzeltme eki kullanmak için aşağıdaki önkoşulları göz önünde bulundurun:

**Işletim sistemi**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server sürümü**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* PowerShell ile otomatik düzeltme eki uygulamayı yapılandırmayı planlıyorsanız [en son Azure PowerShell komutlarını yükleyebilirsiniz](/powershell/azure/overview) .

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Otomatik düzeltme eki uygulama SQL Server IaaS Aracısı uzantısına dayanır. Geçerli SQL sanal makine galeri görüntüleri varsayılan olarak bu uzantıyı ekler. Daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Ayarlar
Aşağıdaki tabloda otomatik düzeltme eki uygulama için yapılandırılabilecek seçenekler açıklanmaktadır. Gerçek yapılandırma adımları Azure portal veya Azure Windows PowerShell komutlarını kullanıp kullanmayacağınızı bağlı olarak değişir.

| Ayar | Olası değerler | Açıklama |
| --- | --- | --- |
| **Otomatik Düzeltme Eki Uygulama** |Etkinleştir/devre dışı bırak (devre dışı) |Bir Azure sanal makinesi için otomatik düzeltme eki uygulamayı etkinleştirilir veya devre dışı bırakır. |
| **Bakım zamanlaması** |Günlük, Pazartesi, Salı, Çarşamba, Perşembe, Cuma, Cumartesi, Pazar |Sanal makineniz için Windows, SQL Server ve Microsoft güncelleştirmelerini indirme ve yükleme zamanlaması. |
| **Bakım başlangıç saati** |0-24 |Sanal makineyi güncelleştirmek için yerel başlangıç saati. |
| **Bakım penceresi süresi** |30-180 |Güncelleştirmelerin indirilmesini ve yüklenmesini tamamlamaya izin verilen dakika sayısı. |
| **Düzeltme Eki kategorisi** |Önemli | İndirilecek ve yüklenecek Windows güncelleştirmelerinin kategorisi.|

## <a name="configuration-in-the-portal"></a>Portalda yapılandırma
Azure portal, sağlama sırasında veya var olan VM 'Lerde otomatik düzeltme eki uygulamayı yapılandırmak için kullanabilirsiniz.

### <a name="new-vms"></a>Yeni VM 'Ler
Kaynak Yöneticisi dağıtım modelinde yeni bir SQL Server sanal makine oluşturduğunuzda otomatik düzeltme eki yapılandırmak için Azure portal kullanın.

**SQL Server ayarları** sekmesinde **Otomatik Düzeltme Eki**altında **yapılandırmayı Değiştir** ' i seçin. Aşağıdaki Azure portal ekran görüntüsünde **SQL otomatik düzeltme eki uygulama** dikey penceresi gösterilmektedir.

![Azure portal 'de SQL otomatik düzeltme eki uygulama](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Bağlam için [Azure 'da SQL Server sanal makine sağlama](virtual-machines-windows-portal-sql-server-provision.md)konusundaki tüm konuya bakın.

### <a name="existing-vms"></a>Mevcut VM 'Ler

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Mevcut SQL Server sanal makineler için [SQL sanal makineler](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) kaynağınızı açın ve **Ayarlar**altında **Düzeltme Eki** ' ni seçin. 

![Mevcut VM 'Ler için SQL otomatik düzeltme eki uygulama](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


İşiniz bittiğinde, değişikliklerinizi kaydetmek için **SQL Server yapılandırma** dikey penceresinin altındaki **Tamam** düğmesine tıklayın.

Otomatik Düzeltme Eki uygulamayı ilk kez etkinleştirirseniz Azure SQL Server IaaS aracısını arka planda yapılandırır. Bu süre boyunca, Azure portal otomatik düzeltme eki uygulama 'nın yapılandırıldığını gösteremeyebilir. Aracının yüklenmesi, yapılandırılması için birkaç dakika bekleyin. Azure portal sonra yeni ayarları yansıtır.

## <a name="configuration-with-powershell"></a>PowerShell ile yapılandırma
SQL VM 'nizi sağlamaktan sonra otomatik düzeltme eki yapılandırmak için PowerShell 'i kullanın.

Aşağıdaki örnekte, PowerShell, mevcut bir SQL Server VM otomatik düzeltme eki yapılandırmak için kullanılır. **New-Azvmsqlserverotomatik Patchingconfig** komutu otomatik güncelleştirmeler için yeni bir bakım penceresi yapılandırır.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s set-AzVMSqlServerExtension-oto Patchingsettings $aps-VMName $vmname-ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Uzantı zaten yüklü değilse, uzantıyı yüklemek SQL Server hizmetini yeniden başlatır.

Bu örneğe bağlı olarak, aşağıdaki tabloda hedef Azure VM üzerinde pratik etki açıklanmaktadır:

| Parametre | Etki |
| --- | --- |
| **DayOfWeek** |Her Perşembe için düzeltme eki yüklendi. |
| **MaintenanceWindowStartingHour** |Güncelleştirmeler 11:00:00:00 ile başlatılır. |
| **MaintenanceWindowsDuration** |Düzeltme eklerinin 120 dakika içinde yüklü olması gerekir. Başlangıç zamanına bağlı olarak, 1:00pm ile tamamlanmaları gerekir. |
| **PatchCategory** |Bu parametre için olası tek ayar **önemlidir**. Bu, önemli olarak işaretlenmiş Windows Update 'i yüklüyor; Bu kategoriye dahil olmayan SQL Server güncelleştirmelerinin hiçbirini yüklemez. |

SQL Server IaaS aracısının yüklenmesi ve yapılandırılması birkaç dakika sürebilir.

Otomatik Düzeltme Eki uygulamayı devre dışı bırakmak için, **New-Azvmsqlserverotomatikpatchingconfig**için **-Enable** parametresi olmadan aynı betiği çalıştırın. **-Enable** parametresinin yokluğu, özelliği devre dışı bırakma komutuna işaret eder.

## <a name="next-steps"></a>Sonraki adımlar
Diğer kullanılabilir otomasyon görevleri hakkında daha fazla bilgi için bkz. [IaaS Aracısı uzantısı SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Azure VM 'lerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](virtual-machines-windows-sql-server-iaas-overview.md).

