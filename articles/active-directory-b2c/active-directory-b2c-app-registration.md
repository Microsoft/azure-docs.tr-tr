---
title: "Azure Active Directory B2C: Uygulama kaydı | Microsoft Belgeleri"
description: "Uygulamanızı Azure Active Directory B2C&quot;ye kaydetme"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e04fbd97dd4d5ecaf12edf47d80572b32d29ed00
ms.lasthandoff: 03/23/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Uygulamanızı kaydetme

> [!IMPORTANT]
> Azure portalında Azure AD B2C dikey penceresinden oluşturulan uygulamaların aynı konumdan yönetilmesi gerekir. B2C uygulamalarını PowerShell veya başka bir portal kullanarak düzenlerseniz desteklenmez duruma gelirler ve Azure AD B2C ile çalışma olasılığı düşüktür.
> 
> 

## <a name="prerequisite"></a>Önkoşul
Tüketicinin kaydolmasını ve oturum açmasını kabul eden bir uygulama oluşturmak için öncelikle uygulamayı Azure Active Directory B2C kiracısına kaydetmeniz gerekir. [Azure AD B2C kiracısı oluşturma](active-directory-b2c-get-started.md) makalesinde ana hatlarıyla belirtilen adımları izleyerek kendi kiracınızı edinin. Söz konusu makaledeki tüm adımları izledikten sonra B2C özellikleri dikey penceresi Başlangıç panonuza sabitlenir.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>B2C özellikleri dikey penceresine gitme
B2C özellikleri dikey penceresini Başlangıç panonuza sabitlediyseniz dikey pencereyi, B2C kiracısının Genel Yöneticisi olarak [Azure portalında](https://portal.azure.com/) oturum açar açmaz görürsünüz.

Ayrıca dikey pencereye, [Azure portalındaki](https://portal.azure.com/) **Diğer hizmetler**’e tıklayarak ve ardından sol gezinti bölmesinde **Azure AD B2C** araması yaparak erişebilirsiniz.

> [!IMPORTANT]
> B2C özellikleri dikey penceresine erişebilmek için B2C kiracısının Genel Yöneticisi olmanız gerekir. Başka bir kiracının Genel Yöneticisi veya başka bir kiracıdan gelen bir kullanıcı, dikey pencereye erişemez.  Azure portalının sağ üst köşesindeki kiracı değiştiriciyi kullanarak B2C kiracınıza geçiş yapabilirsiniz.
> 
> 

## <a name="register-a-web-application"></a>Web uygulaması kaydetme
1. Azure portalındaki B2C özellikleri dikey penceresinde **Applications (Uygulamalar)** seçeneğine tıklayın.
2. Dikey pencerenin en üstündeki **+Add (+Ekle)** seçeneğine tıklayın.
3. Uygulamanızı tüketicilere tanımlayacak bir **Ad** girin. Örneğin, "Contoso B2C uygulaması"na girebilirsiniz.
4. **Web uygulamasını / web API’sini dahil et** anahtarını **Evet**’e getirin. **Yanıt URL'leri**, Azure AD B2C'nin, uygulamanız tarafından istenen belirteçleri getirdiği uç noktalardır. Örneğin, `https://localhost:44316/` girin.
5. Uygulamanızı kaydetmek için **Create (Oluştur)** seçeneğine tıklayın.
6. Oluşturduğunuz uygulamaya tıklayın ve daha sonra kodunuzda kullanacağınız genel benzersiz **Uygulama İstemci Kimliğini** kopyalayın. 
7. Web uygulamanız aynı zamanda Azure AD B2C ile güvenliği sağlanan bir web API’sini çağıracaksa **Anahtarlar** dikey penceresine gidip **Anahtarı Oluştur** düğmesine tıklayarak bir **Uygulama Gizli Anahtarı** da oluşturmak istersiniz.

> [!NOTE]
> **Uygulama Gizli Anahtarı** önemli bir güvenlik kimlik bilgisidir ve güvenliği uygun şekilde sağlanmalıdır.
> 
   

## <a name="register-a-web-api"></a>Web API’si kaydetme
1. Azure portalındaki B2C özellikleri dikey penceresinde **Applications (Uygulamalar)** seçeneğine tıklayın.
2. Dikey pencerenin en üstündeki **+Add (+Ekle)** seçeneğine tıklayın.
3. Uygulamanızı tüketicilere tanımlayacak bir **Ad** girin. Örneğin, "Contoso B2C api" girebilirsiniz.
4. **Web uygulamasını / web API’sini dahil et** anahtarını **Evet**’e getirin. **Yanıt URL'leri**, Azure AD B2C'nin, uygulamanız tarafından istenen belirteçleri getirdiği uç noktalardır. Örneğin, `https://localhost:44316/` girin.
5. Bir **Uygulama Kimliği URI'si** girin. Bu değer, web API’niz için kullanılan tanımlayıcıdır. Örneğin, 'notlar' ifadesini girin. Altında tam tanımlayıcı URI’si oluşturulur. 
6. Uygulamanızı kaydetmek için **Create (Oluştur)** seçeneğine tıklayın.
7. Oluşturduğunuz uygulamaya tıklayın ve daha sonra kodunuzda kullanacağınız genel benzersiz **Uygulama İstemci Kimliğini** kopyalayın.
8. **Yayımlanan kapsamlar**’a tıklayın. Burada diğer uygulamalara verilebilecek izinleri (kapsamları) tanımlarsınız.
9. Gerektiğinde daha fazla kapsam ekleyin. Varsayılan olarak, "user_impersonation" kapsamı tanımlanır. Bunun yapılması, diğer uygulamaların oturum açmış kullanıcı adına bu api’de oturum açmasına olanak tanır. İsterseniz bu seçeneği kaldırabilirsiniz. 
10. **Kaydet** düğmesine tıklayın.

## <a name="register-a-mobilenative-application"></a>Mobil/yerel bir uygulamayı kaydetme
1. Azure portalındaki B2C özellikleri dikey penceresinde **Applications (Uygulamalar)** seçeneğine tıklayın.
2. Dikey pencerenin en üstündeki **+Add (+Ekle)** seçeneğine tıklayın.
3. Uygulamanızı tüketicilere tanımlayacak bir **Ad** girin. Örneğin, "Contoso B2C uygulaması"na girebilirsiniz.
4. **Yerel istemciyi dahil et** anahtarını **Evet**’e getirin.
5. Özel şema ile bir **Yeniden yönlendirme URI’si** girin. Örneğin, com.onmicrosoft.contoso.appname://redirect/path. [İyi bir yeniden yönlendirme URI’si](#choosing-a-redirect-uri) seçtiğinizden emin olun.
6. Uygulamanızı kaydetmek için **Kaydet** seçeneğine tıklayın.
7. Oluşturduğunuz uygulamaya tıklayın ve daha sonra kodunuzda kullanacağınız genel benzersiz **Uygulama İstemci Kimliğini** kopyalayın.
8. Yerel uygulamanız aynı zamanda Azure AD B2C ile güvenliği sağlanan bir web API’sini çağıracaksa **Anahtarlar** dikey penceresine gidip **Anahtarı Oluştur** düğmesine tıklayarak bir **Uygulama Gizli Anahtarı** da oluşturmak istersiniz.

> [!NOTE]
> **Uygulama Gizli Anahtarı** önemli bir güvenlik kimlik bilgisidir ve güvenliği uygun şekilde sağlanmalıdır.
> 

### <a name="choosing-a-redirect-uri"></a>Yönlendirme URI’si seçme
Mobil/yerel uygulamalar için bir yeniden yönlendirme URI’si seçerken dikkat edilmesi gereken iki önemli nokta şunlardır: 
* **Benzersiz**: Yeniden yönlendirme URI’si şeması her uygulama için benzersiz olmalıdır. Örneğimizde (com.onmicrosoft.contoso.appname://redirect/path), şema olarak com.onmicrosoft.contoso.appname kullanılır. Bu örneği izlemeniz önerilir. İki uygulama aynı şemayı paylaşıyorsa, kullanıcı bir “bir uygulama seçin” iletişim kutusu görür. Kullanıcı yanlış seçim yaparsa, oturum açma başarısız olur. 
* **Tam**: Yeniden yönlendirme URI’sinin bir şeması ve yolu olmalıdır. Yol, etki alanından sonra en az bir eğik çizgi içermelidir (örneğin, //contoso/ çalışırken //contoso başarısız olur). 

## <a name="build-a-quick-start-application"></a>Hızlı Başlangıç Uygulaması oluşturma
Azure AD B2C'ye kayıtlı bir uygulamaya sahip olduğunuza göre başlamak ve çalıştırmak için hızlı başlangıç öğreticilerimizden birini tamamlayabilirsiniz. İşte birkaç öneri:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


