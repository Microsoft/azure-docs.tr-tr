---
title: Erişim belirteci almak için bir sanal makinede Yönetilen kimlikler kullanma-Azure AD
description: Bir OAuth erişim belirteci almak için bir sanal makinelerde Azure kaynakları için yönetilen kimliklerin kullanılmasına yönelik adım adım yönergeler ve örnekler.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ee7739d9dbfd34190dc1e856b98fdd21be15743
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364949"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Erişim belirteci almak için bir Azure VM 'de Azure kaynakları için Yönetilen kimlikler kullanma 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen bir kimlikle Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, belirteç alımı için çeşitli kod ve betik örnekleri ve ayrıca belirteç süre sonu ve HTTP hatalarını işleme gibi önemli konularda rehberlik sağlanır. 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Bu makaledeki Azure PowerShell örnekleri kullanmayı planlıyorsanız, [Azure PowerShell](/powershell/azure/install-az-ps)en son sürümünü yüklediğinizden emin olun.


> [!IMPORTANT]
> - Bu makaledeki tüm örnek kod/betik, istemcinin Azure kaynakları için yönetilen kimliklere sahip bir sanal makinede çalıştığını varsayar. VM 'nize uzaktan bağlanmak için Azure portal sanal makine "Bağlan" özelliğini kullanın. Bir VM 'de Azure kaynakları için yönetilen kimlikleri etkinleştirme hakkında daha fazla bilgi için bkz. Azure portal veya varyant makalelerinden birini [kullanarak BIR VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-portal-windows-vm.md)(POWERSHELL, CLI, bir şablon veya BIR Azure SDK kullanarak). 

> [!IMPORTANT]
> - Azure kaynakları için yönetilen kimliklerin güvenlik sınırı, kullanıldığı kaynaktır. Bir sanal makinede çalışan tüm kod/betikler, üzerinde kullanılabilir olan tüm yönetilen kimlikler için belirteçleri isteyebilir ve alabilir. 

## <a name="overview"></a>Genel Bakış

