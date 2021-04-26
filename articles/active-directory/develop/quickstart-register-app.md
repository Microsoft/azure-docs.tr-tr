---
title: "Hızlı başlangıç: uygulamayı Microsoft Identity platformu 'nda kaydetme | Mavisi"
description: Bu hızlı başlangıçta, bir uygulamayı Microsoft Identity platformu ile nasıl kaydedeceğinizi öğrenirsiniz.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 7b7adde8a6e60042bec7695e4553d6470e548316
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055697"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme

Bu hızlı başlangıçta, Microsoft kimlik platformunun uygulamanız ve kullanıcıları için kimlik doğrulama ve yetkilendirme hizmetleri sağlayabilmesi adına bir uygulamayı Azure portal kaydedersiniz.

Microsoft Identity platformu, yalnızca kayıtlı uygulamalar için kimlik ve erişim yönetimi (ıAM) gerçekleştirir. Bir Web veya mobil uygulama gibi bir istemci uygulaması ya da bir istemci uygulamasını yedekleyen bir Web API 'SI olup olmadığı, kaydetme, uygulamanız ile kimlik sağlayıcısı arasında bir güven ilişkisi oluşturur, Microsoft Identity platform.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Kiracı hızlı başlangıcı [ayarlama](quickstart-create-new-tenant.md) işleminin tamamlanması.

## <a name="register-an-application"></a>Bir uygulamayı kaydetme

Uygulamanızı kaydetmek, uygulamanız ve Microsoft Identity platform arasında bir güven ilişkisi kurar. Güven tek yönlü: uygulamanız Microsoft Identity platformu ' na güvendiğinden, bunun diğer bir yolu değildir.

Uygulama kaydını oluşturmak için aşağıdaki adımları izleyin:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden çok kiracıya erişiminiz varsa, en üstteki menüde, bir uygulamayı kaydetmek istediğiniz kiracıyı seçmek için **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir görünen **ad** girin. Uygulamanızın kullanıcıları, uygulamayı kullandıklarında, örneğin oturum açma sırasında görünen adı görebilirler.
    Görünen adı dilediğiniz zaman değiştirebilir ve birden çok uygulama kaydı aynı adı paylaşabilir. Uygulama kaydının, görüntüleme adı değil, otomatik olarak oluşturulan uygulama (istemci) KIMLIĞI, uygulamanızı kimlik platformunda benzersiz bir şekilde tanımlar.
1. Bazen *oturum açma kiti* olarak adlandırılan, uygulamayı ne şekilde kullanacağınızı belirtin.

    | Desteklenen hesap türleri | Description |
    |-------------------------|-------------|
    | **Yalnızca bu kuruluş dizinindeki hesaplar** | *Kiracınızda* yalnızca kullanıcılar (veya konuklar) tarafından kullanılacak bir uygulama oluşturuyorsanız bu seçeneği belirleyin.<br><br>Genellikle, *iş kolu* (LOB) uygulaması olarak adlandırılan bu uygulama, Microsoft Identity platformunda *tek kiracılı* bir uygulamadır. |
    | **Herhangi bir kuruluş dizinindeki hesaplar** | *Herhangi bir* Azure Active Directory (Azure AD) kiracısındaki kullanıcıların uygulamanızı kullanabilbilmesini istiyorsanız bu seçeneği belirleyin. Örneğin, birden çok kuruluşa sağlamak istediğiniz bir hizmet olarak yazılım (SaaS) uygulaması oluşturuyorsanız, bu seçenek uygundur.<br><br>Bu tür bir uygulama, Microsoft Identity platformunda *çok kiracılı* bir uygulama olarak bilinir. |
    | **Herhangi bir kuruluş dizinindeki hesaplar ve kişisel Microsoft hesapları** | En geniş müşteri kümesini hedeflemek için bu seçeneği belirleyin.<br><br>Bu seçeneği belirlediğinizde, kişisel *Microsoft hesapları* olan kullanıcıları da destekleyebilen *çok kiracılı* bir uygulama kaydettiriyorsunuz. |
    | **Kişisel Microsoft hesapları** | Yalnızca kişisel Microsoft hesapları olan kullanıcılar için bir uygulama oluşturuyorsanız bu seçeneği belirleyin. Kişisel Microsoft hesapları, Skype, Xbox, canlı ve hotmail hesaplarını içerir. |

1. **Yeniden yönlendirme URI 'si (isteğe bağlı)** için hiçbir şey girmeyin. Sonraki bölümde bir yeniden yönlendirme URI 'SI yapılandıracaksınız.
1. İlk uygulama kaydını gerçekleştirmek için **Kaydet** ' i seçin.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Bir uygulama kaydet bölmesinin gösterildiği bir Web tarayıcısında Azure portal ekran görüntüsü.":::

Kayıt tamamlandığında, Azure portal uygulama kaydının **genel bakış** bölmesini görüntüler. **Uygulama (istemci) kimliğini** görürsünüz. *ISTEMCI kimliği* olarak da adlandırılan bu değer, uygulamanızı Microsoft Identity platformunda benzersiz şekilde tanımlar. 

