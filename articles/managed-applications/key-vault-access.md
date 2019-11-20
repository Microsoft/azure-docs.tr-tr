---
title: Yönetilen uygulamalarla Azure Key Vault kullanma | Microsoft Docs
description: Yönetilen uygulamalar dağıtıldığında Azure Key Vault erişim sırlarının nasıl kullanılacağını gösterir
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: a87066425845a7f1043576a858a361e601ba9cc8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003424"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Azure yönetilen uygulamalar dağıtıldığında erişim Key Vault gizliliği

Dağıtım sırasında bir parametre olarak güvenli bir değer (parola gibi) geçirmeniz gerektiğinde değeri bir [Azure Key Vault](../key-vault/key-vault-overview.md)alabilirsiniz. Yönetilen uygulamaları dağıttığınızda Key Vault erişmek için, **gereç kaynak sağlayıcısı** hizmet sorumlusuna erişim vermeniz gerekir. Yönetilen uygulamalar hizmeti, işlemleri çalıştırmak için bu kimliği kullanır. Dağıtım sırasında Key Vault bir değeri başarıyla almak için, hizmet sorumlusu Key Vault erişebilmelidir.

Bu makalede, Key Vault yönetilen uygulamalarla çalışacak şekilde nasıl yapılandırılacağı açıklanır.

## <a name="enable-template-deployment"></a>Şablon dağıtımını etkinleştir

1. Portalda Key Vault seçin.

1. **Erişim ilkeleri**'ni seçin.   

   ![Erişim ilkelerini seçin](./media/key-vault-access/select-access-policies.png)

1. **Gelişmiş erişim ilkelerini görüntülemek için tıklayın**'ı seçin.

   ![Gelişmiş erişim ilkelerini göster](./media/key-vault-access/advanced.png)

1. **Şablon dağıtımı için Azure Resource Manager erişimi etkinleştir**' i seçin. Ardından **Kaydet**’i seçin.

   ![Şablon dağıtımını etkinleştir](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Hizmet katkıda bulunan olarak ekle

1. Seçin **erişim denetimi (IAM)** .

   ![Erişim denetimi seçin](./media/key-vault-access/access-control.png)

1. Seçin **rol ataması Ekle**.

   ![Ekle 'yi seçin](./media/key-vault-access/add-access-control.png)

1. Rol için **katkıda bulunan** öğesini seçin. **Gereç kaynak sağlayıcısını** arayın ve kullanılabilir seçeneklerden seçin.

   ![Sağlayıcı ara](./media/key-vault-access/search-provider.png)

1. **Kaydet**’i seçin.

## <a name="reference-key-vault-secret"></a>Key Vault gizli dizi başvurusu

Bir Key Vault gizli anahtar yönetilen uygulamanızdaki bir şablona geçirmek için [bağlantılı şablon](../azure-resource-manager/resource-group-linked-templates.md) kullanmanız ve bağlantılı şablon için parametrelerde Key Vault başvurmanız gerekir. Key Vault kaynak KIMLIĞINI ve gizli dizi adını sağlayın.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Sonraki adımlar

Key Vault yönetilen bir uygulamanın dağıtımı sırasında erişilebilir olacak şekilde yapılandırdınız.

* Bir Key Vault değeri şablon parametresi olarak geçirme hakkında daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Yönetilen uygulama örnekleri için bkz. [Azure yönetilen uygulamalar Için örnek projeler](sample-projects.md).
* Yönetilen bir uygulamaya ait bir kullanıcı arabirimi tanım dosyası oluşturma hakkında bilgi için [CreateUiDefinition ile çalışmaya başlama](create-uidefinition-overview.md) konusunu inceleyin.