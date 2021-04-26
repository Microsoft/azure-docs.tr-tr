---
title: Microsoft uygulamasında oturum açma sorunları | Microsoft Docs
description: Azure AD 'yi kullanarak ilk taraf Microsoft uygulamalarında oturum açarken karşılaşılan yaygın sorunları giderin (Microsoft 365 gibi).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56e356a9bacc6479d3a3a33be905457c26e732e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378185"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Microsoft uygulamasında oturum açma sorunları

Microsoft uygulamaları (Exchange, SharePoint, Yammer vb. gibi), üçüncü taraf SaaS uygulamalarından veya çoklu oturum açma için Azure AD ile tümleştirmiş olduğunuz diğer uygulamalardan farklı bir şekilde atanır ve yönetilir.

Bir kullanıcının Microsoft tarafından yayımlanan bir uygulamaya erişebileceği üç ana yol vardır.

-   Microsoft 365 veya diğer ücretli paketlerde bulunan uygulamalar için, kullanıcılara doğrudan kullanıcı hesaplarına veya grup tabanlı lisans atama becerimizi kullanan bir grup aracılığıyla **Lisans ataması** üzerinden erişim verilir.

-   Microsoft 'un veya üçüncü bir tarafın herkes tarafından kullanılmak üzere serbestçe yayımlayabilen uygulamalar için, kullanıcılara **Kullanıcı izni** aracılığıyla erişim izni verilebilir. Bu, Azure AD Iş veya okul hesabıyla uygulamada oturum açtıklarında ve hesabın hesabında sınırlı miktarda veri kümesine erişmesine izin veren anlamına gelir.

-   Microsoft 'un veya bir üçüncü taraf tarafından herkes tarafından kullanılabilecek uygulamalar için, kullanıcılara **yönetici izni** aracılığıyla da erişim izni verilebilir. Bu, bir yöneticinin uygulamayı kuruluştaki herkes tarafından kullanılabileceğini, böylece bir genel yönetici hesabıyla uygulamada oturum açmasını ve kuruluştaki herkese erişim vermesini belirlediği anlamına gelir.

