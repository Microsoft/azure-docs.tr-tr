---
title: Azure CDN ile büyük dosya indirme iyileştirme
description: Bu makalede, ne kadar büyük dosya indirme iyileştirilebilir açıklanmaktadır.
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
ms.openlocfilehash: 4fe72985a799595908a0ff6bceb1a73dca823c8f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593777"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Azure CDN ile büyük dosya indirme iyileştirme

Dosya boyutu içeriğin internet üzerinden sunulan gelişmiş işlevselliği, Gelişmiş grafikleri ve zengin medya içeriği nedeniyle büyütmeye devam edin. Bu büyüme faktörlerden tarafından yönetilir: geniş bant sızma, daha ucuz depolama cihazları, yüksek çözünürlüklü video ve İnternet'e bağlı cihazların (IOT) yaygın artırın. Büyük dosyalar için bir hızlı ve verimli bir teslim mekanizması kesintisiz ve eğlenceli bir tüketici deneyiminin sağlanması önemlidir.

Büyük dosyaların teslimini çeşitli zorluklar vardır. İlk olarak, uygulamaların tüm verileri sıralı olarak indirmesini değil çünkü büyük dosya indirme için geçen ortalama süre önemli olabilir. Bazı durumlarda, ilk bölümü olmadan önce dosyaya son kısmını uygulamalar indirmesini. Yalnızca az miktarda bir dosya istenen veya bir kullanıcı bir indirme duraklatır, yükleme başarısız olabilir. Content delivery Network'te (CDN), dosyanın tamamı kaynak sunucudan aldıktan sonra indirme ayrıca kadar gecikebilir. 

İkinci olarak, bir kullanıcının machine ve dosya arasındaki gecikme süresi, bunların içeriğini görüntüleyebilir hızını belirler. Ayrıca, Ağ Tıkanıklığı ve kapasite sorunları, aktarım hızı da etkiler. Sunucuları ve kullanıcılar arasındaki büyük mesafeler kalite azaltan gerçekleşmesi, paket kaybı için ek fırsatlar oluşturun. Sınırlı performans tarafından kalite azalmasına neden ve artan paket kaybı tamamlamak bir dosya indirme için bekleme süresini artırabilir. 

Üçüncü olarak, çok büyük dosyaları içinde tamamen teslim edilemedi. Kullanıcılar bir indirme aracılığıyla olan sürenin yarısına ulaşıldığında iptal veya yalnızca ilk birkaç dakika uzunluğunda bir MP4 video izleyin. Bu nedenle, yazılım ve medya teslim şirketlerine yalnızca istenen dosyasının bölümünü sunmak istiyorsunuz. İstenen bölümlerinden etkili bir dağıtım çıkış trafiği kaynak sunucudan azaltır. Etkin dağıtım, bellek ve g/ç baskı kaynak sunucusunda de azaltır. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Azure CDN ile büyük dosyaların teslimini Microsoft gelen en iyi duruma getirme

**Azure CDN standart Microsoft gelen** uç noktaları dosya boyutu büyük dosyaları olmadan bir uç sunun. Ek özellikler büyük dosyaların teslimini hızlandırmak için varsayılan olarak açık olabilir.

### <a name="object-chunking"></a>Öbekleme nesnesi 

**Azure CDN standart Microsoft gelen** nesne Öbekleme adında bir teknik kullanır. Büyük dosya istendiğinde, CDN dosyanın daha küçük parçalara kaynaktan alır. CDN POP sunucunun tam veya bayt aralığı dosya isteği aldıktan sonra CDN uç sunucusu dosyayı kaynaktan 8 MB'lık parçalar halinde ister. 

CDN uç öbek ulaştıktan sonra bu önbelleğe alınmış ve hemen kullanıcıya sunulan. CDN, ardından sonraki öbek paralel prefetches. Bu önceden getirme içeriği gecikmesini azaltır kullanıcı önceden bir öbek kalmasını sağlar. Bu işlem tüm kadar devam eder (istenirse) dosyasını indirdiğiniz, tüm bayt aralıkları (istenirse) kullanılabilir, veya istemci bağlantıyı sonlandırır. 

