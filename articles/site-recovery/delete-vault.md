---
title: Azure Site Recovery kasasını silme
description: Azure Site Recovery için yapılandırılmış bir kurtarma hizmetleri kasasının nasıl silineceğini öğrenin
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: fb1e22b0ca1da00bf2665d863b40f19fa1621771
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721303"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services kasasını silme

Bu makalede, Site Recovery için bir kurtarma hizmetleri kasasının nasıl silineceği açıklanır. Azure Backup kullanılan bir kasayı silmek için bkz. [Azure 'da bir yedekleme kasasını silme](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Başlamadan önce

Bir kasayı silebilmeniz için önce kayıtlı sunucuları ve kasadaki öğeleri kaldırmanız gerekir. Kaldırmanız gereken özellikler, dağıttığınız çoğaltma senaryolarına bağlıdır. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Azure 'da bir kasayı silme-Azure VM

1. Tüm korumalı sanal makineleri silmek için [Bu yönergeleri](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) izleyin.
2. Ardından kasayı silin.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Bir kasayı-VMware VM 'sini Azure 'a silme

1. Tüm korumalı sanal makineleri silmek için [Bu yönergeleri](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) izleyin.
2. Tüm çoğaltma ilkelerini silmek için [Bu adımları](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) izleyin.
3. [Bu adımları](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)kullanarak vCenter başvurularını silin.
4. Bir yapılandırma sunucusunun yetkisini almak için [Bu yönergeleri](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) izleyin.
5. Ardından kasayı silin.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Bir kasayı (VMM ile) Azure 'a silme

1. System Center VMM tarafından yönetilen Hyper-V VM 'lerini silmek için [aşağıdaki adımları](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) izleyin.
2. Tüm çoğaltma ilkelerinin ilişkisini kaldırın ve silin. Bunu, **System Center VMM** > **çoğaltma Ilkeleri**için **Site Recovery altyapı** > >.
3. Bağlı bir VMM sunucusunun kaydını silmek için [Bu adımları](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server) izleyin.
4. Ardından kasayı silin.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Azure 'a bir kasa-Hyper-V VM 'si silme

1. Tüm korumalı sanal makineleri silmek için [Bu adımları](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) izleyin.
2. Tüm çoğaltma ilkelerinin ilişkisini kaldırın ve silin. Bunu, **Hyper-V siteleri** > **çoğaltma Ilkeleri**için **Site Recovery altyapı** > >.
3. Hyper-V konağının kaydını silmek için [Bu yönergeleri](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) izleyin.
4. Hyper-V sitesini silin.
5. Ardından kasayı silin.


## <a name="use-powershell-to-force-delete-the-vault"></a>Kasayı silmeyi zorlamak için PowerShell 'i kullanma 

> [!Important]
> Ürünü sınıyorsanız ve veri kaybı hakkında endişe ediyorsanız, kasayı ve tüm bağımlılıklarını hızlı bir şekilde kaldırmak için silmeyi zorla silme yöntemini kullanın.
> PowerShell komutu kasanın tüm içeriğini siler ve geri **alınamaz**.

Korunan öğeler olsa bile Site Recovery kasasını silmek için şu komutları kullanın:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

[Get-Azrecoveryserviceskasasının](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)yanı sıra [-Azrecoveryserviceskasasını kaldırma](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)hakkında daha fazla bilgi edinin.
