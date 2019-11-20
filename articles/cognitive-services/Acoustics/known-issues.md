---
title: Project Acoustics eklenti bilinen sorunları
titlesuffix: Azure Cognitive Services
description: Project Acoustics 'ta aşağıdaki bilinen sorunlarla karşılaşabilirsiniz.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243033"
---
# <a name="project-acoustics-known-issues"></a>Project Acoustics bilinen sorunlar
Bu makalede Project Acoustics kullandığınızda karşılaşabileceğiniz sorunlar açıklanmaktadır.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Bir sahneyi yeniden adlandırdığınızda Akustik parametreler kayboluyor

Bir sahneyi yeniden adlandırırsanız, söz konusu sahneye ait tüm Akustik parametreler yeni sahneye otomatik olarak aktarılmaz. Ancak bunlar hala eski varlık dosyasında bulunur. Sahne dosyanızın yanındaki *Düzenleyici* dizininde *[manzara adı] _AcousticParameters. varlık* dosyasını arayın. Dosyayı yeni sahnenin adını yansıtacak şekilde yeniden adlandırın.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Bazı Unity sürümlerinden Android 'e dağıtma hatası

Unity 'nin bazı sürümlerinde, Android 'e nasıl ses eklentileri dağıttıkları konusunda bir [hata](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) vardır. Bu hatadan etkilenen bir sürüm kullandığınızdan emin olun.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"Meta veri dosyası System. Security. dll bulunamadı" hatası

**Player** ayarlarındaki **Scripting Runtime sürümünün** *.NET 4. x eşdeğerini*olduğundan emin olun ve Unity 'yi yeniden başlatın.

## <a name="authentication-problems-when-connecting-to-azure"></a>Azure 'a bağlanırken kimlik doğrulama sorunları

Şunları denetleyin:
- Azure hesabınız için doğru kimlik bilgilerini kullandınız.
- Hesabınız, Bake içinde istediğiniz düğüm türünü destekler.
- Sistem saatiniz doğru şekilde ayarlanmış.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Geri al sekmesi yine de iptal ettikten sonra "silme" işlemini gösterir
Project Acoustics başarıyla tamamlandığında veya İptalden sonra bir iş için tüm Azure kaynaklarını temizler. Bu işlem 5 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Unity](unity-quickstart.md) veya [gerçek olmayan](unreal-quickstart.md) örnek içeriğini deneyin.
