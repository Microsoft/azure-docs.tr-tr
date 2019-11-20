---
title: Azure CDN, içerik teslim türü için en iyi duruma getirme
description: Azure CDN, içerik teslim türü için en iyi duruma getirme
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: da8f17da9225da1d2b92bd8515d645bce9a1bbaa
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593642"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Azure CDN, içerik teslim türü için en iyi duruma getirme

İçerik için geniş global kullanıcılara dağıtmanıza, içeriğinizi en iyi duruma getirilmiş tesliminin sağlanması önemlidir. [Azure içerik teslim ağı (CDN)](cdn-overview.md) sahip olduğunuz içerik türüne göre deneyiminin en iyi duruma getirebilirsiniz. İçerik, bir Web sitesi, canlı akış, bir video veya büyük boyutlu bir dosyayı indirmek için olabilir. Bir CDN uç noktası oluşturduğunuzda, belirttiğiniz bir senaryoda **için en iyi duruma getirilmiş** seçeneği. Seçiminiz hangi en iyi duruma getirme CDN uç noktasından teslim edilen içeriği uygulanan belirler.

İyileştirme seçenekleri içerik teslimi performansını artırmak için en iyi yöntem davranışları kullanmak üzere tasarlanmış ve daha iyi kaynak boşaltabilirsiniz. Senaryo seçimlerinizi kısmi önbelleğe alma, nesne Öbekleme ve kaynak hata yeniden deneme ilkesi yapılandırmalarını değiştirerek performansını etkiler. 

Bu makalede, en iyi duruma getirme özellikleri ve ne zaman kullanmanız gerektiğine bir genel bakış sağlar. Özellikler ve sınırlamalar hakkında daha fazla bilgi için tek tek her optimizationtype üzerinde ilgili makalelerine bakın.

> [!NOTE]
> Bir CDN uç noktası oluşturduğunuzda **için en iyi duruma getirilmiş** seçenekleri uç noktası oluşturuldu profilinin türüne göre değişebilir. Azure CDN sağlayıcıları geliştirme senaryoya bağlı olarak farklı şekilde uygulanır. 

## <a name="provider-options"></a>Sağlayıcı seçenekleri

**Azure CDN standart Microsoft gelen** profillerini aşağıdaki iyileştirmeleri destekler:

