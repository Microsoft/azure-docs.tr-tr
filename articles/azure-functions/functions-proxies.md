---
title: Azure Işlevleri 'nde proxy ile çalışma
description: Azure İşlev Proxy'leri kullanmaya genel bakış
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: fb263239f99bcb4ec4c893b700d5c1cce078659f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601382"
---
# <a name="work-with-azure-functions-proxies"></a>Azure İşlev Proxy'leri çalışın

Bu makalede Azure İşlev Proxy'leri yapılandırma ve ile çalışmayı açıklar. Bu özellikle, işlev uygulamanızda başka bir kaynak tarafından uygulanan uç noktaları belirtebilirsiniz. Bu proxy 'leri, büyük bir API 'yi birden çok işlev uygulamasına bölmek için kullanabilirsiniz (mikro hizmet mimarisinde olduğu gibi), yine de istemciler için tek bir API yüzeyi sunmaya devam edebilirsiniz.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standart Işlevler faturalandırma, proxy yürütmeleri için geçerlidir. Daha fazla bilgi için bkz. [Azure işlevleri fiyatlandırması](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Ara sunucu oluşturma

Bu bölümde, Işlevler portalında bir ara sunucu oluşturma konusu gösterilmektedir.

1. [Azure Portal]açın ve ardından işlev uygulamanıza gidin.
2. Sol bölmede **yeni ara sunucu**' yı seçin.
3. Ara sunucunuz için bir ad sağlayın.
4. **Yönlendirme şablonunu** ve **http yöntemlerini** belirterek bu işlev uygulamasında gösterilen uç noktayı yapılandırın. Bu parametreler, [http tetikleyicilerinin]kurallarına göre davranır.
5. **Arka uç URL 'sini** başka bir uç noktaya ayarlayın. Bu uç nokta, başka bir işlev uygulamasındaki bir işlev veya başka bir API olabilir. Değerin statik olması gerekmez ve [özgün istemci isteğinden] [uygulama ayarlarına] ve parametrelerine başvurabilir.
6. **Oluştur**’a tıklayın.

Proxy 'niz artık işlev uygulamanızda yeni bir uç nokta olarak var. Bir istemci perspektifinden, Azure Işlevlerinde bir HttpTrigger ile eşdeğerdir. Proxy URL 'sini kopyalayıp en sevdiğiniz HTTP istemcisiyle test ederek yeni proxy 'nizi deneyebilirsiniz.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>İstekleri ve yanıtları değiştirme

Azure İşlev Proxy'leri, arka uçtaki istekleri ve yanıtları değiştirebilir. Bu dönüşümler değişkenleri [kullanım değişkenlerinde]tanımlanan şekilde kullanabilir.

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Arka uç isteğini değiştirme

Varsayılan olarak, arka uç isteği özgün isteğin bir kopyası olarak başlatılır. Arka uç URL 'sini ayarlamaya ek olarak, HTTP yönteminde, başlıklarında ve sorgu dizesi parametrelerinde değişiklik yapabilirsiniz. Değiştirilen değerler, [özgün istemci isteğinden] [uygulama ayarlarına] ve parametrelere başvurabilir.

Arka uç istekleri portalda, proxy ayrıntısı sayfasının *istek geçersiz kılma* bölümüne genişleterek değişiklik yapılabilir. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Yanıtı değiştirme

Varsayılan olarak, istemci yanıtı arka uç yanıtının bir kopyası olarak başlatılır. Yanıtın durum kodunda, neden tümceciğinin, üst bilgilerinde ve gövdede değişiklik yapabilirsiniz. Değiştirilen değerler [uygulama ayarlarına], [özgün istemci isteğinden parametrelere]ve [arka uç yanıtından parametrelere]başvurabilir.

Arka uç istekleri portalda, proxy ayrıntısı sayfasının *yanıt geçersiz kılma* bölümüne genişleterek değişiklik yapılabilir. 

## <a name="use-variables"></a><a name="using-variables"></a>Değişken kullanma

Bir ara sunucu yapılandırmasının statik olması gerekmez. Özgün istemci isteğinden, arka uç yanıtından veya uygulama ayarlarından değişkenleri kullanmak için bu durumu belirtebilirsiniz.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Yerel işlevlere başvur
`localhost`Aynı işlev uygulaması içindeki bir işleve, gidiş dönüş proxy isteği olmadan doğrudan başvurmak için ' yi kullanabilirsiniz.

