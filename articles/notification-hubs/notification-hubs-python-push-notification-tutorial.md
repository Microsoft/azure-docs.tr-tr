---
title: Python ile Notification Hubs kullanma
description: Azure Notification Hubs 'yi bir Python arka ucundan nasıl kullanacağınızı öğrenin.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 5321802023d57bedc135e2d19b2289f1d66deb72
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213071"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Python 'dan Notification Hubs kullanma

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Bir Java/PHP/Python/Ruby arka ucundan tüm Notification Hubs özelliklerine, MSDN makalesinde [NOTIFICATION HUBS REST API 'lerinde](https://msdn.microsoft.com/library/dn223264.aspx)açıklandığı gibi Bildirim Hub 'ı Rest arabirimini kullanarak erişebilirsiniz.

> [!NOTE]
> Bu, Python 'da bildirim gönderilmesini uygulamaya yönelik bir örnek başvuru uygulamasıdır ve resmi olarak desteklenen bildirimler hub 'ı Python SDK 'Sı değildir. Örnek, Python 3,4 kullanılarak oluşturulmuştur.

Bu makalede nasıl yapılacağı gösterilmektedir:

- Python 'da Notification Hubs özellikleri için bir REST istemcisi oluşturun.
- Bildirim Hub 'ı REST API 'Lerine Python arabirimini kullanarak bildirimler gönderin.
- Hata ayıklama/eğitim amacıyla HTTP REST isteği/yanıtının dökümünü alın.

Tercih ettiğiniz mobil platformunuzun [Başlangıç öğreticisini](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) , Python 'daki arka uç bölümünü uygulayarak izleyebilirsiniz.

> [!NOTE]
> Örneğin kapsamı yalnızca bildirim gönderecek şekilde sınırlandırılmıştır ve kayıt yönetimi yapmaz.

## <a name="client-interface"></a>İstemci arabirimi

Ana istemci arabirimi, [.net Notification Hubs SDK 'sında](https://msdn.microsoft.com/library/jj933431.aspx)kullanılabilen yöntemlerin aynısını sağlayabilir. Bu arabirim, bu sitede Şu anda kullanılabilir olan tüm öğreticileri ve örnekleri doğrudan çevirebilmeniz ve topluluk tarafından Internet 'te katkıda bulunmenizi sağlar.

[Python REST sarmalayıcı örneği]bulunan tüm kodu bulabilirsiniz.

Örneğin, bir istemci oluşturmak için:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Windows bildirim bildirimi göndermek için:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Uygulama

Henüz yapmadıysanız, arka ucun uygulanması gereken son bölüme kadar [Başlangıç eğitmeni] takip edin.

Tam REST sarmalayıcı uygulamak için tüm ayrıntılar [MSDN](https://msdn.microsoft.com/library/dn530746.aspx)'de bulunabilir. Bu bölümde, Notification Hubs REST uç noktalarına erişmek ve bildirim göndermek için gereken ana adımların Python uygulamasını açıklanmaktadır

1. Bağlantı dizesini ayrıştırma
2. Yetkilendirme belirtecini oluşturma
3. HTTP REST API kullanarak bildirim gönderme

### <a name="parse-the-connection-string"></a>Bağlantı dizesini ayrıştırma

Oluşturucu bağlantı dizesini ayrıştırır istemcisini uygulayan ana sınıf aşağıda verilmiştir:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Güvenlik belirteci oluştur

Güvenlik belirteci oluşturma ayrıntılarına [buradan](https://msdn.microsoft.com/library/dn495627.aspx)ulaşabilirsiniz.
Geçerli isteğin URI 'sini ve `NotificationHub` bağlantı dizesinden ayıklanan kimlik bilgilerini temel alan belirteci oluşturmak için aşağıdaki yöntemleri sınıfına ekleyin.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>HTTP REST API kullanarak bildirim gönderme

İlk olarak, bir bildirimi temsil eden bir sınıf tanımla ' yı kullanalım.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Bu sınıf, yerel bir bildirim gövdesi veya bir şablon bildiriminin özellikler kümesi, biçim (yerel platform veya şablon) ve platforma özgü özellikler (Apple Expiration özelliği ve WNS üstbilgileri gibi) içeren bir üst bilgi kümesi olan bir kapsayıcıdır.

Kullanılabilir tüm seçenekler için [NOTIFICATION HUBS REST API belgelerine](https://msdn.microsoft.com/library/dn495827.aspx) ve belirli bildirim platformlarına yönelik biçimlere bakın.

Şimdi bu sınıfla, `NotificationHub` sınıfının içine gönder bildirim yöntemlerini yazın.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Bu yöntemler Bildirim Hub 'ınızın/messages uç noktasına, bildirimi göndermek için doğru gövde ve üst bilgileri içeren bir HTTP POST isteği gönderir.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Ayrıntılı günlük kaydını etkinleştirmek için hata ayıklama özelliğini kullanma

Bildirim Hub 'ını başlatırken hata ayıklama özelliğinin etkinleştirilmesi, HTTP isteği ve yanıt dökümü hakkında ayrıntılı günlük bilgilerini ve ayrıntılı bildirim iletisi gönderme sonucunu yazar.
[Notification Hubs testsend özelliği](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) , bildirim gönderme sonucuyla ilgili ayrıntılı bilgileri döndürür.
Bunu kullanmak için, aşağıdaki kodu kullanarak başlatın:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Bildirim Hub 'ı gönderme isteği HTTP URL 'SI, sonuç olarak bir "test" sorgu dizesiyle eklenir.

## <a name="complete-tutorial"></a>Öğreticiyi doldurun

Artık bir Python arka ucundan bildirim göndererek Başlarken öğreticisini tamamlayabilirsiniz.

Notification Hubs istemcinizi başlatın (bağlantı dizesini ve hub adını, [Başlangıç eğitmeni]belirtildiği gibi değiştirin):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Ardından, hedef mobil platformunuza bağlı olarak gönderme kodunu ekleyin. Bu örnek, platformu temel alarak bildirim göndermeye olanak tanımak için daha yüksek düzey yöntemler de ekler. Örneğin, Windows için send_windows_notification; send_apple_notification (Apple için) vb.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Mağazası ve Windows Phone 8,1 (Silverlight olmayan)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 ve 8,1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Python kodunuzu çalıştırmak, hedef cihazınızda bir bildirim göstermelidir.

## <a name="examples"></a>Örnekler

### <a name="enabling-the-debug-property"></a>`debug` Özelliği etkinleştirme

NotificationHub 'ı başlatırken hata ayıklama bayrağını etkinleştirdiğinizde, ayrıntılı HTTP isteği ve yanıt dökümünü ve aşağıdaki gibi NotificationOutcome görürsünüz. Bu, istekte hangi HTTP üstbilgilerinin geçtiğini ve HTTP yanıtının ne olduğunu anlayabileceğiniz yerdir. Bildirim Hub 'ından alındı:

![][1]

Örneğin ayrıntılı Bildirim Hub 'ı sonucu görürsünüz.

- ileti, anında Iletme bildirimi hizmetine başarıyla gönderildiğinde.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Herhangi bir anında iletme bildirimi için herhangi bir hedef yoksa, yanıt olarak şu çıktıyı görürsünüz (kayıtlar bir miktar eşleşmemiş olduğundan, bildirimi teslim etmek için hiçbir kayıt bulunmadığını gösterir). lerimi
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Windows 'a bildirim bildirimi

Windows istemcisine bir yayın bildirim bildirimi gönderdiğinizde gönderilen üst bilgilere dikkat edin.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Etiket (veya etiket ifadesi) belirten bildirim gönder

HTTP isteğine eklenen Etiketler HTTP üst bilgisi (aşağıdaki örnekte, bildirim yalnızca ' spor ' yüküne sahip kayıtlarla gönderilir) olduğuna dikkat edin

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Birden çok etiket belirten bildirim gönder

Birden çok etiket gönderildiğinde Etiketler HTTP üstbilgisinin nasıl değiştiği hakkında dikkat edin.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Şablonlu bildirim

Http üstbilgisinin biçimi ve yük gövdesinin HTTP istek gövdesinin bir parçası olarak gönderildiğini görürsünüz:

**İstemci tarafında kayıtlı şablon:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Sunucu tarafı-yük gönderiliyor:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Sonraki Adımlar

Bu makalede, Notification Hubs için bir Python REST istemcisinin nasıl oluşturulacağı gösterildi. Buradan şunları yapabilirsiniz:

- Bu makaledeki tüm kodu içeren tam [Python REST sarmalayıcı örneği]indirin.
- [Son Haberler öğreticisi] Notification Hubs etiketleme özelliğini öğrenmeye devam edin
- [Haber öğreticisini yerelleştirme] Notification Hubs şablonları hakkında bilgi edinmeye devam edin

<!-- URLs -->
[Python REST sarmalayıcı örneği]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Başlangıç eğitmeni]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Son Haberler öğreticisi]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Haber öğreticisini yerelleştirme]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
