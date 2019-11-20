---
title: Dağıtım için mantıksal uygulama şablonları oluşturma-Azure Logic Apps
description: Azure Logic Apps içinde dağıtımı otomatikleştirmek için Azure Resource Manager şablonları oluşturmayı öğrenin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 57e9cec16326068cc7de74b8f7266fbe47808fed
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845442"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Azure Logic Apps için dağıtımı otomatikleştirmek üzere Azure Resource Manager şablonları oluşturun

Mantıksal uygulamanızı oluşturma ve dağıtma işlemlerini otomatik hale getirmenize yardımcı olmak için, bu makalede mantıksal uygulamanız için [Azure Resource Manager şablonu](../azure-resource-manager/resource-group-overview.md) oluşturabileceğiniz yollar açıklanmaktadır. İş akışı tanımınızı ve dağıtım için gereken diğer kaynakları içeren bir şablonun yapısı ve sözdizimi hakkında genel bakış için bkz [. genel bakış: Azure Resource Manager şablonlarıyla](logic-apps-azure-resource-manager-templates-overview.md)Logic Apps için dağıtımı otomatikleştirin.

Azure Logic Apps, yalnızca mantıksal uygulamalar oluşturmak için değil, yeniden kullanabileceğiniz [önceden oluşturulmuş bir mantıksal uygulama Azure Resource Manager şablonu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) sağlar, ancak dağıtım için kullanılacak kaynakları ve parametreleri de tanımlayabilir. Bu şablonu kendi iş senaryolarınız için kullanabilir veya şablonu gereksinimlerinize uyacak şekilde özelleştirebilirsiniz.

> [!IMPORTANT]
> Şablonunuzda bulunan bağlantıların mantıksal uygulamanızla aynı Azure kaynak grubunu ve konumunu kullandığınızdan emin olun.

Azure Resource Manager şablonları hakkında daha fazla bilgi için şu konulara bakın:

* [Azure Resource Manager şablon yapısı ve sözdizimi](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager şablonları yaz](../azure-resource-manager/resource-group-authoring-templates.md)
* [Bulut tutarlılığı için Azure Resource Manager şablonları geliştirme](../azure-resource-manager/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Visual Studio ile şablon oluşturma

Dağıtım için en kolay geçerli parametreli mantıksal uygulama şablonları oluşturmanın en kolay yolu için Visual Studio 'Yu (ücretsiz Community Edition veya üzeri) ve Visual Studio için Azure Logic Apps araçları 'nı kullanın. Daha sonra [Visual Studio 'da mantıksal uygulamanızı oluşturabilir](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) veya [var olan bir mantıksal uygulamayı visual Studio 'ya Azure Portal bulup indirebilirsiniz](../logic-apps/manage-logic-apps-with-visual-studio.md).

Mantıksal uygulamanızı indirerek, mantıksal uygulamanızın tanımlarını ve bağlantılar gibi diğer kaynakları içeren bir şablon alırsınız. Şablon, mantıksal uygulamanızı ve diğer kaynaklarınızı dağıtmak için kullanılan değerleri de *parametreleştirir*veya parametrelerini tanımlar. Bu parametrelerin değerlerini ayrı Parametreler dosyasında sağlayabilirsiniz. Bu şekilde, bu değerleri dağıtım gereksinimlerinize göre daha kolay bir şekilde değiştirebilirsiniz. Daha fazla bilgi için şu konulara bakın:

* [Visual Studio ile mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visual Studio ile mantıksal uygulamaları yönetme](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Azure PowerShell ile şablonlar oluşturma

[Logicapptemplate modülüyle](https://github.com/jeffhollan/LogicAppTemplateCreator)Azure PowerShell kullanarak kaynak yöneticisi şablonlar oluşturabilirsiniz. Bu açık kaynaklı modül öncelikle mantıksal uygulamanızı ve mantıksal uygulamanın kullandığı tüm bağlantıları değerlendirir. Modül daha sonra dağıtım için gerekli parametrelerle şablon kaynakları oluşturur.

Örneğin, bir Azure Service Bus kuyruğundan ileti alan ve bir Azure SQL veritabanına veri yükleyen bir mantıksal uygulamanız olduğunu varsayalım. Modül, tüm düzenleme mantığını korur ve SQL ve Service Bus bağlantı dizelerini parametreleştirir ve bu değerleri dağıtım gereksinimlerinize göre sağlayabilmeniz ve değiştirebilmenizi sağlar.

Bu örnekler Azure Resource Manager şablonları kullanarak Logic Apps oluşturmayı ve dağıtmayı, Azure DevOps 'da Azure Pipelines ve Azure PowerShell şunları gösterir:

* [Örnek: Azure Logic Apps Azure Service Bus kuyruklara bağlanma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps Azure Storage hesaplarına bağlanma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps için bir işlev uygulama eylemi ayarlama](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Örnek: Azure Logic Apps bir tümleştirme hesabına bağlanma](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>PowerShell modüllerini yükler

1. Henüz yapmadıysanız, [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)' yi daha sonra yükleyebilirsiniz.

1. [PowerShell Galerisi](https://www.powershellgallery.com/packages/LogicAppTemplate)LogicAppTemplate modülünü yüklemenin en kolay yolu için şu komutu çalıştırın:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   En son sürüme güncelleştirmek için şu komutu çalıştırın:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Veya el ile yüklemek için, [mantıksal uygulama şablonu Oluşturucu](https://github.com/jeffhollan/LogicAppTemplateCreator)için GitHub 'daki adımları izleyin.

### <a name="install-azure-resource-manager-client"></a>Azure Resource Manager istemcisi 'ni yükler

LogicAppTemplate modülünün herhangi bir Azure kiracı ve abonelik erişim belirteciyle çalışması için, Azure Resource Manager API 'sini çağıran basit bir komut satırı aracı olan [Azure Resource Manager istemci aracını](https://github.com/projectkudu/ARMClient)yükleyebilirsiniz.

Bu araçla `Get-LogicAppTemplate` komutunu çalıştırdığınızda, komut önce armclient aracı aracılığıyla bir erişim belirteci alır, belirteci PowerShell betiğine yönelttir ve şablonu bir JSON dosyası olarak oluşturur. Araç hakkında daha fazla bilgi için [Azure Resource Manager istemci aracıyla ilgili bu makaleye](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)bakın.

### <a name="generate-template-with-powershell"></a>PowerShell ile şablon oluşturma

LogicAppTemplate modülünü ve [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)yükledikten sonra şablonunuzu oluşturmak Için Şu PowerShell komutunu çalıştırın:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

[Azure Resource Manager istemci aracından](https://github.com/projectkudu/ARMClient)bir belirteçte boru 'a yönelik öneriyi izlemek için Azure abonelik kimliğiniz `$SubscriptionId` olduğu yerine bu komutu çalıştırın:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Ayıkladıktan sonra, bu komutu çalıştırarak şablonınızdan bir parametreler dosyası oluşturabilirsiniz:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Azure Key Vault başvurularla ayıklama için (yalnızca statik), şu komutu çalıştırın:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametreler | Gerekli | Açıklama |
|------------|----------|-------------|
| TemplateFile | Evet | Şablon dosyanızın dosya yolu |
| KeyVault | Hayır | Olası Anahtar Kasası değerlerini nasıl işleyeceğinizi açıklayan bir sabit listesi. Varsayılan, `None` değeridir. |
||||

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Mantıksal uygulama şablonları dağıtma](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
