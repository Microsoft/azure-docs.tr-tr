---
title: 'Öğretici: GitHub kurumsal yönetilen kullanıcıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve GitHub kurumsal yönetilen Kullanıcı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2021
ms.author: jeedes
ms.openlocfilehash: 864415f421f4fbecf31fd52a624ac568b4cf9c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574822"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-managed-user"></a>Öğretici: GitHub kurumsal yönetilen kullanıcıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, GitHub kurumsal yönetilen kullanıcısını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. GitHub kurumsal yönetilen kullanıcısını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de GitHub kurumsal yönetilen kullanıcısına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla GitHub kurumsal yönetilen kullanıcısına otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* GitHub kurumsal yönetilen Kullanıcı çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* GitHub kurumsal yönetilen Kullanıcı **SP ve ıDP** tarafından başlatılan SSO 'yu destekler.
* GitHub kurumsal yönetilen Kullanıcı **, tam zamanında** Kullanıcı sağlamayı destekler.
* GitHub kurumsal yönetilen Kullanıcı, [ **Otomatik** Kullanıcı sağlamayı](https://docs.microsoft.com/azure/active-directory/saas-apps/github-enterprise-managed-user-provisioning-tutorial)destekler.

## <a name="adding-github-enterprise-managed-user-from-the-gallery"></a>Galeriden GitHub kurumsal yönetilen kullanıcısı ekleniyor

GitHub kurumsal yönetilen kullanıcısının Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden GitHub kurumsal yönetilen Kullanıcı ' yı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **GitHub kurumsal yönetilen Kullanıcı** yazın.
1. Sonuçlar panelinden **GitHub kurumsal yönetilen Kullanıcı** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-managed-user"></a>GitHub kurumsal yönetilen Kullanıcı için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak GitHub kurumsal yönetilen KULLANıCıYLA Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, GitHub kurumsal yönetilen Kullanıcı içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu GitHub kurumsal yönetilen kullanıcıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[GitHub kurumsal yönetilen Kullanıcı SSO 'Yu yapılandırma](#configure-github-enterprise-managed-user-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. GitHub kurumsal yönetilen **[Kullanıcı test kullanıcısı oluşturun](#create-github-enterprise-managed-user-test-user)** ve kullanıcının Azure AD gösterimi Ile bağlantılı GitHub kurumsal yönetilen Kullanıcı için B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **GitHub kurumsal yönetilen Kullanıcı** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/enterprise-managed/<ENTITY>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/enterprises/<ENTITY>/saml/consume`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://github.com/enterprises/<ENTITY>/sso`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [GitHub kurumsal yönetilen Kullanıcı istemci desteği ekibine](mailto:support@github.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **GitHub kurumsal yönetilen Kullanıcı ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, GitHub kurumsal yönetilen kullanıcısına erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **GitHub kurumsal yönetilen Kullanıcı**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-github-enterprise-managed-user-sso"></a>GitHub kurumsal yönetilen Kullanıcı SSO 'SU yapılandırma

**GitHub kurumsal yönetilen Kullanıcı** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' den [GitHub kurumsal yönetilen Kullanıcı desteği ekibine](mailto:support@github.com)doğru kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-github-enterprise-managed-user-test-user"></a>GitHub kurumsal yönetilen Kullanıcı test kullanıcısı oluştur

Bu bölümde, GitHub kurumsal yönetilen Kullanıcı ' da B. Simon adlı bir Kullanıcı oluşturulur. GitHub kurumsal yönetilen Kullanıcı, varsayılan olarak etkinleştirilen tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı GitHub kurumsal yönetilen Kullanıcı 'da zaten mevcut değilse, GitHub kurumsal yönetilen kullanıcısına erişmeye çalıştığınızda yeni bir tane oluşturulur.

GitHub kurumsal yönetilen Kullanıcı ayrıca otomatik Kullanıcı sağlamayı da destekler, otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](https://docs.microsoft.com/azure/active-directory/saas-apps/github-enterprise-managed-user-provisioning-tutorial) .

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz GitHub kurumsal yönetilen Kullanıcı oturum açma URL 'sine yeniden yönlendirilir.  

* GitHub kurumsal yönetilen Kullanıcı oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız GitHub kurumsal yönetilen kullanıcısına otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki GitHub kurumsal yönetilen Kullanıcı kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız GitHub kurumsal yönetilen kullanıcısına otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Sonraki adımlar

GitHub kurumsal yönetilen kullanıcısını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


