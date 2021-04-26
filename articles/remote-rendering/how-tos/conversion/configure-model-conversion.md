---
title: Model dönüştürmeyi yapılandırma
description: Tüm model dönüştürme parametrelerinin açıklaması
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 1cb5312e164bac09930497c377f1590b6a77ca05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205328"
---
# <a name="configure-the-model-conversion"></a>Model dönüştürmeyi yapılandırma

Bu bölüm, model dönüştürme seçeneklerini belgeler.

## <a name="settings-file"></a>Ayarlar dosyası

Adlandırılmış bir dosya giriş `<modelName>.ConversionSettings.json` modelinin yanındaki giriş kapsayıcısında bulunursa `<modelName>.<ext>` , model dönüştürme işlemi için ek yapılandırma sağlamak üzere kullanılacaktır.
Örneğin, `box.ConversionSettings.json` dönüştürme sırasında kullanılır `box.gltf` .

Dosyanın içeriği aşağıdaki JSON şemasını karşılamalıdır:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Örnek bir dosya olabilir `box.ConversionSettings.json` :

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometri parametreleri

* `scaling` -Bu parametre bir modeli bir arada ölçeklendirir. Ölçek, bir modeli büyütmek veya küçültmek için kullanılabilir, örneğin, bir tablo üst kısmında bir yapı modeli görüntüler.
İşleme altyapısı ölçüm gerektirdiğinden, bir model ölçüm dışındaki birimlerde tanımlandığında ölçekleme de önemlidir.
Örneğin, bir model santimetre cinsinden tanımlanmışsa, 0,01 ölçeğini uygulamak modeli doğru boyutta işlemelidir.
Bazı kaynak veri biçimleri (örneğin,. fbx) bir birim ölçekleme ipucu sağlar ve bu durumda dönüştürme, modeli ölçüm birimlerine dolaylı olarak ölçeklendirir. Kaynak biçimi tarafından sunulan örtük ölçekleme, ölçekleme parametresinin üzerine uygulanır.
Son ölçeklendirme faktörü, geometri köşelerine ve sahne grafiği düğümlerinin yerel dönüşümlerine uygulanır. Kök varlığın dönüştürmesinin ölçeklendirilmesi değiştirilmemiş olarak kalır.

* `recenterToOrigin` -Bir modelin, sınırlayıcı kutusunun kaynaktan ortalanmasını sağlamak için dönüştürülmesi gerektiğini belirtir.
Kaynak modelin kaynağı kaynaktan uzakta bulunuyorsa, kayan nokta duyarlık sorunları işleme yapıtlarına neden olabilir.
Modelin ortalama olması bu durumda yardımcı olabilir.

* `opaqueMaterialDefaultSidedness` -İşleme altyapısı, donuk malzemelerin çift taraflı olduğunu varsayar.
Bu varsayım belirli bir modelin doğru değilse, bu parametre "Singletaraflý" olarak ayarlanmalıdır. Daha fazla bilgi için bkz. [ :::no-loc text="single sided"::: işleme](../../overview/features/single-sided-rendering.md).

### <a name="material-de-duplication"></a>Malzeme Çoğaltma sırası

* `deduplicateMaterials` -Bu parametre, aynı özellikleri ve dokuları paylaşan malzemelerin otomatik olarak yinelenmesine izin verebilir veya devre dışı bırakır. Serbest bırakma, malzeme geçersiz kılma işlemleri işlendikten sonra gerçekleşir. Varsayılan olarak etkindir.

* Bir modelde 65.535 ' den fazla malzeme serbest bırakılsa bile, hizmet, benzer özelliklerle malzemeleri birleştirmeye çalışır. Son çare olarak, sınırı aşan malzemeler kırmızı bir hata malzemesiyle değiştirilmeyecektir.

![Görüntüde 68.921 renkli üçgenden oluşan iki küp gösterilmektedir.](media/mat-dedup.png?raw=true)

