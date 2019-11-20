---
title: Azure CDN üzerinden dinamik site hızlandırma
description: Azure CDN, dinamik içerik dosyaları için dinamik site Hızlandırma (DSA) en iyi duruma getirme destekler.
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
ms.openlocfilehash: 08e705d3c3623d4d02ccaea609eb0555aa1c8e33
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593914"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Azure CDN üzerinden dinamik site hızlandırma

Sosyal medya, elektronik ticaret ve kişiselleştirilmiş hyper web açılımı ile hızla artan yüzde son kullanıcılara sunulan içeriğin gerçek zamanlı olarak oluşturulur. Kullanıcılar, kendi tarayıcı, konum, cihaz veya ağ bağımsız, hızlı, güvenilir ve kişiselleştirilmiş web deneyim bekler. Ancak, bu deneyimleri de ilgi çekici hale çok yeniliklerini yavaş sayfa yüklemeleri ve tüketici deneyiminin kalitesini tehlikeye atabilir. 

Standart içerik teslim ağı (CDN) özelliği, statik dosyaların teslimini hızlandırın olanağı dosyaları önbelleğe almak için yakın son kullanıcılara içerir. Sunucu, kullanıcı davranışlarını yanıt içeriği oluşturur. ancak, dinamik web uygulamaları ile edge konumlarda o içeriği önbelleğe mümkün değildir. İçeriklerin teslimini hızlandırma daha geleneksel Uçlarda önbelleğe alma işleminde daha karmaşıktır ve teslimat yeni tüm veriler yol boyunca her öğe ince ayarlar bir uçtan uca çözüm gerektirir. Azure CDN dinamik site Hızlandırma (DSA) iyileştirmesi sayesinde, dinamik içerik web sayfalarına ait performansla yaşamları geliştirildi.

