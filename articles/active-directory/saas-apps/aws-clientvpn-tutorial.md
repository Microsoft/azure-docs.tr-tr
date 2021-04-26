---
title: 'Öğretici: AWS ClientVPN ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve AWS ClientVPN arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/29/2020
ms.author: jeedes
ms.openlocfilehash: 794934e9c3e45f2aeed9310636a0f3ec30daf7de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735354"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-clientvpn"></a>Öğretici: AWS ClientVPN ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, AWS ClientVPN 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. AWS ClientVPN 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de AWS ClientVPN erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla AWS ClientVPN 'ye otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* AWS ClientVPN çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* AWS ClientVPN, **SP** tarafından başlatılan SSO 'yu destekler

* AWS ClientVPN **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-aws-clientvpn-from-the-gallery"></a>Galeriden AWS ClientVPN ekleme

AWS ClientVPN tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden AWS ClientVPN ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **AWS clientvpn** yazın.
1. Sonuçlar panelinden **AWS ClientVPN** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-aws-clientvpn"></a>AWS ClientVPN için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak AWS clientvpn Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, AWS ClientVPN içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

AWS ClientVPN ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[AWS ClientVPN SSO 'Yu yapılandırma](#configure-aws-clientvpn-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. AWS **[clientvpn test kullanıcısı oluşturun](#create-aws-clientvpn-test-user)** -kullanıcının Azure AD gösterimine bağlı olan AWS clientvpn 'de B. Simon 'a karşılık gelen bir karşılığı vardır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **AWS ClientVPN** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<LOCALHOST>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

    | Yanıt URL'si |
    |------------|
    | `http://<LOCALHOST>` |
    | `https://self-service.clientvpn.amazonaws.com/api/auth/sso/saml` |
    |

    > [!NOTE]
    > Bu değerler gerçek değildir.  Bu değerleri, gerçek oturum açma URL 'SI ve yanıt URL 'SI ile güncelleştirin.  Oturum açma URL 'SI ve yanıt URL 'SI aynı değere sahip olabilir ( http://127.0.0.1:35001) .  Ayrıntılar için [AWS ISTEMCISI VPN belgelerine](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/client-authentication.html#ad) başvurun.   Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz. Tüm yapılandırma sorunları için [AWS ClientVPN destek ekibine](https://aws.amazon.com/contact-us/) başvurun. 

1. Azure Active Directory hizmetinde **uygulama kayıtları** ' a gidin ve **tüm uygulamalar**' ı seçin.

1. Arama kutusuna **AWS clientvpn** yazın ve arama panelinden **AWS clientvpn** ' yi seçin.

1. **Bildirim** ' e tıklayın ve tümleştirme çalışmasını sağlamak IÇIN yanıt URL 'sini **https** yerine **http** olarak tutmanız gerekir, **Kaydet**' e tıklayın.

    ![bildirim sayfası](./media/aws-clientvpn-tutorial/reply-url.png)

1. AWS ClientVPN uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. AWS ClientVPN uygulaması, yukarıdakine ek olarak aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name |  Kaynak özniteliği|
    | -------------- | --------- |
    | Üyesi | Kullanıcı. gruplar |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **AWS ClientVPN ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, AWS ClientVPN 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **AWS ClientVPN**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-aws-clientvpn-sso"></a>AWS ClientVPN SSO 'yu yapılandırma

**AWS clientvpn** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal [AWS clientvpn destek ekibine](https://aws.amazon.com/contact-us/)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-aws-clientvpn-test-user"></a>AWS ClientVPN test kullanıcısı oluştur

Bu bölümde, AWS ClientVPN 'de Britta Simon adlı bir Kullanıcı oluşturulur. AWS ClientVPN, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. AWS ClientVPN 'de zaten bir kullanıcı yoksa, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz AWS ClientVPN oturum açma URL 'sine yeniden yönlendirilir. 

* AWS ClientVPN oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki AWS ClientVPN kutucuğuna tıkladığınızda bu, AWS ClientVPN oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

AWS ClientVPN 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).