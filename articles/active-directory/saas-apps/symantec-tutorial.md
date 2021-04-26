---
title: 'Öğretici: Symantec Web Security Service (WSS) ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Symantec Web Security Service (WSS) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: af7d126bfdc9ff8edf6b498747fab9c7f497a0f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484855"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Öğretici: Symantec Web Security Service (WSS) ile Azure Active Directory tümleştirme

Bu öğreticide, WSS 'nin SAML kimlik doğrulaması kullanarak Azure AD 'de sağlanan son kullanıcının kimliğini doğrulayabilmesi ve Kullanıcı veya grup düzeyi ilke kuralları zorlaması için Symantec Web Security Service (WSS) hesabınızı Azure Active Directory (Azure AD) hesabınızla tümleştirmeyi öğreneceksiniz.

Symantec Web Security Service 'i (WSS) Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

- WSS hesabınız tarafından Azure AD portalından kullanılan tüm son kullanıcıları ve grupları yönetin.

- Son kullanıcıların Azure AD kimlik bilgilerini kullanarak WSS 'de kendilerini kimlik doğrulamasına izin verin.

- WSS hesabınızda tanımlanan Kullanıcı ve Grup düzeyindeki ilke kurallarının uygulanmasını etkinleştirin.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Symantec Web Security Service (WSS) çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Symantec Web Security Service (WSS), **IDP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Galeriden Symantec Web Security Service (WSS) ekleme

Symantec Web Security hizmeti 'nin (WSS) Azure AD ile tümleştirilmesini yapılandırmak için, galerideki Symantec Web Security Service (WSS) öğesini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Symantec Web SECURITY Service (WSS)** yazın.
1. Sonuçlar panelinden **Symantec Web Security Service (WSS)** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Symantec Web Security Service (WSS) için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Symantec Web Security SERVICE (WSS) Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Symantec Web Security Service (WSS) içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Symantec Web Security Service (WSS) ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Symantec Web Security Service (WSS) SSO 'Yu yapılandırma](#configure-symantec-web-security-service-wss-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Symantec Web **[Security Service (WSS) test kullanıcısı oluşturun](#create-symantec-web-security-service-wss-test-user)** . Symantec Web Security Service 'TE (WSS), kullanıcının Azure AD gösterimine bağlı olan B. Simon 'a karşılık gelen bir.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal **Symantec Web Security Service (WSS)** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, aşağıdaki adımları uygulayın:

    a. **Tanımlayıcı** metin kutusuna URL 'yi yazın:`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. **Yanıt URL** 'si metin kutusuna URL 'yi yazın:`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > [Symantec Web Security Service (WSS) istemci desteği ekibine](https://www.symantec.com/contact-us) başvurun f **tanımlayıcı** için değerler ve **yanıt URL 'si** bazı nedenlerle çalışmıyor. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

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

Bu bölümde, Symantec Web Security Service (WSS) erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Symantec Web Security Service (WSS)** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-symantec-web-security-service-wss-sso"></a>Symantec Web Security Service (WSS) SSO 'yu yapılandırma

Symantec Web Security Service (WSS) tarafında çoklu oturum açmayı yapılandırmak için WSS çevrimiçi belgelerine bakın. İndirilen **Federasyon meta VERI XML** dosyası WSS portalına aktarılmalıdır. WSS portalındaki yapılandırmayla ilgili yardıma ihtiyacınız varsa [Symantec Web Security Service (WSS) destek ekibine](https://www.symantec.com/contact-us) başvurun.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Symantec Web Security Service (WSS) test kullanıcısı oluştur

Bu bölümde Symantec Web Security Service (WSS) ' de Britta Simon adlı bir Kullanıcı oluşturacaksınız. İlgili Son Kullanıcı adı, WSS portalında el ile oluşturulabilir veya Azure AD 'de sağlanan Kullanıcı/grupların, birkaç dakika sonra (yaklaşık 15 dakika) WSS portalı ile eşitlenmesi için bekleyebilirsiniz. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. Web sitelerine Gözatılacak olan Son Kullanıcı makinesinin genel IP adresi, Symantec Web Security Service (WSS) portalında da sağlanmalıdır.

> [!NOTE]
> Makinenizin genel IPAddress değerini almak için lütfen [buraya](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Symantec Web güvenlik hizmeti 'nde (WSS) otomatik olarak oturum açmış olmanız gerekir.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Symantec Web Security Service (WSS) kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Symantec Web Security Service (WSS) ' de otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

Symantec Web Security hizmeti 'ni (WSS) yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve içe/içe kullanımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
