---
title: "Öğretici: REST API 'Lerini kullanma"
description: Bu öğreticide, kataloğunuzun içeriğine erişmek için Azure purview REST API 'Lerinin nasıl kullanılacağı açıklanmaktadır.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98951251"
---
# <a name="tutorial-use-the-rest-apis"></a>Öğretici: REST API 'Lerini kullanma

Bu öğreticide, Azure purview REST API 'Lerini nasıl kullanacağınızı öğreneceksiniz. Azure purview kataloğuna veri göndermek isteyen herkes, kataloğu otomatik bir işlemin parçası olarak dahil eder veya katalog üzerinde kendi kullanıcı deneyimlerini oluşturmak için REST API 'Lerini kullanabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Hizmet sorumlusu (uygulama) oluşturun.
> * Kataloğunuzu hizmet sorumlusuna (uygulama) güvenmek üzere yapılandırın.
> * REST API 'Leri belgelerini görüntüleyin.
> * REST API 'Lerini kullanmak için gerekli değerleri toplayın.
> * REST API 'Leri çağırmak için Postman istemcisini kullanın.
> * REST API 'Leri çağırmak için bir .NET istemcisi oluşturun.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="prerequisites"></a>Önkoşullar

* Başlamak için, mevcut bir Azure purview hesabına sahip olmanız gerekir. Bir kataloğunuz yoksa, [Azure purview hesabı oluşturmaya yönelik hızlı başlangıç](create-catalog-portal.md)bölümüne bakın.

* Kataloğunuza veri eklemeniz gerekiyorsa, [Başlangıç kitini çalıştırmak ve verileri taramak için öğreticiye](tutorial-scan-data.md)bakın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-service-principal-application"></a>Hizmet sorumlusu oluşturma (uygulama)

REST API istemcisinin kataloğa erişmesi için, istemcinin bir hizmet sorumlusu (uygulama) ve kataloğun tanıdığı ve güvenmek üzere yapılandırıldığı bir kimlik olması gerekir. Kataloğa REST API çağrılar yaptığınızda, hizmet sorumlusunun kimliğini kullanır.

Var olan hizmet sorumlularını kullanan müşteriler (uygulama kimlikleri) yüksek oranda hata yaşadı. Bu nedenle, API 'Leri çağırmak için yeni bir hizmet sorumlusu oluşturmanızı öneririz.

Yeni bir hizmet sorumlusu oluşturmak için:

