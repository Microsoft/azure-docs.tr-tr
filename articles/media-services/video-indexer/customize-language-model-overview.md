---
title: Video Indexer dil modelini özelleştirme-Azure
titleSuffix: Azure Media Services
description: Bu makale, Video Indexer dil modeli nedir ve özelleştirmeyi özelleştirmek için bir genel bakış sunar.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b096b9352be65033f2fb782b118e815dc16b43b6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838312"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Video Indexer bir dil modelini özelleştirme

Video Indexer, Microsoft [özel konuşma tanıma hizmeti](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)ile tümleştirme yoluyla otomatik konuşma tanımayı destekler. Dil modelini uyarlama metnini karşıya yükleyerek özelleştirebilirsiniz. Bu, alt yapısının benzediğini istediğiniz etki alanından metin. Modelinize bir kez eğtikten sonra, uyarlama metninde görüntülenen yeni kelimeler tanınacaktır, bu, varsayılan telaffuz varsayıldığında ve dil modelinin yeni olası sözcük dizilerini öğrenmesi gerekir. Özel dil modelleri Ingilizce, Ispanyolca, Fransızca, Almanca, Italyanca, Çince (Basitleştirilmiş), Japonca, Rusça, Brezilya Portekizcesi, Hintçe ve Korece için desteklenir. 

Örnek olarak, "Kubernetes" (Azure Kubernetes hizmeti bağlamında) gibi yüksek ölçüde özgü bir sözcük alalım. Word Video Indexer yeni bir değer olduğundan, "Topluluklar" olarak tanınır. Modeli, "Kubernetes" olarak tanımak için eğmeniz gerekir. Diğer durumlarda, kelimeler vardır ancak dil modeli, belirli bir bağlamda görünmesini beklemiyordu. Örneğin, "kapsayıcı hizmeti", özel olmayan bir dil modelinin belirli bir sözcük kümesi olarak tanıyacağı 2 sözcüklü bir dizi değildir.

Metin dosyasındaki bir listede bağlam olmadan sözcük yükleme seçeneğiniz vardır. Bu, kısmi uyarlama olarak değerlendirilir. Alternatif olarak, daha iyi bir uyarlama için içeriklerinize ilişkin belge veya Tümcelerin metin dosyalarını karşıya yükleyebilirsiniz.

Video Indexer API 'Leri veya Web sitesini, bu konunun [sonraki adımlar](#next-steps) bölümündeki konular bölümünde açıklandığı gibi özel dil modelleri oluşturmak ve düzenlemek için kullanabilirsiniz.

## <a name="best-practices-for-custom-language-models"></a>Özel dil modelleri için en iyi uygulamalar

Video Indexer, en iyi şekilde bilgi edinmek için, sözcük birleşimlerinin olasılıklarına göre öğreniyor:

* Söyleyecek kadar gerçek tümceler örneği sağlayın.
* Satır başına yalnızca bir cümle koyun, daha fazlasını yapın. Aksi takdirde sistem cümleler genelinde olasılıkların öğrenmesine sahip olur.
* Kelimeyi diğerlerine karşı artırmak için bir sözcük olarak bir kelime koymak normaldir, ancak sistem tam cümlelerden en iyi şekilde öğrenir.
* Mümkünse, yeni sözcüklere veya kısaltmaya giriş yaparken, sisteme mümkün olduğunca fazla bağlam sağlamak için tam bir tümcede birçok kullanım örneği verin.
* Birkaç uyarlama seçeneği koymaya çalışın ve nasıl çalıştığını görün.
* Aynı cümlenin aynısını birden çok kez tekrarlamayı önleyin. Girişin geri kalanına göre sapma oluşturabilir.
* Seyrek görülen sembolleri (~, # @% &) atıldıklarından hariç kullanmayın. Göründükleri tümceler da atılır.
* Yüz binlerce cümle gibi çok büyük girişler yerleştirmekten kaçının, ancak bunu yapmak, artırma etkisini de desteklenecektir.

## <a name="next-steps"></a>Sonraki adımlar

[API 'Leri kullanarak dil modelini özelleştirme](customize-language-model-with-api.md)

[Dil modelini Web sitesini kullanarak özelleştirme](customize-language-model-with-website.md)
