---
title: Azure CDN aracılığıyla dinamik site hızlandırma
description: Azure CDN dinamik içerik içeren dosyalar için dinamik site hızlandırma (DSA) iyileştirmesini destekler.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 5387fdc224cd77ee5273767df5033a51dc27608c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008370"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Azure CDN aracılığıyla dinamik site hızlandırma

Sosyal medya, elektronik ticaret ve hiper kişiselleştirilmiş Web 'in açılımını yaparken son kullanıcılara sunulan içeriğin hızlı bir şekilde artması gerçek zamanlı olarak oluşturulur. Kullanıcılar tarayıcıdan, konumundan, cihazından veya ağından bağımsız olarak hızlı, güvenilir ve kişiselleştirilmiş bir Web deneyimi bekler. Bununla birlikte, bu deneyimlerin büyük bir bölümünü de yavaşlatan ve tüketici deneyiminin kalitesini riske önünde bulundurmaya yönelik yenilikler. 

Standart Content Delivery Network (CDN) özelliği, statik dosyaların teslimini hızlandırmak için son kullanıcılara daha yakın olan dosyaları önbelleğe alma özelliğini içerir. Ancak, dinamik Web uygulamaları ile, sunucu içeriği kullanıcı davranışına yanıt olarak oluşturduğundan, bu içeriğin kenar konumlarında önbelleğe alınması mümkün değildir. Bu tür içeriğin tesliminin hızlandırılmış olması geleneksel kenarından daha karmaşıktır ve her bir öğeyi, veri yolunun tamamına yönelik olarak bir bütün olarak ayrıntılı bir şekilde kontrol eden uçtan uca bir çözüm gerektirir. Azure CDN dinamik site hızlandırma (DSA) iyileştirmesi sayesinde, dinamik içeriğe sahip Web sayfalarının performansı yaşamları geliştirildi.

**Verizon 'Den** Akamai ve Azure CDN **'tan Azure CDN** , uç nokta oluşturma sırasında **iyileştirilmiş for** Menu aracılığıyla dsa iyileştirmesi sunar. Microsoft 'un dinamik site ivmesi, [Azure ön kapı hizmeti](../frontdoor/front-door-overview.md)aracılığıyla sunulur.

> [!Important]
> **Akamai profillerden Azure CDN** için, oluşturulduktan sonra bir CDN uç noktasının iyileştirmesini değiştirmenize izin verilir.
>   
> **Verizon Azure CDN** profilleri için, bir CDN uç noktası oluşturulduktan sonra uç nokta iyileştirmesini değiştiremezsiniz.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Dinamik dosyaların teslimini hızlandırmak için CDN uç noktası yapılandırması

Dinamik dosyaların teslimini iyileştirmek üzere bir CDN uç noktası yapılandırmak için Azure portal, REST API 'Lerini veya istemci SDK 'larından herhangi birini, programlama yoluyla aynı şeyi yapmak için kullanabilirsiniz. 

**Azure portal kullanarak DSA iyileştirmesi için bir CDN uç noktası yapılandırmak için:**

