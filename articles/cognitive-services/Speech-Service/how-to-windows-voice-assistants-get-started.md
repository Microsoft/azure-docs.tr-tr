---
title: Windows 'da sesli yardımcılar-kullanmaya başlama
titleSuffix: Azure Cognitive Services
description: Örnek kod hızlı başlangıcı başvurusu da dahil olmak üzere bir Windows Voice Agent geliştirmeye başlama adımları.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: b50b98095cdfe0e6ec19c89b57887ebc4a0f6317
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101713057"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Windows 'da sesli yardımcılar ile çalışmaya başlama

Bu kılavuz, Windows üzerinde bir ses Yardımcısı geliştirmeye başlama adımlarında size yol gösterecektir.

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

Windows için bir ses Yardımcısı geliştirmeye başlamak için uygun geliştirme ortamınıza sahip olduğunuzdan emin olmanız gerekir.

- **Visual Studio:** [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition veya üstünü yüklemeniz gerekir
- **Windows sürümü**: Windows Insider hızlı halka Windows derlemesi ve Windows SDK Windows Insider sürümü olan bir bilgisayar. Bu örnek kod, Windows SDK 19018 kullanarak Windows Insider sürüm derlemesi 19025.vb_release_analog .191112-1600 üzerinde çalıştığı için doğrulanır. Belirtilen sürümlerin üzerindeki tüm derleme veya SDK uyumlu olmalıdır.
- **UWP geliştirme araçları**: Visual Studio 'da Evrensel Windows platformu geliştirme iş yükü. Makinenizi UWP uygulamaları geliştirmeye hazırlamak için UWP tarafından [Kurulum](/windows/uwp/get-started/get-set-up) sayfasına bakın.
- **Çalışan bir mikrofon ve ses çıkışı**

## <a name="obtain-resources-from-microsoft"></a>Microsoft 'tan kaynak alma

Windows üzerinde tamamen özelleştirilmiş bir ses Aracısı için gereken bazı kaynaklar, Microsoft kaynakları gerektirir. [UWP Voice Yardımcısı örneği](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) , ilk geliştirme ve test için bu kaynakların örnek sürümlerini sağlar; bu nedenle ilk geliştirme için bu bölüm gereksizdir.

- **Anahtar sözcük modeli:** Ses etkinleştirme, bir. bin dosyası biçiminde Microsoft 'tan bir anahtar sözcük modeli gerektirir. UWP ses Yardımcısı örneğinde verilen. bin dosyası *contoso* anahtar sözcüğü üzerinde eğitilir.
- **Sınırlı erişim özelliği belirteci:** Konuşma seslerine mikrofon seslerine erişim sağlanması nedeniyle, bu dosyalar sınırlı erişim özelliği kısıtlamalarına göre korunur. Sınırlı bir erişim özelliğini kullanmak için, Microsoft 'tan uygulamanızın paket kimliğine bağlanmış sınırlı bir erişim özelliği belirteci edinmeniz gerekir.

## <a name="establish-a-dialog-service"></a>İletişim hizmeti oluşturma

Tüm ses Yardımcısı deneyimi için, uygulamanın bir iletişim hizmetine ihtiyacı olacak

- Verilen ses dosyasında bir anahtar sözcük Algıla
- Kullanıcı girişini dinlemek ve metne dönüştürmek
- Bir bot 'a metin sağlayın
- Bot 'ın metin yanıtını bir ses çıktısına çevirin

Bunlar doğrudan hat konuşmayı kullanarak temel bir iletişim hizmeti oluşturma gereksinimleridir.

- **Konuşma Hizmetleri aboneliği:** Konuşmadan metne ve metinden konuşmaya dönüştürme için bilişsel konuşma Hizmetleri için bir abonelik. Konuşma hizmetlerini [buradan](./overview.md#try-the-speech-service-for-free)ücretsiz olarak deneyin.
- **Bot Framework bot:**  Ses girişini ve çıktıyı etkinleştirmek üzere [doğrudan hat konuşmaya](./direct-line-speech.md) abone olmak Için, bot Framework sürüm 4,2 veya üzeri kullanılarak oluşturulan bir bot. [Bu kılavuz](./tutorial-voice-enable-your-bot-speech-sdk.md) , "yankı bot" oluşturmak için adım adım yönergeler içerir ve bunu doğrudan konuşmaya yönlendirmek üzere abone olur. Ayrıca, özelleştirilmiş bir bot oluşturma adımları için [buraya](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) gidebilirsiniz ve sonra da "Echo bot" yerine yeni bir bot 'a abone olmak için [buradaki](./tutorial-voice-enable-your-bot-speech-sdk.md) adımları takip edebilirsiniz.

## <a name="try-out-the-sample-app"></a>Örnek uygulamayı deneyin

Konuşma Hizmetleri abonelik anahtarınız ve yankı bot 'un bot KIMLIĞI sayesinde [UWP Voice Yardımcısı örneğini](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)denemeye hazırsınız demektir. Uygulamayı çalıştırmak için Benioku dosyasındaki yönergeleri izleyin ve kimlik bilgilerinizi girin.

## <a name="create-your-own-voice-assistant-for-windows"></a>Windows için kendi ses yardımcınızı oluşturma

Microsoft 'tan sınırlı erişim özelliği belirtecinizi ve bin dosyanızı aldıktan sonra, Windows 'da kendi ses yardımcınız üzerinde başlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ses Yardımcısı uygulama kılavuzunu okuyun](windows-voice-assistants-implementation-guide.md)