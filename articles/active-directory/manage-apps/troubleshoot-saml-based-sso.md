---
title: Azure Active Directory'de SAML tabanlı çoklu oturum açma sorunlarını giderme
description: SAML tabanlı çoklu oturum açma için yapılandırılmış bir Azure AD uygulamasıyla ilgili sorunları giderin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.openlocfilehash: c4a4f7bfad4254e7c3fa5851e62532ed427ced8b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376435"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Azure Active Directory'de SAML tabanlı çoklu oturum açma sorunlarını giderme
Bir uygulamayı yapılandırırken sorunla karşılaşırsanız. Uygulamanın öğreticisindeki tüm adımları izlemediğinizi doğrulayın. Uygulamanın yapılandırmasında, uygulamanın nasıl yapılandırılacağı üzerine satır içi belgeleriniz vardır. Ayrıca, ayrıntılı adım adım yönergeler için [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğretici listesine](../saas-apps/tutorial-list.md) erişebilirsiniz.

## <a name="cant-add-another-instance-of-the-application"></a>Uygulamanın başka bir örneği eklenemiyor
Uygulamanın ikinci bir örneğini eklemek için şunları yapmanız gerekir:
-   İkinci örnek için benzersiz bir tanımlayıcı yapılandırın. İlk örnek için kullanılan tanımlayıcıyı yapılandıramayacaksınız.
-   İlk örnek için kullanılandan farklı bir sertifika yapılandırın.

Uygulama yukarıdakilerden hiçbirini desteklemiyorsa. Daha sonra, ikinci bir örnek yapılandıramayacaksınız.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Tanımlayıcı veya yanıt URL 'SI eklenemiyor
Tanımlayıcıyı veya yanıt URL 'sini yapılandıramıyorsunuz, tanımlayıcıyı onaylayın ve yanıt URL 'SI değerleri, uygulama için önceden yapılandırılmış desenlerle eşleşir.

Uygulama için önceden yapılandırılmış desenleri bildirmek için:
1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın. 7. adıma gidin. Azure AD 'de uygulama yapılandırması dikey penceresinde zaten varsa.
2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.
3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.
5. tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' a tıklayın.
   * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.
6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin.
7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.
8. **Mod** açılır listesinden **SAML tabanlı oturum açma '** yı seçin.
9. **Etki alanı ve URL 'ler bölümünde** **tanımlayıcı** veya **yanıt URL** metin kutusuna gidin.
10. Uygulama için desteklenen desenleri bilmenin üç yolu vardır:
    * Metin kutusunda desteklenen desenler (ler) yer tutucu örneği olarak görürsünüz *:* <https://contoso.com> .
    * desenler desteklenmiyorsa, metin kutusuna değer girmeye çalıştığınızda kırmızı bir ünlem işareti görürsünüz. Farenizi kırmızı ünlem işaretinin üzerine getirdiğinizde desteklenen desenleri görürsünüz.
    * Uygulamanın öğreticisinde, Desteklenen desenler hakkında da bilgi edinebilirsiniz. **Azure AD çoklu oturum açma yapılandırma** bölümünün altında. **Etki alanı ve URL 'ler** bölümünde değerleri yapılandırmak için adıma gidin.

Değerler, Azure AD 'de önceden yapılandırılmış desenlerle eşleşmezse. Seçenekleriniz şunlardır:
-   Azure AD 'de önceden yapılandırılmış olan desenli eşleşen değerleri almak için uygulama satıcısıyla birlikte çalışın
-   Ya da, <aadapprequest@microsoft.com> uygulama için desteklenen desenlerin güncelleştirilmesini istemek Için Azure AD ekibine başvurabilirsiniz veya öğreticide bir yorum bırakabilirsiniz

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>EntityId (Kullanıcı tanımlayıcısı) biçimini nereden ayarlayabilirim?
Azure AD 'nin Kullanıcı kimlik doğrulamasından sonra yanıtta uygulamaya gönderdiği EntityId (Kullanıcı tanımlayıcısı) biçimini seçemeyeceksiniz.

Azure AD, seçilen değere veya SAML AuthRequest 'te uygulama tarafından istenen biçime göre NameID özniteliği (Kullanıcı tanımlayıcısı) biçimini seçin. Daha fazla bilgi için, Nameıdpolicy bölümünde [tek Sign-On SAML Protokolü](../develop/single-sign-on-saml-protocol.md#authnrequest) makalesini ziyaret edin,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Uygulama ile yapılandırmayı tamamlamaya yönelik Azure AD meta verileri bulunamıyor
Azure AD 'den uygulama meta verilerini veya sertifikasını indirmek için şu adımları izleyin:
1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.
2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.
3. Filtre arama kutusuna **"Azure Active Directory**" yazın ve **Azure Active Directory** öğesini seçin.
4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.
5. tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' a tıklayın.
   * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.
6. Çoklu oturum açma yapılandırdığınız uygulamayı seçin.
7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.
8. **SAML Imzalama sertifikası** bölümüne gidin ve ardından sütun değerini **İndir** ' e tıklayın. Uygulamanın çoklu oturum açmayı yapılandırma ihtiyacı olduğuna bağlı olarak, meta veri XML 'sini veya sertifikayı indirme seçeneğini görürsünüz.

Azure AD meta verileri almak için bir URL sağlamıyor. Meta veriler yalnızca bir XML dosyası olarak alınabilir.

## <a name="customize-saml-claims-sent-to-an-application"></a>Uygulamaya gönderilen SAML taleplerini özelleştirme
Uygulamanıza gönderilen SAML öznitelik taleplerini özelleştirmeyi öğrenmek için, daha fazla bilgi için [Azure Active Directory 'Da talep eşleme](../develop/active-directory-claims-mapping.md) konusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar
* [Uygulama yönetiminde hızlı başlangıç serisi](view-applications-portal.md)