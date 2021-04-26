---
title: Azure haritalar çizim dönüştürme hataları ve uyarıları
description: Azure haritalar dönüştürme hizmetini kullanırken karşılaşabileceğiniz dönüştürme hataları ve uyarıları hakkında bilgi edinin. Bazı örneklerle hataları ve uyarıları çözme önerilerini okuyun.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cecc19f0984ce1801d50e5cbda73e98a01e2825b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906225"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Çizim dönüştürme hataları ve uyarıları

[Azure haritalar dönüştürme hizmeti](/rest/api/maps/conversion) , karşıya yüklenen çizim paketlerini harita verilerine dönüştürmenize olanak sağlar. Çizim paketleri, [Çizim paketi gereksinimlerine](drawing-requirements.md)bağlı olmalıdır. Bir veya daha fazla gereksinim karşılanmazsa, dönüştürme hizmeti hata veya uyarı döndürür. Bu makalede, dönüştürme hatası ve uyarı kodları listelenmektedir ve bunların nasıl çözüleceği ile ilgili öneriler sunulur. Ayrıca, dönüştürme hizmetinin bu kodları döndürmesini sağlayan bazı çizimler örneklerini de sağlar.

Dönüştürme uyarıları varsa dönüştürme hizmeti başarılı olur. Ancak, tüm uyarıları gözden geçirmeniz ve çözmeniz önerilir. Uyarı, dönüştürmenin bir kısmının yoksayıldığını veya otomatik olarak düzeltildiği anlamına gelir. Uyarıları çözememesi, ikinci süreçlerdeki hatalara neden olabilir.

## <a name="general-warnings"></a>Genel uyarılar

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*GeometryWarning için açıklama*

Çizim geçersiz bir varlık içerdiğinde bir **Geometrywarning** oluşur. Geçersiz varlık geometrik kısıtlamalara uymayan bir varlıktır. Geçersiz bir varlığa örnek olarak, yalnızca kapalı geometriyi destekleyen bir katmanda, kendinden kesişen bir çokgen veya kapalı olmayan bir çoklu çizgi verilebilir.

Dönüştürme hizmeti geçersiz bir varlıktan eşleme özelliği oluşturamıyor ve bunun yerine yok sayıyor.

#### <a name="examples-for-geometrywarning"></a>*GeometryWarning örnekleri*

