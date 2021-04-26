---
title: Azure Media Services v3 API 'sine bağlanma-Python
description: Bu makalede, Python ile Media Services v3 API 'sine nasıl bağlanacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 24e2ba4027dc818256dc9572f697fe7ec5a5a56b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960716"
---
# <a name="connect-to-media-services-v3-api---python"></a>Media Services v3 API 'sine bağlanma-Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makalede hizmet sorumlusu oturum açma yöntemi kullanılarak Azure Media Services v3 Python SDK 'sına nasıl bağlanabilmeniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- [Python.org](https://www.python.org/downloads/) 'ten Python indirin
- Ortam değişkenini ayarladığınızdan emin olun `PATH`
- [Media Services hesabı oluşturun](./account-create-how-to.md). Kaynak grubu adını ve Media Services hesap adını unutduğunuzdan emin olun.
- Hizmet sorumlusu kimlik doğrulama yöntemini seçerek, [erişim API 'leri](./access-api-howto.md) konusundaki adımları izleyin. `SubscriptionId`Daha sonraki adımlarda ihtiyacınız olan ABONELIK kimliği (), uygulama ISTEMCI kimliği ( `AadClientId` ), kimlik doğrulama anahtarı () `AadSecret` ve Kiracı kimliğini () kaydedin `AadTenantId` .

> [!IMPORTANT]
> [Adlandırma kurallarını](media-services-apis-overview.md#naming-conventions)gözden geçirin.

## <a name="install-the-modules"></a>Modülleri yükleme

Python kullanarak Azure Media Services çalışmak için bu modülleri yüklemeniz gerekir.

* `azure-mgmt-resource`Active Directory Için Azure modüllerini içeren modül.
* `azure-mgmt-media`Media Services varlıklarını içeren modül.

    [Python için Media Services SDK 'sının en son sürümünü](https://pypi.org/project/azure-mgmt-media/)aldığınızdan emin olun.

Bir komut satırı aracı açın ve modülleri yüklemek için aşağıdaki komutları kullanın.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Python istemcisine bağlanma

1. Uzantılı bir dosya oluşturun `.py`
1. Dosyayı en sevdiğiniz düzenleyicide açın
1. Aşağıdaki kodu dosyaya ekleyin. Kod, gerekli modülleri içeri aktarır ve Media Services bağlanmak için gereken Active Directory kimlik bilgileri nesnesini oluşturur.

      Değişkenlerin değerlerini, [erişim API 'lerinden](./access-api-howto.md)aldığınız değerlere ayarlayın. `ACCOUNT_NAME`Ve değişkenlerini, `RESOUCE_GROUP_NAME` Bu kaynakları oluştururken kullanılan Media Services hesap adı ve kaynak grubu adlarıyla güncelleştirin.

      ```
      import adal
      from azure.mgmt.media import AzureMediaServices
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

      RESOURCE = "https://management.core.windows.net/"
      # Tenant ID for your Azure Subscription (AadTenantId)
      TENANT_ID = "00000000-0000-0000-000000000000"
      # Your Service Principal App ID (AadClientId)
      CLIENT = "00000000-0000-0000-000000000000"
      # Your Service Principal Password (AadSecret)
      KEY = "00000000-0000-0000-000000000000"
      # Your Azure Subscription ID (SubscriptionId)
      SUBSCRIPTION_ID = "00000000-0000-0000-000000000000"
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = "amsaccount"
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = "amsResourceGroup"

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + "/" + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials, RESOURCE, CLIENT, KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print(client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Dosyayı çalıştırma

## <a name="next-steps"></a>Sonraki adımlar

- [Python SDK 'yı](https://aka.ms/ams-v3-python-sdk)kullanın.
- Media Services [Python başvuru](/python/api/overview/azure/mediaservices/management) belgelerini gözden geçirin.
