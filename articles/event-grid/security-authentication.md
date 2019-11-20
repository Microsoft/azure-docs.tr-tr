---
title: Azure Event Grid güvenliğini ve kimlik doğrulaması
description: Azure Event Grid ve kavramlarını açıklar.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: dfa53acaf392e225873a40b05b8517de2f9780dc
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169572"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid güvenliğini ve kimlik doğrulaması 

Azure Event Grid, kimlik doğrulaması üç tür vardır:

* Web kancası olay teslimi
* Olay abonelikleri
* Özel Konu yayımlama

## <a name="webhook-event-delivery"></a>Web kancası olay teslimi

Web kancaları olayları Azure Event Grid'den almak için birçok yöntemlerinden biridir. Yeni bir olay hazır olduğunda, Event Grid hizmet istek gövdesinde olay ile yapılandırılmış uç noktasına bir HTTP isteği gönderir.

Web kancalarını destekleyen birçok diğer hizmetleri gibi Event Grid, olayları için bu endpoint göndermeye başlamadan sahipliğini Web kancası uç noktanızın kanıtlamak gerektirir. Bu gereksinim, uç noktanızı olaylarla taşmasını gelen kötü niyetli bir kullanıcı önler. Aşağıda listelenen üç Azure hizmetlerinden herhangi birinin kullandığınızda, Azure altyapısının bu doğrulama otomatik olarak işler:

* [Event Grid Bağlayıcısı](https://docs.microsoft.com/connectors/azureeventgrid/) ile Azure Logic Apps
* [Web kancası](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) aracılığıyla Azure Otomasyonu
* [Event Grid tetikleyicisiyle](../azure-functions/functions-bindings-event-grid.md) Azure işlevleri

HTTP tetikleyicisi tabanlı Azure işlevi gibi başka türde bir uç noktasını kullanıyorsanız, Event Grid ile bir doğrulama anlaşması'na katılmak uç nokta kodunuzun gerekir. Event Grid aboneliği doğrulama iki şekilde destekler.

1. **ValidationCode el sıkışması (programlı)** : uç noktanız için kaynak kodu denetimi, bu yöntem tavsiye edilir. Olay aboneliği oluşturma sırasında Event Grid aboneliği doğrulama olayı uç noktanıza gönderir. Bu olayın şeması için başka bir Event Grid olayı benzerdir. Bu olay veri bölümünü içeren bir `validationCode` özelliği. Uygulamanızı doğrulama isteği için beklenen olay aboneliği ve doğrulama kodu Event grid'e yankılayan olduğunu doğrular. Bu anlaşma mekanizması tüm Event Grid sürümlerinde desteklenir.

2. **ValidationURL el sıkışması (el ile)** : Bazı durumlarda, kaynak kodu ValidationCode el sıkışması uygulamak için uç nokta erişemez. Örneğin, bir üçüncü taraf hizmet kullanın (gibi [Zapier](https://zapier.com) veya [IFTTT](https://ifttt.com/)), program aracılığıyla doğrulama kodu ile yanıt veremez.

   Event Grid, sürümü 2018-05-01-preview ile başlayarak, el ile doğrulama el sıkışması destekler. Bir olay aboneliği bir SDK veya API sürümü 2018-05-01-preview'ı kullanan aracıyla oluştururken ya da daha sonra Event Grid gönderir, bir `validationUrl` abonelik doğrulama olayı veri bölümünü özelliği. Anlaşma tamamlamak için bulma olay verileri ve el ile bu URL'yi bir GET isteği gönder. Bir REST istemcisi ya da web tarayıcınızı kullanabilirsiniz.

   Belirtilen URL 5 dakika için geçerlidir. Bu süre boyunca, olay aboneliğinin sağlama durumu: `AwaitingManualAction`. El ile doğrulamayı 5 dakika içinde tamamlamazsanız, sağlama durumu `Failed`olarak ayarlanır. Olay aboneliği el ile doğrulama başlatmadan önce yeniden oluşturmanız gerekir.

    Bu kimlik doğrulama mekanizması Ayrıca, el ile doğrulama moduna alınmadan önce doğrulama olayının GÖNDERISINI kabul ettiğini bilmesi için Web kancası uç noktasının 200 HTTP durum kodunu döndürmesini gerektirir. Diğer bir deyişle, uç nokta 200 döndürürse ancak programlı olarak bir doğrulama yanıtı geri dönmezse, mod el ile doğrulama moduna geçirilir. Doğrulama URL 'sinde 5 dakika içinde bir GET varsa, doğrulama el sıkışması başarılı olarak kabul edilir.

> [!NOTE]
> Doğrulama için otomatik olarak imzalanan sertifikalar kullanılması desteklenmez. Bunun yerine bir sertifika yetkilisinden (CA) imzalı bir sertifika kullanın.

### <a name="validation-details"></a>Doğrulama ayrıntıları

* Olay aboneliği oluşturma/güncelleştirme zaman Event Grid aboneliği doğrulama olayı hedef uç noktasına gönderir. 
* Olayı bir üst bilgi değeri içeren "aeg olay türü: SubscriptionValidation".
* Olay gövdesinde diğer Event Grid olaylarına aynı şemaya sahip.
* Olayın eventType özelliği `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Olayın veri özelliği içeren bir `validationCode` özelliği ile rastgele oluşturulmuş bir dize. Örneğin, "validationCode: acb13...".
* Olay verileri, aboneliği el ile doğrulamak için URL ile birlikte bir `validationUrl` özelliği de içerir.
* Dizi doğrulama olay içeriyor. Doğrulama kodu geri echo sonra gelen diğer olayları ayrı bir istek gönderilir.
* EventGrid veri düzlemi SDK'ları abonelik doğrulama olay verileri ve abonelik doğrulama yanıt karşılık gelen sınıfları içerir.

Bir örnek SubscriptionValidationEvent aşağıdaki örnekte gösterilmiştir:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Uç nokta sahipliği kanıtlamak için geri validationResponse özelliğinde doğrulama kodu aşağıdaki örnekte gösterildiği gibi echo:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Bir HTTP 200 Tamam yanıt durum kodu döndürmelidir. Kabul edilen HTTP 202, geçerli bir Event Grid Abonelik doğrulama yanıtı olarak tanınmıyor. Http isteğinin 30 saniye içinde tamamlaması gerekir. İşlem 30 saniye içinde tamamlanmazsa, işlem iptal edilir ve 5 saniye sonra yeniden denenecektir. Tüm denemeler başarısız olursa, doğrulama el sıkışma hatası olarak kabul edilir.

Veya doğrulama URL'si için bir GET isteği göndererek abonelik el ile doğrulayabilirsiniz. Olay aboneliği, onaylanana kadar bekleyen bir durumda kalır. Doğrulama URL 'Si 553 numaralı bağlantı noktasını kullanır. Güvenlik Duvarı kurallarınız bağlantı noktası 553 ' i engellerseniz, başarılı el ile el sıkışma için kuralların güncellenmesi gerekebilir.

Abonelik doğrulama anlaşma işleme ilişkin bir örnek için bkz. bir [ C# örnek](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Denetim Listesi

Olay aboneliği oluşturma sırasında, "belirtilen uç nokta https 'yi doğrulama denemesi:\//Your-Endpoint-Here hatasıyla birlikte bir hata mesajı görüyorsanız. Daha fazla ayrıntı için https:\//aka.ms/esvalidation "adresini ziyaret edin. Bu, doğrulama el sıkışmasının bir hata olduğunu gösterir. Bu hatayı gidermek için aşağıdaki durumlara doğrulayın:

* Hedef uç noktasında denetim uygulama kodu var mı? Örneğin, Azure işlevi bir HTTP tetikleyici tabanlı yazıyorsanız, değişiklik yapmak için uygulama koduna erişmeniz gerekiyor?
* Uygulama koduna erişiminiz varsa, yukarıdaki örnekte gösterildiği gibi temel ValidationCode el sıkışması mekanizması uygulayın.

* Uygulama kodu (örneğin, Web kancalarını destekleyen üçüncü taraf bir hizmet kullanıyorsanız) erişimi yoksa, el ile anlaşma mekanizması kullanabilirsiniz. Doğrulama olay validationUrl almak için 2018-05-01-Önizleme API sürümü veya üzeri (yükleme Event Grid Azure CLI uzantısı) kullandığınızdan emin olun. El ile doğrulama anlaşma tamamlanması değerini almak `validationUrl` özelliği web tarayıcınızda bu URL'yi ziyaret edin. Doğrulama başarılı olursa, doğrulama başarılı olduğunu, web tarayıcınızda bir ileti görürsünüz. Olay aboneliğinin provisioningState "başarılı olduğunu" görürsünüz. 

### <a name="event-delivery-security"></a>Olay teslimi güvenliği

#### <a name="azure-ad"></a>Azure AD

Azure Active Directory kullanarak Web kancası uç noktanızı güvenli hale getirerek olayları, uç noktalarınıza yayımlamak üzere Event Grid kimlik doğrulaması ve yetkilendirmek için kullanabilirsiniz. Bir Azure Active Directory uygulaması oluşturmanız, Event Grid uygulamanızda bir rol ve hizmet ilkesi oluşturmanız ve olay aboneliğini Azure AD uygulamasını kullanacak şekilde yapılandırmanız gerekecektir. [AAD 'yi Event Grid ile yapılandırmayı öğrenin](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Sorgu parametreleri
Bir olay aboneliği oluştururken, Web kancası URL'si sorgu parametreleri ekleyerek, Web kancası uç noktası güvenli hale getirebilirsiniz. Ayarlanmış bir gizli dizi gibi olması için bu sorgu parametreleri bir [erişim belirteci](https://en.wikipedia.org/wiki/Access_token). Web kancası olay geçerli izinleriyle Event Grid'den gelen tanımak için gizli anahtarı kullanabilirsiniz. Olay Kılavuzu her Web kancası olay teslimi bu sorgu parametreleri içerir.

Olay aboneliği düzenlerken, sorgu parametreleri görüntülenmiyor veya sürece döndürülen [--dahil tam-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parametresi Azure'da kullanılan [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Son olarak, Azure Event Grid yalnızca HTTPS Web kancası uç noktaları desteklediğini unutmayın.

## <a name="event-subscription"></a>Olay aboneliği

Bir olaya abone olmak için olay kaynağı ve işleyici erişimi olmasını kanıtlamaları gerekir. Bir Web kancası sahibi olduğunu doğrulayan ve önceki bölümde ele. Bir Web kancası (örneğin, bir olay hub'ı veya kuyruk depolama) olmayan bir olay işleyicisi kullanıyorsanız, bu kaynağa yazma erişimi gerekir. Bu izinleri denetimi, yetkisiz bir kullanıcı, kaynağınıza olayları göndermesini engeller.

Olmalıdır **Microsoft.EventGrid/EventSubscriptions/Write** olay kaynağı olan kaynağı izni. Kaynağın yeni bir abonelik kapsamda yazmakta olduğunuz çünkü bu iznine sahip olmanız gerekir. Gerekli kaynak sistem konusu ya da özel konuya abone göre farklılık gösterir. Bu bölümde iki türü de açıklanmaktadır.

### <a name="system-topics-azure-service-publishers"></a>Sistem konuları (Azure hizmet yayımcılar)

Sistem konuları için yeni bir olay aboneliği kapsamında olayın yayımlanması kaynak yazma izni gerekir. Kaynak biçimi şu şekildedir: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Örneğin, bir depolama hesabı üzerinde bir olaya abone olmak için adlı **myacct**, üzerinde Microsoft.EventGrid/EventSubscriptions/Write izninizin olması gerekiyor: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Özel konular

Özel konu için yeni olay aboneliğinizi kapsamında olay Kılavuzu konusu yazma izni gerekir. Kaynak biçimi şu şekildedir: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Örneğin, bir özel konuya abone olma adlı **mytopic**, üzerinde Microsoft.EventGrid/EventSubscriptions/Write izninizin olması gerekiyor: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Özel Konu yayımlama

Özel konu, paylaşılan erişim imzası (SAS) veya anahtar kimlik doğrulaması kullanın. SAS öneririz, ancak anahtar kimlik doğrulaması basit bir programlama sağlar ve birçok mevcut Web kancası yayımcıları ile uyumludur. 

HTTP üst bilgisi kimlik doğrulaması değeri içerir. SAS için kullanmak **aeg sas belirteci** üstbilgi değeri. Anahtar kimlik doğrulaması için **aeg sas anahtarı** üstbilgi değeri.

### <a name="key-authentication"></a>Anahtar kimlik doğrulaması

Anahtar kimlik doğrulaması, kimlik doğrulamasının en basit biçimidir. Biçimini kullanın: `aeg-sas-key: <your key>`

Örneğin, bir anahtar ile geçirin:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS belirteçleri

Event Grid için SAS belirteci, kaynak, sona erme süresini ve imza içerir. SAS belirteci biçimi: `r={resource}&e={expiration}&s={signature}`.

Kaynak için olayları gönderiyorsunuz olay Kılavuzu konusu yoludur. Örneğin, geçerli bir kaynak yolu şöyledir: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Bir anahtar, imza üret

Örneğin, geçerli **aeg sas belirteci** değerdir:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Aşağıdaki örnek, Event Grid ile kullanmak için bir SAS belirteci oluşturur:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Yönetim erişim denetimi

Azure Event Grid olay abonelikleri listesi gibi çeşitli yönetim işlemlerini yapmak için yenilerini oluşturun ve anahtarlar oluşturmak için farklı kullanıcılara verilen erişim düzeyini denetlemenize olanak tanır. Event Grid, Azure'nın rol tabanlı erişim denetimi (RBAC) kullanır.

### <a name="operation-types"></a>İşlem türleri

Event Grid, aşağıdaki eylemleri destekler:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Son üç işlemi dışında normal okuma işlemleri filtrelenmiş büyük olasılıkla gizli bilgi döndürür. Bu işlemler için erişimi kısıtlamak önerilir. 

### <a name="built-in-roles"></a>Yerleşik roller

Event Grid Olay Aboneliklerini yönetmek için iki yerleşik rol sağlar. Uygularken önemli oldukları [olay etki alanı](event-domains.md) çünkü bunlar kullanıcılara konular, olay etki alanı, abone olmak için ihtiyaç duydukları izinleri verin. Bu roller, olay abonelikleri üzerinde odaklanır ve konuları oluşturma gibi eylemler için erişim izni yok.

Yapabilecekleriniz [bir kullanıcı veya grup bu rolleri Ata](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Eventgrid EventSubscription katılımcısı**: Event Grid abonelik işlemlerini yönetme

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**Eventgrid EventSubscription okuyucusu**: Event Grid abonelikleri okuma

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Özel roller

Yerleşik roller farklı olan izinleri belirtmeniz gerekiyorsa, özel roller oluşturabilirsiniz.

Farklı eylemlerde bulunmak kullanıcıların örnek Event Grid rol tanımları aşağıda verilmiştir. Bunlar yalnızca olay abonelikleri daha geniş erişim vermek için bu özel roller yerleşik rollerden farklıdır.

**EventGridReadOnlyRole.json**: yalnızca salt okunur işlemlere izin verin.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: izin kısıtlı sonrası eylemler ancak Sil eylemlerinin izin vermeyin.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: tüm event grid Eylemler sağlar.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Özel rollerle oluşturabilirsiniz [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md), ve [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Sonraki adımlar

* Event grid'e giriş için bkz [Event Grid hakkında](overview.md)