1. [Azure Portal](https://portal.azure.com), araması yapın ve **Azure Active Directory** seçin.
1. **Azure Active Directory** sayfasından sol bölmeden **uygulama kayıtları** ' u seçin.
1. **Yeni kayıt** seçeneğini belirleyin.
1. **Uygulama kaydetme** sayfasında:
    1. Uygulama için bir **ad** girin (hizmet asıl adı).
    1. **Yalnızca bu kurumsal dizindeki hesapları (_&lt; kiracınızın adı &gt;_ -tek kiracı)** seçin.
    1. **Yeniden yönlendirme URI 'si (isteğe bağlı)** için **Web** ' i seçin ve bir değer girin. Bu değerin geçerli bir URI olması gerekmez.
    1. **Kaydet**’i seçin.
1. Yeni hizmet sorumlusu sayfasında, daha sonra kaydetmek için **görünen ad** ve **uygulama (istemci) kimliğinin** değerlerini kopyalayın.

   Uygulama KIMLIĞI, `client_id` örnek kodda değeridir.

Hizmet sorumlusu 'nı (uygulama) kullanmak için, parolasını almanız gerekir. Aşağıdaki adımları uygulayın:

1. Azure portal, **Azure Active Directory** arayıp seçin ve ardından sol bölmeden **uygulama kayıtları** ' yı seçin.
1. Listeden hizmet sorumlunuzu (uygulama) seçin.
1. Sol bölmedeki **sertifikalar & parolaları** ' nı seçin.
1. **Yeni istemci gizli dizisi**’ni seçin.
1. **İstemci parolası Ekle** sayfasında, bir **Açıklama** girin, **süre sonu ' nun altında** bir sona erme süresi seçin ve ardından **Ekle**' yi seçin.

   **İstemci gizli** dizileri sayfasında, yeni gizliliğin **değer** sütunundaki dize parolanız olur. Bu değeri kaydedin.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="İstemci parolasını gösteren ekran görüntüsü.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Kataloğunuzu hizmet sorumlusuna güvenecek şekilde yapılandırma (uygulama)

Azure purview 'ı yeni hizmet sorumlusuna güvenmek üzere yapılandırmak için:

1. Purview hesabınıza gidin

1. **Purview hesabı** sayfasında, sekme **ERIŞIM denetimi (IAM)** seçeneğini belirleyin.

1. **+ Ekle** 'ye tıklayın

1. **Rol ataması Ekle** ' yi seçin

1. **Purview veri seçicisi** içindeki rol türü için

    > [!Note]
    > Azure purview veri kataloğu izinleri hakkında daha fazla bilgi için bkz. [Katalog izinleri](catalog-permissions.md). Örneğin, taramayı tetiklemek için izninizin olması gerekiyorsa, rol türü **purview veri kaynağı Yöneticisi** olmalıdır.

1. Ata 'nın varsayılan, **Kullanıcı, Grup veya hizmet sorumlusunu** bırakma **erişimi** için

1. **Seç** için, atamak istediğiniz previosly oluşturulan hizmet sorumlusunun adını girin ve ardından sonuçlar bölmesinde adına tıklayın.

1. **Kaydet** 'e tıklayın

Hizmet sorumlusunu, bir uygulama Yöneticisi olarak yapılandırdık, bu da kataloğa içerik göndermesini sağlar.

## <a name="view-the-rest-apis-documentation"></a>REST API 'Leri belgelerini görüntüleme

API Swagger belgelerini görüntülemek için [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip)indirin, dosyalarını ayıklayın ve index.html 'yi açın.

Azure purview 'ın sağladığı gelişmiş arama/öneri API 'SI hakkında daha fazla bilgi edinmek istiyorsanız REST API arama filtresi belgelerine bakın. Oto Rest tarafından oluşturulan istemci şu anda özelleştirilmiş arama parametrelerini desteklememektedir. Geçici çözüm olarak, koddaki filtre sınıflarını API çağrı parametreleri olarak tanımlamak için arama filtresi belgesini izleyin. index.html belgesinde bu API 'Ler örnekleri bulunur.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>REST API 'Lerini kullanmak için gerekli değerleri toplayın

Aşağıdaki değerleri bulun ve kaydedin:

* Kiracı Kimliği:
  * [Azure Portal](https://portal.azure.com), araması yapın ve **Azure Active Directory** seçin.
  * Sol bölmedeki **Yönet** bölümünde **Özellikler**' i seçin, **Kiracı kimliğini** bulun ve sonra değerini kaydetmek için **Panoya Kopyala** simgesini seçin.
* Atlas uç noktası:
  * Azure portal [Azure takip hesapları sayfasında](https://aka.ms/purviewportal) , listeden Azure purview hesabınızı bulun ve seçin.
  * **Özellikler**' i seçin, **Atlas uç noktasını** bulun ve sonra değerini kaydetmek için **Panoya Kopyala** simgesini seçin. Dizeyi daha sonra kullandığınızda dizenin *https://* kısmını kaldırın.
* Hesap adı:
  * Atlas uç nokta dizesinden kataloğunuzun adını ayıklayın. Örneğin, Atlas uç noktanız ise, `https://ThisIsMyCatalog.catalog.purview.azure.com` hesap adınız olur `ThisIsMyCatalog` .

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>REST API 'Leri çağırmak için Postman istemcisini kullanın

1. [Postman istemcisini](https://www.getpostman.com/)yükleme.
1. İstemciden **Içeri aktar**' ı seçin ve [ üzerindeTest.postman_collection.js](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json)kullanın.
1. **Koleksiyonlar**' ı seçin ve ardından **Test**' i seçin.
1. **Belirteci al**' ı seçin:
    1. POST 'un yanındaki URL 'de, *&lt; kiracı kimliğinizi &gt;* ÖNCEKI bölümde kopyaladığınız kiracı kimliğiyle değiştirin.
    1. **Gövde** sekmesini seçin ve önceki adımdan gelen yoldaki ve gövdesindeki yer tutucuları değiştirin.

       **Gönder**' i seçtikten sonra, yanıt gövdesi ad *access_token* ve tırnak içine alınmış bir dize değeri de dahIl olmak üzere bir JSON yapısı içerir. 
    1. Sonraki adımda kullanmak için taşıyıcı belirteç değerini (tırnak işaretleri olmadan) kopyalayın.

1. **/V2/Types/Typedefs** öğesini seçin:
    1. Yoldaki yer tutucuyu Atlas uç nokta değeri ile değiştirin. Bu değer, Ibiza portalındaki Katalog örneğine gidip genel bakış ' a tıklayarak elde edilebilir. 

       Taşıyıcı belirteci ilk adımdan ayarlanır (veya bunu "yetkilendirme" sekmesinde el ile kopyalayabilirsiniz).

    1. Yanıtı almak için **Gönder** ' i seçin.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>REST API 'Leri çağırmak için bir .NET istemcisi oluşturma

### <a name="install-autorest"></a>Oto Rest 'i yükler



1. [Node.js'ı yükler ](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md).
1. PowerShell’i açın ve aşağıdaki komutu çalıştırın:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>rest-api-specs.zip indirin ve istemciyi oluşturun

1. [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) indirin ve dosyalarını ayıklayın.
1. REST API-Özellikler ayıklanan klasöründen PowerShell 'de aşağıdaki komutu çalıştırın:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   Bu işlemin çıktısı, REST-API-Özellikler klasöründe bir PurviewCatalogClient ve CSharp alt klasörü oluşturur.

### <a name="create-a-sample-net-console-application"></a>Örnek .NET konsol uygulaması oluşturma

1. Visual Studio 2019 ' i açın. Bu yönergeler ücretsiz topluluk sürümü ile test edilmiştir.
1. **Yeni proje oluştur** sayfasında C# ' de bir **konsol uygulaması (.NET Core)** projesi oluşturun.
1. Belirtilen [örnek kodu](#sample-code-for-the-console-application)kopyalayıp yapıştırın.
1. *AccountName*, *serviceprincipalıd*, *Servicesprincipalkey* ve *tenantıd* değerlerini daha önce topladığınız değerlerle değiştirin.
1. Visual Studio projesinde **oluşturulan** adlı bir klasörü eklemek için **Çözüm Gezgini** kullanın.
1. Daha önce oluşturduğunuz rest-api-specs\PurviewCatalogClient\CSharp klasörünü \Generated klasörüne kopyalayın. Dosya Gezgini 'ni veya kopyalama işlemi için komut satırı istemi 'ni kullanın, bu, Visual Studio 'Nun dosyaları projeye otomatik olarak eklemesini tetikler.
1. **Çözüm Gezgini**, projeye sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.
1. **Tarayıcı** sekmesini seçin. **Microsoft. Rest. ClientRuntime** bulun ve seçin.
1. Sürümün en az 2.3.21 olduğundan emin olun ve ardından **Install**' ı seçin.
1. Uygulamayı derleyin ve çalıştırın.

Örnek kod, katalogda kaç tane tür tanımları öğesinin olduğunu döndürür ve rol atamalarının nasıl işleneceğini gösterir. Ayrıntılar için bkz `DoRoleAssignmentOperations()` . örnek kodda. Proje hakkında daha fazla bilgi için bkz. [Proje kurulumu](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md).

### <a name="sample-code-for-the-console-application"></a>Konsol uygulaması için örnek kod

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Veri kaynaklarını yönetme](manage-data-sources.md)