1. **CDN profili** sayfasında **uç nokta**' ı seçin.

   ![Yeni bir CDN uç noktası ekle](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   **Uç nokta ekleyin** bölmesi görünür.

2. **Için iyileştirilmiş** altında, **dinamik site hızlandırma**' yi seçin.

    ![DSA ile yeni bir CDN uç noktası oluşturma](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. **Araştırma yolu** için, bir dosyaya geçerli bir yol girin.

    Araştırma yolu, DSA 'ya özgü bir özelliktir ve oluşturma için geçerli bir yol gereklidir. DSA, CDN için ağ yönlendirme yapılandırmasını iyileştirmek üzere kaynak sunucuya yerleştirilmiş küçük bir *araştırma yolu* dosyası kullanır. Araştırma yolu dosyası için, örnek dosyayı karşıdan yükleyebilir ve sitenizde karşıya yükleyebilir veya kaynak üzerinde yaklaşık 10 KB boyutunda olan mevcut bir varlığı kullanabilirsiniz.

4. Diğer gerekli uç nokta seçeneklerini girin (daha fazla bilgi için bkz. [Yeni BIR CDN uç noktası oluşturma](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) ve ardından **Ekle**' yi seçin.

   CDN uç noktası oluşturulduktan sonra, belirli ölçütlerle eşleşen tüm dosyalar için DSA iyileştirmeleri uygular. 


**DSA için var olan bir uç noktayı yapılandırmak için (yalnızca Akamai profillerinden Azure CDN):**

1. **CDN profili** sayfasında, değiştirmek istediğiniz uç noktayı seçin.

2. Sol bölmeden **iyileştirme**' yi seçin. 

   **İyileştirme** sayfası görüntülenir.

3. **Için iyileştirilmiş** altında, **dinamik site hızlandırma**' yı seçin ve ardından **Kaydet**' i seçin.

> [!Note]
> DSA ek ücretler doğurur. Daha fazla bilgi için bkz. [Content Delivery Network fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Azure CDN kullanarak DSA Iyileştirmesi

Azure CDN dinamik site hızlandırma, aşağıdaki teknikleri kullanarak dinamik varlıkların teslimini hızlandırır:

-   [Rota iyileştirmesi](#route-optimization)
-   [TCP iyileştirmeleri](#tcp-optimizations)
-   [Nesne önceden getirme (yalnızca Akamai 'tan Azure CDN)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Uyarlamalı görüntü sıkıştırma (yalnızca Akamai 'tan Azure CDN)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Rota Iyileştirmesi

Internet 'Te dinamik bir yerde olduğu için yol iyileştirmesi önemlidir çünkü trafik ve geçici olarak kesintiler ağ topolojisini sürekli olarak değiştirir. Sınır Ağ Geçidi Protokolü (BGP), Internet 'in yönlendirme protokolüdür, ancak ara sunucu noktası (PoP) sunucuları aracılığıyla daha hızlı yollar olabilir. 

Yol iyileştirme, bir sitenin sürekli olarak erişilebilir olması ve dinamik içeriğin en hızlı ve en güvenilir yol aracılığıyla son kullanıcılara teslim edilmesi için en iyi kaynak yolunu seçer. 

Akamai ağı, gerçek zamanlı verileri toplamak ve Akamai sunucusundaki farklı düğümler aracılığıyla çeşitli yolları karşılaştırmak ve kaynak ile CDN kenarı arasındaki en hızlı yolu belirleyebilmek için açık Internet genelinde varsayılan BGP yolunu kullanmak için teknikler kullanır. Bu teknikler, Internet tıkanıklık noktalarından ve uzun yolların oluşmasını önler. 

Benzer şekilde, Verizon ağı, istemciden kaynağa en iyi şekilde veri yönlendirmekte olan en iyi ağ geçitlerini tespit etmek için DNS, yüksek kapasiteli destek pop 'Ları ve sistem durumu denetimlerinin bir birleşimini kullanır.
 
Sonuç olarak, tamamen dinamik ve işlem içeriği, son kullanıcılara önbelleklenebilir olsa bile daha hızlı ve güvenilir bir şekilde dağıtılır. 

### <a name="tcp-optimizations"></a>TCP Iyileştirmeleri

İletim Denetimi Protokolü (TCP), bir IP ağındaki uygulamalar arasında bilgi sağlamak için kullanılan Internet Protokolü paketinin standardıdır.  Varsayılan olarak, bir TCP bağlantısı kurmak için birkaç geri ve geriye yönelik istek gerekir ve ağ bağlantılarını önlemek için sınırlar, verimsizlikleri ile sonuçlanır. **Akamai öğesinden Azure CDN** , bu sorunu üç alanda iyileştirerek işler: 

 - [TCP yavaş başlangıcını ortadan kaldırma](#eliminating-tcp-slow-start)
 - [Kalıcı bağlantıları kullanma](#leveraging-persistent-connections)
 - [TCP paket parametrelerini ayarlama](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>TCP yavaş başlangıcını ortadan kaldırma

TCP *yavaş başlangıç* , ağ üzerinden gönderilen veri miktarını sınırlayarak ağ tıkanıklığını önleyen TCP protokolünün bir algoritmasıdır. En fazla ulaşılıncaya veya paket kaybı saptanana kadar gönderici ve alıcı arasındaki küçük tıkanıklık pencere boyutları ile başlatılır.

 Akamai **Azure CDN ve Verizon profillerden** **Azure CDN** her ikisi de, aşağıdaki üç adımla TCP yavaş başlangıcını ortadan kaldırır:

1. Durum ve bant genişliği izleme, uç PoP sunucuları arasındaki bağlantı bant genişliğini ölçmek için kullanılır.
    
2. Ölçümler, uç PoP sunucuları arasında paylaşılır, böylece her bir sunucu, çevresindeki diğer pop 'ların ağ koşullarını ve sunucu sistem durumunu algılar.  
    
3. CDN Edge sunucuları, yakındaki diğer CDN Edge sunucularıyla iletişim kurarken en iyi pencere boyutu gibi bazı iletim parametreleri hakkında varsayımlar yapar. Bu adım, CDN Edge sunucuları arasındaki bağlantının sistem durumunun daha yüksek paket veri aktarımları kapasitesine sahip olması durumunda ilk sıkışıklık penceresi boyutunun arttığı anlamına gelir.  

#### <a name="leveraging-persistent-connections"></a>Kalıcı bağlantıları kullanma

CDN kullanarak, daha az benzersiz makine, doğrudan başlangıç kaynağınıza bağlanan kullanıcılarla karşılaştırıldığında, kaynak sunucunuza doğrudan bağlanır. Azure CDN Ayrıca, Origin ile daha az bağlantı kurmak için Kullanıcı isteklerini birbirine da havuzlar.

Daha önce belirtildiği gibi, TCP bağlantısı kurmak için birkaç el sıkışma isteği gerekir. HTTP üst bilgisi tarafından uygulanan kalıcı bağlantılar `Keep-Alive` , gidiş dönüş sürelerini kaydetmek ve teslimi hızlandırmak için birden çok http isteği için mevcut TCP bağlantılarını yeniden kullanır. 

Ayrıca, **Verizon ' dan Azure CDN** , açık bir bağlantının kapatılmasını önlemek için TCP bağlantısı üzerinden düzenli canlı tutma paketleri gönderir.

#### <a name="tuning-tcp-packet-parameters"></a>TCP paket parametrelerini ayarlama

**Akamai** , sunucudan sunucuya bağlantıları yöneten parametreleri ve aşağıdaki teknikleri kullanarak siteye katıştırılmış içerik almak için gereken uzun mesafe gidiş dönüş miktarını azaltır: Azure CDN

- Bildirim beklemeden daha fazla paket gönderilebilmeleri için ilk sıkışıklık penceresini artırma.
- Bir kaybı tespit etmek ve yeniden iletim daha hızlı gerçekleşmesini sağlamak için ilk yeniden aktarım zaman aşımını azaltma.
- Paketlerin iletimde kaybedilmeden önce bekleme süresini azaltmak için en düşük ve en fazla yeniden iletim zaman aşımını azaltma.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Nesne önceden getirme (yalnızca Akamai 'tan Azure CDN)

Çoğu Web sitesi, görüntüler ve betikler gibi çeşitli diğer kaynaklara başvuran bir HTML sayfasından oluşur. Genellikle, bir istemci bir Web sayfası istediğinde, tarayıcı ilk olarak HTML nesnesini indirir ve ayrıştırır ve ardından sayfanın tam olarak yüklenmesi için gereken bağlantılı varlıklara ek istekler yapar. 

*Önceden getirme* , HTML TARAYıCıYA sunulduğunda HTML sayfasına eklenen görüntüleri ve betikleri alma tekniğidir ve tarayıcı bu nesne isteklerini bile yapar. 

CDN, istemci tarayıcısına HTML temel sayfasını sunan zamanda önceden getirme seçeneği açık durumdayken, CDN, HTML dosyasını ayrıştırır ve bağlı kaynaklar için ek istekler yapar ve onu önbelleğinde depolar. İstemci bağlantılı varlıklar için istekleri yaptığında, CDN Edge sunucusu zaten istenen nesnelere sahiptir ve kaynağa gidiş dönüş olmadan hemen sunabilir. Bu iyileştirme hem önbelleklenebilir hem de Önbelleklenmemiş içerik avantajlarına sahiptir.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Uyarlamalı görüntü sıkıştırma (yalnızca Akamai 'tan Azure CDN)

Özellikle de mobil cihazlarda bazı cihazlar, zaman zaman daha yavaş ağ hızları yaşar. Bu senaryolarda, kullanıcının Web sayfasında daha küçük resimleri daha hızlı bir şekilde, tam çözünürlüklü görüntüler için uzun süre beklemek yerine daha hızlı alması daha faydalıdır.

Bu özellik ağ kalitesini otomatik olarak izler ve dağıtım süresini geliştirmek için Ağ hızları yavaşsa standart JPEG sıkıştırma yöntemlerini kullanır.

Uyarlamalı görüntü sıkıştırma | Dosya uzantıları  
--- | ---  
JPEG sıkıştırması | . jpg,. jpeg,. jpe,. JIG,. JGB,. jgi

## <a name="caching"></a>Önbelleğe Alma

DSA ile, yanıtta kaynak dahil `Cache-Control` veya üst bilgiler de dahil olmak üzere CDN 'de varsayılan olarak önbelleğe alma kapalıdır `Expires` . DSA tipik olarak, her bir istemciye benzersiz olduklarından önbelleğe alınmamalıdır dinamik varlıklar için kullanılır. Önbelleğe alma, bu davranışı bozabilir.

Statik ve dinamik varlıkların karışımına sahip bir Web siteniz varsa, en iyi performansı elde etmek için bir karma yaklaşım yapmanız en iyisidir. 

Verizon **Azure CDN ve Akamai profillerden standart** **Azure CDN Standart** için, [önbelleğe alma kurallarını](cdn-caching-rules.md)kullanarak belirli dsa uç noktaları için önbelleğe almayı etkinleştirebilirsiniz.

Önbelleğe alma kurallarına erişmek için:

1. **CDN profili** sayfasında, ayarlar altında, **önbelleğe alma kuralları**' nı seçin.  
    
    ![CDN Önbelleğe alma kuralları düğmesi](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    **Önbelleğe alma kuralları** sayfası açılır.

2. DSA uç noktanız için önbelleğe almayı açmak üzere genel veya özel bir önbelleğe alma kuralı oluşturun. 

Yalnızca **Verizon profillerinin Azure CDN Premium** için, [kurallar ALTYAPıSıNı](./cdn-verizon-premium-rules-engine.md)kullanarak belirli dsa uç noktaları için önbelleğe almayı açın. Oluşturulan tüm kurallar, yalnızca DSA için iyileştirilmiş olan profilinizin uç noktalarını etkiler. 

Kural altyapısına erişmek için:
    
1. **CDN profili** sayfasında **Yönet**' i seçin.  
    
    ![CDN profili Yönet düğmesi](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    CDN yönetim portalı açılır.

2. CDN yönetim portalından **ADN**' yi ve ardından **Rules Engine**' i seçin. 

    ![DSA için kural altyapısı](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternatif olarak, iki CDN uç noktası kullanabilirsiniz: DSA ile en iyi duruma getirilmiş bir uç nokta, dinamik varlıklar ve genel web teslimi gibi bir statik iyileştirme türü ile iyileştirilmiş başka bir uç nokta, önbelleğe alınabilir varlıkların teslim edilmesi için kullanılır. Web sayfası URL 'lerinizi, kullanmayı planladığınız CDN uç noktasındaki varlığa doğrudan bağlanacak şekilde değiştirin. 

Örneğin: `mydynamic.azureedge.net/index.html` dinamik bir sayfa ve dsa uç noktasından yüklenir.  HTML sayfası, JavaScript kitaplıkları veya ve gibi statik CDN uç noktasından yüklenen görüntüler gibi birden çok statik kıymete başvurur `mystatic.azureedge.net/banner.jpg` `mystatic.azureedge.net/scripts.js` .