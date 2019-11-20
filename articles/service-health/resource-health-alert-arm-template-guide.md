---
title: Kaynak Yöneticisi şablonları kullanarak Azure Kaynak durumu uyarılarını yapılandırma | Microsoft Docs
description: Azure kaynaklarınızın kullanım dışı olmasına göre size bildirimde bulunan uyarıları programlı bir şekilde oluşturun.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 9/4/2018
ms.openlocfilehash: 10a940e43b2ab4dff1b7c90aa7d6d274ddef82d9
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023907"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Kaynak Yöneticisi şablonları kullanarak kaynak sistem durumu uyarılarını yapılandırma

Bu makalede, Azure Resource Manager şablonlarını ve Azure PowerShell kullanarak programlı olarak Kaynak Durumu etkinlik günlüğü uyarılarını nasıl oluşturacağınız gösterilmektedir.

Azure Kaynak Durumu, Azure kaynaklarınızın geçerli ve geçmiş sistem durumu hakkında bilgi sahibi olmanızı önler. Azure Kaynak Durumu uyarılar, bu kaynakların sistem durumunda bir değişiklik olduğunda sizi neredeyse gerçek zamanlı olarak uyarabilirler. Kaynak Durumu uyarılar oluşturma, kullanıcıların uyarıları toplu olarak oluşturmalarına ve özelleştirmesine izin verir.

> [!NOTE]
> Kaynak Durumu uyarılar Şu anda önizleme aşamasındadır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu sayfadaki yönergeleri izlemek için, önceden birkaç şey ayarlamanız gerekir:

