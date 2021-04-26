---
title: Azure Resource Manager şablonları
description: Kurtarma Hizmetleri kasaları ve Azure Backup özellikleriyle kullanılacak Azure Resource Manager şablonları
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: a4c2f444cb821f7979571b9d777895a59450e7c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96309588"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Backup için Azure Resource Manager şablonları

Aşağıdaki tabloda Kurtarma Hizmetleri kasaları ve Azure Backup özellikleri ile birlikte kullanılacak Azure Resource Manager şablonlarının bağlantıları bulunur. JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz. [Microsoft. RecoveryServices kaynak türleri](/azure/templates/microsoft.recoveryservices/allversions).

| Şablon | Description |
|---|---|
|**Kurtarma Hizmetleri kasası** | |
| [Kurtarma Hizmetleri Kasası oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Kurtarma Hizmetleri kasası oluşturun. Kasa Azure Backup ve Azure Site Recovery için kullanılabilir. |
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
|**Azure dosya paylaşımlarını yedekleme** |  |
| [Azure dosya paylaşımlarını yedekleme](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-file-share) | Bu şablon, kurtarma hizmetleri kasası ve yedekleme ilkesi için uygun ayrıntıları belirterek mevcut bir Azure dosya paylaşımının korumasını yapılandırır. Bu, isteğe bağlı olarak yeni bir kurtarma hizmetleri kasası ve yedekleme ilkesi oluşturur ve dosya paylaşımının bulunduğu depolama hesabını kurtarma hizmetleri kasasına kaydeder. |
|   |   |
