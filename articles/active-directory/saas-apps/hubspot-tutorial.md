---
title: 'Öğretici: HubSpot ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile HubSpot arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944443"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Öğretici: HubSpot ile tümleştirme Azure Active Directory

Bu öğreticide, Hubu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

Hubleke 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

* Azure AD 'yi, hub 'a kimlerin erişebileceğini denetlemek için kullanabilirsiniz.
* Kullanıcılar, Azure AD hesapları (çoklu oturum açma) ile HubG 'de otomatik olarak oturum açabilirler.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz Azure portal.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini HubSpot ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Çoklu oturum açma özelliği etkinleştirilmiş bir HubSpot aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edin ve Hubu Azure AD ile tümleştirin.

HubSpot aşağıdaki özellikleri destekler:

* **SP tarafından başlatılan çoklu oturum açma**
* **IDP-başlatılan çoklu oturum açma**

## <a name="add-hubspot-in-the-azure-portal"></a>Azure portal HubSpot ekleme

Hubleke 'yi Azure AD ile bütünleştirmek için, yönetilen SaaS uygulamaları listenize HubSpot eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) oturum açın.

1. Sol menüden **Azure Active Directory**' yi seçin.

    ![Azure Active Directory seçeneği](common/select-azuread.png)

1. **Kurumsal uygulamalar** > **tüm uygulamalar**' ı seçin.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Bir uygulama eklemek için **Yeni uygulama**' yı seçin.

    ![Yeni uygulama seçeneği](common/add-new-app.png)

