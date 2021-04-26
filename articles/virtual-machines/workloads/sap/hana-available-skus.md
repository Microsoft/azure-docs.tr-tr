---
title: Azure 'da SAP HANA için SKU 'Lar (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA için SKU 'Lar (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKU 'Lar, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017, references_regions
ms.openlocfilehash: 3ecbbe4d477f3e6c3c6606528c51b934b6cf534a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718746"
---
# <a name="available-skus-for-hana-large-instances"></a>HANA büyük örnekleri için kullanılabilir SKU 'Lar

Rev 4,2 * temelinde BareMetal altyapısı (SAP HANA iş yükleri için sertifikalı) hizmeti aşağıdaki bölgelerde kullanılabilir:
- West Europe
- Kuzey Avrupa
- Bölgeler desteğiyle Almanya Orta Batı
- Bölgeler desteğiyle Doğu ABD
- Doğu ABD 2
- Orta Güney ABD
- Bölgeler desteğiyle Batı ABD 2

Rev 3 * temelinde BareMetal altyapısı (SAP HANA iş yükleri için sertifikalı) hizmeti, aşağıdaki bölgelerde sınırlı kullanılabilirliğe sahiptir:
- Batı ABD
- Doğu ABD 
- Doğu Avustralya 
- Güneydoğu Avustralya
- Doğu Japonya


Aşağıda, kullanılabilir Azure büyük örneklerinin bir listesi verilmiştir.

> [!IMPORTANT]
> Listedeki büyük örnek türlerinin her biri için HANA sertifikası durumunu temsil eden ilk sütunu unutmayın. Sütun, **S** harfiyle başlayan Azure sku 'larının [SAP HANA donanım diziniyle](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) bağıntılı olmalıdır.



| SAP HANA sertifikalı | Modelleme | Toplam bellek | Bellek DRAM | Bellek Optane | Depolama | Kullanılabilirlik |
| --- | --- | --- | --- | --- | --- | --- |
| EVET <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | Azure S96 üzerinde SAP HANA<br /> – 2 x Intel® Xeon® Işlemci E7-8890 v4 <br /> 48 CPU çekirdeği ve 96 CPU iş parçacığı |  768 GB | 768 GB | --- | 3,0 TB | Kullanılabilir |
| EVET <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | Azure S224 üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 112 CPU çekirdeği ve 224 CPU iş parçacığı |  3,0 TB | 3,0 TB | --- | 6,3 TB | Kullanılabilir |
| EVET <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | Azure S224m üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 112 CPU çekirdeği ve 224 CPU iş parçacığı |  6,0 TB | 6,0 TB | --- | 10,5 TB | Kullanılabilir |
| EVET <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | Azure S224om üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 112 CPU çekirdeği ve 224 CPU iş parçacığı | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Kullanılabilir |
| NO | Azure S224oo üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 112 CPU çekirdeği ve 224 CPU iş parçacığı | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Kullanılabilir |
| NO | Azure S224ooo üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 112 CPU çekirdeği ve 224 CPU iş parçacığı | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Kullanılabilir |
| NO | Azure S224oom üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 112 CPU çekirdeği ve 224 CPU iş parçacığı | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Kullanılabilir |
| EVET <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | Azure S384 üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Işlemci E7-8890 v4<br /> 192 CPU çekirdeği ve 384 CPU iş parçacığı |  4,0 TB | 4,0 TB | --- | 16 TB | Kullanılabilir |
| EVET <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | Azure S384m üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Işlemci E7-8890 v4<br /> 192 CPU çekirdeği ve 384 CPU iş parçacığı |  6,0 TB | 6,0 TB | --- | 18 TB |  Kullanılabilir  |
| EVET <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | Azure S384xm üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Işlemci E7-8890 v4<br /> 192 CPU çekirdeği ve 384 CPU iş parçacığı |  8,0 TB | 8,0 TB | --- | 22 TB | Kullanılabilir |
| EVET <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | Azure S448 üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 224 CPU çekirdeği ve 448 CPU iş parçacığı | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Kullanılabilir (yalnızca Rev 4,2) |
| EVET <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | Azure S448m üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 224 CPU çekirdeği ve 448 CPU iş parçacığı | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S448oo üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 224 CPU çekirdeği ve 448 CPU iş parçacığı | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S448om üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 224 CPU çekirdeği ve 448 CPU iş parçacığı | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S448ooo üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 224 CPU çekirdeği ve 448 CPU iş parçacığı | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S448oom üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 224 CPU çekirdeği ve 448 CPU iş parçacığı | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| EVET <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | Azure S576m üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Işlemci E7-8890 v4<br /> 288 CPU çekirdeği ve 576 CPU iş parçacığı |  12,0 TB | 12,0 TB | --- | 28 TB | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S576xm üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Işlemci E7-8890 v4<br /> 288 CPU çekirdeği ve 576 CPU iş parçacığı |  18,0 TB | 18.0 | --- |  41 TB | Kullanılabilir |
| EVET <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | Azure S672 üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 336 CPU çekirdeği ve 672 CPU iş parçacığı | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Kullanılabilir (yalnızca Rev 4,2) |
| EVET <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | Azure S672m üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 336 CPU çekirdeği ve 672 CPU iş parçacığı | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S672oo üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 336 CPU çekirdeği ve 672 CPU iş parçacığı | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S672om üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 336 CPU çekirdeği ve 672 CPU iş parçacığı | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S672ooo üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 336 CPU çekirdeği ve 672 CPU iş parçacığı | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S672oom üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 336 CPU çekirdeği ve 672 CPU iş parçacığı | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| EVET <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | Azure S768m üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Işlemci E7-8890 v4<br /> 384 CPU çekirdeği ve 768 CPU iş parçacığı |  16,0 TB | 16,0 TB | -- | 36 TB | Kullanılabilir |
| NO | Azure S768xm üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Işlemci E7-8890 v4<br /> 384 CPU çekirdeği ve 768 CPU iş parçacığı |  24,0 TB | 24,0 TB | --- | 56 TB | Kullanılabilir |
|  EVET <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | Azure S896 üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 448 CPU çekirdeği ve 896 CPU iş parçacığı | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Kullanılabilir (yalnızca Rev 4,2) |
| EVET <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | Azure S896m üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 448 CPU çekirdeği ve 896 CPU iş parçacığı | 24,0 TB | 24,0 TB | -- | 35,8 TB | Kullanılabilir |
| NO | Azure S896oo üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 448 CPU çekirdeği ve 896 CPU iş parçacığı | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S896om üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 448 CPU çekirdeği ve 896 CPU iş parçacığı | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S896ooo üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 448 CPU çekirdeği ve 896 CPU iş parçacığı | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| NO | Azure S896oom üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Platinum 8276 işlemcisi <br /> 448 CPU çekirdeği ve 896 CPU iş parçacığı | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Kullanılabilir (yalnızca Rev 4,2) |
| EVET <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | Azure S960m üzerinde SAP HANA<br /> – 20 x Intel® Xeon® Işlemci E7-8890 v4<br /> 480 CPU çekirdeği ve 960 CPU iş parçacığı |  20,0 TB | 20,0 TB | -- | 46 TB | Kullanılabilir (yalnızca Rev 4,2) |


- CPU çekirdekleri = sunucu biriminin işlemcilerin toplamının hiper iş parçacıklı olmayan CPU çekirdekleri toplamı.
- CPU iş parçacıkları = sunucu biriminin işlemcilerin toplamının hiper iş parçacıklı CPU çekirdekleri tarafından sunulan işlem iş parçacıklarının toplamı. Çoğu birim, Hyper-Threading teknolojisini kullanmak için varsayılan olarak yapılandırılır.
- S768m, S768xm ve S960m tedarikçilere göre, SAP HANA çalıştırmak için Hyper-Threading kullanacak şekilde yapılandırılmamıştır.


> [!IMPORTANT]
> Yine de desteklenmekle birlikte, şu SKU 'Lar artık satın alınamaz: s72, S72m, S144, S144m, S192 ve S192m 

Seçilen belirli yapılandırma iş yüküne, CPU kaynaklarına ve istenen belleğe bağımlıdır. OLTP iş yükünün OLAP iş yükü için en iyi duruma getirilmiş SKU 'Ları kullanabilmesi mümkündür. 

İki farklı donanım sınıfı, SKU 'Ları içine böler:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 ve S224m, S224oo, S224om, S224ooo, S224oom SKU 'ların "Type ı Class" olarak adlandırılır.
- Diğer tüm SKU 'lar SKU 'ların "tür II sınıfı" olarak adlandırılır.
- Henüz SAP donanım dizininde listelenmeyen SKU 'Lar ile ilgileniyorsanız daha fazla bilgi edinmek için Microsoft hesabı ekibinize başvurun. 


Tüm HANA büyük örnek damgası, tek bir müşteri&#39;kullanımı için özel olarak ayrılmaz. Bu olgu, Azure 'da dağıtılan bir ağ dokusuna göre bağlanmış işlem ve depolama kaynaklarının raflarına yöneliktir. Azure gibi HANA büyük örnek altyapısı, &quot; &quot; aşağıdaki üç düzeyde bir diğerinden yalıtılmış farklı müşteri kiracılar dağıtır:

- **Ağ**: Hana büyük örnek damgasında sanal ağlar aracılığıyla yalıtım.
- **Depolama**: depolama birimleri atanmış depolama sanal makineler aracılığıyla yalıtım yapın ve depolama birimlerini kiracılar arasında yalıtın.
- **İşlem**: sunucu birimlerinin tek bir kiracıya adanmış atanması. Sunucu birimlerinin sabit veya geçici bölümlendirme yok. Kiracılar arasında tek bir sunucu veya konak birimi paylaşımı yoktur. 

Farklı kiracılar arasında HANA büyük örnek birimlerinin dağıtımları birbirleriyle görülemez. Farklı kiracılarda dağıtılan HANA büyük örnek birimleri, HANA büyük örnek damgası düzeyinde birbirleriyle doğrudan iletişim kuramaz. Yalnızca bir kiracının içindeki HANA büyük örnek birimleri, HANA büyük örnek damgası düzeyinde birbirleriyle iletişim kurabilir.

Büyük örnek damgasındaki dağıtılan bir kiracı, Faturalama amacıyla bir Azure aboneliğine atanır. Bir ağ için aynı Azure kaydı içindeki diğer Azure aboneliklerinin sanal ağlarından erişilebilir. Aynı Azure bölgesindeki başka bir Azure aboneliğiyle dağıtım yaparsanız, ayrılmış bir HANA büyük örnek kiracısı isteyip istemediğinizi de tercih edebilirsiniz.

HANA büyük örnekteki SAP HANA çalıştırma ve Azure 'da dağıtılan VM 'lerde çalışan SAP HANA arasında önemli farklılıklar vardır:

- Azure 'da SAP HANA için sanallaştırma katmanı yoktur (büyük örnekler). Temel alınan çıplak donanımın performansını alırsınız.
- Azure 'dan farklı olarak, Azure 'daki SAP HANA (büyük örnekler) sunucu belirli bir müşteriye ayrılmıştır. Sunucu biriminin veya konağın sabit veya geçici olarak bölümlenmiş olması mümkün değildir. Sonuç olarak, bir kiracıya ve sizin için bir bütün olarak atanan bir HANA büyük örnek birimi kullanılır. Sunucunun yeniden başlatılması veya kapatılmasını, işletim sistemine otomatik olarak ve SAP HANA başka bir sunucuya dağıtılmayacak. (Tür ı sınıfı SKU 'Ları için tek istisna, bir sunucunun sorunlarla karşılaştığında ve yeniden dağıtımı başka bir sunucuda gerçekleştirilmesi gerekir.)
- En iyi fiyat/performans oranı için ana bilgisayar işlemcisi türlerinin seçildiği Azure 'dan farklı olarak, Azure 'da SAP HANA için seçilen işlemci türleri (büyük örnekler), Intel E7v3 ve E7v4 işlemci hattının en yüksek gerçekleştirmesinden oluşur.

## <a name="next-steps"></a>Sonraki adımlar
- [HLi boyutlandırılmasına](hana-sizing.md)bakın.
