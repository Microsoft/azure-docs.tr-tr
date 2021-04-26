---
title: Azure CDN ile büyük dosya indirme iyileştirmesi
description: Büyük dosya indirmelerinin Azure Content Delivery Network 'da iyileştireolabileceğini öğrenin. Bu makale çeşitli senaryolar içerir.
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
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: ed5768e89482d32bb140e9ba7064de2d20809892
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020730"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Azure CDN ile büyük dosya indirme iyileştirmesi

İnternet üzerinden sunulan içeriğin dosya boyutları, gelişmiş işlevler, geliştirilmiş grafikler ve zengin medya içeriği nedeniyle büyümeye devam eder. Bu büyüme birçok faktöre sahiptir: geniş bant sızması, daha fazla pahalı depolama cihazı, yüksek tanımlı videonun yaygın artması ve internet 'e bağlı cihazlar (IoT). Büyük dosyalar için hızlı ve verimli bir teslim mekanizması, sorunsuz ve keyifli bir tüketici deneyimi sağlamak açısından önemlidir.

Büyük dosyaların tesliminde bazı sorunlar vardır. İlk olarak, büyük bir dosyanın indirileceği ortalama süre önemli olabilir, çünkü uygulamalar tüm verileri ardışık olarak indiremeyebilir. Bazı durumlarda, uygulamalar ilk bölümden önce bir dosyanın son bölümünü indirebilir. Bir dosyanın yalnızca küçük bir miktarı istendiğinde veya Kullanıcı indirmeyi duraklattığında, indirme başarısız olabilir. İçerik teslim ağı (CDN) tüm dosyayı kaynak sunucudan alana kadar de indirme gecikiyor olabilir. 

İkinci olarak, bir kullanıcının makinesi ile dosyası arasındaki gecikme süresi içeriği görüntüleyebilecekleri hızı belirler. Ayrıca, ağ tıkanıklığı ve kapasite sorunları da aktarım hızını etkiler. Sunucular ve kullanıcılar arasındaki daha fazla uzaklık, paket kaybını sağlamak için ek fırsatlar oluşturur ve bu da kaliteyi azaltır. Kalite açısından sınırlı verimlilik ve daha fazla paket kaybı nedeniyle oluşan azalma, dosya indirmesinin tamamlanabilmesi için bekleme süresini artırabilir. 

Üçüncü, çok büyük dosyalar tamamen teslim edilmez. Kullanıcılar bir indirmeyi tek bir kez iptal edebilir veya uzun bir MP4 videosunu yalnızca ilk birkaç dakika izleyebilirsiniz. Bu nedenle, yazılım ve medya teslim şirketleri yalnızca istenen dosyanın bir bölümünü teslim etmek ister. İstenen bölümlerin verimli dağıtımı, çıkış trafiğini kaynak sunucudan azaltır. Verimli dağıtım Ayrıca, kaynak sunucu üzerindeki belleği ve g/ç basıncını azaltır. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Microsoft 'tan Azure CDN büyük dosyaları teslim etmek için iyileştirin

**Microsoft uç noktalarından Azure CDN Standart** , dosya boyutu üst sınırı olmadan büyük dosyalar sunar. Büyük dosyaları daha hızlı teslim etmek için ek özellikler varsayılan olarak açıktır.

### <a name="object-chunking"></a>Nesne parçalama 

**Microsoft 'tan Azure CDN Standard** , nesne parçalama adında bir teknik kullanır. Büyük bir dosya istendiğinde, CDN dosyanın kaynaktan daha küçük parçalarını alır. CDN POP sunucusu bir tam veya bayt aralığı dosya isteği aldıktan sonra, CDN uç sunucusu, dosyayı sıfırdan 8 MB öbekteki kaynaktan ister. 