1. Arama kutusuna **HubSpot**girin. Arama sonuçlarında, **HubSpot**' yı seçin ve ardından **Ekle**' yi seçin.

    ![Sonuç listesinde HubSpot](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına göre HubSpot ile yapılandırıp test edersiniz. Çoklu oturum açma için, hub 'da bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açmayı hub spot ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını tamamlamalısınız:

| Görev | Açıklama |
| --- | --- |
| **[Azure AD çoklu oturum açmayı yapılandırma](#configure-azure-ad-single-sign-on)** | Kullanıcılarınızın bu özelliği kullanmasına olanak sağlar. |
| **[HubSpot çoklu oturum açmayı yapılandırma](#configure-hubspot-single-sign-on)** | Uygulamadaki çoklu oturum açma ayarlarını yapılandırır. |
| **[Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)** | Britta Simon adlı Kullanıcı için Azure AD çoklu oturum açmayı sınar. |
| **[Azure AD test kullanıcısını atama](#assign-the-azure-ad-test-user)** | Azure AD çoklu oturum açma kullanımı için Britta Simon 'U sağlar. |
| **[HubSpot test kullanıcısı oluşturma](#create-a-hubspot-test-user)** | Kullanıcının Azure AD gösterimine bağlı olan hubo 'da Britta Simon 'ın bir karşılığı oluşturur. |
| **[Çoklu oturum açmayı sına](#test-single-sign-on)** | Yapılandırmanın çalıştığını doğrular. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure AD çoklu oturum açmayı Azure portal HubSpot ile yapılandırırsınız.

1. [Azure Portal](https://portal.azure.com/), **HubSpot** uygulama tümleştirme bölmesinde **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma seçeneğini yapılandırma](common/select-sso.png)

1. Çoklu oturum **açma yöntemi seçin** bölmesinde, çoklu oturum açmayı etkinleştirmek için **SAML** veya **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

1. **SAML Ile çoklu oturum açmayı ayarla** bölmesinde, **temel SAML yapılandırması** bölmesini açmak için **Düzenle** ' yi (kurşun kalem simgesi) seçin.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölmesinde, *IDP tarafından başlatılan modu*yapılandırmak için aşağıdaki adımları izleyin:

    1. **Tanımlayıcı** kutusuna şu düzene sahip bir URL girin: https:\//api.HubSpot.com/login-api/v1/SAML/Login?portalId =\<Customer ID\>.

    1. **Yanıt URL 'si** kutusuna şu düzene sahip bir URL girin: https:\//api.HubSpot.com/login-api/v1/SAML/ACS?portalId =\<Customer ID\>.

    ![HubSpot etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    > [!NOTE]
    > URL 'Leri biçimlendirmek için, Azure portal **temel SAML yapılandırması** bölmesinde gösterilen desenlere de başvurabilirsiniz.

1. Uygulamayı *SP tarafından başlatılan* modda yapılandırmak için:

    1. **Ek URL 'Ler ayarla**' yı seçin.

    1. **Oturum açma URL 'si** kutusuna **https:\//App.HubSpot.com/login**girin.

    ![Ek URL ayarla seçeneği](common/metadata-upload-additional-signon.png)

1. **SAML Ile çoklu oturum açmayı ayarla** bölmesinde, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** yanındaki **İndir** ' i seçin. Gereksinimlerinize göre bir indirme seçeneği belirleyin. Sertifikayı bilgisayarınıza kaydedin.

    ![Sertifika (base64) indirme seçeneği](common/certificatebase64.png)

1. **Hub kümesi ayarla** bölümünde, gereksinimlerinize göre aşağıdaki URL 'leri kopyalayın:

    * Oturum Açma URL'si:
    * Azure AD Tanımlayıcısı
    * Oturum Kapatma URL'si

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot çoklu oturum açmayı yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve HubSpot yönetici hesabınızda oturum açın.

1. Sayfanın sağ üst köşesindeki **Ayarlar** simgesini seçin.

    ![Hubworkıdaki ayarlar simgesi](./media/hubspot-tutorial/config1.png)

1. **Hesap Varsayılanları**' nı seçin.

    ![HubSpot 'daki hesap Varsayılanları seçeneği](./media/hubspot-tutorial/config2.png)

1. **Güvenlik** bölümüne ilerleyin ve sonra **Ayarla**' yı seçin.

    ![HubSpot 'da ayarla seçeneği](./media/hubspot-tutorial/config3.png)

1. **Çoklu oturum açma ayarla** bölümünde aşağıdaki adımları uygulayın:

    1. **Hedef kitle URl 'si (hizmet sağlayıcısı VARLıK kimliği)** kutusunda, değeri kopyalamak için **Kopyala** ' yı seçin. Azure portal, **temel SAML yapılandırması** bölmesinde, değeri **tanımlayıcı** kutusuna yapıştırın.

    1. **Oturum açma URl 'si, ACS, alıcı veya yeniden yönlendirme** kutusunda, değeri kopyalamak için **Kopyala** ' yı seçin. Azure portal, **temel SAML yapılandırması** bölmesinde, **yanıt URL 'si** kutusuna değeri yapıştırın.

    1. Hubworkıda, **kimlik sağlayıcısı tanımlayıcısı veya veren URL** kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısı** için değeri yapıştırın.

    1. Hubworkıda, **kimlik sağlayıcısı çoklu oturum açma URL 'si** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** için değeri yapıştırın.

    1. Windows Not defteri 'nde indirdiğiniz sertifika (base64) dosyasını açın. Dosyanın içeriğini seçin ve kopyalayın. Ardından, Hubin ' de **X. 509.440 sertifika** kutusuna yapıştırın.

    1. **Doğrula**' yı seçin.

        ![Hubın içinde çoklu oturum açma ayarlama bölümü](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure Portal > ,**Kullanıcılar**AzureActiveDirectory > **tüm kullanıcılar**' ı seçin.

    ![Kullanıcılar ve tüm kullanıcılar seçenekleri](common/users.png)

1. **Yeni Kullanıcı**' yı seçin.

    ![Yeni Kullanıcı seçeneği](common/new-user.png)

1. **Kullanıcı** bölmesinde, aşağıdaki adımları izleyin:

    1. **Ad** kutusuna **Brittasıon**yazın.
  
    1. **Kullanıcı adı** kutusuna, **Şirket içi etki alanı > brittasıon\@\<yazın.\< uzantı\>** . Örneğin, **\@brittasıon contoso.com**.

    1. **Parolayı göster** onay kutusunu seçin. **Parola** kutusunda görüntülenen değeri yazın.

    1. **Oluştur**’u seçin.

    ![Kullanıcı bölmesi](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Azure çoklu oturum açma 'yı kullanabilmesi için, Gsıtta, Gleke 'e erişim izni verirsiniz.

1. Azure Portal **Kurumsal uygulamalar** > **tüm uygulamalar** > **HubSpot**' ı seçin.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

1. Uygulamalar listesinde, **HubSpot**' ı seçin.

    ![Uygulamalar listesinde HubSpot](common/all-applications.png)

1. Menüsünde **Kullanıcılar ve gruplar**' ı seçin.

    ![Kullanıcılar ve gruplar seçeneği](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin. Sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** bölmesinde, Kullanıcı listesinde **Britta Simon** ' u seçin. **Seç**’i seçin.

1. SAML assertion 'da bir rol değeri bekliyorsanız, **Rol Seç** bölmesinde, kullanıcının listeden ilgili rolünü seçin. **Seç**’i seçin.

1. **Atama Ekle** bölmesinde **ata**' yı seçin.

### <a name="create-a-hubspot-test-user"></a>HubSpot test kullanıcısı oluşturma

Azure AD 'nin bir kullanıcının HubG 'de oturum açmasını sağlamak için, kullanıcının hubın içinde sağlanması gerekir. HubSpot 'da, sağlama el ile gerçekleştirilen bir görevdir.

Hubın içinde bir kullanıcı hesabı sağlamak için:

1. HubSpot şirket sitenizde yönetici olarak oturum açın.

1. Sayfanın sağ üst köşesindeki **Ayarlar** simgesini seçin.

    ![Hubworkıdaki ayarlar simgesi](./media/hubspot-tutorial/config1.png)

1. **Takımlar & kullanıcılar**' ı seçin.

    ![Hubın içindeki Kullanıcı & takımları seçeneği](./media/hubspot-tutorial/user1.png)

1. **Kullanıcı oluştur**' u seçin.

    ![HubSpot 'da kullanıcı oluştur seçeneği](./media/hubspot-tutorial/user2.png)

1. **E-posta addess 'leri Ekle** kutusunda, kullanıcının e-posta adresini brittasıon\@contoso.com biçiminde girin ve ardından **İleri**' yi seçin.

    ![Hubın kullanıcıları Oluştur bölümünde e-posta adresi ekle (es) kutusu](./media/hubspot-tutorial/user3.png)

1. **Kullanıcıları oluştur** bölümünde her bir sekmeyi seçin. Her sekmede, Kullanıcı için ilgili seçenekleri ve izinleri ayarlayın. Sonra **İleri**’yi seçin.

    ![Hubın kullanıcı oluştur bölümündeki sekmeler](./media/hubspot-tutorial/user4.png)

1. Daveti kullanıcıya göndermek için **Gönder**' i seçin.

    ![HubSpot 'daki gönder seçeneği](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Kullanıcı daveti kabul ettikten sonra Kullanıcı etkinleştirilir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı My Apps portalını kullanarak test edersiniz.

Çoklu oturum açmayı ayarladıktan sonra, My Apps portalında **Hubnokta** ' ı seçtiğinizde, otomatik olarak hubset oturumunuz açılır. Uygulamalarım portalı hakkında daha fazla bilgi için bkz. [Apps Portalındaki uygulamalara erişme ve bunları kullanma](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için şu makaleleri gözden geçirin:

- [SaaS uygulamalarını Azure Active Directory tümleştirme öğreticilerinin listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory uygulamalarda çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
