---
title: Azure Medya Clipper ile klipler oluşturma | Microsoft Docs
description: Varlıklardan medya klipleri oluşturmaya yönelik bir araç olan Azure Media Clipper 'a genel bakış
services: media-services
keywords: klip; alt klip; kodlama; medya
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51f85dffd48e451b477018ef20491f8619a30f25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685005"
---
# <a name="create-clips-with-azure-media-clipper"></a>Azure Medya Clipper ile klipler oluşturma 

Azure Medya Clipper, Web geliştiricilerinin kullanıcılarına medya klipleri oluşturmak için bir arabirim sağlamasına olanak tanıyan ücretsiz bir JavaScript kitaplığıdır. Bu araç herhangi bir Web sayfasıyla tümleştirilebilir ve varlıkları yüklemek ve kırpma işleri göndermek için API 'Ler sağlar.

Azure Media Clipper şunları yapmanızı sağlar:
- Canlı arşivlerden, ön kurşun ve kurşun gönderiyi kırpın 
- AMS canlı olayları, canlı arşivleri veya fMP4 VOD dosyalarından video vurguları oluşturun 
- Birden çok kaynaktan videoları birleştirme 
- AMS medya varlıklarınızdan Özet klipler oluşturun 
- Çerçeve doğruluğu ile videoları kırpın 
- Resim grubu (GOP) doğruluğu ile mevcut canlı ve VOD varlıkları üzerinde dinamik bildirim filtreleri oluşturun 
- Media Services hesabınızdaki varlıklara karşı kodlama işleri üretin

Yeni özellikler istemek, fikir ve geri bildirim sağlamak, [Azure Media Services Için UserVoice](https://aka.ms/amsvoice/)'a gönderin. Ve belirli sorunlarla karşılaşırsanız, sorularınız varsa veya herhangi bir hata bulursanız, Media Services ekibine amcinfo@microsoft.combir satır bırakın.

Aşağıdaki görüntüde Clipper arabirimi gösterilmektedir: Azure Media Clipper ![](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Sürüm notları
Clipper 'ın en son sürümü için Web günlüğü gönderisi, bilinen çeşitli sorunlar ve CHANGELOG için aşağıdaki listeye bakın:
- [Blog gönderisi](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Bilinen sorunlar listesi](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [CHANGELOG](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Tarayıcı desteği
Azure Medya Clipper, modern HTML5 teknolojileri kullanılarak oluşturulmuştur ve aşağıdaki tarayıcıları destekler:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> Şu anda yalnızca Azure Media Services akışlarının HTML5 kayıttan yürütülmesi destekleniyor.

## <a name="language-support"></a>Dil desteği
Clipper pencere öğesi aşağıdaki 18 dilde kullanılabilir:
- Çince (Basitleştirilmiş)
- Çince (Geleneksel)
- Çekçe
- Felemenkçe, Flemish
- Türkçe
- Fransızca
- Almanca
- Macarca
- İtalyanca
- Japonca
- Korece
- Lehçe
- Portekizce (Brezilya)
- Portekizce (Portekiz)
- Rusça
- İspanyolca
- İsveççe
- Türkçe

## <a name="next-steps"></a>Sonraki adımlar
Azure Medya Clipper 'ı kullanmaya başlamak için, pencere öğesinin nasıl dağıtılacağı hakkındaki ayrıntılar için [Başlarken](media-services-azure-media-clipper-getting-started.md) makalesini okuyun.
