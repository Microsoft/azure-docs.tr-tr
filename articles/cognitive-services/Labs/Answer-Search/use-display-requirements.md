---
title: Kullanım ve görüntüleme gereksinimleri-proje yanıtı araması
titlesuffix: Azure Cognitive Services
description: Proje yanıtı arama uç noktası için kullanım ve görüntüleme gereksinimleri.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 2b42d61fd887f166a08b78510d5eaacb8a7cdcb8
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706715"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Proje yanıtı arama kullanımı ve görüntüleme gereksinimleri

Kullanım ve görüntüleme gereksinimleri, içerik ve ilgili bilgilerin herhangi bir uygulaması için geçerlidir (örneğin, ilişkiler, meta veriler ve diğer sinyaller), Bing bilgi arama, Bing Özel Arama, Varlık Arama, Resim Arama, Haber Arama, Video Arama, Görsel Arama, Web Araması, Yazım Denetimi ve otomatik öneri API 'Leri. Bu gereksinimlerle ilgili uygulama ayrıntıları, belirli özellikler ve sonuçlar için belgelerde bulunabilir.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Bing Yazım Denetimi ve Bing Otomatik Öneri API'si.

Yapma:

- Bing Yazım Denetimi veya Bing Otomatik Öneri API 'Lerinden aldığınız verileri kopyalayın, depolayın veya önbelleğe alın
- ya da üçüncü tarafların sunabileceği yeni veya mevcut hizmetleri eğlendirmek, değerlendirmek ya da geliştirmek için, Bing Yazım Denetimi veya API 'Leri Bing Otomatik Öneri API 'Ler veya benzer bir etkinlik kapsamında aldığınız verileri kullanın.

## <a name="2-definitions"></a>2. Tanımlar

- "yanıt", yanıt olarak döndürülen bir sonuç kategorisini ifade eder. Örneğin, Bing Web Araması API'si bir yanıt, Web sayfası sonuçları, görüntü, video ve haberlerinin kategorilerine yanıt içerebilir;
- "yanıt", bir arama API 'sine yapılan tek bir çağrıya yanıt olarak alınan tüm yanıtlar ve ilgili veriler anlamına gelir;
- "sonuç", bir cevap içindeki bir bilgi öğesine başvurur. Örneğin, tek bir haber makalesiyle bağlantılı veri kümesi, bir haber yanıtı ile sonuçlanır.
- "API 'Leri arama", topluca, Bing Özel Arama, Varlık Arama, Resim Arama, Haber Arama, Video Arama, Görsel Arama ve Web Araması API 'Leri anlamına gelir. 


## <a name="3-search-apis"></a>3. API arama

Bu bölüm 3 ' teki gereksinimler arama API 'Leri için geçerlidir.