68.921 renkli üçgenin iki küpü. Left: 68.921 renk malzemeleriyle yinelenenleri kaldırma Işleminden önce. Right: 64.000 renk malzemeleriyle yinelenenleri kaldırma Işleminden sonra. Sınır 65.535 malzemelerdir. (Bkz. [sınırlara](../../reference/limits.md)bakın.)

### <a name="color-space-parameters"></a>Renk alanı parametreleri

İşleme altyapısı, renk değerlerinin doğrusal alanda olmasını bekler.
Bir model gama alanı kullanılarak tanımlanmışsa, bu seçenekler true olarak ayarlanmalıdır.

* `gammaToLinearMaterial` -Gama alanından doğrusal alana malzeme renkleri dönüştürün
* `gammaToLinearVertex` - :::no-loc text="vertex"::: Gamma alanından doğrusal alana Renkleri Dönüştür

> [!NOTE]
> FBX dosyaları için bu ayarlar `true` Varsayılan olarak olarak ayarlanır. Tüm diğer dosya türleri için varsayılan olarak ' dir `false` .

### <a name="scene-parameters"></a>Sahne parametreleri

* `sceneGraphMode` -Kaynak dosyadaki sahne grafiğinin nasıl dönüştürüleceğini tanımlar:
  * `dynamic` (varsayılan): dosyadaki tüm nesneler, API 'de [varlık](../../concepts/entities.md) olarak sunulur ve bağımsız olarak dönüştürülebilir. Çalışma zamanındaki düğüm hiyerarşisi, kaynak dosyadaki yapıyla aynı.
  * `static`: Tüm nesneler API 'de kullanıma sunuldu ancak bağımsız olarak dönüştürülemez.
  * `none`: Sahne grafiği bir nesne olarak daraltılır.

Her mod farklı çalışma zamanı performansına sahiptir. `dynamic`Modunda, hiçbir bölüm taşınmasa bile, performans maliyeti grafikteki [varlıkların](../../concepts/entities.md) sayısıyla doğrusal şekilde ölçeklendirilir. `dynamic`Yalnızca parçaları tek tek taşımak gerektiğinde kullanın, örneğin ' Açılım görünümü ' animasyonu için.

`static`Mod tam sahne grafiğini dışa aktarır, ancak bu grafiğin içindeki bölümlerin kök parçasına göre sabit bir dönüştürmesi vardır. Ancak, nesnenin kök düğümü, önemli bir performans maliyeti olmadan taşınabilir, döndürülebilir veya ölçeklenmeye devam edebilir. Ayrıca, [uzamsal sorgular](../../overview/features/spatial-queries.md) tek tek bölümler döndürür ve her bölüm [durum geçersiz kılmaları](../../overview/features/override-hierarchical-state.md)aracılığıyla değiştirilebilir. Bu modda, nesne başına çalışma zamanı ek yükü göz ardı edilebilir değildir. Nesne başına incelemeden hala ihtiyacınız olan ancak nesne başına dönüşüm değişikliği olmadığında büyük sahneler için idealdir.

`none`Mod en az çalışma zamanı ek yüküne ve çok daha iyi yükleme zamanına sahiptir. Tek nesnelerin denetimi veya dönüşümü bu modda mümkün değildir. Kullanım örnekleri, örneğin, ilk yerinde anlamlı bir sahne grafiğine sahip olmayan photogrammetri modelleridir.

> [!TIP]
> Birçok uygulama birden çok modeli yükler. Her model için dönüştürme parametrelerini, nasıl kullanılacağına bağlı olarak iyileştirmelisiniz. Örneğin, kullanıcının, ayrıntılı bir şekilde ele geçirmesine ve inceleneceği bir otomobil modelini göstermek istiyorsanız, bunu moduyla dönüştürmeniz gerekir `dynamic` . Bununla birlikte, arabayı bir göster odası ortamına eklemek isterseniz, bu model, `sceneGraphMode` veya hatta olarak ayarlanmış şekilde dönüştürülebilir `static` `none` .

### <a name="physics-parameters"></a>Fizik parametreleri