**Akamai'den Azure CDN** ve **verizon'dan Azure CDN** DSA iyileştirme ile her ikisini de teklif **için en iyi duruma getirilmiş** uç noktası oluşturma işlemi sırasında menüsü. Dinamik site hızlandırma Microsoft gelen aracılığıyla sunulanlar [Azure ön kapısı Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

> [!Important]
> İçin **akamai'den Azure CDN** profilleri, oluşturulduktan sonra bir CDN uç noktası iyileştirmesi değiştirme izin verilir.
>   
> **Verizon Azure CDN** profilleri için, bir CDN uç noktası oluşturulduktan sonra uç nokta iyileştirmesini değiştiremezsiniz.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>CDN uç nokta yapılandırması dinamik dosyaların teslimini hızlandırın

Dinamik dosyaların teslimini iyileştirecektir için bir CDN uç noktası yapılandırmak için ya da Azure portalı, REST API veya istemci SDK'larından birini programlı olarak aynı şeyi yapmak için kullanabilirsiniz. 

**Azure portalını kullanarak DSA iyileştirme için bir CDN uç noktasını yapılandırmak için:**

1. İçinde **CDN profili** sayfasında **uç nokta**.

   ![Yeni bir CDN uç noktası ekleme](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   **Uç nokta ekleyin** bölmesi görünür.

2. Altında **için en iyi duruma getirilmiş**seçin **dinamik site hızlandırma**.

    ![DSA ile yeni bir CDN uç noktası oluşturma](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. İçin **yoklama yolu**, bir dosyaya geçerli bir yol girin.

    Araştırma yolu için DSA özgü bir özellik, ve geçerli bir yol oluşturmak için gereklidir. DSA kullanan küçük *yoklama yolu* ağ yönlendirme yapılandırmaları CDN için en iyi duruma getirmek için kaynak sunucuda dosya yerleştirilir. Araştırma yolu dosya için indirme ve örnek dosyayı sitenize karşıya veya noktanızın boyutu yaklaşık 10 KB olduğundan üzerinde var olan bir varlık kullanın.

4. Gerekli uç nokta seçeneklerini girin (daha fazla bilgi için [yeni bir CDN uç noktası](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), ardından **Ekle**.

   CDN uç nokta oluşturulduktan sonra DSA iyileştirmeler belirli ölçütlere uyan tüm dosyaları için geçerlidir. 


**Mevcut bir uç nokta DSA (Azure CDN Akamai profillerinden) yapılandırmak için:**

1. İçinde **CDN profili** sayfasında, değiştirmek istediğiniz uç noktayı seçin.

2. Sol bölmeden **iyileştirme**. 

   **İyileştirme** sayfası görüntülenir.

3. Altında **için en iyi duruma getirilmiş**seçin **dinamik site hızlandırma**, ardından **Kaydet**.

> [!Note]
> DSA, ek ücret alınmaz. Daha fazla bilgi için [Content Delivery Network fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>DSA iyileştirme Azure CDN'yi kullanma

Azure CDN üzerinde dinamik Site hızlandırma, aşağıdaki teknikleri kullanarak dinamik varlıkların teslimini hızlandırır:

-   [Rota iyileştirme](#route-optimization)
-   [TCP en iyi duruma getirme](#tcp-optimizations)
-   [Nesneleri önceden getirme (yalnızca akamai'den Azure CDN)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Uyarlamalı görüntü sıkıştırma (yalnızca akamai'den Azure CDN)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Rota iyileştirme

Rota iyileştirme Internet burada trafiği ve geçici olarak kesintiler sürekli ağ topolojisini değiştiriyorsunuz dinamik bir yerde olduğu için önemlidir. Sınır Ağ Geçidi Protokolü (BGP), Internet yönlendirme protokolü olmakla birlikte bulunma noktası (PoP) Ara sunucu aracılığıyla daha hızlı yollarını olabilir. 

Rota iyileştirme, bir site sürekli olarak erişilebilir ve dinamik içerik olması kaynağı için en iyi yolu olası en hızlı ve en güvenilir yol aracılığıyla son kullanıcılara gönderilir seçer. 

Akamai ağ, gerçek zamanlı veri toplamak ve kaynak ve CDN uç arasındaki en hızlı yolu belirlemek için açık Internet üzerinden varsayılan BGP yolu yanı sıra, Akamai sunucunun farklı düğümlerde çeşitli yollarını karşılaştırmak için teknikleri kullanır. Bu teknikler Internet önlemek Tıkanıklığı noktaları ve uzun yollar. 

Benzer şekilde, Verizon ağ bir DNS Anycast bileşimi, yüksek kapasiteli kullanımlarını POP ve durum denetimlerinin, en iyi rota verileri için en iyi ağ geçidi kaynağı istemciden belirlemek için.
 
Uncacheable olsa bile sonuç olarak, tamamen dinamik ve işlem içeriği daha hızlı ve daha güvenilir bir şekilde, son kullanıcılar için teslim edilir. 

### <a name="tcp-optimizations"></a>TCP en iyi duruma getirme

İletim Denetimi Protokolü (TCP) Internet Protokolü paketinin IP ağ üzerinde uygulamalar arasında bilgi sunmak için kullanılan bir standarttır.  Varsayılan olarak, uygun ölçekte verimsizliklere neden ağ congestions önlemek için bir TCP bağlantı yanı sıra sınırlarını ayarlamak için birkaç geri yönlü istekleri gereklidir. **Akamai'den Azure CDN** üç alanda iyileştirerek bu sorunu giderir: 

 - [TCP yavaş başlatma ortadan kaldırır.](#eliminating-tcp-slow-start)
 - [Yararlanarak kalıcı bağlantılar](#leveraging-persistent-connections)
 - [TCP paket parametrelerini ayarlama](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>TCP yavaş başlatma ortadan kaldırır.

TCP *yavaş başlangıç* ağ üzerinden gönderilen veri miktarını sınırlandırarak Ağ Tıkanıklığı engelleyen TCP protokolünün bir algoritmadır. Bu gönderen ve alıcı arasındaki küçük sıkışma penceresi boyutları ile paket kaybı algılandı veya maksimum sınıra kadar başlamanızı sağlar.

 Her ikisi de **akamai'den Azure CDN** ve **verizon'dan Azure CDN** profilleri ortadan kaldırın, aşağıdaki üç adımı ile TCP yavaş başlatma:

1. Sistem durumu ve bant genişliği izleme uç PoP sunucuları arasındaki bant genişliği ölçmek için kullanılır.
    
2. Her sunucunun ağ koşulları ve sunucu sağlığı etrafında diğer POP farkında olmasını sağlayın ölçümleri uç PoP sunucular arasında paylaşılır.  
    
3. CDN uç sunucularını, yakınlık diğer CDN uç sunucularıyla iletişim kurarken hangi en iyi pencere boyutunu olmalıdır gibi bazı iletim parametreleri hakkında varsayımlar. Bu adım, CDN uç sunucularını arasındaki bağlantının durumunu daha yüksek paket veri aktarımlarını özelliğine sahip olup olmadığını ilk sıkışma penceresi boyutu artırılabilir anlamına gelir.  

#### <a name="leveraging-persistent-connections"></a>Yararlanarak kalıcı bağlantılar

CDN kullanarak, daha az benzersiz makine kaynağınıza yönelik doğrudan bağlanan kullanıcıları doğrudan karşılaştırıldığında kaynak sunucunuza bağlanın. Azure CDN, ayrıca daha az kaynak bağlantılarıyla birlikte kurmak için kullanıcı isteklerini havuza ekler.

Daha önce belirtildiği gibi çeşitli el sıkışması istekler TCP bağlantısı kurmak için gereklidir. Tarafından uygulanan kalıcı bağlantılar `Keep-Alive` HTTP üst bilgisi, yeniden mevcut TCP bağlantılarını birden çok HTTP isteklerini gidiş dönüş süreleri kaydedip teslimini hızlandırın. 

**Verizon'dan Azure CDN** aynı zamanda kapatılan gelen açık bir bağlantı önlemek için TCP bağlantı üzerinden düzenli tutma paketleri gönderir.

#### <a name="tuning-tcp-packet-parameters"></a>TCP paket parametrelerini ayarlama

**Akamai'den Azure CDN** sitede aşağıdaki teknikleri kullanarak katıştırılmış içerik almak için gerekli uzun mesafeli gidiş dönüş azaltır ve sunucudan sunucuya bağlantılar yöneten parametreleri ayarlar:

- Daha fazla paket için bir onay beklemeden gönderilebilir. böylece ilk sıkışma penceresi artırma.
- İlk yeniden aktarım zaman aşımı, böylece kaybı algılandı ve aktarım daha hızlı gerçekleşir kısaltır.
- Paket iletim kaybolduğu varsayılarak önce bekleme süresini azaltmak için minimum ve maksimum yeniden aktarım zaman aşımı kısaltır.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Nesneleri önceden getirme (yalnızca akamai'den Azure CDN)

Çoğu Web sitesi, görüntü ve betik gibi çeşitli diğer kaynaklara başvuran bir HTML sayfası oluşur. Genellikle, bir istemci bir Web sayfası istediğinde, tarayıcısı ilk indirir ve HTML nesneyi ayrıştırır ve sonra sayfanın tam olarak yüklemek için gerekli bağlı varlıkları için ek istekler yapar. 

*Önceden getirme* tarayıcıya HTML sunulur ve tarayıcı bile bu nesne isteği yapan önce görüntü ve betik almak için bir yöntem HTML sayfasına katıştırılır. 

Önceden getirme seçeneğiyle, CDN istemci tarayıcısına temel HTML sayfasına hizmet veren zaman açık CDN HTML dosyasını ayrıştırır ve bağlı tüm kaynaklar için ek istekleri ve önbelleğinde depolayın. İstemci bağlı varlıkları için istek yaptığında, CDN uç sunucu zaten istenen nesnelere sahip ve bunları bir gidiş dönüş kaynağı olmadan hemen görebilir. Bu iyileştirme, içeriği önbelleğe alınabilir ve alınamayan fayda sağlar.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Uyarlamalı görüntü sıkıştırma (yalnızca akamai'den Azure CDN)

Bazı cihazlar, özellikle mobil olanları zaman zaman daha yavaş ağ hızları karşılaşırsınız. Bu senaryolarda, tam çözünürlüklü görüntüleri uzun süredir bekleyen yerine daha küçük resimleri, Web sayfasında daha hızlı bir şekilde almak için bir kullanıcı için daha faydalı olacaktır.

Bu özellik otomatik olarak ağ kalitesini izler ve ağ hızları teslim süresini iyileştirmek daha yavaş olduğunda, standart JPEG sıkıştırma yöntemlerini kullanır.

Uyarlamalı görüntü sıkıştırma | Dosya uzantıları  
--- | ---  
JPEG sıkıştırma | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Önbelleğe alma

Kaynak içerdiğinde bile DSA ile önbelleğe alma CDN varsayılan olarak kapalıdır `Cache-Control` veya `Expires` yanıt üstbilgileri. DSA, genellikle her istemci için benzersiz olduğundan, önbelleğe alınması gerektiğini değil dinamik varlıklar için kullanılır. Önbelleğe alma, bu davranışı bozabilir.

Statik ve dinamik varlıklar karışımını içeren bir Web sitesi varsa, en iyi performansı elde etmek için bir karma yaklaşım en iyisidir. 

İçin **verizon'dan Azure CDN standart** ve **akamai'den Azure CDN standart** profilleri için özel DSA uç noktaları kullanarak önbelleğe alma özelliğini kapatabilir [önbelleğe alma kuralları](cdn-caching-rules.md).

Önbelleğe alma kuralları erişmek için:

1. Gelen **CDN profili** sayfasında, ayarları, select **önbelleğe alma kuralları**.  
    
    ![CDN Önbelleğe alma kuralları düğmesi](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    **Önbelleğe alma kuralları** sayfası açılır.

2. DSA uç noktanız için önbelleğe almayı etkinleştirmek için bir genel veya özel önbelleğe alma kuralı oluşturun. 

İçin **verizon'dan Azure CDN Premium** profilleri yalnızca açmak için belirli DSA uç noktaları kullanarak önbelleğe alma üzerinde [kurallar altyapısı](cdn-rules-engine.md). Yalnızca kendi profilinizi DSA için optimize edilmiş uç oluşturulan tüm kuralları etkiler. 

Kural altyapısı erişmek için:
    
1. Gelen **CDN profili** sayfasında **Yönet**.  
    
    ![CDN profili Yönet düğmesi](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    CDN yönetim portalına açılır.

2. CDN Yönetim Portalı'ndan seçin **ADN**, ardından **kurallar altyapısı**. 

    ![Kural altyapısı DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternatif olarak, iki CDN uç noktası kullanabilirsiniz: bir uç nokta iyileştirilmiş dinamik varlıklar ve başka bir uç nokta gibi genel bir statik iyileştirme türü ile en iyi duruma getirilmiş sunmak için DSA ile teslim önbelleğe varlıklarına web teslimi. Kullanmayı planladığınız CDN uç noktasında varlık doğrudan bağlanmak için Web URL'leri değiştirin. 

Örneğin: `mydynamic.azureedge.net/index.html` dinamik bir sayfa ve DSA uç noktasından yüklenir.  Html sayfasında JavaScript kitaplıkları veya statik CDN uç noktasından gibi yüklenen görüntü gibi birden çok statik varlıklar başvuran `mystatic.azureedge.net/banner.jpg` ve `mystatic.azureedge.net/scripts.js`. 



