---
title: Azure için iOS SDK 'Yı kullanma Mobile Apps
description: Azure için iOS SDK 'Yı kullanma Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: elamalani
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 175351d6bab6cc6aee06b83bdeb8544c2e7c2d24
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435383"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Azure için iOS Istemci kitaplığı 'Nı kullanma Mobile Apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu kılavuzda, en son [Azure Mobile Apps IOS SDK 'sını][1]kullanarak genel senaryolar gerçekleştirmenizi öğretilir. Azure Mobile Apps 'yi yeni kullanıyorsanız, ilk olarak [azure Mobile Apps hızlı başlangıç] tamamladıktan sonra arka uç oluşturun, tablo oluşturun ve önceden oluşturulmuş bir IOS Xcode projesini indirin. Bu kılavuzda, istemci tarafı iOS SDK 'sına odaklanıyoruz. Arka uca yönelik sunucu tarafı SDK 'Sı hakkında daha fazla bilgi edinmek için bkz. Server SDK HOWTOs.

## <a name="reference-documentation"></a>Başvuru belgeleri

İOS istemci SDK 'Sı için başvuru belgeleri şurada bulunur: [Azure Mobile Apps IOS Istemci başvurusu][2].

## <a name="supported-platforms"></a>Desteklenen platformlar

İOS SDK 'Sı, iOS sürümleri 8,0 veya üzeri için amaç-C projelerini, Swift 2,2 projelerini ve Swift 2,3 projelerini destekler.

"Sunucu-akışı" kimlik doğrulaması, sunulan kullanıcı arabirimi için bir WebView kullanır.  Cihaz bir WebView Kullanıcı arabirimini sunmıyorsa, ürünün kapsamı dışında başka bir kimlik doğrulama yöntemi gerekir.  
Bu SDK bu nedenle, Izleme türü veya benzer kısıtlanmış cihazlar için uygun değildir.

## <a name="Setup"></a>Kurulum ve Önkoşullar

Bu kılavuzda, bir tablo ile arka uç oluşturduğunuzu varsayılmaktadır. Bu kılavuzda, tablonun bu öğreticilerde bulunan tablolarla aynı şemaya sahip olduğu varsayılır. Bu kılavuzda ayrıca kodunuzda `MicrosoftAzureMobile.framework` ' a başvurduğunuzu ve `MicrosoftAzureMobile/MicrosoftAzureMobile.h` ' i içeri aktarmanızı kabul edersiniz.

## <a name="create-client"></a>Nasıl yapılır: Istemci oluşturma

Projenizdeki bir Azure Mobile Apps arka uca erişmek için bir @no__t oluşturun-0. @No__t-0 ' yı uygulama URL 'siyle değiştirin. @No__t-0 ve `applicationKey` boş bırakabilirsiniz. Kimlik doğrulaması için bir ağ geçidi ayarlarsanız, ağ geçidi URL 'siyle `gatewayURLString` ' ı doldurun.

**Amaç-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Nasıl yapılır: tablo başvurusu oluşturma

Verilere erişmek veya verileri güncelleştirmek için arka uç tablosuna başvuru oluşturun. `TodoItem` ifadesini tablonuzun adıyla değiştirin

**Amaç-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Nasıl yapılır: verileri sorgulama

Veritabanı sorgusu oluşturmak için `MSTable` nesnesini sorgulayın. Aşağıdaki sorgu `TodoItem` ' daki tüm öğeleri alır ve her öğenin metnini günlüğe kaydeder.

**Amaç-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>Nasıl yapılır: döndürülen verileri filtreleme

Sonuçları filtrelemek için, kullanılabilecek birçok seçenek vardır.

Bir koşul kullanarak filtrelemek için, `NSPredicate` ve `readWithPredicate` kullanın. Aşağıdaki filtreler yalnızca tamamlanmamış Todo öğelerini bulmak için veri döndürdü.

**Amaç-C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>Nasıl yapılır: MSQuery kullanma

