---
title: Hızlı başlangıç-Azure Resource Manager şablonları kullanarak mantıksal uygulama iş akışı oluşturma ve dağıtma
description: Azure Resource Manager şablonları kullanarak mantıksal uygulama oluşturma ve dağıtma
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.date: 04/01/2021
ms.openlocfilehash: a177cf48c479f2e2afb0d6d23b1f4695ed57cfe4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875620"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak mantıksal uygulama iş akışı oluşturma ve dağıtma

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) , [yüzlerce bağlayıcı](/connectors/connector-reference/connector-reference-logicapps-connectors)arasından seçim yaparak verileri, uygulamaları, bulut tabanlı Hizmetleri ve şirket içi sistemleri tümleştiren otomatik iş akışları oluşturmanıza ve çalıştırmanıza yardımcı olan bir bulut hizmetidir. Bu hızlı başlangıç, saatlik zamanlamaya göre Azure 'un durumunu denetleyen temel bir mantıksal uygulama oluşturmak için bir Azure Resource Manager şablonu dağıtma işlemine odaklanır (ARM şablonu). 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta, [Azure hızlı başlangıç şablonları galerisinde](https://azure.microsoft.com/resources/templates) bulabileceğiniz ancak burada gösteremeyeceği çok uzun olan [**bir mantıksal uygulama oluşturma**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) şablonu kullanılmaktadır. Bunun yerine, şablonlar galerisinde hızlı başlangıç şablonunun ["dosya üzerinde azuredeploy.js"](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) bölümünü inceleyebilirsiniz.

Hızlı başlangıç şablonu, her saat çalışacak şekilde ayarlanan yinelenme tetikleyicisini ve Azure 'un durumunu döndüren bir URL 'YI çağıran bir HTTP [ *yerleşik* eylemini](../connectors/built-in.md)kullanan bir mantıksal uygulama iş akışı oluşturur. Yerleşik bir eylem Azure Logic Apps platformuna yereldir.

Bu şablon aşağıdaki Azure kaynağını oluşturur:

* Bir mantıksal uygulama için iş akışını oluşturan [**Microsoft. Logic/iş akışları**](/azure/templates/microsoft.logic/workflows).

Azure Logic Apps yönelik daha fazla hızlı başlangıç şablonu bulmak için galerideki [Microsoft. Logic](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic) Templates ' i gözden geçirin.

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>Şablonu dağıtma

Hızlı başlangıç şablonunu dağıtmak için kullanmak istediğiniz seçeneği izleyin:

