---
title: 'Öğretici: Secret Server (On-Premises) ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Secret Server (On-Premises) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d723bf1ce82e6d443dfa55dda7d33a3a9bfc16b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647030"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Öğretici: Secret Server (On-Premises) Azure Active Directory ile tümleştirin

Bu öğreticide, Secret Server (On-Premises) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Secret Server (On-Premises) Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Secret Server (On-Premises) erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Secret Server (On-Premises) için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) özellikli abonelik Secret Server (On-Premises).

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Secret Server (On-Premises) **SP ve ıDP** tarafından başlatılan SSO 'yu destekler

## <a name="add-secret-server-on-premises-from-the-gallery"></a>Galeriden Secret Server (On-Premises) ekleme

Secret Server (On-Premises) tümleştirmesini Azure AD ile yapılandırmak için, Galeriden Secret Server (On-Premises) yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Secret Server (on-premises)** yazın.
1. Sonuçlar panelinden **Secret Server (on-premises)** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Secret Server (On-Premises) için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Secret Server (on-premises) ile yapılandırın ve test edin. SSO 'nun çalışması için, Secret Server (On-Premises) bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Secret Server (On-Premises) ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Secret Server (on-PREMISES) SSO 'Yu yapılandırın](#configure-secret-server-on-premises-sso)** .
    1. Kullanıcının Azure AD gösterimine bağlı olan Secret Server (On-Premises) bir B. Simon 'ya sahip olmak için **[Secret Server (on-premises) test kullanıcısı oluşturun](#create-secret-server-on-premises-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Secret Server (on-premises)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna URL 'yi yazın:`https://secretserveronpremises.azure`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Yukarıda gösterilen varlık KIMLIĞI yalnızca bir örnektir ve Azure AD 'de gizli sunucu örneğinizi tanımlayan benzersiz bir değer seçebilirsiniz. Bu varlık KIMLIĞINI [Secret Server (on-premises) istemci desteği ekibine](https://thycotic.force.com/support/s/) göndermeniz gerekir ve bunları tarafında yapılandıracaksınız. Daha fazla ayrıntı için lütfen [Bu makaleyi](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server)okuyun.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek yanıt URL 'si ve Sign-On URL 'siyle güncelleştirin. Bu değerleri almak için [Secret Server (on-premises) istemci destek ekibine](https://thycotic.force.com/support/s/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    !["Sertifika (base64" "Indir" eylemi seçiliyken "S A M L Imzalama sertifikası" bölümünü gösteren ekran görüntüsü.)](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. **Imza seçeneğini** belirtin **SAML yanıtı ve onaylama olarak imzala**.

    ![İmzalama seçenekleri](./media/secretserver-on-premises-tutorial/signing-option.png)

1. **Secret Server (on-premises) ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Secret Server (On-Premises) erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Secret Server (on-premises)**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-secret-server-on-premises-sso"></a>Secret Server (On-Premises) SSO 'yu yapılandırma

**Secret Server (on-premises)** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (base64)** ve uygun kopyalanmış URL 'leri Azure Portal [Secret Server (on-premises) destek ekibine](https://thycotic.force.com/support/s/)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-secret-server-on-premises-test-user"></a>Secret Server (On-Premises) test kullanıcısı oluştur

Bu bölümde, Secret Server (On-Premises) 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. Secret Server (On-Premises) platformunda kullanıcıları eklemek için [Secret Server (on-premises) destek ekibi](https://thycotic.force.com/support/s/) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Secret Server (On-Premises) oturum açma URL 'sine yönlendirecektir.  

* Doğrudan Secret Server (On-Premises) oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'yu ayarladığınız Secret Server (on-premises) otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamdaki Secret Server (On-Premises) kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Secret Server (On-Premises) otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Secret Server (On-Premises) yapılandırdıktan sonra, kuruluşunuzun gizli verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).