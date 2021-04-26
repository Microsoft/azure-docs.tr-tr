---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: c6e8be5462e0caffec7a1c88dae54f3f818ec323
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498767"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-android-ios.md)]


**Android Için Hero örneğini çağıran** Azure Iletişim Hizmetleri grubu, Android SDK çağıran iletişim hizmetlerinin, ses ve video içeren bir grup çağırma deneyimi oluşturmak için nasıl kullanılabileceğini gösterir. Bu örnek hızlı başlangıçta, örneği ayarlamayı ve çalıştırmayı öğreneceksiniz. Bağlam için örneğe genel bir bakış sağlanır.

## <a name="download-code"></a>Kodu indirin

[GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)'da bu örneğe yönelik projeyi bulun. [Ekiplerin birlikte çalışması](../../concepts/teams-interop.md) ile örnek bir sürümü ayrı bir [dalda](https://github.com/Azure-Samples/communication-services-android-calling-hero/tree/feature/teams_interop)bulunabilir.

## <a name="overview"></a>Genel Bakış

Örnek, hem ses hem de video çağrılmasını sağlayan bir arama deneyimi oluşturmak için Azure Iletişim Hizmetleri Android SDK 'larını kullanan yerel bir Android uygulamasıdır. Uygulama, daha sonra Azure Iletişim Hizmetleri SDK 'sını başlatmak için kullanılan erişim belirteçlerini sağlamak için bir sunucu tarafı bileşeni kullanır. Bu sunucu tarafı bileşeni yapılandırmak için, [Azure işlevleri öğreticisiyle güvenilir hizmeti](../../tutorials/trusted-service-tutorial.md) takip edin.

Örnek şöyle görünür:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Örnek uygulamanın giriş sayfasını gösteren ekran görüntüsü.":::

"Yeni çağrıya başla" düğmesine bastığınızda, Android uygulaması yeni bir çağrı oluşturur ve bu dosyayı birleştirir. Bu uygulama, var olan çağrının KIMLIĞINI belirterek mevcut bir Azure Iletişim Hizmetleri çağrısına katılırsanız de olanak tanır.

Bir çağrıya katıldıktan sonra, bilgisayarınıza kameranıza ve mikrofona erişim izni vermeniz istenir. Ayrıca, bir görünen ad girmeniz istenir.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Örnek uygulamanın çağrı öncesi ekranını gösteren ekran görüntüsü.":::

Görüntü adınızı ve cihazlarınızı yapılandırdıktan sonra, çağrıya katabilirsiniz. Çekirdek çağırma deneyiminin yaşadığı ana çağrı tuvalini görürsünüz.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Örnek uygulamanın ana ekranını gösteren ekran görüntüsü.":::

Ana arama ekranının bileşenleri:

- **Medya Galerisi**: katılımcıların gösterildiği ana aşama. Bir katılımcının Kamerası etkinse, video akışları burada gösterilir. Her katılımcının, görünen adını ve video akışını (bir tane olduğunda) gösteren bir tek kutucuğu vardır. Galeri birden çok katılımcıyı destekler ve katılımcılar eklendiğinde veya çağrıya kaldırıldığında güncelleştirilir.
- **Eylem çubuğu**: Bu, birincil çağrı denetimlerinin bulunduğu yerdir. Bu denetimler, videonuzu ve mikrofonunuzu açmanıza/kapatmanızı, ekranınızı paylaşmanıza ve çağrıdan ayrılmasına olanak tanır.

Aşağıda, önkoşulları ve örneği ayarlama adımlarını hakkında daha fazla bilgi bulacaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) bilgisayarınızda çalışıyor
- Bir Azure Iletişim Hizmetleri kaynağı. Ayrıntılar için bkz. [Azure Iletişim kaynağı oluşturma](../../quickstarts/create-communication-resource.md).
- Erişim belirteçleri getirmek için [kimlik doğrulama uç noktası](../../tutorials/trusted-service-tutorial.md) çalıştıran bir Azure işlevi.

## <a name="running-sample-locally"></a>Örneği yerel olarak çalıştırma

