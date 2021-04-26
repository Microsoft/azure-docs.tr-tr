---
title: 'Öğretici: Workday mobil uygulamasıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Workday mobil uygulaması arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 94ab61a5c6335d43842ada48f17572c186af773c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077202"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Öğretici: Workday mobil uygulamasıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD), koşullu erişim ve Intune 'u Workday mobil uygulaması ile tümleştirmeyi öğreneceksiniz. Workday mobil uygulamasını Microsoft ile tümleştirdiğinizde şunları yapabilirsiniz:

* Oturum açma işleminden önce cihazların ilkelerinizle uyumlu olduğundan emin olun.
* Kullanıcıların şirket verilerine güvenli bir şekilde erişmesini sağlamak için Workday mobil uygulamasına denetimler ekleyin. 
* Azure AD 'de Workday 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Workday 'de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için:

* Workday 'yi Azure AD ile tümleştirin.
* [Workday ile çoklu oturum açma (SSO) tümleştirmesi Azure Active Directory](./workday-tutorial.md)okuyun.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD koşullu erişim ilkelerini ve Intune 'U Workday mobil uygulamasıyla yapılandırıp test edersiniz.

Çoklu oturum açmayı (SSO) etkinleştirmek için, Workday Federasyon uygulamasını Azure AD ile yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Workday ile çoklu oturum açma (SSO) tümleştirmesi Azure Active Directory](./workday-tutorial.md).

> [!NOTE] 
> Workday, Intune uygulama koruma ilkelerini desteklemiyor. Koşullu erişimi kullanmak için mobil cihaz yönetimi kullanmanız gerekir.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Kullanıcıların Workday mobil uygulamasına erişimi olduğundan emin olun

Workday 'yi mobil uygulamalarına erişime izin verecek şekilde yapılandırın. Workday Mobile için aşağıdaki ilkeleri yapılandırmanız gerekir:

1. İşlevsel alan raporu için etki alanı güvenlik ilkelerine erişin.
1. Uygun güvenlik ilkesini seçin:
    * Mobil kullanım-Android
    * Mobil kullanım-iPad
    * Mobil kullanım-iPhone
1. **Izinleri Düzenle**' yi seçin.
1. Güvenlik gruplarına rapor veya görev güvenliği sağlanabilir öğelerine erişim izni vermek için **görünüm veya değiştirme** onay kutusunu seçin.
1. Güvenlik gruplarına tümleştirme ve rapor veya görev güvenliği sağlanabilir eylemlerine erişim izni vermek için **Al veya koy** onay kutusunu seçin.

Bekleyen **Güvenlik Ilkesi değişikliklerini etkinleştir**' i çalıştırarak bekleyen güvenlik ilkesi değişikliklerini etkinleştirin.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Workday Mobile Browser 'da Workday oturum açma sayfasını aç

Workday mobil uygulamasına koşullu erişim uygulamak için, uygulamayı bir dış tarayıcıda açmanız gerekir. **Kiracı kurulumunu Düzenle-güvenlik** bölümünde, **yerel uygulamalar için mobil tarayıcı SSO 'su etkinleştir**' i seçin. Bu, Intune 'un iOS için cihaza ve Android için iş profilinde yüklü olmasını gerektirir.

