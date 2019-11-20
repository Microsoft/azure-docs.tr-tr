---
title: Medya akışı Azure CDN ile iyileştirme
description: Akış medya dosyaları için sorunsuz bir şekilde teslim iyileştirme
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
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: c6ed546735058e330368151adb0df7323f943050
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593664"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Medya akışı Azure CDN ile iyileştirme 
 
Yüksek çözünürlüklü video kullanımını sorunlar için verimli büyük dosyaların teslimini oluşturur internetteki artmaktadır. Müşteriler, isteğe bağlı kesintisiz video kayıttan beklemez veya dünyanın dört bir yanındaki ağları ve istemcilerin çeşitli video varlıklarının Canlı. Medya dosyaları akışı için bir hızlı ve verimli bir teslim mekanizması, sorunsuz ve keyifli tüketici deneyimi sağlamak için önemlidir.  

Canlı akış medya eşzamanlı görüntüleyiciler sayısı ve büyük boyutları nedeniyle teslim etmek özellikle zor bir işlemdir. Kullanıcılar, gecikmelere neden. Canlı akışlar önceden önbelleğe alınamaz ve büyük gecikme görüntüleyenler için kabul edilebilir değildir çünkü video parçasının zamanında teslim edilmelidir. 

Akış isteği desenleri, yeni zorluklar da sağlar. Milyonlarca izleyiciye binlerce popüler bir canlı akışı veya yeni bir seri için isteğe bağlı video yayımlandığında akış aynı anda isteyebilir. Bu durumda, akıllı istek birleştirme varlıkları henüz önbelleğe değil, kaynak sunucuları zahmet önemlidir.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Medya akışı için Microsoft Azure CDN'den iyileştirmeleri

**Azure CDN standart Microsoft gelen** uç noktaları, doğrudan genel web teslimi iyileştirme türü kullanarak akış medya varlıklarını sunun. 

İyileştirme için akış medya **Azure CDN standart Microsoft gelen** için Canlı etkilidir veya isteğe bağlı video bağımsız medya parçasının tesliminde medya akış. Bu işlem, aşamalı indirme aracılığıyla veya bayt aralığı isteklerini kullanarak aktarılan tek büyük bir varlık farklıdır. Medya teslimi o stilin hakkında daha fazla bilgi için bkz. [Azure CDN ile büyük dosya indirme iyileştirme](cdn-large-file-optimization.md).

Genel medya teslim veya isteğe bağlı video medya teslim iyileştirme türleri medya varlıklarını daha hızlı teslim etmek için arka uç iyileştirmeler ile Azure Content Delivery Network (CDN) kullanın. Bunlar ayrıca yapılandırmaları zamanla öğrendiğimiz en iyi deneyimleri temel medya varlıkları için kullanır.

### <a name="partial-cache-sharing"></a>Kısmi önbellek paylaşımı
Kısmi önbellek paylaşımı yeni istekler için kısmen önbelleğe alınan içerik sunmak için CDN sağlar. Örneğin, CDN ilk isteği önbellek isabetsizliği sonuçlanırsa, istek kaynağa gönderilir. Bu içeriği eksik CDN önbelleğe yüklenir ancak CDN giden diğer isteklerin bu verileri almak başlayabilirsiniz. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Medya akışı verizon'dan Azure CDN için iyileştirme

**Azure CDN standart Verizon** ve **verizon'dan Azure CDN Premium** uç noktaları, doğrudan genel web teslimi iyileştirme türü kullanarak akış medya varlıklarını sunun. Bazı özellikler CDN üzerinde doğrudan ortam varlıkları varsayılan olarak sunma konusunda da yardımcı olur.

### <a name="partial-cache-sharing"></a>Kısmi önbellek paylaşımı

Kısmi önbellek paylaşımı yeni istekler için kısmen önbelleğe alınan içerik sunmak için CDN sağlar. Örneğin, CDN ilk isteği önbellek isabetsizliği sonuçlanırsa, istek kaynağa gönderilir. Bu içeriği eksik CDN önbelleğe yüklenir ancak CDN giden diğer isteklerin bu verileri almak başlayabilirsiniz. 

### <a name="cache-fill-wait-time"></a>Önbellek dolgu bekleme süresi

 HTTP yanıt üst bilgilerini kaynak sunucudan gelen kadar sonraki istekler için aynı kaynak tutacak uç sunucusu önbellek dolgu bekleme süresi özelliği zorlar. Zamanlayıcı süresi dolmadan önce kaynaktan HTTP yanıt üstbilgilerinin ulaşırsa, büyüyen önbellek dışına beklemeye tüm istekleri sunulur. Aynı zamanda, önbellek, veri kaynağından tarafından doldurulur. Varsayılan olarak, önbellek dolgu bekleme süresi 3.000 milisaniye olarak ayarlanır. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Medya akışı akamai'den Azure CDN için iyileştirme
 
