---
title: Bing Varlık Arama API'si kullanarak özel beceri örneği
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de AI zenginlikli bir dizin oluşturma işlem hattına eşlenmiş özel bir beceriyle Bing Varlık Arama hizmetini kullanmayı gösterir.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2994c55b39d30ff16a0ca135e93a116784feb201
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113809"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Örnek: Bing Varlık Arama API'si kullanarak özel bir yetenek oluşturma

Bu örnekte, bir Web API 'SI özel yeteneği oluşturmayı öğrenin. Bu beceri konumları, genel rakamları ve kuruluşları kabul eder ve bunların açıklamalarını döndürür. Örnek, [Bing varlık arama API'si](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) özel beceri arabirimini uygulayan şekilde kaydırmak Için bir [Azure işlevi](https://azure.microsoft.com/services/functions/) kullanır.

## <a name="prerequisites"></a>Önkoşullar

+ Özel bir yeteneğin uygulaması gereken giriş/çıkış arabirimini bilmiyorsanız [özel beceri arabirimi](cognitive-search-custom-skill-interface.md) makalesi hakkında bilgi edinin.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Azure geliştirme iş yükü dahil olmak üzere [Visual Studio 2019](https://www.visualstudio.com/vs/) veya üstünü yükler.

## <a name="create-an-azure-function"></a>Azure İşlevi oluşturma

Bu örnek, bir Web API 'sini barındırmak için bir Azure Işlevi kullansa da gerekli değildir.  Bilişsel [bir beceri için arabirim gereksinimlerini](cognitive-search-custom-skill-interface.md)karşıladığınızdan, aldığınız yaklaşım imsel olur. Ancak Azure Işlevleri, özel bir beceri oluşturmayı kolaylaştırır.

### <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

1. Visual Studio 'da Dosya menüsünden **yeni** > **Proje** ' yi seçin.

1. Yeni proje iletişim kutusunda, **yüklü**' i seçin, **Visual C#**  > **Cloud**' ı genişletin, **Azure işlevleri**' ni seçin, projeniz için bir ad yazın ve **Tamam**' ı seçin. İşlev uygulaması adının bir C# ad alanı olarak geçerli olması gerekir, bu nedenle alt çizgi, kısa çizgi veya alfasayısal olmayan diğer karakterlerden hiçbirini kullanmayın.

1. **Azure işlevleri v2 (.NET Core)** seçeneğini belirleyin. Bunu sürüm 1 ile de yapabilirsiniz, ancak aşağıda yazılan kod v2 şablonunu temel alır.

1. **Http tetikleyicisi** olacak tür seçin

1. Depolama hesabı için, bu işlev için herhangi bir depolamaya ihtiyaç duymayabilmeniz için **hiçbiri**' ni seçebilirsiniz.

1. İşlev projesi ve HTTP ile tetiklenen işlev oluşturmak için **Tamam** ' ı seçin.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Bing Varlık Arama hizmetini çağırmak için kodu değiştirin

Visual Studio bir proje oluşturur ve bu projenin içinde seçili işlev türü için ortak kod içeren bir sınıf bulunur. Metottaki *FunctionName* özniteliği işlevin adını ayarlar. *HttpTrigger* özniteliği, işlevin bir HTTP isteği tarafından tetiklenip tetiklenmediğini belirtir.

Şimdi, *function1.cs* dosyasının tüm içeriğini aşağıdaki kodla değiştirin:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// AI enrichment pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Bing varlık arama API 'sine kaydolurken aldığınız anahtara göre `key` sabitine kendi *anahtar* değerini girdiğinizden emin olun.

Bu örnek, kolay bir şekilde tek bir dosyadaki tüm gerekli kodu içerir. [Power becerileri deposunda](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)aynı becerinin biraz daha yapısal bir sürümünü bulabilirsiniz.

Kuşkusuz `Function1.cs` dosyayı `BingEntitySearch.cs`olarak yeniden adlandırabilirsiniz.

## <a name="test-the-function-from-visual-studio"></a>İşlevi Visual Studio 'dan test etme

Program ve test işlevi davranışlarını çalıştırmak için **F5** tuşuna basın. Bu durumda, iki varlık aramak için aşağıdaki işlevi kullanacağız. Aşağıda gösterildiği gibi bir çağrı vermek için Postman veya Fiddler kullanın:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>İstek gövdesi
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>Yanıt
Aşağıdaki örneğe benzer bir yanıt görmeniz gerekir:

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>İşlevi Azure 'da yayımlayın

İşlev davranışından memnun kaldığınızda, bunu yayımlayabilirsiniz.

1. **Çözüm Gezgini**'nde projeye sağ tıklayın ve **Yayımla**'yı seçin. Yeni > **Yayımla** **Oluştur** seçeneğini belirleyin.

1. Zaten Visual Studio 'Yu Azure hesabınıza bağladıysanız **Hesap Ekle...** seçeneğini belirleyin.

1. Ekrandaki istemleri izleyin. App Service, Azure aboneliği, kaynak grubu, barındırma planı ve kullanmak istediğiniz depolama hesabı için benzersiz bir ad belirtmeniz istenir. Henüz yoksa, yeni bir kaynak grubu, yeni bir barındırma planı ve bir depolama hesabı oluşturabilirsiniz. İşiniz bittiğinde **Oluştur** ' u seçin.

1. Dağıtım tamamlandıktan sonra, site URL 'sine dikkat edin. Bu, Azure 'daki işlev uygulamanızın adresidir. 

1. [Azure Portal](https://portal.azure.com), kaynak grubuna gidin ve yayımladığınız `EntitySearch` işlevi için arama yapın. **Yönet** bölümünün altında ana bilgisayar anahtarlarını görmeniz gerekir. *Varsayılan* ana bilgisayar anahtarı için **Kopyala** simgesini seçin.  

## <a name="test-the-function-in-azure"></a>İşlevi Azure 'da test etme

Artık varsayılan ana bilgisayar anahtarına sahip olduğunuza göre, işlevinizi şu şekilde test edin:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>İstek Gövdesi
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

Bu örnek, işlevi yerel ortamda çalıştırırken daha önce gördüğünüz sonucu üretmelidir.

## <a name="connect-to-your-pipeline"></a>İşlem hattınızla bağlantı kurmak
Artık yeni bir özel beceriye sahip olduğunuza göre, Beceri 'e ekleyebilirsiniz. Aşağıdaki örnekte, belgedeki kuruluşlara açıklama ekleme beceriye nasıl çağrılacağını gösterilmektedir (Bu, konumlar ve kişiler üzerinde de çalışacak şekilde genişletilebilir). `[your-entity-search-app-name]`, uygulamanızın adıyla değiştirin.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Burada, yerleşik [varlık tanıma beceriye](cognitive-search-skill-entity-recognition.md) beceri içinde mevcut olacak ve bu belgeyi kuruluşların listesiyle zenginleştirmemiz için sayılıyız. Başvuru için, ihtiyaç duyduğumuz verileri oluştururken yeterli olabilecek bir varlık ayıklama yeteneği yapılandırması aşağıda verilmiştir:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Sonraki adımlar
Tebrikler! İlk özel becerinizi oluşturdunuz. Artık kendi özel işlevselliklerinizi eklemek için aynı kalıbı izleyebilirsiniz. Daha fazla bilgi edinmek için aşağıdaki bağlantılara tıklayın.

+ [Güç becerileri: özel yeteneklerin bir deposu](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Bir AI zenginleştirme ardışık düzenine özel yetenek ekleme](cognitive-search-custom-skill-interface.md)
+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Beceri oluşturma (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Zenginleştirilmiş alanları eşleme](cognitive-search-output-field-mapping.md)
