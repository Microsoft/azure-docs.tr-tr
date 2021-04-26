---
title: Azure PowerShell ile bir Azure özel kaynak sağlayıcısı oluşturma
description: Azure PowerShell ile bir Azure özel kaynak sağlayıcısı oluşturmayı açıklar
author: MSEvanhi
ms.author: evanhi
ms.date: 09/22/2020
ms.topic: quickstart
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c19eb41210b6fba1935a0d158c8240375f4f8f5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533924"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell bir Azure özel kaynak sağlayıcısı oluşturma

Bu hızlı başlangıçta, [az. CustomProviders](/powershell/module/az.customproviders) PowerShell modülünü kullanarak kendi Azure özel kaynak sağlayıcınızı oluşturmayı öğreneceksiniz.

> [!CAUTION]
> Azure özel sağlayıcılar Şu anda genel önizlemededir. Bu önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır. Üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Gereksinimler

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps). Cloud Shell kullanmayı seçerseniz, daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](../../cloud-shell/overview.md) .

> [!IMPORTANT]
> **Az. CustomProviders** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli bir abonelik seçin.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir [Azure Kaynak grubu](../../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek, belirtilen ada ve belirtilen konuma sahip bir kaynak grubu oluşturur.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Özel bir kaynak sağlayıcısı oluşturma

Özel bir kaynak sağlayıcısı oluşturmak veya güncelleştirmek için, aşağıdaki örnekte gösterildiği gibi [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) cmdlet 'ini kullanırsınız.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Özel kaynak sağlayıcısı bildirimini al

Özel kaynak sağlayıcısı bildirimi hakkında bilgi almak için aşağıdaki örnekte gösterildiği gibi [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) cmdlet 'ini kullanırsınız.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>İlişki oluşturma

Bir ilişki oluşturmak veya güncelleştirmek için, aşağıdaki örnekte gösterildiği gibi [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) cmdlet 'ini kullanırsınız.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>İlişki alın

Bir ilişkilendirme hakkında bilgi almak için aşağıdaki örnekte gösterildiği gibi [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) cmdlet 'ini kullanırsınız.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynaklar gerekmiyorsa, aşağıdaki örnekleri çalıştırarak bunları silebilirsiniz.

### <a name="delete-an-association"></a>Bir ilişkilendirmeyi silme

Bir ilişkilendirmeyi kaldırmak için [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) cmdlet 'ini kullanırsınız. Aşağıdaki örnek bir ilişkilendirmeyi siler.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Özel bir kaynak sağlayıcısını silme

Özel bir kaynak sağlayıcısını kaldırmak için [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) cmdlet 'ini kullanırsınız. Aşağıdaki örnek bir özel kaynak sağlayıcısını siler.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Kaynak grubunu silme

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu makalenin kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure özel kaynak sağlayıcıları](overview.md)hakkında daha fazla bilgi edinin.
