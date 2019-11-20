---
title: Visual Studio Mobile Center ile Gerçek Kullanıcı Ölçümleri-Azure Traffic Manager
description: Gerçek Kullanıcı Ölçümleri Traffic Manager göndermek için Visual Studio Mobile Center kullanarak geliştirilmiş mobil uygulamanızı ayarlama
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 734049a45eca2688b2ad309ee3245bbb7bf152de
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040312"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Visual Studio Mobile Center ile Traffic Manager Gerçek Kullanıcı Ölçümleri gönderme

Visual Studio Mobile Center kullanarak geliştirilmiş mobil uygulamanızı, adımları izleyerek Traffic Manager Gerçek Kullanıcı Ölçümleri göndermek için ayarlayabilirsiniz:

>[!NOTE]
> Şu anda, Traffic Manager 'a Gerçek Kullanıcı Ölçümleri gönderilmesi yalnızca Android için desteklenir.

Gerçek Kullanıcı Ölçümleri yapılandırmak için bir anahtar edinmeniz ve uygulamanızı RUM paketiyle birlikte yapmanız gerekir.

## <a name="step-1-obtain-a-key"></a>1\. Adım: anahtar alma
    
İstemci uygulamanızdan Traffic Manager alıp gönderdiğiniz ölçümler, Gerçek Kullanıcı Ölçümleri (RUM) anahtarı olarak adlandırılan benzersiz bir dize kullanılarak hizmet tarafından tanımlanır. Azure portal, bir REST API veya PowerShell/CLı arabirimlerini kullanarak bir RUM anahtarı alabilirsiniz.

Aşağıdaki yordamı kullanarak Azure portal kullanarak RUM anahtarını elde etmek için:
1. Bir tarayıcıdan Azure portal oturum açın. Henüz bir hesabınız yoksa, ücretsiz bir aylık deneme sürümü için kaydolabilirsiniz.
2. Portalın arama çubuğunda, değiştirmek istediğiniz Traffic Manager profili adını arayın ve ardından görüntülenen sonuçlarda Traffic Manager profiline tıklayın.
3. Traffic Manager profili sayfasında, **Ayarlar**altında **gerçek Kullanıcı ölçümleri** ' a tıklayın.
4. Yeni bir RUM anahtarı oluşturmak için **anahtar oluştur** ' a tıklayın.
        
   ![Gerçek Kullanıcı Ölçümleri anahtar oluştur](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Şekil 1: anahtar oluşturmayı Gerçek Kullanıcı Ölçümleri**

5. Sayfada, oluşturulan RUM anahtarı ve HTML sayfanıza katıştırılması gereken bir JavaScript kod parçacığı görüntülenir.
 
   ![Gerçek Kullanıcı Ölçümleri anahtarı için JavaScript kodu](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Şekil 2: anahtar ve ölçüm JavaScript Gerçek Kullanıcı Ölçümleri**
 
6. RUM anahtarını kopyalamak için **Kopyala** düğmesine tıklayın. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>2\. Adım: uygulamanızı Mobile Center SDK 'nın RUM paketiyle birlikte Işaretleme

Visual Studio Mobile Center 'a yeni başladıysanız, [Web sitesini](https://mobile.azure.com)ziyaret edin. SDK tümleştirmesi hakkında ayrıntılı yönergeler için bkz. [Android SDK kullanmaya](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android)başlama.

Gerçek Kullanıcı Ölçümleri kullanmak için aşağıdaki yordamı izleyin:

1.  SDK 'Yı projeye ekleyin

    ATM RUM SDK 'sının önizlemesi sırasında, paket deposuna açık bir başvuru yapmanız gerekir.

    **App/Build. Gradle** dosyasında aşağıdaki satırları ekleyin:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    **App/Build. Gradle** dosyasında aşağıdaki satırları ekleyin:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. SDK 'Yı başlatma

    Uygulamanızın ana etkinlik sınıfını açın ve aşağıdaki içeri aktarma deyimlerini ekleyin:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Aynı dosyada `onCreate` geri aramayı arayın ve aşağıdaki kodu ekleyin:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Sonraki adımlar
- [Gerçek Kullanıcı ölçümleri](traffic-manager-rum-overview.md) hakkında daha fazla bilgi edinin
- [Traffic Manager nasıl çalıştığını](traffic-manager-overview.md) öğrenin
- [Mobile Center](https://docs.microsoft.com/mobile-center/) hakkında daha fazla bilgi edinin
- Mobile Center 'a [kaydolun](https://mobile.azure.com)
- Traffic Manager tarafından desteklenen [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md) hakkında daha fazla bilgi edinin
- [Traffic Manager profili oluşturmayı](traffic-manager-create-profile.md) öğrenin

