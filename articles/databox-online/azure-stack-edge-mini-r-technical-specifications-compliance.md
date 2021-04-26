---
title: Microsoft Azure Stack Edge Mini Teknik özellikleri ve uyumluluğu | Microsoft Docs
description: Azure Stack Edge Mini R cihazınız için teknik belirtimler ve uyumluluk hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: b0f3b81ac12d0ae96dde0397bc01a4570d992ea9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310181"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini Teknik özellikleri

Microsoft Azure Stack Edge Mini R cihazınızın donanım bileşenleri, bu makalede açıklanan teknik belirtimler ve yasal standartlar standartlarına bağlı olarak. Teknik belirtimlerde CPU, bellek, güç kaynağı birimleri (PSUs), depolama kapasitesi, kasa boyutları ve ağırlık açıklanır.


## <a name="compute-memory-specifications"></a>İşlem, bellek belirtimleri

Azure Stack Edge Mini R cihazının işlem ve bellek için aşağıdaki belirtimleri vardır:

| Belirtim           | Değer                           |
|-------------------------|---------------------------------|
| CPU türü                | Intel Xeon-D 1577               |
| CPU: ham                | 16 toplam çekirdek, 32 Toplam vCPU  |
| CPU: kullanılabilir             | 24 vCPU                        |
| Bellek türü             | 16 GB 2400 MT/s SODıMM          |
| Bellek: ham             | 48 GB RAM (3 x 16 GB)           |
| Bellek: kullanılabilir          | 32 GB RAM                       |


## <a name="compute-acceleration-specifications"></a>İşlem hızlandırma belirtimleri

Bir vizyon Işleme birimi (VPU), Kubernetes, derin sinir ağı ve bilgisayar Vision tabanlı uygulamalar sağlayan her Azure Stack Edge Mini R cihazına dahildir.

| Belirtim             | Değer                  |
|---------------------------|------------------------|
| İşlem hızlandırma kartı | Intel Movidius Myriad X VPU <br> Daha fazla bilgi için bkz. [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>Depolama belirtimleri

Azure Stack Edge Mini R cihazının 1 veri diski ve 1 önyükleme diski vardır (işletim sistemi depolama işlevi görür). Aşağıdaki tabloda, cihazın depolama kapasitesine ilişkin ayrıntılar gösterilmektedir.

|     Belirtim                          |     Değer                                              |
|--------------------------------------------|--------------------------------------------------------|
|    Katı hal sürücüleri (SSD 'Ler) sayısı     |    2 X 1 TB disk <br> Bir veri diski ve bir önyükleme diski |
|    Tek SSD kapasitesi                     |    1 TB                                                |
|    Toplam Kapasite (yalnızca veri)              |    1 TB                                                |
|    Toplam kullanılabilir kapasite *                  |    ~ 750 GB                                            |

**Bazı boşluklar iç kullanım için ayrılmıştır.*

## <a name="network-specifications"></a>Ağ belirtimleri

Azure Stack Edge Mini R cihazının ağ için aşağıdaki özellikleri vardır:


|Belirtim  |Değer  |
|---------|---------|
|Ağ arabirimleri    |2 x 10 GbE SFP + <br> Bağlantı noktası 3 ve bağlantı noktası 4 olarak yerel kullanıcı arabiriminde gösterilir           |
|Ağ arabirimleri    |2 x 1 GbE RJ45 <br> Yerel Kullanıcı arabiriminde bağlantı noktası 1 ve bağlantı noktası 2 olarak gösterilir          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Güç kaynağı birimi belirtimleri

Azure Stack Edge Mini R cihazı, güç sağlamak ve yerleşik pili ücretlendirmesini sağlamak için bir dış 85 W AC bağdaştırıcısı içerir.

Aşağıdaki tabloda güç kaynağı birimi belirtimleri gösterilmektedir:

| Belirtim           | Değer                      |
|-------------------------|----------------------------|
| Maksimum çıkış gücü    | 85 W                       |
| Sıklık               | 50/60 Hz                   |
| Voltaj aralığı seçimi | Otomatik olarak değişen: 100-240 V AC |



## <a name="included-battery"></a>Dahil edilen pil

Azure Stack Edge Mini R cihazı, güç kaynağı tarafından ücretlendirilen bir yerleşik pili de içerir.

Ek bir [2590 pili](https://www.bren-tronics.com/bt-70791ck.html) , kart ücretleri arasında kullanımı genişletmek için yerleşik pille birlikte kullanılabilir. Bu pil, kullanım ülkesinde geçerli olan tüm güvenlik, ulaşım ve çevresel yönetmelikleriyle uyumlu olmalıdır.


| Belirtim            | Değer                      |
|--------------------------|----------------------------|
| Yerleşik pil kapasitesi | 73 WH                     |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Kutu boyutları ve ağırlık belirtimleri

Aşağıdaki tablolarda, Boyutlar ve ağırlığa yönelik çeşitli muhafaza belirtimleri listelenmektedir.

### <a name="enclosure-dimensions"></a>Kutu boyutları

Aşağıdaki tabloda, Rugged Case for milimetre and inç ile birlikte, cihaz boyutları ve USP listelenmiştir.

|     Kutu     |     Milimetre     |     Cm     |
|-------------------|---------------------|----------------|
|    Height         |    68               |    2,68        |
|    Width          |    208              |      8,19      |
|    Uzunluk          |   259              |    10,20       |


### <a name="enclosure-weight"></a>Kasa ağırlığı

Aşağıdaki tabloda, pili dahil Cihaz ağırlığı listelenmektedir.

|     Kutu                     |     Ağırlık          |
|-----------------------------------|---------------------|
|    Cihazın toplam ağırlığı     |     7 lbs           |

## <a name="enclosure-environment-specifications"></a>Muhafaza ortamı belirtimleri


Bu bölümde sıcaklık, nem ve yükseklik gibi çevre duvarı ortamıyla ilgili belirtimler listelenmektedir.


|     Belirtimler             |     Açıklama                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Sıcaklık aralığı          |     0 – 43 ° C (işletimsel)                                              |
|     Titreşimi                  |     MIL-STD-810 yöntemi 514,7 *<br> İşlem ı CAT 4, 20                  |
|     Şok                      |     MIL-STD-810 yöntemi 516,7 *<br> Yordam IV, lojistik                 |
|     Çalışma                   |     İşletimsel: 10.000 fit<br> İşlemsel olmayan: 40.000 fit          |

**Tüm başvurular MIL-STD-810G değiştirme 1 ' dir (2014)*


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Mini R 'nizi dağıtma](azure-stack-edge-placeholder.md)
