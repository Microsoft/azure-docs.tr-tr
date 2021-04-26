---
title: StorSimple 8000 serisi donanım bileşenleri ve durumu | Microsoft Docs
description: StorSimple cihazınızın donanım bileşenlerini StorSimple Device Manager hizmeti aracılığıyla nasıl izleyeceğinizi öğrenin.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960368"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Donanım bileşenlerini ve durumunu izlemek için StorSimple Device Manager hizmetini kullanın

## <a name="overview"></a>Genel Bakış
Bu makalede, şirket içi StorSimple 8000 serisi cihazındaki çeşitli fiziksel ve mantıksal bileşenler açıklanmaktadır. Ayrıca, StorSimple Device Manager hizmetindeki **durum ve donanım sistem** durumu dikey penceresini kullanarak cihaz bileşeni durumunun nasıl izleneceğini de açıklar.

**Durum ve donanım durumu** dikey penceresi, tüm StorSimple cihaz bileşenlerinin donanım durumunu gösterir.

8100 için bileşen listesi altında, aşağıdakileri tanımlayan üç bölüm vardır:

* **Paylaşılan bileşenler** : bunlar; disk sürücüleri, kutu, güç ve soğutma modülü (PCM) BILEŞENLERI ve PCM sıcaklığı, hat gerilimi ve satır geçerli algılayıcılar gibi denetleyicilerin bir parçası değildir.
* **Controller 0 bileşenleri** – DENETLEYICI, SAS Genişleticisi ve bağlayıcı, denetleyici sıcaklık algılayıcıları ve çeşitli ağ arabirimleri gibi denetleyici 0 üzerinde bulunan bileşenler.
* **Denetleyici 1 bileşenleri** : denetleyici 1 ' i oluşturan bileşenler, denetleyici 0 için ayrıntılananlara benzer.

8600 cihazda, uzatılmış disk (EBOD) Kasası 'na karşılık gelen ek bileşenler bulunur. Bileşen listesi altında beş bölüm vardır. Bunlar, birincil muhafazadaki bileşenleri içeren üç bölüm vardır ve 8100 ' de açıklananlarla aynıdır. Şu şekilde tanımlayan EBOD Kasası için iki ek bölüm vardır:

* **Ebod denetleyicisi 0 bileşenleri** : ebod DENETLEYICISI, SAS Genişleticisi ve bağlayıcı ve denetleyici sıcaklık algılayıcıları gibi ebod Muhafazası 0 üzerinde bulunan bileşenler.
* **Ebod denetleyicisi 1 bileşenleri** – ebod Kasası 0 ' ı için ayrıntılandıranlara benzer şekılde, ebod kutu 1 ' i oluşturan bileşenler.
* **Ebod Kasası paylaşılan bileşenleri** : ebod denetleyicisinin parçası olmayan ebod KASASı ve PCM 'de bulunan bileşenler.

> [!NOTE]
> **StorSimple Cloud Appliance (8010/8020) için donanım durumu kullanılamıyor.**


## <a name="monitor-the-hardware-status"></a>Donanım durumunu izleme
Bir cihaz bileşeninin donanım durumunu görüntülemek için aşağıdaki adımları gerçekleştirin:

