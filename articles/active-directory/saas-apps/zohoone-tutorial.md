---
title: 'Öğretici: Zoho One ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Zoho One arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 12ac4d9fbf30873f0392a6d767d7568129bad112
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650660"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Öğretici: Zoho One ile tümleştirme Azure Active Directory

Bu öğreticide, Zoho 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Zoho 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Zoho One erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla bir tane için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Zoho One ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Tek bir oturum açma etkin aboneliğini Zoho.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Zoho One, **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-zoho-one-from-the-gallery"></a>Galeriden bir tane Zoho ekleyin

Zoho 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden bir tane Zoho 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Zoho One** yazın.
1. Sonuçlar panelinden **Zoho One** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Zoho One için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak, Zoho One Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Zoho One içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Zoho One ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Zoho One SSO yapılandırma](#configure-zoho-one-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Zoho **[One test kullanıcısı oluşturun](#create-zoho-one-test-user)** -kullanıcının Azure AD gösterimine bağlı olan, Zoho 'da B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Zoho One** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Tanımlayıcı** metin kutusuna URL 'yi yazın:`one.zoho.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Önceki **yanıt URL 'si** değeri gerçek değil. `<saml-identifier>`#Step4, daha sonra Öğreticinin ilerleyen kısımlarında açıklanan, **Zoho yapılandırma bir çoklu oturum açma** bölümünün değerden yararlanacaktır.

    c. **Ek URL 'Ler ayarla**' ya tıklayın.

    d. **Geçiş durumu** metin kutusuna URL 'yi yazın:`https://one.zoho.com`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımı uygulayın:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Önceki **oturum açma URL 'si** değeri gerçek değil. Değeri, Öğreticinin ilerleyen kısımlarında açıklanan **bir çoklu oturum açma yapılandırma** bölümünün gerçek Sign-On URL 'siyle güncellecektir. 

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **Zoho 'Yi ayarlama** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Zoho 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Zoho One**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-zoho-one-sso"></a>Zoho One SSO yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Zoho One şirket sitenizde yönetici olarak oturum açın.

2. **Kuruluş** sekmesinde, **SAML kimlik doğrulaması** altında **Kurulum** ' a tıklayın.

    ![Zoho One org](./media/zoho-one-tutorial/set-up.png)

3. Açılır sayfada aşağıdaki adımları uygulayın:

    ![Zoho bir SIG](./media/zoho-one-tutorial/save.png)

    a. Oturum açma **URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    b. Oturum kapatma **URL 'si** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    c. Azure portal indirdiğiniz **sertifikayı (base64)** karşıya yüklemek Için, **Gözden** geçirme ' ye tıklayın.

    d. **Kaydet**’e tıklayın.

4. SAML kimlik doğrulaması kurulumunu kaydettikten sonra, **SAML tanımlayıcı** değerini kopyalayın ve ' nin yerine **Yanıt** URL 'sini ekleyin `<saml-identifier>` `https://accounts.zoho.com/samlresponse/one.zoho.com` ve oluşturulan değeri **temel SAML yapılandırması** bölümünün altındaki yanıt **URL 'si** metin kutusuna yapıştırın.

    ![Zoho bir SAML](./media/zoho-one-tutorial/saml-identifier.png)

5. Etki **alanları** sekmesine gidin ve ardından **etki alanı Ekle**' ye tıklayın.

    ![Bir etki alanı Zoho](./media/zoho-one-tutorial/add-domain.png)

6. **Etki alanı Ekle** sayfasında, aşağıdaki adımları uygulayın:

    ![Zoho One etki alanı Ekle](./media/zoho-one-tutorial/add-domain-name.png)

    a. **Etki alanı adı** metin kutusuna contoso.com gibi bir etki alanı yazın.

    b. **Ekle**'ye tıklayın.

    >[!Note]
    >Etki alanını ekledikten sonra, etki alanınızı doğrulamak için [aşağıdaki](https://www.zoho.com/one/help/admin-guide/domain-verification.html) adımları izleyin. Etki alanı doğrulandıktan sonra, Azure portal ' deki **temel SAML yapılandırması** bölümünde **oturum açma URL 'si** içinde etki alanı adınızı kullanın.

### <a name="create-zoho-one-test-user"></a>Zoho One test kullanıcısı oluşturma

Azure AD kullanıcılarının, Zoho 'da oturum açmasını sağlamak için, bunların Zoho One 'a sağlanması gerekir. Zoho One 'da, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Bir güvenlik yöneticisi olarak Zoho 'da oturum açın.

2. **Kullanıcılar** sekmesinde **Kullanıcı logosu**' na tıklayın.

    ![Bir Kullanıcı Zoho](./media/zoho-one-tutorial/user.png)

3. **Kullanıcı Ekle** sayfasında, aşağıdaki adımları uygulayın:

    ![Zoho One Kullanıcı Ekle](./media/zoho-one-tutorial/add-user.png)
    
    a. **Ad** metin kutusuna, **Britta Simon** gibi kullanıcının adını girin.
    
    b. **E-posta adresi** metin kutusuna kullanıcının e-postasını girin brittasimon@contoso.com .

    >[!Note]
    >Etki alanı listesinden doğrulanmış etki alanınızı seçin.

    c. **Ekle**'ye tıklayın.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Zoho One oturum açma URL 'sine yönlendirecektir.  

* Zoho One oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Zoho 'da otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda bir kutucuğa tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Zoho 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Zoho 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).