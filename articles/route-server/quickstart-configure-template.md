---
title: 'Hızlı başlangıç: Azure Resource Manager şablonu kullanarak Azure rota sunucusu oluşturma (ARM şablonu)'
description: Bu hızlı başlangıçta, Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure rota sunucusu oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388049"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure rota sunucusu oluşturma

Bu hızlı başlangıçta, bir Azure Route sunucusunu yeni veya mevcut bir sanal ağa dağıtmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı açıklanmaktadır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-route-server) alınmıştır.

Bu hızlı başlangıçta, yeni veya mevcut bir sanal ağa bir Azure Route sunucusu dağıtırsınız. Yol sunucusunu barındırmak için adlı bir ayrılmış alt ağ `RouteServerSubnet` oluşturulur. BGP eşlemesi oluşturmak için yol sunucusu eş ASN ve eş IP 'si ile de yapılandırılır.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Şablonda birden çok Azure kaynağı tanımlanmış:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft. Network/virtualNetworks/alt ağlar**](/azure/templates/microsoft.network/virtualNetworks/subnets) (iki alt ağ, bir adlandırılmış `routeserversubnet` )
* [**Microsoft. Network/Virtualhub 'ları**](/azure/templates/microsoft.network/virtualhubs) (Route Server dağıtımı)
* [**Microsoft. Network/Virtualhub 'Ları/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft. Network/Virtualhub 'ları/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (eş ASN ve eş IP yapılandırması)


ExpressRoute ile ilgili daha fazla şablon bulmak için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure Cloud Shell açmak için aşağıdaki kod bloğundan **deneyin** ' i seçin ve ardından Azure 'da oturum açmak için yönergeleri izleyin.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Yönlendirme sunucusu Kaynak Yöneticisi şablonu PowerShell dağıtım çıkışı.":::

Azure PowerShell, şablonu dağıtmak için kullanılır. Azure PowerShell ek olarak, Azure portal, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmeden **kaynak grupları** ' nı seçin.

1. Önceki bölümde oluşturduğunuz kaynak grubunu seçin. Varsayılan kaynak grubu adı, **RG** eklenmiş proje adıdır.

1. Kaynak grubu yalnızca sanal ağı içermelidir:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Sanal ağ ile yönlendirme sunucusu dağıtım kaynak grubu.":::

1. https://aka.ms/routeserver öğesine gidin.

1. Dağıtımın başarılı olduğunu doğrulamak için **routeserver** adlı rota sunucusunu seçin.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Yönlendirme sunucusuna Genel Bakış sayfasının ekran görüntüsü.":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yönlendirme sunucusuyla oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu silin. Bu, yol sunucusunu ve ilgili tüm kaynakları kaldırır.

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:

* Rota sunucusu
* Sanal Ağ
* Alt ağ

Azure Route sunucusu oluşturduktan sonra Azure Route sunucusunun ExpressRoute ve VPN ağ geçitleri ile nasıl etkileşime gireceğini öğrenin: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute ve Azure VPN desteği](expressroute-vpn-support.md)
