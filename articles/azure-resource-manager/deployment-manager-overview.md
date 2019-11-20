---
title: Bölgeler arasında güvenli dağıtım-Azure Dağıtım Yöneticisi
description: Azure Deployment Manager ile pek çok bölge üzerinde bir hizmet dağıtmayı açıklar. Bu, tüm bölgelere sunulmadan önce dağıtımınızı kararlılığını doğrulamak için güvenli dağıtım uygulamalarını gösterir.
ms.topic: conceptual
ms.date: 05/31/2019
ms.custom: seodec18
ms.openlocfilehash: 0a0d13150086b393e10bc16c5c2cef173ab0abab
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147199"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Azure Dağıtım Yöneticisi ile güvenli dağıtım uygulamalarını etkinleştirme (Genel Önizleme)

Birçok bölgede hizmetinizi dağıtmadan ve her bölgede beklendiği gibi çalıştığından emin olmak için Azure Deployment Manager hizmetinin aşamalı bir sunum koordine etmek için kullanabilirsiniz. Tüm Azure dağıtımı için yaptığınız gibi kaynakları hizmetinizde tanımladığınız [Resource Manager şablonları](resource-group-authoring-templates.md). Şablonları oluşturduktan sonra Dağıtım Yöneticisi hizmetiniz için topoloji ve nasıl, alınması açıklamak için kullanın.

Dağıtım Yöneticisi Kaynak Yöneticisi'nin bir özelliktir. Bu, dağıtım sırasında yeteneklerinizi genişletir. Karmaşık bir servis olduğunda Dağıtım Yöneticisi'ni kullanın, çeşitli bölgelere dağıtılması gerekiyor. Hizmetinizi aşamalı kullanıma sunarak, tüm bölgelere dağıtılmadan önce olası sorunları bulabilirsiniz. Aşamalı dağıtım, ek güvenlik önlemleri gerekmiyorsa, standart kullanma [dağıtım seçenekleri](resource-group-template-deploy-portal.md) için Resource Manager. Dağıtım Yöneticisi, sürekli tümleştirme ve sürekli teslim (CI/CD) teklifleri gibi Resource Manager dağıtımlarını destekleyen tüm mevcut üçüncü taraf araçları sorunsuzca tümleştirilir.