Karmaşık bir sorgu (sıralama ve sayfalama dahil) gerçekleştirmek için, doğrudan veya bir koşul kullanarak `MSQuery` nesnesi oluşturun:

**Amaç-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`, birkaç sorgu davranışını denetlemenize olanak tanır.

* Sonuçların sırasını belirtin
* Döndürülecek alanları sınırla
* Döndürülecek kayıt sayısını sınırla
* Yanıt olarak toplam sayıyı belirtin
* İstekte özel sorgu dizesi parametrelerini belirtin
* Ek işlevler Uygula

Nesne üzerinde `readWithCompletion` çağırarak `MSQuery` sorgusu yürütün.

## <a name="sorting"></a>Nasıl yapılır: MSQuery ile verileri sıralama

Sonuçları sıralamak için bir örneğe bakalım. ' Text ' alanına göre artan şekilde sıralamak için ' tam ' azalan, `MSQuery` ' ı çağırarak şöyle bir şekilde çağırın:

**Amaç-C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="selecting"></a><a name="parameters"></a>Nasıl yapılır: alanları sınırlama ve sorgu dizesi parametrelerini MSQuery ile genişletme

Bir sorguda döndürülecek alanları sınırlamak için **selectfields** özelliğindeki alanların adlarını belirtin. Bu örnek yalnızca metin ve tamamlanan alanları döndürür:

**Amaç-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

Sunucu isteğine ek sorgu dizesi parametreleri eklemek için (örneğin, özel bir sunucu tarafında komut dosyası onları kullandığından) `query.parameters` ' ı şu şekilde doldurun:

**Amaç-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Nasıl yapılır: sayfa boyutunu yapılandırma

Azure Mobile Apps ile sayfa boyutu, arka uç tablolarından bir kez çekilen kayıt sayısını denetler. @No__t-0 verilerine yapılan bir çağrı, daha sonra çekilecek daha fazla kayıt kalmayana kadar Bu sayfa boyutuna bağlı olarak verileri toplu olarak kaydeder.

Aşağıda gösterildiği gibi, **Mspullsettings** kullanarak bir sayfa boyutu yapılandırmak mümkündür. Varsayılan sayfa boyutu 50 ' dir ve aşağıdaki örnek 3 ' e dönüşür.

Performans nedenleriyle farklı bir sayfa boyutu yapılandırabilirsiniz. Çok sayıda küçük veri kaydı varsa, yüksek sayfa boyutu sunucu gidiş dönüşlerin sayısını azaltır.

Bu ayar yalnızca istemci tarafındaki sayfa boyutunu denetler. İstemci, Mobile Apps arka ucunun desteklediğinden daha büyük bir sayfa boyutu isterse, arka ucun destekleyecek şekilde yapılandırıldığı sayfa boyutu üst sınıra göre belirlenir.

Bu ayar aynı zamanda *bayt boyutunda*değil veri kayıtlarının *sayısıdır* .

İstemci sayfa boyutunu artırırsanız, sunucudaki sayfa boyutunu da artırmanız gerekir. Bunu yapma adımları için ["nasıl yapılır: tablo disk belleği boyutunu ayarlama"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) bölümüne bakın.

**Amaç-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Nasıl yapılır: veri ekleme

Yeni bir tablo satırı eklemek için `NSDictionary` oluşturun ve `table insert` ' i çağırın. [Dinamik şema] etkinse, Azure App Service mobil arka uç otomatik olarak `NSDictionary` ' i temel alarak yeni sütunlar oluşturur.

@No__t-0 sağlanmazsa, arka uç otomatik olarak yeni bir benzersiz KIMLIK oluşturur. E-posta adreslerini, Kullanıcı adlarını veya kendi özel değerlerinizi KIMLIK olarak kullanmak için kendi @no__t (0) sağlayın. Kendi KIMLIĞINIZI sağlamak, kolayca katılımları ve iş odaklı veritabanı mantığını sağlar.

@No__t-0 eklenen yeni öğeyi içerir. Sunucu mantığınıza bağlı olarak, sunucuya geçirilme ile karşılaştırıldığında ek veya değiştirilmiş veriler olabilir.

**Amaç-C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>Nasıl yapılır: verileri değiştirme

Varolan bir satırı güncelleştirmek için, bir öğeyi değiştirin ve @no__t çağırın-0:

**Amaç-C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Alternatif olarak, satır KIMLIĞINI ve güncelleştirilmiş alanı sağlayın:

**Amaç-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Güncelleştirme yapılırken, en azından `id` özniteliği ayarlanmalıdır.

## <a name="deleting"></a>Nasıl yapılır: verileri silme

Bir öğeyi silmek için öğesi ile `delete` ' ı çağırın:

**Amaç-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Alternatif olarak, bir satır KIMLIĞI sağlayarak silme:

**Amaç-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

En azından, silme yapılırken `id` özniteliği ayarlanmalıdır.

## <a name="customapi"></a>Nasıl yapılır: özel API çağırma

Özel bir API ile herhangi bir arka uç işlevini kullanıma sunabilirsiniz. Bir tablo işlemiyle eşleşmesi gerekmez. Mesajlaşma üzerinde daha fazla denetim elde etmekle kalmaz, üstbilgileri okuyabilir/ayarlayabilir ve yanıt gövdesi biçimini değiştirebilirsiniz.

Özel bir API çağırmak için `MSClient.invokeAPI` ' ı çağırın. İstek ve yanıt içeriği JSON olarak değerlendirilir. Diğer medya türlerini kullanmak için [`invokeAPI` ' in diğer aşırı yüklemesini kullanın][5].  @No__t-1 isteği yerine `GET` isteği yapmak için, `HTTPMethod` parametresini `"GET"` ' e ve `nil` ' e (GET isteklerinin ileti gövdelerine sahip olmadığından)  olarak ayarlayın. Özel API 'niz diğer HTTP fiillerini destekliyorsa, `HTTPMethod` ' yı uygun şekilde değiştirin.

**Amaç-C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>Nasıl yapılır: platformlar arası bildirimler göndermek için gönderme şablonlarını kaydetme

Şablonları kaydetmek için, istemci uygulamanızda **Client. Push registerDeviceToken** metodunu kullanarak şablonları geçirin.

**Amaç-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Şablonlarınız NSDictionary türündedir ve aşağıdaki biçimde birden çok şablon içerebilir:

**Amaç-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Tüm Etiketler güvenlik isteğinden çıkarılır.  Yüklemeler içindeki yüklemelere veya şablonlara Etiketler eklemek için bkz. [Azure için .net arka uç sunucu SDK 'sı Ile çalışma Mobile Apps][4].  Bu kayıtlı şablonları kullanarak bildirim göndermek için [Notification Hubs API][3]'lerle çalışın.

## <a name="errors"></a>Nasıl yapılır: hataları Işleme

Azure App Service mobil arka ucunu çağırdığınızda, tamamlama bloğu bir `NSError` parametresi içerir. Bir hata oluştuğunda, bu parametre Nil değildir. Kodunuzda bu parametreyi denetlemeniz ve hatayı, önceki kod parçacıklarında gösterildiği gibi gereken şekilde işlemeniz gerekir.

[@No__t-1][6] dosyası `MSErrorResponseKey`, `MSErrorRequestKey` ve `MSErrorServerItemKey` sabitlerini tanımlar. Hatayla ilgili daha fazla veri almak için:

**Amaç-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Ayrıca, dosya her hata kodu için sabitleri tanımlar:

**Amaç-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Nasıl yapılır: Active Directory Authentication Library ile kullanıcıların kimliğini doğrulama

Azure Active Directory kullanarak uygulamanızdaki kullanıcıları imzalamak için Active Directory Authentication Library (ADAL) kullanabilirsiniz. Kimlik sağlayıcısı SDK 'sını kullanarak istemci akışı kimlik doğrulaması `loginWithProvider:completion:` yönteminin kullanılması tercih edilir.  İstemci akışı kimlik doğrulaması, daha yerel bir UX sağlar ve ek özelleştirmeye izin verir.

1. [Active Directory oturum açma öğreticisini App Service nasıl yapılandıracağınızı][7] izleyerek AAD oturum açma için mobil uygulama arka ucunuzu yapılandırın. Yerel istemci uygulamasını kaydetme işleminin isteğe bağlı adımını tamamladığınızdan emin olun. İOS için, yeniden yönlendirme URI 'sinin `<app-scheme>://<bundle-id>` biçiminde olmasını öneririz. Daha fazla bilgi için bkz. [adal iOS hızlı][8]başlangıcı.
2. Cocoapods kullanarak ADAL 'yi yükler. Aşağıdaki tanımı içerecek şekilde Pod dosyanızı düzenleyerek **-projenizi** Xcode projenizin adıyla değiştirin:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   ve pod:

        pod 'ADALiOS'

