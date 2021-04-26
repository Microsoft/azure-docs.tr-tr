---
title: Web uygulamasından Web API 'si çağırma | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir Web uygulaması oluşturmayı öğrenin (korumalı bir Web API 'SI çağırma)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753292"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Web API 'Leri çağıran bir Web uygulaması: Web API 'SI çağırma

Artık bir belirteciniz olduğuna göre, korumalı bir Web API 'SI çağırabilirsiniz. Genellikle Web uygulamanızın denetleyicisinden veya sayfalarından bir aşağı akış API 'SI çağırabilirsiniz.

## <a name="call-a-protected-web-api"></a>Korumalı bir Web API 'SI çağırma

Korumalı bir Web API 'SI çağırmak dile ve tercih ettiğiniz çerçeveye bağlıdır:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web* kullandığınızda, bir API 'yi çağırmak için üç kullanım seçeneğiniz vardır:

- [Seçenek 1: Microsoft Graph SDK ile Microsoft Graph çağırma](#option-1-call-microsoft-graph-with-the-sdk)
- [2. seçenek: yardımcı sınıfla bir aşağı akış Web API 'SI çağırma](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Seçenek 3: yardımcı sınıfı olmadan bir aşağı akış Web API 'SI çağırma](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Seçenek 1: SDK ile Microsoft Graph çağırma

Microsoft Graph çağırmak istiyorsunuz. Bu senaryoda, `AddMicrosoftGraph` [kod yapılandırması](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)' nda belirtildiği gibi *Startup. cs* ' ye eklemiş olursunuz ve `GraphServiceClient` eylemlerinizi doğrudan denetleyicinize veya sayfa yapıcısına, eylemlerde kullanmak üzere ekleyebilirsiniz. Aşağıdaki örnek Razor sayfası, oturum açmış kullanıcının fotoğrafını görüntüler.

```CSharp
[Authorize]
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
 private readonly GraphServiceClient _graphServiceClient;

 public IndexModel(GraphServiceClient graphServiceClient)
 {
    _graphServiceClient = graphServiceClient;
 }

 public async Task OnGet()
 {
  var user = await _graphServiceClient.Me.Request().GetAsync();
  try
  {
   using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
   {
    byte[] photoByte = ((MemoryStream)photoStream).ToArray();
    ViewData["photo"] = Convert.ToBase64String(photoByte);
   }
   ViewData["name"] = user.DisplayName;
  }
  catch (Exception)
  {
   ViewData["photo"] = null;
  }
 }
}
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>2. seçenek: yardımcı sınıfla bir aşağı akış Web API 'SI çağırma

Microsoft Graph dışında bir Web API 'SI çağırmak istiyorsunuz. Bu durumda, `AddDownstreamWebApi` [kod yapılandırmasında](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)belirtildiği gibi *Startup. cs* ' ye eklemiş olursunuz ve `IDownstreamWebApi` denetleyicinize veya sayfa yapıcısına doğrudan bir hizmet ekleyebilir ve bunu eylemlerde kullanabilirsiniz:

```CSharp
[Authorize]
[AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
public class TodoListController : Controller
{
  private IDownstreamWebApi _downstreamWebApi;
  private const string ServiceName = "TodoList";

  public TodoListController(IDownstreamWebApi downstreamWebApi)
  {
    _downstreamWebApi = downstreamWebApi;
  }

  public async Task<ActionResult> Details(int id)
  {
    var value = await _downstreamWebApi.CallWebApiForUserAsync(
      ServiceName,
      options =>
      {
        options.RelativePath = $"me";
      });
      return View(value);
  }
}
```

`CallWebApiForUserAsync`Ayrıca, doğrudan bir nesne almanızı sağlayan kesin olarak yazılmış genel geçersiz kılmalar içerir. Örneğin, aşağıdaki Yöntem `Todo` , Web API 'si tarafından döndürülen JSON öğesinin kesin türü belirtilmiş bir temsili olan bir örnek alır.

```CSharp
    // GET: TodoList/Details/5
    public async Task<ActionResult> Details(int id)
    {
        var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
            ServiceName,
            null,
            options =>
            {
                options.HttpMethod = HttpMethod.Get;
                options.RelativePath = $"api/todolist/{id}";
            });
        return View(value);
    }
   ```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Seçenek 3: yardımcı sınıfı olmadan bir aşağı akış Web API 'SI çağırma

Hizmeti kullanarak bir belirteci el ile almaya karar verdiniz `ITokenAcquisition` ve şimdi belirteci kullanmanız gerekiyor. Bu durumda, aşağıdaki kod, [Web API 'lerini çağıran bir Web uygulamasında gösterilen örnek koda devam eder: uygulama için bir belirteç alın](scenario-web-app-call-api-acquire-token.md). Kod, Web uygulaması denetleyicilerinin eylemleri içinde çağrılır.

Belirteci aldıktan sonra, bu durumda Microsoft Graph, aşağı akış API 'sini çağırmak için bir taşıyıcı belirteci olarak kullanın.

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> Herhangi bir Web API 'sini çağırmak için aynı prensibi kullanabilirsiniz.
>
> Çoğu Azure Web API 'SI, Microsoft Graph olması durumunda API 'nin çağrılmasını kolaylaştıran bir SDK sağlar. Bkz. Örneğin, Microsoft. Identity. Web kullanarak ve Azure depolama SDK 'sını kullanarak bir Web uygulaması örneği için [Azure AD Ile blob depolamaya erişim yetkisi veren bir Web uygulaması oluşturma](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](scenario-web-app-call-api-production.md).