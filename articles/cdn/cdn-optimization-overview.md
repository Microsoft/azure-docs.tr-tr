---
title: İçerik teslimi türü için Azure CDN iyileştirin
description: Azure Content Delivery Network 'ın, içerik türüne göre teslimi nasıl iyileştirileceği hakkında bilgi edinin. En iyi duruma getirme performansı ve kaynak boşaltması geliştirir.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 3d207ee09a76509a65a2645515b182f8d92753b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387929"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>İçerik teslimi türü için Azure CDN iyileştirin

Büyük küresel bir hedef kitleye içerik sundığınızda, içeriğinizin en iyi şekilde teslim edilmesini sağlamak önemlidir. [Azure Content Delivery Network (CDN)](cdn-overview.md) , sahip olduğunuz içerik türüne göre teslim deneyimini en uygun hale getirebilir. İçerik bir Web sitesi, canlı bir akış, video veya indirmek için büyük bir dosya olabilir. Bir CDN uç noktası oluşturduğunuzda, **en iyileştirilmiş for** seçeneğinde bir senaryo belirlersiniz. Seçtiğiniz, CDN uç noktasından alınan içeriğe hangi iyileştirmenin uygulanacağını belirler.

En iyi duruma getirme seçenekleri, içerik teslimi performansını ve daha iyi kaynak boşaltma 'yı iyileştirmek için en iyi uygulama davranışlarını kullanmak üzere tasarlanmıştır Senaryo seçenekleriniz, kısmi önbelleğe alma, nesne parçalama ve kaynak hatası yeniden deneme ilkesi için konfigürasyonları değiştirerek performansı etkiler. 

Bu makalede, çeşitli iyileştirme özelliklerine ve bunları ne zaman kullanmanız gerektiğine ilişkin bir genel bakış sunulmaktadır. Özellikler ve sınırlamalar hakkında daha fazla bilgi için, her bir iyileştirme türünün ilgili makalelerine bakın.

> [!NOTE]
> Bir CDN uç noktası oluşturduğunuzda, Seçenekler **Için iyileştirilmiş** , uç noktanın oluşturulduğu profil türüne göre değişiklik gösterebilir. Azure CDN sağlayıcılar, senaryoya bağlı olarak farklı yollarla geliştirme uygular. 

## <a name="provider-options"></a>Sağlayıcı seçenekleri

**Microsoft profillerinin Azure CDN Standart** aşağıdaki iyileştirmeleri destekler:

* [Genel web teslimi](#general-web-delivery). Bu iyileştirme, medya akışı ve büyük dosya indirme için de kullanılır.

> [!NOTE]
> Microsoft 'un dinamik site ivmesi, [Azure ön kapı hizmeti](../frontdoor/front-door-overview.md)aracılığıyla sunulur.

**Verizon profillerden Verizon ve Azure CDN Premium** **'dan Azure CDN Standart** aşağıdaki iyileştirmeleri destekler:

* [Genel web teslimi](#general-web-delivery). Bu iyileştirme, medya akışı ve büyük dosya indirme için de kullanılır.

* [Dinamik site hızlandırma](#dynamic-site-acceleration) 


**Akamai profillerinden Azure CDN Standart** aşağıdaki iyileştirmeleri destekler:

* [Genel web teslimi](#general-web-delivery) 

* [Genel medya akışı](#general-media-streaming)

* [İsteğe bağlı video medya akışı](#video-on-demand-media-streaming)

* [Büyük dosya indirme](#large-file-download)

* [Dinamik site hızlandırma](#dynamic-site-acceleration) 

Microsoft, teslimizin için en uygun sağlayıcıyı seçmek üzere farklı sağlayıcılar arasındaki performans çeşitlemelerini sınamanızı önerir.

## <a name="select-and-configure-optimization-types"></a>İyileştirme türlerini seçin ve yapılandırın

Bir CDN uç noktası oluşturduğunuzda, son noktanın teslim etmesini istediğiniz senaryoya ve içerik türüne en iyi eşleşen bir iyileştirme türü seçin. 

**Genel web teslimi** varsayılan seçimdir. Akamai uç noktaları **iyileştirmesinden yalnızca Azure CDN standardını** dilediğiniz zaman güncelleştirebilirsiniz. 

**Microsoft 'tan gelen Azure CDN standart** **Azure CDN Verizon** ve **Verizon 'ten Azure CDN Premium**'dan standart olarak kullanamazsınız.

1. Akamai profilinden **Azure CDN Standart** bir uç nokta seçin.

    ![Uç nokta seçimi](./media/cdn-optimization-overview/01_Akamai.png)

2. Ayarlar altında **iyileştirme**' yi seçin. Ardından, **en iyileştirilmiş for** açılan listesinden bir tür seçin.

    ![İyileştirme ve tür seçimi](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Belirli senaryolar için iyileştirme

Bu senaryolardan biri için CDN uç noktasını iyileştirebilirsiniz. 

### <a name="general-web-delivery"></a>Genel web teslimi

Genel web teslimi en yaygın iyileştirme seçeneğidir. Web sayfaları ve Web uygulamaları gibi genel Web içeriği iyileştirmesi için tasarlanmıştır. Bu iyileştirme, dosya ve video indirmeleri için de kullanılabilir.

Tipik bir Web sitesi statik ve dinamik içerik içerir. Statik içerik, önbelleğe alınıp farklı kullanıcılara teslim edilebilir görüntüler, JavaScript kitaplıkları ve stil sayfaları içerir. Dinamik içerik, Kullanıcı profiline uyarlanmış haber öğeleri gibi bireysel bir kullanıcı için özelleştirilir. Alışveriş sepeti içerikleri gibi dinamik içerik, her bir kullanıcı için benzersiz olduğundan önbelleğe alınmaz. Genel web teslimi, Web sitenizin tamamını iyileştirebilirler. 

> [!NOTE]
> **Akamai profilinden Azure CDN Standart** kullanıyorsanız, ortalama dosya BOYUTUNUZ 10 MB 'den küçükse bu en iyi duruma getirme türünü seçin. Aksi takdirde, ortalama dosya boyutunuz 10 MB 'tan büyükse, **en iyileştirilmiş for** açılan listesinden **büyük dosya indir** ' i seçin.

### <a name="general-media-streaming"></a>Genel medya akışı

Canlı akış ve isteğe bağlı video akışı için uç noktayı kullanmanız gerekiyorsa, genel medya akışı iyileştirme türünü seçin.

Medya akışı zamana duyarlıdır, çünkü istemciye gelen ve video içeriğinin sık belleğe alınması gibi paketler, yoğun bir görüntüleme deneyimine neden olabilir. Medya akışı iyileştirmesi, medya içeriği tesliminin gecikmesini azaltır ve kullanıcılar için sorunsuz bir akış deneyimi sağlar. 

Bu senaryo, Azure Media Service müşterileri için ortaktır. Azure Media Services kullandığınızda hem canlı hem de isteğe bağlı akış için kullanılabilen tek bir akış uç noktası alırsınız. Bu senaryoyla, müşterilerin canlı iken isteğe bağlı akış olarak değiştiklerinde başka bir uç noktaya geçiş yapması gerekmez. Genel medya akışı iyileştirmesi bu tür senaryoyu destekler.

**Microsoft 'tan** **Azure CDN** standart Azure CDN Standart ve Verizon ' den **Premium Azure CDN Premium**, genel akış medya içeriğini sunmak için genel web teslimi iyileştirme türünü kullanın.

Medya akışı iyileştirmesi hakkında daha fazla bilgi için bkz. [medya akışı iyileştirmesi](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>İsteğe bağlı video medya akışı

İsteğe bağlı video medya akışı iyileştirmesi, isteğe bağlı video akış içeriğini geliştirir. İsteğe bağlı video akışı için bir uç nokta kullanırsanız, bu seçeneği kullanın.

Azure CDN **Microsoft 'tan gelen Azure CDN Standart**, **Verizon 'Ten standart** ve **Verizon profillerden Azure CDN Premium** için, isteğe bağlı video akış medya içeriği sunmak için genel Web teslim iyileştirme türünü kullanın.

Medya akışı iyileştirmesi hakkında daha fazla bilgi için bkz. [medya akışı iyileştirmesi](cdn-media-streaming-optimization.md).

> [!NOTE]
> CDN uç noktası öncelikle isteğe bağlı video içeriğine hizmet veriyorsa, bu iyileştirme türünü kullanın. Bu iyileştirme türü ile genel medya akışı iyileştirme türü arasındaki önemli fark, bağlantı yeniden deneme zaman aşımına uğrar. Zaman aşımı, canlı akış senaryolarıyla çalışmanın çok daha kısadır.
>

### <a name="large-file-download"></a>Büyük dosya indirme

**Akamai profillerden Azure CDN Standart** için, büyük dosya INDIRMELERI 10 MB 'tan büyük içerik için iyileştirilmiştir. Ortalama dosya boyutunuz 10 MB 'den küçükse genel web teslimi kullanın. Ortalama dosya boyutlarınız 10 MB 'den sürekli olarak büyükse, büyük dosyalar için ayrı bir uç nokta oluşturulması daha verimli olabilir. Örneğin, bellenim veya yazılım güncelleştirmeleri genellikle büyük dosyalardır. 1,8 GB 'tan büyük dosyaları teslim etmek için büyük dosya indirme iyileştirmesi gereklidir.

Azure CDN **Microsoft 'tan gelen Azure CDN Standart**, **Verizon 'Ten standart** ve **Verizon profillerden Azure CDN Premium** için, büyük dosya indirme içeriği sağlamak için genel Web teslim iyileştirme türünü kullanın. Dosya indirme boyutuyla ilgili bir sınırlama yoktur.

Büyük dosya iyileştirmesi hakkında daha fazla bilgi için bkz. [büyük dosya iyileştirmesi](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Dinamik site hızlandırma

 Dinamik site hızlandırma (DSA), Akamai ' dan standart **Azure CDN Standart**, **Verizon 'ten standart Azure CDN** ve **Verizon profillerden Azure CDN Premium** için kullanılabilir. Bu iyileştirme, kullanmak için ek ücret içerir; daha fazla bilgi için bkz. [Content Delivery Network fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Microsoft 'un dinamik site ivmesi, Microsoft 'un özel küresel ağını kullanarak uygulama iş yüklerinizi sunmaya yönelik küresel bir [her noktaya](https://en.wikipedia.org/wiki/Anycast) hizmet olan [Azure ön kapı hizmeti](../frontdoor/front-door-overview.md) aracılığıyla sunulur.

DSA, dinamik içeriğin gecikmesi ve performansına faydalanabilir çeşitli teknikler içerir. Yönlendirme ve ağ iyileştirme, TCP iyileştirmesi ve daha fazlasını içeren teknikler. 

Bu iyileştirmeyi, önbelleğe alınamayan çok sayıda yanıt içeren bir Web uygulamasını hızlandırmak için kullanabilirsiniz. Arama sonuçları, kullanıma alma işlemleri veya gerçek zamanlı veriler örnekleri verilmiştir. Statik veriler için çekirdek Azure CDN önbelleğe alma özelliklerini kullanmaya devam edebilirsiniz. 

Dinamik site hızlandırma hakkında daha fazla bilgi için bkz. [dinamik site hızlandırma](cdn-dynamic-site-acceleration.md).