---
title: Azure Işlevlerinde bir HTTP uç noktasını özelleştirme
description: Azure Işlevlerinde bir HTTP tetikleyici uç noktasını özelleştirmeyi öğrenin
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7375a46245fbe523ddf0512bb5a55371adff64e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683751"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Azure Işlevlerinde bir HTTP uç noktasını özelleştirme

Bu makalede, Azure Işlevlerinin yüksek düzeyde ölçeklenebilir API 'Ler oluşturmanıza nasıl izin verdiğini öğrenirsiniz. Azure Işlevleri, Node.js, C# ve daha fazlasını içeren çeşitli dillerde bir uç nokta yazmayı kolaylaştıran yerleşik HTTP Tetikleyicileri ve bağlamaları koleksiyonuyla birlikte gelir. Bu makalede, API tasarımınızda belirli eylemleri işlemek üzere bir HTTP tetikleyicisi özelleştireceksiniz. Ayrıca, Azure İşlev Proxy'leri ile tümleştirerek ve sahte API 'Ler ayarlayarak API 'nizi büyümeye hazırlarsınız. Bu görevler, Işlevlerin sunucusuz işlem ortamının üzerinde gerçekleştirilir. bu nedenle, kaynakları ölçeklendirmeniz gerekmez; API mantığınıza odaklanmanız yeterlidir.

## <a name="prerequisites"></a>Önkoşullar 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Sonuçta elde edilen işlev bu makalenin geri kalanında kullanılacaktır.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="customize-your-http-function"></a>HTTP işlevini özelleştirme

Varsayılan olarak HTTP tetikleyici işleviniz herhangi bir HTTP yöntemini kabul edecek şekilde yapılandırılmıştır. Varsayılan URL 'yi de kullanabilirsiniz `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . Bu bölümde, yalnızca ile istekleri almak için Yanıtla işlevini değiştirirsiniz `/api/hello` . 

1. Azure portalda işlevinize gidin. Sol menüden **tümleştirme** ' i seçin ve ardından **tetikleyici** altında **http (req)** öğesini seçin.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="HTTP işlevini özelleştirme":::

1. Aşağıdaki tabloda belirtildiği gibi HTTP tetikleyici ayarlarını kullanın.

    | Alan | Örnek değer | Description |
    |---|---|---|
    | Yol şablonu | /hello | Bu işlevi çağırmak için kullanılacak yolu belirler |
    | Yetkilendirme düzeyi | Anonim | İsteğe bağlı: İşlevinizi API anahtarı olmadan erişilebilir duruma getirir |
    | Seçili HTTP metotları | GET | Yalnızca seçilen HTTP yöntemlerinin bu işlevi çağırmak için kullanılmasını sağlar |

    `/api`Genel bir ayar tarafından işlendiği için yol şablonuna temel yol öneki dahil etmediniz.

1. **Kaydet**’i seçin.

HTTP işlevlerinin özelleştirilmesi hakkında daha fazla bilgi için bkz. [Azure IŞLEVLERI http bağlamaları](./functions-bindings-http-webhook.md).

### <a name="test-your-api"></a>API’nizi test etme

Sonra, yeni API yüzeyi ile nasıl çalıştığını görmek için işlevinizi test edin:
1. İşlev sayfasında, sol menüden **kod + test** ' i seçin.

1. Üstteki menüden **işlev URL 'Sini al** ' ı seçin ve URL 'yi kopyalayın. Şimdi yolunu kullandığını doğrulayın `/api/hello` .
 
1. URL'yi yeni bir tarayıcı sekmesine veya tercih ettiğiniz REST istemcisine kopyalayın. 

   Tarayıcılar varsayılan olarak GET kullanır.
 
1. URL 'nizin sorgu dizesine parametreler ekleyin. 

   Örneğin, `/api/hello/?name=John`.
 
1. Çalıştığını onaylamak için ENTER tuşuna basın. "*Merhaba John*" yanıtını görmeniz gerekir.

1. Ayrıca, işlevin yürütüldüğünü doğrulamak için uç noktayı başka bir HTTP yöntemiyle çağırmayı deneyebilirsiniz. Bunu yapmak için, kıvrık, Postman veya Fiddler gibi bir REST istemcisi kullanın.

