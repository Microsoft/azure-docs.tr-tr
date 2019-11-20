---
title: Azure Işlevlerinde bir HTTP uç noktasını özelleştirme
description: Azure Işlevlerinde bir HTTP tetikleyici uç noktasını özelleştirmeyi öğrenin
author: mattchenderson
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: f7729f5acb8b7b95004265f6802ba2feb1bc3cd7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096751"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Azure Işlevlerinde bir HTTP uç noktasını özelleştirme

Bu makalede, Azure Işlevlerinin yüksek düzeyde ölçeklenebilir API 'Ler oluşturmanıza nasıl izin verdiğini öğrenirsiniz. Azure İşlevleri Node.JS, C# ve daha birçok dilde uç nokta yazmayı kolaylaştıran yerleşik HTTP tetikleyicisi ve bağlama koleksiyonuna sahiptir. Bu makalede, API tasarımınızda belirli eylemleri işlemek üzere bir HTTP tetikleyicisi özelleştirecek. Ayrıca Azure İşlev Proxy'leri tümleştirmesi yapıp sahte API'ler oluşturarak API'nizi ölçeklendirmeye hazır hale getireceksiniz. Tüm bu işlemler Azure İşlevleri'nin sunucusuz işlem ortamında gerçekleştirildiğinden kaynak ölçeklendirme konusunda endişelenmenize gerek yoktur. Tek yapmanız gereken API'nizin mantığına yoğunlaşmaktır.

## <a name="prerequisites"></a>Önkoşullar 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Sonuçta elde edilen işlev bu makalenin geri kalanında kullanılacaktır.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure portalı açın. Bunu yapmak için, Azure hesabınızla [https://portal.azure.com](https://portal.azure.com) oturumu açın.

## <a name="customize-your-http-function"></a>HTTP işlevini özelleştirme

HTTP ile tetiklenen işleviniz varsayılan olarak tüm HTTP yöntemlerini kabul edecek şekilde yapılandırılmıştır. Ayrıca `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` biçiminde varsayılan URL de mevcuttur. Hızlı başlangıç adımlarını takip ettiyseniz `<funcname>` değeri muhtemelen "HttpTriggerJS1" şeklinde olacaktır. Bu bölümde işlevi yalnızca `/api/hello` yoluna yönlendirilen GET isteklerine yanıt verecek şekilde değiştireceksiniz. 

1. Azure portalda işlevinize gidin. Sol gezinti bölmesinden **Tümleştir**'i seçin.

    ![HTTP işlevini özelleştirme](./media/functions-create-serverless-api/customizing-http.png)

1. Tabloda belirtilen HTTP tetikleyici ayarlarını kullanın.

    | Alan | Örnek değer | Açıklama |
    |---|---|---|
    | İzin verilen HTTP metotları | Seçilen yöntemler | Bu işlevi çağırmak için kullanılabilecek HTTP yöntemlerini belirler |
    | Seçili HTTP metotları | GET | Yalnızca seçilen HTTP yöntemlerinin bu işlevi çağırmak için kullanılmasını sağlar |
    | Yol şablonu | /hello | Bu işlevi çağırmak için kullanılacak yolu belirler |
    | Yetkilendirme Düzeyi | Adsız | İsteğe bağlı: Bir API anahtarı olmadan işlevinizin erişilebilir olmasını sağlar |

    > [!NOTE] 
    > Genel ayar tarafından işlendiği için `/api` temel yol ön ekini yol şablonuna dahil etmediniz.

1. **Kaydet**’e tıklayın.

HTTP işlevlerini özelleştirme hakkında daha fazla bilgi için bkz. [Azure İşlevleri HTTP bağlamaları](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook).

### <a name="test-your-api"></a>API’nizi test etme

Şimdi işlevinizi test ederek yeni API yüzeyiyle nasıl çalıştığını görün.
1. Sol gezinti bölmesinde işlevin adına tıklayarak geliştirme sayfasına dönün.
1. **İşlev URL'sini al**'a tıklayıp URL'yi kopyalayın. Artık `/api/hello` yolunu kullandığını görmeniz gerekir.
1. URL'yi yeni bir tarayıcı sekmesine veya tercih ettiğiniz REST istemcisine kopyalayın. Tarayıcılar varsayılan olarak GET kullanır.
1. URL'nizdeki sorgu dizesine parametre ekleyin, örneğin: `/api/hello/?name=John`
1. 'Enter' tuşuna basıp çalıştığını onaylayın. "*Hello John*" yanıtını görmeniz gerekir.
1. Ayrıca uç noktayı başka bir HTTP yöntemi ile çağırmayı deneyerek işlevin yürütülmediğini onaylayabilirsiniz. Bunun için cURL, Postman veya Fiddler gibi bir REST istemcisi kullanmanız gerekir.

