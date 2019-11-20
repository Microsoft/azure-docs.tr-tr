---
title: 'Öğretici: Azure Active Directory Tümleştirmesi ile Sectigo Sertifika Yöneticisi | Microsoft Docs'
description: Azure Active Directory ve Sectigo Sertifika Yöneticisi arasında çoklu oturum açmayı yapılandırmayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588245"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Öğretici: Azure Active Directory Tümleştirmesi ile Sectigo Sertifika Yöneticisi

Bu öğreticide, Sectigo Sertifika Yöneticisi Azure Active Directory (Azure AD) ile tümleştirmeyi öğrenin.

Sectigo Sertifika Yöneticisi Azure AD ile tümleştirme, aşağıdaki avantajları sunar:

* Azure AD için Sertifika Yöneticisi Sectigo erişimi denetlemek için kullanabilirsiniz.
* Kullanıcıların otomatik olarak Sectigo Sertifika Yöneticisi için kendi Azure AD hesapları (çoklu oturum açma) ile oturum açmanız.
* Hesaplarınız bir merkezi konumda, Azure portalında yönetebilir.

Azure AD ile bir hizmet (SaaS) uygulamasını tümleştirme olarak yazılım hakkında daha fazla bilgi için bkz. [Azure Active Directory'de uygulamalar için çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesi Sectigo Sertifika Yöneticisi'yle yapılandırmak için aşağıdaki öğeler gerekir:

* Azure AD aboneliğiniz. Bir Azure AD aboneliğiniz yoksa, oluşturun bir [ücretsiz bir hesap](https://azure.microsoft.com/free/) başlamadan önce.
* Çoklu oturum etkin açma Sectigo Sertifika Yöneticisi abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açma bir test ortamında test edin ve Sectigo Sertifika Yöneticisi Azure AD ile tümleştirme yapılandırın.

Sectigo Sertifika Yöneticisi aşağıdaki özellikleri destekler:

* **SP tarafından başlatılan çoklu oturum açma**
* **IDP tarafından başlatılan çoklu oturum açma**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Azure portalında Sectigo Sertifika Yöneticisi Ekle

Sectigo Sertifika Yöneticisi Azure AD ile tümleştirmek için Sectigo Sertifika Yöneticisi yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) oturum açın.

1. Sol menüde **Azure Active Directory**.

    ![Azure Active Directory seçeneği](common/select-azuread.png)

1. Seçin **kurumsal uygulamalar** > **tüm uygulamaları**.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Bir uygulama eklemek için seçin **yeni uygulama**.

    ![Yeni uygulama seçeneği](common/add-new-app.png)

1. Arama kutusuna **Sectigo Sertifika Yöneticisi**. Arama sonuçlarında seçin **Sectigo Sertifika Yöneticisi**ve ardından **Ekle**.

    ![Sonuç listesinde Sectigo Sertifika Yöneticisi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, yapılandırma ve Azure AD çoklu oturum açma Sectigo sertifika adında bir test kullanıcı tabanlı Yöneticisi ile test etme **Britta Simon**. Tek iş için oturum açma için bir Azure AD kullanıcısı ile ilgili kullanıcı arasında bağlı bir ilişki Sectigo Sertifika Yöneticisi'nde oluşturmanız gerekir.

Yapılandırma ve Azure AD çoklu oturum açma Sectigo Sertifika Yöneticisi ile test etmek için aşağıdaki yapı taşlarını tamamlamanız gerekir:

| Görev | Açıklama |
| --- | --- |
| **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** | Bu özelliği kullanmak olanak sağlar. |
| **[Sertifika Yöneticisi'ni Sectigo çoklu oturum açmayı yapılandırın](#configure-sectigo-certificate-manager-single-sign-on)** | Uygulamada çoklu oturum açma ayarları yapılandırır. |
| **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)** | Testleri Azure AD çoklu oturum açma kullanıcı Britta Simon adı. |
| **[Azure AD test kullanıcısı atayın](#assign-the-azure-ad-test-user)** | Azure AD çoklu oturum açmayı kullanmak Britta Simon sağlar. |
| **[Sertifika Yöneticisi'ni Sectigo test kullanıcısı oluşturma](#create-a-sectigo-certificate-manager-test-user)** | Britta simon'un bir karşılığı Sectigo sertifika kullanıcı Azure AD gösterimini bağlı Yöneticisi'nde oluşturur. |
| **[Çoklu oturum açma testi](#test-single-sign-on)** | Yapılandırma çalıştığını doğrular. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure AD çoklu oturum açma Sectigo Sertifika Yöneticisi'yle Azure portalında yapılandırın.

1. İçinde [Azure portalında](https://portal.azure.com/), **Sectigo Sertifika Yöneticisi** uygulama tümleştirme bölmesinde **çoklu oturum açma**.

    ![Çoklu oturum açma seçeneği yapılandırın](common/select-sso.png)

1. İçinde **tek bir oturum açma yönteminizi seçmeniz** bölmesinde seçin **SAML** veya **SAML/WS-Federasyon** modu, çoklu oturum açmayı etkinleştirmek için.

    ![Çoklu oturum açma seçim modu](common/select-saml-option.png)

1. İçinde **yukarı çoklu oturum açma SAML ile ayarlayın** bölmesinde **Düzenle** (açmak için kalem simgesi) **temel SAML yapılandırma** bölmesi.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. İçinde **temel SAML yapılandırma** bölmesinde yapılandırmak için *IDP tarafından başlatılan modu*, aşağıdaki adımları tamamlayın:

    1. İçinde **tanımlayıcı** kutusuna, bu URL'ler birini girin:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. İçinde **yanıt URL'si** kutusuna, bu URL'ler birini girin:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Seçin **ek URL'lerini ayarlayın**.

    1. İçinde **geçiş durumu** kutusuna, bu URL'ler birini girin:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Oturum açma bilgileri çoklu Sectigo sertifika yöneticisi etki alanı ve URL'ler](common/idp-relay.png)

1.  Uygulamayı yapılandırmak için *SP tarafından başlatılan modu*, aşağıdaki adımları tamamlayın:

    * İçinde **oturum açma URL'si** kutusuna, bu URL'ler birini girin:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Oturum açma bilgileri çoklu Sectigo sertifika yöneticisi etki alanı ve URL'ler](common/both-signonurl.png)

1. İçinde **yukarı çoklu oturum açma SAML ile ayarlanmış** bölmesinde, **SAML imzalama sertifikası** bölümünden **indirme** yanındaki **sertifika (Base64)** . Gereksinimlerinize göre bir indirme seçeneğini seçin. Sertifika bilgisayarınıza kaydedin.

    ![Sertifika (Base64) yükleme seçeneği](common/certificatebase64.png)

1. İçinde **Sectigo Sertifika Yöneticisi'ni ayarlayın** bölümünde, gereksinimlerinize göre aşağıdaki URL'ler kopyalayın:

    * Oturum Açma URL'si:
    * Azure AD Tanımlayıcısı
    * Oturum Kapatma URL'si

    ![Yapılandırma URL'leri kopyalayın](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Sertifika Yöneticisi'ni Sectigo çoklu oturum açmayı yapılandırın

Çoklu oturum açma Sectigo Sertifika Yöneticisi taraftaki yapılandırmak için indirilen sertifika (Base64) dosya ve Azure için portaldan kopyaladığınız ilgili URL'leri Gönder [Sectigo Sertifika Yöneticisi Destek ekibine](https://sectigo.com/support). Sertifika Yöneticisi'ni Sectigo Destek ekibine oturum açma SAML tek bağlantısı her iki kenarı da düzgün ayarlandığından emin olmak için bunları gönderdiğiniz bilgileri kullanır.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma 

Bu bölümde, Azure portalında Britta Simon adlı bir test kullanıcısı oluşturun.

1. Azure portalında **Azure Active Directory** > **kullanıcılar** > **tüm kullanıcılar**.

    ![Kullanıcılar ve tüm kullanıcılar seçenekleri](common/users.png)

1. Seçin **yeni kullanıcı**.

    ![Yeni kullanıcı seçeneği](common/new-user.png)

1. İçinde **kullanıcı** bölmesinde, aşağıdaki adımları tamamlayın:

    1. İçinde **adı** kutusuna **BrittaSimon**.
  
    1. İçinde **kullanıcı adı** kutusuna **brittasimon\@\<your-şirket etki alanı >.\< Uzantı\>** . Örneğin, **brittasimon\@contoso.com**.

    1. Seçin **Show parola** onay kutusu. Görüntülenen değer azaltma **parola** kutusu.

    1. **Oluştur**’u seçin.

    ![Kullanıcı bölmesi](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Filiz Azure çoklu oturum açma kullanabilmeniz için bu bölümde, Britta Simon Sectigo Sertifika Yöneticisi için erişim.

1. Azure portalında **kurumsal uygulamalar** > **tüm uygulamaları** > **Sectigo Sertifika Yöneticisi**.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Uygulamalar listesinde **Sectigo Sertifika Yöneticisi**.

    ![Uygulamalar listesinde Sectigo Sertifika Yöneticisi](common/all-applications.png)

1. Menüde **kullanıcılar ve gruplar**.

    ![Kullanıcılar ve gruplar seçeneği](common/users-groups-blade.png)

1. Seçin **Kullanıcı Ekle**. Ardından **ataması ekleme** bölmesinde **kullanıcılar ve gruplar**.

    ![Ekle atama bölmesi](common/add-assign-user.png)

1. İçinde **kullanıcılar ve gruplar** bölmesinde **Britta Simon** kullanıcılar listesinde. **Seç**’i seçin.

1. SAML onaylaması rol değeri de beklediğiniz varsa **rol seçme** bölmesinde, listeden kullanıcı için uygun rolü seçin. **Seç**’i seçin.

1. İçinde **atama Ekle** bölmesinde **atama**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Sertifika Yöneticisi'ni Sectigo test kullanıcısı oluşturma

Bu bölümde, Britta Simon Sectigo Sertifika Yöneticisi'nde adlı bir kullanıcı oluşturun. Çalışmak [Sectigo Sertifika Yöneticisi Destek ekibine](https://sectigo.com/support) Sectigo Sertifika Yöneticisi platform kullanıcı eklemek için. Kullanıcı oluşturulmalı ve çoklu oturum açma kullanmadan önce etkinleştirildi.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, uygulamalarım portalını kullanarak Azure AD çoklu oturum açma yapılandırmanızı test.

Çoklu oturum açma, getirdiğinizde seçtiğinizde **Sectigo Sertifika Yöneticisi** uygulamalarım portalında, otomatik olarak Sectigo Sertifika Yöneticisi için oturum açtınız. Uygulamalarım portal hakkında daha fazla bilgi için bkz. [erişim ve kullanım uygulamaları uygulamalarım portalında](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bu makaleleri gözden geçirin:

- [İçin Azure Active Directory ile SaaS uygulamalarını tümleştirme konusundaki öğreticilerin listesine](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory'de uygulamalar için çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


