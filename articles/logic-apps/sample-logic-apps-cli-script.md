---
title: Azure CLı betik örneği-mantıksal uygulama oluşturma
description: Azure CLı 'de Logic Apps uzantısı aracılığıyla bir mantıksal uygulama oluşturmak için örnek betik.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: 8086ce0e5964d4e37a5ffc3082d5f2856058e4e5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875494"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLı betik örneği-mantıksal uygulama oluşturma

Bu betik, () [Azure clı Logic Apps uzantısı](/cli/azure/logic)aracılığıyla bir örnek mantıksal uygulama oluşturur `az logic` . Azure CLı aracılığıyla mantıksal uygulamalar oluşturmaya ve yönetmeye yönelik ayrıntılı bir kılavuz için bkz. [Azure CLI için Logic Apps hızlı](quickstart-logic-apps-azure-cli.md)başlangıcı.

> [!WARNING]
> Azure CLı Logic Apps uzantısı Şu anda *deneysel* ve *müşteri desteği kapsamında değil*. Özellikle uzantıyı üretim ortamlarında kullanmayı seçerseniz, bu CLı uzantısını dikkatle kullanın.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. Azure aboneliğiniz yoksa [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Yerel bilgisayarınızda yüklü olan [Azure CLI](/cli/azure/install-azure-cli) .
* Bilgisayarınızda yüklü [Logic Apps Azure CLI uzantısı](/cli/azure/azure-cli-extensions-list) . Bu uzantıyı yüklemek için şu komutu kullanın: `az extension add --name logic`
* Mantıksal uygulamanız için bir [iş akışı tanımı](quickstart-logic-apps-azure-cli.md#workflow-definition) . Bu JSON dosyası [Iş akışı Tanım Dili şemasına](logic-apps-workflow-definition-language.md)uymalıdır.
* Mantıksal uygulamanızla aynı kaynak grubundaki desteklenen bir [Logic Apps Bağlayıcısı](../connectors/apis-list.md) aracılığıyla bir e-posta HESABıNA yönelik API bağlantısı. Bu örnek [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) bağlayıcısını kullanır, ancak [Outlook.com](../connectors/connectors-create-api-outlook.md)gibi diğer bağlayıcıları de kullanabilirsiniz.

### <a name="prerequisite-check"></a>Önkoşul denetimi

Başlamadan önce ortamınızı doğrulayın:

* Azure portal oturum açın ve ' i çalıştırarak aboneliğinizin etkin olduğunu denetleyin `az login` .

* Çalıştıran bir Terminal veya komut penceresinde Azure CLı sürümünüzü denetleyin `az --version` . En son sürüm için bkz. [en son sürüm notları](/cli/azure/release-notes-azure-cli).

  * En son sürüme sahip değilseniz, [işletim sisteminiz veya platformunuzun yükleme kılavuzunu](/cli/azure/install-azure-cli)izleyerek yüklemenizi güncelleştirin.

### <a name="sample-workflow-explanation"></a>Örnek iş akışı açıklaması

Bu örnek iş akışı Tanım dosyası, [Azure Portal için Logic Apps hızlı başlangıç](quickstart-create-first-logic-app-workflow.md)ile aynı temel mantıksal uygulamayı oluşturur. 

Bu örnek iş akışı: 

1. `$schema`Mantıksal uygulama için bir şema belirtir.

1. Tetikleyiciler listesindeki mantıksal uygulama için bir tetikleyiciyi tanımlar `triggers` . Tetikleyici `recurrence` her 3 saatte bir yinelenir (). Eylemler, `When_a_feed_item_is_published` BELIRTILEN RSS akışı () için yeni bir akış öğesi yayımlandığında () tetiklenir `feedUrl` .

1. Eylemler listesindeki mantıksal uygulama için bir eylem tanımlar `actions` . Eylem, `Send_an_email_(V2)` eylem girişlerinin () gövde bölümünde belirtildiği gıbı RSS Akış öğelerinden ayrıntıları içeren Microsoft 365 aracılığıyla bir e-posta gönderir () `body` `inputs` .

## <a name="sample-workflow-definition"></a>Örnek iş akışı tanımı

Örnek betiği çalıştırmadan önce, önce bir örnek [iş akışı tanımı](#prerequisites)oluşturmanız gerekir.

1. Bilgisayarınızda bir JSON dosyası oluşturun `testDefinition.json` . 

1. Aşağıdaki içeriği JSON dosyasına kopyalayın: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Yer tutucu değerlerini kendi bilgileriniz ile güncelleştirin:

    1. Yer tutucu e-posta adresini ( `"To": "test@example.com"` ) değiştirin. Logic Apps bağlayıcılarıyla uyumlu bir e-posta adresi kullanmanız gerekir. Daha fazla bilgi için bkz. [Önkoşullar](#prerequisites).

    1. Office 365 Outlook bağlayıcısından farklı bir e-posta Bağlayıcısı kullanıyorsanız, ek bağlayıcı ayrıntılarını değiştirin.

    1. Bağlantı tanımlayıcılarınız (ve) için yer tutucu abonelik değerlerini (), `00000000-0000-0000-0000-000000000000` `connectionId` `id` `$connections` kendi abonelik değerlerinizle bağlantı parametresi () ile değiştirin.

1. Yaptığınız değişiklikleri kaydedin.

## <a name="sample-script"></a>Örnek betik

> [!NOTE]
> Bu örnek, kabuk için yazılmıştır `bash` . Bu örneği Windows PowerShell veya komut Istemi gibi başka bir kabukta çalıştırmak istiyorsanız, betikte değişiklikler yapmanız gerekebilir.

Bu örnek betiği çalıştırmadan önce, Azure 'a bağlanmak için şu komutu çalıştırın:

```azurecli-interactive

az login

```

Ardından, iş akışı tanımınızı oluşturduğunuz dizine gidin. Örneğin, masaüstünüzde iş akışı tanımı JSON dosyasını oluşturduysanız:

```azurecli

cd ~/Desktop

```

Ardından, mantıksal uygulama oluşturmak için bu betiği çalıştırın. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Dağıtımı temizleme

Örnek betiği kullanmayı bitirdikten sonra, mantıksal uygulama dahil olmak üzere kaynak grubunuzu ve tüm iç içe geçmiş kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Betik açıklaması

Bu örnek betik, yeni bir kaynak grubu ve mantıksal uygulama oluşturmak için aşağıdaki komutları kullanır.

| Komut | Notlar |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | Mantıksal uygulamanızın kaynaklarından depolandığı bir kaynak grubu oluşturur. |
| [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) | Parametresinde tanımlanan iş akışını temel alan bir mantıksal uygulama oluşturur `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Bir kaynak grubunu ve tüm iç içe geçmiş kaynaklarını siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLı hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure/).

[Microsoft 'un kod örnekleri tarayıcısında](/samples/browse/?products=azure-logic-apps)daha fazla Logic Apps CLI betik örneği bulabilirsiniz.
