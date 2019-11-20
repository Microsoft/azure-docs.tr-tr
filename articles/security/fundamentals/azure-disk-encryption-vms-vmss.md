---
title: Sanal makineler ve sanal makine ölçek kümeleri için Azure disk şifrelemesi
description: Bu makalede, Azure disk şifrelemesi 'ne genel bakış sunulmaktadır
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599961"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Sanal makineler ve sanal makine ölçek kümeleri için Azure disk şifrelemesi

Azure disk şifrelemesi hem Linux hem de Windows sanal makinelerine ve sanal makine ölçek kümelerine uygulanabilir. 

## <a name="linux-virtual-machines"></a>Linux sanal makineleri

Aşağıdaki makalelerde Linux sanal makinelerini şifrelemek için rehberlik sağlanmaktadır.

### <a name="current-version-of-azure-disk-encryption"></a>Azure disk şifrelemesi 'nin geçerli sürümü

- [Linux sanal makineleri için Azure disk şifrelemesi 'ne genel bakış](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux VM 'lerinde Azure disk şifrelemesi senaryoları](../../virtual-machines/linux/disk-encryption-linux.md)
- [Azure CLı ile Linux VM oluşturma ve şifreleme](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Azure PowerShell ile Linux VM oluşturma ve şifreleme](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Azure portal bir Linux VM oluşturma ve şifreleme](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure disk şifrelemesi örnek betikleri](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure disk şifrelemesi sorunlarını giderme](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure disk şifrelemesi hakkında sık sorulan sorular](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD ile Azure disk şifrelemesi (önceki sürüm)

- [Linux sanal makineleri için Azure AD ile Azure disk şifrelemesi 'ne genel bakış](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Linux VM 'lerinde Azure AD senaryolarıyla Azure disk şifrelemesi](../../virtual-machines/linux/disk-encryption-linux.md)
- [Azure AD ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows sanal makineleri

Aşağıdaki makalelerde Windows sanal makinelerini şifrelemek için rehberlik sağlanmaktadır.

### <a name="current-version-of-azure-disk-encryption"></a>Azure disk şifrelemesi 'nin geçerli sürümü

- [Windows sanal makineleri için Azure disk şifrelemesi 'ne genel bakış](../../virtual-machines/windows/disk-encryption-overview.md)
- [Windows VM 'lerinde Azure disk şifrelemesi senaryoları](../../virtual-machines/windows/disk-encryption-windows.md)
- [Azure CLı ile Windows VM oluşturma ve şifreleme](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Azure PowerShell ile Windows VM oluşturma ve şifreleme](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Azure portal ile Windows VM oluşturma ve şifreleme](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure disk şifrelemesi örnek betikleri](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure disk şifrelemesi sorunlarını giderme](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure disk şifrelemesi hakkında sık sorulan sorular](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD ile Azure disk şifrelemesi (önceki sürüm)

- [Windows sanal makineleri için Azure AD ile Azure disk şifrelemesi 'ne genel bakış](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Windows VM 'lerinde Azure AD senaryolarıyla Azure disk şifrelemesi](../../virtual-machines/windows/disk-encryption-windows.md)
- [Azure AD ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleri

Aşağıdaki makaleler, sanal makine ölçek kümelerini şifrelemek için rehberlik sağlar.

- [Sanal Makine Ölçek Kümeleri için Azure disk şifrelemesi 'ne genel bakış](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Azure CLı kullanarak sanal makine ölçek kümelerini şifreleme](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Azure PowerShell kullanarak sanal makine ölçek kümelerini şifreleyin](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Azure Resource Manager kullanarak bir sanal makine ölçek kümelerini şifreleme](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Sanal makine ölçek kümesi uzantısı sıralaması ile Azure disk şifrelemesini kullanma](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure şifrelemeye genel bakış](encryption-overview.md)
- [Bekleme sırasında veri şifrelemesi](encryption-atrest.md)
- [Veri güvenliği ve şifreleme için en iyi uygulamalar](data-encryption-best-practices.md)
