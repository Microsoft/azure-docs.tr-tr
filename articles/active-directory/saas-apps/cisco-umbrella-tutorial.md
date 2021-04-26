---
title: 'Öğretici: Cisco şemsiye admin SSO ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Cisco şemsiye admin SSO arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592515"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Öğretici: Cisco şemsiye admin SSO ile tümleştirme Azure Active Directory

Bu öğreticide Cisco şemsiye yönetici SSO 'yu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Cisco şemsiye admin SSO 'yu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Cisco şemsiye yönetici SSO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Cisco şemsiye yönetici SSO 'ya otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Cisco şemsiye admin SSO çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Cisco şemsiye admin SSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Galeriden Cisco şemsiye admin SSO 'SU ekleme

Cisco şemsiye yönetim SSO 'SU ile Azure AD arasında tümleştirmeyi yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Cisco şemsiye yönetici SSO 'SU eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **CISCO şemsiye admin SSO** yazın.
1. Sonuçlar panelinden **Cisco şemsiye ADMIN SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Cisco şemsiye admin SSO 'SU için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Cisco şemsiye yönetici SSO 'Su Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için Cisco şemsiye admin SSO 'daki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Cisco şemsiye yönetici SSO 'SU ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Cisco şemsiye ADMIN SSO SSO 'Yu yapılandırın](#configure-cisco-umbrella-admin-sso-sso)** .
    1. Kullanıcı Azure AD gösterimi ile bağlantılı olan Cisco şemsiye admin SSO 'SU için **[Cisco şemsiye yönetıcı SSO 'su test kullanıcısı oluşturun](#create-cisco-umbrella-admin-sso-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Cisco şemsiye YÖNETICISI SSO** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulama zaten Azure ile önceden tümleştirildiği için kullanıcının herhangi bir adım yapması gerekmez.

    a. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    b. **Ek URL 'Ler ayarla**' ya tıklayın.

    c. **Oturum açma URL 'si** metin kutusuna URL 'yi yazın:`https://login.umbrella.com/sso`

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Cisco şemsiye yönetıcı SSO ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Cisco şemsiye yönetici SSO 'ya erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Cisco şemsiye ADMIN SSO**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Cisco şemsiye admin SSO SSO 'yu yapılandırma

1. Farklı bir tarayıcı penceresinde, Cisco şemsiye admin SSO şirket sitenizde yönetici olarak oturum açın.

2. Menünün sol tarafında, **yönetici** ' ye tıklayın ve **kimlik doğrulaması** ' na gidin ve **SAML**' ye tıklayın.

    ![Yönetici](./media/cisco-umbrella-tutorial/admin.png)

3. **Diğer** ' i seçin ve **İleri**' ye tıklayın.

    ![Diğer](./media/cisco-umbrella-tutorial/other.png)

4. **Cisco şemsiye yönetıcı SSO meta verileri** sayfasında, **İleri**' ye tıklayın.

    ![Meta veriler](./media/cisco-umbrella-tutorial/metadata.png)

5. **Meta verileri karşıya yükle** sekmesinde, önceden yapılandırılmış SAML varsa, **bunları değiştirmek için buraya tıklayın** seçeneğini belirleyin ve aşağıdaki adımları izleyin.

    ![Sonraki](./media/cisco-umbrella-tutorial/next.png)

6. **A: XML dosyasını karşıya yükle seçeneğinde**, Azure Portal Indirdiğiniz **Federasyon meta veri XML** dosyasını karşıya yükleyin ve meta verileri karşıya yükledikten sonra otomatik olarak otomatik olarak doldurulur ve **İleri**' ye tıklayın.

    ![Choosefile](./media/cisco-umbrella-tutorial/choose-file.png)

7. **SAML yapılandırmasını Doğrula** bölümünde **SAML yapılandırmanızı test et**' e tıklayın.

    ![Test](./media/cisco-umbrella-tutorial/test.png)

8. **Kaydet**' e tıklayın.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Cisco şemsiye admin SSO test kullanıcısı oluşturma

Azure AD kullanıcılarının Cisco şemsiye admin SSO 'da oturum açmasını sağlamak için, Cisco şemsiye yönetici SSO ' ya sağlanması gerekir.  
Cisco şemsiye yönetici SSO 'SU durumunda, sağlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Farklı bir tarayıcı penceresinde, Cisco şemsiye admin SSO şirket sitenizde yönetici olarak oturum açın.

2. Menünün sol tarafında, **yönetici** ' ye tıklayın ve **hesaplar**' a gidin.

    ![Hesap](./media/cisco-umbrella-tutorial/account.png)

3. **Hesaplar** sayfasında, sayfanın sağ üst tarafındaki **Ekle** ' ye tıklayın ve aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı](./media/cisco-umbrella-tutorial/create-user.png)

    a. **Ad** alanına **Britta** gibi bir ad girin.

    b. **Soyadı** alanına **Simon** gibi soyadı girin.

    c. **Yönetici temsilcisi rolü** Seç ' de rolünüzü seçin.

    d. **E-posta adresi** alanına **brittasıon \@ contoso.com** gibi kullanıcının Emae-postaadını girin.

    e. **Parola** alanına parolanızı girin.

    f. **Parolayı Onayla** alanına parolanızı yeniden girin.

    örneğin: **Oluştur**' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Cisco şemsiye admin SSO oturum açma URL 'sine yeniden yönlendirilir.  

* Cisco şemsiye admin SSO oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Cisco şemsiye yönetim SSO 'su için otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki Cisco şemsiye admin SSO kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Cisco şemsiye yönetim SSO 'SU için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Cisco şemsiye yönetici SSO 'yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).