---
ms.openlocfilehash: e62aed02a0ad5f26ec8fd0a79de5e91269386095
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450385"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 veya üzeri.
- Dağıtılan bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

Terminal veya komut pencerenizi açın, uygulamanız için yeni bir dizin oluşturun ve ardından bu dizine gidin.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Proje kök dizininde phone_numbers_sample. Kopyala adlı bir dosya oluşturmak ve aşağıdaki kodu eklemek için bir metin düzenleyicisi kullanın. Aşağıdaki bölümlerde kalan hızlı başlangıç kodunu ekleyeceğiz.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Paketi yükler

Hala uygulama dizininde, komutunu kullanarak Python paketi için Azure Communication Services yönetim istemcisi kitaplığı ' nı da yükleyebilirsiniz `pip install` .

```console
pip install azure-communication-phonenumbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Telefon numaraları Istemcisinin kimliğini doğrulama

`PhoneNumbersClient`Azure Active Directory kimlik doğrulaması kullanmak için etkinleştirilir. Nesnesini kullanmak `DefaultAzureCredential` Azure Active Directory kullanmaya başlamak için en kolay yoldur ve komutunu kullanarak yükleyebilirsiniz `pip install` .

```console
pip install azure-identity
```

Bir nesne oluşturmak,, ve ' nin `DefaultAzureCredential` `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET` `AZURE_TENANT_ID` kayıtlı Azure AD uygulamanızdan karşılık gelen değerleriyle birlikte ortam değişkenleri olarak ayarlamış olmasını gerektirir.

Kitaplığı yükledikten sonra `azure-identity` , istemcinin kimliğini doğrulamaya devam edebiliriz.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Alternatif olarak, kimlik doğrulaması için iletişim kaynağından uç nokta ve erişim anahtarı kullanmak da mümkündür.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>İşlevler

`PhoneNumbersClient`Kimlik doğrulamasından sonra, yapamadığı farklı işlevlerde çalışmaya başlayabiliriz.

### <a name="search-for-available-phone-numbers"></a>Kullanılabilir telefon numaralarını ara

Telefon numaralarını satın almak için önce kullanılabilir telefon numaralarını aramanız gerekir. Telefon numaralarını aramak için, alan kodunu, atama türünü, [telefon numarası özelliklerini](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [telefon numarası türünü](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)ve miktarı (varsayılan miktar 1 olarak ayarlanır) belirtin. Ücretsiz telefon numarası türü için, alan kodunu sağlamanın isteğe bağlı olduğunu unutmayın.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Satın alma telefon numaraları

Telefon numarası arama sonucu bir `PhoneNumberSearchResult` . Bu, `searchId` aramada sayıları almak için satın alma NUMARALARı API 'sine geçirilebilen bir içerir. Satın alma telefon numaraları API 'sinin çağrılması, Azure hesabınıza ücretlendirdiğine neden olur.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient,
    PhoneNumberCapabilityType,
    PhoneNumberAssignmentType,
    PhoneNumberType,
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Satın alınan telefon numaralarını al

Bir satın alma numarasından sonra, istemciden alabilirsiniz.

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

Satın alınan tüm telefon numaralarını da alabilirsiniz.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Telefon numarası yeteneklerini güncelleştirme

Daha önce satın alınan bir telefon numarasının yeteneklerini güncelleştirebilirsiniz.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Yayın telefon numarası

Satın alınan bir telefon numarasını serbest bırakabilirsiniz.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>Kodu çalıştırma

Konsol isteminde phone_numbers_sample. Kopyala dosyasını içeren dizine gidin, ardından uygulamayı çalıştırmak için aşağıdaki Python komutunu yürütün.

```console
./phone_numbers_sample.py
```
