---
title: 'Öğretici: SAP Analytics bulutu ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAP Analytics bulutu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347790"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Öğretici: SAP Analytics bulutunu Azure Active Directory ile tümleştirme

Bu öğreticide SAP Analytics bulutunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SAP Analytics bulutu 'nı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SAP Analytics bulutuna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP Analytics bulutuna otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SAP Analytics bulutu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* SAP Analytics bulutu **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Galeriden SAP Analytics bulutu ekleme

SAP Analytics bulutu 'nın Azure AD ile tümleştirilmesini yapılandırmak için, galerisinden SAP Analytics bulutu 'nı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SAP Analytics bulutu** yazın.
1. Sonuçlar panelinden **SAP Analytics bulutu** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

**B. Simon**adlı bir test KULLANıCıSı kullanarak SAP Analytics bulutu Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, SAP Analytics bulutu 'ndaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SAP Analytics bulutu ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. **[SAP Analytics Cloud SSO 'Yu yapılandırma](#configure-sap-analytics-cloud-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. SAP Analytics **[Cloud test kullanıcısı oluşturun](#create-sap-analytics-cloud-test-user)** -bu, kullanıcının Azure AD gösterimine bağlı olan SAP Analytics bulutu 'nda B. Simon 'a karşılık gelen bir.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **SAP Analytics bulutu** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Bu URL 'Lerdeki değerler yalnızca tanıtım amaçlıdır. Değerleri, gerçek oturum açma URL 'SI ve tanımlayıcı URL 'SI ile güncelleştirin. Oturum açma URL 'sini almak için [SAP Analytics bulut istemci desteği ekibine](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)başvurun. Yönetim konsolundan SAP Analytics bulut meta verilerini indirerek tanımlayıcı URL 'sini alabilirsiniz. Bu, öğreticide daha sonra açıklanmaktadır.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. **SAP Analytics bulutu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics Cloud SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, SAP Analytics bulut şirket sitenizde yönetici olarak oturum açın.

2. **Menü** > sistemYönetimi > ' ni seçin.
    
    ![Menü, sistem ve ardından Yönetim ' i seçin](./media/sapboc-tutorial/config1.png)

3. **Güvenlik** sekmesinde **Düzenle** (kalem) simgesini seçin.
    
    ![Güvenlik sekmesinde, Düzenle simgesini seçin.](./media/sapboc-tutorial/config2.png)  

4. **Kimlik doğrulama yöntemi**Için **SAML çoklu oturum açma (SSO)** seçeneğini belirleyin.

    ![Kimlik doğrulama yöntemi için SAML çoklu oturum açma seçin](./media/sapboc-tutorial/config3.png)  

5. Hizmet sağlayıcısı meta verilerini indirmek için (1. adım), **İndir**' i seçin. Meta veri dosyasında **EntityId** değerini bulup kopyalayın. Azure portal, **temel SAML yapılandırması** iletişim kutusunda, değeri **tanımlayıcı** kutusuna yapıştırın.

    ![EntityId değerini kopyalayıp yapıştırın](./media/sapboc-tutorial/config4.png)  

6. Azure portal indirdiğiniz dosyadaki hizmet sağlayıcısı meta verilerini (2. adım) karşıya yüklemek için, **kimlik sağlayıcısı meta verilerini karşıya yükle**altında **karşıya yükle**' yi seçin.  

    ![Kimlik sağlayıcısı meta verilerini karşıya yükle altında Yükle ' yi seçin.](./media/sapboc-tutorial/config5.png)

7. **Kullanıcı özniteliği** listesinde, uygulamanız için kullanmak istediğiniz kullanıcı özniteliğini (3. adım) seçin. Bu kullanıcı özniteliği kimlik sağlayıcısına eşlenir. Kullanıcının sayfasına özel bir öznitelik girmek için **özel SAML eşleme** seçeneğini kullanın. Ya da Kullanıcı özniteliği olarak **e-posta** veya **Kullanıcı kimliği** ' ni seçebilirsiniz. Örneğimizde, Azure portal Kullanıcı **öznitelikleri & talepler** bölümündeki **userPrincipalName** özniteliğiyle Kullanıcı tanımlayıcısı talebini eşleştirdiğimiz için **e-posta** seçtik. Bu, her başarılı SAML yanıtında SAP Analytics bulut uygulamasına gönderilen benzersiz bir kullanıcı e-postası sağlar.

    ![Kullanıcı özniteliği seçin](./media/sapboc-tutorial/config6.png)

8. Kimlik sağlayıcısıyla hesabı doğrulamak için (4. adım), **oturum açma kimlik bilgileri (e-posta)** kutusuna kullanıcının e-posta adresini girin. Ardından, **hesabı doğrula**' yı seçin. Sistem, Kullanıcı hesabına oturum açma kimlik bilgilerini ekler.

    ![E-posta girin ve hesabı Doğrula ' yı seçin.](./media/sapboc-tutorial/config7.png)

9. **Kaydet** simgesini seçin.

    ![Kaydet simgesi](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, SAP Analytics bulutuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **SAP Analytics bulutu**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-sap-analytics-cloud-test-user"></a>SAP Analytics bulut testi Kullanıcı Oluştur

SAP Analytics bulutu 'nda oturum açabilmeniz için önce Azure AD kullanıcıları 'nın SAP Analytics bulutu 'nda sağlanması gerekir. SAP Analytics bulutu 'nda sağlama, el ile gerçekleştirilen bir görevdir.

Bir kullanıcı hesabı sağlamak için:

1. SAP Analytics bulut şirket sitenizde yönetici olarak oturum açın.

2. **Menü** > güvenliğikullanıcıları > ' nı seçin.

    ![Çalışan Ekle](./media/sapboc-tutorial/user1.png)

3. **Kullanıcılar** sayfasında, yeni kullanıcı ayrıntıları eklemek için öğesini seçin **+** . 

    ![Kullanıcı Ekle sayfası](./media/sapboc-tutorial/user4.png)

    Ardından, aşağıdaki adımları izleyin:

    a. **Kullanıcı kimliği** kutusuna kullanıcının kullanıcı kimliğini ( **B**gibi) girin.

    b. **Ilk ad** kutusuna, **B**gibi, kullanıcının adını girin.

    c. **Soyadı** kutusuna kullanıcının soyadı ( **Simon**gibi) girin.

    d. **Görünen ad** kutusuna, **B. Simon**gibi kullanıcının tam adını girin.

    e. **E-posta** kutusuna kullanıcının e-posta adresini girin, örneğin `b.simon@contoso.com`.

    f. **Rolleri seçin** sayfasında, Kullanıcı için uygun rolü seçin ve ardından **Tamam**' ı seçin.

      ![Rol seçin](./media/sapboc-tutorial/user3.png)

    g. **Kaydet** simgesini seçin.

### <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde SAP Analytics bulutu kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız SAP Analytics bulutu 'nda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

