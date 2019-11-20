---
title: C# ile özel terim listesi kullanarak metin denetimi gerçekleştirme - Content Moderator
titleSuffix: Azure Cognitive Services
description: C# için Content Moderator SDK'sını kullanarak özel terim listeleriyle metin denetleme.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 68da335875752d326ee718cade3d501623c70b49
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935951"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>İçindeki özel bir terim listesine karşı metin denetleC#

Azure Content Moderator'daki varsayılan genel terim listesi, içerik moderasyonu ihtiyaçlarının büyük bölümü için yeterlidir. Bununla birlikte, kuruluşunuza özgü terimleri elemek gerekebilir. Örneğin, daha fazla incelemek üzere rakiplerin adlarını etiketlemek isteyebilirsiniz. 

[.NET için Content Moderator SDK'sını](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) kullanarak Metin Moderasyonu API'sinde kullanılacak özel terim listeleri oluşturabilirsiniz.

Bu makalede, aşağıdaki amaçlarla .NET için Content Moderator SDK'sı'nı kullanmaya başlamanıza yardımcı olacak bilgi ve kod örnekleri sağlanır:
- Liste oluşturma.
- Listeye terimleri ekleme.
- Terimleri listedeki terimlere göre eleme.
- Listeden terim silme.
- Listeyi silme.
- Liste bilgileri düzenleme.
- Listede yapılan değişikliklerin yeni taramaya eklenmesi için dizini yenileyin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator hizmetleri için kaydolma

REST API veya SDK aracılığıyla Content Moderator hizmetlerini kullanabilmeniz için önce bir abonelik anahtarınız olması gerekir. Birini almak için [Azure portalda](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) Content Moderator hizmetine abone olabilirsiniz.

## <a name="create-your-visual-studio-project"></a>Visual Studio projenizi oluşturun

1. Çözümünüze yeni bir **Konsol uygulaması (.NET Framework)** projesi ekleyin.

1. Projeyi **TermLists** olarak adlandırın. Bu projeyi çözümün tekil başlangıç projesi olarak seçin.

### <a name="install-required-packages"></a>Gerekli paketleri yükleme

TermLists projesi için aşağıdaki NuGet paketlerini yükleyin:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Programı deyimler kullanarak güncelleştirme

Aşağıdaki `using` deyimlerini ekleyin.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Content Moderator istemcisini oluşturma

Aboneliğiniz için bir Content Moderator istemcisi oluşturmak üzere aşağıdaki kodu ekleyin. `AzureEndpoint` ve `CMSubscriptionKey` alanlarını Endpoint URL 'nizin ve abonelik anahtarınızın değerleriyle güncelleştirin. Bunları, Azure portal kaynağınızın **hızlı başlangıç** sekmesinde bulabilirsiniz.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Özel özellikler ekleme

namespace TermLists, class Program için aşağıdaki özel özellikleri ekleyin.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Terim listesi oluşturma

Terim listesini **ContentModeratorClient.ListManagementTermLists.Create** ile oluşturursunuz. **Create** için ilk parametre MIME türünü içeren bir dizedir ve "application/json" olmalıdır. Daha fazla bilgi için bkz. [API başvurusu](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). İkinci parametre, yeni terim listesi için bir ad ve açıklama içeren **Body** nesnesidir.

> [!NOTE]
> Üst sınır, her biri **10.000 terimi aşmamak** kaydıyla **5 listedir**.

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

> [!NOTE]
> Content Moderator hizmet anahtarınızın saniye başına istek (RPS) hız sınırı vardır ve bu sınırı aşarsanız SDK 429 hata kodu ile bir özel durum oluşturur. Ücretsiz katman anahtarı bir RPS'lik hız sınırına sahiptir.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Terim listesi adını ve açıklamasını güncelleştirme

Terim listesi bilgilerini **ContentModeratorClient.ListManagementTermLists.Update** ile güncelleştirirsiniz. **Update** için ilk parametre terim listesi kimliğidir. İkinci parametre MIME türüdür ve "application/json" olmalıdır. Daha fazla bilgi için bkz. [API başvurusu](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Üçüncü parametre, yeni adı ve açıklamayı içeren **Body** nesnesidir.

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Terim listesine terim ekleme

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Terim listesindeki tüm terimleri alma

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Arama dizinini yenilemek için kod ekleme

Terim listesinde değişiklikler yaptıktan sonra, metni elemek için terim listesini bir sonraki kullanışınızda bu değişiklerin de dahil edilmesi için arama dizinini yenilersiniz. Bu, masaüstünüzdeki arama altyapısının (etkinleştirildiyse) veya web arama altyapısının, yeni dosyaları veya sayfaları eklemek üzere dizinini sürekli yenilemesine benzer.

Terim listesi arama dizinini **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod** ile yenilersiniz.

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Terim listesini kullanarak metni eleme

Aşağıdaki parametreleri alan **ContentModeratorClient.TextModeration.ScreenText** ile terim listesini kullanarak metni eleyin.

- Terim listesindeki terimlerin dili.
- MIME türü; "text/html", "text/xml", "text/markdown" veya "text/plain" olabilir.
- Elenecek metin.
- Boole değeri. Metni elemeden önce otomatik olarak düzeltmek için bu alanı **true** değerine ayarlayın.
- Boole değeri. Metindeki kişisel verileri algılamak için bu alanı **doğru** olarak ayarlayın.
- Terim listesi kimliği.

Daha fazla bilgi için bkz. [API başvurusu](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** bir **Screen** nesnesi döndürür. Bu nesnenin, Content Moderator tarafından elemede algılanan tüm terimleri listeleyen bir **Terms** özelliği vardır. Eleme sırasında Content Moderator hiçbir terim algılamazsa, **Terms** özelliğinin **null** değerini alacağını unutmayın.

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    read.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Terimleri ve listeleri silme

Terim veya listeyi silmek basit bir işlemdir. Aşağıdaki görevleri gerçekleştirmek için SDK kullanırsınız:

- Terim silme. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Listeyi silmeden listedeki tüm terimleri silme. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Listeyi ve tüm içeriğini silme. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Terim silme

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Terim listesindeki tüm terimleri silme

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Terim listesini silme

namespace TermLists, class Program için aşağıdaki yöntem tanımını ekleyin.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>Main metodunu oluşturma

**TermLists** ad alanının **Program** sınıfına **Main** yöntemi tanımını ekleyin. Son olarak, **Program** sınıfını ve **TermLists** ad alanını kapatın.

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>Çıkışı görmek uygulamayı çalıştırma

Konsol çıktılarınız aşağıdakine benzer şekilde görünür:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Sonraki adımlar

Bu ve diğer .NET için Content Moderator hızlı başlangıçları için [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) ve [Visual Studio çözümünü](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) edinin ve tümleştirmeniz üzerinde çalışmaya başlayın.
