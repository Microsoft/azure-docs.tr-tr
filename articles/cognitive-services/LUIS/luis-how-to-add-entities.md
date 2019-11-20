---
title: Varlık Ekle-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUSıS) uygulamalarındaki Kullanıcı dıklarından anahtar verileri ayıklamak için varlıklar oluşturun. Ayıklanan varlık verileri, istemci uygulama tarafından fullfıbcustomer istekleri için kullanılır.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 7de1a1e24c2863b90fe5f1f3ff19124318912cff
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132679"
---
# <a name="add-entities-to-extract-data"></a>Verileri ayıklamak için varlık ekleme 

Language Understanding (LUSıS) uygulamalarındaki Kullanıcı dıklarından anahtar verileri ayıklamak için varlıklar oluşturun. Ayıklanan varlık verileri, istemci uygulamanız tarafından fullfıbcustomer istekleri için kullanılır.

Varlık, bir sözcük veya tümcecik ayıklanan istediğiniz utterance içinde temsil eder. Bazen uygulamanızın görevini gerçekleştirmek gerekli olan ve bilgi ıntent'e ilgili varlıkları anlatmaktadır. Hedefe bir örnek ekleme veya bir amaca göre örnek ekleme (önce veya sonrası) bir amaca bakış ekleyerek varlıklar oluşturabilirsiniz.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Varlıkları planlayın, ardından Oluştur ve etiketle

Makine tarafından öğrenilen varlıklar, örnek dıklarından oluşturabilir veya **varlıklar** sayfasından oluşturulabilir. 

Genel olarak, portalda makine tarafından öğrenilen bir varlık oluşturmadan önce varlıkları planlama süresini harcamanız en iyi uygulamadır. Daha sonra örnekte, makinenizde öğrendiğiniz şekilde, alt bileşenlere ve tanımlayıcılara ve kısıtlamalara göre daha ayrıntılı bilgi vererek makine tarafından öğrenilen varlığı oluşturun. [Birleştirilebilen varlık öğreticisi](tutorial-machine-learned-entity.md) , bu yöntemin nasıl kullanılacağını gösterir. 

Varlıkları planlamanın bir parçası olarak, metin ile eşleşen varlıklara (önceden oluşturulmuş varlıklar, normal ifade varlıkları veya liste varlıkları gibi) ihtiyacınız olduğunu bilirsiniz. Bunları örnek bir şekilde etiketlendirmeleri için **varlıklar** sayfasından oluşturabilirsiniz. 

Etiketleme sırasında tek tek varlıkları etiketleyebilir ve ardından bir üst makineye öğrenilen varlık oluşturabilirsiniz. Ya da bir üst makine öğrenmiş varlıkla başlayabilir ve alt varlıklarda bir varlık oluşturabilir. 

> [!TIP] 
>Sözcükler, istemci uygulamasında ayıklandığında kullanılmasa bile, bir varlığı gösterebilen tüm kelimeleri etiketleyebilir. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Etiketleme öncesinde veya etiketleyerek bir varlık oluşturma

Her bir varlığın hangi varlıkları oluşturmak veya uygulamaya ekleneceğini anlamak için aşağıdaki tabloyu kullanın. 

|Varlık türü|LUSıS portalında varlık oluşturma|
|--|--|
|Makine tarafından öğrenilen varlık|Varlıklar veya amaç ayrıntısı|
|Liste varlığı|Varlıklar veya amaç ayrıntısı|
|Normal ifade varlığı|Varlıklar|
|Pattern.any varlığı|Varlıklar|
|Önceden oluşturulmuş varlık|Varlıklar|
|Önceden oluşturulmuş etki alanı varlığı|Varlıklar|

**Varlıklar** sayfasından tüm varlıkları oluşturabilir veya **Amaç ayrıntısı** sayfasında, varlığı etiketlemenin bir parçası olarak bir dizi varlık oluşturabilirsiniz. Bir varlığı yalnızca **Amaç ayrıntısı** sayfasından bir örnek olarak _etiketleyebilir_ . 

## <a name="create-a-machine-learned-entity"></a>Makine tarafından öğrenilen bir varlık oluşturma

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Metin ile eşleşen bir varlık oluşturma

Metin eşleştirme varlıklarını kullanma, verileri ayıklamak için çeşitli yollar sağlar:

