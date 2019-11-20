---
title: Project Acoustics Unity Bake öğreticisi
titlesuffix: Azure Cognitive Services
description: Bu öğreticide, Unity 'de Project Acoustics ile Acoustics fırlama açıklanmaktadır.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243118"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Project Acoustics Unity Bake öğreticisi
Bu öğreticide, Unity 'de Project Acoustics kullanarak Acoustics baklama açıklanmaktadır.

Yazılım gereksinimleri:
* Windows veya MacOS için [Unity 2018.2 +](https://unity3d.com)
* Unity projenizde veya [Project Acoustics Unity örnek içeriğinde](unity-quickstart.md) [Tümleşik proje Acoustics eklentisi](unity-integration.md)
* *Isteğe bağlı:* Bulut bilgi işlem kullanarak bakmayı hızlandırmak için bir [Azure Batch hesabı](create-azure-account.md)

## <a name="open-the-project-acoustics-bake-window"></a>Project Acoustics hazırlama penceresini açın
Unity 'de, **pencere** menüsünden **Acoustics** öğesini seçin.

![Pencere menüsünde Acoustics seçeneği vurgulanmış olan Unity Düzenleyicisi](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Gezinti kafesi oluşturma
Project Acoustics benzetim için dinleyici araştırma noktaları yerleştirmek üzere bir gezinti ağı kullanır. Unity [Gezinti kafesi iş akışını](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)kullanabilirsiniz. Ya da kendi ağ yayınınızı tasarlamak için farklı bir 3B modelleme paketi kullanabilirsiniz.

## <a name="mark-acoustic-scene-objects"></a>Akustik sahne nesnelerini işaretle
Project Acoustics, simülasyon için iki tip sahne nesnesine dayanır:
- Simülasyonda yansıtan ve occlude sesi veren nesneler
- Benzetimde dinleyici araştırma noktalarını kısıtlayan oynatıcı gezinti kafesi

Her iki nesne türü de **nesneler** sekmesi kullanılarak işaretlenir.

Nesneleri işaretlemek yalnızca *AcousticsGeometry* veya *AcousticsNavigation* bileşenlerini nesnesine eklediğinden, nesneleri işaretlemek veya Işaretlerini kaldırmak için [Standart Unity bileşeni iş akışını](https://docs.unity3d.com/Manual/UsingComponents.html) da kullanabilirsiniz. Yalnızca kafes oluşturuculara ve terrat işaretlerini işaretleyebilirsiniz. Diğer tüm nesne türleri yok sayılacak. Onay kutuları etkilenen tüm nesneleri işaretler veya işaretini kaldırın.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Akustik occlusiyon ve yansıma geometrisini işaretle
**Acoustics** penceresinin **nesneler** sekmesini açın. Nesnelerin occlude, yansıtmaları veya artışlarını devralarak seslerinin *Acoustics geometrisi* olarak işaretlenmesi. Acoustics geometrisi, toprak, duvarlar, Roofs, Windows ve pencere camı, Rug 'ler ve büyük mobilya gibi şeyleri içerebilir. Bu nesneler için herhangi bir rastgele karmaşıklık düzeyi kullanabilirsiniz. Sahnenin simülasyonundan önce voxlik olduğu için, çok sayıda yaprakları olan ağaçlar gibi son derece ayrıntılı kafesler, Basitleştirilmiş nesnelerden daha pahalı değildir.

Görünmez çakışma kafesleri gibi Acoustics etkilemeyen şeyleri eklemeyin.

Bir nesnenin araştırma hesaplaması sırasında dönüştürme işlemi ( **yoklamalar** sekmesi aracılığıyla), hazırlama sonuçlarında düzeltilir. Sahnedeki herhangi bir işaretlenmiş nesne daha sonra taşınırsa, araştırma hesaplamasının ve fırın redone olması gerekir.

### <a name="mark-the-navigation-mesh"></a>Gezinti kafesi işaretle
Unity iş akışı üzerinden oluşturulan gezinti kafesleri Acoustics sistemi tarafından alınacaktır. Kendi kafeslerinizi kullanmak için **nesneler** sekmesinden işaretleyin.

### <a name="for-reference-the-objects-tab-parts"></a>Başvuru için: nesneler sekmesi bölümleri
Sekme sayfasının bölümleri (açıklamalardan sonra resim):

1. Sekme seçimi düğmeleri ( **nesneler** sekmesi seçili olarak). Soldan sağa bir Acoustics Bake 'nin çeşitli adımlarında gezinmek için bu düğmeleri kullanın.
1. Bu sekmeyi kullanarak yapabilecekleriniz için kısa bir açıklama.
1. Hiyerarşi penceresi için kullanılabilir filtreler. Bunları kolayca işaretleyebilmeniz için Hiyerarşi penceresini belirtilen türdeki nesnelere filtrelemek için bu seçenekleri kullanın. Henüz Acoustics için herhangi bir şey eklemediyseniz, son iki seçeneğin seçilmesi hiçbir şey göstermeyecektir. Ancak, bu seçenekler işaretlendikten sonra nesneleri bulmanıza yardımcı olur.
1. Hiçbir nesne seçili değilse, bu bölümde sahnenin tüm nesnelerinin durumu gösterilmektedir.
    * Toplam: etkin, gizli olmayan nesnelerin toplam sayısı.
    * Yoksayıldı: ağ Oluşturucu ya da terrat olmayan nesne sayısı.
    * Kafes: kafes işleyici nesnelerinin sayısı.
    * Teryağmur: teryağmur nesnelerinin sayısı.
    * Geometri: **Acoustics geometrisi**olarak işaretlenen kafes veya teryağmur nesnelerinin sayısı.
    * Gezinti: **Acoustics gezintisi**olarak işaretlenen ağ veya teryağmur nesnelerinin sayısı. Bu sayı Unity Navkafesi içermez.
1. Sahnenin yalnızca ağ Oluşturucu ve terrat 'lar olan "MarkAble" nesnelerinin toplam sayısı. Bu nesneleri, Acoustics için geometri veya gezinti olarak işaretlemek için onay kutularını kullanın (uygun bileşeni öğesine ekleyin).
1. Hiçbir şey seçili olmadığında, bu notta işaretlemek için nesneleri seçmenizi hatırlatır. Sahnedeki tüm nesneleri işaretlemek için bir veya iki onay kutusunu da seçebilirsiniz.
1. Nesneler seçildiğinde, bu bölüm yalnızca seçili nesnelerin durumunu gösterir.
1. "MarkAble" seçili nesnelerinin toplam sayısı. Onay kutularının seçilmesi veya temizlenmesi yalnızca seçili nesneleri işaretler veya işaretlerini kaldırır.

Sahnede seçili bir şey yoksa **nesneler** sekmesi aşağıdaki resme benzer şekilde görünür.

![Hiçbir şey seçili olmayan Acoustics Objects sekmesi](media/objects-tab-no-selection-detail.png)

Sahne veya hiyerarşi pencerenizde seçilmiş bir şey varsa, sekme aşağıdaki resme benzer şekilde görünür.

![Seçimlerle Acoustics Objects sekmesi](media/objects-tab-selection-detail.png)

Bazı nesneler işaretlenir ve bazıları yoksa, uygun onay kutularında aşağıdaki resimde olduğu gibi bir "karma" değeri gösterilir.

![Karışık bir simgeler vurgulanmış şekilde Acoustics Objects sekmesi](media/mixed-object-selection-detail.png)

Tüm öğeleri işaretlemek için onay kutusunu işaretleyin. Tüm nesnelerin işaretini kaldırmak için seçimini kaldırın.

Nesneler hem geometri hem de gezinme için işaretlenebilir.

## <a name="select-acoustic-materials"></a>Akustik malzemeleri seçin
Nesneleriniz işaretlendikten sonra **malzemeler** düğmesini seçin. Ardından akustik malzemeler atayın. Project Acoustics malzemeler sistemi, Unity görsel malzemeler sistemine bağlıdır. İki nesnenin ayrı akustik malzemeleri olması için ayrı görsel malzemelere sahip olmaları gerekir.

Akustik malzeme seçimi, her yüzeyden geri yansıtılan ses enerji miktarını belirler. Varsayılan akustik malzemeler, çelik 'ya benzer şekilde bir anacıya sahiptir. Project Acoustics, görsel malzeme adına göre malzemeler önerir. Ayrıca, ayarlanabilir bir ansorption-katsayı kaydırıcısını etkinleştirmek için bir malzemeden **özel** olarak akustik malzemeler atayabilirsiniz.

Bir odada belirli bir malzemenin ters tutulması süresi, büyük bir süre içinde kendi kendine ilgili bir katsayı ile ilgilidir. Çoğu malzemenin 0,01 ile 0,20 arasında bir aralığında ABD değeri vardır. Bu aralığın dışında, yaygın olmayan katların bulunduğu malzemeler çok büyük bir yerdir.

![Grafik, geri alma süresinin ve emme katının negatif bağıntısını gösterir.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Başvuru için: malzemeler sekmesinin bölümleri
![Unity 'de Acoustics malzemeler sekmesi](media/materials-tab-detail.png)
1. **Malzemeler** düğmesi bu sekmeyi görüntüler.
2. Bu sekmeyi kullanarak yapabilecekleriniz için kısa bir açıklama.
3. Bu onay kutusu seçildiğinde, yalnızca **Acoustics geometrisi** olarak işaretlenen nesneler tarafından kullanılan malzemeler listelenir. Aksi takdirde, sahnede kullanılan tüm malzemeler listelenir.
4. **Acoustics** sütunundaki (#6) bir menüdeki seçeneklerin sırasını değiştirmek için bu seçenekleri kullanın. Akustik malzemeleri **ada** göre veya **Absorptivity**ile düşük ve yüksek olacak şekilde sıralayın.
5. Sahnede kullanılan malzemelerin alfabetik sıralanmış bir listesi. **Yalnızca Işaretlen göster** onay kutusu işaretliyse (#3), yalnızca **Acoustics geometrisi** olarak işaretlenen nesneler tarafından kullanılan malzemeler gösterilir. Sahnedeki bu malzemeyi kullanan tüm nesneleri seçmek için burada bir malzeme seçin.
6. Sahne malzemelerinin atandığı akustik malzemeler. Söz konusu sahne malzemesine atanan akustik malzemeleri değiştirmek için herhangi bir öğe seçin. Bu menülerin sıralama düzenini değiştirmek için **sıralama Acoustics by** seçenekleri (#4) kullanın.
7. Sütunda seçili olan malzemenin akustik, sol tarafında (#6). 0 değeri, kusursuz yansıtıcı (hiçbir şekilde) anlamına gelir, 1 ise kusursuz bir şekilde (yansıma yoktur). Seçili malzeme özel olmadığı için, masorption katsayısı değiştirilemez **.**
8. **Özel**olarak işaretlenen bir malzeme için kaydırıcı etkinleştirilir. Bir asorption katsayısı atamak için kaydırıcıyı taşıyabilir veya bir değer yazabilirsiniz.

## <a name="calculate-and-review-listener-probe-locations"></a>Dinleyici araştırma konumlarını hesapla ve gözden geçirme
Malzemeleri atadıktan sonra, **yoklamalar** sekmesine geçin. benzetim için dinleyici araştırma noktaları yerleştirmek için **Hesapla** 'yı seçin.

### <a name="what-the-calculate-button-does"></a>"Calculate" düğmesi ne yapar
**Hesapla** düğmesi, seçtiğiniz akustik sahne geometrisini kullanarak sahneyi benzetim için hazırlar:

- Sahne alanı kafeslerden yararlanır ve bir *Voxel birimini*hesaplar. Voxel birimi, küçük üçüncü dereceden "voxfit" ile oluşan tüm sahnenin bir birimidir. Voxel boyutu benzetim sıklığıyla belirlenir ve **benzetim çözümleme** ayarı tarafından denetlenir. Her Voxel, "açık hava" veya sahne geometrisi içeren olarak işaretlenir. Bir Voxel, geometri içeriyorsa, Voxel, söz konusu geometriye atanmış olan malzemenin büyük bir katsayısı ile etiketlenir.
- Dinleyici araştırma noktalarını yerleştirmek için gezinti kafesleri kullanır. Algoritma, uzamsal kapsam ve benzetim süresinin ve dosya boyutunun rekabet sorunlarını dengeler. Dar corler ve küçük boşlukların her zaman kapsama sahip olduğundan emin olmak için bu bir amaçlar. Tipik araştırma noktası, küçük sahneler için 100 arasında, büyük sahneler için birkaç bin arasındadır.

Sahnenin boyutuna ve makinenizin hızına bağlı olarak, bu hesaplamalar birkaç dakika sürebilir.

### <a name="review-voxel-and-probe-placement"></a>Voxel ve araştırma yerleşimini gözden geçirin
Sahnıza bakmaya hazırsınız olduğunuzdan emin olmak için hem Voxel verileri hem de araştırma noktası konumlarını önizleyin. Tamamlanmamış bir gezinti ağı veya eksik ya da ek akustik geometri, genellikle önizlemede kolayca görülebilir. **Şeyler** menüsünü kullanarak Voxel ve araştırma yerleşimini etkinleştirin veya devre dışı bırakın.

![Unity 'de şeyler menüsü](media/gizmos-menu.png)

Akustik geometriyi içeren Voxler yeşil küpler olarak gösterilir. Sahneye göz atın ve geometri olması gereken her şeyin Vox 'e sahip olduğunu doğrulayın. Sahne kameranın, Vox 'in gösterilmesi için nesnenin yaklaşık 5 metresinden fazla olması gerekebilir.

Kalın çözünürlükte oluşturulan voxters çözünürlüğe göre karşılaştırırsanız, çok büyük bir Vox 'in büyük olduğunu görürsünüz.

![Unity düzenleyicisinde kaba voxters Önizleme](media/voxel-cubes-preview.png)

Simülasyon sonuçları, çalışma zamanında dinleyici araştırma noktası konumları arasında enterpolasyonlardır. Oyuncunun beklenen her yerde araştırma noktaları olup olmadığını kontrol edin.

![Unity düzenleyicisinde araştırma önizlemesi](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Sahne yeniden adlandırmalarına dikkat edin
Sahne adı, sahneyi araştırma noktası yerleşimini ve voxelileştirme 'u depolayan dosyalara bağlamak için kullanılır. Araştırma noktaları hesaplandıktan sonra sahneyi yeniden adlandırırsanız, malzeme atama ve yerleştirme verileri kaybedilir ve yeniden çalıştırmanız gerekir.

### <a name="for-reference-parts-of-the-probes-tab"></a>Başvuru için: yoklamalar sekmesinin bölümleri
![Unity 'de Acoustics araştırmaları sekmesi](media/probes-tab-detail.png)

1. **Yoklamalar** düğmesi bu sekmeyi getirir.
2. Bu sekmede neler yapabileceğinize ilişkin kısa bir açıklama.
3. Kaba veya ince simülasyon çözünürlüğü ayarlamak için bu seçenekleri kullanın. Kaba bir daha hızlıdır, ancak bir denge vardır. Ayrıntılar için bkz. [Bake çözünürlüğü](bake-resolution.md).
4. Acoustics veri dosyalarının nereye yerleştirileceğini belirtir. Klasör seçicisine erişmek için " **...** " düğmesini seçin. Varsayılan konum *varlıklardır/AcousticsData*. Bu konumda bir *Düzenleyici* alt klasörü de oluşturulur. Daha fazla bilgi için, bu makalenin ilerleyen kısımlarında yer [alarak, hazırlama işlemi tarafından eklenen veri dosyaları](#Data-Files)bölümüne bakın.
5. Burada belirtilen ön ek, bu sahnenin veri dosyalarını adlandırmak için kullanılır. Varsayılan değer "Acoustics_ *[Sahname]* " dir.
6. Yoklamalar hesaplandıktan sonra, az önce açıklandığımız denetimler devre dışı bırakılmıştır. Hesaplamaları silmek ve yeni ayarlarla yeniden hesaplayabilmeniz için denetimleri etkinleştirmek üzere **Temizle** düğmesini seçin.
7. Sahneyi tıklatıp araştırma noktası konumlarını hesaplamak için **Hesapla** ' yı seçin. Hesaplama, makinenizde yerel olarak yapılır. Bir bakmadan önce yapılmalıdır.

Project Acoustics 'in bu sürümünde, yoklamalar el ile yerleştirilemez. **Yoklamalar** sekmesinde otomatikleştirilmiş işlemi kullanın.

İnce çözüm hakkında daha fazla bilgi için bkz. [Bake çözünürlüğü](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Azure Batch kullanarak sahneye bakın
Azure Batch hizmetini kullanarak sahneye bulutta bir işlem kümesi üzerinden bakmanıza neden olabilirsiniz. Project Acoustics Unity eklentisi, her Bake için bir Azure Batch kümesini oluşturmak, yönetmek ve bölmek üzere Azure Batch doğrudan bağlanır. **Bake** sekmesinde, Azure kimlik bilgilerinizi girin, bir küme makine türü ve boyutu seçin ve ardından **Bake**' yi seçin.

### <a name="for-reference-parts-of-the-bake-tab"></a>Başvuru için: Bake sekmesinin parçaları
![Unity 'deki Acoustics Bake sekmesi](media/bake-tab-details.png)

1. **Bake** düğmesi bu sekmeyi görüntüler.
2. Bu sayfada neler yapabileceğinize ilişkin kısa bir açıklama.
3. Azure hesabınız oluşturulduktan sonra bu alanlara Azure kimlik bilgilerinizi girin. Daha fazla bilgi için bkz. [Azure Batch hesabı oluşturma](create-azure-account.md).
4. Acoustics araç takımı için Docker Image etiketi alanı.
5. Aboneliklerinizi yönetmek, kullanımı izlemek ve fatura bilgilerini görüntülemek için Azure portal açar.
6. Hesaplama için kullanılacak Azure Batch işlem düğümü türünü belirtir. Düğüm türü, Azure veri merkezi konumunuz tarafından desteklenmelidir. Emin değilseniz, **Standard_F8s_v2**olarak bırakın.
7. Hesaplama için kullanılacak düğüm sayısı. Bu sayı, FIRE süresini etkiler. Azure Batch çekirdek ayırdınız ile sınırlıdır. Varsayılan ayırma yalnızca iki 8 çekirdekli düğüm veya 1 16 çekirdekli düğüme izin verir, ancak Genişletilebilir. Çekirdek ayırma kısıtlamaları hakkında daha fazla bilgi için bkz. [Azure Batch hesabı oluşturma](create-azure-account.md).
8. İşlem havuzunuzu [düşük öncelikli düğümleri](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)kullanacak şekilde yapılandırmak için bu onay kutusunu işaretleyin. Düşük öncelikli işlem düğümleri çok daha düşük maliyetlidir. Ancak, her zaman kullanılabilir olmayabilir veya herhangi bir zamanda yok edilebilir.
9. **Yoklamanız** sekmesinde hesaplanan şekilde sahnenin araştırma sayısı. Yoklamaların sayısı, bulutta çalıştırılması gereken benzetimleri sayısını belirler. Yoklamalara göre daha fazla düğüm belirtemezsiniz.
10. İş için düğüm başlangıç saati hariç olmak üzere, bulutta çalıştırmak için gereken süre tahmini. İş çalışmaya başladıktan sonra, bu alan, sonuçları geri alana kadar ne kadar süreyle bir tahmin gösterir.
11. Benzetimleri çalıştırmak için gereken bilgi işlem zamanının toplam miktarı. Bu değer, Azure 'da kullanılacak olan düğüm işlem zamanının toplam miktarıdır. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [Azure hazırlama maliyeti tahminini](#Estimating-bake-cost) inceleyin.
12. Bu ileti, iş tamamlandığında hazırlama sonuçlarının kaydedileceği yeri belirtir.
13. *(Yalnızca Gelişmiş kullanım:)* bu düğme, oluşturduğunuz bir fırt hakkında Unity 'yi unutmasını zorlar. Örneğin, sonuçları başka bir makine kullanarak indirdiyseniz, bu işi unutmak için **durumu temizle** düğmesini seçin. Sonuç dosyası, hazırsa *indirilmeyecektir.* *Bu, işi iptal etme ile aynı değildir. Çalışıyorsa, iş bulutta çalışmaya devam edecektir.*
14. Hazırlama 'yi buluta göndermek için bu düğmeyi seçin. Bir iş çalışırken, bu düğme **Iptal işi**olur.
15. Bilgisayarınızda [Acoustics simülasyonu](#Local-bake)işlemeye hazırlanmak için bu düğmeyi seçin.
16. Bu alan, Bake 'nin durumunu gösterir. Hazırlama tamamlandığında, "indirilen" gösterir.

[Azure Portal](https://portal.azure.com)etkin işler, işlem havuzları ve depolama hakkında her zaman bir bilgi edinebilirsiniz.

Bir iş çalışırken, **Bake** düğmesi etiketi **işi iptal et**olarak değişir. Sürmekte olan işi iptal etmek için bu düğmeyi kullanın. Onaylamanız istenecektir. Bir işin iptal edilmesi geri alınamaz. İptal ettiğinizde, hiçbir sonuç kullanılamayacak, ancak yine de kullanılan tüm Azure işlem süresi ücretlendirilirsiniz.

Bir Bake başladıktan sonra Unity 'yi kapatabilirsiniz. Projeye, düğüm türüne ve düğüm sayısına bağlı olarak, bir bulut fırından birkaç saat sürebilir. Projeyi yeniden yüklediğinizde ve Acoustics penceresini açtığınızda hazırlama iş durumu güncelleştirilecektir. İş tamamlanırsa, çıkış dosyası indirilir.

Güvenlik için Azure kimlik bilgileri yerel makinenizde depolanır ve Unity düzenleyicinizle ilişkilendirilir. Yalnızca Azure ile güvenli bir bağlantı kurmak için kullanılır.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Azure portal çalışan bir işin durumunu bulma

1. **Hazırlama** sekmesinde hazırlama iş kimliğini bulun.

    ![Hazırlama sekmesinde, Unity hazırlama iş kimliği vurgulanmış](media/unity-job-id.png)  

2. [Azure Portal](https://portal.azure.com)açın, Bake Için kullanılan Batch hesabına gidin ve **işler**' i seçin.

    ![Azure portal Işler bağlantısı](media/azure-batch-jobs.png)  

3. İş listesinden iş KIMLIĞI için arama yapın.

   ![Azure Portal, hazırlama iş durumu vurgulandı](media/azure-bake-job-status.png)  

4. İlgili görevlerin durumunu ve genel iş durumunu görmek için iş KIMLIĞI ' ni seçin.

   ![Hazırlama görevi durumu](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a>Azure hazırlama maliyetini tahmin etme

Ne kadar fazla maliyet olacağını tahmin etmek için bir süre olan **Tahmini Işlem maliyeti** değeri ile başlayın. Seçtiğiniz **VM düğüm türü** için bu değeri, yerel para biriminizde saatlik maliyet ile çarpın. Sonucun, düğümlerin çalışır duruma getirmek için gereken düğüm süresini içermediğini unutmayın.

Örneğin, bir $0.40/hr maliyeti olan düğüm türü için **Standard_F8s_v2** ' i seçtiğinizi varsayalım. **Tahmini Işlem maliyeti** 3 saat ve 57 dakika ise, işi çalıştırmak için tahmini maliyet $0,40 * ~ 4 saat = ~ $1,60 olur. Düğümlerin çalışmaya başladığı ek süre nedeniyle gerçek maliyet büyük olasılıkla biraz daha yüksek olacaktır.

Saatlik düğüm maliyetlerini [Azure Batch fiyatlandırmayla](https://azure.microsoft.com/pricing/details/virtual-machines/linux)bulun. (Kategori olarak iyileştirilmiş veya **yüksek performanslı** bilgi **işlem** ' i seçin.)

## <a name="Local-bake"></a>Bilgisayarınızda sahne bakın
Ayrıca, sahsitenizin kendinize ait bir BILGISAYAR üzerinde de bir bakın. Bu yöntem, bir Azure Batch hesabı oluşturmadan önce Acoustics for Small sahneleri denemek için yararlı olabilir. Ancak yerel Acoustics simülasyonu sahnenin boyutuna bağlı olarak uzun zaman alabilir.

### <a name="minimum-hardware-requirements"></a>En düşük donanım gereksinimleri
* En az 8 çekirdeğe ve 32 GB RAM 'e sahip bir x86-64 işlemcisi
* Docker 'ı çalıştırmak için [Hyper-V etkinleştirildi](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

Örnek olarak, 8 çekirdekli bir makine üzerinde sınamamız, Intel Xeon E5-1660 @ 3 GHz ve 32 GB RAM:
* 100 araştırmalarla ilgili küçük bir sahne, ince bir fırt veya 32 saati boyunca yaklaşık 2 saat sürdü.
* 1\.000 araştırmaların bulunduğu orta ölçekli bir sahne, ince bir fırt veya 21 gün boyunca yaklaşık 20 saat sürdü.

### <a name="set-up-docker"></a>Docker 'ı ayarlama
Simülasyonu işleyecek BILGISAYARA Docker 'ı yükleyip yapılandırın:
1. [Docker Desktop](https://www.docker.com/products/docker-desktop)'ı yükler.
2. Docker ayarlarını açın, **Gelişmiş**' e gidin ve kaynakları en az 8 GB RAM için yapılandırın. Docker 'a ayırabileceğiniz CPU sayısı ne kadar yüksekse, fırt daha hızlı tamamlanır.  
![ örnek Docker ayarları @ no__t-1
1. **Paylaşılan sürücüler**' e gidin ve işlemek için kullanılan sürücü için paylaşımı açın.  
![Docker paylaşılan sürücü seçenekleri @ no__t-1

### <a name="run-the-local-bake"></a>Yerel bakışta Çalıştır
1. **Bake** sekmesinde **Yerel Bake 'yi hazırla** düğmesini seçin. Ardından, giriş dosyalarını ve yürütme komut dosyalarını kaydetmek için bir klasör konumu seçin. Daha sonra, en düşük donanım gereksinimlerini karşılayan ve bu makineye klasörü kopyalayarak Docker 'ı yüklediğiniz sürece hazırlama 'yi herhangi bir makinede çalıştırabilirsiniz.
2. Benzetimi başlatmak için Windows üzerinde *runlocalbake. bat* betiğini veya macos üzerinde *runlocalbake.sh* betiğini çalıştırın. Bu betik, simülasyon işleme için gereken araç kümesiyle proje Acoustics Docker görüntüsünü getirir ve benzetimi başlatır.
3. Simülasyon bittikten sonra, elde edilen *. Ace* dosyasını Unity projenize geri kopyalayın. Unity 'nin onu ikili bir dosya olarak tanımasını sağlamak için dosya uzantısına ". Bytes" ekleyin (örneğin, "scene1. Ace. Bytes"). Simülasyonu için ayrıntılı Günlükler *AcousticsLog. txt* ' de depolanır. Herhangi bir sorunla karşılaşırsanız sorunu tanılamanıza yardımcı olması için bu dosyayı inceleyin.

## <a name="Data-Files"></a>Hazırlama işlemi tarafından eklenen veri dosyaları

Aşağıdaki dört veri dosyası, hazırlama işlemi sırasında oluşturulur. Biri simülasyon sonuçlarını içerir ve başlığınız ile birlikte gelir. Diğer kullanıcılar Unity Düzenleyicisi ile ilgili verileri depolar.

Simülasyon sonucu:
* *Varlıklar/AcousticsData/Acoustics @ no__t-1 [manzara adı]. Ace. Bytes*: Bu dosya, çalışma zamanı arama tablosudur. Simülasyonu sonuçları ve voxeli, akustik sahne öğeleri içerir. Bu dosyanın adını ve konumunu **yoklamalar** sekmesinden değiştirebilirsiniz.

   *Simülasyon sonuç dosyasını silmemeye dikkat edin. Sahnenin geri bakmasının dışında kurtarılabilir.*

Düzenleyici veri dosyaları:
* *Varlıklar/düzenleyici/[manzara adı] \_AcousticsParameters. varlık*: Bu dosya, Acoustics Kullanıcı arabirimindeki alanlara girdiğiniz verileri depolar. Bu dosyanın adını ve konumunu değiştiremezsiniz.
* *Varlıklar/AcousticsData/Editor/Acoustics_ [manzara adı].* II: Bu **Dosya, araştırma sekmesindeki** **Hesapla** düğmesini seçtiğinizde hesaplanan voxeli, Acoustics geometrisini ve malzeme özelliklerini depolar. Bu dosyanın adını ve konumunu **yoklamalar** sekmesinden değiştirebilirsiniz.
* *Varlıklar/AcousticsData/Editor/Acoustics @ no__t-1 [manzara adı] @no__t -2config. xml*: Bu dosya **Calculate**' i seçtiğinizde hesaplanan simülasyon parametrelerini depolar. Bu dosyanın adını ve konumunu **yoklamalar** sekmesinden değiştirebilirsiniz.

## <a name="set-up-the-acoustics-lookup-table"></a>Acoustics arama tablosunu ayarlama
Projeyi Proje panelinden **Acoustics** prefab 'den sahneye sürükleyin:

![Unity 'de Acoustics prefab](media/acoustics-prefab.png)

**Projectacoustika** oyun nesnesini seçin ve Inspector paneline gidin. Hazırlama sonuçlarınızın konumunu belirtin (. Ace dosyası, *varlıklar/AcousticsData*): dosyayı Acoustics Manager betiğine sürükleyin veya metin kutusunun yanındaki daire düğmesini seçin.

![Unity 'de Acoustics Manager prefab](media/acoustics-manager.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Unity tasarım denetimlerini](unity-workflow.md)araştırma.
* [Project Acoustics tasarım kavramlarını](design-process.md)keşfet.
