---
title: Dağıtım geçmişi silme işlemleri
description: Azure Resource Manager dağıtım geçmişinden dağıtımları otomatik olarak silme işlemini açıklar. Geçmiş 800 sınırını aşmaya yakın olduğunda dağıtımlar silinir.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: b55c022c35c43be6818bb3c551d5db85b1927ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781856"
---
# <a name="automatic-deletions-from-deployment-history"></a>Dağıtım geçmişinden otomatik silme işlemleri

Bir şablonu dağıttığınız her seferinde dağıtım hakkındaki bilgiler dağıtım geçmişine yazılır. Her kaynak grubu, dağıtım geçmişinde 800 dağıtım ile sınırlıdır.

Azure Resource Manager, sınıra yaklaştıklarında geçmişinizden dağıtımları otomatik olarak siler. Otomatik silme, geçmiş davranıştaki bir değişiklikten fazla. Daha önce, bir hatadan kaçınmak için dağıtımları dağıtım geçmişinden el ile silmeniz gerekiyordu. Bu değişiklik 6 Ağustos 2020 ' de uygulandı.

**Otomatik silme işlemleri, kaynak grubu dağıtımları için desteklenir. Şu anda, [abonelik](deploy-to-subscription.md), [Yönetim grubu](deploy-to-management-group.md)ve [kiracı](deploy-to-tenant.md) dağıtımları geçmişindeki dağıtımlar otomatik olarak silinmez.**

> [!NOTE]
> Bir dağıtımı geçmişten silmek, dağıtılan kaynakların hiçbirini etkilemez.

## <a name="when-deployments-are-deleted"></a>Dağıtımlar silindiğinde

Dağıtımlar 775 dağıtımlarını aşarsanız geçmişinizden silinir. Azure Resource Manager, geçmiş 750 tarihine kadar dağıtımları siler. En eski dağıtımlar önce her zaman silinir.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Dağıtım geçmişinden silme işlemleri":::

> [!NOTE]
> Başlangıç numarası (775) ve bitiş numarası (750) değişikliğe tabidir.
>
> Kaynak grubunuz zaten 800 sınırında ise, bir sonraki dağıtımınız hata vererek başarısız olur. Otomatik silme işlemi hemen başlatılır. Kısa bir bekleme sonrasında dağıtımınızı yeniden deneyebilirsiniz.

Dağıtıma ek olarak, [durum işlemini](template-deploy-what-if.md) çalıştırdığınızda veya bir dağıtımı doğrulamak için de silmeleri tetiklersiniz.

Bir dağıtıma geçmişle aynı adı verdiğinizde, geçmişteki yerini sıfırladınız. Dağıtım, geçmişteki en son yere gider. Ayrıca bir hatadan sonra [Bu dağıtıma geri](rollback-on-error.md) döndüğünüzde bir dağıtımın yerini sıfırlayabilirsiniz.

## <a name="remove-locks-that-block-deletions"></a>Silme işlemlerini engelleyen kilitleri kaldırma

Bir kaynak grubunda [Cannotdelete kilidi](../management/lock-resources.md) varsa, bu kaynak grubu için dağıtımlar silinemez. Dağıtım geçmişindeki otomatik silme özelliğinden yararlanmak için kilidi kaldırmanız gerekir.

Bir kilidi silmek üzere PowerShell 'i kullanmak için aşağıdaki komutları çalıştırın:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Bir kilidi silmek için Azure CLı kullanmak için aşağıdaki komutları çalıştırın:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Gerekli izinler

Silme işlemleri, şablonu dağıtan kullanıcının kimliği altında istenir. Dağıtımları silmek için kullanıcının **Microsoft. resources/dağıtımlar/Delete** eylemine erişimi olması gerekir. Kullanıcı gerekli izinlere sahip değilse, dağıtımlar geçmişten silinmez.

Geçerli Kullanıcı gerekli izinlere sahip değilse, bir sonraki dağıtımda otomatik silme işlemi yeniden denenir.

## <a name="opt-out-of-automatic-deletions"></a>Otomatik silme işlemleri devre dışı

Geçmişten otomatik silme işlemleri yapabilirsiniz. **Bu seçeneği yalnızca dağıtım geçmişini kendiniz yönetmek istediğinizde kullanın.** Geçmişteki 800 dağıtım limiti yine de zorlanır. 800 dağıtımlarını aşarsanız bir hata alırsınız ve dağıtımınız başarısız olur.

Otomatik silme işlemlerini devre dışı bırakmak için `Microsoft.Resources/DisableDeploymentGrooming` özellik bayrağını kaydedin. Özellik bayrağını kaydettiğinizde, tüm Azure aboneliği için otomatik silme işlemleri devre dışı. Yalnızca belirli bir kaynak grubunu devre dışı kaydedemezsiniz. Otomatik silme işlemlerini yeniden etkinleştirmek için özellik bayrağının kaydını kaldırın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell için [register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature)komutunu kullanın.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Aboneliğinizin geçerli durumunu görmek için şunu kullanın:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Otomatik silme işlemlerini yeniden etkinleştirmek için Azure REST API veya Azure CLı kullanın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı için [az Feature Register](/cli/azure/feature#az_feature_register)kullanın.

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Aboneliğinizin geçerli durumunu görmek için şunu kullanın:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Otomatik silme işlemlerini yeniden etkinleştirmek için [az Feature Unregister](/cli/azure/feature#az_feature_unregister)kullanın.

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

REST API için, [Özellikler-kaydet](/rest/api/resources/features/register)' i kullanın.

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Aboneliğinizin geçerli durumunu görmek için şunu kullanın:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Otomatik silme işlemlerini yeniden etkinleştirmek için, [özellikleri kullanın-kaydını kaldırın](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Sonraki adımlar

* Dağıtım geçmişini görüntüleme hakkında bilgi edinmek için bkz. [Azure Resource Manager dağıtım geçmişini görüntüleme](deployment-history.md).
