---
title: 'Öğretici: Workfront ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Workfront arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: jeedes
ms.openlocfilehash: cebfca387f1a7dc34f70085966f5b6e8e212f953
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478481"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Öğretici: Iş önden Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile Iş ön 'u tümleştirmeyi öğreneceksiniz. Iş önünü Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Workfront erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Iş önünde yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* İş ön çoklu oturum açma etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Workfront, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-workfront-from-the-gallery"></a>Galeriden Iş önüne ekleme

Azure AD 'de Workfront tümleştirmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Iş önüne ilk eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **workfront** yazın.
1. Sonuçlar panelinden **Workfront** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-workfront"></a>Azure AD SSO 'yu Workfront için yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu workfront ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve iş için ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Iş önünde yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Workfront SSO 'Yu yapılandırma](#configure-workfront-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. İş **[ön test kullanıcısı oluşturun](#create-workfront-test-user)** -kullanıcının Azure AD gösterimine bağlı olan, Iş için B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **çalışma** uygulaması tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.attask-ondemand.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [Workfront Client destek ekibine](https://www.workfront.com/services-and-support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Workfront ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Iş için erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Workfront**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-workfront-sso"></a>Workfront SSO 'yu yapılandırma

1. Workfront şirket sitenizde yönetici olarak oturum açın.

2. **Çoklu oturum açma yapılandırması '** na gidin.

3. **Çoklu oturum açma** iletişim kutusunda aşağıdaki adımları gerçekleştirin
    
    ![Tek Sign-On yapılandırma](./media/workfront-tutorial/single-sign-on.png)
   
    a. **Tür** olarak **SAML 2,0**' ı seçin.
   
    b. **Hizmet sağlayıcı kimliği**' ni seçin.
   
    c. Oturum açma **URL** 'Sini **login Portal URL** metin kutusuna yapıştırın.
   
    d. **Logout URL** 'Sini oturum kapatma **URL 'si** kutusuna yapıştırın.
   
    e. Parola değiştirme URL 'sini **Değiştir** metin **kutusuna yapıştırın.**
   
    f. **Kaydet**’e tıklayın.

### <a name="create-workfront-test-user"></a>Iş ön test kullanıcısı oluşturma

Bu bölümün amacı, Iş başında Britta Simon adlı bir Kullanıcı oluşturmaktır.

**Iş önünde Britta Simon adlı bir kullanıcı oluşturmak için aşağıdaki adımları uygulayın:**

1. Workfront şirket sitenizde yönetici olarak oturum açın.
 
2. Üstteki menüde **kişiler**' e tıklayın.
 
3. **Yeni kişi**' ye tıklayın. 

4. Yeni kişi iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Bir Iş ön test kullanıcısı oluşturma](./media/workfront-tutorial/add-person.png)
   
    a. **Ilk ad** metin kutusuna "Britta" yazın.
   
    b. **Soyadı** metin kutusuna "Simon" yazın.
   
    c. **E-posta adresi** metin kutusuna, Azure Active Directory alanına Britta Simon 'un e-posta adresini yazın.
   
    d. **Kişi ekle**' ye tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Workfront oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Workfront oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Workfront kutucuğuna tıkladığınızda bu işlem, Workfront oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Çalışma işini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
