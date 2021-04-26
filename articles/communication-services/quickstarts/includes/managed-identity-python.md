---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450495"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Iletişim Hizmetleri çözümünüze yönetilen kimlik ekleme

### <a name="install-the-sdk-packages"></a>SDK paketlerini yükler

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>SDK paketlerini kullanma

`import`Azure kimliğini kullanmak için kodunuza aşağıdakileri ekleyin.

```python
from azure.identity import DefaultAzureCredential
```

Aşağıdaki örneklerde [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential)kullanılmaktadır. Bu kimlik bilgisi üretim ve geliştirme ortamları için uygundur.

Yönetilen kimlik doğrulaması kullanarak kolayca geçebileceğiniz kolay bir yol için bkz. [yönetilen kimlikle erişimi yetkilendirme](../managed-identity-from-cli.md)

DefaultAzureCredential nesnesinin nasıl çalıştığı ve bu hızlı başlangıçta belirtilmeyen yollarla nasıl kullanabileceğiniz hakkında daha ayrıntılı bir bakış için bkz. [Python Için Azure Identity istemci kitaplığı](https://docs.microsoft.com/python/api/overview/azure/identity-readme)

### <a name="create-an-identity-and-issue-a-token"></a>Bir kimlik oluşturma ve belirteç verme

Aşağıdaki kod örneği, yönetilen kimliğe sahip bir hizmet istemci nesnesinin nasıl oluşturulduğunu gösterir ve ardından yeni bir kullanıcı için bir belirteç vermek üzere istemciyi kullanır:

```python
from azure.communication.identity import CommunicationIdentityClient

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Azure yönetilen kimliği ile SMS gönderin
Aşağıdaki kod örneği, Azure yönetilen kimliği ile bir hizmet istemci nesnesinin nasıl oluşturulduğunu gösterir ve ardından bir SMS iletisi göndermek için istemcisini kullanır:

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

### <a name="list-all-your-purchased-phone-numbers"></a>Satın alınan tüm telefon numaralarınızı listeleyin

Aşağıdaki kod örneği, Azure yönetilen kimliği ile bir hizmet istemci nesnesinin nasıl oluşturulduğunu gösterir ve ardından kaynağın sahip olduğu tüm satın alınan telefon numaralarını görmek için istemcisini kullanır:

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```