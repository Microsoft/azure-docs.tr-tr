---
title: Azure Data Factory 'de Azure Işlevi etkinliği
description: Azure işlevi etkinliğini kullanarak bir Azure Işlevini Data Factory işlem hattında çalıştırma hakkında bilgi edinin
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 781c5a579fa0cd0383e95b79df1f81f74008111c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679967"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory 'de Azure Işlevi etkinliği

Azure Işlevi etkinliği, [Azure işlevlerini](../azure-functions/functions-overview.md) bir Data Factory işlem hattında çalıştırmanızı sağlar. Bir Azure Işlevi çalıştırmak için, bir bağlı hizmet bağlantısı ve çalıştırmayı planladığınız Azure Işlevini belirten bir etkinlik oluşturmanız gerekir.

Bu özelliğin sekiz dakikalık bir girişi ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure Işlevi bağlı hizmeti

Azure işlevinin dönüş türü geçerli bir `JObject`olmalıdır. ( [Jarray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) 'in bir `JObject`*değil* olduğunu aklınızda bulundurun.) `JObject` dışındaki herhangi bir dönüş türü başarısız olur ve Kullanıcı hata *yanıtı içeriğini başlatır geçerli bir JObject değildir*.

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| type   | Type özelliği: **AzureFunction** olarak ayarlanmalıdır | evet |
| function app url | Azure İşlev Uygulaması URL 'SI. Biçim `https://<accountname>.azurewebsites.net`. Bu URL, Azure portal İşlev Uygulaması görüntülenirken **URL** bölümündeki değerdir  | evet |
| function key | Azure Işlevi için erişim anahtarı. İlgili işlevin **Yönet** bölümüne tıklayın ve **işlev anahtarını** ya da **ana bilgisayar anahtarını**kopyalayın. Buradan daha fazla bilgi edinebilirsiniz: [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | evet |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Işlevi etkinliği

| **Özellik**  | **Açıklama** | **İzin verilen değerler** | **Gerekli** |
| --- | --- | --- | --- |
| ad  | İşlem hattındaki etkinliğin adı  | Dize | evet |
| type  | Etkinliğin türü ' AzureFunctionActivity ' | Dize | evet |
| linked service | Karşılık gelen Azure İşlev Uygulaması için Azure Işlevi bağlı hizmeti  | Bağlı hizmet başvurusu | evet |
| function name  | Bu etkinliğin çağırdığı Azure İşlev Uygulaması işlevin adı | Dize | evet |
| method  | İşlev çağrısı için REST API yöntemi | Dize desteklenen türler: "GET", "POST", "PUT"   | evet |
| üst bilgi  | İsteğe gönderilen üst bilgiler. Örneğin, bir istek için dili ve türü ayarlamak için: "üstbilgiler": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"} | Dize (veya dize resultType 'ı olan ifade) | Hayır |
| bölümü  | işlevin API metoduna isteğiyle birlikte gönderilen gövde  | Dize (veya dize resultType 'ı olan ifade) veya nesne.   | PUT/POST yöntemleri için gereklidir |
|   |   |   | |

İstek yükü [şeması](control-flow-web-activity.md#request-payload-schema) bölümünde istek yükünün şemasına bakın.

## <a name="routing-and-queries"></a>Yönlendirme ve sorgular

Azure Işlevi etkinliği **yönlendirmeyi**destekler. Örneğin, Azure işlevinizde uç nokta `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`varsa, Azure Işlevi etkinliğinde kullanılacak `functionName` `<functionName>/<value>`. Çalışma zamanında istenen `functionName` sağlamak için bu işlevi parametreleştirebilirsiniz.

Azure Işlevi etkinliği **sorguları**da destekler. Bir sorgu `functionName`bir parçası olarak eklenmelidir. Örneğin, işlev adı `HttpTriggerCSharp` ve dahil etmek istediğiniz sorgu `name=hello`, Azure Işlevi etkinliğinde `functionName` `HttpTriggerCSharp?name=hello`gibi oluşturabilirsiniz. Değerin çalışma zamanında belirlenebilmesi için bu işlev parametreleştirilenebilir.

## <a name="timeout-and-long-running-functions"></a>Zaman aşımı ve uzun süre çalışan işlevler

Ayarlarda yapılandırdığınız `functionTimeout` ayarından bağımsız olarak 230 saniye sonra Azure Işlevleri zaman aşımına uğrar. Daha fazla bilgi için [bu makaleye](../azure-functions/functions-versions.md#timeout) bakın. Bu davranışı geçici olarak çözmek için zaman uyumsuz bir model izleyin veya Dayanıklı İşlevler kullanın. Dayanıklı İşlevler avantajı kendi durum izleme mekanizmasını sunduklarında, kendi uygulamanızı uygulamanız gerekmez.

[Bu makaledeki](../azure-functions/durable/durable-functions-overview.md)dayanıklı işlevler hakkında daha fazla bilgi edinin. [Bu örnek](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)gibi farklı bir URI ile yanıt döndüren dayanıklı işlevi çağırmak Için bir Azure işlevi etkinliği ayarlayabilirsiniz. `statusQueryGetUri`, işlev çalışırken HTTP durumu 202 döndürdüğünden, bir Web etkinliği kullanarak işlevin durumunu yoklayabilmeniz gerekir. `url` alan `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`ayarlanmış bir Web etkinliği ayarlamanız yeterlidir. Dayanıklı Işlev tamamlandığında, işlevin çıktısı Web etkinliğinin çıktısı olur.


## <a name="sample"></a>Örnek

Bir tar dosyasının içeriğini [buraya](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)ayıklamak Için Azure işlevi kullanan bir Data Factory örneğini bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Data Factory işlem [hatları ve Azure Data Factory etkinliklerinde](concepts-pipelines-activities.md)etkinlikler hakkında daha fazla bilgi edinin.
