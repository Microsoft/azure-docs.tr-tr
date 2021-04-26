---
title: Event Grid sorunlarını giderme
description: Bu makale Azure Event Grid sorunları gidermeye yönelik farklı yollar sağlar
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: d30b8464de90474ad74853cc423de700b41226a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720568"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Azure Event Grid sorunlarını giderme
Bu makale Azure Event Grid sorunları gidermenize yardımcı olacak bilgiler sağlar. 

## <a name="diagnostic-logs"></a>Tanılama günlükleri
Yayımlama ve teslim hatası günlüklerini yakalamak ve görüntülemek için Event Grid konular veya etki alanları için tanılama ayarlarını etkinleştirin. Daha fazla bilgi için bkz. [tanılama günlükleri](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Ölçümler
Event Grid konuları ve abonelikleri için ölçümleri görüntüleyebilir ve bunlar üzerinde uyarı oluşturabilirsiniz. Daha fazla bilgi için bkz. [Event Grid ölçümleri](monitor-event-delivery.md).

## <a name="alerts"></a>Uyarılar
Azure Event Grid ölçümleri ve etkinlik günlüğü işlemlerinde uyarı oluşturma. Daha fazla bilgi için bkz. [Event Grid ölçümleri ve etkinlik günlükleri hakkında uyarılar](set-alerts.md).

## <a name="subscription-validation-issues"></a>Abonelik doğrulama sorunları
Olay aboneliği oluşturma sırasında, belirtilen uç noktanın doğrulanmadığını belirten bir hata iletisi alabilirsiniz. Abonelik doğrulama sorunlarını gidermek için bkz. [Event Grid abonelik doğrulamaları sorunlarını giderme](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Ağ bağlantısı sorunları
İstemci uygulamalarının bir Event Grid konu/etki alanına bağlanamamasının çeşitli nedenleri vardır. Karşılaşabileceğiniz bağlantı sorunları kalıcı veya geçici olabilir. Bu sorunları nasıl giderebileceğinizi öğrenmek için bkz. [bağlantı sorunlarını giderme](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Hata kodları
400, 409 ve 403 gibi hata kodlarıyla hata iletileri alırsanız bkz. [sorun giderme Event Grid hataları](troubleshoot-errors.md). 

## <a name="distributed-tracing-net"></a>Dağıtılmış izleme (.NET)
Event Grid .NET kitaplığı, izlemeyi dağıtmayı destekler. İzleme dağıtımı sırasında [Cloudevents belirtiminin](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) yönergelerine bağlı kalmak için, kitaplık, `traceparent` `tracestate` Dağıtılmış Izleme etkinleştirildiğinde bir öğesinin [extensionattributes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization#L126) öğesini ayarlar `CloudEvent` . Uygulamanızda dağıtılmış izlemenin nasıl etkinleştirileceği hakkında daha fazla bilgi edinmek için Azure SDK [Dağıtılmış izleme belgelerine](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)göz atın.

### <a name="sample"></a>Örnek
[Satır sayacı örneğine](/samples/azure/azure-sdk-for-net/line-counter/)bakın. Bu örnek uygulama, ASP.NET Core tümleştirme, dağıtılmış izleme ve barındırılan hizmetlerle birlikte depolama, Event Hubs ve Event Grid istemcilerinin kullanımını gösterir. Kullanıcıların dosya adını içeren Event Hubs olayını tetikleyen bir blob 'a dosya yüklemesine olanak tanır. Event Hubs Işlemcisi olayı alır ve ardından uygulama blobu indirir ve dosyadaki satır sayısını sayar. Uygulama, satır sayısını içeren bir sayfanın bağlantısını görüntüler. Bağlantıya tıklandığında, dosyanın adını içeren bir CloudEvent Event Grid kullanılarak yayımlanır.

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla yardıma ihtiyacınız varsa sorununuzu [Stack Overflow forumuna](https://stackoverflow.com/questions/tagged/azure-eventgrid) gönderin veya bir [destek bileti](https://azure.microsoft.com/support/options/)açın. 
