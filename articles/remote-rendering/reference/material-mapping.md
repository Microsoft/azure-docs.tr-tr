---
title: Model biçimleri için malzeme eşleme
description: Model kaynak biçimlerinden PBR malzemeye varsayılan dönüştürmeyi açıklar
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96024053"
---
# <a name="material-mapping-for-model-formats"></a>Model biçimleri için malzeme eşleme

Bir kaynak varlık [model olarak dönüştürüldüğünde](../how-tos/conversion/model-conversion.md), dönüştürücü her bir [ağ](../concepts/meshes.md)için [malzemeler](../concepts/materials.md) oluşturur. Malzemelerin oluşturulma yöntemi [geçersiz kılınabilir](../how-tos/conversion/override-materials.md). Ancak varsayılan olarak, dönüştürme [PBR malzemeleri](../overview/features/pbr-materials.md)oluşturacaktır. FBX gibi her kaynak dosya biçimi, malzemeleri tanımlamak için kendi kurallarını kullandığından, bu kuralların Azure uzaktan Işleme 'nin PBR malzeme parametrelerine eşlenmesi gerekir. 

Bu makalede, malzemeleri kaynak varlıklardan çalışma zamanı malzemelerine dönüştürmek için kullanılan tam eşlemeler listelenmektedir.

## <a name="gltf"></a>glTF

GlTF 2,0 belirtimindeki neredeyse her şey, *EmissiveFactor* ve *EmissiveTexture* hariç Azure uzaktan işlemede desteklenir.

Aşağıdaki tabloda eşleme gösterilmektedir:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   Kabaghnessfactor   |   kablık                |
|   Kabaghnesi Stexture  |   Kabaghnessmap             |
|   Occlusıonfactor   |   kapatma                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   Harflerden mod. donuk  |   alphaClipEnabled = false, ıstransparent = false |
|   Harflerden mod. MASK    |   alphaClipEnabled = true, ıstransparent = false  |
|   Harflerden mod. BLEND   |   ıstransparent = true     |
|   Çift taraflı       |   Isdoubleyüzlü            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

GlTF içindeki her `texCoord` dokuda Azure uzaktan işleme malzemelerde desteklenen bir değer olabilir.

### <a name="embedded-textures"></a>Gömülü dokular

*\* . Bin* veya *\* . GLB* dosyalarına gömülü dokular desteklenir.

### <a name="supported-gltf-extension"></a>Desteklenen glTF uzantısı

Ayrıca, temel özellik kümesine ek olarak Azure uzaktan Işleme aşağıdaki glTF uzantılarını destekler:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): [renk malzemelere](../overview/features/color-materials.md)karşılık gelir. *Duykulu* malzemeler için bu uzantının kullanılması önerilir.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): Metalik bir kabalık dokular yerine, dağıtılmış, kurgusal bir açıklık dokuları sağlayabilirsiniz. Azure uzaktan Işleme uygulamasının uzantısı doğrudan uzantısından sonra dönüştürme formüllerini izler.

## <a name="fbx"></a>FBX

FBX biçimi kapalı-kaynak ve FBX malzemeleri genel olarak PBR malzemeleri ile uyumlu değildir. FBX, çok sayıda benzersiz parametre ve özelliklere sahip yüzeylerin karmaşık bir açıklamasını kullanır ve **bunların hepsi Azure uzaktan işleme işlem hattı tarafından kullanılmaz**.

> [!IMPORTANT]
> Azure uzaktan Işleme modeli dönüştürme işlem hattı yalnızca **FBX 2011 ve üstünü** destekler.

FBX biçimi malzemeler için bir koruyucu yaklaşım tanımlar, resmi FBX belirtiminde yalnızca iki tür vardır:

* *Lambert* -daha önce yalnızca bir süre için kullanılmaz, ancak dönüştürme zamanında Phong 'e dönüştürme işlemi hala desteklenmektedir.
* *Phong* -neredeyse tüm malzemeler ve çoğu içerik aracı bu türü kullanır.

