---
title: Azure IoT Central Micro-Karşılama Merkezi | Microsoft Docs
description: IoT Central içinde mikro Karşılama Merkezi uygulama şablonumuzu kullanarak mikro Karşılama Merkezi uygulaması derlemeyi öğrenin
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 23e1880a3abac6ebda71eaa36276d2bf5815048e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833788"
---
# <a name="micro-fulfillment-center-architecture"></a>Mikro-karşılama merkezi mimarisi

Mikro Karşılama Merkezi çözümleri, güvenlik ve genel verimliliği artırarak kapalı kalma süresini ortadan kaldırarak maliyetleri azaltmak için tamamen otomatik bir karşılama merkezinin tüm yönlerini dijital olarak bağlamanıza, izlemenize ve yönetmenize olanak tanır. Bu çözümler IoT Central içindeki uygulama şablonlarından biri ve kılavuz olarak aşağıdaki mimaride kullanılarak oluşturulabilir.

![Azure IoT Central Mağazası Analizi](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. Bir ağ geçidi cihazına telemetri verileri gönderen IoT sensörleri kümesi
2. IoT Central telemetri ve toplu Öngörüler gönderen ağ geçidi cihazları
3. İşleme için istenen Azure hizmetine sürekli veri aktarma
4. Veriler istenen biçimde yapılandırılabilir ve bir depolama hizmetine gönderilebilir
5. İş uygulamaları, verileri sorgulayabilir ve Power Retail işlemleri ile ilgili öngörüler oluşturabilir
 
Genellikle, mikro Karşılama Merkezi çözümünde bir bölümü oynatacak anahtar bileşenlere göz atalım.

## <a name="robotic-carriers"></a>Robot taşıyıcılar

Mikro Karşılama Merkezi çözümü, farklı türlerde telemetri sinyalleri oluşturan büyük olasılıkla büyük bir robot taşıyıcılar kümesine sahip olacaktır. Bu sinyaller, bir ağ geçidi cihazı tarafından alınabilir, toplanır ve ardından mimari diyagramının sol tarafında yansıtıldığı gibi IoT Central gönderilir.  

## <a name="condition-monitoring-sensors"></a>Koşul izleme algılayıcılar

IoT çözümü, yerine getirme merkezinizden anlamlı sinyalleri yakalayan bir algılayıcı kümesiyle başlar. Yukarıdaki mimari diyagramının en solundaki farklı algılayıcı türleri tarafından yansıtılır.

## <a name="gateway-devices"></a>Ağ Geçidi cihazları

Birçok IoT algılayıcı, ham sinyalleri doğrudan buluta veya neredeyse bulunan bir ağ geçidi cihazına akışa alabilir. Ağ geçidi cihazı, bir IoT Central uygulamasına Özet Öngörüler göndermeden önce, uç sırada veri toplama işlemini gerçekleştirir. Ağ Geçidi cihazları, uygun olduğunda, algılayıcı cihazlarına geçiş komutu ve denetim işlemlerini de sorumludur. 

## <a name="iot-central-application"></a>IoT Central uygulaması

Azure IoT Central uygulaması, farklı türde IoT sensörlerinin, robotların yanı sıra karşılama merkezi ortamındaki ağ geçidi cihazlarından verileri alır ve anlamlı bir Öngörüler kümesi oluşturur.

Azure IoT Central Ayrıca, altyapı cihazlarını uzaktan izleyip yönetebilmesini sağlayan mağaza işlecine özel bir deneyim sağlar.

## <a name="data-transform"></a>Veri dönüştürme
Bir çözüm içindeki Azure IoT Central uygulaması, ham veya toplu öngörüleri veri işleme gerçekleştirebilen ve bu öngörüleri bir iş uygulamasına göre daha zengin bir şekilde zenginleştiren bir dizi Azure PaaS (hizmet olarak platform) hizmetine aktarmak üzere yapılandırılabilir. 

## <a name="business-application"></a>İş uygulaması
IoT verileri, perakende ortamında dağıtılan farklı türlerde iş uygulamalarının gücünü desteklemek için kullanılabilir. Bir karşılama Merkezi Yöneticisi veya çalışanı, bu uygulamaları iş öngörülerini görselleştirmek ve gerçek zamanlı olarak anlamlı işlemler yapmak için kullanabilir. Perakende ekibiniz için gerçek zamanlı Power BI panosu oluşturmayı öğrenmek için [öğreticiyi](./tutorial-in-store-analytics-create-app.md)izleyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Mikro Karşılama Merkezi](https://aka.ms/checkouttemplate) uygulama şablonunu kullanmaya başlayın. 
* Mikro Karşılama Merkezi uygulama şablonunu kullanarak bir çözüm oluşturma konusunda size yol gösteren [öğreticiye](https://aka.ms/mfc-tutorial) göz atın.
