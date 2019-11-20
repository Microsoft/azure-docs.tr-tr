---
title: Project Acoustics hakkında sık sorulan sorular
titlesuffix: Azure Cognitive Services
description: Bu sayfa, yükleme yönergeleri ve hazırlama işlemi de dahil olmak üzere Project Acoustics hakkında sık sorulan soruların yanıtlarını sağlar.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: f0484fc9fc2af5514ba0f5b61277146a51757057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855039"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Project Acoustics hakkında sık sorulan sorular

## <a name="what-is-project-acoustics"></a>Project Acoustics nedir?

Project Acoustics eklenti Suite, çalışma zamanı öncesinde ses dalgası davranışını hesaplayan ve statik aydınlatmaya yönelik bir Acoustics sistemidir. Bulut, Wave fizik hesaplamaları için ağır bir şekilde çalışıyor, bu nedenle çalışma zamanı CPU maliyeti düşük.  

## <a name="where-can-i-download-the-plugin"></a>Eklentiyi nereden indirebilirim?

[Project Acoustics Unity eklentisini](https://www.microsoft.com/download/details.aspx?id=57346) veya [Project Acoustics Unreal eklentisini](https://www.microsoft.com/download/details.aspx?id=58090)indirebilirsiniz.

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Project Acoustics x&gt; platformunu &lt;destekliyor mu?

Project Acoustics platform desteği, müşteri gereksinimlerine göre gelişir. Ek platformlar için destek hakkında bilgi için lütfen [Project Acoustics sorun forumundan](https://github.com/microsoft/ProjectAcoustics/issues) bizimle iletişime geçin.

## <a name="is-azure-used-at-runtime"></a>Azure çalışma zamanında mı kullanılıyor?

Hayır, bulut tümleştirmesi yalnızca sahne kurulumunun bir parçası olarak önceden işlem aşamasında kullanılır.
 
## <a name="what-is-simulation-input"></a>Simülasyon girişi nedir? 

Simülasyon girişi, 3B sahsitenizin, sanal ortamınızın veya oyun düzeyindedir. Project Acoustics, sorunsuz bir şekilde ve saçılmadan ve dağılmanın yanı sıra daha yakından bir şekilde modellemeyi sağlayan 3D Volumetric dalga benzetimleri gerçekleştirir
 
## <a name="what-is-the-runtime-cost"></a>Çalışma zamanı maliyeti nedir?

Acoustics çerçeve başına kaynak başına CPU% 0,01 ' ü alır. RAM kullanımı sahnenin boyutuna bağlıdır ve 10 ila 100 MB arasında değişebilir.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Düzey geometriyi basitleştirmem gerekir mi? Üçgen sayısı kontrol edilsin mi? Kafesler filigran sıkı olsun mu?

Hayır. Sistem ayrıntılı düzey geometriyi doğrudan alacak. İç işleme için göz atın.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Çalışma zamanı arama tablosunda neler var?

ACE dosyası, çok sayıda kaynak ve dinleyici konumu çifti arasındaki akustik parametrelerin yanı sıra parametre ilişkilendirme için kullanılan voxeli, sahgeometry ' i içerir.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Project Acoustics işleme kaynakları taşıbiliyor mu?

Evet, Project Acoustics arama tablosunu danışmanlar ve her tick üzerinde ses DSP 'sini güncelleştirir, bu sayede kaynak ve dinleyiciyi taşımak işleyebilir.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Proje Acoustics, dinamik geometriyi işleyebilir mi? Kapılar kapatılıyor mi? Duvarlar gelişmiş uzakta mi?

Hayır. Akustik parametreler, bir oyun düzeyinin statik durumuna göre önceden hesaplanır. Kapı geometrisinin Acoustics 'den çıkmasını ve daha sonra sağlanan teknikleri kullanarak geri dönüşlü ve taşınabilir oyun nesnelerinin durumuna göre ek bir geri alma uygulanmasını öneririz.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Project Acoustics akustik malzemeler kullanıyor mu?

Evet. Malzemeler, düzeyinizdeki fiziksel malzeme adlarından çekilir ve bu da masorptivity 'yi yönlendirmiş.
 
## <a name="what-do-the-probes-represent"></a>"Yoklamalar" ne ifade eder?

Yoklamalar, olası oynatıcı konumlarının bir örnekleniyor. Her araştırma, araştırma konumunda kaynaklanan sahnenin ayrı bir dalga simülasyonu temsil eder. Çalışma zamanında, dinleyici konumu için Akustik parametreler, yakındaki araştırma konumlarından enterpolasyonlardır.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Bulutta neden çok fazla işlem harcasın? Bana ne satın alım?

Project Acoustics, her mimari yönü hesaba ayırarak, Ultra karmaşık sanal ortamlar için bile doğru ve güvenilir Akustik parametreler sağlar. Çizim birimlerinin el ile çalışması olmadan sorunsuz bir şekilde occlusiyon ve düşüşe ve dinamik Reverb çeşitlemesi sağlar. Çalışma zamanı sırasında CPU üzerinde kalan tüm hafif.

## <a name="what-exactly-happens-during-baking"></a>"Fırlama" sırasında tam olarak ne olur?

Bir hazırlama, her dinleyici araştırmasına ortalanmış cuboıd simülasyon bölgelerinin akustik dalga benzetimlerinden oluşur.

## <a name="next-steps"></a>Sonraki adımlar
* [Projeyi Acoustics Unity örnek içeriğini](unity-quickstart.md) veya [gerçek olmayan örnek içeriği](unreal-quickstart.md) deneyin