1. [Azure PowerShell modülünü](https://docs.microsoft.com/powershell/azure/install-Az-ps) yüklemeniz gerekiyor
2. Sizi bilgilendirmek için yapılandırılmış [bir eylem grubu oluşturmanız veya yeniden kullanmanız](../azure-monitor/platform/action-groups.md) gerekir

## <a name="instructions"></a>Yönergeler
1. PowerShell 'i kullanarak hesabınızı kullanarak Azure 'da oturum açın ve etkileşimde bulunmak istediğiniz aboneliği seçin

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Erişiminiz olan abonelikleri listelemek için `Get-AzSubscription` kullanabilirsiniz.

2. Eylem grubunuz için tam Azure Resource Manager KIMLIĞINI bulun ve kaydedin

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Kaynak Durumu Uyarıları için `resourcehealthalert.json` olarak Kaynak Yöneticisi şablonu oluşturun ve kaydedin ([aşağıdaki ayrıntılara bakın](#resource-manager-template-options-for-resource-health-alerts))

4. Bu şablonu kullanarak yeni bir Azure Resource Manager dağıtımı oluşturun

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Daha önce kopyaladığınız uyarı adını ve eylem grubu kaynak KIMLIĞINI yazmanız istenir:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Her şey başarıyla çalıştıysa, PowerShell 'de bir onay alacaksınız

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Bu işlemi tamamen otomatikleştirmeye planlarken, 5. adımdaki değerleri sorması için yalnızca Kaynak Yöneticisi şablonunu düzenlemeniz gerektiğini unutmayın.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Kaynak Durumu Uyarıları için Kaynak Yöneticisi şablonu seçenekleri

Bu temel şablonu, Kaynak Durumu uyarı oluşturmak için başlangıç noktası olarak kullanabilirsiniz. Bu şablon yazıldığı gibi çalışacaktır ve bir abonelikteki tüm kaynaklar genelinde yeni etkinleştirilen tüm kaynak sistem durumu olayları için uyarı almak üzere oturumunuzu açacaktır.

> Bu makalenin en altında, bu şablonla karşılaştırıldığında Kaynak Durumu Uyarıları için gürültü oranına yönelik sinyali artırması gereken daha karmaşık bir uyarı şablonu da sunuyoruz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Ancak, bunun gibi geniş bir uyarı genellikle önerilmez. Bu uyarıyı aşağıda ele aldığımız olaylara odaklanmak için nasıl kapsam yapabileceğinizi öğrenin.

### <a name="adjusting-the-alert-scope"></a>Uyarı kapsamını ayarlama

Kaynak Durumu uyarılar, olayları üç farklı kapsam üzerinde izlemek için yapılandırılabilir:

 * Abonelik Düzeyi
 * Kaynak grubu düzeyi
 * Kaynak düzeyi

Uyarı şablonu abonelik düzeyinde yapılandırılır, ancak uyarınızı yalnızca belirli kaynaklar veya belirli bir kaynak grubundaki kaynaklar hakkında bilgilendirecek şekilde yapılandırmak istiyorsanız Yukarıdaki şablonda `scopes` bölümünü değiştirmeniz yeterlidir.

Kaynak grubu düzeyi kapsamı için kapsamlar bölümü şöyle görünmelidir:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

Kaynak düzeyi kapsamı için kapsam bölümü şöyle görünmelidir:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Örneğin, `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Azure portalına giderek bu dizeyi almak için Azure kaynağınız görüntülenirken URL 'ye bakabilirsiniz.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Sizi uyaran kaynak türlerini ayarlama

Abonelik veya kaynak grubu düzeyindeki uyarıların farklı türlerde kaynakları olabilir. Yalnızca belirli bir kaynak türü alt kümesinden gelen uyarıları sınırlamak istiyorsanız, bunu şöyle olacak şekilde şablonun `condition` bölümünde tanımlayabilirsiniz:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Burada, kaynak sistem durumu uyarısının belirlediğimiz koşullardan herhangi biriyle eşleşmesini sağlamak için `anyOf` sarmalayıcı kullanılır ve belirli kaynak türlerini hedefleyen uyarılara izin verilir.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Sizi uyaran Kaynak Durumu olaylarını ayarlama
Kaynaklar bir sistem durumu olayına sahip olduğunda, sistem durumu olayının durumunu temsil eden bir dizi aşamadan geçebilir: `Active`, `In Progress`, `Updated`ve `Resolved`.

Yalnızca bir kaynak sağlıksız hale geldiğinde bildirim almak isteyebilirsiniz, bu durumda uyarınızı yalnızca `status` `Active`olduğunda bilgilendirecek şekilde yapılandırmak isteyebilirsiniz. Bununla birlikte, diğer aşamalar üzerinde de bildirim almak istiyorsanız bu ayrıntıları şöyle ekleyebilirsiniz:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Tüm dört sistem durumu olayı aşaması için bildirim almak istiyorsanız, bu koşulu tümü birlikte kaldırabilirsiniz ve uyarı `status` özelliğinden bağımsız olarak sizi uyarır.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>"Bilinmeyen" olayları önlemek için Kaynak Durumu uyarılarını ayarlama

Azure Kaynak Durumu, test çalıştıranlar kullanarak onları sürekli izleyerek kaynaklarınızın en son durumunu rapor edebilir. Bildirilen ilgili sistem durumu durumları şunlardır: "kullanılabilir", "kullanılamıyor" ve "düşürülmüş". Ancak, Çalıştırıcısı ve Azure kaynağının iletişim kurmadığı durumlarda, kaynak için "Bilinmeyen" bir sistem durumu bildirilir ve bu durum "etkin" bir sistem durumu olayı olarak kabul edilir.

Ancak, bir kaynak "bilinmiyor" olarak raporladığında, büyük olasılıkla sistem durumu en son doğru rapordan bu yana değişmemiştir. "Bilinmeyen" olaylarda uyarıları kaldırmak istiyorsanız, bu mantığı şablonda belirtebilirsiniz:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

Bu örnekte, yalnızca geçerli ve önceki sistem durumunun "Unknown" olmadığı olaylara bildirimde bulunur. Bu değişiklik, uyarılarınız doğrudan cep telefonunuza veya e-postaya gönderiliyorsa yararlı bir ek olabilir. 

CurrentHealthStatus ve previousHealthStatus özelliklerinin bazı olaylarda null olması mümkün olduğunu unutmayın. Örneğin, güncelleştirilmiş bir olay gerçekleştiğinde kaynağın sistem durumunun son rapordan bu yana değişmediğinden, yalnızca ilgili ek olay bilgileri kullanılabilir (ör. nedeni). Bu nedenle, yukarıdaki yan tümcesinin kullanılması bazı uyarıların tetiklenmesi nedeniyle, Properties. currentHealthStatus ve Properties. previousHealthStatus değerleri null olarak ayarlanacak.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Kullanıcı tarafından başlatılan olayların oluşmasını önlemek için uyarıyı ayarlama

Kaynak Durumu olaylar, platform tarafından başlatılan ve Kullanıcı tarafından başlatılan olaylar tarafından tetiklenebilir. Yalnızca Azure platformunun sistem durumu olayına neden olduğunda bir bildirim gönderilmesi anlamlı olabilir.

Bu olay türlerini filtrelemek için uyarınızı kolayca yapılandırmak kolaydır:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Nedeni alanının bazı olaylarda null olması mümkün olduğunu unutmayın. Diğer bir deyişle, bir sistem durumu geçişi gerçekleşir (örneğin, kullanılamaz olarak kullanılabilir) ve olay bildirim gecikmelerini engellemek için hemen günlüğe kaydedilir. Bu nedenle, yukarıdaki yan tümceyi kullanmak bir uyarının tetiklenmediği için, Properties. Clause özelliği değeri null olarak ayarlanacak.

## <a name="complete-resource-health-alert-template"></a>Kaynak Durumu uyarı şablonunu doldurun

Önceki bölümde açıklanan farklı ayarlamaları kullanarak, gürültü oranına sinyali en üst düzeye çıkarmak için yapılandırılmış örnek bir şablon aşağıda verilmiştir. Yukarıda belirtilen uyarıları, bazı olaylarda currentHealthStatus, previousHealthStatus ve neden özellik değerlerinin null olabileceğini göz önünde bulundurun.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Bununla birlikte, en iyi yapılandırmaların sizin için geçerli olduğunu bilirsiniz, bu nedenle kendi özelleştirmenizi yapmak için bu belgede size olan araçları kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Kaynak Durumu hakkında daha fazla bilgi edinin:
-  [Azure Kaynak Durumu genel bakış](Resource-health-overview.md)
-  [Azure Kaynak Durumu aracılığıyla kullanılabilen kaynak türleri ve durum denetimleri](resource-health-checks-resource-types.md)


Hizmet durumu uyarıları oluşturma:
-  [Hizmet durumu için uyarıları yapılandırma](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Azure etkinlik günlüğü olay şeması](../azure-monitor/platform/activity-log-schema.md)