3. Terminal kullanarak, projenizi içeren dizinden `pod install` ' ı çalıştırın ve ardından oluşturulan Xcode çalışma alanını (proje değil) açın.
4. Aşağıdaki kodu, kullanmakta olduğunuz dile göre uygulamanıza ekleyin. Her birinde şu değişiklikleri yapın:

   * Eklentiyi, uygulamanızı sağladığınız kiracının **adıyla değiştirin.** Biçim https://login.microsoftonline.com/contoso.onmicrosoft.com olmalıdır. Bu değer, [Azure portalda]Azure Active Directory etki alanı sekmesinden kopyalanabilir.
   * **Insert-Resource-ID-burada** , mobil uygulama arka ucunuzun istemci kimliği ile değiştirin. İstemci KIMLIĞINI, portalda **Azure Active Directory ayarlar** ' ın altında bulunan **Gelişmiş** sekmesinden elde edebilirsiniz.
   * **Ekle-ISTEMCI kimliği-** ' ni yerel istemci uygulamasından KOPYALADıĞıNıZ istemci kimliğiyle değiştirin.
   * {1 & gt; **Insert-REDIRECT-URI** & lt; 1} ÖĞESINI, https şemasını kullanarak sitenizin */.Auth/login/done* uç noktasıyla değiştirin Bu değer *https://contoso.azurewebsites.net/.auth/login/done* ' e benzer olmalıdır.

