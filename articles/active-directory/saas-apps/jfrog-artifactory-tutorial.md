---
title: 'Öğretici: JFrog Artifactory ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve JFrog Artifactory arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: b943be684d84e1e193d9318e9f1c6423dcd38795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648960"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Öğretici: JFrog Artifactory 'yi Azure Active Directory tümleştirme

Bu öğreticide, JFrog Artifactory 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. JFrog Artifactory 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de JFrog Artifactory erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla JFrog Artifactory ' de otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* JFrog Artifactory çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* JFrog Artifactory **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.
* JFrog Artifactory **, tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Galeriden JFrog Artifactory ekleme

JFrog Artifactory 'nin tümleştirmesini Azure AD 'ye göre yapılandırmak için galerideki JFrog Artifactory 'yi yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **JFrog Artifactory** yazın.
1. Sonuçlar panelinden **JFrog Artifactory** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>JFrog Artifactory için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak JFrog Artifactory Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve JFrog Artifactory içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu JFrog Artifactory ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[JFrog Artifactory SSO 'Yu yapılandırın](#configure-jfrog-artifactory-sso)** .
    1. **[JFrog Artifactory test kullanıcısı oluşturun](#create-jfrog-artifactory-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan JFrog Artifactory 'de B. Simon 'a karşılık gelen bir değere sahip olmalıdır.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **JFrog Artifactory** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`<servername>.jfrog.io`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:
    
    - Artifactory 6. x için: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Artifactory 7. x için: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:
    - Artifactory 6. x için: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Artifactory 7. x için: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [JFrog Artifactory istemci destek ekibine](https://support.jfrog.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. JFrog Artifactory uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Ekran görüntüsü, düzenleme denetimi olarak adlandırılan Kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

1. Yukarıdaki ' a ek olarak, JFrog Artifactory, bir dizi ek özniteliğin SAML yanıtına geri geçirilmesini bekler. **Grup talepleri (Önizleme)** Iletişim kutusundaki **Kullanıcı öznitelikleri & talepler** bölümünde aşağıdaki adımları uygulayın:

    a. **Talepte döndürülen gruplar ' ın** yanındaki **kaleme** tıklayın.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan talepleri & Kullanıcı özniteliklerini gösterir.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Ekran görüntüsü tüm gruplar seçili olan grup talepleri bölümünü gösterir.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Radyo listesinden **tüm gruplar** ' ı seçin.

    c. **Kaydet**’e tıklayın.

4. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, sertifikayı Indirip bilgisayarınıza kaydetmek Için sertifikayı bulun **(base64)** ve **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Artifactory 'yi (SAML hizmeti sağlayıcısı adı) ' tanımlayıcı ' alanıyla yapılandırın (bkz. 4. adım). **JFrog Artifactory 'Yi ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   - Artifactory 6. x için: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Artifactory 7. x için: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

Bu bölümde, JFrog Artifactory 'ye erişim vererek, B. Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **JFrog Artifactory**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-jfrog-artifactory-sso"></a>JFrog Artifactory SSO 'yu yapılandırma

**JFrog Artifactory** tarafında çoklu oturum açmayı yapılandırmak için, indirilen **sertifikayı (ham)** ve Azure Portal ' den [JFrog Artifactory destek ekibine](https://support.jfrog.com)doğru kopyalanmış URL 'leri göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-jfrog-artifactory-test-user"></a>JFrog Artifactory test kullanıcısı oluşturma

Bu bölümde, JFrog Artifactory içinde B. Simon adlı bir Kullanıcı oluşturulur. JFrog Artifactory, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı JFrog Artifactory 'de zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz JFrog Artifactory oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan JFrog Artifactory oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız JFrog Artifactory ' de otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki JFrog Artifactory kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız JFrog Artifactory ' de otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

JFrog Artifactory 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).