---
title: Microsoft &reg; kesintisiz akış Istemci taşıma kiti lisanslama
description: Microsoft Kesintisiz Akış Istemci taşıma paketi 'nin nasıl lisanslandığı hakkında bilgi edinin &reg; .
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: xpouyat
ms.openlocfilehash: f2c66b08d399ddf53686074079665e36be9aace7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870480"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Microsoft &reg; kesintisiz akış Istemci taşıma kiti lisanslama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Genel Bakış
Microsoft Kesintisiz Akış Client taşıma kiti (Short için **Sspk** ), yerleşik cihaz üreticilerine, kablolu ve mobil işleçlere, içerik hizmeti sağlayıcılarına, ahize üreticilerine, bağımsız yazılım satıcılarına (ISV) ve kesintisiz akış biçimde Uyarlamalı içerik akışı için ürün ve hizmet oluşturmaya yönelik çözüm sağlayıcılarına yardımcı olmak üzere iyileştirilmiş bir kesintisiz akış istemci uygulamasıdır. SSPK, lisans tarafından herhangi bir cihaza ve platforma bir cihaz tarafından aşılenebilir Kesintisiz Akış istemcisinin platformdan bağımsız bir uygulamasıdır. 

Aşağıda yer alan üst düzey bir mimari ve IIS Kesintisiz Akış taşıma paketi kutusu, Microsoft tarafından sağlanan Kesintisiz Akış Istemci uygulamasıdır ve Kesintisiz Akış içeriğinin kayıttan yürütülmesi için tüm çekirdek mantığını içerir. Daha sonra bu içerik, uygun arabirimleri uygulayarak belirli bir cihaz veya platformun iş ortakları tarafından yer verilir. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Description
SSPK, mükemmel iş değeri sunan koşullara göre lisanslanır. SSPK lisansı sektörde şunları sağlar:

* C++ ' ta Kesintisiz Akış taşıma paketi kaynağı 
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
  * Microsoft PlayReady taşıma kiti ayrı olarak dağıtılır ancak bu arabirim aracılığıyla tümleştirilir. [Microsoft PlayReady cihaz lisanslama hakkında daha fazla bilgi Için bkz](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl)..
* Uygulama örnekleri 
  * Linux için örnek PAL uygulamasının
  * GStreamer için örnek HAL uygulama

## <a name="licensing-options"></a>Lisanslama seçenekleri
Microsoft Kesintisiz Akış Istemci taşıma kiti, lisansların iki ayrı lisans sözleşmesi altında görüebilmesini sağlar: Kesintisiz Akış Istemci geçici ürünlerini geliştirme ve son kullanıcılara Kesintisiz Akış Istemci son ürünlerini dağıtmaya yönelik diğeri.

* Yonga ürünleri geliştirmek için bir kaynak kodu taşıma paketi gerektiren yonga kümesi üreticileri, sistem tümleştiricileri veya bağımsız yazılım satıcıları (ISV 'Ler) için bir Microsoft Kesintisiz Akış Istemci taşıma Seti **geçici ürün lisansı** yürütülmelidir.
* Son kullanıcılara Kesintisiz Akış Istemci son ürünleri için dağıtım hakları gerektiren cihaz üreticileri veya ISV 'Ler için, Microsoft Kesintisiz Akış Istemci taşıma paketi **son ürün lisansının** yürütülmesi gerekir.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Kesintisiz Akış Istemci taşıma seti geçici ürün lisansı
Bu lisans kapsamında, Microsoft Kesintisiz Akış Istemci geçici ürünlerini geliştirmek ve dağıtmak için gereken fikri mülkiyet haklarını Kesintisiz Akış istemci son ürünlerini Kesintisiz Akış dağıtan Kesintisiz Akış Istemci bağlantı noktası seti cihaz lisanslamalarına sunmaktadır.

#### <a name="fee-structure"></a>Ücret yapısı
Bir ABD $50.000 tek seferlik lisans ücreti Kesintisiz Akış Istemci taşıma paketine erişim sağlar. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Kesintisiz Akış Client taşıma paketi son ürün lisansı
Bu lisans kapsamında, Microsoft diğer Kesintisiz Akış istemci ara ürünlerini Kesintisiz Akış almak için gereken tüm fikri mülkiyet haklarını sunar ve şirket markalı Kesintisiz Akış Istemci son ürünlerini son kullanıcılara dağıtır.

#### <a name="fee-structure"></a>Ücret yapısı
Kesintisiz Akış Istemcisi son ürünü, şu şekilde bir ayrıcalıklı model altında sunulmaktadır:

* cihaz uygulamasına göre $0,10
* Telif ücreti her yıl $50.000 ' de kadık
* Her yıl ilk 10.000 cihaz uygulaması için lisanslı değildir 

## <a name="licensing-procedure-and-sspk-access"></a>Lisans prosedürü ve SSPK erişimi
[sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com)Tüm lisans sorguları için e-posta.

SSPK dağıtım portalına kayıtlı geçici lisans sahipleri tarafından erişilebilir.

Ara ve nihai SSPK lisanslamamaları, konusunda teknik sorular gönderebilir [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) .

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
* Guangzhou Kııkun elektronik., Ltd.
* HANDAN geniş Infocom Co., Ltd.
* Infomir GMBH
* Irdeto ABD Inc.
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
* En popüler zafer yatırımları, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Kesintisiz Akış Client son ürün sözleşmesi lisans sahipleri
* Gelişmiş dijital yayın SA
* AirTies Kablosuz Iletism SanayiVe Dis Ticaret A.S.
* AmTRAN Technology Co., Ltd 
* Arcadyan teknoloji şirketi
* Arcelik A. S
* Compal elektroniği, Inc.
* HıZLı ŞANSLAR TEKNOLOJISI SıNıRLı
* Floendo G.A.
* FUNAı ELEKTRIK CO., LTD
* HiSense International Co., Ltd.
* HiSense Visual Technology Co., Ltd
* HKC Corporation sınırlı
* Hong Kong konka Ltd
* Innolux Corporation
* Jınpin elektrik şirketi Ltd. Zhuhai. S. E. Z
* K-tronics (Suzhou) Technology Co., Ltd. 
* Kaonmedia Co., Ltd.
* KDDı Corporation
* Mega Fame Elektronik Co. Limited
* IRC elektroniği sınırlı
* MOKA ULUSLARARASı SıNıRLı
* ONEPLUS ELEKTRONIĞI (SHENZHEN) CO., LTD.
* Panasonic Corporation
* Qingdao Haier Optronics Co., Ltd.
* Shenzhen ATEKO fotoğraf Elektrik Co., Ltd.
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Jiuzhou Elektrik Co., Ltd
* Shenzhen KTC Technology Co., Ltd. 
* Shenzhen Maxhazırlayan Technology Co., Ltd
* Shenzhen MTC Co., Ltd
* Kıenzhen ufuk, dijital teknoloji Co., Ltd
* Sichuan Changhong Elektrik Co., Ltd.
* Skardın Endüstriyel Corp.
* Gök CP Ltd
* SMARDTV KÜRESEL SAS
* SoftAtHome
* Sony Corporation
* Teknisyen teslim teknolojileri, SAS
* En popüler zafer yatırımları, Ltd.
* UMC Polonya Sp. z. o.o.
* Vizio, Inc.
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

