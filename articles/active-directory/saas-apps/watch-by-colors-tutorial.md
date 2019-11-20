---
title: 'Öğretici: renklerle Izle ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Renklerle Azure Active Directory ve Izleme arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8fdc8ba2337a3be49a4645e48a45120fb0ccec
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026098"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Öğretici: renklerle Izleme ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Izlemeyi renklerle nasıl tümleştirileceğini öğreneceksiniz. Izlemeyi renklerle Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, renklere göre Izleme erişimine sahip olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla renkler ile Izlemek için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Renklerle tek oturum açma (SSO) özellikli aboneliğe göre izleyin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Renklere göre izleme **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-watch-by-colors-from-the-gallery"></a>Galeriden renklere göre Izleme ekleme

Watch 'un renkler tarafından Azure AD 'ye tümleştirilmesini yapılandırmak için, galerisindeki renkleri, yönetilen SaaS uygulamaları listenize göre Izle eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **renklere göre izle** yazın.
1. Sonuçlar panelinden **renklere göre izle** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Renklere göre Izleme için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu, renklerle izleme ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Watch 'da ilgili Kullanıcı arasında renklerle bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu, Izleme renkleriyle birlikte yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Renkleri, uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[renklere göre Izle SSO 'Yu yapılandırın](#configure-watch-by-colors-sso)** .
    1. Kullanıcıların Azure AD gösterimine bağlı olan renklere göre Watch 'da B. Simon 'a göre izleme **[oluşturun](#create-watch-by-colors-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **renklerle izle** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama **IDP** tarafından başlatılan modda önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusuna bir URL yazın: `https://app.colorscorporation.com/login`

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, renklere göre Izleme erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **renklere göre izle**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-watch-by-colors-sso"></a>Renklere göre gözcü yapılandırma SSO 'SU

1. Renklere göre Izleme içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek**üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **kuruluma göre izleme** ' ye tıklayın, sizi renklere göre izle uygulamasına yönlendirir. Buradan, renklerle Izlemek için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-5 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Izlemeyi renklerle el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve renkler şirket sitesini yönetici olarak Izleyin ve aşağıdaki adımları gerçekleştirin:

4. Sayfanın sağ üst köşesinde, **profil** > **Hesap ayarları** > **SSO (çoklu oturum açma)** seçeneğine tıklayın.

    ![Renklerin yapılandırmasına göre izleme](./media/watch-by-colors-tutorial/config01.png)

5. **SSO (çoklu oturum açma)** sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Renklerin yapılandırmasına göre izleme](./media/watch-by-colors-tutorial/config02.png)

    a. **SAML etkinleştirme** 'yi **Açık**olarak değiştirin.

    b. **URL** metin kutusunda, Azure Portal kopyaladığınız **Federasyon meta veri URL 'sini**yapıştırın.

    c. **Içeri aktar**' a tıklayın, ardından aşağıdaki alanlar sayfada otomatik olarak doldurulur.

    d. **Kaydet** düğmesine tıklayın.

### <a name="create-watch-by-colors-test-user"></a>Renklere göre Izleme test kullanıcısı oluştur

Azure AD kullanıcılarının renklerle Izlemek üzere oturum açmasını sağlamak için, renkler tarafından Watch 'a sağlanması gerekir. Renklerle görüntüleme bölümünde, sağlama işlemi el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Renklerle bir güvenlik yöneticisi olarak Izlemek için oturum açın.

1. Sayfanın sağ üst köşesinde, **profil** > **kullanıcıları** > **Kullanıcı Ekle**' ye tıklayın.

    ![Renklerin yapılandırmasına göre izleme](./media/watch-by-colors-tutorial/config03.png)

1. **Kullanıcı ayrıntıları** sayfasında, aşağıdaki adımları uygulayın:

    ![Renklerin yapılandırmasına göre izleme](./media/watch-by-colors-tutorial/config04.png)

    a. **Ad** metin kutusuna **B**gibi kullanıcının adını girin.

    b. **Soyadı** metin kutusuna, **Simon**gibi kullanıcı adının soyadını girin.

    c. **E-posta** metin kutusuna `B.Simon@contoso.com` gibi kullanıcının e-postasını girin.

    d. **Parola** metin kutusuna parolayı girin.

    e. Kuruluşunuza göre **hesap izinlerini** seçin.

    f. **Kaydet** düğmesine tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde renklere göre Izle kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız renklere göre Izlemek için otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile renklerle Izlemeyi deneyin](https://aad.portal.azure.com/)

