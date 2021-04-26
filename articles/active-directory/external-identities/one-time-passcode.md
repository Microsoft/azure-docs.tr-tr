---
title: B2B Konuk kullanıcıları için bir kerelik geçiş kodu kimlik doğrulaması-Azure AD
description: Bir Microsoft hesabı ihtiyaç duymadan B2B Konuk kullanıcılarının kimliğini doğrulamak için bir kerelik e-posta geçiş kodu kullanma.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4089559b341dd268928b1f150b6fc173869ead
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529920"
---
# <a name="email-one-time-passcode-authentication"></a>E-posta bir kerelik geçiş kodu kimlik doğrulaması

Bu makalede, B2B Konuk kullanıcıları için bir kerelik geçiş kodu kimlik doğrulamasının nasıl etkinleştirileceği açıklanır. E-posta bir kerelik geçiş kodu özelliği, Azure AD, Microsoft hesabı (MSA) veya Google Federasyonu gibi diğer yollarla kimlik doğrulamasından geçiyorlarsa B2B Konuk kullanıcılarının kimliğini doğrular. Tek seferlik geçiş kodu kimlik doğrulamasıyla Microsoft hesabı oluşturmanız gerekmez. Konuk Kullanıcı bir davetiyeyi bir davet edebilir veya paylaşılan bir kaynağa eriştiğinde, kendi e-posta adreslerine gönderilen geçici bir kod isteyebilir. Sonra oturum açmaya devam etmek için bu kodu girer.

