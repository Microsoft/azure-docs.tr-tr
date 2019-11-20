---
title: Azure Resource Manager ile uygulama ve hizmetleri dağıtma ve yükseltme | Microsoft Docs
description: Azure Resource Manager şablonu kullanarak bir Service Fabric kümesine uygulamalar ve hizmetler dağıtmayı öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: atsenthi
ms.openlocfilehash: 0bec430cbb98452f8c852c96053f3f699ce5098e
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71153581"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Uygulamaları ve Hizmetleri Azure Resource Manager kaynak olarak yönetme

Uygulama ve hizmetlerinizi Service Fabric kümesine Azure Resource Manager üzerinden dağıtabilirsiniz. Bu, kümenin hazır olmasını beklemek zorunda kalmadan PowerShell veya CLı aracılığıyla uygulama dağıtmak ve yönetmek yerine, artık JSON 'daki uygulama ve Hizmetleri ifade edebilir ve bunları kümeniz ile aynı Kaynak Yöneticisi şablonunda dağıtabilirsiniz. Uygulama kaydetme, hazırlama ve dağıtma işlemlerinin tümü tek bir adımda gerçekleşir.

Bu, kümenizde gerekli olan kurulum, idare veya küme yönetimi uygulamalarını dağıtmanız için önerilen yoldur. Bu, diğer uygulama veya hizmetler dağıtılmadan önce kümenizde çalıştırılması gereken [Düzeltme Eki düzenleme uygulamasını](service-fabric-patch-orchestration-application.md), Watchdogs veya herhangi bir uygulamayı içerir. 

Uygun olduğunda, uygulamalarınızı geliştirmek için Kaynak Yöneticisi kaynak olarak yönetin:
* Denetim izi: Kaynak Yöneticisi her işlemi denetler ve bu uygulamalarda ve kümeniz üzerinde yapılan değişiklikleri izlemenize yardımcı olabilecek ayrıntılı bir *etkinlik günlüğü* tutar.
* Rol tabanlı erişim denetimi (RBAC): Kümeye erişimin yanı sıra küme üzerinde dağıtılan uygulamaların de aynı Kaynak Yöneticisi şablonu aracılığıyla yapılması yapılabilir.
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
3. Bu şekilde hangi uygulamaları dağıtacağınızı iletişime, uygulamaların paketlenmesi, sıkıştırılması ve bir dosya paylaşımında bulunması gerekir. Dağıtım sırasında kullanmak üzere Azure Resource Manager için bir REST uç noktası aracılığıyla paylaşıma erişilebilir olması gerekir.
4. Kaynak Yöneticisi şablonunuzda, küme bildirimenizle, her uygulamanın özelliklerini betimleyen. Bu özellikler, çoğaltma veya örnek sayısını ve kaynaklar (diğer uygulamalar veya hizmetler) arasında herhangi bir bağımlılık zincirlerini içerir. Kapsamlı özelliklerin listesi için bkz. [Swagger Spec REST API](https://aka.ms/sfrpswaggerspec). Bunun uygulama veya hizmet bildirimlerinin yerini almaz, ancak bunun yerine, kümenin Kaynak Yöneticisi şablonunun bir parçası olarak bunların bazı özellikleri açıklanmıştır. Aşağıda, Service1 bir parçası olarak durum bilgisi olmayan bir Service ve durum bilgisi olan bir Service *Service2* dağıtmanın bir örnek şablonu *verilmiştir:*

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
   > *Apiversion* , olarak `"2019-03-01"`ayarlanmalıdır. Bu şablon, küme zaten dağıtıldığı sürece kümeden bağımsız olarak da dağıtılabilir.

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

Kümeniz zaten çalışır durumda ve Kaynak Yöneticisi Kaynakları olarak yönetmek istediğiniz bazı uygulamalar üzerinde zaten dağıtıldıysa, uygulamaları kaldırmak ve yeniden dağıtmak yerine, uygulamaların alması için aynı API 'Leri kullanarak bir PUT çağrısı kullanabilirsiniz Kaynak Yöneticisi kaynaklar olarak kabul edildi. Daha fazla bilgi için [Service Fabric uygulama kaynak modeli nedir?](https://docs.microsoft.com/azure/service-fabric/service-fabric-concept-resource-model) bölümüne bakın.

> [!NOTE]
> Bir küme yükseltmesinin sağlıksız uygulamaları yoksaymasına izin vermek için, müşteri "Maxyüztunhealthyapplications" belirtebilir: 100 "," upgradeDescription/healthPolicy "bölümünde; tüm ayarların ayrıntılı açıklamaları, [Service yapılar REST API küme yükseltme ilkesi belgelerinde](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)bulunur.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer uygulamaları kümenize dağıtmak için [SERVICE fabrıc CLI](service-fabric-cli.md) veya [PowerShell](service-fabric-deploy-remove-applications.md) kullanın. 
* [Service Fabric kümenizi yükseltme](service-fabric-cluster-upgrade.md)