**Amaç-C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Swift**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="facebook-sdk"></a>Nasıl yapılır: iOS için Facebook SDK 'Sı ile kullanıcıların kimliğini doğrulama

Kullanıcıları Facebook kullanarak uygulamanıza imzalamak için iOS için Facebook SDK 'sını kullanabilirsiniz.  @No__t-0 yönteminin kullanılması, istemci akışı kimlik doğrulamasının kullanılması tercih edilir.  İstemci akışı kimlik doğrulaması, daha yerel bir UX sağlar ve ek özelleştirmeye izin verir.

1. Facebook oturum açma öğreticisini [App Service nasıl yapılandıracağınızı][9] izleyerek Facebook oturum açma için mobil uygulama arka ucunuzu yapılandırın.
2. İOS için Facebook SDK [-kullanmaya başlama][10] belgelerini uygulayarak IOS için Facebook SDK 'sını yükler. Uygulama oluşturmak yerine, mevcut kaydınız için iOS platformunu ekleyebilirsiniz.
3. Facebook belgeleri, uygulama temsilcisinde bazı amaç-C kodu içerir. **Swift**kullanıyorsanız appdelegate. Swift için aşağıdaki çevirileri kullanabilirsiniz:

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. Projenize `FBSDKCoreKit.framework` eklemenin yanı sıra aynı şekilde `FBSDKLoginKit.framework` ' e bir başvuru da ekleyin.
5. Aşağıdaki kodu, kullanmakta olduğunuz dile göre uygulamanıza ekleyin.

    **Amaç-C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Swift**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="twitter-fabric"></a>Nasıl yapılır: iOS için Twitter dokusuna sahip kullanıcıların kimliğini doğrulama

