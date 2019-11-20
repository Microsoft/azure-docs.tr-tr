---
title: Microsoft® Kesintisiz Akış Istemci taşıma setini lisanslama
description: Microsoft® Kesintisiz Akış Istemci taşıma paketi 'nin nasıl lisanslandığı hakkında bilgi edinin.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: 3e7742e7035f3c4f4827a2edae1ad5a705aa221f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693423"
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Microsoft® Kesintisiz Akış Istemci taşıma setini lisanslama 
## <a name="overview"></a>Genel Bakış
Microsoft Kesintisiz Akış Client taşıma kiti (Short için**Sspk** kesintisiz akış), katıştırılmış cihaz üreticilerine, kablolu ve mobil işleçlere, içerik hizmeti sağlayıcılarına, ahize üreticilerine ve bağımsız yazılım satıcıları (ISV 'Ler) ve Kesintisiz Akış biçimde Uyarlamalı içerik akışı için ürün ve hizmet oluşturmaya yönelik çözüm sağlayıcıları. SSPK, lisans tarafından herhangi bir cihaza ve platforma bir cihaz tarafından aşılenebilir Kesintisiz Akış istemcisinin platformdan bağımsız bir uygulamasıdır. 

Aşağıda yer alan üst düzey bir mimari ve IIS Kesintisiz Akış taşıma paketi kutusu, Microsoft tarafından sağlanan Kesintisiz Akış Istemci uygulamasıdır ve Kesintisiz Akış içeriğinin kayıttan yürütülmesi için tüm çekirdek mantığını içerir. Daha sonra bu içerik, uygun arabirimleri uygulayarak belirli bir cihaz veya platformun iş ortakları tarafından yer verilir. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Açıklama
SSPK, mükemmel iş değeri sunan koşullara göre lisanslanır. SSPK lisansı sektörde şunları sağlar:

* ' De Kit kaynağı taşıma Kesintisiz AkışC++ 
  * Kesintisiz Akış Istemci işlevselliği uygular
  * biçim ayrıştırma, buluşsal yöntemler, arabelleğe alma mantığı vb. ekler.
* Oynatıcı uygulaması API 'Leri 
  * Media Player uygulamasıyla etkileşim için programlama arabirimleri
* Platform soyutlama katmanı (PAL) arabirimi 
  * işletim sistemi (iş parçacıkları, yuvalar) ile etkileşim için programlama arabirimleri
* Donanım soyutlama katmanı (HAL) arabirimi 
  * donanım A/V kod çözücüleriyle etkileşim için programlama arabirimleri (kod çözme, oluşturma)
* Dijital Rights Management (DRM) arabirimi 
  * DRM soyutlama katmanı (DAL) aracılığıyla DRM işlemeye yönelik programlama arabirimleri
  * Microsoft PlayReady taşıma kiti ayrı olarak dağıtılır ancak bu arabirim aracılığıyla tümleştirilir. Microsoft PlayReady cihaz lisanslama hakkında daha fazla bilgi için [buraya](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl)tıklayın.
* Uygulama örnekleri 
  * Linux için örnek PAL uygulamasının
  * GStreamer için örnek HAL uygulama

## <a name="licensing-options"></a>Lisanslama Seçenekleri
Microsoft Kesintisiz Akış Istemci taşıma kiti, lisansların iki ayrı lisans sözleşmesi altında görüebilmesini sağlar: Kesintisiz Akış Istemci geçici ürünlerini geliştirme ve son kullanıcılara Kesintisiz Akış Istemci son ürünlerini dağıtmaya yönelik diğeri.

* Yonga ürünleri geliştirmek için bir kaynak kodu taşıma paketi gerektiren yonga kümesi üreticileri, sistem tümleştiricileri veya bağımsız yazılım satıcıları (ISV 'Ler) için bir Microsoft Kesintisiz Akış Istemci taşıma Seti **geçici ürün lisansı** yürütülmelidir.
* Son kullanıcılara Kesintisiz Akış Istemci son ürünleri için dağıtım hakları gerektiren cihaz üreticileri veya ISV 'Ler için, Microsoft Kesintisiz Akış Istemci taşıma paketi **son ürün lisansının** yürütülmesi gerekir.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Kesintisiz Akış Istemci taşıma seti geçici ürün lisansı
Bu lisans kapsamında, Microsoft Kesintisiz Akış Istemci ara ürünlerini diğer Kesintisiz Akış geliştirmek ve dağıtmak için gerekli fikri mülkiyet haklarını Kesintisiz Akış istemci taşıma paketi cihaz lisansı \ ' i görür Kesintisiz Akış Client son ürünlerini dağıtın.

#### <a name="fee-structure"></a>Ücret yapısı
Bir ABD $50.000 tek seferlik lisans ücreti Kesintisiz Akış Istemci taşıma paketine erişim sağlar. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Kesintisiz Akış Client taşıma paketi son ürün lisansı
Bu lisans kapsamında, Microsoft diğer Kesintisiz Akış istemci ara ürünlerini Kesintisiz Akış almak için gereken tüm fikri mülkiyet haklarını sunar ve şirket markalı Kesintisiz Akış Istemcisinin son dağıtımını sağlar Son kullanıcılar için ürünler.

#### <a name="fee-structure"></a>Ücret yapısı
Kesintisiz Akış Istemcisi son ürünü, şu şekilde bir ayrıcalıklı model altında sunulmaktadır:

* cihaz uygulamasına göre $0,10
* Telif ücreti her yıl $50.000 ' de kadık
* Her yıl ilk 10.000 cihaz uygulaması için lisanslı değildir 

## <a name="licensing-procedure-and-sspk-access"></a>Lisans prosedürü ve SSPK erişimi
Tüm lisans sorguları için e-posta [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) .

[Sspk dağıtım portalına](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) kayıtlı geçici lisans sahipleri tarafından erişilebilir.

Ara ve nihai SSPK lisanslamamaları, [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)Teknik sorular gönderebilir.

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Kesintisiz Akış Istemci ara ürün sözleşmesi lisans sahipleri

* Adroit Business Solutions, Inc
* Gelişmiş dijital yayın SA
* AirTies Kablosuz Iletism SanayiVe Dis Ticaret A.S.
* Albümde Technologies Ltd.
* Alticast şirketi
* Amazon Digital Services, Inc.
* Ariyon Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* Yankı Star satın alma şirketi
* Enseo, Inc.
* Floendo G.A.
* HANDAN geniş Infocom Co., Ltd.
* Infomir GMBH
* Irdeto ABD Inc.
* ıwedia G.A. 
* Liberty küresel hizmetler BV
* MediaTek Inc.
* MStar Co, Ltd
* Ni do Co., Ltd.
* OpenTV, Inc.
* Saffron dijital sınırlı
* Sichuan Changhong Elektrik Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* TCL Technology Electronics (Huizhou) Co., Ltd.
* En popüler zafer yatırımları, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Kesintisiz Akış Client son ürün sözleşmesi lisans sahipleri
* Gelişmiş dijital yayın SA
* AirTies Kablosuz Iletism SanayiVe Dis Ticaret A.S.
* Albümde Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan teknoloji şirketi
* Ariyon Technology, Inc.
* ATMACA ELEKTRONIK SAN. VE TIC. A. Ş
* İngiliz gök yayını sınırlı
* Kalepal Technology Inc., Shenzhen
* Compal elektroniği, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* Yankı Star satın alma şirketi
* Enseo, Inc.
* FilmFlex filmleri sınırlı
* Floendo G.A.
* FUNAı ELEKTRIK CO., LTD
* Aşson buluşları sınırlı
* Haier Information Applicantion S. R. L
* HANDAN geniş Infocom Co., Ltd.
* HiSense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision sektör Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDı Corporation
* Ni do Co., Ltd.
* Turuncu SA
* Saffron dijital sınırlı
* Sagemcom geniş bant SAS
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Conakliye Electronics CO., LTD
* Shenzhen Jiuzhou Elektrik Co., Ltd
* Kıenzhen ufuk, dijital teknoloji Co., Ltd
* Sichuan Changhong Elektrik Co., Ltd.
* Skardın Endüstriyel Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV G.A.
* SoftAtHome
* Sony Corporation
* TCL fazla aşırı pazarlama (Makao Commercial Offkısale) sınırlı
* Teknisyen teslim teknolojileri, SAS
* Tongfang genel Ltd.
* En popüler zafer yatırımları, Ltd.
* Toshiba yaşam stil ürünleri & Services Corporation
* Evrensel medya şirketi/Slovakia/s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirim sağlayın
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

