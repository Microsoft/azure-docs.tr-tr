---
title: Çalışma alanı ve proje nedir? -Özel çevirici
titleSuffix: Azure Cognitive Services
description: Bu makalede, bir çalışma alanı ile proje arasındaki farklar ile özel çevirmen hizmeti için Proje kategorileri ve Etiketler açıklanmaktadır.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d2f7903fa85c645357e46a753d1cb043e0893254
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647312"
---
# <a name="what-is-a-custom-translator-workspace"></a>Özel çevirmen çalışma alanı nedir?

Çalışma alanı, özel çeviri sisteminizi oluşturmaya ve oluşturmaya yönelik bir çalışma alanıdır. Bir çalışma alanı birden fazla proje, model ve belge içerebilir. Özel çeviricisinde yaptığınız tüm işler belirli bir çalışma alanının içindedir.

Çalışma alanı sizin ve çalışma alanınıza davet ettiğiniz kişiler için özeldir. Davet edilmemiş kişilerin çalışma alanınızın içeriğine erişimi yok. Çalışma alanınıza istediğiniz kadar kişiyi davet edebilir ve bunların her zaman erişimini değiştirebilir veya kaldırabilirsiniz. Ayrıca, yeni bir çalışma alanı da oluşturabilirsiniz. Varsayılan olarak, bir çalışma alanı diğer çalışma alanlarınızın içindeki herhangi bir proje veya belge içermez.

## <a name="what-is-a-custom-translator-project"></a>Özel çevirmen Projesi nedir?

Proje, bir model, belge ve test için bir sarmalayıcıdır. Her proje, doğru dil çiftine sahip olan bu çalışma alanına yüklenen tüm belgeleri otomatik olarak içerir. Örneğin, hem bir Ingilizce-Ispanyolca projesi hem de Ispanyolca ile Ingilizce proje varsa, aynı belgeler her iki projeye de dahil edilir. Her projenin, çevirileri için [v3 API 'si](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) sorgulanırken kullanılan bir KategoriNo vardır. CategoryID, özel çevirmenle oluşturulmuş özelleştirilmiş bir sistemden Çeviriler almak için kullanılır.

## <a name="project-categories"></a>Proje kategorileri

Kategori, projeniz için, kullanmak istediğiniz terminolojinin ve stilin alanı olan etki alanını tanımlar. Belgeleriniz için en uygun kategoriyi seçin. Bazı durumlarda, kategori seçiminiz özel çeviricisinin davranışını doğrudan etkiler.

İki temel model kümesi vardır. Bunlar genel ve teknolojisidir. Kategori **teknolojisi** seçilirse teknoloji temel modelleri kullanılacaktır. Diğer tüm kategori seçimleri için genel taban çizgisi modelleri kullanılır. Teknoloji temel modeli teknoloji etki alanında iyi bir şekilde görünür, ancak çeviri için kullanılan cümleler teknoloji etki alanında yoksa daha düşük kalite gösterir. Müşterilerin kategori teknolojisini seçmesini, yalnızca cümleler teknoloji etki alanının içinde tamamen düşeceğini öneririz.

Aynı çalışma alanında, farklı kategorilerde aynı dil çifti için projeler oluşturabilirsiniz. Özel çevirmen, aynı dil çifti ve kategorisiyle yinelenen bir proje oluşturulmasını engeller. Projenize bir etiket uygulamak, bu kısıtlamayı önlemenize olanak sağlar. Birden çok istemci için çeviri sistemleri oluşturmadığınız sürece etiketleri kullanmayın; projenize benzersiz bir etiket eklemek, KategoriNo projeleriniz içinde yansıtılcaktır.

## <a name="project-labels"></a>Proje etiketleri

Özel çevirici, projenize bir proje etiketi atamanıza izin verir. Proje etiketi, aynı dil çiftine ve kategoriye sahip birden fazla proje arasında ayrım yapar. En iyi uygulama olarak, gerekli olmadığı sürece proje etiketlerini kullanmaktan kaçının.

Proje etiketi CategoryID 'nin bir parçası olarak kullanılır. Proje etiketi bırakılır veya projeler genelinde aynı şekilde ayarlanırsa, aynı kategoriye ve *farklı* dil çiftlerine sahip projeler aynı kategorili olur. Bu yaklaşım, sizin veya müşterinizin, her proje için benzersiz olan bir KategoriNo hakkında endişelenmeden metin Çeviricisi API 'sini kullanırken diller arasında geçiş yapmasına olanak sağladığından avantajlıdır.

Örneğin, teknoloji etki alanındaki çevirileri Ingilizce 'den Fransızca 'ya ve Fransızca 'dan Ingilizce 'ye etkinleştirmek istiyorsam iki proje oluştururdum: bir adet Ingilizce-\> Fransızca ve Fransızca\> Ingilizce için bir tane. Her ikisi için de aynı kategoriyi (teknoloji) belirtir ve proje etiketini boş bırakın. Her iki projenin CategoryID 'si de eşleşir, bu nedenle, CategoryID 'umu değiştirmek zorunda kalmadan hem Ingilizce hem de Fransızca çevirileri için API 'yi sorgulayabilir.

Bir dil hizmet sağlayıcısıysanız ve aynı kategori ve Dil çiftini koruyan farklı modellerle birden çok müşteriye hizmet vermek istiyorsanız, müşteriler arasında ayrım yapmak için bir proje etiketi kullanılması, bir karar veren bir karardır.

## <a name="next-steps"></a>Sonraki adımlar

- [Eğitim ve model](training-and-model.md) hakkında bilgi edinmek için bkz. bir çeviri modelini verimli bir şekilde oluşturma.
