---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 31704e705b828cc0070e3b79f5d527cfa9deb0c3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386655"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce şunları yaptığınızdan emin olun:

- Etkin abonelikle bir Azure hesabı oluşturun. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 'ı yükler
- Bir Azure Iletişim Hizmetleri kaynağı oluşturun. Ayrıntılar için bkz. [Azure Iletişim kaynağı oluşturma](../../create-communication-resource.md). Bu hızlı başlangıç için kaynak **uç** noktanızı kaydetmeniz gerekir
- Bir [Kullanıcı erişim belirteci](../../access-tokens.md). Kapsamı "sohbet" olarak ayarladığınızdan emin olun ve belirteç dizesinin yanı sıra Kullanıcı kimliği dizesini de unutmayın.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-python-application"></a>Yeni Python uygulaması oluşturma

Terminal veya komut pencerenizi açın, uygulamanız için yeni bir dizin oluşturun ve bu dizine gidin.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Proje kök dizininde **Start-chat.py** adlı bir dosya oluşturmak ve temel özel durum işleme dahil olmak üzere programın yapısını eklemek için bir metin düzenleyicisi kullanın. Bu hızlı başlangıç için tüm kaynak kodu aşağıdaki bölümlerde bu dosyaya ekleyeceksiniz.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>SDK yükleme

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Python için Azure Communication Services sohbet SDK 'sının önemli özelliklerinden bazılarını idare edebilir.

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Bu sınıf, sohbet işlevselliği için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve iş parçacıklarını oluşturmak, almak ve silmek için kullanın. |
| ChatThreadClient | Bu sınıf, sohbet Iş parçacığı işlevselliği için gereklidir. ChatClient aracılığıyla bir örnek edinirsiniz ve bu örneği kullanarak ileti gönderebilir/alabilir/güncelleştirebilir/silebilirsiniz, kullanıcıları ekleyin/kaldırın/alın, yazma bildirimleri gönderin ve okundu bilgilerini okuyun. |

## <a name="create-a-chat-client"></a>Sohbet istemcisi oluşturma

Bir sohbet istemcisi oluşturmak için, Iletişim hizmeti uç noktası ' nı ve `Access Token` Önkoşul adımlarının bir parçası olarak oluşturulan ' i kullanacaksınız. [Kullanıcı erişim belirteçleri](../../access-tokens.md)hakkında daha fazla bilgi edinin.

Bu hızlı başlangıç, sohbet uygulamanız için belirteçleri yönetmek üzere bir hizmet katmanı oluşturmayı kapsamaz, ancak önerilir. Daha ayrıntılı [sohbet mimarisi](../../../concepts/chat/concepts.md) için aşağıdaki belgelere bakın

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Sohbet iş parçacığı başlatma

`create_chat_thread`Bir sohbet iş parçacığı oluşturmak için yöntemini kullanın.