|Metin eşleştirme varlıkları|Amaç|
|--|--|
|[Varlık listeleme](#add-list-entities-for-exact-matches)|alternatif formlar olarak eş anlamlılarla birlikte kurallı adların listesi|
|Normal ifade varlığı|normal ifade varlığı kullanarak metni Eşleştir|
|[Önceden oluşturulmuş varlık](tutorial-machine-learned-entity.md#add-prebuilt-number-to-app-to-help-extract-data)|sayı, e-posta, tarih gibi ortak veri türlerini Eşleştir|
|Önceden oluşturulmuş etki alanı varlığı|seçili konu etki alanlarını kullanarak Eşleştir|
|[Pattern.Any](#add-a-patternany-entity)| çevreleyen metinle kolayca karışabilme varlıkları eşleştirmek için|  

Önceden oluşturulmuş varlıklar, herhangi bir özel eğitim verisi sağlamadan çalışır. Diğer varlıkların, müşteri eğitim verileri (liste varlığının öğeleri gibi) veya bir ifade (normal ifade veya model gibi) sağlamanız gerekir.

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Yeni bir özel varlık oluşturma

1. LUU portalında **Yönet** bölümüne, ardından **varlıklar** sayfasına gidin. 
1. **+ Oluştur**' u seçin ve varlık türünü seçin. 
1. Varlığı yapılandırmaya devam edin, sonra işiniz bittiğinde **Oluştur** ' u seçin. 

### <a name="add-list-entities-for-exact-matches"></a>Tam eşleşmeler için liste varlıkları ekleme

Liste varlık ilgili sözcükler sabit, kapalı bir kümesini temsil eder. Yazar olarak, yazar olarak listeyi değiştireyken, LUYA listeyi küçültmez veya küçülemez. Ayrıca, bir [list Entity. JSON biçimi (başvuru-varlık-List. MD # örnek-JSON------------------------- 

Aşağıdaki listede kurallı adı ve eş anlamlılar gösterilmektedir. 

|Renk listesi öğe adı|Renk eşanlamlıları|
|--|--|
|Kırmızı|Crimson, kan, Apple, Fire-Engine|
|Mavi|gök, Azure, Cobalt|
|Yeşil|Kelly, limon sarısı|

Bir liste varlığı oluşturmak için yordamını kullanın. Liste varlığı oluşturulduktan sonra, bir amaç için örnek söyleyeni etiketlemenize gerek yoktur. Liste öğeleri ve eş anlamlılar, tam metin kullanılarak eşleştirilir. 

1. **Derleme** bölümünde, sol paneldeki **varlıklar** ' ı seçin ve ardından **+ Oluştur**' u seçin.

1. **Varlık türü oluştur** iletişim kutusunda, varlığın adını girin, örneğin `Colors` ve **liste**seçin.
1. **Liste varlığı oluştur** iletişim kutusunda **Yeni alt liste ekle...** ' da, `Green`gibi bir liste öğesi adı girin ve sonra eşanlamlı ekleyin.

    > [!div class="mx-imgBorder"]
    > ![varlık ayrıntısı sayfasında bir liste varlığı olarak renklerin bir listesini oluşturun.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Liste öğeleri ve eş anlamlılar ekleme işiniz bittiğinde **Oluştur**' u seçin.

    Uygulamada bir değişiklik grubuyla işiniz bittiğinde, uygulamayı **eğiten** unutmayın. Uygulamayı tek bir değişiklikten sonra eğmeyin. 

    > [!NOTE]
    > Bu yordam, **Amaç ayrıntısı** sayfasında bir liste varlığının bir örnek ile oluşturulmasını ve etiketlenmesini gösterir. Aynı varlığı **varlıklar** sayfasından da oluşturabilirsiniz.

## <a name="add-a-role-for-an-entity"></a>Bir varlık için rol ekleme

Rol, bir varlığın bağlam temelinde adlandırılmış bir alt türüdür. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Farklı bağlamları ayırt etmek için rol ekleme

Aşağıdaki söyleyde iki konum vardır ve her biri, `to` ve `from`gibi sözcükler tarafından anlam olarak belirtilmiştir: 

`Pick up the package from Seattle and deliver to New York City.`

Bu yordamda, önceden oluşturulmuş bir geographyV2 varlığına `origin` ve `destination` rolleri ekleyin.

1. Gelen **derleme** bölümünden **varlıkları** sol bölmesinde.

1. **+ Önceden oluşturulmuş varlık Ekle**' yi seçin. **GeographyV2** ' ı seçin ve **bitti**' yi seçin. Bu, uygulamaya önceden oluşturulmuş bir varlık ekler.
    
    Pattern.any içerdiğinde deseninizin varlıkları yanlış ayıkladığını fark ederseniz bu sorunu gidermek için [açık liste](reference-pattern-syntax.md#explicit-lists) kullanın. 

1. **Varlıklar sayfa listesinden** yeni eklenen önceden oluşturulmuş geographyV2 varlığını seçin. 
1. Yeni bir rol eklemek için, **rol eklenmemiş** **+** ileri ' yi seçin.
1. **Rol... TextBox yazın** `Origin` rolün adını girin ve ardından girin. İkinci bir rol adı `Destination` ekleyin ve ardından girin. 

    > [!div class="mx-imgBorder"]
    > Konum varlığına kaynak rolü eklemenin ekran görüntüsünü ![](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Rol önceden oluşturulmuş varlığa eklenir, ancak bu varlık kullanılarak herhangi bir söyleye eklenmez. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Örnekte bir rol ile metin etiketle

1. Rolü kullanan örnek bir parametre olan amaç ayrıntıları sayfasına gidin. 
1. Rol ile etiketlemek için, örnekte varlık etiketini (metin altında Solid Line) seçin ve ardından açılır listeden **varlık paletinde görüntüle** ' yi seçin. 

    > [!div class="mx-imgBorder"]
    > varlık paletinde görünüm seçme ![ekran görüntüsü](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Varlık paleti sağ tarafta açılır. 

1. Varlığı seçin, sonra paletin en altına gidin ve rolü seçin. 

    > [!div class="mx-imgBorder"]
    > varlık paletinde görünüm seçme ![ekran görüntüsü](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Bir model ekleyin. herhangi bir varlık

[Desen. tüm](luis-concept-entity-types.md) varlıklar yalnızca [desenlere](luis-how-to-model-intent-pattern.md)göre geçerlidir, amaç örnekleri değildir. Bu varlık türü, değişken uzunluğu ve sözcük seçimi varlıklarının sonuna Bul LUIS yardımcı olur. Bu varlık içindeki bir desenle kullanıldığından LUIS son varlık utterance şablonda olduğu bilir.

### <a name="steps-to-create-a-patternany-entity"></a>Bir model oluşturma adımları. herhangi bir varlık

1. **Derleme** bölümünde, sol paneldeki **varlıklar** ' ı seçin ve ardından **+ Oluştur**' u seçin.

1. **Varlık türü seç** iletişim kutusunda, **ad** kutusuna varlık adını girin ve ardından model ' i seçin **.** tür olarak **yazın** ve **Oluştur**' u seçin.

    Bu varlığı kullanarak [bir desen oluşturduktan](luis-how-to-model-intent-pattern.md) sonra varlık, birleştirilmiş bir makine tarafından öğrenilen ve metin eşleştirme algoritması ile ayıklanır. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Desen kullanmak için desen şablonu oluşturma. herhangi bir varlık

Pattern.any varlık kullanmak için üzerinde bir desen Ekle **desenleri** sayfasında **uygulama performansını** doğru küme ayracı sözdizimi bölümündeki `Where is **{HumanResourcesFormTitle}** on the server?`.

Pattern.any içerdiğinde deseninizin varlıkları yanlış ayıkladığını fark ederseniz bu sorunu gidermek için [açık liste](reference-pattern-syntax.md#explicit-lists) kullanın. 

## <a name="do-not-change-entity-type"></a>Varlık türünü değiştirme

LUIS, ekleme veya kaldırma, varlık oluşturmak için gerekenler bilmediği varlık türünü değiştirmek izin vermez. Türü değiştirmek için biraz daha farklı bir adla doğru türde yeni bir varlık oluşturmak iyidir. Varlık oluşturulduktan sonra eski etiketli varlık adı her utterance içinde kaldırıp yeni varlık adı ekleyin. Tüm sesleri relabeled sonra eski varlığı silin. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Sonraki adımlar

Desenler hakkında daha fazla bilgi edinin:

* [Desenler kavramları](luis-concept-patterns.md)
* [Desenler sözdizimi](reference-pattern-syntax.md)

Önceden oluşturulmuş varlıklarla ilgili daha fazla bilgi için bkz. [tanıyıcıları metin](https://github.com/Microsoft/Recognizers-Text) proje. 

Varlık JSON uç nokta sorgu yanıtına görüntülenme şeklini hakkında daha fazla bilgi için bkz: [veri ayıklama](luis-concept-data-extraction.md)

Hedefleri ve konuşma varlıklarını ekledikten sonra temel bir LUIS uygulaması sahip. Bilgi nasıl [eğitme](luis-how-to-train.md), [test](luis-interactive-test.md), ve [yayımlama](luis-how-to-publish-app.md) uygulamanızı.
 
