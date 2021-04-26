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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bce6bfb61eb59d1fa66c550a133ac8b6f8d7f2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769016"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak App Service uygulaması oluşturma

Şunu kullanarak bir uygulamayı buluta dağıtarak [Azure App Service](overview.md) kullanmaya başlayın <abbr title="Dağıtılan kaynaklarla ilgili bir veya daha fazla Azure kaynağını ve bağımlılıklarını bildirimli olarak tanımlayan bir JSON dosyası. Şablon, kaynakları tutarlı ve sürekli olarak dağıtmak için kullanılabilir.">ARM şablonu</abbr> Cloud Shell ' de [Azure CLI](/cli/azure/get-started-with-azure-cli) . Ücretsiz bir App Service katmanı kullandığınız için, bu hızlı başlangıcı tamamlamaya yönelik bir ücret ödeirsiniz. <abbr title="Bildirim temelli sözdiziminde, dağıtım oluşturmak için programlama komutlarının dizisini yazmadan amaçladığınız dağıtımı ifade edersiniz.">Bu şablonda, bildirim temelli sözdizimi kullanılır.</abbr>

 Ortamınız önkoşulları karşılıyorsa ve [ARM şablonlarını](../azure-resource-manager/templates/overview.md)kullanma hakkında bilginiz varsa, **Azure 'a dağıt** düğmesini seçin. Şablon Azure portalda açılır.

::: zone pivot="platform-windows"
[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. şablonu gözden geçirin

::: zone pivot="platform-windows"
Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows) alınmıştır. Bir App Service planını ve bir App Service uygulamasını Windows üzerinde dağıtır.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Şablonda hangi kaynaklar ve parametreler tanımlanmıştır?</summary>

Şablonda iki Azure kaynağı tanımlanmıştır:

* [**Microsoft. Web/sunucugrupları**](/azure/templates/microsoft.web/serverfarms): App Service planı oluşturun.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): App Service uygulaması oluşturun.

Aşağıdaki tabloda, varsayılan parametrelerin ayrıntıları ve açıklamaları verilmiştir:

| Parametreler | Tür    | Varsayılan değer                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Uygulama adı |
| location   | string  | "[[resourceGroup (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Uygulama bölgesi |
| isteyin        | string  | F1                         | Örnek boyutu (F1 = ücretsiz katman) |
| language   | string  | .net                       | Programlama dili yığını (.net, php, Node, HTML) |
| helloWorld | boolean | Yanlış                        | Doğru = "Merhaba Dünya" uygulamasını dağıt |
| repoUrl    | string  | " "                          | Dış git deposu (isteğe bağlı) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux) alınmıştır. Bir App Service planını ve bir App Service uygulamasını Windows üzerinde dağıtır.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Bu şablon, size kolaylık sağlaması için tanımlanan Azure kaynaklarını ve parametrelerini içerir.

<details>
<summary>Şablonda hangi kaynaklar ve parametreler tanımlanmıştır?</summary>

Şablonda iki Azure kaynağı tanımlanmıştır:

* [**Microsoft. Web/sunucugrupları**](/azure/templates/microsoft.web/serverfarms): App Service planı oluşturun.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): App Service uygulaması oluşturun.

Aşağıdaki tabloda, varsayılan parametrelerin ayrıntıları ve açıklamaları verilmiştir:

| Parametreler | Tür    | Varsayılan değer                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Uygulama adı |
| location   | string  | "[[resourceGroup (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Uygulama bölgesi |
| isteyin        | string  | F1                         | Örnek boyutu (F1 = ücretsiz katman) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3,0        | "Programlama dil yığını &#124; sürümü" |
| repoUrl    | string  | " "                          | Dış git deposu (isteğe bağlı) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. şablonu dağıtma

::: zone pivot="platform-windows"
Azure CLı kullanarak Windows 'a bir .NET Framework uygulaması dağıtmak için aşağıdaki kodu çalıştırın. 

Değiştir <abbr title="Geçerli karakter karakterleri `a-z` , `0-9` , ve `-` .">`<app-name>`</abbr> Genel olarak benzersiz bir uygulama adıyla. Diğerini öğrenmek için <abbr title="Azure portal, Azure PowerShell ve REST API de kullanabilirsiniz.">dağıtım yöntemleri</abbr>bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-powershell.md). Daha fazla [Azure App Service şablon örneği](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)bulabilirsiniz.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Linux üzerinde bir Python uygulaması oluşturmak için aşağıdaki kodu çalıştırın. 

Değiştir <abbr title="Geçerli karakter karakterleri `a-z` , `0-9` , ve `-` .">`<app-name>`</abbr> Genel olarak benzersiz bir uygulama adıyla.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Kod ne yapıyor?</summary>
<p>Komutlar aşağıdaki eylemleri yapılır:</p>
<ul>
<li>Varsayılan oluşturma <abbr title="Birim olarak yönetebileceğiniz ilgili Azure kaynakları için mantıksal bir kapsayıcı.">kaynak grubu</abbr>.</li>
<li>Varsayılan oluşturma <abbr title="Uygulamanızı barındıran Web sunucusu grubunun konumunu, boyutunu ve özelliklerini belirten plan.">App Service planı</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Oluşturma <abbr title="Uygulama kodunuzu, DNS ana bilgisayar adlarını, sertifikalarınızı ve ilgili kaynaklarınızı içeren Web uygulamanızın temsili. "> App Service uygulaması</abbr></a> Belirtilen ada sahip.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Farklı bir dil yığını Nasıl yaparım? dağıtmak istiyor musunuz?</summary>
Farklı bir dil yığını dağıtmak için, güncelleştirme <abbr title="Bu şablon .NET Core, .NET Framework, PHP, Node.js ve statik HTML uygulamalarıyla uyumludur. "> dil parametresi</abbr> uygun değerleri vardır. Java için bkz. <a href="/azure/app-service/quickstart-java-uiex">Java uygulaması oluşturma</a>.

| Parametreler | Tür    | Varsayılan değer                | Description |
|------------|---------|------------------------------|-------------|
| language   | string  | .net                       | Programlama dili yığını (.net, php, Node, HTML) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Farklı bir dil yığını Nasıl yaparım? dağıtmak istiyor musunuz?</summary>
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

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. dağıtımı doğrulama

' A gidin `http://<app_name>.azurewebsites.net/` ve oluşturulduğunu doğrulayın.

<hr/>

## <a name="5-clean-up-resources"></a>5. Kaynakları Temizleme

Artık gerekli olmadığında [kaynak grubunu silin](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

<hr/>

## <a name="next-steps"></a>Sonraki adımlar

- [Yerel Git’ten dağıtım](deploy-local-git.md)
- [SQL Veritabanı ile ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)
- [Postgres ile Python](tutorial-python-postgresql-app.md)
- [MySQL ile PHP](tutorial-php-mysql-app.md)
- [Java ile Azure SQL veritabanı 'na bağlanma](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Özel etki alanı eşleme](app-service-web-tutorial-custom-domain-uiex.md)
