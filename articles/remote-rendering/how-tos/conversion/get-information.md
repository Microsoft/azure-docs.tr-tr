---
title: Dönüştürmeler hakkında bilgi alma
description: Dönüştürmeler hakkında bilgi alma
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91576634"
---
# <a name="get-information-about-conversions"></a>Dönüştürmeler hakkında bilgi alma

## <a name="information-about-a-conversion-the-result-file"></a>Bir dönüştürme hakkında bilgi: sonuç dosyası

Dönüştürme hizmeti bir varlığı dönüştürdüğünde, herhangi bir sorunun özetini "sonuç dosyasına" yazar. Örneğin, bir dosya `buggy.gltf` dönüştürülürse, çıkış kapsayıcısı adlı bir dosya içerir `buggy.result.json` .

Sonuç dosyası, dönüştürme sırasında oluşan tüm hataları ve uyarıları listeler ve bir sonuç Özeti verir; bu, veya ' den biridir `succeeded` `failed` `succeeded with warnings` .
Sonuç dosyası, her birinin,,, ve ' den biri olan dize özelliğine sahip olan bir JSON dizisi olarak yapılandırılır `warning` `error` `internal warning` `internal error` `result` . En fazla bir hata (veya) olacaktır, `error` `internal error` her zaman bir tane olur `result` .

## <a name="example-result-file"></a>Örnek *sonuç* dosyası

Aşağıdaki örnek, bir arrAsset başarıyla oluşturulan bir dönüştürmeyi açıklar. Ancak eksik bir doku olduğundan, elde edilen arrAsset istenen şekilde olmayabilir.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>Dönüştürülen model hakkında bilgi: bilgi dosyası

Dönüştürme hizmeti tarafından üretilen arrAsset dosyası yalnızca işleme hizmeti tarafından tüketimine yöneliktir. Ancak, bir işleme oturumu başlatmadan bir modelle ilgili bilgilere erişmek istediğinizde bazı zamanlar olabilir. Bu iş akışını desteklemek için, dönüştürme hizmeti çıkış kapsayıcısındaki arrAsset dosyasının yanına bir JSON dosyası koyar. Örneğin, bir dosya `buggy.gltf` dönüştürülürse, çıkış kapsayıcısı, dönüştürülmüş varlığın yanında adlı bir dosya içerir `buggy.info.json` `buggy.arrAsset` . Kaynak modeli, dönüştürülmüş model ve dönüştürmenin kendisi hakkında bilgiler içerir.

## <a name="example-info-file"></a>Örnek *bilgi* dosyası

Aşağıda adlı bir dosya dönüştürülürken oluşturulan örnek bir *bilgi* dosyası verilmiştir `buggy.gltf` :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Bilgi dosyasındaki bilgiler

### <a name="the-files-section"></a>*Dosyalar* bölümü

Bu bölüm, belirtilen dosya adlarını içerir.

* `input`: Kaynak dosyanın adı.
* `output`: Kullanıcı varsayılan olmayan bir ad belirtmiştir, çıkış dosyasının adı.

### <a name="the-conversionsettings-section"></a>*Conversionsettings* bölümü

