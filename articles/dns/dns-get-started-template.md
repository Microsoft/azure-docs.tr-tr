---
title: 'Hızlı başlangıç: Azure DNS bölgesi oluşturma ve kayıt-Azure Resource Manager şablonu (ARM şablonu)'
titleSuffix: Azure DNS
description: Azure DNS'te DNS bölgesi ve kaydı oluşturma hakkında bilgi edinin. Bu, Azure Resource Manager şablonu (ARM şablonu) kullanarak ilk DNS bölgenizi ve kaydınızı oluşturmak ve yönetmek için adım adım hızlı bir başlangıç sağlar.
services: dns
author: duongau
ms.author: duau
ms.date: 09/8/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 34e810fa8244cf3ff00e57f193624a2c1fa4d755
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539010"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Azure DNS bölgesi ve kaydı oluşturma

Bu hızlı başlangıçta, içindeki bir kayıt ile bir DNS bölgesi oluşturmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı açıklanmaktadır `A` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone) alınmıştır.

Bu hızlı başlangıçta, soneki olan benzersiz bir DNS bölgesi oluşturacaksınız `azurequickstart.org` . `A`Ikı IP adresine işaret eden bir kayıt da bölgeye yerleştirilecek.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmış:

- [**Microsoft. Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft. Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): bölgede bir kayıt oluşturmak için kullanılır `A` .

Azure Traffic Manager ile ilgili daha fazla şablon bulmak için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure Cloud Shell açmak için aşağıdaki kod bloğundan **deneyin** ' i seçin ve ardından Azure 'da oturum açmak için yönergeleri izleyin.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    İstemi konsolundan görene kadar bekleyin.

1. PowerShell betiğini kopyalamak için önceki kod bloğundan **Kopyala** ' yı seçin.

1. Kabuk konsol bölmesine sağ tıklayın ve ardından **Yapıştır**' ı seçin.

1. Değerleri girin.

    Şablon dağıtımı, `A` ıkı IP adresini işaret eden bir kayıt içeren bir bölge oluşturur. Kaynak grubu adı, **RG** eklenmiş proje adıdır.

    Şablonun dağıtılması birkaç saniye sürer. Tamamlandığında, çıkış şuna benzerdir:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS bölgesi Kaynak Yöneticisi şablonu PowerShell dağıtım çıkışı":::

Azure PowerShell, şablonu dağıtmak için kullanılır. Azure PowerShell ek olarak, Azure portal, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmeden **kaynak grupları** ' nı seçin.

1. Önceki bölümde oluşturduğunuz kaynak grubunu seçin. Varsayılan kaynak grubu adı, **RG** eklenmiş proje adıdır.

1. Kaynak grubunun burada görülen aşağıdaki kaynakları içermesi gerekir:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="DNS bölgesi dağıtımı kaynak grubu":::

1. `azurequickstart.org`Bölgesinin `A` ve değerine başvuran bir kayıtla düzgün şekilde oluşturulduğunu doğrulamak için, öğesinin SONEKINE sahip DNS bölgesini seçin `1.2.3.4` `1.2.3.5` .

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="DNS bölgesi dağıtımı":::

1. Önceki adımdan ad sunucusu adlarından birini kopyalayın.

1. Bir komut istemi açın ve aşağıdaki komutu çalıştırın:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Örnek:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Aşağıdaki ekran görüntüsüne benzer bir şey görmeniz gerekir:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS bölgesi nslookup":::

Ana bilgisayar adı `www.2lwynbseszpam.azurequickstart.org` `1.2.3.4` `1.2.3.5` , yapılandırdığınız gibi ve olarak çözümlenir. Bu sonuç, ad çözümlemenin doğru çalıştığını doğrular.

## <a name="clean-up-resources"></a>Kaynakları temizleme

DNS bölgesi ile oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu silin. Bu, DNS bölgesini ve tüm ilgili kaynakları kaldırır.

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:

- DNS bölgesi
- `A` kayıtlar

Artık ilk DNS bölgenizi ve bir ARM şablonu kullanarak kaydınızı oluşturduğunuza göre, özel bir etki alanında bir Web uygulaması için kayıtlar oluşturabilirsiniz.

> [!div class="nextstepaction"]
> [Özel etki alanında web uygulaması için DNS kayıtları oluşturma](./dns-web-sites-custom-domain.md)