**A. Internet arama deneyimi.** Yanıtlarda döndürülen tüm veriler yalnızca İnternet araması deneyimlerinde kullanılabilir. Internet araması deneyimi, uygun olduğu şekilde içeriğin görüntülendiği anlamına gelir: 
- , son kullanıcının doğrudan sorgusuna veya kullanıcının arama ilgi alanı ve amacına ilişkin diğer belirtiye (örneğin, Kullanıcı tarafından belirtilen arama sorgusu) uygun ve yanıt veriyor. 
- kullanıcıların veri kaynaklarını bulmasına ve kaynaklarına gitmesine yardımcı olur (örneğin, girilen URL 'Ler köprü olarak uygulanır, böylece içerik veya atılama, verilerle birlikte görüntülenen tıklatılabilir bir bağlantıdır); ya da Bing Varlık Arama API'si ise, kullanıcının bing.com üzerinde ilgili sorgunun arama sonuçlarına gitmesini sağlayan yanıtta belirtilen bing.com URL 'sine görünür bir şekilde bağlanır;
- Son kullanıcının seçim yapmak için birden çok sonuç içerir (örneğin, haber yanıtının bazı sonuçları görüntülenir veya üçten az sayıda döndürülürse); 
- , arama amacını karşılamak için uygun bir tutarla sınırlıdır (örneğin, görüntü küçük resimleri kullanıcının görüntüsüne göre, küçük resim boyutunda); 
- son kullanıcıya içeriğin Internet araması sonuçları (örneğin, içeriğin "Web 'Den" bir bildirim) olduğunu gösteren görünür bir gösterge içerir; '
- Arama API 'Lerinden alınan verilerin kullanılmasını sağlamak için uygun olan ölçülerin herhangi bir birleşimini içerir (örneğin, bir Creative Commons lisansına güvenmek, ilgili lisansla uyumlu koşullar). Hangi ölçümlerin uygun olabileceğini belirlemek için yasal danışmanlarınızı inceleyin.
Internet arama deneyimi gereksiniminin tek istisnası, aşağıda belirtilen 3E (görüntü olmayan URL keşfi) bölümünde açıklandığı gibi URL keşfi içindir. 

**KENARI. Larından.** Yapma:

- yanıtlardan tüm verileri kopyalayın, depolayın veya önbelleğe alma ("hizmet sürekliliği" bölümünün izin verdiği ölçüde saklama süresi dışında); 
- ya da üçüncü tarafların sunabileceği yeni veya mevcut hizmetleri eğlendirmek, değerlendirmek veya geliştirmek için, arama API 'Lerinden alınan verileri herhangi bir makine öğrenimi veya benzer bir algoritmik etkinliğinin parçası olarak kullanın.
- yasalar veya Microsoft tarafından kabul etmedikleri müddetçe, sonuçların içeriğini değiştirme (diğer gereksinimi ihlal etmediği bir şekilde yeniden biçimlendirmek yerine). 
- Sonuç içeriğiyle ilişkili atfı ve URL 'leri atlayın;
- bir sipariş veya sıralama Bing Özel Arama API'si sağlandığında bir yanıt olarak bir yanıt olarak görüntülenecek olan, bu kural, customsearch.ai portalı aracılığıyla uygulanan yeniden sıralama için geçerlidir. ;
- bir yanıtın herhangi bir bölümündeki diğer içerikleri, son kullanıcıya yanıtın bir parçası olduğunu düşünmenize yol açacak şekilde görüntüleyin; 
- yanıtın herhangi bir bölümünü görüntüleyen herhangi bir sayfada Microsoft tarafından sağlanmayan reklamları görüntüleme; -Bing görüntüsünden, haber veya Video Arama API 'Lerinden yanıtları (i) olan tüm reklamları görüntüleyin; ya da (ii) yalnızca görüntü, haber ve/veya video sonuçlarına göre filtrelenen veya bunlarla sınırlı olur.

**,. Bildirimler ve marka.** 

- Göze çarpacak, kullanıcı deneyiminin (UX) bir kullanıcıya bir arama https://go.microsoft.com/fwlink/?LinkId=521839 sorgusu girişi olanağı sunan her bir noktaya yakın olan, Microsoft gizlilik bildirimine yönelik işlevsel bir köprü içerir. "Microsoft gizlilik bildirimi" köprüsünü etiketleyin.
- Belirgin bir şekilde, bir kullanıcıya bir arama sorgusu girişi olanağı sunan https://go.microsoft.com/fwlink/?linkid=833278 UX içindeki her bir noktaya yakın bir şekilde, Bing markalamayı, ' de kullanılabilen kılavuzlarla tutarlı şekilde görüntüleyin.  Bu marka, Microsoft 'un Internet arama deneyimini güçleme kullanıcısına açıkça değer vermelidir.
- Microsoft 'un, sizin kullanım için yazılmasında aksini belirtmediği sürece, Bing Web, görüntü, haber ve video API 'lerinde, ' de https://go.microsoft.com/fwlink/?linkid=833278 açıklandığı gibi, her yanıta (veya bir yanıtın kısmına) öznitelik ekleyebilirsiniz. 
- Microsoft 'un belirli bir kullanım için yazılmasında aksini belirtmedikleri müddetçe, Bing Özel Arama API'si, Microsoft 'a (veya Yanıt bölümlerine) karşılık gelen yanıtları (veya yanıtların bölümlerini) değiştirmeyin.


**TID. Yanıtları aktarma.** Bir kullanıcının bir ara API 'den bir iletiyi bir ileti uygulaması veya sosyal medya gönderme gibi başka bir kullanıcıya aktarmasını etkinleştirirseniz aşağıdakiler geçerlidir: 
- Aktarılan yanıtların şunları olması gerekir:
  - Aktarım kullanıcısına görüntülenen yanıtların içeriğinden değiştirilmemiş içerikten oluşur (biçimlendirme değişiklikleri izin verilir);
  - Meta veri biçiminde herhangi bir veri eklemeyin;
  - Bing Web, görüntü, haber ve video API 'Lerinin yanıtları için, yanıtın Bing tarafından desteklenen bir internet arama deneyimiyle elde edilmediğini belirten dili görüntüle (örneğin, "Bing ile desteklenir", "Bing üzerinde bu görüntü hakkında daha fazla bilgi edinin" veya Bing logosu kullanarak);
  - Bing Özel Arama API'si yanıtları için, yanıtı bir internet araması deneyimiyle (örneğin, "Bu arama sonucu hakkında daha fazla bilgi edinin") gösteren görüntüleme dili.
  - Karşılığı, yanıtı oluşturmak için kullanılan tam sorguyu görüntüler; '
  - Doğrudan ya da arama altyapısı (bing.com, m.bing.com veya özel arama hizmetiniz) aracılığıyla yanıtın temel kaynağına benzer bir bağlantı veya benzer bir öznitelik ekleyin.
- Yanıtların aktarımını otomatikleştiremeyebilirsiniz. Bir aktarım, bir kullanıcı eylemi tarafından bir yanıtı aktarmaya yönelik bir amaç açıkça açıkça anlaşılmalıdır.
- Bir kullanıcıyı yalnızca aktarım yapan kullanıcının sorgusuna yanıt olarak görüntülenen yanıtları aktarmaya izin verebilir.

**A. Hizmetin sürekliliği.** Arama API 'SI yanıtlarındaki verileri kopyalamayın, depolamaya da önbelleğe vermeyin. Ancak, hizmet erişimi ve veri işleme sürekliliği sağlamak için sonuçları yalnızca aşağıdaki koşullarda koruyabilirsiniz:

**Aygıtların.** Bir son kullanıcının, bir cihazdaki sonuçları, bir son kullanıcı güncelleştirilmiş sonuçlar için başka bir sorgu gönderene kadar (örneğin,, korunan sonuçlar yalnızca kullanılabilir) için bir cihazda bekletmek üzere veya (ii)

- Son kullanıcının bu cihazdaki son kullanıcıya daha önce döndürülen sonuçlara erişmesini sağlamak için (örneğin, hizmet kesintisi durumunda); veya
- proaktif sorgunuz için döndürülen sonuçları, son kullanıcının sinyallerine bağlı olarak son kullanıcının (örneğin, beklenen hizmet kesintisi olması durumunda) son kullanıcının gereksinimlerine göre olasılığına.

**Server.** Tek bir son kullanıcıya özgü sonuçları, kontrol ettiğiniz ve yalnızca korunan sonuçları görüntüleyen bir sunucuya güvenli bir şekilde koruyabilirsiniz:

- Son kullanıcının çözümünüzde daha önce geri döndürülen bir geçmiş raporuna erişmesine olanak tanımak için, sonuçlar (i) son kullanıcının ilk sorgusunun ve (ii) bitiş u 'ya yanıt olarak görüntülenmek üzere 21 günden uzun süre boyunca saklanmayabilir. verenin yeni veya yinelenen sorgusu; veya
- Son kullanıcının, son kullanıcının, güncelleştirilmiş sonuçlar için başka bir sorgu gönderene kadar bu son kullanıcının, sorgu zamanından (i) 24 saat (ii) için olan sinyallere bağlı olarak, son kullanıcının gereksinimlerine göre olasılığına içinde kişiselleştirilmiş sonuçları depolamak için.

Her durumda, belirli bir kullanıcı için sonuçlar başka bir kullanıcının sonuçlarıyla birlikte kullanılamaz, yani her bir kullanıcının sonuçları ayrı ayrı tutulmalı ve teslim edilebilir.

**Genel.** Korunan sonuçların tüm sunumu için:

- sorgunun gönderilme zamanına açık ve görünür bir bildirim ekleyin,
- kullanıcıya bir düğme veya benzer bir yol sunun ve güncelleştirilmiş sonuçları yeniden sorgulamak ve elde etmek için 
- Bing markalamasını sonuçları sunumunda tutun ve
- Belirtilen süreleri içindeki depolanan sonuçları silin (gerekiyorsa yeni bir sorgu ile Yenile).

**VADELİ. Görüntüleme olmayan URL keşfi.** Yalnızca Kullanıcı veya müşteriden gelen bir sorguya yanıt veren bilgi kaynaklarının URL 'Lerini bulmaktan bağımsız olarak yalnızca İnternet dışı bir arama deneyiminde arama yanıtlarını kullanabilirsiniz. Bu tür URL 'Leri ya da söz konusu sorguya karşılık gelen (i) bir raporda veya benzer bir yanıtta, bu sorguya ve (ii), sorguyla ilgili önemli miktarda daha fazla değerli içerik içeren, bu sorguya ve (ii) bir şekilde kopyalayabilirsiniz. Bu kullanım ve görüntüleme gereksinimlerinin, bu kullanım ve görüntüleme gereksinimlerinin 3. bölümlerinde yer aldığı gereksinimler, aşağıdakiler dışında, bu ekran olmayan kullanım için de geçerlidir: 

- Daha önce açıklandığı gibi, sınırlı URL kopyalaması dışında, arama yanıtından gelen veya ondan türetilmiş herhangi bir veri ya da içeriği önbelleğe almaz, kopyalayamazsınız veya depolamadınız;
- Arama API 'Lerinden alınan veri (URL 'Ler dahil) kullanmanın ilgili yasaları veya üçüncü taraf haklarını ihlal etmemesini sağlayın; '
- Arama API 'lerinden alınan verileri (URL 'Ler dahil), sizin veya üçüncü tarafların sunabileceği Hizmetleri eğitmek, değerlendirmek veya geliştirmek için herhangi bir arama dizini veya makine öğrenimi veya benzer algoritmik etkinliğinin bir parçası olarak kullanamazsınız.

## <a name="next-steps"></a>Sonraki adımlar
[Yanıt aramasına genel bakış](overview.md)
