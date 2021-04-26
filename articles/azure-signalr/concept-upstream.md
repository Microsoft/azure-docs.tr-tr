---
title: Azure SignalR hizmetindeki yukarı akış ayarları
description: Yukarı akış ayarlarının ve yukarı akış iletilerinin protokollerinin bir özetini alın.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 6752a9564dc0d9351d1c21f5be14eb626186ac0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724073"
---
# <a name="upstream-settings"></a>Yukarı akış ayarları

Yukarı akış, Azure SignalR hizmetinin, sunucusuz moddaki bir uç nokta kümesine ileti ve bağlantı olayları göndermesini sağlayan bir önizleme özelliğidir. Sunucudan, sunucusuz moddaki bir hub yöntemi çağırmak ve istemci bağlantıları bağlandığında veya bağlantısı kesildiğinde uç noktaların bildirim almasına izin vermek için yukarı akış kullanabilirsiniz.

> [!NOTE]
> Yalnızca sunucusuz mod, yukarı akış ayarlarını yapılandırabilir.

## <a name="details-of-upstream-settings"></a>Yukarı akış ayarlarının ayrıntıları

Yukarı akış ayarları bir sıra duyarlı öğe listesinden oluşur. Her öğe aşağıdakilerden oluşur:

* İletilerin nereye gönderileceğini belirten bir URL şablonu.
* Bir kurallar kümesi.
* Kimlik doğrulama yapılandırması. 

Belirtilen olay gerçekleştiğinde, bir öğenin kuralları sırayla tek tek denetlenir. İletiler, ilk eşleşen öğenin yukarı akış URL 'sine gönderilir.

### <a name="url-template-settings"></a>URL şablonu ayarları

Farklı desenleri desteklemek için URL 'YI parametreleştirebilirsiniz. Önceden tanımlanmış üç parametre vardır:

|Önceden tanımlanmış parametre|Description|
|---------|---------|
|Hub| Hub, Azure SignalR hizmeti kavramıdır. Hub bir yalıtım birimidir. Kullanıcıların ve ileti tesliminin kapsamı bir hub ile kısıtlanır.|
|alan| Bir kategori aşağıdaki değerlerden biri olabilir: <ul><li>**Bağlantılar**: bağlantı ömrü olayları. İstemci bağlantısı bağlandığında veya bağlantısı kesildiğinde tetiklenir. Bağlı ve bağlantısı kesilmiş olayları içerir.</li><li>**mesajlar**: istemciler bir hub yöntemi çağıryüklerken tetiklenir. **Bağlantılar** kategorisinden hariç olmak üzere diğer tüm olayları içerir.</li></ul>|
|olay| **İletiler** kategorisi için, bir olay, istemcilerin gönderdikleri bir [çağırma iletisindeki](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) hedeftir. **Bağlantılar** kategorisi için yalnızca *bağlı* ve *bağlantısı kesik* kullanılır.|

Bu önceden tanımlanmış parametreler, URL modelinde kullanılabilir. Yukarı akış URL 'sini değerlendirirken Parametreler belirtilen değerle değiştirilmelidir. Örnek: 
```
http://host.com/{hub}/api/{category}/{event}
```
"Sohbet" hub 'ında bir istemci bağlantısı bağlandığında, bu URL 'ye bir ileti gönderilir:
```
http://host.com/chat/api/connections/connected
```
"Sohbet" hub 'ındaki bir istemci hub yöntemini çağırdığında `broadcast` , bu URL 'ye bir ileti gönderilir:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>URL şablonu ayarlarında gizli başvuru Key Vault

Yukarı akış URL 'SI, bekleyen şifreleme değildir. Hassas bilgileriniz varsa, bunları kaydetmek için Key Vault kullanmanız önerilir. Temel olarak, Azure SignalR hizmeti 'nin yönetilen kimliğini etkinleştirebilir ve sonra bir Key Vault örneği üzerinde okuma izni verebilir ve yukarı akış URL 'SI düzeninde düz metin yerine Key Vault başvurusu kullanabilirsiniz.

