---
title: Azure Batch hesapları ile özel uç noktaları kullanma
description: Özel uç noktaları kullanarak bir Azure Batch hesabına özel olarak nasıl bağlanacağınızı öğrenin.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: d2e9d36e9e964f2e9f9a5a986fbf55d19b3069d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920012"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Azure Batch hesapları ile özel uç noktaları kullanma

Varsayılan olarak, [Azure Batch hesapları](accounts.md) ortak bir uç noktaya sahiptir ve herkese açık bir şekilde erişilebilir. Batch hizmeti, genel ağ erişimini devre dışı bırakarak özel Batch hesapları oluşturma özelliği sunar.

[Azure özel bağlantısı](../private-link/private-link-overview.md)' nı kullanarak, bir Azure Batch hesabına [özel bir uç nokta](../private-link/private-endpoint-overview.md)aracılığıyla bağlanabilirsiniz. Özel uç nokta, sanal ağınız içindeki bir alt ağda bulunan özel IP adresleri kümesidir. Daha sonra, erişimi özel IP adresleri üzerinden bir Azure Batch hesabıyla sınırlayabilirsiniz.

Özel bağlantı, kullanıcıların sanal ağ içinden veya eşlenen herhangi bir sanal ağdan bir Azure Batch hesabına erişmesini sağlar. Özel bağlantıyla eşlenen kaynaklara, VPN veya [Azure ExpressRoute](../expressroute/expressroute-introduction.md)aracılığıyla özel eşleme üzerinden şirket içinde de erişilebilir. [Otomatik veya el ile onay yöntemini](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)kullanarak, özel bağlantıyla yapılandırılmış bir Azure Batch hesabına bağlanabilirsiniz.

> [!IMPORTANT]
> Azure Batch ' de özel bağlantı desteği şu anda Almanya Orta, Almanya Kuzeydoğu, Çin Doğu, Çin Doğu 2, Çin Kuzey ve Çin Kuzey 2 dışındaki tüm bölgelerde kullanılabilir.

Bu makalede özel bir Batch hesabı oluşturma ve özel bir uç nokta kullanarak erişim adımları açıklanır.

## <a name="azure-portal"></a>Azure portalı

Azure portal kullanarak özel bir Batch hesabı oluşturmak için aşağıdaki adımları kullanın:

1. **Kaynak oluştur** bölmesinde **Batch hizmeti** ' ni seçin ve ardından **Oluştur**' u seçin.
2. **Temel bilgiler** sekmesinde abonelik, kaynak grubu, bölge ve Batch hesabı adını girip **İleri: Gelişmiş**' i seçin.
3. **Gelişmiş** sekmesinde, **genel ağ erişimini** **devre dışı** olarak ayarlayın.
4. **Ayarlar**' da **Özel uç nokta bağlantıları** ' nı seçin ve **+ Özel uç noktası**' nı seçin
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Özel uç nokta bağlantıları":::
5. **Temel bilgiler** bölmesinde, abonelik, kaynak grubu, Özel uç nokta kaynağı adı ve bölge ayrıntılarını girin veya seçin, sonra **İleri: kaynak**' ı seçin.
6. **Kaynak** bölmesinde, **kaynak türünü** **Microsoft.Batch/batchaccounts** olarak ayarlayın. Erişmek istediğiniz özel Batch hesabını seçin ve ardından **İleri: yapılandırma**' yı seçin.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Özel uç nokta oluşturma-kaynak bölmesi":::
7. **Yapılandırma** bölmesinde, bu bilgileri girin veya seçin:
   - **Sanal ağ**: Sanal ağınızı seçin.
   - **Alt ağ**: alt ağlarınızı seçin.
   - **Özel DNS bölgesi Ile tümleştirin**: **Evet**' i seçin. Özel uç noktanıza özel olarak bağlanmak için bir DNS kaydına ihtiyacınız vardır. Özel uç noktanızı özel bir DNS bölgesiyle tümleştirmenizi öneririz. Ayrıca, kendi DNS sunucularınızı kullanabilir veya sanal makinelerinizdeki konak dosyalarını kullanarak DNS kayıtları oluşturabilirsiniz.
   - **Özel DNS bölgesi**: Privatelink seç. \<region\> . batch.azure.com. Özel DNS bölgesi otomatik olarak belirlenir. Azure portal kullanarak değiştiremezsiniz.
