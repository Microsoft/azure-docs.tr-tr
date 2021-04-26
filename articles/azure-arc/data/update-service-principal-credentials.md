---
title: Hizmet sorumlusu kimlik bilgilerini güncelleştirme
description: Hizmet sorumlusu için kimlik bilgilerini güncelleştirme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669551"
---
# <a name="update-service-principal-credentials"></a>Hizmet sorumlusu kimlik bilgilerini güncelleştirme

Hizmet sorumlusu kimlik bilgileri değiştiğinde, veri denetleyicisindeki gizli dizileri güncelleştirmeniz gerekir.

Örneğin, veri denetleyicisini hizmet sorumlusu kiracı KIMLIĞI, istemci KIMLIĞI ve istemci parolası için belirli bir değer kümesi kullanarak dağıttıysanız ve sonra bu değerlerden birini veya daha fazlasını değiştirirseniz, veri denetleyicisindeki gizli dizileri güncelleştirmeniz gerekir.  Aşağıda kiracı KIMLIĞINI, Istemci KIMLIĞINI veya Istemci parolasını güncelleştirme yönergeleri verilmiştir. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Arka Plan

Hizmet sorumlusu, [hizmet sorumlusu oluşturma](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)sırasında oluşturulmuştur. 

## <a name="steps"></a>Adımlar

1. Varsayılan düzenleyicide hizmet sorumlusu gizliliğine erişin.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Örneğin, hizmet sorumlusu gizliliğini ad alanındaki bir veri denetleyicisine düzenlemek için `arc` aşağıdaki komutu çalıştırın:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit`Komut, varsayılan düzenleyicide Credentials. yıml dosyasını açar. 


1. Hizmet sorumlusu gizliliğini düzenleyin. 

   Varsayılan düzenleyicide, veri bölümündeki değerleri güncelleştirilmiş kimlik bilgileri ile değiştirin.

   Örneğin:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   `clientID` `clientSecret` Ve/veya değerlerini `tenantID` uygun şekilde düzenleyin. 

> [!NOTE]
>Değerlerin Base64 kodlamalı olması gerekir. Başka hiçbir özelliği düzenlemeyin.

İçin yanlış bir değer sağlanmışsa `clientId` , `clientSecret` `tenantID` `control-xxxx` Pod/Controller kapsayıcı günlüklerinde aşağıdaki gibi bir hata iletisi görürsünüz:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Sonraki adımlar

[Arc Veri Denetleyicisi'ne bağlanmak için kullanıcı adı ve parolayı alma](retrieve-the-username-password-for-data-controller.md)

[Hizmet sorumlusu oluşturma](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
