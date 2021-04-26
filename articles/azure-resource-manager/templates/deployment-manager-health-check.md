---
title: Durum tümleştirme dağıtımı-Azure Deployment Manager
description: Azure Deployment Manager ile birçok bölge üzerinden bir hizmetin nasıl dağıtılacağını açıklar. Tüm bölgelere geçmeden önce dağıtımınızın kararlılığını doğrulamak üzere güvenli dağıtım uygulamalarını gösterir.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99536976"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Azure Deployment Manager durum tümleştirmesi dağıtımını tanıtma (Genel Önizleme)

[Azure Deployment Manager](./deployment-manager-overview.md) , Azure Resource Manager kaynakları hazırlanan piyasaya çıkarma kaynaklarını gerçekleştirmenize olanak tanır. Kaynaklar bölge tarafından bölgeye göre düzenli bir biçimde dağıtılır. Azure Deployment Manager tümleşik sistem durumu denetimi piyasaya çıkarma izleyebilir ve sorun gidermek ve etki alanının ölçeğini azaltmak için sorunlu piyasaya çıkarma 'yi otomatik olarak durdurabilir. Bu özellik, güncelleştirmelerde gerilemeler nedeniyle oluşan hizmet kullanılamamasını azaltabilir.

## <a name="health-monitoring-providers"></a>Sistem durumu izleme sağlayıcıları

Sistem durumu tümleştirmesini mümkün olduğunca kolay hale getirmek için, Microsoft, en önemli hizmet durumu izleme şirketleriyle birlikte çalışarak dağıtımlarınızla durum denetimlerini tümleştirmek üzere basit bir kopyalama/yapıştırma çözümü sağlar. Zaten bir sistem durumu İzleyicisi kullanmıyorsanız, bunlar şu şekilde başlamak için harika bir çözümdür:

