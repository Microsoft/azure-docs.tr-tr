---
title: 'Öğretici: Dropbox Business ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Dropbox Iş arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 41f6db8cf2454c224addac525e9d039954a95712
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601508"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Öğretici: Dropbox Işletmelerini Azure Active Directory ile tümleştirme

Bu öğreticide Dropbox Işletmelerini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Dropbox Business 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Dropbox Iş 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Dropbox Iş 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Dropbox Iş çoklu oturum açma (SSO) etkin aboneliği.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

* Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Dropbox Business, **SP** tarafından başlatılan SSO 'yu destekler.

* Dropbox Işi [Otomatik Kullanıcı sağlamayı ve sağlamayı kaldırmayı](dropboxforbusiness-tutorial.md)destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-dropbox-business-from-the-gallery"></a>Galeriden Dropbox Işi ekleme

Dropbox Business 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden Dropbox Business 'ı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Dropbox Business** yazın.
1. Sonuçlar panelinden **Dropbox Business** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-dropbox-business"></a>Dropbox Business için Azure AD SSO 'yu yapılandırma ve test etme

**Britta Simon** adlı bir test kullanıcısı kullanarak Dropbox Business Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile Dropbox Işlerinde ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Dropbox Işletmeyle yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.    
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
1. **[Dropbox BUSINESS SSO 'Yu yapılandırma](#configure-dropbox-business-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    1. Dropbox Business **[test kullanıcısı oluşturun](#create-dropbox-business-test-user)** -Dropbox Business 'Ta kullanıcının Azure AD gösterimine bağlanmış bir Britta Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Dropbox iş** uygulaması tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://www.dropbox.com/sso/<id>`
    
     b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna şu değeri yazın:`Dropbox`
    
    > [!NOTE]
    > Dropbox **IŞARETI SSO kimliği** , Dropbox sitesindeki Dropbox sitesinde bulunabilir > yönetici konsolu > ayarlar > çoklu oturum açma > SSO oturum açma URL 'si.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **Dropbox Iş ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Dropbox Business 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Dropbox Business**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-dropbox-business-sso"></a>Dropbox Iş SSO 'SU yapılandırma

1. Dropbox Business içindeki yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemek** üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **Kurulum Dropbox Business** 'a tıklayarak sizi Dropbox iş uygulamasına yönlendirebilirsiniz. Buradan, Dropbox Business 'ta oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-8 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Dropbox Işlerinizi el ile ayarlamak istiyorsanız, yeni bir Web tarayıcısı penceresi açın ve Dropbox iş kiracınıza gidin ve Dropbox Iş kiracınızda oturum açın. ve aşağıdaki adımları gerçekleştirin:

    !["Dropbox Iş oturum açma&quot; sayfasını gösteren ekran görüntüsü.](./media/dropboxforbusiness-tutorial/account.png &quot;Çoklu oturum açmayı yapılandırma")

4. **Kullanıcı simgesine** tıklayın ve **Ayarlar** sekmesini seçin.

    !["Kullanıcı SIMGESI" eylemini ve "ayarları" seçili olduğunu gösteren ekran görüntüsü.](./media/dropboxforbusiness-tutorial/configure-1.png "Çoklu oturum açmayı yapılandırma")

5. Sol taraftaki Gezinti bölmesinde, **Yönetim Konsolu**' na tıklayın.

    !["Yönetici Konsolu" nin seçili olduğunu gösteren ekran görüntüsü.](./media/dropboxforbusiness-tutorial/configure-2.png "Çoklu oturum açmayı yapılandırma")

6. **Yönetim konsolunda**, sol gezinti bölmesinde **Ayarlar** ' a tıklayın.

    !["Ayarlar" ın seçili olduğunu gösteren ekran görüntüsü.](./media/dropboxforbusiness-tutorial/configure-3.png "Çoklu oturum açmayı yapılandırma")

7. **Kimlik doğrulama** bölümünde **Çoklu oturum açma** seçeneğini belirleyin.

    !["Çoklu oturum açma" seçiliyken "kimlik doğrulama" bölümünü gösteren ekran görüntüsü.](./media/dropboxforbusiness-tutorial/configure-4.png "Çoklu oturum açmayı yapılandırma")

8. **Çoklu oturum açma** bölümünde aşağıdaki adımları gerçekleştirin:  

    !["Çoklu oturum açma" yapılandırma ayarlarını gösteren ekran görüntüsü.](./media/dropboxforbusiness-tutorial/configure-5.png "Çoklu oturum açmayı yapılandırma")

    a. **Çoklu oturum açma** için açılan listeden bir seçenek olarak **gerekli** ' ı seçin.

    b. **Oturum açma URL 'Si Ekle** ' ye tıklayın ve **kimlik sağlayıcısı oturum açma URL 'si** metin kutusuna, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın ve **bitti**' yi seçin.

    ![Çoklu oturum açmayı yapılandırma](./media/dropboxforbusiness-tutorial/sso.png "Çoklu oturum açmayı yapılandırma")

    c. **Sertifikayı karşıya yükle**' ye tıklayın ve ardından Azure Portal indirdiğiniz **Base64 kodlamalı sertifika dosyanıza** gidin.

    d. **Kopyala bağlantısı** ' na tıklayın ve kopyalanmış değeri Azure Portal **Dropbox iş etki alanı ve URL 'Leri** bölümünün **oturum açma URL 'si** metin kutusuna yapıştırın.

    e. **Kaydet**’e tıklayın.

### <a name="create-dropbox-business-test-user"></a>Dropbox Iş testi kullanıcısı oluşturma

Bu bölümde, Dropbox Business 'ta B. Simon adlı bir Kullanıcı oluşturulur. Dropbox Business, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Dropbox Business 'ta bir kullanıcı zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

>[!Note]
>Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [Dropbox Iş istemci destek ekibine](https://www.dropbox.com/business/contact) başvurun

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bunu yaptığınızda, oturum açma akışını başlatabileceğiniz Dropbox Iş oturum açma URL 'sine yeniden yönlendirilir. 

* Dropbox Iş oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Dropbox Iş kutucuğuna tıkladığınızda bu işlem Dropbox Iş oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Dropbox Işletmelerini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).