Phong modeli daha doğru ve FBX malzemeleri için *tek* model olarak kullanılır. Bu işlem, *FBX malzemesi* olarak adlandırılır.

> Maya, bir malzemenin PBR ve Stingray türleri için özel özellikler tanımlayarak FBX için iki özel uzantı kullanır. Bu ayrıntılar FBX belirtimine dahil değildir, bu nedenle şu anda Azure uzaktan oluşturma tarafından desteklenmez.

FBX malzemeleri, dağıtılmış bir dokusundaki bir Albedo eşlemesine dönüştürmek için, diğer parametreleri dağıtmadan çıkarmak için, diğer parametreleri hesapladık.

> FBX 'teki tüm renkler ve dokular sRGB alanında (Gama alanı olarak da bilinir), ancak Azure uzaktan Işleme görselleştirme sırasında doğrusal boşluk ile ve çerçevenin sonunda her şeyi sRGB alanına geri dönüştürür. Azure uzaktan Işleme varlık ardışık düzeni her şeyi, oluşturucuyu hazırlanan veri olarak göndermek için doğrusal alana dönüştürür.

Bu tabloda, dokuların FBX malzemelerinden Azure uzaktan Işleme malzemelerinden nasıl eşlendiği gösterilmektedir. Bazıları doğrudan kullanılmaz, ancak formüllere katılan diğer dokularla birlikte (örneğin, dağıtma dokusu):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Occlusiyon eşleme   |
| DiffuseColor | *Albedo, Metalness için kullanılır* |
| TransparentColor | *Albedo 'ın alfa kanalı için kullanılır* |
| TransparencyFactor | *Albedo 'ın alfa kanalı için kullanılır* |
| Opaklık | *Albedo 'ın alfa kanalı için kullanılır* |
| SpecularColor | *Albedo, Metalness, Kabalılığını için kullanılır* |
| SpecularFactor| *Albedo, Metalness, Kabalılığını için kullanılır* |
| ShininessExponent | *Albedo, Metalness, Kabalılığını için kullanılır* |
| NormalMap | NormalMap |
| Masına | *NormalMap 'e dönüştürüldü* |
| Emissivecrenkli | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

Yukarıdaki eşleme, çok sayıda varsayımlar nedeniyle, malzeme dönüştürmenin en karmaşık kısmıdır. Bu varsayımlar aşağıda açıklanmıştır.

