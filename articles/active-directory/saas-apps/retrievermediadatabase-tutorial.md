---
title: 'Öğretici: Azure Active Directory ile çoklu oturum açma (SSO) tümleştirmesi. Microsoft Docs'
description: Azure Active Directory ve edinme ortambir veritabanı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 786925799d19bf01e8edebbf85de04d92213298b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732855"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-retrievermediadatabase"></a>Öğretici: yeniden alma Mediadatabase ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, edinme ve Mediadatabase 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Alma ve Azure AD ile birlikte tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, alma işlemi Mediadatabase 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte almak için kullanıcıların otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Alınan Mediadatabase çoklu oturum açma (SSO) aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Yeniden başlatılan Mediadatabase, **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-retrievermediadatabase-from-the-gallery"></a>Galeriden alma yaprak Mediadatabase ekleme

Alayaprak Mediadatabase 'in Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize alma yaprak Mediadatabase öğesini eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna alma **yaprak mediadatabase** yazın.
1. Sonuçlar panelinden alma ve uygulamayı ekleme ' **yi seçin.** Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-retrievermediadatabase"></a>Azure AD SSO 'yu alma ve deneme veritabanı için test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, Azure AD SSO 'yu, hazırlanan yaprak mediadatabase ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu alma ve kullanmaya test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[ınlationmediadatabase SSO 'Yu yapılandırın](#configure-retrievermediadatabase-sso)** .
    1. Kullanıcı Azure AD gösterimi ile bağlantılı olarak hazırlanan her bir B. Simon 'a sahip olmak için, bir alma işlemi **[mediadatabase test kullanıcısı oluşturun](#create-retrievermediadatabase-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, alma ve **ortam veritabanı** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulama önceden yapılandırılmıştır ve gerekli URL 'ler Azure ile önceden doldurulmuştur. Kullanıcının **Kaydet** düğmesine tıklayarak yapılandırmayı kaydetmesi gerekir.


1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalamak ve bilgisayarınıza kaydetmek için Kopyala düğmesine tıklayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)
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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak, kullanıma açık yaprak Mediadatabase 'e erişim vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, yeniden al. **Mediadatabase**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-retrievermediadatabase-sso"></a>Alma. Mediadatabase SSO 'yu yapılandırma

Alma. **mediadatabase** tarafında çoklu oturum açma 'yı yapılandırmak Için, **uygulama Federasyon meta veri URL 'sini** , ın, [mediadatabase destek ekibine](mailto:support@retriever.nl)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-retrievermediadatabase-test-user"></a>Alma yaprak Ortamveritabanı test kullanıcısı oluştur

Bu bölümde, alma alanı \ Kullanıcı olarak Britta Simon adlı bir Kullanıcı oluşturacaksınız. Alınan kullanıcıları, alma, Mediadatabase platformunda eklemek için, kullanmaya çalışan [mediadatabase destek ekibi](mailto:support@retriever.nl) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

1. Azure portal ' de bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız yeniden alma Mediamediadatabase ' de otomatik olarak oturum açmış olmanız gerekir

1. Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Alayaprak Mediadatabase kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız repiki Mediamediadatabase ' de otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Alımı her ne kadar yapılandırdığınızda, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).