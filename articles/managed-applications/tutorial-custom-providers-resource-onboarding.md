---
title: Azure özel sağlayıcılarıyla kaynak ekleme
description: Özel sağlayıcılar aracılığıyla kaynak ekleme, mevcut Azure kaynaklarını yönetmenize ve genişletmenize olanak tanır.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 45086987d5ba3a619028ced798712f2870c6d487
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826795"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Öğretici: Azure özel sağlayıcılarıyla kaynak ekleme

Bu öğreticide, Microsoft. CustomProviders/Association kaynak türü ile Azure Resource Manager API 'yi genişleten özel bir kaynak sağlayıcısı Azure 'a dağıtırsınız. Öğreticide, özel sağlayıcı örneğinin bulunduğu kaynak grubunun dışındaki mevcut kaynakların nasıl genişletileceği gösterilmektedir. Bu öğreticide, özel kaynak sağlayıcısı bir Azure Logic App tarafından desteklenir, ancak herhangi bir genel API uç noktasını kullanabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlayabilmeniz için şunları bilmeniz gerekir:

* [Azure özel sağlayıcılarının](custom-providers-overview.md)özellikleri.
* [Özel sağlayıcılarla kaynak ekleme](concepts-custom-providers-resourceonboarding.md)hakkında temel bilgiler.

## <a name="get-started-with-resource-onboarding"></a>Kaynak ekleme ile çalışmaya başlama

Bu öğreticide, dağıtılması gereken iki parça vardır: özel sağlayıcı ve ilişkilendirme. İşlemi daha kolay hale getirmek için isteğe bağlı olarak her ikisini de dağıtan tek bir şablon kullanabilirsiniz.

Şablon şu kaynakları kullanacaktır:

* Microsoft. CustomProviders/resourceProviders
* Microsoft. Logic/iş akışları
* Microsoft. CustomProviders/ilişkilendirmeleri

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Özel sağlayıcı altyapısını dağıtma

Şablonun ilk bölümü özel sağlayıcı altyapısını dağıtır. Bu altyapı, ilişkilendirmeler kaynağının etkisini tanımlar. Özel sağlayıcılar hakkında bilgi sahibi değilseniz bkz. [özel sağlayıcı temelleri](custom-providers-overview.md).

Özel sağlayıcı altyapısını dağıtalım. Önceki şablonu kopyalayın, kaydedin ve dağıtın ya da Azure portal kullanarak altyapıyı uygulayın ve dağıtın.