`"backendUri": "https://localhost/api/httptriggerC#1"` rotada yerel bir HTTP tetiklemeli işleve başvuracaktır `/api/httptriggerC#1`

 
>[!Note]  
>İşleviniz *işlev, yönetici veya sys* Yetkilendirme düzeylerini kullanıyorsa, özgün işlev URL 'sine göre kodu ve ClientID sağlamanız gerekir. Bu durumda, başvurunun şöyle görünmesi gerekir: `"backendUri": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` Bu anahtarların [uygulama ayarlarında] depolanmasını ve proxy 'leriniz ile başvurmayı öneririz. Bu, kaynak kodunuzda gizli dizileri depolamayı önler. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Başvuru isteği parametreleri

İstek parametrelerini, arka uç URL 'SI özelliğine giriş olarak veya isteklerin ve yanıtların değiştirilmesi kapsamında kullanabilirsiniz. Bazı parametreler, temel proxy yapılandırmasında belirtilen yol şablonundan bağlanabilir ve diğerleri gelen isteğin özelliklerinden gelebilir.

#### <a name="route-template-parameters"></a>Rota şablonu parametreleri
Yol şablonunda kullanılan parametrelere ad tarafından başvurulmak üzere erişilebilir. Parametre adları küme ayracı () içine alınır {} .

Örneğin, bir ara sunucu gibi bir rota şablonu varsa, `/pets/{petId}` arka uç URL 'si ' de ' nin değerini içerebilir `{petId}` `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` . Yol şablonu, gibi bir joker karakterle sonlandığında, bu `/api/{*restOfPath}` değer `{restOfPath}` Gelen istekten kalan yol segmentlerinin bir dize gösterimidir.

#### <a name="additional-request-parameters"></a>Ek istek parametreleri
Yol şablonu parametrelerine ek olarak, aşağıdaki değerler yapılandırma değerlerinde kullanılabilir:

* **{Request. Method}**: özgün ISTEKTE kullanılan http yöntemi.
* **{Request. Headers. \<HeaderName\> }**: özgün istekten okunabilecek bir üst bilgi. *\<HeaderName\>* Okumak istediğiniz üstbilginin adıyla değiştirin. Üst bilgi isteğe bağlı değilse, değer boş dize olur.
* **{Request. QueryString. \<ParameterName\> }**: özgün istekten okunabilecek bir sorgu dizesi parametresi. *\<ParameterName\>* Okumak istediğiniz parametrenin adıyla değiştirin. Parametre isteğe bağlı değilse, değer boş dize olur.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Başvuru arka ucu yanıt parametreleri

Yanıt parametreleri, istemciye olan yanıtı değiştirmenin bir parçası olarak kullanılabilir. Aşağıdaki değerler yapılandırma değerlerinde kullanılabilir:

* **{arka uç. Response. StatusCode}**: arka uç YANıTıNDA döndürülen http durum kodu.
* **{arka uç. Response. statusReason}**: arka uç YANıTıNDA döndürülen http neden tümceciği.
* **{arka uç. Response. Headers. \<HeaderName\> }**: arka uç yanıtından okunabilecek bir üst bilgi. *\<HeaderName\>* Okumak istediğiniz üstbilginin adıyla değiştirin. Üst bilgi yanıta dahil değilse, değer boş dize olur.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Başvuru uygulama ayarları

Ayrıca, ayar adını yüzde işaretleri (%) ile çevreleyerek [işlev uygulaması için tanımlanan uygulama ayarlarına](./functions-how-to-use-azure-function-app-settings.md) başvurabilirsiniz.

Örneğin, bir arka uç URL 'SI *https://%ORDER_PROCESSING_HOST%/api/orders* "% ORDER_PROCESSING_HOST%" değerini ORDER_PROCESSING_HOST ayarı değeriyle değiştirdi.

> [!TIP] 
> Birden çok dağıtıma veya test ortamına sahip olduğunuzda arka uç konakları için uygulama ayarlarını kullanın. Bu şekilde, bu ortam için her zaman doğru arka uca konuşduğunuzdan emin olabilirsiniz.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Proxy sorunlarını giderme

