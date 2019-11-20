---
title: Visual Studio ve Azure hizmetleriyle istemci uygulamaları oluşturmak için ön uç geliştirme platformunu seçin
description: İstemci uygulamaları derlemek için desteklenen yerel ve platformlar arası diller hakkında bilgi edinin.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795503"
---
# <a name="choose-mobile-development-framework"></a>Mobil geliştirme çerçevesini seçin
Geliştiriciler, platformlar arası bir yaklaşım için belirli çerçeveleri ve desenleri kullanarak mobil uygulamalar oluşturmak için istemci tarafı teknolojileri kullanabilirler. Geliştiriciler, kararlarına bağlı olarak, yerel (hedef-C ve Java gibi dilleri kullanarak, Xamarin, .NET, ve C#) ve karma (Cordova kullanarak) platformlar ve kendi türevlerini oluşturabilir etmen.

## <a name="native-platforms"></a>Yerel platformlar
Yerel bir uygulama oluşturmak, platforma özgü programlama dilleri, SDK 'lar, geliştirme ortamı ve işletim sistemi satıcıları tarafından sunulan diğer araçları gerektirir.

### <a name="ios"></a>iOS
Apple tarafından oluşturulup geliştirilmiş, iOS üzerinde oluşturulan uygulamalar Apple cihazlarında, yani iPhone ve iPad olarak çalışır.

- **Programlama dilleri** -amaç-C, Swift
- **IDE** -Xcode
- **SDK** -IOS SDK 'sı

### <a name="android"></a>Android
Google ve dünyanın en popüler işletim sistemi tarafından tasarlanan Android 'de oluşturulan uygulamalar, akıllı telefonlar ve tabletlerde çalışabilir.

- **Programlama dili** -Java, Kotlin 
- **IDE** -Android Studio ve Android Geliştirici Araçları 
- **SDK** -Android SDK

### <a name="windows"></a>Windows
- **Programlama dili** -C#
- **IDE** -Visual Studio, Visual Studio Code
- **SDK** -Windows SDK

#### <a name="pros"></a>Ları
- İyi kullanıcı deneyimi
- Yüksek performansa sahip ve yerel kitaplıklarla arabirim yeteneği olan yanıt veren uygulamalar
- Yüksek oranda güvenli uygulamalar

#### <a name="cons"></a>Simgeler
- Uygulama yalnızca bir platformda çalışır
- Uygulama oluşturmak için daha fazla geliştirici kaynağı ve pahalıdır
- Daha düşük kod yeniden kullanımı

## <a name="cross-platforms-and-hybrid-applications"></a>Platformlar arası ve karma uygulamalar
Platformlar arası uygulamalar, yerel mobil uygulamaları bir kez yazmak, kod paylaşmak ve iOS, Android ve Windows 'da çalıştırmak için güç sağlar.

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/) C#, Microsoft 'un sahip olduğu bir sınıf kitaplığı ve çalışma zamanı ile IOS, Android ve Windows dahil olmak üzere çok sayıda platformda çalışan bir sınıf kitaplığı ve çalışma zamanı ile, hala yerel (Yorumlanmamış) derleme ) yüksek performanslı uygulamalar. Xamarin, yerel platformların tüm yeteneklerini birleştirir ve kendi güçlü özelliklerinin bir sayısını ekler.

- **Programlama dili** -C#
- **IDE** -Windows veya Mac üzerinde Visual Studio

### <a name="react-native"></a>React Native
2015 ' de Facebook tarafından yayınlanan [doğal, yerel](https://facebook.github.io/react-native/) olarak, IOS ve Android için mobil uygulamaları gerçek zamanlı olarak işlemeye yönelik [Açık kaynaklı](https://github.com/facebook/react-native) bir JavaScript çerçevesidir. BT, Facebook 'un Kullanıcı arabirimleri oluşturmak için JavaScript kitaplığına tepki verir, ancak tarayıcıyı hedeflemek yerine mobil platformları hedefler. Doğal olarak, derleme blokları olarak Web bileşenleri yerine yerel bileşenleri kullanır.
 
- **Programlama dili** -JavaScript
- **IDE** -Visual Studio Code

### <a name="unity"></a>Unity
 Unity, geliştiricilerin Windows, iOS, Android ve Xbox gibi farklı platformlar C# için yüksek kaliteli 2B/3B uygulamalar oluşturmalarına olanak tanıyan oyunlar oluşturmak için iyileştirilmiş bir altyapıdır.

### <a name="cordova"></a>Cordova
Cordova, Apache Codova için Visual Studio Araçları veya Cordova uzantıları ile Visual Studio Code kullanarak karma uygulamalar oluşturmanıza olanak tanır. Karma yaklaşım sayesinde, bileşenleri Web siteleri ile paylaşabilir ve Web sunucusu tabanlı uygulamaları Cordova tabanlı Web uygulamaları yaklaşımıyla yeniden kullanabilirsiniz.

#### <a name="pros"></a>Ları
- Birden çok platform için bir kod temeli oluşturarak daha fazla kod kullanılabilirliği
- Birçok platformda daha geniş bir hedef kitleye karşı karşıya
- Geliştirme sırasında çarpıcı bir azalma
- Başlatma ve güncelleştirme kolay

#### <a name="cons"></a>Simgeler
- Düşük performans
- Esneklik eksikliği
- Her platformun, yerel uygulamayı daha yaratıcı hale getirmek için benzersiz bir özellikler ve işlevler kümesi vardır
- Artan UI tasarım zamanı
- Araç sınırlaması
