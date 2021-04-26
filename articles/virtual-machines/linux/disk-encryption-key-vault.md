---
title: Azure Disk Şifrelemesi için anahtar kasası oluşturma ve yapılandırma
description: Bu makalede bir Linux sanal makinesinde Azure disk şifrelemesi ile kullanmak üzere bir Anahtar Kasası oluşturma ve yapılandırma adımları sağlanmaktadır.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 29f6f01ece0536d93ca18ca54379757adc120308
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818791"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Şifrelemesi için anahtar kasası oluşturma ve yapılandırma

Azure disk şifrelemesi, disk şifreleme anahtarlarını ve gizli dizileri denetlemek ve yönetmek için Azure Key Vault kullanır.  Anahtar kasaları hakkında daha fazla bilgi için bkz. [Azure Key Vault kullanmaya başlama](../../key-vault/general/overview.md) ve [anahtar kasanızın güvenliğini sağlama](../../key-vault/general/security-features.md). 

> [!WARNING]
> - Bir VM 'yi şifrelemek için Azure AD ile Azure disk şifrelemesi 'ni daha önce kullandıysanız, VM 'nizi şifrelemek için bu seçeneği kullanmaya devam etmeniz gerekir. Ayrıntılar için Azure [ad (önceki sürüm) Ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault-aad.md) konusuna bakın.

Azure disk şifrelemesi ile kullanım için bir Anahtar Kasası oluşturmak ve yapılandırmak üç adımdan oluşur:

1. Gerekirse bir kaynak grubu oluşturma.
2. Anahtar Kasası oluşturma. 
3. Anahtar Kasası Gelişmiş erişim ilkeleri ayarlanıyor.

Bu adımlar aşağıdaki hızlı başlangıçlarda gösterilmiştir:

- [Azure CLI ile Linux VM oluşturma ve şifreleme](disk-encryption-cli-quickstart.md)
- [Azure PowerShell ile Linux VM oluşturma ve şifreleme](disk-encryption-powershell-quickstart.md)

Ayrıca, anahtar şifreleme anahtarı (KEK) oluşturabilir veya içeri aktarabilirsiniz.

> [!Note]
> Bu makaledeki adımlar, [Azure disk şifrelemesi ÖNKOŞULLARı CLI betiği](https://github.com/ejarvi/ade-cli-getting-started) ve [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)içinde otomatikleştirilir.

## <a name="install-tools-and-connect-to-azure"></a>Araçları yükleyip Azure 'a bağlanın

Bu makaledeki adımlar [Azure CLI](/cli/azure/), [Azure PowerShell Az Module](/powershell/azure/)veya [Azure Portal](https://portal.azure.com)ile tamamlanabilir. 

Portal, tarayıcınız aracılığıyla erişilebilir olsa da, Azure CLı ve Azure PowerShell yerel yükleme gerektirir; bkz. [Linux Için Azure disk şifrelemesi: Ayrıntılar için araçları yükler](disk-encryption-linux.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma

Azure CLı veya Azure PowerShell kullanmadan önce, önce Azure aboneliğinize bağlanmanız gerekir. [Azure CLI Ile oturum](/cli/azure/authenticate-azure-cli)açarak, [Azure PowerShell ile oturum](/powershell/azure/authenticate-azureps)açarak veya istendiğinde kimlik bilgilerinizi Azure Portal sağlayarak bunu yapabilirsiniz.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Sonraki adımlar

- [Azure disk şifrelemesi önkoşulları CLı betiği](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Linux VM 'Lerinde Azure disk şifrelemesi senaryoları](disk-encryption-linux.md) öğrenin
- [Azure disk şifrelemesi sorunlarını giderme](disk-encryption-troubleshooting.md) hakkında bilgi edinin
- [Azure disk şifrelemesi örnek betiklerini](disk-encryption-sample-scripts.md) okuyun
