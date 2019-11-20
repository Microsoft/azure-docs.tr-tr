---
title: 'Azure ExpressRoute şablonu: ExpressRoute bağlantı hattı oluşturma'
description: ExpressRoute bağlantı hattı oluşturma, sağlama, silme ve sağlamayı kaldırma.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 25ed38e72f5a21622a87e36ad811ffd66f6a4c90
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083522"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak bir ExpressRoute devresi oluşturma

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager şablonu](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-circuit-classic.md)
>

Azure PowerShell kullanarak Azure Resource Manager şablonu dağıtarak bir ExpressRoute devresi oluşturmayı öğrenin. Kaynak Yöneticisi şablonları geliştirme hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi belgeleri](/azure/azure-resource-manager/) ve [şablon başvurusu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Başlamadan önce

* Gözden geçirme [önkoşulları](expressroute-prerequisites.md) ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.
* Yeni ağ kaynakları oluşturma izni olduğundan emin olun. Doğru izinlere sahip değilsiniz, hesap yöneticinize başvurun.
* Yapabilecekleriniz [video görüntüleme](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) adımları daha iyi anlamak için başlamadan önce.

## <a name="create"></a>Oluşturma ve bir ExpressRoute bağlantı hattı sağlama

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

   * **SKU katmanı** , bir ExpressRoute devresinin [Yerel](expressroute-faqs.md#expressroute-local), standart veya [Premium](expressroute-faqs.md#expressroute-premium)olup olmadığını belirler. *Yerel*, *Standart* veya *Premium*belirtebilirsiniz.
   * **SKU ailesi** Faturalandırma türünü belirler. Belirtebileceğiniz *Metereddata* ölçülen veri planı için ve *Unlimiteddata* sınırsız veri planı için. Fatura türünden değiştirebilirsiniz *Metereddata* için *Unlimiteddata*, ancak türünden değiştiremezsiniz *Unlimiteddata* için *Metereddata*. *Yerel* devre yalnızca *limiteddata* ' dır.
   * **Eşdüzey hizmet sağlama konumu** nerede sizin eşlemeyi Microsoft ile fiziksel konumu.

     > [!IMPORTANT]
     > Eşleme konumu gösteren [fiziksel konum](expressroute-locations.md) nerede sizin eşlemeyi Microsoft ile. Bu **değil** başvuran Azure ağ kaynak sağlayıcısı bulunduğu coğrafi konum "Location" özelliğine bağlı. Bunlar ilişkili değildir, ancak bir ağ kaynağı sağlayıcı eşleme konumu bağlantı hattının coğrafi olarak yakın seçmek için iyi bir uygulamadır.

    Kaynak grubu adı, **RG** eklenmiş hizmet veri yolu ad alanı adıdır.

2. Seçin **kopyalama** PowerShell betiğini kopyalanacak.
3. Kabuk konsoluna sağ tıklayın ve ardından **Yapıştır**' ı seçin.

Bir olay hub 'ı oluşturmak birkaç dakika sürer.

Azure PowerShell, bu öğreticide şablonu dağıtmak için kullanılır. Diğer şablon dağıtım yöntemleri için bkz.:

* [Azure Portal kullanarak](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Azure CLI kullanarak](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [REST API kullanarak](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>Sağlama kaldırmayı ve bir ExpressRoute bağlantı hattı siliniyor

ExpressRoute devreniz seçerek silebilirsiniz **Sil** simgesi. Aşağıdaki bilgileri not edin:

* ExpressRoute bağlantı hattınızdaki tüm sanal ağların bağlantısını kaldırmanız gerekir. Bu işlem başarısız olursa, herhangi bir sanal ağa bağlantı hattına bağlı olup olmadığını denetleyin.
* ExpressRoute bağlantı hattı Hizmet Sağlayıcısı sağlama durumu ise **sağlama** veya **sağlanan** kendi tarafında bağlantı hattını sağlamasını kaldırmak için hizmet sağlayıcınızla birlikte çalışmanız gerekir. Kaynak ayırmanıza ve hizmeti sağlayıcısı devreyi sağlamayı kaldırma tamamlandıktan ve bize bildiren kadar faturalandırılırsınız devam ediyoruz.
* Hizmet sağlayıcısı devreyi sağlamayı durdurduğunda varsa (Hizmet Sağlayıcısı sağlama durumu ayarlamak **sağlanmadı**), bağlantı hattının silebilirsiniz. Bu durumda bağlantı hattının faturalandırılması durdurulur.

Aşağıdaki PowerShell komutunu çalıştırarak ExpressRoute devrenizi silebilirsiniz:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Sonraki adımlar

Bağlantı hattınızı oluşturduktan sonra sonraki adımlara devam edin:

* [ExpressRoute bağlantı hattı için yönlendirme oluşturma ve değiştirme](expressroute-howto-routing-portal-resource-manager.md)
* [Sanal ağınız, ExpressRoute devresine bağlama](expressroute-howto-linkvnet-arm.md)
