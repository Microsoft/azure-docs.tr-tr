---
title: 'Öğretici: en iyi çalışmalarla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve yeniden oluşturma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 64a8907ec359235c2856b1f92388d9daa38cd190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651731"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Öğretici: en iyi çalışmalarla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile en iyi çalışmaları tümleştirmeyi öğreneceksiniz. Yalnızca Azure AD ile birlikte çalışarak tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, En Iyi şekilde çalışan erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte çalışmak üzere otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* En adet, çoklu oturum açma (SSO) özellikli aboneliği kullanılabilir.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* En güncel çalışma **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="add-freshworks-from-the-gallery"></a>Galeriden En Iyi şekilde çalışma ekleme

En Iyi çalışmalardan Azure AD ile tümleşmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize en güncel çalışmalar eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna en **uygun ' ı yazın.**
1. Sonuçlar panelinden yeniden çalıştır ' **ı seçin ve** ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-freshworks"></a>Azure AD SSO 'yu yapılandırma ve test etme için yeniden çalışma

Azure AD SSO 'yu, **B. Simon** adlı bir test kullanıcısı kullanarak en iyi şekilde yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve ondaki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu bir sonraki adımda yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, tek başına **[çalışma SSO 'Yu yapılandırın](#configure-freshworks-sso)** .
    1. Kullanıcı için Azure AD gösterimine bağlı olan, en geç çalışmadaki B. Simon 'a sahip olmak için, yalnızca bir **[test kullanıcısı oluşturun](#create-freshworks-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, en **iyi çalışma** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.freshworks.com/sp/SAML/CUSTOM_URL`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SUBDOMAIN>.freshworks.com/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'si ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için, en güncel [istemci desteği ekibine](mailto:support@freshworks.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **İmzalama** seçeneklerini gereksiniminize göre değiştirmek Için, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

     ![image](common/edit-certificate.png)

     !["Düzenle" düğmesi seçiliyken "S A M L Imzalama sertifikası" iletişim kutusunu gösteren ekran görüntüsü.](./media/freshworks-tutorial/response.png)

    a. **SAML yanıtını** **Imzalama seçeneği** olarak imzala seçeneğini belirleyin.

    b. **Kaydet**’e tıklayın.

1. **En güncel çalışmaları ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak En Iyi şekilde çalışmasına erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **yeniden Seç ' i seçin**.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-freshworks-sso"></a>En Iyi çalışma SSO 'yu yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve yeni bir yönetici olarak, yeni bir şirket sitesinde oturum açın ve aşağıdaki adımları gerçekleştirin:

2. Menünün sol tarafında **güvenlik** simgesine tıklayın ve ardından **Çoklu oturum açma** seçeneğini Işaretleyin ve **KIMLIK doğrulama yöntemleri** altında **SAML SSO** 'yu seçin.

    !["Çoklu oturum açma" seçeneği açık ve "S A M L s S O" seçiliyken "güvenlik-kimlik doğrulama yöntemleri" bölümünü gösteren ekran görüntüsü.](./media/freshworks-tutorial/configure01.png)

3. **Çoklu oturum açma** bölümünde aşağıdaki adımları gerçekleştirin:

    ![En iyi iş yapılandırması](./media/freshworks-tutorial/configure02.png)

    a. Örneğiniz için **hizmet sağlayıcısı (SP) VARLıK kimliğini** kopyalamak ve Azure Portal **temel SAML yapılandırması** bölümünde **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırmak için **Kopyala** ' ya tıklayın.

    b. **IDP metin kutusu tarafından belirtilen VARLıK kimliğinde** , Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.

    c. **SAML SSO URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** değerini yapıştırın.

    d. Base64 Ile kodlanmış sertifikayı not defteri 'nde açın, içeriğini kopyalayın ve **güvenlik sertifikası** metin kutusuna yapıştırın.

    e. **Kaydet**’e tıklayın.

### <a name="create-freshworks-test-user"></a>Yalnızca bir test kullanıcısı oluştur

Bu bölümde, En Iyi şekilde çalışması için B. Simon adlı bir Kullanıcı oluşturacaksınız. En güncel kullanıcıları [istemci destek ekibi](mailto:support@freshworks.com) ile çalışarak, kullanıcıları en iyi çalışma platformunda ekleyin. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz en güncel oturum açma URL 'sine yönlendirecektir.  

* Doğrudan oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'yu ayarladığınız otomatik olarak çalışır durumda oturum açmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda otomatik olarak çalışır Kutucuğa tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız otomatik olarak çalışır durumda oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

En Iyi şekilde çalışma yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).