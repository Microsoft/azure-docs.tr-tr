---
title: 'Öğretici: Logic Apps e-posta gönderin'
description: App Service uygulamanızdan iş süreçlerini çağırmayı öğrenin. E-posta, ara sıra ve Facebook gönderileri gönderin, posta listelerine ekleyin ve çok daha fazlasını yapın.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 6b15c09162146de7aef8abfcdba40627eb77621c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830943"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Öğretici: App Service e-posta gönderin ve diğer iş süreçlerini çağırın

Bu öğreticide, App Service uygulamanızı iş süreçlerinizle tümleştirmeyi öğreneceksiniz. Bu, aşağıdaki gibi Web uygulaması senaryolarında ortaktır:

- Bir işlem için onay e-postası gönder
- Facebook grubuna kullanıcı ekleme
- SAP, Salesforce vb. gibi üçüncü taraf sistemlere bağlanın.
- Exchange standart B2B iletileri

Bu öğreticide, [Azure Logic Apps](../logic-apps/logic-apps-overview.md)kullanarak App Service uygulamanızdan Gmail ile e-posta gönderirsiniz. Dil çatısı tarafından sunulan SMTP yapılandırması gibi bir Web uygulamasından e-posta göndermenin başka yolları da vardır. Ancak Logic Apps, kodunuza karmaşıklık eklemeden App Service uygulamanıza çok daha fazla güç sunar. Logic Apps en popüler iş tümleştirmeleri için basit bir yapılandırma arabirimi sağlar ve uygulamanız bunları bir HTTP isteğiyle dilediğiniz zaman çağırabilir.

## <a name="prerequisite"></a>Önkoşul

