---
title: 'Öğretici: Lucidchart ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Lucidchart arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5d5b07e761d5ed38cb2083054708265189bdd72f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651593"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Öğretici: Lucidchart ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Lucidchart 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Lucidchart 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Lucidchart 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Lucidchart otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Lucidchart çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Lucidchart **SP** tarafından başlatılan SSO 'yu destekler
* Lucidchart **, tam zamanında** Kullanıcı sağlamayı destekler

## <a name="add-lucidchart-from-the-gallery"></a>Galeriden Lucidchart ekleme

Lucidchart tümleştirmesini Azure AD 'ye göre yapılandırmak için, Galeriden Lucidchart yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Lucidchart** yazın.
1. Sonuçlar panelinden **Lucidchart** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Lucidchart için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu Lucidchart ile yapılandırın ve test edin. SSO 'nun çalışması için, Lucidchart içinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Lucidchart ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[LUCIDCHART SSO 'Yu yapılandırın](#configure-lucidchart-sso)** .
    * Kullanıcının Azure AD gösterimine bağlı olan Lucidchart 'de B. Simon 'ya karşılık gelen bir **[Lucidchart test kullanıcısı oluşturun](#create-lucidchart-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Lucidchart** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

   **Oturum açma URL 'si** metin kutusuna URL 'yi şu şekilde yazın:`https://chart2.office.lucidchart.com/saml/sso/azure`

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **Lucidchart ayarla** bölümünde, uygun URL 'leri gereksiniminize göre kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    
### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD test kullanıcısını atama

Bu bölümde, Lucidchart 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Lucidchart**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-lucidchart-sso&quot;></a>Lucidchart SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Lucidchart şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüde **ekip**' e tıklayın.

    ![Team](./media/lucidchart-tutorial/ic791190.png &quot;Takım") (Takım)

3. **Uygulamalar \> SAML 'yi Yönet**' e tıklayın.

    ![SAML 'yi Yönet](./media/lucidchart-tutorial/ic791191.png "SAML 'yi Yönet")

4. **SAML kimlik doğrulama ayarları** iletişim sayfasında, aşağıdaki adımları uygulayın:

    a. **SAML kimlik doğrulamasını etkinleştir**' i seçin ve **isteğe bağlı**' ya tıklayın.

    ![SAML kimlik doğrulama ayarları](./media/lucidchart-tutorial/ic791192.png "SAML kimlik doğrulama ayarları")

    b. **Etki alanı** metin kutusuna etki alanınızı yazın ve **Sertifikayı Değiştir**' e tıklayın.

    ![Sertifikayı Değiştir](./media/lucidchart-tutorial/ic791193.png "Sertifikayı Değiştir")

    c. İndirilen meta veri dosyanızı açın, içeriği kopyalayın ve ardından **verileri karşıya yükle** metin kutusuna yapıştırın.

    ![Meta verileri karşıya yükle](./media/lucidchart-tutorial/ic791194.png "Meta verileri karşıya yükle")

    d. **Takıma otomatik olarak yeni Kullanıcı Ekle**' yi seçin ve ardından **Değişiklikleri Kaydet**' e tıklayın.

    ![Değişiklikleri Kaydet](./media/lucidchart-tutorial/ic791195.png "Değişiklikleri Kaydet")

### <a name="create-lucidchart-test-user"></a>Lucidchart test kullanıcısı oluştur

Kullanıcı sağlamayı Lucidchart olarak yapılandırabilmeniz için herhangi bir eylem öğesi yoktur.  Atanan bir Kullanıcı erişim panelini kullanarak Lucidchart oturum açmaya çalıştığında, Lucidchart kullanıcının mevcut olup olmadığını denetler.  

Henüz kullanılabilir bir kullanıcı hesabı yoksa, Lucidchart tarafından otomatik olarak oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Lucidchart oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan Lucidchart oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Lucidchart kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Lucidchart için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

 Lucidchart 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimleri uygulayabilir. Oturum denetimleri koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)