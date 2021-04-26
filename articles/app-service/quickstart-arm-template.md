---
title: Azure Resource Manager şablonu kullanarak App Service uygulaması oluşturma
description: App Service dağıtmanın birçok yolu olan bir Azure Resource Manager şablonu (ARM şablonu) kullanarak saniyeler içinde Azure App Service ilk uygulamanızı oluşturun.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-arm-template-uiex
ms.openlocfilehash: 8b048127c8a5581a34a62cfb67c19ba7aa50f2d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701595"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak App Service uygulaması oluşturma

Azure Resource Manager şablonu (ARM şablonu) ve Cloud Shell [Azure CLI](/cli/azure/get-started-with-azure-cli) kullanarak buluta uygulama dağıtarak [Azure App Service](overview.md) kullanmaya başlayın. Ücretsiz bir App Service katmanı kullandığınız için, bu hızlı başlangıcı tamamlamaya yönelik bir ücret ödeirsiniz.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

**Linux**'ta dağıtmak için aşağıdaki düğmeyi kullanın:

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)

**Windows**'a dağıtmak için aşağıdaki düğmeyi kullanın:

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Şablonu gözden geçirme

::: zone pivot="platform-windows"
Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows) alınmıştır. Bir App Service planını ve bir App Service uygulamasını Windows üzerinde dağıtır. .NET Core, .NET Framework, PHP, Node.js ve statik HTML uygulamalarıyla uyumludur. Java için bkz. [Java uygulaması oluşturma](./quickstart-java.md).

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmıştır:

* [**Microsoft. Web/sunucugrupları**](/azure/templates/microsoft.web/serverfarms): App Service planı oluşturun.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): App Service uygulaması oluşturun.

Bu şablon, kolaylık olması için önceden tanımlanmış birkaç parametre içerir. Parametre Varsayılanları ve bunların açıklamaları için aşağıdaki tabloya bakın:

| Parametreler | Tür    | Varsayılan değer                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Uygulama adı |
| location   | string  | "[[resourceGroup (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Uygulama bölgesi |
| isteyin        | string  | F1                         | Örnek boyutu (F1 = ücretsiz katman) |
| language   | string  | .net                       | Programlama dili yığını (.net, php, Node, HTML) |
| helloWorld | boolean | Yanlış                        | Doğru = "Merhaba Dünya" uygulamasını dağıt |
| repoUrl    | string  | " "                          | Dış git deposu (isteğe bağlı) |
::: zone-end
::: zone pivot="platform-linux"
Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux) alınmıştır. Linux üzerinde bir App Service planı ve bir App Service uygulaması dağıtır. App Service tüm desteklenen programlama dilleri ile uyumludur.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmıştır:

* [**Microsoft. Web/sunucugrupları**](/azure/templates/microsoft.web/serverfarms): App Service planı oluşturun.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): App Service uygulaması oluşturun.

Bu şablon, kolaylık olması için önceden tanımlanmış birkaç parametre içerir. Parametre Varsayılanları ve bunların açıklamaları için aşağıdaki tabloya bakın:

| Parametreler | Tür    | Varsayılan değer                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Uygulama adı |
| location   | string  | "[[resourceGroup (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Uygulama bölgesi |
| isteyin        | string  | F1                         | Örnek boyutu (F1 = ücretsiz katman) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3,0        | "Programlama dil yığını &#124; sürümü" |
| repoUrl    | string  | " "                          | Dış git deposu (isteğe bağlı) |

---
::: zone-end

## <a name="deploy-the-template"></a>Şablonu dağıtma

Azure CLı, şablonu dağıtmak için burada kullanılır. Azure portal, Azure PowerShell ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-powershell.md).

Aşağıdaki kod, bir kaynak grubu, bir App Service planı ve bir Web uygulaması oluşturur. Sizin için varsayılan bir kaynak grubu, App Service planı ve konum ayarlanmış. `<app-name>`Genel olarak benzersiz bir uygulama adıyla değiştirin (geçerli karakterler `a-z` , `0-9` ve `-` ).

::: zone pivot="platform-windows"
Windows 'a bir .NET Framework uygulaması dağıtmak için aşağıdaki kodu çalıştırın.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Farklı bir dil yığınını dağıtmak için `linuxFxVersion` uygun değerlerle güncelleştirin. Örnekler aşağıda gösterilmiştir. Geçerli sürümleri göstermek için Cloud Shell aşağıdaki komutu çalıştırın: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Dil    | Örnek                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion = "DOTNETCORE&#124;3,0"                 |
| **PHP**     | linuxFxVersion = "PHP&#124;7,4"                        |
| **Node.js** | linuxFxVersion = "NODE&#124;10,15"                     |
| **Java**    | linuxFxVersion = "JAVA&#124;1,8 &#124;TOMCAT&#124;9,0" |
| **Python**  | linuxFxVersion = "PYTHON&#124;3,7"                     |
| **Ruby**    | linuxFxVersion = "RUBY&#124;2,6"                       |

---
::: zone-end

> [!NOTE]
> Daha fazla [Azure App Service şablon örneği](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)bulabilirsiniz.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

' A gidin `http://<app_name>.azurewebsites.net/` ve oluşturulduğunu doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında [kaynak grubunu silin](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yerel Git’ten dağıtım](deploy-local-git.md)

> [!div class="nextstepaction"]
> [SQL Veritabanı ile ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Postgres ile Python](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [MySQL ile PHP](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Java ile Azure SQL veritabanı 'na bağlanma](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [Özel etki alanı eşleme](app-service-web-tutorial-custom-domain.md)
