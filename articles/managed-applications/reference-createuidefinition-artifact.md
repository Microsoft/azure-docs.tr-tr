---
title: Azure yönetilen uygulama Createuıdefinition yapıtı
description: Azure yönetilen uygulaması için Createuıdefinition yapay öğesinin nasıl oluşturulacağını gösterir. Dosya Createuıdefinition. JSON olarak adlandırılır.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 3e952476373d1692494d06f22dfeb202cab6d6d7
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528980"
---
# <a name="reference-user-interface-elements-artifact"></a>Başvuru: Kullanıcı arabirimi öğeleri yapıtı

Bu makale, Azure yönetilen uygulamalarında *Createuıdefinition. JSON* yapıtı için bir başvurudur. Kullanıcı arabirimi öğeleri yazma hakkında daha fazla bilgi için bkz. [Kullanıcı arabirimi öğeleri oluşturma](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Kullanıcı arabirimi öğeleri

Aşağıdaki JSON, Azure yönetilen uygulamalar için *Createuıdefinition. JSON* dosyasının bir örneğini göstermektedir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: özel eylemler ve kaynaklarla yönetilen uygulama oluşturma](tutorial-create-managed-app-with-custom-provider.md)
- [Başvuru: dağıtım şablonu yapıtı](reference-main-template-artifact.md)
- [Başvuru: tanım yapıtı görüntüle](reference-view-definition-artifact.md)