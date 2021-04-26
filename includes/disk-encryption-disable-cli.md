---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 37571a82ca73342b8edfc4702686ccd9091887c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771517"
---
Azure PowerShell, Azure CLı veya Kaynak Yöneticisi şablonuyla şifrelemeyi devre dışı bırakabilirsiniz. 

>[!IMPORTANT]
>Linux VM 'lerde Azure disk şifrelemesi ile şifrelemeyi devre dışı bırakmak yalnızca veri birimlerinde desteklenir. İşletim sistemi birimi şifrelendiyse veri veya işletim sistemi birimlerinde desteklenmez.  

- **Azure PowerShell ile disk şifrelemeyi devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [Disable-Azvmdiskencryptıon](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet 'ini kullanın. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Azure CLI ile şifrelemeyi devre dışı bırakın:** Şifrelemeyi devre dışı bırakmak için [az VM ENCRYPTION Disable](/cli/azure/vm/encryption#az_vm_encryption_disable) komutunu kullanın. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Kaynak Yöneticisi şablonuyla şifrelemeyi devre dışı bırak:** Şifrelemeyi devre dışı bırakmak için [çalışan bir LINUX VM şablonunda şifrelemeyi devre dışı bırak '](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) i kullanın.
     1. **Azure’a dağıt**’a tıklayın.
     2. Abonelik, kaynak grubu, konum, VM, yasal koşullar ve Sözleşme ' yi seçin.
