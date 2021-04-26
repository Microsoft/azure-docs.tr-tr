---
title: Azure Nesnelerin İnterneti 'ye giriş (IoT)
description: IoT kullanımını göstermeye yardımcı olan örnekler de dahil olmak üzere Azure IoT ve IoT hizmetlerinin temellerini açıklayan giriş.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f1053062302c9a00cf49cee1cd8de5ca6652b745
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167711"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Azure Nesnelerin İnterneti (IoT) nedir?

Azure Nesnelerin İnterneti (IoT), Microsoft tarafından yönetilen ve milyarlarca IoT varlığını bağlayan, izleyen ve denetleyen bulut hizmetlerinden oluşan bir koleksiyondur. Daha basit koşullarda, bir IoT çözümü, bulutta barındırılan bir veya daha fazla arka uç hizmetleriyle iletişim kuran bir veya daha fazla IoT cihazlarından oluşur. 

## <a name="iot-devices"></a>IoT cihazları

IoT cihazı genellikle internet 'e bağlanmak için WiFi kullanan sensörlerin eklendiği bir devre panosundan yapılır. Örnek:

* Uzaktaki bir yağ pompa üzerinde basınç algılayıcısı.
* Hava koşuldaki bir birimdeki sıcaklık ve nem sensörleri.
* Asansör bir hızlandırma.
* Bir odada bulunan varlık algılayıcıları.

Çözümünüzü derlemek için farklı üreticilerin sunduğu çok sayıda cihaz mevcuttur. Azure IoT Hub ile çalışmak üzere sertifikalı cihazların listesi için bkz. [IoT Için Azure Sertifikalı cihaz kataloğu](https://devicecatalog.azure.com). Prototip oluşturma için, bir [Mxyonga IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) veya bir [Raspberry PI](https://www.raspberrypi.org/)gibi cihazları kullanabilirsiniz. Devkit, sıcaklık, basınç, nem ve bir jroscope, ivometer ve manyetik tometer için yerleşik sensörlerden sahiptir. Raspberry PI, birçok farklı algılayıcı türü eklemenize olanak sağlar. 

Microsoft, cihazlarınızda çalışan uygulamaları oluşturmak için kullanabileceğiniz açık kaynaklı [cihaz SDK 'ları](../iot-hub/iot-hub-devguide-sdks.md) sağlar. Bu SDK 'lar IoT çözümlerinizin geliştirilmesini [basitleştirir ve hızlandırır](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) .

## <a name="communication"></a>İletişim

Genellikle IoT cihazları sensörlerden bulutta arka uç hizmetlerine telemetri gönderir. Ancak, cihazlarınız için bir arka uç hizmeti gönderme gibi diğer iletişim türleri mümkündür. Cihazdan buluta ve buluttan cihaza iletişimin bazı örnekleri aşağıda verilmiştir:

* Mobil soğutma kamyonu 5 dakikada bir IoT Hub sıcaklığını gönderir. 

* Arka uç hizmeti bir sorunu tanılamanıza yardımcı olmak için telemetri gönderdiği sıklığı değiştirmek üzere cihaza bir komut gönderir. 

* Bir cihaz, algılayıcılarından okunan değerlere göre uyarılar gönderir. Örneğin, bir kimyasal tesiste toplu yeniden aktöri izleyen bir cihaz, sıcaklık belirli bir değeri aştığında bir uyarı gönderir.

* Cihazlarınız, insan işleçlerine göre görüntülenmek üzere bir panoda görüntülenecek bilgileri gönderir. Örneğin, bir iyileşme içindeki bir denetim odası, her bir kanalda sıcaklık, basınç ve akış birimlerini gösterebilir ve bu da operatörlerin, Tesis izlemesini sağlar. 

[IoT cihaz SDK 'ları](../iot-hub/iot-hub-devguide-sdks.md) ve IoT Hub, http, MQTT ve AMQP gibi yaygın [iletişim protokollerini](../iot-hub/iot-hub-devguide-protocols.md) destekler.

IoT cihazlarının, tarayıcılar ve mobil uygulamalar gibi diğer istemcilerle karşılaştırıldığında farklı özellikleri vardır. Cihaz SDK 'Ları, cihazları güvenli ve güvenilir bir şekilde arka uç hizmetinize bağlama sorunlarını gidermenize yardımcı olur.  IoT cihazları şu özelliklere sahiptir:

* Genellikle insan kullanıcısı bulunmayan katıştırılmış sistemlerdir (bir telefonun aksine).
* Fiziksel erişimin pahalı olduğu uzak konumlarda dağıtılabilir.
* Yalnızca çözüm arka ucu aracılığıyla erişilebilir.
* Sınırlı güç ve işleme kaynaklarına sahip olabilir.
* Aralıklı, yavaş veya pahalı bir ağ bağlantısına sahip olabilir.
* Mülkiyete ait, özel veya sektöre özgü uygulama protokolleri kullanması gerekebilir.

## <a name="back-end-services"></a>Arka uç hizmetleri 

Bir IoT çözümünde, arka uç hizmeti şöyle bir işlevsellik sağlar:

* Cihazlarınızdan ölçeğe göre telemetri alma ve bu verileri işleme ve depolamayı belirleme.
* Gerçek zamanlı veya olgu sonrasında öngörüleri sağlamak için Telemetriyi analiz etme.
* Buluttan belirli bir cihaza komut gönderme. 
* Cihazları hazırlama ve altyapınızı hangi cihazların bağlayabileceğini denetleme.
* Cihazlarınızın durumunu denetleme ve etkinliklerini izleme.
* Cihazlarınızda yüklü olan bellenimi yönetme.

Örneğin, yağ pompalama istasyonu için bir uzaktan izleme çözümünde, bulut arka ucu, anormal davranışları belirlemek için pompalara 'den telemetri kullanır. Arka uç hizmeti bir anomali belirlediğinde, düzeltici bir eylem gerçekleştirmek için otomatik olarak cihaza bir komut gönderebilir. Bu işlem, cihaz ile bulut arasında çözüm verimliliğini önemli ölçüde artıran otomatik bir geri bildirim döngüsü oluşturur.

## <a name="azure-iot-examples"></a>Azure IoT örnekleri

Kuruluşların Azure IoT kullanımıyla ilgili gerçek yaşam örnekleri için bkz. [IoT Için Microsoft Teknik olay incelemeleri](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

IoT mimarisinin ayrıntılı incelemesi için bkz. [Microsoft Azure IoT Başvuru Mimarisi](/azure/architecture/reference-architectures/iot).

## <a name="next-steps"></a>Sonraki adımlar

Bazı gerçek iş durumlarında ve kullanılan mimaride, [Microsoft Azure IoT teknik olay incelemeleri](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)' ne bakın.

IoT DevKit ile deneyebileceğiniz bazı örnek projelerde [IoT devkit proje kataloğuna](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)bakın. 

Farklı hizmetlerin ve nasıl kullanıldıkları hakkında daha kapsamlı bir açıklama için bkz. [Azure IoT Hizmetleri ve teknolojileri](iot-services-and-technologies.md).