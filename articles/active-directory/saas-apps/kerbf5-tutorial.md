---
title: 'Öğretici: F5 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve F5 arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea331bbabe238c351921a02a5012a9f8a087646f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166313"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Öğretici: F5 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, F5 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. F5 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de F5 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla F5 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* F5 çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* F5 **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* F5 SSO üç farklı şekilde yapılandırılabilir.

- [Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](#configure-f5-single-sign-on-for-kerberos-application)

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Galeriden F5 ekleme

F5 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize F5 ' i eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **F5** yazın.
1. Sonuçlar panelinden **F5** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>F5 için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu F5 ile yapılandırın ve test edin. SSO 'nun çalışması için, F5 'teki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu F5 ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[F5 SSO 'Yu yapılandırın](#configure-f5-sso)** .
    1. F5 'te kullanıcının Azure AD gösterimine bağlı olan B. Simon 'un bir karşılığı olacak şekilde **[F5 test kullanıcısı oluşturun](#create-f5-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **F5** uygulaması tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YourCustomFQDN>.f5.com/`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YourCustomFQDN>.f5.com/`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [F5 istemci destek ekibine](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **F5 ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, F5 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **F5**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-f5-sso"></a>F5 SSO 'yu Yapılandır

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve F5 (Kerberos) Şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Meta veri sertifikasını, kurulum işleminin ilerleyen kısımlarında kullanılacak F5 (Kerberos) içine aktarmanız gerekir. **> trafik sertifika yönetimi > > SSL sertifika listesi**' ne > gidin. Sağ köşedeki **Içeri aktarma** seçeneğine tıklayın.

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure01.png) 

1. Ayrıca, ana bilgisayar adı (`Kerbapp.superdemo.live`) için bir **SSL sertifikasına** de ihtiyacınız vardır. Bu örnekte joker karakter sertifikası kullanılmaktadır.

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure02.png) 
 
1. Şuraya git – **F5 BIG-IP erişim > Kılavuzlu yapılandırma > federasyon > SAML hizmet sağlayıcısı ' na tıklayın**.

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure03.png) 

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure04.png)

1. **VARLıK kimliğini** belirtin (Azure AD uygulama yapılandırmasında yapılandırdığınız ile aynı)

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure05.png) 

1. Yeni bir sanal sunucu oluşturun, **hedef adresi**belirtin. Daha önce ve **Ilişkili özel anahtarı**karşıya yüklediğimiz **joker karakter sertifikasını** (veya uygulama için karşıya yüklediğiniz **sertifikayı** ) seçin.

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure06.png)

1. Yapılandırma **meta verilerini** karşıya YÜKLEYIN ve **SAML IDP Bağlayıcısı Için** yeni bir ad belirtin. Ayrıca, daha önce karşıya yüklenen Federasyon sertifikasını belirtmeniz gerekecektir.

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure07.png)  

1. **Yeni oluştur** Arka uç uygulama havuzu, arka uç uygulama sunucularının **IP adreslerini** belirtin.
 
    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure08.png)

1. **Çoklu oturum açma ayarları**altında **Kerberos** ' u seçin ve **Gelişmiş ayarlar**' ı seçin. İsteğin oluşturulması `user@domain.suffix`gerekir.

1. **Kullanıcı adı kaynağında** belirtin `session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`. Değişkenlerin ve değerlerin tamamı listesi için ek 'e bakın.
Hesap adı F5 temsilcisini oluşturma hesabıdır (F5 belgelerini kontrol edin).

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure09.png)   

1. **Uç nokta denetimi özellikleri** belge ayrıntıları için [F5 (Kerberos) istemci desteği ekibine](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) başvurun.

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure10.png) 

1. **Oturum yönetimi özellikleri** belge ayrıntıları için [F5 (Kerberos) istemci desteği ekibine](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) başvurun.

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure11.png) 
 
1. **Özeti gözden geçirin** ve **Dağıt**' a tıklayın.
 
    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure12.png)

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure13.png)

### <a name="create-f5-test-user"></a>F5 test kullanıcısı oluştur

Bu bölümde, F5 'te B. Simon adlı bir Kullanıcı oluşturacaksınız. F5 platformunda kullanıcıları eklemek için [F5 istemci destek ekibi](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) ile çalışın. Kullanıcı oluşturulmalı ve çoklu oturum açma kullanmadan önce etkinleştirildi. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde F5 kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız F5 'te otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile F5 'i deneyin](https://aad.portal.azure.com/)

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](advance-kerbf5-tutorial.md)