Grup çağırma örneği, Android Studio kullanılarak yerel olarak çalıştırılabilir. Geliştiriciler, uygulamayı test etmek için fiziksel cihazlarını ya da öykünücüyü kullanabilirler.

### <a name="before-running-the-sample-for-the-first-time"></a>Örneği ilk kez çalıştırmadan önce

1. Android Studio açın ve seçin `Open an Existing Project`
2. `AzureCalling`Örnek için indirilen sürümün içindeki klasörü açın.
3. Güncelleştirilecek uygulama/varlıklar ' ı genişletin `appSettings.properties` . Anahtar için değeri `communicationTokenFetchUrl` kimlik doğrulama uç noktasının ön koşul olarak AYARLANDıĞı URL olacak şekilde ayarlayın.

### <a name="run-sample"></a>Örnek Çalıştır

Android Studio ' de örneği derleyin ve çalıştırın.

## <a name="optional-securing-an-authentication-endpoint"></a>Seçim Kimlik doğrulama uç noktasının güvenliğini sağlama

Tanıtım amacıyla bu örnek, varsayılan olarak bir Azure Iletişim Hizmetleri belirteci getirmek için genel olarak erişilebilen bir uç nokta kullanır. Üretim senaryolarında kendi belirteçlerinizi sağlamak için kendi güvenli uç noktanızı kullanmanızı öneririz.

Ek yapılandırma ile bu örnek, uygulamanın bir Azure Iletişim Hizmetleri belirtecini getirmesi için Kullanıcı oturum açması gereken **Azure Active Directory** (Azure AD) korumalı bir uç noktaya bağlanmayı destekler. Aşağıdaki adımlara bakın:

1. Uygulamanızda Azure Active Directory kimlik doğrulamasını etkinleştirin.  
   - [Uygulamanızı Azure Active Directory altına kaydetme (Android Platform ayarlarını kullanarak)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [App Service veya Azure Işlevleri uygulamanızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma](../../../app-service/configure-authentication-provider-aad.md)

2. Azure Active Directory uygulama kayıtları altında kayıtlı uygulamanıza genel bakış sayfasına gidin. , `Package name` ,, `Signature hash``MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure portal Yapılandırma Azure Active Directory.":::

3. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` `isAADAuthEnabled` Azure Active Directory etkinleştirmek için Düzenle ve ayarla
4. Düzenleyin `AndroidManifest.xml` ve `android:path` anahtar deposu imza karmasını olarak ayarlayın. Seçim. Geçerli değer, paketlenmiş hata ayıklama. KeyStore 'dan karma kullanır. Farklı bir anahtar deposu kullanılıyorsa, bu güncelleştirilmiş olmalıdır.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Azure portal MSAL Android yapılandırmasını kopyalayın ve öğesine yapıştırın `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` . Ekle "account_mode": "SINGLE"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`Anahtar değerini, `communicationTokenFetchUrl` güvenli kimlik doğrulama uç noktanızın URL 'si olacak şekilde düzenleyin ve ayarlayın.
7. `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`Kapsamlardan anahtar için değeri düzenleyin ve ayarlayın `aadScopes` `Azure Active Directory` `Expose an API`

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../../quickstarts/create-communication-resource.md#clean-up-resources)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

>[!div class="nextstepaction"]
>[GitHub 'dan örneği indirin](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Çağıran SDK 'yı kullanma](../../quickstarts/voice-video-calling/calling-client-samples.md) hakkında bilgi edinin
- [Nasıl çalıştığını çağırma](../../concepts/voice-video-calling/about-call-types.md) hakkında daha fazla bilgi edinin

### <a name="additional-reading"></a>Ek okuma

- [Azure Iletişim GitHub](https://github.com/Azure/communication) -resmi GitHub sayfasında daha fazla örnek ve bilgi bulun
- [Örnekler](./../overview.md) -örneklere genel bakış sayfamızda daha fazla örnek ve örnek bulun.
- [Azure Iletişim çağırma özellikleri](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) -[Azure COMMUNICATION Android çağıran SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling) hakkında daha fazla bilgi edinmek için
