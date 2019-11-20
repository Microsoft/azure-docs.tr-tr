---
title: Azure Işlevleri için Microsoft Graph bağlamaları
description: Azure Işlevlerinde Microsoft Graph Tetikleyicileri ve bağlamaları nasıl kullanacağınızı anlayın.
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 1db929e3ec4b400113d04cb7dd1cd4b8d2e86a9a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176606"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Azure Işlevleri için Microsoft Graph bağlamaları

Bu makalede, Azure Işlevlerinde Microsoft Graph Tetikleyicileri ve bağlamaları yapılandırma ve bunlarla nasıl çalışılacağı açıklanmaktadır. Bunlarla, [Microsoft Graph](https://developer.microsoft.com/graph)veri, öngörü ve olaylarla çalışmak Için Azure işlevlerini kullanabilirsiniz.

Microsoft Graph uzantısı aşağıdaki bağlamaları sağlar:
- Bir [kimlik doğrulama belirteci giriş bağlaması](#token-input) , herhangi BIR Microsoft Graph API ile etkileşim kurmanıza olanak tanır.
- Excel [tablo girişi bağlama](#excel-input) , Excel 'den veri okumanızı sağlar.
- [Excel tablosu çıkış bağlaması](#excel-output) Excel verilerini değiştirmenize olanak sağlar.
- [OneDrive dosya girişi bağlama](#onedrive-input) , OneDrive 'daki dosyaları okumanızı sağlar.
- [OneDrive dosya çıkış bağlaması](#onedrive-output) OneDrive 'daki dosyalara yazmanızı sağlar.
- [Outlook ileti çıkış bağlaması](#outlook-output) Outlook aracılığıyla e-posta göndermenizi sağlar.
- [Microsoft Graph Web kancası Tetikleyicileri ve bağlamaları](#webhooks) koleksiyonu, Microsoft Graph olaylara tepki vermenize olanak tanır.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph bağlamaları Şu anda Azure Işlevleri sürüm 2. x için önizleme aşamasındadır. Bunlar, sürüm 1. x Işlevleri içinde desteklenmez.

## <a name="packages"></a>Paketlerle

Kimlik doğrulama belirteci giriş bağlaması, [Microsoft. Azure. WebJobs. Extensions. authtokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet paketinde sunulmaktadır. Diğer Microsoft Graph bağlamaları [Microsoft. Azure. WebJobs. Extensions. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) paketinde sunulmaktadır. Paketlere yönelik kaynak kodu, [Azure-Functions-microsoftgraph-Extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub deposunda bulunur.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Uzantıları ayarlama

_Bağlama uzantıları_aracılığıyla Microsoft Graph bağlamaları kullanılabilir. Bağlama uzantıları, Azure Işlevleri çalışma zamanına yönelik isteğe bağlı bileşenlerdir. Bu bölümde Microsoft Graph ve Auth belirteci uzantılarının nasıl ayarlanacağı gösterilmektedir.

### <a name="enabling-functions-20-preview"></a>Işlevleri etkinleştirme 2,0 Preview

Bağlama uzantıları yalnızca Azure Işlevleri 2,0 Önizleme için kullanılabilir. 

Işlevler çalışma zamanının Preview 2,0 sürümünü kullanmak üzere bir işlev uygulamasının nasıl ayarlanacağı hakkında bilgi için bkz. [Azure işlevleri çalışma zamanı sürümlerini hedefleme](set-runtime-version.md).

### <a name="installing-the-extension"></a>Uzantıyı yükleme

Bir uzantıyı Azure portal yüklemek için, ona başvuran bir şablona veya bağlamaya gidin. Yeni bir işlev oluşturun ve şablon seçim ekranında "Microsoft Graph" senaryosunu seçin. Bu senaryodaki şablonlardan birini seçin. Alternatif olarak, var olan bir işlevin "tümleştir" sekmesine gidebilir ve bu makalede ele alınan bağlamalardan birini seçebilirsiniz.

Her iki durumda da, Yüklenecek uzantıyı belirten bir uyarı görüntülenir. Uzantıyı almak için **yükler** ' e tıklayın. Her uzantının işlev uygulaması başına bir kez yüklenmesi gerekir. 

> [!Note] 
> Portal içi yükleme işlemi, bir tüketim planında en fazla 10 dakika sürebilir.

Visual Studio kullanıyorsanız, [Bu makalede daha önce listelenen NuGet paketlerini](#packages)yükleyerek uzantıları alabilirsiniz.

### <a name="configuring-authentication--authorization"></a>Kimlik doğrulama/Yetkilendirme yapılandırma

Bu makalede özetlenen bağlamalar, kullanılacak bir kimlik gerektirir. Bu, Microsoft Graph izinleri ve denetim etkileşimini zorlayasağlar. Kimlik, uygulamanıza veya uygulamaya erişen bir kullanıcı olabilir. Bu kimliği yapılandırmak için, Azure Active Directory [App Service kimlik doğrulaması/yetkilendirme](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) ayarlayın. Ayrıca, işlevlerinizin gerektirdiği tüm kaynak izinlerini istemeniz gerekir.

> [!Note] 
> Microsoft Graph uzantısı yalnızca Azure AD kimlik doğrulamasını destekler. Kullanıcıların bir iş veya okul hesabıyla oturum açması gerekir.

Azure portal kullanıyorsanız, uzantıyı yüklemek için istemde bir uyarı görürsünüz. Uyarı, App Service kimlik doğrulaması/yetkilendirme yapılandırmak ve şablon ya da bağlamanın gerektirdiği tüm izinleri istemek için istemde bulunur. **Azure AD 'yi Şimdi Yapılandır** ' a tıklayın veya **izinleri şimdi** uygun şekilde ekleyin.



<a name="token-input"></a>
## <a name="auth-token"></a>Kimlik doğrulama belirteci

Kimlik doğrulama belirteci girişi bağlama, belirli bir kaynak için bir Azure AD belirteci alır ve bu dizeyi bir dize olarak kodunuza sağlar. Kaynak, uygulamanın izinlere sahip olduğu herhangi bir olabilir. 

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#auth-token---example)
* [Özelliklerine](#auth-token---attributes)
* [Yapılandırmada](#auth-token---configuration)
* [Kullanımıyla](#auth-token---usage)

### <a name="auth-token---example"></a>Kimlik doğrulama belirteci-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Kimlik doğrulama belirteci C# -betik örneği

Aşağıdaki örnekte Kullanıcı profili bilgileri alınır.

*Function. JSON* dosyası bir belirteç girişi bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# Betik kodu, Microsoft Graph http çağrısı yapmak için belirteci kullanır ve sonucu döndürür:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Kimlik doğrulama belirteci-JavaScript örneği

Aşağıdaki örnekte Kullanıcı profili bilgileri alınır.

*Function. JSON* dosyası bir belirteç girişi bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript kodu, Microsoft Graph HTTP çağrısı yapmak için belirteci kullanır ve sonucu döndürür.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Kimlik doğrulama belirteci-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [belirteç](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) özniteliğini kullanın.

### <a name="auth-token---configuration"></a>Kimlik doğrulama belirteci-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `Token` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Gerekli-kimlik doğrulama belirtecinin işlev kodunda kullanılan değişken adı. Bkz. [Koddan bir kimlik doğrulama belirteci girişi bağlama kullanma](#token-input-code).|
|**türüyle**||Gerekli-`token` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`in` olarak ayarlanmalıdır.|
|**IDENTITY**|**Kimlik**|Gerekli-eylemi gerçekleştirmek için kullanılacak kimlik. Aşağıdaki değerlerden biri olabilir:<ul><li><code>userFromRequest</code>-yalnızca [HTTP tetikleyicisi]geçerlidir. Çağıran kullanıcının kimliğini kullanır.</li><li><code>userFromId</code>-belirtilen KIMLIĞE sahip, daha önce oturum açmış bir kullanıcının kimliğini kullanır. @No__t-0 özelliğine bakın.</li><li><code>userFromToken</code>-belirtilen belirteç tarafından temsil edilen kimliği kullanır. @No__t-0 özelliğine bakın.</li><li><code>clientCredentials</code>-işlev uygulamasının kimliğini kullanır.</li></ul>|
|**UserID**|**UserID**  |Yalnızca _kimlik_ `userFromId` olarak ayarlandıysa gereklidir. Daha önce oturum açmış kullanıcıyla ilişkili bir Kullanıcı asıl KIMLIĞI.|
|**userToken**|**UserToken**|Yalnızca _kimlik_ `userFromToken` olarak ayarlandıysa gereklidir. İşlev uygulaması için geçerli bir belirteç. |
|**Kaynak**|**Kaynak**|Gerekli-belirtecin istendiği bir Azure AD kaynak URL 'SI.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Kimlik doğrulama belirteci-kullanım

Bağlamanın kendisi Azure AD izinleri gerektirmez, ancak belirtecin nasıl kullanıldığına bağlı olarak ek izinler istemeniz gerekebilir. Belirteç ile erişmeyi düşündüğünüz kaynağın gereksinimlerini denetleyin.

Belirteç her zaman bir dize olarak kod olarak sunulur.

> [!Note]
> @No__t-0, `userFromToken` veya `userFromRequest` seçenekleriyle yerel olarak geliştirirken, gereken belirteç [el ile elde](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) edilebilir ve bir çağıran istemci uygulamasından `X-MS-TOKEN-AAD-ID-TOKEN` istek üstbilgisinde belirtilebilir.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel girişi

Excel tablo girişi bağlama, OneDrive 'da depolanan bir Excel tablosunun içeriğini okur.

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#excel-input---example)
* [Özelliklerine](#excel-input---attributes)
* [Yapılandırmada](#excel-input---configuration)
* [Kullanımıyla](#excel-input---usage)

### <a name="excel-input---example"></a>Excel girişi-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel girişi- C# betik örneği

Aşağıdaki *function. JSON* dosyası bir Excel giriş bağlaması ile http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aşağıdaki C# betik kodu, belirtilen tablonun içeriğini okur ve bunları kullanıcıya döndürür:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel girişi-JavaScript örneği

Aşağıdaki *function. JSON* dosyası bir Excel giriş bağlaması ile http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aşağıdaki JavaScript kodu belirtilen tablonun içeriğini okur ve bunları kullanıcıya döndürür.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel girişi-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) özniteliğini kullanın.

### <a name="excel-input---configuration"></a>Excel girişi-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `Excel` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Required-Excel tablosu için işlev kodunda kullanılan değişken adı. Bkz. [Koddan Excel tablo girişi bağlama kullanma](#excel-input-code).|
|**türüyle**||Gerekli-`excel` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`in` olarak ayarlanmalıdır.|
|**IDENTITY**|**Kimlik**|Gerekli-eylemi gerçekleştirmek için kullanılacak kimlik. Aşağıdaki değerlerden biri olabilir:<ul><li><code>userFromRequest</code>-yalnızca [HTTP tetikleyicisi]geçerlidir. Çağıran kullanıcının kimliğini kullanır.</li><li><code>userFromId</code>-belirtilen KIMLIĞE sahip, daha önce oturum açmış bir kullanıcının kimliğini kullanır. @No__t-0 özelliğine bakın.</li><li><code>userFromToken</code>-belirtilen belirteç tarafından temsil edilen kimliği kullanır. @No__t-0 özelliğine bakın.</li><li><code>clientCredentials</code>-işlev uygulamasının kimliğini kullanır.</li></ul>|
|**UserID**|**UserID**  |Yalnızca _kimlik_ `userFromId` olarak ayarlandıysa gereklidir. Daha önce oturum açmış kullanıcıyla ilişkili bir Kullanıcı asıl KIMLIĞI.|
|**userToken**|**UserToken**|Yalnızca _kimlik_ `userFromToken` olarak ayarlandıysa gereklidir. İşlev uygulaması için geçerli bir belirteç. |
|**Yolun**|**Yolun**|Gerekli-OneDrive 'daki Excel çalışma kitabı yolu.|
|**Çalışma sayfa sayfası**|**Çalışma sayfa sayfası**|Tablonun bulunduğu çalışma sayfası.|
|**tableName**|**TableName**|Tablonun adı. Belirtilmemişse, çalışma sayfasının içeriği kullanılacaktır.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel giriş kullanımı

Bu bağlama için aşağıdaki Azure AD izinleri gereklidir:

|Kaynak|İzin|
|--------|--------|
|Microsoft Graph|Kullanıcı dosyalarını oku|

Bağlama, .NET işlevleri için aşağıdaki türleri kullanıma sunar:
- String [] []
- Microsoft. Graph. WorkbookTable
- Özel nesne türleri (yapısal model bağlamayı kullanarak)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel çıktısı

Excel çıkış bağlaması, OneDrive 'da depolanan bir Excel tablosunun içeriğini değiştirir.

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#excel-output---example)
* [Özelliklerine](#excel-output---attributes)
* [Yapılandırmada](#excel-output---configuration)
* [Kullanımıyla](#excel-output---usage)

### <a name="excel-output---example"></a>Excel çıkışı-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel çıkışı- C# betik örneği

Aşağıdaki örnek, bir Excel tablosuna satır ekler.

*Function. JSON* dosyası bir Excel çıkış bağlaması ile http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# Komut dosyası kodu, tabloya yeni bir satır ekler (tek sütun olarak kabul edilir) ve sorgu dizesinden gelen giriş temel alır:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Excel çıktısı-JavaScript örneği

Aşağıdaki örnek, bir Excel tablosuna satır ekler.

*Function. JSON* dosyası bir Excel çıkış bağlaması ile http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aşağıdaki JavaScript kodu, tabloya yeni bir satır ekler (tek sütun olarak kabul edilir) ve sorgu dizesinden girişe göre

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel çıkışı-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) özniteliğini kullanın.

### <a name="excel-output---configuration"></a>Excel çıkışı-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `Excel` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Gerekli-kimlik doğrulama belirtecinin işlev kodunda kullanılan değişken adı. Bkz. [Koddan Excel tablo çıkış bağlamayı kullanma](#excel-output-code).|
|**türüyle**||Gerekli-`excel` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`out` olarak ayarlanmalıdır.|
|**IDENTITY**|**Kimlik**|Gerekli-eylemi gerçekleştirmek için kullanılacak kimlik. Aşağıdaki değerlerden biri olabilir:<ul><li><code>userFromRequest</code>-yalnızca [HTTP tetikleyicisi]geçerlidir. Çağıran kullanıcının kimliğini kullanır.</li><li><code>userFromId</code>-belirtilen KIMLIĞE sahip, daha önce oturum açmış bir kullanıcının kimliğini kullanır. @No__t-0 özelliğine bakın.</li><li><code>userFromToken</code>-belirtilen belirteç tarafından temsil edilen kimliği kullanır. @No__t-0 özelliğine bakın.</li><li><code>clientCredentials</code>-işlev uygulamasının kimliğini kullanır.</li></ul>|
|**UserID** |**UserID** |Yalnızca _kimlik_ `userFromId` olarak ayarlandıysa gereklidir. Daha önce oturum açmış kullanıcıyla ilişkili bir Kullanıcı asıl KIMLIĞI.|
|**userToken**|**UserToken**|Yalnızca _kimlik_ `userFromToken` olarak ayarlandıysa gereklidir. İşlev uygulaması için geçerli bir belirteç. |
|**Yolun**|**Yolun**|Gerekli-OneDrive 'daki Excel çalışma kitabı yolu.|
|**Çalışma sayfa sayfası**|**Çalışma sayfa sayfası**|Tablonun bulunduğu çalışma sayfası.|
|**tableName**|**TableName**|Tablonun adı. Belirtilmemişse, çalışma sayfasının içeriği kullanılacaktır.|
|**Güncelleştirme türü**|**Güncelleştirme türü**|Gerekli-tabloda yapılacak değişikliğin türü. Aşağıdaki değerlerden biri olabilir:<ul><li><code>update</code>-OneDrive 'daki tablonun içeriğini değiştirir.</li><li><code>append</code>-yeni satırlar oluşturarak yükü OneDrive 'daki tablonun sonuna ekler.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel çıkış kullanımı

Bu bağlama için aşağıdaki Azure AD izinleri gereklidir:

|Kaynak|İzin|
|--------|--------|
|Microsoft Graph|Kullanıcı dosyalarına tam erişim sahibi|

Bağlama, .NET işlevleri için aşağıdaki türleri kullanıma sunar:
- String [] []
- Newtonsoft. JSON. LINQ. JObject
- Microsoft. Graph. WorkbookTable
- Özel nesne türleri (yapısal model bağlamayı kullanarak)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Dosya girişi

OneDrive dosya girişi bağlama, OneDrive 'da depolanan bir dosyanın içeriğini okur.

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#file-input---example)
* [Özelliklerine](#file-input---attributes)
* [Yapılandırmada](#file-input---configuration)
* [Kullanımıyla](#file-input---usage)

### <a name="file-input---example"></a>Dosya girişi-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Dosya girişi- C# betik örneği

Aşağıdaki örnek, OneDrive 'da depolanan bir dosyayı okur.

*Function. JSON* dosyası, OneDrive dosya girişi bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# Betik kodu, sorgu dizesinde belirtilen dosyayı okur ve uzunluğunu günlüğe kaydeder:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Dosya girişi-JavaScript örneği

Aşağıdaki örnek, OneDrive 'da depolanan bir dosyayı okur.

*Function. JSON* dosyası, OneDrive dosya girişi bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aşağıdaki JavaScript kodu, sorgu dizesinde belirtilen dosyayı okur ve uzunluğunu döndürür.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Dosya girişi-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) özniteliğini kullanın.

### <a name="file-input---configuration"></a>Dosya girişi-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `OneDrive` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Gerekli-dosya için işlev kodunda kullanılan değişken adı. Bkz. [Koddan OneDrive dosya girişi bağlama kullanma](#onedrive-input-code).|
|**türüyle**||Gerekli-`onedrive` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`in` olarak ayarlanmalıdır.|
|**IDENTITY**|**Kimlik**|Gerekli-eylemi gerçekleştirmek için kullanılacak kimlik. Aşağıdaki değerlerden biri olabilir:<ul><li><code>userFromRequest</code>-yalnızca [HTTP tetikleyicisi]geçerlidir. Çağıran kullanıcının kimliğini kullanır.</li><li><code>userFromId</code>-belirtilen KIMLIĞE sahip, daha önce oturum açmış bir kullanıcının kimliğini kullanır. @No__t-0 özelliğine bakın.</li><li><code>userFromToken</code>-belirtilen belirteç tarafından temsil edilen kimliği kullanır. @No__t-0 özelliğine bakın.</li><li><code>clientCredentials</code>-işlev uygulamasının kimliğini kullanır.</li></ul>|
|**UserID**|**UserID**  |Yalnızca _kimlik_ `userFromId` olarak ayarlandıysa gereklidir. Daha önce oturum açmış kullanıcıyla ilişkili bir Kullanıcı asıl KIMLIĞI.|
|**userToken**|**UserToken**|Yalnızca _kimlik_ `userFromToken` olarak ayarlandıysa gereklidir. İşlev uygulaması için geçerli bir belirteç. |
|**Yolun**|**Yolun**|Gerekli-dosyada OneDrive 'daki yol.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Dosya girişi-kullanım

Bu bağlama için aşağıdaki Azure AD izinleri gereklidir:

|Kaynak|İzin|
|--------|--------|
|Microsoft Graph|Kullanıcı dosyalarını oku|

Bağlama, .NET işlevleri için aşağıdaki türleri kullanıma sunar:
- Byte []
- Ka
- dize
- Microsoft. Graph. Driveöğesi






<a name="onedrive-output"></a>
## <a name="file-output"></a>Dosya çıktısı

OneDrive dosyası çıkış bağlaması, OneDrive 'da depolanan bir dosyanın içeriğini değiştirir.

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#file-output---example)
* [Özelliklerine](#file-output---attributes)
* [Yapılandırmada](#file-output---configuration)
* [Kullanımıyla](#file-output---usage)

### <a name="file-output---example"></a>Dosya çıkışı-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Dosya çıkışı- C# betik örneği

Aşağıdaki örnek, OneDrive 'da depolanan bir dosyaya yazar.

*Function. JSON* dosyası bir OneDrive çıkış bağlaması ile http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# Betik kodu, sorgu dizesinden metin alır ve çağıranın OneDrive kökündeki bir metin dosyasına (önceki örnekte tanımlandığı şekilde functionstest. txt) Yazar:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Dosya çıktısı-JavaScript örneği

Aşağıdaki örnek, OneDrive 'da depolanan bir dosyaya yazar.

*Function. JSON* dosyası bir OneDrive çıkış bağlaması ile http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript kodu, sorgu dizesinden metin alır ve çağıranın OneDrive kökündeki bir metin dosyasına (yukarıdaki yapılandırmaya göre tanımlanan FunctionsTest. txt) yazar.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Dosya çıkışı-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) özniteliğini kullanın.

### <a name="file-output---configuration"></a>Dosya çıkışı-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `OneDrive` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Gerekli-dosya için işlev kodunda kullanılan değişken adı. Bkz. [Koddan OneDrive dosya çıkış bağlamayı kullanma](#onedrive-output-code).|
|**türüyle**||Gerekli-`onedrive` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`out` olarak ayarlanmalıdır.|
|**IDENTITY**|**Kimlik**|Gerekli-eylemi gerçekleştirmek için kullanılacak kimlik. Aşağıdaki değerlerden biri olabilir:<ul><li><code>userFromRequest</code>-yalnızca [HTTP tetikleyicisi]geçerlidir. Çağıran kullanıcının kimliğini kullanır.</li><li><code>userFromId</code>-belirtilen KIMLIĞE sahip, daha önce oturum açmış bir kullanıcının kimliğini kullanır. @No__t-0 özelliğine bakın.</li><li><code>userFromToken</code>-belirtilen belirteç tarafından temsil edilen kimliği kullanır. @No__t-0 özelliğine bakın.</li><li><code>clientCredentials</code>-işlev uygulamasının kimliğini kullanır.</li></ul>|
|**UserID** |**UserID** |Yalnızca _kimlik_ `userFromId` olarak ayarlandıysa gereklidir. Daha önce oturum açmış kullanıcıyla ilişkili bir Kullanıcı asıl KIMLIĞI.|
|**userToken**|**UserToken**|Yalnızca _kimlik_ `userFromToken` olarak ayarlandıysa gereklidir. İşlev uygulaması için geçerli bir belirteç. |
|**Yolun**|**Yolun**|Gerekli-dosyada OneDrive 'daki yol.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Dosya çıkışı-kullanım

Bu bağlama için aşağıdaki Azure AD izinleri gereklidir:

|Kaynak|İzin|
|--------|--------|
|Microsoft Graph|Kullanıcı dosyalarına tam erişim sahibi|

Bağlama, .NET işlevleri için aşağıdaki türleri kullanıma sunar:
- Byte []
- Ka
- dize
- Microsoft. Graph. Driveöğesi





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook çıktısı

Outlook ileti çıkış bağlaması Outlook aracılığıyla bir posta iletisi gönderir.

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#outlook-output---example)
* [Özelliklerine](#outlook-output---attributes)
* [Yapılandırmada](#outlook-output---configuration)
* [Kullanımıyla](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook çıkışı-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook çıkışı- C# betik örneği

Aşağıdaki örnek Outlook aracılığıyla bir e-posta gönderir.

*Function. JSON* dosyası bir Outlook ileti çıkış bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

C# Betik kodu, çağırandan sorgu dizesinde belirtilen bir alıcıya bir e-posta gönderir:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Outlook çıktısı-JavaScript örneği

Aşağıdaki örnek Outlook aracılığıyla bir e-posta gönderir.

*Function. JSON* dosyası bir Outlook ileti çıkış bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

JavaScript kodu, çağırandan sorgu dizesinde belirtilen bir alıcıya bir e-posta gönderir:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Outlook çıktısı-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) özniteliğini kullanın.

### <a name="outlook-output---configuration"></a>Outlook çıkışı-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `Outlook` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Gerekli-posta iletisi için işlev kodunda kullanılan değişken adı. Bkz. [Koddan Outlook ileti çıkış bağlaması kullanma](#outlook-output-code).|
|**türüyle**||Gerekli-`outlook` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`out` olarak ayarlanmalıdır.|
|**IDENTITY**|**Kimlik**|Gerekli-eylemi gerçekleştirmek için kullanılacak kimlik. Aşağıdaki değerlerden biri olabilir:<ul><li><code>userFromRequest</code>-yalnızca [HTTP tetikleyicisi]geçerlidir. Çağıran kullanıcının kimliğini kullanır.</li><li><code>userFromId</code>-belirtilen KIMLIĞE sahip, daha önce oturum açmış bir kullanıcının kimliğini kullanır. @No__t-0 özelliğine bakın.</li><li><code>userFromToken</code>-belirtilen belirteç tarafından temsil edilen kimliği kullanır. @No__t-0 özelliğine bakın.</li><li><code>clientCredentials</code>-işlev uygulamasının kimliğini kullanır.</li></ul>|
|**UserID**|**UserID**  |Yalnızca _kimlik_ `userFromId` olarak ayarlandıysa gereklidir. Daha önce oturum açmış kullanıcıyla ilişkili bir Kullanıcı asıl KIMLIĞI.|
|**userToken**|**UserToken**|Yalnızca _kimlik_ `userFromToken` olarak ayarlandıysa gereklidir. İşlev uygulaması için geçerli bir belirteç. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook çıkış kullanımı

Bu bağlama için aşağıdaki Azure AD izinleri gereklidir:

|Kaynak|İzin|
|--------|--------|
|Microsoft Graph|Kullanıcı olarak posta gönder|

Bağlama, .NET işlevleri için aşağıdaki türleri kullanıma sunar:
- Microsoft. Graph. Message
- Newtonsoft. JSON. LINQ. JObject
- dize
- Özel nesne türleri (yapısal model bağlamayı kullanarak)






## <a name="webhooks"></a>Web kancaları

Web kancaları Microsoft Graph olaylara tepki verir. Web kancalarını desteklemek için, _Web kancası aboneliklerini_oluşturmak, yenilemek ve bunlara yanıt vermek için işlevleri gereklidir. Tüm Web kancası çözümü aşağıdaki bağlamaların birleşimini gerektirir:
- [Microsoft Graph Web kancası tetikleyicisi](#webhook-trigger) , gelen bir Web kancasına tepki vermenize olanak tanır.
- [Microsoft Graph Web kancası abonelik girişi bağlama](#webhook-input) , mevcut abonelikleri listeetmenize ve isteğe bağlı olarak bunları yenilemenize olanak tanır.
- [Microsoft Graph Web kancası aboneliği çıkış bağlaması](#webhook-output) , Web kancası abonelikleri oluşturmanıza veya silmenizi sağlar.

Bağlamaların kendileri Azure AD izinleri gerektirmez, ancak tepki vermek istediğiniz kaynak türüyle ilgili izinler istemeniz gerekir. Her kaynak türü için gereken izinlerin bir listesi için, bkz. [abonelik izinleri](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Web kancaları hakkında daha fazla bilgi için bkz. [Microsoft Graph 'de Web kancaları ile çalışma].





## <a name="webhook-trigger"></a>Web kancası tetikleyicisi

Microsoft Graph Web kancası tetikleyicisi, bir işlevin Microsoft Graph gelen bir Web kancasına tepki vermesini sağlar. Bu tetikleyicinin her örneği bir Microsoft Graph kaynak türüne tepki verebilir.

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#webhook-trigger---example)
* [Özelliklerine](#webhook-trigger---attributes)
* [Yapılandırmada](#webhook-trigger---configuration)
* [Kullanımıyla](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Web kancası tetikleyicisi-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Web kancası tetikleyicisi C# -betik örneği

Aşağıdaki örnek, gelen Outlook iletileri için Web kancalarını işler. Bir Web kancası tetikleyicisi kullanmak için [bir abonelik oluşturun](#webhook-output---example)ve aboneliğin süresinin dolmasını engellemek için [aboneliğinizi yenileyebilirsiniz](#webhook-subscription-refresh) .

*Function. JSON* dosyası bir Web kancası tetikleyicisi tanımlıyor:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Betik C# kodu, gelen posta iletilerine yeniden davranır ve alıcı tarafından gönderilen ve "Azure işlevleri" ni içeren gövde gövdesini günlüğe kaydeder:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Web kancası tetikleyicisi-JavaScript örneği

Aşağıdaki örnek, gelen Outlook iletileri için Web kancalarını işler. Bir Web kancası tetikleyicisi kullanmak için [bir abonelik oluşturun](#webhook-output---example)ve aboneliğin süresinin dolmasını engellemek için [aboneliğinizi yenileyebilirsiniz](#webhook-subscription-refresh) .

*Function. JSON* dosyası bir Web kancası tetikleyicisi tanımlıyor:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

JavaScript kodu, gelen posta iletilerine tepki verir ve alıcı tarafından gönderilen ve "Azure Işlevleri" ni içeren gövde gövdesini günlüğe kaydeder:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Web kancası tetikleyicisi-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [graphwebkancatrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) özniteliğini kullanın.

### <a name="webhook-trigger---configuration"></a>Web kancası tetikleyicisi-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `GraphWebhookTrigger` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Gerekli-posta iletisi için işlev kodunda kullanılan değişken adı. Bkz. [Koddan Outlook ileti çıkış bağlaması kullanma](#outlook-output-code).|
|**türüyle**||Gerekli-`graphWebhook` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`trigger` olarak ayarlanmalıdır.|
|**Kaynak**|**Kaynak**|Gerekli-bu işlevin Web kancalarına yanıt vermesi gereken grafik kaynağı. Aşağıdaki değerlerden biri olabilir:<ul><li><code>#Microsoft.Graph.Message</code>-Outlook iletilerinde yapılan değişiklikler.</li><li><code>#Microsoft.Graph.DriveItem</code>-OneDrive kök öğelerinde yapılan değişiklikler.</li><li><code>#Microsoft.Graph.Contact</code>-Outlook 'ta kişisel kişilerde yapılan değişiklikler.</li><li><code>#Microsoft.Graph.Event</code>-Outlook Takvim öğelerinde yapılan değişiklikler.</li></ul>|

> [!Note]
> Bir işlev uygulaması yalnızca belirli bir `resourceType` değerine göre kaydedilmiş bir işleve sahip olabilir.

### <a name="webhook-trigger---usage"></a>Web kancası tetikleyicisi-kullanım

Bağlama, .NET işlevleri için aşağıdaki türleri kullanıma sunar:
- @No__t-0 veya `Microsoft.Graph.DriveItem` gibi kaynak türüyle ilgili SDK türleri Microsoft Graph.
- Özel nesne türleri (yapısal model bağlamayı kullanarak)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Web kancası girişi

Microsoft Graph Web kancası giriş bağlaması, bu işlev uygulaması tarafından yönetilen Aboneliklerin listesini almanıza olanak sağlar. Bağlama, işlev uygulaması depolamadan okurken uygulamanın dışından oluşturulan diğer abonelikleri yansıtmaz.

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#webhook-input---example)
* [Özelliklerine](#webhook-input---attributes)
* [Yapılandırmada](#webhook-input---configuration)
* [Kullanımıyla](#webhook-input---usage)

### <a name="webhook-input---example"></a>Web kancası girişi-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Web kancası girişi C# -betik örneği

Aşağıdaki örnek, çağıran kullanıcı için tüm abonelikleri alır ve siler.

*Function. JSON* dosyası bir abonelik girişi bağlaması ve silme eylemini kullanan bir abonelik çıkış bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# Betik kodu, abonelikleri alır ve siler:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Web kancası girişi-JavaScript örneği

Aşağıdaki örnek, çağıran kullanıcı için tüm abonelikleri alır ve siler.

*Function. JSON* dosyası bir abonelik girişi bağlaması ve silme eylemini kullanan bir abonelik çıkış bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript kodu, abonelikleri alır ve siler:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Web kancası girişi-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [graphwebkancalı abonelik](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) özniteliğini kullanın.

### <a name="webhook-input---configuration"></a>Web kancası girişi-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `GraphWebhookSubscription` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Gerekli-posta iletisi için işlev kodunda kullanılan değişken adı. Bkz. [Koddan Outlook ileti çıkış bağlaması kullanma](#outlook-output-code).|
|**türüyle**||Gerekli-`graphWebhookSubscription` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`in` olarak ayarlanmalıdır.|
|**filtreyle**|**Filtreyle**| @No__t-0 olarak ayarlanırsa bağlama yalnızca çağıran kullanıcı tarafından sahip olunan abonelikleri alır (yalnızca [HTTP tetikleyicisi]geçerlidir).| 

### <a name="webhook-input---usage"></a>Web kancası girişi kullanımı

Bağlama, .NET işlevleri için aşağıdaki türleri kullanıma sunar:
- String []
- Özel nesne türü dizileri
- Newtonsoft. JSON. LINQ. JObject []
- Microsoft. Graph. Subscription []





## <a name="webhook-output"></a>Web kancası çıkışı

Web kancası aboneliği çıkış bağlaması, Microsoft Graph Web kancası abonelikleri oluşturmanıza, silmesine ve yenilemesine olanak tanır.

Bu bölüm aşağıdaki alt bölümleri içerir:

* [Örneğinde](#webhook-output---example)
* [Özelliklerine](#webhook-output---attributes)
* [Yapılandırmada](#webhook-output---configuration)
* [Kullanımıyla](#webhook-output---usage)

### <a name="webhook-output---example"></a>Web kancası çıkışı-örnek

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Web kancası çıkışı C# -betik örneği

Aşağıdaki örnek bir abonelik oluşturur. [Aboneliği,](#webhook-subscription-refresh) süresinin dolmasını engellemek için yenileyebilirsiniz.

*Function. JSON* dosyası, oluşturma eylemini kullanarak bir abonelik çıkış bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# Komut dosyası kodu, çağıran kullanıcı bir Outlook iletisi aldığında, bu işlev uygulamasını bildiren bir Web kancası kaydeder:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Web kancası çıkışı-JavaScript örneği

Aşağıdaki örnek bir abonelik oluşturur. [Aboneliği,](#webhook-subscription-refresh) süresinin dolmasını engellemek için yenileyebilirsiniz.

*Function. JSON* dosyası, oluşturma eylemini kullanarak bir abonelik çıkış bağlaması Ile bir http tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript kodu, çağıran kullanıcı bir Outlook iletisi aldığında, bu işlev uygulamasını bildiren bir Web kancası kaydeder:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Web kancası çıkışı-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [graphwebkancalı abonelik](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) özniteliğini kullanın.

### <a name="webhook-output---configuration"></a>Web kancası çıkışı-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `GraphWebhookSubscription` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**ada**||Gerekli-posta iletisi için işlev kodunda kullanılan değişken adı. Bkz. [Koddan Outlook ileti çıkış bağlaması kullanma](#outlook-output-code).|
|**türüyle**||Gerekli-`graphWebhookSubscription` olarak ayarlanmalıdır.|
|**Görünüm**||Gerekli-`out` olarak ayarlanmalıdır.|
|**IDENTITY**|**Kimlik**|Gerekli-eylemi gerçekleştirmek için kullanılacak kimlik. Aşağıdaki değerlerden biri olabilir:<ul><li><code>userFromRequest</code>-yalnızca [HTTP tetikleyicisi]geçerlidir. Çağıran kullanıcının kimliğini kullanır.</li><li><code>userFromId</code>-belirtilen KIMLIĞE sahip, daha önce oturum açmış bir kullanıcının kimliğini kullanır. @No__t-0 özelliğine bakın.</li><li><code>userFromToken</code>-belirtilen belirteç tarafından temsil edilen kimliği kullanır. @No__t-0 özelliğine bakın.</li><li><code>clientCredentials</code>-işlev uygulamasının kimliğini kullanır.</li></ul>|
|**UserID**|**UserID**  |Yalnızca _kimlik_ `userFromId` olarak ayarlandıysa gereklidir. Daha önce oturum açmış kullanıcıyla ilişkili bir Kullanıcı asıl KIMLIĞI.|
|**userToken**|**UserToken**|Yalnızca _kimlik_ `userFromToken` olarak ayarlandıysa gereklidir. İşlev uygulaması için geçerli bir belirteç. |
|**ön**|**Eylem**|Gerekli-bağlamanın gerçekleştirmesi gereken eylemi belirtir. Aşağıdaki değerlerden biri olabilir:<ul><li><code>create</code>-yeni bir abonelik kaydeder.</li><li><code>delete</code>-belirtilen aboneliği siler.</li><li><code>refresh</code>-belirtilen aboneliği, süresinin dolmasını önlemek için yeniler.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Yalnızca _eylem_ `create` olarak ayarlandıysa gereklidir. Değişiklikler için izlenecek Microsoft Graph kaynağını belirtir. Bkz. [Microsoft Graph 'de Web kancaları ile çalışma]. |
|**changeType**|**ChangeType**|Yalnızca _eylem_ `create` olarak ayarlandıysa gereklidir. Abone olunan kaynaktaki bir bildirimi oluşturacak değişikliğin türünü gösterir. Desteklenen değerler şunlardır: `created`, `updated`, `deleted`. Birden çok değer, virgülle ayrılmış bir liste kullanılarak birleştirilebilir.|

### <a name="webhook-output---usage"></a>Web kancası çıkışı-kullanım

Bağlama, .NET işlevleri için aşağıdaki türleri kullanıma sunar:
- dize
- Microsoft. Graph. Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Web kancası aboneliği yenileme

Abonelikleri yenilemek için iki yaklaşım vardır:

- Tüm aboneliklerle uğraşmak için uygulama kimliğini kullanın. Bu, Azure Active Directory yöneticisinden onay gerektirir. Azure Işlevleri tarafından desteklenen tüm diller bu şekilde kullanılabilir.
- Her kullanıcı KIMLIĞINI el ile bağlayarak her abonelikle ilişkili kimliği kullanın. Bu, bağlamayı gerçekleştirmek için bazı özel kod gerektirir. Bu, yalnızca .NET işlevleri tarafından kullanılabilir.

Bu bölümde, bu yaklaşımların her biri için bir örnek yer almaktadır:

* [Uygulama kimliği örneği](#webhook-subscription-refresh---app-identity-example)
* [Kullanıcı kimliği örneği](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Web kancası aboneliği yenileme-uygulama kimliği örneği

Dile özgü örneğe bakın:

* [C#betik (. CSX)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Uygulama kimliği yenileme- C# betik örneği

Aşağıdaki örnek, bir aboneliği yenilemek için uygulama kimliğini kullanır.

*Function. JSON* , bir abonelik girişi bağlaması ve bir abonelik çıkış bağlaması ile bir Zamanlayıcı tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

C# Betik kodu abonelikleri yeniler:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Uygulama kimliği yenileme- C# betik örneği

Aşağıdaki örnek, bir aboneliği yenilemek için uygulama kimliğini kullanır.

*Function. JSON* , bir abonelik girişi bağlaması ve bir abonelik çıkış bağlaması ile bir Zamanlayıcı tetikleyicisi tanımlar:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

JavaScript kodu abonelikleri yeniler:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Web kancası aboneliği yenileme-Kullanıcı kimliği örneği

Aşağıdaki örnek, bir aboneliği yenilemek için Kullanıcı kimliğini kullanır.

*Function. JSON* dosyası bir Zamanlayıcı tetikleyicisi tanımlar ve işlev koduna abonelik girişi bağlamasını erteler:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# Betik kodu abonelikleri yeniler ve her kullanıcının kimliğini kullanarak kodda çıkış bağlamayı oluşturur:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

[HTTP tetikleyicisi]: functions-bindings-http-webhook.md
[Microsoft Graph 'de Web kancaları ile çalışma]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
