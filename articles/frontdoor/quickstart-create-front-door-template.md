---
title: 'Hızlı başlangıç: Azure Resource Manager şablonu kullanarak Azure ön kapı hizmeti oluşturma (ARM şablonu)'
description: Bu hızlı başlangıç, Azure Resource Manager şablonu (ARM şablonu) kullanarak bir Azure ön kapısının nasıl oluşturulacağını açıklar.
services: front-door
documentationcenter: ''
author: duongau
ms.author: duau
editor: ''
ms.date: 09/14/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: de8a592f6eecbb43b58a044096e8ba2e0f9b5973
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539007"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak ön kapı oluşturma

Bu hızlı başlangıçta, bir Web uç noktası için yüksek kullanılabilirlik ayarlamak üzere bir ön kapı oluşturmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı açıklanmaktadır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* Bir Web sitesinin veya Web uygulamasının IP veya FQDN 'si.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-front-door-create-basic) alınmıştır.

Bu hızlı başlangıçta, tek bir arka uçta ve tek bir varsayılan yol eşleştirmeden bir ön kapı yapılandırması oluşturacaksınız `/*` .

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

Şablonda bir Azure kaynağı tanımlanmıştır:

* [**Microsoft. Network/Frontkapaklı**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure Cloud Shell açmak için aşağıdaki kod bloğundan **deneyin** ' i seçin ve ardından Azure 'da oturum açmak için yönergeleri izleyin.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    İstemi konsolundan görene kadar bekleyin.

1. PowerShell betiğini kopyalamak için önceki kod bloğundan **Kopyala** ' yı seçin.

1. Kabuk konsol bölmesine sağ tıklayın ve ardından **Yapıştır**' ı seçin.

1. Değerleri girin.

    Şablon dağıtımı, tek bir arka uçta ön kapı oluşturur. Bu örnekte `microsoft.com` **Backenbaddress** olarak kullanılır.

    Kaynak grubu adı, **RG** eklenmiş proje adıdır.

    > [!NOTE]
    > şablonun başarıyla dağıtılması için **frontDoorName** 'in genel olarak benzersiz bir ad olması gerekir. Dağıtım başarısız olursa, 1. adımdan baştan başlayın.

    Şablonun dağıtılması birkaç dakika sürer. Tamamlandığında, çıkış şuna benzerdir:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Ön kapı Kaynak Yöneticisi şablonu PowerShell dağıtım çıkışı":::

Azure PowerShell, şablonu dağıtmak için kullanılır. Azure PowerShell ek olarak, Azure portal, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmeden **kaynak grupları** ' nı seçin.

1. Önceki bölümde oluşturduğunuz kaynak grubunu seçin. Varsayılan kaynak grubu adı, **RG** eklenmiş proje adıdır.

1. Daha önce oluşturduğunuz ön kapıyı seçin ve **ön uç ana bilgisayar** bağlantısına tıklayın. Bağlantı, sizi oluşturma sırasında tanımladığınız arka uç FQDN 'nize yönlendiren bir Web tarayıcısı açar.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Ön kapı portalına genel bakış":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Ön kapı hizmetine artık ihtiyacınız kalmadığında, kaynak grubunu silin. Bu, ön kapıyı ve ilgili tüm kaynakları kaldırır.

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir ön kapı oluşturdunuz.

Ön kapıya özel bir etki alanı eklemeyi öğrenmek için ön kapı öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Ön kapı öğreticileri](front-door-custom-domain.md)
