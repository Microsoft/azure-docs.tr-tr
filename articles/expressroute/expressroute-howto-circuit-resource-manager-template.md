---
title: 'Azure ExpressRoute şablonu: ExpressRoute bağlantı hattı oluşturma'
description: Azure PowerShell kullanarak bir Azure Resource Manager şablonu dağıtarak Azure ExpressRoute devresi oluşturmayı öğrenin.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 2e9b6ddc9da4467590946af12a47f1473a4ea494
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202064"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak bir ExpressRoute devresi oluşturma

> [!div class="op_single_selector"]
> * [Azure portalı](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager şablonu](expressroute-howto-circuit-resource-manager-template.md)
> * [Video-Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-circuit-classic.md)
>

Azure PowerShell kullanarak Azure Resource Manager şablonu dağıtarak bir ExpressRoute devresi oluşturmayı öğrenin. Kaynak Yöneticisi şablonları geliştirme hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi belgeleri](../azure-resource-manager/index.yml) ve [şablon başvurusu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Başlamadan önce

* Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md) ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.
* Yeni ağ kaynakları oluşturmak için izinleriniz olduğundan emin olun. Doğru izinleriniz yoksa, hesap yöneticinize başvurun.
* Adımları daha iyi anlamak için başlamadan önce [bir video görüntüleyebilirsiniz](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) .

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute bağlantı hattı oluşturma ve sağlama

[Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/) Kaynak Yöneticisi şablonu için iyi bir koleksiyon içerir. ExpressRoute bağlantı hattı oluşturmak için [mevcut şablonlardan](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) birini kullanırsınız.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Daha fazla ilgili şablon görmek için [burada](https://azure.microsoft.com/resources/templates/?term=expressroute)öğesini seçin.

Bir şablonu dağıtarak bir ExpressRoute devresi oluşturmak için:

1. Aşağıdaki kod bloğundan **deneyin** ' i seçin ve ardından Azure Cloud Shell 'de oturum açmak için yönergeleri izleyin.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **SKU katmanı** , bir ExpressRoute devresinin [Yerel](expressroute-faqs.md#expressroute-local), standart veya [Premium](expressroute-faqs.md#expressroute-premium)olduğunu belirler. *Yerel*, * standart veya *Premium* belirtebilirsiniz. SKU 'YU *Standart/Premium* 'dan *Yerel* olarak değiştiremezsiniz.
   * **SKU ailesi** Faturalandırma türünü belirler. Tarifeli veri planı için *Metereddata* , sınırsız bir veri planı Için de *unlimiteddata* belirtebilirsiniz. Faturalandırma türünü *Metereddata* 'Den *unlimiteddata* olarak değiştirebilirsiniz, ancak türü *Unlimiteddata* iken *Metereddata* olarak değiştiremezsiniz. *Yerel* devre yalnızca *limiteddata* ' dır.
   * **Eşleme konumu** , Microsoft ile eşolduğunuz fiziksel konumdur.

     > [!IMPORTANT]
     > Eşleme konumu, Microsoft ile eşolduğunuz [fiziksel konumu](expressroute-locations.md) gösterir. Bu, Azure ağ kaynak sağlayıcısı 'nın bulunduğu coğrafi konuma başvuran "location" özelliği ile bağlantılı **değildir** . İlgili olmadıkları sürece, bir ağ kaynağı sağlayıcısını coğrafi olarak devrenin eşleme konumuna yakın bir şekilde tercih eden bir uygulamadır.

    Kaynak grubu adı, **RG** eklenmiş hizmet veri yolu ad alanı adıdır.

2. PowerShell betiğini kopyalamak için **Kopyala** ' yı seçin.
3. Kabuk konsoluna sağ tıklayın ve ardından **Yapıştır**' ı seçin.

Bir olay hub 'ı oluşturmak birkaç dakika sürer.

Azure PowerShell, bu öğreticide şablonu dağıtmak için kullanılır. Diğer şablon dağıtım yöntemleri için bkz.:

* [Azure Portal kullanarak](../azure-resource-manager/templates/deploy-portal.md).
* [Azure CLI kullanarak](../azure-resource-manager/templates/deploy-cli.md).
* [REST API kullanarak](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Bir ExpressRoute bağlantı hattının sağlamasını kaldırma ve silme

ExpressRoute devrenizi **Sil** simgesini seçerek silebilirsiniz. Aşağıdaki bilgileri not edin:

* ExpressRoute bağlantı hattınızdaki tüm sanal ağların bağlantısını kaldırmanız gerekir. Bu işlem başarısız olursa, herhangi bir sanal ağın bağlantı hattına bağlanıp bağlanmadığını denetleyin.
* ExpressRoute bağlantı hattı hizmeti sağlayıcısı sağlama **durumu sağlandıysa veya** sağlanmışsa  , devre dışı bırakmak için hizmet sağlayıcınızla birlikte çalışmanız gerekir. Hizmet sağlayıcısı, devre sağlamasını kaldırmayı ve bize haber verene kadar, kaynakları ayırmaya ve sizi faturalandırmaya devam ediyoruz.
* Hizmet sağlayıcı devre sağlamasını kaldırmışsa (hizmet sağlayıcı sağlama durumu **sağlanmadı** olarak ayarlandıysa), devresini silebilirsiniz. Bu durumda bağlantı hattının faturalandırılması durdurulur.

Aşağıdaki PowerShell komutunu çalıştırarak ExpressRoute devrenizi silebilirsiniz:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Sonraki adımlar

Devrenizi oluşturduktan sonra aşağıdaki adımlarla devam edin:

* [ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme](expressroute-howto-routing-portal-resource-manager.md)
* [Sanal ağınızı ExpressRoute devrenizi ile bağlama](expressroute-howto-linkvnet-arm.md)