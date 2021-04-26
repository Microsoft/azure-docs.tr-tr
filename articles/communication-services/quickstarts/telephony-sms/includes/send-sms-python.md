---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: 2b96d62fb2be27de03964212557446d2e792beb8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112962"
---
SMS mesajları göndermek için Iletişim Hizmetleri Python SMS SDK 'sını kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7 veya 3.6 +.
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- SMS etkin telefon numarası. [Telefon numarası alın](../get-phone-number.md).

### <a name="prerequisite-check"></a>Önkoşul denetimi

- Bir Terminal veya komut penceresinde, `python --version` Python 'un yüklü olup olmadığını denetlemek için komutunu çalıştırın.
- Iletişim Hizmetleri kaynağınız ile ilişkili telefon numaralarını görüntülemek için [Azure Portal](https://portal.azure.com/)oturum açın, iletişim hizmetleri kaynağınızı bulun ve sol gezinti bölmesinden **telefon numaraları** sekmesini açın.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

Terminal veya komut pencerenizi açın, uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Proje kök dizininde **Send-SMS.py** adlı bir dosya oluşturmak ve temel özel durum işleme dahil olmak üzere programın yapısını eklemek için bir metin düzenleyicisi kullanın. Bu hızlı başlangıç için tüm kaynak kodu aşağıdaki bölümlerde bu dosyaya ekleyeceksiniz.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Paketi yükler

Hala uygulama dizininde, komutunu kullanarak Python paketi için Azure Communication Services SMS SDK 'sını yükleyebilirsiniz `pip install` .

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Python için Azure Communication Services SMS SDK 'sının önemli özelliklerinden bazılarını işler.

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Bu sınıf tüm SMS işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve SMS mesajları göndermek için kullanabilirsiniz.                                                                                                                 |
| SmsSendResult               | Bu sınıf, SMS hizmetinden elde edilen sonucu içerir.                                          |

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Bağlantı dizeniz ile bir **SmsClient** örneği oluşturun. [Kaynağınızın bağlantı dizesini yönetme](../../create-communication-resource.md#store-your-connection-string)hakkında bilgi edinin.

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Kolaylık olması için bu hızlı başlangıçta bağlantı dizeleri kullanıyoruz, ancak üretim ortamlarında daha güvenli ve ölçeklenebilir olduklarından, [Yönetilen kimlikler](../../../quickstarts/managed-identity.md) kullanmanızı öneririz.


## <a name="send-a-11-sms-message"></a>1:1 SMS Iletisi gönder

Tek bir alıcıya SMS iletisi göndermek için, ```send``` **SmsClient** 'deki yöntemi tek bir alıcı telefon numarasıyla çağırın. Ayrıca, teslim raporunun etkinleştirilip etkinleştirilmeyeceğini ve özel Etiketler ayarlayamayacağını belirtmek için isteğe bağlı parametreleri de geçirebilirsiniz. `try` **Send-SMS.py** içindeki blok sonuna bu kodu ekleyin:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

`<from-phone-number>`İletişim hizmetinize ve `<to-phone-number>` ileti göndermek istediğiniz telefon numarasıyla ILIŞKILI bir SMS etkin telefon numarasıyla değiştirmelisiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör: + 14255550123).

## <a name="send-a-1n-sms-message"></a>1: N SMS Iletisi gönder

Bir alıcı listesine SMS iletisi göndermek için, ```send``` **SmsClient** adresinden alıcının telefon numaralarının listesini içeren yöntemi çağırın. Ayrıca, teslim raporunun etkinleştirilip etkinleştirilmeyeceğini ve özel Etiketler ayarlayamayacağını belirtmek için isteğe bağlı parametreleri de geçirebilirsiniz. `try` **Send-SMS.py** içindeki blok sonuna bu kodu ekleyin:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

`<from-phone-number>`İletişim hizmetinize ve `<to-phone-number-1>` `<to-phone-number-2>` ileti göndermek istediğiniz telefon NUMARASıNA sahip bir SMS etkin telefon numarasıyla değiştirmelisiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör: + 14255550123).

## <a name="optional-parameters"></a>İsteğe Bağlı Parametreler

`enable_delivery_report`Parametresi, teslim raporlamayı yapılandırmak için kullanabileceğiniz isteğe bağlı bir parametredir. Bu, SMS iletileri teslim edildiğinde olayları yayma isteyebileceğiniz senaryolar için yararlıdır. SMS iletilerinize yönelik teslim raporlamayı yapılandırmak için [SMS olayları](../handle-sms-events.md) Hızlı Başlangıç Kılavuzu ' na bakın.

`tag`Parametresi, teslim raporuna bir etiket uygulamak için kullanabileceğiniz isteğe bağlı bir parametredir.

## <a name="run-the-code"></a>Kodu çalıştırma
Uygulamayı komut ile uygulama dizininizden çalıştırın `python` .

```console
python send-sms.py
```

Tüm Python betiği şöyle görünmelidir:

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