![E-posta bir kerelik geçiş kodu genel bakış Diyagramı](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **2021 Ekim 'e başlayarak**, tüm mevcut kiracılar için e-posta bir kerelik geçiş kodu özelliği açık olur ve yeni kiracılar için varsayılan olarak etkinleştirilir. Bu özelliğin otomatik olarak kullanılmasına izin vermek istemiyorsanız, devre dışı bırakabilirsiniz. Bkz. [e-posta bir kerelik geçiş kodunu devre dışı bırakma](#disable-email-one-time-passcode) .
> - E-posta bir kerelik geçiş kodu ayarları, **dış işbirliği ayarlarından** Azure Portal **tüm kimlik sağlayıcılarına** taşınmıştır.

> [!NOTE]
> Bir kerelik geçiş kodu kullanıcılarının kiracı bağlamını içeren bir bağlantı kullanarak oturum açması gerekir (örneğin, `https://myapps.microsoft.com/?tenantid=<tenant id>` veya `https://portal.azure.com/<tenant id>` doğrulanmış bir etki alanı söz konusu olduğunda `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Uygulama ve kaynakların doğrudan bağlantıları, kiracı bağlamını dahil ettikleri sürece da çalışır. Konuk kullanıcılar şu anda kiracı bağlamı olmayan uç noktaları kullanarak oturum açamıyor. Örneğin, kullanarak `https://myapps.microsoft.com` , `https://portal.azure.com` bir hataya neden olur.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Bir kerelik geçiş kodu Konuk kullanıcıları için Kullanıcı deneyimi

E-posta bir kerelik geçiş kodu özelliği etkinleştirildiğinde, [belirli koşullara uyan](#when-does-a-guest-user-get-a-one-time-passcode) yeni davet edilen kullanıcılar bir kerelik geçiş kodu kimlik doğrulamasını kullanır. E-posta bir kerelik geçiş kodu etkinleştirilmeden önce daveti kullanan Konuk kullanıcılar aynı kimlik doğrulama yöntemini kullanmaya devam eder.

Bir kerelik geçiş kodu kimlik doğrulamasıyla, Konuk Kullanıcı doğrudan bağlantıya tıklayarak veya davet e-postasını kullanarak davetinizi kullanabilir. Her iki durumda da tarayıcıda bir ileti, Konuk kullanıcının e-posta adresine bir kod gönderileceğini belirtir. Konuk Kullanıcı, **kodu gönder**' i seçer:

   ![Kod Gönder düğmesini gösteren ekran görüntüsü](media/one-time-passcode/otp-send-code.png)

Kullanıcının e-posta adresine bir geçiş kodu gönderilir. Kullanıcı geçiş kodunu e-postayla alır ve tarayıcı penceresine girer:

   ![Kod gir sayfasını gösteren ekran görüntüsü](media/one-time-passcode/otp-enter-code.png)

Artık Konuk kullanıcının kimliği doğrulanır ve paylaşılan kaynağı görebilir veya oturum açmaya devam edebilir.

> [!NOTE]
> Bir kerelik geçiş kodları 30 dakika için geçerlidir. 30 dakika sonra, belirli bir kerelik geçiş kodu artık geçerli değildir ve kullanıcının yeni bir tane istemesi gerekir. Kullanıcı oturumlarının süresi 24 saat sonra dolacak. Bu süreden sonra, Konuk Kullanıcı kaynağa erişirken yeni bir geçiş kodu alır. Oturum süre sonu, özellikle bir ziyaretçi Kullanıcı şirketten ayrıldığında veya artık erişime ihtiyaç duymuyorsa ek güvenlik sağlar.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Konuk Kullanıcı bir kerelik geçiş kodu ne zaman alır?

Konuk Kullanıcı bir daveti bir kez kullanır veya bununla paylaşılan bir kaynağın bağlantısını kullandığında, şu durumlarda bir kerelik geçiş kodu alırlar:

- Azure AD hesabı yoktur
- Microsoft hesabı yoktur
- Davet eden kiracı, Google Federation @gmail.com 'i ve @googlemail.com kullanıcılarını ayarladı

Davet sırasında, davet ettiğiniz kullanıcının bir kerelik geçiş kodu kimlik doğrulamasını kullanacağı belirtilecektir. Ancak Konuk Kullanıcı oturum açtığında, başka bir kimlik doğrulama yöntemi kullanılmıyorsa, tek seferlik geçiş kodu kimlik doğrulaması geri dönüş yöntemi olur.

Kullanıcının ayrıntılarında **kaynak** özelliğini görüntüleyerek bir Konuk kullanıcının kimlik doğrulamasını tek seferlik geçiş kodlarını kullanarak doğruladığını görebilirsiniz. Azure Portal, **Azure Active Directory**  >  **Kullanıcılar**' a gidin ve ardından Ayrıntılar sayfasını açmak için kullanıcıyı seçin.

![Kaynak değeri OTP olan bir kerelik geçiş kodu kullanıcısını gösteren ekran görüntüsü](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Bir Kullanıcı bir kerelik geçiş kodunu ve daha sonra bir MSA, Azure AD hesabını ya da başka bir Federasyon hesabını aldığında, bir kerelik geçiş kodu kullanarak kimlik doğrulamasından devam eder. Kullanıcının kimlik doğrulama yöntemini güncelleştirmek istiyorsanız, kullanım [durumlarını sıfırlayabilirsiniz](reset-redemption-status.md).

### <a name="example&quot;></a>Örnek

Konuk Kullanıcı teri@gmail.com , Google Federasyonu ayarlanmamış olan Fabrikam 'a davet edilir. Teri Microsoft hesabı yok. Kimlik doğrulaması için bir kerelik geçiş kodu alırlar.

## <a name=&quot;disable-email-one-time-passcode&quot;></a>E-posta bir kerelik geçiş kodunu devre dışı bırak

2021 Ekim 'e başlayarak, tüm mevcut kiracılar için e-posta bir kerelik geçiş kodu özelliği açık olur ve yeni kiracılar için varsayılan olarak etkinleştirilir. Bu sırada, Microsoft, B2B işbirliği senaryolarında yönetilmeyen (&quot;viral&quot; veya &quot;tam zamanında") Azure AD hesapları ve kiracılar oluşturarak davetlerin kullanımını desteklememektedir. Konuk kullanıcılarınız için sorunsuz bir geri dönüş kimlik doğrulama yöntemi sağladığından e-posta bir kerelik geçiş kodu özelliğini etkinleştiriyoruz. Ancak, bu özelliği kullanmayı tercih ederseniz bu özelliği devre dışı bırakma seçeneğiniz vardır.

> [!NOTE]
>
> Kiracınızda e-posta geçiş kodu özelliği etkinleştirilmişse ve devre dışı bırakırsanız, bir kerelik geçiş kodu kullanan Konuk kullanıcılar oturum açamaz. Başka bir kimlik doğrulama yöntemi kullanarak yeniden oturum açabilmeniz için kullanım [durumlarını sıfırlayabilirsiniz](reset-redemption-status.md) .

### <a name="to-disable-the-email-one-time-passcode-feature"></a>E-posta bir kerelik geçiş kodu özelliğini devre dışı bırakmak için

1. [Azure Portal](https://portal.azure.com/) Azure AD Genel Yöneticisi olarak oturum açın.

2. Gezinti bölmesinde **Azure Active Directory**' yi seçin.

3. **Dış kimlikler**  >  **tüm kimlik sağlayıcıları**' nı seçin.

4. **E-posta bir kerelik geçiş kodu** seçin ve ardından **Konuklar için tek seferlik e-posta geçiş kodunu devre dışı bırak** seçeneğini

   > [!NOTE]
   > E-posta bir kerelik geçiş kodu ayarları, **dış işbirliği ayarlarından** Azure Portal **tüm kimlik sağlayıcılarına** taşınmıştır.
   > E-posta bir kerelik geçiş kodu seçeneği yerine bir geçiş görürseniz, bu, daha önce etkin, devre dışı veya özelliğin önizlemesini kabul ettiğiniz anlamına gelir. Özelliği devre dışı bırakmak için **Hayır** ' ı seçin.
   >
   >![E-posta tek seferlik geçiş kodu devre dışı](media/one-time-passcode/enable-email-otp-disabled.png)

5. **Kaydet**’i seçin.

## <a name="note-for-public-preview-customers"></a>Genel Önizleme müşterileri için göz önünde

Daha önce bir kerelik geçiş kodu genel önizlemesine sahipseniz, otomatik özellik etkinleştirme için 2021 Ekim tarihi sizin için uygun değildir, bu nedenle ilgili iş işlemleriniz etkilenmez. Ayrıca, Azure portal, **Konuklar için tek seferlik bir geçiş kodu** altında, **2021 Ekim 'e başlayan konuklarda e-posta geçiş kodunu otomatik olarak etkinleştirme** seçeneğini görmezsiniz. Bunun yerine, aşağıdaki **Evet** veya **Hayır** biçimini görürsünüz:

![E-posta bir kerelik geçiş kodu kabul edildi](media/one-time-passcode/enable-email-otp-opted-in.png)

Ancak, özelliği devre dışı bırakmayı tercih ediyorsanız ve BT 'nin 2021 Ekim 'de otomatik olarak etkinleştirilmesini istiyorsanız, Microsoft Graph API [e-posta kimlik doğrulama yöntemi yapılandırma kaynak türünü](/graph/api/resources/emailauthenticationmethodconfiguration)kullanarak varsayılan ayarlara geri döndürebilirsiniz. Varsayılan ayarlara döndükten sonra, **Konuklar için bir kerelik geçiş kodu** altında aşağıdaki seçenekler kullanılabilir:

![E-posta bir kerelik geçiş kodunu etkinleştir](media/one-time-passcode/email-otp-options.png)

- **2021 Ekim 'e başlayan Konuklar için tek seferlik parola geçiş kodunu otomatik olarak etkinleştirin**. Varsayılanını E-posta bir kerelik geçiş kodu özelliği kiracınız için zaten etkin değilse, 2021 Ekim tarihinde başlayacak şekilde otomatik olarak açılır. Özelliğin o anda etkinleştirilmesini istiyorsanız başka bir eylem gerekmez. Özelliği zaten etkinleştirdiyseniz veya devre dışı bıraktığınız takdirde bu seçenek kullanılamaz.

- **Şimdi geçerli olan konuklar için tek seferlik geçiş kodunu etkinleştirin**. Kiracınız için bir kerelik geçiş kodu özelliğini etkinleştirir.

- **Konuklar için tek seferlik e-posta geçiş kodunu devre dışı bırakın**. , Kiracınız için bir kerelik geçiş kodu özelliğini kapatır ve özelliğin 2021 Ekim 'de açılmasını önler.

## <a name="note-for-azure-us-government-customers"></a>Azure ABD kamu müşterileri için göz önünde bulunun

E-posta bir kerelik geçiş kodu özelliği, Azure ABD kamu bulutunda varsayılan olarak devre dışıdır. Bu özellik etkinleştirilmediği takdirde, iş ortaklarınız oturum açamıyor. Azure genel bulutundan farklı olarak, Azure ABD kamu bulutu, self servis Azure Active Directory hesaplarıyla davetleri benimseme desteğini desteklemez.

 ![E-posta bir kerelik geçiş kodu devre dışı](media/one-time-passcode/enable-email-otp-disabled.png)

Azure ABD kamu bulutunda e-posta bir kerelik geçiş kodu özelliğini etkinleştirmek için:

1. [Azure Portal](https://portal.azure.com) Azure AD Genel Yöneticisi olarak oturum açın.
2. Gezinti bölmesinde **Azure Active Directory**' yi seçin.
3. **Kuruluş ilişkileri**   >  **tüm kimlik sağlayıcıları**' nı seçin.

   > [!NOTE]
   > - **Kuruluş ilişkilerini** görmüyorsanız, üstteki arama çubuğunda "dış kimlikler" araması yapın.

4. **E-posta bir kerelik geçiş kodu** seçin ve ardından **Evet**' i seçin.
5. **Kaydet**’i seçin.

Geçerli sınırlamalar hakkında daha fazla bilgi için bkz. [Azure ABD kamu bulutları](current-limitations.md#azure-us-government-clouds).
