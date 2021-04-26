---
title: Depolama Analizi mimarisi
description: IoT Central 'de kullanıma alma uygulama şablonunu kullanarak bir mağaza içi analiz uygulaması derlemeyi öğrenin
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 972ab8eaaf7c0b9fe8beb446d74bc8d2e89bbc55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832207"
---
# <a name="in-store-analytics-architecture"></a>Mağaza içi analiz mimarisi


Mağaza içi analiz çözümleri, perakende mağaza ortamındaki çeşitli koşulları izlemenizi sağlar. Bu çözümler IoT Central içindeki uygulama şablonlarından biri ve kılavuz olarak aşağıdaki mimaride kullanılarak oluşturulabilir.


![Azure IoT Central Mağazası Analizi](./media/architecture/store-analytics-architecture-frame.png)

- Bir ağ geçidi cihazına telemetri verileri gönderen IoT sensörleri kümesi
- IoT Central telemetri ve toplu Öngörüler gönderen ağ geçidi cihazları
- İşleme için istenen Azure hizmetine sürekli veri aktarma
- Veriler istenen biçimde yapılandırılabilir ve bir depolama hizmetine gönderilebilir
- İş uygulamaları, verileri sorgulayabilir ve Power Retail işlemleri ile ilgili öngörüler oluşturabilir
 
Genellikle mağaza Analizi çözümünde bir bölümü oynatacak anahtar bileşenlere göz atalım.

## <a name="condition-monitoring-sensors"></a>Koşul izleme algılayıcılar

IoT çözümü, perakende mağaza ortamının içinden anlamlı sinyalleri yakalayan bir algılayıcı kümesiyle başlar. Yukarıdaki mimari diyagramının en solundaki farklı algılayıcı türleri tarafından yansıtılır.

## <a name="gateway-devices"></a>Ağ Geçidi cihazları

Birçok IoT algılayıcı, ham sinyalleri doğrudan buluta veya neredeyse bulunan bir ağ geçidi cihazına akışa alabilir. Ağ geçidi cihazı, bir IoT Central uygulamasına Özet Öngörüler göndermeden önce, uç sırada veri toplama işlemini gerçekleştirir. Ağ Geçidi cihazları, uygun olduğunda, algılayıcı cihazlarına geçiş komutu ve denetim işlemlerini de sorumludur. 

## <a name="iot-central-application"></a>IoT Central uygulaması

Azure IoT Central uygulaması, perakende mağaza ortamındaki farklı IoT algılayıcılarının yanı sıra ağ geçidi cihazlarından verileri alır ve anlamlı Öngörüler kümesi oluşturur.

Azure IoT Central Ayrıca, altyapı cihazlarını uzaktan izleyip yönetebilmesini sağlayan mağaza işlecine özel bir deneyim sağlar.

## <a name="data-transform"></a>Veri dönüştürme
Bir çözüm içindeki Azure IoT Central uygulaması, ham veya toplu öngörüleri veri işleme gerçekleştirebilen ve bu öngörüleri bir iş uygulamasına göre daha zengin bir şekilde zenginleştiren bir dizi Azure PaaS (hizmet platformu) hizmetine aktarmak üzere yapılandırılabilir. 

## <a name="business-application"></a>İş uygulaması
IoT verileri, perakende ortamında dağıtılan farklı türlerde iş uygulamalarının gücünü desteklemek için kullanılabilir. Bir perakende mağaza yöneticisi veya personel üyesi, bu uygulamaları iş öngörülerini görselleştirmek ve gerçek zamanlı olarak anlamlı işlemler yapmak için kullanabilir. Perakende ekibiniz için gerçek zamanlı Power BI panosu oluşturmayı öğrenmek için [öğreticiyi](./tutorial-in-store-analytics-create-app.md)izleyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Mağaza Içi analiz kullanıma alma](https://aka.ms/checkouttemplate) ve [Mağaza Içi analiz koşulu izleme](https://aka.ms/conditiontemplate) uygulama şablonlarını kullanmaya başlayın. 
* In-Store Analytics Uygulama şablonlarından birini kullanarak çözüm oluşturma konusunda size yol gösteren [uçtan uca öğreticiye](https://aka.ms/storeanalytics-tutorial) göz atın.
