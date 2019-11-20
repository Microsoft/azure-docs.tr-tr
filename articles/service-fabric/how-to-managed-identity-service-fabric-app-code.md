---
title: Azure Service Fabric-yönetilen kimliği Service Fabric uygulamalarla kullanma | Microsoft Docs
description: Service Fabric uygulama kodundan Yönetilen kimlikler kullanma
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 6a3d33954bda0605e752555922914a9fd432d8c1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968232"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>Azure hizmetlerine erişmek için Service Fabric uygulamasının yönetilen kimliğinden yararlanma (Önizleme)

Service Fabric uygulamalar, Azure Active Directory tabanlı kimlik doğrulamasını destekleyen diğer Azure kaynaklarına erişmek için yönetilen kimliklerden yararlanabilir. Bir uygulama, kimliğini temsil eden, sistem tarafından atanan veya Kullanıcı tarafından atanan bir [erişim belirteci](../active-directory/develop/developer-glossary.md#access-token) alabilir ve kendisini, [korunan kaynak sunucu](../active-directory/develop/developer-glossary.md#resource-server)olarak da bilinen başka bir hizmete doğrulamak için bir ' taşıyıcı ' belirteci olarak kullanabilir. Belirteç Service Fabric uygulamasına atanan kimliği temsil eder ve yalnızca bu kimliği paylaşan Azure kaynaklarına (SF uygulamaları dahil) verilmeyecektir. Yönetilen kimliklerin ayrıntılı bir açıklaması ve sistem tarafından atanan ve Kullanıcı tarafından atanan kimlikler arasındaki ayrım için [yönetilen kimliğe genel bakış](../active-directory/managed-identities-azure-resources/overview.md) belgelerine bakın. Bu makalede, yönetilen kimlik özellikli bir Service Fabric uygulamasına [istemci uygulaması](../active-directory/develop/developer-glossary.md#client-application) olarak başvuracağız.

> [!IMPORTANT]
> Yönetilen bir kimlik, kaynağı içeren abonelikle ilişkili olan ilgili Azure AD kiracısında bir Azure kaynağı ile hizmet sorumlusu arasındaki ilişkiyi temsil eder. Bu nedenle, Service Fabric bağlamında yönetilen kimlikler yalnızca Azure kaynakları olarak dağıtılan uygulamalar için desteklenir. 

> [!IMPORTANT]
> Service Fabric uygulamasının yönetilen kimliğini kullanmadan önce, istemci uygulamasına korunan kaynağa erişim verilmesi gerekir. Lütfen desteği denetlemek için [Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) listesine ve ardından ilgili hizmetin ilgilendiğiniz kaynaklara yönelik bir kimlik erişimi sağlamak için ilgili hizmetin belgelerine bakın. 

## <a name="acquiring-an-access-token-using-rest-api"></a>REST API kullanarak erişim belirteci edinme
Yönetilen kimlik için etkinleştirilmiş kümeler ' de Service Fabric çalışma zamanı, uygulamaların erişim belirteçlerini almak için kullanabileceği bir localhost uç noktasını kullanıma sunar. Uç nokta, kümenin her düğümünde kullanılabilir ve bu düğümdeki tüm varlıklar için erişilebilir. Yetkili çağıranlar, bu uç noktayı çağırarak ve bir kimlik doğrulama kodu sunarak erişim belirteçleri elde edebilir. kod, her ayrı hizmet kodu paketi etkinleştirmesi için Service Fabric çalışma zamanı tarafından oluşturulur ve bu hizmet kodu paketini barındıran işlemin ömrüne bağlanır.

Özellikle, yönetilen kimlik özellikli Service Fabric hizmetinin ortamı aşağıdaki değişkenlerle birlikte sunulacaktır:
- ' MSI_ENDPOINT ': localhost uç noktası, yol, API sürümü ve bu hizmetin yönetilen kimliğine karşılık gelen parametrelerle birlikte tamamlanır
- ' MSI_SECRET ': bir donuk dize olan ve geçerli düğümdeki hizmeti benzersiz olarak temsil eden bir kimlik doğrulama kodu

> [!NOTE]
> ' MSI_ENDPOINT ' ve ' MSI_SECRET ' adları, yönetilen kimliklerin ("Yönetilen Hizmet Kimliği") önceki bir atamasını ifade eder ve artık kullanım dışıdır. Adlar Ayrıca, yönetilen kimlikleri destekleyen diğer Azure hizmetleri tarafından kullanılan eşdeğer ortam değişkeni adlarıyla de tutarlıdır.

> [!IMPORTANT]
> Uygulama kodu, ' MSI_SECRET ' ortam değişkeninin değerini hassas veriler olarak kabul etmelidir; bunların günlüğe kaydedilmeyeceğini veya başka bir şekilde dağıtımını kaldırmamalıdır. Kimlik doğrulama kodu, yerel düğümün dışında bir değere sahip değildir veya hizmeti barındıran işlem sonlandıktan sonra, Service Fabric hizmetin kimliğini temsil eder ve bu nedenle erişim belirtecinin kendisi ile aynı önlemler ile değerlendirilmelidir.

İstemci, bir belirteç almak için aşağıdaki adımları gerçekleştirir:
- API sürümü ve belirteç için gereken kaynak (hedef kitle) ile yönetilen kimlik uç noktasını (MSI_ENDPOINT değeri) birleştirerek bir URI oluşturur
- Belirtilen URI için bir GET http isteği oluşturur
- istek için bir üst bilgi olarak kimlik doğrulama kodu (MSI_SECRET değeri) ekler
- isteği gönderir

Başarılı bir yanıt, elde edilen erişim belirtecini temsil eden bir JSON yükünün yanı sıra bunu tanımlayan meta verileri içerir. Başarısız bir yanıt hatanın açıklamasını da içerecektir. Hata işleme hakkında daha fazla bilgi için aşağıya bakın.

Erişim belirteçleri çeşitli düzeylerde (düğüm, küme, kaynak sağlayıcısı hizmeti) Service Fabric önbelleğe alınır, bu nedenle başarılı bir yanıt, belirtecin Kullanıcı uygulamasının isteğine yanıt olarak doğrudan verildiğini göstermez. Belirteçler yaşam sürelerinden daha az olacak şekilde önbelleğe alınır ve bu nedenle bir uygulamanın geçerli bir belirteç almasına garanti edilir. Uygulama kodunun kendi sahip olduğu tüm erişim belirteçlerini önbelleğe alması önerilir; önbelleğe alma anahtarı, hedef kitleyi (bir türevi) içermelidir. 


Örnek istek:
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
burada:

| Öğe | Açıklama |
| ------- | ----------- |
| `GET` | Uç noktadan veri almak istediğinizi gösteren HTTP fiili. Bu durumda, bir OAuth erişim belirteci. | 
| `http://localhost:2377/metadata/identity/oauth2/token` | MSI_ENDPOINT ortam değişkeni aracılığıyla sağlanmış Service Fabric uygulamalar için yönetilen kimlik uç noktası. |
| `api-version` | Yönetilen kimlik belirteci hizmetinin API sürümünü belirten bir sorgu dizesi parametresi; Şu anda kabul edilen tek değer `2019-07-01-preview`, ve değiştirilebilir. |
| `resource` | Hedef kaynağın uygulama KIMLIĞI URI 'sini gösteren bir sorgu dizesi parametresi. Bu, verilen belirtecin `aud` (hedef kitle) talebi olarak yansıtılacaktır. Bu örnek, bir uygulama kimliği URI 'si https:\//keyvault.Azure.com/olan Azure Key Vault erişmek için bir belirteç ister. |
| `Secret` | Çağıranın kimliğini doğrulamak için Service Fabric Hizmetleri için Service Fabric yönetilen kimlik belirteci hizmeti için gerekli olan bir HTTP istek üst bilgisi alanı. Bu değer, MSI_SECRET ortam değişkeni aracılığıyla SF Runtime tarafından sağlanır. |


Örnek yanıt:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
burada:

| Öğe | Açıklama |
| ------- | ----------- |
| `token_type` | Belirtecin türü; Bu durumda, bu belirtecin sunucu (' taşıyıcı ') belirtecin amaçlanan konusu olduğu anlamına gelen bir "taşıyıcı" erişim belirteci. |
| `access_token` | İstenen erişim belirteci. Güvenli bir REST API çağrılırken, belirteç `Authorization` istek üst bilgisi alanına bir "taşıyıcı" belirteci olarak katıştırılır ve bu da API 'nin çağıranın kimliğini doğrulamasına izin verir. | 
| `expires_on` | Erişim belirtecinin süre sonu zaman damgası; "1970-01-01t0:0: 0z UTC" değerinden saniye sayısı olarak gösterilir ve belirtecin `exp` talebine karşılık gelir. Bu durumda, belirtecin süresi 2019 ' de dolar-08-08T06:10:11 + 00:00 (RFC 3339 ' de)|
| `resource` | İsteğin `resource` sorgu dizesi parametresi aracılığıyla belirtilen erişim belirtecinin verildiği kaynak; belirtecin ' AUD ' talebine karşılık gelir. |