1. **Cihazlar**' a gidin, belirli bir StorSimple cihazı seçin. **> donanım durumunu izle**' ye gidin.

    ![Device Manager hizmetinin ayarlar dikey penceresini gösteren ekran görüntüsü. Izleyici altında, donanım durumu seçilidir.](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. **Donanım bileşenleri** bölümünü bulun ve kullanılabilir bileşenler ' i seçin. Bileşen etiketine tıklayarak listeyi genişletin ve çeşitli cihaz bileşenlerinin durumunu görüntüleyebilirsiniz. [Birincil kasa için ayrıntılı bileşen listesini](#component-list-for-primary-enclosure-of-storsimple-device) ve [ebod Kasası için ayrıntılı bileşen listesini](#component-list-for-ebod-enclosure-of-storsimple-device)görüntüleyin.

    ![Donanım bileşenleri bölümünde seçilen bir bileşeni gösteren ekran görüntüsü. Bitişik dikey pencerede, bu bileşenin bölümlerinin adı ve durumu gösterilir.](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Bileşen durumunu yorumlamak için aşağıdaki renk kodlama şemasını kullanın:
   
   * **Yeşil onay** : **Tamam** durumu ile sağlıklı bir bileşen gösterir.
   * **Sarı** – **Uyarı** durumunda düşürülmüş bir bileşeni gösterir.
   * **Red ünlem** : **hata** durumu olan başarısız bir bileşeni gösterir.
   * **Siyah metinle beyaz** : mevcut olmayan bir bileşeni gösterir.
   
   Aşağıdaki ekran görüntüsünde, **Tamam**, **Uyarı** ve **hata** durumunda bileşenleri olan bir cihaz gösterilmektedir.
       
   ![Donanım bileşenleri bölümünde üç bileşeni gösteren ekran görüntüsü: biri sorunsuz durumda, biri başarısız durumunda ve diğeri de uyarı durumunda.](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   **Paylaşılan bileşenler listesini** GENIŞLETEREK, NVRAM 'ın ve kümenin düşürülmüş olduğunu görebiliriz.

   ![Seçili paylaşılan bileşenler öğesini gösteren ekran görüntüsü. Bitişik dikey pencerede N V R A M ve küme bir uyarı durumundadır.](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   **Denetleyici 1 bileşenleri** listesini genişleterek, küme düğümünün başarısız olduğunu görebiliriz.  

   ![Denetleyici 1 bileşenleri öğesinin seçili olduğunu gösteren ekran görüntüsü. Bitişik dikey pencerede, küme düğümü hatalı durumda.](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. **Sağlıklı** durumda olmayan bir bileşenle karşılaşırsanız Microsoft desteği başvurun. Cihazınızda uyarılar etkinse, bir e-posta uyarısı alırsınız. Hatalı bir donanım bileşenini değiştirmeniz gerekiyorsa bkz. [StorSimple donanım bileşeni değiştirme](./storsimple-8000-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>StorSimple cihazının birincil Kasası için bileşen listesi
Aşağıdaki tabloda, şirket içi StorSimple cihazınızın birincil kasasında (hem 8100 hem de 8600 ' de bulunur) bulunan fiziksel ve mantıksal bileşenler özetlenmektedir.

| Bileşen | Modül | Tür | Konum | Alan değiştirilebilir birim (FRU)? | Description |
| --- | --- | --- | --- | --- | --- |
| Yuva içindeki sürücü [0-11] |Disk sürücüleri |Fiziksel |Paylaşılan |Yes |Birincil kasadaki her SSD veya HDD sürücüsü için bir satır sunulur. |
| Çevresel sıcaklık algılayıcısı |Kutu |Fiziksel |Paylaşılan |No |Kasadaki sıcaklığı ölçer. |
| Orta düzlem sıcaklık algılayıcısı |Kutu |Fiziksel |Paylaşılan |No |Orta düzlemin sıcaklığını ölçer. |
| Duyulabilir alarm |Kutu |Fiziksel |Paylaşılan |No |Kasa içindeki duyulabilir alarm alt sisteminin işlevsel olup olmadığını gösterir. |
| Kutu |Kutu |Fiziksel |Paylaşılan |Yes |Bir gövdenin varlığını gösterir. |
| Kutu ayarları |Kutu |Fiziksel |Paylaşılan |No |Kasanın ön paneline başvurur. |
| Hat gerilimi sensörleri |PCM |Fiziksel |Paylaşılan |No |Sayısız hat gerilimi algılayıcılarının durumu görüntülenir ve bu, ölçülen voltaj tolerans dahilinde olup olmadığını gösterir. |
| Geçerli algılayıcılar satırı |PCM |Fiziksel |Paylaşılan |No |Çok sayıda satır geçerli sensörlerden biri görüntülenir, bu da ölçülen geçerli, tolerans dahilinde olup olmadığını gösterir. |
| PCM 'de sıcaklık algılayıcıları |PCM |Fiziksel |Paylaşılan |No |Giriş ve etkin nokta algılayıcıları gibi çok sayıda sıcaklık algılayıcıları, ölçülen sıcaklığın tolerans dahilinde olup olmadığını belirten durumlarını gösterir. |
| Güç kaynağı [0-1] |PCM |Fiziksel |Paylaşılan |Yes |Cihazın arkasında bulunan iki PCMs içindeki her güç kaynağı için bir satır sunulur. |
| Soğutma [0-1] |PCM |Fiziksel |Paylaşılan |Yes |İki PCMs 'de bulunan dört Soğutma fanının her biri için bir satır sunulur. |
| Pil [0-1] |PCM |Fiziksel |Paylaşılan |Yes |PCM 'de dağıtılmış olan her yedekleme pil modülü için bir satır sunulur. |
| METIS |Yok |Mantıksal |Paylaşılan |Yok |Pillerin durumunu görüntüler: şarj gerekip gerekmediğini ve yaşam sonuna yaklaşmayı ister. |
| Küme |Yok |Mantıksal |Paylaşılan |Yok |İki tümleşik denetleyici modülü arasında oluşturulan kümenin durumunu görüntüler. |
| Küme düğümü |Yok |Mantıksal |Paylaşılan |Yok |Denetleyicinin, kümenin bir parçası olarak durumunu gösterir. |
| Küme çekirdeği |Yok |Mantıksal | |Yok |HDD depolama havuzundaki çoğunluk disk üyeliğinin varlığını gösterir. |
| HDD veri alanı |Yok |Mantıksal |Paylaşılan |Yok |Sabit disk sürücüsü (HDD) depolama havuzundaki veriler için kullanılan depolama alanı. |
| HDD yönetim alanı |Yok |Mantıksal |Paylaşılan |Yok |Yönetim görevleri için HDD depolama havuzunda ayrılmış alan. |
| HDD çekirdek alanı |Yok |Mantıksal |Paylaşılan |Yok |Küme çekirdeği için HDD depolama havuzunda ayrılmış alan. |
| HDD değiştirme alanı |Yok |Mantıksal |Paylaşılan |Yok |Denetleyiciyi değiştirme için HDD depolama havuzunda ayrılmış alan. |
| SSD veri alanı |Yok |Mantıksal |Paylaşılan |Yok |Katı hal sürücüsü (SSD) depolama havuzundaki veriler için kullanılan depolama alanı. |
| SSD NVRAM alanı |Yok |Mantıksal |Paylaşılan |Yok |SSD depolama havuzundaki, NVRAM Logic için ayrılmış depolama alanı. |
| HDD depolama havuzu |Yok |Mantıksal |Paylaşılan |Yok |Cihaz HDD 'lerden oluşturulan mantıksal depolama havuzunun durumunu görüntüler. |
| SSD depolama havuzu |Yok |Mantıksal |Paylaşılan |Yok |Cihaz SSD 'lerden oluşturulan mantıksal depolama havuzunun durumunu görüntüler. |
| Denetleyici [0-1] [durum] |G/Ç |Fiziksel |Denetleyici |Yes |Denetleyicinin durumunu ve kasadaki etkin veya bekleme modunda olup olmadığını gösterir. |
| Denetleyicideki sıcaklık algılayıcıları |G/Ç |Fiziksel |Denetleyici |No |G/ç modülü, CPU sıcaklık, DıMM ve PCIe algılayıcıları gibi çok sayıda sıcaklık algılayıcıları, karşılaştığı sıcaklığın tolerans dahilinde olup olmadığını belirten bir durum görüntülenir. |
| SAS Genişleticisi |G/Ç |Fiziksel |Denetleyici |No |Tümleşik depolamayı denetleyiciye bağlamak için kullanılan seri ekli SCSI (SAS) genişletic'nın durumunu gösterir. |
| SAS Bağlayıcısı [0-1] |G/Ç |Fiziksel |Denetleyici |No |Her SAS bağlayıcısının, tümleşik depolamayı SAS genişleticiye bağlamak için kullanılan durumunu gösterir. |
| SBB Orta düzlem bağlantısı |G/Ç |Fiziksel |Denetleyici |No |Her denetleyiciyi orta düzleme bağlamak için kullanılan orta düzlem bağlayıcısının durumunu gösterir. |
| İşlemci çekirdeği |G/Ç |Fiziksel |Denetleyici |No |Her denetleyici içindeki işlemci çekirdekleri durumunu gösterir. |
| Kasa elektronik gücü |G/Ç |Fiziksel |Denetleyici |No |Kasa tarafından kullanılan güç sisteminin durumunu gösterir. |
| Çevre kutusu elektronik tanılama |G/Ç |Fiziksel |Denetleyici |No |Denetleyici tarafından verilen tanılama alt sistemlerinin durumunu gösterir. |
| Temel Kart Yönetim Denetleyicisi (BMC) |G/Ç |Fiziksel |Denetleyici |No |Donanım cihazını algılayıcıları aracılığıyla izleyen ve bağımsız bir bağlantı aracılığıyla sistem yöneticisiyle iletişim kuran özel bir hizmet işlemcisi olan temel kart yönetim denetleyicisi 'nin (BMC) durumunu gösterir. |
| Ethernet |G/Ç |Fiziksel |Denetleyici |No |Ağ arabirimlerinin her birinin durumunu, diğer bir deyişle, denetleyicide belirtilen yönetim ve veri bağlantı noktalarını gösterir. |
| SORGULAMAK |G/Ç |Fiziksel |Denetleyici |No |Güç kesintisi durumunda uygulamanın kritik bilgilerini tutmaya yönelik, pil tarafından yedeklenen, geçici olmayan bir rastgele erişim belleği olan NVRAM 'ın durumunu gösterir. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>StorSimple cihazının EBOD Kasası için bileşen listesi
Aşağıdaki tabloda, şirket içi StorSimple cihazınızın EBOD muhafazasının (yalnızca 8600 modelinde mevcuttur) bulunan fiziksel ve mantıksal bileşenleri özetlenmektedir.

| Bileşen | Modül | Tür | Konum | FRU? | Description |
| --- | --- | --- | --- | --- | --- |
| Yuva içindeki sürücü [0-11] |Disk sürücüleri |Fiziksel |Paylaşılan |Yes |EBOD Kasası 'nın önündeki her HDD sürücüsünün bir satırı sunulur. |
| Çevresel sıcaklık algılayıcısı |Kutu |Fiziksel |Paylaşılan |No |Kasadaki sıcaklığı ölçer. |
| Orta düzlem sıcaklık algılayıcısı |Kutu |Fiziksel |Paylaşılan |No |Orta düzlemin sıcaklığını ölçer. |
| Duyulabilir alarm |Kutu |Fiziksel |Paylaşılan |No |Kasa içindeki duyulabilir alarm alt sisteminin işlevsel olup olmadığını gösterir. |
| Kutu |Kutu |Fiziksel |Paylaşılan |Yes |Bir gövdenin varlığını gösterir. |
| Kutu ayarları |Kutu |Fiziksel |Paylaşılan |No |Kasanın OPS veya ön panelini ifade eder. |
| Hat gerilimi sensörleri |PCM |Fiziksel |Paylaşılan |No |Sayısız hat gerilimi algılayıcılarının durumu görüntülenir ve bu, ölçülen voltaj tolerans dahilinde olup olmadığını gösterir. |
| Geçerli algılayıcılar satırı |PCM |Fiziksel |Paylaşılan |No |Çok sayıda satır geçerli sensörlerden biri görüntülenir, bu da ölçülen geçerli, tolerans dahilinde olup olmadığını gösterir. |
| PCM 'de sıcaklık algılayıcıları |PCM |Fiziksel |Paylaşılan |No |Giriş ve etkin nokta algılayıcıları gibi çok sayıda sıcaklık algılayıcıları, ölçülen sıcaklığın tolerans dahilinde olup olmadığını gösteren durumunun görüntülenmesini ister. |
| Güç kaynağı [0-1] |PCM |Fiziksel |Paylaşılan |Yes |Cihazın arkasında bulunan iki PCMs içindeki her güç kaynağı için bir satır sunulur. |
| Soğutma [0-1] |PCM |Fiziksel |Paylaşılan |Yes |İki PCMs 'de bulunan dört Soğutma fanının her biri için bir satır sunulur. |
| Yerel depolama [HDD] |Yok |Mantıksal |Paylaşılan |Yok |Cihaz HDD 'lerden oluşturulan mantıksal depolama havuzunun durumunu görüntüler. |
| Denetleyici [0-1] [durum] |G/Ç |Fiziksel |Denetleyici |Yes |EBOD modülündeki denetleyicilerin durumunu görüntüler. |
| EBOD içindeki sıcaklık algılayıcıları |G/Ç |Fiziksel |Denetleyici |No |Her denetleyicideki çok sayıda sıcaklık algılayıcıları, karşılaşılan sıcaklığın tolerans dahilinde olup olmadığını gösteren durumunun görüntülenmesini ister. |
| SAS Genişleticisi |G/Ç |Fiziksel |Denetleyici |No |Tümleşik depolamayı denetleyiciye bağlamak için kullanılan SAS genişletic'nın durumunu gösterir. |
| SAS Bağlayıcısı [0-2] |G/Ç |Fiziksel |Denetleyici |No |Her SAS bağlayıcısının, tümleşik depolamayı SAS genişleticiye bağlamak için kullanılan durumunu gösterir. |
| SBB Orta düzlem bağlantısı |G/Ç |Fiziksel |Denetleyici |No |Her denetleyiciyi orta düzleme bağlamak için kullanılan orta düzlem bağlayıcısının durumunu gösterir. |
| Kasa elektronik gücü |G/Ç |Fiziksel |Denetleyici |No |Kasa tarafından kullanılan güç sisteminin durumunu gösterir. |
| Çevre kutusu elektronik tanılama |G/Ç |Fiziksel |Denetleyici |No |Denetleyici tarafından verilen tanılama alt sistemlerinin durumunu gösterir. |
| Cihaz denetleyicisiyle bağlantı |G/Ç |Fiziksel |Denetleyici |No |EBOD g/ç modülü ile cihaz denetleyicisi arasındaki bağlantının durumunu gösterir. |

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple Device Manager hizmetini kullanarak cihazınızı yönettiğinizde, StorSimple cihazınızı yönetmek için StorSimple [Device Manager hizmetini kullanın](storsimple-8000-manager-service-administration.md)bölümüne gidin.
* Azaltılmış veya başarısız durumuna sahip bir cihaz bileşeninde sorun gidermeniz gerekiyorsa, [StorSimple izleme göstergeleri](storsimple-monitoring-indicators.md)' ne bakın.
* Hatalı bir donanım bileşenini değiştirmek için bkz. [StorSimple donanım bileşeni değiştirme](./storsimple-8000-hardware-component-replacement.md).
* Cihaz sorunlarını yaşamaya devam ederseniz [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md).