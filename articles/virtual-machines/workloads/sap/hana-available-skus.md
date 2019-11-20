---
title: Azure 'da SAP HANA için SKU 'Lar (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA için SKU 'Lar (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 987f84301608129075b55769f886912ec2354cbb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099895"
---
# <a name="available-skus-for-hli"></a>HLI için kullanılabilir SKU'lar

Düzeltme 3 damgalarına dayanan Azure (büyük örnekler) hizmeti SAP HANA, Azure bölgelerindeki çeşitli yapılandırmalarda kullanılabilir:

- Batı ABD
- East US
- Avustralya Doğu
- Avustralya Güneydoğu
- Batı Avrupa
- Kuzey Avrupa
- Japonya Doğu
- Japonya Batı

Düzeltme 4 damgalarına dayanan Azure (büyük örnekler) hizmetinde SAP HANA, Azure bölgelerindeki çeşitli yapılandırmalarda mevcuttur:

- Batı ABD 2
- East US
- Batı Avrupa
- Kuzey Avrupa



[LISANSLı Hana büyük örnek listesi SAP HANA,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) örneğin:

| SAP çözümü | CPU | Bellek | Depolama | Kullanılabilirlik |
| --- | --- | --- | --- | --- |
| OLAP için iyileştirildi: SAP BW, SIYAH BEYAZ/4HANA<br /> veya genel OLAP iş yükü için SAP HANA | Azure s72 üzerinde SAP HANA<br /> – 2 x Intel® Xeon® Işlemci E7-8890 v3<br /> 36 CPU çekirdeği ve 72 CPU iş parçacığı |  768 GB |  3 TB | Artık sunulmadı |
| --- | Azure S144 üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Işlemci E7-8890 v3<br /> 72 CPU çekirdeği ve 144 CPU iş parçacığı |  1,5 TB |  6 TB | Artık sunulmadı |
| --- | Azure S192 üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Işlemci E7-8890 v4<br /> 96 CPU çekirdeği ve 192 CPU iş parçacığı |  2,0 TB |  8 TB | Kullanılabilir |
| --- | Azure S384 üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Işlemci E7-8890 v4<br /> 192 CPU çekirdeği ve 384 CPU iş parçacığı |  4,0 TB |  16 TB | Kullanılabilir |
| OLTP için iyileştirildi: SAP Business Suite<br /> SAP HANA veya S/4HANA (OLTP) üzerinde,<br /> Genel OLTP | Azure S72m üzerinde SAP HANA<br /> – 2 x Intel® Xeon® Işlemci E7-8890 v3<br /> 36 CPU çekirdeği ve 72 CPU iş parçacığı |  1,5 TB |  6 TB | Artık sunulmadı |
|---| Azure S144m üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Işlemci E7-8890 v3<br /> 72 CPU çekirdeği ve 144 CPU iş parçacığı |  3,0 TB |  12 TB | Artık sunulmadı |
|---| Azure S192m üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Işlemci E7-8890 v4<br /> 96 CPU çekirdeği ve 192 CPU iş parçacığı  |  4,0 TB |  16 TB | Kullanılabilir |
|---| Azure S384m üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Işlemci E7-8890 v4<br /> 192 CPU çekirdeği ve 384 CPU iş parçacığı |  6,0 TB |  18 TB | Kullanılabilir |
|---| Azure S384xm üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Işlemci E7-8890 v4<br /> 192 CPU çekirdeği ve 384 CPU iş parçacığı |  8,0 TB |  22 TB |  Kullanılabilir |
|---| Azure S576m üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Işlemci E7-8890 v4<br /> 288 CPU çekirdeği ve 576 CPU iş parçacığı |  12,0 TB |  28 TB | Kullanılabilir |
|---| Azure S768m üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Işlemci E7-8890 v4<br /> 384 CPU çekirdeği ve 768 CPU iş parçacığı |  16,0 TB |  36 TB | Kullanılabilir |
|---| Azure S960m üzerinde SAP HANA<br /> – 20 x Intel® Xeon® Işlemci E7-8890 v4<br /> 480 CPU çekirdeği ve 960 CPU iş parçacığı |  20,0 TB |  46 TB | Kullanılabilir |


SAP HANA TDIv5 ' nin altında SAP, müşteriye özgü boyutlandırma ve müşteriye özgü projelere izin verir ve bu, içinde sertifikalı olarak listelenmeyen sunucu yapılandırmalarına yol açabilir:

- [Sertifikalı gereçler SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Sertifikalı IaaS platformlarını SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Çoğu durumda, bu müşteriye özgü sunucu yapılandırmalarının SAP ile sertifikalı sunucu birimlerinden daha fazla bellek taşıması gerekir. SAP ile birlikte çalışarak müşteriler, müşterilere özgü boyutlardaki sunucu yapılandırmalarına yönelik SAP desteği ve onaylama olanağı sağlar. Azure 'da, aşağıdaki HANA büyük örnek standart SKU 'Ları ve bu TDIv5 müşterilere özgü boyutlandırma projeleri için Microsoft fiyat listesi 'nde kullanılabilir.

| SKU|CPU | Bellek | Depolama | Kullanılabilirlik |
| ---| --- | --- | --- | --- |
| S96 | Azure S96 üzerinde SAP HANA<br /> – 2 x Intel® Xeon® Işlemci E7-8890 v4<br /> 48 CPU çekirdeği ve 96 CPU iş parçacığı |  768 GB |  3 TB | Kullanılabilir |


| Özgün SKU <br /> Genişletilmiş bellek | CPU | Bellek | Depolama | Kullanılabilirlik |
| --- | --- | --- | --- | --- |
| S192m, şu şekilde genişletilebilir | Azure S192xm üzerinde SAP HANA<br /> – 4 x Intel® Xeon® Işlemci E7-8890 v4<br /> 96 CPU çekirdeği ve 192 CPU iş parçacığı |  6,0 TB |  16 TB | Kullanılabilir |
| S384xm, şu şekilde genişletilebilir | Azure S384xxm üzerinde SAP HANA<br /> – 8 x Intel® Xeon® Işlemci E7-8890 v4<br /> 192 CPU çekirdeği ve 384 CPU iş parçacığı |  12,0 TB |  28 TB | Kullanılabilir |
| S576m, şu şekilde genişletilebilir | Azure S576xm üzerinde SAP HANA<br /> – 12 x Intel® Xeon® Işlemci E7-8890 v4<br /> 288 CPU çekirdeği ve 576 CPU iş parçacığı |  18,0 TB |  41 TB | Kullanılabilir |
| S768m, şu şekilde genişletilebilir | Azure S768xm üzerinde SAP HANA<br /> – 16 x Intel® Xeon® Işlemci E7-8890 v4<br /> 384 CPU çekirdeği ve 768 CPU iş parçacığı |  24,0 TB |  56 TB | Kullanılabilir |

- CPU çekirdekleri = sunucu biriminin işlemcilerin toplamının hiper iş parçacıklı olmayan CPU çekirdekleri toplamı.
- CPU iş parçacıkları = sunucu biriminin işlemcilerin toplamının hiper iş parçacıklı CPU çekirdekleri tarafından sunulan işlem iş parçacıklarının toplamı. Çoğu birim, varsayılan olarak hiper Iş parçacığı teknolojisini kullanacak şekilde yapılandırılır.
- Üretici S768m, S768xm ve S960m temel alınarak SAP HANA çalıştırmak için hiper Iş parçacığı kullanacak şekilde yapılandırılmamıştır.


Seçilen belirli yapılandırma iş yüküne, CPU kaynaklarına ve istenen belleğe bağımlıdır. OLTP iş yükünün OLAP iş yükü için en iyi duruma getirilmiş SKU 'Ları kullanabilmesi mümkündür. 

Müşterilere özgü boyutlandırma projelerine yönelik birimler hariç, tekliflere yönelik donanım tabanı, TDı sertifikalı SAP HANA. İki farklı donanım sınıfı, SKU 'Ları içine böler:

- SKU 'ların "Type ı Class" olarak adlandırılan s72, S72m, S96, S144, S144m, S192, S192m ve S192xm.
- SKU 'ların "Type II Class" olarak adlandırılan S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm ve S960m.

Tüm HANA büyük örnek damgası, tek bir müşteri&#39;s kullanımı için özel olarak ayrılmaz. Bu olgu, Azure 'da dağıtılan bir ağ dokusuna göre bağlanmış işlem ve depolama kaynaklarının raflarına yöneliktir. Azure gibi Hana büyük örnek altyapısı, aşağıdaki üç düzeyde bir &quot;diğerinden&quot; yalıtılmış farklı müşteri kiracılar dağıtır:

- **Ağ**: HANA büyük örnek damgasında sanal ağlar aracılığıyla yalıtım.
- **Depolama alanı**: Depolama birimleri atanan ve kiracılar arasında depolama birimlerini yalıtmak için depolama sanal makineler aracılığıyla yalıtım.
- **İşlem**: Sunucu birimlerinin tek bir kiracıya adanmış atanması. Sunucu birimlerinin sabit veya geçici bölümlendirme yok. Kiracılar arasında tek bir sunucu veya konak birimi paylaşımı yoktur. 

Farklı kiracılar arasında HANA büyük örnek birimlerinin dağıtımları birbirleriyle görülemez. Farklı kiracılarda dağıtılan HANA büyük örnek birimleri, HANA büyük örnek damgası düzeyinde birbirleriyle doğrudan iletişim kuramaz. Yalnızca bir kiracının içindeki HANA büyük örnek birimleri, HANA büyük örnek damgası düzeyinde birbirleriyle iletişim kurabilir.

Büyük örnek damgasındaki dağıtılan bir kiracı, Faturalama amacıyla bir Azure aboneliğine atanır. Bir ağ için aynı Azure kaydı içindeki diğer Azure aboneliklerinin sanal ağlarından erişilebilir. Aynı Azure bölgesindeki başka bir Azure aboneliğiyle dağıtım yaparsanız, ayrılmış bir HANA büyük örnek kiracısı isteyip istemediğinizi de tercih edebilirsiniz.

HANA büyük örnekteki SAP HANA çalıştırma ve Azure 'da dağıtılan VM 'lerde çalışan SAP HANA arasında önemli farklılıklar vardır:

- Azure 'da SAP HANA için sanallaştırma katmanı yoktur (büyük örnekler). Temel alınan çıplak donanımın performansını alırsınız.
- Azure 'dan farklı olarak, Azure 'daki SAP HANA (büyük örnekler) sunucu belirli bir müşteriye ayrılmıştır. Sunucu biriminin veya konağın sabit veya geçici olarak bölümlenmiş olması mümkün değildir. Sonuç olarak, bir kiracıya ve sizin için bir bütün olarak atanan bir HANA büyük örnek birimi kullanılır. Sunucunun yeniden başlatılması veya kapatılmasını, işletim sistemine otomatik olarak ve SAP HANA başka bir sunucuya dağıtılmayacak. (Tür ı sınıfı SKU 'Ları için tek istisna, bir sunucunun sorunlarla karşılaştığında ve yeniden dağıtımı başka bir sunucuda gerçekleştirilmesi gerekir.)
- En iyi fiyat/performans oranı için ana bilgisayar işlemcisi türlerinin seçildiği Azure 'dan farklı olarak, Azure 'da SAP HANA için seçilen işlemci türleri (büyük örnekler), Intel E7v3 ve E7v4 işlemci hattının en yüksek gerçekleştirmesinden oluşur.

**Sonraki adımlar**
- [HLi boyutlandırmayı](hana-sizing.md) inceleyin
