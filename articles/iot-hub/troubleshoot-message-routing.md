---
title: Azure IoT ileti yönlendirme sorunlarını giderme
description: Azure IoT ileti yönlendirme için sorun giderme gerçekleştirme
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 3abff5645775d724042acba3ee2461c7cad771a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149673"
---
# <a name="troubleshooting-message-routing"></a>İleti yönlendirme sorunlarını giderme

Bu makalede, IoT Hub [ileti yönlendirme](iot-hub-devguide-messages-d2c.md)için sık karşılaşılan sorunlar ve çözümleme için izleme ve sorun giderme kılavuzu sağlanmaktadır.

## <a name="monitoring-message-routing"></a>İzleme iletisi yönlendirme

[İleti yönlendirme ve uç noktalarla ilgili IoT Hub ölçümlerini](monitor-iot-hub-reference.md#routing-metrics) izlemenizi öneririz. böylece, gönderilen iletilere bir genel bakış sunulmaktadır. Ayrıca, özel işleme için Azure Izleyici günlüklerine, Event Hubs veya Azure Storage 'a [IoT Hub kaynak günlüklerinde **yollar**](monitor-iot-hub-reference.md#routes) için işlem göndermek üzere bir tanılama ayarı oluşturabilirsiniz. Ölçümleri, kaynak günlüklerini ve tanılama ayarlarını kullanma hakkında daha fazla bilgi için bkz. [Monitor IoT Hub](monitor-iot-hub.md). Öğretici için bkz. [IoT Hub ile ölçümleri ve kaynak günlüklerini ayarlama ve kullanma](tutorial-use-metrics-and-diags.md).

Ayrıca, yolların hiçbirinde sorguyla eşleşmeyen iletileri sürdürmek istiyorsanız [geri dönüş yolunu](iot-hub-devguide-messages-d2c.md#fallback-route) etkinleştirmenizi öneririz. Bunlar, yapılandırılan bekletme gün miktarı için [yerleşik uç noktada](iot-hub-devguide-messages-read-builtin.md) tutulabilirler.

## <a name="top-issues"></a>En önemli sorunlar

İleti yönlendirme ile gözlemlendiği en yaygın sorunlar aşağıda verilmiştir. Sorun gidermeye başlamak için, ayrıntılı adımlar için soruna tıklayın.

* [Cihazlarım 'daki iletiler beklendiği gibi yönlendirilmiyor](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Yerleşik Event Hubs uç noktasında iletileri almayı aniden durdurdum](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>Cihazlarım 'daki iletiler beklendiği gibi yönlendirilmiyor

Bu sorunu gidermek için aşağıdakileri çözümleyin.

#### <a name="the-routing-metrics-for-this-endpoint"></a>Bu uç nokta için yönlendirme ölçümleri

Yönlendirme ile [ilgili tüm IoT Hub ölçümlerinin](monitor-iot-hub-reference.md#routing-metrics) ön eki, ön ekine *sahiptir.* Sorunların temel nedenini belirlemek için birden fazla ölçüden bilgi birleştirebilirsiniz. Örneğin, bir uç noktaya teslim edilen veya yolların hiçbirinde sorgu eşleşmediği zaman ve geri dönüş rotası devre dışı bırakıldığında bırakılan ileti sayısını belirlemek için ölçüm **yönlendirme teslim girişimlerini** kullanın. İleti tesliminin sürekli veya artan olduğunu gözlemlemek için **yönlendirme gecikmesi** ölçüsünü denetleyin. Büyümekte olan bir gecikme, belirli bir uç nokta ile ilgili bir sorun olduğunu gösterebilir ve [uç noktanın sistem durumunu](#the-health-of-the-endpoint)kontrol etmenizi öneririz. Bu yönlendirme ölçümlerinde Ayrıca, uç nokta türü, belirli bir uç nokta adı ve iletinin neden teslim edilmediğini belirten bir neden gibi ölçüm hakkında ayrıntılar sağlayan [Boyutlar](monitor-iot-hub-reference.md#metric-dimensions) vardır.

#### <a name="the-resource-logs-for-any-operational-issues"></a>İşlem sorunları için kaynak günlükleri

Yönlendirme ve uç nokta [işlemleri](#operation-names) hakkında daha fazla bilgi edinmek için [ **yollar** kaynak günlüklerini](monitor-iot-hub-reference.md#routes) gözlemleyin veya sorunları daha fazla anlamak için hataları ve ilgili [hata kodunu](#common-error-codes) inceleyin. Örneğin, günlükteki **RouteEvaluationError** işlem adı ileti biçimindeki bir sorun nedeniyle yolun değerlendirilmediğini belirtir. Sorunu azaltmak için, belirli [işlem adları](#operation-names) için belirtilen ipuçlarını kullanın. Bir olay bir hata olarak günlüğe kaydedildiğinde, bu işlem, değerlendirmenin neden başarısız olduğuna ilişkin daha fazla bilgi de sağlar. Örneğin, işlem adı **EndpointUnhealthy** ise, 403004 [hata kodu](#common-error-codes) , uç noktanın boş olduğunu gösterir.

#### <a name="the-health-of-the-endpoint"></a>Uç noktanın sistem durumu

Uç noktaların [sistem durumunu](iot-hub-devguide-endpoints.md#custom-endpoints) almak Için [uç nokta durumunu Al](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) REST API kullanın. *Uç nokta durumunu Al* API 'si Ayrıca, bir iletinin uç noktaya başarıyla gönderildiği son [bilinen hata](#last-known-errors-for-iot-hub-routing-endpoints), bilinen son hata zamanı ve bu uç nokta için bir gönderme girişiminin en son gönderilme zamanı hakkında bilgiler sağlar. [Bilinen son bilinen hata](#last-known-errors-for-iot-hub-routing-endpoints)için sunulan olası risk azaltma ' yı kullanın.

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Yerleşik uç noktada iletileri almayı aniden durdurdum

Bu sorunu gidermek için aşağıdakileri çözümleyin.

#### <a name="was-a-new-route-created"></a>Yeni bir rota oluşturuldu mu?

Bir yol oluşturulduktan sonra, bu uç nokta için bir yol oluşturulmadığı takdirde veriler yerleşik uç noktaya akar. Yeni bir yol eklenirse, iletilerin yerleşik uç noktaya akışa devam ettiğinden emin olmak için, *Olaylar* uç noktasına bir yol yapılandırın. 

#### <a name="was-the-fallback-route-disabled"></a>Geri dönüş rotası devre dışı bırakıldı mi?

Geri dönüş yolu, var olan yolların herhangi birine (iletiler/olaylar) [Event Hubs](../event-hubs/index.yml)ile uyumlu olan herhangi bir mevcut [Event Hubs](iot-hub-devguide-messages-read-builtin.md) rotadaki sorgu koşullarını karşılamayan tüm iletileri gönderir. İleti yönlendirme açıksa, geri dönüş yolu özelliğini etkinleştirebilirsiniz. Yerleşik uç noktaya bir yol yoksa ve bir geri dönüş yolu etkinleştirilirse, yalnızca rotalardaki sorgu koşullarına uymamaları, yerleşik uç noktaya gönderilir. Ayrıca, mevcut tüm yollar silinirse, tüm verileri yerleşik uç noktada almak için geri dönüş yolunun etkinleştirilmesi gerekir.

Azure portal >Ileti yönlendirme dikey penceresinde geri dönüş yolunu etkinleştirebilir/devre dışı bırakabilirsiniz. Ayrıca, geri dönüş yolu için özel bir uç nokta kullanmak üzere [Fallbackrouteproperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) için Azure Resource Manager de kullanabilirsiniz.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>IoT Hub yönlendirme uç noktaları için bilinen son hatalar

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-resource-logs"></a>Kaynak günlüklerini rotalar

Aşağıda, [rotalar kaynak günlüklerinde](monitor-iot-hub-reference.md#routes)günlüğe kaydedilen işlem adları ve hata kodları verilmiştir.

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>İşlem adları

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Genel hata kodları

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla yardıma ihtiyacınız varsa, [Microsoft Q&A ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
