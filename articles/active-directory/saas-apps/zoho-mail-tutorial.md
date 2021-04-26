---
title: 'Öğretici: Zoho ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Zoho arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648382"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Öğretici: Zoho ile tümleştirme Azure Active Directory

Bu öğreticide, Zoho 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Zoho 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Zoho erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Zoho 'ye otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Zoho One ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Zoho çoklu oturum açma etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Zoho, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-zoho-from-the-gallery"></a>Galeriden Zoho ekleyin

Zoho 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Zoho eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zoho** yazın.
1. Sonuçlar panelinden **Zoho** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Zoho için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Zoho Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Zoho içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Zoho ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Zoho SSO 'Yu yapılandırma](#configure-zoho-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Zoho **[test kullanıcısı oluşturun](#create-zoho-test-user)** -kullanıcının Azure AD gösterimine bağlı olan, Zoho 'de B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zoho** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.zohomail.com`

    > [!NOTE]
    > Değer gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Değeri almak için [Zoho istemci destek ekibine](https://www.zoho.com/mail/contact.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Zoho ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD test kullanıcısı oluşturma 

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD test kullanıcısını atama

Bu bölümde, Zoho erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Zoho**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name=&quot;configure-zoho-sso&quot;></a>Zoho SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Zoho mail şirket sitenizde yönetici olarak oturum açın.

2. **Denetim Masası**'na gidin.
   
    ![Denetim Masası](./media/zoho-mail-tutorial/control-panel.png &quot;Denetim Masası")

3. **SAML kimlik doğrulaması** sekmesine tıklayın.
   
    ![SAML kimlik doğrulaması](./media/zoho-mail-tutorial/saml-authentication.png "SAML kimlik doğrulaması")

4. **SAML kimlik doğrulaması ayrıntıları** bölümünde aşağıdaki adımları uygulayın:
   
    ![SAML kimlik doğrulaması ayrıntıları](./media/zoho-mail-tutorial/details.png "SAML kimlik doğrulaması ayrıntıları")
   
    a. **Oturum açma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'sini** yapıştırın.
   
    b. **Logout URL** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'sini** yapıştırın.
   
    c. **Parolayı DEĞIŞTIR URL** metin kutusuna, Azure Portal kopyaladığınız **Parola Değiştir URL 'sini** yapıştırın.
       
    d. Not defteri 'nde Azure portal indirilen Base-64 kodlu sertifikanızı açın, bu içeriği panonuza kopyalayın ve sonra **PublicKey** metin kutusuna yapıştırın.
   
    e. **Algoritma** olarak **RSA**' yı seçin.
   
    f. **Tamam**'a tıklayın.

### <a name="create-zoho-test-user"></a>Zoho test kullanıcısı oluşturma

Azure AD kullanıcılarının, Zoho postasına oturum açmasını sağlamak için, Zoho mail 'e sağlanması gerekir. Zoho mail söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için, Zoho Mail tarafından sunulan diğer bir Zoho mail Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:

1. **Zoho mail** şirket sitenizde yönetici olarak oturum açın.

1. **Denetim Masası \> posta & docs**' a gidin.

1. **Kullanıcı ayrıntıları \> Kullanıcı Ekle**' ye gidin.
   
    ![Ekran görüntüsü, Zoho mail sitesini Kullanıcı ayrıntılarına ve seçili Kullanıcı Ekle ' ye gösterir.](./media/zoho-mail-tutorial/add-user-1.png "Kullanıcı Ekleme")

1. **Kullanıcı Ekle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Ekran görüntüsü, tanımlanan değerleri girebileceğiniz Kullanıcı Ekle iletişim kutusunu gösterir.](./media/zoho-mail-tutorial/add-user-2.png "Kullanıcı Ekleme")
   
    a. **Ad** metin kutusuna, ilk Kullanıcı adını **Britta** gibi yazın.

    b. **Soyadı** metin kutusunda, **Simon** gibi kullanıcı adının soyadını yazın.

    c. **E-posta kimliği** metin kutusuna, **brittasıon \@ contoso.com** gıbı kullanıcının e-posta kimliğini yazın.

    d. **Parola** metin kutusuna kullanıcının parolasını girin.
   
    e. **Tamam**'a tıklayın.  
      
    > [!NOTE]
    > Azure Active Directory hesap sahibi, hesabı etkin olmadan önce onaylamak için bağlantı içeren bir e-posta alır.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Zoho oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Zoho oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Zoho kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Zoho 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Zoho 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).