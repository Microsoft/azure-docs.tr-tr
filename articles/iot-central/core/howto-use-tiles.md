---
title: Azure IoT Central uygulama panosu 'nda kutucukları kullanma | Microsoft Docs
description: Oluşturucu olarak, uygulama panoları, cihaz kümesi panoları ve cihaz panoları üzerinde kutucukları nasıl kullanacağınızı öğrenin.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0803258c362279049a49a7eee00e7a4763621672
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952634"
---
# <a name="how-to-use-tiles"></a>Kutucukları kullanma
Bölmeleri, uygulama panoları, cihaz panoları ve cihaz kümesi panoları özelleştirmek için kullanabilirsiniz. Bir panoya birden fazla kutucuk ekleyebilir ve bu kutucukları özelleştirerek uygulamanızla ilgili bilgileri görüntüleyebilirsiniz. Ayrıca kutucukları yeniden boyutlandırabilir ve herhangi bir panoda düzeni özelleştirebilirsiniz. Aşağıdaki ekran görüntüsünde, farklı kutucukları olan uygulama panosu gösterilmektedir.

![Uygulama panosu](media/howto-use-tiles/image1a.png)


Aşağıdaki tabloda Azure IoT Central kutucukların kullanımı özetlenmektedir:

 
| Kaldır | Pano | Açıklama
| ----------- | ------- | ------- |
| Bağlantı | Uygulama ve cihaz kümesi panoları |Bağlantı kutucukları, başlık ve açıklama metnini görüntüleyen tıklatılabilir kutucuklardır. Bir kullanıcının uygulamanızla ilişkili bir URL 'ye gitmesini sağlamak için bir bağlantı kutucuğu kullanın. |
| Görüntü | Uygulama ve cihaz kümesi panoları |Görüntü kutucukları özel bir görüntü görüntüler ve tıklatılabilir olabilir. Panoya grafik eklemek ve isteğe bağlı olarak bir kullanıcının uygulamanızla ilgili bir URL 'ye gitmesini sağlamak için bir görüntü kutucuğu kullanın.|
| Etiket | Uygulama panoları |Etiket kutucukları, bir panoda özel metin görüntüler. Metnin boyutunu seçebilirsiniz. Panoya ilgili bilgileri, iletişim ayrıntılarını veya yardım bilgilerini eklemek için bir etiket kutucuğu kullanın|
| Eşleme | Uygulama ve cihaz kümesi panoları |Harita kutucukları bir haritadaki bir cihazın konumunu ve durumunu görüntüler. Örneğin, bir cihazın nerede olduğunu ve fanı 'nin geçiş yapıp yapmadığını görüntüleyebilirsiniz.|
| Çizgi grafik | Uygulama ve cihaz panoları |Çizgi grafik kutucukları bir zaman dönemi için bir cihazın toplam ölçümünün bir grafiğini görüntüler. Örneğin, son saat için bir cihaza ortalama sıcaklık ve basınç gösteren bir çizgi grafiği görüntüleyebilirsiniz|
| Çubuk grafik | Uygulama ve cihaz panoları |Çubuk grafik kutucukları bir zaman dönemi için bir cihazın toplam ölçümlerinin bir grafiğini görüntüler. Örneğin, son saat için bir cihaza ortalama sıcaklık ve basınç gösteren bir çubuk grafik görüntüleyebilirsiniz |
| Olay geçmişi | Uygulama ve cihaz panoları |Olay geçmişi kutucuğu bir zaman dönemi içinde bir cihaz için olayları görüntüler. Örneğin, son bir saat içinde bir cihaz için gerçekleşen tüm sıcaklık değişikliklerini göstermek için bunu kullanabilirsiniz. |
| Durum geçmişi | Uygulama ve cihaz panoları |Durum geçmişi kutucuğu bir zaman döneminin ölçüm değerlerini görüntüler. Örneğin, son bir saat içinde bir cihaz için sıcaklık değerlerini göstermek üzere onu kullanabilirsiniz.|
| KPI | Uygulama ve cihaz panoları | KPI kutucuğu bir dönem için bir toplama telemetrisi veya olay ölçümü görüntüler. Örneğin, son bir saat içinde bir cihaz için ulaşılan en fazla sıcaklık sayısını göstermek için bunu kullanabilirsiniz.|
| Bilinen son değer | Uygulama ve cihaz panoları |Son bilinen değer kutucuğu, bir telemetri veya durum ölçümü için en son değeri görüntüler. Örneğin, bu kutucuğu, bir cihaz için en son sıcaklık, basınç ve nem ölçümlerini göstermek için kullanabilirsiniz.|
| Cihaz kümesi Kılavuzu | Uygulama ve cihaz kümesi panoları | Cihaz kümesi Kılavuzu cihaz kümesi hakkındaki bilgileri görüntüler. Cihaz kümesi içindeki tüm cihazların adı, konumu ve modeli gibi bilgileri göstermek için bir cihaz kümesi kılavuz kutucuğu kullanın.|


Panoyu Azure IoT Central uygulamanızda yapılandırma hakkında daha fazla bilgi edinmek için bkz. [panonuza kutucuk ekleme](./howto-add-tiles-to-your-dashboard.md).
