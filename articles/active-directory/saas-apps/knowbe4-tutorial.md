---
title: 'Öğretici: KnowBe4 güvenlik tanıma eğitimi ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve KnowBe4 güvenlik tanıma eğitimi arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: jeedes
ms.openlocfilehash: 77d810fc8327e3777102f2a09f0ca32835aad4b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180773"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Öğretici: KnowBe4 güvenlik tanıma eğitimi ile Azure Active Directory tümleştirme

Bu öğreticide, KnowBe4 güvenlik tanıma eğitimini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
KnowBe4 güvenlik tanıma eğitimini Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* KnowBe4 güvenlik tanıma eğitimine erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla KnowBe4 güvenlik tanıma eğitimine (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini KnowBe4 güvenlik tanıma eğitiminde yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* KnowBe4 güvenlik tanıma Eğitimi çoklu oturum açma özellikli abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* KnowBe4 güvenlik tanıma Eğitimi **SP** tarafından başlatılan SSO 'yu destekler

* KnowBe4 güvenlik tanıma Eğitimi **, tam zamanında** Kullanıcı sağlamayı destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-knowbe4-from-the-gallery"></a>Galeriden KnowBe4 ekleme

KnowBe4 tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden KnowBe4 yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **KnowBe4** yazın.
1. Sonuçlar panelinden **KnowBe4** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre KnowBe4 ile yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve KnowBe4 'deki ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı KnowBe4 ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD SSO 'yu, Britta Simon ile test etmek için Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    * Azure AD **[Test kullanıcısına,](#assign-the-azure-ad-test-user)** Azure AD SSO 'yu kullanmak Için Britta Simon 'u etkinleştirmek üzere atayın.
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[KnowBe4 güvenlik tanıma EĞITIMI SSO 'Yu yapılandırın](#configure-knowbe4-security-awareness-training-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan KnowBe4 güvenlik tanıma eğitiminde Britta Simon 'a sahip olmak için **[KnowBe4 güvenlik tanıma Eğitimi test kullanıcısı oluşturun](#create-knowbe4-security-awareness-training-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **KnowBe4** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > Oturum açma URL 'SI değeri gerçek değil. Bu değeri, gerçek oturum açma URL 'siyle güncelleştirin. Bu değeri almak için [KnowBe4 güvenlik tanıma Eğitimi istemci destek ekibine](mailto:support@KnowBe4.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen düzene de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (ham)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

6. **KnowBe4 güvenlik tanımayı ayarlama eğitimi** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, KnowBe4 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **KnowBe4**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-knowbe4-security-awareness-training-sso"></a>KnowBe4 güvenlik tanıma Eğitimi SSO 'yu yapılandırma

**KnowBe4 güvenlik tanıma eğitim** tarafında çoklu oturum açma 'yı yapılandırmak için, indirilen **sertifikayı (ham)** ve Azure Portal ' den uygun kopyalanmış URL 'Leri [KnowBe4 güvenlik tanıma Eğitimi destek ekibine](mailto:support@KnowBe4.com)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>KnowBe4 güvenlik tanıma Eğitimi test kullanıcısı oluşturma

Bu bölümde, KnowBe4 içinde Britta Simon adlı bir Kullanıcı oluşturulur. KnowBe4, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı KnowBe4 içinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

1. Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz KnowBe4 oturum açma URL 'sine yeniden yönlendirilir. 

2. Doğrudan KnowBe4 oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

3. Microsoft Access panel ' i kullanabilirsiniz. Erişim panelinde KnowBe4 kutucuğuna tıkladığınızda bu, KnowBe4 oturum açma URL 'sine yeniden yönlendirilir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

KnowBe4 yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).