---
title: Gerekli gereçlerini tanımlama
description: IoT sensörlerine ve şirket içi yönetim konsoluna yönelik sertifikalı Defender için donanım ve sanal gereçler hakkında bilgi edinin.
ms.date: 01/13/2021
ms.topic: how-to
ms.openlocfilehash: 242e88e92d6197fd018c56fa55a4dd8166f5d027
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782767"
---
# <a name="identify-required-appliances"></a>Gerekli gereçlerini tanımlama

Bu makalede, IoT algılayıcı gereçlerine yönelik sertifikalı Defender hakkında bilgi sağlanır. Defender Fort IoT, fiziksel ve sanal gereçlere dağıtılabilir.

Bu, yazılımın zaten yüklü olduğu, *önceden yapılandırılmış* sertifikalı gereçlerden ve gerekli yazılımları indirebileceğiniz ve yükleyebileceğiniz, yapılandırılmamış sertifikalı gereçlerden de oluşur.

Makale Ayrıca, şirket içi yönetim konsolu gereci için özellikler de sağlar. Şirket içi Yönetim Konsolu önceden yapılandırılmış bir gereç olarak kullanılamaz.

- Önceden yapılandırılmış bir algılayıcı satın almak istiyorsanız, [algılayıcı gereçlerinin](#sensor-appliances) bölümünde bulunan modelleri gözden geçirin ve satın alma işlemine devam edin.

- Kendi gerecinizi satın almak istiyorsanız, [algılayıcı gereçlerinin](#sensor-appliances) bölümünde ve [ek sertifikalı gereçlerdeki](#additional-certified-appliances) kullanılabilir modelleri gözden geçirin. Gereci aldıktan sonra yazılımı indirebilir ve yükleyebilirsiniz.

- Şirket içi yönetim konsolunu satın almak istiyorsanız şirket [içi yönetim konsolu gereç](#on-premises-management-console-appliance) bölümündeki bilgileri gözden geçirin. Cihazı aldıktan sonra yazılımı indirebilir ve yükleyebilirsiniz.

Buradaki görevleri tamamladıktan sonra, yazılımı yükleyebilir ve ağınızı kurabilirsiniz.

## <a name="sensor-appliances"></a>Algılayıcı gereçlerinin

IoT için Defender hem fiziksel hem de sanal dağıtımları destekler.

### <a name="physical-sensors"></a>Fiziksel algılayıcılar

Bu bölümde, kullanılabilir fiziksel algılayıcı modelleriyle ilgili bir genel bakış sunulmaktadır. Önceden yapılandırılmış yazılımlar veya önceden yapılandırılmış bir satın alma sensörle sensörlerle satın alabilirsiniz.

| Dağıtım türü | Kurumsal | Kurumsal | SMB raf bağlama| SMB rugkanlı|
|--|--|--|--|--|
| Görüntü | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Kurumsal düzeyde model."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Kurumsal düzeyde model."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="SMB düzeyi modeli."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="SMB-ruggesıed Level modeli."::: |
| Modelleme | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| İzleme bağlantı noktaları | En fazla 15 RJ45 veya 8 OPT | En fazla 8 RJ45 veya 6 OPT | 4 RJ45 | En fazla 5 |
| Maksimum bant genişliği [1](#anchortext) | 3 GB/sn | 1 GB/sn | 200 MB/sn | 100 MB/sn |
| En fazla korumalı cihaz | 30.000 | 15.000 | 1.000 | 800 |

Satıcı ayrıntıları için bkz. [gereç belirtimleri](#appliance-specifications) .

Önceden yapılandırılmış algılayıcılar hakkında: Microsoft, önceden yapılandırılmış algılayıcılar sağlamak için ok ile işbirliği yaptı. Önceden yapılandırılmış bir algılayıcı satın almak için aşağıdaki adreste iletişim oku: <hardware.sales@arrow.com>

Kendi gerecinizi getirme hakkında: burada açıklanan desteklenen modelleri gözden geçirin. Gerecinizi aldıktan sonra,   >  yazılımı indirmek için IoT **ağ sensörler ISO**  >  **yüklemesi** için Defender ' a gidin.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Ağ sensörleri ISO.":::

<a id="anchortext">1</a> bant genişliği kapasitesi, protokollerin dağıtımına bağlı olarak farklılık gösterebilir.

### <a name="virtual-sensors"></a>Sanal algılayıcılar

Bu bölüm, kullanılabilir sanal sensörlerden bir genel bakış sağlar.

| Dağıtım türü | Kurumsal | Kurumsal | SMB |
|--|--|--|--|
| Maksimum bant genişliği | 2,5 GB/sn | 800 MB/sn | 160 MB/sn |
| En fazla korumalı cihaz | 30.000 | 10,000 | 2,500 |

## <a name="on-premises-management-console-appliance"></a>Şirket içi yönetim konsolu gereci

Yönetim Konsolu, sanal dağıtım olarak kullanılabilir.

| Dağıtım türü | Kurumsal |
|--|--|
| Gereç türü | HPE DL20, VM |
| Yönetilen algılayıcı sayısı | 300 kadar |

Şirket içi yönetim konsolunu aldıktan sonra, ISO 'yı indirmek üzere **IoT**  >  **Şirket içi yönetim konsolu**  >  **ISO yüklemesi** için Defender ' a gidin.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Şirket içi yönetim konsolu.":::

## <a name="appliance-specifications"></a>Gereç belirtimleri

Bu bölümde, aşağıdaki gereçlere yönelik donanım belirtimleri açıklanmaktadır:

- Şirket dağıtımı: HPE ProLiant DL360

- Kurumsal Dağıtım: HPE ProLiant DL20

- SMB dağıtımı: HPE ProLiant DL20

- Sanal Gereç belirtimleri

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Şirket dağıtımı: HPE ProLiant DL360

| Bileşen | Teknik özellikler |
|--|--|
| Kasa | 1U raf sunucusu |
| Boyutlar | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(içinde) |
| Ağırlık | Maksimum 16,27 kg (35,86 lb) |
| İşlemci | Intel Xeon gümüş 4215 R 3,2 GHz, 11M önbellek, 8C/16T, 130 W |
| Ç | Intel C621 |
| Bellek | 32 GB = 2 x 16-GB 2666MT/s DDR4 ECC UDıMM |
| Depolama | Hot-Plug sabit sürücüde 6 x 1,2-TB SAS 12G Enterprise 10.000 SFF (2,5)-RAID 5 |
| Ağ denetleyicisi | Yerleşik: 2 x 1 GB Broadcom BCM5720<br>Yerleşik LOM: Idrac bağlantı noktası kartı 1-GB Broadcom BCM5720<br><br>Harici: 1 x Intel Ethernet i350 QP 1-GB sunucu bağdaştırıcısı, düşük profil |
| Yönetim | HPE iLO gelişmiş |
| Cihaz erişimi | İki arka USB 3,0<br>Bir ön USB 2,0<br>Bir adet iç USB 3,0 |
| Güç | 2 x HPE 500 W esnek yuva Platinum Hot plug düşük Hagen güç kaynağı seti |
| Raf desteği | HPE 1U Gen10 SFF kolay yüklenen demiryolu seti |

### <a name="appliance-bom"></a>Gereç ürün ağacı

| DÖNÜŞTÜRME | Description | Miktar |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO sunucusu | 1 |
| P19766-B21 | Avrupa-çok dilli yerelleştirme | 1 |
| P24479-L21 | DL360 G10 için Intel Xeon-S 4215 R FIO seti | 1 |
| P24479-B21 | DL360 Gen10 için Intel Xeon-S 4215 R Kit | 1 |
| P00922-B21 | HPE 16-GB 2Rx8 PC4-2933Y-R Smart Kit | 2 |
| 872479-B21 | HPE 1,2-TB SAS 10.000 SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p BASE-T I350 bağdaştırıcısı | 1 |
| P02377-B21 | HPE akıllı karma Kapaıtor w \_ 145 mm kablosu | 1 |
| 804331-B21 | HPE akıllı dizi P408i-bir SR Gen10 denetleyicisi | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 bağdaştırıcısı | 1 |
| 871244-B21 | HPE DL360 Gen10 yüksek performanslı fan seti | 1 |
| 865408-B21 | HPE 500-W FS Plat Hot plug LH güç kaynağı seti | 2 |
| 512485-B21 | HPE ILO adv 1-sunucu lisansı 1 yıl desteği | 1 |
| 874543-B21 | HPE 1U Gen10 SFF kolay yüklenen demiryolu seti | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Kurumsal Dağıtım: HPE ProLiant DL20

| Bileşen | Teknik özellikler |
|--|--|
| Kasa | 1U raf sunucusu |
| Boyutlar (yükseklik x Genişlik x derinlik) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 inç |
| Ağırlık | 7,9 kg/17.41 lb |
| İşlemci | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Ç | Intel C242 |
| Bellek | 2 x 16 GB çift sıra x8 DDR4-2666 |
| Depolama | 3 x 1-TB SATA 6G orta çizgi 7,2 K SFF (2,5) – RAID 5 Smart Array P408i-a SR Controller |
| Ağ denetleyicisi | Panoda: 2 x 1 GB <br>Yerleşik: ILO bağlantı noktası kartı 1 GB <br>Harici: 1 x HPE Ethernet 1-GB 4-bağlantı noktası 366FLR bağdaştırıcısı |
| Yönetim | HPE iLO gelişmiş |
| Cihaz erişimi | Ön: 1 x USB 3,0, 1 x USB ILO hizmet bağlantı noktası <br>Arka: 2 x USB 3,0 <br>Dahili: 1 x USB 3,0 |
| Güç | Çift Hot plug güç kaynakları 500 W |
| Raf desteği | HPE 1U kısa Friction demiryolu Seıl seti |

### <a name="appliance-bom"></a>Gereç ürün ağacı

| DÖNÜŞTÜRME | Açıklama: yüksek uç | Miktar |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO sunucusu | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO sunucusu | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO seti | 1 |
| 879507-B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND Kit | 2 |
| 655710-B21 | HPE 1-TB SATA 7,2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOMYÜKSELTICI seti | 1 |
| 665240-B21 | HPE Ethernet 1-GB 4-bağlantı noktası 366FLR bağdaştırıcısı | 1 |
| 782961-B21 | HPE 12-W Akıllı Depolama pili | 1 |
| 869081-B21 | HPE akıllı dizi P408i-a SR G10 LH Controller | 1 |
| 865408-B21 | HPE 500-W FS Plat Hot plug LH güç kaynağı seti | 2 |
| 512485-B21 | HPE ILO adv 1-sunucu lisansı 1 yıl desteği | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS etkinleştirme FIO seti | 1 |
| 775612-B21 | HPE 1U kısa Friction demiryolu Seıl seti | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB dağıtımı: HPE ProLiant DL20

| Bileşen | Teknik özellikler |
|--|--|
| Kasa | 1U raf sunucusu |
| Boyutlar (yükseklik x Genişlik x derinlik) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 inç |
| Ağırlık | 7,88 kg/17.37 lb |
| İşlemci | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Ç | Intel C242 |
| Bellek | 1 x 8 GB çift sıra x8 DDR4-2666 |
| Depolama | 2 x 1-TB SATA 6G orta çizgi 7,2 K SFF (2,5) – RAID 1 Smart Array P208i-a |
| Ağ denetleyicisi | Panoda: 2 x 1 GB <br>Yerleşik: ILO bağlantı noktası kartı 1 GB <br>Harici: 1 x HPE Ethernet 1-GB 4-bağlantı noktası 366FLR bağdaştırıcısı |
| Yönetim | HPE iLO gelişmiş |
| Cihaz erişimi | Ön: 1 x USB 3,0, 1 x USB ILO hizmet bağlantı noktası <br>Arka: 2 x USB 3,0 <br>Dahili: 1 x USB 3,0 |
| Güç | Etkin tak güç kaynağı 290 W |
| Raf desteği | HPE 1U kısa Friction demiryolu Seıl seti |

### <a name="appliance-bom"></a>Gereç ürün ağacı

| DÖNÜŞTÜRME | Description | Miktar |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO sunucusu | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO sunucusu | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO seti | 1 |
| 879505-B21 | HPE 8 GB 1Rx8 PC4-2666V-E STND Kit | 1 |
| 801882-B21 | HPE 1-TB SATA 7,2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOMYÜKSELTICI seti | 1 |
| 665240-B21 | HPE Ethernet 1-GB 4-bağlantı noktası 366FLR bağdaştırıcısı | 1 |
| 869079-B21 | HPE akıllı dizi E208i-a SR G10 LH Controller | 1 |
| P21649-B21 | HPE DL20 Gen10 Plat 290 W FIO PSU seti | 1 |
| P06683-B21 | HPE DL20 Gen10 d. 2 SATA/LFF AROC kablo seti | 1 |
| 512485-B21 | HPE ILO adv 1-sunucu lisansı 1 yıl desteği | 1 |
| 775612-B21 | HPE 1U kısa Friction demiryolu Seıl seti | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB Rugged: HPE Edgeline EL300

| Bileşen | Teknik özellikler |
|--|--|
| İnşaat | Alumum, daha az & toz-prova tasarımı |
| Boyutlar (yükseklik x Genişlik x derinlik) | 200.5 mm (7,9 ") yüksekliğinde, 232mm (9,14") Genişlik, 100 mm (3,9 ") derin |
| Ağırlık | 4,91 KG (10,83 lbs.) |
| CPU | Intel Core i7-8650U (1.9 GHz/4-çekirdek/15W) |
| Ç | Intel® Q170 platform denetleyicisi hub 'ı |
| Bellek | 8DDR4 2133MHz Wide sıcaklık SODıMM |
| Depolama | 128GB 3ME3 geniş sıcaklık mSATA SSD |
| Ağ denetleyicisi | Intel® I219 'e göre 6x Gigabit Ethernet bağlantı noktaları |
| Cihaz erişimi  | 4 USBs: 2 ön yüz; 2 yeniden Vana; 1 iç |
| Güç bağdaştırıcısı | 250V/10A |
| Takarak | Bağlama kiti, DIN |
| İşletim sıcaklığı | 0C 'den + 70C  |
| Nem oranı | %10, %90, yoğunlaşmayan |
| Titreşimi | 0,3 grms 10Hz ila 300Hz, eksen başına 15 dakika-DIN demiryolu   |
| Şok | 10G 10ms, yarım sinüs, her eksen için üç. (Pozitif & negatif Pulse) – DIN |

### <a name="appliance-bom"></a>Gereç ürün ağacı
| Ürün | Description |
|--|--|
| P25828-B21 | HPE Edgeline EL300 v2 yakınsanmış uç sistemi |
| P25828-B21 B19 | HPE EL300 v2 Yakınsanan uç sistemi |
| P25833-B21 | Intel Core i7-8650U (1.9 GHz/4-Core/15W) HPE Edgeline EL300 için FIO temel Işlemci seti |
| P09176-B21 | HPE Edgeline 8GB (1X8s) Dual Rank x8 DDR4-2666 SODıMM WT CAS-19-19-19 kayıtlı bellek FIO kiti |
| P09188-B21 | HPE Edgeline 256GB SATA 6G yoğun okuma yoğun M. 2 2242 3yr WTY geniş geçici SSD |
| P04054-B21 | HPE Edgeline EL300 SFF-d. 2 etkinleştirme paketi |
| P08120-B21 | HPE Edgeline EL300 12VDC FIO aktarma panosu |
| P08641-B21 | HPE Edgeline EL300 80W 12VDC güç kaynağı |
| AF564A | HPE C13-SI-32 Il 250V 10Amp 1.83 m güç kablosu |
| P25835-B21 | HPE EL300 v2 FIO taşıyıcı panosu |
| R1P49AAE | HPE EL300 ISM adv 3 yıl 7/24 Sup_Upd E-LTU |
| P08018-B21 isteğe bağlı | HPE Edgeline EL300 düşük profil ayracı kiti  |
| P08019-B21 isteğe bağlı | HPE Edgeline EL300 DIN demiryolu seti |
| P08020-B21 isteğe bağlı | HPE Edgeline EL300 duvar bağlama seti |
| P03456-B21 isteğe bağlı | HPE Edgeline 1GbE 4-bağlantı noktası TSN FIO dişi kartı |

## <a name="virtual-appliance-specifications"></a>Sanal Gereç belirtimleri

### <a name="sensors"></a>Sensörlerden

| Tür | Kurumsal | Kurumsal | SMB |
|--|--|--|--|
| Sanal işlemci | 32 | 8 | 4 |
| Bellek | 32 GB | 32 GB | 8 GB |
| Depolama | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Şirket içi yönetim konsolu gereci

| Tür | Kurumsal |
|--|--|
| Description | Kurumsal Dağıtım türleri için Sanal Gereç |
| Sanal işlemci | 8 |
| Bellek | 32 GB |
| Depolama | 1,8 TB |

Desteklenen hiper Yöneticiler: VMware ESXi sürüm 5,0 ve üzeri, Hyper-V

## <a name="additional-certified-appliances"></a>Ek sertifikalı gereçler

Bu bölümde, Microsoft tarafından sertifikalı ancak önceden yapılandırılmış gereçler olarak sunulmayan ek gereçler ayrıntıları sağlanır.

| Dağıtım türü | Kurumsal |
|--|--|
| Görüntü | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Kurumsal Dağıtım türü."::: |
| Modelleme | Dell PowerEdge R340 XL |
| İzleme bağlantı noktaları | En fazla dokuz RJ45 veya altı OPT |
| En fazla bant genişliği [1](#anchortext2)| 1 GB/sn |
| En fazla korumalı cihaz | 10,000 |

<a id="anchortext2">Bir</a> Bant genişliği kapasitesi, protokoller dağıtımına bağlı olarak farklılık gösterebilir.

Gereci satın aldıktan sonra,   >  yazılımı indirmek için IoT **ağ sensör ISO**  >  **yüklemesi** Defender ' a gidin.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Ağ sensörleri ISO.":::

## <a name="next-steps"></a>Sonraki adımlar

[IoT yüklemesi için Azure Defender hakkında](how-to-install-software.md)

[IoT Ağ kurulumu için Azure Defender hakkında](how-to-set-up-your-network.md)

