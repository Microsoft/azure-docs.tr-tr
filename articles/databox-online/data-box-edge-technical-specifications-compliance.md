---
title: Teknik özellikleri ve uyumluluğu Data Box Edge Microsoft Azure | Microsoft Docs
description: Azure Data Box Edge için teknik belirtimler ve uyumluluk hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/26/2019
ms.author: alkohli
ms.openlocfilehash: 365507f482217bd804bbd5017d4dbbaf8c187ad2
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326891"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure Data Box Edge teknik belirtimleri

Microsoft Azure Data Box Edge cihazınızın donanım bileşenleri, bu makalede açıklanan teknik belirtimler ve yasal standartlar standartlarına uyar. Teknik belirtimler, güç kaynağı birimlerini (PSUs), depolama kapasitesini, kasaları ve çevresel standartları anlatmaktadır. 

## <a name="compute-memory-specifications"></a>İşlem, bellek belirtimleri

Data Box Edge cihaz, işlem ve bellek için aşağıdaki belirtimlere sahiptir:

| Min           | Value                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 çekirdek CPU                     |
| Bellek              | 128 GB RAM                  |


## <a name="fpga-specifications"></a>FPGA belirtimleri

Bir alan programlanabilir kapı dizisi (FPGA), Machine Learning (ML) senaryolarına izin veren her Data Box Edge cihazına dahildir. 