App Service için tercih ettiğiniz dil çerçevesiyle bir uygulama dağıtın. Örnek bir uygulama dağıtmaya yönelik öğreticiyi izlemek için aşağıdakilere bakın:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Öğretici: Azure'da SQL Veritabanı ile ASP.NET uygulaması oluşturma](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Öğretici: Azure App Service ASP.NET Core ve SQL veritabanı uygulaması oluşturma](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Öğretici: Azure 'da Node.js ve MongoDB uygulaması oluşturma](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Öğretici: Azure 'da PHP ve MySQL uygulaması derleme](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Öğretici: Azure App Service içindeki PostgreSQL ile Python (Docgo) Web uygulaması çalıştırma](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Linux üzerinde Azure App Service Ruby ve Postgres uygulaması oluşturma](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Mantıksal uygulama oluşturma

1. [Azure Portal](https://portal.azure.com), [mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)bölümündeki yönergeleri izleyerek boş bir mantıksal uygulama oluşturun. **Logic Apps tasarımcısını** gördüğünüzde Bu öğreticiye geri dönün.
1. Logic Apps Tasarımcısı ' nın giriş sayfasında, **ortak bir tetikleyiciden başlayarak** bir **http isteği alındığında** ' ı seçin.

    ![Bir H T T isteği alındığında, ile Logic Apps tasarımcı için giriş sayfasını gösteren ekran görüntüsü.](./media/tutorial-send-email/receive-http-request.png)
1. **Http isteğinin alındığı zaman** iletişim kutusunda, **şema oluşturmak Için örnek yük kullan**' ı seçin.

    ![Bir g/ç isteği oluştur iletişim kutusu ve seçilen şema için örnek yükü Kullan seçili olan ekran görüntüsü. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Aşağıdaki örnek JSON öğesini metin kutusuna kopyalayın ve **bitti**' yi seçin.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Şema artık istediğiniz istek verileri için oluşturulmuştur. Uygulamada, uygulama kodunuzun oluşturduğu gerçek istek verilerini yakalayabilir ve Azure 'un JSON şemasını sizin için oluşturmasını sağlayabilirsiniz. 
1. Logic Apps tasarımcısının en üstünde **Kaydet**' i seçin. 

    Artık HTTP istek tetikleyicinizin URL 'sini görebilirsiniz. Daha sonra kullanılmak üzere kopyalamak için Kopyala simgesini seçin.

    ![H T T P istek tetikleyicinizin U R L 'sini kopyalamak için Kopyala simgesini vurgulayan ekran görüntüsü.](./media/tutorial-send-email/http-request-url.png)

    Bu HTTP istek tanımı, bu mantıksal uygulamada yapmak istediğiniz her şeye yönelik bir tetikleyiciden, Gmail veya başka bir şey olmasını sağlar. Daha sonra bu URL 'YI App Service uygulamanızda çağıracaksınız. İstek tetikleyicisi hakkında daha fazla bilgi için bkz. [http istek/yanıt başvurusu](../connectors/connectors-native-reqres.md).

1. Tasarımcının alt kısmındaki **yeni adım**' a tıklayın, eylemler arama kutusuna **Gmail** yazın ve **e-posta gönder (v2)** seçeneğini belirleyin.
    
    > [!TIP]
    > SendGrid, MailChimp, Microsoft 365 ve SalesForce gibi diğer tümleştirme türlerini arayabilirsiniz. Daha fazla bilgi için bkz. [Logic Apps belgeleri](../logic-apps/index.yml).

1. **Gmail** Iletişim kutusunda **oturum aç** ' ı seçin ve e-postayı göndermek istediğiniz Gmail hesabında oturum açın.

    ![E-posta göndermek istediğiniz Gmail hesabında oturum açmak için kullandığınız Gmail iletişim kutusunu gösteren ekran görüntüsü.](./media/tutorial-send-email/gmail-sign-in.png)

1. Oturum açıldıktan sonra, **to** metin kutusuna tıklayın ve dinamik içerik iletişim kutusu otomatik olarak açılır.

1. **BIR http isteği alındığında** eylem ' in yanında, **daha fazla göster**' i seçin.

    ![G/ç isteği alındığında eylem ' in yanında bulunan daha fazla göster düğmesini gösteren ekran görüntüsü.](./media/tutorial-send-email/expand-dynamic-content.png)

    Şimdi, daha önce kullandığınız örnek JSON verilerinden üç özelliği görmeniz gerekir. Bu adımda, HTTP isteğinden bu özellikleri kullanarak bir e-posta oluşturursunuz.
1. **To** alanı için değeri seçerken **e-posta**' yı seçin. İsterseniz, dinamik içerik **Ekle**' ye tıklayarak dinamik içerik iletişim kutusunu kapatın.

    ![E-posta seçeneğini gösteren ve dinamik çekişme Ekle seçeneği vurgulanmış ekran görüntüsü.](./media/tutorial-send-email/hide-dynamic-content.png)

1. **Yeni parametre Ekle** açılan menüsünde **Konu** ve **gövde**' yi seçin.

1. **Konu** metin kutusuna tıklayın ve aynı şekilde **görev**' i seçin. İmleç hala **Konu** kutusuna, *oluşturuldu* yazın. 

1. **Gövdeye** tıklayın ve aynı şekilde, **son**' u seçin. İmleci **süresi** sola taşı ve *Bu iş öğesinin son tarihini* yazın.

    > [!TIP]
    > HTML içeriğini doğrudan e-posta gövdesinde düzenlemek istiyorsanız, Logic Apps tasarımcı penceresinin en üstündeki **kod görünümü** ' nü seçin. Yalnızca dinamik içerik kodunu koruduğunuzdan emin olun (örneğin, `@{triggerBody()?['due']}` )
    >
    > ![E-posta gövdesinde H T M içeriğini doğrudan görüntülemek için kod görünümünü gösteren ekran görüntüsü.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Ardından, HTTP tetikleyicisine zaman uyumsuz bir HTTP yanıtı ekleyin. HTTP tetikleyicisi ve Gmail eylemi arasında, **+** işaretine tıklayın ve **paralel dal Ekle**' yi seçin.

    ![+ İşaretini gösteren ve bir paralel dal seçeneğinin vurgulandığı ekran görüntüsü.](./media/tutorial-send-email/add-http-response.png)

1. Arama kutusunda, **Yanıt**' ı arayın ve ardından **Yanıt** eylemini seçin.

    ![Arama çubuğunu ve yanıt eylemini vurguladığını gösteren ekran görüntüsü.](./media/tutorial-send-email/choose-response-action.png)

    Varsayılan olarak, yanıt eylemi bir HTTP 200 gönderir. Bu öğreticide yeterince iyidir. Daha fazla bilgi için bkz. [http istek/yanıt başvurusu](../connectors/connectors-native-reqres.md).

1. Logic Apps tasarımcısının üst kısmında yeniden **Kaydet** ' i seçin. 

## <a name="add-http-request-code-to-app"></a>Uygulamaya HTTP istek kodu ekle

HTTP istek tetikleyicisinin URL 'sini daha önce kopyaladığınızdan emin olun. Hassas bilgiler içerdiğinden, kodu doğrudan koda yerleştirmemenizi en iyi uygulamadır. App Service, uygulama ayarlarını kullanarak bunun yerine bir ortam değişkeni olarak başvurabilirsiniz. 

[Cloud Shell](https://shell.azure.com), uygulama ayarını aşağıdaki komutla oluşturun (değiştir *\<app-name>* , *\<resource-group-name>* ve *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

Kodunuzda, dil iskeyağınız için kullanılabilen herhangi bir HTTP istemci dilini kullanarak URL 'de standart bir HTTP gönderisini aşağıdaki yapılandırmayla yapın:

- İstek gövdesi, mantıksal uygulamanıza sağladığınız JSON biçimini içerir:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- İstek başlığı içerir `Content-Type: application/json` . 
- Performansı iyileştirmek için, mümkünse isteği zaman uyumsuz olarak gönderin.

Bir örnek görmek için aşağıdaki tercih edilen dil/çerçeve sekmesine tıklayın.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

ASP.NET ' de, HTTP POST 'u [System .net. http. HttpClient](/dotnet/api/system.net.http.httpclient) sınıfı ile gönderebilirsiniz. Örnek:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Bu kodu örnek uygulamada test ediyorsanız, [öğretici: Azure 'DA SQL veritabanı ile bir ASP.NET uygulaması derleme](app-service-web-tutorial-dotnet-sqldatabase.md), öğe eklendikten sonra [oluşturma eyleminde](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)bir e-posta onayı göndermek için kullanabilirsiniz `Todo` . Yukarıdaki zaman uyumsuz kodu kullanmak için, oluşturma eylemini zaman uyumsuz olarak dönüştürün.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

ASP.NET Core ' de, HTTP gönderisini [System .net. http. HttpClient](/dotnet/api/system.net.http.httpclient) sınıfı ile gönderebilirsiniz. Örnek:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Bu kod, tanıtım basitliği için yazılmıştır. Uygulamada, `HttpClient` her istek için bir nesne örneği oluşturmayın. Dayanıklı [http istekleri uygulamak Için ıhttpclientfactory kullanma](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)kılavuzundaki yönergeleri izleyin.

Bu kodu örnek uygulamada test ediyorsanız [: Azure App Service ' de bir ASP.NET Core ve SQL veritabanı uygulaması oluşturun](tutorial-dotnetcore-sqldb-app.md), öğe eklendikten sonra [oluşturma eyleminde](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)bir e-posta onayı göndermek için bunu kullanabilirsiniz `Todo` .

### <a name="nodejs"></a>[Node.js](#tab/node)

Node.js, HTTP POST 'u [Axios](https://www.npmjs.com/package/axios)gibi bir NPM paketiyle kolayca gönderebilirsiniz. Örnek:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Bu kodu örnek uygulamada test ediyorsanız, [öğretici: Azure 'da bir Node.js ve MongoDB uygulaması oluşturun](tutorial-nodejs-mongodb-app.md), [Makale başarıyla kaydedildikten](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)sonra [Create işlevinde](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)bir e-posta onayı göndermek için bunu kullanabilirsiniz.

### <a name="php"></a>[PHP](#tab/php)

PHP 'de, HTTP POST 'u [guzzle](http://docs.guzzlephp.org/en/stable/index.html)ile kolayca gönderebilirsiniz. Örnek:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Bu kodu örnek uygulamada test ediyorsanız, [öğretici: Azure 'da BIR php ve MySQL uygulaması oluşturun](tutorial-php-mysql-app.md), return ifadesinden hemen önce [Route::p OST işlevinde](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)bir e-posta onayı göndermek için bunu kullanabilirsiniz.

### <a name="python"></a>[Python](#tab/python)

Python 'da HTTP POST 'u [isteklerle](https://pypi.org/project/requests/)kolayca gönderebilirsiniz. Örnek:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
import os
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post(os.environ['LOGIC_APP_URL'], data = payload)
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Bu kodu örnek uygulamada test ediyorsanız [: Azure App Service ' de PostgreSQL Ile Python (Docgo) Web uygulaması çalıştırın](tutorial-python-postgresql-app.md), bunu, return ifadesinden hemen önce [Route::p OST işlevinde](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)bir e-posta onayı göndermek için kullanabilirsiniz.

### <a name="ruby"></a>[Ruby](#tab/ruby)

Ruby 'de, HTTP POST 'u JSONClient ile kolayca gönderebilirsiniz. Örnek:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

[Linux üzerinde Azure App Service bir Ruby ve Postgres uygulaması oluşturmak](tutorial-ruby-postgres-app.md)için örnek uygulamada bu kodu test ediyorsanız, bu işlemi [ @task.save başarılı olduğunda](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30) [oluşturma](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) eyleminde bir e-posta onayı göndermek için kullanabilirsiniz.

---

## <a name="more-resources"></a>Diğer kaynaklar

[Öğretici: Azure App Service’te CORS ile RESTful API barındırma](app-service-web-tutorial-rest-api.md)  
[Logic Apps için HTTP istek/yanıt başvurusu](../connectors/connectors-native-reqres.md)  
[Hızlı başlangıç: Azure Logic Apps Azure portal kullanarak ilk iş akışınızı oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md)