| ![Azure Deployment Manager sistem durumu İzleyicisi sağlayıcısı Azure izleyici](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![Azure Deployment Manager sistem durumu İzleyicisi sağlayıcısı veri köpek](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Azure Deployment Manager sistem durumu İzleyicisi sağlayıcısı site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Azure Deployment Manager sistem durumu İzleyicisi sağlayıcısı dalga ön](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Izleyici, Microsoft 'un bulut Yerel & karma izleme ve analiz için tam Stack Observability platformudur. |Modern bulut ortamları için önde gelen izleme ve analiz platformu olan dataköpek. [Dataköpek 'Nın Azure Deployment Manager ile nasıl tümleştiğini](https://www.datadoghq.com/azure-deployment-manager/)görün.|Site24x7, hepsi bir arada özel ve genel bulut hizmetleri izleme çözümü. [Site24x7 'In Azure Deployment Manager ile nasıl tümleştiğini](https://www.site24x7.com/azure/adm.html)görün.| Dalga ön, çok bulut uygulama ortamları için izleme ve analiz platformu. [Wavefront 'ın Azure Deployment Manager ile nasıl tümleştiğini](https://go.wavefront.com/wavefront-adm/)görün.|

## <a name="how-service-health-is-determined"></a>Hizmet durumu nasıl belirlenir

[Sistem durumu izleme sağlayıcıları](#health-monitoring-providers) Hizmetleri izlemek için çeşitli mekanizmalar sunar ve hizmet durumu sorunlarından dolayı sizi uyarır. [Azure izleyici](../../azure-monitor/overview.md) , bu tür bir teklife bir örnektir. Azure Izleyici, belirli eşikler aşıldığında uyarı oluşturmak için kullanılabilir. Örneğin, hizmetinize yeni bir güncelleştirme dağıttığınızda bellek ve CPU kullanımı beklenen düzeylerin ötesine geçmiş. Bildirim geldiğinde, düzeltici eylemler gerçekleştirebilirsiniz.

Bu sistem durumu sağlayıcıları genellikle REST API 'Leri sunar, böylece hizmet izleyicilerinin durumu programlı bir şekilde incelenebilir. REST API 'Leri, basit bir sağlıklı/sağlıksız sinyalle (HTTP yanıt kodu tarafından belirlenir) ve/veya aldığı sinyallerle ilgili ayrıntılı bilgilerle geri dönebilir.

`healthCheck`Azure Deployment Manager 'daki yeni adım, sağlıklı bir hizmeti gösteren http kodları bildirmenize olanak tanır. Karmaşık REST sonuçları için, eşleştirildiği zaman sağlıklı bir yanıt belirten normal ifadeler belirtebilirsiniz.

Azure Deployment Manager durum denetimlerini ayarlama akışı:

1. İstediğiniz bir sistem sağlığı hizmeti sağlayıcısı aracılığıyla sistem durumu izleyicilerini oluşturun.
1. `healthCheck`Azure Deployment Manager piyasaya çıkın bir parçası olarak bir veya daha fazla adım oluşturun. `healthCheck`Aşağıdaki bilgilerle adımları doldurun:

    1. Sistem sağlığı izleyiclarınız için REST API URI 'SI (sistem sağlığı hizmeti sağlayıcınız tarafından tanımlanan şekilde).
    1. Kimlik doğrulama bilgileri. Şu anda yalnızca API anahtar stili kimlik doğrulaması destekleniyor. Azure Izleyici için, Azure `RolloutIdentity` Deployment Manager dağıtımı için kullanılan Kullanıcı tarafından atanan yönetilen kimlik Azure izleyici 'yi genişlettiğinden, kimlik doğrulama türü ayarlanmalıdır.
    1. Sağlıklı bir yanıt tanımlayan [http durum kodları](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) veya normal ifadeler. Yanıtın sağlıklı olarak değerlendirilmesi için her birinin eşleşmesi gereken normal ifadeler sağlayabilir veya yanıtın sağlıklı olarak kabul edilmesi için herhangi birinin eşleşmesi gereken ifadeler sağlayabilirsiniz. Her iki yöntem de desteklenir.

    Aşağıdaki JSON Azure Izleyici 'yi Azure Deployment Manager tümleştirmede bir örnektir. Örnek kullanır `RolloutIdentity` ve uyarı yoksa bir dağıtımı devam eden bir sistem durumu denetimi oluşturur. Desteklenen tek Azure Izleyici API: [Uyarılar – tümünü al](/rest/api/monitor/alertsmanagement/alerts/getall).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    Aşağıdaki JSON tüm diğer sistem durumu izleme sağlayıcıları için bir örnektir:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. `healthCheck`Azure Deployment Manager dağıtımı için uygun zamanda adımları çağırın. Aşağıdaki örnekte, `healthCheck` içinde bir adım çağrılır `postDeploymentSteps` `stepGroup2` .

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Örnek adım adım yönergeler için bkz. [öğretici: Azure Deployment Manager sistem durumu denetimi kullanma](./deployment-manager-tutorial-health-check.md).

## <a name="phases-of-a-health-check"></a>Bir sistem durumu denetiminin aşamaları

Bu noktada Azure Deployment Manager, hizmetinizin sistem durumunu ve hangi aşamalarda bunu yapmak için nasıl sorgu yapılacağını bilir. Ancak Azure Deployment Manager, bu denetimlerin zamanlamasını ayrıntılı olarak yapılandırmaya de olanak tanır. Bir `healthCheck` adım, tümü yapılandırılabilir süreleri olan üç sıralı aşamada yürütülür:

1. Wait

    1. Bir dağıtım işlemi tamamlandıktan sonra, VM 'Ler yeniden başlatılabilir, yeni verilere göre yeniden yapılandırılabilir, hatta ilk kez başlatılmış olabilir. Ayrıca, hizmetlerin sistem durumu izleme sağlayıcısı tarafından, yararlı bir şeye toplanacak durum sinyallerini yayarak başlatılması zaman alır. Bu döner işlem sırasında, güncelleştirme henüz sabit bir duruma ulaşılmadığından hizmet durumunu denetlemek mantıklı olmayabilir. Gerçekte, hizmet, kaynaklar tarafından beklendiği gibi sağlıklı ve sağlıksız durumlar arasında salını olabilir.
    1. Bekleme aşamasında, hizmet durumu izlenmiyor. Bu, dağıtılan kaynaklara sistem durumu denetimi işlemine başlamadan önce bakma süresi vermek için kullanılır.

1. Tik

    1. Kaynaklar kararlı hale gelmeden önce ne kadar süreceğiyle ilgili tüm durumlarda, elastik bir süre, kaynakların kararsız durumda olma süresi arasında esnek bir süre sağlar ve sağlıklı bir durum yaşlıyordu.
    1. Elastik aşama başladığında Azure Deployment Manager, hizmet durumu için belirtilen REST uç noktasını düzenli aralıklarla yoklamaya başlar. Yoklama aralığı yapılandırılabilir.
    1. Sistem durumu İzleyicisi, hizmetin sağlıksız olduğunu belirten sinyallerle geri dönerse, bu sinyaller yok sayılır, elastik aşama devam eder ve yoklamaya devam eder.
    1. Sistem durumu İzleyicisi, hizmetin sağlıklı olduğunu belirten sinyalleri döndürdüğünde, elastik aşama sonlanır ve Healthi durum aşaması başlar.
    1. Bu nedenle, elastik bir yanıt zorunlu kabul edilmeden önce hizmet durumunu yoklamaya harcanan en uzun süre (esnek aşama için belirtilen süre).

1. Healthyıstate

    1. Healthi durum aşamasında, hizmet durumu sürekli olarak esnek aşamayla aynı aralığa göre yoklanır.
    1. Hizmetin, belirtilen sürenin tamamına yönelik sistem durumu izleme sağlayıcısından sağlıklı sinyalleri tutması beklenir.
    1. Herhangi bir noktada sağlıksız bir yanıt algılanırsa Azure Deployment Manager, tüm dağıtımı durdurur ve sağlıksız hizmet sinyallerini taşıyan REST yanıtını döndürür.
    1. Healthi durum süresi sona erdikten sonra, `healthCheck` tamamlanır ve dağıtım bir sonraki adımla devam eder.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Deployment Manager sistem durumu izlemenin nasıl tümleştirileceğini öğrendiniz. Deployment Manager ile dağıtmayı öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: Azure Deployment Manager sistem durumu denetimi kullanma](./deployment-manager-tutorial-health-check.md)
