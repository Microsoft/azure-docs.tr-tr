---
title: Microsoft Azure Stack Edge Pro R teknik belirtimleri ve uyumluluğu | Microsoft Docs
description: Azure Stack Edge Pro R cihazınız için teknik belirtimler ve uyumluluk hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: 3b323bf920bd884e821d03bf2def37471775e720
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312714"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge Pro R teknik özellikleri

Azure Stack Edge Pro R cihazınızın donanım bileşenleri, bu makalede açıklanan teknik belirtimlere uyar. Teknik belirtimler, güç kaynağı birimlerini (PSUs), depolama kapasitesini, kasaları ve çevresel standartları anlatmaktadır.


## <a name="compute-memory-specifications"></a>İşlem, bellek belirtimleri

Azure Stack Edge Pro R cihazının işlem ve bellek için aşağıdaki belirtimleri vardır:

| Belirtim  | Değer                                             |
|----------------|---------------------------------------------------|
| CPU türü       | Çift Intel Xeon gümüş 4114 CPU                   |
| CPU: ham       | 20 toplam çekirdek, 40 toplam vCPU                    |
| CPU: kullanılabilir    | 32 vCPU                                          |
| Bellek türü    | Dell uyumlu 16 GB RDıMM, 2666 MT/s, Çift derece |
| Bellek: ham    | 256 GB RAM (16 x 16 GB)                           |
| Bellek: kullanılabilir | 230 GB RAM                                        |

## <a name="compute-acceleration-specifications"></a>İşlem hızlandırma belirtimleri

Kubernetes, derin öğrenme ve makine öğrenimi senaryolarına izin veren her cihaza bir grafik Işleme birimi (GPU) dahildir.

| Belirtim           | Değer                      |
|-------------------------|----------------------------|
| GPU   | Bir NVIDIA T4 GPU 'SU <br> Daha fazla bilgi için bkz. [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). | 

## <a name="power-supply-unit-specifications"></a>Güç kaynağı birimi belirtimleri

Azure Stack Edge Pro R cihazının yüksek performanslı fanlarla iki 100-240 V güç kaynağı birimi (PSUs) vardır. İki PSUs, yedekli bir güç yapılandırması sağlar. Bir PSU başarısız olursa, başarısız modül değiştirilinceye kadar cihaz normal olarak diğer PSU üzerinde çalışmaya devam eder. Aşağıdaki tabloda, PSUs teknik özellikleri listelenmektedir.

| Belirtim              | 550 W PSU                  |
|----------------------------|----------------------------|
| Maksimum çıkış gücü       | 550 W                      |
| Isı dağılımı (maksimum) | 2891 BTU/saat                |
| Sıklık                  | 50/60 Hz                   |
| Voltaj aralığı seçimi    | Otomatik olarak değişen: 115-230 V AC |
| Etkin takılabilir              | Yes                        |

## <a name="network-specifications"></a>Ağ belirtimleri

Azure Stack Edge Pro R cihazının dört ağ arabirimi vardır, PORT1-PORT4.


|Belirtim         |Açıklama                       |
|----------------------|----------------------------------|
|Ağ arabirimleri    |**2 x 1 GbE RJ45** <br> Bağlantı noktası 1, ilk kurulum için yönetim arabirimi olarak kullanılır ve varsayılan olarak statiktir. İlk kurulum tamamlandıktan sonra, herhangi bir IP adresine sahip veriler için arabirimi kullanabilirsiniz. Ancak, sıfırlama sırasında Arabirim statik IP 'ye geri döner. <br>Diğer arabirim, Kullanıcı tarafından yapılandırılabilen bağlantı noktası 2, veri aktarımı için kullanılabilir ve varsayılan olarak DHCP 'dir. |
|Ağ arabirimleri    |**2 x 25 GbE SFP28** <br> Bağlantı noktası 3 ve bağlantı noktası 4 ' te bu veri arabirimleri, DHCP (varsayılan) veya statik olarak yapılandırılabilir. |

Azure Stack Edge Pro R cihazınız aşağıdaki ağ donanımlarına sahiptir:

* **Mellanox Dual Port 25G ConnectX-4 kanal ağ bağdaştırıcısı** -bağlantı noktası 3 ve bağlantı noktası 4. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25 GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

Bu ağ kartlarında desteklenen kabloların, anahtarların ve alıcı sayısının tam listesi için, [Mellanox Dual Port 25G ConnectX-4 kanal ağ bağdaştırıcısı uyumlu ürünler](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)' e gidin.

## <a name="storage-specifications"></a>Depolama belirtimleri

Azure Stack Edge Pro R cihazlarında sekiz veri diski ve işletim sistemi diskleri olarak görev sunan iki adet M. 2 SATA diski vardır. Daha fazla bilgi için, [d. 2 SATA disklere](https://en.wikipedia.org/wiki/M.2)gidin.

#### <a name="storage-for-1-node-device"></a>1 düğümlü cihaz için depolama

Aşağıdaki tabloda, 1 düğümlü cihazın depolama kapasitesi için ayrıntılar bulunur.

|     Belirtim                          |     Değer             |
|--------------------------------------------|-----------------------|
|    Katı hal sürücüleri (SSD 'Ler) sayısı     |    8                  |
|    Tek SSD kapasitesi                     |    8 TB               |
|    Toplam kapasite                          |    64 TB              |
|    Toplam kullanılabilir kapasite *                  |    ~ 42 TB            |

**Bazı boşluklar iç kullanım için ayrılmıştır.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Kutu boyutları ve ağırlık belirtimleri

Aşağıdaki tablolarda, Boyutlar ve ağırlığa yönelik çeşitli muhafaza belirtimleri listelenmektedir.

### <a name="enclosure-dimensions"></a>Kutu boyutları 

Aşağıdaki tabloda, milimetre ve inç 'de Rugged büyük/küçük harfe sahip cihaz boyutları ve UPS listelenmiştir.

|     Kutu     |     Milimetre     |     Cm     |
|-------------------|---------------------|----------------|
|    Height         |    301,2            |    11,86       |
|    Width          |    604,5            |    23,80       |
|    Uzunluk         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Kasa ağırlığı 

Cihazın ağırlığı, kutunun yapılandırmasına bağlıdır.

|     Kutu                                 |     Ağırlık          |
|-----------------------------------------------|---------------------|
|    Son büyük 1-node cihaz + Rugged Case ağırlığı     |    ~ 114 lbs          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Muhafaza ortamı belirtimleri

Bu bölümde sıcaklık, titreşim, darbe ve yükseklik gibi muhafaza ortamıyla ilgili belirtimler listelenmektedir.


|     Belirtim              |     Değer    |
|--------------------------------|-------------------------------------------------------------------|
|     Sıcaklık aralığı          |     0 – 43 ° C (işletimsel)    |
|     Titreşimi                  |     MIL-STD-810 yöntemi 514,7 *<br>İşlem ı CAT 4, 20                  |
|     Şok                      |     MIL-STD-810 yöntemi 516,7 *<br>Yordam IV, lojistik                 |
|     Çalışma                   |     İşletimsel: 10.000 fit<br>İşlemsel olmayan: 40.000 fit          |

**Tüm başvurular MIL-STD-810G değiştirme 1 ' dir (2014)*

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack kenarını dağıtın](azure-stack-edge-placeholder.md)
