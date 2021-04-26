---
title: Azure AD uygulamalarımın bir uygulamada oturum açma sorunlarını giderme
description: Azure AD uygulamalarımın bir uygulamada oturum açma sorunlarını giderme
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a54c7d6cc4ccf1d9f42702be030598ad1edfab24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225158"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Azure AD uygulamalarımın bir uygulamada oturum açma sorunlarını giderme

Uygulamalarım, Azure Active Directory (Azure AD) ' de iş veya okul hesabı olan bir kullanıcının Azure AD yöneticisinin erişim izni verdiği bulut tabanlı uygulamaları görüntülemesini ve başlatmasını sağlayan Web tabanlı bir portaldır. Uygulamalarıma adresinde bir Web tarayıcısı kullanılarak erişilir [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Azure AD 'yi bir uygulama için kimlik sağlayıcısı olarak kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure AD 'de uygulama yönetimi nedir](what-is-application-management.md). Hızlı bir şekilde yararlanmak için [uygulama yönetiminde hızlı başlangıç serisine](view-applications-portal.md)göz atın.

Bu uygulamalar, Azure AD portalındaki Kullanıcı adına yapılandırılır. Uygulama, uygulama için düzgün şekilde yapılandırılmalı ve Kullanıcı veya Kullanıcı üyesi olan bir gruba atanmış olmalıdır. 

Bir kullanıcının görebilecekleri uygulamaların türü aşağıdaki kategorilere ayrılır:
-   Microsoft 365 uygulamalar
-   Federasyon tabanlı SSO ile yapılandırılan Microsoft ve üçüncü taraf uygulamalar
-   Parola tabanlı SSO uygulamaları
-   Var olan SSO çözümlerini içeren uygulamalar

Uygulamanın görünmemiş veya görünmediğinden emin olmak için bazı şeyler aşağıda verilmiştir.
- Uygulamanın Azure AD 'ye eklendiğinden emin olun ve kullanıcının atandığından emin olun. Daha fazla bilgi edinmek için bkz. [uygulama yönetiminde hızlı başlangıç serisi](add-application-portal.md).
- Uygulama son zamanlarda eklendiyse, kullanıcının oturumu kapatıp tekrar oturum açmasını sağlayabilirsiniz. 
- Uygulama Office gibi bir lisans gerektiriyorsa, kullanıcıya uygun lisans atandığından emin olun.
- Lisans değişiklikleri için gereken süre, grubun boyutuna ve karmaşıklığına bağlı olarak farklılık gösterebilir.

## <a name="general-issues-to-check-first"></a>Önce denetlenecek genel sorunlar

-   Web tarayıcısının gereksinimleri karşıladığından emin olun, bkz. [uygulamalarımın desteklediği tarayıcılar](../user-help/my-apps-portal-end-user-access.md).
-   Kullanıcının tarayıcısının uygulamanın URL 'sini **Güvenilen sitelerine** eklediğinizden emin olun.
-   Uygulamanın doğru şekilde **yapılandırıldığını** kontrol ettiğinizden emin olun.
-   Oturum açma işlemlerinin Kullanıcı hesabının **etkinleştirildiğinden** emin olun.
-   Kullanıcı hesabının **kilitlenmediğinden** emin olun.
-   Kullanıcının **parolasının dolmadığından veya unutulmuş** olduğundan emin olun.
-   **Multi-Factor Authentication** Kullanıcı erişimini engellemediğinden emin olun.
-   **Koşullu erişim ilkesi** veya **kimlik koruma** ilkesinin Kullanıcı erişimini engellemediğinden emin olun.
-   Multi-Factor Authentication veya koşullu erişim ilkelerinin zorlanmasını sağlamak için kullanıcının **kimlik doğrulaması iletişim bilgilerinin** güncel olduğundan emin olun.
-   Ayrıca, tarayıcınızın tanımlama bilgilerini temizlemeyi ve yeniden oturum açmayı denediğinizden emin olun.

