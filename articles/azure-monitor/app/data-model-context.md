---
title: Azure Application Insights Telemetri veri modeli-telemetri bağlamı | Microsoft Docs
description: Application Insights telemetri bağlamı veri modeli
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0351f4fe65745242da58d3c3fb2f9fbe5c722d06
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677452"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetri bağlamı: Application Insights veri modeli

Her telemetri öğesinin türü kesin belirlenmiş bir bağlam alanı olabilir. Her alan, belirli bir izleme senaryosuna izin vermez. Özel veya uygulamaya özel bağlamsal bilgileri depolamak için özel özellikler koleksiyonunu kullanın.


## <a name="application-version"></a>Uygulama sürümü

Uygulama bağlamı alanlarındaki bilgiler, her zaman telemetri gönderen uygulama hakkında bir uygulamadır. Uygulama sürümü, uygulama davranışında eğilim değişikliklerinin çözümlenmesi ve dağıtımlarıyla bağıntısı için kullanılır.

En fazla uzunluk: 1024


## <a name="client-ip-address"></a>İstemci IP adresi

İstemci cihazının IP adresi. IPv4 ve IPv6 desteklenir. Telemetri bir hizmetten gönderildiğinde, konum bağlamı hizmette işlemi başlatan kullanıcı hakkında olur. Application Insights coğrafi konum bilgilerini istemci IP 'sinden ayıklayın ve ardından kesin. Bu nedenle, istemci IP 'si, Son Kullanıcı tanımlı bilgiler olarak kullanılamaz. 

En fazla uzunluk: 46


## <a name="device-type"></a>Cihaz türü

Başlangıçta bu alan, uygulamanın son kullanıcısının kullandığı cihazın türünü belirtmek için kullanılmıştır. Günümüzde, birincil olarak JavaScript telemetrisini ' Browser ' cihaz türü ile sunucu tarafı telemetrisinden ' PC ' cihaz türüyle ayırt etmek için kullanılır.

En fazla uzunluk: 64


## <a name="operation-id"></a>İşlem kimliği

Kök işleminin benzersiz tanımlayıcısı. Bu tanımlayıcı, telemetrinin birden çok bileşen arasında gruplandırmesine izin verir. Ayrıntılar için bkz. [telemetri bağıntısı](../../azure-monitor/app/correlation.md) . İşlem kimliği bir istek ya da sayfa görünümü tarafından oluşturulur. Diğer tüm telemetri, bu alanı içeren istek veya sayfa görünümü için değer olarak ayarlar. 

En fazla uzunluk: 128


## <a name="parent-operation-id"></a>Üst işlem KIMLIĞI

Telemetri öğesinin anlık üst öğesinin benzersiz tanımlayıcısı. Ayrıntılar için bkz. [telemetri bağıntısı](../../azure-monitor/app/correlation.md) .

En fazla uzunluk: 128


## <a name="operation-name"></a>İşlem adı

İşlemin adı (Grup). İşlem adı bir istek ya da sayfa görünümü tarafından oluşturulur. Diğer tüm telemetri öğeleri, bu alanı içeren istek veya sayfa görünümü için değere ayarlar. İşlem adı bir işlem grubuna yönelik tüm telemetri öğelerini bulmak için kullanılır (örneğin, ' GET Home/Index '). Bu bağlam özelliği, "Bu sayfada oluşan tipik özel durumlar nelerdir." gibi soruları yanıtlamak için kullanılır.

En fazla uzunluk: 1024


## <a name="synthetic-source-of-the-operation"></a>İşlemin yapay kaynağı

Yapay kaynak adı. Uygulamadan gelen bazı telemetri yapay trafiği temsil edebilir. Web Gezgini, Web sitesi, site kullanılabilirliği testleri veya Application Insights SDK 'nın kendisi gibi tanılama kitaplıklarından izlemeleri dizine alıyor olabilir.

En fazla uzunluk: 1024


## <a name="session-id"></a>Oturum kimliği

Oturum KIMLIĞI-kullanıcının uygulamayla etkileşimi örneği. Oturum bağlam alanlarındaki bilgiler her zaman son kullanıcı hakkında. Telemetri bir hizmetten gönderildiğinde, oturum bağlamı hizmette işlemi başlatan kullanıcı hakkında olur.

En fazla uzunluk: 64


## <a name="anonymous-user-id"></a>Anonim Kullanıcı kimliği

Anonim Kullanıcı kimliği. Uygulamanın son kullanıcısını temsil eder. Telemetri bir hizmetten gönderildiğinde, Kullanıcı bağlamı hizmette işlemi başlatan kullanıcı hakkında olur.

[Örnekleme](../../azure-monitor/app/sampling.md) , toplanan Telemetriyi en aza indirme tekniklerinden biridir. Örnekleme algoritması, tüm bağıntılı telemetrinin içinde veya dışında örnek olarak çalışır. Örnekleme puanı oluşturma için anonim kullanıcı kimliği kullanılır. Bu nedenle anonim kullanıcı kimliği yeterince rastgele bir değer olmalıdır. 

Kullanıcı adını depolamak için anonim kullanıcı kimliği kullanılması alanın kötüye kullanılması. Kimliği doğrulanmış kullanıcı kimliği kullan.

En fazla uzunluk: 128


## <a name="authenticated-user-id"></a>Kimliği doğrulanmış kullanıcı kimliği

Kimliği doğrulanmış kullanıcı kimliği. Anonim Kullanıcı kimliğinin tersi olan bu alan, kolay bir ada sahip kullanıcıyı temsil eder. PII bilgileri, çoğu SDK tarafından varsayılan olarak toplanmaz.

En fazla uzunluk: 1024


## <a name="account-id"></a>Hesap kimliği

Çok kiracılı uygulamalarda bu, kullanıcının işlem gördüğü hesap KIMLIĞI veya addır. Örnek, Azure portal veya blog adı blog platformu için abonelik KIMLIĞI olabilir.

En fazla uzunluk: 1024


## <a name="cloud-role"></a>Bulut rolü

Uygulamanın bir parçası olduğu rolün adı. Doğrudan Azure 'daki rol adıyla eşlenir. , Tek bir uygulamanın parçası olan mikro hizmetleri ayırt etmek için de kullanılabilir.

En fazla uzunluk: 256


## <a name="cloud-role-instance"></a>Bulut rolü örneği

Uygulamanın çalıştığı örneğin adı. Şirket içi için bilgisayar adı, Azure için örnek adı.

En fazla uzunluk: 256


## <a name="internal-sdk-version"></a>Dahili: SDK sürümü

SDK sürümü. Bilgi için bkz. https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification.

En fazla uzunluk: 64


## <a name="internal-node-name"></a>İç: düğüm adı

Bu alan, fatura amaçları için kullanılan düğüm adını temsil eder. Düğümlerin standart algılamayı geçersiz kılmak için bunu kullanın.

En fazla uzunluk: 256


## <a name="next-steps"></a>Sonraki adımlar

- [Telemetrinin nasıl genişletileceğini ve filtreleneceğini](../../azure-monitor/app/api-filtering-sampling.md)öğrenin.
- Application Insights türleri ve veri modeli için [veri modeli](data-model.md) 'ne bakın.
- Standart bağlam özellikleri koleksiyonu [yapılandırmasını](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)inceleyin.
