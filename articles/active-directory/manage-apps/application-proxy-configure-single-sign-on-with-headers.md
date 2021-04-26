---
title: Azure AD Uygulaması Proxy ile şirket içi uygulamalar için üst bilgi tabanlı çoklu oturum açma
description: Üst bilgi tabanlı kimlik doğrulamasıyla güvenliği sağlanan şirket içi uygulamalar için çoklu oturum açma sağlamayı öğrenin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 813e360ffc6c78b52151af5b9867560ae6d58f2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198250"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy"></a>Azure AD Uygulaması Proxy ile şirket içi uygulamalar için üst bilgi tabanlı çoklu oturum açma

Azure Active Directory (Azure AD) uygulama proxy 'Si, kimlik doğrulaması için üst bilgileri kullanan uygulamalara çoklu oturum açma erişimini yerel olarak destekler. Azure AD 'de uygulamanız için gereken üst bilgi değerlerini yapılandırabilirsiniz. Üst bilgi değerleri uygulama proxy 'Si aracılığıyla uygulamaya gönderilir. Uygulama proxy 'Si ile üst bilgi tabanlı kimlik doğrulaması için yerel destek kullanmanın avantajları şunlardır:  

* Şirket **içi uygulamalarınıza uzaktan erişim sağlamayı kolaylaştırın** -uygulama proxy 'si, mevcut uzaktan erişim mimarinizi basitleştirmenizi sağlar. VPN erişimini bu uygulamalara değiştirebilirsiniz. Ayrıca, kimlik doğrulaması için şirket içi kimlik çözümlerindeki bağımlılıkları da kaldırabilirsiniz. Kullanıcılarınız şirket uygulamalarınızı kullanmak için oturum açtıklarında farklı hiçbir şey yapmaz. Herhangi bir cihazdaki herhangi bir yerden çalışmaya devam edebilirler.  

* **Uygulamalarınızdaki ek yazılım veya değişiklik yok** -mevcut uygulama proxy bağlayıcılarınızı kullanabilir ve ek bir yazılımın yüklenmesini gerektirmez.  

