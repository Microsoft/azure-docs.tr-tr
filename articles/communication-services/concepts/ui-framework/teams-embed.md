---
title: UI çerçevesi takımları ekleme
titleSuffix: An Azure Communication Services quickstart
description: Bu belgede, Azure Iletişim Hizmetleri Kullanıcı arabirimi çerçevesi ekip ekleme özelliğinin, anahtar çağırma deneyimleri oluşturmak için nasıl kullanılabileceğini öğreneceksiniz.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8301f81b21db50814df1bc764cc1fae38b4f14de
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307648"
---
# <a name="teams-embed"></a>Ekip ekleme

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Ekip ekleme, ortak işletmeden işletmeye ve işletmeler arası çağrı etkileşimlerine odaklanan bir Azure Iletişim Hizmetleri özelliğidir. Takımlar ekleme sistemi, [video ve sesli çağrı](../voice-video-calling/calling-sdk-features.md), ancak takımlar, Microsoft ekipleri toplantılarına dayalı olarak tüm Kullanıcı deneyimini sunmak için Azure 'un arama temel elemanlarına sistem derlemeleri katıştırır.

Ekipler ekleme SDK 'Ları kapalı kaynaktır ve bu özellikleri bir anahtar, bileşik biçimde kullanıma hazır hale getirir. Ekipleri uygulamanızın tuvaline gömdüğünüzde ve SDK, tüm Kullanıcı deneyimi oluşturuyor. Bu Kullanıcı deneyimi Microsoft ekipleri toplantılarına çok benzer olduğundan, aşağıdaki avantajlardan yararlanabilirsiniz:

- Azaltılan geliştirme süresi ve mühendislik karmaşıklığı
- Ekipler ile son kullanıcı hakkında benzerlik
- [Takımlar Son Kullanıcı eğitim içeriğini](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67) yeniden kullanma yeteneği

Takımlar ekleme, takımlar toplantılarında desteklenen özelliklerin çoğunu sağlar; örneğin:

- Kullanıcının ses ve video cihazlarını yapılandırdığı ön toplantı deneyimi
- Ses ve video cihazlarını yapılandırmak için toplantı içi deneyim
- [Video arka planları](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): katılımcıların arka planlarını bulanıklaştırmasını veya değiştirmesini sağlar
- Video galerisinin büyük galerisiyle birlikte mod, odak, sabitleme ve Spotlight [Için birden çok seçenek](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae)
- [Içerik paylaşımı](https://support.microsoft.com/en-us/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): katılımcıların kendi ekranını paylaşmasına izin verme

Diğer Azure Iletişim SDK 'larına kıyasla bu kullanıcı arabirimi hakkında daha fazla bilgi için, bkz. [UI SDK 'sı kavramı tanıtımı](ui-sdk-overview.md). 
