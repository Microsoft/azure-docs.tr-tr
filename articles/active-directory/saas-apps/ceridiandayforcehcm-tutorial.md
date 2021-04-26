---
title: 'Öğretici: Ceriyen Dayzorla HCM ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Ceriyen Dayzorlamalı HCM arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 1f2e01a79f980e63102bb6538859f0da30c555c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651856"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Öğretici: Ceriyen Dayzorlamalı HCM ile tümleştirme Azure Active Directory

Bu öğreticide, Ceriyen Dayzorlamalı HCM 'yi Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Ceriyen Dayzorlamalı HCM 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Ceriyen Dayzorlamalı HCM erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Ceriyen Dayto HCM 'ye otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Ceriyen Dayon HCM çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Ceriyen Dayzorlamalı HCM, **SP** tarafından başlatılan SSO 'yu destekler

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Galeriden Ceriyen Dayzorlamalı HCM ekleyin

Ceriyen Dayzorlamalı HCM tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeriden Ceriyen Dayzorlamalı HCM ' i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Ceriyen DAYZORLAMALı HCM** yazın.
1. Sonuçlar panelinden **Ceriyen Dayzorlamalı HCM** ' yi seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Sertifika için Azure AD SSO 'yu yapılandırma ve test etme Dayiyen HCM

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Ceriyen DAYZORLAMALı HCM ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Ceriyen Dayzorlamalı HCM içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Ceriyen Dayzorlamalı HCM ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Ceriyen Dayzorlamalı HCM SSO 'Yu yapılandırın](#configure-ceridian-dayforce-hcm-sso)** .
    1. Kullanıcı Azure AD gösterimi ile bağlantılı olan Seriyen Dayon HCM 'de B. Simon 'a sahip olmak için, **[Ceriyen DAYZORLAMALı HCM test kullanıcısı oluşturun](#create-ceridian-dayforce-hcm-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma 

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Ceriyen Dayzorlamalı HCM** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Ceriyen dayı HCM etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, kullanıcılarınız tarafından, Seriyen DAYı HCM uygulamanızda oturum açmak için kullanılan URL 'yi yazın.

    | Ortam | URL |
    | :-- | :-- |
    | Üretim için | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Test için | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak URL 'yi yazın:

    | Ortam | URL |
    | :-- | :-- |
    | Üretim için | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Test için | `https://fs-test.dayforcehcm.com/sp` |

    c. Yanıt **URL** metin kutusuna, yanıtı göndermek IÇIN Azure AD tarafından kullanılan URL 'yi yazın.

    | Ortam | URL |
    | :-- | :-- |
    | Üretim için | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Test için | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Bu değerleri almak için [Ceriyen DAYYORHCM istemci desteği ekibine](https://www.ceridian.com/support) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. Ceriyen Dayzorlamalı HCM uygulaması, SAML onaylamalarını belirli bir biçimde bekliyor. Bu uygulama için aşağıdaki talepleri yapılandırın. Bu özniteliklerin değerlerini, uygulama tümleştirme sayfasındaki **Kullanıcı öznitelikleri** bölümünden yönetebilirsiniz. **SAML Ile tek Sign-On ayarlama** sayfasında, **Kullanıcı öznitelikleri** Iletişim kutusunu açmak için **Düzenle** düğmesine tıklayın.

    ![Ekran görüntüsü, düzenleme simgesi seçili olan kullanıcı özniteliklerini gösterir.](common/edit-attribute.png)

6. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, YUKARıDAKI görüntüde gösterildiği gibi SAML belirteci özniteliğini yapılandırın ve aşağıdaki adımları gerçekleştirin:

    | Name | Kaynak özniteliği|
    | ---------| --------- |
    | name  | User. ExtensionAttribute2 |

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.

    ![Ekran görüntüsü, yeni talep ekleme seçeneğiyle Kullanıcı taleplerini gösterir.](common/new-save-attribute.png)

    ![Ekran görüntüsü, açıklanan değerleri girebileceğiniz Kullanıcı taleplerini Yönet iletişim kutusunu gösterir.](common/new-attribute-details.png)

    b. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.

    c. **Ad alanını** boş bırakın.

    d. **Öznitelik** olarak kaynak seçin.

    e. **Kaynak özniteliği** listesinden, uygulamanız için kullanmak istediğiniz kullanıcı özniteliğini seçin. Örneğin, EmployeeID 'yi benzersiz kullanıcı tanımlayıcısı olarak kullanmak istiyorsanız ve öznitelik değerini ExtensionAttribute2 içinde depoladıysanız User. ExtensionAttribute2 ' yi seçin.

    f. **Tamam 'a** tıklayın

    örneğin: **Kaydet**’e tıklayın.

7. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

8. **Ceriyen Dayzorlamalı HCM 'Yi ayarlama** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, Seriyen Dayzorla HCM erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Ceriyen Dayzorlamalı HCM**' yi seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Ceriyen Dayzorlamalı HCM SSO 'yu yapılandırma

**Ceriyen Dayzorlamalı HCM** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **meta veri XML** 'Sini ve uygun kopyalanmış URL 'Leri Azure Portal ' den [ceriyen dayzorlamalı HCM destek ekibine](https://www.ceridian.com/support)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Ceriyen Dayzorlamalı HCM test kullanıcısı oluşturma

Bu bölümde, Ceriyen Dayon HCM 'de Britta Simon adlı bir Kullanıcı oluşturacaksınız. Ceriyen dayzorlamalı HCM platformunda Kullanıcı eklemek için [Ceriyen DAYZORLAMALı HCM ' i destek ekibi](https://www.ceridian.com/support) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Ceriyen Dayrcm oturum açma URL 'sine yeniden yönlendirilir. 

* Ceriyen Dayto HCM oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Ceriyen Dayzorla HCM kutucuğuna tıkladığınızda, bu, Ceriyen Dayrcm oturum açma URL 'sine yönlendirecektir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Ceriyen Dayenforce HCM 'yi yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve içeri ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).