`"debug":true`Seçtiğiniz herhangi bir proxy 'ye bayrak ekleyerek `proxies.json` hata ayıklama günlüğünü etkinleştirecektir. Günlükler, `D:\home\LogFiles\Application\Proxies\DetailedTrace` Gelişmiş araçlar (kudu) üzerinden depolanır ve erişilebilir. Tüm HTTP yanıtları `Proxy-Trace-Location` , günlük dosyasına erişmek IÇIN URL içeren bir üst bilgi de içerecektir.

' A bir üst bilgi kümesi ekleyerek istemci tarafındaki bir proxy 'yi hata ayıklaması yapabilirsiniz `Proxy-Trace-Enabled` `true` . Bu işlem ayrıca dosya sistemine bir izleme de kaydeder ve yanıtta bir üst bilgi olarak izleme URL 'sini döndürür.

### <a name="block-proxy-traces"></a>Proxy izlemelerini engelle

Güvenlik nedenleriyle, hizmetinizi çağıran herkesin izleme oluşturmasına izin vermek istemeyebilirsiniz. Bu kişiler, oturum açma kimlik bilgileriniz olmadan izleme içeriğine erişemez, ancak izlemenin oluşturulması kaynakları tüketir ve Işlev proxy 'Leri kullandığınızı gösterir.

İçindeki belirli bir ara sunucuya ekleyerek izlemeleri tamamen devre dışı bırakın `"debug":false` `proxies.json` .

## <a name="advanced-configuration"></a>Gelişmiş yapılandırma

Yapılandırdığınız proxy 'ler, bir işlev uygulama dizininin kökünde bulunan dosyası *proxies.js* depolanır. Bu dosyayı el ile düzenleyebilir ve Işlevlerin desteklediği [dağıtım yöntemlerinden](./functions-continuous-deployment.md) birini kullandığınızda uygulamanızın bir parçası olarak dağıtabilirsiniz. 

> [!TIP] 
> Dağıtım yöntemlerinden birini görmüyorsanız, portaldaki *proxies.js* dosya ile de çalışabilirsiniz. İşlev uygulamanıza gidin, **platform özellikleri**' ni seçin ve ardından **App Service Düzenleyicisi**' yi seçin. Bunu yaptığınızda, işlev uygulamanızın tüm dosya yapısını görüntüleyebilir ve sonra değişiklikler yapabilirsiniz.

*Proxies.js* , adlandırılmış proxy 'ler ve bunların tanımlarından oluşan bir proxy nesnesi tarafından tanımlanır. İsteğe bağlı olarak, düzenleyiciniz destekliyorsa, kod tamamlama için bir [JSON şemasına](http://json.schemastore.org/proxies) başvurabilirsiniz. Örnek bir dosya aşağıdaki gibi görünebilir:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Her proxy 'nin, önceki örnekteki *Proxy1* gibi kolay bir adı vardır. Karşılık gelen proxy tanımı nesnesi aşağıdaki özellikler tarafından tanımlanır:

* **Matchcondition**: gerekli--bu proxy 'nin yürütülmesini tetikleyen istekleri tanımlayan nesne. [Http tetikleyicilerle]paylaşılan iki özellik içerir:
    * _Yöntemler_: proxy 'nin yanıt verdiği http yöntemlerinin bir dizisi. Belirtilmemişse, ara sunucu rotadaki tüm HTTP yöntemlerine yanıt verir.
    * _yol_: gerekli--proxy 'nizin hangi Istek URL 'lerine yanıt vereceğini denetleyen yol şablonunu tanımlar. HTTP tetikleyicilerinin aksine, varsayılan değer yoktur.
* **Backenduri**: isteğin proxy olması gereken arka uç kaynağının URL 'si. Bu değer, özgün istemci isteğinden uygulama ayarlarına ve parametrelere başvurabilir. Bu özellik dahil değilse, Azure Işlevleri bir HTTP 200 Tamam ile yanıt verir.
* **requestOverrides**: arka uç isteğine dönüştürmeleri tanımlayan bir nesne. Bkz. [requestOverrides nesnesi tanımlama].
* **Responsekılmalar**: istemci yanıtına dönüştürmeleri tanımlayan bir nesne. Bkz. [Responsegeçersiz kılmalar nesnesi tanımlama].

