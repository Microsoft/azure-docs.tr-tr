---
title: Azure Data Factory 'de Azure Işlevi etkinliği
description: Azure işlevi etkinliğini kullanarak bir Azure Işlevini Data Factory işlem hattında çalıştırma hakkında bilgi edinin
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 13f00907737a99bc5dcd8c21d660ce83aa681908
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783804"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory 'de Azure Işlevi etkinliği
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Azure Işlevi etkinliği, [Azure işlevlerini](../azure-functions/functions-overview.md) bir Data Factory işlem hattında çalıştırmanızı sağlar. Bir Azure Işlevi çalıştırmak için, bir bağlı hizmet bağlantısı ve çalıştırmayı planladığınız Azure Işlevini belirten bir etkinlik oluşturmanız gerekir.

Bu özelliğin sekiz dakikalık bir girişi ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure Işlevi bağlı hizmeti

Azure işlevinin dönüş türü geçerli bir olmalıdır `JObject` . ( [Jarray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) öğesinin bir *değil* olduğunu aklınızda bulundurun `JObject` .) Diğer herhangi bir dönüş türü `JObject` başarısız olur ve Kullanıcı hata *yanıtı içeriğini başlatır geçerli bir JObject değildir*.

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| tür   | Type özelliği: **AzureFunction** olarak ayarlanmalıdır | evet |
| işlev uygulaması URL 'si | Azure İşlev Uygulaması URL 'SI. Biçim `https://<accountname>.azurewebsites.net` . Bu URL, Azure portal İşlev Uygulaması görüntülenirken **URL** bölümündeki değerdir  | evet |
| işlev anahtarı | Azure Işlevi için erişim anahtarı. İlgili işlevin **Yönet** bölümüne tıklayın ve **işlev anahtarını** ya da **ana bilgisayar anahtarını** kopyalayın. Buradan daha fazla bilgi edinebilirsiniz: [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | evet |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Işlevi etkinliği

| **Özellik**  | **Açıklama** | **İzin verilen değerler** | **Gerekli** |
| --- | --- | --- | --- |
| name  | İşlem hattındaki etkinliğin adı  | Dize | evet |
| tür  | Etkinliğin türü ' AzureFunctionActivity ' | Dize | evet |
| bağlı hizmet | Karşılık gelen Azure İşlev Uygulaması için Azure Işlevi bağlı hizmeti  | Bağlı hizmet başvurusu | evet |
| işlev adı  | Bu etkinliğin çağırdığı Azure İşlev Uygulaması işlevin adı | Dize | evet |
| method  | İşlev çağrısı için REST API yöntemi | Dize desteklenen türler: "GET", "POST", "PUT"   | evet |
| üst bilgi  | İsteğe gönderilen üst bilgiler. Örneğin, bir istek için dili ve türü ayarlamak için: "üstbilgiler": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"} | Dize (veya dize resultType 'ı olan ifade) | No |
| body  | işlevin API metoduna isteğiyle birlikte gönderilen gövde  | Dize (veya dize resultType 'ı olan ifade) veya nesne.   | PUT/POST yöntemleri için gereklidir |
|   |   |   | |

İstek [yükü şeması](control-flow-web-activity.md#request-payload-schema) 'nda istek yükü şeması bölümüne bakın.

## <a name="routing-and-queries"></a>Yönlendirme ve sorgular

Azure Işlevi etkinliği **yönlendirmeyi** destekler. Örneğin, Azure işlevinizin uç noktası varsa,  `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` `functionName` Azure işlevi etkinliğinde kullanmak için `<functionName>/<value>` . Çalışma zamanında istenen işlemi sağlamak için bu işlevi parametreleştirebilirsiniz `functionName` .

Azure Işlevi etkinliği **sorguları** da destekler. Bir sorgu öğesinin bir parçası olarak eklenmelidir `functionName` . Örneğin, işlev adı `HttpTriggerCSharp` ve dahil etmek istediğiniz sorgu ise, `name=hello` bunu `functionName` olarak Azure işlevi etkinliğinde oluşturabilirsiniz `HttpTriggerCSharp?name=hello` . Değerin çalışma zamanında belirlenebilmesi için bu işlev parametreleştirilenebilir.

## <a name="timeout-and-long-running-functions"></a>Zaman aşımı ve uzun süre çalışan işlevler

Ayarlarda yapılandırdığınız ayardan bağımsız olarak, 230 saniye sonra Azure Işlevleri zaman aşımına uğrar `functionTimeout` . Daha fazla bilgi için [bu makaleye](../azure-functions/functions-versions.md#timeout) bakın. Bu davranışı geçici olarak çözmek için zaman uyumsuz bir model izleyin veya Dayanıklı İşlevler kullanın. Dayanıklı İşlevler avantajı kendi durum izleme mekanizmasını sunduklarında, kendi uygulamanızı uygulamanız gerekmez.

[Bu makaledeki](../azure-functions/durable/durable-functions-overview.md)dayanıklı işlevler hakkında daha fazla bilgi edinin. [Bu örnek](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)gibi farklı bir URI ile yanıt döndüren dayanıklı işlevi çağırmak Için bir Azure işlevi etkinliği ayarlayabilirsiniz. , `statusQueryGetUri` İşlev çalışırken http durumu 202 ' i döndürdüğünden, bir Web etkinliği kullanarak işlevin durumunu yoklayabilmeniz gerekir. Yalnızca alanı olarak ayarlanmış bir Web etkinliği ayarlamanız yeterlidir `url` `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` . Dayanıklı Işlev tamamlandığında, işlevin çıktısı Web etkinliğinin çıktısı olur.


## <a name="sample"></a>Örnek

Bir tar dosyasının içeriğini [buraya](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)ayıklamak Için Azure işlevi kullanan bir Data Factory örneğini bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Data Factory işlem [hatları ve Azure Data Factory etkinliklerinde](concepts-pipelines-activities.md)etkinlikler hakkında daha fazla bilgi edinin.
