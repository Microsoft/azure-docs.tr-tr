---
title: Azure API Management kullanarak sunucusuz bir API için Openapı tanımı oluşturma
description: Azure’da diğer uygulama ve hizmetlerin işlevinize çağrı yapmasına imkan tanıyan bir OpenAPI tanımı oluşturun.
keywords: OpenAPI, Swagger, bulut uygulamaları, bulut hizmetleri,
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 54a4c6eba094231e8e73cdef87b911dfba20f657
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533548"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Azure API Management kullanarak sunucusuz bir API için Openapı tanımı oluşturma

REST API 'Ler genellikle bir Openapı tanımı kullanılarak açıklanır. Bu tanım, bir API’de hangi işlemlerin kullanılabildiğinin yanı sıra API için istek ve yanıt verilerinin nasıl yapılandırılması gerektiğiyle ilgili bilgileri içerir.

Bu öğreticide, bir rüzgar türbini için acil onarımın uygun maliyetli olup olmadığını belirleyen bir işlev oluşturursunuz. Daha sonra, işlevin diğer uygulamalardan ve hizmetlerden çağrılabilmesi için [Azure API Management](../api-management/api-management-key-concepts.md) kullanarak işlev uygulaması Için bir openapı tanımı oluşturursunuz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure’da işlev oluşturma
> * Azure API Management kullanarak bir Openapı tanımı oluşturma
> * İşleve çağrı yaparak tanımı test etme
> * Openapı tanımını indirin

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

İşlevlerinizin yürütülmesini barındıran bir işlev uygulamasına sahip olmanız gerekir. İşlev uygulaması, kaynakların daha kolay yönetilmesi, dağıtılması, ölçeklendirilmesi ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanıza olanak tanır.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>İşlevi oluşturma

Bu öğretici, iki parametre alan HTTP ile tetiklenen bir işlev kullanır:

* Bir türbin onarımı için saat cinsinden tahmini süre.
* Kilowatts cinsinden türbin kapasitesi. 

Daha sonra, işlev tarafından onarım maliyetinin ne olacağı ve türbinin 24 saatlik bir dönemde ne kadar gelir kazandırabileceği hesaplanır. [Azure Portal](https://portal.azure.com)http ile tetiklenen işlevi oluşturmak için.

1. İşlev uygulamanızı genişletin ve **İşlevler**'in yanındaki **+** düğmesini seçin. **Portalda** > **devam et**' i seçin.

1. **Daha fazla şablon seçin...** , ardından son ' u seçin **ve şablonları görüntüleyin**

1. HTTP tetikleyicisi ' ni seçin `TurbineRepair` , işlev `Function` **adını**yazın, **[kimlik doğrulama düzeyi](functions-bindings-http-webhook.md#http-auth)** için öğesini seçin ve ardından **Oluştur**' u seçin.  

    ![Openapı için HTTP işlevi oluşturma](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Run. CSX C# betik dosyasının içeriğini aşağıdaki kodla değiştirin ve ardından **Kaydet**' i seçin:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Bu işlev kodu, acil onarımın uygun maliyetli olup olmadığının yanı sıra türbinin temsil ettiği gelir fırsatını ve türbin onarımının maliyetini gösteren `Yes` veya `No` şeklinde bir ileti döndürür.

1. İşlevi test etmek için en sağdaki **Test** seçeneğine tıklayarak test sekmesini genişletin. **İstek gövdesi** için aşağıdaki değeri girip **Çalıştır**’a tıklayın.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![İşlevi Azure portalında test etme](media/functions-openapi-definition/test-function.png)

    Yanıtın gövdesinde aşağıdaki değer döndürülür.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Acil onarımların maliyet açısından uygunluğunu belirleyen bir işleviniz oldu. Sonra, işlev uygulaması için bir Openapı tanımı oluşturursunuz.

## <a name="generate-the-openapi-definition"></a>OpenAPI tanımını oluşturma

Artık OpenAPI tanımını oluşturmaya hazırsınız.

1. İşlev uygulamasını seçin, ardından **platform özellikleri**' nde **API Management** ' yi seçin ve **API Management**altında **Yeni oluştur** ' u seçin.

    ![Platform özelliklerinde API Management seçin](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Görüntünün altındaki tabloda belirtilen API Management ayarlarını kullanın.

    ![Yeni API Management hizmeti oluştur](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Ayar      | Önerilen değer  | Açıklama                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Genel olarak benzersiz bir ad | İşlev uygulamanızın adı temel alınarak bir ad oluşturulur. |
    | **Abonelik** | Aboneliğiniz | Bu yeni kaynağın altında oluşturulduğu abonelik. |  
    | **[Kaynak Grubu](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Sizin için ayarlanmış olması gereken işlev uygulamanız ile aynı kaynak. |
    | **Location** | Batı ABD | Batı ABD konumunu seçin. |
    | **Kuruluş adı** | Contoso | Geliştirici portalında kullanılan kuruluşun adı ve e-posta bildirimleri için. |
    | **Yönetici e-postası** | e-postanız | API Management 'den sistem bildirimleri alan e-posta. |
    | **Fiyatlandırma katmanı** | Tüketim (Önizleme) | Tüketim katmanı önizleme aşamasındadır ve tüm bölgelerde kullanılamaz. Tüm fiyatlandırma ayrıntıları için [API Management fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/api-management/) bakın |

1. API Management örneğini oluşturmak için **Oluştur** ' u seçin ve bu işlem birkaç dakika sürebilir.

1. Günlükleri işlev uygulamasıyla aynı yere göndermek için **Application Insights etkinleştir** ' i seçin, ardından kalan Varsayılanları kabul edin ve **bağlantı API 'si**' ni seçin.

1. **Içeri aktarma Azure işlevleri** , **Turbinerepair** işlevi vurgulanmış şekilde açılır. Devam etmek için **Seç ' i** seçin.

    ![Azure Işlevlerini API Management içine aktarın](media/functions-openapi-definition/import-function-openapi.png)

1. **İşlev uygulaması oluştur** sayfasında, Varsayılanları kabul edin ve **Oluştur** ' u seçin.

    ![İşlev Uygulamasından oluştur](media/functions-openapi-definition/create-function-openapi.png)

API artık işlev için oluşturulmuştur.

## <a name="test-the-api"></a>API'yi test etme

Openapı tanımını kullanmadan önce API 'nin çalıştığını doğrulamanız gerekir.

1. İşlevinizin **Test** sekmesinde, **Post** işlemi ' ni seçin.

1. **Saatlerin** ve **kapasitenin** değerlerini girin

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. **Gönder**' e tıklayın, ardından HTTP yanıtını görüntüleyin.

    ![Test işlevi API 'SI](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Openapı tanımını indirin

API 'niz beklendiği gibi çalışıyorsa, Openapı tanımını indirebilirsiniz.

1. Sayfanın üst kısmında **Openapı tanımını indir** ' i seçin.
   
   ![OpenAPI tanımını indirin](media/functions-openapi-definition/download-definition.png)

2. İndirilen JSON dosyasını açın ve tanımı gözden geçirin.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

İşlevlerinizin bir Openapı tanımını oluşturmak için API Management tümleştirme kullandınız. Tanımı artık portalda API Management düzenleyebilirsiniz. Ayrıca [API Management hakkında daha fazla bilgi](../api-management/api-management-key-concepts.md)edinebilirsiniz.

> [!div class="nextstepaction"]
> [API Management 'de Openapı tanımını düzenleme](../api-management/edit-api.md)
