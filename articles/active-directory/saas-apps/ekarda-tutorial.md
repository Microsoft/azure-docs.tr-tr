---
title: 'Öğretici: ekarda ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve ekarda arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: b057d07e10676291f42a9a070e32cb17df672651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735170"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Öğretici: ekarda ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, ekarda 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Ekarda 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de ekarda 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarını kullanarak ekarda ' de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) için etkinleştirilen bir ekarda aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* ekarda, SP tarafından başlatılan ve ıDP tarafından başlatılan SSO 'yu destekler.
* ekarda, tam zamanında Kullanıcı sağlamayı destekler.

## <a name="add-ekarda-from-the-gallery"></a>Galeriden ekarda ekleme

Ekarda tümleştirmesini Azure AD 'ye göre yapılandırmak için galerideki ekarda 'i yönetilen SaaS uygulamaları listenize ekleyin:

1. İş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.

1. Sol bölmede **Azure Active Directory** hizmeti seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulamayı eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ekarda** yazın.
1. Sonuçlar panelinden **ekarda** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>Ekarda için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu ekarda ile yapılandırın ve test edin. SSO 'nun çalışması için, ekarda içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Azure AD SSO 'yu ekarda ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .

    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [EKARDA SSO 'Yu yapılandırın](#configure-ekarda-sso) .
    * Kullanıcının Azure AD gösterimine bağlı olan ekarda 'de B. Simon 'ya karşılık gelen bir [ekarda test kullanıcısı oluşturun](#create-an-ekarda-test-user) .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure AD SSO 'yu etkinleştirmek için Azure portal aşağıdaki adımları izleyin:

1. Azure portalında oturum açın.
1. **Ekarda** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, **temel SAML yapılandırma** ayarlarını düzenlemek için kurşun kalem simgesini seçin.

   ![Kurşun kalem simgesi vurgulanmış şekilde SAML sayfası ile tek Sign-On ayarlama ekran görüntüsü.](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyası**' nı görürseniz, aşağıdaki adımları izleyin:
    1. **Meta veri dosyasını karşıya yükle**' yi seçin.
    1. Meta veri dosyasını seçmek için klasör simgesini seçin ve ardından **karşıya yükle**' yi seçin.
    1. Meta veri dosyası başarıyla karşıya yüklendiğinde, **tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak ekarda bölümü metin kutusunda görünür.

    > [!Note]
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak görünmezse, değerleri gereksinimlerinize göre el ile girin.

1. **Temel SAML yapılandırması** bölümünde **hizmet sağlayıcı meta veri dosyası** görmüyorsanız ve uygulamayı IDP tarafından başlatılan modda yapılandırmak istiyorsanız, aşağıdaki alanlar için değerler girin:

    1. **Tanımlayıcı** metin kutusuna bu kalıbı IZLEYEN bir URL yazın:`https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. **Yanıt URL 'si** metin kutusuna, bu kalıbı IZLEYEN bir URL yazın:`https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Uygulamayı SP tarafından başlatılan modda yapılandırmak istiyorsanız **ek URL 'Ler ayarla** ' yı seçin ve bunu yapın:

    **Oturum açma URL 'si** metin kutusuna, bu kalıbı IZLEYEN bir URL yazın:`https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Yukarıdaki iki adımda yer almayan değerler gerçek değildir. Bunları gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI değerleriyle güncelleştirin. Bu değerleri almak için [Ekarda istemci destek ekibine](mailto:contact@ekarda.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, bilgisayarınıza **sertifika (base64)** kaydetmek için **İndir** ' i seçin.

    ![Base64 sertifikası için karşıdan yükleme bağlantısı vurgulanmış şekilde SAML ile tek Sign-On ayarlama sayfasının SAML Imzalama sertifikası bölümünün ekran görüntüsü.](common/certificatebase64.png)

1. **Ekarda ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın.

    ![SAML ile tek Sign-On ayarla sayfasındaki URL kopyalama bağlantıları vurgulanmış şekilde, set up ekarda bölümünün ekran görüntüsü.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı bir test kullanıcısı oluşturmak için Azure portal kullanacaksınız.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.

1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com` girin.
   1. **Parolayı göster** onay kutusunu seçin ve **parola** kutusunda görünen değeri yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, ekarda 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **ekarda**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

1. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

1. **Kullanıcılar ve gruplar** iletişim kutusunda, Kullanıcı listesinden **B. Simon** öğesini seçin. Ardından, ekranın alt kısmında **Seç** ' i seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-ekarda-sso"></a>Ekarda SSO 'yu yapılandırma

1. Yapılandırmayı ekarda içinde otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya Uzantı eklendikten sonra, **set up ekarda** öğesine tıkladığınızda sizi ekarda uygulamasına yönlendirirsiniz. Buradan, ekarda 'de oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-6 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Ekarda 'yi el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, ekarda şirket sitenizde yönetici olarak oturum açın.

1. **Yönetici**  >  **Hesabım**' ı seçin.

    ![Yönetici menüsünde Hesabım vurgulanarak ekarda site Kullanıcı arabiriminin ekran görüntüsü.](./media/ekarda-tutorial/ekarda.png)

1. Sayfanın alt kısmındaki **SAML ayarları** bölümünü bulun. Bu bölüm, SAML tümleştirmesini yapılandırdığınız yerdir.
1. **SAML ayarları** bölümünde, şu adımları izleyin:

    ![SAML yapılandırma alanları vurgulanmış şekilde ekarda SAML ayarları sayfasının ekran görüntüsü.](./media/ekarda-tutorial/ekarda1.png)

    1. **Hizmet sağlayıcısı meta verileri** bağlantısını seçin ve bilgisayarınıza dosya olarak kaydedin.
    1. **SAML etkinleştir** onay kutusunu seçin.
    1. **IDP VARLıK kimliği** metin kutusunda, daha önce Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcı** değerini yapıştırın.
    1. **IDP oturum açma URL 'si** metin kutusunda, daha önce kopyaladığınız **oturum açma URL** değerini Azure Portal yapıştırın.
    1. **IDP oturum kapatma URL 'si** metin kutusunda, daha önce Azure Portal KOPYALADıĞıNıZ **Logout URL** değerini yapıştırın.
    1. Azure portal indirdiğiniz **sertifika (base64)** dosyasını açmak Için Not defteri ' ni kullanın. Bu içeriği **IDP x509 sertifikası** metin kutusuna yapıştırın.
    1. **Seçenekler** bölümünde **SLO 'yu etkinleştir** onay kutusunu seçin.
    1. **Güncelleştir**’i seçin.

### <a name="create-an-ekarda-test-user"></a>Ekarda test kullanıcısı oluşturma

Bu bölümde, ekarda içinde B. Simon adlı bir Kullanıcı oluşturulur. ekarda, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde yapılacak bir işlem yok. B. Simon adlı bir Kullanıcı ekarda içinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz ekarda oturum açma URL 'sine yeniden yönlendirilir.

* Doğrudan ekarda oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'yu ayarladığınız ekarda otomatik olarak oturum açmış olmanız gerekir

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda ekarda kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız ekarda için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Ekarda yapılandırdıktan sonra, oturum denetimini zorunlu kılabilirsiniz. Bu önlem, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanma ve savunma sürecinde koruma sağlar. Oturum denetimi Koşullu Erişim Uygulama Denetimi genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).