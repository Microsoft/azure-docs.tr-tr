---
title: Genel Bakış-Azure Logic Apps için dağıtımı otomatikleştirin
description: Azure Logic Apps için dağıtımı otomatikleştirmek üzere Azure Resource Manager şablonlar hakkında bilgi edinin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: bc61e39a02d16827521758ca8248488e46c109b5
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838090"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Genel Bakış: Azure Resource Manager şablonları kullanarak Azure Logic Apps dağıtımı otomatikleştirin

Mantıksal uygulamanızı oluşturma ve dağıtma işlemini otomatik hale getirmeye hazırsanız, mantıksal uygulamanızın temel alınan iş akışı tanımını bir [Azure Resource Manager şablonuna](../azure-resource-manager/resource-group-overview.md)genişletebilirsiniz. Bu şablon, mantıksal uygulamanızı sağlamak ve dağıtmak için altyapıyı, kaynakları, parametreleri ve diğer bilgileri tanımlar. Aynı zamanda *parametrelendirme*olarak da bilinen dağıtımda farklılık gösteren değerler için parametreler tanımlayarak, farklı dağıtım ihtiyaçlarına göre mantıksal uygulamaları sürekli ve tutarlı bir şekilde dağıtabilirsiniz.

Örneğin, geliştirme, test ve üretim için ortamlara dağıtırsanız, büyük olasılıkla her ortam için farklı bağlantı dizeleri kullanırsınız. Farklı bağlantı dizelerini kabul eden şablon parametreleri bildirebilir ve sonra bu dizeleri ayrı bir [parametre dosyasında](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)depoaktarabilirsiniz. Bu şekilde, şablonu güncelleştirmek ve yeniden dağıtmak zorunda kalmadan bu değerleri değiştirebilirsiniz. Gizli olan veya güvenli hale getirilmesi gereken, parola ve gizli dizi gibi parametre değerlerine sahip olduğunuz senaryolarda, bu değerleri [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) saklayabilir ve parametreler dosyanızın bu değerleri almasına sahip olursunuz. Ancak, bu senaryolarda geçerli değerleri almak için yeniden dağıtmanız gerekir.

