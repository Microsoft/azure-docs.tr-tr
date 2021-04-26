---
title: include dosyası
description: include dosyası
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: a550087f552238820ecff079ff1dc2523c4172d3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776801"
---
Azure disk şifrelemesi, [Azure CLI](/cli/azure) ve [Azure PowerShell](/powershell/azure/new-azureps-module-az)aracılığıyla etkinleştirilebilir ve yönetilebilir. Bunu yapmak için araçları yerel olarak yüklemeli ve Azure aboneliğinize bağlamanız gerekir.

### <a name="azure-cli"></a>Azure CLI

[Azure clı 2,0](/cli/azure) , Azure kaynaklarını yönetmeye yönelik bir komut satırı aracıdır. CLı, verileri esnek bir şekilde sorgulamak, engelleyici olmayan işlemler olarak uzun süreli işlemleri desteklemek ve komut dosyasını kolay hale getirmek için tasarlanmıştır. [Azure CLI 'Yı yüklemeye](/cli/azure/install-azure-cli)ilişkin adımları izleyerek yerel olarak yükleyebilirsiniz.

Azure [CLI Ile Azure hesabınızda oturum açmak](/cli/azure/authenticate-azure-cli)için [az Login](/cli/azure/reference-index#az_login) komutunu kullanın.

```azurecli
az login
```

Oturum açmak için bir kiracı seçmek istiyorsanız şunu kullanın:
    
```azurecli
az login --tenant <tenant>
```

Birden çok aboneliğiniz varsa ve belirli bir tane belirtmek istiyorsanız, [az Account List](/cli/azure/account#az_account_list) komutuyla abonelik listenizi alın ve [az Account set](/cli/azure/account#az_account_set)ile belirtin.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Daha fazla bilgi için bkz. [Azure CLI ile çalışmaya başlama 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az Module](/powershell/azure/new-azureps-module-az) , Azure kaynaklarınızı yönetmek için [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) modelini kullanan bir cmdlet kümesi sağlar. Bunu tarayıcınızda [Azure Cloud Shell](../articles/cloud-shell/overview.md)ile kullanabilir veya [Azure PowerShell modülünü yüklemeye](/powershell/azure/install-az-ps)ilişkin yönergeleri kullanarak yerel makinenize yükleyebilirsiniz. 

Yerel olarak zaten yüklüyse, Azure disk şifrelemesini yapılandırmak için Azure PowerShell SDK sürümünün en son sürümünü kullandığınızdan emin olun. [Azure PowerShell sürümünün](https://github.com/Azure/azure-powershell/releases)en son sürümünü indirin.

[Azure hesabınızda Azure PowerShell oturum açmak](/powershell/azure/authenticate-azureps)için [Connect-azaccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'ini kullanın.

```powershell
Connect-AzAccount
```

Birden çok aboneliğiniz varsa ve bir tane belirtmek istiyorsanız, [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet 'ini kullanarak bunları listeleyin ve ardından [set-azcontext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanın:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet 'ini çalıştırmak, doğru aboneliğin seçildiğini doğrular.

Azure disk şifrelemesi cmdlet 'lerinin yüklendiğini doğrulamak için [Get-Command](/powershell/module/microsoft.powershell.core/get-command) cmdlet 'ini kullanın:
     
```powershell
Get-command *diskencryption*
```
Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya](/powershell/azure/get-started-azureps)başlama.