> [!IMPORTANT]
> Yeni uygulama kayıtları varsayılan olarak kullanıcılara gizlidir. Kullanıcıların uygulamayı [uygulamalarım sayfasında](../user-help/my-apps-portal-end-user-access.md) görmesini hazırsanız bu ayarı etkinleştirebilirsiniz. Uygulamayı etkinleştirmek için Azure Portal   >  **Kurumsal uygulamalar** Azure Active Directory gidin ve uygulamayı seçin. Sonra **Özellikler** sayfasında, **kullanıcılar için görünür mı?** Evet.

Uygulamanızın kodu veya daha yaygın olarak uygulamanızda kullanılan bir kimlik doğrulama kitaplığı, istemci KIMLIĞINI de kullanır. Kimlik, kimlik platformundan aldığı güvenlik belirteçlerini doğrulamaya bir parçası olarak kullanılır.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Bir uygulama kaydının genel bakış bölmesini gösteren bir Web tarayıcısında Azure portal ekran görüntüsü.":::

## <a name="add-a-redirect-uri"></a>Yeniden yönlendirme URI 'SI Ekle

*Yeniden yönlendirme URI 'si* , Microsoft Identity platformunun bir kullanıcının istemcisini yeniden yönlendirdiği ve kimlik doğrulamasından sonra güvenlik belirteçleri gönderdiği konumdur.

Bir üretim Web uygulamasında, örneğin, yeniden yönlendirme URI 'SI genellikle uygulamanızın çalıştırıldığı ortak bir uç noktadır `https://contoso.com/auth-response` . Geliştirme sırasında, uygulamanızı yerel olarak çalıştırdığınız (veya gibi) uç noktasını da eklemek yaygın bir uygulamadır `https://127.0.0.1/auth-response` `http://localhost/auth-response` .