Kullanıcıları Twitter kullanarak uygulamanıza imzalamak için iOS Fabric ' i kullanabilirsiniz. Daha yerel bir UX sunan ve ek özelleştirmeye izin verdiği için, istemci akışı kimlik doğrulaması `loginWithProvider:completion:` yönteminin kullanılması tercih edilir.

1. [Twitter oturumu açma öğreticisini yapılandırmak için App Service nasıl yapılandırılacağını](../app-service/configure-authentication-provider-twitter.md) izleyerek, mobil uygulama arka ucunuzu Twitter için yapılandırın.
2. [İOS için doku-Başlarken] belgelerini ve dallı bir paket ayarlamayı Izleyerek projenize doku ekleyin.

   > [!NOTE]
   > Varsayılan olarak, doku sizin için bir Twitter uygulaması oluşturur. Aşağıdaki kod parçacıklarını kullanarak, daha önce oluşturduğunuz tüketici anahtarını ve tüketici gizliliğini kaydederek bir uygulama oluşturmaktan kaçınabilirsiniz.    Alternatif olarak, sağladığınız tüketici anahtarı ve tüketici gizli değerlerini [Yapı panosu]gördüğünüz değerlerle App Service değiştirebilirsiniz. Bu seçeneği belirlerseniz, geri çağırma URL 'sini `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback` gibi bir yer tutucu değere ayarladığınızdan emin olun.

    Daha önce oluşturduğunuz gizli dizileri kullanmayı seçerseniz, aşağıdaki kodu uygulama temsilcinizi ekleyin:

    **Amaç-C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Swift**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. Aşağıdaki kodu, kullanmakta olduğunuz dile göre uygulamanıza ekleyin.

    **Amaç-C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Swift**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="google-sdk"></a>Nasıl yapılır: iOS için Google oturum açma SDK 'Sı ile kullanıcıların kimliğini doğrulama

Kullanıcıları bir Google hesabı kullanarak uygulamanıza imzalamak için iOS için Google oturum açma SDK 'sını kullanabilirsiniz.  Google son zamanlarda, OAuth güvenlik ilkelerine ilişkin değişiklikleri duyurdu.  Bu ilke değişikliklerinden sonra Google SDK 'nın kullanılması gerekir.

1. Google oturum açma öğreticisini [yapılandırma App Service nasıl yapılandıracağınızı](../app-service/configure-authentication-provider-google.md) izleyerek Google oturum açma için mobil uygulama arka ucunuzu yapılandırın.
2. İOS için Google [oturum açma-tümleştirme](https://developers.google.com/identity/sign-in/ios/start-integrating) belgelerini uygulayarak IOS için Google SDK 'Yı yüklemeyi başlatın. "Arka uç sunucusuyla kimlik doğrulama" bölümünü atlayabilirsiniz.
3. Kullandığınız dile göre temsilcinizin `signIn:didSignInForUser:withError:` yöntemine aşağıdakileri ekleyin.

    **Amaç-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Uygulama temsilcinizdeki `application:didFinishLaunchingWithOptions:` ' a App Service eklediğinizden emin olun, "SERVER_CLIENT_ID" değerini 1. adımdaki yapılandırmak için kullandığınız KIMLIKLE değiştirin.

    **Amaç-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Aşağıdaki kodu, kullanmakta olduğunuz dile göre `GIDSignInUIDelegate` protokolünü uygulayan bir UIViewController içinde uygulamanıza ekleyin.  Yeniden oturum açmadan önce oturumunuz açıldı ve kimlik bilgilerinizi yeniden girmeniz gerekmese de bir onay iletişim kutusu görürsünüz.  Bu yöntemi yalnızca oturum belirtecinin süresi dolduğunda çağırın.

   **Amaç-C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Swift**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile Apps hızlı başlangıç]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure portalda]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dinamik şema]: https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/dynamic-schema
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Yapı panosu]: https://www.fabric.io/home
[İOS için doku-Başlarken]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
