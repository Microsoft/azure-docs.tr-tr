---
title: include dosyası
description: include dosyası
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518089"
---
Bu makalede, Azure sanal makinelerini ve Azure disklerini birleştirdiğinizde disk performansını ve nasıl çalıştığını açıklığa kavuşturmasına yardımcı olur. Ayrıca, disk GÇ ve performans için iyileştirmek üzere yapabileceğiniz değişiklikler için performans sorunlarını nasıl tanılabileceğinizi açıklar.

## <a name="how-does-disk-performance-work"></a>Disk performansı nasıl çalışır?
Azure sanal makineleri, sanal makine türü ve boyutuna bağlı olarak saniyede giriş/çıkış işlemi (ıOPS) ve üretilen iş performansı sınırlarına sahiptir. İşletim sistemi diskleri ve veri diskleri, sanal makinelere eklenebilir. Disklerin kendi ıOPS ve verimlilik limitleri vardır.

Uygulamanızın performansı, sanal makineler veya bağlı diskler için ayrılan miktardan daha fazla ıOPS veya üretilen iş istediğinde daha fazla zaman alır. Bu durumda uygulama, performans performansından yararlanıyorsa. Bu, artan gecikme süresi gibi olumsuz sonuçlara neden olabilir. Bu kavramı açıklığa kavuşturacak birkaç örnek üzerinden çalıştıralım. Bu örneklerin izlenmesi kolay hale getirmek için yalnızca ıOPS 'ye bakacağız. Ancak, aynı Logic üretilen iş için de geçerlidir.

## <a name="disk-io-capping"></a>Disk GÇ dönüşü

**Yapısı**

- Standard_D8s_v3
  - Önbelleğe alınmamış ıOPS: 12.800
- E30 işletim sistemi diski
  - IOPS: 500
- İki E30 veri diski × 2
  - IOPS: 500

![Disk düzeyi dönüşü gösteren diyagram.](media/vm-disk-performance/disk-level-throttling.jpg)

Sanal makinede çalışan uygulama, sanal makinede 10.000 ıOPS gerektiren bir istek oluşturur. Standard_D8s_v3 sanal makine en fazla 12.800 ıOPS yürütebildiğinden VM tarafından kullanılabilir.

10.000 ıOPS isteği, farklı disklere üç farklı isteğe ayrılmıştır:

- 1.000 ıOPS, işletim sistemi diskine istendi.
- Her veri diskine 4.500 ıOPS istendi.

Tüm eklenen diskler E30 disklerdir ve yalnızca 500 ıOPS 'yi işleyebilir. Bu nedenle, her biri 500 ıOPS ile geri yanıt verir. Uygulamanın performansı, eklenen diskler tarafından belirlenir ve yalnızca 1.500 ıOPS 'yi işleyebilir. Premium SSD P30 diskler gibi daha iyi çalışan diskler kullanılıyorsa, uygulama 10.000 ıOPS 'de yoğun performans üzerinde çalışabilir.

## <a name="virtual-machine-io-capping"></a>Sanal makine GÇ GÇ

**Yapısı**

- Standard_D8s_v3
  - Önbelleğe alınmamış ıOPS: 12.800
- P30 işletim sistemi diski
  - IOPS: 5.000
- İki P30 veri diski × 2
  - IOPS: 5.000

![Sanal makine düzeyinde dönüşü gösteren diyagram.](media/vm-disk-performance/vm-level-throttling.jpg)

Sanal makinede çalışan uygulama, 15.000 ıOPS gerektiren bir istek yapar. Ne yazık ki Standard_D8s_v3 sanal makine yalnızca 12.800 ıOPS 'yi işleyecek şekilde sağlanır. Uygulama, sanal makine sınırlarına göre belirlenir ve ayrılan 12.800 ıOPS 'yi ayırması gerekir.

İstenen 12.800 ıOPS, farklı disklere üç farklı istek olarak bölünür:

- 4.267 ıOPS, işletim sistemi diskine istendi.
- Her veri diskine 4.266 ıOPS istendi.

Tüm eklenen diskler, 5.000 ıOPS 'yi işleyebilen P30 disklerdir. Bu nedenle, istenen tutarlarla geri yanıt verir.