> [!NOTE] 
> Azure İşlev Proxy'leri *route* özelliği, işlev uygulaması ana bilgisayar yapılandırmasının *routeprefix* özelliğini karşılamıyor. Gibi bir ön ek eklemek istiyorsanız `/api` , *route* özelliğine eklenmelidir.

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> Ayrı proxy 'leri devre dışı bırak

Dosyadaki ara sunucuya ekleyerek ayrı proxy 'leri devre dışı bırakabilirsiniz `"disabled": true` `proxies.json` . Bu, matchCondition 'a uyan isteklerin 404 döndürmesini sağlar.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a> Uygulama ayarları

Proxy davranışı birkaç uygulama ayarı tarafından denetlenebilir. Bunlar, [Işlevler uygulama ayarları başvurusu](./functions-app-settings.md) 'nda özetlenmiştir

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> Ayrılmış karakterler (dize biçimlendirme)

Proxy 'ler, bir JSON dosyasının dışında tüm dizeleri okur ve bunu çıkış simgesi olarak kullanın. Proxy 'ler Ayrıca küme ayraçları da yorumlayabilir. Aşağıdaki örnek bir dizi örneğe bakın.

|Karakter|Kaçan karakter|Örnek|
|-|-|-|
|veya|{{veya}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>RequestOverrides nesnesi tanımlama

RequestOverrides nesnesi, arka uç kaynağı çağrıldığında istek üzerinde yapılan değişiklikleri tanımlar. Nesnesi aşağıdaki özellikler tarafından tanımlanır:

* **arka uç. Request. Method**: arka ucu çağırmak IÇIN kullanılan http yöntemi.
* **arka uç. Request. QueryString \<ParameterName\> .**: arka uca çağrı için ayarlanyleyebileceğiniz bir sorgu dizesi parametresi. *\<ParameterName\>* Ayarlamak istediğiniz parametrenin adıyla değiştirin. Boş bir dize sağlanırsa, parametrenin yine de arka uç isteğine dahil edildiğini unutmayın.
* **arka uç. Request. Headers \<HeaderName\> .**: arka uca çağrı için ayarlayabilme üst bilgi. *\<HeaderName\>* Ayarlamak istediğiniz üstbilginin adıyla değiştirin. Boş bir dize sağlanırsa, parametrenin yine de arka uç isteğine dahil edildiğini unutmayın.

Değerler, özgün istemci isteğinden uygulama ayarlarına ve parametrelere başvurabilir.

Örnek bir yapılandırma aşağıdaki gibi görünebilir:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Responsekılmalar nesnesi tanımlama

RequestOverrides nesnesi, istemciye geri geçirilmiş yanıtta yapılan değişiklikleri tanımlar. Nesnesi aşağıdaki özellikler tarafından tanımlanır:

* **Response. StatusCode**: ISTEMCIYE döndürülecek http durum kodu.
* **Response. statusReason**: ISTEMCIYE döndürülecek http neden tümceciği.
* **Response. Body**: istemciye döndürülecek gövdenin dize temsili.
* **Response. Headers. \<HeaderName\>**: istemciye yanıt olarak ayarlanabilir bir üst bilgi. *\<HeaderName\>* Ayarlamak istediğiniz üstbilginin adıyla değiştirin. Boş bir dize sağlarsanız, üst bilgi yanıta eklenmez.

Değerler uygulama ayarlarına, özgün istemci isteğinden parametrelere ve arka uç yanıtından parametrelere başvurabilir.

Örnek bir yapılandırma aşağıdaki gibi görünebilir:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> Bu örnekte, yanıt gövdesi doğrudan ayarlanır, bu nedenle hiçbir `backendUri` özellik gerekmez. Örnek, sahte işlem API 'leri için Azure işlev proxy'leri nasıl kullanabileceğinizi gösterir.

[Azure portalı]: https://portal.azure.com
[HTTP Tetikleyicileri]: ./functions-bindings-http-webhook.md
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[RequestOverrides nesnesi tanımlama]: #requestOverrides
[Responsekılmalar nesnesi tanımlama]: #responseOverrides
[Uygulama ayarları]: #use-appsettings
[Değişken kullanma]: #using-variables
[özgün istemci isteğinden parametreler]: #request-parameters
[arka uç yanıtından parametreler]: #response-parameters
