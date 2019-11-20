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
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511532"
---
Azure disk şifrelemesi, [Azure CLI](/cli/azure) ve [Azure PowerShell](/powershell/azure/new-azureps-module-az)aracılığıyla etkinleştirilebilir ve yönetilebilir. Bunu yapmak için araçları yerel olarak yüklemeli ve Azure aboneliğinize bağlamanız gerekir.

### <a name="azure-cli"></a>Azure CLI

[Azure clı 2,0](/cli/azure) , Azure kaynaklarını yönetmeye yönelik bir komut satırı aracıdır. CLı, verileri esnek bir şekilde sorgulamak, engelleyici olmayan işlemler olarak uzun süreli işlemleri desteklemek ve komut dosyasını kolay hale getirmek için tasarlanmıştır. [Azure CLI 'Yı yüklemeye](/cli/azure/install-azure-cli?view=azure-cli-latest)ilişkin adımları izleyerek yerel olarak yükleyebilirsiniz.

Azure [CLI Ile Azure hesabınızda oturum açmak](/cli/azure/authenticate-azure-cli)için [az Login](/cli/azure/reference-index?view=azure-cli-latest#az-login) komutunu kullanın.

```azurecli
az login
```

Oturum açmak için bir kiracı seçmek istiyorsanız şunu kullanın:
    
```azurecli
az login --tenant <tenant>
```

Birden çok aboneliğiniz varsa ve belirli bir tane belirtmek istiyorsanız, [az Account List](/cli/azure/account#az-account-list) komutuyla abonelik listenizi alın ve [az Account set](/cli/azure/account#az-account-set)ile belirtin.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Daha fazla bilgi için bkz. [Azure CLI ile çalışmaya başlama 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az Module](/powershell/azure/new-azureps-module-az) , Azure kaynaklarınızı yönetmek için [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) modelini kullanan bir cmdlet kümesi sağlar. Bunu tarayıcınızda [Azure Cloud Shell](/azure/cloud-shell/overview)ile kullanabilir veya [Azure PowerShell modülünü yüklemeye](/powershell/azure/install-az-ps)ilişkin yönergeleri kullanarak yerel makinenize yükleyebilirsiniz. 

Yerel olarak zaten yüklüyse, Azure disk şifrelemesini yapılandırmak için Azure PowerShell SDK sürümünün en son sürümünü kullandığınızdan emin olun. [Azure PowerShell sürümünün](https://github.com/Azure/azure-powershell/releases)en son sürümünü indirin.

[Azure hesabınızda Azure PowerShell oturum açmak](/powershell/azure/authenticate-azureps?view=azps-2.5.0)için [Connect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet 'ini kullanın.

```powershell
Connect-AzAccount
```

Birden çok aboneliğiniz varsa ve bir tane belirtmek istiyorsanız, [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet 'ini kullanarak bunları listeleyin ve ardından [set-azcontext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet 'ini kullanın:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet 'ini çalıştırmak, doğru aboneliğin seçildiğini doğrular.

Azure disk şifrelemesi cmdlet 'lerinin yüklendiğini doğrulamak için [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) cmdlet 'ini kullanın:
     
```powershell
Get-command *diskencryption*
```
Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya](/powershell/azure/get-started-azureps)başlama. 