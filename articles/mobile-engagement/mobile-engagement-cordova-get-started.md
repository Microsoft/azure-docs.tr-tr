---
title: "Cordova/Phonegap için Azure Mobile Engagement Kullanmaya Başlama"
description: "Cordova/Phonegap uygulamaları için Analizler ve Anında İletme Bildirimleri ile Azure Mobile Engagement kullanmayı öğrenin."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Cordova/Phonegap için Azure Mobile Engagement Kullanmaya Başlama
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Bu konuda, uygulama kullanımınızı anlamak için nasıl Azure Mobile Engagement kullanılacağı ve Cordova ile geliştirilen mobil bir uygulamanın segmentli kullanıcılarına nasıl anında iletme bildirimleri gönderileceği gösterilmektedir.

Bu öğreticide, Mac kullanarak boş bir Cordova uygulaması oluşturup Mobile Engagement SDK ile tümleştireceğiz. Uygulama, temel analiz verileri toplar, iOS için Apple Anında İletilen Bildirim Sistemi (APNS) ve Android için Google Cloud Messaging (GCM) kullanarak anında iletme bildirimlerini alır. Bu uygulamayı, test etme amacıyla bir iOS veya Android cihazına dağıtacağız.  

> [!NOTE]
> Bu öğreticiyi tamamlamak için etkin bir Azure hesabınızın olması gerekir. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılar için bkz. [Azure Ücretsiz Deneme](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Bu öğretici için aşağıdakiler gereklidir:

* Mac App Store'dan yükleyebileceğiniz XCode (iOS’a dağıtmak için)
* [Android SDK ve Öykünücüsü](http://developer.android.com/sdk/installing/index.html) (Android’e dağıtmak için)
* APNS için Apple Dev Center'dan edinebileceğiniz anında iletme bildirimi sertifikası (.p12)
* GCM için Google Developer Console’unuzdan edinebileceğiniz GCM Proje numarası
* [Mobile Engagement Cordova eklentisi](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Cordova eklentisinin kaynak kodunu ve BeniOku dosyasını [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova) sayfasında bulabilirsiniz
> 
> 

## <a id="setup-azme"></a>Cordova uygulamanız için Mobile Engagement kurma
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Uygulamanızı Mobile Engagement arka ucuna bağlama
Bu öğreticide, veri toplamak ve anında iletme bildirimi göndermek için gerekli en küçük grup olan bir "temel tümleştirme" gösterilmektedir. 

Tümleştirmeyi göstermek için Cordova ile temel bir uygulama oluşturacağız:

### <a name="create-a-new-cordova-project"></a>Yeni bir Cordova projesi oluşturma
1. Mac makinenizde *Terminal* penceresi başlatın ve varsayılan şablondan yeni bir Cordova projesi oluşturacak olan aşağıdakileri yazın. iOS uygulamanızı dağıtmak için kullanacağınız yayımlama profilinin Uygulama Kimliği olarak 'com.mycompany.myapp' kullandığından emin olun. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Projenizi **iOS** için yapılandırıp iOS Simulator’da çalıştırmak için aşağıdakileri yürütün:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Projenizi **Android** için yapılandırıp Android öykünücüsünde çalıştırmak için aşağıdakileri yürütün. Android SDK Öykünücüsü ayarlarınızda hedef olarak Google API'leri (Google Inc.) ve CPU / ABI olarak Google API'ler ARM’si bulunduğundan emin olun.  
   
        $ cordova platform add android
        $ cordova run android
4. Cordova Konsolu eklentisini ekleyin. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Uygulamanızı Mobile Engagement arka ucuna bağlama
1. Azure Mobile Engagement Cordova eklentisini yükleyip, eklentiyi yapılandırmak üzere değişken değerlerini sağlayın:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android Reach Simgesi*: Herhangi bir uzantı veya drawable ön eki olmadan kaynağın adı olmalıdır (ör. mynotificationicon) ve simge dosyası android projenize (platforms/android/res/drawable) kopyalanmalıdır

*iOS Reach Simgesi*: Uzantısı ile birlikte kaynağın adı olmalıdır (ör. mynotificationicon.png) ve simge dosyası XCode ile iOS projenize eklenmelidir (Dosya Ekle Menüsü kullanılarak)

## <a id="monitor"></a>Gerçek zamanlı izlemeyi etkinleştirme
1. Cordova projesinde **www/js/index.js** dosyasını düzenleyerek, *deviceReady* olayı alındığında yeni bir etkinlik bildirmek üzere Mobile Engagement çağrısı ekleyin.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Uygulamayı çalıştırın:
   
   * **iOS için**
     
       `Terminal` penceresinde aşağıdakini yürüterek uygulamanızı yeni bir Simulator örneğinde başlatın:
     
           cordova run ios
   * **Android için**
     
       `Terminal` penceresinde aşağıdakini yürüterek uygulamanızı yeni bir öykünücü örneğinde başlatın:
     
           cordova run android
3. Konsol günlüklerine aşağıdakileri görebilirsiniz:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Uygulamayı gerçek zamanlı izlemeyle bağlama
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Anında İletme Bildirimlerini ve uygulama içi mesajlaşmayı etkinleştirme
Mobile Engagement, kampanyalar bağlamında Anında İletme Bildirimleri ve uygulama içi mesajlaşma aracılığıyla kullanıcılarınız ile etkileşim kurmanızı sağlar. Mobile Engagement portalında bu modüle REACH adı verilir.
Aşağıdaki bölümler, uygulamanızı bu bildirim ve mesajları alacak şekilde ayarlar.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Mobile Engagement için Gönderim kimlik bilgilerini yapılandırma
Mobile Engagement’ın sizin adınıza Anında İletme Bildirimleri göndermesine izin vermek için, Apple iOS sertifikanıza veya GCM Server API Anahtarınıza erişmesine izin vermeniz gerekir. 

1. Mobile Engagement portalınıza gidin. Bu proje için kullanmakta olduğunuz uygulamada olduğunuzdan emin olun ve alt kısımdaki **Engage** (Katıl) düğmesine tıklayın:
   
    ![][1]
2. Engagement Portal’ınızdaki ayarlar sayfasına gideceksiniz. Bu sayfada **Yerel Gönderim** bölümüne tıklayın:
   
    ![][2]
3. iOS Sertifikasını/GCM Server API Anahtarını yapılandırın
   
    **[iOS]**
   
    a. .p12 sertifikanızı seçin, bunu yükleyip parolanızı yazın:
   
    ![][3]
   
    **[Android]**
   
    a. GCM Ayarları bölümünde **API Anahtarı**’nın önündeki düzenle simgesine tıklayıp, gösterilen açılır pencerede GCM Server Anahtarını yapıştırın ve **Tamam**’a tıklayın. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Cordova uygulamasında anında iletme bildirimlerini etkinleştirme
**www/js/index.js** dosyasını düzenleyerek anında iletme bildirimleri istemek ve bir işleyici bildirmek üzere Mobile Engagement çağrısı ekleyin:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Uygulamayı çalıştırma
**[iOS]**

1. iOS yalnızca gerçek bir cihaza anında iletme bildirimlerine izin verdiğinden anında iletme bildirimlerini test etmek için, XCode kullanarak uygulamayı cihazda derleyip dağıtacağız. Cordova projenizin oluşturulduğu konumda **...\platforms\ios** konumuna gidin. Yerel .xcodeproj dosyasını XCode’da açın. 
2. Biraz önce Mobile Engagement portalına yüklediğiniz sertifikayı içeren sağlama profilinin bulunduğu hesabı ve Cordova uygulamasını oluştururken sağladığınız Uygulama Kimliğinin aynısını kullanarak Cordova uygulamasını derleyin ve iOS cihazına dağıtın. Eşleştirme amacıyla XCode’da **Resources\*-info.plist** dosyasında *Paket tanımlayıcısına* bakabilirsiniz. 
3. Cihazınızda uygulamanın bildirim gönderme izni istediğini belirten standart iOS açılır penceresini görürsünüz. İzni verin. 

**[Android]**

GCM bildirimleri Android öykünücüsünde desteklendiğinden Android uygulamasını öykünücüde çalıştırmanız yeterlidir. 

    cordova run android

## <a id="send"></a>Uygulamanıza bildirim gönderme
Şimdi, cihazda çalışan uygulamanıza bir anında iletim gönderecek olan basit bir Anında İletme Bildirimi kampanyası oluşturacağız:

1. Mobile Engagement portalınızın **Reach** sekmesine gidin
2. Anında iletme kampanyanızı oluşturmak için **Yeni Duyuru**’ya tıklayın
   
    ![][6]
3. Kampanyanızı oluşturacak girişleri yapın **[Android]**
   
   * Kampanyanıza bir **Ad** verin. 
   * **Teslimat Türü** olarak *Sistem bildirimi* *Basit* seçeneğini belirleyin
   * **Teslimat zamanı** olarak *"Her Zaman"* seçeneğini belirleyin
   * Bildiriminiz için, anında iletimin ilk satırı olacak olan bir **Başlık** girin.
   * Bildiriminiz için, ileti gövdesi görevini görecek olan bir **İleti** girin. 
     
     ![][11]
4. Kampanyanızı oluşturacak girişleri yapın **[iOS]**
   
   * Kampanyanıza bir **Ad** verin. 
   * **Teslimat zamanı** olarak *"Out of app only"* (Yalnızca uygulama dışında) seçeneğini belirleyin
   * Bildiriminiz için, anında iletimin ilk satırı olacak olan bir **Başlık** girin.
   * Bildiriminiz için, ileti gövdesi görevini görecek olan bir **İleti** girin. 
     
     ![][12]
5. Kaydırarak aşağı gidin ve içerik bölümünde **Yalnızca bildirim**’i seçin
   
    ![][8]
6. [İsteğe bağlı] Bir Eylem URL'si de sağlayabilirsiniz. Eklentinin **AZME\_REDIRECT\_URL** değişkeni yapılandırılırken sağlanmış olan bir URL şemasını kullandığından emin olun ör. *myapp://test*.  
7. Olabilecek en temel kampanyanın ayarlarını yapmayı bitirdiniz. Şimdi kaydırarak yeniden aşağı gidin ve **Oluştur** düğmesine tıklayarak kampanyanızı kaydedin.
8. Son olarak **Etkinleştir** düğmesine tıklayarak kampanyanızı etkinleştirin
   
    ![][10]
9. Şimdi cihazınızda veya öykünücünüzde bu kampanyanın bir parçası olan bir anında iletme bildirimi görmelisiniz. 

## <a id="next-steps"></a>Sonraki Adımlar
[Cordova Mobile Engagement SDK ile kullanılabilen tüm metotlara genel bakış](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png