1. [Azure Portal](https://portal.azure.com) gidin.

2. **Tüm hizmetlerde** **Şablonlar** arayın veya ana arama kutusunu kullanarak:

   ![Şablon ara](media/custom-providers-resource-onboarding/templates.png)

3. **Şablonlar** bölmesinde **Ekle** ' yi seçin:

   ![Ekle 'yi seçin](media/custom-providers-resource-onboarding/templatesadd.png)

4. **Genel**altında, yeni şablon Için bir **ad** ve **Açıklama** girin:

   ![Şablon adı ve açıklaması](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Bu makalenin "kaynak ekleme ile çalışmaya başlama" bölümünde JSON şablonunda kopyalama yaparak Kaynak Yöneticisi şablonu oluşturun:

   ![Kaynak Yöneticisi şablonu oluşturma](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Şablonu oluşturmak için **Ekle** ' yi seçin. Yeni şablon görünmezse **Yenile**' yi seçin.

7. Yeni oluşturulan şablonu seçin ve ardından **Dağıt**' ı seçin:

   ![Yeni şablonu seçin ve ardından Dağıt ' ı seçin.](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Gerekli alanların ayarlarını girin ve ardından abonelik ve kaynak grubunu seçin. **Özel kaynak sağlayıcısı kimliği** kutusunu boş bırakabilirsiniz.

   | Ayar adı | Gerekli mi? | Açıklama |
   | ------------ | -------- | ----------- |
   | Konum | Evet | Şablondaki kaynakların konumu. |
   | Mantıksal uygulama adı | Hayır | Mantıksal uygulamanın adı. |
   | Özel kaynak sağlayıcısı adı | Hayır | Özel kaynak sağlayıcısı adı. |
   | Özel kaynak sağlayıcısı kimliği | Hayır | İlişki kaynağını destekleyen mevcut bir özel kaynak sağlayıcısı. Burada bir değer belirtirseniz, mantıksal uygulama ve özel sağlayıcı dağıtımı atlanır. |
   | İlişkilendirme adı | Hayır | İlişkilendirme kaynağının adı. |

   Örnek parametreler:

   ![Şablon parametrelerini girin](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Dağıtıma gidip işlemin bitmesini bekleyin. Aşağıdaki ekran görüntüsüne benzer bir şey görmeniz gerekir. Yeni ilişkilendirme kaynağını çıkış olarak görmeniz gerekir:

   ![Başarılı dağıtım](media/custom-providers-resource-onboarding/customproviderdeployment.png)

   Aşağıda **gizli türleri göster** seçiliyken kaynak grubu verilmiştir:

   ![Özel sağlayıcı dağıtımı](media/custom-providers-resource-onboarding/showhidden.png)

10. İlişki oluşturma çağrılarını görmek için Logic App **çalıştırmaları geçmişi** sekmesini inceleyin:

    ![Mantıksal uygulama çalıştırma geçmişi](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Ek ilişkilendirmeler dağıtma

Özel sağlayıcı altyapısını ayarladıktan sonra kolayca daha fazla ilişkilendirme dağıtabilirsiniz. Ek İlişkilendirmelerin kaynak grubunun, özel sağlayıcı altyapısını dağıttığınız kaynak grubuyla aynı olması gerekmez. Bir ilişki oluşturmak için, belirtilen özel kaynak sağlayıcısı KIMLIĞINDE Microsoft. CustomProviders/resourceproviders/Write izinlerine sahip olmanız gerekir.

1. Önceki dağıtımın kaynak grubundaki özel sağlayıcı **Microsoft. customproviders/resourceproviders** kaynağına gidin. **Gizli türleri göster** onay kutusunu seçmeniz gerekir:

   ![Kaynağa git](media/custom-providers-resource-onboarding/showhidden.png)

2. Özel sağlayıcının kaynak KIMLIĞI özelliğini kopyalayın.

3. **Tüm hizmetlerde** **Şablonlar** arayın veya ana arama kutusunu kullanarak:

   ![Şablon ara](media/custom-providers-resource-onboarding/templates.png)

4. Önceden oluşturulan şablonu seçin ve ardından **Dağıt**' ı seçin:

   ![Önceden oluşturulan şablonu seçin ve ardından Dağıt ' ı seçin.](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Gerekli alanların ayarlarını girin ve ardından aboneliği ve farklı bir kaynak grubunu seçin. **Özel kaynak sağlayıcısı kimliği** ayarı için, daha önce dağıttığınız özel sağlayıcıdan KOPYALADıĞıNıZ kaynak kimliğini girin.

6. Dağıtıma gidip işlemin bitmesini bekleyin. Şimdi yalnızca yeni ilişkilendirmeler kaynağını dağıtmalıdır:

   ![Yeni ilişkilendirmeler kaynağı](media/custom-providers-resource-onboarding/createdassociationresource.png)

İsterseniz, mantıksal uygulama **çalıştırma geçmişine** geri dönerek mantıksal uygulama için başka bir çağrının yapıldığını görebilirsiniz. Mantıksal uygulamayı, oluşturulan her ilişkilendirme için ek işlevselliği artırmak üzere güncelleştirebilirsiniz.

## <a name="getting-help"></a>Yardım alma

Azure özel sağlayıcılar hakkında sorularınız varsa, [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers)üzerinde soru sormayı deneyin. Benzer bir soru zaten yanıtlanmış olabilir, bu nedenle göndermeden önce kontrol edin. Hızlı bir yanıt almak için etiketi `azure-custom-providers` ekleyin!

