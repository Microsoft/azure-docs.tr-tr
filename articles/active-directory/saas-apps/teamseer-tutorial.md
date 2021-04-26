---
title: 'Öğretici: TeamSeer ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve TeamSeer arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 6085ba5091b2b9973354280175aeb01f93ad7e28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521178"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Öğretici: TeamSeer ile tümleştirme Azure Active Directory

Bu öğreticide ekip oluşturma 'yı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Ekip Seer 'ı Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Ekip Seer 'a erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla ekip Seer (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini TeamSeer ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* TeamSeer çoklu oturum açma etkin abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* TeamSeer, **SP** tarafından başlatılan SSO 'yu destekliyor

## <a name="adding-teamseer-from-the-gallery"></a>Galeriden ekip kümesi ekleme

TeamSeer 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden ekip Seer 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden ekip kümesi eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **TeamSeer** yazın, sonuç panelinden **TeamSeer** ' ı seçin, sonra da uygulamayı eklemek için düğme **Ekle** ' ye tıklayın.

     ![Sonuç listesindeki ekip Seer](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre TeamSeer ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve ekipte ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Ekip oluşturma ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[Ekip kümesi çoklu oturum açmayı yapılandırma](#configure-teamseer-single-sign-on)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[TeamSeer test kullanıcısı oluşturun](#create-teamseer-test-user)** -Kullanıcı IÇIN Azure AD gösterimine bağlı olan, ekip ortamında Britta Simon 'a sahip olmak.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

TeamSeer ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **TeamSeer** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![TeamSeer etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-signonurl.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [TeamSeer istemci destek ekibine](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Ekip kümesini ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure AD tanımlayıcısı

    c. Oturum kapatma URL 'SI

### <a name="configure-teamseer-single-sign-on&quot;></a>TeamSeer tek Sign-On yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak TeamSeer şirket sitenizde oturum açın.

1. **HR Yöneticisi**' ne gidin.

    ![Ekran görüntüsü, TeamSeer penceresinden seçilen H R yöneticisini gösterir.](./media/teamseer-tutorial/ic789634.png &quot;IK yönetici")

1. **Kurulum**'a tıklayın.

    ![Kurulum](./media/teamseer-tutorial/ic789635.png "Kurulum")

1. **SAML sağlayıcısı ayrıntılarını ayarla**' ya tıklayın.

    ![Ekran görüntüsü SAML sağlayıcısı ayrıntılarının seçili olduğunu gösterir.](./media/teamseer-tutorial/ic789636.png "SAML ayarları")

1. SAML sağlayıcı ayrıntıları bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz SAML sağlayıcısı ayrıntılarını gösterir.](./media/teamseer-tutorial/ic789637.png "SAML ayarları")

    a. **URL** metin kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. Base-64 kodlu sertifikanızı Not defteri 'nde açın, içindeki içeriğini panonuza kopyalayın ve **IDP ortak sertifika** metin kutusuna yapıştırın.

1. SAML sağlayıcısı yapılandırmasını tamamlamak için aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz SAML sağlayıcısı yapılandırmasını gösterir.](./media/teamseer-tutorial/ic789638.png "SAML ayarları")

    a. **Sınama e-posta adresleri**' nde, test kullanıcısının e-posta adresini yazın.
  
    b. **Veren** metin kutusuna hizmet sağlayıcının veren URL 'sini yazın.
  
    c. **Kaydet**’e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon** girin.
  
    b. **Kullanıcı adı** alan türü **brittasimon@yourcompanydomain.extension**  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, ekip uygulamasına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **TeamSeer**' ı seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **TeamSeer**' ı seçin.

    ![Uygulamalar listesindeki TeamSeer bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-teamseer-test-user&quot;></a>TeamSeer test kullanıcısı oluştur

Azure AD kullanıcılarının TeamSeer 'da oturum açmasını sağlamak için, ShiftPlanning ' de sağlanması gerekir. TeamSeer söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **TeamSeer** şirket sitenizde yönetici olarak oturum açın.

1. **HR yönetici \> kullanıcıları** ' na gidin ve ardından **Yeni Kullanıcı Sihirbazı 'nı Çalıştır**' a tıklayın.

    ![Ekran görüntüsü, çalıştırılacak Sihirbazı seçebileceğiniz H R Yönetici sekmesini gösterir.](./media/teamseer-tutorial/ic789640.png &quot;IK yönetici")

1. **Kullanıcı ayrıntıları** bölümünde aşağıdaki adımları uygulayın:

    ![Kullanıcı ayrıntıları](./media/teamseer-tutorial/ic789641.png "Kullanıcı ayrıntıları")

    a. İlgili metin kutularına sağlamak istediğiniz geçerli bir Azure AD hesabının **adı**, **Soyadı**, **Kullanıcı adı (e-posta adresi)** yazın.
  
    b. **İleri**’ye tıklayın.

1. Yeni bir kullanıcı eklemek için ekrandaki yönergeleri izleyin ve **son**' a tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için TeamSeer tarafından sunulan diğer bir TeamSeer Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde TeamSeer kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız ekipte otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)