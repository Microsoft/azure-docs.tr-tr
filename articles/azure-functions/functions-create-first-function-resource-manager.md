---
title: Azure Resource Manager şablonları kullanarak ilk işlevinizi oluşturma
description: Azure Resource Manager şablonu (ARM şablonu) kullanarak basit bir HTTP ile tetiklenen sunucusuz bir işlev oluşturun ve Azure 'a dağıtın.
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 0badc233597c13228e9826ed062cc15828c65cf2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833391"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Hızlı başlangıç: ARM şablonundan Azure Işlevleri kaynaklarını oluşturma ve dağıtma

Bu makalede, HTTP isteklerine yanıt veren bir işlev oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız. 

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

### <a name="azure-account"></a>Azure hesabı 

Başlamadan önce, etkin aboneliği olan bir Azure hesabınızın olması gerekir. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

### <a name="create-a-local-functions-project"></a>Yerel işlevler projesi oluşturma

Bu makale, oluşturduğunuz Azure kaynaklarında çalıştırmak için yerel işlevler kod projesi gerektirir. İlk olarak yayımlanacak bir proje oluşturmazsanız, bu makalenin dağıtım bölümünü tamamlayamazsınız. 

Aşağıdaki sekmelerden birini seçin, bağlantıyı izleyin ve tercih ettiğiniz dilde bir işlev uygulaması oluşturmak için bölümü tamamlayın:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code ' de seçtiğiniz dilde yerel işlevler projenizi oluşturun:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Visual Studio 'da yerel işlevler projenizi oluşturma](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Komut satırı](#tab/command-line)

Komut satırından seçtiğiniz dilde yerel işlevler projenizi oluşturun:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

Projenizi yerel olarak oluşturduktan sonra, yeni işlevinizi Azure 'da çalıştırmak için gereken kaynakları oluşturursunuz. 

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Aşağıdaki dört Azure kaynağı bu şablon tarafından oluşturulmuştur:

+ [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): işlevler için gerekli olan bir Azure depolama hesabı oluşturun.
+ [**Microsoft. Web/sunucugrupları**](/azure/templates/microsoft.web/serverfarms): işlev uygulaması için sunucusuz bir tüketim barındırma planı oluşturun.
+ [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): işlev uygulaması oluşturma.
+ [**Microsoft. Insights/bileşenler**](/azure/templates/microsoft.insights/components): izleme için Application Insights bir örnek oluşturun.

## <a name="deploy-the-template"></a>Şablonu dağıtma

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Daha sonra, projenizi Azure 'da yayımlayarak ve işlevin HTTP uç noktasını çağırarak oluşturduğunuz kaynak barındıran işlev uygulamasını doğrularsınız.

### <a name="publish-the-function-project-to-azure"></a>İşlev projesini Azure 'da yayımlayın

Projenizi yeni Azure kaynaklarında yayımlamak için aşağıdaki adımları kullanın:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

Çıktıda, HTTP tetikleyicisinin URL 'sini kopyalayın. Bu işlemi, Azure 'da çalışan işlevinizi test etmek için kullanırsınız. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Çözüm Gezgini**'nde projeye sağ tıklayın ve **Yayımla**'yı seçin.

1. **Bir yayımlama hedefi** seçin bölümünde, **mevcut Seç** ' ın bulunduğu **Azure işlevleri tüketim planı** ' nı seçin ve **Profil oluştur**' u

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Mevcut bir yayımlama hedefi seçin":::

1. **Aboneliğinizi** seçin, kaynak grubunu genişletin, işlev uygulamanızı seçin ve **Tamam**' ı seçin.

1. Yayımlama işlemi tamamlandıktan sonra, **site URL 'sini** kopyalayın.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Yayımlama özetinden site URL 'sini kopyalayın":::

1. Yolu ekleyin `/api/<FUNCTION_NAME>?name=Functions` , burada `<FUNCTION_NAME>` işlevinizin adıdır. HTTP tetikleyici işlevinizi çağıran URL aşağıdaki biçimdedir:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Azure 'da çalışan HTTP tetikleme işlevinizi test etmek için bu URL 'YI kullanırsınız.

# <a name="command-line"></a>[Komut satırı](#tab/command-line)

Yerel kodunuzu Azure 'da bir işlev uygulamasına yayımlamak için şu `publish` komutu kullanın:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

Bu örnekte, öğesini `<FUNCTION_APP_NAME>` işlev uygulamanızın adıyla değiştirin. Kullanarak yeniden oturum açmanız gerekebilir `az login` . 

Çıktıda, HTTP tetikleyicisinin URL 'sini kopyalayın. Bu işlemi, Azure 'da çalışan işlevinizi test etmek için kullanırsınız.

---

### <a name="invoke-the-function-on-azure"></a>Azure 'da işlevi çağırma

HTTP isteği için kopyaladığınız URL 'YI tarayıcınızın adres çubuğuna yapıştırın, `name` sorgu dizesinin `?name=Functions` Bu URL 'nin sonuna eklenmiş olduğundan emin olun ve sonra isteği yürütün. 

Şöyle bir yanıt görmeniz gerekir:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki adımla devam ederseniz ve bir Azure depolama kuyruğu çıktı bağlaması eklerseniz, daha önce yapmış olduğunuz yerde oluşturacağınız sürece tüm kaynaklarınızı saklayın.

Aksi takdirde, daha fazla maliyet ödemeden kaçınmak için kaynak grubunu ve içerdiği tüm kaynakları silmek için aşağıdaki komutu kullanın.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

`<RESOURCE_GROUP_NAME>`Kaynak grubunuzun adıyla değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

İlk işlevinizi yayımladığınıza göre, işlevlerinize bir çıkış bağlaması ekleyerek daha fazla bilgi edinin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Komut satırı](#tab/command-line)

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-cli.md)

---
