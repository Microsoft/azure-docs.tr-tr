---
title: 'Öğretici Azure Active Directory: Concur seyahat ve harcama ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Concur seyahat ve harcama arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 56eab06bd1afd18bfd4e23b9acb0b44d7bd1f80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737057"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Öğretici: Concur seyahat ve harcama ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Concur seyahat ve masrafın Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Concur seyahat ve harcamayı tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Concur seyahat ve gideri erişimine sahip olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla gezecek ve ücrettabileceği şekilde otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Concur seyahat ve gider aboneliği.
* Concur Kullanıcı hesabınızın altındaki "Şirket Yöneticisi" rolü. [Concur SSO Self-Service aracına](https://www.concursolutions.com/nui/authadmin/ssoadmin)giderek doğru erişime sahip olmanız durumunda test edebilirsiniz. Erişiminiz yoksa lütfen Concur destek veya uygulama Proje Yöneticisi ile iletişime geçin. 

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu yapılandırıp test edersiniz.

* Concur seyahat ve harcama, **IDP** ve **SP** tarafından başlatılan SSO 'yu destekler
* Concur seyahat ve harcama, hem üretim hem de uygulama ortamında SSO sınamasını destekler 

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, üç bölgenin her biri için sabit bir dize değeridir: US, EMEA ve Çin. Bu nedenle, tek bir Kiracıdaki her bölge için yalnızca bir örnek yapılandırılabilir. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Galeri 'den Concur seyahat ve harcama ekleme

Concur seyahat ve masrafın tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Concur seyahat ve harcama eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Concur seyahat ve harcama** yazın.
1. Sonuçlar panelinden **Concur seyahat ve harcama** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-concur-travel-and-expense"></a>Concur seyahat ve gideri için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Concur seyahat ve harcama ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında Concur seyahat ve gider arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Concur seyahat ve masrafına göre yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Concur seyahat ve harcama SSO 'Su yapılandırma](#configure-concur-travel-and-expense-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Concur **[seyahat ve harcama testi kullanıcısı oluşturun](#create-concur-travel-and-expense-test-user)** ve kullanıcının Azure AD gösterimine bağlı olan yarışmayı ve harcamayı Içeren bir B. Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Concur seyahat ve harcama** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama **IDP** tarafından başlatılan modda önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.

    > [!NOTE]
    > Tanımlayıcı (varlık KIMLIĞI) ve yanıt URL 'SI (onaylama tüketici hizmeti URL 'SI) bölgeye özeldir. Lütfen Concur varlığınızın veri merkezine göre ' yi seçin. Concur varlığınızın veri merkezini görmüyorsanız lütfen Concur desteğiyle iletişime geçin. 

5. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek Için **Kullanıcı özniteliği** için Düzenle/kalem simgesine tıklayın. Benzersiz kullanıcı tanımlayıcısının, Concur User login_id ile eşleşmesi gerekir. Genellikle **User. UserPrincipalName** öğesini **User. Mail** olarak değiştirmelisiniz.

    ![Kullanıcı özniteliğini Düzenle](common/edit-attribute.png)

6. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve meta verileri indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak, Concur seyahat ve masrafına erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Concur seyahat ve harcama**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-concur-travel-and-expense-sso"></a>Concur seyahat ve harcama SSO 'SU yapılandırma

1. Uygulamayı yarışma ve harcama dahilinde otomatik hale getirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımın güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **yarışmayı ayarla** ' ya tıklayın ve gider, sizi Concur seyahat ve gider uygulamasına yönlendirir. Buradan, yarışmayı ve harcamayı açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-7 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Farklı bir Web tarayıcısı penceresinde el ile seyahat ve gider ayarlamak istiyorsanız, indirilen **Federasyon meta VERI XML** 'Sini [concur SSO Self-Service aracına](https://www.concursolutions.com/nui/authadmin/ssoadmin) yüklemeniz ve yönetici olarak Concur seyahat ve harcama şirketi sitenizde oturum açmanız gerekir.

1. **Ekle**'ye tıklayın.
1. IDP 'niz için "Azure AD (US)" gibi özel bir ad girin. 
1. **XML dosyasını karşıya yükle** ' ye tıklayın ve daha önce Indirdiğiniz **Federasyon meta veri XML 'sini** ekleyin
1. Değişikliği kaydetmek için **meta veri Ekle** ' ye tıklayın.

    ![Concur SSO self servis aracı ekran görüntüsü](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Concur seyahat ve harcama testi kullanıcısı oluşturma

Bu bölümde, Concur seyahat ve harcama için B. Simon adlı bir Kullanıcı oluşturacaksınız. Concur seyahat ve gider platformunda kullanıcıları eklemek için Concur destek ekibi ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. 

> [!NOTE]
> B. Simon 'ın Concur oturum açma kimliği, Azure AD 'de B. Simon 'ın benzersiz tanımlayıcısı ile eşleşmelidir. Örneğin, B. Simon 'un Azure AD benzersiz tanımlayıcı, `B.Simon@contoso.com` . B. Simon 'ın Concur oturum açma kimliği de olmalıdır `B.Simon@contoso.com` . 

## <a name="configure-concur-mobile-sso"></a>Concur Mobile SSO 'yu yapılandırma
Concur Mobile SSO 'yu etkinleştirmek için, Concur destek ekibi **Kullanıcı erişim URL 'si** sağlamanız gerekir. Azure AD 'den **Kullanıcı erişimi URL 'si** almak için aşağıdaki adımları izleyin:
1. **Kurumsal uygulamalara** git
1. **Concur seyahat ve gideri** ' ne tıklayın
1. **Özellikler** 'e tıklayın
1. **Kullanıcı erişim URL 'sini** kopyalayın ve bu URL 'Yi Concur desteği 'ne verin

> [!NOTE]
> SSO 'yu yapılandırmak için Self-Service seçeneği kullanılamıyor, bu nedenle mobil SSO 'yu etkinleştirmek için Concur destek ekibiyle birlikte çalışın. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz bir yarışmayı ve gider oturum açma URL 'sini yönlendirecektir.

* Concur seyahat ve gider oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız yarışmayı ve gideri otomatik olarak açmanız gerekir

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım ile ilgili seyahat ve gider kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız yarışma ve harcama için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Concur seyahat ve harcamayı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).