| Seçenek | Açıklama |
|--------|-------------|
| [Azure portalı](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Azure ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, bu adımlar doğrudan Azure 'da oturum açmanıza ve Azure portal hızlı başlangıç şablonunu açmanıza yardımcı olur. Daha fazla bilgi için bkz. [ARM şablonlarıyla kaynakları dağıtma ve Azure Portal](../azure-resource-manager/templates/deploy-portal.md). |
| [Azure CLI](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | Azure komut satırı arabirimi (Azure CLı), Azure kaynakları oluşturmak ve yönetmek için bir komut kümesidir. Bu komutları çalıştırmak için Azure CLı sürüm 2,6 veya sonraki bir sürümü gerekir. CLı sürümünüzü denetlemek için yazın `az --version` . Daha fazla bilgi için şu konulara bakın: <p><p>- [Azure CLı nedir?](/cli/azure/what-is-azure-cli) <br>- [Azure CLı ile çalışmaya başlama](/cli/azure/get-started-with-azure-cli) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | Azure PowerShell, Azure kaynaklarınızı yönetmek için Azure Resource Manager modelini kullanan bir dizi cmdlet sunar. Daha fazla bilgi için şu konulara bakın: <p><p>- [Azure PowerShell genel bakış](/powershell/azure/azurerm/overview) <br>- [Azure PowerShell az Module 'e giriş](/powershell/azure/new-azureps-module-az) <br>- [Azure PowerShell kullanmaya başlayın](/powershell/azure/get-started-azureps) |
| [Azure Kaynak Yönetimi REST API](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | Azure, hizmet kaynaklarına erişimi oluşturmak, almak, güncelleştirmek veya silmek için kullandığınız HTTP işlemlerini (yöntemleri) destekleyen hizmet uç noktaları olan temsili durum aktarımı (REST) API 'Leri sağlar. Daha fazla bilgi için bkz. [Azure REST API kullanmaya başlama](/rest/api/azure/). |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure hesabınızla oturum açmak için aşağıdaki görüntüyü seçin ve Azure portal hızlı başlangıç şablonunu açın:

   [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. Portalda, **bir şablon kullanarak mantıksal uygulama oluşturma** sayfasında şu değerleri girin veya seçin:

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Abonelik** | <*Azure-abonelik-adı*> | Kullanılacak Azure aboneliğinin adı |
   | **Kaynak grubu** | <*Azure-Resource-Group-Name*> | Yeni veya mevcut bir Azure Kaynak grubunun adı. Bu örnekte `Check-Azure-Status-RG` kullanılmıştır. |
   | **Bölge** | <*Azure-bölge*> | Mantıksal uygulamanızı kullanmak için Azure veri merkezi bölgesi. Bu örnekte `West US` kullanılmıştır. |
   | **Mantıksal uygulama adı** | <*Logic-App-adı*> | Mantıksal uygulamanız için kullanılacak ad. Bu örnekte `Check-Azure-Status-LA` kullanılmıştır. |
   | **Test URI 'Si** | <*test-URI*> | Belirli bir zamanlamaya göre çağrılacak hizmetin URI 'SI. Bu örnek `https://status.azure.com/en-us/status/` , Azure durum sayfası olan ' ı kullanır. |
   | **Konum** |  <*Azure-bölge-for-Resources*> | Varsayılan değerden farklıysa, tüm kaynaklar için kullanılacak Azure bölgesi. Bu örnek, `[resourceGroup().location]` kaynak grubu konumu olan varsayılan değeri kullanır. |
   ||||

   Sayfada bu örnekte kullanılan değerlerle birlikte nasıl göründüğü aşağıda verilmiştir:

   ![Hızlı başlangıç şablonu için bilgi sağlama](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin.

1. [Dağıtılan kaynakları İnceleme](#review-deployed-resources)bölümündeki adımlarla devam edin.

#### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name name to use for generating resource names:" projectName &&
read -p "Enter the location, such as 'westus':" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

Daha fazla bilgi için şu konulara bakın:

* [Azure CLı: az Deployment Group](/cli/azure/deployment/group)
* [ARM şablonları ve Azure CLı ile kaynak dağıtma](../azure-resource-manager/templates/deploy-cli.md)

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name to use for generating resource names"
$location = Read-Host -Prompt "Enter the location, such as 'westus'"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

Daha fazla bilgi için şu konulara bakın:

* [Azure PowerShell: New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
* [Azure PowerShell: New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [ARM şablonları ve Azure PowerShell kaynak dağıtma](../azure-resource-manager/templates/deploy-powershell.md)

#### <a name="rest-api"></a>[REST API](#tab/rest-api)

1. Var olan bir Azure kaynak grubunu kullanmak istemiyorsanız, kaynak yönetimine göndereceğiniz istek için bu sözdizimini izleyerek yeni bir kaynak grubu oluşturun REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Değer | Açıklama |
   |-------|-------------|
   | `subscriptionId`| Kullanmak istediğiniz Azure aboneliğinin GUID 'ı |
   | `resourceGroupName` | Oluşturulacak Azure Kaynak grubunun adı. Bu örnekte `Check-Azure-Status-RG` kullanılmıştır. |
   |||

   Örnek:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   Daha fazla bilgi için şu konulara bakın:

   * [Azure REST API başvurusu-Azure REST API 'Lerini çağırma](/rest/api/azure/)
   * [Kaynak yönetimi REST API: kaynak grupları-oluştur veya Güncelleştir](/rest/api/resources/resourcegroups/createorupdate).

1. Hızlı başlangıç şablonunu kaynak grubunuza dağıtmak için, kaynak yönetimine göndereceğiniz istek için bu sözdizimini izleyin REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Değer | Açıklama |
   |-------|-------------|
   | `subscriptionId`| Kullanmak istediğiniz Azure aboneliğinin GUID 'ı |
   | `resourceGroupName` | Kullanılacak Azure Kaynak grubunun adı. Bu örnekte `Check-Azure-Status-RG` kullanılmıştır. |
   | `deploymentName` | Dağıtımınız için kullanılacak ad. Bu örnekte `Check-Azure-Status-LA` kullanılmıştır. |
   |||

   Örnek:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   Daha fazla bilgi için bkz. [kaynak yönetimi REST API: dağıtımlar-oluştur veya Güncelleştir](/rest/api/resources/deployments/createorupdate).

1. Dağıtım için kullanılacak değerleri (örneğin, Azure bölgesi) ve dağıtımda kullanılacak hızlı başlangıç şablonunun değerlerini içeren hızlı başlangıç şablonu ve [parametre dosyası](../azure-resource-manager/templates/template-parameters.md)bağlantıları sağlamak Için, kaynak yönetimine göndereceğiniz istek gövdesi için bu sözdizimini izleyin REST API:

   ```json
   {
      "location": "{Azure-region}",
      "properties": {
         "templateLink": {
            "uri": "{quickstart-template-URL}",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "{quickstart-template-parameter-file-URL}",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | `location`| <*Azure-bölge*> | Dağıtım için kullanılacak Azure bölgesi. Bu örnekte `West US` kullanılmıştır. |
   | `templateLink` : `uri` | <*hızlı başlangıç-şablon-URL*> | Dağıtım için kullanılacak hızlı başlangıç şablonunun URL konumu: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*hızlı başlangıç-şablon-parametre-dosya-URL*> | Dağıtım için kullanılacak hızlı başlangıç şablonunun parametre dosyasının URL konumu: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>Kaynak Yöneticisi parametre dosyası hakkında daha fazla bilgi için şu konulara bakın: <p><p>- [Kaynak Yöneticisi parametre dosyası oluştur](../azure-resource-manager/templates/parameter-files.md) <br>- [Öğretici: ARM şablonunuzu dağıtmak için parametre dosyalarını kullanma](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*dağıtım modu*> | Artımlı bir güncelleştirme çalıştırın veya güncelleştirmeyi tamamen yapın. Bu örnek `Incremental` , varsayılan değer olan ' ı kullanır. Daha fazla bilgi için bkz. [Azure Resource Manager Dağıtım modları](../azure-resource-manager/templates/deployment-modes.md). |
   |||

   Örnek:

   ```json
   {
      "location": "West US",
      "properties": {
         "templateLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

Daha fazla bilgi için şu konulara bakın:

* [Kaynak yönetimi REST API](/rest/api/resources/)
* [ARM şablonlarıyla kaynakları dağıtma ve Kaynak Yöneticisi REST API](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Mantıksal uygulamayı görüntülemek için Azure portal kullanabilir, Azure CLı veya Azure PowerShell ile oluşturduğunuz bir betiği çalıştırabilir ya da Logic App REST API kullanabilirsiniz.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal arama kutusuna mantıksal uygulamanızın adını girin ( `Check-Azure-Status-LA` Bu örnekte). Sonuçlar listesinden mantıksal uygulamanızı seçin.

1. Azure portal, bu örnekteki mantıksal uygulamanızı bulun ve seçin `Check-Azure-Status-RG` .

1. Mantıksal uygulama Tasarımcısı açıldığında, hızlı başlangıç şablonu tarafından oluşturulan mantıksal uygulamayı gözden geçirin.

1. Mantıksal uygulamayı test etmek için tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

Daha fazla bilgi için bkz. [Azure CLI: az Logic Workflow Show](/cli/azure/logic/workflow#az_logic_workflow_show).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

Daha fazla bilgi için bkz. [Azure PowerShell: Get-AzLogicApp](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="rest-api"></a>[REST API](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Değer | Açıklama |
|-------|-------------|
| `subscriptionId`| Hızlı başlangıç şablonunu dağıttığınız Azure aboneliğinin GUID 'ı. |
| `resourceGroupName` | Hızlı başlangıç şablonunu dağıttığınız Azure Kaynak grubunun adı. Bu örnekte `Check-Azure-Status-RG` kullanılmıştır. |
| `workflowName` | Dağıttığınız mantıksal uygulamanın adı. Bu örnekte `Check-Azure-Status-LA` kullanılmıştır. |
|||

Örnek:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

Daha fazla bilgi için bkz. [Logic Apps REST API: Iş akışları-al](/rest/api/logic/workflows/get).

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıçlarla ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları tutmak isteyebilirsiniz. Mantıksal uygulamaya artık ihtiyaç duymadığınızda, Azure portal, Azure CLı, Azure PowerShell veya kaynak yönetimi REST API kullanarak kaynak grubunu silin.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure portal, bu örnekte yer alan silmek istediğiniz kaynak grubunu bulun ve seçin `Check-Azure-Status-RG` .

1. Kaynak grubu menüsünde, henüz seçili değilse **genel bakış** ' ı seçin. Genel Bakış sayfasında, **kaynak grubunu sil**' i seçin.

1. Onaylamak için kaynak grubunun adını girin.

Daha fazla bilgi için bkz. [kaynak grubunu silme](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

Daha fazla bilgi için bkz. [Azure CLI: az Group Delete](/cli/azure/group#az_group_delete).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Daha fazla bilgi için bkz. [Azure PowerShell: Remove-AzResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

### <a name="rest-api"></a>[REST API](#tab/rest-api)

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
```

| Değer | Açıklama |
|-------|-------------|
| `subscriptionId`| Hızlı başlangıç şablonunu dağıttığınız Azure aboneliğinin GUID 'ı. |
| `resourceGroupName` | Hızlı başlangıç şablonunu dağıttığınız Azure Kaynak grubunun adı. Bu örnekte `Check-Azure-Status-RG` kullanılmıştır. |
|||

Örnek:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

Daha fazla bilgi için bkz. [kaynak yönetimi REST API: kaynak grupları-sil](/rest/api/resources/resourcegroups/delete).

---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
