---
title: Azure AD Galeri uygulaması için Federasyon çoklu oturum açmayı yapılandırma | Microsoft Docs
description: Mevcut bir Azure AD Galeri uygulaması için Federasyon çoklu oturum açmayı yapılandırma ve öğreticilerle hızlı bir şekilde çalışmaya başlama
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
ROBOTS: NOINDEX
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207122"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD Galeri uygulaması için Federasyon çoklu oturum açmayı yapılandırma

Kurumsal Çoklu oturum açma özelliğine sahip Azure Active Directory (Azure AD) galerisinde bulunan tüm uygulamalar için adım adım bir öğretici vardır. Ayrıntılı adım adım yönergeler için, [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğretici listesine](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) erişebilirsiniz.

## <a name="overview-of-steps-required"></a>Gerekli adımlara genel bakış
Azure AD Galerisi 'nden bir uygulamayı yapılandırmak için şunları yapmanız gerekir:

-   [Azure AD Galerisi 'nden uygulama ekleme](#add-an-application-from-the-azure-ad-gallery)

-   [Azure AD 'de uygulamanın meta veri değerlerini yapılandırma (oturum açma URL 'SI, tanımlayıcı, yanıt URL 'SI)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Kullanıcı tanımlayıcısı ' nı seçin ve uygulamaya gönderilecek kullanıcı özniteliklerini ekleyin](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD meta verilerini ve sertifikasını alma](#download-the-azure-ad-metadata-or-certificate)

-   [Uygulamada Azure AD meta veri değerlerini yapılandırma (oturum açma URL 'SI, verenin, oturum kapatma URL 'SI ve sertifika)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Uygulamaya Kullanıcı atama](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD Galerisi 'nden uygulama ekleme

Azure AD Galerisi 'nden bir uygulama eklemek için şu adımları izleyin:

1.  [Azure Portal](https://portal.azure.com) açın ve **genel yönetici** veya **ortak yönetici**olarak oturum açın.

2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3.  Arama kutusuna "Azure Active Directory" yazın ve **Azure Active Directory**' ı seçin.

4.  Azure AD sol tarafı gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.

5.  **Kurumsal uygulamalar** bölmesindeki sağ üst köşede **Ekle** ' yi seçin.

6.  **Galeriden Ekle** bölümünde **bir ad girin** kutusuna uygulamanın adını yazın.

7.  Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

8.  Uygulamayı eklemeden önce **ad** kutusunda adını değiştirebilirsiniz.

9.  Uygulamayı eklemek için **Ekle** ' yi seçin.

Kısa bir süre sonra uygulamanın yapılandırma bölmesini görmeniz gerekir.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Azure AD galerisinden bir uygulama için çoklu oturum açmayı yapılandırma

Bir uygulama için çoklu oturum açmayı yapılandırmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici**olarak oturum açın.

2. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3. Arama kutusuna "Azure Active Directory" yazın ve **Azure Active Directory**' ı seçin.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.

5. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   * Burada istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.

6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğini belirleyin.

8. **Mod** açılır listesinden **SAML tabanlı oturum açma '** yı seçin.

9. **Etki alanı ve URL 'ler** için gerekli değerleri girin. Bu değerleri uygulama satıcısından almalısınız.

   1. Uygulamayı SP tarafından başlatılan SSO olarak yapılandırmak için, oturum açma URL 'SI gerekli bir değerdir. Bazı uygulamalarda, tanımlayıcı de gerekli bir değerdir.

   2. Uygulamayı IDP tarafından başlatılan SSO olarak yapılandırmak için, yanıt URL 'SI gerekli bir değerdir. Bazı uygulamalarda, tanımlayıcı de gerekli bir değerdir.

10. **Isteğe bağlı**: Gerekli olmayan değerleri görmek istiyorsanız **GELIŞMIŞ URL ayarlarını göster** ' i seçin.

11. **Kullanıcı öznitelikleri**' nde, **Kullanıcı tanımlayıcısı** açılan menüsünde kullanıcılarınız için benzersiz tanımlayıcıyı seçin.

12. **Isteğe bağlı**: Kullanıcılar oturum açtığında SAML belirtecinde uygulamaya gönderilecek öznitelikleri düzenlemek için **diğer tüm Kullanıcı özniteliklerini görüntüle ve Düzenle '** yi seçin.

    Bir öznitelik eklemek için:
   
    1. **Öznitelik Ekle**' yi seçin. **Adı** girin ve açılan listeden **değeri** seçin.

    1. Kaydet ' i seçin **.** Yeni özniteliği tabloda görürsünüz.

13. Uygulamada çoklu oturum açmayı yapılandırma ile ilgili belgelere erişmek için  **&lt;uygulama adını&gt; Yapılandır** ' ı seçin. Ayrıca, uygulama ile SSO 'yu ayarlamak için gerekli meta veri URL 'Leri ve sertifikanız vardır.

14. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin.

15. Kullanıcılara uygulama atama.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Kullanıcı tanımlayıcısı ' nı seçin ve uygulamaya gönderilecek kullanıcı özniteliklerini ekleyin

Kullanıcı tanımlayıcısını seçmek veya Kullanıcı öznitelikleri eklemek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3. Arama kutusuna "Azure Active Directory" yazın ve **Azure Active Directory**' ı seçin.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.

5. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   * Burada istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.

6. Çoklu oturum açma ile yapılandırdığınız uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğini belirleyin.

8. **Kullanıcı öznitelikleri** bölümünde, **Kullanıcı tanımlayıcısı** açılan menüsünde kullanıcılarınız için benzersiz tanımlayıcıyı seçin. Seçili seçeneğin, kullanıcının kimliğini doğrulamak için uygulamadaki beklenen değerle eşleşmesi gerekir.

   >[!NOTE] 
   >Azure AD, NameID özniteliği (Kullanıcı tanımlayıcısı) için seçilen değere veya SAML AuthRequest içinde uygulama tarafından istenen biçime göre biçim seçer. Daha fazla bilgi için bkz. Nameıdpolicy bölümünde [Çoklu oturum açma SAML Protokolü](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) .
   >
   >

9. Kullanıcı öznitelikleri eklemek için, kullanıcılar oturum açtığında SAML belirtecinde uygulamaya gönderilecek öznitelikleri düzenlemek için **diğer tüm Kullanıcı özniteliklerini görüntüle ve Düzenle** ' yi seçin.

   Bir öznitelik eklemek için:
  
   1. **Öznitelik Ekle**' yi seçin. **Adı** girin ve açılan listeden **değeri** seçin.

   2. **Kaydet**’i seçin. Yeni özniteliği tabloda görürsünüz.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Azure AD meta verilerini veya sertifikasını indirin

Azure AD 'den uygulama meta verilerini veya sertifikasını indirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3. Arama kutusuna "Azure Active Directory" yazın ve **Azure Active Directory**' ı seçin.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.

5. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   *  Burada istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar**olarak ayarlayın.

6. Çoklu oturum açma ile yapılandırdığınız uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğini belirleyin.

8. **SAML Imzalama sertifikası** bölümüne gidin ve ardından sütun değerini **İndir** ' i seçin. Uygulamanın çoklu oturum açmayı yapılandırma ihtiyacı olduğuna bağlı olarak, meta veri XML 'sini veya sertifikayı indirme seçeneğini görürsünüz.

Azure AD, meta verilere erişim için de bir URL sağlar. Uygulamaya özel meta veri URL 'sini almak için aşağıdaki modeli kullanın:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Uygulamaya kullanıcı atama

Bir uygulamaya doğrudan bir veya daha fazla kullanıcı atamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) açın ve **genel yönetici** olarak oturum açın.

2. Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3. Arama kutusuna "Azure Active Directory" yazın ve **Azure Active Directory**' ı seçin.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.

5. Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

   * Burada istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.

6. Listeden bir kullanıcıya atamak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol tarafındaki gezinti menüsünden **Kullanıcılar ve gruplar** ' ı seçin.

8. **Atama Ekle** bölmesini açmak için **Kullanıcılar ve gruplar** listesinin üstündeki **Ekle** düğmesini seçin.

9. **Atama Ekle** bölmesinden **Kullanıcılar ve gruplar** seçicisini seçin.

10. **Ada veya e-posta adresine göre ara** arama kutusuna atamak istediğiniz kullanıcının **tam adını** veya **e-posta adresini** yazın.

11. **Onay kutusunu**göstermek için listedeki **kullanıcının** üzerine gelin. Kullanıcıyı **Seçili** listeye eklemek için kullanıcının profil fotoğrafı veya logosu yanındaki onay kutusunu işaretleyin.

12. **Isteğe bağlı**: Birden **fazla kullanıcı eklemek**istiyorsanız **ada veya e-posta adresine göre ara** kutusuna başka bir **tam ad** veya **e-posta adresi** yazın ve bu kullanıcıyı **Seçili** listeye eklemek için onay kutusunu işaretleyin.

13. Kullanıcıları seçmeyi tamamladığınızda, uygulamayı atanacak kullanıcılar ve gruplar listesine eklemek için **Seç** düğmesini seçin.

14. **Isteğe bağlı**: Seçtiğiniz kullanıcılara atanacak bir rol seçmek için **atama Ekle** bölmesinde **rol seçicisini Seç** ' i seçin.

15. Uygulamayı seçili kullanıcılara atamak için **ata** düğmesini seçin.

Bir kısa süre sonra seçtiğiniz kullanıcıların çözüm Açıklama bölümünde açıklanan yöntemleri kullanarak bu uygulamaları başlatması mümkün.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Bir uygulamaya gönderilen SAML taleplerini özelleştirme

Uygulamanıza gönderilen SAML öznitelik taleplerini özelleştirmeyi öğrenmek için [Azure Active Directory 'de talep eşlemesi](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar
[Uygulama Ara sunucusu ile uygulamalarınıza çoklu oturum açma sağlayın](application-proxy-configure-single-sign-on-with-kcd.md)