## <a name="proxies-overview"></a>Proxy'lere genel bakış

Bir sonraki bölümde API'nizi proxy aracılığıyla erişilebilir duruma getireceksiniz. Azure İşlev Proxy'leri, istekleri başka kaynaklara yönlendirmenizi sağlar. HTTP tetikleyicisinde olduğu gibi bir HTTP uç noktası tanımlarsınız ancak bu uç nokta çağrıldığında yürütülecek bir kod yazmak yerine yürütmenin uzakta gerçekleştirilmesi için bir URL sağlarsınız. Bu sayede tek bir API yüzeyinde birden fazla API kaynağı oluşturabilir ve tüketiciler için kolaylık sağlayabilirsiniz. Bu işlev özellikle API'nizi mikro hizmetler olarak derlemek istediğiniz durumlarda kullanışlı olacaktır.

Proxy, herhangi bir HTTP kaynağına yönlendirme yapabilir, örneğin:
- Azure İşlevleri 
- [Azure App Service](https://docs.microsoft.com/azure/app-service/overview)'teki API uygulamaları
- [Linux App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)'teki Docker kapsayıcıları
- Barındırılan herhangi bir API

Proxy'ler hakkında daha fazla bilgi için bkz. [Azure İşlev Proxy'leri ile çalışma].

## <a name="create-your-first-proxy"></a>İlk proxy'nizi oluşturma

Bu bölümde API'nizin ön ucu olarak görev yapacak yeni bir proxy oluşturacaksınız. 

### <a name="setting-up-the-frontend-environment"></a>Ön uç ortamını ayarlama

Proxy'nizi oluşturacağınız yeni bir işlev uygulaması oluşturmak için [İşlev uygulaması oluşturma](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) bölümündeki adımları tekrarlayın. Bu yeni uygulamanın URL'si, API'nizin ön ucu olacak ve önceden düzenlediğiniz işlev uygulaması arka uç olarak görev yapacak.

1. Portalda yeni ön uç işlev uygulamanıza gidin.
1. **Platform Özellikleri**'ni ve **Uygulama Ayarları**'nı seçin.
1. Anahtar/değer çiftlerinin depolandığı **Uygulama ayarları** bölümüne inin ve "HELLO_HOST" anahtarıyla yeni bir ayar oluşturun. Değerini arka uç işlev uygulamanızın ana bilgisayarı olacak şekilde ayarların, örneğin: `<YourBackendApp>.azurewebsites.net`. Bu değer HTTP işlevinizi test ederken kopyaladığınız URL'nin bir bölümüdür. Bu ayarı yapılandırmanın ilerleyen bölümlerinde kullanacaksınız.

    > [!NOTE] 
    > Proxy için sabit olarak yazılmış ortam bağımlılığı oluşmasını önleme amacıyla ana bilgisayar yapılandırması için uygulama ayarlarının kullanılması önerilir. Uygulama ayarlarını kullanarak proxy yapılandırmasını birden fazla ortamda kullanabilirsiniz ve bu durumda ortama özgü uygulama ayarları geçerli olur.

1. **Kaydet**’e tıklayın.

### <a name="creating-a-proxy-on-the-frontend"></a>Ön uçta proxy oluşturma

1. Portalda ön uç işlev uygulamanıza dönün.
1. Sol gezinti bölmesinde "Proxy'ler" girişinin yanındaki artı işaretine '+' tıklayın.
    ![Proxy oluşturma](./media/functions-create-serverless-api/creating-proxy.png)
1. Tabloda belirtilen proxy ayarlarını kullanın. 

    | Alan | Örnek değer | Açıklama |
    |---|---|---|
    | Name | HelloProxy | Yalnızca yönetim için kullanılan kolay ad |
    | Yol şablonu | /api/remotemerhaba | Bu proxy'yi çağırmak için kullanılacak yolu belirler |
    | Arka uç URL'si | https://%HELLO_HOST%/api/hello | İsteğe proxy uygulanacak uç noktayı belirtir |
    
