---
title: Azure Resource Manager şablonlarıyla bir sanal makine ölçek kümesi oluşturma ve şifreleme
description: Bu hızlı başlangıçta, bir sanal makine ölçek kümesi oluşturmak ve şifrelemek için Azure Resource Manager şablonlarını kullanmayı öğreneceksiniz
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530003"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Sanal makine ölçek kümelerini Azure Resource Manager ile şifreleyin

Azure Resource Manager şablonları kullanarak Linux sanal makine ölçek kümelerini şifreleyebilir veya şifrelerini çözebilirsiniz.

## <a name="deploying-templates"></a>Şablonları dağıtma

İlk olarak, senaryonuza uygun olan şablonu seçin.

- [Çalışan bir Linux sanal makine ölçek kümesi üzerinde disk şifrelemeyi etkinleştir](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Çalışan bir Windows sanal makine ölçek kümesi üzerinde disk şifrelemeyi etkinleştir](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Bir sıçrama kutusu ile Linux VM 'leri sanal makine ölçek kümesi dağıtma ve Linux sanal makine ölçek kümelerinde şifrelemeyi mümkün](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Bir sıçrama kutusu ile bir sanal makine ölçek kümesi dağıtın ve Windows sanal makine ölçek kümelerinde şifrelemeyi etkinleştirilir](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Çalışan bir Linux sanal makine ölçek kümesi üzerinde disk şifrelemesini devre dışı bırak](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Çalışan bir Windows sanal makine ölçek kümesi üzerinde disk şifrelemesini devre dışı bırak](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Ardından şu adımları izleyin:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal Makine Ölçek Kümeleri için Azure disk şifrelemesi](disk-encryption-overview.md)
- [Azure CLı kullanarak sanal makine ölçek kümelerini şifreleme](disk-encryption-cli.md)
- [Azure PowerShell kullanarak bir sanal makine ölçek kümelerini şifreleme](disk-encryption-powershell.md)
- [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md)
- [Sanal makine ölçek kümesi uzantısı sıralaması ile Azure disk şifrelemesini kullanma](disk-encryption-extension-sequencing.md)