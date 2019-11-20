---
title: Azure AD Galeri uygulamaları için Federasyon çoklu oturum açmayı yapılandırma sorunları | Microsoft Docs
description: Azure AD uygulama galerisinde listelenen uygulamalar için SAML kullanarak federe çoklu oturum açmayı yapılandırırken karşılaşabileceğiniz yaygın sorunlardan bazılarını çözün
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f4e690b440604a312ddfa8cd320ac200c0f40cf
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381211"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD Galeri uygulaması için Federasyon çoklu oturum açmayı yapılandırma sorunu

Bir uygulamayı yapılandırırken sorunla karşılaşırsanız. Uygulamanın öğreticisindeki tüm adımları izlemediğinizi doğrulayın. Uygulamanın yapılandırmasında, uygulamanın nasıl yapılandırılacağı üzerine satır içi belgeleriniz vardır. Ayrıca, ayrıntılı adım adım yönergeler için [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğretici listesine](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) erişebilirsiniz.

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

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **Mod** açılır listesinden **SAML tabanlı oturum açma '** yı seçin.

9. **Etki alanı ve URL 'ler bölümünde** **tanımlayıcı** veya **yanıt URL** metin kutusuna gidin.

10. Uygulama için desteklenen desenleri bilmenin üç yolu vardır:

    * Metin kutusunda desteklenen desenler (ler) yer tutucu örneği olarak görürsünüz *:* <https://contoso.com>.

    * desenler desteklenmiyorsa, metin kutusuna değer girmeye çalıştığınızda kırmızı bir ünlem işareti görürsünüz. Farenizi kırmızı ünlem işaretinin üzerine getirdiğinizde desteklenen desenleri görürsünüz.

    * Uygulamanın öğreticisinde, Desteklenen desenler hakkında da bilgi edinebilirsiniz. **Azure AD çoklu oturum açma yapılandırma** bölümünün altında. **Etki alanı ve URL 'ler** bölümünde değerleri yapılandırmak için adıma gidin.

Değerler, Azure AD 'de önceden yapılandırılmış desenlerle eşleşmezse. Şunları yapabilirsiniz:

-   Azure AD 'de önceden yapılandırılmış olan desenli eşleşen değerleri almak için uygulama satıcısıyla birlikte çalışın

-   Ya da, uygulama için desteklenen desenlerin güncelleştirilmesini <aadapprequest@microsoft.com> istemek için Azure AD ekibine başvurabilirsiniz veya öğreticide bir yorum bırakabilirsiniz

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>EntityId (Kullanıcı tanımlayıcısı) biçimini nereden ayarlayabilirim?

Azure AD 'nin Kullanıcı kimlik doğrulamasından sonra yanıtta uygulamaya gönderdiği EntityId (Kullanıcı tanımlayıcısı) biçimini seçemeyeceksiniz.

Azure AD, seçilen değere veya SAML AuthRequest 'te uygulama tarafından istenen biçime göre NameID özniteliği (Kullanıcı tanımlayıcısı) biçimini seçin. Daha fazla bilgi için, Nameıdpolicy bölümünde [Çoklu oturum açma SAML Protokolü](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) makalesini ziyaret edin.

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Uygulama ile yapılandırmayı tamamlamaya yönelik Azure AD meta verileri bulunamıyor

Azure AD 'den uygulama meta verilerini veya sertifikasını indirmek için şu adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açma yapılandırdığınız uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **SAML Imzalama sertifikası** bölümüne gidin ve ardından sütun değerini **İndir** ' e tıklayın. Uygulamanın çoklu oturum açmayı yapılandırma ihtiyacı olduğuna bağlı olarak, meta veri XML 'sini veya sertifikayı indirme seçeneğini görürsünüz.

Azure AD meta verileri almak için bir URL sağlamıyor. Meta veriler yalnızca bir XML dosyası olarak alınabilir.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Bir uygulamaya gönderilen SAML taleplerini özelleştirmeyi bilmiyorum

Uygulamanıza gönderilen SAML öznitelik taleplerini özelleştirmeyi öğrenmek için, daha fazla bilgi için [Azure Active Directory 'Da talep eşleme](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) konusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar
[Uygulamaları Azure Active Directory ile yönetme](what-is-application-management.md)
