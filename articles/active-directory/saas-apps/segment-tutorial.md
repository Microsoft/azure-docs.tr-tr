---
title: 'Öğretici: kesimle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve segment arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: fe8acfd1bfd14f339a0109cab215b8a9ab65256f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021563"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>Öğretici: kesimle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, segmenti Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Segmenti Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de segmente erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla segmentlere ayırmak için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli abonelik segmenti.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Segment **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Segment **, tam zamanında** Kullanıcı sağlamayı destekler

* Segmenti yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve infilini koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-segment-from-the-gallery"></a>Galeriden segment ekleme

Segmentin Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize segment eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **segment** yazın.
1. Sonuçlar panelinden **segment** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-segment"></a>Segment için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu kesimle yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve kesimdeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu kesimle yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Segment SSO 'Yu yapılandırma](#configure-segment-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan kesimdeki B. Simon 'a karşılık gelen segment **[test kullanıcısı oluşturun](#create-segment-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **segment** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın:`https://app.segment.com`

    > [!NOTE]
    > Bu değerler yer tutuculardır. Gerçek tanımlayıcıyı, yanıt URL 'sini ve oturum açma URL 'sini kullanmanız gerekir. Bu değerleri almak için adımlar Bu öğreticinin ilerleyen kısımlarında açıklanmıştır.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Segmenti ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, segmente erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **segment**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-segment-sso"></a>Segment SSO 'yu Yapılandır

1. Yeni bir Web tarayıcısı penceresinde, segmentinizdeki şirket sitenizde yönetici olarak oturum açın.

1. **Ayarlar simgesine** tıklayın ve **kimlik doğrulaması** ' na gidin ve **Bağlantılar**' a tıklayın.

    !["Ayarlar" simgesinin seçili olduğunu gösteren ekran görüntüsü ve "kimlik doğrulama" menüsünden "bağlantılar" seçilidir.](./media/segment-tutorial/segment1.PNG)

1. **Yeni bağlantı ekle**' ye tıklayın.

    !["Yeni bağlantı ekle" düğmesinin seçili olduğu "bağlantılar" bölümünü gösteren ekran görüntüsü.](./media/segment-tutorial/segment2.PNG)

1. Yapılandırma bağlantısı olarak **SAML 2,0** ' i seçin ve **Bağlantı Seç** düğmesine tıklayın.

    !["Bağlantı Seç" bölümünün "S A M L 2,0" ve "bağlantı Seç" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/segment-tutorial/segment3.PNG)

1. Aşağıdaki sayfada aşağıdaki adımları gerçekleştirin:

    !["Tek Sign-On U R L" ve "Izleyici U R L" metin kutuları vurgulanmış ve "Ileri" düğmesi seçili olan "kimlik sağlayıcısını Yapılandır" sayfasını gösteren ekran görüntüsü.](./media/segment-tutorial/segment4.PNG)

    a. **Tek Sign-On URL** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** iletişim kutusundaki **yanıt URL 'si** kutusuna yapıştırın.

    b. * * * * Hedef kitle URL 'SI * * * * değerini kopyalayın ve Azure portal **temel SAML yapılandırması** Iletişim KUTUSUNDAKI **tanımlayıcı URL** kutusuna yapıştırın.

    c. **İleri**' ye tıklayın.

    ![Segment yapılandırması](./media/segment-tutorial/segment5.PNG)

1. **SAML 2,0 ENDPOINT URL** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

1. İndirilen sertifika **(base64)** Azure Portal Not defteri ' nden açın ve Içeriği **ortak sertifika** metin kutusuna yapıştırın.

1. **Bağlantıyı Yapılandır** seçeneğine tıklayın.

### <a name="create-segment-test-user"></a>Segment test kullanıcısı oluştur

Bu bölümde, kesiminde B. Simon adlı bir Kullanıcı oluşturulur. Segment, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten kesimde yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde segment kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız kesimde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile segmenti deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle segmenti koruma](/cloud-app-security/proxy-intro-aad)