Bu genel bakışta, bir mantıksal uygulama iş akışı tanımını içeren Kaynak Yöneticisi şablonundaki öznitelikler açıklanmaktadır. Hem şablon hem de iş akışı tanımınız JSON sözdizimini kullanır, ancak iş akışı tanımı aynı zamanda iş akışı [tanım dili şemasını](../logic-apps/logic-apps-workflow-definition-language.md)takip ettiğinden bazı farklılıklar vardır. Örneğin, şablon ifadeleri ve iş akışı tanımı ifadeleri, [parametrelere](#parameter-references) ve kabul edebileceği değerlere göre farklılık gösterir.

> [!TIP]
> Dağıtım için en kolay geçerli bir parametreli mantıksal uygulama şablonu almanın en kolay yolu için Visual Studio 'Yu (ücretsiz Community Edition veya üzeri) ve Visual Studio için Azure Logic Apps araçları 'nı kullanın. Daha sonra [mantıksal uygulamanızı Visual Studio 'da oluşturabilir](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) veya [mevcut bir mantıksal uygulamayı Azure 'da Visual Studio 'ya bulabilir ve indirebilirsiniz](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Ya da, [LogicAppTemplate modülüyle Azure PowerShell](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)kullanarak mantıksal uygulama şablonları oluşturabilirsiniz.

Bu konudaki örnek mantıksal uygulama, yeni bir e-posta geldiğinde tetiklenen bir [Office 365 Outlook tetikleyicisi](/connectors/office365/) ve e-posta gövdesi için bir blob oluşturan ve bu Blobun bir Azure depolama kapsayıcısına yükleyen bir [Azure Blob depolama eylemi](/connectors/azureblob/) kullanır. Örnekler Ayrıca dağıtımda farklılık gösteren değerlerin nasıl parametreleştirilemez olduğunu gösterir.

Kaynak Yöneticisi şablonları hakkında daha fazla bilgi için şu konulara bakın:

* [Azure Resource Manager şablon yapısı ve sözdizimi](../azure-resource-manager/resource-group-authoring-templates.md)
* [Resource Manager şablonu en iyi yöntemleri](../azure-resource-manager/template-best-practices.md)
* [Bulut tutarlılığı için Azure Resource Manager şablonları geliştirme](../azure-resource-manager/templates-cloud-consistency.md)

Örnek mantıksal uygulama şablonları için aşağıdaki örneklere bakın:

* Bu konunun örnekleri için kullanılan [Tam şablon](#full-example-template)
* GitHub 'da [örnek hızlı başlangıç mantıksal uygulama şablonu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create)

Logic Apps, tümleştirme hesapları ve tümleştirme hesabı yapılarına özgü şablon kaynak bilgileri için bkz. [Microsoft. Logic Resource Types](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="template-structure"></a>

## <a name="template-structure"></a>Şablon yapısı

En üst düzeyde, bir Kaynak Yöneticisi şablonu, [Azure Resource Manager şablon yapısı ve sözdizimi](../azure-resource-manager/resource-group-authoring-templates.md) konusunda tam olarak açıklanan bu yapıyı izler:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Mantıksal uygulama şablonu için öncelikle bu şablon nesneleriyle çalışırsınız:

| Öznitelik | Açıklama |
|-----------|-------------|
| `parameters` | Azure 'da dağıtım için kaynakları oluştururken ve özelleştirirken kullanılacak değerleri kabul etmek için [şablon parametrelerini](../azure-resource-manager/resource-group-authoring-templates.md#parameters) bildirir. Örneğin, bu parametreler mantıksal uygulamanızın adı ve konumu, bağlantıları ve dağıtım için gereken diğer kaynaklarla ilgili değerleri kabul eder. Bu parametre değerlerini, bu konunun ilerleyen kısımlarında açıklanan bir [Parametreler dosyasında](#template-parameter-files)saklayabilirsiniz. Genel Ayrıntılar için bkz. [Parametreler-Kaynak Yöneticisi Şablon yapısı ve sözdizimi](../azure-resource-manager/resource-group-authoring-templates.md#parameters). |
| `resources` | Mantıksal uygulamanız, bağlantılarınız, Azure depolama hesaplarınız ve benzeri bir Azure Kaynak grubu oluşturmak veya güncelleştirmek ve dağıtmak için [kaynakları](../azure-resource-manager/resource-group-authoring-templates.md#resources) tanımlar. Genel Ayrıntılar için bkz. [Resources-Kaynak Yöneticisi Şablon yapısı ve sözdizimi](../azure-resource-manager/resource-group-authoring-templates.md#resources). |
||||

Mantıksal uygulama şablonunuz bu dosya adı biçimini kullanır:

**<*Logic-App-adı*>. JSON**

> [!IMPORTANT]
> Şablon sözdizimi büyük/küçük harfe duyarlıdır, bu nedenle tutarlı büyük harf kullandığınızdan emin olun. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Şablon parametreleri

Mantıksal uygulama şablonunda, farklı düzeylerde bulunan ve farklı işlevler gerçekleştiren birden çok `parameters` nesnesi vardır. Örneğin, en üst düzeyde, Azure 'da kaynak oluştururken ve dağıttığınızda dağıtım sırasında kabul edilecek ve kullanılacak değerler için [şablon parametreleri](../azure-resource-manager/resource-group-authoring-templates.md#parameters) bildirebilirsiniz, örneğin:

* Mantıksal uygulamanız
* Mantığınızın [yönetilen bağlayıcılar](../connectors/apis-list.md) aracılığıyla diğer hizmetlere ve sistemlere erişmek için kullandığı bağlantılar
* Mantıksal uygulamanızın dağıtım için ihtiyaç duyacağı diğer kaynaklar

  Örneğin, mantıksal uygulamanız işletmeden işletmeye (B2B) senaryolar için bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kullanıyorsa, şablonun en üst düzey `parameters` nesnesi bu tümleştirme HESABıNıN kaynak kimliğini kabul eden parametreyi bildirir.

[Parametreler-Kaynak Yöneticisi Şablon yapısı ve sözdizimi](../azure-resource-manager/resource-group-authoring-templates.md#parameters)tarafından tam olarak açıklanan bir parametre tanımının genel yapısı ve sözdizimi aşağıda verilmiştir:

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Bu örnekte, Azure 'da bu kaynakları oluşturmak ve dağıtmak için kullanılan değerler için yalnızca şablon parametreleri gösterilmektedir:

* Mantıksal uygulamanızın adı ve konumu
* Mantıksal uygulamayla bağlantılı bir tümleştirme hesabı için kullanılacak KIMLIK

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Gizli olan veya güvenli hale getirilmesi gereken (örneğin, Kullanıcı adları, parolalar ve gizlilikler) değerleri işleyen parametreler dışında, tüm bu parametreler `defaultValue` öznitelikleri içerir, ancak bazı durumlarda varsayılan değerler boş değerlerdir. Bu şablon parametreleri için kullanılacak dağıtım değerleri, bu konunun ilerleyen kısımlarında açıklanan örnek [Parametreler dosyası](#template-parameter-files) tarafından sağlanır.

Şablon parametrelerinin güvenliğini sağlamak için şu konulara bakın:

* [Şablon parametreleri için güvenlik önerileri](../azure-resource-manager/template-best-practices.md#parameters)
* [Güvenli şablon parametreleri](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Azure Key Vault ile güvenli parametre değerlerini geçirme](../azure-resource-manager/resource-manager-keyvault-parameter.md)

Diğer şablon nesneleri genellikle şablon parametrelerine başvurur, böylece şablon parametrelerinden geçen değerleri kullanabilirler. Örneğin:

* [Şablonunuzun kaynak nesnesi](#template-resources), bu konunun ilerleyen kısımlarında açıklanan Azure 'da, [mantıksal uygulamanızın kaynak tanımı](#logic-app-resource-definition)gibi, oluşturmak ve dağıtmak istediğiniz her kaynağı tanımlar. Bu kaynaklar genellikle mantıksal uygulamanızın adı ve konumu ile bağlantı bilgileri gibi şablon parametre değerlerini kullanır.

* Mantıksal uygulamanızın kaynak tanımında daha derin bir düzeyde, [iş akışı tanımınızın Parameters nesnesi](#workflow-definition-parameters) mantıksal uygulamanızın çalışma zamanında kullanılacak değerler için parametreler bildirir. Örneğin, bir HTTP tetikleyicisinin kimlik doğrulaması için kullandığı Kullanıcı adı ve parola için iş akışı Tanım parametrelerini bildirebilirsiniz. İş akışı tanımı parametrelerinin değerlerini belirtmek için, iş akışı tanımınızın *dışında* , ancak mantıksal uygulamanızın kaynak *tanımında hala olan* `parameters` nesnesini kullanın. Bu dış `parameters` nesnesinde, önceden tanımlanmış şablon parametrelerine başvurabilirsiniz, bu, bir parametreler dosyasından dağıtımda değer kabul edebilir.

Parametrelere başvurulduğunda, şablon ifadeleri ve işlevleri farklı sözdizimi kullanır ve iş akışı Tanım ifadelerinden ve işlevlerinden farklı şekilde davranır. Bu farklılıklar hakkında daha fazla bilgi için, bu konunun ilerleyen kısımlarında [parametrelere başvurular](#parameter-references) bölümüne bakın.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>En iyi yöntemler-şablon parametreleri

Parametreleri tanımlamaya yönelik bazı en iyi uygulamalar şunlardır:

* Yalnızca dağıtım gereksinimlerinize göre değişen değerler için parametreler bildirin. Farklı dağıtım gereksinimleri boyunca aynı olan değerler için parametre bildirme.

* Hassas olan veya güvenli hale getirilmesi gereken değerler hariç tüm parametreler için boş değerler belirtebileceğiniz `defaultValue` özniteliğini ekleyin. Kullanıcı adları, parolalar ve gizli diziler için her zaman güvenli parametreleri kullanın. Hassas parametre değerlerini gizlemek veya korumak için bu konulardaki yönergeleri izleyin:

  * [Şablon parametreleri için güvenlik önerileri](../azure-resource-manager/template-best-practices.md#parameters)

  * [Güvenli şablon parametreleri](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Azure Key Vault ile güvenli parametre değerlerini geçirme](../azure-resource-manager/resource-manager-keyvault-parameter.md)

* Şablon parametre adlarını iş akışı Tanım parametresi adlarından ayırt etmek için, açıklayıcı şablon parametre adlarını kullanabilirsiniz, örneğin: `TemplateFabrikamPassword`

Daha fazla şablon en iyi uygulamaları için bkz. [şablon parametreleri Için en iyi uygulamalar](../azure-resource-manager/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Şablon parametreleri dosyası

Şablon parametrelerinin değerlerini sağlamak için bu değerleri bir [Parametreler dosyasında](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)depolayın. Bu şekilde, dağıtım gereksinimlerinize göre farklı parametre dosyalarını kullanabilirsiniz. Kullanılacak dosya adı biçimi aşağıda verilmiştir:

* Mantıksal uygulama şablonu dosya adı: **<*Logic-app-name*>. JSON**
* Parametreler dosya adı: **<*Logic-App-adı*>. Parameters. JSON**

Aşağıda, [Azure Key Vault ile güvenli bir parametre değeri geçirmek](../azure-resource-manager/resource-manager-keyvault-parameter.md)için bir Anahtar Kasası başvurusu içeren parametreler dosyasının içindeki yapısı verilmiştir:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Bu örnek parametre dosyası, bu konuda daha önce belirtilen şablon parametrelerinin değerlerini belirtir:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Şablon kaynakları

Şablonunuz, [mantıksal uygulamanızın kaynak tanımı](#logic-app-resource-definition), herhangi bir [bağlantı kaynak](#connection-resource-definitions)tanımı ve mantıksal uygulamanızın dağıtım için ihtiyaç duyacağı diğer tüm kaynaklar gibi Azure 'da oluşturulacak ve dağıtılacak her kaynak için tanımları içeren bir `resources` nesnesine sahiptir.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Şablonlar birden çok Logic Apps için kaynak tanımları içerebilir, bu nedenle tüm mantıksal uygulama kaynaklarınızın aynı Azure kaynak grubunu belirttiğinizden emin olun. Şablonu Visual Studio kullanarak bir Azure Kaynak grubuna dağıttığınızda, sizden açmak istediğiniz mantıksal uygulama sorulur. Ayrıca, Azure Kaynak grubu projeniz birden fazla şablon içerebilir, bu nedenle istendiğinde doğru parametreler dosyasını seçtiğinizden emin olun.

Şablon kaynakları ve öznitelikleri hakkında genel bilgi için şu konulara bakın:

* [Kaynaklar-Kaynak Yöneticisi Şablon yapısı ve sözdizimi](../azure-resource-manager/resource-group-authoring-templates.md#resources)
* [Şablon kaynakları için en iyi uygulamalar](../azure-resource-manager/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Mantıksal uygulama kaynak tanımı

Mantıksal uygulamanızın kaynak tanımı, bu bilgileri içeren `properties` nesnesiyle başlar:

* Dağıtım sırasında mantıksal uygulamanızın durumu
* Mantıksal uygulamanız tarafından kullanılan herhangi bir tümleştirme hesabının KIMLIĞI
* Mantıksal uygulamanızın iş akışı tanımı
* Çalışma zamanında kullanılacak değerleri ayarlayan `parameters` nesnesi
* Mantıksal uygulamanız hakkındaki ad, tür, konum ve benzeri diğer kaynak bilgileri

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Mantıksal uygulama kaynak tanımınıza özel öznitelikler şunlardır:

| Öznitelik | Gerekli | Tür | Açıklama |
|-----------|----------|------|-------------|
| `state` | Evet | Dize | `Enabled`, mantıksal uygulamanızın canlı olduğu ve `Disabled` mantıksal uygulamanızın etkin olmadığı anlamına gelen dağıtımda, mantıksal uygulamanızın durumu. Örneğin, mantıksal uygulamanızın canlı olmaya devam etmek, ancak taslak sürümü dağıtmak istiyorsanız, `Disabled` seçeneğini kullanabilirsiniz. |
| `integrationAccount` | Hayır | Nesne | Mantıksal uygulamanız, işletmeden işletmeye (B2B) senaryolar için yapıtları depolayan bir tümleştirme hesabı kullanıyorsa, bu nesne tümleştirme hesabının KIMLIĞINI belirten `id` özniteliğini içerir. |
| `definition` | Evet | Nesne | Mantıksal uygulamanızın temel alınan iş akışı tanımı, kod görünümünde görüntülenen ve bu nesne, [Iş akışı tanımlama dili Için şema başvurusu](../logic-apps/logic-apps-workflow-definition-language.md) içinde tam olarak açıklanmıştır. Bu iş akışı tanımında `parameters` nesnesi, mantıksal uygulama çalışma zamanında kullanılacak değerler için parametreler bildirir. Daha fazla bilgi için bkz. [Iş akışı tanımı ve parametreleri](#workflow-definition-parameters). <p><p>Mantıksal uygulamanızın iş akışı tanımındaki öznitelikleri görüntülemek için, Azure portal veya Visual Studio 'da "Tasarım görünümü" ne "kod görünümü" ne, yoksa [Azure Kaynak Gezgini](https://resources.azure.com)gibi bir araç kullanarak geçiş yapın. |
| `parameters` | Hayır | Nesne | Mantıksal uygulama çalışma zamanında kullanılacak [iş akışı tanımı parametre değerleri](#workflow-definition-parameters) . Bu değerler için parametre tanımları, [iş akışı tanımınızın parametreler nesnesinin](#workflow-definition-parameters)içinde görünür. Ayrıca, mantıksal uygulamanız diğer hizmetlere ve sistemlere erişmek için [yönetilen bağlayıcılar](../connectors/apis-list.md) kullanıyorsa, bu nesne, çalışma zamanında kullanılacak bağlantı değerlerini ayarlayan bir `$connections` nesnesi içerir. |
| `accessControl` | Hayır | Nesne | Mantıksal uygulamanıza yönelik olarak IP erişimini kısıtlama veya çalıştırma geçmişi girişleri ve çıkışları gibi güvenlik özniteliklerini belirtmek için. Daha fazla bilgi için bkz. [Logic Apps 'e güvenli erişim](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

Logic Apps, tümleştirme hesapları ve tümleştirme hesabı yapılarına özgü şablon kaynak bilgileri için bkz. [Microsoft. Logic Resource Types](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>İş akışı tanımı ve parametreleri

Mantıksal uygulamanızın iş akışı tanımı, mantıksal uygulamanızın kaynak tanımının içindeki `properties` nesnesinde görüntülenen `definition` nesnesi içinde görüntülenir. Bu `definition` nesnesi, kod görünümünde görüntülenen ve [Iş akışı tanımlama dili Için şema başvurusu](../logic-apps/logic-apps-workflow-definition-language.md) konusunda tam olarak açıklanan nesnedir. İş akışı tanımınızda, çalışma zamanında iş akışı tanımınızda kullanılan değerler için yeni veya mevcut parametreleri düzenleme oluşturabileceğiniz bir iç `parameters` bildirim nesnesi vardır. Daha sonra bu parametrelere iş akışınızda tetikleyici veya Eylemler içinde başvurabilirsiniz. Mantıksal uygulamanız [yönetilen bağlayıcılar](../connectors/apis-list.md)aracılığıyla diğer hizmetlere ve sistemlere bağlantı oluşturmadığı için, varsayılan olarak bu `parameters` nesnesi boştur.

İş akışı tanımı parametrelerinin değerlerini ayarlamak için, iş akışı tanımınızın *dışında* , ancak mantıksal uygulamanızın kaynak *tanımında hala olan* `parameters` nesnesini kullanın. Bu dış `parameters` nesnesinde daha önce önceden tanımlanmış şablon parametrelerine başvurabilir, bu da bir parametre dosyasından dağıtım sırasında değerleri kabul edebilir.

> [!TIP]
>
> En iyi uygulama olarak, iş akışı tanımının içinden dağıtımda değerlendirilen şablon parametrelerine doğrudan başvurmayın. Bunun yerine, bir iş akışı tanımı parametresi bildirin, bu, iş akışı tanımınızın *dışında* , *ancak yine de* mantıksal uygulamanızın kaynak tanımında bulunan `parameters` nesnesi içinde ayarlayabilirler. Daha fazla bilgi için bkz. [parametrelere başvurular](#parameter-references).

Bu söz dizimi, şablon ve iş akışı Tanım parametrelerine başvurarak bu parametre değerlerini ayarlayabileceğiniz ve hem şablonda hem de iş akışı Tanım düzeylerindeki parametreleri nerede bildirebileceğiniz gösterilmektedir:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": "[parameters('<template-parameter-name>')]"
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Güvenli iş akışı Tanım parametreleri

Çalışma zamanında gizli bilgileri, parolaları, erişim anahtarlarını veya gizli dizileri işleyen bir iş akışı tanımı parametresi için `securestring` veya `secureobject` parametre türünü kullanmak üzere parametresini bildirin veya düzenleyin. Bu parametreye, iş akışı tanımınızın tamamında ve içinde başvurabilirsiniz. Şablonun en üst düzeyinde, bu bilgileri dağıtımda işlemek için aynı türe sahip bir parametre bildirin.

İş akışı tanımı parametresinin değerini ayarlamak için, iş akışı tanımınızın *dışında* olan `parameters` nesnesini kullanın, ancak mantıksal uygulama kaynak *tanımınızda hala şablon* parametresine başvuru yapın. Son olarak, dağıtım sırasında değeri şablon parametrıza geçirmek için bu değeri [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) depolayın ve dağıtım sırasında şablonunuz tarafından kullanılan [Parametreler dosyasında](#template-parameter-files) anahtar kasasının başvurusunu yapın.

Bu örnek şablon, değerlerini Azure Key Vault içinde depolayabilmeniz için gerektiğinde güvenli parametreleri tanımlayarak bu görevleri nasıl tamamlayakullanabileceğinizi gösterir:

* Erişimin kimliğini doğrulamak için kullanılan değerler için güvenli parametreler bildirin.
* Bu değerleri hem şablonda hem de iş akışı Tanım düzeylerinde kullanın.
* Bu değerleri parametreler dosyası kullanarak sağlayın.

**Şablon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Parametre dosyası**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>En iyi yöntemler-iş akışı Tanım parametreleri

Mantıksal uygulama Tasarımcısı 'nın iş akışı Tanım parametrelerini doğru bir şekilde gösterebilmesi için aşağıdaki en iyi yöntemleri izleyin:

* Hassas olan veya güvenli hale getirilmesi gereken değerler hariç tüm parametreler için boş değerler belirtebileceğiniz `defaultValue` özniteliğini ekleyin.

* Kullanıcı adları, parolalar ve gizli diziler için her zaman güvenli parametreleri kullanın. Hassas parametre değerlerini gizlemek veya korumak için bu konulardaki yönergeleri izleyin:

  * [Eylem parametreleri için güvenlik önerileri](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [İş akışı tanımlarında parametreler için güvenlik önerileri](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Azure Key Vault ile güvenli parametre değerlerini geçirme](../azure-resource-manager/resource-manager-keyvault-parameter.md)

İş akışı tanımı parametreleri hakkında daha fazla bilgi için bkz. [Parametreler-Iş akışı Tanım Dili](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Bağlantı kaynak tanımları

Mantıksal uygulamanız [yönetilen bağlayıcılar](../connectors/apis-list.md)kullanarak diğer hizmetlere ve sisteme bağlantı oluşturup kullandığında, şablonunuzun `resources` nesnesi bu bağlantılara ait kaynak tanımlarını içerir.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Bağlantı kaynak tanımları, şablonun en üst düzey parametrelerine başvurur, bu da bu değerleri bir parametreler dosyası kullanarak dağıtımda sağlayabilmeniz anlamına gelir. Bağlantıların, mantıksal uygulamanız ile aynı Azure kaynak grubunu ve konumunu kullandığınızdan emin olun.

İşte Office 365 Outlook bağlantısı ve ilgili şablon parametreleri için örnek bir kaynak tanımı aşağıda verilmiştir:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

Mantıksal uygulamanızın kaynak tanımı ayrıca bağlantı kaynağı tanımlarına şu yollarla da çalışmaktadır:

* İş akışı tanımınızda, `parameters` nesnesi, mantıksal uygulama çalışma zamanında kullanılacak bağlantı değerleri için bir `$connections` parametresi bildirir. Ayrıca, bir bağlantı oluşturan tetikleyici veya eylem, bu `$connections` parametresi aracılığıyla geçen karşılık gelen değerleri kullanır.

* İş akışı tanımınızın *dışında* , ancak mantıksal uygulamanızın kaynak tanımında hala *içinde* , başka bir `parameters` nesnesi, karşılık gelen şablon parametrelerine başvurarak `$connections` parametresi için çalışma zamanında kullanılacak değerleri ayarlar. Bu değerler, mantıksal uygulamanızdaki bağlantıların meta verilerini güvenli bir şekilde depolayan kaynaklara başvurmak için şablon ifadeleri kullanır.

  Örneğin, meta veriler, [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)depolayabileceğiniz bağlantı dizelerini ve erişim belirteçlerini içerebilir. Bu değerleri şablon parametrelerinizle geçirmek için, dağıtım sırasında şablonunuz tarafından kullanılan [Parametreler dosyasında](#template-parameter-files) bu anahtar kasasına başvurarak başvurabilirsiniz. Başvuru parametrelerinin farklılıkları hakkında daha fazla bilgi için, bu konunun ilerleyen kısımlarında [parametrelere başvurular](#parameter-references) bölümüne bakın.

  Mantıksal uygulamanızın iş akışı tanımını Azure portal veya Visual Studio aracılığıyla kod görünümünde açtığınızda, `$connections` nesnesi iş akışı tanımınızın dışında, ancak aynı düzeyde görüntülenir. Kod görünümündeki bu sıralama, iş akışı tanımını el ile güncelleştirdiğinizde bu parametreleri başvuruya daha kolay hale getirir:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Mantıksal uygulamanızın kaynak tanımında, mantıksal uygulamanız tarafından kullanılan bağlantıların bağımlılıklarını belirten bir `dependsOn` nesnesi vardır.

Oluşturduğunuz her bağlantının Azure 'da benzersiz bir adı vardır. Aynı hizmet veya sisteme birden fazla bağlantı oluşturduğunuzda, her bağlantı adına her bir yeni bağlantı (örneğin, `office365`, `office365-1`vb.) eklenerek bir sayı eklenir.

Bu örnekte, mantıksal uygulamanızın kaynak tanımı ve Office 365 Outlook için bir bağlantı kaynak tanımı arasındaki etkileşimler gösterilmektedir:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Güvenli bağlantı parametreleri

Gizli bilgileri, parolaları, erişim anahtarlarını veya gizli dizileri işleyen bir bağlantı parametresi için bağlantının kaynak tanımı, bu değerleri ad-değer çifti biçiminde belirten bir `parameterValues` nesnesi içerir. Bu bilgileri gizlemek için, `securestring` veya `secureobject` parametre türlerini kullanarak bu değerler için şablon parametreleri bildirebilir veya düzenleyebilirsiniz. Daha sonra bu bilgileri [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)kaydedebilirsiniz. Bu değerleri şablon parametrelerinizle geçirmek için, dağıtım sırasında şablonunuz tarafından kullanılan [Parametreler dosyasında](#template-parameter-files) bu anahtar kasasına başvurarak başvurabilirsiniz.

Bir Azure Blob depolama bağlantısı için hesap adı ve erişim anahtarı sağlayan bir örnek aşağıda verilmiştir:

**Parametre dosyası**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Şablon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Kimlik doğrulama bağlantıları

Dağıtımdan sonra mantıksal uygulamanız geçerli parametrelerle uçtan uca çalışacak. Ancak, [kimlik bilgilerinizi doğrulamak](../active-directory/develop/authentication-scenarios.md)için geçerli erişim belirteçleri oluşturmak üzere herhangi bir OAuth bağlantısını yetkilendirmelisiniz. Daha fazla bilgi için bkz. [OAuth bağlantılarını yetkilendirme](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Bazı bağlantılar [, Azure AD 'de kayıtlı](../active-directory/develop/quickstart-register-app.md)bir mantıksal uygulama için bağlantıları yetkilendirmek üzere bir Azure Active Directory (Azure AD) [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md) kullanmayı destekler. Örneğin, bu Azure Data Lake bağlantı kaynağı tanımı, hizmet sorumlusunun bilgilerini işleyen şablon parametrelerine nasıl başvurulacağını ve şablonun bu parametreleri nasıl bildirdiğini gösterir:

**Bağlantı kaynak tanımı**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Öznitelik | Açıklama |
|-----------|-------------|
| `token:clientId` | Hizmet sorumlusu ile ilişkili uygulama veya istemci KIMLIĞI |
| `token:clientSecret` | Hizmet sorumlusu ile ilişkili anahtar değeri |
| `token:TenantId` | Azure AD kiracınız için dizin KIMLIĞI |
| `token:grantType` | İstenen izin türü `client_credentials`olması gerekir. Daha fazla bilgi için bkz. [Microsoft Identity platform ve OAuth 2,0 istemci kimlik bilgileri akışı](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Şablon parametre tanımları**

Şablonun en üst düzey `parameters` nesnesi bu parametreleri örnek bağlantı için bildirir:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Hizmet sorumluları ile çalışma hakkında daha fazla bilgi için şu konulara bakın:

* [Azure portal kullanarak bir hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell kullanarak bir Azure hizmet sorumlusu oluşturma](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Azure PowerShell kullanarak sertifikayla hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Parametrelere başvurular

Şablon parametrelerine başvurmak için, dağıtım sırasında değerlendirilen şablon [işlevleriyle](../azure-resource-manager/resource-group-template-functions.md)şablon ifadeleri kullanabilirsiniz. Şablon ifadeleri köşeli parantezler ( **[]** ) kullanır:

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

İş akışı tanımı parametrelerine başvurmak için, çalışma zamanında değerlendirilen [Iş akışı tanımlama dili ifadeleri ve işlevleri](../logic-apps/workflow-definition-language-functions-reference.md)kullanılır. Bazı şablon işlevleri ve iş akışı tanımı işlevlerinin aynı ada sahip olduğunu fark edebilirsiniz. İş akışı tanımı ifadeleri "at" simgesiyle başlar ( **@** ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Mantıksal uygulamanızın çalışma zamanında kullanması için, şablon parametre değerlerini iş akışı tanımınıza geçirebilirsiniz. Ancak, mantıksal uygulama Tasarımcısı şablon öğelerini desteklemediğinden, iş akışı tanımınızda şablon parametrelerini, ifadeleri ve söz dizimini kullanmaktan kaçının. Ayrıca, ifadelerin değerlendirildiği zaman içindeki farklılıklar nedeniyle şablon söz dizimi ve ifadeler kodunuzu karmaşıklaştırabilir.

Bunun yerine, çalışma zamanında kullanmak üzere iş akışı Tanım parametrelerini bildirmek ve başvurmak, dağıtımda kullanılacak şablon parametrelerini bildirmek ve bunlara başvurmak ve bir parametreler dosyası kullanarak dağıtımda geçirilecek değerleri belirtmek için bu genel adımları izleyin. Tüm ayrıntılar için bu konunun önceki kısımlarında yer alarak [Iş akışı tanımı ve parametreleri](#workflow-definition-parameters) bölümüne bakın.

1. Şablonunuzu oluşturun ve dağıtımda kullanılacak değerler için şablon parametrelerini bildirin.

1. İş akışı tanımınızda, kabul edilecek ve çalışma zamanında kullanılacak değerlerin parametrelerini bildirin. Daha sonra bu değerlere, iş akışı tanımınızda ve içinde başvurabilirsiniz.

1. İş akışı tanımınızın *dışında* , ancak mantıksal uygulamanızın kaynak tanımında hala *içinde* olan `parameters` nesnesinde, karşılık gelen şablon parametrelerine başvurarak iş akışı Tanım parametrelerinizin değerlerini ayarlayın. Bu şekilde, şablon parametre değerlerini iş akışı Tanım parametrelerinizle geçirebilirsiniz.

1. Parametreler dosyasında, dağıtımda kullanılacak şablon değerlerini belirtin.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Tam örnek şablon

Bu konunun örnekleri tarafından kullanılan parametreli örnek şablon aşağıda verilmiştir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Mantıksal uygulama şablonları oluşturma](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)