---
title: 'Öğretici: tuvalle Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve tuval arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: a71dac55c860348f31ce8da27ab050a6c71a5c68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653077"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Öğretici: tuvalle Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile tuvali tümleştirmeyi öğreneceksiniz. Tuvali Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de tuvale erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla tuvalde otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:
 
* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Tuval çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Tuval **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-canvas-from-the-gallery"></a>Galeriden tuval ekleyin

Tuvalin tümleştirmesini Azure AD 'ye göre yapılandırmak için galerideki tuvalden yönetilen SaaS uygulamaları listenize tuval eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **tuval** yazın.
1. Sonuçlar panelinden **tuval** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Tuval için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu tuvalle yapılandırın ve test edin. SSO 'nun çalışması için, tuvaldeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu tuvalle yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Tuval SSO 'Yu yapılandırma](#configure-canvas-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan tuvalde B. Simon 'a karşılık gelen, tuval **[testi kullanıcısı oluşturun](#create-canvas-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **tuval** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Tuval etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.instructure.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [tuval istemci desteği ekibine](https://community.canvaslms.com/community/help) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Imzalama sertifikası** bölümünde, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![SAML Imzalama sertifikasını Düzenle](common/edit-certificate.png)

6. **SAML Imzalama sertifikası** bölümünde, **parmak izini** kopyalayın ve bilgisayarınıza kaydedin.

    ![Parmak Izi değerini Kopyala](common/copy-thumbprint.png)

7. **Tuvali ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, tuval 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **tuval**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-canvas-sso"></a>Tuval SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, tuval şirket sitenizde yönetici olarak oturum açın.

2. **Kurslar \> yönetilen hesapları \> Microsoft**'a gidin.

    ![Tuval](./media/canvas-lms-tutorial/ic775990.png "Tuval")

3. Sol taraftaki Gezinti bölmesinde **kimlik doğrulaması**' nı seçin ve ardından **Yeni SAML Yapılandırması Ekle**' ye tıklayın.

    ![Kimlik Doğrulaması](./media/canvas-lms-tutorial/ic775991.png "Kimlik Doğrulaması")

4. Geçerli tümleştirme sayfasında, aşağıdaki adımları gerçekleştirin:

    ![Güncel tümleştirme](./media/canvas-lms-tutorial/save.png "Güncel tümleştirme")

    a. **IDP VARLıK kimliği** metin kutusunda, Azure Portal kopyaladığınız **Azure AD tanımlayıcısının** değerini yapıştırın.

    b. **URL 'de oturum** Aç metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    c. **Oturumu Kapat URL** metin kutusunda, Azure Portal kopyaladığınız **Logout URL 'si** değerini yapıştırın.

    d. **Parola Değiştir bağlantı** metin kutusunda, Azure Portal kopyaladığınız **parola URL 'si Değiştir** değerini yapıştırın.

    e. **Sertifika parmak izi** metin kutusunda, Azure Portal kopyaladığınız sertifikanın **parmak izi** değerini yapıştırın.

    f. **Oturum açma özniteliği** listesinden **NameID**' yi seçin.

    örneğin: **Tanımlayıcı biçimi** listesinden **emapostaadresi**' ni seçin.

    h. **Kimlik doğrulama ayarlarını kaydet**' e tıklayın.

### <a name="create-canvas-test-user"></a>Tuval testi kullanıcısı oluştur

Azure AD kullanıcılarının tuvalde oturum açmasını sağlamak için, tuvalde sağlanması gerekir. Tuval durumunda, Kullanıcı hazırlama el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **Tuval** kiracınızda oturum açın.

2. **Kurslar \> yönetilen hesapları \> Microsoft**'a gidin.

   ![Tuval](./media/canvas-lms-tutorial/ic775990.png "Tuval")

3. **Kullanıcılar**’a tıklayın.

   ![Ekran görüntüsü, kullanıcıların seçtiği tuval menüsünü gösterir.](./media/canvas-lms-tutorial/ic775995.png "Kullanıcılar")

4. **Yeni Kullanıcı Ekle**' ye tıklayın.

   ![Ekran görüntüsü Yeni Kullanıcı Ekle düğmesini gösterir.](./media/canvas-lms-tutorial/ic775996.png "Kullanıcılar")

5. Yeni Kullanıcı Ekle iletişim sayfasında, aşağıdaki adımları uygulayın:

   ![Kullanıcı Ekle](./media/canvas-lms-tutorial/ic775997.png "Kullanıcı Ekleme")

   a. **Tam ad** metin kutusuna, **Brittasıon** gibi kullanıcının adını girin.

   b. **E-posta** metin kutusuna, **brittasıon \@ contoso.com** gibi kullanıcının e-postasını girin.

   c. **Oturum açma** metin kutusunda, kullanıcının Azure ad e-posta adresini **brittasıon \@ contoso.com** gibi girin.

   d. **Kullanıcıya bu hesap oluşturma hakkında e-posta gönder**' i seçin.

   e. **Kullanıcı Ekle**'ye tıklayın.

> [!NOTE]
> Azure AD Kullanıcı hesapları sağlamak için tuval tarafından sunulan diğer tuval Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz tuval oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan tuval oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki tuval kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız tuvalde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Tuvali yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).