![Işgünü mobil tarayıcı oturum açma ekranının ekran görüntüsü.](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Koşullu erişim ilkesini ayarlama

Bu ilke yalnızca bir iOS veya Android cihazında oturum açmayı etkiler. Tüm platformlara genişletmek istiyorsanız, **herhangi bir cihaz** seçin. Bu ilke, cihazın ilkeyle uyumlu olmasını gerektirir ve bunu Intune aracılığıyla doğrular. Android 'in iş profilleri olduğundan, bu, iş profilinde oturum açtıklarında ve uygulamayı Intune şirket portalı aracılığıyla yüklemedikleri takdirde tüm kullanıcıların Workday 'e kaydolmasını engeller. Aynı durumun geçerli olduğundan emin olmak için iOS için bir ek adım vardır.

Workday aşağıdaki erişim denetimlerini destekler:
* Çok faktörlü kimlik doğrulaması gerektir
* Cihazın uyumlu olarak işaretlenmesini gerektir

Workday uygulaması aşağıdakileri desteklemez:
* Onaylanan istemci uygulaması gerektir
* Uygulama koruma ilkesi gerektir (Önizleme)

Workday 'i yönetilen bir cihaz olarak ayarlamak için aşağıdaki adımları gerçekleştirin:

![Yalnızca yönetilen cihazların ve bulut uygulamalarının veya eylemlerinin ekran görüntüsü.](./media/workday-tutorial/managed-devices-only.png)

1. **Ana**  >  **Microsoft Intune**  >  **koşullu erişim-ilkeleri**' ni seçin. Ardından **yalnızca yönetilen cihazlar**' ı seçin. 

1. **Yalnızca yönetilen cihazlarda**, **ad**' ın altında **yalnızca yönetilen cihazlar** ' ı seçin ve ardından **bulut uygulamaları veya eylemler**' i seçin.

1. **Bulut uygulamaları veya eylemleri**:

    a. Bu ilkenin **bulut uygulamaları** için **geçerli olduğunu Değiştir ' i seçin** .

    b. **Ekle**' de, **Uygulama Seç**' i seçin.

    c. **Seçim** listesinden **Workday**' i seçin.

    d. **Bitti** seçeneğini belirleyin.

1. İlkeyi **Açık** olarak **etkinleştirin** .

1. **Kaydet**’i seçin.

Erişim **verme** için aşağıdaki adımları gerçekleştirin:

![Yalnızca yönetilen cihazların ekran görüntüsü ve Izin ver.](./media/workday-tutorial/managed-devices-only-2.png)

1. **Ana**  >  **Microsoft Intune**  >  **koşullu erişim-ilkeleri**' ni seçin. Ardından **yalnızca yönetilen cihazlar**' ı seçin. 

1. **Yalnızca yönetilen cihazlarda**, **ad**' ın altında **yalnızca yönetilen cihazlar**' ı seçin. **Erişim denetimleri** altında **Ver**’i seçin.

1. **Verme** aşamasında:

    a. **Erişim izni** olarak uygulanacak denetimleri seçin.

    b. **Cihazın uyumlu olarak işaretlenmesini gerektir**’i seçin.

    c. **Seçili denetimlerden birini gerektir**' i seçin.

    d. **Seç**’i seçin.

1. İlkeyi **Açık** olarak **etkinleştirin** .

1. **Kaydet**’i seçin.

## <a name="set-up-device-compliance-policy"></a>Cihaz Uyumluluk ilkesini ayarlama

İOS cihazlarının yalnızca mobil cihaz yönetimi tarafından yönetilen Workday aracılığıyla oturum açabildiğinden emin olmak için, kısıtlı uygulamalar listesine **com. Workday. workdayapp** ekleyerek Uygulama Mağazası uygulamasını engellemeniz gerekir. Bu, yalnızca şirket portalı aracılığıyla iş günü yüklenmiş cihazların Workday 'e erişebilmesini sağlar. Tarayıcı için cihazlar yalnızca cihaz Intune tarafından yönetiliyorsa ve yönetilen bir tarayıcı kullanıyorsa Workday 'e erişebilir.

![İOS cihaz uyumluluk ilkesinin ekran görüntüsü.](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Intune uygulama yapılandırma ilkelerini ayarlama

| Senaryo | Anahtar değer çiftleri |
|----------------------------------------------------------------------------------------   |-----------|
| Kiracı ve Web adresi alanlarını otomatik olarak doldur:<br>Android for Work profillerini etkinleştirdiğinizde Android üzerinde ● Workday.<br>İPad ve iPhone üzerinde ● Workday.     | Kiracınızı yapılandırmak için şu değerleri kullanın: <br>● Yapılandırma anahtarı = `UserGroupCode`<br>● Değer türü = dize <br>● Yapılandırma değeri = kiracı adınız. Örnek: `gms`<br>Web adresinizi yapılandırmak için şu değerleri kullanın:<br>● Yapılandırma anahtarı = `AppServiceHost`<br>● Değer türü = dize<br>● Yapılandırma değeri = kiracınızın temel URL 'SI. Örnek: `https://www.myworkday.com`                                | 
| İPad ve iPhone üzerinde Workday için bu eylemleri devre dışı bırakın:<br>● Kes, Kopyala ve Yapıştır<br>● Yazdır                       | `False`İşlevselliği devre dışı bırakmak için bu anahtarlar üzerinde değeri (Boole) olarak ayarlayın:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Android 'de Workday için ekran görüntülerini devre dışı bırakın. |`False` `AllowScreenshots` İşlevselliği devre dışı bırakmak için anahtar üzerinde değeri (Boole) olarak ayarlayın.|
| Kullanıcılarınız için önerilen güncelleştirmeleri devre dışı bırakın.|`False` `AllowSuggestedUpdates` İşlevselliği devre dışı bırakmak için anahtar üzerinde değeri (Boole) olarak ayarlayın.|
|Mobil kullanıcıları tercih ettiğiniz uygulama deposuna yönlendirmek için uygulama mağazası URL 'sini özelleştirin.|Uygulama Mağazası URL 'sini değiştirmek için şu değerleri kullanın:<br>● Yapılandırma anahtarı = `AppUpdateURL`<br>● Değer türü = dize<br> ● Yapılandırma değeri = App Store URL 'SI|

## <a name="ios-configuration-policies"></a>iOS yapılandırma ilkeleri

1. [Azure portalına](https://portal.azure.com/) gidip oturum açın.
1. **Intune 'u** arayın veya listeden pencere öğesini seçin.
1. **İstemci uygulamaları**  >  **uygulamalar**  >  **uygulama yapılandırma ilkeleri**' ne gidin. Ardından **+**  >  **yönetilen cihaz** Ekle ' yi seçin.
1. Bir ad girin.
1. **Platform** altında **IOS/ıpados**' ı seçin.
1. **Ilişkili uygulama** altında, eklediğiniz IOS için Workday uygulamasını seçin.
1. **Yapılandırma ayarları**' nı seçin. **Yapılandırma ayarları biçimi** altında, **XML verisi gir**' i seçin.
1. Örnek bir XML dosyası aşağıda verilmiştir. Uygulamak istediğiniz konfigürasyonları ekleyin. `STRING_VALUE`Kullanmak istediğiniz dize ile değiştirin. `<true /> or <false />`Veya ile `<true />` değiştirin `<false />` . Bir yapılandırma eklememeniz durumunda bu örnek olarak ayarlanır `True` .

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. **Add (Ekle)** seçeneğini belirleyin.
1. Sayfayı yenileyin ve yeni oluşturulan ilkeyi seçin.
1. **Atamalar**' ı seçin ve uygulamanın uygulanmasını istediğinizi seçin.
1. **Kaydet**’i seçin.

## <a name="android-configuration-policies"></a>Android yapılandırma ilkeleri

1. [Azure portalına](https://portal.azure.com/) gidip oturum açın.
2. **Intune 'u** arayın veya listeden pencere öğesini seçin.
3. **İstemci uygulamaları**  >  **uygulamalar**  >  **uygulama yapılandırma ilkeleri**' ne gidin. Ardından **+**  >  **yönetilen cihaz** Ekle ' yi seçin.
5. Bir ad girin. 
6. **Platform** altında **Android**' i seçin.
7. **Ilişkili uygulama** altında, eklediğiniz Android uygulaması için Workday 'yi seçin.
8. **Yapılandırma ayarları**' nı seçin. **Yapılandırma ayarları biçimi** altında **JSON verisi gir**' i seçin.