* `generateCollisionMesh` -Bir modelde [uzamsal sorgular](../../overview/features/spatial-queries.md) için desteğe ihtiyacınız varsa, bu seçeneğin etkinleştirilmesi gerekir. En kötü durumda, bir çakışma ağı oluşturma, dönüştürme süresini iki katına alabilir. Çakışma kafesleri olan modellerin yüklenmesi daha uzun sürer ve bir `dynamic` sahne grafiği kullanılırken, ayrıca daha yüksek bir çalışma zamanı performans yükü vardır. Genel en iyi performans için, bu seçeneği, uzamsal sorgular gerektirmeyen tüm modeller üzerinde devre dışı bırakmanız gerekir.

### <a name="unlit-materials"></a>Aydınlatılmamış malzemeler

* `unlitMaterials` -Varsayılan olarak dönüştürme, [PBR malzemeleri](../../overview/features/pbr-materials.md)oluşturmayı tercih edecektir. Bu seçenek, dönüştürücünün tüm malzemeleri [renk malzemeleri](../../overview/features/color-materials.md) olarak görmesini söyler. Photogrammetri aracılığıyla oluşturulan modeller gibi, zaten aydınlatma içeren verileriniz varsa, bu seçenek her bir malzemenin tek tek [geçersiz kılınmasına](override-materials.md) gerek kalmadan, tüm malzemelerde doğru dönüştürmeyi hızlı bir şekilde zorlamanıza olanak sağlar.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Bir Phong malzeme modeliyle eski FBX biçimlerinden dönüştürme

* `fbxAssumeMetallic` -FBX biçiminin daha eski sürümleri, bir Phong malzeme modeli kullanarak materyalleri tanımlar. Dönüştürme işleminin, bu malzemelerin oluşturucunun [PBR modeliyle](../../overview/features/pbr-materials.md)nasıl eşlendiğini çıkarması gerekebilir. Genellikle bu iyi işe yarar, ancak bir malzemenin dokuya, yüksek yansımalı değerler ve gri olmayan bir Albedo rengine sahip olmadığı durumlarda belirsizlik ortaya çıkabilir. Bu durumda, dönüştürmenin yüksek yansımalı değerlerin önceliklerini belirleme, Albedo renginin çıkaran, yüksek oranda yansıtmalı, metalik bir malzeme tanımlama veya Albedo renginin ayırt ettiği, parçalı renkli plastik gibi bir şey tanımlama arasında seçim yapması gerekir. Varsayılan olarak, dönüştürme işlemi, yüksek oranda yansımalı değerlerin belirsizlik 'in uygulandığı durumlarda metalik bir malzeme olduğunu varsayar. Bu parametre `false` , ters geçiş için olarak ayarlanabilir.

### <a name="coordinate-system-overriding"></a>Koordinat sistemi geçersiz kılma

* `axis` -Koordinat sistem birimi vektörlerini geçersiz kılmak için. Varsayılan değerler şunlardır `["+x", "+y", "+z"]` . Teorik olarak, FBX biçiminde bu vektörler tanımlanmış ve dönüştürme söz konusu bilgileri sahneyi dönüştürmek için kullandığı bir üst bilgi vardır. GlTF biçimi de sabit bir koordinat sistemi tanımlar. Uygulamada, bazı varlıkların başlığında yanlış bilgiler var veya farklı bir koordinat sistemi kuralıyla kaydedilmiş. Bu seçenek, telafi sistemini dengelemek için geçersiz kılmanızı sağlar. Örneğin: `"axis" : ["+x", "+z", "-y"]` Z ekseni ve y eksenini değiş tokuş eder ve y ekseni yönünü tersine getirerek koordinat sistem hanliðini koruyun.

### <a name="node-meta-data"></a>Düğüm meta verileri

