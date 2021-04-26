---
title: Dönüştürme için dosyaları yerleştirme
description: Giriş kapsayıcısına dosyaların ne kadar en iyi şekilde yerleştirileceği hakkında öneriler.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 9a094755dfb9381b1e4d6abdf1c0e6342d7427c1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308448"
---
# <a name="laying-out-files-for-conversion"></a>Dönüştürme için dosyaları yerleştirme

Bir varlığı doğru bir şekilde işlemek için, dönüştürme hizmetinin tüm giriş dosyalarını bulması gerekir.
Bunlar, dönüştürülen ana varlık dosyasından ve genellikle varlık dosyasındaki yolların başvurduğu bazı diğer dosyalara oluşur.
Bir varlığı dönüştürme isteği, dönüştürme hizmetinin bu dosyaları nasıl bulduğunu belirleyen iki parametre verilmesini sağlar: `settings.inputLocation.blobPrefix` (isteğe bağlı) ve `settings.inputLocation.relativeInputAssetPath` .
Bunlar, [dönüştürme REST API](conversion-rest-api.md) sayfasında tamamen belgelenmiştir.
Dosyaları yerleştirme amacıyla, dikkat edilmesi gereken önemli şey, `BlobPrefix` varlığı işlerken dönüştürme hizmeti için kullanılabilen tüm dosya kümesini belirler.

> [!Note]
> Hizmet, girişin altındaki tüm dosyaları indirir. BlobPrefix. Hizmet sorunlarını önlemek için dosya adlarının ve yolların [Windows yol uzunluğu sınırlarını](https://docs.microsoft.com/windows/win32/fileio/maximum-file-path-limitation) aşmadığından emin olun. 

## <a name="placing-files-so-they-can-be-found"></a>Dosyalar bulunabilmeleri için yerleştiriliyor

Bir kaynak varlık dış dosyaları kullandıklarında, bu dosyalara yönelik yollar varlık içinde depolanır.
Dönüştürme hizmeti, varlığın özgün dosya sisteminden farklı bir dosya sisteminde bu yolları yorumlamak için gereklidir.
Yollar göreli yollar olarak depolanırsa ve kaynak varlık ile başvurduğu dosya arasındaki göreli konum değişmeden olursa, dönüştürme hizmetinin başvurulan dosyayı bulması kolaylaşır.

> [!Note]
> Dosyaların göreli konumlarının varlık oluşturulduğu sırada oldukları gibi olması için, dosyaları giriş kapsayıcısına yerleştirmeniz önerilir.

> [!Note]
> Göreli yolları taşıyan varlıklar oluşturmayı tercih edin.
> [3ds Max için malzeme ayarlama](../../tutorials/modeling/3dsmax-material-setup.md) hakkında öğretici, bir varlığın göreli yollar kullanması Için en fazla 3ds örneği sağlar.

## <a name="finding-textures"></a>Dokuları bulma

Varlıkların üretilebilmesinin pek çok yolu nedeniyle, dönüştürme hizmeti esnek olmalıdır.
Özellikle, varlık içindeki yolların ve dokuların konumunun tam olarak eşleşmediği durumları işlemek zorunda değildir.
Bu yollar hiçbir zaman dönüştürme hizmeti tarafından kullanılan dosya sistemiyle eşleşmediğinden, varlıkların mutlak yollar içeren bir örneği oluşturulur.
Bu durumu diğerleri arasında işlemek için dokuları bulmak için en iyi çaba yaklaşımını kullanıyoruz.

Dokuları bulma algoritması şu şekildedir: bir varlık içinde depolanan bir yol verildiğinde, kaynak varlığın konumundan bir göreli yol olarak kullanıldığında, var olan bir dosyayı hedeflediğinde, en uzun alt yol sonekini bulur.
Böyle bir alt yol (yolun tamamı dahil) bir dosyayı hedeflerse, doku eksik olarak kabul edilir.

Aşağıdaki contrived dosya sistemini göz önünde bulundurun: 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
MyAsset. fbx göreli yol ile bir dokuya başvuruyorsa `..\Textures\MyAssetTextures\myTexture.png` , dönüştürme hizmeti d dosyasını kullanır. Aslında, dosya E 'nin, en uzun son ekte bulunduğu bu yana bir, C ve o dosya tarafından tercih edildiği durumlarda, bu dosyalardan herhangi birini kullanabilir.
`Textures\myTexture.png`Depolanan yolun herhangi bir sonekinin parçası olmadığından b ve D dosyaları hiçbir zaman kullanılmaz.
Varlık yollar içeriyorsa `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` veya varsa `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` , dönüştürme hizmeti a ve c dosyalarını bulabiliyor (c üzerinde tercih ediliyor). Ancak E bu şekilde bu şekilde bulunamamıştır ve dosyanın yeniden konumlandırılması gerekir.
Bu, varlıkların yanına dokular klasörü taşınarak düzeltilebilir.

> [!Note]
> Dokular bulunamazsa, varlığın dokuları içeren bir alt ağacın eşdüzey bir eşdüzey olduğundan emin olmak olası bir çözüm.

## <a name="next-steps"></a>Sonraki adımlar

- [Model dönüştürme](model-conversion.md)
