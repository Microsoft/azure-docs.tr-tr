---
title: Veri etiketleme projesi oluşturma
titleSuffix: Azure Machine Learning
description: Machine Learning için verileri etiketlemek üzere etiketleme projelerini oluşturma ve çalıştırma hakkında bilgi edinin. Göreve yardımcı olması için ML yardımlı etiketleme veya döngü etiketleyerek insan 'yi kullanın.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: ff1783dd31b8139940e56d24ae82866b428838b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861166"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Veri etiketleme projesi ve dışarı aktarma etiketleri oluşturma 

Azure Machine Learning verileri etiketlemek için veri etiketleme projeleri oluşturmayı ve çalıştırmayı öğrenin.  Göreve yardımcı olması için makine öğrenimi yardımlı veri etiketlemeyi veya insan döngüsü etiketlemeyi kullanın.


## <a name="data-labeling-capabilities"></a>Veri etiketleme özellikleri

> [!Important]
> Veri görüntülerinin bir Azure blob veri deposunda bulunması gerekir. (Mevcut bir veri deposu yoksa, proje oluşturma sırasında görüntüleri karşıya yükleyebilirsiniz.)

Azure Machine Learning veri etiketleme, etiketleme projelerini oluşturmak, yönetmek ve izlemek için merkezi bir yerdir:
 - Etiketleme görevlerini verimli bir şekilde yönetmek için verileri, etiketleri ve ekip üyelerini koordine edin. 
 - İlerlemeyi izler ve tamamlanmamış etiketleme görevlerinin kuyruğunu korur.
 - Projeyi başlatıp durdurun ve etiketleme ilerlemesini denetleyin.
 - Etiketli verileri gözden geçirin ve COCO formatında veya Azure Machine Learning veri kümesi olarak etiketlenmiş dışarı aktarın.

## <a name="prerequisites"></a>Önkoşullar

* Yerel dosyalarda veya Azure Blob depolamada etiketlemek istediğiniz veriler.
* Uygulamak istediğiniz Etiketler kümesi.
* Etiketleme için yönergeler.
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://aka.ms/AMLFree) oluşturun.
* Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

## <a name="create-a-data-labeling-project"></a>Veri etiketleme projesi oluşturma

Etiketleme projeleri Azure Machine Learning yönetilir. Projelerinizi yönetmek için **proje etiketleme** sayfasını kullanın.

Verileriniz zaten Azure Blob depolamadaki etiketleme projesini oluşturmadan önce onu bir veri deposu olarak kullanılabilir hale getirin. Veri deposu kullanmanın bir örneği için bkz. [öğretici: ilk görüntü sınıflandırma etiketleme projenizi oluşturma](tutorial-labeling.md).

Proje oluşturmak için **Proje Ekle**' yi seçin. Projeye uygun bir ad verin ve **etiketleme görev türünü** seçin. Proje daha sonra silinmiş olsa bile, proje adı yeniden kullanılamaz.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Proje oluşturma Sihirbazı etiketleme":::

* Bir etiket kümesinden bir resme yalnızca *tek bir etiket* uygulamak istediğinizde, projeler Için **görüntü sınıflandırması çoklu sınıfı** ' nı seçin.
* Bir etiket kümesinden bir resme bir *veya daha fazla* etiket uygulamak istediğinizde, projeler Için **görüntü sınıflandırması çoklu etiketi** ' ni seçin. Örneğin, bir köpek fotoğrafı hem *köpek* hem de *Daytime* ile etiketlenebilir.
* Bir resim içindeki her nesneye bir etiket ve bir sınırlayıcı kutu atamak istediğinizde, projeler için **nesne tanımlama (sınırlama kutusu)** seçeneğini belirleyin.
* Bir etiket atamak ve bir görüntünün içindeki her nesnenin çevresine bir çokgen çizmek istediğinizde, projeler için **örnek segmentleme (çokgen)** seçeneğini belirleyin.

Devam etmeye hazırsanız **İleri ' yi** seçin.

## <a name="specify-the-data-to-label"></a>Etiketlemek için verileri belirtin

Verilerinizi içeren bir veri kümesi zaten oluşturduysanız, **mevcut bir veri kümesi seçin** açılır listesinden bunu seçin. Ya da mevcut bir Azure veri deposunu kullanmak veya yerel dosyaları karşıya yüklemek için **veri kümesi oluştur** ' u seçin.