| Min           | Value                  |
|-------------------------|----------------------------|
| FPGA   | Intel vara 10 <br> Kullanılabilir derin sinir ağı (DNN) modelleri, [bulut FPGA örnekleri tarafından desteklenenlerle](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#whats-supported-on-azure)aynıdır.| 


## <a name="power-supply-unit-specifications"></a>Güç kaynağı birimi belirtimleri

Data Box Edge cihazda yüksek performanslı fanlarla iki 100-240 V güç kaynağı birimi (PSUs) vardır. İki PSUs, yedekli bir güç yapılandırması sağlar. Bir PSU başarısız olursa, başarısız modül değiştirilinceye kadar cihaz normal olarak diğer PSU üzerinde çalışmaya devam eder. Aşağıdaki tabloda, PSUs teknik özellikleri listelenmektedir.

| Min           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maksimum çıkış gücü    | 750 W                     |
| Sıklık               | 50/60 Hz                   |
| Voltaj aralığı seçimi | Otomatik olarak değişen: 100-240 V AC |
| Etkin takılabilir           | Evet                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Depolama belirtimleri

Data Box Edge cihazlarda 9 X 2,5 "NVMe SSDs, her biri 1,6 TB kapasiteye sahiptir. Bu SSD 'Ler, 1 bir işletim sistemi diski ve diğer 8 ise veri disklerdir. Cihaz için kullanılabilen toplam kapasite kabaca 12,5 TB 'tır. Aşağıdaki tabloda, cihazın depolama kapasitesinin ayrıntıları verilmiştir.

|     Min                          |     Value             |
|--------------------------------------------|-----------------------|
|    Katı hal sürücüleri (SSD 'Ler) sayısı     |    8                  |
|    Tek SSD kapasitesi                     |    1,6 TB             |
|    Toplam kapasite                          |    12,8 TB            |
|    Toplam kullanılabilir kapasite *                  |    ~ 12,5 TB            |

**Bazı boşluklar iç kullanım için ayrılmıştır.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Kutu boyutları ve ağırlık belirtimleri

Aşağıdaki tablolarda, Boyutlar ve ağırlığa yönelik çeşitli muhafaza belirtimleri listelenmektedir.

### <a name="enclosure-dimensions"></a>Kutu boyutları

Aşağıdaki tabloda, milimetre ve inç 'teki kutunun boyutları listelenmiştir.

|     Kutu     |     Milimetre     |     Cm     |
|-------------------|---------------------|----------------|
|    Yükseklik         |    44,45            |    1,75 "          |
|    Genişlik          |    434,1           |    17,09 "          |
|    Uzunluk          |    740,4           |    29,15 "          |

Aşağıdaki tabloda, nakliye paketinin boyutları milimetre ve inç olarak listelenmektedir.

|     Paket     |     Milimetre     |     Cm     |
|-------------------|---------------------|----------------|
|    Yükseklik         |    311,2            |    12,25 "          |
|    Genişlik          |    642,8          |    25,31 "          |
|    Uzunluk          |   1\.051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Kasa ağırlığı

Cihaz paketi 66 lbs. ve bunu işlemek için iki kişi gerektirir. Cihazın ağırlığı, kutunun yapılandırmasına bağlıdır.

|     Kutu                                 |     Ağırlık          |
|-----------------------------------------------|---------------------|
|    Paketleme dahil toplam ağırlık       |    61 lbs.          |
|    Cihazın ağırlığı                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Muhafaza ortamı belirtimleri

Bu bölümde sıcaklık, nem ve yükseklik gibi çevre duvarı ortamıyla ilgili belirtimler listelenmektedir.

### <a name="temperature-and-humidity"></a>Sıcaklık ve nem

|     Kutu         |     Çevresel sıcaklık aralığı     |     Çevresel göreli nem     |     En fazla Dew noktası     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    İşletimsel        |    10 °C-35 °C (50 °F-86 °F)         |    % 10-% 80 yoğunlaşmayan.         |    29 °C (84 °F)            |
|    İşlemsel olmayan    |    -40 °C ila 65 °C (-40 °F-149 °F)     |    % 5-% 95 yoğunlaşmayan.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Airflow, yükseklik, darbe, titreşim, yönlendirme, güvenlik ve EMC

|     Kutu                           |     İşletimsel belirtimler                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Airflow                              |    Sistem Airflow, baştan sona. Sistemin düşük basınç, arka uç yüklemesiyle birlikte çalıştırılması gerekir. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maksimum yükseklik, işletimsel        |    3048 metreler (10.000 metre), [çalışma sıcaklığı derecelendirme belirtimlerine](#operating-temperature-de-rating-specifications)göre belirlenen maksimum işletim sıcaklığı ile derecelendirildi.                                                                                |
|    Maksimum yükseklik, işlemsel olmayan    |    12.000 ölçü (39.370 feet)                                                                                                                                                                                         |
|    Darbe, işlemsel                   |    6 yönde 11 milisaniye için 6 G                                                                                                                                                                         |
|    Darbe, işlemsel olmayan               |    6 yönte 2 milisaniye için 71 G                                                                                                                                                                           |
|    Titreşim, işletimsel               |    0,26 G<sub>RMS</sub> 5 hz Ila 350 Hz rastgele                                                                                                                                                                                     |
|    Titreşim, işlemsel olmayan           |    15 dakika boyunca 1,88 G<sub>RMS</sub> 10 hz Ila 500 Hz (altı tarafta test edilmiştir.)                                                                                                                                                  |
|    Yönlendirme ve bağlama             |    19 "raf bağlama                                                                                                                                                                                        |
|    Güvenlik ve onaylar                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/ıEC 60950-1:2005 ED2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPPR 32:2012  <br>EN 55032:2015/CISPPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/ıEC 61000-3-2:2014 (sınıf D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Enerji             |    Komisyon Yönetmeliği (AB) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>İşletim sıcaklığı de derecelendirme belirtimleri

|     İşletim sıcaklığı de derecelendirme     |     Çevresel sıcaklık aralığı                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    En fazla 35 °C (95 °F)                       |    Maksimum sıcaklık 950 (3.117 ft) üzerinde 1 °C/300 milyon (1 °F/547 ft) azaltılır.    |
|    35 °C ila 40 °C (95 °F ila 104 °F)            |    Maksimum sıcaklık 1 °C/175 milyon (1 °F/319 ft) 950 (3.117 ft) üzerinde azaltılır.    |
|    40 °C ila 45 °C (104 °F/113 °f)           |    Maksimum sıcaklık 1 °C/125 milyon (1 °F/228 ft) 950 (3.117 ft) üzerinde azaltılır.    |


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Box Edge dağıtın](data-box-edge-deploy-prep.md)
