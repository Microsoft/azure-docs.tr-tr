---
title: 'Öğretici: Chargebee ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve şarj eden arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.openlocfilehash: 22753b80931956af6ce448cfee974ae746fff6e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456234"
---
# <a name="tutorial-integrate-chargebee-with-azure-active-directory"></a>Öğretici: Azure Active Directory ile Chargebee 'yi tümleştirin

Bu öğreticide, Azure Active Directory (Azure AD) ile Chargebee tümleştirmeyi öğreneceksiniz. Azure AD ile Chargebee 'i tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Chargebee 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte otomatik olarak oturum açmalarına izin vermek için etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Chargebee çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Chargebee **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-chargebee-from-the-gallery"></a>Galeriden Chargebee ekleme

Chargebee 'in Azure AD ile tümleştirilmesini yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize Chargebee eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **chargebee** yazın.
1. Sonuçlar panelinden **Chargebee** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-chargebee"></a>Azure AD çoklu oturum açma özelliğini, Chargebee için yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Chargebee ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bu kullanıcı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu, Chargebee ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Chargebee SSO 'Yu yapılandırın](#configure-chargebee-sso)** .
    1. Kullanıcı Azure AD gösterimine bağlı olan Chargebee 'de B. Simon 'a sahip olmak için, **[chargebee test kullanıcısı oluşturun](#create-chargebee-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **chargebee** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domainname>.chargebee.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://app.chargebee.com/saml/<domainname>/acs`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>` , hesabı talep ettikten sonra kullanıcının oluşturduğu etki alanının adıdır. Diğer bilgiler söz konusu olduğunda, [Chargebee istemci destek ekibine](mailto:support@chargebee.com)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Chargebee ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Chargebee 'e erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, geri **ödeme**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-chargebee-sso"></a>Chargebee SSO 'yu yapılandırma

1. Yeni bir Web tarayıcısı penceresi açın ve bir yönetici olarak Chargebee şirket sitenizde oturum açın.

4. Menünün sol tarafında **Ayarlar**  >  **güvenlik**  >  **Yönet**' e tıklayın.

    ![Ekran görüntüsü, ayarlar, güvenlik ve Yönet ' i içeren Chargebee şirket sitesini gösterir.](./media/chargebee-tutorial/config01.png)

5. **Çoklu oturum açma** açılır penceresinde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, SAML seçiliyken tek Sign-On iletişim kutusunu ve onaylama seçeneğini gösterir.](./media/chargebee-tutorial/config02.png)

    a. **SAML**'yi seçin.

    b. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. Base64 Ile kodlanmış sertifikayı not defteri 'nde açın, içeriğini kopyalayın ve **SAML sertifikası** metin kutusuna yapıştırın.

    d. **Onayla**'ya tıklayın.

### <a name="create-chargebee-test-user"></a>Chargebee test kullanıcısı oluşturma

Azure AD kullanıcılarını etkinleştirmek için, Chargebee 'de oturum açın. Chargebee 'de, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Farklı bir Web tarayıcısı penceresinde, bir güvenlik yöneticisi olarak Chargebee 'de oturum açın.

2. Menünün sol tarafında, **müşteriler** ' e tıklayın ve ardından **Yeni Müşteri Oluştur ' a** gidin.

    ![Ekran görüntüsü, müşteriler ile Chargebee sitesini gösterir ve yeni bir müşteri oluşturur.](./media/chargebee-tutorial/config03.png)

3. **Yeni müşteri** sayfasında, aşağıda gösterilen ilgili alanları doldurup Kullanıcı oluşturma Için **Müşteri Oluştur** ' a tıklayın.

    ![Ekran görüntüsü, müşteri bilgilerini girebileceğiniz yeni müşteri sayfasını gösterir.](./media/chargebee-tutorial/config04.png)

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde, bu kutucuğa tıkladığınızda, SSO 'yu ayarladığınız Chargebee 'te otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)