Bir istemci uygulaması, belirli bir kaynağa erişmek için yalnızca Azure kaynakları için Yönetilen kimlikler, [yalnızca uygulama erişim belirteci](../develop/developer-glossary.md#access-token) isteyebilir. Belirteç, [Azure kaynakları hizmet sorumlusu için yönetilen kimliklere dayalıdır](overview.md#managed-identity-types). Bu nedenle, istemcinin kendi hizmet sorumlusu altında erişim belirteci almak için kendisini kaydetmesi gerekmez. Belirteç, [istemci kimlik bilgileri gerektiren hizmetten hizmete çağrılar](../develop/v2-oauth2-client-creds-grant-flow.md)için bir taşıyıcı belirteci olarak kullanım için uygundur.

| Bağlantı | Açıklama |
| -------------- | -------------------- |
| [HTTP kullanarak belirteç al](#get-a-token-using-http) | Azure kaynakları belirteç uç noktası için Yönetilen kimlikler protokol ayrıntıları |
| [.NET için Microsoft. Azure. Services. AppAuthentication kitaplığını kullanarak bir belirteç alın](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | .NET istemcisinden Microsoft. Azure. Services. AppAuthentication kitaplığını kullanma örneği
| [C kullanarak belirteç al #](#get-a-token-using-c) | Bir C# istemcisinden Azure kaynakları REST uç noktası için Yönetilen kimlikler kullanma örneği |
| [Java kullanarak belirteç edinme](#get-a-token-using-java) | Java istemcisinden Azure kaynakları REST uç noktası için Yönetilen kimlikler kullanma örneği |
| [Go kullanarak belirteç alın](#get-a-token-using-go) | Bir go istemcisinden Azure kaynakları REST uç noktası için Yönetilen kimlikler kullanma örneği |
| [Azure PowerShell kullanarak belirteç alın](#get-a-token-using-azure-powershell) | PowerShell istemcisinden Azure kaynakları REST uç noktası için Yönetilen kimlikler kullanma örneği |
| [KıVRıMLı kullanarak belirteç alın](#get-a-token-using-curl) | Bir bash/KıVRıMLı istemciden Azure kaynakları REST uç noktası için Yönetilen kimlikler kullanma örneği |
| Belirteç önbelleğe alma işleme | Süre dolma erişim belirteçlerini işlemeye yönelik kılavuz |
| [Hata işleme](#error-handling) | Azure kaynakları belirteç uç noktası için yönetilen kimliklerden döndürülen HTTP hatalarını işlemeye yönelik kılavuz |
| [Azure hizmetleri için kaynak kimlikleri](#resource-ids-for-azure-services) | Desteklenen Azure hizmetleri için kaynak kimliklerinin nereden alınacağı |

## <a name="get-a-token-using-http"></a>HTTP kullanarak belirteç al 

Erişim belirteci almak için temel arabirim, geri kalanı temel alarak, VM üzerinde çalışan ve HTTP REST çağrısı yapan tüm istemci uygulamaları için erişilebilir hale getirir. Bu, istemci sanal makinede bir uç nokta (Azure AD uç noktası) kullandığından Azure AD programlama modeline benzer.

Azure Instance Metadata Service (ıMDS) uç noktasını kullanan örnek istek *(önerilir)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Öğe | Açıklama |
| ------- | ----------- |
| `GET` | Uç noktadan veri almak istediğinizi gösteren HTTP fiili. Bu durumda, bir OAuth erişim belirteci. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Instance Metadata Service için Azure kaynakları için Yönetilen kimlikler uç noktası. |
| `api-version`  | IMDS uç noktası için API sürümünü gösteren bir sorgu dizesi parametresi. Lütfen API sürümünü `2018-02-01` veya üstünü kullanın. |
| `resource` | Hedef kaynağın uygulama KIMLIĞI URI 'sini gösteren bir sorgu dizesi parametresi. Ayrıca, `aud` verilen belirtecin (hedef kitle) talebinde de görüntülenir. Bu örnek, uygulama KIMLIĞI URI 'SI olan Azure Resource Manager erişmek için bir belirteç ister `https://management.azure.com/` . |
| `Metadata` | Sunucu tarafı Isteği forgery (SSRF) saldırılarına karşı risk azaltma olarak Azure kaynakları için Yönetilen kimlikler için gereken bir HTTP istek üst bilgisi alanı. Bu değer, tüm küçük durumlarda "true" olarak ayarlanmalıdır. |
| `object_id` | Seçim Belirteci istediğiniz yönetilen kimliğin object_id belirten bir sorgu dizesi parametresi. SANAL makinenizde birden çok kullanıcı tarafından atanan yönetilen kimlik varsa, gereklidir.|
| `client_id` | Seçim Belirteci istediğiniz yönetilen kimliğin client_id belirten bir sorgu dizesi parametresi. SANAL makinenizde birden çok kullanıcı tarafından atanan yönetilen kimlik varsa, gereklidir.|
| `mi_res_id` | Seçim Belirteci istediğiniz yönetilen kimliğin mi_res_id (Azure Kaynak KIMLIĞI) belirten bir sorgu dizesi parametresi. SANAL makinenizde birden çok kullanıcı tarafından atanan yönetilen kimlik varsa, gereklidir. |

Örnek yanıt:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Öğe | Açıklama |
| ------- | ----------- |
| `access_token` | İstenen erişim belirteci. Güvenli bir REST API çağrılırken, belirteç `Authorization` istek üst bilgisi alanına bir "taşıyıcı" belirteci olarak katıştırılır ve bu da API 'nin çağıranın kimliğini doğrulamasına izin verir. | 
| `refresh_token` | Azure kaynakları için Yönetilen kimlikler tarafından kullanılmıyor. |
| `expires_in` | Erişim belirtecinin, verme zamanından önce geçerli olmaya devam etmesi için geçmesi gereken saniye sayısı. Verme süresi belirtecin `iat` talebinde bulunabilir. |
| `expires_on` | Erişim belirtecinin süresi dolduğu zaman aralığı. Tarih, "1970-01-01T0:0: 0Z UTC" (belirtecin talebine karşılık gelir) için saniye sayısı olarak gösterilir `exp` . |
| `not_before` | Erişim belirteci yürürlüğe girer ve kabul edilebilir. Tarih, "1970-01-01T0:0: 0Z UTC" (belirtecin talebine karşılık gelir) için saniye sayısı olarak gösterilir `nbf` . |
| `resource` | İsteğin sorgu dizesi parametresiyle eşleşen erişim belirtecinin istendiği kaynak `resource` . |
| `token_type` | "Taşıyıcı" erişim belirteci olan belirtecin türü, bu, kaynağın bu belirtecin taşıyıcının erişim izni verebileceği anlamına gelir. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>.NET için Microsoft. Azure. Services. AppAuthentication kitaplığını kullanarak bir belirteç alın

.NET uygulamaları ve işlevleri için, Azure kaynakları için Yönetilen kimlikler ile çalışmanın en kolay yolu Microsoft. Azure. Services. AppAuthentication paketidir. Bu kitaplık Ayrıca, Visual Studio, [Azure CLI](/cli/azure)veya Active Directory tümleşik kimlik doğrulaması için kullanıcı hesabınızı kullanarak kodunuzu geliştirme makinenizde yerel olarak sınamanızı sağlar. Bu kitaplıkla ilgili yerel geliştirme seçenekleri hakkında daha fazla bilgi için [Microsoft. Azure. Services. AppAuthentication başvurusuna](/dotnet/api/overview/azure/service-to-service-authentication)bakın. Bu bölümde, kodunuzda kitaplığı kullanmaya nasıl başlacağınız gösterilmektedir.

1. Uygulamanıza [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ve [Microsoft. Azure. keykasa](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet paketlerine başvurular ekleyin.

2.  Uygulamanıza aşağıdaki kodu ekleyin:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Microsoft. Azure. Services. AppAuthentication ve sunduğu işlemler hakkında daha fazla bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler .net örneği Ile](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet) [Microsoft. Azure. Services. appauthentication başvurusu](/dotnet/api/overview/azure/service-to-service-authentication) ve App Service ve keykasası.

## <a name="get-a-token-using-c"></a>C kullanarak belirteç al #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Java kullanarak belirteç edinme

Java kullanarak bir belirteç almak için bu [JSON kitaplığını](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) kullanın.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Go kullanarak belirteç alın

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Azure PowerShell kullanarak belirteç alın

Aşağıdaki örnek, bir PowerShell istemcisinden Azure kaynakları REST uç noktası için yönetilen kimliklerin nasıl kullanılacağını gösterir:

1. Erişim belirteci alın.
2. Bir Azure Resource Manager REST API çağırmak ve VM hakkında bilgi almak için erişim belirtecini kullanın. Abonelik KIMLIĞINIZI, kaynak grubu adını ve sanal makine adını sırasıyla, ve için değiştirdiğinizden emin olun `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>` `<VM-NAME>` .

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Yanıttan erişim belirtecini ayrıştırmaya ilişkin örnek:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>KıVRıMLı kullanarak belirteç alın

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Yanıttan erişim belirtecini ayrıştırmaya ilişkin örnek:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Belirteç önbelleğe alma

Azure kaynakları alt sistemi için Yönetilen kimlikler, önbellek belirteçleri sağlarken kodunuzda belirteç önbelleği de uygulamanızı öneririz. Sonuç olarak, kaynağın belirtecin dolduğunu gösterdiği senaryolar için hazırlanmalısınız. 

Yalnızca şu durumlarda Azure AD 'ye yönelik hat üzeri aramalar:

- Azure kaynakları alt sistemi önbelleği için yönetilen kimliklerden belirteç olmaması nedeniyle önbellek isabetsizlik durumu oluşur.
- Önbelleğe alınan belirtecin geçerliliği zaman aşımına uğradı.

## <a name="error-handling"></a>Hata işleme

Azure kaynakları için Yönetilen kimlikler uç noktası, HTTP yanıt iletisi üstbilgisinin durum kodu alanı aracılığıyla 4xx veya 5xx hatası olarak hata verir:

| Durum Kodu | Hata nedeni | Nasıl Idare edilecek |
| ----------- | ------------ | ------------- |
| 404 bulunamadı. | IDS uç noktası güncelleştiriliyor. | Üstel geri alma ile yeniden deneyin. Aşağıdaki rehbere bakın. |
| 429 çok fazla istek. |  IDS kısıtlama sınırına ulaşıldı. | Üstel geri alma ile yeniden deneyin. Aşağıdaki rehbere bakın. |
| istekte 4xx hatası. | İstek parametrelerinden biri veya daha fazlası hatalıydı. | Yeniden denemeyin.  Daha fazla bilgi için hata ayrıntılarını inceleyin.  4 xx hata tasarım zamanı hatalardır.|
| 5xx hizmetten geçici bir hata. | Azure kaynakları için Yönetilen kimlikler alt sistem veya Azure Active Directory geçici bir hata döndürdü. | En az 1 saniye bekledikten sonra yeniden denemek güvenlidir.  Çok hızlı veya çok sık yeniden deneme yaparsanız, ıDS ve/veya Azure AD bir hız sınırı hatası döndürebilir (429).|
| timeout | IDS uç noktası güncelleştiriliyor. | Üstel geri alma ile yeniden deneyin. Aşağıdaki rehbere bakın. |

Bir hata oluşursa, karşılık gelen HTTP yanıt gövdesi hata ayrıntılarına sahip JSON içerir:

| Öğe | Açıklama |
| ------- | ----------- |
| error   | Hata tanımlayıcısı. |
| error_description | Hatanın ayrıntılı açıklaması. **Hata açıklamaları herhangi bir zamanda değişebilir. Hata açıklamasındaki değerlere göre dalların kodunu yazma.**|

### <a name="http-response-reference"></a>HTTP yanıt başvurusu

Bu bölüm olası hata yanıtlarını belgeler. Bir "200 OK" durumu başarılı bir yanıt ve erişim belirteci access_token öğesinde JSON yanıt gövdesi içinde yer alır.

| Durum kodu | Hata | Hata Açıklaması | Çözüm |
| ----------- | ----- | ----------------- | -------- |
| 400 Hatalı İstek | invalid_resource | AADSTS50001: adlı uygulama *\<URI\>* adlı kiracıda bulunamadı *\<TENANT-ID\>* . Uygulama, kiracının yöneticisi tarafından yüklenmediyse veya kiracıdaki herhangi bir kullanıcı tarafından onaylanmadıysa bu durum ortaya çıkabilir. Kimlik doğrulama isteğinizi yanlış kiracıya göndermiş olabilirsiniz. \ | (Yalnızca Linux) |
| 400 Hatalı İstek | bad_request_102 | Gerekli meta veri üst bilgisi belirtilmedi | İsteğiniz için `Metadata` istek üst bilgisi alanı eksik ya da yanlış biçimlendirilmiş. Değer `true` , her küçük örnekte, olarak belirtilmelidir. Bir örnek için önceki REST bölümünde "örnek istek" başlığına bakın.|
| 401 Yetkisiz | unknown_source | Bilinmeyen kaynak *\<URI\>* | HTTP GET istek URI 'nizin doğru biçimlendirildiğinden emin olun. `scheme:host/resource-path`Bölüm olarak belirtilmelidir `http://localhost:50342/oauth2/token` . Bir örnek için önceki REST bölümünde "örnek istek" başlığına bakın.|
|           | invalid_request | İstekte gerekli bir parametre eksik, geçersiz bir parametre değeri içeriyor, birden fazla parametre içeriyor veya başka şekilde hatalı biçimlendirilmiş. |  |
|           | unauthorized_client | İstemci bu yöntemi kullanarak bir erişim belirteci isteme yetkisine sahip değil. | Azure kaynakları için yönetilen kimliklere doğru şekilde yapılandırılmış bir VM 'deki bir istek neden oldu. VM yapılandırması için yardıma ihtiyacınız varsa [Azure Portal kullanarak VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-portal-windows-vm.md) konusuna bakın. |
|           | access_denied | Kaynak sahibi veya yetkilendirme sunucusu isteği reddetti. |  |
|           | unsupported_response_type | Yetkilendirme sunucusu, bu yöntemi kullanarak bir erişim belirteci edinmeyi desteklemez. |  |
|           | invalid_scope | İstenen kapsam geçersiz, bilinmiyor veya hatalı biçimlendirilmiş. |  |
| 500 İç sunucu hatası | bilinmeyen | Active Directory 'den belirteç alınamadı. Ayrıntılar için bkz. günlüklere ait Günlükler *\<file path\>* | Azure kaynakları için yönetilen kimliklerin VM 'de etkinleştirildiğini doğrulayın. VM yapılandırması için yardıma ihtiyacınız varsa [Azure Portal kullanarak VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-portal-windows-vm.md) konusuna bakın.<br><br>Ayrıca, HTTP GET istek URI 'nizin, özellikle de sorgu dizesinde belirtilen kaynak URI 'sinin doğru biçimlendirildiğinden emin olun. Örneğin önceki REST bölümünde yer alan "örnek isteği" veya hizmetlerin bir listesi ve bunların ilgili kaynak kimlikleri için [Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri](./services-support-managed-identities.md) bölümüne bakın.

## <a name="retry-guidance"></a>Yeniden deneme Kılavuzu 

404, 429 veya 5xx hata kodu alırsanız yeniden denemeniz önerilir (bkz. Yukarıdaki [hata işleme](#error-handling) ).

Azaltma sınırları, ıMDS uç noktasına yapılan çağrıların sayısı için geçerlidir. Daraltma eşiği aşıldığında, azaltma etkin durumdayken ıDS uç noktası diğer istekleri sınırlandırır. Bu süre boyunca, ıMDS uç noktası 429 ("çok fazla istek") HTTP durum kodunu döndürür ve istekler başarısız olur. 

Yeniden denemek için aşağıdaki stratejiyi öneririz: 

| **Yeniden deneme stratejisi** | **Ayarlar** | **Değerler** | **Nasıl çalışır?** |
| --- | --- | --- | --- |
|ExponentialBackoff |Yeniden deneme sayısı<br />En düşük geri alma<br />En yüksek geri alma<br />Delta geri alma<br />İlk hızlı yeniden deneme |5<br />0 sn<br />60 sn<br />2 sn<br />yanlış |Deneme 1 - 0 sn gecikme<br />Deneme 2 - yaklaşık 2 sn gecikme<br />Deneme 3 - yaklaşık 6 sn gecikme<br />Deneme 4 - yaklaşık 14 sn gecikme<br />Deneme 5 - yaklaşık 30 sn gecikme |

## <a name="resource-ids-for-azure-services"></a>Azure hizmetleri için kaynak kimlikleri

Azure AD [kimlik doğrulamasını](./services-support-managed-identities.md) destekleyen ve Azure kaynakları için Yönetilen kimlikler ve bunlara karşılık gelen kaynak kimlikleri ile test edilmiş bir kaynak listesi için bkz. Azure hizmetleri.


## <a name="next-steps"></a>Sonraki adımlar

- Azure VM 'de Azure kaynakları için yönetilen kimlikleri etkinleştirmek üzere, [Azure Portal kullanarak BIR VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-portal-windows-vm.md)konusuna bakın.