* [Genel web teslimatı](#general-web-delivery). Bu iyileştirme, medya akışı için de kullanılır ve büyük dosya indirin.

> [!NOTE]
> Dinamik site hızlandırma Microsoft gelen aracılığıyla sunulanlar [Azure ön kapısı Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

**Azure CDN standart Verizon** ve **verizon'dan Azure CDN Premium** profilleri, aşağıdaki iyileştirmeleri destekler:

* [Genel web teslimatı](#general-web-delivery). Bu iyileştirme, medya akışı için de kullanılır ve büyük dosya indirin.

* [Dinamik site hızlandırma](#dynamic-site-acceleration) 


**Azure CDN standart Akamai** profilleri, aşağıdaki iyileştirmeleri destekler:

* [Genel web teslimatı](#general-web-delivery) 

* [Genel medya akışı](#general-media-streaming)

* [İsteğe bağlı video medya akışı](#video-on-demand-media-streaming)

* [Büyük dosya indirme](#large-file-download)

* [Dinamik site hızlandırma](#dynamic-site-acceleration) 

Microsoft, teslim için en iyi sağlayıcısı seçmek için farklı sağlayıcılar arasında performans çeşitlemeleri test önerir.

## <a name="select-and-configure-optimization-types"></a>Seçin ve iyileştirme türlerini yapılandır

Bir CDN uç noktası oluşturduğunuzda senaryo en iyi şekilde eşleşen bir iyileştirme türü ve sunmak için uç nokta istediğiniz içerik türü seçin. **Genel web teslimatı** varsayılan seçim. Mevcut **akamai'den Azure CDN standart** uç noktaları yalnızca iyileştirme seçeneği herhangi bir zamanda güncelleştirebilirsiniz. Bu değişiklik, Azure CDN teslim kesme değil. 

1. İçinde bir **akamai'den Azure CDN standart** profilini, bir uç nokta seçin.

    ![Uç nokta seçimi](./media/cdn-optimization-overview/01_Akamai.png)

2. Ayarları altında **iyileştirme**. Ardından, bir türden **için en iyi duruma getirilmiş** aşağı açılan listesi.

    ![En iyi duruma getirme ve türü seçimi](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Belirli senaryolar için iyileştirme

CDN uç noktası bu senaryoları biri için en iyi duruma getirebilirsiniz. 

### <a name="general-web-delivery"></a>Genel web teslimatı

Genel web teslimatı en yaygın bir iyileştirme seçenektir. Web sayfalarını ve web uygulamaları gibi genel web içerik iyileştirme için tasarlanmıştır. Bu iyileştirme, dosya için de kullanılabilir ve videosunu indirir.

Tipik bir Web sitesi, statik ve dinamik içerik içerir. Statik içerik, görüntü, JavaScript kitaplıklarını ve önbelleğe alınmış ve farklı kullanıcılar için teslim stil sayfaları içerir. Dinamik içerik, bir kullanıcı profili için uyarlanmış haber öğelerini gibi bireysel bir kullanıcı için kişiselleştirilmiş. Her kullanıcı için benzersiz olduğundan, alışveriş sepeti içeriği gibi dinamik içerik önbelleğe değil. Genel web teslimatı, tüm Web sitenizin en iyi duruma getirebilirsiniz. 

> [!NOTE]
> Kullanıyorsanız bir **akamai'den Azure CDN standart** profilini, ortalama dosyanızın boyutu 10 MB'den küçükse, bu en iyi duruma getirme türünü seçin. Ortalama dosyanızın boyutu 10 MB'den daha büyük ise, aksi takdirde seçin **büyük dosya indirme** gelen **için en iyi duruma getirilmiş** aşağı açılan listesi.

### <a name="general-media-streaming"></a>Genel medya akışı

Uç nokta kullanmanız gerekiyorsa için canlı akış ve isteğe bağlı video akışı, genel medya akışı iyileştirme türü seçin.

İstemcide video içeriğinin sık sık arabelleğe alma gibi geç ulaşması paketleri deneyimini neden olabileceği için medya akışı zamana duyarlı. Medya akışı en iyi duruma getirme, medya içeriği teslim gecikme süresini azaltır ve kullanıcılara bir kesintisiz akış deneyimi sunar. 

Bu senaryo, Azure media service müşterileri için yaygındır. Azure media services'ı kullandığınızda, canlı ve isteğe bağlı akış için kullanılabilen bir tek akış uç noktasını alın. Bu senaryo ile müşteriler Canlı isteğe bağlı akış için değiştirdiğinden zaman başka bir uç noktaya geçiş gerek yoktur. Bu tür bir senaryo genel medya akışı iyileştirmesi destekler.

İçin **Azure CDN standart Microsoft gelen**, **verizon'dan Azure CDN standart**, ve **verizon'dan Azure CDN Premium**, genel web teslimi iyileştirme türünü kullanın. Genel akış medya içeriği teslim etme.

Medya akışı iyileştirmesi hakkında daha fazla bilgi için bkz: [iyileştirme akış medya](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>İsteğe bağlı video medya akışı

İsteğe bağlı video medya akışı iyileştirmesi isteğe bağlı video akış içeriğini artırır. İsteğe bağlı video akışı için bir uç nokta kullanırsanız, bu seçeneği kullanın.

İçin **Azure CDN standart Microsoft gelen**, **verizon'dan Azure CDN standart**, ve **verizon'dan Azure CDN Premium** profillerini, genel web teslimatı iyileştirmesi kullanın İsteğe bağlı video akış medya içeriği teslim türü.

Medya akışı iyileştirmesi hakkında daha fazla bilgi için bkz: [iyileştirme akış medya](cdn-media-streaming-optimization.md).

> [!NOTE]
> CDN uç noktası isteğe bağlı video içeriği öncelikle hizmet veriyorsa, bu en iyi duruma getirme türünü kullanın. Bu iyileştirme türü ve iyileştirme türü akış genel medya arasındaki başlıca fark, bağlantı yeniden deneme zaman aşımı ' dir. Zaman aşımı, canlı akış senaryoları ile çalışmak için çok kısadır.
>

### <a name="large-file-download"></a>Büyük dosya indirme

İçin **akamai'den Azure CDN standart** profilleri, büyük dosya indirme iyileştirilmiş içeriği 10 MB'tan büyük. Ortalama dosyanızın boyutu 10 MB'den küçükse, genel web teslimatı kullanın. Ortalama dosya boyutlarınızın tutarlı bir şekilde 10 MB'den daha büyük ise büyük dosyalar için ayrı bir uç nokta oluşturmak için daha verimli olabilir. Örneğin, üretici yazılımı veya yazılım güncelleştirmeleri genellikle büyük dosyalardır. 1\.8 GB'tan büyük dosyalar sunmak için en büyük dosya indirme iyileştirme gereklidir.

İçin **Azure CDN standart Microsoft gelen**, **verizon'dan Azure CDN standart**, ve **verizon'dan Azure CDN Premium** profillerini, genel web teslimatı iyileştirmesi kullanın büyük dosya indirme içeriği sunmak için bunu yazın. Dosya indirme boyutu bir sınırlama yoktur.

Büyük dosya iyileştirmesi hakkında daha fazla bilgi için bkz: [büyük dosya iyileştirmesi](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Dinamik site hızlandırma

 Dinamik site Hızlandırma (DSA), kullanılabilir **akamai'den Azure CDN standart**, **verizon'dan Azure CDN standart**, ve **verizon'dan Azure CDN Premium** profilleri. Bu iyileştirme kullanmak için ek bir ücreti gerektirir; Daha fazla bilgi için [Content Delivery Network fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Dinamik site hızlandırma Microsoft gelen aracılığıyla sunulur [Azure ön kapısı hizmet](https://docs.microsoft.com/azure/frontdoor/front-door-overview) genel olduğu [anycast](https://en.wikipedia.org/wiki/Anycast) yararlanarak uygulama iş yüklerinizi sunmak için Microsoft'un özel genel ağ hizmeti.

DSA gecikme süresi ve dinamik içerik performansının yararlanan çeşitli teknikler içerir. Yolun ve ağın en iyi duruma getirme, TCP iyileştirme ve diğer teknikleri içerir. 

Bu iyileştirme, önbelleğe olmayan çok sayıda yanıtları içeren bir web uygulaması hızlandırmak için kullanabilirsiniz. Arama sonuçları, kullanıma alma işlemleri veya gerçek zamanlı verileri verilebilir. Statik veriler için temel Azure CDN önbelleğe alma özellikleri kullanmaya devam edebilirsiniz. 

Dinamik site hızlandırma hakkında daha fazla bilgi için bkz: [dinamik site hızlandırma](cdn-dynamic-site-acceleration.md).



