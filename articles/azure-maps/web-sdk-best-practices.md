---
title: Azure Maps web SDK en iyi yöntemleri | Microsoft Azure haritaları
description: Azure Haritalar Web SDK 'sının kullanımını iyileştirmek için püf noktaları & ipuçları edinin.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878755"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Azure Maps web SDK en iyi yöntemleri

Bu belge, Azure Maps web SDK 'sının en iyi uygulamalarına odaklanmaktadır ancak, ana hatlarıyla özetlenen en iyi uygulamalar ve iyileştirmelerin birçoğu diğer tüm Azure Maps SDK 'larına uygulanabilir.

Azure Haritalar Web SDK 'Sı, büyük uzamsal veri kümelerini birçok farklı şekilde işlemek için güçlü bir tuval sağlar. Bazı durumlarda, verileri aynı şekilde işlemenin birden çok yolu vardır, ancak veri kümesinin boyutuna ve istenen işlevlere bağlı olarak, bir yöntem diğerlerinden daha iyi çalışabilir. Bu makale, performansı en üst düzeye çıkarmak ve sorunsuz bir kullanıcı deneyimi oluşturmak için en iyi yöntemleri ve ipuçlarını vurgular.

Genellikle, haritanın performansını artırmak istediğinizde, katman ve kaynak sayısını ve kullanılan veri kümelerinin ve işleme stillerinin karmaşıklığını azaltmak için yollar arayın.

## <a name="security-basics"></a>Güvenlik temelleri

Uygulamanızın en önemli bölümünün güvenliği şunlardır. Uygulamanız güvenli değilse, kullanıcı deneyiminin ne kadar iyi olabileceğini bağımsız olarak bir korsan herhangi bir uygulamayı ruya edebilir. Azure haritalar uygulamanızı güvende tutmanın bazı ipuçları aşağıda verilmiştir. Azure 'u kullanırken, size sunulan güvenlik araçlarını öğrendiğinizden emin olun. [Azure güvenliğine giriş](https://docs.microsoft.com/azure/security/fundamentals/overview)için bu belgeye bakın.

