---
title: 'Öğretici Azure Active Directory: Saba bulutu ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Saba bulutu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572717"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Öğretici: Saba bulutu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Saba bulutu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Saba bulutunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Saba buluta erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Saba bulutta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Saba bulutu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Saba bulutu **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.
* Saba bulutu **, tam zamanında** Kullanıcı sağlamayı destekler.
* Saba bulut mobil uygulaması artık, SSO 'yu etkinleştirmek için Azure AD ile yapılandırılabilir. Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

## <a name="adding-saba-cloud-from-the-gallery"></a>Galeriden Saba bulutu ekleme

Saba bulutu 'nın Azure AD 'ye tümleştirilmesini yapılandırmak için, galerisindeki Saba bulutu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Saba bulutu** yazın.
1. Sonuçlar panelinden **Saba bulutu** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Saba bulutu için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Saba bulutuyla Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile Saba bulutu 'ndaki ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Saba bulutla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Saba Cloud SSO 'Yu yapılandırma](#configure-saba-cloud-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Saba Cloud **[test kullanıcısı oluşturun](#create-saba-cloud-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Saba bulutta B. Simon 'a karşılık gelen bir karşılığı olacak.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için, **[Saba bulutu (mobil) IÇIN SSO 'Yu test](#test-sso-for-saba-cloud-mobile)** edin.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Saba bulut** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<CUSTOMER_NAME>.sabacloud.com`

    b. **Geçiş durumu** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` ya da SAML bir mikro site için yapılandırılmışsa, aşağıdaki KALıBı kullanarak bir URL yazın:`IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > RelayState yapılandırma hakkında daha fazla bilgi için lütfen [Bu](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) bağlantıya başvurun.

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI, oturum açma URL 'SI ve geçiş durumu ile güncelleştirin. Bu değerleri almak için [Saba bulut istemci desteği ekibine](mailto:support@saba.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Saba bulutu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Saba buluta erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Saba bulutu**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-saba-cloud-sso"></a>Saba bulut SSO 'yu yapılandırma

1. Saba bulut şirket sitenizde yönetici olarak oturum açın.
1. **Menü** simgesine tıklayın ve **yönetici**' ye ve ardından **Sistem Yöneticisi** sekmesini seçin.

    ![Sistem Yöneticisi ekran görüntüsü](./media/saba-cloud-tutorial/system.png)

1. **Sistemi Yapılandır** bölümünde **SAML SSO kurulumu** ' nu seçin ve **SAML SSO** düğmesine tıklayın. 

    ![yapılandırma için ekran görüntüsü](./media/saba-cloud-tutorial/configure.png)

1. Açılan pencerede, açılan listeden **mikro site** ' yi seçin ve **Ekle ve Yapılandır**' a tıklayın.

    ![Site/mikro site Ekle ekran görüntüsü](./media/saba-cloud-tutorial/microsite.png)

1. **IDP 'Yi Yapılandır** bölümünde, Azure Portal Indirdiğiniz **Federasyon meta veri XML** dosyasını karşıya yüklemek için **Araştır** ' a tıklayın. **Siteye özel IDP** onay kutusunu etkinleştirin ve **içeri aktar**' a tıklayın.

    ![Sertifika içeri aktarma ekran görüntüsü](./media/saba-cloud-tutorial/certificate.png) 

1. SP 'yi **Yapılandır** bölümünde, **varlık diğer adı** değerini kopyalayın ve bu değeri Azure Portal **temel SAML YAPıLANDıRMASı** bölümündeki **tanımlayıcı (varlık kimliği)** metin kutusuna yapıştırın. **Oluştur**' a tıklayın.

    ![SP yapılandırma için ekran görüntüsü](./media/saba-cloud-tutorial/generate-metadata.png) 

1. **Özellikleri Yapılandır** bölümünde, doldurulmuş alanları doğrulayın ve **Kaydet**' e tıklayın. 

    ![Yapılandırma özellikleri ekran görüntüsü](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Saba bulut test kullanıcısı oluştur

Bu bölümde, Saba bulutu 'nda Britta Simon adlı bir Kullanıcı oluşturulur. Saba bulutu, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı zaten Saba bulutta yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!NOTE]
> Saba bulutuyla yalnızca zaman Kullanıcı sağlama SAML 'yi etkinleştirmek için lütfen [Bu](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) belgeye başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Saba bulut oturum açma URL 'sine yeniden yönlendirilir.  

* Saba bulut oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Saba bulutta otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamdaki Saba bulut kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Saba bulutta otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Azure AD 'de oturum açma URL 'SI doldurulmamışsa, uygulama ıDP tarafından başlatılan mod olarak değerlendirilir ve oturum açma URL 'SI doldurulduktan sonra Azure AD, kullanıcıyı her zaman hizmet sağlayıcısı tarafından başlatılan akış için Saba bulut uygulamasına yönlendirir.

## <a name="test-sso-for-saba-cloud-mobile"></a>Saba bulutu için test SSO 'SU (mobil)

1. Saba bulut mobil uygulamasını açın, metin kutusuna **site adını** verin ve **ENTER**' a tıklayın.

    ![Site adı için ekran görüntüsü.](./media/saba-cloud-tutorial/site-name.png)

1. **E-posta adresinizi** girip **İleri**' ye tıklayın.

    ![E-posta adresi için ekran görüntüsü.](./media/saba-cloud-tutorial/email-address.png)

1. Son olarak, başarıyla oturum açtıktan sonra uygulama sayfası görüntülenir.

    ![Başarılı oturum açma için ekran görüntüsü.](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

Saba bulutu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