**Azure CDN standart Akamai** akış medya varlıklarını dünya ölçeğinde kullanıcılar için verimli bir şekilde sunan bir özellik sunar. Kaynak sunucularındaki yükü azaltır çünkü özellik gecikmeleri azaltır. Bu özellik, fiyatlandırma katmanını standart Akamai ile kullanılabilir. 

İyileştirme için akış medya **akamai'den Azure CDN standart** etkili olduğu Canlı veya isteğe bağlı video akış bağımsız medya parçasının tesliminde medya. Bu işlem, aşamalı indirme aracılığıyla veya bayt aralığı isteklerini kullanarak aktarılan tek büyük bir varlık farklıdır. Medya teslimi o stilin hakkında daha fazla bilgi için bkz. [büyük dosya iyileştirmesi](cdn-large-file-optimization.md).

Genel medya teslim veya isteğe bağlı video medya teslim iyileştirme türleri medya varlıklarını daha hızlı teslim etmek için arka uç iyileştirmeler ile CDN kullanın. Bunlar ayrıca yapılandırmaları zamanla öğrendiğimiz en iyi deneyimleri temel medya varlıkları için kullanır.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Medya akışı en iyi duruma getirmek için bir Akamai CDN uç noktasını yapılandırma
 
Azure portal aracılığıyla büyük dosya teslimi iyileştirmek için içerik teslim ağı (CDN) uç noktanızı yapılandırabilirsiniz. Bunu yapmak için REST API veya istemci SDK'larından birini kullanabilirsiniz. İşlem için Azure Portalı aracılığıyla aşağıdaki adımlarda bir **akamai'den Azure CDN standart** profili:

1. Yeni bir uç nokta üzerinde bir Akamai eklemek için **CDN profili** sayfasında **uç nokta**.
  
    ![Yeni uç nokta](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. İçinde **için en iyi duruma getirilmiş** aşağı açılan listesinden **isteğe bağlı medya akış Video** isteğe bağlı video varlıklar için. Bir birleşimi canlı ve isteğe bağlı video akış bunu yaparsanız, seçin **genel medya akışı**.

    ![Akış seçilmedi](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Uç nokta oluşturduktan sonra en iyi duruma getirme belirli ölçütlere uyan tüm dosyaları için geçerlidir. Aşağıdaki bölümde, bu işlemi açıklanmaktadır. 

### <a name="caching"></a>Önbelleğe alma

Varsa **akamai'den Azure CDN standart** algılar akış bildirimi veya parça varlık olduğundan, genel web teslimatı farklı önbellek sona erme sürelerinden kullanır. (Aşağıdaki tabloda tam listesine bakın.) Cache-control veya Expires üst bilgileri ve kaynaktan gönderilen her zaman, kabul gibi. Varlık bir medya varlığını değilse genel web teslimatı için zaman aşımı süresinin kullanarak önbelleğe alır.

Çok sayıda kullanıcı henüz mevcut olmayan bir parça istediğinde kısa negatif önbellek zaman kaynağı boşaltması için kullanışlıdır. Burada paketleri bu ikinci kaynaktan kullanılamayan bir canlı akış bir örnektir. Artık önbelleğe alma aralığı, video içeriği genellikle değiştirilmez çünkü kaynak gelen istekleri boşaltması da yardımcı olur.
 

|   | Genel web teslimatı | Genel medya akışı | İsteğe bağlı video medya akışı  
--- | --- | --- | ---
Önbelleğe alma: Pozitif <br> HTTP 200 203, 300, <br> 301, 302 ve 410 | 7 gün |365 gün | 365 gün   
Önbelleğe alma: Negatif <br> HTTP 204 305, 404, <br> ve 405 | None | 1 saniye | 1 saniye
 
### <a name="deal-with-origin-failure"></a>Kaynak hatası işlem  

Genel medya teslimi ve isteğe bağlı video medya teslim kaynak zaman aşımları ve tipik istek modelleri için en iyi uygulamaları temel alan bir yeniden deneme günlük da vardır. Örneğin, genel medya teslimi için canlı ve isteğe bağlı olarak isteğe bağlı video medya dağıtımı, zamana duyarlı niteliği nedeniyle daha kısa bir bağlantı zaman aşımı kullanan olduğundan canlı akış.

Bağlantı zaman aşımına uğradığında CDN bir sayı, bir "504 - ağ geçidi zaman aşımı" hatası istemciye gönderilmeden önce kaç kez yeniden dener. 

Bir dosyayı dosya türü ve boyutu koşullar listesi eşleştiğinde, medya akışı için CDN davranışı kullanır. Aksi takdirde, genel web teslimatı kullanır.
   
### <a name="conditions-for-media-streaming-optimization"></a>Medya akışı iyileştirme için koşullar 

Aşağıdaki tabloda, iyileştirme akış medya için karşılanması için ölçüt kümesini listeler: 
 
Akış türleri desteklenir | Dosya uzantıları  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, anahtarı, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Seg parça URL yapısı <br> (normal ifade eşleştirme: ^(/.*)Seq(\d+)-Frag(\d+)
TİRE | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Kesintisiz akış | /manifest/, /QualityLevels/Fragments/
  
 
