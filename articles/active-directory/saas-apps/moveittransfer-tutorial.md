---
title: 'Öğretici: MoveIt aktarımı ile Azure Active Directory tümleştirme-Azure AD tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve MoveIt aktarma-Azure AD tümleştirmesi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653085"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Öğretici: MoveIt aktarımı ile Azure Active Directory tümleştirme-Azure AD tümleştirmesi

Bu öğreticide, MOVEit aktarma-Azure AD tümleştirmesini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. MOVEit transfer-Azure AD tümleştirmesini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de MoveIt aktarımına erişimi olan denetim-Azure AD tümleştirmesi.
* Kullanıcılarınızın Azure AD hesaplarıyla Azure AD tümleştirmesi için, MoveIt aktarma için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* MoveIt aktarımı-Azure AD tümleştirmesi çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* MoveIt aktarımı-Azure AD tümleştirmesi, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Galeriden MoveIt aktarımı ekleme-Azure AD tümleştirmesi

MOVEit transfer-Azure AD tümleştirmesini Azure AD 'ye tümleştirmeyi yapılandırmak için, Galeriden MoveIt transfer-Azure AD tümleştirmesini, Galeri 'den yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **moveit transfer-Azure AD tümleştirmesi** yazın.
1. Hareket panelinden **moveit transfer-Azure AD tümleştirmesi** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Azure AD SSO 'yu MoveIt aktarımı için yapılandırma ve test etme-Azure AD tümleştirmesi

