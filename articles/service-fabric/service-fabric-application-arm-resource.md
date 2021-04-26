---
title: Azure Resource Manager dağıtma ve yükseltme
description: Azure Resource Manager şablonu kullanarak bir Service Fabric kümesine uygulamalar ve hizmetler dağıtmayı öğrenin.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ed6bc7d96cb3ea0934929e6543c5e637a9f42c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97930846"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Uygulamaları ve Hizmetleri Azure Resource Manager kaynak olarak yönetme

Uygulamaları ve Hizmetleri, Service Fabric kümenize Azure Resource Manager aracılığıyla dağıtabilirsiniz. Bu, kümenin hazır olmasını beklemek zorunda kalmadan PowerShell veya CLı aracılığıyla uygulama dağıtmak ve yönetmek yerine, artık JSON 'daki uygulama ve Hizmetleri ifade edebilir ve bunları kümeniz ile aynı Kaynak Yöneticisi şablonunda dağıtabilirsiniz. Uygulama kaydı, sağlama ve dağıtım işlemleri tek bir adımda gerçekleşir.

Bu, kümenizde gerekli olan kurulum, idare veya küme yönetimi uygulamalarını dağıtmanız için önerilen yoldur. Bu, diğer uygulama veya hizmetler dağıtılmadan önce kümenizde çalıştırılması gereken [Düzeltme Eki düzenleme uygulamasını](service-fabric-patch-orchestration-application.md), Watchdogs veya herhangi bir uygulamayı içerir. 

Uygun olduğunda, uygulamalarınızı geliştirmek için Kaynak Yöneticisi kaynak olarak yönetin:
* Denetim izi: Kaynak Yöneticisi her işlemi denetler ve bu uygulamalarda ve kümeniz üzerinde yapılan değişiklikleri izlemenize yardımcı olabilecek ayrıntılı bir *etkinlik günlüğü* tutar.
* Azure rol tabanlı erişim denetimi (Azure RBAC): kümelere erişimin yanı sıra kümede dağıtılan uygulamaların de aynı Kaynak Yöneticisi şablonu aracılığıyla yapılması yapılabilir.
* Azure Resource Manager (Azure portal aracılığıyla), kümenizin ve kritik uygulama dağıtımlarınızın yönetilmesi için tek bir durdurulmalı bir mağaza haline gelir.

Aşağıdaki kod parçacığında, bir şablon aracılığıyla yönetilebilen farklı kaynak türleri gösterilmektedir:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

## <a name="add-a-new-application-to-your-resource-manager-template"></a>Kaynak Yöneticisi şablonunuza yeni bir uygulama ekleme

1. Kümenizin Kaynak Yöneticisi şablonunu dağıtım için hazırlayın. Hakkında daha fazla bilgi için bkz. [Azure Resource Manager kullanarak Service Fabric kümesi oluşturma](service-fabric-cluster-creation-via-arm.md) .
2. Kümede dağıtımı planladığınız bazı uygulamaları düşünün. Diğer uygulamaların bağımlılığı olabilecek her zaman çalışacak mı? Tüm küme idare veya kurulum uygulamalarını dağıtmaya mi planlıyorsunuz? Bu tür uygulamalar yukarıda anlatıldığı gibi Kaynak Yöneticisi şablonu aracılığıyla en iyi şekilde yönetilir. 
3. Bu şekilde hangi uygulamaları dağıtacağınızı iletişime, uygulamaların paketlenmesi, sıkıştırılması ve bir depolama paylaşımında yerleştirilmesi gerekir. Dağıtım sırasında kullanmak üzere Azure Resource Manager için bir REST uç noktası aracılığıyla paylaşıma erişilebilir olması gerekir. Ayrıntılar için bkz. [depolama hesabı oluşturma](service-fabric-concept-resource-model.md#create-a-storage-account) .
4. Kaynak Yöneticisi şablonunuzda, küme bildirimenizle, her uygulamanın özelliklerini betimleyen. Bu özellikler, çoğaltma veya örnek sayısını ve kaynaklar (diğer uygulamalar veya hizmetler) arasında herhangi bir bağımlılık zincirlerini içerir. Bunun uygulama veya hizmet bildirimlerinin yerini almaz, ancak bunun yerine, kümenin Kaynak Yöneticisi şablonunun bir parçası olarak bunların bazı özellikleri açıklanmıştır. Aşağıda, Service1 bir parçası olarak durum bilgisi olmayan bir Service  ve durum bilgisi olan bir Service *Service2* dağıtmanın bir örnek şablonu *verilmiştir:*

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > Tek tek şablon özellikleriyle ilgili kullanımı ve ayrıntıları bulmak için Service Fabric [Azure Resource Manager başvurusuna](/azure/templates/microsoft.servicefabric/clusters/applicationtypes) bakın.

5. Dağıtımı! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Service Fabric kaynak sağlayıcısı uygulama kaynağını kaldır
Aşağıdakiler, uygulama paketinin kümeden sağlanması için tetiklenecek ve bu, kullanılan disk alanını temizleyecektir:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
ARM şablonunuzun Microsoft. ServiceFabric/kümeleri/uygulamasını kaldırmak, uygulamanın sağlamasını kaldıramayacak

>[!NOTE]
> Kaldırma işlemi tamamlandıktan sonra, paket sürümünü SFX veya ARM 'de görmemelisiniz. Uygulamanın çalıştığı uygulama türü sürüm kaynağını silemezsiniz; ARM/SFRP bunu engelleyecek. Çalışan paketin sağlamasını kaldırmayı denerseniz, SF çalışma zamanı bunu engelleyecek.


## <a name="manage-an-existing-application-via-resource-manager"></a>Mevcut bir uygulamayı Kaynak Yöneticisi aracılığıyla yönetme

Kümeniz zaten çalışır durumda ve Kaynak Yöneticisi Kaynakları olarak yönetmek istediğiniz bazı uygulamalar üzerinde zaten dağıtılmışsa, uygulamaları kaldırmak ve yeniden dağıtmak yerine, uygulamaların Kaynak Yöneticisi kaynakları kabul etmek için aynı API 'Leri kullanarak bir PUT çağrısı kullanabilirsiniz. Daha fazla bilgi için [Service Fabric uygulama kaynak modeli nedir?](./service-fabric-concept-resource-model.md) bölümüne bakın.

> [!NOTE]
> Bir küme yükseltmesinin sağlıksız uygulamaları yok saymasına izin vermek için, "upgradeDescription/healthPolicy" bölümünde "Maxyüztunhealthyapplications: 100" belirtebilir; tüm ayarların ayrıntılı açıklamaları, [Service yapılar REST API küme yükseltme ilkesi belgelerinde](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)bulunur.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer uygulamaları kümenize dağıtmak için [SERVICE fabrıc CLI](service-fabric-cli.md) veya [PowerShell](service-fabric-deploy-remove-applications.md) kullanın. 
* [Service Fabric kümenizi yükseltme](service-fabric-cluster-upgrade.md)
