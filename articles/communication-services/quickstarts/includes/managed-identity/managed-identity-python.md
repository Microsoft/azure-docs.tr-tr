---
ms.openlocfilehash: 3fcfb364ebffdd3643e803922cbe4f3dd0d87935
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513387"
---
## <a name="setting-up"></a>Ayarlanıyor

## <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

Terminal veya komut pencerenizi açın, uygulamanız için yeni bir dizin oluşturun ve bu dizine gidin.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

### <a name="install-the-sdk-packages"></a>SDK paketlerini yükler

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Yeni dosya oluşturma
Oluşturulan klasörünüzde yeni bir dosya açıp kaydedin `managed-identity.py` , bu dosyanın içine kodumuzu yerleştiriyoruz.

### <a name="use-the-sdk-packages"></a>SDK paketlerini kullanma

`import`Yüklemediğimiz SDK 'ları kullanmak için aşağıdaki deyimlerini dosyanızın en üstüne ekleyin.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential oluşturma

[DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential)kullanacağız. Bu kimlik bilgisi üretim ve geliştirme ortamları için uygundur. Bu hızlı başlangıç boyunca kullanacağız. Bu, dosyayı dosyanın en üstünde oluşturacağız.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Yönetilen kimliklerle bir kimlik oluşturma ve belirteç verme

Şimdi bir VoIP erişim belirteci vermek için oluşturulan kimlik bilgisini kullanan kodu ekleyeceğiz. Bu kodu daha sonra şu numaradan çağıracağız:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-managed-identities"></a>Yönetilen kimliklerle SMS gönderin
Yönetilen kimlikler kullanmanın başka bir örneği olarak, SMS göndermek için aynı kimlik bilgisini kullanan bu kodu ekleyeceğiz:

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>Ana kodumuzu yazın

İşlevlerimiz oluşturulduktan sonra, daha önce yazdığımız işlevleri çağıran ana kodu yazabilirsiniz.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

Son `managed-identity.py` dosya şuna benzer görünmelidir:

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>Programı çalıştırma

Her şey tamamlanarak, projenin dizininden girerek dosyayı çalıştırabilirsiniz `python managed-identity.py` . Her şey iyi sorun olursa aşağıdakine benzer bir şey görmeniz gerekir.

```Bash
    $ python managed-identity.py
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
