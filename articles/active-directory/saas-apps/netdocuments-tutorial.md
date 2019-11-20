---
title: 'Öğretici: NetDocuments ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve NetDocuments arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71e5e13485c4a10664d98363e8e99bfd3b4f4bcf
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035713"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Öğretici: NetDocuments ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, NetDocuments Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. NetDocuments 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de NetDocuments erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla NetDocuments 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* NetDocuments çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* NetDocuments **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-netdocuments-from-the-gallery"></a>Galeriden NetDocuments ekleme

NetDocuments 'un Azure AD ile tümleştirilmesini yapılandırmak için, galerideki yönetim SaaS uygulamaları listenize NetDocuments eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **NetDocuments** yazın.
1. Sonuçlar panelinden **NetDocuments** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>NetDocuments için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu NetDocuments ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve NetDocuments içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu NetDocuments ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[NetDocuments SSO 'Yu yapılandırma](#configure-netdocuments-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan NetDocuments 'ta B. Simon 'a sahip olmak için, **[NetDocuments test kullanıcısı oluşturun](#create-netdocuments-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **NetDocuments** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna şu kalıbı kullanarak bir URL yazın: `http://netdocuments.com/VAULT`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve yanıt URL 'SI ile güncelleştirin. Depo KIMLIĞI, **CA 'dan** ve ardından NetDocuments deponuz ile ilişkili 8 karakter kodundan başlayan bir değerdir. Daha fazla bilgi için [NetDocuments federal kimlik desteği belgesine](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) bakabilirsiniz. Alternatif olarak, yukarıdaki bilgileri kullanarak yapılandırma zorluklarla karşılaşıyorsanız, bu değerleri almak için [NetDocuments istemci destek ekibine](https://support.netdocuments.com/hc/) başvurabilirsiniz. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. NetDocuments uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName**ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. NetDocuments uygulaması, **NameIdentifier** 'ın **EmployeeID** ile veya **NameIdentifier**olarak kuruluşunuz için geçerli olan başka bir taleple eşlenmesini bekler, bu nedenle **düzenleme** simgesine tıklayarak öznitelik eşlemesini düzenlemeniz gerekir ve öznitelik eşlemesini değiştirin.

    ![image](common/edit-attribute.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **NetDocuments ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, NetDocuments 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **NetDocuments**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-netdocuments-sso"></a>NetDocuments SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, NetDocuments şirket sitenizde yönetici olarak oturum açın.

2. **Yönetici**'ye gidin.

3. **Kullanıcıları ve grupları Ekle ve Kaldır**' a tıklayın.
   
    ![Depo](./media/netdocuments-tutorial/ic795047.png "deposu")

4. **Gelişmiş kimlik doğrulama seçeneklerini yapılandır**seçeneğine tıklayın.
    
    ![Gelişmiş kimlik doğrulama seçeneklerini yapılandırma](./media/netdocuments-tutorial/ic795048.png "Gelişmiş kimlik doğrulama seçeneklerini yapılandırma")

5. **Federal Kimlik** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
    ![Federal Kimlik](./media/netdocuments-tutorial/ic795049.png "Federal kimliği")
   
    a. **Federal Kimlik sunucu türü**olarak **Active Directory Federasyon Hizmetleri (AD FS)** ' yi seçin.
   
    b. Azure portal 'ten indirdiğiniz indirilen meta veri dosyasını karşıya yüklemek için **Dosya Seç**' e tıklayın.
   
    c. **Tamam**’a tıklayın.

### <a name="create-netdocuments-test-user"></a>NetDocuments test kullanıcısı oluştur

Azure AD kullanıcılarının NetDocuments 'ta oturum açmasını sağlamak için bunların NetDocuments içinde sağlanması gerekir.  
NetDocuments durumunda sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **NetDocuments** şirket sitenizde yönetici olarak oturum açın.

2. Üstteki menüden **yönetici**' ye tıklayın.
   
    ![Yönetici](./media/netdocuments-tutorial/ic795051.png "Yöneticisi")

3. **Kullanıcıları ve grupları Ekle ve Kaldır**' a tıklayın.
   
    ![Depo](./media/netdocuments-tutorial/ic795047.png "deposu")

4. **E-posta adresi** metin kutusuna, sağlamak istediğiniz geçerli bir Azure Active Directory hesabının e-posta adresini yazın ve ardından **Kullanıcı Ekle**' ye tıklayın.
   
    ![E-posta adresi](./media/netdocuments-tutorial/ic795053.png "eposta adresi")
   
    >[!NOTE]
    >Azure Active Directory hesap sahibi, hesabı etkin olmadan önce onaylamaya yönelik bir bağlantı içeren bir e-posta alır. Azure Active Directory Kullanıcı hesapları sağlamak için NetDocuments tarafından sunulan diğer tüm NetDocuments Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde NetDocuments kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız NetDocuments 'ta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile NetDocuments kullanmayı deneyin](https://aad.portal.azure.com/)

