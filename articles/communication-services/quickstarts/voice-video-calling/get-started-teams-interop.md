---
title: Hızlı başlangıç-Azure Iletişim hizmetlerinde takımlar birlikte çalışabilirliği
titleSuffix: An Azure Communication Services quickstart
description: Bu hızlı başlangıçta, SDK 'Yı çağıran Azure Iletişimi ile bir takım toplantısına nasıl katılacağınızı öğreneceksiniz.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095620"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Hızlı başlangıç: çağıran uygulamanızı bir takımlar toplantısı 'na ekleme

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> [Ekipte kiracı birlikte çalışabilirliğini](../../concepts/teams-interop.md)etkinleştirmek/devre dışı bırakmak için [Bu formu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)doldurun.

JavaScript SDK 'sını kullanarak, çağırma çözümünüzü Microsoft ekiplerine bağlayarak Azure Iletişim Hizmetleri ile çalışmaya başlayın.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

Bu belgede açıklanan işlevsellik, Iletişim Hizmetleri SDK 'larının genel erişilebilirlik sürümünü kullanır. Ekipler birlikte çalışabilirliği, Iletişim Hizmetleri SDK 'larının beta sürümünü gerektirir. Beta SDK 'Ları [sürüm notları sayfasında](https://github.com/Azure/Communication/tree/master/releasenotes)araştırılabilir.

Beta SDK 'leriyle "paketi Kur" adımını yürütürken, `@1.0.0-beta.10` paket adında sürüm (Bu makalenin yazıldığı sırada sürüm) belirterek paketinizin sürümünü en son beta sürümüyle değiştirin `communication-calling` . `communication-common`Paket komutunu değiştirmeniz gerekmez. Örnek:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../create-communication-resource.md#clean-up-resources)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Çağıran Hero örnekimize](../../samples/calling-hero-sample.md) göz atın
- [SDK yeteneklerini çağırma](./calling-client-samples.md) hakkında bilgi edinin
- [Nasıl çalıştığını çağırma](../../concepts/voice-video-calling/about-call-types.md) hakkında daha fazla bilgi edinin
