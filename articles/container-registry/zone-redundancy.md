---
title: Yüksek kullanılabilirlik için bölge yedekli kayıt defteri
description: Azure Container Registry bölgede yedekliliği etkinleştirme hakkında bilgi edinin. Bir Azure kullanılabilirlik bölgesinde kapsayıcı kayıt defteri veya çoğaltma oluşturun. Bölge artıklığı Premium hizmet katmanının bir özelliğidir.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 8c1ab42aa505448bd81ff42eba54727b24773c60
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479024"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Dayanıklılık ve yüksek kullanılabilirlik için Azure Container Registry bölgede yedekliliği etkinleştirin

[Coğrafi çoğaltmanın](container-registry-geo-replication.md)yanı sıra, kullanılabilirlik sağlamak ve bölgesel işlemlere yönelik gecikme süresini azaltmak üzere bir veya daha fazla Azure bölgesinde kayıt defteri verilerini çoğaltan Azure Container Registry, isteğe bağlı *bölge yedekliliği* destekler. [Bölge artıklığı](../availability-zones/az-overview.md#availability-zones) , belirli bir bölgedeki bir kayıt defterine veya çoğaltma kaynağına (çoğaltmaya) dayanıklılık ve yüksek kullanılabilirlik sağlar.

Bu makalede, Azure CLı, Azure portal veya Azure Resource Manager şablonunu kullanarak, bölgesel olarak yedekli kapsayıcı kayıt defterinin veya çoğaltmanın nasıl ayarlanacağı gösterilmektedir. 

Bölge artıklığı, Premium kapsayıcı kayıt defteri hizmet katmanının bir **Önizleme** özelliğidir. Kayıt defteri hizmeti katmanları ve limitleri hakkında bilgi için bkz. [Azure Container Registry hizmet katmanları](container-registry-skus.md).

## <a name="preview-limitations"></a>Önizleme sınırlamaları

* Şu anda şu bölgelerde destekleniyor: Doğu ABD, Doğu ABD 2, Batı ABD 2, Kuzey Avrupa, Batı Avrupa, Japonya Doğu.
* Kullanılabilirlik bölgelerine bölge dönüştürmeleri Şu anda desteklenmemektedir. Bir bölgede kullanılabilirlik alanı desteğini etkinleştirmek için, kayıt defterinin istenen bölgede oluşturulması gerekir, kullanılabilirlik alanı desteği etkinleştirilmiştir veya bir çoğaltılan bölge, kullanılabilirlik alanı desteğinin etkin olması gerekir.
* Bölge artıklığı bir bölgede devre dışı bırakılamaz.
* [ACR görevleri](container-registry-tasks-overview.md) henüz kullanılabilirlik bölgelerini desteklemez.

## <a name="about-zone-redundancy"></a>Bölge artıklığı hakkında

Azure bölgesel [bölgelerini](../availability-zones/az-overview.md) kullanarak bir Azure bölgesi içinde dayanıklı ve yüksek oranda kullanılabilir bir Azure Container Registry oluşturun. Örneğin, kuruluşlar, bölge içinde yüksek kullanılabilirlik sağlarken, veri fazlalığını veya diğer uyumluluk gereksinimlerini karşılamak üzere [desteklenen Azure kaynaklarıyla](../availability-zones/az-region.md) bölge yedekli bir Azure Container Registry ayarlayabilir.

Azure Container Registry Ayrıca, aynı zamanda hizmeti birden çok bölgede çoğaltan [Coğrafi çoğaltmayı](container-registry-geo-replication.md)da destekler, böylece diğer konumlardaki kaynakları hesaplamak için yedeklilik ve konum sağlar. Bölge içindeki artıklık için kullanılabilirlik bölgelerinin birleşimi ve birden çok bölgede coğrafi çoğaltma, bir kayıt defterinin güvenilirliğini ve performansını geliştirir.

Kullanılabilirlik alanları, bir Azure bölgesi içinde benzersiz fiziksel konumlardır. Dayanıklılığı güvence altına almak için etkinleştirilmiş tüm bölgelerde en az üç ayrı alan vardır. Her bölgede, bağımsız güç, soğutma ve ağ ile donatılmış bir veya daha fazla veri merkezi bulunur. Bölge artıklığı için yapılandırıldığında, bir kayıt defteri (veya farklı bir bölgedeki kayıt defteri çoğaltması) bölgedeki tüm kullanılabilirlik bölgelerinde çoğaltılır ve bu da veri merkezi hatalarının olması durumunda kullanılabilir durumda tutulmalıdır.

## <a name="create-a-zone-redundant-registry---cli"></a>Bölgesel olarak yedekli kayıt defteri oluşturma-CLı

Bölge yedekliliği sağlamak için Azure CLı 'yı kullanmak için Azure CLı sürüm 2.17.0 veya üzeri ya da Azure Cloud Shell gerekir. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Gerekirse, kayıt defteri için bir kaynak grubu oluşturmak için [az Group Create](/cli/azure/group#az_group_create) komutunu çalıştırın.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Bölge özellikli kayıt defteri oluşturma

Premium hizmet katmanında bölge yedekli bir kayıt defteri oluşturmak için [az ACR Create](/cli/azure/acr#az_acr_create) komutunu çalıştırın. Azure Container Registry için [kullanılabilirlik bölgelerini destekleyen](../availability-zones/az-region.md) bir bölge seçin. Aşağıdaki örnekte, *eastus* bölgesinde bölge artıklığı etkinleştirilmiştir. `az acr create`Daha fazla kayıt defteri seçeneği için komut yardımına bakın.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

Komut çıkışında, `zoneRedundancy` kayıt defteri için özelliği aklınızda. Etkinleştirildiğinde, kayıt defteri bölge yedekli olur:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Bölge yedekli çoğaltma oluşturma

*Westus2* gibi Azure Container Registry [kullanılabilirlik bölgelerini destekleyen](../availability-zones/az-region.md) bir bölgede bölgesel olarak yedekli bir kayıt defteri çoğaltması oluşturmak için [az ACR Replication Create](/cli/azure/acr/replication#az_acr_replication_create) komutunu çalıştırın. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Komut çıkışında, `zoneRedundancy` çoğaltma için özelliği aklınızda. Etkinleştirildiğinde, çoğaltma bölge yedekli olur:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Bölgesel olarak yedekli kayıt defteri oluşturma-Portal

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
1. Container Registry **kaynak kapsayıcıları oluştur**' u seçin  >    >  .
1. **Temel bilgiler** sekmesinde, bir kaynak grubu seçin veya oluşturun ve benzersiz bir kayıt defteri adı girin. 
1. **Konum**' da, *Doğu ABD* gibi Azure Container Registry için bölge yedekliliği destekleyen bir bölge seçin.
1. **SKU**'da **Premium**' u seçin.
1. **Kullanılabilirlik alanları**' nda **etkin**' i seçin.
1. İsterseniz, ek kayıt defteri ayarlarını yapılandırın ve ardından **gözden geçir + oluştur**' u seçin.
1. Kayıt defteri örneğini dağıtmak için **Oluştur** ' u seçin.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Azure portal bölgede yedekliliği etkinleştir":::

Bölgesel olarak yedekli bir çoğaltma oluşturmak için:

1. Premium katman kapsayıcısı Kayıt defterinize gidin ve **çoğaltmalar**' ı seçin.
1. Görüntülenen haritada, **Batı ABD 2** gibi Azure Container Registry için bölge yedekliliği destekleyen bir bölgede yeşil altıon seçin. Veya **+ Ekle**' yi seçin.
1. **Çoğaltma oluştur** penceresinde, **konumu** onaylayın. **Kullanılabilirlik alanları**' nda, **etkin**' i seçin ve ardından **Oluştur**' u seçin.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Azure portal bölgede yedekli çoğaltmayı etkinleştirme":::

## <a name="create-a-zone-redundant-registry---template"></a>Bölgesel olarak yedekli kayıt defteri oluşturma-şablon

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Gerekirse, *eastus* gibi Azure Container Registry [kullanılabilirlik bölgelerini destekleyen](../availability-zones/az-region.md) bir bölgede kayıt defteri için bir kaynak grubu oluşturmak için [az Group Create](/cli/azure/group#az_group_create) komutunu çalıştırın. Bu bölge, şablon tarafından kayıt defteri konumunu ayarlamak için kullanılır.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Şablonu dağıtma 

Bölgesel olarak yedekli, coğrafi olarak çoğaltılan bir kayıt defteri oluşturmak için aşağıdaki Kaynak Yöneticisi şablonunu kullanabilirsiniz. Şablon varsayılan olarak kayıt defterinde ve bölgesel çoğaltmada bölge yedekliliği sunar. 

Aşağıdaki içerikleri yeni bir dosyaya kopyalayın ve gibi bir dosya adı kullanarak kaydedin `registryZone.json` .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Önceki şablon dosyasını kullanarak kayıt defterini oluşturmak için aşağıdaki [az Deployment Group Create](/cli/azure/group/deployment#az_group_deployment_create) komutunu çalıştırın. Belirtilen yerlerde şunları sağlayın:

* benzersiz bir kayıt defteri adı veya şablonu parametre olmadan dağıtın ve sizin için benzersiz bir ad oluşturur
* çoğaltma için *westus2* gibi kullanılabilirlik bölgelerini destekleyen bir konum

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Komut çıkışında, `zoneRedundancy` kayıt defteri ve çoğaltma için özelliği aklınızda. Etkinleştirildiğinde, her kaynak bölge yedekli olur:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanılabilirlik bölgelerini destekleyen bölgeler](../availability-zones/az-region.md)hakkında daha fazla bilgi edinin.
* Azure 'da [güvenilirlik](/azure/architecture/framework/resiliency/overview) için oluşturma hakkında daha fazla bilgi edinin.
