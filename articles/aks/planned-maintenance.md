---
title: Azure Kubernetes Service (AKS) kümeniz için planlı bakım kullanma (Önizleme)
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service 'te (AKS) planlı bakımın nasıl kullanılacağını öğrenin.
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f1e0822e77d8466b1b9796041fbdba53c3f9c91f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782918"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Azure Kubernetes Service (AKS) kümeniz için bakım pencerelerini zamanlamak üzere planlı bakım kullanma (Önizleme)

AKS kümenizde düzenli bakım otomatik olarak gerçekleştirilir. Bu iş, varsayılan olarak herhangi bir zamanda gerçekleşebilir. Planlı bakım, bir VMSS örneğindeki kuas sistem yığınlarınızı ve iş yükü etkisini en aza indirmenize olanak tanıyan haftalık bakım pencerelerini zamanlamanıza izin verir. Zamanlandıktan sonra, seçtiğiniz pencere sırasında tüm bakım gerçekleşmeyecektir. Belirli bir gün için gün veya saat aralığı belirterek, kümenizde bir veya daha fazla haftalık pencere zamanlayabilirsiniz. Bakım pencereleri Azure CLı kullanılarak yapılandırılır.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Sınırlamalar

Planlı bakım kullanırken aşağıdaki kısıtlamalar geçerlidir:

- AKS, bu pencereleri acil veya kritik olan plansız/reaktif bakım işlemleri için kesme hakkını saklı tutar.
- Şu anda, bakım işlemlerinin gerçekleştirilmesi *yalnızca en iyi çaba* olarak değerlendirilir ve belirtilen bir pencerede oluşma garantisi yoktur.
- Güncelleştirmeler yedi günden daha uzun bir süre için engellenemez.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI uzantısını yükleme

Ayrıca, *aks-Preview* Azure CLI uzantısı sürüm 0.5.4 veya sonraki bir sürüme de ihtiyacınız vardır. [Az Extension Add][az-extension-add] komutunu kullanarak *aks-Preview* Azure CLI uzantısını yükler. Veya [az Extension Update][az-extension-update] komutunu kullanarak kullanılabilir tüm güncelleştirmeleri yükler.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Her Pazartesi günü 1:00:00'da 2:00:00:00 olarak bakım yapmasına izin ver

Bir bakım penceresi eklemek için `az aks maintenanceconfiguration add` komutunu kullanabilirsiniz.

> [!IMPORTANT]
> Planlı bakım pencereleri Eşgüdümlü Evrensel Saat 'te (UTC) belirtilir.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

Aşağıdaki örnek çıktıda bakım penceresi 1:00:00-2:00:00:00 her Pazartesi gösterilmektedir.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Bir gün içinde herhangi bir zamanda bakım sağlamak için *Başlangıç saati* parametresini atlayın. Örneğin, aşağıdaki komut, her Pazartesi için tüm gün bakım penceresini ayarlar:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>JSON dosyası ile bakım yapılandırması ekleme

Ayrıca, parametreleri kullanmak yerine bir JSON dosyası kullanarak bakım penceresi oluşturabilirsiniz. `test.json`Aşağıdaki içeriğe sahip bir dosya oluşturun:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

Yukarıdaki JSON dosyası bakım pencerelerini her Salı günü 1:00:00:00-3:00:00:00 ve 1:00:00:00-2:00:00:00 ve 6:00:00:00-7:00:00:00. Ayrıca, *2021-05-26T03:00:00Z* ile *2021-05-30T12:00:00Z* arasında bir özel durum vardır. Bu, bakım penceresiyle örtüştüğü halde bakım yapılmasına izin verilmez. Aşağıdaki komut, bakım pencerelerini ' dan ekler `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Mevcut bir bakım penceresini güncelleştirme

Var olan bir bakım yapılandırmasını güncelleştirmek için komutunu kullanın `az aks maintenanceconfiguration update` .

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Mevcut bir kümedeki tüm bakım pencerelerini listeleme

AKS kümenizdeki tüm geçerli bakım yapılandırma pencerelerini görmek için `az aks maintenanceconfiguration list` komutunu kullanın.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Aşağıdaki çıktıda, myAKSCluster için yapılandırılmış iki bakım penceresi olduğunu görebilirsiniz. Tek bir pencere 1:00:00:00 ve diğer bir pencere ise Cuma günü 4:00:00:00 üzerinde.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>AKS kümesinde belirli bir bakım Yapılandırması penceresi gösterme

AKS kümenizdeki belirli bir bakım yapılandırma penceresini görmek için `az aks maintenanceconfiguration show` komutunu kullanın.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

Aşağıdaki örnek çıktıda *varsayılan* olarak bakım penceresi gösterilmektedir:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Mevcut bir AKS kümesindeki belirli bir bakım yapılandırma penceresini silme

AKS kümenizdeki belirli bir bakım yapılandırma penceresini silmek için `az aks maintenanceconfiguration delete` komutunu kullanın.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Sonraki adımlar

- AKS kümenizi yükseltmeye başlamak için bkz. [AKS kümesini yükseltme][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
