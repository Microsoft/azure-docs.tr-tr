---
title: İstek telemetrisi için veri modeli-Azure Application Insights
description: İstek telemetrisi için veri modeli Application Insights
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 7a352f4ce3528d395599a91b53031c74b0873152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320570"
---
# <a name="request-telemetry-application-insights-data-model"></a>İstek telemetrisi: Application Insights veri modeli

İstek telemetrisi öğesi ( [Application Insights](./app-insights-overview.md)), uygulamanıza yönelik bir dış istek tarafından tetiklenen mantıksal yürütme dizisini temsil eder. Her istek yürütmesi benzersiz `ID` ve `url` tüm yürütme parametrelerini içerecek şekilde tanımlanır. İstekleri mantıksal olarak gruplandırabilir `name` ve `source` Bu isteğin sayısını tanımlayabilirsiniz. Kod yürütme, `success` veya ile sonuçlanabilir `fail` `duration` . Başarılı ve başarısız yürütmeler, tarafından daha fazla gruplandırılabilir `resultCode` . Zarf düzeyinde tanımlanan istek telemetrisi için başlangıç zamanı.

İstek telemetrisi, özel ve kullanan standart genişletilebilirlik modelini `properties` destekler `measurements` .

## <a name="name"></a>Name

İsteğin adı, isteği işlemek için alınan kod yolunu temsil eder. İsteklerin daha iyi gruplanmasına izin vermek için düşük kardinalite değeri. HTTP istekleri için, gerçek değer olmadan gibi HTTP yöntemini ve URL yol şablonunu temsil eder `GET /values/{id}` `id` .

Application Insights Web SDK, "olduğu gibi" istek adını harf büyük harfe göre gönderir. Kullanıcı arabirimindeki gruplandırma, büyük/küçük harfe duyarlıdır; bu nedenle, `GET /Home/Index` `GET /home/INDEX` genellikle aynı denetleyici ve eylem yürütmeye neden olur. Bunun nedeni, genel olarak URL 'lerin [büyük/küçük harfe duyarlıdır](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). `404`Büyük harfle yazılan URL 'ler için tümünün olup olmadığını görmek isteyebilirsiniz. İstek adı koleksiyonu hakkında daha fazla bilgi için ASP.NET Web SDK tarafından [blog gönderisine](https://apmtips.com/posts/2015-02-23-request-name-and-url/)erişebilirsiniz.

Maksimum Uzunluk: 1024 karakter

## <a name="id"></a>ID

İstek çağrısı örneğinin tanımlayıcısı. İstek ve diğer telemetri öğeleri arasındaki bağıntı için kullanılır. KIMLIK, genel olarak benzersiz olmalıdır. Daha fazla bilgi için bkz. [bağıntı](./correlation.md) sayfası.

Maksimum Uzunluk: 128 karakter

## <a name="url"></a>Url

Tüm sorgu dizesi parametreleriyle birlikte istek URL 'SI.

Maksimum Uzunluk: 2048 karakter

## <a name="source"></a>Kaynak

İsteğin kaynağı. Örnekanahtarın, arayanın veya IP adresinin izleme anahtarıdır. Daha fazla bilgi için bkz. [bağıntı](./correlation.md) sayfası.

Maksimum Uzunluk: 1024 karakter

## <a name="duration"></a>Süre

Şu biçimdeki istek süresi: `DD.HH:MM:SS.MMMMMM` . Pozitif ve günlerden küçük olmalıdır `1000` . İstek telemetrisi, başlangıcı ve bitişi olan işlemi temsil ettiğinden bu alan gereklidir.

## <a name="response-code"></a>Yanıt kodu

İstek yürütmenin sonucu. HTTP istekleri için HTTP durum kodu. `HRESULT`Diğer istek türleri için değer veya özel durum türü olabilir.

Maksimum Uzunluk: 1024 karakter

## <a name="success"></a>Başarılı

Başarılı veya başarısız çağrı göstergesi. Bu alan gereklidir. Açıkça olarak ayarlanmamışsa `false` , istek başarılı olarak kabul edilir. `false`İşlem özel durum tarafından kesintiye uğrarsa veya hata sonucu kodu döndürdüğünden, bu değeri olarak ayarlayın.

Web uygulamaları için, yanıt kodu değerinden küçük veya buna eşit olduğunda isteği başarılı olarak tanımlayın Application Insights `400` `401` . Ancak, bu varsayılan eşlemenin uygulamanın anlam ile eşleşmemesi durumunda durumlar vardır. Yanıt kodu `404` , normal akışın bir parçası olabilen "kayıt yok" anlamına gelebilir. Aynı zamanda bozuk bir bağlantı olduğunu gösteriyor olabilir. Bozuk bağlantılar için daha gelişmiş mantık da uygulayabilirsiniz. Hatalı bağlantıları, yalnızca bu bağlantılar aynı sitede bulunduğunda, URL başvuran ' i çözümleyerek hatalara göre işaretleyebilirsiniz. Ya da şirketin mobil uygulamasından erişildiğinde bunları hatalara göre işaretleyin. Benzer şekilde `301` , `302` yeniden yönlendirmeyi desteklemeyen istemciden erişildiğinde hata olduğunu gösterir.

Kısmen kabul edilen içerik `206` , genel bir isteğin bir başarısızlığını gösterebilir. Örneğin, Application Insights uç noktası tek bir istek olarak telemetri öğelerini toplu olarak alır. `206`Toplu işteki bazı öğelerin başarıyla işlenmediği zaman döndürür. Artış oranı, `206` Araştırılması gereken bir sorunu gösterir. Benzer mantık `207` , başarıyı ayrı yanıt kodlarının en kötü olabileceği çok durum için geçerlidir.

İstek sonucu kodu ve durum kodu hakkında daha fazla bilgi için [blog gönderisine](https://apmtips.com/posts/2016-12-03-request-success-and-response-code/)erişebilirsiniz.

## <a name="custom-properties"></a>Özel Özellikler

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Özel ölçümler

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Özel istek telemetrisi yazma](./api-custom-events-metrics.md#trackrequest)
- Application Insights türleri ve veri modeli için [veri modeli](data-model.md) 'ne bakın.
- ASP.NET Core uygulamayı Application Insights ile [yapılandırmayı](./asp-net.md) öğrenin.
- Application Insights tarafından desteklenen [platformları](./platforms.md) inceleyin.