Bayt aralığı isteği hakkında daha fazla bilgi için bkz. [RFC 7233](https://tools.ietf.org/html/rfc7233).

Bunlar aldığı gibi CDN herhangi öbekleri önbelleğe alır. Dosyanın tamamı, CDN önbelleğinin üzerinde önbelleğe alınması gerekmez. Dosya veya bayt aralıkları için sonraki istekler CDN önbelleğinden sunulur. Aksi takdirde öbekleri CDN'de önbelleğe alınır, önceden getirme öbekleri kaynaktan istemek için kullanılır. Bu iyileştirme, bayt aralığı isteklerini destekleme özelliği, kaynak sunucu üzerinde kullanır; Kaynak sunucu bayt aralığı isteklerini desteklemiyorsa, bu en iyi duruma getirme etkili değildir. 

### <a name="conditions-for-large-file-optimization"></a>Büyük dosya iyileştirmesi için koşullar
Büyük dosya iyileştirmesi özelliklerini **Azure CDN standart Microsoft gelen** genel web teslimi iyileştirme türünü kullandığınızda varsayılan olarak açık olabilir. En büyük dosya boyutu sınırı yoktur.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Verizon'dan Azure CDN ile büyük dosyaların teslimini en iyi duruma getirme

**Azure CDN standart Verizon** ve **verizon'dan Azure CDN Premium** uç noktaları dosya boyutu büyük dosyaları olmadan bir uç sunun. Ek özellikler büyük dosyaların teslimini hızlandırmak için varsayılan olarak açık olabilir.

### <a name="complete-cache-fill"></a>Tam önbelleğini doldurma

Varsayılan tam Önbelleği doldurma özelliği CDN bir ilk isteği terk kaybolur veya, bir dosya önbelleğine çekmesine olanak sağlar. 

Tam önbellek dolgu büyük varlıklar için kullanışlıdır. Genellikle, kullanıcıların onları baştan yüklemeyin. Aşamalı indirme kullanırlar. Varsayılan davranış, varlık kaynak sunucusundan bir arka planda getirme başlatmak için uç sunucusunu zorlar. Ardından, uç sunucusunun yerel önbelleğine varlıktır. Tam nesne önbellekte sonra uç sunucusunu CDN önbelleğe alınmış nesne için bayt aralığı isteklerini karşılar.

Varsayılan davranış kural altyapısı aracılığıyla devre dışı bırakılabilir **verizon'dan Azure CDN Premium**.

### <a name="peer-cache-fill-hot-filing"></a>Eş Önbelleği doldurma hot dosyalama

Varsayılan eş önbellek dolgu hot dosyalama özelliği, karmaşık bir özel algoritması kullanır. Ek edge sunucuları bant genişliğine göre önbelleğe alma kullanan ve büyük, yüksek oranda popüler nesneleri için istemci isteklerini karşılamak için ölçümleri toplama ister. Bu özellik, bir kullanıcının kaynak sunucuya ek istekler çok sayıda gönderilen bir durum önler. 

### <a name="conditions-for-large-file-optimization"></a>Büyük dosya iyileştirmesi için koşullar

Büyük dosya iyileştirmesi özelliklerini **verizon'dan Azure CDN standart** ve **verizon'dan Azure CDN Premium** genel web teslimi iyileştirme türünü kullandığınızda varsayılan olarak açık olabilir. En büyük dosya boyutu sınırı yoktur. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Akamai'den Azure CDN standart ile büyük dosyaların teslimini en iyi duruma getirme

**Azure CDN standart Akamai** profil uç noktaları büyük dosyaları dünya ölçeğinde kullanıcılar için verimli bir şekilde sunan bir özellik sunar. Kaynak sunucularındaki yükü azaltır çünkü özellik gecikmeleri azaltır.

Büyük dosya iyileştirmesi tür özelliği, ağ iyileştirmeleri ve büyük dosyaları daha hızlı ve daha duyarlı bir şekilde sunmak için yapılandırmaları açar. Genel web teslimatı ile **akamai'den Azure CDN standart** uç noktaları yalnızca 1,8 GB altındaki dosyalar önbelleğe alır ve tünel (cache değil) dosyaları 150 GB'ye kadar. Büyük dosya iyileştirmesi önbellekler 150 GB'ye kadar dosyalar.

Belirli koşullar karşılandığında büyük dosya iyileştirmesi etkili olur. Koşullar içeren kaynak sunucunun nasıl çalıştığını ve boyutları ve istenen dosya türleri. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Büyük dosyaların teslimini iyileştirecektir Akamai CDN uç nokta yapılandırma

Yapılandırabileceğiniz, **akamai'den Azure CDN standart** teslim Azure portal aracılığıyla büyük dosyalar için en iyi duruma getirmek için uç nokta. Bunu yapmak için REST API veya istemci SDK'larından birini kullanabilirsiniz. İşlem için Azure Portalı aracılığıyla aşağıdaki adımlarda bir **akamai'den Azure CDN standart** profili:

1. Yeni bir uç nokta üzerinde bir Akamai eklemek için **CDN profili** sayfasında **uç nokta**.

    ![Yeni uç nokta](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. İçinde **için en iyi duruma getirilmiş** aşağı açılan listesinden **büyük dosya indirme**.

    ![Seçili büyük dosya iyileştirmesi](./media/cdn-large-file-optimization/cdn-large-file-select.png)


CDN uç noktasını oluşturduktan sonra büyük dosya iyileştirmeler belirli ölçütlere uyan tüm dosyaları için geçerlidir. Aşağıdaki bölümde, bu işlemi açıklanmaktadır.

### <a name="object-chunking"></a>Öbekleme nesnesi 

Büyük dosya iyileştirmesi ile **akamai'den Azure CDN standart** nesne Öbekleme adında bir teknik kullanır. Büyük dosya istendiğinde, CDN dosyanın daha küçük parçalara kaynaktan alır. CDN POP sunucunun tam veya bayt aralığı dosya isteği aldıktan sonra bu en iyi duruma getirme için desteklenen dosya türü olup olmadığını denetler. Dosya türünü dosya boyutu gereksinimleri karşılayıp karşılamadığını denetler. CDN uç sunucusu dosyayı dosya boyutu 10 MB'den daha büyük ise, 2 MB'lık parçalar halinde kaynaktan ister. 

CDN uç öbek ulaştıktan sonra bu önbelleğe alınmış ve hemen kullanıcıya sunulan. CDN, ardından sonraki öbek paralel prefetches. Bu önceden getirme içeriği gecikmesini azaltır kullanıcı önceden bir öbek kalmasını sağlar. Bu işlem tüm kadar devam eder (istenirse) dosyasını indirdiğiniz, tüm bayt aralıkları (istenirse) kullanılabilir, veya istemci bağlantıyı sonlandırır. 

Bayt aralığı isteği hakkında daha fazla bilgi için bkz. [RFC 7233](https://tools.ietf.org/html/rfc7233).

Bunlar aldığı gibi CDN herhangi öbekleri önbelleğe alır. Dosyanın tamamı, CDN önbelleğinin üzerinde önbelleğe alınması gerekmez. Dosya veya bayt aralıkları için sonraki istekler CDN önbelleğinden sunulur. Aksi takdirde öbekleri CDN'de önbelleğe alınır, önceden getirme öbekleri kaynaktan istemek için kullanılır. Bu iyileştirme, bayt aralığı isteklerini destekleme özelliği, kaynak sunucu üzerinde kullanır; Kaynak sunucu bayt aralığı isteklerini desteklemiyorsa, bu en iyi duruma getirme etkili değildir.

### <a name="caching"></a>Önbelleğe alma
Büyük dosya iyileştirmesi, genel web teslimatı farklı varsayılan önbellek sona erme sürelerinden kullanır. Önbelleğe alma pozitif ve negatif HTTP yanıt kodlarına göre önbelleğe arasında ayırır. Kaynak sunucu bir cache-control aracılığıyla sona erme süresini belirtir veya sona erme yanıt üstbilgisi, CDN, değer geçerlidir. Kaynak belirttiğinizde değil ve bu dosyayı bu en iyi duruma getirme türü için türü ve boyutu koşullara uyan CDN büyük dosya iyileştirmesi için varsayılan değerleri kullanır. Aksi takdirde, CDN, genel web teslimatı için varsayılan değerleri kullanır.


|    | Genel web | Büyük dosya iyileştirmesi 
--- | --- | --- 
Önbelleğe alma: Pozitif <br> HTTP 200 203, 300, <br> 301, 302 ve 410 | 7 gün |1 gün  
Önbelleğe alma: Negatif <br> HTTP 204 305, 404, <br> ve 405 | Yok. | 1 saniye 

### <a name="deal-with-origin-failure"></a>Kaynak hatası işlem

Kaynağı oku-zaman aşımı uzunluğu, genel web teslimatı büyük dosya iyileştirmesi türü için iki dakika için iki saniyelerden artar. Bu artış, erken zaman aşımı bağlantısı önlemek için daha büyük dosya boyutlarına hesaplar.

Bağlantı zaman aşımına uğradığında CDN bir sayı, bir "504 - ağ geçidi zaman aşımı" hatası istemciye gönderilmeden önce kaç kez yeniden dener. 

### <a name="conditions-for-large-file-optimization"></a>Büyük dosya iyileştirmesi için koşullar

Aşağıdaki tabloda, büyük dosya iyileştirmesi için karşılanması gereken ölçütleri kümesini listeler:

Koşul | Değerler 
--- | --- 
Desteklenen dosya türleri | 3g, 2, 3gp, asf, AVI, bz2, dmg, exe, f4v, flv <br> GZ, hdp, ISO, jxr, m4v, mkv, mov, mp4 <br> MPEG mpg, mts, paket, qt, rm, swf, hedefi, <br> tgz, wdp, webm, webp, wma, wmv, zip  
En küçük dosya boyutu | 10 MB 
En büyük dosya boyutu | 150 GB 
Kaynak sunucu özellikleri | Bayt aralığı isteklerini desteklemesi gerekir 

## <a name="additional-considerations"></a>Diğer konular

Bu iyileştirme türü için aşağıdaki ek konuları göz önünde bulundurun:

- Kümeleme işlemi, kaynak sunucuya ek istekler oluşturur. Ancak, genel kaynaktan sunulan veri hacmi çok daha küçüktür. Kümeleme sonuçlarında, CDN önbelleğe alma özelliklerini daha iyi.

- Dosyanın daha küçük parçalara teslim edildiğinden bellek ve g/ç baskısı kaynak azaltılır.

- CDN önbelleğe alınmış öbekleri için var. kaynağı ek istek yok içeriğin süresi dolar veya önbellekten çıkarıldığı kadar

- Kullanıcılar aralığı isteklerini CDN için normal bir dosya gibi davranılır yapabilir. En iyi duruma getirme, yalnızca geçerli bir dosya türü olan ve bayt aralığı 10 MB ile 150 GB arasında ise geçerlidir. İstenen ortalama dosya boyutu 10 MB'den küçükse, genel web teslimatı kullanın.