- `topic`İş parçacığı konusu vermek için kullanın; Konu, işlevi kullanılarak sohbet iş parçacığı oluşturulduktan sonra güncelleştirilemeyebilir `update_thread` .
- `thread_participants` `ChatParticipant` Sohbet iş parçacığına eklenecek olan öğesini listelemek için kullanın, `ChatParticipant` `CommunicationUserIdentifier` `user` [bir kullanıcı oluşturarak](../../access-tokens.md#create-an-identity) oluşturduğunuz tarihten sonra aldığınız gibi, türü olarak alır

`CreateChatThreadResult` Sonuç, iş parçacığı oluşturma işleminden döndürülen, `id` oluşturulan sohbet iş parçacığını getirmek için onu kullanabilirsiniz. Bu `id` , daha sonra `ChatThreadClient` yöntemi kullanılarak bir nesne getirmek için kullanılabilir `get_chat_thread_client` . `ChatThreadClient` Bu sohbet iş parçacığı için başka sohbet işlemleri gerçekleştirmek üzere kullanılabilir.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Sohbet iş parçacığı istemcisi al
`get_chat_thread_client`Yöntemi, zaten var olan bir iş parçacığı için bir iş parçacığı istemcisi döndürür. Oluşturulan iş parçacığında işlem gerçekleştirmek için kullanılabilir: katılımcı ekleme, ileti gönderme vb. thread_id, mevcut sohbet iş parçacığının benzersiz KIMLIĞIDIR.

`ChatThreadClient` Bu sohbet iş parçacığı için başka sohbet işlemleri gerçekleştirmek üzere kullanılabilir.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Tüm sohbet iş parçacıklarını Listele
`list_chat_threads`Yöntemi türünde bir yineleyici döndürür `ChatThreadItem` . Tüm sohbet iş parçacıklarını listelemek için kullanılabilir.

- `start_time`Sohbet iş parçacıklarının alınacağı en erken zaman noktasını belirtmek için kullanın.
- `results_per_page`Sayfa başına döndürülen en fazla sohbet iş parçacığı sayısını belirtmek için kullanın.

Bir yineleyici, `[ChatThreadItem]` listeleme iş parçacıklarından döndürülen yanıttır

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Sohbet iş parçacığına ileti gönderin

`send_message`Yeni oluşturduğunuz bir sohbet iş parçacığına ileti göndermek için yöntemini kullanın, thread_id tarafından tanımlanır.

- `content`Sohbet iletisi içeriğini sağlamak için kullanın;
- `chat_message_type`İleti içerik türünü belirtmek için kullanın. Olası değerler şunlardır ' text ' ve ' HTML '; belirtilmemişse, varsayılan ' text ' değeri atanmamıştır.
- `sender_display_name`Gönderenin görünen adını belirtmek için kullanın;

`SendChatMessageResult` yanıt ileti göndermekten döndürülen yanıt, iletinin benzersiz KIMLIĞI olan bir ID içeriyor.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Sohbet iş parçacığından sohbet iletileri alma

Belirtilen aralıklarda yöntemi yoklayarak sohbet iletileri alabilirsiniz `list_messages` .

- `results_per_page`Sayfa başına döndürülecek en fazla ileti sayısını belirtmek için kullanın.
- `start_time`İletileri almak için en erken zaman noktasını belirtmek üzere kullanın.

Bir yineleyici, `[ChatMessage]` Listeleme iletilerinden döndürülen yanıttır

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` ve kullanarak iletide gerçekleşen tüm düzenleme veya silme işlemleri dahil olmak üzere iletinin en son sürümünü döndürür `update_message` `delete_message` . Silinen iletiler için `ChatMessage.deleted_on` , iletinin silindiğini gösteren bir tarih saat değeri döndürür. Düzenlenen iletiler için, `ChatMessage.edited_on` iletinin ne zaman düzenlendiğini gösteren bir tarih saat döndürür. İleti oluşturmaya yönelik özgün saate, `ChatMessage.created_on` iletileri sıralamak için kullanılabilecek kullanılarak erişilebilir.

`list_messages` tarafından tanımlanabilecek farklı ileti türlerini döndürür `ChatMessage.type` . 

Ileti türleri hakkında buradan daha fazla bilgi edinin: [Ileti türleri](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Okundu bilgisi gönder
`send_read_receipt`Yöntemi, bir kullanıcı adına bir iş parçacığına okundu bilgisi olayı vermek için kullanılabilir.

- `message_id`Geçerli Kullanıcı tarafından okunan en son ILETI kimliğini belirtmek için kullanın.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Sohbet iş parçacığına katılımcı olarak Kullanıcı ekleme

Sohbet iş parçacığı oluşturulduktan sonra, bundan sonra kullanıcı ekleyebilir ve kaldırabilirsiniz. Kullanıcıları ekleyerek, sohbet iş parçacığına ileti gönderebilmeleri ve başka katılımcılar ekleyip kaldırabilmesi için onlara erişim izni verirsiniz. Yöntemi çağırmadan önce `add_participants` , bu kullanıcı için yeni bir erişim belirteci ve kimliği aldığınızdan emin olun. Kullanıcının sohbet istemcisini başlatması için bu erişim belirtecine ihtiyacı olacak.

Bir veya daha fazla Kullanıcı, yöntemi kullanılarak sohbet iş parçacığına eklenebilir, bu, `add_participants` Yeni erişim belirteci ve tanımlanabilmesi tüm kullanıcılar için kullanılabilir.

Bir `list(tuple(ChatParticipant, CommunicationError))` döndürülür. Katılımcı başarıyla eklendiğinde boş bir liste beklenmektedir. Katılımcı eklenirken bir hata olması durumunda, listede başarısız katılımcılar ve karşılaşılan hata ile birlikte doldurulur.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Sohbet iş parçacığında iş parçacığı katılımcıları listeleme

Katılımcı eklemeye benzer şekilde, bir iş parçacığından katılımcıları de listeleyebilirsiniz.

`list_participants`İş parçacığının katılımcılarını almak için kullanın.
- `results_per_page`Sayfa başına döndürülecek en fazla katılımcı sayısını, isteğe bağlı olarak kullanın.
- `skip`Yanıt olarak belirtilen konuma kadar katılımcıları atlayıp, isteğe bağlı ' yı kullanın.

Yineleyici, `[ChatParticipant]` liste katılımcılarından döndürülen yanıttır

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı komut ile uygulama dizininizden çalıştırın `python` .

```console
python start-chat.py
```
