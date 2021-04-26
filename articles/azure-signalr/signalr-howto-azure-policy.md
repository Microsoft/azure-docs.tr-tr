---
title: Azure Ilkesi kullanılarak uyumluluk
description: Azure SignalR hizmeti kaynaklarınızın uyumluluğunu denetlemek için Azure Ilkesinde yerleşik ilkeler atayın.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: c8776102602f5bdcf29139d808a6f603cc5c7473
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784582"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Azure Ilkesi kullanarak Azure SignalR hizmeti kaynaklarının uyumluluğunu denetleme

[Azure ilkesi](../governance/policy/overview.md) , Azure 'da ilke oluşturmak, atamak ve yönetmek için kullandığınız bir hizmettir. Bu ilkeler, kaynaklarınız üzerinden farklı kuralları ve eylemleri uygulatarak kaynaklarınızın kurumsal standartlarınız ve hizmet düzeyi sözleşmelerinizle uyumlu kalmasını sağlar.

Bu makalede, Azure SignalR hizmeti için yerleşik ilkeler (Önizleme) tanıtılmıştır. Yeni ve mevcut SignalR kaynaklarını uyumluluk açısından denetlemek için bu ilkeleri kullanın.

Azure Ilkesi kullanımı için herhangi bir ücret alınmaz.

## <a name="built-in-policy-definitions"></a>Yerleşik ilke tanımları

Aşağıdaki yerleşik ilke tanımları Azure SignalR hizmetine özgüdür:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>İlke tanımları atama

* [Azure Portal](../governance/policy/assign-policy-portal.md), [azure CLI](../governance/policy/assign-policy-azurecli.md), [Kaynak Yöneticisi şablonu](../governance/policy/assign-policy-template.md)veya Azure ilkesi SDK 'larını kullanarak ilke tanımları atayın.
* Bir kaynak grubuna, aboneliğe veya bir [Azure yönetim grubuna](../governance/management-groups/overview.md)ilke atamasını kapsam. SignalR ilke atamaları, kapsamdaki mevcut ve yeni SignalR kaynakları için geçerlidir.
* [İlke zorlamayı](../governance/policy/concepts/assignment-structure.md#enforcement-mode) dilediğiniz zaman etkinleştirin veya devre dışı bırakın.

> [!NOTE]
> Bir ilkeyi atadıktan veya güncelleştirdikten sonra, bu, atamanın tanımlanan kapsamdaki kaynaklara uygulanması biraz zaman alır. [İlke değerlendirme Tetikleyicileri](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)hakkında bilgi için bkz..

## <a name="review-policy-compliance"></a>İlke uyumluluğunu gözden geçirme

İlke atamalarınız tarafından oluşturulan uyumluluk bilgilerine Azure portal, Azure komut satırı araçlarını veya Azure Policy SDK 'larını kullanarak erişin. Ayrıntılar için bkz. [Azure kaynaklarının uyumluluk verilerini edinme](../governance/policy/how-to/get-compliance-data.md).

Bir kaynak uyumsuz olduğunda birçok olası neden vardır. Nedenini öğrenmek veya sorumluyu bulmak için bkz. [uyumsuzluk belirleme](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Portalda ilke uyumluluğu:

1. **Tüm hizmetler**' i seçin ve **ilke** araması yapın.
1. **Uyumluluk**' i seçin.
1. Uyumluluk durumlarını sınırlamak veya ilkeleri aramak için filtreleri kullanın
   
    [![Portalda ](./media/signalr-howto-azure-policy/azure-policy-compliance.png) ilke uyumluluğu](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Toplu uyumluluk ayrıntılarını ve olaylarını gözden geçirmek için bir ilke seçin. İsterseniz, kaynak uyumluluğu için belirli bir SignalR öğesini seçin.

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLı 'de ilke uyumluluğu

Uyumluluk verilerini almak için Azure CLı 'yi de kullanabilirsiniz. Örneğin, uygulanan Azure SignalR hizmeti ilkelerinin ilke kimliklerini almak için CLı 'daki [az Policy atama listesi](/cli/azure/policy/assignment#az_policy_assignment_list) komutunu kullanın:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Örnek çıktı:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Ardından, belirli bir kaynak grubundaki tüm kaynaklar için JSON biçimli uyumluluk durumunu döndürmek için [az Policy State List](/cli/azure/policy/state#az_policy_state_list) ' i çalıştırın:

```azurecli
az policy state list --g <resourceGroup>
```

Veya belirli bir SignalR kaynağının JSON biçimli uyumluluk durumunu döndürmek için [az Policy State List](/cli/azure/policy/state#az_policy_state_list) ' i çalıştırın:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Ilke [tanımları](../governance/policy/concepts/definition-structure.md) ve [etkileri](../governance/policy/concepts/effects.md) hakkında daha fazla bilgi edinin

* [Özel ilke tanımı](../governance/policy/tutorials/create-custom-policy-definition.md) oluşturma

* Azure 'da [idare özellikleri](../governance/index.yml) hakkında daha fazla bilgi edinin


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/