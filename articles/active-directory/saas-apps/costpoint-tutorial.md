---
title: 'Öğretici: Costpoint ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Costpoint arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840075"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Öğretici: Costpoint 'i Azure Active Directory tümleştirin

Bu öğreticide, Costpoint 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Costpoint 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Costpoint 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla maliyette otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bir Costpoint çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Costpoint **SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="generate-costpoint-metadata"></a>Costpoint meta verileri oluştur

Costpoint SAML SSO yapılandırması **DeltekCostpoint711Security. PDF** kılavuzunda açıklanmıştır. Bu kılavuzu Deltek Costpoint destek sitesinden indirin ve **SAML çoklu oturum açma kurulumu** >  '**in, costpoint ve MICROSOFT Azure bölümü arasında SAML çoklu oturum açmayı yapılandırma** bölümüne bakın. Yönergeleri izleyin ve bir **Costpoint SP Federasyon meta VERI XML** dosyası oluşturun. 

![Costpoint yapılandırma yardımcı programı](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Galeriden Costpoint ekleyin

Costpoint 'i Azure AD ile tümleştirmek için, önce Azure portal Galeriden yönetilen SaaS uygulamaları listenize Costpoint ekleyin:

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.

1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.

   ![Azure Active Directory düğmesi](common/select-azuread.png)

1. **Tüm uygulamalar**@no__t **Kurumsal uygulamalar**' ı seçin.

   ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.

   ![Yeni uygulama düğmesi](common/add-new-app.png)

1. **Galeriden Ekle** bölümünde, arama kutusuna **costpoint** yazın.

   ![Sonuçlar listesinde costpoint](common/search-new-app.png)

1. Sonuçlar listesinde, **Costpoint**' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Azure AD Single SGN 'yi yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak, costpoint Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Costpoint ile ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Costpoint ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
1. Uygulama tarafında SAML SSO ayarlarını yapılandırmak için **[Costpoint 'ı yapılandırın](#configure-costpoint)** .
1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Kullanıcı Azure AD gösterimine bağlı olan Costpoint 'te B. Simon 'ya karşılık gelen bir **[costpoint test kullanıcısı oluşturun](#create-a-costpoint-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. **Costpoint** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

   ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

1. **Temel SAML yapılandırması** bölümünde, *hizmet sağlayıcısı meta verileri dosyanız*varsa, şu adımları izleyin:

   > [!NOTE]
   > Hizmet sağlayıcı meta veri dosyasını, [Costpoint meta verilerini oluşturma](#generate-costpoint-metadata)bölümünde alırsınız. Dosya kullanımı öğreticide daha sonra açıklanmaktadır.
 
   1. **Meta veri dosyasını karşıya yükle** düğmesini seçin, ardından daha önce costpoint tarafından oluşturulan **Costpoint SP Federasyon meta veri XML** dosyasını seçin ve ardından dosyayı karşıya yüklemek için **Ekle** düğmesini seçin.

      ![Meta veri dosyasını karşıya yükle](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Meta veri dosyası başarıyla karşıya yüklendiğinde, **tanımlayıcı** ve **yanıt URL 'Si** değerleri, costpoint bölümünde otomatik olarak doldurulur.

      > [!NOTE]
      > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak yoksa, gereksinimlerinize göre değerleri el ile girin. **Tanımlayıcının (VARLıK kimliği)** ve **yanıt URL 'Sinin (onaylama tüketici hizmeti URL 'si)** doğru ayarlandığını ve **ACS URL 'sinin** **/loginservlet.exe**ile biten geçerli bir costpoint URL olduğunu doğrulayın.

   1. **Ek URL 'Ler ayarla**' yı seçin. **Geçiş durumu**için şu stili kullanarak bir değer girin: `system=[your system]` (örneğin, **System = deltekcp**).

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** kopyalayıp Not defteri 'ne kaydetmek için **Kopyala** simgesini seçin.

   ![SAML Imzalama sertifikası](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Costpoint 'i yapılandırın

1. Costpoint yapılandırma yardımcı programına dönün. **IDP Federasyon meta VERILERI XML** metin kutusunda, *uygulama Federasyon meta verileri URL 'si* dosyasının içeriğini yapıştırın. 

   ![Costpoint yapılandırma yardımcı programı](./media/costpoint-tutorial/config-utility-idp.png)

1. Costpoint SAML kurulumunu son almak için **DeltekCostpoint711Security. PDF** kılavuzundaki yönergelere devam edin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, B. Simon adlı Azure portal bir test kullanıcısı oluşturmaktır.

1. Azure portal sol bölmedeki **Azure Active Directory** > **Kullanıcı** > **tüm kullanıcılar**' ı seçin.

   !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

1. **Yeni Kullanıcı**' yı seçin.

   ![Yeni Kullanıcı düğmesi](common/new-user.png)

1. **Kullanıcı** özellikleri ' nde şu adımları uygulayın:

   ![Kullanıcı iletişim kutusu](common/user-properties.png)

   1. **Ad** alanına **B. Simon**girin.
   
   1. **Kullanıcı adı** alanına `b.simon\@yourcompanydomain.extension` girin (örneğin, B.Simon@contoso.com).
   
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** alanında görüntülenen değeri yazın.
   
   1. **Oluştur**' u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Costpoint 'e B. Simon erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar** > **tüm uygulamalar**' ı seçin.

1. Uygulamalar listesinde, **Costpoint**' i seçin.

1. Uygulamanın genel bakış sayfasının **Yönet** bölümünde **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin. **Atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.

   ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinde, **B. Simon**' u seçin. Sonra **Seç**' i seçin.

1. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından **Seç**' i seçin.

1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-a-costpoint-test-user"></a>Costpoint test kullanıcısı oluşturma

Bu bölümde, Costpoint 'te bir Kullanıcı oluşturacaksınız. Kullanıcı kimliğinin **b. Simon** ve Kullanıcı adının **b. Simon**olduğunu varsayalım. Kullanıcı costpoint platformunda eklemek için [Costpoint istemci destek ekibi](https://www.deltek.com/about/contact-us) ile çalışın. Kullanıcının çoklu oturum açmayı kullanabilmesi için önce oluşturulması ve etkinleştirilmesi gerekir.

Kullanıcı oluşturulduktan sonra, kullanıcının **kimlik doğrulama yöntemi** seçimi **Active Directory**olmalı, **SAML çoklu oturum açma** onay kutusu seçilmelidir ve Azure Active Directory Kullanıcı adının Active Directory olması gerekir  **Sertifika KIMLIĞI** (aşağıdaki ekran görüntüsünde gösterilen).

![Costpoint kullanıcısı](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Costpoint kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız için, Costpoint uygulamasında otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticilerinin listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