## <a name="acquiring-an-access-token-using-c"></a>Kullanarak erişim belirteci almaC#
Yukarıdaki ' de yer C#alır:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Yönetilen kimlik kullanarak Service Fabric uygulamadan Key Vault erişme
Bu örnek, yönetilen kimlik kullanılarak Key Vault depolanan bir gizli dizi ile ilgili olarak bir gizli dizi erişimini göstermek için yukarıdaki ' a

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Hata işleme
HTTP yanıt üst bilgisinin ' durum kodu ' alanı, isteğin başarı durumunu belirtir; ' 200 OK ' durumu başarıyı gösterir ve yanıt yukarıda açıklanan erişim belirtecini içerecektir. Olası hata yanıtlarının kısa bir listesi aşağıda verilmiştir.

| Durum Kodu | Hata nedeni | Nasıl Idare edilecek |
| ----------- | ------------ | ------------- |
| 404 bulunamadı. | Bilinmeyen kimlik doğrulama kodu veya uygulamaya yönetilen bir kimlik atanmadı. | Uygulama kurulumunu veya belirteç alma kodunu yeniden yapın. |
| 429 çok fazla istek. |  AAD veya SF tarafından uygulanan kısıtlama sınırına ulaşıldı. | Üstel geri alma ile yeniden deneyin. Aşağıdaki rehbere bakın. |
| istekte 4xx hatası. | İstek parametrelerinden biri veya daha fazlası hatalıydı. | Yeniden denemeyin.  Daha fazla bilgi için hata ayrıntılarını inceleyin.  4 xx hata tasarım zamanı hatalardır.|
| hizmetten 5xx hatası. | Yönetilen kimlik alt sistemi veya Azure Active Directory geçici bir hata döndürdü. | Kısa bir süre sonra yeniden denemek güvenlidir. Yeniden denemeden sonra bir azaltma koşuluna (429) ulaşırsınız.|

