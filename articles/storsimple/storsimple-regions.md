---
title: StorSimple bölgesi kullanılabilirliği | Microsoft Docs
description: Çeşitli StorSimple cihaz modellerinin kullanılabildiği Azure bölgelerini açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 5b41f738c2419f806aef60374a545ff004a6f19e
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965133"
---
# <a name="available-regions-for-your-storsimple"></a>StorSimple için kullanılabilir bölgeler

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış

Azure veri merkezleri, müşterinin performans, gereksinim ve veri konumuyla ilgili tercihleri taleplerini karşılamak için dünyanın dört bir yanındaki birden fazla coğrafi ortamda çalışır. Azure coğrafya, en az bir Azure bölgesi içeren, dünyanın tanımlı bir alanıdır. Bir Azure bölgesi, bir veya daha fazla veri merkezi içeren coğrafya içindeki bir alandır.

Bir Azure bölgesinin seçilmesi çok önemlidir ve bölge seçimi, veri yerleşimi, hizmet kullanılabilirliği, performans, maliyet ve artıklık gibi faktörlerden etkilenir. Bölge seçme hakkında daha fazla bilgi için [hangi Azure bölgesinin uygun olduğunu seçin?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

StorSimple çözümü için bölge seçimi özellikle aşağıdaki etkenlere göre belirlenir:

- StorSimple Aygıt Yöneticisi hizmetinin kullanılabildiği bölgeler.
- StorSimple fiziksel, bulut veya sanal cihazının kullanılabildiği ülkeler/bölgeler.
- StorSimple verilerini depolayan depolama hesaplarının en iyi performans için bulunması gereken bölgeler.

Bu öğretici, StorSimple Aygıt Yöneticisi hizmeti, şirket içi fiziksel ve bulut cihazlarıyla ilgili bölge kullanılabilirliğini açıklamaktadır. Bu makalede yer alan bilgiler, StorSimple 8000 ve 1200 serisi cihazlar için geçerlidir.

## <a name="region-availability-for-storsimple-device-manager-service"></a>StorSimple Aygıt Yöneticisi hizmeti için bölge kullanılabilirliği

StorSimple Aygıt Yöneticisi hizmeti şu anda 12 ortak bölgede ve 2 Azure Kamu bölgelerinde desteklenmektedir.

StorSimple Aygıt Yöneticisi hizmetini ilk oluşturduğunuzda bölge veya konum tanımlarsınız. Genel olarak, cihazın dağıtıldığı coğrafi bölgeye en yakın bir konum seçilir. Ancak cihaz ve hizmet farklı konumlara de dağıtılabilir.

StorSimple Aygıt Yöneticisi hizmetinin Azure genel bulutu 'nda kullanılabildiği ve dağıtılabileceği bölgelerin listesi aşağıda verilmiştir.

![StorSimple-Device-Manager-Service-Region](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Azure Kamu Bulutu için, StorSimple Aygıt Yöneticisi hizmeti US Gov Iowa ve US Gov Virginia veri merkezlerinde kullanılabilir.

## <a name="region-availability-for-data-stored-in-storsimple"></a>StorSimple 'da depolanan veriler için bölge kullanılabilirliği

StorSimple verileri Azure depolama hesaplarında fiziksel olarak depolanır ve bu hesaplar tüm Azure bölgelerinde kullanılabilir. Bir Azure depolama hesabı oluşturduğunuzda, depolama hesabının birincil konumu seçilir ve verilerin bulunduğu bölgeyi belirler.

İlk olarak bir StorSimple Aygıt Yöneticisi hizmeti oluşturup bir depolama hesabını onunla ilişkilendirdiğinizde, StorSimple Aygıt Yöneticisi hizmetiniz ve Azure depolama alanı iki ayrı konumda olabilir. Böyle bir durumda, StorSimple Cihaz Yöneticisi ve Azure Storage hesabını ayrı ayrı oluşturmanız gerekir.

Genel olarak, depolama hesabınız için hizmetinize en yakın bölgeyi seçin. Ancak en yakın Microsoft Azure bölgesi en düşük gecikme süresine sahip olan bölge olmayabilir. Ağ Hizmeti performansını ve bu nedenle çözümün performansını belirleyen gecikmedir. Bu nedenle, farklı bir bölgede bir depolama hesabı tercih ediyorsanız, gecikme sürelerinin hizmetinize ve depolama hesabınızla ilişkili bölgeden ne kadar gecikdiklerini bilmek önemlidir.

StorSimple Cloud Appliance kullanıyorsanız, hizmetin ve ilişkili depolama hesabının aynı bölgede olması önerilir. Farklı bir bölgedeki depolama hesapları, performansın düşmesine neden olabilir.

## <a name="availability-of-storsimple-device"></a>StorSimple cihazının kullanılabilirliği

Modele bağlı olarak, StorSimple cihazları farklı coğrafi bölgelerde veya ülkelerde/bölgelerde kullanılabilir.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple fiziksel cihazı (modeller 8100/8600)

StorSimple 8100 veya 8600 fiziksel cihaz kullanılıyorsa, cihaz aşağıdaki ülkelerde/bölgelerde kullanılabilir.

| #  | Ülke/Bölge        | #  | Ülke/Bölge     | #  | Ülke/Bölge      | #  | Ülke/Bölge             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1\.  | Avustralya             | 16 | Hong Kong SAR      | 31 | Yeni Zelanda         | 46 | Güney Afrika               |
| 2  | Avusturya               | 17 | Macaristan            | 32 | Nijerya             | 47 | Güney Kore                |
| 3  | Bahreyn               | 18 | İzlanda            | 33 | Norveç              | 48 | İspanya                      |
| 4  | Belçika               | 19 | Hindistan              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brezilya                | 20 | Endonezya          | 35 | Filipinler         | 50 | İsveç                     |
| 6  | Kanada                | 21 | İrlanda            | 36 | Polonya              | 51 | İsviçre                |
| 7  | Şili                 | 22 | İsrail             | 37 | Portekiz            | 52 | Tayvan                     |
| 8  | Kolombiya              | 23 | İtalya              | 38 | Porto Riko         | 53 | Tayland                   |
| 9  | Çek Cumhuriyeti        | 24 | Japonya              | 39 | Katar               | 54 | Türkiye                     |
| 10 | Danimarka               | 25 | Kenya              | 40 | Romanya             | 55 | Ukrayna                    |
| 11 | Mısır                 | 26 | Kuveyt             | 41 | Rusya              | 56 | Birleşik Arap Emirlikleri       |
| 12 | Finlandiya               | 27 | Makao ÖİB          | 42 | Suudi Arabistan        | 57 | Birleşik Krallık             |
| 13 | Fransa                | 28 | Malezya           | 43 | Singapur           | 58 | Amerika Birleşik Devletleri              |
| 14 | Almanya               | 29 | Meksika             | 44 | Slovakya            | 59 | Vietnam                    |
| 15 | Yunanistan                | 30 | Hollanda        | 45 | Slovenya            | 60 | Hırvatistan                    |

Bu liste, daha fazla ülke/bölge eklendikçe değişir. Geographıes 'in en güncel listesi için [ürün koşullardaki](https://www.microsoft.com/en-us/licensing/product-licensing/products)depolama dizisi terimleri ek seçeneğine gidin.

Microsoft, fiziksel donanım sunabilir ve yukarıdaki listede coğrafi olarak StorSimple için donanım yedek parçalar değişikliği sağlayabilir.

> [!IMPORTANT]
> StorSimple fiziksel cihazını StorSimple 'un desteklenmediği bir bölgeye yerleştirmeyin. Microsoft, StorSimple 'un desteklenmediği ülkelere/bölgelere herhangi bir değişiklik konusu teslim edemeyecektir.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modeller 8010/8020)

8010 veya 8020 StorSimple Cloud Appliance kullanıyorsanız, cihaz, temel alınan VM 'nin desteklendiği tüm bölgelerde desteklenir ve kullanılabilir. 8010, tüm Azure bölgelerinde desteklenen bir _Standard_A3_ VM kullanır.

8020, bir bulut gereci oluşturmak için Premium Storage ve _Standard_DS3_ VM kullanır. 8020, Premium depolamayı ve _Standard_DS3_ Azure VM 'Lerini destekleyen Azure bölgelerinde desteklenir. Bölgenizde hem **Sanal Makineler > DS serisi** hem de **Depolama > Disk depolamanın** mevcut olup olmadığını görmek için [bu listeyi](https://azure.microsoft.com/regions/services/) kullanın.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Sanal dizisi (model 1200)

1200 serisi StorSimple Sanal dizisi kullanılıyorsa, sanal disk görüntüsü tüm Azure bölgelerinde desteklenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Çeşitli StorSimple modelleriyle ilgili fiyatlandırma](https://azure.microsoft.com/pricing/calculator/#storsimple2)hakkında daha fazla bilgi edinin.
* [StorSimple depolama hesabınızı yönetme](storsimple-8000-manage-storage-accounts.md)hakkında daha fazla bilgi edinin.
* StorSimple [cihazınızı yönetmek Için storsimple Aygıt Yöneticisi hizmetini kullanma](storsimple-8000-manager-service-administration.md)hakkında daha fazla bilgi edinin.
