---
title: Azure IoT SDK 'larını kullanarak mobil cihazlar için geliştirme | Microsoft Docs
description: Geliştirici Kılavuzu-Azure IoT Hub SDK 'Ları kullanarak mobil cihazlar için geliştirme hakkında bilgi edinin.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883089"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Azure IoT SDK 'larını kullanarak mobil cihazlar için geliştirme

Nesnelerin İnterneti işlemler, farklı yetenek: sensörler, mikro denetleyiciler, akıllı cihazlar, endüstriyel ağ geçitleri ve hatta mobil cihazlar içeren çok çeşitli cihazlara başvurabilir.  Mobil cihaz, cihazdan buluta telemetri gönderen ve bulut tarafından yönetilen bir IoT cihazı olabilir.  Ayrıca, diğer IoT cihazlarını yöneten bir arka uç hizmeti uygulaması çalıştıran cihaz olabilir.  Her iki durumda da, mobil cihazlar için çalışan uygulamalar geliştirmek üzere [Azure IoT Hub SDK 'ları](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) kullanılabilir.  

## <a name="develop-for-native-ios-platform"></a>Yerel iOS platformu için geliştirme

Azure IoT Hub SDK 'Ları, Azure IoT Hub C SDK 'Sı aracılığıyla Yerel iOS platformu desteği sağlar.  Bunu, Swift veya amaç C XCode projenizde birleştirebilmeniz için bir iOS SDK 'Sı olarak düşünebilirsiniz.  İOS üzerinde C SDK 'Yı kullanmanın iki yolu vardır:

* XCode projesindeki CocoaPod kitaplıklarını doğrudan kullanın.  
* MacOS için [derleme yönergesini](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) izleyerek C SDK 'sı için kaynak kodu ve iOS platformu için derleme ' yı indirin.  

Azure IoT Hub C SDK, çeşitli platformlara en fazla taşınabilirlik için C99 dilinde yazılmıştır.  Taşıma işlemi, platforma özgü bileşenlere yönelik bir ince benimseme katmanı yazmayı içerir ve burada [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx)için burada bulabilirsiniz.  C SDK 'sindeki özellikler, desteklenen Azure IoT Hub temel özellikleri ve ağ güvenilirliği için yeniden deneme İlkesi gibi SDK 'ya özgü özellikler dahil olmak üzere iOS platformunda yararlanılabilir olabilir.  İOS SDK arabirimi, Azure IoT Hub C SDK arabirimine de benzer.  

Bu belgemalar, bir iOS cihazında cihaz uygulaması veya hizmet uygulaması geliştirmeyi adım adım göstermektedir:

* [Hızlı Başlangıç: Bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-ios.md)  
* [IoT Hub ile buluttan cihaza ileti gönderme](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Azure IoT Hub CocoaPod kitaplıklarıyla geliştirme

Azure IoT Hub SDK 'Ları, iOS geliştirmesi için bir dizi amaç-C CocoaPod kitaplıklarını yayınlar.  CocoaPod kitaplıklarının en son listesini görmek için bkz. [Cocoapods IoT for Microsoft Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  İlgili kitaplıklar XCode projenize eklendikten sonra, IoT Hub ilgili kodu yazmanın iki yolu vardır:

* Amaç C işlevi: Projeniz hedef C 'de yazılmışsa, API 'Leri doğrudan Azure IoT Hub C SDK 'dan çağırabilirsiniz.  Projeniz Swift 'ta yazılmışsa, işlevinizi oluşturmadan önce ' yi `@objc func` çağırabilir ve c veya amaç-c kodu kullanarak Azure IoT Hub ile ilgili tüm Logics yazılmasına devam edebilirsiniz.  [Örnek depoda](https://github.com/Azure-Samples/azure-iot-samples-ios), her ikisini de gösteren bir örnek kümesi.  

* C örneklerini dahil edin: Bir C cihaz uygulaması yazdıysanız, bunu doğrudan XCode projenizde başvurabilirsiniz:
    * Xcode ' dan Xcode projenize Sample. c dosyasını ekleyin.  
    * Üst bilgi dosyasını bağımlılığına ekleyin.  Örnek olarak [örnek depoya](https://github.com/Azure-Samples/azure-iot-samples-ios) bir üstbilgi dosyası dahildir. Daha fazla bilgi için lütfen [hedef-C](https://developer.apple.com/documentation/objectivec)için Apple 'ın belge sayfasını ziyaret edin.

## <a name="develop-for-android-platform"></a>Android platformu için geliştirme
Azure IoT Hub Java SDK 'Sı Android platformunu destekler.  Test edilen belirli API sürümü için lütfen en son güncelleştirme için [Platform desteği](iot-hub-device-sdk-platform-support.md) sayfamızı ziyaret edin.

Bu belge, Gradle ve Android Studio kullanarak bir Android cihazında cihaz uygulaması veya hizmet uygulaması geliştirmeyi adım adım göstermektedir:

* [Hızlı Başlangıç: Bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-android.md)  
* [Hızlı Başlangıç: Bir cihazı denetleme](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Sonraki adımlar

* [IoT Hub REST API başvurusu](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK kaynak kodu](https://github.com/Azure/azure-iot-sdk-c)
