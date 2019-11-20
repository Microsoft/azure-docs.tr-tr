---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 0c263ed1f18ceaa2db976632ea31b9fe1eb47a93
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907131"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core projesi oluşturma

Yeni bir komut istemi (veya terminal oturumu) açın ve şu komutları çalıştırın:

```console
dotnet new console -o detect-sample
cd detect-sample
```

İlk komut şu iki işlemi yapar. Yeni bir .NET konsol uygulaması oluşturur ve adlı bir dizin oluşturur `detect-sample`. İkinci komut, proje dizinine değiştirir.

Sonra, Json.Net yüklemeniz gerekir. Projenizin dizininden şunu çalıştırın:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Seçin C# dil sürümü

Bu Hızlı Başlangıç C# 7.1 veya sonraki sürümleri. Değiştirmek için birkaç yol vardır C# projeniz için bir sürüm. Bu kılavuzda, nasıl ayarlanacağını göstereceğiz `detect-sample.csproj` dosya. Visual Studio'da dilini değiştirme gibi tüm kullanılabilir seçenekleri görmek için [seçin C# dil sürümü](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Projenizi açın ve ardından `detect-sample.csproj`. Emin olun `LangVersion` 7.1 veya sonraki bir sürüme ayarlayın. Özellik grubu dil sürümü için değilse, bu satırları ekleyin:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Gerekli ad alanları projenize ekleyin.

`dotnet new console` Daha önce çalıştırdığınız komutu tarafından oluşturulan bir projeyi dahil olmak üzere `Program.cs`. Burada uygulama kodunuza giriyorum bu dosyasıdır. Açık `Program.cs`, mevcut using deyimlerinin değiştirin. Bu deyimler, derlemek ve örnek uygulamayı çalıştırmak için gerekli tüm türlerine erişimi olduğundan emin olun.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>JSON yanıtı için sınıflar oluşturma

Sonra, Translator Metin Çevirisi API'si tarafından döndürülen JSON yanıtının serisi kaldırılırken kullanılan bir sınıf oluşturacağız.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class DetectResult
{
    public string Language { get; set; }
    public float Score { get; set; }
    public bool IsTranslationSupported { get; set; }
    public bool IsTransliterationSupported { get; set; }
    public AltTranslations[] Alternatives { get; set; }
}
public class AltTranslations
{
    public string Language { get; set; }
    public float Score { get; set; }
    public bool IsTranslationSupported { get; set; }
    public bool IsTransliterationSupported { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Ortam değişkenlerinden abonelik bilgilerini al

`Program` Sınıfına aşağıdaki satırları ekleyin. Bu satırlar, ortam değişkenlerinden, abonelik anahtarınızı ve uç noktayı okur ve herhangi bir sorunla karşılaşırsanız bir hata oluşturur.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-detect-the-source-texts-language"></a>Kaynak metnin dilini algılamak için bir işlev oluşturma

`Program` Sınıfında adlı`DetectTextRequest()`bir işlev oluşturun. Bu sınıf, Algıla kaynağını çağırmak için kullanılan kodu kapsüller ve sonucu konsola yazdırır.

```csharp
static public async Task DetectTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-detect-request"></a>Algılama isteğini serileştirme

Daha sonra, dil algılamayı gerçekleştiren metni içeren JSON nesnesi oluşturup serileştirmemiz gerekir.

```csharp
System.Object[] body = new System.Object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>İstemci örneği oluşturma ve bir istek oluşturma

Bu satırlar `HttpClient` `HttpRequestMessage`ve ' nin örneğini oluşturur:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>İsteği oluşturun ve yanıtı yazdırın

İçinde şunları `HttpRequestMessage` yapabilirsiniz:

* HTTP yöntemini bildirin
* İstek URI 'sini oluşturun
* İstek gövdesini (serileştirilmiş JSON nesnesi) ekleme
* Gerekli üst bilgileri ekle
* Zaman uyumsuz istek oluşturma
* Yanıtı yazdırma

Bu kodu öğesine `HttpRequestMessage`ekleyin:

```csharp
// Build the request.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
DetectResult[] deserializedOutput = JsonConvert.DeserializeObject<DetectResult[]>(result);
// Iterate over the deserialized response.
foreach (DetectResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.\nTranslation supported: {2}.\nTransliteration supported: {3}.\n",
        o.Language, o.Score, o.IsTranslationSupported, o.IsTransliterationSupported);
    // Create a counter
    int counter = 0;
    // Iterate over alternate translations.
    foreach (AltTranslations a in o.Alternatives)
    {
        counter++;
        Console.WriteLine("Alternative {0}", counter);
        Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.\nTranslation supported: {2}.\nTransliteration supported: {3}.\n",
            a.Language, a.Score, a.IsTranslationSupported, a.IsTransliterationSupported);
    }
}
```

Bilişsel hizmetler çoklu hizmet aboneliği kullanıyorsanız, istek parametrelerinize de dahil `Ocp-Apim-Subscription-Region` etmeniz gerekir. [Multi-Service aboneliğiyle kimlik doğrulama hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Hepsini bir araya getirin

Son adım, `DetectTextRequest()` `Main` işlevini çağırmak için kullanılır. Bu `static void Main(string[] args)` kodla bulun ve değiştirin:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string route = "/detect?api-version=3.0";
    string detectSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await DetectTextRequest(subscriptionKey, endpoint, route, detectSentenceText);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```
## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

Bu, örnek uygulamanızı çalıştırmaya hazırsınız. Komut satırını (veya terminal oturumu), proje dizinine gidin ve çalıştırın:

```console
dotnet run
```

## <a name="sample-response"></a>Örnek yanıt

Örneği çalıştırdıktan sonra, şu şekilde terminalden yazdırılmış olduğunu görmeniz gerekir:

> [!NOTE]
> Bu [diller listesinde](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)ülke/bölge kısaltmasını bulun.

```bash
The detected language is 'en'. Confidence is: 1.
Translation supported: True.
Transliteration supported: False.

Alternative 1
The detected language is 'fil'. Confidence is: 0.82.
Translation supported: True.
Transliteration supported: False.

Alternative 2
The detected language is 'ro'. Confidence is: 1.
Translation supported: True.
Transliteration supported: False.
```

Bu ileti ham JSON 'dan oluşturulmuştur ve bu şöyle görünür:

```json
[  
    {  
        "language":"en",
        "score":1.0,
        "isTranslationSupported":true,
        "isTransliterationSupported":false,
        "alternatives":[  
            {  
                "language":"fil",
                "score":0.82,
                "isTranslationSupported":true,
                "isTransliterationSupported":false
            },
            {  
                "language":"ro",
                "score":1.0,
                "isTranslationSupported":true,
                "isTransliterationSupported":false
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Abonelik anahtarları gibi örnek uygulamanızın kaynak kodundan olan gizli bilgilerin kaldırdığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Translator Metin Çevirisi API'si ile yapabileceğiniz her şeyi anlamak için API başvurusuna göz atın.

> [!div class="nextstepaction"]
> [API başvurusu](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
