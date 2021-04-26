---
title: Azure Resource Manager şablonu kullanarak bir Azure uygulama yapılandırma deposu oluşturma (ARM şablonu)
titleSuffix: Azure App Configuration
description: Azure Resource Manager şablonu (ARM şablonu) kullanarak bir Azure uygulama yapılandırma deposu oluşturmayı öğrenin.
author: GrantMeStrength
ms.author: jken
ms.date: 10/16/2020
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 92ca80a6c807394c45be8f0187c7add736ba83ce
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831789"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure uygulama yapılandırma deposu oluşturma

Bu hızlı başlangıçta nasıl yapılacağı açıklanmaktadır:

- Azure Resource Manager şablonu kullanarak bir uygulama yapılandırma deposu dağıtın (ARM şablonu).
- ARM şablonunu kullanarak bir uygulama yapılandırma deposunda anahtar değerleri oluşturma.
- ARM şablonundan uygulama yapılandırma deposundaki anahtar değerlerini okuyun.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/) alınmıştır. İçinde iki anahtar değeri olan yeni bir uygulama yapılandırma deposu oluşturur. Ardından, `reference` iki anahtar-değer kaynağı değerlerini çıkarmak için işlevini kullanır. Anahtarın değerini bu şekilde okumak, şablonun şablondaki diğer yerlerde kullanılmasına izin verir.

Hızlı başlangıç, `copy` anahtar-değer kaynağının birden çok örneğini oluşturmak için öğesini kullanır. Öğesi hakkında daha fazla bilgi edinmek için `copy` bkz. [ARM şablonlarındaki kaynak yinelemesi](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> Bu şablon, uygulama yapılandırma kaynak sağlayıcısı sürümü `2020-07-01-preview` veya üzerini gerektirir. Bu sürüm, `reference` anahtar değerlerini okumak için işlevini kullanır. `listKeyValue`Önceki sürümdeki anahtar değerlerini okumak için kullanılan işlev sürümden itibaren kullanılamıyor `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmıştır:

- [Microsoft. AppConfiguration/Configurationmağazaların](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores): bir uygulama yapılandırma deposu oluşturun.
- [Microsoft. appconfiguration/Configurationmağazaları/keyValues](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues): uygulama yapılandırma deposu içinde bir anahtar değeri oluşturun.

> [!TIP]
> `keyValues`Kaynağın adı, anahtar ve etiketin bir birleşimidir. Anahtar ve etiket `$` sınırlayıcıyla birleştirilir. Etiket isteğe bağlıdır. Yukarıdaki örnekte, `keyValues` adı olan kaynak, `myKey` etiket olmadan bir anahtar değeri oluşturur.
>
> URL kodlaması olarak da bilinen yüzde Encoding, anahtar veya etiketlerin ARM şablon kaynak adlarında izin verilmeyen karakterler içermesini sağlar. `%` , izin verilen bir karakter değil, `~` onun yerinde kullanılır. Bir adı doğru bir şekilde kodlamak için aşağıdaki adımları izleyin:
>
> 1. URL kodlaması Uygula
> 2. Değiştir `~``~7E`
> 3. Değiştir `%``~`
>
> Örneğin, anahtar adı ve etiket adı ile bir anahtar-değer çifti oluşturmak için `AppName:DbEndpoint` `Test` , kaynak adı olmalıdır `AppName~3ADbEndpoint$Test` .

> [!NOTE]
> Uygulama yapılandırması, sanal ağınızdan [özel bir bağlantı](concept-private-endpoint.md) üzerinden anahtar değer veri erişimine izin verir. Varsayılan olarak, özellik etkinleştirildiğinde, genel ağ üzerinden uygulama yapılandırma verilerinize yönelik tüm istekler reddedilir. ARM şablonu sanal ağınızın dışında çalıştığından, ARM şablonundan veri erişimine izin verilmez. Özel bir bağlantı kullanıldığında ARM şablonundan veri erişimine izin vermek için aşağıdaki Azure CLı komutunu kullanarak ortak ağ erişimini etkinleştirebilirsiniz. Bu senaryoda ortak ağ erişimini etkinleştirmenin güvenlik etkilerine yönelik etkileri göz önünde bulundurmanız önemlidir.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>Şablonu dağıtma

Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon, içinde iki anahtar değeri olan bir uygulama yapılandırma deposu oluşturur.

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

Ayrıca, aşağıdaki PowerShell cmdlet 'ini kullanarak şablonu dağıtabilirsiniz. Anahtar değerleri PowerShell konsolunun çıkışında yer alacak.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure portal arama kutusuna **uygulama yapılandırması** yazın. Listeden **uygulama yapılandırması** ' nı seçin.
1. Yeni oluşturulan uygulama yapılandırma kaynağını seçin.
1. **İşlemler** altında **yapılandırma Gezgini**' ne tıklayın.
1. İki anahtar değerinin mevcut olduğunu doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, uygulama yapılandırma deposunu ve tüm ilgili kaynakları silin. Uygulama yapılandırma deposunu gelecekte kullanmayı planlıyorsanız, silme işlemini atlayabilirsiniz. Bu depoyu kullanmaya devam edemeyecekecekseniz, aşağıdaki cmdlet 'i çalıştırarak bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silin:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Sonraki adımlar

Uygulama yapılandırma deposuna Özellik bayrağı ve Key Vault başvurusu ekleme hakkında bilgi edinmek için ARM şablonu örneklerinin altına bakın.

- [101-uygulama-yapılandırma-mağaza-FF](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-ff)
- [101-App-Configuration-Store-keyvaultref](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-keyvaultref)
