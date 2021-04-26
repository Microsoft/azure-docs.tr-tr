---
title: 'Öğretici: Tableau Online ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Tableau arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5318570ed77e3352f37c2306ecd003195992d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732009"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Öğretici: Tableau Online ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Tableau online 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Tableau online 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Tableau online 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla çevrimiçi Tableau için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Tableau çevrimiçi çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Tableau Online, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-tableau-online-from-the-gallery"></a>Galeriden çevrimiçi Tableau ekleme

Tableau online 'ın tümleştirmesini Azure AD ile yapılandırmak için, galerideki Tableau online 'ı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Tableau online** yazın.
1. Sonuçlar panelinden **Tableau online** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Tableau Online için Azure AD SSO 'yu yapılandırın ve test edin

Bu bölümde, **Britta Simon** adlı bir test kullanıcısına göre Tableau online Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Tableau online 'daki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD SSO 'yu Tableau Online ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Tableau ONLINE SSO 'Yu yapılandırma](#configure-tableau-online-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. User 'ın Azure AD gösterimine bağlı olan Tableau online 'da B. Simon 'a sahip olmak için **[Tableau online test kullanıcısı oluşturun](#create-tableau-online-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Tableau çevrimiçi** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusuna URL 'yi yazın:`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. **Tanımlayıcı (VARLıK kimliği)** metın kutusuna URL yazın:`https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > `<entityid>`Bu öğreticideki **Tableau online 'ı ayarla** bölümünde değeri alacaksınız. Varlık KIMLIĞI değeri, **Tableau online 'ı ayarla** bölümünde **Azure AD tanımlayıcı** değeri olacak.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Tableau online 'ı ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Tableau çevrimiçi erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Tableau online**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-tableau-online-sso"></a>Tableau online SSO 'yu yapılandırma

1. Tableau online 'daki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **set up Tableau online** 'a tıkladığınızda sizi Tableau Online uygulamasına yönlendirirsiniz. Buradan, Tableau çevrimiçi oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Tableau online 'ı el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, Tableau Online şirket sitenizde yönetici olarak oturum açın.

1. **Ayarlar** ' a ve ardından **kimlik doğrulaması**' na gidin.

    ![Ekran görüntüsü ayarlar menüsünden seçilen kimlik doğrulamasını gösterir.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. SAML 'yi etkinleştirmek için **kimlik doğrulama türleri** bölümünde bölümüne gidin. **Ek bir kimlik doğrulama yöntemini etkinleştirip** **SAML** onay kutusunu işaretleyin.

    ![Ekran görüntüsü, değerleri seçebileceğiniz kimlik doğrulama türleri bölümünü gösterir.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. **Meta veri dosyasını Tableau online bölümüne aktarmak** için aşağı kaydırın.  Araştır ' a tıklayın ve Azure AD 'den indirdiğiniz meta veri dosyasını içeri aktarın. Sonra **Uygula**' ya tıklayın.

   ![Ekran görüntüsü, meta veri dosyasını içeri aktarabileceğiniz bölümü gösterir.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. **Onayları Eşleştir** bölümünde **e-posta adresi**, **adı** ve **Soyadı** için karşılık gelen kimlik sağlayıcısı onaylama adını ekleyin. Bu bilgileri Azure AD 'den almak için: 
  
    a. Azure portal, **Tableau çevrimiçi** uygulama tümleştirme sayfasına gidin.

    b. **Kullanıcı öznitelikleri & talepler** bölümünde düzenleme simgesine tıklayın.

   ![Ekran görüntüsü, düzenleme simgesini seçebileceğiniz Kullanıcı öznitelikleri & talepler bölümünü gösterir.](./media/tableauonline-tutorial/attributesection.png)

    c. Aşağıdaki adımları kullanarak şu özniteliklerin ad alanı değerini kopyalayın: 1., e-posta ve Soyadı:

   ![Ekran görüntüsü,, soyadı ve Emadresi özniteliklerini gösterir.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. **User.** bir değer ' e tıklayın

    e. **Ad alanı** metin kutusundan değeri kopyalayın.

    ![Ekran görüntüsü, ad alanını girebileceğiniz Kullanıcı taleplerini Yönet bölümünü gösterir.](./media/tableauonline-tutorial/attributesection2.png)

    f. E-posta için ad alanı değerlerini kopyalamak ve soyadı yukarıdaki adımları tekrarlayın.

    örneğin: Tableau Online uygulamasına geçiş yapın ve ardından **Kullanıcı öznitelikleri & talep** bölümünü aşağıdaki şekilde ayarlayın:

    * E-posta: **posta** veya **userPrincipalName**

    * Ad **: önce**

    * Soyadı: **Soyadı**

    ![Ekran görüntüsü, değerleri girebileceğiniz eşleşme öznitelikleri bölümünü gösterir.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tableau online test kullanıcısı oluşturma

Bu bölümde, Tableau online 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız.

1. **Tableau online**'da **Ayarlar** ' a ve ardından **kimlik doğrulama** Bölümü ' ne tıklayın. **Kullanıcıları Yönet** bölümünde aşağı kaydırın. **Kullanıcı Ekle** ' ye ve ardından **e-posta adreslerini girin ' e** tıklayın
  
    ![Ekran görüntüsü kullanıcıları Yönet bölümünü gösterir ve buradan Kullanıcı Ekle seçeneğini belirleyebilirsiniz.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. **(SAML) kimlik doğrulaması için Kullanıcı Ekle**' yi seçin. **E-posta adreslerini girin** metin kutusuna Britta. simon \@ contoso.com ekleyin
  
    ![Ekran görüntüsü, bir e-posta adresi girebileceğiniz Kullanıcı Ekle sayfasını gösterir.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. **Kullanıcı Ekle**' ye tıklayın.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Tableau çevrimiçi oturum açma URL 'sine yeniden yönlendirilir.

* Doğrudan Tableau çevrimiçi oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarımın Tableau çevrimiçi kutucuğuna tıkladığınızda bu, Tableau Online oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Tableau online 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)