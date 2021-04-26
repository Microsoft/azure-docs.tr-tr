---
title: 'Öğretici: Marketo ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Marketo arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 433303bf0d51eff3bd3ab37726c9e98e8a766d25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686963"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Öğretici: Marketo ile tümleştirme Azure Active Directory

Bu öğreticide, Marketo 'yı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
Marketo 'nın Azure AD ile tümleştirilmesi aşağıdaki avantajları sağlar:

* Azure AD 'de Marketo 'ya erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Marketo 'nın (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Marketo ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Marketo çoklu oturum açma etkin aboneliği

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Marketo, **IDP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-marketo-from-the-gallery"></a>Galeriden Marketo ekleme

Marketo 'nın Azure AD 'ye tümleştirilmesini yapılandırmak için, galerisindeki Marketo 'yı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Marketo** yazın.
1. Sonuçlar panelinden **Marketo** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Marketo için Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına bağlı olan Marketo ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Marketo 'daki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı Marketo ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD SSO 'yu, Britta Simon ile test etmek için Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD **[Test kullanıcısına,](#assign-the-azure-ad-test-user)** Azure AD SSO 'yu kullanmak Için Britta Simon 'u etkinleştirmek üzere atayın.
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[MARKETO SSO 'Yu yapılandırın](#configure-marketo-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Marketo 'da Britta Simon 'a sahip olmak için **[Marketo test kullanıcısı oluşturun](#create-marketo-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Marketo** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna URL 'yi yazın:`https://saml.marketo.com/sp`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://login.marketo.com/saml/assertion/<munchkinid>`

    c. **Geçiş durumu** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'SI ve geçiş durumuyla güncelleştirin. Bu değerleri almak için [Marketo istemci desteği ekibine](https://investors.marketo.com/contactus.cfm) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. Marketo 'yı **Ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Marketo 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Marketo**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-marketo-sso"></a>Marketo SSO 'yu yapılandırma

1. Marketo 'daki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **set up Marketo** 'ya tıkladığınızda, sizi Marketo uygulamasına yönlendirirsiniz. Buradan, Marketo 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Marketo 'yı el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, Marketo şirket sitenizde yönetici olarak oturum açın.

1. Uygulamanızın daha hızlı bir KIMLIK almasını sağlamak için aşağıdaki eylemleri gerçekleştirin:
   
    a. Yönetici kimlik bilgilerini kullanarak Marketo uygulamasında oturum açın.
   
    b. Üst gezinti bölmesindeki **yönetici** düğmesine tıklayın.
   
    ![Tek Sign-On1 yapılandırma](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Tümleştirme menüsüne gidin ve daha fazla **bağlantı**' ya tıklayın.
   
    ![Tek Sign-On2 yapılandırma](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Ekranda gösterilen Munchkaal KIMLIĞINI kopyalayın ve Azure AD Yapılandırma Sihirbazı 'nda yanıt URL 'nizi doldurun.
   
    ![Tek Sign-On3 yapılandırma](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Uygulamada SSO 'yu yapılandırmak için aşağıdaki adımları izleyin:
   
    a. Yönetici kimlik bilgilerini kullanarak Marketo uygulamasında oturum açın.
   
    b. Üst gezinti bölmesindeki **yönetici** düğmesine tıklayın.
   
    ![Tek Sign-On4 yapılandırma](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Tümleştirme menüsüne gidin ve **Çoklu oturum aç**' a tıklayın.
   
    ![Tek Sign-On5 yapılandırma](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. SAML ayarlarını etkinleştirmek için **Düzenle** düğmesine tıklayın.
   
    ![Tek Sign-On6 yapılandırma](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Etkin** Tek Sign-On ayarları.
   
    f. **Azure ad tanımlayıcısını** **veren kimliği** metin kutusuna yapıştırın.
   
    örneğin: **VARLıK kimliği** metin kutusuna URL 'yi girin `http://saml.marketo.com/sp` .
   
    h. **Ad tanımlayıcı öğesi** olarak Kullanıcı kimliği konumunu seçin.
   
    ![Tek Sign-On7 yapılandırma](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Kullanıcı tanımlarınız UPN değeri değilse, öznitelik sekmesinde değeri değiştirin.
   
    i. Azure AD yapılandırma sihirbazından indirdiğiniz sertifikayı karşıya yükleyin. Ayarları **kaydedin** .
   
    j. Yeniden yönlendirme sayfaları ayarlarını düzenleyin.
   
    k. **Login URL metin kutusuna** **oturum açma URL** 'sini yapıştırın.
   
    l. **Oturum kapatma** URL 'sini, Logout **URL** metin kutusuna yapıştırın.
   
    m. **Hata URL 'si** Içinde, **Marketo örnek URL** 'nizi kopyalayın ve **Kaydet** düğmesine tıklayarak ayarları kaydedin.
   
    ![Tek Sign-On8 yapılandırma](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Kullanıcılar için SSO 'yu etkinleştirmek üzere aşağıdaki işlemleri yapın:
   
    a. Yönetici kimlik bilgilerini kullanarak Marketo uygulamasında oturum açın.
   
    b. Üst gezinti bölmesindeki **yönetici** düğmesine tıklayın.
   
    ![Tek Sign-On9 yapılandırma](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. **Güvenlik** menüsüne gidin ve **oturum açma ayarları**' na tıklayın.
   
    ![Tek Sign-On10 yapılandırma](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. **SSO gerektir** seçeneğini işaretleyin ve ayarları **kaydedin** .
   
    ![Tek Sign-On11 yapılandırma](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Marketo test kullanıcısı oluşturma

Bu bölümde, Marketo 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Marketo platformunda Kullanıcı oluşturmak için bu adımları izleyin.

1. Yönetici kimlik bilgilerini kullanarak Marketo uygulamasında oturum açın.

2. Üst gezinti bölmesindeki **yönetici** düğmesine tıklayın.
   
    ![Kullanıcı1 test et](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. **Güvenlik** menüsüne gidip **Kullanıcılar & roller** ' e tıklayın
   
    ![test kullanıcı2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kullanıcılar sekmesinde **Yeni Kullanıcı davet et** bağlantısına tıklayın
   
    ![test User3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Yeni Kullanıcı davet Sihirbazı 'nda aşağıdaki bilgileri girin
   
    a. Metin kutusuna kullanıcı **e-posta** adresini girin
   
    ![test User4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Metin kutusuna **Ilk adı** girin
   
    c. Metin kutusundaki **son adı**  girin
   
    d. **İleri** 'ye tıklayın

6. **İzinler** sekmesinde, **Userroles** ' ı seçin ve **İleri** ' ye tıklayın.
   
    ![test user5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kullanıcı davetini göndermek için **Gönder** düğmesine tıklayın
   
    ![test user6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Kullanıcı e-posta bildirimini alır ve hesabı etkinleştirmek için bağlantıyı tıklamak ve parolayı değiştirmeli. 

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Marketo 'da otomatik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım kutucuğunda tıklattığınızda, SSO 'yu ayarladığınız Marketo 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Marketo 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).