Azure Dağıtım Yöneticisi önizlemededir. [Geri bildirim](https://aka.ms/admfeedback)sağlayarak özelliği geliştirmemize yardımcı olun.

Deployment Manager'ı kullanmak için dört dosyaları oluşturmak gerekir:

* Topoloji şablonu
* Dağıtım şablonu
* Topoloji için parametre dosyası
* Piyasaya çıkma için parametre dosyası

Topoloji şablon, dağıtım şablonu dağıtmadan önce dağıtın.

Ek kaynaklar:

- [Azure Dağıtım Yöneticisi REST API başvurusu](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Öğretici: Azure Dağıtım Yöneticisi kaynak yöneticisi şablonlarıyla kullanın](./deployment-manager-tutorial.md).
- [Öğretici: Azure dağıtım Yöneticisi 'da sistem durumu denetimi kullanın](./deployment-manager-tutorial-health-check.md).
- [Azure dağıtım Yöneticisi örneği](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Kimlik ve erişim

Dağıtım Yöneticisi ile bir [yönetilen kullanıcı tarafından atanan kimliği](../active-directory/managed-identities-azure-resources/overview.md) dağıtım eylemleri gerçekleştirir. Bu kimlik, dağıtımınıza başlamadan önce oluşturun. Bu abonelik için hizmet dağıtıyorsanız ve dağıtımı tamamlamak için yeterli izne erişiminiz olmalıdır. Rolleri verilen eylemler hakkında daha fazla bilgi için bkz: [Azure kaynakları için yerleşik roller](../role-based-access-control/built-in-roles.md).

Kimliğin dağıtım ile aynı konumda bulunması gerekir.

## <a name="topology-template"></a>Topoloji şablonu

Topoloji şablonu hizmetinizin ve nerede dağıtacağınızı oluşturan Azure kaynaklarını açıklar. Aşağıdaki görüntüde bir örnek hizmeti için topoloji gösterilmektedir:

![Hizmet topoloji bir hiyerarşiden hizmetlerine hizmet birimleri](./media/deployment-manager-overview/service-topology.png)

Topoloji şablon aşağıdaki kaynakları içermektedir:

* Yapıt kaynağı - Resource Manager şablonlarını ve parametre depolandığı
* Hizmet topolojisi - yapıt kaynağına işaret eder
  * Hizmetler - konum ve Azure abonelik kimliği belirtir
    * Hizmet birimi - kaynak grubu, dağıtım modu ve şablonu ve parametre dosyasının yolu belirtir

Her düzeyde neler olduğunu anlamak için sağladığınız hangi değerleri görmek yararlıdır.

![Her düzey değerleri](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Yapıt kaynağı için şablonlar

Topoloji şablonunda, şablon ve parametre dosyalarının bulunduğu bir yapıt kaynağı oluşturun. Yapıt kaynağı dağıtım için dosyaları çekmek için bir yoldur. Başka bir yapıt kaynağı için ikili dosyaları bu makalenin sonraki bölümlerinde görürsünüz.

Aşağıdaki örnek, genel yapıt kaynağı biçimini gösterir.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

Daha fazla bilgi için [artifactSources şablon başvurusu](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Hizmet topolojisi

Aşağıdaki örnek, service topolojisi kaynak genel biçimini gösterir. Şablonlarını ve parametre dosyalarını tutan yapıt kaynağının kaynak kimliği sağlayın. Hizmet topoloji tüm hizmet kaynakları içerir. Yapıt kaynağının kullanılabilir olduğundan emin olmak için üzerinde hizmet topolojiye bağlıdır.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

Daha fazla bilgi için [serviceTopologies şablon başvurusu](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Hizmetler

Aşağıdaki örnek, hizmetler kaynağın genel biçimini gösterir. Her hizmet içinde hizmeti dağıtmak için kullanılacak konumu ve Azure abonelik kimliği sağlayın. Birkaç bölgeye dağıtmak için her bölge için bir hizmet tanımlar. Hizmet hizmet topolojiye bağlıdır.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

Daha fazla bilgi için [şablon başvurusu Hizmetleri](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Hizmet Birimleri

Aşağıdaki örnek, hizmet birimi kaynağı genel biçimini gösterir. Kaynak grubunu, belirttiğiniz her hizmet birimi [dağıtım modu](deployment-modes.md) dağıtım ve şablonu ve parametre dosyasının yolu için kullanılacak. Şablon ve parametreleri için göreli bir yol belirtirseniz, yolun tamamı yapıt kaynağı kök klasöründeki oluşturulur. Şablon ve parametreleri için mutlak bir yol belirtebilirsiniz, ancak sürümlerinizi sürümüne bir kolayca özelliğini kaybedersiniz. Hizmet Birimi hizmete bağlıdır.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

Her şablon, tek bir adımda dağıtmak istediğiniz kaynaklar içermelidir. Örneğin, bir hizmet birimi dağıtan tüm kaynakların hizmetinizin ön uç için bir şablon olabilir.

Daha fazla bilgi için [serviceUnits şablon başvurusu](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Dağıtım şablonu

Dağıtım şablonu, hizmeti dağıtırken atılması gereken adımları açıklar. Hizmet topoloji kullanın ve hizmet birimi dağıtmak için sırasını tanımlamak için belirttiğiniz. Bu dağıtım için ikili dosyaları depolamak için bir yapıt kaynağı içerir. Piyasaya çıkma şablonunuzda aşağıdaki hiyerarşi tanımlayın:

* Yapıt kaynağı
* Adım
* Kullanıma sunma
  * Adım grupları
    * Dağıtım işlemlerini

Aşağıdaki görüntüde, dağıtım şablonu hiyerarşisini gösterir:

![Piyasaya çıkma bir hiyerarşiden adımları](./media/deployment-manager-overview/Rollout.png)

Her dağıtım, birçok adımı grupları olabilir. Her adım grubunun service topolojisi hizmet biriminde işaret eden bir dağıtım işlem vardır.

### <a name="artifact-source-for-binaries"></a>Yapıt kaynağı için ikili dosyaları

Dağıtım şablonu ve hizmeti dağıtmak için ihtiyacınız ikililer için yapıt kaynağı oluşturun. Bu yapıt kaynağı benzer [şablonları için yapıt kaynağı](#artifact-source-for-templates)dışında betikler, web sayfaları, derlenmiş kod veya hizmetiniz tarafından gereken diğer dosyaları içerir.

### <a name="steps"></a>Adımlar

Önce veya sonra dağıtım işlemi gerçekleştirmek için bir adım tanımlayabilirsiniz. Şu anda yalnızca `wait` adımı ve ' healthCheck ' adımı kullanılabilir.

Devam etmeden önce dağıtım bekleme adım duraklatır. Hizmetiniz bir sonraki hizmet birimi dağıtmadan önce beklendiği gibi çalıştığını doğrulamak sağlar. Aşağıdaki örnek, bir bekleme adımı genel biçimi gösterir.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Süresi özelliği kullanan [ISO 8601 standardına](https://en.wikipedia.org/wiki/ISO_8601#Durations). Yukarıdaki örnekte, bir dakikalık bekleme belirtir.

Sistem durumu denetimi adımı hakkında daha fazla bilgi için bkz. [Azure 'da sistem durumu tümleştirmesi dağıtımı dağıtım Yöneticisi](./deployment-manager-health-check.md) ve [öğretici: Azure dağıtım Yöneticisi 'da sistem durumu denetimi kullanma](./deployment-manager-tutorial-health-check.md).

Daha fazla bilgi için [adımları şablon başvurusu](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Piyasaya çıkarmalar

Yapıt kaynağının kullanılabilir olduğundan emin olmak için piyasaya çıkma üzerinde bağlıdır. Dağıtım adımları grupları dağıtılan her hizmet birimi tanımlar. Dağıtımdan önce veya sonra yapılacak işlemler tanımlayabilirsiniz. Örneğin, hizmet birimi dağıtıldıktan sonra dağıtımın bekleyin belirtebilirsiniz. Adım grupları sırasını tanımlayabilirsiniz.

Kimlik nesneyi belirtir [yönetilen kullanıcı tarafından atanan kimliği](#identity-and-access) , dağıtım eylemleri gerçekleştirir.

Aşağıdaki örnek, genel biçim dağıtımı gösterir.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

Daha fazla bilgi için [piyasaya çıkarma şablon başvurusu](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Parametre dosyası

İki parametre dosyası oluşturun. Bir parametre dosyası service topolojisi dağıtırken kullanılır ve diğer ürün dağıtımı için kullanılır. Bazı değerler emin olmanız gerekir, her iki parametre dosyaları aynı vardır.

## <a name="containerroot-variable"></a>containerRoot değişkeni

Tutulan dağıtımlarda, her yeni sürümle yapıtlarınızı yolunu değiştirir. İlk kez yolun bir dağıtım çalıştırdığınızda olabilir `https://<base-uri-blob-container>/binaries/1.0.0.0`. İkinci kez olabilir `https://<base-uri-blob-container>/binaries/1.0.0.1`. Dağıtım Yöneticisi'ni kullanarak geçerli dağıtım için doğru kök yolunu alma basitleştirir `$containerRoot` değişkeni. Bu değer, her sürümü ile değiştirir ve dağıtımdan önce bilinen değil.

Kullanım `$containerRoot` Azure kaynakları dağıtmak şablonu parametre dosyasında değişken. Dağıtım sırasında bu değişken, piyasaya çıkma gerçek değerlerle değiştirilir.

Örneğin, dağıtım sırasında ikili yapıtlar için yapıt kaynağı oluşturun.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

Bildirim `artifactRoot` ve `sasUri` özellikleri. Yapıt kök gibi bir değere ayarlanabilir `binaries/1.0.0.0`. SAS URI'sini erişim için bir SAS belirteci ile depolama kapsayıcınızda URI'dir. Dağıtım Yöneticisi, değeri otomatik olarak oluşturur `$containerRoot` değişkeni. Bu değerleri biçimde birleştirir `<container>/<artifactRoot>`.

Şablonu ve parametre dosyanız tutulan ikili dosyaları almak için doğru yolunu bilmeniz gerekir. Örneğin, bir web uygulaması için dosyaları dağıtmak için $containerRoot değişkenle şu parametre dosyası oluşturun. İki ters eğik çizgi kullanmanız gerekir (`\\`) yolu için ilk bir kaçış karakteri olduğundan.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

Ardından, bu parametre, şablonunuzda kullanın:

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

Yeni klasör oluşturma ve bu kök dizininde amaçlıyoruz geçirerek tutulan dağıtımları yönetin. Yolun kaynakları dağıtan şablonla aracılığıyla akar.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Deployment Manager hakkında bilgi edindiniz. Deployment Manager ile dağıtma hakkında bilgi edinmek için sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Öğretici: Azure Dağıtım Yöneticisi'ni kullanın Resource Manager şablonları ile](./deployment-manager-tutorial.md)