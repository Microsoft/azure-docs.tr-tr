---
title: 'Öğretici: LinkedIn çözümleri ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve LinkedIn çözümleri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727355"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>Öğretici: LinkedIn çözümleri ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, LinkedIn çözümlerini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. LinkedIn çözümlerini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de LinkedIn 'e erişebilen çözümlere erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla LinkedIn çözümlerini otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* LinkedIn 'e yönelik çözüm panonuzda hesap merkezi 'ne erişim

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* LinkedIn **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* LinkedIn 'in **yalnızca zamanında** Kullanıcı sağlamasını desteklediği


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>Galeriden LinkedIn çözümlerini ekleme

LinkedIn 'in Azure AD 'ye tümleştirilmesi için, Galeri 'den yönetilen SaaS uygulamaları listenize LinkedIn 'e yönelik çözümler eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **LinkedIn Taödünççözümlerini** yazın.
1. Sonuçlar panelinden **LinkedIn Taödünççözümlerini** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>LinkedIn için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, LinkedIn Ile Azure AD SSO çözümlerini yapılandırın ve test edin. SSO 'nun çalışması için, LinkedIn 'de bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu LinkedIn aracılığıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[LinkedIn çözümlerini yapılandırma SSO](#configure-linkedin-talent-solutions-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan LinkedIn Tatatsi çözümlerinde B. Simon 'a karşılık gelen, LinkedIn 'e yönelik **[çözüm test kullanıcısı oluşturun](#create-linkedin-talent-solutions-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, LinkedIn 'e yönelik **çözümler** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa, aşağıdaki adımları uygulayın:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    ![image1](common/upload-metadata.png)

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    ![image2](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra **tanımlayıcı** ve **yanıt URL** değerleri temel SAML yapılandırması bölümünde otomatik olarak doldurulur:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak almıyorsanız, değerleri gereksinimlerinize göre el ile girin.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL** 'si metin kutusuna URL 'yi yazın:`https://www.linkedin.com/talent/`

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **LinkedIn çözümlerini ayarlama** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, LinkedIn 'e erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde LinkedIn 'e yönelik **çözümler**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-linkedin-talent-solutions-sso"></a>LinkedIn çözümlerini yapılandırma SSO 'SU

1. LinkedIn Web sitesinde yönetici olarak oturum açın.
1. **Hesap Merkezi**' ne gidin. 
1. Gezinti çubuğundan **Ayarlar** sekmesini seçin.

    ![Ayarlar sayfası](./media/linkedin-talent-solutions-tutorial/settings.png)

1. **Tek Sign-On (SSO)** bölümünü genişletin. 

1. **Meta veri dosyasını** Indirmek için **İndir** düğmesine tıklayın veya yapılandırma verilerini açığa çıkarmak için **form bağlantısından tek tek alanları yüklemek ve kopyalamak üzere buraya** tıklayın.

    ![ yapılandırma verileri](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. Formdan tek tek alanları kopyalamak için aşağıdaki adımları gerçekleştirin.

    ![ Giriş verileriyle yapılandırma](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. **VARLıK kimliği** değerini Kopyala, bu değeri Azure Portal **temel SAML YAPıLANDıRMASı** bölümündeki **Azure AD tanımlayıcı** metin kutusuna yapıştırın.

    b. **ACS URL 'si** değerini kopyalayın, bu değeri Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'si** metin kutusuna yapıştırın.

    c. **SP X. 509.952 sertifikası (imzalama)** metin kutusunun içeriğini Not defteri 'ne kopyalayın ve bilgisayarınıza kaydedin.

1. Azure portal kopyaladığınız **Federasyon meta VERI XML** dosyasını karşıya yüklemek için **XML dosyasını karşıya yükle** ' ye tıklayın.

    ![ XML dosyasını karşıya yükle](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>LinkedIn 'e yönelik çözümler test kullanıcısı oluşturma

Bu bölümde, LinkedIn 'e açık çözümler adlı bir Kullanıcı oluşturulmuştur. LinkedIn çözümleri, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Kullanıcı LinkedIn çözümlerinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz LinkedIn 'e yönelik LinkedIn oturum açma URL 'sine yönlendirecektir.  

* LinkedIn 'e doğrudan çözüm oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız LinkedIn 'de otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki LinkedIn 'e tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız LinkedIn için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

LinkedIn çözümlerini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).