## <a name="problems-with-the-users-account"></a>Kullanıcının hesabıyla ilgili sorunlar
Kullanıcı hesabındaki bir sorun nedeniyle uygulamalarıma erişim engelleniyor olabilir. Aşağıda, kullanıcılarla ilgili sorunları giderebilmeniz ve sorunları ve bunların hesap ayarlarını çözmeniz için kullanabileceğiniz bazı yollar verilmiştir:
-   [Azure Active Directory bir kullanıcı hesabının bulunup bulunmadığını denetle](#check-if-a-user-account-exists-in-azure-active-directory)
-   [Kullanıcının hesap durumunu denetleme](#check-a-users-account-status)
-   [Kullanıcının parolasını sıfırlama](#reset-a-users-password)
-   [Kendi kendine parola sıfırlamayı etkinleştirme](#enable-self-service-password-reset)
-   [Kullanıcının Multi-Factor Authentication durumunu denetleme](#check-a-users-multi-factor-authentication-status)
-   [Kullanıcının kimlik doğrulaması iletişim bilgilerini denetleme](#check-a-users-authentication-contact-info)
-   [Kullanıcının grup üyeliklerini denetleme](#check-a-users-group-memberships)
-   [Kullanıcının 999 'den fazla uygulama rolü ataması olup olmadığını denetleyin](#check-if-a-user-has-more-than-999-app-role-assignments)
-   [Kullanıcının atanan lisanslarını denetleme](#check-a-users-assigned-licenses)
-   [Kullanıcıya lisans atama](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Azure Active Directory bir kullanıcı hesabının bulunup bulunmadığını denetle
Bir kullanıcının hesabının mevcut olup olmadığını denetlemek için şu adımları izleyin:
1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4.  Gezinti menüsünde **Kullanıcılar ve gruplar ' ı** seçin.
5.  **Tüm kullanıcılar**' ı seçin.
6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satırı seçin** .
7.  Beklenen şekilde göründüğünden ve hiç veri bulunmadığından emin olmak için Kullanıcı nesnesinin özelliklerini denetleyin.

### <a name="check-a-users-account-status"></a>Kullanıcının hesap durumunu denetleme
Kullanıcının hesap durumunu denetlemek için şu adımları izleyin:
1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4.  Gezinti menüsünde **Kullanıcılar ve gruplar ' ı** seçin.
5.  **Tüm kullanıcılar**' ı seçin.
6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satırı seçin** .
7.  **Profil**' i seçin.
8.  **Ayarlar** ' ın altında, **blok oturum açma** seçeneğinin **Hayır** olarak ayarlandığından emin olun.

### <a name="reset-a-users-password"></a>Kullanıcının parolasını sıfırlama
Bir kullanıcının parolasını sıfırlamak için şu adımları izleyin:
1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4.  Gezinti menüsünde **Kullanıcılar ve gruplar ' ı** seçin.
5.  **Tüm kullanıcılar**' ı seçin.
6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satırı seçin** .
7.  Kullanıcı bölmesinin en üstündeki **Parolayı Sıfırla** düğmesini seçin.
8.  Görüntülenen **Parolayı Sıfırla** bölmesinde **Parolayı Sıfırla** düğmesini seçin.
9.  **Geçici parolayı** kopyalayın veya Kullanıcı için **Yeni bir parola girin** .
10. Bu yeni parolayı kullanıcıyla iletişim kurmak için, bir sonraki oturum açma sırasında Azure Active Directory için bu parolanın değiştirilmesi gerekir.

### <a name="enable-self-service-password-reset"></a>Kendi kendine parola sıfırlamayı etkinleştirme
Self servis parola sıfırlamayı etkinleştirmek için şu dağıtım adımlarını izleyin:
-   [Kullanıcıların Azure Active Directory parolalarını sıfırlamalarını sağlama](../authentication/tutorial-enable-sspr.md)
-   [Kullanıcıların şirket içi parolalarını Active Directory sıfırlamalarını veya değiştirmesini sağlama](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kullanıcının Multi-Factor Authentication durumunu denetleme
Kullanıcının Multi-Factor Authentication durumunu denetlemek için şu adımları izleyin:
1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4. Gezinti menüsünde **Kullanıcılar ve gruplar ' ı** seçin.
5. **Tüm kullanıcılar**' ı seçin.
6. Bölmenin üst kısmındaki **Multi-Factor Authentication** düğmesini seçin.
7. **Multi-Factor Authentication yönetim portalı** yüklendikten sonra, **Kullanıcılar** sekmesinde olduğunuzdan emin olun.
8. Kullanıcıları arayarak, filtreleyerek veya sıralayarak Kullanıcı listesinde bulun.
9. Kullanıcı listesinden kullanıcıyı seçin, istediğiniz şekilde Multi-Factor Authentication 'ı **etkinleştirin**, **devre dışı bırakın** veya **zorlayın** .
   >[!NOTE]
   >Bir Kullanıcı **zorlanan** durumdaysa, hesaplarına geri dönmek için onları geçici olarak **devre dışı** olarak ayarlayabilirsiniz. Yeniden **etkinleştikten** sonra, bir sonraki oturum açma sırasında iletişim bilgilerinin yeniden kaydolmasını gerektirmek için durumlarını tekrar etkin olarak değiştirebilirsiniz. Alternatif olarak, bu verileri doğrulamak veya ayarlamak için [kullanıcının kimlik doğrulaması iletişim bilgilerini denetleme](#check-a-users-authentication-contact-info) bölümündeki adımları izleyebilirsiniz.

### <a name="check-a-users-authentication-contact-info"></a>Kullanıcının kimlik doğrulaması iletişim bilgilerini denetleme
Kullanıcının Multi-Factor Authentication, koşullu erişim, kimlik koruması ve parola sıfırlama için kullandığı kimlik doğrulama iletişim bilgilerini denetlemek için şu adımları izleyin:
1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4.  Gezinti menüsünde **Kullanıcılar ve gruplar ' ı** seçin.
5.  **Tüm kullanıcılar**' ı seçin.
6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satırı seçin** .
7.  **Profil**' i seçin.
8.  Aşağı kaydırarak **kimlik doğrulama iletişim bilgilerine** gidin.
9.  Kullanıcı için kaydedilen verileri **gözden geçirin** ve gerektiğinde güncelleştirin.

### <a name="check-a-users-group-memberships"></a>Kullanıcının grup üyeliklerini denetleme
Bir kullanıcının grup üyeliklerini denetlemek için şu adımları izleyin:
1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4.  Gezinti menüsünde **Kullanıcılar ve gruplar ' ı** seçin.
5.  **Tüm kullanıcılar**' ı seçin.
6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satırı seçin** .
7.  Kullanıcının üyesi olduğu grupları görmek için **grupları** seçin.

### <a name="check-if-a-user-has-more-than-999-app-role-assignments"></a>Kullanıcının 999 'den fazla uygulama rolü ataması olup olmadığını denetleyin
Bir kullanıcının 999 'den fazla uygulama rolü ataması varsa uygulamalarım üzerinde tüm uygulamalarını görmeyebilir.

Bunun nedeni uygulamalarımın, kullanıcıların atandığı uygulamaları belirlemede en fazla 999 uygulama rolü atamasını okumasıdır. Bir Kullanıcı 999 ' den fazla uygulamaya atanmışsa, bu uygulamalardan hangisinin uygulama portalında gösterileceğini denetlemek mümkün değildir.

Bir kullanıcının 999 taneden fazla uygulama rolü ataması olup olmadığını denetlemek için şu adımları izleyin:
1. [**Microsoft. Graph**](https://github.com/microsoftgraph/msgraph-sdk-powershell) PowerShell modülünü yükler.
2. `Connect-MgGraph -Scopes "User.ReadBasic.All Application.Read.All"` öğesini çalıştırın.
3. `(Get-MgUserAppRoleAssignment -UserId "<user-id>" -PageSize 999).Count`Kullanıcının şu anda verdiği uygulama rolü atamalarının sayısını öğrenmek için öğesini çalıştırın.
4. Sonuç 999 ise Kullanıcı büyük olasılıkla 999 ' den fazla uygulama rolü atamadır.

### <a name="check-a-users-assigned-licenses"></a>Kullanıcının atanan lisanslarını denetleme
Bir kullanıcının atanmış lisanslarını denetlemek için şu adımları izleyin:
1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4.  Gezinti menüsünde **Kullanıcılar ve gruplar ' ı** seçin.
5.  **Tüm kullanıcılar**' ı seçin.
6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satırı seçin** .
7.  Kullanıcının şu anda hangi lisansları atandığını görmek için **lisanslar** ' ı seçin.

### <a name="assign-a-user-a-license"></a>Kullanıcıya lisans atama 
Bir kullanıcıya lisans atamak için aşağıdaki adımları izleyin:
1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.
2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4.  Gezinti menüsünde **Kullanıcılar ve gruplar ' ı** seçin.
5.  **Tüm kullanıcılar**' ı seçin.
6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satırı seçin** .
7.  Kullanıcının şu anda hangi lisansları atandığını görmek için **lisanslar** ' ı seçin.
8.  **Ata** düğmesini seçin.
9.  Kullanılabilir ürünler listesinden **bir veya daha fazla ürün** seçin.
10. **Isteğe bağlı** ürün atama **seçenekleri** öğesini seçin. **Tamam**'ı seçin.
11. Bu lisansları bu kullanıcıya atamak için **ata** düğmesini seçin.

## <a name="troubleshooting-deep-links"></a>Derin bağlantılar sorunlarını giderme
Derin bağlantılar veya Kullanıcı erişimi URL 'Leri, kullanıcılarınızın parola SSO uygulamalarına doğrudan tarayıcıların URL çubuklarından erişmek için kullanabileceği bağlantılardır. Bu bağlantıya giderek kullanıcılar önce uygulamalarıma gitmek zorunda kalmadan otomatik olarak uygulamada oturum açanlar. Bağlantı, kullanıcıların Microsoft 365 uygulama başlatıcıdan bu uygulamalara erişmek için kullandığı aynı bir uygulamadır.

### <a name="checking-the-deep-link"></a>Derin bağlantı denetleniyor

Doğru derin bağlantısına sahip olup olmadığınızı denetlemek için şu adımları izleyin:
1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.
2. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4. Azure Active Directory sol gezinti menüsünde **Kurumsal Uygulamalar**’ı seçin.
5. Tüm uygulamalarınızın listesini görüntülemek için **Tüm Uygulamalar**'ı seçin.
   * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.
6. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.
7. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.
8. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
9. Azure Active Directory sol gezinti menüsünde **Kurumsal Uygulamalar**’ı seçin.
10. Tüm uygulamalarınızın listesini görüntülemek için **Tüm Uygulamalar**'ı seçin.
    * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.
11. Ayrıntılı bağlantısını kontrol etmek istediğiniz uygulamayı seçin.
12. **Kullanıcı erişim URL 'si** etiketini bulun. Derin bağlantınız Bu URL ile eşleşmelidir.

## <a name="contact-support"></a>Desteğe başvurun
Varsa, aşağıdaki bilgilerle bir destek bileti açın:
-   Bağıntı hata KIMLIĞI
-   UPN (Kullanıcı e-posta adresi)
-   Değerine
-   Tarayıcı türü
-   Hata sırasında saat dilimi ve zaman/zaman dilimi
-   Fiddler izlemeleri

## <a name="next-steps"></a>Sonraki adımlar
- [Uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md)