[Platform ayarlarını](#configure-platform-settings)yapılandırarak kayıtlı uygulamalarınız için yeniden yönlendirme URI 'leri ekler ve değiştirirsiniz.

### <a name="configure-platform-settings"></a>Platform ayarlarını yapılandırma

Yeniden yönlendirme URI 'Leri dahil olmak üzere her uygulama türünün ayarları, Azure portal **Platform yapılandırmalarında** yapılandırılır. **Web** ve **tek sayfalı uygulamalar** gibi bazı platformlar, BIR yeniden yönlendirme URI 'sini el ile belirtmenizi gerektirir. Mobil ve Masaüstü gibi diğer platformlar için, diğer ayarlarını yapılandırırken sizin için oluşturulan yeniden yönlendirme URI 'Leri arasından seçim yapabilirsiniz.

Hedeflediğiniz platformu veya cihazı temel alan uygulama ayarlarını yapılandırmak için:

1. Azure portal, **uygulama kayıtları**' de Uygulamanızı seçin.
1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Platform yapılandırması** altında **Platform Ekle**' yi seçin.
1. **Platformları Yapılandır** altında, ayarlarını yapılandırmak için uygulama türü (Platform) kutucuğunu seçin.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Azure portal platform yapılandırma bölmesinin ekran görüntüsü." border="false":::

    | Platform | Yapılandırma ayarları |
    | -------- | ---------------------- |
    | **Web** | Uygulamanız için bir **yeniden yönlendirme URI 'si** girin. Bu URI, Microsoft Identity platformunun bir kullanıcının istemcisini yeniden yönlendirdiği ve kimlik doğrulamasından sonra güvenlik belirteçleri gönderdiği konumdur.<br/><br/>Sunucuda çalışan standart Web uygulamaları için bu platformu seçin. |
    | **Tek sayfalı uygulama** | Uygulamanız için bir **yeniden yönlendirme URI 'si** girin. Bu URI, Microsoft Identity platformunun bir kullanıcının istemcisini yeniden yönlendirdiği ve kimlik doğrulamasından sonra güvenlik belirteçleri gönderdiği konumdur.<br/><br/>JavaScript veya angular, Vue.js, React.js veya Blazor WebAssembly gibi bir çatı kullanarak istemci tarafı Web uygulaması oluşturuyorsanız bu platformu seçin. |
    | **iOS/macOS** | Uygulama **paket kimliğini** girin. **Derleme ayarları** 'Nda veya *Info. plist* içinde Xcode içinde bulun.<br/><br/>Bir **paket kimliği** belirttiğinizde sizin için bir yeniden yönlendirme URI 'si oluşturulur. |
    | **Android** | Uygulama **paketi adını** girin. *AndroidManifest.xml* dosyasında bulun. Ayrıca **imza karmasını** oluşturun ve girin.<br/><br/>Bu ayarları belirttiğinizde sizin için bir yeniden yönlendirme URI 'SI oluşturulur. |
    | **Mobil ve Masaüstü uygulamaları** | **Önerilen yeniden yönlendirme URI**'lerinden birini seçin. Veya özel bir **yeniden yönlendirme URI 'si** belirtin.<br/><br/>Katıştırılmış tarayıcı kullanan masaüstü uygulamaları için<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Sistem tarayıcısı kullanan masaüstü uygulamaları için<br/>`http://localhost`<br/><br/>En son Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanmayan veya bir aracı kullanmayan mobil uygulamalar için bu platformu seçin. Masaüstü uygulamaları için de bu platformu seçin. |
1. Platform yapılandırmasını gerçekleştirmek için **Yapılandır** ' ı seçin.

### <a name="redirect-uri-restrictions"></a>Yeniden yönlendirme URI kısıtlamaları

Uygulama kaydına eklediğiniz yeniden yönlendirme URI 'Leri biçiminde bazı kısıtlamalar vardır. Bu kısıtlamalar hakkındaki ayrıntılar için bkz. [yeniden YÖNLENDIRME URI 'si (yanıt URL 'si) kısıtlamaları ve sınırlamaları](reply-url.md).

## <a name="add-credentials"></a>Kimlik bilgileri ekle

Kimlik bilgileri, bir Web API 'sine erişen [Gizli istemci uygulamaları](msal-client-applications.md) tarafından kullanılır. Gizli istemci örnekleri [Web Apps](scenario-web-app-call-api-overview.md), diğer [Web API 'leri](scenario-protected-web-api-overview.md)veya [hizmet türü ve Daemon türünde uygulamalardır](scenario-daemon-overview.md). Kimlik bilgileri, uygulamanızın çalışma zamanında Kullanıcı etkileşimi gerektirmeksizin kendisi olarak kimlik doğrulaması yapmasına olanak sağlar. 

Gizli istemci uygulaması kaydınız için kimlik bilgileri olarak hem sertifikaları hem de istemci gizli dizilerini (bir dize) ekleyebilirsiniz.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Uygulama kaydında sertifikalar ve gizlilikler bölmesini gösteren Azure portal ekran görüntüsü.":::

### <a name="add-a-certificate"></a>Sertifika ekle

Bazen *ortak anahtar* olarak da bilinen bir sertifika, önerilen kimlik bilgisi türüdür. İstemci gizliliğine göre daha fazla güvence sağlar. Uygulamanızda kimlik doğrulama yöntemi olarak bir sertifika kullanma hakkında daha fazla bilgi için bkz. [Microsoft Identity Platform uygulaması kimlik doğrulama sertifikası kimlik bilgileri](active-directory-certificate-credentials.md).

1. Azure portal, **uygulama kayıtları**' de Uygulamanızı seçin.
1. **Sertifikalar & parolaları**  >  **karşıya yükleme sertifikası**' nı seçin.
1. Karşıya yüklemek istediğiniz dosyayı seçin. Şu dosya türlerinden biri olmalıdır: *. cer*, *. pek*, *. CRT*.
1. **Add (Ekle)** seçeneğini belirleyin.

### <a name="add-a-client-secret"></a>İstemci parolası ekleme

İstemci parolası, *uygulama parolası* olarak da bilinir. Bu, uygulamanızın kimliğini kimlik için bir sertifika yerine kullanabileceği bir dize değeridir. İstemci parolası, kullanılacak iki kimlik bilgisi türünün daha kolay bir türüdür. Genellikle geliştirme sırasında kullanılır, ancak bir sertifikadan daha az güvenli kabul edilir. Uygulamalarınızdaki, üretimde çalışan sertifikaları kullanın. 

Uygulama güvenliği önerileri hakkında daha fazla bilgi için bkz. [Microsoft Identity platform en iyi uygulamaları ve önerileri](identity-platform-integration-checklist.md#security).

1. Azure portal, **uygulama kayıtları**' de Uygulamanızı seçin.
1. **Sertifikalar & parolaları** seçin  >   **yeni istemci gizli anahtarı**.
1. İstemci gizli diziniz için bir açıklama ekleyin.
1. Bir süre seçin.
1. **Add (Ekle)** seçeneğini belirleyin.
1. *Gizli dizi değerini* istemci uygulama kodunuzda kullanmak üzere kaydedin. Bu gizli değer bu sayfadan ayrıldıktan sonra *hiçbir şekilde hiçbir şekilde gösterilmez* .


## <a name="next-steps"></a>Sonraki adımlar

İstemci uygulamalarının genellikle bir Web API 'sindeki kaynaklara erişmesi gerekir. İstemci uygulamanızı Microsoft Identity platformunu kullanarak koruyabilirsiniz. Platformu, Web API 'nize kapsamlı ve izin tabanlı erişimi yetkilendirmek için de kullanabilirsiniz.

Web API 'niz için başka bir uygulama kaydı oluşturmak ve kapsamlarını göstermek için serideki bir sonraki hızlı başlangıca gidin.

> [!div class="nextstepaction"]
> [Web API 'sini kullanıma sunmak için uygulama yapılandırma](quickstart-configure-app-expose-web-apis.md)
