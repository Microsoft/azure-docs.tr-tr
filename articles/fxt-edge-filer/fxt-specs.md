---
title: Microsoft Azure FXT Edge Filer özellikleri | Microsoft Docs
description: Azure FXT Edge Filer donanımı için fiziksel ve ortam belirtimleri
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 90d56ad0cec3d187d4c7307f8c004b14c3860390
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254700"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Azure FXT Edge Filer özellikleri

Bu makalede, Azure FXT Edge Filer donanım düğümlerine yönelik donanım belirtimleri açıklanmaktadır. Uygulamada, kümelenmiş önbellek sistemi sağlamak için üç veya daha fazla düğüm birlikte yapılandırılır.

## <a name="hardware-specifications"></a>Donanım belirtimleri

| Bileşen | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| CPU çekirdekleri |  16 | 16 |
| He  | 1536 GB | 768 GB |
| Ağ bağlantı noktaları | 6 x 25/10 GB + 2 x 1 GB | 6 x 25/10 GB + 2 x 1 GB |
| NVMe SSD kapasitesi | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Sürücü belirtimleri

Sistemde, baştan erişilebilen on sürücü yuvası vardır. Doldurulan her sürücü, kapasite bilgileriyle sağ tarafta etiketlidir. 

Sürücü numaraları, sürücüler arasındaki alana yazdırılır. Azure FXT Edge Filsi içinde, 0. sürücü, sol üst sürücüdür ve 1. Sürücü doğrudan onun altında.

![FXT kasadaki bir sabit sürücü bölmesinin fotoğrafı, sürücü numaralarını ve kapasite etiketlerini gösterir](media/fxt-drives-photo.png)

| Sürücü numaraları    |  Bir yönetim grubuna bağlanmak veya bağlı bir yönetim grubunun özelliklerini düzenlemek için Yönetim çalışma alanında   |  Lerinize |
|------------------|--------|-----------------|
| 0, 1             | ATAYAMADı     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Veri   | FXT 6600:3,2 TB NVMe SSD <br> FXT 6400:1,6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Boyutlar ve ağırlık

Azure FXT Edge Filer, standart 19 "ekipman rafıyla uyum sağlayacak şekilde tasarlanmıştır ve bir raf birimi yüksek (1U). 

<!-- 10x2.5 inches version -->

| Filer boyutları           |                          |
|-----------------------------|--------------------------|
| Yükseklik                      | 42,8 mm (1,68 inç)    |
| Genişlik (raf kulakları dahil) | 482,0 mm (18,97 inç)  |
| Genişlik-ana kutu      | 434,0 mm (17,08 inç) |
| Derinlik rafı, ana kutunun geri dönmesi                   | 733,82 mm (29,61 inç) |
| Derinlik rafı, en yüksek arka uç                 | 772,67 mm (30,42 inç) |
| Derinlemesine önde bir ön ödeme yapmak için derinlik rafı, kasa olmadan | 22,0 mm (0,87 inç)  |
| Derinlemesine, ön ödemeli, kasa    | 35,84 mm (1,41 inç) |

| Lık | |
|-----------------|----------------------|
| Düğüm ağırlığı (paketsiz, aksesuarlar olmadan) | 40 lbs (18,1 kg) |
| Net ağırlığı (Donatılar dahil olmak üzere paketlemeden) | 51 lbs (23,1 kg)|
| Brüt ağırlık (sevk edilen, tüm paketlemeler dahil) |  64 lbs (29,0 kg) |

### <a name="shipping-dimensions"></a>Teslimat boyutları

| Paket boyutu | Milimetre | Cm |
|-------------------|-------------|--------|
| Yükseklik            | 311,2       | 12,25 " |
| Genişlik             | 642,8       | 25,31 " |
| Uzunluklu            | 1\.051,1     | 41,38 " |

## <a name="power-and-thermal-specifications"></a>Güç ve ısı belirtimleri

Bu bölüm, Azure FXT Edge Filer için güç derecelendirmeleri ve ölçümler sağlar.

### <a name="nameplate-ratings"></a>Ad levhası derecelendirmeleri

| FXT 6000 serisi modelleriyle ilgili ad levhası derecelendirmeleri |
|----------------|
| 100-240V ~    |
| 10A-5A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Güç ve sıcaklık ölçümleri 

Azure FXT Edge filigran düğümleri değişken hız fanı kullanır, bu nedenle güç sıcaklığa ve yüke bağlıdır. Yüksek yük ve yükseltilmiş çevresel sıcaklıkların belirli birleşimlerinde en fazla fan hızına erişilebilir. 

Bu grafikler, yaygın olarak kullanılan voltaj sıklığı birleşimleri için güç tüketimi ve sıcaklık çıkış ölçümleri sağlar. 