> [!IMPORTANT]
> Azure haritalar iki kimlik doğrulama yöntemi sağlar.
> * Abonelik anahtar tabanlı kimlik doğrulaması
> * Azure Active Directory kimlik doğrulaması, tüm üretim uygulamalarında Azure Active Directory kullanır.
> Abonelik anahtar tabanlı kimlik doğrulaması basittir ve birçok eşleme platformu, platform kullanımınızı faturalandırma amaçlarıyla ölçmek için bir ışık yolu yöntemi olarak kullanılır. Ancak, bu güvenli bir kimlik doğrulama biçimi değildir ve yalnızca uygulamalar geliştirilirken yerel olarak kullanılmalıdır. Bazı platformlar, isteklerde hangi IP adreslerinin ve/veya HTTP başvuran olduğunu kısıtlama yeteneği sağlar, ancak bu bilgiler kolayca sahte olabilir. Abonelik anahtarları kullanıyorsanız, [bunları düzenli olarak döndürtığınızdan](how-to-manage-authentication.md#manage-and-rotate-shared-keys)emin olun.
> Azure Active Directory, tüm uygulama senaryoları için çok sayıda güvenlik özelliği ve ayarı içeren bir kurumsal kimlik hizmetidir. Microsoft, Azure haritalar kullanan tüm üretim uygulamalarının kimlik doğrulaması için Azure Active Directory kullanmasını önerir.
> Bu belgedeki [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md) hakkında daha fazla bilgi edinin.

### <a name="secure-your-private-data"></a>Özel verilerinizin güvenliğini sağlama

Azure Maps etkileşimli harita SDK 'larına veri eklendiğinde, bu, son kullanıcının cihazında yerel olarak işlenir ve herhangi bir nedenle internet 'e hiçbir zaman geri gönderilmez.

Uygulamanız herkese açık olmaması gereken verileri yüklüyorsanız, verilerin güvenli bir şekilde erişilebilir olduğundan, güvenli bir şekilde erişildiği ve uygulamanın kendisinin kilitli ve yalnızca istenen kullanıcılarınız için kullanılabilir olduğundan emin olun. Bu adımlardan herhangi biri atlandıysa, yetkisiz bir kişi bu verilere erişme potansiyeline sahiptir. Azure Active Directory, bunu kilitlemek için size yardımcı olabilir.

[Azure App Service üzerinde çalışan Web uygulamanıza kimlik doğrulaması ekleme](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service) hakkında Bu öğreticiye bakın

### <a name="use-the-latest-versions-of-azure-maps"></a>Azure haritalar 'ın en son sürümlerini kullanma

Azure Haritalar SDK 'ları, SDK 'lar tarafından kullanılabilen herhangi bir dış bağımlılık kitaplığıyla birlikte düzenli güvenlik testi aracılığıyla gider. Bilinen güvenlik sorunları zamanında düzeltilir ve üretime serbest bırakılır. Uygulamanız Azure Maps web SDK 'sının barındırılan sürümünün en son ana sürümüne işaret ediyorsa, güvenlikle ilgili düzeltmeleri içerecek olan tüm ikincil sürüm güncelleştirmelerini otomatik olarak alır.

Azure Haritalar Web SDK 'sını NPM modülü aracılığıyla şirket içinde barındırıyorsanız, `package.json` her zaman en son ikincil sürüme işaret edebilmesi için, dosyanızdaki Azure Maps NPM paket sürüm numarasıyla birlikte giriş işareti (^) simgesini kullandığınızdan emin olun.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>İlk harita yükünü iyileştirme

Bir Web sayfası yüklenirken, yapmak istediğiniz ilk şey, kullanıcının boş bir ekranda olmaması için mümkün olan en kısa sürede bir şey işlemeye başlar.

### <a name="watch-the-maps-ready-event"></a>Haritalar için Ready olayını izleyin

Benzer şekilde, eşleme başlangıçta ilk kez yüklendiğinde, verileri bir kez daha hızlı bir şekilde yüklemek istenir, böylece kullanıcı boş bir eşlemeye bakmıyor. Eşleme kaynakları zaman uyumsuz olarak yüklediği için, bu eşleme üzerinde kendi verilerinizi işlemeye çalışmadan önce eşlemeye başlamaya kadar beklemeniz gerekir. Bir olay ve olay için beklerseniz iki olay vardır `load` `ready` . Yükleme olayı, eşleme ilk harita görünümünü tamamen yükledikten sonra ve her harita kutucuğu yüklendikten sonra harekete geçmeyecektir. Eşleme ile etkileşimde bulunmak için gereken en az eşleme kaynakları olduğunda, ready olayı harekete geçmeyecektir. Ready olayı, genellikle yükleme olayının yarısını başlatabilir ve bu sayede verilerinizi haritaya daha erken yüklemeye başlayabilmeniz için izin verir.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Azure Haritalar Web SDK 'sını geç yükleme

Haritanın hemen gerekli olmaması durumunda, geç, Azure Maps web SDK 'sını gerekli olana kadar yükleyin. Bu işlem, Azure Maps web SDK 'Sı tarafından gerekli olana kadar kullanılan JavaScript ve CSS dosyalarının yüklenmesini geciktirecek. Bunun gerçekleştiği yaygın bir senaryo, eşlemenin sayfa yükünde görüntülenmeyen bir sekmeye veya açılan pencere paneline yüklenmesinden kaynaklanır.
Aşağıdaki kod örneğinde, bir düğmeye basılana kadar Azure Maps web SDK 'sını yükleme ile nasıl geciktireyapılacağı gösterilmektedir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eşleştirmeyi geç yükle" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bodepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>Haritayı geç yükleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Eşleme için bir yer tutucu ekleyin

Eşleme ağ sınırlamaları veya uygulamanızdaki diğer öncelikler nedeniyle yüklenmeye bir süre sürüyorsa, eşleme `div` için bir yer tutucu olarak haritaya küçük bir arka plan görüntüsü eklemeyi göz önünde bulundurun. Bu, yükleme sırasında haritanın hükümsüz kılınacağı dolguyu doldurur `div` .

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Başlangıçta ilk harita stili ve kamera seçeneklerini ayarla

Genellikle uygulamalar, Haritayı belirli bir konuma veya stile yüklemek ister. Bazen geliştiriciler, eşleme yüklenene kadar bekler (veya `ready` olayı bekler) ve sonra `setCemer` haritanın veya ' `setStyle` nin işlevlerini kullanır. Bu işlem, istenen harita görünümü için gereken kaynaklar yüklenmeden önce varsayılan olarak çok sayıda kaynak son kez yüklendiği için, istenen ilk harita görünümüne ulaşmak daha uzun sürer. Daha iyi bir yaklaşım, başlatma sırasında haritaya istenen harita kamerası ve stil seçeneklerini geçirmektir.

## <a name="optimize-data-sources"></a>Veri kaynaklarını iyileştirme

Web SDK 'Sı iki veri kaynağına sahiptir,

* **Geojson kaynağı**: sınıf olarak bilinir `DataSource` , GeoJSON biçimindeki ham konum verilerini yerel olarak yönetir. Küçük ve orta ölçekli veri kümeleri için iyi (yüzlerce binlerce özelliği yukarı doğru).
* **Vektör kutucuk kaynağı**: `VectorTileSource` sınıfında bilinen, haritalar döşeme sistemine bağlı olarak, geçerli harita görünümü için vektör kutucukları olarak biçimlendirilen verileri yükler. Büyük ve çok büyük veri kümeleri için idealdir (milyonlarca veya milyarlarca özellik).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Büyük veri kümeleri için kutucuk tabanlı çözümler kullanma

Milyonlarca özellik içeren daha büyük veri kümeleriyle çalışıyorsanız, en iyi performansı elde etmenin önerilen yolu, verileri vektör veya raster görüntü kutucuk hizmeti gibi bir sunucu tarafı çözümü kullanarak kullanıma sunmasıdır.  
Vektör kutucukları, yalnızca geometrilerin odak alanına kırpıldığı ve kutucuğun yakınlaştırma düzeyi için haritanın çözümlenmesinden eşleşecek şekilde genelleştirilmiş olan görünümdeki verileri yüklemek için iyileştirilmiştir.

[Azure haritalar Oluşturucu platformu](creator-indoor-maps.md) , vektör kutucuk biçiminde veri alma olanağı sağlar. Diğer veri biçimleri, [Tipö anoe](https://github.com/mapbox/tippecanoe) gibi araçları veya [Bu sayfadaki birçok kaynak listesinden](https://github.com/mapbox/awesome-vector-tiles)birini kullanıyor olabilir.

Ayrıca, veri kümelerini sunucu tarafında raster görüntü kutucukları olarak işleyen ve eşleme SDK 'sında TileLayer sınıfını kullanarak verileri yükleyen özel bir hizmet oluşturmak mümkündür. Bu, eşlemenin yalnızca birkaç düzine görüntüyü yüklemek ve yönetmek gerektiğinden olağanüstü performans sağlar. Ancak, ham veriler yerel olarak kullanılamadığından raster kutucukları kullanmayla ilgili bazı sınırlamalar vardır. Bir ikincil hizmet genellikle her türlü etkileşim deneyimini desteklemek için gereklidir. Örneğin, bir kullanıcının hangi şekle tıkladığını öğrenin. Ayrıca, bir raster kutucuğunun dosya boyutu genellikle Genelleştirilmiş ve yakınlaştırma düzeyi iyileştirilmiş geometriler içeren sıkıştırılmış bir vektör kutucuğundan daha büyüktür.

[Veri kaynağı oluşturma](create-data-source-web-sdk.md) belgesi içindeki veri kaynakları hakkında daha fazla bilgi edinin.

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Birden çok veri kümesini tek bir vektör kutucuk kaynağı halinde birleştirme

Haritanın yönetmesi için daha az veri kaynağı, görüntülenecek tüm özellikleri işleyebilir. Özellikle, kutucuk kaynaklarına geldiğinde, iki vektör kutucuk kaynağını birleştirmek, kutucukları yarı yana almak için HTTP isteklerinin sayısını keser ve yalnızca bir dosya üstbilgisi olduğundan, toplam veri miktarı biraz daha küçüktür.

Birden çok veri kümesini tek bir vektör kutucuk kaynağında birleştirmek, [tipi Anoe](https://github.com/mapbox/tippecanoe)gibi bir araç kullanılarak sağlanabilir. Veri kümeleri tek bir özellik koleksiyonunda birleştirilebilir veya kaynak katmanları olarak bilinen vektör kutucuğu içindeki ayrı katmanlara ayrılabilir. Bir vektör kutucuk kaynağını bir işleme katmanına bağlarken, katmanla birlikte işlemek istediğiniz verileri içeren kaynak katmanını belirlersiniz.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Veri güncelleştirmeleri nedeniyle tuval yenilemelerinin sayısını azaltın

Bir `DataSource` sınıftaki verilerin eklenmesi veya güncellenmesi için çeşitli yollar vardır. Aşağıda listelenen farklı yöntemler ve iyi performans sağlamak için bazı noktalar verilmiştir.

* Veri kaynakları `add` işlevi bir veri kaynağına bir veya daha fazla özellik eklemek için kullanılabilir. Bu işlev her çağrıldığında, bir harita tuvali yenilemesi tetikler. Birçok özellik ekliyorsanız, bunları bir dizi veya özellik koleksiyonuna birleştirin ve bir veri kümesi üzerinde döngü yapmak ve bu işlevi her bir özellik için çağırmak yerine bu işleve bir kez geçirerek.
* Veri kaynakları `setShapes` işlevi bir veri kaynağındaki tüm şekillerin üzerine yazmak için kullanılabilir. Aynı şekilde, veri kaynaklarını `clear` ve `add` işlevleri birleştirir ve ikisi yerine tek bir harita tuvali yenilemesi yapar, bu çok daha hızlıdır. Bir veri kaynağındaki tüm verileri güncelleştirmek istediğinizde bunu kullandığınızdan emin olun.
* Veri kaynakları işlevi, bir `importDataFromUrl` veri KAYNAĞıNA URL aracılığıyla GeoJSON dosyasını yüklemek için kullanılabilir. Veriler indirildikten sonra veri kaynakları `add` işlevine geçirilir. GeoJSON dosyası farklı bir etki alanında barındırılıyorsa, diğer etki alanının etki alanları arası istekleri (CORs) desteklediğinden emin olun. Bu, verileri etki alanındaki yerel bir dosyaya kopyalamayı veya CORs 'nin etkinleştirildiği bir proxy hizmetini oluşturmayı düşünmezse. Dosya büyükse, bir vektör kutucuk kaynağına dönüştürmeyi düşünün.
* Özellikler sınıfla sarıldıktan sonra `Shape` `addProperty` `setCoordinates` şeklin, ve `setProperties` işlevlerinin hepsi veri kaynağındaki bir güncelleştirmeyi tetikleyip harita tuvali yenilemesi olur. Veri kaynakları ve işlevler tarafından döndürülen tüm `getShapes` Özellikler `getShapeById` , sınıfıyla otomatik olarak sarmalanır `Shape` . Birden çok şekli güncelleştirmek istiyorsanız, veri kaynakları işlevini kullanarak bunları JSON 'a dönüştürmek `toJson` , GeoJSON 'u düzenleyip bu verileri veri kaynakları işlevine geçirmek daha hızlıdır `setShapes` .

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Veri kaynakları temizleme işlevini gereksiz şekilde çağırmadan kaçının

Sınıfın Clear işlevinin çağrılması, `DataSource` harita tuvali yenilemeye neden olur. `clear`İşlev bir satırda birden çok kez çağrılırsa, eşleme her yenilemenin gerçekleşmesini beklerken bir gecikme meydana gelebilir.

Bu sıklıkla uygulamalarda göründüğü yaygın bir senaryo, bir uygulama veri kaynağını temizlediğinde, yeni verileri indirir, veri kaynağını yeniden temizler ve veri kaynağına yeni verileri ekler. İstenen kullanıcı deneyimine bağlı olarak, aşağıdaki alternatifler daha iyi olacaktır.

* Yeni verileri indirmeden önce verileri temizleyin, sonra yeni verileri veri kaynaklarına `add` veya `setShapes` işleve geçirin. Bu, haritada tek veri kümesi ise, yeni veriler indirilirken harita boş olur.
* Yeni verileri indirin ve veri kaynakları `setShapes` işlevine geçirin. Bu, haritadaki tüm verilerin yerini alır.

### <a name="remove-unused-features-and-properties"></a>Kullanılmayan özellikleri ve özellikleri Kaldır

Veri kümeniz uygulamanızda kullanılmayacak özellikler içeriyorsa, bunları kaldırın. Benzer şekilde, gerekli olmayan özelliklerde özellikleri kaldırın. Bunun birkaç avantajı vardır:

* İndirilmesi gereken veri miktarını azaltır.
* Verileri işlerken kullanılması gereken özellik sayısını azaltır.
* Bazen işleme zamanında gereken daha az işlem olan veri tabanlı ifadeleri ve filtreleri basitleştirmeye veya kaldırmaya yardımcı olabilir.

Özelliklerin çok fazla özellikleri veya içeriği olduğunda, veri kaynağına yalnızca oluşturma için gerekli olanların ve gerektiğinde ek özellik veya içeriği almak için ayrı bir yöntem ya da hizmet sağlamak için nelerin ekleneceğini kısıtlamak çok daha performanlıdır. Örneğin, bir harita üzerinde konumları görüntüleyen basit bir eşlemeniz varsa, bir dizi ayrıntılı içerik görüntülenir. Konumların haritada nasıl işleneceğini özelleştirmek için veri odaklı stil kullanmak istiyorsanız, yalnızca veri kaynağına gereken özellikleri yükleyin. Ayrıntılı içeriği göstermek istediğinizde, ek içeriği ayrı olarak almak için özelliğin KIMLIĞINI kullanın. İçerik sunucu tarafında depolanıyorsa, zaman uyumsuz olarak almak için bir hizmet kullanılabilir ve bu, eşleme ilk yüklendiğinde indirilmesi gereken veri miktarını büyük ölçüde azaltır.

Ayrıca, özelliklerin koordinatlarındaki önemli basamakların sayısını azaltmak de veri boyutunu önemli ölçüde azaltabilir. Koordinatlar 12 veya daha fazla ondalık basamak içermesi için yaygın olmayan bir durumdur; Ancak, altı ondalık basamak yaklaşık 0,1 ölçğa sahiptir. Bu, genellikle koordinat temsil eden konumdan daha kesin (ınkapı oluşturma düzenleri gibi küçük konum verileriyle çalışırken altı ondalık basamak önerilir). Altıdan fazla ondalık basamak olması muhtemelen verilerin nasıl işlendiği konusunda fark vermez ve yalnızca kullanıcının ek bir avantaj için daha fazla veri indirmesini ister.

[Coğrafi JSON verileriyle çalışmaya yönelik faydalı araçların](https://github.com/tmcw/awesome-geojson)listesi aşağıda verilmiştir.

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Hızla değişen veriler için ayrı bir veri kaynağı kullanın

Bazen, akış verilerinin canlı güncelleştirmelerini veya hareketlendirilen özellikleri göstermek gibi işlemler için haritada verileri hızlı bir şekilde güncelleştirmeniz gerekir. Bir veri kaynağı güncelleştirildiği zaman, işleme altyapısı veri kaynağındaki tüm özellikleri geçer ve işler. Statik verilerin hızla değişen verilerden farklı veri kaynaklarına ayrılması, her güncelleştirmede veri kaynağına yeniden işlenen özelliklerin sayısını önemli ölçüde azaltabilir ve genel performansı geliştirir.

Canlı verilerle vektör kutucukları kullanıyorsanız, güncelleştirmeleri desteklemeye yönelik kolay bir yol, `expires` yanıt üst bilgisini kullanmaktır. Varsayılan olarak, herhangi bir vektör kutucuk kaynağı ya da raster kutucuk katmanı, bu tarihten sonra kutucukları otomatik olarak yeniden yükler `expires` . Haritadaki trafik akışı ve olay kutucukları, yeni gerçek zamanlı trafik verilerinin haritada görüntülenmesini sağlamak için bu özelliği kullanır. Bu özellik haritalar hizmet seçeneği olarak ayarlanarak devre dışı bırakılabilir `refreshExpiredTiles` `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>GeoJSON veri kaynaklarında arabellek ve tolerans seçeneklerini ayarlama

`DataSource`Sınıfı, ham konum verilerini, hareket halindeyken işleme için yerel vektör kutucuklarına dönüştürür. Bu yerel vektör kutucukları, kutucuklar arasında kesintisiz işleme sağlamak için ham verileri kutucuk alanının sınırlarına kırpın. Seçenek ne kadar küçükse `buffer` , daha az çakışan veriler yerel vektör kutucuklarında ve daha iyi performans halinde depolanır, ancak, işleme yapıtlarının değişikliği de artar. Minimum işleme yapıtlarıyla performansın doğru karışımını elde etmek için bu seçeneği kullanmayı deneyin.

`DataSource`Sınıf Ayrıca, `tolerance` oluşturma amacıyla geometrilerin çözümlenmesi azaltılarak Douglas-Peucker basitleştirme algoritmasıyla birlikte kullanılan bir seçeneğe sahiptir. Bu tolerans değerinin artırılması, geometrilerin çözümlenme süresini azaltır ve performansı artırır. Veri kümesi için geometri çözünürlüğünün ve performansının doğru karışımını elde etmek için bu seçeneği ince ayar.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>GeoJSON veri kaynaklarının en fazla yakınlaştırma seçeneğini ayarlayın

`DataSource`Sınıfı, ham konum verilerini, hareket halindeyken işleme için yerel vektör kutucuklarına dönüştürür. Varsayılan olarak, bu işlem, yakınlaştırma düzeyi 18 ' i kadar yapılır. bu noktada, daha yakından yakınlaştırıldığında, yakınlaştırma düzeyi 18 için oluşturulan kutucukların verileri örnek olarak gösterilir. Bu, bu düzeylerde yakınlaştırıldığında yüksek çözünürlüğe sahip olması gereken çoğu veri kümesi için iyi bir sonuç verir. Ancak, durum veya bölge poligonları görüntülerken olduğu gibi, daha fazla yakınlaştırıldığında görüntülenmek üzere daha büyük olan veri kümeleri ile çalışırken, `minZoom` veri kaynağı seçeneğini gibi daha küçük bir değere ayarlamak, `12` Miktar hesaplamasını, gerçekleşen yerel kutucuk üretimini ve veri kaynağı tarafından kullanılan belleği ve performansı artırır.

### <a name="minimize-geojson-response"></a>GeoJSON yanıtını Küçült

Bir sunucudan coğrafi JSON verilerini yükleme veya düz bir dosya yükleme sırasında, indirme boyutunu gerekenden daha büyük hale getiren gereksiz alan karakterlerini kaldırmak için verilerin küçültüldüğünden emin olun.

### <a name="access-raw-geojson-using-a-url"></a>URL kullanarak ham GeoJSON 'a erişin

Coğrafi JSON nesnelerini JavaScript 'in içinde depolamak mümkündür, ancak bu nesne için oluşturduğunuz değişkende ve ayrı bir Web çalışanı içinde yöneten veri kaynağı örneğinde depolanacaksa, bu işlem çok miktarda bellek kullanır. Bunun yerine bir URL kullanarak coğrafi JSON 'u uygulamanıza sunun ve veri kaynağı verilerin tek bir kopyasını doğrudan veri kaynakları Web çalışanına yükler.  

## <a name="optimize-rendering-layers"></a>İşleme katmanlarını iyileştirme

Azure Maps, bir haritada veri işlemeye yönelik birkaç farklı katman sağlar. Bu katmanları senaryolarınızı ve genel kullanıcı deneyimini daha iyi bir şekilde uyarlamak için avantajlarından faydalanabileceğiniz birçok iyileştirme vardır.

### <a name="create-layers-once-and-reuse-them"></a>Katmanları bir kez oluşturup yeniden kullanın

Azure Haritalar Web SDK 'sının veri odaklı olduğuna karar verilir. Veriler, daha sonra işleme katmanlarına bağlanan veri kaynaklarına gider. Haritadaki verileri değiştirmek istiyorsanız veri kaynağındaki verileri güncelleştirin veya bir katmandaki stil seçeneklerini değiştirin. Bu çoğunlukla, her değişiklik olduğunda katmanları kaldırıp yeniden oluşturma özelliğinden çok daha hızlıdır.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Sembol katmanının üzerinde kabarcık katmanını düşünün

Kabarcık katmanı haritada daire olarak işler ve veri temelli bir ifade kullanılarak kolayca kendi yarıçapını ve renklerinde bulunabilir. Daire Web GL 'nin çizilmesine yönelik basit bir şekil olduğundan, işleme altyapısı bu görüntüyü yüklemek ve işlemek için gereken bir sembol katmanından daha hızlı işleyebilir. Bu iki işleme katmanlarının performans farkı, on binlerce noktayı işlerken dikkat çekici değildir.

### <a name="use-html-markers-and-popups-sparingly"></a>HTML işaretçilerini ve açılanları gelişigüzel kullanın

Azure Maps Web denetimindeki birçok katmanın farklı olarak, işleme için WebGL kullanan, HTML Işaretçileri ve açılanlar, işleme için geleneksel DOM öğeleri kullanır. Bu nedenle, daha fazla HTML işaretçileri ve açılan pencere bir sayfa eklediğine göre, daha fazla DOM öğesi vardır. Birkaç yüz HTML işaretçisi veya açılan pencere eklendikten sonra performans düşebilir. Daha büyük veri kümeleri için verilerinizi kümelendirmeyi veya sembol ya da kabarcık katmanı kullanmayı düşünün. Açılır pencereler için, yaygın bir strateji tek bir açılan pencere oluşturmak ve aşağıdaki örnekte gösterildiği gibi içeriğini ve konumunu güncelleştirerek onu yeniden kullanmaktır:

<br/>

<iframe height='500' scrolling='no' title='Çoklu PIN ile açılan pencereyi yeniden kullanma' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>birden fazla PIN Ile açılan pencereyi yeniden kullanma</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

Yani, haritada işlemek için yalnızca birkaç noktanız varsa, HTML işaretlerinin basitliği tercih edilebilir. Ayrıca, gerekirse HTML işaretçileri sürüklenebilir kolayca yapılabilir.

### <a name="combine-layers"></a>Katmanları birleştirme

Harita yüzlerce katmanı işleyebilir, ancak daha fazla katman varsa, bir sahneyi işlemek için daha fazla zaman alır. Katman sayısını azaltmak için bir strateji, benzer stillerdeki katmanları birleştirmek ya da [veri odaklı stiller](data-driven-style-expressions-web-sdk.md)kullanılarak Stillenebilir.

Örneğin, tüm özelliklerin veya değerine sahip bir özelliği olan bir veri kümesini düşünün `isHealthy` `true` `false` . Bu özelliğe göre farklı renkli kabarcıklar oluşturan bir kabarcık katmanı oluşturuyorsanız, bunu aşağıda listelenen en düşük performansa göre yapmak için birkaç yol vardır.

* Verileri değere göre iki veri kaynağına ayırın `isHealthy` ve her veri kaynağına sabit kodlanmış renk seçeneğiyle bir kabarcık katmanı ekleyin.
* Tüm verileri tek bir veri kaynağına yerleştirin ve sabit kodlanmış renk seçeneğiyle iki kabarcık katmanı ve özelliği temel alan bir filtre oluşturun `isHealthy` . 
* Tüm verileri tek bir veri kaynağına yerleştirin, `case` özelliği temel alan renk seçeneği için stil ifadesiyle tek bir kabarcık katmanı oluşturun `isHealthy` . Bunu gösteren bir kod örneği aşağıda verilmiştir.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Yumuşak sembol katmanı animasyonları oluşturma

Sembol katmanlarında çakışma algılama varsayılan olarak etkinleştirilmiştir. Bu çarpışma, iki sembolün çakışmadığından emin olmak için amaçlar 'i tespit edin. Bir sembol katmanının simge ve metin seçeneklerinin iki seçeneği vardır.

* `allowOverlap` -simgenin diğer simgelerle çakışıyorsa görünür olacağını belirtir.
* `ignorePlacement` -diğer sembollerin sembolle çakışılsın izin verilip verilmeyeceğini belirtir.

Bu seçeneklerin her ikisi de `false` Varsayılan olarak olarak ayarlanır. Bir sembol canlandırdığında, çakışma algılama hesaplamaları animasyonun her bir çerçevesinde çalışır ve bu da animasyonu yavaşlatabilir ve daha az akıcı hale getirir. Animasyonu düzgünleştirmek için bu seçenekleri olarak ayarlayın `true` .

Aşağıdaki kod örneği, bir sembol katmanını hareketlendirmek için basit bir yoldur.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sembol katmanı animasyonu" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () ile kalem <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>sembolü katmanı animasyonunu</a> inceleyin <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Yakınlaştırma düzeyi aralığını belirtin

Verileriniz aşağıdaki ölçütlerden birini karşılıyorsa, işleme altyapısının yakınlaştırma düzeyi aralığının dışında atlayabilmesi için katmanın en düşük ve en yüksek yakınlaştırma düzeyini belirttiğinizden emin olun.

* Veriler bir vektör kutucuk kaynağından geliyorsa, genellikle farklı veri türleri için kaynak katmanları yalnızca bir yakınlaştırma düzeyi aralığında kullanılabilir.
* Tüm yakınlaştırma düzeyleri için 0 ile 24 arasında kutucuk olmayan bir döşeme katmanı kullanıyorsanız ve bu katmanın yalnızca kutucukları olan düzeylerde işlemesini istiyorsanız ve kayıp kutucukları diğer yakınlaştırma düzeylerinden kutucuk ile doldurmaya çalışmayın.
* Yalnızca belirli yakınlaştırma düzeylerinde bir katman işlemek istiyorsanız.
Tüm katmanlarda `minZoom` `maxZoom` Bu mantığı temel alan yakınlaştırma düzeyleri arasında katmanın oluşturulacağı ve seçeneği vardır ` maxZoom > zoom >= minZoom` .

**Örnek**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Döşeme katmanı sınırlarını ve kaynak yakınlaştırma aralığını belirtin

Varsayılan olarak, döşeme katmanları bütün dünya genelinde kutucukları yükler. Ancak, kutucuk hizmeti yalnızca belirli bir alan için kutucuk içeriyorsa, eşleme bu alanın dışında kutucukları yüklemeye çalışacaktır. Bu durumda, her kutucuk için bir istek oluşturulur ve eşleme tarafından yapılan diğer istekleri engelleyebilen bir yanıt bekler ve böylece diğer katmanların işlenmesini yavaşlatır. Bir kutucuk katmanının sınırlarını belirtmek, eşlemenin yalnızca o sınırlayıcı kutudaki kutucukları talep eder. Ayrıca, kutucuk katmanı yalnızca belirli yakınlaştırma düzeyleri arasında kullanılabiliyorsa, aynı nedenden dolayı en düşük ve en fazla kaynak yakınlaştırmasını belirtin.

**Örnek**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Taban eşleme görünür olmadığında boş bir harita stili kullan

Katman, temel eşlemeyi tamamen kapsayan haritada yer alıyorsa, `blank` `blank_accessible` temel haritanın işlenmemesi için harita stilini veya olarak ayarlamayı düşünün. Bunu yapmanın yaygın bir senaryosu, bir tam dünya kutucuğunun temel haritanın üzerinde hiçbir opaklık veya saydam alana sahip olmadığı bir senaryodur.

### <a name="smoothly-animate-image-or-tile-layers"></a>Görüntü veya döşeme katmanlarını sorunsuzca canlandırın

Haritada bir dizi görüntü veya döşeme katmanı üzerinden animasyon uygulamak istiyorsanız. Her görüntü veya döşeme katmanı için bir katman oluşturmak ve her bir animasyon çerçevesinde tek bir katmanın kaynağını güncelleştirmek üzere opaklığı değiştirmek için genellikle daha hızlıdır. Opaklığı sıfıra ayarlayarak ve opaklık değerini sıfırdan büyük bir değere ayarlayarak yeni bir katman göstererek, katmandaki kaynağı güncelleştirmeden çok daha hızlı bir şekilde bir katman gizleme. Alternatif olarak, katmanların görünürlüğü değiştirilebilir, ancak katmanın silinme süresini sıfır olarak ayarladığınızdan emin olun, aksi takdirde, görüntüleme sırasında katmana animasyon uygular, bu da yeni katman görünür olmadan önceki katmanın gizlenmesi için bir titreşme etkisi oluşmasına neden olur.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>İnce ayar symbol Layer çakışma algılama mantığı

Sembol katmanının hem ve hem de metin olarak adlandırılan iki seçeneği vardır `allowOverlap` `ignorePlacement` . Bu iki seçenek, bir simgenin simgesinin ya da metninin örtüşebilir veya örtüşmesini belirler. Bunlar olarak ayarlandığında `false` , sembol katmanı her bir noktayı işlerken hesaplamalar yapar ve bu durumda, katmanda başka bir işlenmiş sembol olup olmadığını görür ve varsa, çakışan sembolü işlemez. Bu, haritadaki dağınıklığı azaltmak ve işlenen nesne sayısını azaltmak için iyidir. Bu seçenekleri öğesine ayarlayarak `false` , bu çakışma algılama mantığı atlanır ve tüm semboller haritada işlenir. Performans ve kullanıcı deneyiminin en iyi birleşimini elde etmek için bu seçeneği ince ayar.

### <a name="cluster-large-point-data-sets"></a>Küme büyük noktası veri kümeleri

Büyük veri noktaları kümesiyle çalışırken, belirli yakınlaştırma düzeylerinde işlendiğinde, noktaların çoğu çakıştığında ve yalnızca kısmen görünür olduğunu fark edebilirsiniz. Kümeleme, birlikte yakın olan ve bunları tek bir kümelenmiş nokta olarak temsil eden gruplandırma noktaları işlemidir. Kullanıcı Haritayı ' de yakınlaştırıp, kümeler ayrı noktalarına parçalanacaktır. Bu, işlenmesi gereken veri miktarını önemli ölçüde azaltabilir, haritanın daha az karışık hale gelmesini ve performansı iyileştirmenize olanak sağlar. `DataSource`Sınıfı yerel olarak kümeleme verilerine yönelik seçeneklere sahiptir. Ayrıca, vektör kutucukları üreten birçok araca kümeleme seçenekleri de vardır.

Ayrıca, performansı artırmak için küme yarıçapının boyutunu artırın. Küme yarıçapı arttıkça, daha az kümelenmiş işaret, takip ve işleme devam eder.
[Kümeleme noktası veri belgesinde](clustering-point-data-web-sdk.md) daha fazla bilgi edinin

### <a name="use-weighted-clustered-heat-maps"></a>Ağırlıklı kümelenmiş ısı haritaları kullanın

Isı haritası katmanı on binlerce veri noktasını kolayca işleyebilir. Daha büyük veri kümeleri için veri kaynağında kümelendirmeyi etkinleştirmeyi ve küçük bir küme yarıçapı kullanmayı düşünün ve kümeler `point_count` özelliğini yükseklik eşlemesi için bir ağırlık olarak kullanın. Küme yarıçapı yalnızca birkaç piksel boyutunda olduğunda, işlenen ısı haritasında küçük bir görsel fark olacaktır. Daha büyük bir küme yarıçapı kullanmak performansı daha da iyileştirir, ancak işlenmiş ısı haritasının çözünürlüğünü azaltabilir.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

[Bu belgedeki kümeleme ve ısı haritaları](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer) hakkında daha fazla bilgi edinin

### <a name="keep-image-resources-small"></a>Görüntü kaynaklarını küçük tut

Görüntüler, bir sembol katmanındaki sembolleri veya çokgen katmanındaki desenleri işlemek için haritalar görüntüsü Sprite öğesine eklenebilir. İndirilmesi gereken veri miktarını ve haritalar görüntüsü Sprite öğesinde kapladığı alan miktarını en aza indirmek için bu görüntüleri küçük tutun. Seçeneği kullanılarak simgeyi ölçeklendirilen bir sembol katmanını kullanırken `size` , haritada görüntülenecek en büyük boyut olan ve daha büyük olmayan bir görüntü kullanın. Bu, simgenin kullandığı kaynakları en aza indirerek yüksek çözünürlükte işlenmesini güvence altına alacak. Ayrıca, SVG, basit simge görüntüleri için daha küçük bir dosya biçimi olarak da kullanılabilir.

## <a name="optimize-expressions"></a>İfadeleri iyileştirme

[Veri tabanlı stil ifadeleri](data-driven-style-expressions-web-sdk.md) , haritadaki verilerin filtrelenmesi ve stillenmesi için çok sayıda esneklik ve güç sağlar. İfadelerin en iyi duruma getirilmesi için birçok yol vardır. Burada birkaç ipucu verilmiştir.

### <a name="reduce-the-complexity-of-filters"></a>Filtrelerin karmaşıklığını azaltma

Filtreler bir veri kaynağındaki tüm verilerin üzerinde döngü uygular ve her filtrenin filtredeki mantığa eşleşip eşleşmediğini denetler. Filtreler karmaşık hale gelirse bu performans sorunlarına neden olabilir. Bunu ele almak için bazı olası stratejiler şunlardır.

* Vektör kutucukları kullanıyorsanız, verileri farklı kaynak katmanlarına bölün.
* `DataSource`Sınıfını kullanıyorsanız, bu verileri ayrı veri kaynaklarına kadar bölün. Veri kaynaklarının sayısını filtrenin karmaşıklığıyla dengelemeye çalışın. Çok fazla veri kaynağı performans sorunlarına neden olabilir, bu nedenle senaryonuz için en uygun şeyi öğrenmek üzere bazı testler yapmanız gerekebilir.
* Bir katman üzerinde karmaşık bir filtre kullanırken, filtrenin karmaşıklığını azaltmak için stil ifadeleriyle birden çok katman kullanmayı göz önünde bulundurun. Stil ifadeleri çok sayıda katman olarak kullanıldığında, performans sorunlarına neden olabileceği için, sabit kodlanmış stillerle bir dizi katman oluşturmaktan kaçının.

### <a name="make-sure-expressions-dont-produce-errors"></a>İfadelerin hata üretmez olduğundan emin olun

İfadeler, oluşturma zamanında hesaplamalar veya mantıksal işlemler gerçekleştirmek için kod oluşturmak üzere kullanılır. Uygulamanızın geri kalanında bulunan kodda olduğu gibi, hesaplamaların ve mantıksal açıdan anlamlı olduğundan ve hata durumunda olmadığından emin olun. Deyimlerdeki hatalar, ifadenin hesaplanmasıyla ilgili sorunlara neden olur ve bu da performansı ve işleme sorunlarına yol açabilir.

En az bir yaygın hata, tüm özelliklerde mevcut olmayabilecek bir özellik özelliğine bağlı bir ifadeye sahip. Örneğin, aşağıdaki kod bir kabarcık katmanının Color özelliğini bir özelliğin özelliğine ayarlamak için bir ifade kullanır `myColor` .

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

Yukarıdaki kod, veri kaynağındaki tüm özelliklerin bir `myColor` özelliği varsa ve bu özelliğin değeri bir renk ise ince çalışır. Veri kaynağındaki verilerin denetimini tamamen tamamsanýz ve bazı tüm özelliklerin bir özellikte geçerli renge sahip olacağını biliyorsanız, bu bir sorun olmayabilir `myColor` . Bu kodun hatalardan güvenli hale getirmek için, özelliği özelliği olup olmadığını `case` denetlemek için ifadesiyle bir ifade kullanılabilir `has` `myColor` . Varsa, `to-color` tür ifadesi daha sonra bu özelliğin değerini bir renge dönüştürmeyi denemek için kullanılabilir. Renk geçersizse, bir geri dönüş rengi kullanılabilir. Aşağıdaki kodda bunun nasıl yapılacağı gösterilmektedir ve geri dönüş rengi yeşil olarak ayarlanır.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Boolean ifadelerini en belirli olandan en az belirli bir düzende sırala

Birden çok koşullu test içeren Boole ifadeleri kullanırken, koşullu testleri en belirli olandan en az belirli bir sıraya sıralayın. Bunu yaptığınızda, ilk koşul ikinci koşulun test edileceği veri miktarını azaltmalıdır ve bu nedenle gerçekleştirilmesi gereken toplam koşullu test sayısını azaltır.

### <a name="simplify-expressions"></a>İfadeleri Basitleştir

İfadeler güçlü ve bazen karmaşık olabilir. Daha basit bir ifade, daha hızlı değerlendirilir. Örneğin, basit bir karşılaştırma gerekiyorsa, gibi bir ifade `['==', ['get', 'category'], 'restaurant']` eşleşme ifadesi kullanmaktan daha iyi olacaktır `['match', ['get', 'category'], 'restaurant', true, false]` . Bu durumda, denetlenen özellik bir Boole değeri ise, bir `get` ifade bile daha basit olur `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>Web SDK sorunlarını giderme

Aşağıda, Azure Maps web SDK ile geliştirme sırasında karşılaşılan bazı yaygın sorunların ayıklanmasında bazı ipuçları verilmiştir.

**Web denetimini yüklediğimde eşleme neden görüntülenmez?**

Şunları yapın:

* Eklenmiş kimlik doğrulama seçeneklerinizi haritaya eklemiş olduğunuzdan emin olun. Bu eklenmemişse, eşleme, kimlik doğrulaması olmadan temel harita verilerine erişemediğinden ve tarayıcının Geliştirici araçlarının Ağ sekmesinde 401 hata görünmesine yönelik olarak boş bir tuval ile yüklenir.
* İnternet bağlantınızın olduğundan emin olun.
* Tarayıcı Geliştirici araçlarının hataları için konsolu denetleyin. Bazı hatalar eşlemenin işlenmemesine neden olabilir. Uygulamanızda hata ayıklayın.
* [Desteklenen bir tarayıcı](supported-browsers.md)kullandığınızdan emin olun.

**Tüm verilerim dünyanın diğer tarafında gösteriliyor, ne olacak?**
Azure Haritalar SDK 'lerinde konum olarak da adlandırılan koordinatlar Jeo-uzamsal sektör standart biçimiyle hizalanır `[longitude, latitude]` . Aynı biçim aynı zamanda koordinat coğrafi JSON şemasında tanımlananlardır. Azure Haritalar SDK 'Ları içinde kullanılan temel veriler. Verileriniz dünyanın diğer tarafında görüniyorsa, büyük olasılıkla koordinat/konum bilgileriniz için boylam ve enlem değerlerinin ters çevrilme nedenidir.

**HTML işaretçileri Web denetimindeki yanlış yerde neden görünüyor?**

Denetlenecek şeyler:

* İşaretleyici için özel içerik kullanılıyorsa, `anchor` ve seçeneklerinin doğru olduğundan emin olun `pixelOffset` . Varsayılan olarak, içeriğin alt merkezi haritadaki konumla hizalanır.
* Azure haritalar için CSS dosyasının yüklendiğinden emin olun.
* Uygulamanızdaki herhangi bir CSS 'nin kendisinin işaretine eklenmiş olup olmadığını ve konumunu etkileyip etkilemediğini görmek için HTML işaretleyici DOM öğesini inceleyin.

**Sembol katmanındaki simgeler veya metin neden yanlış yerde görünüyor?**
`anchor`Ve `offset` seçeneklerinin, resminizin veya metnin harita üzerindeki koordinat ile hizalamak istediğiniz bölümüyle uyumlu olacak şekilde doğru bir şekilde yapılandırıldığından emin olun.
Sembol yalnızca eşleme döndürüldüğünde yer alıyorsa, `rotationAlignment` seçeneğini işaretleyin. Varsayılan olarak, simgeler Kullanıcı için sağ üst görünecek şekilde haritalar Görünüm penceresi ile döndürülecektir. Ancak senaryonuza bağlı olarak, simgenin haritanın yönüne kilitlenmesi istenebilir. `rotationAlignment` `’map’` Bunu yapmak için seçeneğini ayarlayın.
Yalnızca harita genişletilmiş/aşağı eklendiğinde sembol yalnızca yerinde değilse, `pitchAlignment` seçeneğini işaretleyin. Varsayılan olarak, harita açık veya ayrılmış olduğu için haritalar Görünüm penceresi ile sağ üst kalacağız. Ancak, senaryonuza bağlı olarak, simgenin eşlemenin adına kilitlenmesi istenebilir. `pitchAlignment` `’map’` Bunu yapmak için seçeneğini ayarlayın.

**Hiç verilerim neden haritada görünmüyor?**

Denetlenecek şeyler:

* Hatalar için tarayıcının geliştirici araçlarındaki konsolu denetleyin.
* Bir veri kaynağının oluşturulduğundan ve haritaya eklendiğinden ve veri kaynağının haritaya eklenmiş bir işleme katmanına bağlı olduğundan emin olun.
* Kodunuzda kesme noktaları ekleyin ve verilerin veri kaynağına eklendiğinden emin olmak için adım adım ilerleyin ve veri kaynağı ile Katmanlar haritaya bir hata olmadan eklenmekte.
* İşleme katmanınızdan veri tabanlı ifadeleri kaldırmayı deneyin. Bu, soruna neden olan bir hata olabilir.

**Azure Maps web SDK 'sını bir korumalı iframe 'de kullanabilir miyim?**

Evet. Safari 'nin, korumalı iframe 'ler tarafından Azure Maps web SDK gereksinimi olan Web çalışanlarının çalıştırılmasını engelleyen [bir hata](https://bugs.webkit.org/show_bug.cgi?id=170075) olduğunu unutmayın. Çözüm, `"allow-same-origin"` iframe 'in Sandbox özelliğine etiketi eklemektir.

## <a name="get-support"></a>Destek alın

Sorununuzla ilgili olarak Azure haritalar için destek almanın farklı yolları aşağıda verilmiştir.

**Bir veri sorunu veya bir adresle ilgili bir sorun rapor Nasıl yaparım? mı?**

Azure Maps, veri sorunlarının raporlanabilmesi ve İzlenme şeklinde bir veri geri bildirim aracına sahiptir. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Gönderilen her sorun, veri sorununun ilerlemesini izlemek için kullanabileceğiniz benzersiz bir URL oluşturur. Bir veri sorununu çözmek için gereken süre, sorunun türüne ve değişikliğin doğru şekilde doğrulandığına göre farklılık gösterir. Düzeltildiğinde, oluşturma hizmeti güncelleştirmeyi haftalık güncelleştirmede görür, ancak coğrafi kodlama ve yönlendirme gibi diğer hizmetler aylık güncelleştirmede güncelleştirmeyi görür. Bu [belgede](how-to-use-feedback-tool.md)veri sorunu bildirme hakkında ayrıntılı yönergeler sunulmaktadır.

**Nasıl yaparım? bir hizmet veya API 'de hata rapor ediyor mu?**

https://azure.com/support

**Azure haritalar için teknik yardım nereden edinebilirim?**

Power BI 'de Azure Maps görsele ilişkiliyse, https://powerbi.microsoft.com/support/ diğer tüm Azure Maps Hizmetleri için: https://azure.com/support veya Geliştirici forumları için: https://docs.microsoft.com/answers/topics/azure-maps.html

**Nasıl yaparım? bir özellik isteği yapılsın mı?**

Kullanıcı sesli sitesinde bir özellik isteği oluşturun: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızdaki Kullanıcı deneyimini geliştirme hakkında daha fazla ipucu için aşağıdaki makalelere bakın.

> [!div class="nextstepaction"]
> [Uygulamanızı erişilebilir yapma](map-accessibility.md)

Azure haritalar ve jeo-uzamsal sektör tarafından kullanılan terminoloji hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Haritalar sözlüğü](glossary.md)
