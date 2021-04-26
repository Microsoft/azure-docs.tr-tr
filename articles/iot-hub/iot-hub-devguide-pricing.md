---
title: Azure IoT Hub fiyatlandırmasını anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-kullanım örnekleri dahil olmak üzere ölçüm ve fiyatlandırmanın IoT Hub nasıl çalıştığı hakkında bilgi.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5b67f505a71d2af7950422a86ab45ef8aebb3bf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056445"
---
# <a name="azure-iot-hub-pricing-information"></a>Azure IoT Hub fiyatlandırma bilgileri

[Azure IoT Hub fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-hub) , IoT Hub Için farklı SKU 'ların ve fiyatlandırmayla ilgili genel bilgileri sağlar. Bu makalede çeşitli IoT Hub işlevleri IoT Hub tarafından ileti olarak nasıl ölçülmüş ilişkin ek ayrıntılar yer almaktadır.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>İşlem başına ücretler

| İşlem | Fatura bilgileri | 
| --------- | ------------------- |
| Kimlik kayıt defteri işlemleri <br/> (oluşturma, alma, listeleme, güncelleştirme, silme) | Ücretlendirilmedi. |
| Cihazdan buluta iletiler | Başarıyla gönderilen iletiler, IoT Hub giriş sırasında 4 KB 'lık öbeklere göre ücretlendirilir. Örneğin, 6 KB 'lik bir ileti, 2 ileti olarak ücretlendirilir. |
| Buluttan cihaza iletiler | Başarıyla gönderilen iletiler 4 KB 'lık parçalar halinde ücretlendirilir. Örneğin, 6 KB 'lik bir ileti, 2 ileti olarak ücretlendirilir. |
| Dosya karşıya yüklemeleri | Azure depolama 'ya dosya aktarımı IoT Hub tarafından tarifeli değildir. Dosya aktarımı başlatma ve tamamlama iletileri, 4 KB 'lik artışlarla ileti ölçümlü olarak ücretlendirilir. Örneğin, 10 MB 'lık bir dosyanın aktarılması, Azure depolama maliyetine ek olarak iki ileti olarak ücretlendirilir. |
| Doğrudan yöntemler | Başarılı Yöntem istekleri 4 KB 'lık öbeklere göre ücretlendirilir ve yanıtlar ek iletiler olarak 4 KB 'lık parçalar halinde ücretlendirilir. Bağlantısı kesilen cihazlara yönelik istekler, 4 KB 'lık öbeklerdeki iletiler olarak ücretlendirilir. Örneğin, cihazdan gövdesi olmayan bir Yanıt ile sonuçlanan 4 KB 'lık gövdeye sahip bir yöntem iki ileti olarak ücretlendirilir. Cihazdan 1 KB Yanıt ile sonuçlanan 6 KB 'lık bir gövdeye sahip bir yöntem, istek için iki ileti ve yanıt için başka bir ileti olarak ücretlendirilir. |
| Cihaz ve modül ikizi okumaları | Cihazdan veya modülden ikizi okumaları ve çözüm arka ucu, 4 KB 'lık öbeklerdeki iletiler olarak ücretlendirilir. Örneğin, 8 KB 'lik ikizi okumak 2 ileti olarak ücretlendirilir. |
| Cihaz ve modül ikizi güncelleştirmeleri (Etiketler ve Özellikler) | Cihazdan veya modülden ikizi güncelleştirmeleri ve çözüm arka ucu, 4 KB 'lık öbeklerdeki iletiler olarak ücretlendirilir. Örneğin, 12 KB 'lik bir ikizi okumak 3 ileti olarak ücretlendirilir. |
| Cihaz ve modül ikizi sorguları | Sorgular, 4 KB 'lık öbeklerdeki sonuç boyutuna bağlı olarak ileti olarak ücretlendirilir. |
| İş işlemleri <br/> (oluşturma, güncelleştirme, listeleme, silme) | Ücretlendirilmedi. |
| Cihaz başına iş işlemleri | İşler (ikizi güncelleştirmeleri ve yöntemler gibi) işlemleri normal olarak ücretlendirilir. Örneğin, 1 KB istek ve boş gövde yanıtları ile 1000 Yöntem çağrısı sonucu olan bir iş, 1000 ileti üzerinden ücretlendirilir. |
| Canlı tut iletileri | AMQP veya MQTT protokollerini kullanırken, bağlantıyı kurmak için değiş tokuş edilen iletiler ve anlaşmede değiştirilen mesajlar ücretlendirilmez. |

> [!NOTE]
> Tüm Boyutlar, yük boyutunu bayt cinsinden dikkate alarak hesaplanır (protokol çerçeveleme yok sayılır). Özellikleri ve gövdesi olan iletilerde, boyut, protokol belirsiz bir şekilde hesaplanır. Daha fazla bilgi için [IoT Hub ileti biçimi](iot-hub-devguide-messages-construct.md)bölümüne bakın.

## <a name="example-1"></a>Örnek #1

Bir cihaz, IoT Hub için dakika başına 1 1 KB 'lik cihazdan buluta ileti gönderir ve sonra Azure Stream Analytics tarafından okunabilir. Çözüm arka ucu, belirli bir eylemi tetiklemek için her 10 dakikada bir bir yöntemi (512 baytlık yük ile) çağırır. Cihaz, 200 baytlık bir sonuçla bir yönteme yanıt verir.

Cihaz şunları kullanır:

* Bir ileti * 60 dakika * 24 saat = her gün cihazdan buluta iletiler için 1440 ileti.
* İki istek ve bir saatte 6 kez Yanıt * 24 saat = 288 ileti.

Bu hesaplama günde toplam 1728 ileti verir.

## <a name="example-2"></a>Örnek #2

Bir cihaz her saat 1 100 KB 'lik cihazdan buluta ileti gönderir. Ayrıca, her dört saatte bir 1 KB 'lik yük ile cihaz ikizi güncelleştirir. Çözüm arka ucu, günde bir kez, 14 KB 'lik cihaz ikizi okur ve yapılandırmayı değiştirmek için 512 baytlık yükleri güncelleştirir.

Cihaz şunları kullanır:

* 25 (100 KB/4 KB) ileti * cihazdan buluta iletiler için 24 saat.
* Cihaz ikizi güncelleştirmeleri için iki adet ileti (1 KB/0,5 KB) * gün başına altı kez.

Bu hesaplama günde toplam 612 ileti verir.

Çözüm arka ucu, cihaz ikizi okumak için 28 mesaj (14 KB/0,5 KB), Ayrıca, toplam 29 ileti için de bir ileti ile güncelleştirmek üzere bir ileti kullanır.

Toplam olarak, cihaz ve çözüm arka ucu günde 641 ileti tüketir.
