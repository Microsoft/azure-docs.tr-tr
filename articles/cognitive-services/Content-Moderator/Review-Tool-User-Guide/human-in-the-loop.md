---
title: Araç kavramlarını gözden geçirmeyi öğrenin-Content Moderator
titleSuffix: Azure Cognitive Services
description: Birleştirilmiş bir AI ve insan incelemesi denetleme çabasının eşgüdümünü sağlayan bir Web sitesi olan Content Moderator gözden geçirme aracı hakkında bilgi edinin.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93146646"
---
# <a name="content-moderator-review-tool"></a>Content Moderator Inceleme aracı

Azure Content Moderator, makine öğrenimi içerik yönetimini insan incelemeleriyle birleştiren hizmetler sağlar. [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com) Web sitesi, bu hizmetlere ayrıntılı erişim sağlayan Kullanıcı dostu bir ön uçdır.

## <a name="what-it-does"></a>Ne yapar?

Makine destekli denetleme API 'Leriyle birlikte kullanıldığında [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com), içerik denetleme işleminde aşağıdaki görevleri gerçekleştirmenize olanak tanır:

- Birden çok biçimdeki (metin, resim ve video) içerik için tek bir araç kümesi kullanın.
- API sonuçlarının denetlemesi sırasında insan [incelemelerinin](../review-api.md#reviews) oluşturulmasını otomatikleştirin.
- İçerik kategorilerine veya deneyim düzeyine göre düzenlenmiş şekilde, birden çok gözden geçirme ekiplerine içerik incelemeleri atayın veya ilerletin.
- Herhangi bir kod yazmadan içeriği sıralamak ve izlemek için varsayılan veya özel mantık filtreleri ([iş akışları](../review-api.md#workflows)) kullanın.
- Content Moderator API 'Lerine ek olarak Microsoft PhotoDNA, Metin Analizi ve yüz Hizmetleri ile içerik işlemek için [bağlayıcıları](./configure.md#connectors) kullanın.
- İçerik denetleme işlemlerinizde önemli performans ölçümlerini alın.

## <a name="review-tool-dashboard"></a>Araç panosunu gözden geçir

**Pano** sekmesinde, araç içinde yapılan içerik incelemeleri için temel ölçümleri görebilirsiniz. Görüntü, metin ve video içeriği için toplam, tam ve bekleyen incelemeler sayısını görün. 

**Bekleyen incelemeler** tablosu, bekleyen veya tamamlanmış İncelemeleri olan kullanıcıların ve alt takımların dökümünü, Ayrıca kalan SLA süresini gösterir. Gözden geçirmelerine gitmek için tablodaki öğeleri seçebilirsiniz. Tablonun üzerindeki arama kutusu, sonuçları takım adına göre filtrelemenize izin verir ve **filtre** simgesi diğer ölçümlere göre filtrelemenize izin verir.

**Tamamlanan incelemeler** sekmesine geçiş yapmak, kullanıcılar ve alt takımlar tarafından işlenen veya tamamlanan toplam öğe sayısını gösterir. Bu verileri bekleyen incelemeleriyle aynı şekilde filtreleyebilirsiniz.

Panonun sağ üst köşesindeki metne tıkladığınızda her bir içerik türü için tamamlanan gözden geçirmeler sayısını raporlayan günlük kişisel ölçümler görüntülenir.

> [!div class="mx-imgBorder"]
> ![Tarayıcıda inceleme aracı panosu](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Araç kimlik bilgilerini gözden geçirme

[İnceleme aracı](https://contentmoderator.cognitive.microsoft.com)ile kaydolduğunuzda, hesabınız Için bir Azure bölgesi seçmeniz istenir. Bunun nedeni, [Gözden geçirme aracının](https://contentmoderator.cognitive.microsoft.com) Azure Content moderator Hizmetleri için ücretsiz bir deneme anahtarı üretmesine neden olur. Bu anahtar, bir REST çağrısından veya istemci SDK 'sından hizmetlerden herhangi birine erişmek için gereklidir. Anahtar ve API uç nokta URL 'nizi, **yönetici**  >  **kimlik bilgileri**' ni seçerek görüntüleyebilirsiniz.

> [!div class="mx-imgBorder"]
> ![Content Moderator kimlik bilgileri](images/settings-6-credentials.png)

## <a name="next-steps"></a>Sonraki adımlar

Inceleme aracı kaynaklarına erişme ve ayarları değiştirme hakkında bilgi edinmek için bkz. [İnceleme aracını yapılandırma](./configure.md) .