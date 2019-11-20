---
title: Mantıksal uygulama şablonları dağıtma-Azure Logic Apps
description: Azure Logic Apps için oluşturulan Azure Resource Manager şablonlarını dağıtmayı öğrenin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 083c908609ebcf1bdc6dcad5e37ae03daa41758f
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706498"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Logic Apps için Azure Resource Manager şablonları dağıtma

Mantıksal uygulamanız için bir Azure Resource Manager şablonu oluşturduktan sonra, şablonunuzu şu yollarla dağıtabilirsiniz:

* [Azure portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Azure portal üzerinden dağıtma

Mantıksal uygulama şablonunu Azure 'a otomatik olarak dağıtmak için, aşağıdaki **Azure 'A dağıt** düğmesini seçebilirsiniz ve bu, Azure Portal oturumunuzu açar ve mantıksal uygulamanız hakkında bilgi ister. Daha sonra mantıksal uygulama şablonunda veya parametrelerde gerekli değişiklikleri yapabilirsiniz.

[![Azure’a dağıtma](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Örneğin, Azure portal oturum açtıktan sonra aşağıdaki bilgiler istenir:

* Azure abonelik adı
* Kullanmak istediğiniz kaynak grubu
* Mantıksal uygulama konumu
* Mantıksal uygulamanızın adı
* Test URI 'SI
* Belirtilen hüküm ve koşulları kabul etme

Daha fazla bilgi için şu konulara bakın:

* [Bakýþ Azure Resource Manager şablonlarıyla Logic Apps için dağıtımı otomatikleştirin](logic-apps-azure-resource-manager-templates-overview.md)
* [Kaynakları Azure Resource Manager şablonlarıyla ve Azure portal dağıtma](../azure-resource-manager/resource-group-template-deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Visual Studio ile dağıtma

Visual Studio kullanarak oluşturduğunuz bir Azure Kaynak grubu projesinden bir Logic App şablonu dağıtmak için, [mantıksal uygulamanızı Azure 'a el ile dağıtmak üzere aşağıdaki adımları](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) izleyin.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Azure PowerShell ile dağıtma

Belirli bir *Azure Kaynak grubuna*dağıtmak için aşağıdaki komutu kullanın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Daha fazla bilgi için şu konulara bakın:

* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Azure CLI ile dağıtma

Belirli bir *Azure Kaynak grubuna*dağıtmak için aşağıdaki komutu kullanın:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Daha fazla bilgi için şu konulara bakın:

* [Kaynakları Resource Manager şablonları ve Azure CLI ile dağıtma](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Azure DevOps ile dağıtma

Mantıksal uygulama şablonları dağıtmak ve ortamları yönetmek için, ekipler genellikle [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)'da [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) gibi bir araç kullanır. Azure Pipelines, herhangi bir derleme veya yayın ardışık düzenine ekleyebileceğiniz bir [Azure Kaynak grubu dağıtım görevi](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) sağlar. Yayın işlem hattını dağıtma ve oluşturma yetkilendirmesi için Azure Active Directory (AD) [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md)da gerekir. [Azure Pipelines ile hizmet sorumlularını kullanma](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)hakkında daha fazla bilgi edinin.

Azure Pipelines olan Azure Resource Manager şablonlar için sürekli tümleştirme ve sürekli dağıtım (CI/CD) hakkında daha fazla bilgi için şu konulara ve örneklere bakın:

* [Kaynak Yöneticisi şablonlarını Azure Pipelines ile tümleştirme](../azure-resource-manager/vs-resource-groups-project-devops-pipelines.md)
* [Öğretici: Azure Pipelines olan Azure Resource Manager şablonlar için sürekli tümleştirme](../azure-resource-manager/resource-manager-tutorial-use-azure-pipelines.md)
* [Örnek: Azure DevOps 'da Azure Logic Apps Azure Service Bus kuyruklara bağlanma ve Azure Pipelines ile dağıtma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure depolama hesaplarına Azure Logic Apps ve Azure DevOps 'da Azure Pipelines ile dağıtma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps için bir işlev uygulaması eylemi ayarlama ve Azure DevOps 'da Azure Pipelines ile dağıtma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps 'ten bir tümleştirme hesabına bağlanın ve Azure DevOps 'da Azure Pipelines ile dağıtın](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

Azure Pipelines kullanmaya yönelik genel üst düzey adımlar aşağıda verilmiştir:

1. Azure Pipelines ' de boş bir işlem hattı oluşturun.

1. İşlem hattı için ihtiyaç duyduğunuz kaynakları, örneğin, el ile veya yapı sürecinin bir parçası olarak oluşturduğunuz mantıksal uygulama şablonu ve şablon parametreleri dosyaları gibi seçin.

1. Aracı işiniz için, **Azure Kaynak grubu dağıtım** görevini bulun ve ekleyin.

   !["Azure Kaynak grubu dağıtımı" görevi Ekle](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. [Hizmet sorumlusu](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)ile yapılandırma.

1. Mantıksal uygulama şablonunuz ve şablon parametreleri dosyalarınıza başvurular ekleyin.

1. Diğer bir ortam, otomatikleştirilmiş test veya gerektiğinde onaylayanlar için yayın işlemindeki adımları oluşturmak için devam edin.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth bağlantıları yetkilendirin

Dağıtımdan sonra mantıksal uygulamanız geçerli parametrelerle uçtan uca çalışacak. Ancak, [kimlik bilgilerinizi doğrulamak](../active-directory/develop/authentication-scenarios.md)için geçerli erişim belirteçleri oluşturmak üzere herhangi bir OAuth bağlantısını yetkilendirmelisiniz. OAuth bağlantıları yetkilendirebileceğiniz yollar şunlardır:

* Otomatikleştirilmiş dağıtımlar için, her OAuth bağlantısı için onay sağlayan bir komut dosyası kullanabilirsiniz. Aşağıda, [Logicappconnectionauth](https://github.com/logicappsio/LogicAppConnectionAuth) projesinde GitHub 'da örnek bir betik verilmiştir.

* OAuth bağlantılarını el ile yetkilendirmek için, mantıksal uygulama tasarımcısında Azure portal veya Visual Studio 'da mantıksal uygulamanızı açın. Tasarımcıda gerekli tüm bağlantıları yetkilendirin.

Bağlantıları yetkilendirmek için bir Azure Active Directory (Azure AD) [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md) kullanıyorsanız, [mantıksal uygulama şablonunuzda hizmet sorumlusu parametrelerini belirtmeyi](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Mantıksal Uygulamaları izleme](../logic-apps/logic-apps-monitor-your-logic-apps.md)
