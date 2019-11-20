---
title: StorSimple teknik özellikleri | Microsoft Docs
description: StorSimple donanım bileşenlerine yönelik teknik belirtimler ve mevzuat standartları uyumluluk bilgilerini açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965282"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>StorSimple cihazı için teknik belirtimler ve uyumluluk

## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Microsoft Azure StorSimple cihazınızın donanım bileşenleri, bu makalede açıklanan teknik belirtimler ve yasal standartlar standartlarına bağlı kalmaktadır. Teknik belirtimlerde güç ve soğutma modülleri (PCMs), disk sürücüleri, depolama kapasitesi ve kasaları açıklanır. Uyumluluk bilgileri, uluslararası standartlar, güvenlik ve emissions ve kablolama gibi şeyleri ele alır.

## <a name="power-and-cooling-module-specifications"></a>Güç ve soğutma modülü belirtimleri

StorSimple cihazında iki 100-240 V Dual fan, SBB ile uyumlu güç soğutma modülleri (PCMs) vardır. Bu, yedekli bir güç yapılandırması sağlar. PCM başarısız olursa, başarısız modül değiştirilinceye kadar cihaz normal olarak diğer PCM üzerinde çalışmaya devam eder.

EBOD Kasası 580 W PCM kullanır ve birincil kutu 764 W PCM kullanır. Aşağıdaki tablolarda, PCMs ile ilişkili Teknik özellikler listelenmiştir.

| Min | 580 W PCM (EBOD) | 764 W PCM (birincil) |
| --- | --- | --- |
| Maksimum çıkış gücü |580 W |764 |
| Sıklık |50/60 Hz |50/60 Hz |
| Voltaj aralığı seçimi |Otomatik olarak değişen: 90 – 264 V AC, 47/63 Hz |Otomatik olarak değişen: 90-264 V AC, 47/63 Hz |
| En fazla inrush geçerli |20 A |20 A |
| Güç faktörü düzeltmesi |> 95% nominal giriş gerilimi |> 95% nominal giriş gerilimi |
| Uyumcs |EN61000-3-2 karşılar |EN61000-3-2 karşılar |
| Output |5 v bekleme gerilimi \@ 2,0 A |5 v bekleme gerilimi \@ 2,7 A |
| \+ 5 V \@ 42 A |\+ 5 V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Etkin takılabilir |Evet |Evet |
| Anahtarlar ve LED 'ler |AC açık/kapalı anahtarı ve dört durum göstergesi LED 'Leri |AC açık/kapalı anahtarı ve altı durum göstergesi LED 'Leri |
| Kasa soğutma |Axial soğutma fanları, değişken fan hız denetimi |Axial soğutma fanları, değişken fan hız denetimi |

## <a name="power-consumption-statistics"></a>Güç tüketimi istatistikleri

Aşağıdaki tabloda, StorSimple cihazının çeşitli modelleri için tipik güç tüketimi verileri (gerçek değerler yayımlanan bilgisayardan farklılık gösterebilir) listelenmiştir.

| Koşullar | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Fanlar yavaş, sürücüler boşta |1,45 A |0,31 kW |1057,76 BTU/saat |3,19 A |0,34 kW |1160,13 BTU/saat |
|  Fanlar yavaş, erişen sürücüler |1,54 A |0,33 kW |1126,01 BTU/saat |3,27 A |0,36 kW |1228,37 BTU/saat |
|  Fanlar hızlı, diskler boşta, iki adet PSUs destekleniyor |2,14 A |0,49 kW |1671,95 BTU/saat |4,99 A |0,54 kW |1842,56 BTU/saat |
|  Fanların hızlı, boş disk, bir adet açık bir boşta |2,05 A |0,48 kW |1637,83 BTU/saat |4,58 A |0,50 kW |1706,07 BTU/saat |
|  Fanları hızlı, erişen Sürücüler, iki adet PSUs destekleniyor |2,26 A |0,51 kW |1740,19 BTU/saat |4,95 A |0,54 kW |1842,56 BTU/saat |
|  Fanları hızlı, erişen Sürücüler, bir adet boş güç |2,14 A |0,49 kW |1671,95 BTU/saat |4,81 A |0,53 kW |1808,44 BTU/saat |

## <a name="disk-drive-specifications"></a>Disk sürücüsü belirtimleri

