---
title: StorSimple izleme göstergeleri | Microsoft Docs
description: StorSimple cihazının durumunu izlemek için kullanılan açık yayıcı (LED 'ler) ve duyulabilir alarmlar açıklanmaktadır.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 97209dca7d30de037dbd21f5cc145b2941060e70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015409"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Cihazınızı yönetmek için StorSimple izleme göstergelerini kullanın


## <a name="overview"></a>Genel Bakış
StorSimple cihazınız, modülleri ve StorSimple cihazının genel durumunu izlemek için kullanabileceğiniz açık-yayıcı (LED 'ler) ve alarmlar içerir. İzleme göstergeleri, cihazın birincil kasası ve EBOD Kasası 'nın donanım bileşenlerinde bulunabilir. İzleme göstergeleri, LED 'ler ya da duyulabilir alarmlar olabilir.

Bir modülün durumunu göstermek için kullanılan üç LED durumu vardır: yeşil, yanıp sönen yeşil, kırmızı-bir.  

* Yeşil LED 'ler sağlıklı bir çalışma durumunu temsil eder.  
* Yeşil ' e yanıp sönen Red-bir LED 'ler, Kullanıcı müdahalesi gerektirebilecek kritik olmayan koşulların varlığını temsil eder.  
* Red-IBU LED 'ler, modülde önemli bir hata olduğunu gösterir.  

Bu makalenin geri kalanında çeşitli izleme göstergesi LED 'Leri, StorSimple cihazındaki konumları, LED durumlarına bağlı olarak cihaz durumu ve ilişkili duyulabilir alarmlar açıklanmaktadır.

## <a name="front-panel-indicator-leds"></a>Ön panel göstergesi LED 'Leri
*İşlemler paneli* veya *Ops paneli* olarak da bilinen ön panel, sistemdeki tüm modüllerin toplam durumunu görüntüler. Ön panel, StorSimple birincil ve EBOD kasası ile aynıdır ve aşağıda gösterilmiştir.  

   ![Cihaz ön paneli][1]

Ön panel aşağıdaki göstergeleri içerir:  

1. Sessiz düğme
2. Güç göstergesi ışığı (yeşil/Red-bir)
3. Modül hatası göstergesi ışığı (kırmızı-bir ÜZERINDE/kapalı)
4. Mantıksal hata göstergesi ışığı (kırmızı-ve kapalı)
5. Birim KIMLIĞI görüntüleme  

Cihazın ön panel led 'Leri ve EBOD kasası ile ilgili önemli fark, LED görüntüsünde gösterilen **sistem birimi kimlik numarasıdır** . Cihazda görünen varsayılan birim KIMLIĞI **00**' dır, ancak EBOD muhafazasında görünen varsayılan birim kimliği **01**' dir. Bu, cihaz açık olduğunda cihazı ve EBOD kasasının arasında hızlı bir şekilde ayrım yapmanıza olanak tanır. Cihazınız kapalıysa, cihazı EBOD kasasında ayırt etmek için [Yeni bir cihazı açma](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) bölümünde belirtilen bilgileri kullanın.  

## <a name="front-panel-led-status"></a>Ön Panel LED durumu
Cihaz veya EBOD Kasası için ön panelde bulunan LED 'ler tarafından belirtilen durumu belirlemek için aşağıdaki tabloyu kullanın.  

| Sistem gücü | Modül hatası | Mantıksal hata | Alarm | Durum |
| --- | --- | --- | --- | --- |
| Red-, |KAPALI |KAPALI |Yok |AC güç kaybı, yedekleme gücüyle çalışma veya AC gücü açık ve denetleyici modülleri kaldırılmıştır. |
| Yeşil |AÇIK |AÇIK |Yok |Ops panel güç açma (5 s) test durumu |
| Yeşil |KAPALI |KAPALI |Yok |Power On, tüm işlevler iyi |
| Yeşil |AÇIK |Yok |PCM hata LED 'Leri, fan hata LED 'Leri |Herhangi bir PCM hatası, fan hatası, sıcaklığın üzerinde veya altında |
| Yeşil |AÇIK |Yok |G/ç Modülü LED 'Leri |Herhangi bir denetleyici modülü hatası |
| Yeşil |AÇIK |Yok |Yok |Kasa mantığı hatası |
| Yeşil |In |Yok |Modül durumu denetleyici modülünde ışığı. PCM hata LED 'Leri, fan hata LED 'Leri |Bilinmeyen denetleyici modülü türü yüklendi, ı2C veri yolu hatası, denetleyici modülü önemli ürün verileri (VPD) yapılandırma hatası |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Güç soğutma modülü (PCM) göstergesi LED 'Leri
Güç soğutma modülü (PCM) göstergesi LED 'Leri, her bir PCM modülündeki birincil kutunun arkasında veya EBOD Kasası bulunabilir. Bu konuda, StorSimple cihazınızın durumunu izlemek için aşağıdaki LED 'ler nasıl kullanılacağı ele alınmaktadır.  

* Birincil kasa için PCM LED 'Leri
* EBOD Kasası için PCM LED 'Leri

## <a name="pcm-leds-for-the-primary-enclosure"></a>Birincil kasa için PCM LED 'Leri
StorSimple cihazının ek pili olan bir 764W PCM modülü vardır. Aşağıdaki çizimde cihaz için LED paneli gösterilmektedir.  

   ![Birincil kasada PCM LED 'Leri][2]

LED göstergesi:

1. AC güç hatası
2. Fan hatası
3. Pil hatası
4. PCM TAMAM
5. DC hatası
6. Pil iyi  

PCM 'nin durumu,, LED panelinde belirtilir. Cihaz PCM LED paneli altı LED 'e sahiptir. Bu LED 'ler, güç kaynağının ve fanı 'nin durumunu görüntüler. Kalan iki LED 'i, PCM 'de yedekleme pil modülünün durumunu gösterir. PCM 'nin durumunu öğrenmek için aşağıdaki tabloları kullanabilirsiniz.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Güç kaynağı ve fanı için PCM göstergesi LED 'Leri
| Durum | PCM OK (yeşil) | AC başarısız (hata) | Fan başarısız (bir hata) | DC başarısız (hata) |
| --- | --- | --- | --- | --- |
| AC gücü yok (e-kasa) |KAPALI |KAPALI |KAPALI |KAPALI |
| AC gücü yok (yalnızca bu PCM) |KAPALI |AÇIK |KAPALI |AÇIK |
| AC mevcut PCM-Tamam |AÇIK |KAPALI |KAPALI |KAPALI |
| PCM başarısız (fan başarısız) |KAPALI |KAPALI |AÇIK |Yok |
| PCM hatası (amp üzerinde, voltaj üzeri, güncel) |KAPALI |AÇIK |AÇIK |AÇIK |
| PCM (tolerans dışı fan) |AÇIK |KAPALI |KAPALI |AÇIK |
| Bekleme modu |Yanıp sönen |KAPALI |KAPALI |KAPALI |
| PCM üretici yazılımı indirme |KAPALI |Yanıp sönen |Yanıp sönen |Yanıp sönen |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Yedekleme pili için PCM göstergesi LED 'Leri
| Durum | Pil iyi (yeşil) | Pil hatası (bir hata) |
| --- | --- | --- |
| Pil yok |KAPALI |KAPALI |
| Pil var ve ücretlendirilir |AÇIK |KAPALI |
| Pil şarj veya bakım kesilmeleri |Yanıp sönen |KAPALI |
| Pil "geçici" hata (kurtarılabilir) |KAPALI |Yanıp sönen |
| Pil "Hard" hatası (kurtarılabilir değil) |KAPALI |AÇIK |
| Pili ayırıcı |Yanıp sönen |KAPALI |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>EBOD Kasası için PCM LED 'Leri
EBOD Kasası, 5 80W PCM ve ek pil yoktur. EBOD Kasası için PCM panelinde yalnızca güç kaynakları ve fanı için gösterge LED 'Leri vardır. Aşağıdaki çizimde bu LED 'ler gösterilmektedir.

   ![EBOD muhafazasında PCM LED 'Leri][3] 

PCM 'nin durumunu öğrenmek için aşağıdaki tabloyu kullanabilirsiniz.  

| Durum | PCM OK (yeşil) | AC başarısız (hata) | Fan başarısız (bir hata) | DC başarısız (hata) |
| --- | --- | --- | --- | --- |
| AC gücü yok (e-kasa) |KAPALI |KAPALI |KAPALI |KAPALI |
| AC gücü yok (yalnızca bu PCM) |KAPALI |AÇIK |KAPALI |AÇIK |
| AC mevcut PCM-Tamam |AÇIK |KAPALI |KAPALI |KAPALI |
| PCM başarısız (fan başarısız) |KAPALI |KAPALI |AÇIK |X |
| PCM hatası (amp üzerinde, voltaj üzeri, güncel |KAPALI |AÇIK |AÇIK |AÇIK |
| PCM (tolerans dışı fan) |AÇIK |KAPALI |KAPALI |AÇIK |
| Bekleme modeli |Yanıp sönen |KAPALI |KAPALI |KAPALI |
| PCM üretici yazılımı indirme |KAPALI |Yanıp sönen |Yanıp sönen |Yanıp sönen |

## <a name="controller-module-indicator-leds"></a>Denetleyici modülü göstergesi LED 'Leri
StorSimple cihazı, birincil denetleyicinin ve EBOD denetleyici modüllerinin LED 'lerini içerir.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Birincil denetleyicinin izleme LED 'Leri
Aşağıdaki çizim, birincil denetleyicideki LED 'Leri tanımlamanızı sağlar. (Tüm bileşenler yönlendirmeye yardımcı olmak için listelenir.)  

   ![İzleme LED 'Leri-birincil denetleyici][4]

Denetleyici modülünün doğru şekilde çalışıp çalışmadığını anlamak için aşağıdaki tabloyu kullanın.  

### <a name="controller-indicator-leds"></a>Denetleyici göstergesi LED 'Leri
| GELIŞTIRMESINE | Description |
| --- | --- |
| KIMLIK ışığı (mavi) |Modülün tanımlanmakta olduğunu gösterir. Mavi ışığı çalışan bir denetleyicide yanıp sönüğünde, denetleyici etkin denetleyici ve diğeri de bekleme denetleyicisidir. Daha fazla bilgi için bkz. [cihazınızdaki etkin denetleyiciyi tanımla](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Hata ışığı (bir hata) |Denetleyicideki bir hatayı gösterir. |
| Tamam ışığı (yeşil) |Sabit yeşil, denetleyicinin tamam olduğunu gösterir. Yanıp sönen yeşil, bir Controller VPD yapılandırma hatası gösterir. |
| SAS etkinlik LED 'Leri (yeşil) |Sabit yeşil, geçerli etkinlik olmayan bir bağlantıyı gösterir. Yanıp sönen yeşil, bağlantının devam eden etkinliği olduğunu gösterir. |
| Ethernet durumu LED 'Leri |Sağ tarafta bağlantı/ağ etkinliği: (sürekli yeşil) bağlantı etkin, (yanıp sönen yeşil) ağ etkinliği gösterilir. Sol taraf, ağ hızını gösterir: (sarı) 1000 MB/s, (yeşil) 100 MB/s ve (kapalı) 10 MB/sn. Bileşen modeline bağlı olarak, ağ arabirimi etkinleştirilmediği halde bu ışık yanıp sönmeyebilir. |
| Son LED 'Leri |Denetleyici açık olduğunda önyükleme ilerlemesini gösterir. StorSimple cihazı önyükleme yapamıyor, bu işlem, hatanın gerçekleştiği önyükleme işleminde noktayı belirlemek Microsoft Desteği yardımcı olur. |

> [!IMPORTANT]
> Hata ışığı aydınlatılmış ise denetleyiciyi yeniden başlatarak çözülebilen denetleyici modülüyle ilgili bir sorun vardır. Denetleyicinin yeniden başlatılması bu sorunu çözmezse lütfen Microsoft Desteği başvurun.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>EBOD için izleme LED 'Leri (EBOD Kasası)
6 GB/s SAS EBOD denetleyicilerinin her biri, aşağıdaki çizimde gösterildiği gibi durumunu gösteren LED 'Leri içerir.  

  ![İzleme LED 'Leri-EBOD Kasası][5]

EBOD denetleyici modülünün normal şekilde çalışıp çalışmadığını anlamak için aşağıdaki tabloyu kullanın.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD denetleyicisi modül göstergesi LED 'Leri
| Durum | G/ç modülü Tamam (yeşil) | G/ç modülü hatası (modulü) | Ana bilgisayar bağlantı noktası etkinliği (yeşil) |
| --- | --- | --- | --- |
| Denetleyici modülü Tamam |AÇIK |KAPALI |- |
| Denetleyici modülü hatası |KAPALI |AÇIK |- |
| Dış ana bilgisayar bağlantı noktası bağlantısı yok |- |- |KAPALI |
| Dış konak bağlantı noktası bağlantısı – etkinlik yok |- |- |AÇIK |
| Dış konak bağlantı noktası bağlantısı-etkinlik |- |- |Yanıp sönen |
| Denetleyici modülü meta verileri hatası |Yanıp sönen |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Birincil kutu ve EBOD Kasası için disk sürücüsü göstergesi LED 'Leri
StorSimple cihazının hem birincil hem de EBOD Kasası 'nda bulunan disk sürücüleri vardır. Her disk sürücüsü, bu bölümde açıklandığı gibi izleme göstergesi LED 'Leri içerir. 

Disk sürücüleri için, sürücü durumu yeşil bir LED ve her sürücü taşıyıcısı modülünün önüne takılmış bir kırmızı-bir ile belirtilir. Aşağıdaki çizimde bu LED 'ler gösterilmektedir.

  ![Disk sürücüsü LED 'Leri][6]

Her bir disk sürücüsünün durumunu öğrenmek için aşağıdaki tabloyu kullanın. bu durum, genel olarak ön panel LED durumunu da etkiler.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>EBOD Kasası için disk sürücüsü göstergesi LED 'Leri
| Durum | Etkinlik Tamam ışığı (yeşil) | Hata LED 'i (Red-bir) | İlişkili Ops paneli ışığı |
| --- | --- | --- | --- |
| Yüklü sürücü yok |KAPALI |KAPALI |Yok |
| Sürücü yüklendi ve çalışıyor |Etkinlik ile yanıp sönmeye açık/kapalı |X |Yok |
| SCSI kutu Hizmetleri (SES) cihaz kimliği kümesi |AÇIK |1 saniye açık/1 saniye kapalı |Yok |
| SES cihazı hata biti kümesi |AÇIK |AÇIK |Mantıksal hata (kırmızı) |
| Güç denetimi devre hatası |KAPALI |AÇIK |Modül hatası (kırmızı) |

## <a name="audible-alarms"></a>Duyulabilir alarmlar
StorSimple cihazı, hem birincil hem de EBOD kasası ile ilişkili duyulabilir alarmlar içerir. Duyulabilir bir alarm, her iki kasada ön panelde (Ops paneli olarak da bilinir) bulunur. Duyulabilir alarm bir hata koşulunun ne zaman olduğunu gösterir. Aşağıdaki koşullar alarmını etkinleştirececektir:  

* Fan hatası veya hatası
* Voltaj Aralık dışı
* Fazla veya daha fazla sıcaklık koşulu
* Sıcaklık taşması
* Sistem hatası
* Mantıksal hata
* Güç kaynağı hatası
* Güç soğutma modülünü (PCM) kaldırma  

Aşağıdaki tabloda çeşitli alarm durumları açıklanmaktadır.  

### <a name="alarm-states"></a>Uyarı durumları
| Uyarı durumu | Eylem | Sessiz düğmeye basıldığında gerçekleştirilecek eylem |
| --- | --- | --- |
| S0 |Normal mod: sessiz |İki kez bip sesi |
| S1 |Hata modu: 1 saniye/1 saniye kapalı |S2 veya S3 'e geçiş (bkz. notlar) |
| S2 |Anımsatma modu: aralıklı bip |Yok |
| S3 |Sessiz mod: sessiz |Yok |
| S4 |Kritik hata modu: sürekli alarm |Kullanılamıyor: sessiz etkin değil |

> [!NOTE]
> * Alarm durumu S1 'de, 2 dakika içinde sessize basmayın durumunda durum otomatik olarak S2 veya S3 ' e geçiş yapar.  
> * Uyarı durumları S1-S4 hata koşulu kaldırıldıktan sonra S0 'e geri döner.  
> * Kritik hata durumu S4, herhangi bir başka durumdan girilebilir.  


Ops panelinde susma düğmesine basarak duyulabilir Alarmın sesini kapatabilirsiniz. Sessiz anahtar el ile çalışıısında iki dakika sonra otomatik olarak kapatma gerçekleşmeyecektir. Alarmın sesi kapatıldığında, bir sorunun hala mevcut olduğunu göstermek için kısa süreli bip sesle sesle devam edecektir. Tüm sorunlar silindiğinde alarm sessizce olur.

Aşağıdaki tabloda çeşitli alarm koşulları açıklanmaktadır.

### <a name="alarm-conditions"></a>Uyarı koşulları
| Durum | Önem derecesi | Alarm | Ops paneli LED 'i |
| --- | --- | --- | --- |
| PCM uyarısı – tek bir PCM 'den DC gücü kaybı |Hata – artıklık kaybı yok |S1 |Modül hatası |
| PCM uyarısı – tek bir PCM 'den DC gücü kaybı |Hata – artıklık kaybı |S1 |Modül hatası |
| PCM fanı başarısız |Hata – artıklık kaybı |S1 |Modül hatası |
| SBB modülü PCM hatası algıladı |Dayanıklı |S1 |Modül hatası |
| PCM kaldırıldı |Yapılandırma hatası |Yok |Modül hatası |
| Kutu yapılandırma hatası |Hata – kritik |S1 |Modül hatası |
| Düşük uyarı sıcaklık uyarısı |Uyarı |S1 |Modül hatası |
| Yüksek uyarı sıcaklık uyarısı |Uyarı |S1 |Modül hatası |
| Sıcaklık üzeri alarm |Hata – kritik |S1 |Modül hatası |
| I2C veri yolu hatası |Hata – artıklık kaybı |S1 |Modül hatası |
| Ops panel iletişim hatası (ı2C) |Hata – kritik |S1 |Modül hatası |
| Denetleyici hatası |Hata – kritik |S1 |Modül hatası |
| SBB arabirim modülü hatası |Hata – kritik |S1 |Modül hatası |
| SBB Interface modülü hatası – hiçbir çalışan modül kaldı |Hata – kritik |S4 |Modül hatası |
| SBB arabirim modülü kaldırıldı |Uyarı |Yok |Modül hatası |
| Sürücü güç denetimi hatası |Uyarı – sürücü gücü kaybı yok |S1 |Modül hatası |
| Sürücü güç denetimi hatası |Hata – kritik; Sürücü gücü kaybı |S1 |Modül hatası |
| Sürücü kaldırıldı |Uyarı |Yok |Modül hatası |
| Yeterli güç yok |Uyarı |yok |Modül hatası |

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple donanım bileşenleri ve durumu](storsimple-8000-monitor-hardware-status.md)hakkında daha fazla bilgi edinin.

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


