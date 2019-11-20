---
title: Project Acoustics Unreal Bake öğreticisi
titlesuffix: Azure Cognitive Services
description: Bu belgede, gerçek olmayan Düzenleyici uzantısı kullanılarak bir Acoustics hazırlama gönderme işlemi açıklanmaktadır.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854897"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Project Acoustics Unreal Bake öğreticisi
Bu belgede, gerçek olmayan Düzenleyici uzantısı kullanılarak bir Acoustics hazırlama gönderme işlemi açıklanmaktadır.

Bake yapmanın beş adımı vardır:

1. Oynatıcı gezinti kafesi oluşturun veya etiketleyin
2. Etiket Acoustics geometrisi
3. Geometriye akustik malzemeler özellikleri atama
4. Önizleme araştırması yerleşimi
5. Hazırlama

## <a name="open-the-project-acoustics-editor-mode"></a>Project Acoustics düzenleyici modunu açın

Project Acoustics eklenti paketini projenize aktarın. Bu konuda yardım almak için, [Unreal Integration](unreal-integration.md) konusuna bakın. Eklenti tümleşik olduktan sonra, yeni Acoustics modu simgesine tıklayarak Acoustics Kullanıcı arabirimini açın.

![Gerçek olmayan düzenleyici Acoustics modu seçeneğinin ekran görüntüsü](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Acoustics için biçim aktörleri

Nesneler sekmesi, Acoustics modunu açtığınızda görüntülenen ilk sekmedir. **AcousticsGeometry** veya **AcousticsNavigation** etiketlerini aktöre eklemek için bu sekmeyi, düzeyinizdeki bir şekilde etiketleyerek kullanın.

Dünya dışı bir veya daha fazla nesne seçin veya **toplu seçim** bölümünü kullanarak belirli bir kategorinin tüm nesnelerini seçmeye yardımcı olun. Nesneler seçildikten sonra, istenen etiketi seçili nesnelere uygulamak için **etiketleme** bölümünü kullanın.

Bir **AcousticsGeometry** veya **AcousticsNavigation** etiketi yoksa simülasyonu göz ardı edilir. Yalnızca statik kafesler, NAV kafesler ve fdscapes desteklenir. Başka bir şey etiketlerseniz, yok sayılır.

### <a name="for-reference-the-objects-tab-parts"></a>Başvuru için: Nesneler sekmesi bölümleri

![Gerçek olmayan Acoustics Objects sekmesinin ekran görüntüsü](media/unreal-objects-tab-details.png)

1. Sekme seçimi düğmeleri (**nesneler** sekmesi seçili). En üstten en alta Acoustics Bake yapmak için çeşitli adımlarda gezinmek üzere bu düğmeleri kullanın.
2. Bu sayfayı kullanarak yapmanız gerekenler hakkında kısa bir açıklama.
3. Düzeydeki aktörlerin kullanılabilir seçicileri.
4. **Seç** ' e tıkladığınızda, denetlenen aktör türlerinden en az biriyle eşleşen tüm nesneleri seçin.
5. **Tüm seçimi kaldır** ' a tıkladığınızda geçerli seçim temizlenir. Bu, kaçış anahtarıyla vurmaya benzer.
6. Seçili aktörlerin geometrisi veya gezinti etiketinin uygulanıp uygulanmayacağını belirlemek için bu radyo düğmelerini kullanın.
7. **Etiketi** tıklatmak seçili etiketi seçili olan tüm aktörlerin tamamına ekler.
8. **Untag** tıklamak seçili etiketi seçili olan tüm aktörlerden kaldırır.
9. **Etiketli Seç** ' i tıklatmak geçerli seçimi temizler ve seçili olan etikete sahip tüm aktörleri seçer.
10. Bu istatistikler, her etiket türüyle kaç aktörlerin etiketlenmekte olduğunu gösterir.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Etiket Acoustics occlusiyon ve yansıma geometrisi

Acoustics penceresinin nesneler sekmesini açın. Nesnelerin occlude, yansıtmaları veya artışlarını devralarak seslerinin Acoustics geometrisi olarak işaretlenmesi. Acoustics geometrisi, toprak, duvarlar, Roofs, Windows & pencere camı, Rug 'ler ve büyük mobilya gibi şeyleri içerebilir. Bu nesneler için herhangi bir rastgele karmaşıklık düzeyi kullanabilirsiniz. Sahnenin simülasyonundan önce voxlik olduğu için, çok küçük yaprakları olan ağaçlar gibi son derece ayrıntılı kafesler, Basitleştirilmiş nesnelerden daha pahalı değildir.

Görünmez çakışma kafesleri gibi Acoustics etkilemeyen şeyleri eklemeyin.

Bir nesnenin araştırma hesaplaması sırasında dönüştürme işlemi (aşağıdaki yoklamalar sekmesi aracılığıyla), hazırlama sonuçlarında düzeltilir. Sahnedeki işaretli nesnelerden herhangi birinin taşınması, araştırma hesaplamasının yeniden yapılması ve sahnenin yeniden gerçekleştirilmesi için gerekli olacaktır.

### <a name="create-or-tag-a-navigation-mesh"></a>Gezinti kafesi oluşturma veya etiketleme

Simülasyon için araştırma noktaları yerleştirmek üzere bir gezinti kafesi kullanılır. Gerçek olmayan [Gezgin kafes sınırları birimini](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)kullanabilir veya kendi gezinti kafesinizi belirtebilirsiniz. En az bir nesneyi **Acoustics gezintisi**olarak etiketlemelisiniz. Eğer gerçek olmayan gezinti kafesi kullanıyorsanız, ilk olarak oluşturulduğundan emin olun.

### <a name="acoustics-volumes"></a>Acoustics birimleri ###

**Acoustics birimleri**ile gezinti alanlarınızla yapabileceğiniz daha fazla gelişmiş özelleştirme de vardır. **Acoustics birimler** , sahneye ekleyebileceğiniz aktörlerdir ve gezinti kafesine dahil edilecek ve göz ardı edilecek bölgeleri seçmenizi sağlar. Aktör, "Include" ve "Exclude" arasında geçiş yapılabilir bir özellik sunar. "Dahil" birimler yalnızca içindeki gezinti kafesinin ve "hariç tutma" birimlerinin kabul edildiği ve bu alanların yoksayılmasını sağlar. "Hariç tut" birimleri her zaman "Ekle" birimlerinden sonra uygulanır. Nesneler sekmesindeki olağan işlem aracılığıyla **Acoustics birimlerini** **Acoustics gezintisi** olarak etiketlediğinizden emin olun. Bu aktörler otomatik olarak etiketlenemez.

![Acoustics Volume özelliklerinin gerçek zamanlı olmayan ekran görüntüsü](media/unreal-acoustics-volume-properties.png)

"Hariç tutma" birimleri genellikle daha sıkı kaynak kullanımı için araştırmaların nereye yerleştirmediğinden ilgili ayrıntılı denetim sağlamak amacıyla tasarlanmıştır.

![Acoustics biriminin gerçek zamanlı hariç tutulmasını kaldırma ekran görüntüsü](media/unreal-acoustics-volume-exclude.png)

"Dahil etme" birimleri sahnenin el ile bölümlerini oluşturmak için faydalıdır. Örneğin, sahnesinizi birden çok akustik bölgeye bölmek ister. Örneğin, çok sayıda kilometre kare ve büyük bir sahneye sahipseniz ve Acoustics hakkında daha fazla ilgi alanına ihtiyacınız varsa iki ilginiz varsa. Sahnede iki büyük "Içerme" birimi çizebilir ve her biri için her biri için as dosyaları üretebilirsiniz. Ardından, oyuncu her kutucuğa yaklaşırsa, uygun ACE dosyasını yüklemek için şema çağrılarıyla birleştirilmiş tetikleyici birimleri kullanabilirsiniz.

**Acoustics birimler** yalnızca, geometriyi kısıtlar, yalnızca gezinmeyi kısıtlar. Bir "Include" **Acoustics birimi** içindeki her bir araştırma, dalga simülasyonu gerçekleştirirken birimin dışındaki tüm gerekli geometriyi yine de çekmeye devam edecektir. Bu nedenle, bir bölümden diğerine geçen oyuncudan kaynaklanan, occlusiyon veya diğer Acoustics hiçbir süreksizlik olmaması gerekir.

## <a name="select-acoustic-materials"></a>Akustik malzemeleri seçin

Nesneleriniz etiketledikten sonra malzemeler sekmesine gitmek için **malzemeler** düğmesine tıklayın. Bu sekme, düzeydeki her malzemenin malzeme özelliklerini belirtmek için kullanılacaktır. Aktörlerin etiketlenmesi için boş olur.

Akustik malzemeler her bir yüzeyden geri yansıtılan ses enerji miktarını denetler. Varsayılan akustik malzemeler somut ' a benzer. Project Acoustics, sahne malzemesi adına göre malzeme önerisinde bulunur.

Bir odadaki belirli bir malzemenin geri alma süresi, 0,01 ile 0,20 arasında çok sayıda malzemeyle ilgili değer elde eden, büyük bir ihtimalle kendi emme katsayısı ile ilgilidir. Bu aralığın üzerindeki çok büyük katlamalar içeren malzemeler çok büyük bir yerdir. Örneğin, bir oda çok daha fazla yankı alıyorsa, duvarların, tabanın veya tavan 'nin akustik malzemesini daha yüksek bir absorptivity ile değiştirin. Akustik malzeme atama, söz konusu sahne malzemesini kullanan tüm aktörler için geçerlidir.

![Bağımsız katsayı ile birlikte ters geçen sürenin negatif bağıntısını gösteren grafik](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Başvuru için: Malzemeler sekmesinin bölümleri

![Gerçek olmayan Acoustics Objects sekmesinin ekran görüntüsü](media/unreal-materials-tab-details.png)

1. Bu sayfayı getirmek için kullanılan **malzemeler** sekmesi düğmesi.
2. Bu sayfayı kullanarak yapmanız gerekenler hakkında kısa bir açıklama.
3. **AcousticsGeometry**olarak etiketlenen aktörlerden alınan düzeyde kullanılan malzemeler listesi. Buradaki bir malzemeye tıklanması, sahformdaki bu malzemeyi kullanan tüm nesneleri seçer.
4. Sahne malzemelerinin atandığı akustik malzemeleri gösterir. Bir sahne malzemesini farklı bir akustik malzemeye yeniden atamak için bir açılır listeye tıklayın.
5. Önceki sütunda seçili olan malzemenin akustik bir katsayısını gösterir. Sıfır değeri tam yansıtıcı (hiçbir şekilde) anlamına gelir, 1 değeri mükemmel bir şekilde (yansıma yoktur). Bu değerin değiştirilmesi, Acoustics malzemesini (adım #4) **özel**olarak güncelleştirecek.

Sahnedeki malzemelerde değişiklik yaparsanız, **malzemeler** sekmesinde yansıtılan değişiklikleri görmek Için Project Acoustics eklentisindeki sekmeleri değiştirmeniz gerekir.

## <a name="calculate-and-review-listener-probe-locations"></a>Dinleyici araştırma konumlarını hesapla ve gözden geçirme

Malzemeleri atadıktan sonra, yoklamalar sekmesine geçin .

### <a name="for-reference-parts-of-the-probes-tab"></a>Başvuru için: Yoklamalar sekmesinin bölümleri

![Gerçek olmayan Acoustics araştırmaları sekmesinin ekran görüntüsü](media/unreal-probes-tab-details.png)

1. Bu sayfayı getirmek Için kullanılan yoklamalar sekme düğmesi
2. Bu sayfayı kullanarak yapmanız gerekenler hakkında kısa bir açıklama
3. Bu seçeneği kullanarak kaba bir benzetim veya ince simülasyon çözümü seçin. Kaba bir daha hızlıdır, ancak belirli bir avantajları vardır. Ayrıntılar için aşağıdaki [Bake çözümlemesine](bake-resolution.md) bakın.
4. Acoustics veri dosyalarının bu alan kullanılarak yerleştirilmesi gereken konumu seçin. "..." İle düğmeye tıklayın bir klasör seçici kullanmak için. Veri dosyaları hakkında daha fazla bilgi için aşağıdaki [veri dosyalarına](#Data-Files) bakın.
5. Bu sahnenin veri dosyaları burada belirtilen ön ek kullanılarak adlandırılacak. Varsayılan değer "[level Name] _AcousticsData".
6. Sahneyi tıklatıp araştırma noktası konumlarını hesaplamak için **Hesapla** düğmesine tıklayın. Bu, makinenizde yerel olarak yapılır ve bir fırt yapılmadan önce gerçekleştirilmelidir. Yoklamalar hesaplandıktan sonra yukarıdaki denetimler devre dışı bırakılır ve bu düğme **Açık**olacak şekilde değişir. Hesaplamaları silmek ve yeni ayarları kullanarak yeniden hesaplayabilmeniz için denetimleri etkinleştirmek üzere **Temizle** düğmesine tıklayın.

Yoklamalar, yoklamalar sekmesinde belirtilen otomatik işlem aracılığıyla yerleştirilmelidir .


### <a name="what-the-calculate-button-calculates"></a>"Calculate" düğmesi ne hesaplar

**Hesapla** düğmesi, şimdiye kadar verdiğiniz tüm verileri (geometri, gezinti, malzemeler ve kaba/ince ayar) alır ve birkaç adımdan geçer:

1. Sahne alanı kafeslerden yararlanır ve bir Voxel birimini hesaplar. Voxel birimi, sahsitenizin tamamını kapsayan 3 boyutlu bir birimdir ve küçük üçüncü dereceden "voxfit" den oluşur. Voxbüyüklük 'ın boyutu simülasyon **çözünürlüğü** ayarı tarafından belirlenen benzetim sıklığıyla belirlenir. Her Voxel, "açık hava" ya da sahne geometrisi içeren olarak işaretlenir. Bir Voxel, geometri içeriyorsa, Voxel, bu geometriye atanan malzemenin büyük bir katsayısı ile etiketlenir.
2. Daha sonra, oynatıcının gidebileceği acoustically ilginç konumları hesaplamak için gezinti verilerini kullanır. Bu konumların, doorways ve Hallas gibi daha küçük alanlar ve sonra boşluklar açmak için Odalar ve odalar gibi küçük bir kümesini bulmaya çalışır. Küçük sahneler için genellikle 100 konumundan daha az, büyük sahneler en fazla 1000 olabilir.
3. Hesapladığı son dinleyici konumlarının her biri için, "açık" olarak boşluk, bulunduğu odanın boyutu vb. gibi çeşitli parametreleri belirler.
4. Bu hesaplamaların sonuçları, belirttiğiniz konumdaki dosyalarda saklanır (aşağıdaki [veri dosyalarına](#Data-Files) bakın)

Sahnenin boyutuna ve makinenizin hızına bağlı olarak, bu hesaplamalar birkaç dakika sürebilir.

Bu hesaplamalar tamamlandıktan sonra, hazırlama 'nin iyi sonuçlar vermesini sağlamak için hem Voxel verilerinin hem de araştırma noktası konumlarından önizleme yapabilirsiniz. Hatalı gezinti kafesi veya eksik/ekstra geometri gibi şeyler genellikle önizleme sırasında hızla görünür hale gelir. böylece, bunu düzeltebilirsiniz.


## <a name="debug-display"></a>Hata ayıklama görüntüleme

Araştırma hesaplaması tamamlandıktan sonra, **AcousticsDebugRenderer**adlı dünya çapında yeni bir aktör görüntülenir. **Işleme araştırmaları** ve **Işleme voxiksel** onay kutuları, hata ayıklama görüntüsünü düzenleyici görünüm penceresinin içinde etkinleştirir.

![Acoustics hata ayıklama Oluşturucu aktörünü gerçek olmayan düzenleyicide gösteren ekran görüntüsü](media/acoustics-debug-renderer.png)

Düzeyinize ait herhangi bir voxor veya araştırmasını görmüyorsanız, görünüm sayfasında gerçek zamanlı işlemenin etkinleştirildiğinden emin olun.

![Reel olmayan gerçek zamanlı işleme seçeneğinin ekran görüntüsü](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxpiksel

Voxler, katılan geometrisi etrafında yeşil küpler olarak sahne penceresinde gösterilir. Yalnızca AIR içeren Voxler gösterilmez. Tüm sahnenin etrafında, simülasyonda kullanılacak tam Voxel birimini belirten büyük bir yeşil kutu vardır.
Sahnenin etrafında ilerleyin ve acoustically-occluding geometrisinin voxunkinin olduğunu doğrulayın. Ayrıca, çakışma kafesler gibi Acoustics olmayan nesnelerin voxsuz olmadığını kontrol edin. Sahne kameranın, Vox 'in gösterilmesi için nesnenin yaklaşık 5 metresinden fazla olması gerekebilir.

En iyi çözünürlük ile oluşturulan voxters çözünürlüğe göre karşılaştırırsanız, büyük ölçekli voxters 'ın büyük olduğunu görürsünüz.

![Gerçek olmayan düzenleyicide Acoustics VOX, Preview 'ın ekran görüntüsü](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Araştırma noktaları

Araştırma noktaları, olası oynatıcı (dinleyici) konumları ile eşanlamlıdır. Fırıllama yaparken benzetim, tüm olası kaynak konumlarını her bir araştırma noktasına bağlamayı Acoustics hesaplar. Çalışma zamanında, dinleyici konumu yakın araştırma noktaları arasında enterpoladır.

Gezindiğinde oyuncunun sahnede gezinildiği her yerde araştırma noktalarının mevcut olup olmadığını kontrol etmek önemlidir. Araştırma noktaları, proje Acoustics altyapısı tarafından gezinti altyapısına yerleştirilir ve taşınamaz ya da düzenlenemez, bu nedenle gezinti kafesinin araştırma noktalarını inceleyerek tüm olası oynatıcı konumlarını kapsadığından emin olun.

![Acoustics araştırmaları önizlemesi 'nin gerçek zamanlı olmayan görüntüsü](media/unreal-probes-preview.png)

Daha ayrıntılı bir çözüm hakkında daha fazla bilgi için bkz. [Bake çözünürlüğü](bake-resolution.md) .

## <a name="bake-your-level-using-azure-batch"></a>Azure Batch kullanarak düzeyinize bakın

Azure Batch hizmetini kullanarak sahneye bulutta bir işlem kümesiyle bakmanıza neden olabilirsiniz. Project Acoustics Unreal eklentisi, her Bake için bir Azure Batch kümesini oluşturmak, yönetmek ve bölmek üzere Azure Batch doğrudan bağlanır. Bake sekmesinde, Azure kimlik bilgilerinizi girin, bir küme makinesi türü ve boyutu seçin ve Bake ' ye tıklayın.

### <a name="for-reference-parts-of-the-bake-tab"></a>Başvuru için: Hazırlama sekmesinin parçaları

![Gerçek olmayan Acoustics Bake sekmesinin ekran görüntüsü](media/unreal-bake-tab-details.png)

1. Bu sayfayı getirmek için kullanılan Bake sekmesi düğmesi.
2. Bu sayfada yapılacaklar hakkında kısa bir açıklama.
3. Azure hesabınız oluşturulduktan sonra Azure kimlik bilgilerinizi girme alanları. Daha fazla bilgi için bkz. [Azure Batch hesabı oluşturma](create-azure-account.md).
4. Aboneliklerinizi yönetmek, kullanımı izlemek ve faturalandırma bilgilerini görüntülemek için Azure portal başlatın. 
5. Hesaplama için kullanılacak Azure Batch işlem düğümü türü. Düğüm türü, Azure veri merkezi konumunuz tarafından desteklenmelidir. Emin değilseniz, **Standard_F8s_v2**adresinden ayrılın.
6. Bu hesaplama için kullanılacak düğüm sayısı. Buraya girdiğiniz sayı, fırt 'in tamamlanma süresini etkiler ve Azure Batch çekirdek ayırmala sınırlıdır. Varsayılan ayırma yalnızca iki 8 çekirdekli düğüm veya 1 16 çekirdek düğümüne izin verir, ancak Genişletilebilir. Çekirdek ayırma kısıtlamaları hakkında daha fazla bilgi için bkz. [Azure Batch hesabı oluşturma](create-azure-account.md).
7. İşlem havuzunuzu [düşük öncelikli düğümleri](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)kullanacak şekilde yapılandırmak için bu onay kutusunu seçin. Düşük öncelikli işlem düğümleri çok daha düşük maliyetlidir, ancak her zaman kullanılamayabilir veya herhangi bir zamanda yok edilebilir.
8. İşinizin bulutta çalışması için gerçekleşmesi beklenen geçen süre miktarı. Bu, düğüm başlangıç saatini içermez. İş çalışmaya başladıktan sonra, sonuçları geri almadan önce ne kadar süreyle olması gerektiğine ilişkin olur. Bunun yalnızca bir tahmin olduğunu unutmayın.
9. Benzetimleri çalıştırmak için gereken bilgi işlem zamanının toplam miktarı. Bu, Azure 'da kullanılacak olan düğüm işlem zamanının toplam miktarıdır. Bu değeri kullanma hakkında daha fazla bilgi için bkz. [hazırlama maliyeti tahmini](#Estimating-bake-cost) .
10. Fırt düğmesine tıklayarak, hazırlama 'yi buluta gönderebilirsiniz. Bir iş çalışırken bunun yerine **Iptal işi** gösterilir. Bu sekmede herhangi bir hata varsa veya yoklamalar sekmesindeki iş akışı tamamlanmadıysa, bu düğme devre dışı bırakılır.
11. **Yoklamanız** sekmesinde hesaplanan şekilde sahnenin araştırma sayısı. Yoklamaların sayısı, bulutta çalıştırılması gereken benzetimleri sayısını belirler. Yoklamalara göre daha fazla düğüm belirtemezsiniz.
12. Bu ileti, işin geçerli durumunu veya bu sekmede hata olduğunu, bu hataların ne olduğunu bildirir.

[Azure Portal](https://portal.azure.com)etkin işler, işlem havuzları ve depolama hakkında her zaman bir bilgi edinebilirsiniz.

Bir iş, **Işi iptal**etmek Için **Bake** düğmesi değişikliklerini çalıştırırken. Devam eden işi iptal etmek için bu düğmeyi kullanın. Bir işin iptal edilmesi geri alınamaz, hiçbir sonuç kullanılamayacak ve iptal edilmeden önce kullanılan tüm Azure işlem süresi ücretlendirmeye devam edecek.

Bir fırt başlattığınızda, Unreal ' ı kapatabilirsiniz. Projeye, düğüm türüne ve düğüm sayısına bağlı olarak, bir bulut fırından birkaç saat sürebilir. Projeyi yeniden yüklediğinizde ve Acoustics penceresini açtığınızda hazırlama iş durumu güncelleştirilecektir. İş tamamlanırsa, çıkış dosyası indirilir.

Azure kimlik bilgileri yerel makinenizde güvenli bir şekilde depolanır ve gerçek olmayan projem ile ilişkilendirilir. Yalnızca Azure ile güvenli bir bağlantı kurmak için kullanılır.

### <a name="Estimating-bake-cost"></a>Azure hazırlama maliyeti tahmini

Belirli bir fırt 'in maliyet olacağını tahmin etmek için, **Tahmini Işlem maliyeti**için gösterilen değeri alın ve bu süre, seçtiğiniz **VM düğüm türünün** yerel para birimindeki saatlik maliyet ile çarpın. Sonuç, düğümlerin çalışır duruma getirmek için gereken düğüm süresini içermez. Örneğin, düğüm türü için $0.40/sa maliyeti olan **Standard_F8s_v2** ' u seçerseniz ve tahmini işlem maliyeti 3 saat ve 57 dakika ise, işi çalıştırmak için tahmini maliyet $0,40 * ~ 4 saat = ~ $1,60 olur. Düğümlerin başlatılması, daha fazla zaman kaplamasından kaynaklanabilir. Saatlik düğüm maliyetini [Azure Batch fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-machines/linux) sayfasında bulabilirsiniz (kategori Için "işlem için iyileştirilmiş" veya "yüksek performanslı işlem" seçeneğini belirleyebilirsiniz).

### <a name="reviewing-the-bake-results"></a>Hazırlama sonuçlarını gözden geçirme

Hazırlama tamamlandıktan sonra, çalışma zamanı eklentisini çalıştırarak VOX, araştırma noktalarının beklenen konumlarda olup olmadığını kontrol edin.

## <a name="Data-Files"></a>Veri dosyaları

Bu eklenti tarafından çeşitli noktalarda oluşturulan dört veri dosyası vardır. Çalışma zamanında bunlardan yalnızca biri gereklidir ve projenizin paketleme yoluna otomatik olarak eklenen projenizin Content/Acoustics klasörüne yerleştirilir. Diğer üç, Acoustics veri klasörünün içindedir ve paketlenmez.

* **[Proje]/Config/ProjectAcoustics.cfg**: Bu dosya, Acoustics modundaki Kullanıcı arabirimindeki alanlara girdiğiniz verileri depolar. Bu dosyanın konumu ve adı değiştirilemez. Bu dosyada, Bake 'yi etkileyen başka değerler de vardır, ancak bunlar gelişmiş kullanıcılar içindir ve değiştirilmemelidir.
* **[Proje]/Content/Acoustics/[LevelName]\_AcousticsData. Ace**: Bu dosya, hazırlama simülasyonu sırasında oluşturulur ve sahnenin Acoustics işlemek için çalışma zamanı tarafından kullanılan arama verilerini içerir. Bu dosyanın konumu ve adı, **yoklamalar** sekmesindeki alanlar kullanılarak değiştirilebilir. Oluşturulduktan sonra bu dosyayı yeniden adlandırmak isterseniz, gerçek olmayan projenizden Uvarlığı silin, dosyayı dosya Gezgini 'nde Unreal dışında yeniden adlandırın ve ardından yeni bir Uvarlık oluşturmak için bu dosyayı gerçek olmayan olarak yeniden içeri aktarın. Uıthe Ukıymeti 'nin yeniden adlandırılması çalışmaz.
* **[Proje]/plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData. insan**: Bu dosya voxeliacoustics geometrisi ve malzeme özelliklerini depolar. Yoklamalar sekmesindeki **Hesapla** düğmesi kullanılarak hesaplanır . Bu dosyanın konumu ve adı, **yoklamalar** sekmesindeki alanlar kullanılarak değiştirilebilir.
* **[Proje]/plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config. xml**: Bu dosya, yoklamalar sekmesindeki **Hesapla** düğmesi kullanılarak hesaplanan parametreleri depolar. Bu dosyanın konumu ve adı, **yoklamalar** sekmesindeki alanlar kullanılarak değiştirilebilir.

Azure 'dan indirilen *. ACE dosyasını silmeyen bir işlem yapın. Bu dosya, sahneyi yeniden fırlama haricinde kurtarılabilir değildir.

## <a name="next-steps"></a>Sonraki adımlar
* [Gerçek zamanlı olmayan tasarım denetimlerini](unreal-workflow.md) keşfet
* [Project Acoustics tasarım kavramlarını](design-process.md) keşfet