> [!NOTE]
> Proje 500.000 'den fazla görüntü içeremez.  Veri kümeniz daha fazla ise, yalnızca ilk 500.000 resim yüklenecektir.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Azure veri deposundan veri kümesi oluşturma

Çoğu durumda, yalnızca yerel dosyaları karşıya yüklemek çok uygundur. Ancak [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) büyük miktarda veriyi aktarmanın daha hızlı ve daha sağlam bir yolunu sağlar. Dosyaları taşımanın varsayılan yolu olarak Depolama Gezgini önerilir.

Azure Blob depolamada zaten depoladığınız verilerden veri kümesi oluşturmak için:

1. Veri **deposundan veri kümesi oluştur**' u seçin  >  .
1. Veri kümenize bir **ad** atayın.
1. **Veri kümesi türü** olarak **Dosya** ' yı seçin.  Yalnızca dosya veri kümesi türleri desteklenir.
1. Veri deposunu seçin.
1. Verileriniz blob depolamalarınızın içindeki bir alt klasördeyse, yolu seçmek için, **Araştır** ' ı seçin.
    * Seçili yolun alt klasörlerindeki tüm dosyaları dahil etmek için yola "/* *" ekleyin.
    * */* Tüm verileri geçerli kapsayıcıya ve alt klasörlerine dahil etmek için "*. *" ekleyin.
1. Veri kümeniz için bir açıklama sağlayın.
1. **İleri**’yi seçin.
1. Ayrıntıları onaylayın. Ayarları değiştirmek için **geri** ' yi veya veri kümesini oluşturmak için **Oluştur** ' u seçin.


### <a name="create-a-dataset-from-uploaded-data"></a>Karşıya yüklenen verilerden veri kümesi oluşturma

Verilerinizi doğrudan karşıya yüklemek için:

1. Yerel **dosyalardan veri kümesi oluştur**' u seçin  >  .
1. Veri kümenize bir **ad** atayın.
1. **Veri kümesi türü** olarak "dosya" yı seçin.
1. *Isteğe bağlı:* Veri deposu, kapsayıcı ve yolun yolunu özelleştirmek için **Gelişmiş ayarlar** ' ı seçin.
1. Karşıya yüklenecek yerel dosyaları seçmek için **Araştır** ' ı seçin.
1. Veri kümeniz için bir açıklama sağlayın.
1. **İleri**’yi seçin.
1. Ayrıntıları onaylayın. Ayarları değiştirmek için **geri** ' yi veya veri kümesini oluşturmak için **Oluştur** ' u seçin.

Veriler, Machine Learning çalışma alanınızın varsayılan blob deposuna ("Workspace BlobStore") yüklenir.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a> Artımlı yenilemeyi yapılandırma

Veri kümenize yeni görüntüler eklemeyi planlıyorsanız, bu yeni görüntüleri projenize eklemek için artımlı yenileme kullanın.   **Artımlı yenileme** etkinleştirildiğinde, veri kümesi, etiketleme tamamlanma hızına bağlı olarak bir projeye eklenecek yeni görüntülerin düzenli aralıklarla denetlenir.   Yeni veri denetimi, proje en fazla 500.000 görüntüsünü içerdiğinde duraklar.

Projenize daha fazla görüntü eklemek için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanarak blob depolamada uygun klasöre yükleyin. 

Projenizin veri deposundaki yeni verileri sürekli olarak izlemesini istediğinizde **artımlı yenilemeyi etkinleştir** kutusunu işaretleyin. Bu veriler, etkinleştirildikten sonra projenizde bir kez çekilir, bu nedenle projenizde görüntülemeden önce veri deposuna yeni veriler ekledikten sonra beklemeniz gerekecektir.  Projenizin **Ayrıntılar** sekmesinin **artımlı yenileme** bölümünde verilerin son yenilenme sırasında zaman damgası görebilirsiniz.

Veri deposunda görüntülenen yeni görüntülerin projenize eklenmesini istemiyorsanız bu kutunun işaretini kaldırın.

## <a name="specify-label-classes"></a>Etiket sınıfları belirtme

**Etiket sınıfları** sayfasında, verilerinizi sınıflandırmak için sınıflar kümesini belirtin. Etiketlerinizin doğruluğu ve hızı sınıflar arasında seçim yapabilme olanlarından etkilenir. Örneğin, bitkiler veya hayvanlar için tam Genus ve türler yazım denetimi yapmak yerine bir alan kodu kullanın veya genabd 'yi kısaltılabilir.

Satır başına bir etiket girin. **+** Yeni bir satır eklemek için düğmesini kullanın. 3 veya 4 ' ten fazla etiket varsa ve 10 ' dan küçükse, Etiketleyiciler, çalışmalarını hızlandırmak için sayı tuşlarını kullanabilmesi için, adlara ("1:", "2:") önek eklemek isteyebilirsiniz.

## <a name="describe-the-data-labeling-task"></a>Veri etiketleme görevini açıkla

Etiketleme görevi açıkça açıklanmak önemlidir. **Etiketleme yönergeleri** sayfasında, yönergelerin etiketlenmesi için bir dış siteye bağlantı ekleyebilir veya sayfadaki düzenleme kutusunda yönergeler sağlayabilirsiniz. Yönergeleri görev yönelimli ve hedef kitleye uygun halde tutun. Bu soruları göz önünde bulundurun:

* Göreceğiniz Etiketler nelerdir ve bunların arasından nasıl seçim yapılır? Başvurmak için bir başvuru metni var mı?
* Uygun bir etiket yoksa ne yapmalı?
* Birden çok etiket uygun görünüyorsa ne yapmalı?
* Bir etikete hangi güven eşiğinin uygulanmaları gerekir? Belirli değillerse "en iyi tahminlerinden" istediklerinizi ister misiniz?
* Kısmen veya çakışan nesnelerle ne yapmalı?
* İlgilendiğiniz bir nesne görüntünün kenarıyla kırpıldıklarında ne yapmalı?
* Bir etiketi gönderdikten sonra, bir hata yaptıklarında ne yapacaklarınız gerekir?

Sınırlayıcı kutular için, önemli sorular şunlardır:

* Bu görev için sınırlayıcı kutu nasıl tanımlanır? Tamamen nesnenin iç kısmında mi olmalı, yoksa dış mi olmalıdır? Mümkün olduğunca yakından kırpılmalıdır mi yoksa bazı bir açıklığı kabul edilebilir mi?
* Etiketleyicilerin sınırlayıcı kutuları tanımlamaya ne kadar uygun olduğunu ve tutarlılığını düşünüyorsunuz?
* Görüntüde kısmen gösterilen nesne nasıl etiketlenmesi? 
* Diğer nesne tarafından kısmen kapsanan nesne nasıl etiketlenmesi?

>[!NOTE]
> Etiketleyiciler 1-9 numaralı anahtarları kullanarak ilk 9 etiketi seçebileceğini unutmayın.

## <a name="use-ml-assisted-data-labeling"></a>ML yardımlı veri etiketlemeyi kullanma

**Ml yardımlı etiketleme** sayfası etiketleme görevini hızlandırmak için otomatik makine öğrenimi modellerini tetiklemenizi sağlar. Etiketleme projenizin başlangıcında, görüntüler potansiyel bir farkı azaltmak için rastgele bir sıraya bölünür. Ancak, veri kümesinde bulunan herhangi bir sapmaları eğitilen modele yansıtılır. Örneğin, görüntülerinizin %80 ' u tek bir sınıfta ise, modeli eğitmek için kullanılan verilerin yaklaşık %80 ' i o sınıfa ait olacaktır. Bu eğitim, etkin öğrenimi içermez.


*Ml yardımlı etiketlemeyi etkinleştir* ' i seçin ve iki aşamadan oluşan yardımlı etiketlemeyi etkinleştirmek IÇIN bir GPU belirtin:
* Kümeleme
* Önceden etiketleme

Yardımlı etiketleme başlatmak için gereken etiketli görüntülerin tam sayısı sabit bir sayı değil.  Bu, bir etiketleme projesinden diğerine önemli ölçüde farklılık gösterebilir. Bazı projelerde, 300 görüntü el ile etiketlendikten sonra önceden etiket veya küme görevlerini görmek bazen mümkündür. ML yardımlı etiketleme, eğitim işlemini başlatmak için önceden eğitilen bir model kullanan *Aktarım öğrenimi* adlı bir teknik kullanır. Veri kümenizin sınıfları, önceden eğitilen modelindekilerle benzerdir ise, ön Etiketler yalnızca birkaç yüz el ile etiketlenmiş görüntüler ile kullanılabilir. Veri kümeniz, modeli önceden eğitmek için kullanılan verilerden önemli ölçüde farklıysa, çok daha uzun sürebilir.

Son Etiketler etiketleyici 'den girişe hala dayandığından, bu teknoloji bazen *döngü* etiketlenmesi olarak adlandırılır.

> [!NOTE]
> ML yardımlı veri etiketlemeyi, bir [sanal ağın](how-to-network-security-overview.md)arkasında güvenliği sağlanmış varsayılan depolama hesaplarını desteklemez. ML yardımlı veri etiketlemeyi için varsayılan olmayan bir depolama hesabı kullanmanız gerekir. Varsayılan olmayan depolama hesabı, sanal ağın arkasında güvenli hale getirilmiş olabilir.

### <a name="clustering"></a>Kümeleme

Belirli sayıda etiket gönderildikten sonra, görüntü sınıflandırması için makine öğrenimi modeli benzer görüntüleri birlikte gruplamak için başlar.  Bu benzer görüntüler, el ile etiketlemesini hızlandırmak için aynı ekranda Etiketleyiciler tarafından sunulur. Kümeleme, özellikle etiketleyici 4, 6 veya 9 ' un bir kılavuzunu görüntülerken yararlıdır. 

Bir Machine Learning modeli el ile etiketlenmiş veriler üzerinde eğitilirken, model en son tam bağlantılı katmana kesilir. Etiketlendirmesiz görüntüler, daha sonra "gömme" veya "uygun hale getirme" olarak bilinen bir işlemdeki kesilen modelden geçirilir. Bu, her görüntüyü bu model katmanı tarafından tanımlanan yüksek boyutlu bir alana katıştırır. Alanda en yakın komşu olan görüntüler kümeleme görevleri için kullanılır. 

Kümeleme aşaması, nesne algılama modelleri için görüntülenmez.

### <a name="prelabeling"></a>Önceden etiketleme

Yeterli resim etiketi gönderildikten sonra, görüntü etiketlerini tahmin etmek için bir sınıflandırma modeli kullanılır. Ya da bir nesne algılama modeli, sınırlayıcı kutuları tahmin etmek için kullanılır. Etiketleyici artık her görüntüde önceden tahmin edilen etiketler içeren sayfaları görüyor. Nesne algılama için tahmin edilen kutular da gösterilir. Görev daha sonra bu tahminleri gözden geçirdikten sonra sayfayı göndermeden önce, yanlış etiketlenmiş görüntüleri düzeltir.  

Bir Machine Learning modeli el ile etiketlenmiş veriler üzerinde eğitilene kadar, model el ile etiketlenmiş bir test kümesi üzerinde değerlendirilir ve farklı güven eşiklerinde doğruluğu tespit edilir. Bu değerlendirme işlemi, modelin, ön etiketleri göstermek için yeterince doğru olduğu bir güven eşiğini belirlemede kullanılır. Model daha sonra etiketli olmayan verilere karşı değerlendirilir. Tahmine dayalı olarak bu eşikten daha güvenli olan görüntüler, önceden etiketleme için kullanılır.

## <a name="initialize-the-data-labeling-project"></a>Veri etiketleme projesini Başlat

Etiketleme projesi başlatıldıktan sonra, projenin bazı yönleri sabittir. Görev türünü veya veri kümesini değiştiremezsiniz. Görev açıklamasının etiketlerini ve URL *'sini değiştirebilirsiniz.* Projeyi oluşturmadan önce ayarları dikkatle gözden geçirin. Projeyi gönderdikten sonra, **veri etiketleme** giriş sayfasına döndürülürsünüz, bu da projeyi **başlatılıyor** olarak gösterir.

> [!NOTE]
> Bu sayfa otomatik olarak Yenilemeyebilir. Bu nedenle, bir duraklama sonrasında, projenin durumunu **oluşturulduğu** şekilde görmek için sayfayı el ile yenileyin.

## <a name="run-and-monitor-the-project"></a>Projeyi çalıştırma ve izleme
Projeyi başlattıktan sonra Azure bu uygulamayı çalıştırmaya başlayacaktır. Projenin ayrıntılarını görmek için ana **veri etiketleme** sayfasında projeyi seçin

Projeyi duraklatmak veya yeniden başlatmak için sağ üst köşedeki **çalışma** durumunu değiştirin. Yalnızca proje çalışırken verileri etiketleyebilir.

### <a name="dashboard"></a>Pano

**Pano** sekmesi etiketleme görevinin ilerlemesini gösterir.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Veri etiketleme panosu":::

İlerleme grafiği kaç öğe etiketlendiği ve ne kadar çok işlem yapılmadığından gösterir.  Bekleyen öğeler şu olabilir:

* Henüz bir göreve eklenmemiş
* Etiketleyici 'ye atanan ancak henüz tamamlanmamış bir göreve dahil edildi 
* Henüz atanmayan görev kuyruğunda

Orta bölümde, henüz atanmak üzere görevlerin kuyruğu gösterilmektedir. ML yardımlı etiketleme devre dışı bırakıldığında, bu bölümde atanacak el ile görevlerin sayısı gösterilmektedir. ML yardımlı etiketleme açık olduğunda, bu da şu şekilde görünür:

* Kuyruktaki kümelenmiş öğeleri içeren görevler
* Kuyruktaki önceden etiketlenmiş öğeleri içeren görevler

Buna ek olarak, ML yardımlı etiketleme etkinleştirildiğinde, bir sonraki eğitimin ne zaman gerçekleşeceğini gösteren küçük bir ilerleme çubuğu görüntülenir.  Denemeleri bölümleri, makine öğrenimi çalıştırmaları her biri için bağlantılar sağlar.

* Eğitim-etiketleri tahmin etmek için bir model yapın
* Doğrulama-bu modelin tahmini öğelerin önceden etiketlenmesi için kullanılıp kullanılmayacağını belirler 
* Yeni öğeler için çıkarım tahmini çalıştırması
* Korlaleştirme-kümeler öğeleri (yalnızca görüntü sınıflandırma projeleri için)

Sağ tarafta, tamamlanmış görevlere ait etiketlerin bir dağılımı vardır.  Bazı proje türlerinde bir öğe birden çok etikete sahip olabileceğini unutmayın. Bu durumda, toplam etiket sayısı toplam sayı öğelerinden daha büyük olabilir.

### <a name="data-tab"></a>Veri sekmesi

**Veri** sekmesinde, veri kümenizi görebilir ve etiketli verileri gözden geçirebilirsiniz. Hatalı etiketlenmiş verileri görürseniz, bunu seçin ve **Reddet**' i seçin. Bu, etiketleri kaldırır ve verileri etiketsiz kuyruğa geri koyar.

### <a name="details-tab"></a>Ayrıntılar sekmesi

Projenizin ayrıntılarını görüntüleyin.  Bu sekmede şunları yapabilirsiniz:

* Proje ayrıntılarını ve giriş veri kümelerini görüntüleme
* Artımlı yenilemeyi etkinleştir
* Projenizde etiketlenmiş çıkışları depolamak için kullanılan depolama kapsayıcısının ayrıntılarını görüntüleme
* Projenize Etiketler ekleyin
* Etiketlerinize verdiğiniz yönergeleri düzenleyin
* Enable/Disable dahil olmak üzere ML yardımlı etiketlemenin ayrıntılarını düzenleme

### <a name="access-for-labelers"></a>Etiketleyiciler için erişim

Çalışma alanınıza erişimi olan herkes projenizdeki verileri etiketleyebilir.  Etiketleyiciler için izinleri, çalışma alanının veya etiketleme projenizin diğer bölümlerine yalnızca etiketlemeye erişebilecek şekilde özelleştirebilirsiniz.  Daha fazla ayrıntı için bkz. [Azure Machine Learning çalışma alanına erişimi yönetme](how-to-assign-roles.md)ve [Labeler özel rolünü](how-to-assign-roles.md#labeler)oluşturmayı öğrenme.

## <a name="add-new-label-class-to-a-project"></a>Projeye yeni Label sınıfı ekleme

Veri etiketleme işlemi sırasında, görüntülerinizi sınıflandırmak için ek etiketlerin gerekli olduğunu fark edebilirsiniz.  Örneğin, kafa karıştırıcı görüntüleri göstermek için bir "Bilinmeyen" veya "diğer" etiketi eklemek isteyebilirsiniz.

Bir projeye bir veya daha fazla etiket eklemek için bu adımları kullanın:

1. Ana **veri etiketleme** sayfasında projeyi seçin.
1. Sayfanın sağ üst köşesinde, etiketleyicilerinin etkinliklerinden durdurulması için **çalışmayı** **duraklatıldı** olarak değiştirin.
1. **Ayrıntılar** sekmesini seçin.
1. Sol taraftaki listede **etiket sınıfları**' nı seçin.
1. Listenin üst kısmında **+** etiket ekle ![ etiket ekle ' yi seçin](media/how-to-create-labeling-projects/add-label.png)
1. Formunda, yeni etiketinizi ekleyin ve nasıl devam etmek istediğinizi seçin.  Bir görüntü için kullanılabilir etiketleri değiştirdiğiniz için, zaten etiketlenmiş verilerin nasıl davranalınacağını seçersiniz:
    * Tüm mevcut etiketleri kaldırarak baştan başlayın.  Yeni tam Etiketler kümesiyle başlayarak etiketlemeye başlamak istiyorsanız bu seçeneği belirleyin. 
    * Tüm mevcut etiketleri koruyarak, baştan başlayın.  Tüm verileri etiketsiz olarak işaretlemek için bu seçeneği belirleyin, ancak önceden etiketlenmiş görüntüler için mevcut etiketleri varsayılan etiket olarak tutun.
    * Tüm mevcut etiketleri koruyarak devam edin. Tüm verilerin zaten olduğu şekilde etiketlendiği ve henüz etiketsiz veriler için yeni etiketi kullanmaya başlamış olan bu seçeneği belirleyin.
1. Yönergeler sayfanızı yeni etiketler için gereken şekilde değiştirin.
1. Tüm yeni etiketleri ekledikten sonra, sayfanın sağ üst köşesinde, projeyi yeniden **başlatmak Için** **duraklatıldıktan sonra duraklatıldı** .  

## <a name="export-the-labels"></a>Etiketleri dışarı aktarma

Machine Learning deneme için etiket verilerini dilediğiniz zaman dışarı aktarabilirsiniz. Resim etiketleri [Coco formatında](http://cocodataset.org/#format-data) veya [etiketli bir Azure Machine Learning veri kümesi](how-to-use-labeled-dataset.md)olarak aktarılabilir. Etiketleme projenizin **proje ayrıntıları** sayfasında **dışarı aktar** düğmesini kullanın.

COCO dosyası, Azure Machine Learning çalışma alanının varsayılan Blob deposunda *dışarı aktarma/Coco* içindeki bir klasörde oluşturulur. Machine Learning **veri kümeleri** bölümünde, dışarıya aktarılmış Azure Machine Learning veri kümesine erişebilirsiniz. Veri kümesi ayrıntıları sayfası, Python 'daki etiketlerinize erişmek için örnek kod de sağlar.

![Aktarılmış veri kümesi](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Sorun giderme

Bu sorunlardan herhangi birini görürseniz, bu ipuçlarını kullanın.

|Sorun  |Çözüm  |
|---------|---------|
|Yalnızca blob veri depolarında oluşturulan veri kümeleri kullanılabilir.     |  Bu, geçerli sürümün bilinen bir sınırlamasıdır.       |
|Oluşturulduktan sonra proje, uzun süredir "başlatılıyor" olarak gösterilir.     | Sayfayı el ile yenileyin. Başlatma, saniyede yaklaşık 20 veri noktasında devam etmelidir. Bu, bilinen bir sorundur.         |
|Görüntüleri gözden geçirirken yeni etiketlenmiş görüntüler gösterilmez.     |   Etiketlenmiş tüm görüntüleri yüklemek için **ilk** düğmeyi seçin. **İlk** düğme, listenin önüne geri götürür, ancak etiketlenmiş tüm verileri yükler.      |
|Nesne algılama için etiketleme sırasında Esc tuşuna basmak, sol üst köşede Sıfır boyutlu bir etiket oluşturur. Etiketlerin bu durumda gönderilmesi başarısız oluyor.     |   Yanındaki çapraz işaretine tıklayarak etiketi silin.  |
|Belirli bir etiketleyici görev kümesi atanamıyor.     |   Bu, geçerli sürümün bilinen bir sınırlamasıdır.  |

## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: ilk görüntü sınıflandırma etiketleme projenizi oluşturun](tutorial-labeling.md).
* [Resim sınıflandırması veya nesne algılaması](how-to-label-images.md) için etiket resimleri
* [Azure Machine Learning ve Machine Learning Studio (klasik)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) hakkında daha fazla bilgi edinin