8. **Gözden geçir + oluştur**' u seçin ve ardından Azure 'un yapılandırmanızı doğrulaması için bekleyin.
9. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

Özel uç nokta sağlandıktan sonra, Özel uç noktasını kullanarak aynı sanal ağdaki VM 'lerden Batch hesabına erişebilirsiniz.

> [!IMPORTANT]
> Özel uç noktanın sağlandığı sanal ağ dışında işlem gerçekleştirmek, Azure portalında bir "AuthorizationFailure" iletisine neden olur.

Azure portal IP adresini görüntülemek için:

1. **Tüm kaynaklar**’ı seçin.
2. Daha önce oluşturduğunuz özel uç nokta için arama yapın.
3. DNS ayarlarını ve IP adreslerini görmek için **genel bakış** sekmesini seçin.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="Özel uç nokta DNS ayarları ve IP adresleri":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

[Azure Resource Manager şablonu kullanarak bir Batch hesabı oluştururken](quick-create-template.md), aşağıda gösterildiği gibi, **Publicnetworkaccess** öğesini **devre dışı** olarak ayarlamak için şablonu değiştirin.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>DNS bölgelerini yapılandırma

Özel uç noktasını oluşturduğunuz alt ağ içinde [Özel BIR DNS bölgesi](../dns/private-dns-privatednszone.md) kullanın. Uç noktaları, her özel IP adresinin bir DNS girdisiyle eşlenecek şekilde yapılandırın.

Özel uç noktayı oluştururken, Azure 'da [Özel BIR DNS bölgesi](../dns/private-dns-privatednszone.md) ile tümleştirilebilir. Bunun yerine [özel bir etki alanı](../dns/dns-custom-domain.md)kullanmayı tercih ederseniz, Özel uç nokta için ayrılan tüm özel IP ADRESLERI için DNS kayıtları eklemek üzere onu yapılandırmanız gerekir.

## <a name="pricing"></a>Fiyatlandırma

Özel uç noktalarla ilgili maliyetler hakkında ayrıntılı bilgi için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="current-limitations-and-best-practices"></a>Geçerli sınırlamalar ve en iyi uygulamalar

Özel Batch hesabınızı oluştururken şunları göz önünde bulundurun:

- Özel uç nokta kaynaklarının, Batch hesabıyla aynı abonelikte oluşturulması gerekir.
- Özel bağlantıyı silmek için özel uç nokta kaynağını silmeniz gerekir.
- Genel ağ erişimiyle bir Batch hesabı oluşturulduktan sonra, bunu yalnızca özel erişim olarak değiştiremezsiniz.
- Özel bir uç noktayı sildiğinizde veya Batch hesabından bir bölgeyi kaldırdığınızda, özel DNS bölgesindeki DNS kayıtları otomatik olarak kaldırılmaz. Bu özel DNS bölgesine bağlı yeni bir özel uç nokta eklemeden önce DNS kayıtlarını el ile kaldırmanız gerekir. DNS kayıtlarını temizleyemezseniz, Özel uç nokta kaldırma veya bölge kaldırma işleminden sonra eklenen bölgelere veri kesintileri gibi beklenmeyen veri düzlemi sorunları oluşabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal ağlarda Batch havuzları oluşturmayı](batch-virtual-network.md)öğrenin.
- [Genel IP adresleri olmadan Batch havuzları oluşturmayı](batch-pool-no-public-ip-address.md) öğrenin
- [Belirtilen genel IP adresleriyle Batch havuzları oluşturmayı](create-pool-public-ip.md)öğrenin.
- [Azure özel bağlantısı](../private-link/private-link-overview.md)hakkında bilgi edinin.