* `metadataKeys` -Dönüştürme sonucunda tutmak istediğiniz düğüm meta verileri özelliklerinin anahtarlarını belirtmenize izin verir. Tam anahtarları veya joker karakter anahtarlarını belirtebilirsiniz. Joker karakterler "ABC *" biçimindedir ve "ABC" ile başlayan herhangi bir anahtarla eşleşir. Desteklenen meta veri değer türleri `bool` , `int` , `float` ve `string` .

    GLTF dosyaları için bu veriler [düğümlerdeki ekstralar nesnesinden](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras)gelir. FBX dosyaları için bu veriler `Properties70` üzerindeki verilerden gelir `Model nodes` . Daha fazla ayrıntı için lütfen 3B varlık aracınızın belgelerine bakın.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: formatını

:::no-loc text="vertex":::Bir kafesin biçimini, bellek tasarrufları için bir hassaslığına ayarlamak mümkündür. Daha düşük bir bellek kaplama daha büyük modeller yüklemenize veya daha iyi performans elde etmenize olanak tanır. Ancak verilerinize bağlı olarak, yanlış biçim işleme kalitesini önemli ölçüde etkileyebilir.

> [!CAUTION]
> :::no-loc text="vertex":::Modeller artık belleğe sığmıyor veya mümkün olan en iyi performans için iyileştirirken biçimin değiştirilmesi son çare olmalıdır. Değişiklikler, hem açık hem de hafif olan işleme yapıtları kolayca ortaya çıkarabilir. Ne bakabileceğinizi bilmiyorsanız, Varsayılanı değiştirmemelisiniz.

Bu ayarlamalar mümkündür:

* Belirli veri akışları açıkça dahil edilebilir veya hariç tutulabilir.
* Veri akışlarının doğruluğu, bellek ayak izini azaltmak için azaltılabilir.

Dosyadaki aşağıdaki `vertex` bölüm `.json` isteğe bağlıdır. Açıkça belirtilmeyen her bir bölüm için, dönüştürme hizmeti varsayılan ayarına geri döner.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Bir bileşeni uygulamasına zorlayarak `NONE` , çıkış kafesinin ilgili akışa sahip olmadığı garanti edilir.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Akış başına bileşen biçimleri :::no-loc text="vertex":::

İlgili bileşenler için bu biçimlere izin verilir:

| :::no-loc text="Vertex"::: bileşeni | Desteklenen biçimler (kalın = varsayılan) |
|:-----------------|:------------------|
|konum| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, yok |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **yok**|
|olağan| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, yok |
|Tanjant| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, yok |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, yok |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, yok |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, yok |

#### <a name="supported-component-formats"></a>Desteklenen bileşen biçimleri

Biçimlerin bellek yazmalar aşağıdaki gibidir:

| Biçimlendir | Açıklama | Bayt başına :::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|iki bileşen tam kayan nokta duyarlığı|8
|16_16_FLOAT|iki bileşenden oluşan yarı kayan nokta duyarlığı|4
|32_32_32_FLOAT|üç bileşen tam kayan nokta duyarlığı|12
|16_16_16_16_FLOAT|dört bileşen yarım kayan nokta duyarlığı|8
|8_8_8_8_UNSIGNED_NORMALIZED|dört bileşen bayt, `[0; 1]` aralığa normalleştirilmiş|4
|8_8_8_8_SIGNED_NORMALIZED|dört bileşen bayt, `[-1; 1]` aralığa normalleştirilmiş|4

#### <a name="best-practices-for-component-format-changes"></a>Bileşen biçimi değişiklikleri için en iyi yöntemler

* `position`: Azaltılmış doğruluk yeterlidir. **16_16_16_16_FLOAT** , küçük modeller için bile fark edilebilir quantileştirme yapıtları sunar.
* `normal`, `tangent` , `binormal` : Genellikle bu değerler birlikte değiştirilir. Normal bir quantiktan kaynaklanan fark edilebilir ışık yapıtları olmadığı için, doğruluğunu arttırmanın bir nedeni yoktur. Ancak bazı durumlarda, bu bileşenler **none** olarak ayarlanabilir:
  * `normal`, `tangent` ve `binormal` yalnızca modeldeki en az bir malzemenin aydınlatılmış olması durumunda gereklidir. ARR 'de, modelde herhangi bir zamanda bir [PBR malzemesi](../../overview/features/pbr-materials.md) kullanıldığında bu durum söz konusu olur.
  * `tangent` ve `binormal` yalnızca herhangi bir aydınlatma malzemelerinden biri normal harita dokusunu kullandığında gereklidir.
