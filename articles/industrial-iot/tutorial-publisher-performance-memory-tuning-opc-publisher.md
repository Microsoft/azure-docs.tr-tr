---
title: Microsoft OPC yayımcı performansı ve belleği ayarlama
description: Bu öğreticide OPC yayımcısının performansını ve belleğini ayarlamayı öğreneceksiniz.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787893"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Öğretici: OPC yayımcısı performansını ve belleğini ayarlama

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Performansı ayarla
> * İleti akışını bellek kaynaklarına ayarlama

OPC yayımcısı 'nı üretim kurulumlarında çalıştırırken, ağ performansı gereksinimleri (verimlilik ve gecikme süresi) ve bellek kaynakları göz önünde bulundurulmalıdır. OPC yayımcısı, bu gereksinimleri karşılamaya yardımcı olmak için aşağıdaki komut satırı parametrelerini kullanıma sunar:

* İleti sırası kapasitesi (sürüm `mq` 2,5 ve altı için sürüm 2,6 ' de kullanılamaz, sürüm `om` 2,7 için)
* IoT Hub gönderme aralığı ( `si` )
* IoT Hub ileti boyutu ( `ms` )

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>IoT Hub gönderme aralığını ve IoT Hub ileti boyutunu ayarlama

`mq/om`Parametresi, iç ileti sırasının kapasitesinin üst sınırını denetler. Bu kuyruk, IoT Hub gönderilmeden önce tüm iletileri arabelleğe alır. Sıranın varsayılan boyutu, OPC yayımcısı sürüm 2,5 ve 2,7 IoT Hub 4000 altı için 2 MB 'a kadar (örneğin, IoT Hub ileti boyutu ayarı 256 KB ise, sıranın boyutu ise 1 GB 'a kadar olur). OPC yayımcısı, yeterince hızlı IoT Hub ileti gönderebilmedikçe, bu kuyruktaki öğelerin sayısı artar. Bu durum test çalıştırmaları sırasında olursa aşağıdakilerden biri veya her ikisi de hafifletmek için yapılabilir:

* IoT Hub gönderme aralığını azalt ( `si` )

* IoT Hub ileti boyutunu arttırın ( `ms` Bu değer 256 KB olarak ayarlanabilir)

`si`Ve parametreleri ayarlanmış olsa bile sıra `ms` büyümeye devam ederse, en fazla sıra kapasitesine ulaşıldığında iletiler kaybedilir. Bunun nedeni, hem `si` hem de `ms` parametresinin fiziksel sınırlara sahip olması ve OPC yayımcısı ile IoT Hub arasındaki Internet bağlantısının, belirli bir senaryoya gönderilmesi gereken ileti sayısı için yeterince hızlı olmamasından kaynaklanır. Bu durumda, yalnızca birkaç, paralel OPC yayımcısı ayarlamak yardımcı olur. `mq/om`Parametresi, OPC yayımcısı tarafından bellek tüketimine ilişkin en büyük etkiye de sahiptir. 

`si`Parametresi OPC yayımcısının belirtilen aralıkta IoT Hub ileti göndermesini zorlar. 256 KB 'lık verilerin en büyük IoT Hub ileti boyutu (sıfırlamaya göre gönderme aralığı tetikleniyor) veya belirtilen Aralık süresi geçtiğinde bir ileti gönderilir.

`ms`Parametresi, IoT Hub gönderilen iletilerin toplu işlem yapılmasını sağlar. Çoğu ağ kurulumu 'nda, IoT Hub için tek bir ileti göndermenin gecikmesi, yükü aktarmak için gereken zamana kıyasla yüksek. İleti yalnızca IoT Hub tarafından işlendikten sonra onaylandığından, bu durum genellikle hizmet kalitesi (QoS) gereksinimlerinden kaynaklanır. Bu nedenle, verilerin IoT Hub gelmesi için bir gecikme kabul edilebilir ise OPC yayımcısı, parametreyi 0 olarak ayarlayarak 256 KB 'lık en büyük ileti boyutunu kullanacak şekilde yapılandırılmalıdır `ms` . OPC yayımcısını kullanmanın en uygun maliyetli yoludur.

Varsayılan yapılandırma, verileri her 10 saniyede bir IoT Hub ( `si=10` ) veya 256 KB IoT Hub ileti verisi kullanılabilir olduğunda () gönderir `ms=0` . Bu, en fazla 10 saniyelik bir gecikme ekler, ancak büyük ileti boyutu nedeniyle verilerin kaybedilmesi için düşük bir olasılıktır. `monitored item notifications enqueue failure`OPC yayımcısı sürüm 2,5 ve altındaki ve `messages lost` OPC yayımcısı sürüm 2,7 ' deki ölçüm, kaç ileti kaybolduğunu gösterir.

Hem `si` hem de `ms` parametreleri 0 olarak ayarlandığında OPC yayımcısı, veriler kullanılabilir duruma geldiğinde IoT Hub bir ileti gönderir. Bu, 200 baytlık bir ortalama IoT Hub ileti boyutuna neden olur. Ancak, bu yapılandırmanın avantajı OPC yayımcısının verileri gecikmeli olmayan bağlantılı varlıktan göndermesi. Kayıp ileti sayısı büyük miktarda verinin yayımlanması gereken kullanım durumları için yüksek olacaktır, bu nedenle bu senaryolar için bu önerilmez.

OPC yayımcısının performansını ölçmek için `di` parametresi, performans ölçümlerini belirtilen aralıktaki (saniye cinsinden) izleme günlüğüne yazdırmak için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
OPC yayımcısının performansını ve belleğini ayarlamayı öğrendiğinize göre, daha fazla kaynak için OPC yayımcı GitHub deposuna bakabilirsiniz:

> [!div class="nextstepaction"]
> [OPC yayımcısı GitHub deposu](https://github.com/Azure/Industrial-IoT)