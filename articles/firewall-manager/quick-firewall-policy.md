---
title: 'Hızlı başlangıç: Azure Güvenlik Duvarı ve güvenlik duvarı ilkesi oluşturma-Kaynak Yöneticisi şablonu'
description: Bu hızlı başlangıçta bir Azure Güvenlik Duvarı ve bir güvenlik duvarı ilkesi dağıtırsınız.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 43853d9e0b955167905af4777d533114a1d1f2ba
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529863"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Hızlı başlangıç: Azure Güvenlik Duvarı ve güvenlik duvarı ilkesi oluşturma-ARM şablonu

Bu hızlı başlangıçta, bir Azure Güvenlik Duvarı ve bir güvenlik duvarı ilkesi oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız. Güvenlik duvarı ilkesinde, bağlantılara `www.microsoft.com` ve **WindowsUpdate** FQDN etiketi kullanılarak Windows Update bağlantılara izin veren bir kuralla izin veren bir uygulama kuralı vardır. Ağ kuralı, 13.86.101.172 adresinde bir zaman sunucusuna UDP bağlantılarına izin verir.

Ayrıca, IP grupları, **kaynak** IP adreslerini tanımlamak için kurallar içinde kullanılır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Güvenlik Duvarı Yöneticisi hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Yöneticisi nedir?](overview.md).

Azure Güvenlik Duvarı hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı nedir?](../firewall/overview.md).

IP grupları hakkında daha fazla bilgi için bkz. [Azure Güvenlik duvarında IP grupları](../firewall/ip-groups.md).

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu şablon, senaryoyu desteklemek için gerekli kaynaklarla birlikte bir hub sanal ağı oluşturur.

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Şablonda birden çok Azure kaynağı tanımlanmış:

- [**Microsoft. Network/ıpgroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/Publicıpaddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Şablonu dağıtma

ARM şablonunu Azure 'a dağıtma:

1. Azure 'da oturum açmak için **Azure 'A dağıt** ' ı seçin ve şablonu açın. Şablon bir Azure Güvenlik Duvarı, sanal bir WAN ve sanal hub, ağ altyapısı ve iki sanal makine oluşturur.

   [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. Portalda, **kurallar ve ıpgroups Ile güvenlik duvarı ve FirewallPolicy oluştur** sayfasında aşağıdaki değerleri yazın veya seçin:
   - Abonelik: mevcut abonelikler arasından seçim yapın.
   - Kaynak grubu: mevcut kaynak gruplarından seçim yapın veya **Yeni oluştur**' u seçin ve **Tamam**' ı seçin.
   - Bölge: bir bölge seçin.
   - Güvenlik duvarı adı: güvenlik duvarı için bir ad yazın.

3. **Gözden Geçir + oluştur**’u ve sonra da **Oluştur**’u seçin. Dağıtımın tamamlanması 10 dakika veya daha uzun sürebilir.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtım tamamlandıktan sonra, aşağıdaki benzer kaynakları görürsünüz.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Dağıtılan kaynaklar":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik duvarıyla oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu silin. Bu, güvenlik duvarını ve tüm ilgili kaynakları kaldırır.

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Güvenlik Duvarı Yöneticisi ilkesine genel bakış](policy-overview.md)