Bu bölüm, model dönüştürüldüğünde belirtilen [Conversionsettings](configure-model-conversion.md#settings-file) 'in bir kopyasını içerir.

### <a name="the-inputinfo-section"></a>*Inputınfo* bölümü

Bu bölüm, kaynak dosya biçimiyle ilgili bilgileri kaydeder.

* `sourceAssetExtension`: Kaynak dosyanın dosya uzantısı.
* `sourceAssetFormat`: Kaynak dosya biçiminin açıklaması.
* `sourceAssetFormatVersion`: Kaynak dosya biçiminin sürümü.
* `sourceAssetGenerator`: Varsa, kaynak dosyayı oluşturan aracın adı.

### <a name="the-materialoverrides-section"></a>*MaterialOverrides* bölümü

Bu bölüm, dönüştürme hizmetine bir malzeme geçersiz kılma dosyası sağlandığında [malzeme üzerine yazma](override-materials.md) hakkında bilgi verir.
Aşağıdaki bilgileri içerir:
* `numOverrides`: Malzeme geçersiz kılma dosyasından okunan geçersiz kılma girişlerinin sayısı.
* `numOverriddenMaterials`: Geçersiz kılınan malzeme sayısı.

### <a name="the-inputstatistics-section"></a>*Inputstatistics* bölümü

Bu bölüm, kaynak sahneyi hakkında bilgi sağlar. Bu bölümdeki değerler ve kaynak modeli oluşturan araçtaki eşdeğer değerler arasında genellikle tutarsızlıklar olur. Bu farklar beklenmektedir, çünkü model dışa aktarma ve dönüştürme adımları sırasında değiştirilir.

* `numMeshes`: Her parçanın tek bir malzemeye başvurmasına neden olan kafes bölümlerinin sayısı.
* `numFaces`: Tüm modeldeki _üçgenin_ toplam sayısı. Ağ dönüştürme sırasında triangulated olduğunu unutmayın. Bu sayı, [standart işleme sunucusu boyutundaki](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)Çokgen sınırına katkıda bulunur.
* `numVertices`: Tüm modeldeki köşelerin toplam sayısı.
* `numMaterial`: Tüm modeldeki Toplam malzeme sayısı.
* `numFacesSmallestMesh`: Modelin en küçük ağı içindeki üçgenin sayısı.
* `numFacesBiggestMesh`: Modelin en büyük kafesteki üçgenler sayısı.
* `numNodes`: Modelin sahne grafiğindeki düğüm sayısı.
* `numMeshUsagesInScene`: Düğümlerin kafeslerin kaç kez başvurulacağını. Birden fazla düğüm aynı kafesde başvurabilir.
* `maxNodeDepth`: Sahne grafiği içindeki düğümlerin maksimum derinliği.

### <a name="the-outputinfo-section"></a>*Outputınfo* bölümü

Bu bölüm, oluşturulan çıkış hakkındaki genel bilgileri kaydeder.

* `conversionToolVersion`: Model dönüştürücünün sürümü.
* `conversionHash`: ArrAsset içinde işleme katkıda bulunan verilerin karması. Aynı dosya üzerinde yeniden çalıştırıldığında, dönüştürme hizmetinin farklı bir sonuç üretmediğini anlamak için kullanılabilir.

### <a name="the-outputstatistics-section"></a>*Outputstatistics* bölümü

Bu bölüm, dönüştürülmüş varlık tarafından hesaplanan bilgileri kaydeder.

* `numMeshPartsCreated`: ArrAsset içindeki kafeslerin sayısı. `numMeshes` `inputStatistics` Örnek, dönüştürme işleminden etkilendiğinden, bölümünde farklılık gösterebilir.
* `numMeshPartsInstanced`: ArrAsset içinde yeniden kullanılan kafeslerin sayısı.
* `recenteringOffset`: `recenterToOrigin` [Conversionsettings](configure-model-conversion.md) seçeneği etkin olduğunda, bu değer dönüştürülen modeli özgün konumuna geri taşıyabilecek çevirmadır.
* `boundingBox`: Modelin sınırları.

## <a name="deprecated-features"></a>Kullanım dışı bırakılan özellikler

Dönüştürme hizmeti dosyaları `stdout.txt` ve `stderr.txt` Çıkış kapsayıcısına yazar ve bunlar uyarı ve hataların tek kaynağıdır.
Bu dosyalar artık kullanım dışıdır. Bunun yerine lütfen bu amaçla [sonuç dosyalarını](#information-about-a-conversion-the-result-file) kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Model dönüştürme](model-conversion.md)
* [Model dönüştürmeyi yapılandırma](configure-model-conversion.md)