* `texcoord0``texcoord1`: Doku koordinatları, değerleri aralıkta kaladığında vebahsedilen `[0; 1]` dokuların 2048 x 2048 piksellik en büyük boyuta sahip olduğunda, daha az doğruluk (16_16_FLOAT) kullanabilir. Bu sınırlar aşılırsa, doku eşlemenin kalitesi de düşer.

#### <a name="example"></a>Örnek

Dokularla aydınlatma sağlayan bir photogrammetri modeliniz olduğunu varsayalım. Modeli işlemek için gereken tek şey, :::no-loc text="vertex"::: konumlar ve doku koordinatları.

Varsayılan olarak, dönüştürücünün her zaman bir modelde PBR malzemeleri kullanmak isteyebileceğiniz varsayımında, bu nedenle `normal` `tangent` sizin için, ve verileri üretecektir `binormal` . Sonuç olarak, köşe başına bellek kullanımı (12 bayt) + (8 bayt) + (4 bayt) + ( `position` `texcoord0` `normal` `tangent` 4 bayt) + `binormal` (4 bayt) = 32 bayttır. Bu türün daha büyük modelleri, :::no-loc text="vertices"::: birden fazla gigabayt belleği elde eden çok sayıda daha fazla model elde edebilir. Bu çok büyük miktarlarda veri performansı etkiler ve hatta belleğiniz tükenmez.

Model üzerinde hiçbir şey dinamik aydınlatma gerektirmez ve tüm doku koordinatlarının aralıkta olduğunu bilmenin yanı sıra,,, `[0; 1]` `normal` ve ile `tangent` `binormal` `NONE` `texcoord0` yarı duyarlık ( `16_16_FLOAT` ) ayarlayabilirsiniz :::no-loc text="vertex"::: . Ağ verilerini yarı bir şekilde kesmek, daha büyük modeller yüklemeniz ve potansiyel olarak performansı artırmanızı sağlar.

## <a name="memory-optimizations"></a>Bellek iyileştirmeleri

Yüklenen içeriğin bellek tüketimi, işleme sisteminde bir performans sorunu haline gelebilir. Bellek yükü çok büyük hale gelirse, işleme performansının güvenliğini tehlikeye atabilir veya modelin tamamen yüklenmemesine neden olabilir. Bu paragraf, bellek ayak izini azaltmaya yönelik bazı önemli stratejileri tartışır.

### <a name="instancing"></a>Örnek Oluşturma

Örnek oluşturma, kafeslerin ayrı uzamsal Dönüştürmelere sahip parçalar için yeniden kullanıldığı bir kavramdır. Bu, kendi benzersiz geometrisine başvuruda bulunan her parçanın aksine. Örnek oluşturma, bellek ayak izi üzerinde önemli bir etkiye sahiptir.
Örnek kullanım örnekleri, bir altyapı modelindeki veya bir mimari modeldeki sandalyeler içindeki screws.

> [!NOTE]
> Örnek oluşturma, bellek tüketimini (ve dolayısıyla, yükleme sürelerini) önemli ölçüde iyileştirebilir, ancak işleme performansı tarafında iyileştirmeler önemli değildir.

Dönüştürme hizmeti, parçalar kaynak dosyada uygun şekilde işaretlenmişse örnek oluşturma örneği. Bununla birlikte, dönüştürme, yeniden kullanılabilir parçaları belirlemek için ağ verilerinin ayrıntılı analizini gerçekleştirmez. Böylece, içerik oluşturma aracı ve dışa aktarma işlem hattı, doğru örnek oluşturma kurulumu için uygun ölçütlerdir.

