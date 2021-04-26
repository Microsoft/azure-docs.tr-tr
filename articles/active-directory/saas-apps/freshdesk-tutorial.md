---
title: 'Öğretici: en güncel çalışma ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve en güncel çalışma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651962"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Öğretici: en güncel çalışma ile Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile en güncel şekilde tümleştirmeyi öğreneceksiniz. Aynı şekilde Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, en yenisi olan bir masa 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak oturum açmalarına izin vermek için etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:
 
* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bir adet, tek oturum açma (SSO) özellikli bir abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* En güncel çalışma **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-freshdesk-from-the-gallery"></a>Galeriden en güncel çalışma ekleme

En güncel çalışma alanının Azure AD ile tümleştirilmesini yapılandırmak için Galeriden, yönetilen SaaS uygulamaları listenize en güncel çalışma sayfasına eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna en **sonra yaz '** ı yazın.
1. Sonuçlar panelinden **yeniden Seç ' i seçin** ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>En güncel çalışma için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Azure AD SSO 'Yu sıdb ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve bu kullanıcı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu bir sonraki adımda yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için, en soldaki **[çalışma SSO 'Yu yapılandırın](#configure-freshdesk-sso)** .
    1. Kullanıcı için Azure AD gösterimine bağlı olan, en güncel bir masa 'daki Britta Simon 'a sahip olmak için, yalnızca bir **[kayıt testi kullanıcısı oluşturun](#create-freshdesk-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

1. Azure portal, **en sonra, uygulama tümleştirmesi** sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarla** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<tenant-name>.freshdesk.com` ya da başka bir değer varsa, bu değeri kullanın.

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<tenant-name>.freshdesk.com` ya da başka bir değer içeren herhangi bir değer varsa, bunu yapmanız önerilir.
     
    c. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için, en güncel olan [istemci desteği ekibine](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Bu uygulama, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde, **benzersiz kullanıcı tanımlayıcısı** Kullanıcı ile eşlenirken, varsayılan özniteliklerin listesi gösterilmektedir **. UserPrincipalName** , ancak bu talebin **Kullanıcı. Mail** ile eşlenmesini bekliyor, bu nedenle, Düzenle simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i, gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. Yalnızca kurulum **ön eki** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, B. Simon 'u kullanarak tek bir oturum açma özelliğini etkinleştirerek, en sonra da isteğe bağlı olarak masasına erişin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **yeniden Seç ' i seçin**.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-freshdesk-sso&quot;></a>En güncel çalışma SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, bir yönetici olarak iki adımda şirket sitenizde oturum açın.

2. **Güvenlik simgesini** seçin ve **güvenlik** bölümünde aşağıdaki adımları uygulayın:

    ![Çoklu Oturum Açma](./media/freshdesk-tutorial/configure-1.png &quot;Çoklu Oturum Açma")
  
    a. **Çoklu oturum açma** için **Açık**' ı seçin.

    b. **Oturum açma yönteminde** **SAML SSO**' yı seçin.

    c. **IDP metin kutusu tarafından belirtilen VARLıK kimliğinde** , Azure Portal KOPYALADıĞıNıZ **varlık kimliği** değeri yapıştırın.

    d. **SAML SSO URL** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    e. **Imzalama seçeneklerinde**, açılan listeden **yalnızca imzalı onayları** seçin.

    f. **Oturum kapatma URL 'si** metin kutusunda, Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.

    örneğin: **Güvenlik sertifikası** metin kutusu ' nda, daha önce edindiğiniz **sertifika (base64)** değerini yapıştırın.
  
    h. **Kaydet**’e tıklayın.

## <a name="create-freshdesk-test-user"></a>Yalnızca bir test kullanıcısı oluştur

Azure AD kullanıcılarının, en güncel şekilde oturum açmasını sağlamak için, bu kullanıcıların, tek yapmanız gerekir.  
Bu durumda, sağlama işlemi el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Bu **kiracınızda** oturum açın.

1. Soldaki menüde, **yönetici** ' ye tıklayın ve **Genel ayarlar** sekmesinde **aracılar**' a tıklayın.
  
    ![Aracılar](./media/freshdesk-tutorial/create-user-1.png "Aracılar")

1. **Yeni Aracı**' e tıklayın.

    ![Yeni Aracı](./media/freshdesk-tutorial/create-user-2.png "Yeni Aracı")

1. Aracı bilgileri iletişim kutusunda gerekli alanları girin ve **aracı oluştur**' a tıklayın.

    ![Aracı bilgileri](./media/freshdesk-tutorial/create-user-3.png "Aracı bilgileri")

    >[!NOTE]
    >Azure AD hesap tutucusu, hesabı etkinleştirilmeden önce onaylanacak bir bağlantı içeren bir e-posta alır.
    >
    >[!NOTE]
    >Farklı bir şekilde Azure AD Kullanıcı hesapları sağlamak için, en güncel kullanıcı hesabı oluşturma araçlarından veya en güncel API tarafından sunulan API 'Leri kullanabilirsiniz.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz bir yenonu oturum açma URL 'sine yönlendirecektir. 

* Doğrudan bir oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım ' daki bir otomatik olarak bulunan kutucuğa tıkladığınızda, SSO 'yu ayarladığınız en sonra otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

SII 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).