Azure AD SSO 'yu, MOVEit aktarma-Azure AD tümleştirmesi ile **B. Simon** adlı bir test kullanıcısı kullanarak yapılandırın ve test edin. SSO 'nun çalışması için, Azure AD kullanıcısı ve MoveIt aktarımı-Azure AD tümleştirmesi içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu MoveIt aktarma-Azure AD tümleştirmesi ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[MoveIt aktarımını Yapılandırma-Azure AD TÜMLEŞTIRME SSO](#configure-moveit-transfer---azure-ad-integration-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. **[MoveIt transfer-Azure AD tümleştirme test kullanıcısına](#create-moveit-transfer---azure-ad-integration-test-user)** , MoveIt aktarma-Azure AD tümleştirmesi ' nin, kullanıcının Azure AD gösterimine bağlı olduğu bir B. Simon 'a sahip olmasını sağlamak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **moveit transfer-Azure AD tümleştirme** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa, aşağıdaki adımları uygulayın:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![Meta veri dosyasını karşıya yükle](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![meta veri dosyası seçin](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL** değeri **temel SAML yapılandırması** bölümünde otomatik olarak doldurulur:

    ![MoveIt aktarımı-Azure AD tümleştirme etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın:`https://contoso.com`

    > [!NOTE]
    > **Oturum açma URL 'si** değeri gerçek değil. Değeri gerçek Sign-On URL 'siyle güncelleştirin. Bu değeri almak için [MoveIt transfer-Azure AD tümleştirme istemci destek](https://community.ipswitch.com/s/support) ekibine başvurun. Service Provider meta verileri **dosyasını** , daha sonra öğreticideki **MoveIt aktarımını Yapılandırma-Azure AD tümleştirmesi çoklu oturum açma** bölümünde açıklanan **hizmet sağlayıcı meta veri URL** 'sinden indirebilirsiniz. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **MoveIt aktarımını ayarla-Azure AD tümleştirmesi** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, MoveIt aktarma-Azure AD tümleştirmesi 'ne erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **moveit aktarımı-Azure AD tümleştirmesi**' ni seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>MoveIt aktarımını Yapılandırma-Azure AD tümleştirme SSO 'SU

1. MoveIt aktarım kiracınızda yönetici olarak oturum açın.

2. Sol gezinti bölmesinde, **Ayarlar**' a tıklayın.

    ![Uygulama tarafındaki ayarlar bölümü](./media/moveittransfer-tutorial/settings.png)

3. **Güvenlik ilkeleri-> Kullanıcı kimlik doğrulaması** altında bulunan **tek** oturum açma bağlantısı ' na tıklayın.

    ![Uygulama tarafında güvenlik Ilkeleri](./media/moveittransfer-tutorial/sso.png)

4. Meta veri belgesini indirmek için meta veri URL 'SI bağlantısına tıklayın.

    ![Hizmet sağlayıcı meta veri URL 'SI](./media/moveittransfer-tutorial/metadata.png)
    
   * **Temel SAML yapılandırması** bölümünde **EntityId** 'nin **tanımlayıcıyla** eşleştiğini doğrulayın.
   * **Assertionconsumerservice** location URL 'Si, **temel SAML YAPıLANDıRMASı** bölümündeki **yanıt URL** 'siyle eşleşiyor.
    
     ![Uygulama tarafında tek Sign-On yapılandırma](./media/moveittransfer-tutorial/xml.png)

5. Yeni bir federal kimlik sağlayıcısı eklemek için **kimlik sağlayıcı ekle** düğmesine tıklayın.

    ![Kimlik sağlayıcısı ekle](./media/moveittransfer-tutorial/idp.png)

6. Azure portal ' den indirdiğiniz meta veri dosyasını seçmek için **araştır...** ' a tıklayın ve ardından indirilen dosyayı karşıya yüklemek Için **kimlik sağlayıcısı ekle** ' ye tıklayın.

    ![SAML kimlik sağlayıcısı](./media/moveittransfer-tutorial/saml.png)

7. **Federal kimlik sağlayıcısı ayarlarını Düzenle...** sayfasında, **etkin** olarak "**Evet**" seçeneğini belirleyin ve **Kaydet**' e tıklayın.

    ![Federal Kimlik sağlayıcısı ayarları](./media/moveittransfer-tutorial/save.png)

8. **Federal kimlik sağlayıcısı Kullanıcı ayarlarını Düzenle** sayfasında, aşağıdaki işlemleri gerçekleştirin:
    
    ![Federal Kimlik sağlayıcısı ayarlarını Düzenle](./media/moveittransfer-tutorial/attributes.png)
    
    a. **SAML NameID** öğesini **oturum açma adı** olarak seçin.
    
    b. **Tam ad** olarak **diğer** ' i seçin ve **öznitelik adı** metin kutusuna değeri koyun: `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. **E-posta** olarak **diğer** ' i seçin ve **öznitelik adı** metin kutusuna değeri koyun: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. **Oturum açma sırasında hesabı otomatik oluştur** olarak **Evet** ' i seçin.
    
    e. **Kaydet** düğmesine tıklayın.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MoveIt aktarımı oluşturma-Azure AD tümleştirme test kullanıcısı

Bu bölümün amacı, MoveIt aktarma-Azure AD tümleştirmesi ' nde Britta Simon adlı bir Kullanıcı oluşturmaktır. MoveIt aktarımı-Azure AD tümleştirmesi, etkinleştirdiğiniz tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Taşınabilir BT aktarımına erişme denemesi sırasında yeni bir Kullanıcı oluşturulur-Azure AD tümleştirmesi henüz yoksa.

>[!NOTE]
>Bir kullanıcıyı el ile oluşturmanız gerekiyorsa, [moveit transfer-Azure AD tümleştirme istemci desteği ekibine](https://community.ipswitch.com/s/support)başvurmanız gerekir.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum akışını başlatabileceğiniz MoveIt aktarımı-Azure AD tümleştirme oturum açma URL 'sine yönlendirilir. 

* MoveIt transfer-Azure AD tümleştirme oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım 'daki MoveIt transfer-Azure AD tümleştirme kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız MoveIt aktarımı-Azure AD tümleştirmesinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

MoveIt aktarımını yapılandırdıktan sonra Azure AD tümleştirmesi, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve içeri aktarılmasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).