Öbek CDN Edge 'e ulaştıktan sonra önbelleğe alınır ve anında kullanıcıya sunulur. CDN daha sonra sonraki öbeği paralel olarak önceden getirir. Bu önceden getirme, içeriğin kullanıcının önüne bir öbek kalmasını sağlar ve gecikme süresini azaltır. Bu işlem, dosyanın tamamı indirilene kadar devam eder (isteniyorsa), tüm bayt aralıkları kullanılabilir (isteniyorsa) veya istemci bağlantıyı sonlandırır. 

Bayt aralığı isteği hakkında daha fazla bilgi için bkz. [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN, alındıkları tüm öbekleri önbelleğe alır. Tüm dosyanın CDN önbelleğinde önbelleğe alınması gerekmez. Dosya veya bayt aralıklarının sonraki istekleri CDN önbelleğinden sunulur. Tüm parçalar CDN 'de önbelleğe alınmamışsa, kaynaktan öbekleri istemek için önceden getirme kullanılır. Bu iyileştirme, kaynak sunucunun, bayt aralığı isteklerini destekleme özelliğine dayanır; Kaynak sunucu, bayt aralığı isteklerini desteklemiyorsa, daha fazla veri indirme isteği başarısız olur. 

### <a name="conditions-for-large-file-optimization"></a>Büyük dosya iyileştirmesi için koşullar
En büyük dosya boyutu için sınır yoktur.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Verizon adresinden büyük dosyaların Azure CDN teslimi için iyileştirin

Verizon uç noktalarında Verizon ve **Azure CDN Premium** **'dan Azure CDN Standart** , büyük dosyaları dosya boyutunda bir Cap olmadan sunar. Büyük dosyaları daha hızlı teslim etmek için ek özellikler varsayılan olarak açıktır.

### <a name="complete-cache-fill"></a>Önbellek dolgusunu tamamla

Varsayılan önbellek dolgusu özelliği, CDN 'nin bir ilk istek terk edildiğinde veya kaybedildiği zaman önbelleğe bir dosya çekmesini sağlar. 

Tüm önbellek dolgusu, büyük varlıklar için en yararlı seçenektir. Genellikle, kullanıcılar bunları baştan sona indirmez. Aşamalı indirme kullanır. Varsayılan davranış, uç sunucusunu kaynak sunucudan varlığın bir arka plan getirme işlemini başlatacak şekilde zorlar. Daha sonra varlık, uç sunucunun yerel önbelleğinde bulunur. Tam nesne önbellekte olduktan sonra, uç sunucu, önbelleğe alınmış nesne için, bayt aralığı isteklerini CDN 'ye karşılar.

Varsayılan davranış, **Verizon ' den Azure CDN Premium**'daki Rules altyapısı aracılığıyla devre dışı bırakılabilir.

### <a name="peer-cache-fill-hot-filing"></a>Eş önbellek doldurma etkin-dosyalama

Varsayılan eş önbellek dolgusu sık erişimli özelliği, gelişmiş bir özel algoritma kullanır. Büyük, yüksek oranda popüler nesnelere yönelik istemci isteklerini karşılamak için bant genişliği ve toplam istek ölçümleri temelinde ek Edge önbelleğe alma sunucuları kullanır. Bu özellik, çok sayıda ek isteğin bir kullanıcının kaynak sunucusuna gönderildiği durumu önler. 

### <a name="conditions-for-large-file-optimization"></a>Büyük dosya iyileştirmesi için koşullar

Verizon ve Verizon ' den **Premium Premium Azure CDN** ' dan **Azure CDN Standart** için büyük dosya iyileştirme özellikleri, genel Web teslim iyileştirme türünü kullandığınızda varsayılan olarak açıktır. En büyük dosya boyutu için sınır yoktur. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Akamai 'ten Azure CDN Standard ile büyük dosyaların teslim edilmesi için iyileştirin

Akamai profil uç noktalarından **Azure CDN Standard** , büyük dosyaları, dünyanın her yerindeki kullanıcılara verimli bir şekilde sunan bir özellik sunar. Bu özellik, kaynak sunuculardaki yükü azalttığından gecikme sürelerini azaltır.

Büyük dosya iyileştirme türü özelliği, büyük dosyaları daha hızlı ve daha boyutlandırılabilir iletmek için ağ iyileştirmelerini ve konfigürasyonları etkinleştirir. Akamai bitiş noktalarından **Azure CDN Standart** olan genel web teslimi, dosyaları yalnızca 1,8 GB 'ın altında önbelleğe alır ve dosyaları 150 GB 'a kadar (önbelleğe değil) alabilir. Büyük dosya iyileştirmesi dosyaları 150 GB 'a kadar önbelleğe alır.

Büyük dosya iyileştirmesi, belirli koşullar karşılanmadığınızda etkilidir. Koşullar, kaynak sunucunun nasıl çalıştığını ve istenen dosyaların boyutlarını ve türlerini içerir. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Büyük dosyaların teslimini iyileştirmek için bir Akamai CDN uç noktası yapılandırın

Azure portal aracılığıyla büyük dosyaların teslimini iyileştirmek için **Azure CDN standardını, Akamai** uç noktasından yapılandırabilirsiniz. Bunu yapmak için REST API 'Lerini veya istemci SDK 'Larını de kullanabilirsiniz. Aşağıdaki adımlarda, Akamai profilinden bir **Azure CDN Standart** için Azure Portal işlem gösterilmektedir:

1. Yeni bir uç nokta eklemek için, bir Akamai **CDN profili** sayfasında **uç nokta**' ı seçin.

    ![Yeni uç nokta](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. **En iyileştirilmiş for** açılan listesinde, **büyük dosya yükleme**' yi seçin.

    ![Büyük dosya iyileştirmesi seçildi](./media/cdn-large-file-optimization/cdn-large-file-select.png)


CDN uç noktasını oluşturduktan sonra, belirli ölçütlerle eşleşen tüm dosyalar için büyük dosya iyileştirmeleri uygular. Aşağıdaki bölümde bu işlem açıklanmaktadır.

### <a name="object-chunking"></a>Nesne parçalama 

**Akamai tarafından Azure CDN Standart** olan büyük dosya iyileştirmesi, nesne parçalama adlı bir teknik kullanır. Büyük bir dosya istendiğinde, CDN dosyanın kaynaktan daha küçük parçalarını alır. CDN POP sunucusu bir tam veya bayt aralığı dosya isteği aldıktan sonra, dosya türünün bu iyileştirme için desteklenip desteklenmediğini denetler. Ayrıca dosya türünün dosya boyutu gereksinimlerini karşılayıp karşılamadığını denetler. Dosya boyutu 10 MB 'den büyükse, CDN uç sunucusu dosyayı 2 MB öbekteki kaynaktan ister. 

Öbek CDN Edge 'e ulaştıktan sonra önbelleğe alınır ve anında kullanıcıya sunulur. CDN daha sonra sonraki öbeği paralel olarak önceden getirir. Bu önceden getirme, içeriğin kullanıcının önüne bir öbek kalmasını sağlar ve gecikme süresini azaltır. Bu işlem, dosyanın tamamı indirilene kadar devam eder (isteniyorsa), tüm bayt aralıkları kullanılabilir (isteniyorsa) veya istemci bağlantıyı sonlandırır. 

Bayt aralığı isteği hakkında daha fazla bilgi için bkz. [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN, alındıkları tüm öbekleri önbelleğe alır. Tüm dosyanın CDN önbelleğinde önbelleğe alınması gerekmez. Dosya veya bayt aralıklarının sonraki istekleri CDN önbelleğinden sunulur. Tüm parçalar CDN 'de önbelleğe alınmamışsa, kaynaktan öbekleri istemek için önceden getirme kullanılır. Bu iyileştirme, kaynak sunucunun, bayt aralığı isteklerini destekleme özelliğine dayanır; Kaynak sunucu, bayt aralığı isteklerini desteklemiyorsa, bu iyileştirme etkili olmaz.

### <a name="caching"></a>Önbelleğe Alma
Büyük dosya iyileştirmesi, genel Web tesliminden farklı varsayılan önbelleğe alma zaman aşımı sürelerini kullanır. Olumlu önbellek ve HTTP yanıt kodlarına göre negatif önbellek arasında ayrım yapar. Kaynak sunucu, yanıtta Cache-Control veya Expires üst bilgisi aracılığıyla bir sona erme saati belirtiyorsa, CDN bu değere sahiptir. Kaynak belirtilmezse ve dosya bu iyileştirme türü için tür ve boyut koşullarıyla eşleşiyorsa, CDN büyük dosya iyileştirmesi için varsayılan değerleri kullanır. Aksi takdirde, CDN genel web teslimi için Varsayılanları kullanır.

| Önbelleğe Alma  | Genel Web | Büyük dosya iyileştirmesi 
--- | --- | --- 
Önbelleğe alma: pozitif <br> HTTP 200, 203, 300, <br> 301, 302 ve 410 | 7 gün |1 gün  
Önbelleğe alma: negatif <br> HTTP 204, 305, 404, <br> ve 405 | Yok | 1 saniye 

### <a name="deal-with-origin-failure"></a>Kaynak hatasıyla uğraşın

Kaynak okuma zaman aşımı uzunluğu, genel Web tesliminin iki saniyeden uzun dosya iyileştirme türü için iki dakikaya kadar artar. Bu, zamanından önce bir zaman aşımı bağlantısını önlemek için daha büyük dosya boyutlarına yönelik hesapları arttırır.

Bir bağlantı zaman aşımına uğrarsa, CDN istemciye bir "504-Gateway timeout" hatası göndermeden önce birkaç kez yeniden dener. 

### <a name="conditions-for-large-file-optimization"></a>Büyük dosya iyileştirmesi için koşullar

Aşağıdaki tabloda, büyük dosya iyileştirmesi için karşılanması için ölçüt kümesi listelenmektedir:

Koşul | Değerler 
--- | --- 
Desteklenen dosya türleri | 3g2, 3GP, ASF, AVI, bz2, dmg, exe, F4V, FLV, <br> gz, HDP, ISO, jxr, M4V, MKV, MOV, MP4, <br> MPEG, MPG, MTS, PKG, QT, RM, SWF, tar, <br> tgz, WDP, WebM, WEBP, WMA, WMV, zip  
En küçük dosya boyutu | 10 MB 
En büyük dosya boyutu | 150 GB 
Kaynak sunucu özellikleri | Bayt aralığı isteklerini desteklemesi gerekir 

## <a name="additional-considerations"></a>Diğer konular

Bu iyileştirme türü için aşağıdaki ek konuları göz önünde bulundurun:

- Öbek oluşturma işlemi, kaynak sunucuya ek istekler üretir. Ancak, kaynaktan alınan toplam veri hacmi çok küçüktür. Öbek oluşturma, CDN 'de daha iyi önbelleğe alma özelliklerine neden olur.

- Bellek ve g/ç baskısı, dosyanın küçük parçaları teslim edildiğinden kaynak üzerinde azalır.

- CDN 'de önbelleğe alınan parçalar için, içeriğin süresi dolana veya önbellekten çıkarılana kadar kaynağa ek istek yapılmaz.

- Kullanıcılar, herhangi bir normal dosya gibi davranılan CDN 'ye Aralık istekleri yapabilir. İyileştirme yalnızca geçerli bir dosya türü ise ve bayt aralığı 10 MB ile 150 GB arasında olduğunda geçerlidir. İstenen ortalama dosya boyutu 10 MB 'den küçükse, bunun yerine genel web teslimi kullanın.

