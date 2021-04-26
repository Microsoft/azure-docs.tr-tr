---
title: Identityselector Kullanıcı arabirimi öğesi
description: Azure portal için Microsoft. Managedıdentity. ıdentityselector Kullanıcı arabirimi öğesini açıklar. Bir kaynağa Yönetilen kimlikler atamak için kullanın.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063396"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft. Managedıdentity. ıdentityselector Kullanıcı arabirimi öğesi

Dağıtımdaki bir kaynak için [Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md) atamaya yönelik bir denetim.

## <a name="ui-sample"></a>UI örneği

Denetim aşağıdaki öğelerden oluşur:

![Microsoft. Managedıdentity. ıdentityselector ilk adım](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

Kullanıcı **Ekle**' yi seçtiğinde aşağıdaki form açılır. Kullanıcı, kaynak için bir veya daha fazla kullanıcı tarafından atanan kimlik seçebilir.

![Microsoft. Managedıdentity. ıdentityselector ikinci adım](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

Seçili kimlikler tabloda görüntülenir. Kullanıcı bu tablodan öğe ekleyebilir veya silebilir.

![Microsoft. Managedıdentity. ıdentityselector üçüncü adım](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>Şema

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Açıklamalar

- Sistem tarafından atanan kimlik seçenekleri denetimi için bir başlangıç değeri ayarlamak için **defaultValue.sysTemassignedıdentity** kullanın. Varsayılan değer **kapalıdır**. Aşağıdaki değerlere izin verilir:
  - **Açık** – kaynağa bir sistem tarafından atanan kimlik atanır.
  - **Off** : sisteme atanan bir kimlik kaynağa atanmaz.
  - **Ononly** : kaynağa bir sistem tarafından atanan kimlik atanır. Kullanıcılar bu değeri dağıtım sırasında düzenleyemez.
  - **Yalnızca çevrimdışı** : kaynağa bir sistem tarafından atanan kimlik atanmaz. Kullanıcılar bu değeri dağıtım sırasında düzenleyemez.

- **Options. Hidesystemassignedıdentity** **true** olarak ayarlanırsa, sistem tarafından atanan kimliği yapılandırmak için Kullanıcı arabirimi gösterilmez. Bu seçenek için varsayılan değer **false**'dur.
- **Options. Hideuseratandıdentity** **değeri true** olarak ayarlanırsa, Kullanıcı tarafından atanan KIMLIĞI yapılandırmak için Kullanıcı arabirimi gösterilmez. Kaynağa Kullanıcı tarafından atanan bir kimlik atanmamıştır. Bu seçenek için varsayılan değer **false**'dur.

## <a name="next-steps"></a>Sonraki adımlar

- UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
- UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).