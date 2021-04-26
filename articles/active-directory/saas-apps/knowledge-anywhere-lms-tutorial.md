---
title: 'Öğretici: her yerde LMS ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve her yerde LMS arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.openlocfilehash: eab1304408f896f5c9d736488997c1246ae167f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458993"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Öğretici: Azure Active Directory her yerde LMS 'yi tümleştirin

Bu öğreticide, Azure Active Directory (Azure AD) ile bilgi her yerde LMS 'yi tümleştirmeyi öğreneceksiniz. Azure AD ile bilgi her yerde LMS 'yi tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, her yerde LMS bilgisine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla ilgili her yerde LMS için otomatik olarak oturum açmaya olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Her yerde LMS çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Her yerde LMS, **SP** tarafından başlatılan SSO 'yu destekler ve **yalnızca zamanında** Kullanıcı sağlamayı destekler.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Galeriden her yerden bilgi ekleme galerisinden

Azure AD 'de herhangi bir yerde bilginin tümleştirilmesini yapılandırmak için, Galeriden her yerde LMS 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **herhangi bir yerde bilgi** yazın.
1. Sonuçlar panelinden **her yerde bilgi** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu her yerde bilinen bilgi ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında, her yerde LMS 'de bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu, her yerde bir LMS ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[her yerde Bilgi Bankası yapılandırın](#configure-knowledge-anywhere-lms)** .
3. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimi ile bağlantılı her yerde, Bilgi Bankası 'nda her yerde her yerde bir bilgi sahibi olmak üzere **[her yerde LMS test kullanıcısı oluşturun](#create-knowledge-anywhere-lms-test-user)** .
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **her yerde LMS** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    1. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, Öğreticinin ilerleyen kısımlarında açıklanan gerçek tanımlayıcı ve yanıt URL 'siyle güncelleştirin.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Bu değeri, gerçek oturum açma URL 'siyle güncelleştirin. Bu değeri almak için [her yerde LMS istemci desteği ekibinin bilgi bilgisine](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) ulaşın. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Bilgi Bankası her yerde ayarla LMS** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Her yerde bilgi yapılandırın LMS

1. Her yerde bilgi olan LMS 'de yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımın güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, kurulum hakkında ' ya tıklayın **her** yerde LMS uygulaması hakkında bilgi sahibi olun. Buradan, her yerde LMS 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Her yerde LMS 'yi el ile ayarlamak isterseniz, yeni bir Web tarayıcısı penceresi açın ve bilginiz içinde her yerde LMS şirket sitesinde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

4. **Site** sekmesinde öğesini seçin.

    ![Ekran görüntüsü site sekmesini gösterir.](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. **SAML ayarları** sekmesinde öğesini seçin.

    ![Ekran görüntüsünde, SAML ayarlarının seçildiği her yerde bilgi görüntülenir.](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. **Yeni Ekle**' ye tıklayın.

    ![Ekran görüntüsünde, hizmet sağlayıcı ayarları ' nda yeni Ekle düğmesi gösterilir.](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. **SAML ayarlarını Ekle/güncelleştir** sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, burada açıklanan değişiklikleri yapabileceğiniz SAML ayarlarını Ekle/güncelleştir sayfasını gösterir.](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. IDP adını kuruluşunuza göre girin. For Ex:- `Azure` .

    b. **IDP VARLıK kimliği** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. **IDP URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. İndirilen sertifika dosyasını Azure portal Not defteri 'nde açın, sertifikanın içeriğini kopyalayın ve **sertifikayı sertifika** metin kutusuna yapıştırın.

    e. **Oturum kapatma URL** 'si metin kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    f. **Etki alanının** açılan listesinden **ana site** ' yi seçin.

    örneğin: **SP VARLıK kimliği** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı** metin kutusuna yapıştırın.

    h. **SP yanıtı (ACS) URL 'si** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümünde **yanıt URL 'si** metin kutusuna yapıştırın.

    i. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B. Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, her yerde bilgi LMS 'ye erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **her yerde BILGI LMS**'yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Her yerde LMS test kullanıcısı oluşturma

Bu bölümde, her yerde Bilgi Bankası 'nda B. Simon adlı bir Kullanıcı oluşturulur. Bilgi her yerde LMS, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı bilgisi her yerde LMS 'de mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

### <a name="test-sso"></a>Test SSO 'SU

Erişim paneli 'nde her yerde LMS kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız her yerde LMS 'de otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)