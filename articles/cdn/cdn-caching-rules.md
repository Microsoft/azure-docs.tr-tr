---
title: Azure CDN önbelleğe alma kuralları ile önbelleğe alma davranışını denetleme | Microsoft Docs
description: CDN önbelleğe alma kuralları ayarlayın veya hem genel hem de bir URL yolu ve dosya uzantıları gibi koşullarla varsayılan önbellek sona erme davranışını değiştirmek için kullanabilirsiniz.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: d4ab3ca32f229e92ae6bae5906c6c70593e9f9d3
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594030"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Denetim Azure CDN önbelleğe alma kuralları ile önbelleğe alma davranışı

> [!NOTE] 
> Önbelleğe alma kuralları, yalnızca kullanılabilir **verizon'dan Azure CDN standart** ve **akamai'den Azure CDN standart** profilleri. İçin **verizon'dan Azure CDN Premium** profilleri kullanmalıdır [Azure CDN kurallar altyapısı](cdn-rules-engine.md) içinde **Yönet** benzer işlevselliği için portal.
 
Azure Content Delivery Network (CDN), dosyaların önbelleğe nasıl alındığını denetlemek için iki yol sunar: 

- Önbelleğe alma kuralları: Bu makalede, ayarlamak veya hem genel hem de bir URL yolu ve dosya uzantısı gibi özel koşullar ile varsayılan önbellek sona erme davranışını değiştirmek için önbelleğe alma kuralları content delivery Network'te (CDN) nasıl kullanabileceğinizi açıklar. Azure CDN iki tür önbelleğe alma kuralı sağlar:

   - Genel önbelleğe alma kuralları: Uç nokta için tüm istekleri etkileyen profilinizde her uç nokta için bir genel önbelleğe alma kuralı ayarlayabilirsiniz. Genel önbelleğe alma kuralı ayarlandığında tüm HTTP önbellek yönergesi üst bilgilerini geçersiz kılar.

   - Özel önbelleğe alma kuralları: Profilinizde bir veya daha fazla özel önbelleğe alma kuralları her uç noktası için ayarlayabilirsiniz. Özel önbelleğe alma kuralları ayarlandığında belirli yollar ve dosya uzantılarıyla eşleşir, sırasıyla işlenir ve genel önbelleğe alma kuralını geçersiz kılar. 

- Sorgu dizesini önbelleğe alma: İstekleri ile sorgu dizeleri için önbelleğe alma, Azure CDN nasıl işler ayarlayabilirsiniz. Bilgi için [denetimi Azure CDN önbelleğe alma davranışını sorgu dizeleriyle](cdn-query-string.md). Dosyayı önbelleğe alınabilir bir durumda değilse, sorgu dizesi ayarı tabanlı önbelleğe alma, kurallar ve CDN varsayılan davranışlar üzerinde hiçbir etkisi vardır.