* **Kullanılabilen özniteliklerin ve dönüşümlerin geniş listesi** -kullanılabilir tüm üst bilgi değerleri, Azure AD tarafından verilen standart talepleri temel alır. [SAML veya OıDC uygulamalarına yönelik talepleri yapılandırmaya](../develop/active-directory-saml-claims-customization.md#attributes) yönelik tüm öznitelikler ve dönüştürmeler, üst bilgi değerleri olarak da kullanılabilir. 

## <a name="pre-requisites"></a>Ön koşullar
Üst bilgi tabanlı kimlik doğrulama uygulamaları için çoklu oturum açma ile çalışmaya başlamadan önce ortamınızın aşağıdaki ayarlar ve yapılandırmalara göre hazırlandığına emin olun:
- Uygulama ara sunucusunu etkinleştirip uygulamalarınıza site satırı olan bir bağlayıcı yüklemeniz gerekir. Şirket içi ortamınızı hazırlamaya, bir bağlayıcıyı yüklemeye ve kaydetmeye ve bağlayıcıyı test etme hakkında bilgi edinmek için [uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) öğreticisine bakın. 

## <a name="supported-capabilities"></a>Desteklenen özellikler

Aşağıdaki tabloda, uygulama proxy 'Si ile desteklenen üst bilgi tabanlı kimlik doğrulama uygulamaları için gereken yaygın yetenekler listelenmektedir. 

|Gereksinim   |Açıklama|
|----------|-----------|
|Federasyon SSO'su |Ön kimlik doğrulamalı modda, tüm uygulamalar Azure AD kimlik doğrulamasıyla korunur ve kullanıcıların çoklu oturum açma özelliğini etkinleştirir. |
|Uzaktan erişim |Uygulama proxy 'Si, uygulamaya uzaktan erişim sağlar. Kullanıcılar, dış URL 'yi kullanarak herhangi bir tarayıcıda uygulamaya internet 'ten erişebilir. Uygulama proxy 'Si, kurumsal erişim kullanımı için tasarlanmamıştır. |
|Üst bilgi tabanlı tümleştirme |Uygulama proxy 'Si, SSO 'yu Azure AD ile tümleştirerek kimliği veya diğer uygulama verilerini uygulamaya HTTP üstbilgileri olarak geçirir. |
|Uygulama yetkilendirmesi |Ortak ilkeler, erişilmekte olan uygulamaya, kullanıcının grup üyeliğine ve diğer ilkelere göre belirlenebilir. Azure AD 'de ilkeler [koşullu erişim](../conditional-access/overview.md)kullanılarak uygulanır. Uygulama yetkilendirme ilkeleri yalnızca ilk kimlik doğrulama isteği için geçerlidir. |
|Adım kimlik doğrulaması |İlkeler, eklenen kimlik doğrulamasını zorlamak için tanımlanabilir, örneğin, hassas kaynaklara erişim elde edilebilir. |
|Hassas yetkilendirme |URL düzeyinde erişim denetimi sağlar. Eklenen ilkeler erişilen URL 'ye göre uygulanabilir. Uygulama için yapılandırılan iç URL, ilkenin uygulandığı uygulamanın kapsamını tanımlar. En ayrıntılı yol için yapılandırılan ilke zorlanır.  |

> [!NOTE] 
> Bu makalede, uygulama proxy 'Si kullanarak üst bilgi tabanlı kimlik doğrulama uygulamalarını Azure AD 'ye bağlama ve önerilen modeldir. Alternatif olarak, üst bilgi tabanlı kimlik doğrulamasını etkinleştirmek için Azure AD ile PingAccess kullanan bir tümleştirme deseninin de vardır. Daha fazla ayrıntı için bkz. [uygulama proxy 'si ve PingAccess ile çoklu oturum açma Için üst bilgi tabanlı kimlik doğrulama](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Nasıl çalışır?

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Üst bilgi tabanlı çoklu oturum açma, uygulama proxy 'Si ile nasıl kullanılır." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. Yönetici, Azure AD portalındaki uygulamanın gerektirdiği öznitelik eşlemelerini özelleştirir. 
2. Kullanıcı uygulamaya eriştiğinde, uygulama proxy 'Si kullanıcının kimliğinin Azure AD tarafından doğrulanmasını sağlar 
3. Uygulama proxy 'Si bulut hizmeti, gerekli özniteliklerin farkındadır. Bu nedenle hizmet, kimlik doğrulama sırasında alınan KIMLIK belirtecinden ilgili talepleri getirir. Daha sonra hizmet, bağlayıcıya isteğin bir parçası olarak değerleri gerekli HTTP üst bilgilerine çevirir. 
4. Daha sonra bu istek bağlayıcıya geçirilir ve ardından arka uç uygulamasına geçirilir. 
5. Uygulama üst bilgileri alır ve gerektiğinde bu üst bilgileri kullanabilir. 

## <a name="publish-the-application-with-application-proxy"></a>Uygulamayı uygulama proxy 'Si ile yayımlama

1. Uygulamanızı [uygulama proxy 'si Ile yayımlama](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)bölümünde açıklanan yönergelere göre yayımlayın.  
    - Iç URL değeri, uygulamanın kapsamını belirler. Uygulamanın kök yolunda Iç URL değerini yapılandırırsanız, kök altındaki tüm alt yollar aynı üstbilgi yapılandırmasını ve diğer uygulama yapılandırmasını alır. 
    - Yapılandırdığınız uygulamadan daha ayrıntılı bir yol için farklı bir üst bilgi yapılandırması veya Kullanıcı Ataması ayarlamak üzere yeni bir uygulama oluşturun. Yeni uygulamada, iç URL 'YI istediğiniz belirli bir yolla yapılandırın ve ardından bu URL için gereken belirli üst bilgileri yapılandırın. Uygulama proxy 'Si, yapılandırma ayarlarınızla her zaman bir uygulama için ayarlanan en ayrıntılı yol kümesiyle eşleşir. 

2.  ****    **Ön kimlik doğrulama yöntemi** olarak Azure Active Directory ' yi seçin. 
3. **Kullanıcılar ve gruplar** ' a giderek ve uygun kullanıcıları ve grupları atayarak bir test kullanıcısı atayın. 
4. Bir tarayıcı açın ve uygulama proxy 'si ayarlarından **dış URL**'ye gidin   . 
5. Uygulamaya bağlanabildiğinizi doğrulayın. Bağlanabilseniz de, üst bilgiler yapılandırılmadığı için uygulamaya henüz erişemezsiniz. 

## <a name="configure-single-sign-on"></a>Çoklu oturum açmayı yapılandırma 
Üst bilgi tabanlı uygulamalar için çoklu oturum açma ile çalışmaya başlamadan önce, bir uygulama proxy Bağlayıcısı zaten yüklemiş olmanız ve bağlayıcı hedef uygulamalara erişebilmelidir. Aksi takdirde, [öğretici: Azure AD uygulama ara sunucusu](application-proxy-add-on-premises-application.md)içindeki adımları izleyin ve   sonra buraya geri dönün. 

1. Uygulamanız kurumsal uygulamalar listesinde görüntülendikten sonra, seçin ve **Çoklu oturum açma**' yı seçin. 
2. Çoklu oturum açma modunu **üst bilgi tabanlı** olarak ayarlayın. 
3. Temel yapılandırmada **Azure Active Directory**, varsayılan olarak seçilir. 
4. Uygulamaya göndermek üzere üst bilgileri yapılandırmak için üst bilgiler ' de Düzenle kalem ' i seçin. 
5. **Yeni üst bilgi Ekle**' yi seçin. Üst bilgi için bir **ad** girin ve **öznitelik** veya **dönüşüm** ' i seçin ve uygulamanızın ihtiyaç duyacağı üst bilgiden seçim yapın.  
    - Kullanılabilir öznitelik listesi hakkında daha fazla bilgi edinmek için bkz. [talep özelleştirmeleri-öznitelikler](../develop/active-directory-saml-claims-customization.md#attributes). 
    - Kullanılabilir dönüştürme listesi hakkında daha fazla bilgi edinmek için bkz. [talep özelleştirmeleri-talep dönüştürmeleri](../develop/active-directory-saml-claims-customization.md#claim-transformations). 
    - Bir kullanıcının parçası olduğu tüm grupları veya bir üst bilgi olarak uygulamaya atanan grupları göndermek için de bir **Grup üstbilgisi** ekleyebilirsiniz. Grupları bir değer olarak yapılandırma hakkında daha fazla bilgi için bkz: [uygulamalar için grup taleplerini yapılandırma](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Kaydet’i seçin. 

## <a name="test-your-app"></a>Uygulamanızı test etme 

Tüm bu adımları tamamladığınızda, uygulamanız çalışıyor ve kullanılabilir olmalıdır. Uygulamayı test etmek için: 
1. Daha önce önbelleğe alınmış üstbilgilerin temizlendiğinden emin olmak için yeni bir tarayıcı veya özel tarayıcı penceresi açın. Ardından, uygulama proxy 'si ayarlarından **dış URL**'ye gidin   .
2. Uygulamaya atadığınız test hesabıyla oturum açın. SSO kullanarak uygulamayı yükleyip oturum açabilirseniz, iyi bir uygulamadır! 

## <a name="considerations"></a>Dikkat edilmesi gerekenler

- Uygulama proxy 'Si, şirket içinde veya özel bulutta uygulamalara uzaktan erişim sağlamak için kullanılır. Şirket ağından iç kaynaklı trafiği işlemek için uygulama proxy 'Si önerilmez.
- Üst bilgi tabanlı kimlik doğrulama uygulamalarına erişim yalnızca bağlayıcının veya diğer izin verilen üst bilgi tabanlı kimlik doğrulama çözümünün trafiğiyle sınırlandırılmalıdır. Bu, uygulama sunucusunda bir güvenlik duvarı veya IP kısıtlaması kullanılarak uygulamaya ağ erişimini kısıtlayarak genellikle yapılır.

## <a name="next-steps"></a>Sonraki adımlar

- [Çoklu oturum açma nedir?](what-is-single-sign-on.md)
- [Uygulama proxy nedir?](what-is-application-proxy.md)
- [Uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md)
