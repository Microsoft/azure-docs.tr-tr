---
title: Video Indexer bir kişi modelini özelleştirme-Azure
titleSuffix: Azure Media Services
description: Bu makale, Video Indexer kişi modeli nedir ve özelleştirmeyi özelleştirmek için bir genel bakış sunar.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838306"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Video Indexer bir kişi modelini özelleştirme

Video Indexer, videolarınızdaki ünlüleri tanımayı destekler. Ünlüçilerin tanınma özelliği, ıMDB, Viveze ve en popüler LinkedIn etkileyen, sık istenen veri kaynağına göre yaklaşık 1.000.000 yüz içerir. Video Indexer tarafından tanınmayan yüzler hala algılanır, ancak adlandırılmamaktadır. Müşteriler özel kişi modelleri oluşturabilir ve varsayılan olarak tanınmayan yüzeyleri tanımak için Video Indexer etkinleştirebilir. Müşteriler kişilerin adını kişinin yüzlerindeki görüntü dosyalarıyla eşleştirerek bu kişi modellerini oluşturabilir.  

Hesabınız farklı kullanım durumlarına karşı, hesap başına birden fazla kişi modeli oluşturabilmeniz için avantaj sağlayabilirsiniz. Örneğin, hesabınızdaki içerik farklı kanallarda sıralanacaksa, her kanal için ayrı bir kişi modeli oluşturmak isteyebilirsiniz. 

> [!NOTE]
> Her kişi modeli en fazla 1.000.000 kişiyi destekler ve her hesabın 50 kişi modeli sınırlaması vardır. 

Bir model oluşturulduktan sonra, bir videoyu karşıya yüklerken/dizinleme yaparken veya yeniden dizinlerken belirli bir kişi modelinin model KIMLIĞINI sağlayarak onu kullanabilirsiniz. Video için yeni bir yüz eğitimi yapın, videonun ilişkilendirildiği özel modeli günceller. 

Birden çok kişi modeli desteği gerekmiyorsa, karşıya yükleme/dizinleme veya yeniden dizin oluşturma sırasında videonuza bir kişi modeli KIMLIĞI atamayın. Bu durumda Video Indexer hesabınızda varsayılan kişi modelini kullanacaktır. 

Video Indexer Web sitesini kullanarak bir videoda algılanan yüzeyleri düzenleyebilir ve bir [Web sitesi kullanarak kişi modeli özelleştirme](customize-person-model-with-website.md) konu başlığı altında açıklandığı gibi hesabınızdaki birden fazla özel kişi modelini yönetebilirsiniz. API 'yi, API ['leri kullanarak bir kişi modelini özelleştirme](customize-person-model-with-api.md)bölümünde açıklandığı gibi de kullanabilirsiniz.