Varsayılan önbelleğe alma davranışını ve yönergesi üst bilgilerinin önbelleğe alma hakkında daha fazla bilgi için bkz. [önbelleğe alma nasıl işler](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Azure CDN önbelleğe alma kuralları erişme

1. Azure portalını açın, CDN profilini seçin ve ardından bir uç nokta seçin.

2. Ayarların altındaki sol bölmede **Önbelleğe alma kuralları**’nı seçin.

   ![CDN Önbelleğe alma kuralları düğmesi](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   **Önbelleğe alma kuralları** sayfası görüntülenir.

   ![CDN Önbelleğe alma kuralları sayfası](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Önbelleğe alma davranışı ayarları
Genel ve özel önbelleğe alma kuralları için aşağıdakileri belirtebilirsiniz **önbelleğe alma davranışını** ayarları:

- **Önbelleği atla**: Önbellekte değil ve sağlanan kaynak önbellek yönergesi üstbilgileri yoksay.

- **Geçersiz kılma**: Sağlanan kaynak önbellek süresi yoksayın; Bunun yerine, sağlanan önbellek süresi kullanın. Cache-control kılmaz: no-cache.

- **Eksikse Ayarla**: Sağlanan kaynak önbellek yönergesi üstbilgileri varsa dikkate; Aksi takdirde, sağlanan önbellek süresi kullanın.

![Genel önbelleğe alma kuralları](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Özel önbelleğe alma kuralları](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Önbellek sona erme süresi
Genel ve özel önbelleğe alma kuralları için gün, saat, dakika ve saniye önbellek sona erme süresi belirtebilirsiniz:

- İçin **geçersiz kılma** ve **eksikse Ayarla** **önbelleğe alma davranışını** ayarları, geçerli önbellek süreleri aralığında 0 saniye ve 366 günü. 0 saniye değerini, CDN içeriği önbelleğe alır, ancak kaynak sunucu her bir istekle düzeltin gerekir.

- İçin **önbelleği atla** ayarlama, önbelleğe alma süresi 0 saniyeye otomatik olarak ayarlanır ve değiştirilemez.

## <a name="custom-caching-rules-match-conditions"></a>Özel önbelleğe alma kuralları eşleştirme koşulları

Özel önbellek kuralları için iki eşleştirme koşulları kullanılabilir:
 
- **Yol**: Bu durum, URL'nin etki alanı adı dışında bir yol ile eşleşen ve joker karakter sembolünü destekler (\*). Örneğin, _/myfile.html_, _/my/folder / *_ , ve _/my/images/*.jpg_. En fazla uzunluk 260 karakterdir.

- **Uzantı**: Bu durum, istenen dosyanın dosya uzantısı ile eşleşir. Eşleştirilecek virgülle ayrılmış dosya uzantıları listesini sağlar. Örneğin, _.jpg_, _.mp3_, veya _.png_. Uzantıları sayısı 50'dir ve uzantı başına karakter sayısı 16'dır. 

## <a name="global-and-custom-rule-processing-order"></a>Genel ve özel kural işleme sırası
Genel ve özel önbelleğe alma kuralları aşağıdaki sırayla işlenir:

- Genel önbelleğe alma kuralları varsayılan CDN önbelleğe alma davranışını (HTTP önbellek yönergesi üst bilgi ayarları) göre önceliklidir. 

- Özel önbelleğe alma kuralları genel önbelleğe alma kuralları, burada geçerli önceliklidir. Özel önbelleğe alma kuralları, yukarıdan aşağıya sırasında işlenir. Bir istek koşulların her ikisi de eşleşirse, diğer bir deyişle, kurallar listesinin altındaki listenin üst kısmındaki kurallar önceliklidir. Bu nedenle, daha belirli kurallar alt listesinde yerleştirmeniz gerekir.

**Örnek**:
- Genel önbelleğe alma kuralı: 
   - Önbelleğe alma davranışı: **geçersiz kılma**
   - Önbellek sona erme süresi: 1 gün

- Özel önbelleğe alma kuralı #1:
   - Eşleşme koşulu: **Yolu**
   - Eşleşen değer:   _/home / *_
   - Önbelleğe alma davranışı: **geçersiz kılma**
   - Önbellek sona erme süresi: 2 gün

- Özel önbelleğe alma kuralı #2:
   - Eşleşme koşulu: **Uzantı**
   - Eşleşen değer: _.html_
   - Önbelleğe alma davranışı: **Eksikse Ayarla**
   - Önbellek sona erme süresi: 3 gün

Bu kurallar ayarlandığında, bir istek için  _&lt;uç noktası ana bilgisayar&gt;_ özel önbelleğe alma kuralı ayarlamak için #2.azureedge.net/home/index.html Tetikleyicileri: **Eksikse Ayarla** ve 3 gün. Bu nedenle, varsa *index.html* dosyasının `Cache-Control` veya `Expires` HTTP üstbilgileri ödenmiş oldukları; bu üstbilgileri ayarlanmazsa dosyası için 3 gün Aksi takdirde, önbelleğe alınır.

> [!NOTE] 
> Bir kural değişikliği önce önbelleğe alınan dosyalar, kaynak önbellek süresi ayarı koruyun. Önbellek süreler sıfırlamak için gereken [dosya temizleme](cdn-purge-endpoint.md). 
>
> Azure CDN yapılandırma değişiklikleri ağ üzerinden yayılması biraz zaman alabilir: 
> - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
> - İçin **verizon'dan Azure CDN standart** profilleri için yayma genellikle 10 dakika içinde tamamlanır.  
>

## <a name="see-also"></a>Ayrıca bkz.

- [Önbelleğe alma nasıl işler?](cdn-how-caching-works.md)
- [Öğretici: Azure CDN önbelleğe alma kuralları ayarlayın](cdn-caching-rules-tutorial.md)