Aşağıda kullanılan bazı tanımlar:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Red ∗ 0,2125 +  `Specular` . Yeşil ∗ 0,7154 + `Specular` . Mavi ∗ 0,0721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Kırmızı<sup>2</sup> + 0,587 * `Diffuse` . Yeşil<sup>2</sup> + 0,114 * `Diffuse` . Mavi<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Kırmızı<sup>2</sup> + 0,587 * `Specular` . Yeşil<sup>2</sup> + 0,114 * `Specular` . Mavi<sup>2</sup>
* `SpecularStrength` = Max ( `Specular` . Kırmızı, `Specular` . Yeşil, `Specular` . Ma

Specularyoğunluğu formülü [buradan](https://en.wikipedia.org/wiki/Luma_(video))elde edilir.
Parlaklık formülü bu [belirtimde](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)açıklanmıştır.

### <a name="roughness"></a>Kablık

`Roughness` , `Specular` ve `ShininessExponent` [Bu formül](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)kullanılarak hesaplanır. Bu formül, Phong yansımalı üsünden bağımsız bir şekilde bir yaklaşık olarak oluşur:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalness

`Metalness` , ' dan hesaplanır `Diffuse` ve `Specular` [gltf belirtiminde bu formül](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)kullanılarak kullanılır.

Buradaki fikir denklemi çözeceğiz: AX<sup>2</sup> + BX + C = 0.
Temel olarak, dielektrik yüzeyleri, hafif bir şekilde ışığın %4 ' i etrafında yansıtmaktadır ve geri kalan dağıtılmış olur. Metalik yüzeyler, dağıtılmış bir şekilde hiçbir ışık göstermez, ancak tamamen yansımalı bir şekilde yansıtır.
Parlak plastik ve parlak metalik yüzeyleri ayırt etmenin bir yolu olmadığından, bu formülün bazı dezavantajları vardır. Yüzeyde birçok zaman metalik özelliklerine sahip olduğunu varsayıyoruz ve bu nedenle parlak plastik/lastik yüzeyleri beklendiği gibi görünmeyebilir.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` , ve ' den hesaplanır `Diffuse` `Specular` `Metalness` .

Metalness bölümünde açıklandığı gibi, dielektrik yüzeyleri ışığın %4 ' i etrafında yansıtır.  
Buradaki fikir, `Dielectric` `Metal` `Metalness` bir faktör olarak değer kullanan renkler arasında ve renklerle daha erken enterpoladır. Metalness ise `0.0` , yansımalı öğesine bağlı olarak, koyu bir renk (yansımalı ise yüksekse) veya dağıtma değişmez (herhangi bir yansımalı yoksa) olur. Metalness büyük bir değer ise, bir daha sonra yansımalı renk yerine, dağıtma rengi kaybolacaktır.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` Yukarıdaki formül tarafından hesaplandı, ancak alfa kanalı ek hesaplamalar gerektiriyor. FBX biçimi saydamlık ile ilgilidir ve bunu tanımlamanın birçok yolu vardır. Farklı içerik araçları farklı yöntemler kullanır. Buradaki fikir, bunları tek bir formülde birleştirmenizde yarar vardır. Bazı varlıkları yanlışlıkla saydam olarak gösteriliyor, ancak ortak bir şekilde oluşturulmadıysa.

Bu,, `TransparentColor` `TransparencyFactor` ,, ' den hesaplanır `Opacity` :

`Opacity`tanımlanmışsa, bunu doğrudan kullanın: `AlbedoAlpha`  =  `Opacity` Else  
`TransparencyColor`tanımlanmışsa `AlbedoAlpha` = 1,0-(( `TransparentColor` . Kırmızı + `TransparentColor` . Yeşil + `TransparentColor` . Mavi)/3,0) else  
Eğer `TransparencyFactor` , then `AlbedoAlpha` = 1,0- `TransparencyFactor`

Son `Albedo` rengin, ile birleştiren dört kanalı vardır `AlbedoRGB` `AlbedoAlpha` .

### <a name="summary"></a>Özet

Burada özetlemek gerekirse, `Albedo` `Diffuse` sıfıra yakınsa, orijinale yakın olacak `Specular` . Aksi takdirde yüzey metalik bir yüzey gibi görünür ve dağıtma rengini kaybeder. Yüzey yeterince büyükse daha parlak ve yansıtıcı görünür `ShininessExponent` ve `Specular` parlak olur. Aksi takdirde yüzey kabaca görünür ve ortamı yansıtmaktadır.

### <a name="known-issues"></a>Bilinen sorunlar

* Geçerli formül basit renkli geometri için iyi çalışmıyor. `Specular`Yeterince parlak ise, tüm geometriler herhangi bir renk olmadan yansıtmalı metalik yüzeyler haline gelir. Buradaki geçici çözüm, `Specular` orijinalden %30 ' a düşürüme veya dönüştürme ayarı [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)' i kullanmaktır.
* PBR malzemeleri son zamanlarda `Maya` ve `3DS Max` içerik oluşturma araçlarına eklendi. Bu kişiler, FBX 'e geçirmek için Kullanıcı tanımlı özel siyah kutu özelliklerini kullanırlar. Azure uzaktan Işleme, açıklanmadığı ve biçim kapalı-kaynak olduğundan bu ek özellikleri okummıyor.

## <a name="next-steps"></a>Sonraki adımlar

* [Model dönüştürme](../how-tos/conversion/model-conversion.md)
* [Model dönüştürme sırasında malzemeleri geçersiz kılma](../how-tos/conversion/override-materials.md)