Bir hata oluşursa, karşılık gelen HTTP yanıt gövdesi hata ayrıntılarına sahip bir JSON nesnesi içerir:

| Öğe | Açıklama |
| ------- | ----------- |
| code | Hata kodu. |
| correlationId | Hata ayıklama için kullanılabilen bir bağıntı KIMLIĞI. |
| message | Hatanın ayrıntılı açıklaması. **Hata açıklamaları herhangi bir zamanda değişebilir. Hata iletisinin kendisine bağlı değildir.**|

Örnek hata:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Yönetilen kimliklere özgü tipik Service Fabric hatalarının listesi aşağıda verilmiştir:

| Kod | `Message` | Açıklama | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Gizli dizi, istek üst bilgilerinde bulunamadı. | İstek ile kimlik doğrulama kodu sağlanmadı. | 
| Managedıdentitynotfound | Belirtilen uygulama konağı için yönetilen kimlik bulunamadı. | Uygulamanın kimliği yok veya kimlik doğrulama kodu bilinmiyor. |
| ArgumentNullOrEmpty | ' Resource ' parametresi null veya boş dize olmamalıdır. | İstekte kaynak (hedef kitle) sağlanmadı. |
| InvalidApiVersion | ' ' Api sürümü desteklenmiyor. Desteklenen sürüm ' 2019-07-01-Preview '. | İstek URI 'sinde belirtilen eksik veya desteklenmeyen API sürümü. |
| Dahili sunucu hatası | Bir hata oluştu. | Yönetilen kimlik alt sisteminde, muhtemelen Service Fabric yığının dışında bir hatayla karşılaşıldı. Büyük olasılıkla kaynak için geçersiz bir değer belirtilmiş olabilir (sonda '/' denetimi için denetim) | 

## <a name="retry-guidance"></a>Yeniden deneme Kılavuzu 

Genellikle yeniden denenebilir hata kodu 429 ' dir (çok fazla Istek); iç sunucu hataları/5xx hata kodları yeniden denenebilir, ancak nedeni kalıcı olabilir. 

Azaltma sınırları, yönetilen kimlik alt sistemine yapılan çağrı sayısı için geçerlidir-özellikle ' yukarı akış ' bağımlılıkları (yönetilen kimlik Azure hizmeti veya güvenli belirteç hizmeti). Service Fabric, işlem hattındaki çeşitli düzeylerde önbelleğe alır, ancak ilgili bileşenlerin dağıtılmış yapısı verildiğinde, arayan tutarsız azaltma yanıtları ile karşılaşabilir (örneğin, bir uygulamanın bir düğümünde/örneğinde değil, aynı kimlik için bir belirteç istenirken farklı düğüm.) Daraltma koşulu ayarlandığında, aynı uygulamadan gelen sonraki istekler, koşul temizlenene kadar HTTP durum kodu 429 (çok fazla Istek) ile başarısız olabilir.  

Azaltma nedeniyle başarısız olan isteklerin üstel geri alma ile yeniden denenmesi önerilir, örneğin: 

| Çağrı dizini | 429 alma eylemi | 
| --- | --- | 
| 1\. | 1 saniye bekleyin ve yeniden deneyin |
| 2 | 2 saniye bekleyip yeniden deneyin |
| 3 | 4 saniye bekleyin ve yeniden deneyin |
| 4 | 8 saniye bekleyin ve yeniden deneyin |
| 4 | 8 saniye bekleyin ve yeniden deneyin |
| 5 | 16 saniye bekleyip yeniden deneyin |

## <a name="resource-ids-for-azure-services"></a>Azure hizmetleri için kaynak kimlikleri
Bkz. Azure AD [kimlik doğrulamasını](../active-directory/managed-identities-azure-resources/services-support-msi.md) destekleyen Azure Hizmetleri, Azure AD 'yi destekleyen kaynakların listesi ve bunların Ilgili kaynak kimlikleri.

## <a name="next-steps"></a>Sonraki adımlar
* [Sistem tarafından atanan yönetilen kimlik ile Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Kullanıcı tarafından atanan yönetilen kimlik ile bir Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric uygulamasına diğer Azure kaynaklarına erişim izni verme](./how-to-grant-access-other-resources.md)