1. Proxy'ler `/api` temel yol ön ekini sağlamaz ve bu ekin yol şablonuna dahil edilmesi gerekir.
1. `%HELLO_HOST%` söz dizimi önceden oluşturduğunuz uygulama ayarına başvuracaktır. Çözümlenen URL, özgün işlevinize işaret edecektir.
1. **Oluştur**'a tıklayın.
1. Yeni proxy'yi denemek için Proxy URL'sini kopyalayıp tarayıcıda veya sık kullandığınız HTTP istemcisinde test edebilirsiniz.
    1. Anonim işlev için şunu kullanın:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    1. Yetkilendirme özelliğine sahip işlev için şunu kullanın:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Sahte API oluşturma

Bu adımda proxy kullanarak çözümünüz için sahte API oluşturacaksınız. Bu işlem arka uç uygulaması tamamlanmadan istemci geliştirme sürecinin devam etmesini sağlar. Geliştirmenin sonraki aşamalarında bu mantığı destekleyen ve proxy'nizi yönlendiren yeni bir işlev uygulaması oluşturabilirsiniz.

Bu sahte API'yi oluşturmak için yeni bir proxy oluşturacağız ve bu kez [App Service Düzenleyicisi](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)'ni kullanacağız. Başlamak için portalda işlev uygulamanıza gidin. **Platform Özellikleri**'ni seçin ve **Geliştirme Araçları** bölümünde **App Service Düzenleyicisi**'ni bulun. Tıkladığınızda App Service Düzenleyicisi yeni bir sekmede açılır.

Sol gezinti bölmesinden `proxies.json` öğesini seçin. Bu, tüm proxy'lerinizin yapılandırmasının bulunduğu dosyadır. [İşlev dağıtım yöntemlerinden](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) birini kullanıyorsanız kaynak denetiminde tutmanız gereken dosya budur. Bu dosya hakkında daha fazla bilgi için bkz. [Gelişmiş proxy yapılandırması](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Bu noktaya kadar gösterilen adımları uyguladıysanız proxies.json dosyanız şu şekilde görünmelidir:

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

Şimdi sahte API'nizi ekleyeceksiniz. proxies.json dosyanızı şu şekilde değiştirin:

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

Bu kod backendUri özelliği olmayan "GetUserByName" adlı yeni bir proxy ekler. Bu proxy başka bir kaynağı çağırmak yerine yanıt geçersiz kılma özelliğini kullanarak Proxy'lerden gelen varsayılan yanıtı değiştirir. İstek ve yanıt geçersiz kılma işlemleri bir arka uç URL'si ile birlikte de kullanılabilir. Bu özellikle üst bilgi ve sorgu parametresi gibi değerleri değiştirmenizin gerekebileceği eski bir sisteme proxy ile erişme aşamasında kullanışlı olabilir. İstek ve yanıt geçersiz kılma işlemleri hakkında daha fazla bilgi için bkz. [Proxy'lerde istekleri ve yanıtları değiştirme](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

Sahte API'nizi test etmek için bir tarayıcı veya sık kullandığınız REST istemcisini kullanarak `<YourProxyApp>.azurewebsites.net/api/users/{username}` uç noktasını çağırın. _{username}_ yerine kullanıcı adını temsil eden bir dize değeri yazmayı unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Işlevlerinde bir API oluşturmayı ve özelleştirmeyi öğrendiniz. Ayrıca sahteler dahil olmak üzere birden fazla API'yi bir araya getirerek birleştirilmiş bir API yüzeyi oluşturmayı da öğrendiniz. Bu teknikleri kullanarak istediğiniz karmaşıklık düzeyinde API'ler derleyebilir ve tümünü Azure İşlevleri tarafından sunulan sunucusuz işlem modeli üzerinde çalıştırabilirsiniz.

API'nizi geliştirirken aşağıdaki konulara da başvurabilirsiniz:

- [Azure İşlevleri HTTP bağlamaları](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Azure İşlev Proxy'leri ile çalışma]
- [Azure İşlevleri API'sini belgeleme (önizleme)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Azure İşlev Proxy'leri ile çalışma]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