Dönüştürme sırasında örnek bilgilerinin korunup korunmayacağını sınamanın basit bir yolu, özel olarak üyenin [Çıkış istatistiklerine](get-information.md#example-info-file)göz atalım `numMeshPartsInstanced` . Değeri `numMeshPartsInstanced` sıfırdan büyükse, kafeslerin örnekler arasında paylaşıldığını gösterir.

#### <a name="example-instancing-setup-in-3ds-max"></a>Örnek: 3ds Max 'da kurulum örnek oluşturma

[Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max) **`Copy`** ,, **`Instance`** , ve **`Reference`** verilen dosyadaki örnek oluşturma ile farklı şekilde davranan farklı nesne kopyalama modlarına sahiptir `.fbx` .

![3ds Max 'da kopyalama](./media/3dsmax-clone-object.png)

* **`Copy`** : Bu modda, kafes klonlanmıştır, bu nedenle hiçbir örnek kullanılmaz ( `numMeshPartsInstanced` = 0).
* **`Instance`** : İki nesne aynı kafesi paylaşır, bu nedenle örnek oluşturma ( `numMeshPartsInstanced` = 1) kullanılır.
* **`Reference`** : Benzersiz değiştiriciler geometrilere uygulanabilir, bu nedenle dışarı aktarıcı bir koruyucu yaklaşım seçer ve örnek oluşturma ( `numMeshPartsInstanced` = 0) kullanmaz.


### <a name="depth-based-composition-mode"></a>Derinlik tabanlı bileşim modu

Bellek bir sorun oluşturacaksa, oluşturucuyu [derinlik tabanlı kompozisyon moduyla](../../concepts/rendering-modes.md#depthbasedcomposition-mode)yapılandırın. Bu modda, GPU yükü birden fazla GPU genelinde dağıtılır.

### <a name="decrease-vertex-size"></a>Köşe boyutunu azalt

[Bileşen biçimi değişiklikleri için en iyi yöntemler](configure-model-conversion.md#best-practices-for-component-format-changes) bölümünde açıklandığı gibi, köşe biçiminin ayarlanması, bellek ayak izini düşürebilir. Ancak, bu seçenek son çare olmalıdır.

### <a name="texture-sizes"></a>Doku boyutları

Senaryonun türüne bağlı olarak, doku verileri miktarı, ağ verileri için kullanılan belleği engelleyebilir. Photogrammetri modelleri adaylardır.
Dönüştürme yapılandırması, dokuları otomatik olarak ölçeklendirmek için bir yol sağlamaz. Gerekirse, doku ölçeklendirmesinin bir istemci tarafı ön işleme adımı olarak yapılması gerekir. Dönüştürme adımı ancak uygun bir [doku sıkıştırma biçimi](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)seçer:

* `BC1` donuk renk dokuları için
* `BC7` alfa kanallı kaynak renk dokuları için

Biçim `BC7` için bellek parmak izi iki kez karşılaştırıldığından `BC1` , giriş dokuların gereksiz bir şekilde alfa kanalı sağlamadıklarından emin olmak önemlidir.

## <a name="typical-use-cases"></a>Tipik kullanım örnekleri

Belirli bir kullanım durumu için iyi içeri aktarma ayarlarını bulma sıkıcı bir işlem olabilir. Diğer yandan, dönüştürme ayarları çalışma zamanı performansı üzerinde önemli bir etkiye sahip olabilir.

Belirli iyileştirmeler için uygun olan bazı kullanım örneği sınıfları vardır. Bazı örnekler aşağıda verilmiştir.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Kullanım örneği: mimari görselleştirme/büyük dış mekharitaları

* Bu tür sahneler statik olmaya eğilimlidir, yani taşınabilir parçalar gerekmez. Buna uygun olarak, `sceneGraphMode` `static` `none` çalışma zamanı performansını artıran veya hatta olarak ayarlanabilir. `static`Modunda, sahnenin kök düğümü taşınabilir, döndürülebilir ve ölçeklenebilir olabilir. Örneğin, 1:1 ölçek (ilk kişi görünümü için) ve tablo üst görünümü arasında dinamik geçiş yapabilirsiniz.

* Parçaları taşımaya ihtiyacınız olduğunda, genellikle bu bölümleri ilk yerde seçebilmeniz için raylara veya diğer [uzamsal sorgulara](../../overview/features/spatial-queries.md)yönelik desteğe ihtiyacınız olduğu anlamına gelir. Öte yandan, bir şeyi hareket ettirmek istemiyorsanız, bunun da uzamsal sorgulara katılmasını gerektirmez ve bu nedenle `generateCollisionMesh` bayrağı kapatabilir. Bu anahtar, dönüştürme sürelerini, yükleme sürelerini ve ayrıca kare başına güncelleştirme maliyetlerini önemli ölçüde etkiler.

* Uygulama [kesme düzlemleri](../../overview/features/cut-planes.md)kullanmıyorsa, `opaqueMaterialDefaultSidedness` bayrağın kapalı olması gerekir. Performans kazancı genellikle %20-%30 ' dur. Kesme düzlemleri hala kullanılabilir, ancak sayaç kullanımı kolay olan nesnelerin iç bölümlerine baktığı sırada arka yüz kalmaz. Daha fazla bilgi için bkz. [ :::no-loc text="single sided"::: işleme](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Kullanım örneği: Photogrammetri modelleri

Photogrammetri modellerini işlerken genellikle bir sahne grafiğine gerek yoktur, bu nedenle öğesini `sceneGraphMode` olarak ayarlayabilirsiniz `none` . Bu modeller ile başlamak için nadiren karmaşık bir sahne grafiği içerdiğinden, bu seçeneğin etkisi, ancak önemli olmalıdır.

Işıklandırma dokuya zaten bakdığı için dinamik aydınlatma gerekmez. Bu nedenle:

* `unlitMaterials` `true` Tüm malzemeleri açılmamış [renk malzemelere](../../overview/features/color-materials.md)açmak için bayrağını ayarlayın.
* Gereksiz verileri köşe biçiminden kaldırın. Yukarıdaki [örneğe](#example) bakın.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Kullanım örneği: kompakt makinelerin görselleştirilmesi vb.

Bu kullanım durumlarında, modeller genellikle küçük bir birim içinde çok yüksek ayrıntılara sahiptir. İşleyici, bu tür örnekleri de işlemek için yoğun bir şekilde iyileştirilir. Ancak, önceki kullanım durumunda bahsedilen iyileştirmelerin çoğu burada uygulanmaz:

* Tek parçaların seçilebilir ve taşınabilir olması gerekir, bu nedenle `sceneGraphMode` sol tarafta olmalıdır `dynamic` .
* Işın genellikle uygulamanın ayrılmaz bir parçası olduğundan, çakışma kafeslerinin oluşturulması gerekir.
* Kesin olmayan düzlemleri, bayrağın etkin olduğunu daha iyi görünür `opaqueMaterialDefaultSidedness` .

## <a name="deprecated-features"></a>Kullanım dışı bırakılan özellikler

Modele özgü olmayan dosya adı kullanılarak ayarların sağlanması `conversionSettings.json` hala desteklenir ancak kullanım dışıdır.
Lütfen bunun yerine modele özgü dosya adını kullanın `<modelName>.ConversionSettings.json` .

`material-override`Dönüştürme ayarları dosyasındaki bir [malzeme geçersiz kılma dosyasını](override-materials.md) tanımlamak için bir ayarın kullanılması hala destekleniyor ancak kullanım dışıdır. Lütfen bunun yerine modele özgü dosya adını kullanın `<modelName>.MaterialOverrides.json` .

## <a name="next-steps"></a>Sonraki adımlar

* [Model dönüştürme](model-conversion.md)
* [Renk malzemeleri](../../overview/features/color-materials.md)
* [PBR malzemeleri](../../overview/features/pbr-materials.md)
* [Model dönüştürme sırasında malzemeleri geçersiz kılma](override-materials.md)