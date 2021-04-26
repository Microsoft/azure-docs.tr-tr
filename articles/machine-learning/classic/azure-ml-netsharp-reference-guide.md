---
title: 'ML Studio (klasik): net # özel sinir Networks-Azure'
description: 'NET # sinir Networks belirtim dili için sözdizimi Kılavuzu. Azure Machine Learning Studio (klasik) içinde özel sinir ağ modelleri oluşturmayı öğrenin.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: 5137b633f66088efbee41b96ba715eb3b18961dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519261"
---
# <a name="guide-to-net-neural-network-specification-language-for-machine-learning-studio-classic"></a>Machine Learning Studio için net # sinir ağ belirtim diline kılavuzluk (klasik)

**Uygulama hedefi:** ![ İçin geçerlidir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ için geçerlidir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

NET #, Microsoft tarafından geliştirilen ve derin sinir ağları ya da rastgele boyutlardaki evler gibi karmaşık sinir ağ mimarilerini tanımlamak için kullanılan bir dildir. Görüntü, video veya ses gibi verilerin öğrenmesini geliştirmek için karmaşık yapıları kullanabilirsiniz.

Şu bağlamlarda net # Architecture belirtimini kullanabilirsiniz:

+ Microsoft Azure Machine Learning Studio (klasik) içindeki tüm sinir ağ modülleri: [birden çok Lass sinir ağı](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [Iki sınıflı sinir ağı](/azure/machine-learning/studio-module-reference/two-class-neural-network)ve [sinir ağ gerileme](/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Microsoft ML Server 'de sinir ağ işlevleri: R dili için [Neuralnet](/machine-learning-server/r-reference/microsoftml/neuralnet) ve [Rxneuralnet](/machine-learning-server/r-reference/microsoftml/rxneuralnet)ve Python için [rx_neural_network](/machine-learning-server/python-reference/microsoftml/rx-neural-network) .


Bu makalede, net # kullanarak özel bir sinir ağı geliştirmek için gereken temel kavramlar ve söz dizimi açıklanmaktadır:

+ Sinir ağ gereksinimleri ve birincil bileşenlerin nasıl tanımlanacağı
+ NET # belirtim dilinin söz dizimi ve anahtar sözcükleri
+ NET kullanılarak oluşturulan özel sinir Networks örnekleri #



## <a name="neural-network-basics"></a>Sinir ağ temelleri

Bir sinir ağ yapısı, katmanlarda düzenlenmiş düğümlerden oluşur ve düğümler arasında bağlantıları (veya kenarları) ağırlıklı olarak kapsar. Bağlantılar yönlüdür ve her bağlantının bir kaynak düğümü ve bir hedef düğümü vardır.

Her bir veya daha fazla **bağlantı paketi**(gizli veya çıkış katmanı), bir veya daha fazla bağlantı paketinde bulunur. Bir bağlantı paketi, kaynak katmandan ve bu kaynak katmandaki bağlantılardan oluşan bir belirtiden oluşur. Belirli bir paketteki tüm bağlantılar kaynak ve hedef katmanları paylaşır. NET # öğesinde, bir bağlantı paketi, paketin hedef katmanına ait olduğu kabul edilir.

NET #, girişlerin gizli katmanlarla eşlenme ve çıkışlarla eşlenme şeklini özelleştirmenize olanak sağlayan çeşitli türlerde bağlantı paketlerini destekler.

Varsayılan veya standart paket, kaynak katmandaki her bir düğümün hedef katmandaki her düğüme bağlandığı **tam bir paket** olur.

Ayrıca, net # aşağıdaki dört gelişmiş bağlantı paketi türünü destekler:

+ **Filtrelenmiş** paket. Kaynak katman düğümünün ve hedef katman düğümünün konumlarını kullanarak bir koşul tanımlayabilirsiniz. Her koşul doğru olduğunda düğümler bağlanır.

+ **Evsel** paket. Kaynak katmanda küçük neighborhoods düğümleri tanımlayabilirsiniz. Hedef katmandaki her düğüm, kaynak katmandaki düğümlerin bir komşuları ile bağlanır.

+ **Havuzlama paketleri** ve **Yanıt normalleştirme paketleri**. Bunlar, kullanıcının kaynak katmanda küçük neighborhoods düğümleri tanımladığından, bu, evsel paketlerde benzerdir. Aradaki fark, bu paketlardaki kenarlarının ağırlıklarıyla karşılaşmaz. Bunun yerine, hedef düğüm değerini belirleyebilmek için kaynak düğüm değerlerine önceden tanımlanmış bir işlev uygulanır.


## <a name="supported-customizations"></a>Desteklenen özelleştirmeler

Azure Machine Learning Studio (klasik) içinde oluşturduğunuz sinir ağ modellerinin mimarisi, NET # kullanılarak kapsamlı bir şekilde özelleştirilebilir. Seçenekleriniz şunlardır:

+ Gizli katmanlar oluşturun ve her katmandaki düğümlerin sayısını denetleyin.
+ Katmanların birbirlerine nasıl bağlandığını belirtin.
+ Evler ve ağırlık paylaşım paketleri gibi özel bağlantı yapılarını tanımlayın.
+ Farklı etkinleştirme işlevleri belirtin.

Belirtim dili sözdiziminin ayrıntıları için bkz. [Yapı belirtimi](#structure-specifications).

Ortak makine öğrenimi görevleri için sinir ağlarını tanımlama örnekleri için, simpleks 'ten karmaşık olarak, [örneklere](#examples-of-net-usage)bakın.

## <a name="general-requirements"></a>Genel gereksinimler

+ Tam olarak bir çıkış katmanı, en az bir giriş katmanı ve sıfır veya daha fazla gizli katman olmalıdır.
+ Her katmanın, kavramsal olarak rastgele boyutlarda dikdörtgen bir dizide düzenlenmiş sabit sayıda düğümü vardır.
+ Giriş katmanlarında ilişkili eğitimli parametreler yoktur ve örnek verilerinin ağa girdiği noktayı temsil eder.
+ Karmaşık Katmanlar (gizli ve çıkış katmanları) ağırlık ve Kaynakça olarak bilinen ilişkili eğitimli parametrelere sahiptir.
+ Kaynak ve hedef düğümlerin ayrı katmanlarda olması gerekir.
+ Bağlantılar Çevrimsiz olmalıdır; diğer bir deyişle, ilk kaynak düğümüne arka planda bir bağlantı zinciri olamaz.
+ Çıkış katmanı, bir bağlantı paketinin kaynak katmanı olamaz.

## <a name="structure-specifications"></a>Yapı belirtimleri

Sinir ağ yapısı belirtimi üç bölümden oluşur: **sabit bildirim**, **Katman bildirimi**, **bağlantı bildirimi**. Ayrıca, isteğe bağlı bir **Share bildirimi** bölümü de vardır. Bölümler herhangi bir sırada belirlenebilir.

## <a name="constant-declaration"></a>Sabit bildirimi

Sabit bir bildirim isteğe bağlıdır. Sinir ağ tanımında başka bir yerde kullanılan değerleri tanımlamak için bir yol sağlar. Bildirim deyimi, ardından eşittir işareti ve değer ifadesi gelen bir tanımlayıcıdan oluşur.

Örneğin, aşağıdaki ifade bir sabiti tanımlar `x` :

`Const X = 28;`

İki veya daha fazla sabiti aynı anda tanımlamak için, tanımlayıcı adlarını ve değerlerini küme ayraçları içine alın ve noktalı virgül kullanarak ayırın. Örnek:

`Const { X = 28; Y = 4; }`

Her atama ifadesinin sağ tarafı bir tamsayı, gerçek sayı, Boole değeri (true veya false) veya matematik ifadesi olabilir. Örnek:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Katman bildirimi

Katman bildirimi gereklidir. Bu, bağlantı paketleri ve öznitelikleri dahil olmak üzere katmanın boyutunu ve kaynağını tanımlar. Bildirim bildirimi, katmanın (girdi, gizli veya çıktı) adı ve ardından katmanın boyutları (pozitif tamsayılar kümesi) ile başlar. Örnek:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Boyutların çarpımı, katmandaki düğümlerin sayısıdır. Bu örnekte, iki boyut olan [5, 20] vardır. Bu, katmanda 100 düğüm olduğu anlamına gelir.
+ Katmanlar herhangi bir sırada, tek bir istisna ile bildirilebilecek: birden fazla giriş katmanı tanımlanmışsa, bunların bildirildiği sıra giriş verilerinde özelliklerin sırasıyla aynı olmalıdır.

Bir katmandaki düğümlerin sayısının otomatik olarak belirlendiğini belirtmek için `auto` anahtar sözcüğünü kullanın. `auto`Anahtar sözcüğü katmana bağlı olarak farklı etkilere sahiptir:

+ Giriş katmanı bildiriminde, düğüm sayısı girdi verilerinde özelliklerin sayısıdır.
+ Gizli katman bildiriminde, düğüm sayısı, **gizli düğümlerin sayısı** için parametre değeri tarafından belirtilen sayıdır.
+ Çıkış katmanı bildiriminde, düğüm sayısı iki sınıf sınıflandırması için 2, regresyon için 1 ve çok Lass sınıflandırması için çıkış düğümü sayısına eşittir.

Örneğin, aşağıdaki ağ tanımı tüm katmanların boyutunun otomatik olarak belirlenmesine izin verir:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Gizli olmayan bir katmana yönelik katman bildirimi (gizli veya çıkış katmanları) isteğe bağlı olarak çıkış işlevini (bir etkinleştirme işlevi olarak da bilinir), sınıflandırma modelleri için varsayılan olarak **sigmoıd** ve regresyon modelleri için **Doğrusal** olarak içerebilir. Varsayılan değer kullanıyor olsanız bile, etkinleştirme işlevini açık bir şekilde isterseniz, açıkça belirtebilirsiniz.

Aşağıdaki çıkış işlevleri desteklenir:

+ sigmoıd
+ Doğrusal
+ Yazılım en fazla
+ rdoğrusal
+ kare
+ k
+ srdoğrusal
+ mutlak
+ tanh
+ brdoğrusal

Örneğin, aşağıdaki bildirim **SOFTMAX** işlevini kullanır:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Bağlantı bildirimi

Daha önce, bir katmanı tanımladıktan hemen sonra, tanımladığınız katmanlar arasında bağlantı bildirmeniz gerekir. Bağlantı paketi bildirimi, anahtar sözcüğüyle başlar `from` ve sonra paketin kaynak katmanının adı ve oluşturulacak bağlantı paketi türüdür.

Şu anda, beş tür bağlantı demeti desteklenir:

+ Anahtar sözcüğü ile gösterilen **tam** demeti`all`
+  Anahtar sözcüğü ve `where` ardından bir koşul Ifadesi tarafından belirtilen filtrelenmiş paket
+ Anahtar sözcüğü ve ardından evme öznitelikleri tarafından belirtilen **evsel** paketleri `convolve`
+ Anahtar sözcük **en fazla havuz** veya **Ortalama havuz** tarafından belirtilen **Havuz** paketleri
+ Anahtar sözcük **Yanıt norm** tarafından belirtilen **Yanıt normalleştirme** paketleri

## <a name="full-bundles"></a>Tam demeti

Tam bir bağlantı paketi, kaynak katmandaki her düğümden hedef katmandaki her düğüme bir bağlantı içerir. Bu, varsayılan ağ bağlantı türüdür.

## <a name="filtered-bundles"></a>Filtrelenmiş demeti

Filtrelenmiş bir bağlantı paketi belirtimi, C# lambda ifadesine benzer şekilde, sözdizimi ifade edilen bir koşul içerir. Aşağıdaki örnek, iki filtrelenmiş paketi tanımlar:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ İçin koşulunda, `ByRow` `s` giriş katmanının düğüm dizisinin dikdörtgen dizisine bir dizin temsil eden bir parametredir, `Pixels` ve `d` gizli katmanın düğüm dizisine bir dizin temsil eden bir parametredir `ByRow` . Hem hem de türü `s` , `d` iki uzunluğunda tamsayıların bir tanımlama dizisidir. Kavramsal olarak, ve ile tüm `s` tamsayı çiftleri üzerinde aralıklar ve `0 <= s[0] < 10` `0 <= s[1] < 20` `d` ve ile tüm tamsayı çiftleri üzerinde aralıklar `0 <= d[0] < 10` `0 <= d[1] < 12` .

+ Koşul ifadesinin sağ tarafında bir koşul vardır. Bu örnekte, her değeri için `s` ve `d` koşulun true olması gibi, kaynak katman düğümünden hedef katman düğümüne bir kenar vardır. Bu nedenle, bu filtre ifadesi, paketin tarafından tanımlanan düğümden, `s` `d` s [0] değerinin d [0] değerine eşit olduğu her durumda tarafından tanımlanan düğüme bir bağlantı içerdiğini belirtir.

İsteğe bağlı olarak, filtrelenmiş bir paket için bir ağırlık kümesi belirtebilirsiniz. **Ağırlıklar** özniteliğinin değeri, paket tarafından tanımlanan bağlantı sayısıyla eşleşen bir uzunluğu olan kayan nokta değerlerinin bir grubu olmalıdır. Varsayılan olarak ağırlıklar rastgele oluşturulur.

Ağırlık değerleri, hedef düğüm dizinine göre gruplandırılır. Diğer bir deyişle, ilk hedef düğümü K kaynak düğümlerine bağlandıysa, `K` **ağırlıkların** kayıt düzeninin ilk öğeleri, kaynak dizin sırası ' nda ilk hedef düğümün ağırlıklardır. Aynı, kalan hedef düğümler için de geçerlidir.

Ağırlıkları doğrudan sabit değerler olarak belirtmek mümkündür. Örneğin, daha önce ağırlıkları öğrendiyseniz, bu söz dizimini kullanarak bunları sabitler olarak belirtebilirsiniz:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Evsel demeti

Eğitim verileri homojen bir yapıya sahip olduğunda, verilerin üst düzey özelliklerini öğrenmek için, evsel bağlantılar yaygın olarak kullanılır. Örneğin, görüntü, ses veya video verileri, uzamsal veya zamana bağlı boyutlılık oldukça Tekdüzen olabilir.

Evsel paketler, Boyutlar aracılığıyla ince olan dikdörtgen **çekirdekler** için kullanılır. Temelde, her çekirdek, **çekirdek uygulamalar** olarak adlandırılan yerel neighborhoods içinde uygulanan bir ağırlık kümesi tanımlar. Her çekirdek uygulama, **Merkezi düğüm** olarak adlandırılan kaynak katmandaki bir düğüme karşılık gelir. Bir çekirdeğin ağırlıkları birçok bağlantı arasında paylaşılır. Bir evsel pakette, her çekirdek dikdörtgen ve tüm çekirdek uygulamalar aynı boyutlardır.

Evsel paketlerde aşağıdaki öznitelikler desteklenir:

**Inputshape** , bu evsel paketin amaçları doğrultusunda kaynak katmanın boyutlılık düzeyini tanımlar. Değer pozitif tamsayılar grubu olmalıdır. Tamsayıların çarpımı kaynak katmandaki düğüm sayısına eşit olmalıdır, ancak Aksi takdirde, kaynak katman için belirtilen boyutalya uyması gerekmez. Bu kayıt düzeninin uzunluğu, evsel paket için **parametre sayısı** değeri olur. Genellikle parametre sayısı, bir işlevin götürebileceğiniz bağımsız değişken veya işlenen sayısını ifade eder.

Kernels 'in şeklini ve konumlarını tanımlamak için, **Kernelshape**, ilerme, **doldurma**, **küçük** **panel** ve en büyük **panel** özniteliklerini kullanın:

+ **Kernelshape**: (gerekli), her bir çekirdeğin, evsel paket için boyutlılık sayısını tanımlar. Değer, bir uzunluğu paketin parametre sayısına eşit olan pozitif tamsayılar grubu olmalıdır. Bu kayıt düzeninin her bileşeni, **ınputshape**'in karşılık gelen bileşenlerinden daha büyük olmamalıdır.

+ **İlerleme**: (isteğe bağlı), merkezi düğümler arasındaki mesafe olan evlenme (her boyut için bir adım boyutunda) kayan adım boyutlarını tanımlar. Değer, bir uzunluğun parametre sayısı olan pozitif tamsayılar kümesi olmalıdır. Bu kayıt düzeninin her bileşeni, karşılık gelen **Kernelshape** bileşenlerinden daha büyük olmamalıdır. Varsayılan değer, tüm bileşenleri birine eşit olan bir tanımlama grubu olur.

+ **Paylaşma**: (isteğe bağlı) her bir evlenme boyutu için ağırlık paylaşımını tanımlar. Değer, tek bir Boole değeri veya paket parametre sayısı olan bir Boolean değer kümesi olabilir. Tek bir Boole değeri, tüm bileşenleri belirtilen değere eşit olan doğru uzunlukta bir tanımlama grubu olacak şekilde genişletilir. Varsayılan değer, tüm doğru değerlerden oluşan bir tanımlama grubu olur.

+ **Mapcount**: (isteğe bağlı), evsel paket için özellik haritaları sayısını tanımlar. Değer, tek bir pozitif tamsayı veya paket parametre sayısı olan pozitif tamsayılar kümesi olabilir. Tek bir tamsayı değeri, belirtilen değere ve kalan tüm bileşenlere eşit olan ilk bileşenlere sahip doğru uzunlukta bir tanımlama grubu olacak şekilde genişletilir. Varsayılan değer bir değeridir. Özellik eşlemelerinin toplam sayısı, kayıt düzeni bileşenlerinin ürünüdür. Bileşenlerin tamamında bu toplam sayının düzenleme, özellik eşleme değerlerinin hedef düğümlerde nasıl gruplandığını belirler.

+ **Ağırlıklar**: (isteğe bağlı) paket için başlangıç ağırlıklarını tanımlar. Değer, bu makalede daha sonra tanımlandığı gibi, çekirdek başına düşen çekirdekler sayısı kadar olan kayan nokta değerlerinin bir kümesi olmalıdır. Varsayılan ağırlıklar rastgele oluşturulur.

Doldurmayı denetleyen iki özellik kümesi vardır: birbirini dışlayan Özellikler:

+ **Doldurma**: (isteğe bağlı) girişin **varsayılan bir doldurma şeması** kullanılarak doldurulmuş olup olmayacağını belirler. Değer tek bir Boole değeri olabilir veya bir Boolean değer kümesi olabilir ve bu değer, paketin parametre sayısı olan bir uzunluğa sahip olabilir.

    Tek bir Boole değeri, tüm bileşenleri belirtilen değere eşit olan doğru uzunlukta bir tanımlama grubu olacak şekilde genişletilir.

    Bir boyutun değeri doğruysa, kaynak katmanda bu boyuttaki ilk ve son kerinin merkezi düğümleri, kaynak katmandaki bu boyuttaki ilk ve son düğümler olacak şekilde, kaynak, ek çekirdek uygulamalarını desteklemek üzere sıfır değerli hücrelerle mantıksal olarak doldurulur. Bu nedenle, her boyuttaki "kukla" düğümlerin sayısı, doldurulmuş kaynak katmana tam olarak kerklere sığacak şekilde otomatik olarak belirlenir `(InputShape[d] - 1) / Stride[d] + 1` .

    Bir boyutun değeri false ise, çekirdekler, bırakılan her bir taraftaki düğümlerin sayısı aynı (1 ' e kadar bir farka kadar) olacak şekilde tanımlanır. Bu özniteliğin varsayılan değeri, tüm bileşenleri false değerine eşit olan bir tanımlama grubu olur.

+ **Üsteli panel** ve küçük **panel**: (isteğe bağlı) kullanılacak doldurma miktarı üzerinde daha fazla denetim sağlar. **Önemli:** Bu öznitelikler ve yalnızca yukarıdaki **doldurma** özelliği ***_ tanımlanmazsa tanımlanabilir. Değerler, paket parametre sayısı olan uzunluklara sahip tamsayı değerli diziler olmalıdır. Bu öznitelikler belirtildiğinde, giriş katmanının her boyutunun alt ve üst uçlarına "kukla" düğümler eklenir. Her boyuttaki alt ve üst uçlarına eklenen düğümlerin sayısı _* küçük panel**[i] ve en büyük **panel**[i] tarafından belirlenir.

    Çekirdekler 'in yalnızca "gerçek" düğümlere karşılık geldiğinden ve "kukla" düğümlere karşılık gelmesini sağlamak için aşağıdaki koşulların karşılanması gerekir:
  - Küçük **panel** bileşeninin her bileşeni, öğesinden kesinlikle küçük olmalıdır `KernelShape[d]/2` .
  - Üstün her bileşeni, ' den **büyük olmamalıdır** `KernelShape[d]/2` .
  - Bu özniteliklerin varsayılan değeri, tüm bileşenleri 0 ' a eşit olan bir tanımlama grubu olur.

    **Padding** = true ayarı, çekirdeğin "Center" değerini "Real" girişinin içinde tutmak için gereken kadar doldurma sağlar. Bu, çıktı boyutunu bilgi işlem için matematik bir bit olarak değiştirir. Genellikle, çıkış boyutu *D* olarak hesaplanır `D = (I - K) / S + 1` , burada `I` giriş boyutudur, `K` çekirdek boyutudur, `S` ilerdir ve `/` tamsayı bölümü olur (sıfıra yuvarlar). Üstepad = [1, 1] ayarlarsanız, giriş boyutu `I` etkin olarak 29 ve bu nedenle `D = (29 - 5) / 2 + 1 = 13` . Ancak, **Padding** = true olduğunda, temelde `I` tarafından tamponlanır `K - 1` ; Bu nedenle `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14` . Üstpanel ve küçük  **panel** değerlerini belirterek, doldurma üzerinde yalnızca **Padding** = true değerini ayarlamış kadar daha fazla denetim sahibi olursunuz.

Evsel ağlar ve uygulamaları hakkında daha fazla bilgi için şu makalelere bakın:

+ [http://d2l.ai/chapter_convolutional-neural-networks/lenet.html ](http://d2l.ai/chapter_convolutional-neural-networks/lenet.html )
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Havuz demeti

Bir **havuzlama paketi** , temel bağlantıya benzer bir geometri uygular, ancak hedef düğüm değerini türetmek için önceden tanımlanmış işlevler kaynak düğüm değerlerini kullanır. Bu nedenle, Havuzlama paketleri hiç bir duruma sahip değildir (ağırlık veya biases). Havuzlama paketleri **Paylaşım**, **Mapcount** ve **ağırlıklar** hariç tüm evsel öznitelikleri destekler.

Genellikle, bitişik havuz birimlerine göre özetlenen çekirdekler çakışmaz. Her boyutta [d] Ilerlemesiyle, her boyuttaki KernelShape [d] değerine eşitse, elde edilen katman, genellikle evsel sinir ağlarında kullanılan geleneksel yerel havuz katmanıdır. Her hedef düğüm, kaynak katmandaki çekirdeğin etkinliklerinin en büyük veya ortalama sayısını hesaplar.

Aşağıdaki örnekte bir havuz paketi gösterilmektedir:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Paket parametre sayısı 3 ' dir: diğer bir deyişle, ve tanımlama gruplarının uzunluğu, `InputShape` `KernelShape` ve `Stride` .
+ Kaynak katmandaki düğümlerin sayısı `5 * 24 * 24 = 2880` .
+ Bu, geleneksel bir yerel havuz katmanıdır çünkü **Kernelshape** ve bir **adım** eşit.
+ Hedef katmandaki düğümlerin sayısı `5 * 12 * 12 = 1440` .

Havuz oluşturma katmanları hakkında daha fazla bilgi için şu makalelere bakın:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Bölüm 3,4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Yanıt normalleştirme demeti

**Yanıt normalleştirme** , [derin evsel sinir ağlarla kağıt Imagenet sınıflandırmasında](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)Ilk olarak Geoffey Hinton, et al tarafından tanıtılan yerel bir normalleştirme düzenidir.

Yanıt normalleştirme, sinir ağlarında genelleştirmeye yardımcı olmak için kullanılır. Bir neuron çok yüksek etkinleştirme düzeyinde başlatıldığında, yerel bir yanıt normalleştirme katmanı, çevreleyen eklentilerin etkinleştirme düzeyini bastırır. Bu, üç parametre ( `α` , `β` , ve `k` ) ve bir evsel yapı (veya komşu şekli) kullanılarak yapılır. Hedef katman **y** içindeki her neuron kaynak katmandaki bir neuron **x** 'e karşılık gelir. **Y** etkinleştirme düzeyi aşağıdaki formül tarafından verilir, burada `f` bir neuron öğesinin etkinleştirme düzeyidir ve `Nx` çekirdek (ya da **x**'in komşuları içindeki neurons 'yi içeren küme) aşağıdaki alt yapı tarafından tanımlandığı şekilde belirlenir:

![Evsel yapı için formül](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Yanıt normalleştirme paketleri, **Paylaşım**, **Mapcount** ve **ağırlıklar** hariç tüm evsel öznitelikleri destekler.

+ Çekirdek, ***x** _ ile aynı haritada neurlanlar içeriyorsa, normalleştirme şeması _ * aynı eşleme normalleştirme * * olarak adlandırılır. Aynı eşleme normalleştirmesini tanımlamak için **ınputshape** içindeki ilk koordinat 1 değerini içermelidir.

+ Çekirdek, ***x** _ ile aynı uzamsal konumda neurlanlar içeriyorsa, ancak bu haritalar diğer Eşlemlerde ise, normalleştirme şeması, haritalar normalleştirmesi * * üzerinde _ * olarak adlandırılır. Bu tür bir yanıt normalleştirme, gerçek olarak bulunan tür tarafından ilham olarak bulunan, büyük etkinleştirme düzeyleri için, farklı haritalar üzerinde hesaplanan neuron çıkışları arasında rekabet oluşturma gibi bir yan yana engelleme biçimi uygular. Haritalar normalleştirmesini belirlemek için ilk koordinat, bir tamsayı ve eşleme sayısından büyük bir tamsayı olmalıdır ve koordinatların geri kalanı 1 değerini içermelidir.

Yanıt normalleştirme paketleri, hedef düğüm değerini belirlemede önceden tanımlanmış bir işlevi kaynak düğüm değerlerine uygulayarak, hiçbir zaman kaybı yoktur (ağırlık veya Bialar).

> [!NOTE]
> Hedef katmandaki düğümler, kernels 'in merkezi düğümleri olan neurlanlar 'a karşılık gelir. Örneğin, `KernelShape[d]` tek ise, `KernelShape[d]/2` Merkezi çekirdek düğümüne karşılık gelir. `KernelShape[d]`Bile, merkezi düğüm de olur `KernelShape[d]/2 - 1` . Bu nedenle, `Padding[d]` false ise, ilk ve son `KernelShape[d]/2` düğümlerin hedef katmanda karşılık gelen düğümleri yoktur. Bu durumdan kaçınmak için, **doldurmayı** [true, true,..., true] olarak tanımlayın.

Daha önce açıklanan dört özniteliğe ek olarak, yanıt normalleştirme paketleri de aşağıdaki öznitelikleri destekler:

+ **Alfa**: (gerekli) önceki formülde karşılık gelen bir kayan nokta değeri belirtir `α` .
+ **Beta**: (gerekli) önceki formülde karşılık gelen bir kayan nokta değeri belirtir `β` .
+ **Kaydırma**: (isteğe bağlı) önceki formülde karşılık gelen kayan nokta değerini belirtir `k` . Varsayılan olarak 1 ' dir.

Aşağıdaki örnek, bu öznitelikleri kullanarak bir yanıt normalleştirme paketi tanımlar:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ Kaynak katmanda, her biri 12x12 boyutunda bir, 1440 düğümünde toplam olan beş harita bulunur.
+ **Kernelshape** değeri, bu, komşun bir 3x3 dikdörtgen olduğu bir harita normalleştirme katmanı olduğunu gösterir.
+ Varsayılan **doldurma** değeri false ' tur, bu nedenle hedef katmanın her boyutta yalnızca 10 düğümü vardır. Kaynak katmandaki her düğüme karşılık gelen hedef katmana bir düğüm eklemek için, Padding = [true, true, true]; ve RN1 boyutunu [5, 12, 12] olarak değiştirin.

## <a name="share-declaration"></a>Share bildirimi

NET # isteğe bağlı olarak, paylaşılan ağırlıkları olan birden çok paket tanımlamayı destekler. Yapıları aynı ise, iki paketin ağırlıkları paylaşılabilir. Aşağıdaki sözdizimi, paylaşılan ağırlıkları olan paketleri tanımlar:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

Örneğin, aşağıdaki paylaşım bildirimi, katman adlarını belirtir; Bu, her iki ağırlığın ve bialerin paylaşılması gerektiğini gösterir:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ Giriş özellikleri iki eşit ölçekli giriş katmanına bölümlenir.
+ Gizli katmanlar daha sonra iki giriş katmanında daha yüksek düzey özellikleri hesaplar.
+ Share-bildirimi, *H1* ve *H2* 'ın ilgili girişlerinden aynı şekilde hesaplanması gerektiğini belirtir.

Alternatif olarak, bu, aşağıdaki gibi iki ayrı Share-bildirimi ile de belirtilebilir:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Kısa formu yalnızca katmanlar tek bir paket içerdiğinde kullanabilirsiniz. Genel olarak, yalnızca ilgili yapı özdeş olduğunda, ancak aynı boyuta sahip oldukları, aynı ana geometri ve benzeri bir şekilde paylaşım yapılabilir.

## <a name="examples-of-net-usage"></a>NET # Usage örnekleri

Bu bölümde, gizli katmanları eklemek için net # ' ı nasıl kullanabileceğinizi gösteren bazı örnekler verilmektedir, Gizli katmanların diğer katmanlarla etkileşim kurma biçimini tanımlayabilir ve evsel ağlar oluşturabilirsiniz.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Basit bir özel sinir ağı tanımlayın: "Merhaba Dünya" örneği

Bu basit örnek, tek bir gizli katman içeren bir sinir ağ modelinin nasıl oluşturulacağını gösterir.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

Örnek aşağıda gösterildiği gibi bazı temel komutları göstermektedir:

+ İlk satır giriş katmanını (adlandırılmış `Data` ) tanımlar. `auto`Anahtar sözcüğünü kullandığınızda, sinir ağı giriş örneklerinde tüm özellik sütunlarını otomatik olarak ekler.
+ İkinci satır gizli katmanı oluşturur. Ad, `H` 200 düğümü bulunan gizli katmana atanır. Bu katman, giriş katmanına tam olarak bağlıdır.
+ Üçüncü satır `Out` , 10 çıkış düğümü içeren çıkış katmanını (adlandırılmış) tanımlar. Sınıflandırma için sinir ağı kullanılıyorsa, sınıf başına bir çıkış düğümü vardır. **Sigmoıd** anahtar sözcüğü çıkış işlevinin çıkış katmanına uygulandığını gösterir.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Birden çok gizli katman tanımlayın: görüntü görme örneği

Aşağıdaki örnek, birden çok özel gizli katman ile biraz daha karmaşık bir sinir ağının nasıl tanımlanacağını göstermektedir.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

Bu örnekte, sinir Networks belirtim dilinin çeşitli özellikleri gösterilmektedir:

+ Yapı iki giriş katmanına sahiptir `Pixels` ve `MetaData` .
+ `Pixels`Katman, hedef katmanları ve ile iki bağlantı paketi için bir kaynak katmanıdır `ByRow` `ByCol` .
+ Katmanlar `Gather` ve `Result` çok sayıda bağlantı paketinde hedef katmanlardır.
+ Çıkış katmanı, `Result` iki bağlantı paketinde bulunan bir hedef katmandır; biri hedef katman olarak ikinci düzey gizli katmana `Gather` ve diğeri de hedef katman olarak giriş katmanına sahip olur `MetaData` .
+ Gizli katmanlar `ByRow` ve `ByCol` , koşul ifadelerini kullanarak filtrelenmiş bağlantı belirtin. Daha kesin olarak, `ByRow` [x, y] konumundaki düğüm, `Pixels` ilk dizin koordinatına, düğümün ilk koordinatına (x) eşit olan düğümlere bağlıdır. Benzer şekilde, `ByCol` [x, y] konumundaki düğüm, `Pixels` düğüm ikinci koordinatı, y içindeki ikinci dizin koordinasyonu olan içindeki düğümlere bağlıdır.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Birden çok Lass sınıflandırması için bir evsel ağ tanımlayın: basamak tanıma örneği

Aşağıdaki ağın tanımı, sayıları tanımak için tasarlanmıştır ve bir sinir ağını özelleştirmek için bazı gelişmiş teknikleri gösterir.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ Yapının tek bir giriş katmanı vardır `Image` .
+ Anahtar sözcüğü, `convolve` ve adlı katmanların `Conv1` `Conv2` evsel katmanlar olduğunu gösterir. Bu katman bildirimlerinin her birine, ardından evme özniteliklerinin bir listesi gelir.
+ NET, `Hid3` ikinci gizli katmana tamamen bağlı olan üçüncü bir gizli katmana sahiptir `Conv2` .
+ Çıkış katmanı, `Digit` yalnızca üçüncü gizli katmana bağlanır `Hid3` . Anahtar sözcüğü, `all` çıktı katmanının tam olarak bağlandığını belirtir `Hid3` .
+ Evin parametre sayısı üç:,, ve tanımlama gruplarının uzunluğu `InputShape` `KernelShape` `Stride` `Sharing` .
+ Çekirdek başına ağırlık sayısı `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26` . Veya `26 * 50 = 1300` .
+ Her gizli katmandaki düğümleri aşağıdaki şekilde hesaplayabilirsiniz:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Toplam düğüm sayısı, [50, 5, 5] katmanının belirtilen boyutlılık kullanılarak hesaplanabilir, örneğin: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ `Sharing[d]`Yalnızca için false olduğundan `d == 0` , çekirdekler sayısı `MapCount * NodeCount\[0] = 10 * 5 = 50` .

## <a name="acknowledgements"></a>Teşekkürler

Sinir Networks mimarisini özelleştirmeye yönelik net # dili, Microsoft 'ta Shon Katzenberger (mimar, Machine Learning) ve Alexey Kamenev (yazılım mühendisi, Microsoft Research) tarafından geliştirilmiştir. Görüntü algılamada metin analizinden değişen makine öğrenimi projeleri ve uygulamaları için dahili olarak kullanılır. Daha fazla bilgi için bkz. [Azure Machine Learning Studio 'Da sinir ağları-net # 'A giriş](/archive/blogs/machinelearning/neural-nets-in-azure-ml-introduction-to-net)