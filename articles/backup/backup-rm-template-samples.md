---
title: Azure Resource Manager şablonları
description: Kurtarma Hizmetleri kasaları ve Azure Backup özellikleriyle kullanılacak Azure Resource Manager şablonları
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 3c05f345c6c5df5d0350c75e52381c24b0609501
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172175"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Backup için Azure Resource Manager şablonları

Aşağıdaki tabloda Kurtarma Hizmetleri kasaları ve Azure Backup özellikleri ile birlikte kullanılacak Azure Resource Manager şablonlarının bağlantıları bulunur. JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz. [Microsoft. RecoveryServices kaynak türleri](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Kurtarma Hizmetleri kasası** | |
| [Kurtarma Hizmetleri kasası oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Kurtarma Hizmetleri kasası oluşturun. Kasa Azure Backup ve Azure Site Recovery için kullanılabilir. |
|**Sanal makineleri yedekleme**| |
| [Kaynak Yöneticisi VM’lerini yedekleme](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Aynı kaynak grubundaki Kaynak Yöneticisi sanal makinelerini yedeklemek için kaynak var olan Kurtarma Hizmetleri kasasını ve Yedekleme ilkesini kullanın.|
| [IaaS sanal makinelerini Kurtarma Hizmetleri kasasına yedekleme](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Klasik ve Kaynak Yöneticisi sanal makinelerini yedekleme şablonu. |
| [IaaS sanal makineleri için Haftalık Yedekleme ilkesi oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Şablon, kurtarma hizmetleri kasası ve klasik ve Kaynak Yöneticisi sanal makinelerini yedeklemek için kullanılan bir haftalık yedekleme ilkesi oluşturur.|
| [IaaS sanal makineleri için Günlük Yedekleme ilkesi oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Şablon, kurtarma hizmetleri kasası ve klasik ve Kaynak Yöneticisi sanal makinelerini yedeklemek için kullanılan günlük yedekleme ilkesi oluşturur.|
| [Yedekleme özelliği etkin Windows Server VM dağıtma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Şablon, varsayılan yedekleme ilkesi etkinken bir Windows Server VM ve Kurtarma Hizmetleri kasası oluşturur.|
|**Yedekleme işlerini izleme** |  |
| [Azure Izleyici günlüklerini Azure Backup ile kullanma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Şablon, Azure Izleyici günlüklerini, yedekleme ve geri yükleme işlerini, yedekleme uyarılarını ve kurtarma hizmetleri kasalarınızda kullanılan bulut depolama alanını izlemenize olanak tanıyan Azure Backup ile dağıtır.|  
|**Azure VM 'de yedekleme SQL Server** |  |
| [Azure VM 'de yedekleme SQL Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Şablon, bir kurtarma hizmetleri kasası ve Iş yüküne özgü yedekleme Ilkesi oluşturur. VM 'yi Azure Backup hizmetine kaydeder ve bu VM 'de korumayı yapılandırır. Şu anda yalnızca SQL galeri görüntüleri için geçerlidir. |
|   |   |
