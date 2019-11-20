---
title: Web API 'Lerine erişmek için bir uygulama yapılandırma-Microsoft Identity platform
description: Microsoft kimlik platformuyla kaydedilen uygulamaları yeniden yönlendirme URI’leri, kimlik bilgileri veya web API’lerine erişmek için izinleri içerecek şekilde yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: afa757020ff6de3be23403b78fd9a12c2de97016
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106599"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Hızlı başlangıç: Web API 'Lerine erişmek için bir istemci uygulaması yapılandırma

Bir web/gizli istemci uygulamasının kimlik doğrulaması gerektiren bir yetkilendirme akışına dahil olabilmesi (ve erişim belirteci alabilmesi) için güvenli kimlik bilgileri kullanması gerekir. Azure portal tarafından desteklenen varsayılan kimlik doğrulaması yöntemi istemci kimliği ve gizli anahtar kullanımıdır.

Ayrıca, istemcinin bir kaynak uygulaması tarafından kullanıma sunulan web API'sine (Microsoft Graph API gibi) erişebilmesi için, onay çerçevesi istenen izinlere bağlı olarak istemcinin gerekli izni almasını sağlar. Varsayılan olarak, tüm uygulamalar Microsoft Graph API’sinden izinleri seçebilir. [Graph API “Oturum açma ve kullanıcı profilini okuma” izni](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) varsayılan olarak seçilir. İstenen her web API'si için [iki izin türü](developer-glossary.md#permissions) arasından seçim yapabilirsiniz:

* **Uygulama izinleri** - İstemci uygulamanızın web API'sine kendisi olarak (kullanıcı bağlamı olmadan) doğrudan erişmesi gerekir. Bu izin türü için yönetici onayı gerekir ve bu tür genel (masaüstü ve mobil) istemci uygulamalarında kullanılamaz.
* **Temsilcili izinler** - İstemci uygulamanızın web API'sine oturum açmış kullanıcı olarak erişmesi gerekir ancak erişim seçilen izinle sınırlı olur. Yönetici onayı gerektirmediği sürece bu izin türü bir kullanıcı tarafından verilebilir.

  > [!NOTE]
  > Bir uygulamaya temsilcili izin eklenmesi kiracı içindeki kullanıcılara otomatik olarak onay vermez. Yöneticinin tüm kullanıcıların adına onay vermemesi durumunda kullanıcıların yine çalışma zamanında eklenen temsilcili izinler için el ile onay vermesi gerekir.

Bu hızlı başlangıçta, uygulamanızı aşağıdakiler için yapılandırmayı öğreneceksiniz:

* [Uygulamanıza yeniden yönlendirme URI’leri ekleme](#add-redirect-uris-to-your-application)
* [Uygulamanızın gelişmiş ayarlarını yapılandırma](#configure-advanced-settings-for-your-application)
* [Desteklenen hesap türlerini değiştirme](#modify-supported-account-types)
* [Web uygulamanıza kimlik bilgileri ekleme](#add-credentials-to-your-web-application)
* [Web API’lerine erişim izinleri ekleme](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki önkoşulları tamamladığınızdan emin olun:

* Diğer kullanıcılar veya uygulamalar tarafından kullanılması gereken uygulamaları derleme konusunda önemli olan desteklenen [izinler ve onaylar](v2-permissions-and-consent.md) hakkında bilgi edinin.
* Uygulamaların kaydedilmiş olduğu bir kiracı kullanın.
  * Kayıtlı uygulama yoksa, [Microsoft kimlik platformu ile uygulamaları kaydetmeyi öğrenin](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure portalında oturum açın ve uygulamayı seçin

Uygulamayı yapılandırmadan önce, aşağıdaki adımları izleyin:

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabınızı kullanarak [Azure portalında](https://portal.azure.com) oturum açın.
1. Hesabınız birden fazla kiracıya erişim veriyorsa, sağ üst köşede hesabınızı seçin ve Portal oturumunuzu istenen Azure AD kiracısı olarak ayarlayın.
1. Arama yapın ve **Azure Active Directory**seçin. 
1. Sol bölmeden **uygulama kayıtları**' yi seçin.
1. Yapılandırmak istediğiniz uygulamayı bulun ve seçin. Uygulamayı seçtiğinizde, uygulamanın **Genel Bakış** veya ana kayıt sayfasını görürsünüz.
1. Uygulamanızı web API'lerine erişmek üzere yapılandırmak için adımları izleyin:
    * [Uygulamanıza yeniden yönlendirme URI’leri ekleme](#add-redirect-uris-to-your-application)
    * [Uygulamanızın gelişmiş ayarlarını yapılandırma](#configure-advanced-settings-for-your-application)
    * [Desteklenen hesap türlerini değiştirme](#modify-supported-account-types)
    * [Web uygulamanıza kimlik bilgileri ekleme](#add-credentials-to-your-web-application)
    * [Web API’lerine erişim izinleri ekleme](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Uygulamanıza yeniden yönlendirme URI’leri ekleme

Uygulamanıza yeniden yönlendirme URI’si eklemek için:

1. Uygulamanın **Genel Bakış** sayfasında, **Kimlik doğrulaması** bölümünü seçin.
1. Web ve genel istemci uygulamalarına özel bir yeniden yönlendirme URI’si eklemek için:
   1. **Yeniden yönlendirme URI’si** bölümün bulun.
   1. **Web** veya **Genel istemci (mobil ve masaüstü)** seçenekleri arasından oluşturduğunuz uygulama türünü seçin.
   1. Uygulamanız için Yeniden Yönlendirme URI’sini girin.
      * Web uygulamaları için, uygulamanızın temel URL'sini girin. Örneğin `http://localhost:31544` yerel makinenizde çalışan bir web uygulamasının URL'si olabilir. Kullanıcılar, bir web istemci uygulamasında oturum açmak için bu URL'yi kullanır.
      * Genel uygulamalar için, Azure AD'nin belirteç yanıtlarını döndürmek üzere kullandığı URI'yi girin. Uygulamanıza özgü bir değer girin, örneğin: `https://MyFirstApp`.

1. Ortak istemciler (mobil, masaüstü) için önerilen Yeniden Yönlendirme URI'lerini seçemk için bu adımları izleyin:
    1. **Ortak istemciler (mobil, masaüstü) için önerilen Yeniden Yönlendirme URI'leri** bölümünü bulun.
    1. Onay kutularını kullanarak uygulamanız için uygun Yeniden Yönlendirme URI’lerini seçin. Ayrıca, özel bir yeniden yönlendirme URI 'SI de girebilirsiniz. Ne kullandığınızdan emin değilseniz, kitaplık belgelerine göz atın.

Yeniden yönlendirme URI 'Leri için uygulanan bazı kısıtlamalar vardır. [Yeniden YÖNLENDIRME URI kısıtlamaları ve sınırlamaları](https://docs.microsoft.com/azure/active-directory/develop/reply-url)hakkında daha fazla bilgi edinin.
> [!NOTE]
> Hedeflemek istediğiniz platforma veya cihaza göre uygulamanızın ayarlarını yapılandırabileceğiniz yeni **kimlik doğrulama** ayarları deneyimini deneyin.
>
> Bu görünümü görmek için varsayılan **kimlik doğrulama** sayfası görünümündeki **Yeni deneyimi deneyin** ' i seçin.
>
> ![Platform yapılandırma görünümünü görmek için "yeni deneyimi deneyin" seçeneğine tıklayın](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Bu sizi [Yeni **Platform konfigürasyonları** sayfasına](#configure-platform-settings-for-your-application)götürür.

### <a name="configure-advanced-settings-for-your-application"></a>Uygulamanızın gelişmiş ayarlarını yapılandırma

Kayıt yaptığınız uygulamaya bağlı olarak, yapılandırmanız gerekebilecek bazı ek ayarlar vardır, örneğin:

* **Oturum kapatma URL 'SI**
* Tek sayfalı uygulamalar için, **örtük izni** etkinleştirebilir ve yetkilendirme uç noktasının vermesini istediğiniz belirteçleri seçebilirsiniz.
* **Varsayılan istemci türü** bölümünde tümleşik Windows kimlik doğrulaması, cihaz kod akışı veya Kullanıcı adı/parola ile belirteç alan masaüstü uygulamaları için, **uygulamayı ortak istemci olarak değerlendir** ayarını **Evet**olarak yapılandırın.
* Microsoft hesabı hizmetiyle tümleştirilecek canlı SDK 'yı kullanan eski uygulamalar için, **LIVE SDK desteği**' ni yapılandırın. Yeni uygulamalara bu ayara gerek yoktur.
* **Varsayılan istemci türü**

### <a name="modify-supported-account-types"></a>Desteklenen hesap türlerini değiştirme

**Desteklenen hesap türleri** , uygulamayı KULLANABILECEK veya API 'ye erişebilecek kişileri belirtir.

Uygulamayı ilk kez kaydettiğinizde [Desteklenen hesap türlerini yapılandırdıktan](quickstart-register-app.md) sonra, bu ayarı yalnızca şu durumlarda uygulama bildirimi düzenleyicisini kullanarak değiştirebilirsiniz:

* Hesap türlerini **Azureadmyorg** veya **Azureadmultipleorgs** Iken **Azureadandpersonmicrosoftaccount**veya tam tersi olarak değiştirirsiniz.
* Hesap türlerini **Azureadmyorg** 'Den **Azureadmultipleorgs**olarak veya bunun tersini değiştirirsiniz.

Mevcut bir uygulama kaydı için desteklenen hesap türlerini değiştirmek için:

* Bkz. [uygulama bildirimini yapılandırma](reference-app-manifest.md) ve `signInAudience` anahtarını güncelleştirme.

## <a name="configure-platform-settings-for-your-application"></a>Uygulamanız için platform ayarlarını yapılandırma

[![platforma veya cihaza bağlı olarak uygulamanızın ayarlarını yapılandırma](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-small.png#lightbox)

Uygulama ayarlarını platforma veya cihaza göre yapılandırmak için şunları hedefliyorsanız:

1. **Platform konfigürasyonları** sayfasında **Platform Ekle** ' yi seçin ve kullanılabilir seçeneklerden birini belirleyin.

   ![Platformları Yapılandır sayfasını gösterir](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Seçtiğiniz platforma bağlı olarak ayarlar bilgilerini girin.

   | Platform                | Yapabileceği              | Yapılandırma ayarları            |
   |-------------------------|----------------------|-----------------------------------|
   | **Web uygulamaları**    | **Web**              | Uygulamanız için **yeniden yönlendirme URI 'si** girin. |
   | **Mobil uygulamalar** | **iOS**              | Info. plist veya Build Settings içinde XCode içinde bulabileceğiniz uygulamanın **paket kimliğini**girin. Paket KIMLIĞINI eklemek, otomatik olarak uygulama için bir yeniden yönlendirme URI 'SI oluşturur. |
   |                         | **Android**          | * AndroidManifest. xml dosyasında bulabileceğiniz uygulamanın **paket adını**sağlayın.<br/>* **İmza karmasını**oluşturun ve girin. İmza karmasını eklemek, otomatik olarak uygulama için bir yeniden yönlendirme URI 'SI oluşturur.  |
   | **Masaüstü + cihazlar**   | **Masaüstü + cihazlar** | Seçim. Masaüstü ve cihazlar için uygulamalar oluşturuyorsanız önerilen **Önerilen yeniden yönlendirme URI** 'lerinden birini seçin.<br/>Seçim. Azure AD 'nin kullanıcıları kimlik doğrulama isteklerine yanıt olarak yeniden yönlendireceği konum olarak kullanılan **özel bir yeniden yönlendirme URI 'si**girin. Örneğin, etkileşimi istediğiniz .NET Core uygulamaları için `https://localhost`kullanın. |

   > [!IMPORTANT]
   > En son MSAL kitaplığını kullanmayan veya bir aracı kullanmayan mobil uygulamalarda, bu uygulamalar için yeniden yönlendirme URI 'Lerini **Masaüstü + cihazlarda**yapılandırmanız gerekir.

1. Seçtiğiniz platforma bağlı olarak, yapılandırabileceğiniz ek ayarlar olabilir. **Web** Apps için şunları yapabilirsiniz:
    * Daha fazla yeniden yönlendirme URI 'si ekleyin
    * Kimlik doğrulama uç noktası tarafından verilmek istediğiniz belirteçleri seçmek için **örtülü izni** yapılandırın:
        * Tek sayfalı uygulamalar için, hem **erişim belirteçlerini** hem de **kimlik belirteçlerini** seçin
        * Web Apps için **Kimlik belirteçleri** seçin

## <a name="add-credentials-to-your-web-application"></a>Web uygulamanıza kimlik bilgileri ekleme

Web uygulamanıza kimlik bilgileri eklemek için:

1. Uygulamanın **Genel Bakış** sayfasında, **Sertifikalar ve gizli diziler** bölümünü seçin.

1. Bir sertifika eklemek için aşağıdaki adımları izleyin:

    1. **Sertifikayı karşıya yükle**’yi seçin.
    1. Yüklemek istediğiniz dosyayı seçin. Şu dosya türlerinden biri olmalıdır: .cer, .pem, .crt.
    1. **Add (Ekle)** seçeneğini belirleyin.

1. Bir istemci gizli dizisi eklemek için aşağıdaki adımları izleyin:

    1. **Yeni istemci gizli dizisi**’ni seçin.
    1. İstemci gizli diziniz için bir açıklama ekleyin.
    1. Bir süre seçin.
    1. **Add (Ekle)** seçeneğini belirleyin.

> [!NOTE]
> Yapılandırmada yaptığınız değişiklikleri kaydettikten sonra en sağdaki sütunda istemci gizli dizi değeri gösterilir. İstemci uygulamanızın kodunda kullanmak üzere **değeri kopyalamayı unutmayın**. Bu sayfadan ayrıldıktan sonra bu değere erişemezsiniz.

## <a name="add-permissions-to-access-web-apis"></a>Web API’lerine erişim izinleri ekleme

İstemcinizden kaynak API'lerine erişim izni eklemek için:

1. Uygulamanın **Genel Bakış** sayfasında, **API izinleri** bölümünü seçin.
1. **Yapılandırılan izinler** bölümünde, **izin Ekle** düğmesini seçin.
1. Varsayılan olarak, görünüm **Microsoft API’leri** arasından seçim yapmanızı sağlar. İlgilendiğiniz API’leri seçin:
    * **Microsoft API’leri** - Microsoft Graph gibi Microsoft API’leri için izinleri seçmenize olanak sağlar.
    * **Kuruluşumun kullandığı API’ler** - Kuruluşunuz tarafından kullanıma sunulan API’ler veya kuruluşunuzun tümleştirdiği API’ler için izinleri seçmenize olanak sağlar.
    * **API’lerim** - Kullanıma sunduğunuz API’ler için izinleri seçmenize olanak sağlar.
1. API’leri seçtikten sonra, **API İzinleri İsteme** sayfasını görürsünüz. API hem temsilci hem de uygulama izinlerini kullanıma sunuyorsa, uygulamanız için gereken izin türünü seçin.
1. İşiniz bittiğinde, **İzinleri ekle** seçeneğini belirleyin. İzinlerin kaydedilip tabloya eklendiği **API izinleri** sayfasına dönersiniz.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>API izinlerini ve yönetici onayı Kullanıcı arabirimini anlama

### <a name="configured-permissions"></a>Yapılandırılan izinler

Bu bölümde, uygulama nesnesinde (\uygulamanın gerekli kaynak erişim listesinin parçası olan izinler) açık olarak yapılandırılmış izinler gösterilmektedir. Bu tabloya izinleri ekleyebilir veya kaldırabilirsiniz. Yönetici olarak Ayrıca, bu bölümde bir API 'nin izinleri veya ayrı izinler kümesi için yönetici onayı verebilir/iptal edebilirsiniz.

### <a name="other-permissions-granted"></a>Diğer izinler verildi

Uygulamanız bir kiracıda kayıtlıysa, **kiracı için verilen diğer izinler**başlıklı ek bir bölüm görebilirsiniz. Bu bölümde, kiracı için verilen ancak uygulama nesnesinde açıkça yapılandırılmamış olan izinler gösterilir (örneğin, dinamik olarak istenen ve onaylanan izinler). Bu bölüm yalnızca en az bir tane geçerli izin varsa görüntülenir.

Bu bölümde görüntülenen bir API 'nin izinleri veya ayrı izinler kümesi, **yapılandırılan izinler** bölümüne eklenebilir. Yönetici olarak, bu bölümdeki tek tek API 'Ler veya izinler için yönetici onayını de iptal edebilirsiniz.

### <a name="admin-consent-button"></a>Yönetici onay düğmesi

Uygulamanız bir kiracıda kayıtlıysa, **kiracı için yönetici onayı verme** düğmesine görürsünüz. Yönetici değilseniz ya da uygulama için hiçbir izin yapılandırılmamışsa devre dışı bırakılır.
Bu düğme, yöneticinin uygulama için yapılandırılan izinlere kolayca yönetici onayı vermesini sağlar. Yönetici onay düğmesine tıkladığınızda, tüm yapılandırılmış izinleri gösteren bir onay istemiyle birlikte yeni bir pencere açılır.

> [!NOTE]
> Uygulama için yapılandırılan izinlerle izin isteminde görüntülenen izinler arasında bir gecikme vardır. İzin isteminde yapılandırılan tüm izinleri görmüyorsanız, kapatın ve yeniden başlatın.

Verilmiş ancak yapılandırılmamış izinleriniz varsa, yönetici onay düğmesine tıklarken bu izinleri nasıl işleyeceğinize karar vermeniz istenir. Bunları, yapılandırılmış izinlere ekleyebilir veya kaldırabilirsiniz.

Onay istemi, **kabul etme** veya **iptal**etme seçeneğini sağlar. **Kabul et**' i seçerseniz, yönetici izni verilir. **İptal**' i seçerseniz, yönetici onayı verilmez ve onay reddedildiğini belirten bir hata görürsünüz.

> [!NOTE]
> Yönetici onayı verme (onay isteminde **kabul et** ' i seçme) ve yönetici izninin Kullanıcı arabiriminde yansıtıldığı durum arasında bir gecikme vardır.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamalar için diğer ilgili uygulama yönetimi hızlı başlangıçları hakkında bilgi edinin:

* [Microsoft kimlik platformuna uygulama kaydetme](quickstart-register-app.md)
* [Bir uygulamayı web API'lerini kullanıma sunacak şekilde yapılandırma](quickstart-configure-app-expose-web-apis.md)
* [Bir uygulama tarafından desteklenen hesapları değiştirme](quickstart-modify-supported-accounts.md)
* [Microsoft kimlik platformu ile kaydedilmiş bir uygulamayı kaldırma](quickstart-remove-app.md)

Kayıtlı uygulamayı temsil eden iki Azure AD nesnesi ve aralarındaki ilişki hakkında daha fazla bilgi edinmek için bkz. [Uygulama nesneleri ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).

Azure Active Directory ile uygulama geliştirirken kullanmanız gereken markalama yönergeleri hakkında daha fazla bilgi edinmek için bkz. [Uygulamalar için markalama yönergeleri](howto-add-branding-in-azure-ad-apps.md).
