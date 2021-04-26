---
title: PBR malzemeleri
description: PBR malzeme türünü açıklar.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e9908c106e57801cb1b7def8b3353a983cc97de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99591948"
---
# <a name="pbr-materials"></a>PBR malzemeleri

*PBR malzemeleri* , Azure uzaktan işlemede desteklenen [malzeme türlerinden](../../concepts/materials.md) biridir. Gerçekçi aydınlatma alması gereken [kafesler](../../concepts/meshes.md) için kullanılırlar.

PBR, **P** HYS, **B** ased **R** endering için temsil eder ve malzemenin, bir yüzeyin görsel özelliklerini fiziksel bir şekilde, her aydınlatma koşullarında gerçekçi sonuçlar olabilmesinden kaynaklanan bir şekilde açıkladığı anlamına gelir. Çoğu modern oyun altyapısı ve içerik oluşturma araçları, gerçek zamanlı işleme için gerçek dünya senaryolarının en iyi şekilde değerlendirildikleri için PBR malzemelerini destekler.

![ARR tarafından oluşturulan Helmet glTF örnek modeli](media/helmet.png)

PBR malzemeleri evrensel bir çözüm değildir, ancak. Görüntüleme açısına göre rengi farklı şekilde yansıtan malzemeler vardır. Örneğin, bazı yapılar veya otomobil boyar. Bu tür malzemeler standart PBR modeli tarafından işlenmez ve şu anda Azure uzaktan Işleme tarafından desteklenmemektedir. Bu, *Ince Film* (çok katmanlı yüzeyler) ve *clear-Coat* (otomobil BOYAYMALARı) gibi PBR uzantıları içerir.

## <a name="common-material-properties"></a>Ortak malzeme özellikleri

Bu özellikler tüm malzemelerde ortaktır:

* **Albedocolor:** Bu renk, *Albedomap* veya *:::no-loc text="vertex "::: renkler* gibi diğer renklerle çarpılır. Bir malzemeden *Saydamlık* etkinse alfa kanalı, `1` tam donuk ve anlamı tamamen saydam olan opaklığı ayarlamak için kullanılır `0` . Varsayılan değer beyazdır.

  > [!NOTE]
  > Bir PBR malzemesi tamamen saydam olduğunda, mükemmel bir cam taş gibi, ortamı yine de yansıtır. Güneş gibi parlak noktalar hala yansıma içinde görünür. Bu [renk malzemeleri](color-materials.md)için farklıdır.

* **Albedomap:** Piksel başına Albedo değerleri için [2B doku](../../concepts/textures.md) .

* **alphaclipenabled** ve **alphaclipthreshold:** *alphaclipenabled* değeri true ise, Albedo Alpha değerinin *alphaclipthreshold* değerinden düşük olduğu tüm pikseller çizilmez. Alfa kırpması, saydamlığı etkinleştirmeden ve işlemek için çok daha hızlı bir şekilde kullanılabilir. Alfa kırpılan malzemeler, ancak tamamen opak malzemelerden daha yavaş işleme almaya devam etmektedir. Varsayılan olarak Alfa kırpması devre dışıdır.

* **textureCoordinateScale** ve **TEXTURECOORDINATEOFFSET:** ölçek UV doku koordinatlarına çarpılır, bu da ona eklenir. Dokuları uzatmak ve kaydırmak için kullanılabilir. Varsayılan ölçek (1, 1) ve konum (0, 0).

* **Usevertexcolor:** Kafes :::no-loc text="vertex"::: renkler içeriyorsa ve bu seçenek etkinleştirilirse, kafeslerin :::no-loc text="vertex"::: rengi *Albedocolor* ve *albedomap* ile çarpılır. Varsayılan olarak *UI, Texcolor* devre dışıdır.

* **ıdoubleyüzlü:** Çift sidebir değer true olarak ayarlanırsa, kamera arka yüzlerine baksa bile bu malzemeden üçgenler işlenir. PBR malzemeleri için aydınlatma, arka yüzlere uygun olarak da hesaplanır. Varsayılan olarak bu seçenek devre dışıdır. Ayrıca bkz. [ :::no-loc text="Single-sided"::: işleme](single-sided-rendering.md).