* Aşağıdaki iki görüntüde otomatik olarak kesişen çokgenler örnekleri gösterilmektedir.

     ![Kendinden kesişen bir çokgen örneği, örnek bir örnek.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Kendinden kesişen bir çokgen örneği, örneğin iki.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Kapalı olmayan bir çoklu çizgiyi gösteren bir resim aşağıda verilmiştir. Katmanın yalnızca kapalı geometriyi desteklediğini varsayın.

    ![Kapalı olmayan bir çoklu çizgi örneği](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*GeometryWarning 'ı çözme*

Geometrik kısıtlamaları takip ettiğini doğrulamak için her varlık için **Geometrywarning** 'ı inceleyin.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*UnexpectedGeometryInLayer için açıklama*

Çizim, belirli bir katman için beklenen geometri türüyle uyumsuz bir geometri içerdiğinde bir **unexpectedGeometryInLayer** uyarısı oluşur. Dönüştürme hizmeti bir **unexpectedGeometryInLayer** uyarısı döndürdüğünde, bu geometriyi yoksayar.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*UnexpectedGeometryInLayer için örnek*

Aşağıdaki görüntüde kapalı olmayan bir çoklu çizgi gösterilmektedir. Katmanın yalnızca kapalı geometriyi desteklediğini varsayın.

![Kapalı olmayan bir çoklu çizgi örneği](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*UnexpectedGeometryInLayer nasıl düzeltileceğini*

Her **unexpectedGeometryInLayer** uyarısını inceleyin ve uyumsuz geometriyi uyumlu bir katmana taşıyın. Diğer katmanların hiçbiriyle uyumlu değilse, kaldırılması gerekir.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*UnsupportedFeatureRepresentation için açıklama*

Çizim desteklenmeyen bir varlık türü içerdiğinde **Unsupportedfeaturerepresentation** uyarısı oluşur.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*UnsupportedFeatureRepresentation örneği*

Aşağıdaki görüntüde, desteklenmeyen bir varlık türü, bir etiket katmanında çok satırlı bir metin nesnesi olarak gösterilmiştir.
  
![Etiket katmanında çok satırlı bir metin nesnesi örneği](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*UnsupportedFeatureRepresentation nasıl düzeltilir?*

DWG dosyalarınızın yalnızca desteklenen varlık türlerini içerdiğinden emin olun. Desteklenen türler, [Çizim paketi gereksinimleri makalesindeki çizim dosyaları gereksinimleri bölümünde](drawing-requirements.md#drawing-package-requirements)listelenir.

### <a name="automaticrepairperformed"></a>**otomatik olarak yapılan automaticrepair**

#### <a name="description-for-automaticrepairperformed"></a>*Automaticrepairiçin açıklama*

Dönüştürme hizmeti geçersiz geometriyi otomatik olarak onardığınızda **Automaticrepairgerçekleştirildi** uyarısı oluşur.

#### <a name="examples-for-automaticrepairperformed"></a>*Otomatik olarak yapılan Automaticrepairörnekleri*

* Aşağıdaki görüntüde, dönüştürme hizmetinin otomatik olarak kesişen çokgeni geçerli geometriye nasıl onarıldığı gösterilmektedir.

    ![Kendinden kesişen bir çokgen onarıldı](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* Aşağıdaki görüntüde, dönüştürme hizmeti 'nin kapalı bir çoklu çizgi oluşturmak için kapalı bir çoklu çizginin ilk ve son köşesinin nasıl bir kez yapıldığını ve ilk ve son köşenin 1 oranından küçük olduğunu gösterir.  

    ![Bir yapışbağlantılı çoklu çizgi örneği](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* Aşağıdaki görüntüde, yalnızca kapalı PolyLines destekleyen bir katmanda, dönüştürme hizmeti kapalı olmayan birden çok PolyLines onarılan gösterilmektedir. Kapalı olmayan PolyLines 'ı atmaktan kaçınmak için, hizmet onları tek bir kapalı çoklu çizgiye ayırır.

    ![Kapalı olmayan Polylines, tek bir kapalı çoklu çizgi halinde birleştirilmiş örnek](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Otomatik olarak yapılan Automaticrepairnasıl düzeltilir?*

Otomatik olarak **gerçekleştirilen** bir uyarıyı onarmak için aşağıdaki eylemleri gerçekleştirin:

1. Her uyarının geometrisini ve belirli uyarı metnini inceleyin.
2. Otomatik onarımın doğru olup olmadığını belirleme.
3. Onarım doğruysa devam edin. Aksi takdirde, tasarım dosyasına gidin ve uyarıyı el ile çözümleyin.

>[!TIP]
>Gelecekte bir uyarıyı gizlemek için özgün çizimde değişiklikler yapıp özgün çizim onarılan çizimle eşleşir.

## <a name="manifest-warnings"></a>Bildirim uyarıları

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*RedundantAttribution için açıklama*

Bildirim gereksiz veya çakışan nesne özellikleri içerdiğinde **redundantAttribution** uyarısı oluşur.

#### <a name="examples-for-redundantattribution"></a>*RedundantAttribution için örnekler*

* Aşağıdaki JSON kod parçacığı aynı olan iki veya daha fazla `unitProperties` nesne içeriyor `name` .

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* Aşağıdaki JSON parçacığında, iki veya daha fazla `zoneProperties` nesne aynı `name` .

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*RedundantAttribution nasıl düzeltileceğini*

Bir **redundantAttribution* uyarısını onarmak için, gereksiz veya çakışan nesne özelliklerini kaldırın.

### <a name="manifestwarning"></a>**manifestWarning uyarısı**

#### <a name="description-for-manifestwarning"></a>*ManifestWarning için açıklama*

Bildirim, dönüştürme sırasında kullanılmamış unitProperties veya zoneProperties nesneleri içerdiğinde bir **Manifestwarning** oluşur.

#### <a name="examples-for-manifestwarning"></a>*ManifestWarning örnekleri*

* Bildirim, `unitProperties` bir `unitName` katmanda eşleşen etiketi olmayan bir nesnesi içeriyor `unitLabel` .

* Bildirim, `zoneProperties` bir `zoneName` katmanda eşleşen etiketi olmayan bir nesnesi içeriyor `zoneLabel` .

#### <a name="how-to-fix-manifestwarning"></a>*ManifestWarning 'ı çözme*

Bir **Manifestwarning**'ı onarmak için, kullanılmayan `unitProperties` veya `zoneProperties` nesneyi bildirimden kaldırın veya bir birim/bölge etiketini, dönüştürme sırasında Özellikler nesnesinin kullanılması için çizime ekleyin.

## <a name="wall-warnings"></a>Duvar uyarıları

### <a name="walloutsidelevel"></a>**Walloutsıdelelevel**

#### <a name="description-for-walloutsidelevel"></a>*Walloutsıdelelevel için açıklama*

Bir düzey ana hat sınırlarının dışında bir duvar geometrisi içerdiğinde, **Walloutsıdelelevel** uyarısı oluşur.

#### <a name="example-for-walloutsidelevel"></a>*Walloutsıdelelevel örneği*

* Aşağıdaki görüntüde, kırmızı, sarı düzey sınırının dışında bir iç duvar gösterilmektedir.

    ![Düzey sınırının dışında iç duvar örneği](./media/drawing-conversion-error-codes/wall-outside-level.png)

* Aşağıdaki görüntüde, sarı düzey sınırının dışında, kırmızı renkte bir dış duvar gösterilmektedir.

    ![Düzey sınırının dışında dış duvar örneği](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Walloutsıdelelevel 'ı çözme*

Bir **Walloutsıdeley** uyarısını onarmak için, tüm duvarları dahil etmek için düzey geometriyi genişletin. Ya da, duvar sınırlarını düzey sınırın içine sığacak şekilde değiştirin.

## <a name="unit-warnings"></a>Birim uyarıları

### <a name="unitoutsidelevel"></a>**Unitoutsideletik**

#### <a name="description-for-unitoutsidelevel"></a>*Unitoutsidelelevel için açıklama*

Çizim, düzey ana hat sınırları dışında birim geometrisi içerdiğinde, bir **Unitoutsidelelevel** uyarısı oluşur.

#### <a name="example-for-unitoutsidelevel"></a>*Unitoutsidelelevel örneği*

 Aşağıdaki görüntüde, birim geometrisi kırmızı, sarı düzeyi sınırının sınırlarını aşıyor.

 ![Düzey sınırını aşan birim örneği](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Unitoutsideletik 'ı çözme*

Bir **Unitoutsideley** uyarısını onarmak için, tüm birimleri dahil etmek için düzey sınırını genişletin. Veya, birim geometrisini düzey sınırı içine sığacak şekilde değiştirin.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*PartiallyOverlappingUnit için açıklama*

Çizim, başka bir birim geometrisi üzerinde kısmen çakışan bir birim geometrisi içerdiğinde bir **Partıallyoverlappingunit** uyarısı oluşur. Dönüştürme hizmeti çakışan birimleri atar.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Örnek senaryolar partiallyOverlappingUnit*

Aşağıdaki görüntüde çakışan birim kırmızı renkle vurgulanır. `UNIT110` ve `HALLWAY` atılır.

![Çakışan birim örneği](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*PartiallyOverlappingUnit nasıl düzeltilir?*

Bir **Partıallyoverlappingunit** uyarısını onarmak için, her bir kısmen çakışan birimi diğer herhangi bir birimle çakışmayacak şekilde yeniden çizin.

## <a name="door-warnings"></a>Kapı uyarıları

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*DoorOutsideLevel için açıklama*

Çizim, düzey geometrisinin sınırları dışında bir kapı geometrisi içerdiğinde bir **doorOutsideLevel** uyarısı oluşur.

#### <a name="example-for-dooroutsidelevel"></a>*DoorOutsideLevel için örnek*

Aşağıdaki görüntüde, kırmızı renkle vurgulanmış olan kapı geometrisi, sarı düzey sınırı ile çakışıyor.

![Düzey sınır ile çakışan bir kapı örneği](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*DoorOutsideLevel nasıl düzeltileceğini*

Bir **doorOutsideLevel** uyarısını onarmak için, kapı geometrisini, düzey sınırlarının içinde olacak şekilde yeniden çizin.

## <a name="zone-warnings"></a>Bölge uyarıları

### <a name="zonewarning"></a>**Bölge Uyarısı**

#### <a name="description-for-zonewarning"></a>*Bölge Uyarısı için açıklama*

Bölgede bir etiket yoksa bölge **uyarısı** oluşur. Dönüştürme hizmeti etiket. l olmayan bir bölgeyi atar

#### <a name="example-for-zonewarning"></a>*Bölge Uyarısı için örnek*

Aşağıdaki görüntüde etiket içermeyen bir bölge gösterilmektedir.

![Bölge örneği bir etiket içermez](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Bölge uyarısını çözme*

Bir bölge **uyarısını** onarmak için, her bir bölgenin tek bir etikete sahip olduğunu doğrulayın.

## <a name="label-warnings"></a>Etiket uyarıları

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*LabelWarning için açıklama*

Çizim belirsiz veya çelişkili Etiketler özelliği içerdiğinde **Labelwarning** oluşur.

Aşağıdaki nedenlerden biri veya birkaçı nedeniyle bir **Labelwarning** oluşur:

* Birim etiketi herhangi bir birimde değil.
* Bölge etiketi herhangi bir bölgede değildir.
* Bölge etiketi iki veya daha fazla bölgenin içindedir.

#### <a name="example-for-labelwarning"></a>*LabelWarning örneği*

Aşağıdaki görüntüde, iki bölgenin içindeki bir etiket gösterilmektedir.

![İki bölgenin içindeki bir etiket örneği ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*LabelWarning 'ı çözme*

Bir **Labelwarning**'ı onarmak için şunları doğrulayın:

* Tüm birim etiketleri birim içindedir.
* Tüm bölge etiketleri bölgeler içindedir.
* Tüm bölge etiketleri tek bir bölgede ve yalnızca bir bölgedir.

## <a name="drawing-package-errors"></a>Paket hatalarını çizme

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*İnvalidArchiveFormat için açıklama*

Çizim paketi GZIP veya 7-zip gibi geçersiz bir arşiv biçiminde olduğunda bir **invalidArchiveFormat** hatası oluşur. Yalnızca ZIP Arşivi biçimi desteklenir.

ZIP arşivi boşsa bir **invalidArchiveFormat** hatası da oluşur.

#### <a name="how-to-fix-invalidarchiveformat"></a>*İnvalidArchiveFormat nasıl düzeltileceğini*

Bir **invalidArchiveFormat** hatasını onarmak için şunları doğrulayın:

* Arşiv dosyanızın adı _. zip_' de sona erer.
* ZIP arşiv verileri içeriyor.
* ZIP arşivinizi açabilirsiniz.

### <a name="invaliduserdata"></a>**ınvaliduserdata**

#### <a name="description-for-invaliduserdata"></a>*Invaliduserdata açıklaması*

Dönüştürme hizmeti bir kullanıcı veri nesnesini depolamadan okuyamediğinde **ınvaliduserdata** hatası oluşur.

#### <a name="example-scenario-for-invaliduserdata"></a>*Invaliduserdata için örnek senaryo*

Hatalı bir parametre içeren bir çizim paketini karşıya yüklemeye çalıştınız `udid` .

#### <a name="how-to-fix-invaliduserdata"></a>*Invaliduserdata nasıl düzeltilir?*

Bir **ınvaliduserdata** hatasını onarmak için şunları doğrulayın:

* Karşıya yüklenen paket için doğru bir şekilde sağladınız `udid` .
* Azure haritalar Oluşturucu (Önizleme), çizim paketini karşıya yüklemek için kullandığınız Azure Maps hesabı için etkinleştirildi.
* Dönüştürme hizmetine yönelik API isteği, çizim paketini karşıya yüklemek için kullandığınız Azure Maps hesabına abonelik anahtarını içerir.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*DwgError için açıklama*

Çizim paketi karşıya yüklenen ZIP arşivinde bir veya daha fazla DWG dosyası ile ilgili bir sorun içerdiğinde bir **Dwgerror** .

Çizim paketi geçersiz veya bozuk olduğu için açılamayan bir DWG dosyası içerdiğinde **Dwgerror** oluşur.

* Bir DWG dosyası geçerli bir AutoCAD DWG dosya biçimi çizimi değildir.
* Bir DWG dosyası bozuk.
* Bir DWG dosyası _manifest.js_ dosyasında listelenir, ancak ZIP arşivi tarafından eksiktir.

#### <a name="how-to-fix-dwgerror"></a>*DwgError 'ı çözme*

Bir **Dwgerror**'ı onarmak için, dosyadaki _manifest.js_ inceleyin ve şunları doğrulayın:

* ZIP arşivinizdeki tüm DWG dosyaları geçerli AutoCAD DWG biçim çizimlerinin her birini AutoCAD 'de açar. Tüm geçersiz çizimleri kaldırın veya onarın.
* _manifest.jsÜZERINDEKI_ DWG dosyalarının LISTESI, ZIP ARŞIVI içindeki DWG dosyalarıyla eşleşir.

## <a name="manifest-errors"></a>Bildirim hataları

### <a name="invalidjsonformat"></a>**ınvalidjsonformat**

#### <a name="description-for-invalidjsonformat"></a>Invalidjsonformat için açıklama

Dosya _manifest.js_ okunamıyor bir **ınvalidjsonformat** hatası oluşur.

JSON biçimlendirme veya sözdizimi hataları nedeniyle _manifest.json_file okunamıyor. JSON biçimi ve sözdizimi hakkında daha fazla bilgi için bkz [. JavaScript nesne gösterimi (JSON) veri değişim biçimi](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*Invalidjsonformat 'ı çözme*

Bir **ınvalidjsonformat** hatasını düzeltmek için, JSON hatalarını tespit etmek ve çözmek IÇIN bir JSON Bağlayıcısı kullanın.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*MissingRequiredField açıklaması*

Dosyadaki _manifest.js_ gerekli verileri eksikse bir **missingrequiredfield** hatası oluşur.

#### <a name="how-to-fix-missingrequiredfield"></a>*MissingRequiredField 'ı çözme*

**Missingrequiredfield** hatasını onarmak için bildirimin tüm gerekli özellikleri içerdiğini doğrulayın. Gerekli bildirim nesnesinin tam listesi için bkz [. çizim paketi gereksinimleri içindeki bildirim bölümü](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*MissingManifest için açıklama*

Bulunamayan **hata kodu, ZIP** arşivinde dosya _manifest.js_ oluşur.

**Missingmanifest** hatası aşağıdaki nedenlerden biri veya birkaçı nedeniyle oluşur:

* Dosyadaki _manifest.js_ yanlış yazılmıştır.
* _Üzerindemanifest.js_ eksik.
* _manifest.js_ , ZIP arşivi kök dizininin içinde değil.

#### <a name="how-to-fix-missingmanifest"></a>*MissingManifest nasıl düzeltilir?*

Bir **Missingmanifest** hatasını onarmak için, arşiv 'ın, ZIP arşivi kök düzeyinde _manifest.js_ adlı bir dosyaya sahip olduğunu doğrulayın.

### <a name="conflict"></a>**çakışma**

#### <a name="description-for-conflict"></a>*Çakışma açıklaması*

Dosyadaki _manifest.js_ çakışan bilgiler içerdiğinde **Çakışma** hatası oluşur.

#### <a name="example-scenario-for-conflict"></a>*Çakışma için örnek senaryo*

Aynı düzey sıra sayısı ile birden fazla düzey tanımlandığında, dönüştürme hizmeti bir **Çakışma** hatası döndürür. Aşağıdaki JSON kod parçacığında aynı sıra ile tanımlanmış iki düzey gösterilmektedir.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Çakışmayı çözme*

Bir **Çakışma** hatasını onarmak için _üzerindemanifest.js_ inceleyin ve çakışan bilgileri kaldırın.

### <a name="invalidgeoreference"></a>**ınvalidgeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Invalidgeoreference için açıklama*

Dosyasındaki bir _manifest.js_ geçersiz bir georeference Içerdiğinde **ınvalidgeoreference** hatası oluşur.

Aşağıdaki nedenlerden biri veya birkaçı nedeniyle **ınvalidgeoreference** hatası oluşur:

* Kullanıcı, Aralık dışında bir enlem veya boylam değerine coğrafi olarak başvuruyor.
* Kullanıcı, Aralık dışında bir döndürme değerine coğrafi olarak başvuruyorlar.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Invalidgeoreference için örnek senaryo*

Aşağıdaki JSON parçacığında, enlem üst sınırın üzerinde.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Invalidgeoreference nasıl düzeltilir*

**Invalidgeoreference** hatasını onarmak için, coğrafi başvurulan değerlerin Aralık dahilinde olduğunu doğrulayın.

>[!IMPORTANT]
>GeoJSON 'da koordinatlar sırası boylam ve enlem 'dir. Doğru sırayı kullanmıyorsanız, yanlışlıkla aralığın dışında bir enlem veya boylam değeri yazabilirsiniz.

## <a name="wall-errors"></a>Duvar hataları

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*WallError için açıklama*

Bir duvar özelliği oluşturulmaya çalışılırken, çizim bir hata içerdiğinde **Wallerror** oluşur.

#### <a name="example-scenario-for-wallerror"></a>*WallError için örnek senaryo*

Aşağıdaki görüntüde herhangi bir birimle örtüşmeyen bir duvar özelliği görüntülenmektedir.

![Herhangi bir birimi örtüşmeyen duvar özelliği örneği](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*WallError 'ı çözme*

Bir **Wallerror** hatasını onarmak için, duvarı en az bir birimle örtüşen şekilde yeniden çizin. Veya, duvardan çakışan yeni bir birim oluşturun.

## <a name="vertical-penetration-errors"></a>Dikey sızma hataları

### <a name="verticalpenetrationerror"></a>**Verticalpenetoytionerror**

#### <a name="description-for-verticalpenetrationerror"></a>*Verticalpenetoytionerror için açıklama*

Çizim belirsiz bir dikey penme özelliği içerdiğinde **Verticalpenetkılationerror** oluşur.

Aşağıdaki nedenlerden biri veya birkaçı nedeniyle **Verticalpenetoytionerror** oluşur:

* Çizim, üzerinde veya altında herhangi bir düzeyde dikey bir oyma alanı bulunmayan dikey bir peni alanı içeriyor.
* Çizim paketi, iki veya daha fazla dikey sızma özelliği olan bir düzeyi içerir. Bu özellik, her ikisi de doğrudan veya altındaki başka bir düzeydeki tek bir dikey penme özelliğinden çakışıyor.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Verticalpenetoytionerror için örnek senaryo*

Aşağıdaki görüntüde, üzerinde veya altında bulunan düzeyler üzerinde çakışan dikey penmeyen alanlar bulunmayan dikey bir penetasyon alanı gösterilmektedir.

![Dikey bir sızma örneği 1](./media/drawing-conversion-error-codes/vrt-2.png)

Aşağıdaki görüntüde, bitişik bir düzeyde birden fazla dikey penme alanından çakışan dikey bir penetasyon alanı gösterilmektedir.

![Dikey bir sızma örneği 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Verticalpenetoytionerror nasıl düzeltilir?

Bir **Verticalpenetiz hata** hatasını onarmak Için, [Çizim paketi gereksinimleri](drawing-requirements.md) makalesinde dikey bir penetasyon özelliği kullanma hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure haritalar çizim hatası Görselleştiricisini Kullanma](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Inkapıeşleme için Oluşturucu (Önizleme)](creator-indoor-maps.md)