| FXT 6600 güç odası sıcaklığı <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Voltaj (V) | 100 | 120 | 208 | 230 | 240 | 
| Sıklık (Hz) | 60 | 60 | 60 | 50 | 50 |
| Geçerli (A) | 5,02 | 4,16 |2,40 | 2,20 | 2,16 |
| Görünen güç (VA) | 502 | 499 | 499 | 506 | 518|
| Güç faktörü | 0,99 | 0,99 |0,98 | 0,98 | 0,98 |
| Gerçek güç (W) | 497 |494 | 489 | 496 | 508 |
| Sıcaklık dağılımı (BTU/saat) |1696 | 1686 | 1669 | 1692 | 1733 |

| Maksimum fan hızlarındaki FXT 6600 gücü | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Voltaj (V) | 100 |120 | 208 | 230 | 240| 
| Sıklık (Hz) | 60 | 60 | 60 | 50 | 50 |
| Geçerli (A) | 5,98 | 5,01 | 2,81 | 2,55 | 2,48 |
| Görünen güç (VA) | 598 | 601 | 584 | 587 | 595 |
| Güç faktörü | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Gerçek güç (W) | 592 | 595 | 573 | 575 | 583 |
| Sıcaklık dağılımı (BTU/saat) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 güç odası sıcaklığı <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Voltaj (V) | 100 | 120 | 208 | 230 | 240 |
| Sıklık (Hz) |60 | 60 | 60 | 50 | 50 |
| Geçerli (A) | 4,63 | 3,86 | 2,24 | 2,04 | 1,94 |
| Görünen güç (VA) | 463 | 463 | 466 | 469 | 466 |
| Güç faktörü | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 | 
| Gerçek güç (W) | 458 | 459 | 457 | 460 | 456 |
| Sıcaklık dağılımı (BTU/saat) | 1564 | 1565 | 1558 | 1569 | 1557 |

| Maksimum fan hızlarındaki FXT 6400 gücü | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Voltaj (V) | 100 | 120 | 208 | 230 | 240 |
| Sıklık (Hz) | 60 | 60 | 60 | 50 | 50 |
| Geçerli (A) | 5,15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Görünen güç (VA) | 515 | 514 | 516 | 524 | 511 |
| Güç faktörü | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Gerçek güç (W) | 510 | 508 | 506 | 514 | 501 |
| Sıcaklık dağılımı (BTU/saat) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Çevresel gereksinimler

Bu bölüm, donanımın çevresel ortamı için belirtim sağlar.

### <a name="temperature-and-humidity"></a>Sıcaklık ve nem

| Çevresel özniteliği   | İşletim aralığı                   | İşletim dışı Aralık         |
|---------------------------|-----------------------------------|-----------------------------|
| Çevresel sıcaklık aralığı | 10 °C ila 35 °C (50-86 °F)          | -40 °C ila 65 °C (-40-149 °F) |
| Çevresel göreli nem | % 10-% 80 yoğunlaşmayan          | % 5-% 95 yoğunlaşmayan     |
| En fazla Dew noktası         | 29 °C (84 °F)                       | 33 °C (91 °F)                 |
| Çalışma                  | en çok 3048 ölçüm (10.000 feet), aşağıda belirtilen sıcaklık derecelendirmesi ile ilgili konular | en fazla 12.000 ölçüm (39.370 metre) |

> [!NOTE] 
> **Yükseklik sıcaklık sırası derecelendirmesi:** Maksimum sıcaklık 950 (3.117 ft) üzerinde 1 °C/300 milyon (1 °F/547 ft) azaltılır.

### <a name="airflow-shock-and-vibration"></a>Airflow, şok ve titreşim 

| Öznitelik         | Min |
|-------------------|---------------|
| Airflow                    | Sistem Airflow, baştan sona. Sistemin düşük basınç bir arka yükleme yüklemesiyle birlikte çalıştırılması gerekir. |
| Darbe, işlemsel         | 11 milisaniye için 6 G (6 yönde test edilmiştir) |
| Darbe, işlemsel olmayan     | 2 milisaniye için 71 G (6 yönde test edilmiştir) |
| Titreşim, işletimsel     | 0,26 G<sub>RMS</sub> 5 hz Ila 350 Hz rastgele         |
| Titreşim, işlemsel olmayan | 15 dakika boyunca 1,88 G<sub>RMS</sub> 10 hz Ila 500 Hz (altı tarafta test edilmiştir)  |

## <a name="safety-regulation-compliance"></a>Güvenlik düzenlemesi uyumluluğu 

Azure FXT Edge filigran, listelenen yönetmeliklere uyar. 

| Kategori       | Yasal belirtim | 
|----------------|--------------------------|
| Genel güvenlik | EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/ıEC 60950-1:2005 ED2 + a1:2009 + a2:2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ıCES-003  <br>EN 55032:2012/CıSPPR 32:2012  <br>EN 55032:2015/CıSPPR 32:2015  <br>EN 55024:2010 + A1:2015/CıSPPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/ıEC 61000-3-2:2014 (sınıf D)   <br>EN 61000-3-3:2013/ıEC 61000-3-3:2013 |
| Enerji         | Komisyon Yönetmeliği (AB) No. 617/2013  |
| RoHS           |    EN 50581:2012   |