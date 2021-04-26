---
title: ServicePrincipalSelector Kullanıcı arabirimi öğesi
description: Azure portal için Microsoft. Common. Servicesprincipalselector Kullanıcı arabirimi öğesini açıklar. Bir parola veya sertifika parmak izi girmek için bir uygulama ve metin kutusu seçmek üzere bir denetim sağlar.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184459"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft. Common. Servicesprincipalselector Kullanıcı arabirimi öğesi

Kullanıcıların mevcut bir [hizmet sorumlusunu](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) seçmesini veya yeni bir uygulamayı kaydetmesini sağlayan bir denetim. **Yeni oluştur**' u seçtiğinizde, yeni bir uygulama kaydetmek için aşağıdaki adımları izleyin. Mevcut bir uygulamayı seçtiğinizde, denetim bir parola veya sertifika parmak izi girişi için bir metin kutusu sağlar.

## <a name="ui-samples"></a>UI örnekleri

Varsayılan bir uygulama kullanabilir, yeni bir uygulama oluşturabilir veya var olan bir uygulamayı kullanabilirsiniz.

### <a name="use-default-application-or-create-new"></a>Varsayılan uygulamayı kullan veya yeni oluştur

Varsayılan görünüm, özelliğindeki değerler tarafından belirlenir `defaultValue` ve **hizmet sorumlusu türü** **Yeni oluştur** olarak ayarlanır. `principalId`Özellik geçerli bir genel benzersiz tanımlayıcı (GUID) içeriyorsa, denetim uygulama için arama yapar `objectId` . Kullanıcı denetimden seçim yapmazsa varsayılan değer geçerlidir.

Yeni bir uygulama kaydetmek istiyorsanız **Seçimi Değiştir** ' i seçin ve **bir uygulamayı kaydet** iletişim kutusu görüntülenir. **Ad**, **Desteklenen hesap türünü** girin ve **Kaydet** düğmesini seçin.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Microsoft. Common. Servicesprincipalselector ilk görünümü.":::

Yeni bir uygulamayı kaydettikten sonra, bir parola veya sertifika parmak izi girmek için **kimlik doğrulama türünü** kullanın.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Microsoft. Common. Servicesprincipalselector kimlik doğrulaması.":::

### <a name="use-existing-application"></a>Mevcut uygulamayı kullan

Mevcut bir uygulamayı kullanmak için **Varolanı Seç** ' i seçin ve sonra **seçim yap**' ı seçin. Uygulamanın adını aramak için **bir uygulama Seç** iletişim kutusunu kullanın. Sonuçlardan uygulamayı ve ardından **Seç** düğmesini seçin. Bir uygulamayı seçtikten sonra Denetim, **kimlik doğrulama türünü** bir parola veya sertifika parmak izi girmek üzere görüntüler.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft. Common. Servicesprincipalselector var olan uygulamayı seçin.":::

## <a name="schema"></a>Şema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Açıklamalar

- Gerekli özellikler aşağıdaki gibidir:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Varsayılan ve belirtir `principalId` `name` .

- İsteğe bağlı özellikler aşağıdaki gibidir:
  - `toolTip`: Her etikete bir araç ipucu ekler `infoBalloon` .
  - `visible`: Denetimi gizleyin veya görüntüleyin.
  - `options`: Sertifika parmak izi seçeneğinin kullanılabilir yapılıp yapılmayacağını belirtir.
  - `constraints`: Parola doğrulaması için Regex kısıtlamaları.

## <a name="example"></a>Örnek

Aşağıda, denetimin bir örneği verilmiştir `Microsoft.Common.ServicePrincipalSelector` . `defaultValue`Özelliği `principalId` `<default guid>` Varsayılan uygulama tanımlayıcı GUID 'si için bir yer tutucu olarak ayarlanır.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Örnek çıkış

, `appId` Seçtiğiniz veya oluşturduğunuz uygulama kaydının kimliğidir. , `objectId` Seçilen uygulama kaydı için yapılandırılmış hizmet sorumluları için bir nesne kimlikleri dizisidir.

Denetimden hiçbir seçim yapılkalmadığında, `newOrExisting` özellik değeri **yenidir**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

**Yeni oluştur** veya var olan bir uygulama denetimden seçiliyse, `newOrExisting` özellik değeri **mevcut** olur:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
- UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).