StorSimple cihazınız en fazla 12 3,5-inç form faktörü seri ekli SCSI (SAS) disk sürücüsünü destekler. Gerçek sürücüler, ürün yapılandırmasına bağlı olarak katı hal sürücüleri (SSD 'Ler) veya sabit disk sürücüleri (HDD 'Ler) karışımı olabilir. 12 disk sürücü yuvaları, kutunun önünde 3 ile 4 arasında bir yapılandırmada bulunur. EBOD Kasası, başka bir 12 disk sürücüsüne ek depolama olanağı sağlar. Bunlar her zaman HDD 'ler.

## <a name="storage-specifications"></a>Depolama belirtimleri

StorSimple cihazlarında hem 8100 hem de 8600 için sabit disk sürücüleri ve katı hal sürücüleri karışımı vardır. 8100 ve 8600 için kullanılabilen toplam kapasite, sırasıyla yaklaşık 15 TB ve 38 TB 'tır. Aşağıdaki tabloda, StorSimple çözüm kapasitesi bağlamında SSD, HDD ve bulut kapasitesinin ayrıntıları belgelerde yer verilmiştir.

| Cihaz modeli/kapasitesi | 8100 | 8600 |
| --- | --- | --- |
| Sabit disk sürücülerinin (HDD 'Ler) sayısı |8 |19 |
| Katı hal sürücüleri (SSD 'Ler) sayısı |4 |5 |
| Tek HDD kapasitesi |4 TB |4 TB |
| Tek SSD kapasitesi |400 GB |800 GB |
| Yedek kapasite |4 TB |4 TB |
| Kullanılabilir HDD kapasitesi |14 TB |36 TB |
| Kullanılabilir SSD kapasitesi |800 GB |2 TB |
| Toplam kullanılabilir kapasite * |~ 15 TB |~ 38 TB |
| Maksimum çözüm kapasitesi (bulut dahil) |200 TB |500 TB |

<sup>* </sup>- *Kullanılabilir toplam kapasite, veriler, meta veriler ve arabellekler için kullanılabilir kapasiteyi içerir. 8100 cihazında 8,5 TB 'a kadar veya daha büyük 8600 cihazında 22,5 TB 'a kadar yerel olarak sabitlenmiş birimler sağlayabilirsiniz. Daha fazla bilgi için, [StorSimple yerel olarak sabitlenmiş birimlere](storsimple-8000-local-volume-faq.md)gidin.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Kutu boyutları ve ağırlık belirtimleri

Aşağıdaki tablolarda, Boyutlar ve ağırlığa yönelik çeşitli muhafaza belirtimleri listelenmektedir.

### <a name="enclosure-dimensions"></a>Kutu boyutları

Aşağıdaki tabloda, milimetre ve inç 'teki kutunun boyutları listelenmiştir.

| Kutu | Milimetre | Cm |
| --- | --- | --- |
| Yükseklik |87.9 |3.46 |
| Montaj flanş genelinde Genişlik |483 |19.02 |
| Kutunun gövdesinde Genişlik |443 |17.44 |
| Ön bağlama flanş 'ın extremity of kutu gövdesine kadar derinlik |577 |22.72 |
| İşlemler panelinden kapsamlı extremity muhafaza |630.5 |24.82 |
| Flanş 'in kasadan en uzextremity |603 |23.74 |

### <a name="enclosure-weight"></a>Kasa ağırlığı

Yapılandırmaya bağlı olarak, tam olarak yüklenen bir birincil kutu 21 ' den 33 KGS 'e ağırlık verebilir ve iki kişinin bunu işlemesini gerektirir.

| Kutu | Ağırlık |
| --- | --- |
| En yüksek ağırlık (yapılandırmaya bağlıdır) |30 kg – 33 kg |
| Boş (hiçbir sürücü uydurulur) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Muhafaza ortamı belirtimleri

Bu bölümde, kasa ortamıyla ilgili belirtimler listelenmektedir. Bu kategoriye sıcaklık, nem, yükseklik, darbe, titreşim, yönlendirme, güvenlik ve elektromanyetik uyumluluk (EMC) dahildir.

### <a name="temperature-and-humidity"></a>Sıcaklık ve nem

| Kutu | Çevresel sıcaklık aralığı | Çevresel göreli nem | Maksimum ıslak ampul |
| --- | --- | --- | --- |
| İşletimsel |5 °C-35 °C (41 °F-95 °F) |% 20-% 80 yoğunlaşmayan- |28 °C (82 °F) |
| İşlemsel olmayan |-40 °C-70 °C (40 °F-158 °F) |% 5-% 100 yoğunlaşmayan |29 °C (84 °F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Airflow, yükseklik, darbe, titreşim, yönlendirme, güvenlik ve EMC

| Kutu | İşletimsel belirtimler |
| --- | --- |
| Airflow |Sistem Airflow, baştan sona. Sistemin düşük basınç, arka uç yüklemesiyle birlikte çalıştırılması gerekir. Raf kapıları ve engellere göre oluşturulan arka basınç 5 pascals (0,5 mm su ölçer) değerini aşmamalıdır. |
| Yükseklik, işletimsel |-30 metreden 3045 metreye (-100 metreye 10.000 kadar), 7000 fit üzerinde 5 °C ile maksimum işletim sıcaklığı ile derecelendirildi. |
| Yükseklik, işlemsel olmayan |-305 metreye 12.192 ölçüm (-1.000 fit 40.000 'e kadar) |
| Darbe, işlemsel |5g 10 MS 1/2 sinüs |
| Darbe, işlemsel olmayan |30g 10 MS 1/2 sinüs |
| Titreşim, işletimsel |0.21 g RMS 5-500 Hz rastgele |
| Titreşim, işlemsel olmayan |1.04 g RMS 2-200 Hz rastgele |
| Titreşim, konum değiştirme |3G 2-200 Hz sinüsü |
| Yönlendirme ve bağlama |19 "raf bağlama (2 EA birimi) |
| Raf rayları |IEC 297 ile uyumlu minimum 700 mm (31,50 inç) derinlik raflarını sığdırmak için |
| Güvenlik ve onaylar |CE ve UL EN 61000-3, ıEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CIPR-A), FCC A |

## <a name="international-standards-compliance"></a>Uluslararası standartlar uyumluluğu

Microsoft Azure StorSimple cihazınız aşağıdaki uluslararası standartlarla uyumludur:  

* CE-EN 60950-1
* IEC 60950-1 için CB raporu
* UL ve cUL-UL 60950-1

## <a name="safety-compliance"></a>Güvenlik uyumluluğu

Microsoft Azure StorSimple cihazınız aşağıdaki güvenlik derecelendirmelerini karşılar:

* Sistem Ürün türü onayı: UL, cUL, CE
* Güvenlik uyumluluğu: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC uyumluluğu

Microsoft Azure StorSimple cihazınız aşağıdaki EMC derecelendirmelerini karşılıyor.

### <a name="emissions"></a>Emisyonlarını

Cihaz, yürütülen ve ışınlandırılmış emisyonlarını düzeyleri için EMC uyumludur.

* Emisyonlarını limit düzeyleri yapılmıştır: CFR 47 Bölüm 15B sınıf A EN55022 sınıfı CıPR sınıfı A
* Işınlandırılmış emisyonlarını sınır düzeyleri: CFR 47 Bölüm 15B sınıf A EN55022 sınıfı CıPR sınıfı A

### <a name="harmonics-and-flicker"></a>Uyumcs ve titreşim

Cihaz EN61000-3-2/3 ile uyumludur.

### <a name="immunity-limit-levels"></a>Immunity limit düzeyleri

Cihaz EN55024 ile uyumludur.

## <a name="ac-power-cord-compliance"></a>AC güç kablosu uyumluluğu

Tak ve Tamam güç kablosu derlemesi, cihazın kullanıldığı ülke/bölge için uygun olan standartları karşılamalıdır ve bu ülkede/bölgede kabul edilebilir güvenlik onayları olmalıdır. Aşağıdaki tablolarda, ABD ve Avrupa için standartlar listelenmektedir.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC gücü kablosu sayısı-USA (NRTL listede olmalıdır)

| Bileşen | Min |
| --- | --- |
| Kablo türü |ZF veya SVT, 18 AWG en az, 3 iletken, 2,0 ölçüm maksimum uzunluğu |
| Çalıştır |NEMA 5-15P grounding-tür ek eklentisi 120 V, 10 A; veya ıEC 320 C14, 250 V, 10 A |
| Yuvasının |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>AC güç kablosu-Avrupa

| Bileşen | Min |
| --- | --- |
| Kablo türü |Uyumlanmış, H05-VVF-3G 1.0 |
| Yuvasının |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Desteklenen ağ kabloları

10 GbE ağ arabirimleri, DATA 2 ve DATA 3 için [desteklenen ağ kabloları ve modülleri listesine](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Artık veri merkezinize bir StorSimple cihazı dağıtmaya hazırsınız. Daha fazla bilgi için bkz. Şirket [içi cihazınızı dağıtma](storsimple-8000-deployment-walkthrough-u2.md).