## <a name="proxies-overview"></a>Proxy'lere genel bakış

Sonraki bölümde, API 'nizi bir ara sunucu üzerinden sunacağız. Azure İşlev Proxy'leri, istekleri başka kaynaklara yönlendirmenizi sağlar. Http tetikleyicisi gibi bir HTTP uç noktası tanımlarsınız. Ancak, bu uç nokta çağrıldığında yürütülecek kod yazmak yerine, uzak bir uygulamaya yönelik bir URL sağlarsınız. Bunun yapılması, API 'nizi mikro hizmetler olarak derlemek istediğinizde yararlı olan tek bir API yüzeyine çoklu API kaynakları oluşturmanıza olanak sağlar.

Proxy, herhangi bir HTTP kaynağına yönlendirme yapabilir, örneğin:
- Azure İşlevleri 
- [Azure App Service](../app-service/overview.md)'teki API uygulamaları
- [Linux App Service](../app-service/overview.md#app-service-on-linux)'teki Docker kapsayıcıları
- Barındırılan herhangi bir API

Proxy'ler hakkında daha fazla bilgi için bkz. [Azure İşlev Proxy'leri ile çalışma].

## <a name="create-your-first-proxy"></a>İlk proxy'nizi oluşturma

Bu bölümde, genel API 'niz için ön uç görevi gören yeni bir ara sunucu oluşturacaksınız. 

### <a name="setting-up-the-frontend-environment"></a>Ön uç ortamını ayarlama

Proxy'nizi oluşturacağınız yeni bir işlev uygulaması oluşturmak için [İşlev uygulaması oluşturma](./functions-get-started.md) bölümündeki adımları tekrarlayın. Bu yeni uygulamanın URL 'SI API 'imiz için ön uç işlevi görür ve daha önce düzenlediğiniz işlev uygulaması arka uç görevi görür.

1. Portalda yeni ön uç işlev uygulamanıza gidin.
1. **Platform Özellikleri**'ni ve **Uygulama Ayarları**'nı seçin.
1. Anahtar/değer çiftlerinin depolandığı **uygulama ayarlarına** aşağı kaydırın ve anahtarla yeni bir ayar oluşturun `HELLO_HOST` . Değerini arka uç işlev uygulamanızın ana bilgisayarı olacak şekilde ayarların, örneğin: `<YourBackendApp>.azurewebsites.net`. Bu değer, HTTP işlevinizi sınarken daha önce kopyaladığınız URL 'nin bir parçasıdır. Bu ayarı yapılandırmanın ilerleyen bölümlerinde kullanacaksınız.

    > [!NOTE] 
    > Proxy için sabit olarak yazılmış ortam bağımlılığı oluşmasını önleme amacıyla ana bilgisayar yapılandırması için uygulama ayarlarının kullanılması önerilir. Uygulama ayarlarını kullanarak proxy yapılandırmasını birden fazla ortamda kullanabilirsiniz ve bu durumda ortama özgü uygulama ayarları geçerli olur.

1. **Kaydet**’i seçin.

### <a name="creating-a-proxy-on-the-frontend"></a>Ön uçta proxy oluşturma

1. Portalda ön uç işlev uygulamanıza geri gidin.

1. Sol taraftaki menüde **proxy 'ler**' i seçin ve ardından **Ekle**' yi seçin. 

1. **Yeni proxy** sayfasında, aşağıdaki tabloda yer alan ayarları kullanın ve **Oluştur**' u seçin.

    | Alan | Örnek değer | Açıklama |
    |---|---|---|
    | Ad | HelloProxy | Yalnızca yönetim için kullanılan kolay ad |
    | Yol şablonu | /api/remotemerhaba | Bu proxy'yi çağırmak için kullanılacak yolu belirler |
    | Arka uç URL'si | https://%HELLO_HOST%/api/hello | İsteğe proxy uygulanacak uç noktayı belirtir |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Proxy oluşturma":::

    Azure İşlev Proxy'leri `/api` , yol şablonuna dahil olması gereken temel yol önekini sağlamaz. `%HELLO_HOST%`Sözdizimi, daha önce oluşturduğunuz uygulama ayarına başvurur. Çözümlenen URL, özgün işlevinize işaret edecektir.

