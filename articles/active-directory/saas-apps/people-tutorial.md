---
title: 'Öğretici: kişilerle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve kişiler arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 43edf62a625d80de0a5de8e0924e771d68a595e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647124"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Öğretici: kişilerle çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, kişileri Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Kullanıcıları Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de kişilere erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarına sahip kişiler için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Kişilerin çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* İnsanlar **SP** tarafından başlatılan SSO 'yu destekler
* Kişiler mobil uygulaması, artık SSO 'yu etkinleştirmek için Azure AD ile yapılandırılabilir. Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

>[!NOTE]
>Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-people-from-the-gallery"></a>Galeriden kişi ekleme

Kişilerin Azure AD ile tümleştirilmesini yapılandırmak için galerideki kişileri yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **kişiler** yazın.
1. Sonuçlar panelinden **kişiler** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-people"></a>Kişiler için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu, kişilerle birlikte yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve kişiler 'de ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu kişilerle birlikte yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
2. **[Kişileri yapılandırma SSO](#configure-people-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan kişilerde B. Simon 'a sahip olmak için **[kişi test kullanıcısı oluşturun](#create-people-test-user)** .
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **kişiler** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.peoplehr.net`

    b. **Tanımlayıcı** kutusuna URL 'yi yazın:`https://www.peoplehr.com`

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [kişi istemci destek ekibine](mailto:customerservices@peoplehr.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Kişileri ayarlama** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, kişilere erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **kişiler**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-people-sso"></a>Kişileri yapılandırma SSO 'SU

1. Yapılandırmaları kişiler içinde otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalarımı güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **Kurulum** ' a tıklayarak kişileri Kişiler uygulamasına yönlendirirsiniz. Buradan, kişilerin oturum açmasını sağlamak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Kişileri el ile ayarlamak istiyorsanız yeni bir Web tarayıcı penceresi açın ve kişiler Şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:
   
4. Sol taraftaki menüde, **Ayarlar**' a tıklayın.

    !["Ayarlar" seçiliyken sol taraftaki menüyü gösteren ekran görüntüsü.](./media/people-tutorial/settings.png)

5. **Şirket**' e tıklayın.

    !["Ayarlar" menüsünden Seçili "Şirket" i gösteren ekran görüntüsü.](./media/people-tutorial/company.png)

6. **' Çoklu oturum açma ' SAML meta veri dosyasında karşıya yükle** **' ye tıklayarak indirilen** meta veri dosyasını karşıya yükleyin.

    ![Tek Sign-On yapılandırma](./media/people-tutorial/xml.png)

### <a name="create-people-test-user"></a>Kişi testi kullanıcısı oluştur

Bu bölümde, kişiler 'de B. Simon adlı bir Kullanıcı oluşturacaksınız. Kullanıcıları kişiler platformunda eklemek için [kişi istemci destek ekibi](mailto:customerservices@peoplehr.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz kişilerin oturum açma URL 'sine yönlendirecektir. 

* Kişiler oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım kutucuğunda tıklattığınızda, bu, kişilerin oturum açma URL 'sine yönlendirecektir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-people-mobile"></a>Kişiler için test SSO 'SU (mobil)

1. Kişiler mobil uygulamasını açın. Oturum aç sayfasında, **e-posta kimliğini** girin ve **Çoklu oturum açma**' ya tıklayın.

    ![Oturum açma](./media/people-tutorial/test01.png)

2. **Kuruluş Kullanıcı kimliği** ' ni girip **İleri**' ye tıklayın.

    ![E-posta](./media/people-tutorial/test02.png)

3. Son olarak, başarıyla oturum açtıktan sonra, uygulama giriş sayfası aşağıda gösterildiği gibi görüntülenir:

    ![Bir kez](./media/people-tutorial/test03.png)

## <a name="next-steps"></a>Sonraki adımlar

Kullanıcıları yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).