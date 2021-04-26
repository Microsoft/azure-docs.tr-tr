---
title: Azure Ilkesi kullanılarak uyumluluk
description: Azure Container kayıt defterlerinden uyumluluğu denetlemek için Azure Ilkesinde yerleşik ilkeler atama
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 62a1fd8d3c996fd3a0bac3cadf77fc7e7ace0ce3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784182"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Azure Ilkesi kullanarak Azure Container Registry 'nin uyumluluğunu denetleme

[Azure ilkesi](../governance/policy/overview.md) , Azure 'da ilke oluşturmak, atamak ve yönetmek için kullandığınız bir hizmettir. Bu ilkeler, kaynaklarınız üzerinden farklı kuralları ve eylemleri uygulatarak kaynaklarınızın kurumsal standartlarınız ve hizmet düzeyi sözleşmelerinizle uyumlu kalmasını sağlar.

Bu makalede Azure Container Registry için yerleşik ilkeler tanıtılmıştır. Yeni ve mevcut kayıt defterlerinin uyumluluğunu denetlemek için bu ilkeleri kullanın.

Azure Ilkesi kullanımı için herhangi bir ücret alınmaz.

## <a name="built-in-policy-definitions"></a>Yerleşik ilke tanımları

Aşağıdaki yerleşik ilke tanımları Azure Container Registry özgüdür:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>İlke atama

* [Azure Portal](../governance/policy/assign-policy-portal.md), [azure CLI](../governance/policy/assign-policy-azurecli.md), [Kaynak Yöneticisi şablonu](../governance/policy/assign-policy-template.md)veya Azure ilkesi SDK 'larını kullanarak ilke atayın.
* Bir kaynak grubuna, aboneliğe veya bir [Azure yönetim grubuna](../governance/management-groups/overview.md)ilke atamasını kapsam. Kapsayıcı kayıt defteri ilke atamaları, kapsamdaki mevcut ve yeni kapsayıcı kayıt defterleri için geçerlidir.
* [İlke zorlamayı](../governance/policy/concepts/assignment-structure.md#enforcement-mode) dilediğiniz zaman etkinleştirin veya devre dışı bırakın.

> [!NOTE]
> Bir ilkeyi atadıktan veya güncelleştirdikten sonra, bu, atamanın tanımlanan kapsamdaki kaynaklara uygulanması biraz zaman alır. [İlke değerlendirme Tetikleyicileri](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)hakkında bilgi için bkz..

## <a name="review-policy-compliance"></a>İlke uyumluluğunu gözden geçirme

İlke atamalarınız tarafından oluşturulan uyumluluk bilgilerine Azure portal, Azure komut satırı araçlarını veya Azure Policy SDK 'larını kullanarak erişin. Ayrıntılar için bkz. [Azure kaynaklarının uyumluluk verilerini edinme](../governance/policy/how-to/get-compliance-data.md).

Bir kaynak uyumsuz olduğunda birçok olası neden vardır. Nedenini öğrenmek veya sorumluyu bulmak için bkz. [uyumsuzluk belirleme](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Portalda ilke uyumluluğu:

1. **Tüm hizmetler**' i seçin ve **ilke** araması yapın.
1. **Uyumluluk**' i seçin.
1. Uyumluluk durumlarını sınırlamak veya ilkeleri aramak için filtreleri kullanın.

    ![Portalda ilke uyumluluğu](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Toplu uyumluluk ayrıntılarını ve olaylarını gözden geçirmek için bir ilke seçin. İsterseniz, kaynak uyumluluğu için belirli bir kayıt defteri seçin.

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLı 'de ilke uyumluluğu

Uyumluluk verilerini almak için Azure CLı 'yi de kullanabilirsiniz. Örneğin, uygulanan Azure Container Registry ilkelerinin ilke kimliklerini almak için CLı 'daki [az Policy atama listesi](/cli/azure/policy/assignment#az_policy_assignment_list) komutunu kullanın:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Örnek çıktı:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Ardından, belirli bir ilke KIMLIĞI altındaki tüm kaynaklar için JSON biçimli uyumluluk durumunu döndürmek üzere [az Policy State List](/cli/azure/policy/state#az_policy_state_list) öğesini çalıştırın:

```azurecli
az policy state list \
  --resource <policyID>
```

Veya, *myregistry* gibi belirli bir kayıt DEFTERI kaynağının JSON biçimli uyumluluk durumunu döndürmek için [az Policy State List](/cli/azure/policy/state#az_policy_state_list) ' i çalıştırın:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Ilke [tanımları](../governance/policy/concepts/definition-structure.md) ve [etkileri](../governance/policy/concepts/effects.md)hakkında daha fazla bilgi edinin.

* Özel bir [ilke tanımı](../governance/policy/tutorials/create-custom-policy-definition.md)oluşturun.

* Azure 'da [idare özellikleri](../governance/index.yml) hakkında daha fazla bilgi edinin.
