---
title: 'Öğretici: LogicMonitor ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve LogicMonitor arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d5342782c26b5c274699bacc4ea0c7cdf5b7f880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649436"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Öğretici: LogicMonitor ile tümleştirme Azure Active Directory

Bu öğreticide, LogicMonitor 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. LogicMonitor 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de LogicMonitor 'a erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla LogicMonitor 'da otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini LogicMonitor ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık deneme sürümü edinebilirsiniz.
* LogicMonitor çoklu oturum açma etkinleştirildi aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* LogicMonitor **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-logicmonitor-from-the-gallery"></a>Galeriden LogicMonitor ekleme

LogicMonitor 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize LogicMonitor eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **LogicMonitor** yazın.
1. Sonuçlar panelinden **LogicMonitor** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>LogicMonitor için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, LogicMonitor Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve LogicMonitor içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu LogicMonitor ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[LogicMonitor SSO 'Yu yapılandırın](#configure-logicmonitor-sso)** .
    1. **[LogicMonitor test kullanıcısı oluşturma](#create-logicmonitor-test-user)** -kullanıcının Azure AD gösterimine bağlı olan LogicMonitor 'da B. Simon 'a karşılık gelen bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **LogicMonitor** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![LogicMonitor etki alanı ve URL 'Leri çoklu oturum açma bilgileri](common/sp-identifier.png)

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.logicmonitor.com`

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.logicmonitor.com`
    
    c. **Yanıt URL 'si (onaylama tüketici hizmeti URL 'si)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve tanımlayıcısı ile güncelleştirin. Bu değerleri almak için [LogicMonitor istemci destek ekibine](https://www.logicmonitor.com/contact/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **LogicMonitor ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, LogicMonitor 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **LogicMonitor**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name=&quot;configure-logicmonitor-sso&quot;></a>LogicMonitor SSO 'yu yapılandırma

1. **LogicMonitor** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde, **Ayarlar**' a tıklayın.

    ![Ayarlar](./media/logicmonitor-tutorial/ic790052.png &quot;Ayarlar")

3. Sol taraftaki Gezinti bat öğesinde **Çoklu oturum açma**' ya tıklayın.

    ![Çoklu oturum açma](./media/logicmonitor-tutorial/ic790053.png "Çoklu Oturum Açma")

4. **Çoklu oturum açma (SSO) ayarları** bölümünde aşağıdaki adımları uygulayın:

    ![Tek Sign-On ayarları](./media/logicmonitor-tutorial/ic790054.png "Tek Sign-On ayarları")

    a. **Çoklu oturum açmayı etkinleştir '** i seçin.

    b. **Varsayılan rol ataması** olarak **salt okunur**' ı seçin.

    c. İndirilen meta veri dosyasını Not defteri 'nde açın ve sonra dosyanın içeriğini **kimlik sağlayıcısı meta verileri** metin kutusuna yapıştırın.

    d. **Değişiklikleri Kaydet**’e tıklayın.

### <a name="create-logicmonitor-test-user"></a>LogicMonitor test kullanıcısı oluştur

Azure AD kullanıcılarının oturum açabilmeleri için, Azure Active Directory Kullanıcı adlarını kullanarak LogicMonitor uygulamasına sağlanması gerekir.

**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. LogicMonitor şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **Ayarlar**' a ve ardından **Roller ve kullanıcılar**' a tıklayın.

    ![Roller ve kullanıcılar](./media/logicmonitor-tutorial/ic790056.png "Roller ve kullanıcılar")

3. **Ekle**'ye tıklayın.

4. **Hesap Ekle** bölümünde aşağıdaki adımları uygulayın:

    ![Hesap ekleme](./media/logicmonitor-tutorial/ic790057.png "Hesap ekleme")

    a. İlgili metin kutularına sağlamak istediğiniz Azure Active Directory kullanıcının Kullanıcı **adını**, **e-posta**, **parola** ve **yeniden yazma parolasını** yazın.

    b. **Roller**, **Izinleri görüntüle** ve **durum**' u seçin.

    c. **Gönder**' e tıklayın.

> [!NOTE]
> Azure Active Directory Kullanıcı hesapları sağlamak için LogicMonitor tarafından sunulan diğer bir LogicMonitor Kullanıcı hesabı oluşturma aracını veya API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz LogicMonitor oturum açma URL 'sine yeniden yönlendirilir. 

* LogicMonitor oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki LogicMonitor kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız LogicMonitor 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

LogicMonitor 'u yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).