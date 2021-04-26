---
title: 'Öğretici: StatusPage ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve StatusPage arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: 6bc2508220a8c0193efb55eb6cd11f7e151c80be
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284600"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Öğretici: StatusPage ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, StatusPage 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. StatusPage 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de StatusPage 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla StatusPage 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini StatusPage ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* StatusPage çoklu oturum açma etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* StatusPage **IDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-statuspage-from-the-gallery"></a>Galeriden StatusPage ekleme

StatusPage 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden StatusPage 'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **StatusPage** yazın.
1. Sonuçlar panelinden **StatusPage** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>StatusPage için Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre StatusPage ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve StatusPage içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD SSO 'yu StatusPage ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[StatusPage SSO 'Yu yapılandırın](#configure-statuspage-sso)** .
    1. StatusPage **[test kullanıcısı oluşturun](#create-statuspage-test-user)** ve kullanıcının Azure AD gösterimine bağlı olarak, StatusPage 'de Britta Simon 'un bir karşılığı vardır.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Askyourteam** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **SAML Ile tek Sign-On ayarlama** sayfasında, aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Tanımlayıcı |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

     | Yanıt URL'si |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)Çoklu oturum açmayı yapılandırmak için gereken meta verileri istemek amacıyla ' de StatusPage destek ekibine başvurun. 
    >
    > a. Meta verilerden, veren değerini kopyalayın ve sonra **tanımlayıcı** metin kutusuna yapıştırın.
    >
    > b. Meta verilerden, yanıt URL 'sini kopyalayın ve ardından **yanıt URL 'si** metin kutusuna yapıştırın.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **StatusPage ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, StatusPage 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon özelliğini etkinleştirirsiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **StatusPage**' i seçin.

2. Uygulamalar listesinde **StatusPage**' i seçin.

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-statuspage-sso"></a>StatusPage SSO 'yu yapılandırma

1. Durumu StatusPage içinde otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, durumu **Ayarla** ' ya tıklayın, sizi StatusPage uygulamasına yönlendirir. Buradan, StatusPage 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. StatusPage 'i el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, StatusPage şirket sitenizde yönetici olarak oturum açın.

1. Ana araç çubuğunda **Hesabı Yönet**' e tıklayın.

    ![Ekran görüntüsü, StatusPage şirket sitesinden seçilen hesabı Yönet ' i gösterir.](./media/statuspage-tutorial/account.png)

1. **Çoklu oturum açma** sekmesine tıklayın.

    ![Ekran görüntüsü çoklu oturum açma sekmesini gösterir.](./media/statuspage-tutorial/tab.png)

1. SSO kurulum sayfasında, aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, tanımlanan değerleri girebileceğiniz S S O kurulum sayfasını gösterir.](./media/statuspage-tutorial/setup.png)

    ![Ekran görüntüsünde yapılandırmayı Kaydet düğmesi gösterilir.](./media/statuspage-tutorial/configuration.png)

    a. **SSO hedef URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. İndirilen sertifikanızı Not defteri 'nde açın, içeriği kopyalayın ve sonra **sertifika** metin kutusuna yapıştırın.

    c. **Yapılandırmayı kaydet**' e tıklayın.

### <a name="create-statuspage-test-user"></a>StatusPage test kullanıcısı oluştur

Bu bölümün amacı, StatusPage 'de Britta Simon adlı bir Kullanıcı oluşturmaktır.

StatusPage tam zamanında sağlamayı destekler. Bu özelliği [Azure AD çoklu oturum açma yapılandırması](#configure-azure-ad-sso)' nda zaten etkinleştirdiniz.

**StatusPage 'de Britta Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

1. StatusPage şirket sitenizde yönetici olarak oturum açın.

1. Üstteki menüde **Hesabı Yönet**' e tıklayın.

    ![Ekran görüntüsü, StatusPage şirket sitesinden seçilen hesabı Yönet ' i gösterir.](./media/statuspage-tutorial/account.png)

1. **Ekip üyeleri** sekmesine tıklayın.
  
    ![Ekran görüntüsü takım üyeleri sekmesini gösterir.](./media/statuspage-tutorial/sandbox.png) 

1. **Takım üyesi ekle**' ye tıklayın.
  
    ![Ekran görüntüsü takım üyesi ekle düğmesini gösterir.](./media/statuspage-tutorial/team.png) 

1. İlgili metin kutularına sağlamak istediğiniz geçerli bir kullanıcının **e-posta adresini**, **adını** ve **soyadını** yazın. 

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı Ekle iletişim kutusunu gösterir.](./media/statuspage-tutorial/user.png) 

1. **Rol** olarak **İstemci Yöneticisi**' ni seçin.

1. **Hesap oluştur**' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız StatusPage içinde otomatik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki StatusPage kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız StatusPage öğesinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

StatusPage 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).