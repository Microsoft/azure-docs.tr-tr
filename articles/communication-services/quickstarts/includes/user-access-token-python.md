---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: db6e4a9c6d2829c7980164d5b79bd33e4b3fb6eb
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112779"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7 veya 3.6 +.
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../create-communication-resource.md).

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

1. Terminal veya komut pencerenizi açın, uygulamanız için yeni bir dizin oluşturun ve bu dizine gidin.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Proje kök dizininde **issue-Access-Tokens.py** adlı bir dosya oluşturmak ve temel özel durum işleme dahil olmak üzere programın yapısını eklemek için bir metin düzenleyicisi kullanın. Bu hızlı başlangıç için tüm kaynak kodu aşağıdaki bölümlerde bu dosyaya ekleyeceksiniz.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Paketi yükler

Hala uygulama dizininde, komutunu kullanarak Python için Azure Communication Services Identity SDK 'sını yükleyebilirsiniz `pip install` .

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

`CommunicationIdentityClient`Bağlantı dizeniz ile bir örneğini oluşturun. Aşağıdaki kod, adlı bir ortam değişkeninden kaynak için bağlantı dizesini alır `COMMUNICATION_SERVICES_CONNECTION_STRING` . [Kaynağınızın bağlantı dizesini yönetme](../create-communication-resource.md#store-your-connection-string)hakkında bilgi edinin.

Bu kodu bloğunun içine ekleyin `try` :

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Alternatif olarak, yönetilen kimlik ayarladıysanız, bkz. yönetilen [kimlikler kullanma](../managed-identity.md), yönetilen kimlik ile kimlik doğrulama de yapabilirsiniz.
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

## <a name="create-an-identity"></a>Kimlik oluşturma

Azure Iletişim Hizmetleri, hafif bir kimlik dizini sağlar. `create_user`Dizinde benzersiz olan yeni bir giriş oluşturmak için yöntemini kullanın `Id` . Uygulamanın kullanıcılarına eşleme ile alınan kimliği depola. Örneğin, bunları uygulama sunucunuzun veritabanında depolayarak. Daha sonra erişim belirteçleri vermek için kimlik gereklidir.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier)
```

## <a name="issue-access-tokens"></a>Erişim belirteçleri verme

`get_token`Zaten var olan Iletişim Hizmetleri kimliği için bir erişim belirteci vermek üzere metodunu kullanın. Parametresi `scopes` , bu erişim belirtecini yetkilendirecek temel öğeler kümesini tanımlar. [Desteklenen eylemlerin listesine](../../concepts/authentication.md)bakın. Parametresinin yeni örneği, `CommunicationUserIdentifier` Azure Iletişim hizmeti kimliğinin dize gösterimine göre oluşturulabilir.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Erişim belirteçleri yeniden verilmesini gerektiren kısa ömürlü kimlik bilgileridir. Bunu yapmamak, uygulamanızın kullanıcı deneyiminin kesintiye uğramasına neden olabilir. `expires_on`Response özelliği, erişim belirtecinin ömrünü gösterir.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Aynı istek içinde bir kimlik oluşturma ve erişim belirteci verme

`create_user_and_token`Bir Iletişim Hizmetleri kimliği oluşturmak ve bunun için bir erişim belirteci vermek için yöntemini kullanın. Parametresi `scopes` , bu erişim belirtecini yetkilendirecek temel öğeler kümesini tanımlar. [Desteklenen eylemlerin listesine](../../concepts/authentication.md)bakın.

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0].identifier
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nCreated an identity with ID: " + identity)
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>Erişim belirteçlerini yenileme

Bir erişim belirtecini yenilemek için, öğesini kullanarak yeniden `CommunicationUserIdentifier` yayımlayın:

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Erişim belirteçlerini iptal et

Bazı durumlarda, erişim belirteçlerini açıkça iptal edebilirsiniz. Örneğin, bir uygulamanın kullanıcısı hizmetinize kimlik doğrulaması yapmak için kullandıkları parolayı değiştirdiğinde. Yöntem `revoke_tokens` , kimliğe verilen tüm etkin erişim belirteçlerini geçersiz kılar.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Kimlik silme

Bir kimlik silindiğinde tüm etkin erişim belirteçleri iptal olur ve kimlik için erişim belirteçleri yayınınızdan sonra. Ayrıca, kimlik ile ilişkili tüm kalıcı içeriği de kaldırır.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Kodu çalıştırma

Konsol isteminde *issue-Access-Tokens.py* dosyasını içeren dizine gidin ve `python` uygulamayı çalıştırmak için aşağıdaki komutu yürütün.

```console
python ./issue-access-tokens.py
```