* **TransparencyWritesDepth:** TransparencyWritesDepth bayrağı malzeme üzerinde ayarlandıysa ve malzeme saydamsa, bu malzemeyi kullanan nesneler Ayrıca son derinlik arabelleğine katkıda bulunur. Sonraki bölümde bkz. PBR malzeme bayrağı *saydam* . Kullanım betiğini tamamen saydam sahneler için daha fazla sayıda daha uygun bir [geç doldurma](late-stage-reprojection.md) gerekirse bu özelliğin etkinleştirilmesi önerilir. Karışık donuk/saydam sahneler için, bu ayar, kesin yansıtma davranışı veya yeniden projeksiyon yapıtları ortaya çıkarabilir. Bu nedenle, genel kullanım durumu için varsayılan ve önerilen ayar bu bayrağı devre dışı bırakktır. Yazılı derinlik değerleri, kameraya en yakın nesnenin piksel başına derinlik katmanından alınır.

* **FresnelEffect:** Bu malzeme bayrağı, ilgili malzemeden [adfresnel düşüşünü efektini](../../overview/features/fresnel-effect.md) sunar. Efektin görünümü, aşağıda açıklanan diğer Fresnel düşüşünü parametrelerine tabidir. 

* **FresnelEffectColor:** Bu malzeme için kullanılan Fresnel düşüşünü rengi. Yalnızca bu malzeme üzerinde Fresnel düşüşünü etkisi biti ayarlandığında önemlidir (yukarıya bakın). Bu özellik Fresnel düşüşünü görünün taban rengini denetler (tam açıklama için bkz. [Fresnel düşüşünü etkisi](../../overview/features/fresnel-effect.md) ). Şu anda yalnızca RGB kanalı değerleri önemlidir ve alfa değeri yok sayılır.

* **FresnelEffectExponent:** Bu malzeme için kullanılan Fresnel düşüşünü üs. Yalnızca bu malzeme üzerinde Fresnel düşüşünü etkisi biti ayarlandığında önemlidir (yukarıya bakın). Bu özellik Fresnel düşüşünü görünün yayılmasını denetler. Minimum 0,01 değeri nesnenin tamamına yayılmasına neden olur. 10,0 en büyük değeri, yalnızca en fazla kullanılan kenarları görünür olarak aydınlatır.

## <a name="pbr-material-properties"></a>PBR malzeme özellikleri

Fiziksel olarak temel işlemenin temel fikri, çok çeşitli gerçek dünyada malzemelere öykünmek için *Basecolor*, *Metalness* ve *kablık* özelliklerini kullanmaktır. PBR 'nin ayrıntılı bir açıklaması Bu makalenin kapsamı dışındadır. PBR hakkında daha fazla bilgi için bkz. [diğer kaynaklar](http://www.pbr-book.org). Aşağıdaki özellikler, PBR malzemelere özgüdür:

* **Basecolor:** PBR malzemeleri ' nde, *Albedo rengi* *temel renk* olarak adlandırılır. Azure uzaktan Işlemede *Albedo Color* özelliği ortak malzeme özellikleri aracılığıyla zaten mevcut olduğundan, ek bir temel renk özelliği yoktur.

* **kabalık** ve **kabonlik** , yüzlü, yüzeyin ne kadar kaba veya düzgün olduğunu tanımlar. Kaba yüzeyler, ışığın keskin yüzeylere göre daha fazla yönde dağılımı ve bu da yansımaları keskin hale getirir. Değer aralığı-' dır `0.0` `1.0` . `roughness`Eşit olduğunda `0.0` , yansımalar keskin olur. `roughness`Eşitse `0.5` , yansıtımları bulanık hale gelir.

  Hem bir kabın değeri hem de bir kabonu eşleme sağlanırsa, son değer iki ürünün çarpımı olur.

* **metalness** ve **metalnessMap:** bu özellik, bir yüzeyin iletken ya da dielektrik olup olmadığına karşılık gelir. İletken malzemeler farklı yansıtmalı özelliklere sahiptir ve Albedo rengi olmadan yansıtıcı olarak eğilimlidir. PBR malzemelerde, bu özellik, çevreleyen ortamı ne kadar bir yüzey yansıttığını etkiler. Değerler ile arasında `0.0` değişir `1.0` . Metalness olduğunda `0.0` , Albedo rengi tamamen görünür olur ve malzemeler plastik ya da ceramikler gibi görünür. Metalness olduğunda `0.5` , boyanmış metal gibi görünür. Metalness olduğunda `1.0` yüzey, Albedo rengini neredeyse kaybeder ve yalnızca surlaları yansıtır. Örneğin, ve ise `metalness` , `1.0` `roughness` `0.0` bir yüzey gerçek dünya yansıtmaya benzer şekilde görünür.

  Hem bir metalness değeri hem de bir metalness eşlemesi sağlanırsa, son değer iki ürünün çarpımı olur.

  ![Farklı metalness ve kabalık değerleriyle işlenen sışlar](./media/metalness-roughness.png)

  Yukarıdaki resimde, sağ alt köşedeki Sphere gerçek metal malzemeler gibi görünür ve sol alt, Ceramik veya plastik gibi görünür. Albedo rengi Ayrıca fiziksel özelliklere göre değişiyor. Büyük bir artış sayesinde, malzeme yansıma keskinliğini kaybeder.