1. Proxy URL 'sini kopyalayıp tarayıcıda veya sık kullandığınız HTTP istemcisi ile test ederek yeni proxy 'nizi deneyin:
    - Anonim işlev için şunu kullanın:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - Yetkilendirme kullanımı olan bir işlev için:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>Sahte API oluşturma

Daha sonra, çözümünüz için bir sahte API oluşturmak üzere bir ara sunucu kullanacaksınız. Bu proxy, arka uç tam olarak uygulanmış olması gerekmeden istemci geliştirmenin ilerlemesini sağlar. Daha sonra geliştirmede, bu mantığı destekleyen ve proxy 'nizi buna yönlendiren yeni bir işlev uygulaması oluşturabilirsiniz.

Bu sahte API 'yi oluşturmak için, bu kez [App Service Düzenleyicisi](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)kullanarak yeni bir ara sunucu oluşturacağız. Başlamak için portalda işlev uygulamanıza gidin. **Platform özellikleri**' ni seçin ve **geliştirme araçları** altında **App Service Düzenleyicisi** bulun. App Service Düzenleyicisi yeni bir sekmede açılır.

Sol gezinti bölmesinden `proxies.json` öğesini seçin. Bu dosya, tüm proxy 'lerinizin yapılandırmasını depolar. [İşlevlerden birini dağıtım yöntemlerinden](./functions-continuous-deployment.md)birini kullanırsanız, bu dosyayı kaynak denetiminde koruursunuz. Bu dosya hakkında daha fazla bilgi için bkz. [Gelişmiş proxy yapılandırması](./functions-proxies.md#advanced-configuration).

Şimdiye kadar ilerlediyseniz, üzerinde proxies.jsaşağıdaki gibi görünmelidir:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Bundan sonra, sahte API 'nizi ekleyeceksiniz. proxies.jsdosyadaki aşağıdaki kodla değiştirin:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Bu kod, özelliği olmadan yeni bir ara sunucu ekler `GetUserByName` `backendUri` . Bu proxy başka bir kaynağı çağırmak yerine yanıt geçersiz kılma özelliğini kullanarak Proxy'lerden gelen varsayılan yanıtı değiştirir. İstek ve yanıt geçersiz kılma işlemleri bir arka uç URL'si ile birlikte de kullanılabilir. Bu teknik özellikle, üst bilgileri, sorgu parametrelerini ve benzerlerini değiştirmeniz gerekebilecek eski bir sisteme proxy oluştururken kullanışlıdır. İstek ve yanıt geçersiz kılma işlemleri hakkında daha fazla bilgi için bkz. [Proxy'lerde istekleri ve yanıtları değiştirme](./functions-proxies.md).

Sahte API'nizi test etmek için bir tarayıcı veya sık kullandığınız REST istemcisini kullanarak `<YourProxyApp>.azurewebsites.net/api/users/{username}` uç noktasını çağırın. _{username}_ yerine kullanıcı adını temsil eden bir dize değeri yazmayı unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Işlevlerinde bir API oluşturmayı ve özelleştirmeyi öğrendiniz. Ayrıca sahteler dahil olmak üzere birden fazla API'yi bir araya getirerek birleştirilmiş bir API yüzeyi oluşturmayı da öğrendiniz. Bu teknikleri kullanarak istediğiniz karmaşıklık düzeyinde API'ler derleyebilir ve tümünü Azure İşlevleri tarafından sunulan sunucusuz işlem modeli üzerinde çalıştırabilirsiniz.

API'nizi geliştirirken aşağıdaki konulara da başvurabilirsiniz:

- [Azure İşlevleri HTTP bağlamaları](./functions-bindings-http-webhook.md)
- [Azure İşlev Proxy'leri ile çalışma]
- [Azure İşlevleri API'sini belgeleme (önizleme)](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Azure İşlev Proxy'leri ile çalışma]: ./functions-proxies.md