Sorunu gidermek için, [uygulama erişimi ile Ilgili genel sorun alanlarıyla](#general-problem-areas-with-application-access-to-consider) başlayın ve ardından izlenecek yolu okuyun: ayrıntıları almak Için Microsoft uygulama erişiminin sorunlarını giderme adımları.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Göz önünde bulundurmanız gereken genel sorun ve uygulama erişimi

Aşağıda, nereden başlayacağınız konusunda bir fikriniz varsa detaya gidebileceğiniz genel sorun alanlarının bir listesi verilmiştir, ancak hızlıca çalışmaya başlamak için izlenecek yolu okumanızı öneririz: Izlenecek yol: Microsoft uygulama erişimi sorunlarını giderme adımları.

-   [Kullanıcının hesabıyla ilgili sorunlar](#problems-with-the-users-account)

-   [Gruplarla ilgili sorunlar](#problems-with-groups)

-   [Koşullu erişim ilkeleriyle ilgili sorunlar](#problems-with-conditional-access-policies)

-   [Uygulama onayı ile ilgili sorunlar](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Microsoft uygulama erişimi sorunlarını giderme adımları

Kullanıcıları bir Microsoft uygulamasında oturum açarken bazı yaygın sorunlar aşağıda verilmiştir.

- Önce denetlenecek genel sorunlar

  * Kullanıcının yerel uygulama URL 'SI değil **doğru URL** 'de oturum açmasını sağlayın.

  * Kullanıcı hesabının **kilitlenmediğinden** emin olun.

  * **Kullanıcı hesabının** Azure Active Directory bulunduğundan emin olun. [Azure Active Directory bir kullanıcı hesabının bulunup bulunmadığını denetle](#problems-with-the-users-account)

  * Oturum açma işlemlerinin Kullanıcı hesabının **etkinleştirildiğinden** emin olun. [Kullanıcının hesap durumunu denetleme](#problems-with-the-users-account)

  * Kullanıcının **parolasının dolmadığından veya unutulmuş** olduğundan emin olun. [Kullanıcının parolasını sıfırlama](#reset-a-users-password) veya [self servis parola sıfırlamayı etkinleştirme](../authentication/tutorial-enable-sspr.md)

  * **Multi-Factor Authentication** Kullanıcı erişimini engellemediğinden emin olun. [Kullanıcının Multi-Factor Authentication durumunu denetleme](#check-a-users-multi-factor-authentication-status) veya [kullanıcının kimlik doğrulaması iletişim bilgilerini denetleme](#check-a-users-authentication-contact-info)

  * **Koşullu erişim ilkesi** veya **kimlik koruma** ilkesinin Kullanıcı erişimini engellemediğinden emin olun. Belirli bir [koşullu erişim Ilkesini denetleme](#problems-with-conditional-access-policies) veya belirli bir [uygulamanın koşullu erişim ilkesini denetleme](#check-a-specific-applications-conditional-access-policy) veya [belirli bir koşullu erişim ilkesini devre dışı bırakma](#disable-a-specific-conditional-access-policy)

  * Multi-Factor Authentication veya koşullu erişim ilkelerinin zorlanmasını sağlamak için kullanıcının **kimlik doğrulaması iletişim bilgilerinin** güncel olduğundan emin olun. [Kullanıcının Multi-Factor Authentication durumunu denetleme](#check-a-users-multi-factor-authentication-status) veya [kullanıcının kimlik doğrulaması iletişim bilgilerini denetleme](#check-a-users-authentication-contact-info)

- **Lisans gerektiren** **Microsoft** uygulamaları için (Office365 gibi), yukarıdaki genel sorunları ortaya aldıktan sonra denetlenecek bazı özel sorunlar şunlardır:

  * Kullanıcıya veya **atanmış bir lisansa** sahip olduğundan emin olun. [Kullanıcının atanan lisanslarını denetleme](#check-a-users-assigned-licenses) veya [grubun atanmış lisanslarını denetleme](#check-a-groups-assigned-licenses)

  * Lisans **statik** bir gruba **atanmışsa** , kullanıcının bu grubun **üyesi** olduğundan emin olun. [Kullanıcının grup üyeliklerini denetleme](#check-a-users-group-memberships)

  * Lisans **dinamik** bir gruba **atanmışsa** , **dinamik grup kuralının doğru ayarlandığından** emin olun. [Dinamik bir grubun üyelik ölçütlerini denetleme](#check-a-dynamic-groups-membership-criteria)

  * Lisans **dinamik** bir gruba **atanmışsa** , dinamik grubun üyeliğini **işlemeyi bitirdiğinden** ve **kullanıcının üye olduğundan** emin olun (Bu işlem biraz zaman alabilir). [Kullanıcının grup üyeliklerini denetleme](#check-a-users-group-memberships)

  *  Lisansın atandığından emin olduktan sonra, lisansın **zaman aşımına uğradığı** emin olun.

  *  Lisansın eriştiği **uygulama için** olduğundan emin olun.

- **Lisansı gerektirmeyen** **Microsoft** uygulamaları için aşağıdaki noktalara dikkat edin:

  * Uygulama **Kullanıcı düzeyinde izinler** istiyorsa (örneğin, "Bu kullanıcının posta kutusuna erişin"), kullanıcının uygulamada oturum açmış olduğundan ve uygulamanın verilerine erişmesine izin vermek için **Kullanıcı düzeyinde bir izin işlemi** gerçekleştirdiğinden emin olun.

  * Uygulama, **yönetici düzeyinde izinler** istiyorsa (örneğin, "tüm kullanıcıların posta kutularına erişin"), genel bir yöneticinin kuruluştaki **tüm kullanıcılar adına yönetici düzeyinde bir izin işlemi** gerçekleştirdiğinizden emin olun.

## <a name="problems-with-the-users-account"></a>Kullanıcının hesabıyla ilgili sorunlar

Uygulamaya atanan bir kullanıcıyla ilgili bir sorun nedeniyle uygulama erişimi engelleniyor olabilir. Aşağıda, kullanıcılarla ilgili sorunları giderebilmeniz ve sorunları ve bunların hesap ayarlarını çözmeniz için kullanabileceğiniz bazı yollar verilmiştir:

-   [Azure Active Directory bir kullanıcı hesabının bulunup bulunmadığını denetle](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kullanıcının hesap durumunu denetleme](#check-a-users-account-status)

-   [Kullanıcının parolasını sıfırlama](#reset-a-users-password)

-   [Kendi kendine parola sıfırlamayı etkinleştirme](#enable-self-service-password-reset)

-   [Kullanıcının Multi-Factor Authentication durumunu denetleme](#check-a-users-multi-factor-authentication-status)

-   [Kullanıcının kimlik doğrulaması iletişim bilgilerini denetleme](#check-a-users-authentication-contact-info)

-   [Kullanıcının grup üyeliklerini denetleme](#check-a-users-group-memberships)

-   [Kullanıcının atanan lisanslarını denetleme](#check-a-users-assigned-licenses)

-   [Kullanıcıya lisans atama](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Azure Active Directory bir kullanıcı hesabının bulunup bulunmadığını denetle

Bir kullanıcının hesabının mevcut olup olmadığını denetlemek için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm kullanıcılar**' a tıklayın.

6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satıra tıklayın** .

7.  Beklenen şekilde göründüğünden ve hiç veri bulunmadığından emin olmak için Kullanıcı nesnesinin özelliklerini denetleyin.

### <a name="check-a-users-account-status"></a>Kullanıcının hesap durumunu denetleme

Kullanıcının hesap durumunu denetlemek için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm kullanıcılar**' a tıklayın.

6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satıra tıklayın** .

7.  **profil**' e tıklayın.

8.  **Ayarlar** ' ın altında, **blok oturum açma** seçeneğinin **Hayır** olarak ayarlandığından emin olun.

### <a name="reset-a-users-password"></a>Kullanıcının parolasını sıfırlama

Bir kullanıcının parolasını sıfırlamak için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm kullanıcılar**' a tıklayın.

6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satıra tıklayın** .

7.  Kullanıcı bölmesinin en üstündeki **Parolayı Sıfırla** düğmesine tıklayın.

8.  görüntülenen **parola sıfırlama** bölmesinde **Parolayı Sıfırla** düğmesine tıklayın.

9.  **Geçici parolayı** kopyalayın veya Kullanıcı için **Yeni bir parola girin** .

10. Bu yeni parolayı kullanıcıyla iletişim kurmak için, Azure Active Directory bir sonraki oturum açma sırasında bu parolayı değiştirmesi gerekir.

### <a name="enable-self-service-password-reset"></a>Kendi kendine parola sıfırlamayı etkinleştirme

Self servis parola sıfırlamayı etkinleştirmek için aşağıdaki dağıtım adımlarını izleyin:

-   [Kullanıcıların Azure Active Directory parolalarını sıfırlamalarını sağlama](../authentication/tutorial-enable-sspr.md)

-   [Kullanıcıların şirket içi parolalarını Active Directory sıfırlamalarını veya değiştirmesini sağlama](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kullanıcının Multi-Factor Authentication durumunu denetleme

Kullanıcının Multi-Factor Authentication durumunu denetlemek için şu adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4. Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5. **tüm kullanıcılar**' a tıklayın.

6. bölmenin üst kısmındaki **Multi-Factor Authentication** düğmesine tıklayın.

7. **Multi-Factor Authentication yönetim portalı** yüklendikten sonra, **Kullanıcılar** sekmesinde olduğunuzdan emin olun.

8. Kullanıcıları arayarak, filtreleyerek veya sıralayarak Kullanıcı listesinde bulun.

9. Kullanıcı listesinden kullanıcıyı seçin, istediğiniz şekilde Multi-Factor Authentication 'ı **etkinleştirin**, **devre dışı bırakın** veya **zorlayın** .

   * **Note**: bir Kullanıcı **zorlanan** durumdaysa, hesaplarına geri dönmek Için geçici olarak **devre dışı** olarak ayarlayabilirsiniz. Yeniden **etkinleştikten** sonra, bir sonraki oturum açma sırasında iletişim bilgilerinin yeniden kaydolmasını gerektirmek için durumlarını tekrar etkin olarak değiştirebilirsiniz. Alternatif olarak, bu verileri doğrulamak veya ayarlamak için [kullanıcının kimlik doğrulaması iletişim bilgilerini denetleme](#check-a-users-authentication-contact-info) bölümündeki adımları izleyebilirsiniz.

### <a name="check-a-users-authentication-contact-info"></a>Kullanıcının kimlik doğrulaması iletişim bilgilerini denetleme

Kullanıcının Multi-Factor Authentication, koşullu erişim, kimlik koruması ve parola sıfırlama için kullandığı kimlik doğrulama iletişim bilgilerini denetlemek için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm kullanıcılar**' a tıklayın.

6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satıra tıklayın** .

7.  **profil**' e tıklayın.

8.  Aşağı kaydırarak **kimlik doğrulama iletişim bilgilerine** gidin.

9.  Kullanıcı için kaydedilen verileri **gözden geçirin** ve gerektiğinde güncelleştirin.

### <a name="check-a-users-group-memberships"></a>Kullanıcının grup üyeliklerini denetleme

Bir kullanıcının grup üyeliklerini denetlemek için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm kullanıcılar**' a tıklayın.

6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satıra tıklayın** .

7.  kullanıcının üyesi olduğu grupları görmek için **gruplar** ' a tıklayın.

### <a name="check-a-users-assigned-licenses"></a>Kullanıcının atanan lisanslarını denetleme

Bir kullanıcının atanmış lisanslarını denetlemek için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm kullanıcılar**' a tıklayın.

6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satıra tıklayın** .

7.  kullanıcının şu anda hangi lisansları atandığını görmek için **lisanslar** ' a tıklayın.

### <a name="assign-a-user-a-license"></a>Kullanıcıya lisans atama 

Bir kullanıcıya lisans atamak için aşağıdaki adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm kullanıcılar**' a tıklayın.

6.  İlgilendiğiniz kullanıcıyı **arayın** ve seçilecek **satıra tıklayın** .

7.  kullanıcının şu anda hangi lisansları atandığını görmek için **lisanslar** ' a tıklayın.

8.  **ata** düğmesine tıklayın.

9.  Kullanılabilir ürünler listesinden **bir veya daha fazla ürün** seçin.

10. **Isteğe bağlı** olarak, ürünleri daha fazla atamak için **atama seçenekleri** öğesine tıklayın. Bu tamamlandığında **Tamam** ' a tıklayın.

11. Bu lisansları bu kullanıcıya atamak için **ata** düğmesine tıklayın.

## <a name="problems-with-groups"></a>Gruplarla ilgili sorunlar

Uygulamaya atanan bir grup ile ilgili bir sorun nedeniyle uygulama erişimi engelleniyor olabilir. Gruplar ve grup üyelikleriyle ilgili sorunları gidermek ve çözmek için bazı yollar şunlardır:

-   [Grubun üyeliğini denetleme](#check-a-groups-membership)

-   [Dinamik bir grubun üyelik ölçütlerini denetleme](#check-a-dynamic-groups-membership-criteria)

-   [Grubun atanmış lisanslarını denetleme](#check-a-groups-assigned-licenses)

-   [Bir grubun lisanslarını yeniden işleme](#reprocess-a-groups-licenses)

-   [Bir gruba lisans atama](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Grubun üyeliğini denetleme

Bir grubun üyeliğini denetlemek için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm gruplar**' a tıklayın.

6.  İlgilendiğiniz grubu **arayın** ve seçilecek **satıra tıklayın** .

7.  Bu gruba atanan kullanıcıların listesini gözden geçirmek için **Üyeler** ' e tıklayın.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Dinamik bir grubun üyelik ölçütlerini denetleme 

Dinamik bir grubun üyelik ölçütlerini denetlemek için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm gruplar**' a tıklayın.

6.  İlgilendiğiniz grubu **arayın** ve seçilecek **satıra tıklayın** .

7.  **dinamik üyelik kuralları** ' na tıklayın.

8.  Bu grup için tanımlanan **basit** veya **Gelişmiş** kuralı gözden geçirin ve bu grubun üyesi olmasını istediğiniz kullanıcının bu ölçütleri karşıladığından emin olun.

### <a name="check-a-groups-assigned-licenses"></a>Grubun atanmış lisanslarını denetleme

Grubun atanmış lisanslarını denetlemek için şu adımları izleyin:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5.  **tüm gruplar**' a tıklayın.

6.  İlgilendiğiniz grubu **arayın** ve seçilecek **satıra tıklayın** .

7.  grubun şu anda hangi lisanslardan atandığını görmek için **lisanslar** ' a tıklayın.

### <a name="reprocess-a-groups-licenses"></a>Bir grubun lisanslarını yeniden işleme

Bir grubun atanmış lisanslarını yeniden işlemek için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4. Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5. **tüm gruplar**' a tıklayın.

6. İlgilendiğiniz grubu **arayın** ve seçilecek **satıra tıklayın** .

7. grubun şu anda hangi lisanslardan atandığını görmek için **lisanslar** ' a tıklayın.

8. Bu grubun üyelerine atanan lisansların güncel olduğundan emin olmak için **yeniden işle** düğmesine tıklayın. Bu işlem, grubun boyutuna ve karmaşıklığına bağlı olarak uzun zaman alabilir.

   >[!NOTE]
   >Bunu daha hızlı yapmak için, kullanıcıya doğrudan bir lisans atamayı göz önünde bulundurun. [Kullanıcıya bir lisans atayın](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Bir gruba lisans atama

Bir gruba lisans atamak için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4. Gezinti menüsünde **Kullanıcılar ve gruplar** ' a tıklayın.

5. **tüm gruplar**' a tıklayın.

6. İlgilendiğiniz grubu **arayın** ve seçilecek **satıra tıklayın** .

7. grubun şu anda hangi lisanslardan atandığını görmek için **lisanslar** ' a tıklayın.

8. **ata** düğmesine tıklayın.

9. Kullanılabilir ürünler listesinden **bir veya daha fazla ürün** seçin.

10. **Isteğe bağlı** olarak, ürünleri daha fazla atamak için **atama seçenekleri** öğesine tıklayın. Bu tamamlandığında **Tamam** ' a tıklayın.

11. Bu lisansları bu gruba atamak için **ata** düğmesine tıklayın. Bu işlem, grubun boyutuna ve karmaşıklığına bağlı olarak uzun zaman alabilir.

    >[!NOTE]
    >Bunu daha hızlı yapmak için, kullanıcıya doğrudan bir lisans atamayı göz önünde bulundurun. [Kullanıcıya bir lisans atayın](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Koşullu erişim ilkeleriyle ilgili sorunlar

### <a name="check-a-specific-conditional-access-policy"></a>Belirli bir koşullu erişim ilkesini denetleme

Tek bir koşullu erişim ilkesini denetlemek veya doğrulamak için:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4. Gezinti menüsünde **Kurumsal uygulamalar** ' a tıklayın.

5. **koşullu erişim** gezinti öğesine tıklayın.

6. İnceleme konusunda ilgilendiğiniz ilkeye tıklayın.

7. Belirli bir koşul, atama veya Kullanıcı erişimini engelleyebilecek diğer ayarlar olmadığını gözden geçirin.

   >[!NOTE]
   >Bu ilkeyi, oturum açma işlemlerini etkilemediğinden emin olmak için geçici olarak devre dışı bırakmak isteyebilirsiniz. Bunu yapmak için **Ilkeyi etkinleştir** seçeneğini **Hayır** olarak ayarlayın ve **Kaydet** düğmesine tıklayın.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Belirli bir uygulamanın koşullu erişim ilkesini denetleme

Tek bir uygulamanın yapılandırılmış olan koşullu erişim ilkesini denetlemek veya doğrulamak için:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kurumsal uygulamalar** ' a tıklayın.

5.  **tüm uygulamalar**' a tıklayın.

6.  İlgilendiğiniz uygulamayı arayın veya Kullanıcı uygulama görünen adına veya uygulama KIMLIĞINE göre oturum açmaya çalışıyor.

     >[!NOTE]
     >Aradığınız uygulamayı görmüyorsanız, **filtre** düğmesine tıklayın ve listenin kapsamını **tüm uygulamalar**' a genişletin. Daha fazla sütun görmek isterseniz, uygulamalarınız için ek ayrıntılar eklemek üzere **sütunlar** düğmesine tıklayın.
     >
     >

7.  **koşullu erişim** gezinti öğesine tıklayın.

8.  İnceleme konusunda ilgilendiğiniz ilkeye tıklayın.

9.  Belirli bir koşul, atama veya Kullanıcı erişimini engelliyor olabilecek diğer ayarlar olmadığını gözden geçirin.

     >[!NOTE]
     >Bu ilkeyi, oturum açma işlemlerini etkilemediğinden emin olmak için geçici olarak devre dışı bırakmak isteyebilirsiniz. Bunu yapmak için **Ilkeyi etkinleştir** seçeneğini **Hayır** olarak ayarlayın ve **Kaydet** düğmesine tıklayın.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Belirli bir koşullu erişim ilkesini devre dışı bırakma

Tek bir koşullu erişim ilkesini denetlemek veya doğrulamak için:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2.  Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.

4.  Gezinti menüsünde **Kurumsal uygulamalar** ' a tıklayın.

5.  **koşullu erişim** gezinti öğesine tıklayın.

6.  İnceleme konusunda ilgilendiğiniz ilkeye tıklayın.

7.  İlkeyi **Etkinleştir** ayarını **Hayır** olarak ayarlayarak ilkeyi devre dışı bırakın ve **Kaydet** düğmesine tıklayın.

## <a name="problems-with-application-consent"></a>Uygulama onayı ile ilgili sorunlar

Uygun izinler onay işlemi gerçekleşmediğinden uygulama erişimi engellenebilir. Uygulama onayı sorunlarını gidermek ve çözmek için bazı yollar şunlardır:

-   [Kullanıcı düzeyinde onay işlemi gerçekleştirme](#perform-a-user-level-consent-operation)

-   [Herhangi bir uygulama için yönetici düzeyinde onay işlemi gerçekleştirin](#perform-administrator-level-consent-operation-for-any-application)

-   [Tek kiracılı bir uygulama için yönetici düzeyinde onay gerçekleştirme](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Çok kiracılı bir uygulama için yönetici düzeyinde onay gerçekleştirme](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Kullanıcı düzeyinde onay işlemi gerçekleştirme

-   İzinleri isteyen herhangi bir açık KIMLIK Connect özellikli uygulama için, uygulamanın oturum açma ekranına gitmek, oturum açmış kullanıcı için uygulama için Kullanıcı düzeyinde onay gerçekleştirir.

-   Bunu programlı olarak yapmak istiyorsanız, bkz. [bireysel kullanıcı onayı isteme](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Herhangi bir uygulama için yönetici düzeyinde onay işlemi gerçekleştirin

-   **Yalnızca v1 uygulama modeli kullanılarak geliştirilmiş uygulamalarda**, bir uygulamanın oturum açma URL 'sinin sonuna "**? Prompt = admin \_ onay**" ekleyerek bu yönetici düzeyi onayını ortaya çıkmaya zorlayabilirsiniz.

-   **V2 uygulama modeli kullanılarak geliştirilen herhangi bir uygulama** için, [Yönetici onay uç noktasını kullanarak](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) **bir dizin Yöneticisi 'nden izinleri iste** bölümündeki yönergeleri izleyerek bu yönetici düzeyinde onay oluşmasını zorunlu kılabilirsiniz.

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Tek kiracılı bir uygulama için yönetici düzeyinde onay gerçekleştirme

-   İzinleri isteyen **tek kiracılı uygulamalar** (kuruluşunuzda geliştirdiğinize veya sahibiyseniz), genel yönetici olarak oturum açarak ve **uygulama kayıt defteri-tüm uygulamalar ' ın en üstündeki izin ver düğmesine tıklayarak tüm kullanıcılar adına yönetim düzeyinde onay Işlemi gerçekleştirebilirsiniz. &gt; uygulama için &gt; &gt; gerekli izinler bölmesini seçin** .  

-   **V1 veya v2 uygulama modeli kullanılarak geliştirilen herhangi bir uygulama** için, [Yönetici onay uç noktasını kullanarak](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) **bir dizin Yöneticisi 'nden izinleri iste** bölümündeki yönergeleri izleyerek bu yönetici düzeyinde onay oluşmasını zorunlu kılabilirsiniz.

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Çok kiracılı bir uygulama için yönetici düzeyinde onay gerçekleştirme

-   İzinler isteyen **çok kiracılı uygulamalar** (örneğin, üçüncü taraf veya Microsoft, geliştirir) için, **yönetim düzeyinde bir izin** işlemi gerçekleştirebilirsiniz. Genel yönetici olarak oturum açın ve kurumsal uygulamalar-tüm uygulamalar altında **Izin ver** düğmesine tıklayın. **&gt; &gt; bir uygulama &gt; izinleri** bölmesi (yakında kullanılabilir) seçin.

-   Yönetici [onay uç noktasını kullanarak](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) **bir dizin Yöneticisi 'nden izinleri iste** bölümündeki yönergeleri izleyerek bu yönetici düzeyinde onay uygulanmasını da zorunlu kılabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[Yönetici onay uç noktasını kullanma](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)