* **NormalMap:** İnce ayrıntıların benzetimini yapmak için, [normal bir eşleme](https://en.wikipedia.org/wiki/Normal_mapping) sağlanverilebilir.

* **Occlusionmap** ve **aoscale:** [ortam occlusiyon](https://en.wikipedia.org/wiki/Ambient_occlusion) , occluya ve daha gerçekçi alanlara gölge ekleyerek nesnelerin daha gerçekçi görünmesini sağlar. Occlusiyon değeri, değerinden `0.0` `1.0` `0.0` (occluded) anlamına gelir ve `1.0` hiçbir anlamı yoktur. Bir 2B doku bir occlusiyon eşlemesi olarak sağlanıyorsa, efekt etkinleştirilir ve *AOCE ölçeği* bir çarpan gibi davranır.

  ![Ortam occlusiyon ile ve ile işlenen nesne](./media/boom-box-ao2.gif)

* **saydam:** PBR malzemeleri için yalnızca bir saydamlık ayarı vardır: etkin veya değildir. Opaklık, Albedo renginin alfa kanalı tarafından tanımlanır. Etkinleştirildiğinde yarı saydam yüzeyler çizmek için daha karmaşık bir işleme işlem hattı çağrılır. Azure uzaktan Işleme, gerçek [sıra bağımsız saydamlığı](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT) uygular.

  Saydam geometri işleme pahalıdır. Yalnızca bir yüzeydeki deliklere ihtiyacınız varsa (örneğin, bir ağacın yaprakları için), bunun yerine Alfa kırpması kullanmak daha iyidir.

  ![](./media/transparency.png)Yukarıdaki görüntüde, en sağdaki Sphere 'in tamamen saydam olduğu, ancak yansıma hala görünür durumda olduğu için, üstteki görüntüde sıfır ile tam şeffaf bir bildirimde işlenen küreler.

  > [!IMPORTANT]
  > Çalışma zamanında, herhangi bir malzemenin donuk ' den saydam 'e geçiş olması gerekiyorsa, oluşturucunun *Tilebasedcomposition* [işleme modunu](../../concepts/rendering-modes.md)kullanması gerekir. Bu sınırlama, ile başlamak için saydam malzemeler olarak dönüştürülmüş malzemeler için geçerlidir.

## <a name="technical-details"></a>Teknik ayrıntılar

Azure uzaktan Işleme, bir Lambert dağıtma terimiyle GGX NDF, Schlick Fresnel düşüşünü ve GGX Smith bağıntılı görünürlük terimi ile Micro-model BRDF Cook-Torrance kullanır. Bu model, şu anda endüstri standardıdır. Daha ayrıntılı bilgi için şu makaleye bakın: [fiziksel tabanlı işleme-Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Azure uzaktan Işlemede kullanılan *Metalness-Kabghize* yönelik PBR modelinin bir alternatifi, *Yansımalı* bir ve daha fazla kullanım PBR modelidir. Bu model, daha geniş bir malzeme aralığını temsil edebilir. Ancak, daha pahalıdır ve genellikle gerçek zamanlı durumlarda iyi çalışmaz.
( *Basecolor, Metalness)* öğesine dönüştürülemeyen *(dağıtılmış, yansımalı)* değer çiftleri olduğu Için, *Yansımalı bir ışıldan* *Metalness-kabıya* dönüştürme her zaman mümkün değildir. Tüm *(Basecolor, Metalness)* çiftleri iyi tanımlanmış *(dağıtılmış, yansımalı)* çiftlerine karşılık geldiğinden, diğer yönde dönüştürme daha basit ve daha kesin bir hale gelir.

## <a name="api-documentation"></a>API belgeleri

* [C# pbrmalzemeler sınıfı](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection. Createmalzemesi ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ Pbrmalzemeler sınıfı](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection:: Createmateryal()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Sonraki adımlar

* [Renk malzemeleri](color-materials.md)
* [Dokular](../../concepts/textures.md)
* [Ağ yapıları](../../concepts/meshes.md)