---
title: 'Hızlı başlangıç: Azure Resource Manager şablonu kullanarak bir ExpressRoute devresi oluşturma (ARM şablonu)'
description: Bu hızlı başlangıçta, Azure Resource Manager şablonu (ARM şablonu) kullanarak bir ExpressRoute bağlantı hattı oluşturma gösterilmektedir.
services: expressroute
author: duongau
ms.author: duau
manager: kumud
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 3dc1d5f5ec3dfb004468deb2bec80927c7ec189d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529889"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak özel eşleme ile bir ExpressRoute devresi oluşturma

Bu hızlı başlangıçta, özel eşleme ile bir ExpressRoute bağlantı hattı oluşturmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı açıklanmaktadır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet) alınmıştır.

Bu hızlı başlangıçta, hizmet sağlayıcısı olarak *Equinx* Ile bir ExpressRoute devresi oluşturacaksınız. Devre, *50 Mbps* bant genişliğine sahip BIR *Premium SKU* ve *Washington DC*'nin eşleme konumunu kullanacaktır. Özel eşleme, sırasıyla *192.168.10.16/30* ve *192.168.10.20/30* birincil ve ikincil alt ağıyla etkinleştirilecek. Ayrıca, bir sanal ağ, *HighPerformance ExpressRoute ağ geçidiyle* birlikte oluşturulur.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

Şablonda birden çok Azure kaynağı tanımlanmış:

* [**Microsoft. Network/Expressroutedevreleri**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft. Network/Expressroutedevreleri/peerler**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (devre üzerinde özel eşlemeyi etkinleştirmek için kullanılır)
* [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (ağ güvenlik grubu, sanal ağ içindeki alt ağlara uygulanır)
* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft. Network/Publicıpaddresses**](/azure/templates/microsoft.network/publicIPAddresses) (genel IP, ExpressRoute ağ geçidi tarafından kullanılır)
* [**Microsoft. Network/Virtualnetworkgateway**](/azure/templates/microsoft.network/virtualNetworkGateways) (ExpressRoute ağ geçidi, VNET 'i devreye bağlamak için kullanılır)

ExpressRoute ile ilgili daha fazla şablon bulmak için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure Cloud Shell açmak için aşağıdaki kod bloğundan **deneyin** ' i seçin ve ardından Azure 'da oturum açmak için yönergeleri izleyin.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    İstemi konsolundan görene kadar bekleyin.

1. PowerShell betiğini kopyalamak için önceki kod bloğundan **Kopyala** ' yı seçin.

1. Kabuk konsol bölmesine sağ tıklayın ve ardından **Yapıştır**' ı seçin.

1. Değerleri girin.

    Kaynak grubu adı, **RG** eklenmiş proje adıdır.

    Şablonun dağıtılması yaklaşık 20 dakika sürer. Tamamlandığında, çıkış şuna benzerdir:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute Kaynak Yöneticisi şablonu PowerShell dağıtım çıkışı":::

Azure PowerShell, şablonu dağıtmak için kullanılır. Azure PowerShell ek olarak, Azure portal, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmeden **kaynak grupları** ' nı seçin.

1. Önceki bölümde oluşturduğunuz kaynak grubunu seçin. Varsayılan kaynak grubu adı, **RG** eklenmiş proje adıdır.

1. Kaynak grubunun burada görülen aşağıdaki kaynakları içermesi gerekir:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute dağıtım kaynak grubu":::

1. Devre durumunun **etkin** olduğunu doğrulamak Için ExpressRoute bağlantı hattı **-CK01** ' ı seçin, sağlayıcı durumu **sağlanmadı** ve özel eşleme **sağlanan** durumuna sahip.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute dağıtım devresi":::

> [!NOTE]
> Sanal ağı devre 'ya bağlayabilmeniz için önce sağlayıcıyı çağırmanız gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

ExpressRoute devresi ile oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu silin. Bu, ExpressRoute devresini ve ilgili tüm kaynakları kaldırır.

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:

* ExpressRoute bağlantı hattı
* Sanal Ağ
* VPN Gateway
* Genel IP
* ağ güvenlik grupları

Bir sanal ağı bir devresine bağlamayı öğrenmek için ExpressRoute öğreticilerine geçin.

> [!div class="nextstepaction"]
> [ExpressRoute öğreticileri](expressroute-howto-linkvnet-portal-resource-manager.md)
