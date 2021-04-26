---
title: 'Öğretici: MVISION bulutu Azure AD SSO yapılandırmasıyla Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve MVISION bulutu Azure AD SSO yapılandırması arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: eaf6b2526125b13eec67680c292ed1dbae6fcfee
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284439"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Öğretici: MVISION bulutu Azure AD SSO yapılandırmasını Azure Active Directory ile tümleştirin

Bu öğreticide, MVISION bulutu Azure AD SSO yapılandırmasını Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. MVISION bulutu Azure AD SSO yapılandırmasını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, MVISION bulutu Azure AD SSO yapılandırmasına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Azure AD SSO yapılandırmasına yönelik olarak Azure AD SSO yapılandırması için otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* MVISION bulutu Azure AD SSO yapılandırması çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* MVISION bulutu Azure AD SSO yapılandırması **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Galeriden MVISION bulutu Azure AD SSO yapılandırması ekleme

MVISION bulutu Azure AD SSO yapılandırmasının Azure AD 'ye tümleştirilmesini yapılandırmak için, galerisindeki MVISION bulutu Azure AD SSO yapılandırmasını yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **MVision Cloud Azure AD SSO yapılandırması** yazın.
1. Sonuçlar panelinden **MVision Cloud Azure AD SSO yapılandırması** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-mvision-cloud-azure-ad-sso-configuration"></a>MVISION bulutu Azure AD SSO yapılandırması için Azure AD SSO 'yu yapılandırma ve test etme

**Britta Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'YU MVision bulutu Azure AD SSO yapılandırmasıyla yapılandırın ve test edin. SSO 'nun çalışması için, MVISION bulutu Azure AD SSO yapılandırmasındaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu MVISION bulutu Azure AD SSO yapılandırmasıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
1. **[MVISION bulutu Azure AD SSO yapılandırma SSO](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için yapılandırın.
    1. MVision **[bulutu Azure AD SSO yapılandırma test kullanıcısı](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** -kullanıcının Azure AD gösterimine bağlı MVision bulutu Azure AD SSO yapılandırmasında Britta Simon 'un bir karşılığı olacak şekilde.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **dataköpek** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [MVision bulutu Azure AD SSO yapılandırma istemci destek ekibine](mailto:support@skyhighnetworks.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

7. **MVISION bulutu 'Nı ayarla Azure AD SSO yapılandırma** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, MVISION Cloud Azure AD SSO yapılandırmasına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **MVision bulutu Azure AD SSO yapılandırması**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>MVISION bulutu Azure AD SSO yapılandırma SSO 'SU yapılandırma

**MVision bulutu Azure AD SSO yapılandırma** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (Base64)** ve Azure Portal ' dan [MVISION bulutu Azure AD SSO yapılandırma desteği ekibine](mailto:support@skyhighnetworks.com)uygun şekilde kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>MVISION bulutu Azure AD SSO yapılandırma test kullanıcısı oluşturma

Bu bölümde, MVISION bulutu Azure AD SSO yapılandırması 'nda B. Simon adlı bir Kullanıcı oluşturacaksınız. MVision Cloud Azure AD SSO yapılandırma platformunda kullanıcıları eklemek için [MVision Cloud Azure AD SSO yapılandırma destek ekibi](mailto:support@skyhighnetworks.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum akışını başlatabileceğiniz MVISION bulutu Azure AD SSO yapılandırma oturum açma URL 'sine yönlendirecektir.  

* MVISION Cloud Azure AD SSO yapılandırma oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'yu ayarladığınız MVision bulutu Azure AD SSO yapılandırmasında otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda MVISION Cloud Azure AD SSO yapılandırma kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız MVISION bulutu Azure AD SSO yapılandırmasında otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

MVISION Cloud Azure AD SSO yapılandırması 'nı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve içe/içe kullanımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