1. Sistem tarafından atanan bir kimlik veya Kullanıcı tarafından atanan kimlik ekleyin. Bkz. [Azure portalında yönetilen kimlik ekleme](./howto-use-managed-identity.md#add-a-system-assigned-identity)

2. Key Vault erişim ilkelerinde yönetilen kimlik için gizli okuma izni verin. Bkz [. Azure Portal kullanarak Key Vault erişim Ilkesi atama](../key-vault/general/assign-access-policy-portal.md)

3. Hassas metninizi, `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` yukarı akış URL 'Si deseninin sözdizimi ile değiştirin.

> [!NOTE]
> Yalnızca yukarı akış ayarlarını değiştirdiğinizde veya yönetilen kimliği değiştirdiğinizde gizli içerik rereads. Key Vault gizli dizi başvurusunu kullanmadan önce yönetilen kimlik için gizli okuma izni verildiğinden emin olun.

### <a name="rule-settings"></a>Kural ayarları

*Hub kuralları*, *Kategori kuralları* ve *olay kuralları* için kurallar oluşturabilirsiniz. Eşleşen kural üç biçimi destekler. Olay kurallarını örnek olarak alın:
- Herhangi bir olayla eşleştirmek için yıldız işareti (*) kullanın.
- Birden çok olayı birleştirmek için virgül (,) kullanın. Örneğin, `connected, disconnected` bağlı ve bağlantısı kesilmiş olaylarla eşleşir.
- Olayı eşleştirmek için tam olay adını kullanın. Örneğin, `connected` bağlantılı olayla eşleşir.

> [!NOTE]
> Azure Işlevleri ve [SignalR tetikleyicisi](../azure-functions/functions-bindings-signalr-service-trigger.md)kullanıyorsanız, SignalR tetikleyicisi aşağıdaki biçimde tek bir uç noktayı kullanıma sunar: `<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>` .
> **URL şablonu ayarlarını** bu URL 'ye yapılandırabilir ve **Kural ayarlarının** varsayılan kalmasını sağlayabilirsiniz. Ve bulma hakkında ayrıntılı bilgi için bkz. [SignalR hizmeti tümleştirmesi](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration) `<Function_App_URL>` `<API_KEY>` .

### <a name="authentication-settings"></a>Kimlik doğrulaması ayarları

Her yukarı akış ayarı öğesi için kimlik doğrulamasını ayrı ayrı yapılandırabilirsiniz. Kimlik doğrulamasını yapılandırırken, `Authentication` yukarı akış iletisinin üst bilgisinde bir belirteç ayarlanır. Şu anda Azure SignalR hizmeti aşağıdaki kimlik doğrulama türlerini destekler:
- `None`
- `ManagedIdentity`

Seçeneğini belirlediğinizde `ManagedIdentity` , Azure SignalR hizmetinde yönetilen bir kimliği önceden etkinleştirmeniz ve isteğe bağlı olarak bir kaynak belirtmeniz gerekir. Ayrıntılar için bkz. [Azure SignalR hizmeti Için Yönetilen kimlikler](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-the-azure-portal"></a>Azure portal aracılığıyla yukarı akış ayarları oluşturma

1. Azure SignalR Service 'e gidin.
2. **Ayarlar** ' ı ve **hizmet modunu** **sunucusuz** olarak değiştir ' i seçin. Yukarı akış ayarları şu şekilde görünür:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Yukarı akış ayarları":::

3. **Yukarı akış URL 'Si deseninin** altında URL 'ler ekleyin. Ardından, **hub kuralları** gibi ayarlar varsayılan değeri gösterir.
4. **Hub kuralları**, **olay kuralları**, **Kategori kuralları** ve **yukarı akış kimlik doğrulaması** ayarlarını belirlemek için **hub kuralları** değerini seçin. Ayarları düzenlemenizi sağlayan bir sayfa görüntülenir:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Yukarı akış ayarı ayrıntıları":::

5. **Yukarı akış kimlik doğrulamasını** ayarlamak için, önce yönetilen bir kimliği etkinleştirdiğinizden emin olun. Ardından **yönetilen kimliği kullan**' ı seçin. Gereksinimlerinize göre, **kimlik doğrulama kaynak kimliği** altında herhangi bir seçeneği belirleyebilirsiniz. Ayrıntılar için bkz. [Azure SignalR hizmeti Için Yönetilen kimlikler](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-resource-manager-template"></a>Kaynak Yöneticisi şablonu aracılığıyla yukarı akış ayarları oluşturma

[Azure Resource Manager şablonu](../azure-resource-manager/templates/overview.md)kullanarak yukarı akış ayarları oluşturmak için, özelliği `upstream` özelliğindeki özelliği ayarlayın `properties` . Aşağıdaki kod parçacığında, `upstream` yukarı akış ayarlarını oluşturma ve güncelleştirme özelliğinin nasıl ayarlanacağı gösterilmektedir.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>Sunucusuz protokoller

Azure SignalR hizmeti, aşağıdaki protokolleri izleyen uç noktalara iletiler gönderir. [SignalR Service Trigger bağlamasını](../azure-functions/functions-bindings-signalr-service-trigger.md) , sizin için bu protokolleri işleyen işlev uygulaması ile birlikte kullanabilirsiniz.

### <a name="method"></a>Yöntem

POST

### <a name="request-header"></a>İstek üst bilgisi

|Ad |Açıklama|
|---------|---------|
|X-ASRS-bağlantı kimliği |İstemci bağlantısı için bağlantı KIMLIĞI.|
|X-ASRS-hub |İstemci bağlantısının ait olduğu merkez.|
|X-ASRS-kategori |İletinin ait olduğu kategori.|
|X-ASRS-olay |İletinin ait olduğu olay.|
|X-ASRS-Imza |Doğrulama için kullanılan, karma tabanlı bir ileti kimlik doğrulama kodu (HMAC). Ayrıntılar için bkz. [imza](#signature) .|
|X-ASRS-Kullanıcı talepleri |İstemci bağlantısının talep grubu.|
|X-ASRS-Kullanıcı kimliği |İletiyi gönderen istemcinin kullanıcı kimliği.|
|X-ASRS-Client-Query |İstemciler hizmete bağlandıklarında isteğin sorgusu.|
|Kimlik Doğrulaması |Kullanırken isteğe bağlı bir belirteç `ManagedIdentity` . |

### <a name="request-body"></a>İstek gövdesi

#### <a name="connected"></a>Bağlı

Content-Type: Application/JSON

#### <a name="disconnected"></a>Bağlantı kesildi

İçerik türü: `application/json`

|Ad  |Tür  |Description  |
|---------|---------|---------|
|Hata |string |Kapalı bir bağlantının hata iletisi. Bağlantılar hata olmadan kapatıldığında boştur.|

#### <a name="invocation-message"></a>Çağırma iletisi

İçerik türü: `application/json` veya `application/x-msgpack`

|Ad  |Tür  |Description  |
|---------|---------|---------|
|Invocationıd |string | Bir çağrı iletisini temsil eden isteğe bağlı bir dize. [Etkinleştirmeleri](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)içindeki ayrıntıları bulun.|
|Hedef |string | Olay ile aynı ve bir [çağırma iletisindeki](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)hedefle aynı. |
|Bağımsız değişkenler |Nesne dizisi |İçinde başvurulan yönteme uygulanacak bağımsız değişkenleri içeren bir dizi `Target` . |

### <a name="signature"></a>İmza

Bu hizmet, `X-ASRS-Connection-Id` anahtar olarak hem birincil erişim anahtarını hem de ikincil erişim anahtarını kullanarak değer IÇIN SHA256 kodunu hesaplar `HMAC` . Hizmet, `X-ASRS-Signature` yukarı akış IÇIN http istekleri yaparken üst bilgide ayarlanır:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SignalR hizmeti için Yönetilen kimlikler](howto-use-managed-identity.md)
- [Azure SignalR Hizmeti ile Azure İşlevleri geliştirme ve yapılandırma](signalr-concept-serverless-development-config.md)
- [SignalR hizmetinden iletileri işleme (tetikleyici bağlama)](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [SignalR hizmeti tetikleyici bağlama örneği](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)