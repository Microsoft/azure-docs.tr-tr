---
title: 'Öğretici: Palo Alto ağları ile tümleştirme portalı ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ile Palo Alto ağları arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160171"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Öğretici: Palo Alto Networks ile tümleştirme portalı Azure Active Directory tümleştirme

Bu öğreticide, Palo Alto ağlarını Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.

Azure AD ile Palo Alto Networks barındırması portalını tümleştirdiğinizde aşağıdaki avantajları elde edersiniz:

* Azure AD 'de, Palo Alto Networks ile ilgilenen portala kimlerin erişebileceğini kontrol edebilirsiniz.
* Kullanıcı Azure AD hesapları 'nı kullanarak Palo Alto ağlarındaki ağlarda (çoklu oturum açma) kullanıcıları otomatik olarak oturum açabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda (Azure portal) yönetebilirsiniz.

Azure AD ile hizmet olarak yazılım (SaaS) uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD 'yi Palo Alto ağları açıklamalı portalı ile tümleştirmek için aşağıdaki öğeler gereklidir:

* Azure Active Directory aboneliği. Azure AD yoksa, bir [aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)alabilirsiniz.
* Bir Palo Alto Networks-yerleşik Portal çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

Palo Alto ağları açıklamalı portalı şu senaryoları destekler:

* **IDP-başlatılan çoklu oturum açma**
* **Tam zamanında Kullanıcı hazırlama**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Galerideki Palo Alto ağları giriş portalı ekleme

Başlamak için, galeride, yönetilen SaaS uygulamaları listenize Palo Alto ağları captive portalını ekleyin:

1. [Azure Portal](https://portal.azure.com)sol menüsünde **Azure Active Directory**' i seçin.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Tüm uygulamalar** > **Kurumsal uygulamalar** ' ı seçin.

    ![Menüdeki kurumsal uygulamalar seçeneği](common/enterprise-applications.png)

3. **Yeni uygulama**’yı seçin.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna, **Palo Alto ağları açıklamalı portalı**girin. Arama sonuçlarında, **Palo Alto Networks-Captive Portal**' ı seçin ve ardından **Ekle**' yi seçin.

     ![Sonuçlar listesinde Palo Alto Networks-captive portalı](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

*Britta Simon*adlı bir test kullanıcısına bağlı olarak, Palo Alto Networks Ile Azure AD çoklu oturum açma 'yı yapılandırıp test edersiniz. Çoklu oturum açma için, Palo Alto Networks Portal 'da bir Azure AD kullanıcısı ile aynı kullanıcı arasında bir ilişki kurmanız gerekir. 

Azure AD çoklu oturum açmayı, Palo Alto Networks ile birlikte yapılandırmak ve test etmek için aşağıdaki görevleri doldurun:

1. **[Azure AD çoklu oturum açmayı yapılandırma](#configure-azure-ad-single-sign-on)** : kullanıcının bu özelliği kullanmasını etkinleştirin.
2. **[Palo Alto ağlarını yapılandırma açıklamalı portalı çoklu oturum açma](#configure-palo-alto-networks-captive-portal-single-sign-on)** : uygulamadaki çoklu oturum açma ayarlarını yapılandırın.
3. **[Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** : Kullanıcı Ile Azure AD çoklu oturum açmayı, *Britta Simon*ile test edin.
4. **[Azure AD test kullanıcısına atama](#assign-the-azure-ad-test-user)** : Azure AD çoklu oturum açma kullanmak Için Britta Simon 'u ayarlayın.
5. **Bir Palo Alto ağı oluşturma giriş portalı test kullanıcısı**: Azure AD kullanıcısına bağlı olan, Palo Alto Networks *Şirket* portalı 'nda bir karşılık gelen kullanıcı oluşturur.
6. **[Çoklu oturum açmayı sına](#test-single-sign-on)** : yapılandırmanın çalıştığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

İlk olarak, Azure portal Azure AD çoklu oturum açmayı etkinleştirin:

1. [Azure Portal](https://portal.azure.com/), **Palo Alto Networks-captive portalı** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. **Çoklu oturum açma yöntemi seçin** bölmesinde **SAML**' yi seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarla** bölmesinde kurşun kalem **düzenleme** simgesini seçin.

    ![Penciled düzenleme simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölmesinde, aşağıdaki adımları izleyin:

    ![Palo Alto ağları captive portalı temel SAML yapılandırma bölmesi](common/idp-intiated.png)

   1. **Tanımlayıcı**için, `https://<customer_firewall_host_name>/SAML20/SP`düzenine sahıp bir URL girin.

   2. **Yanıt URL 'si**için, `https://<customer_firewall_host_name>/SAML20/SP/ACS`düzenine sahıp bir URL girin.

      > [!NOTE]
      > Bu adımdaki yer tutucu değerlerini gerçek tanımlayıcı ve yanıt URL 'Leriyle güncelleştirin. Gerçek değerleri almak için, [Palo Alto ağları captive portalı istemci destek ekibine](https://support.paloaltonetworks.com/support)başvurun.

5. **SAML Imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**' nun yanındaki **İndir**' i seçin. İndirilen dosyayı bilgisayarınıza kaydedin.

    ![Federasyon meta verileri XML indirme bağlantısı](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Palo Alto ağlarını yapılandırma ana portalı çoklu oturum açma

Ardından, Palo Alto Networks Şirket portalı 'nda çoklu oturum açma ayarlayın:

1. Farklı bir tarayıcı penceresinde, bir yönetici olarak Palo Alto Networks Web sitesinde oturum açın.

2. **Cihaz** sekmesini seçin.

    ![Palo Alto Networks Web sitesi cihaz sekmesi](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Menüsünde **SAML kimlik sağlayıcısı**' nı seçin ve ardından **içeri aktar**' ı seçin.

    ![Içeri aktar düğmesi](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. **SAML kimlik sağlayıcısı sunucu profili Içeri aktarma** iletişim kutusunda aşağıdaki adımları izleyin:

    ![Palo Alto ağlarını çoklu oturum açma yapılandırma](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. **Profil adı**Için **Azuread-captiveportal**gibi bir ad girin.
    
    2. **Kimlik sağlayıcısı meta verileri**' nin yanında, **Araştır**' ı seçin. Azure portal indirdiğiniz Metadata. xml dosyasını seçin.
    
    3. **Tamam**’ı seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Sonra, Azure portal *Britta Simon* adlı bir test kullanıcısı oluşturun:

1. Azure portal, **tüm kullanıcılar** > **Kullanıcılar** > **Azure Active Directory** ' i seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. **Yeni Kullanıcı**' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. **Kullanıcı** bölmesinde, aşağıdaki adımları izleyin:

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    1. **Ad**Için **Brittasıon**girin.
  
    2. **Kullanıcı adı**Için **Brittasıon\@\<your_company_domain\>** girin. Örneğin, **Brittasıon\@contoso.com**.

    3. **Parola**için bir parola girin. Girdiğiniz parolanın bir kaydını tutmanız önerilir. Parolayı görüntülemek için **parolayı göster** onay kutusunu seçebilirsiniz.

    4. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Ardından, Palo Alto ağları açıklamalı portalı 'na erişim izni verin ve Britta Simon, Azure çoklu oturum açma 'yı kullanabilir.

1. Azure portal, **tüm uygulamalar** > **Kurumsal uygulamalar** ' ı seçin.

    ![Kurumsal uygulamalar bölmesi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Palo Alto Networks-Captive Portal**' ı girip uygulamayı seçin.

    ![Uygulamalar listesinde Palo Alto Networks-Captive Portal bağlantısı](common/all-applications.png)

3. Menüsünde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin. Sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** bölmesinde, **Kullanıcılar** listesinde **Britta Simon**' u seçin. **Seç**' i seçin.

6. SAML onaylama 'ya bir rol değeri eklemek için, **Rol Seç** bölmesinde, Kullanıcı için ilgili rolü seçin. **Seç**' i seçin.

7. **Atama Ekle** bölmesinde **ata**' yı seçin.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Bir Palo Alto ağı oluşturma yönetim portalı test kullanıcısı

Ardından, Palo Alto Networks Portal 'da *Britta Simon* adlı bir kullanıcı oluşturun. Palo Alto ağları captive portalı, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümdeki herhangi bir görevi gerçekleştirmeniz gerekmez. Bir Kullanıcı Palo Alto ağları giriş portalı 'nda zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!NOTE]
> Bir kullanıcıyı el ile oluşturmak istiyorsanız, [Palo Alto Networks ana portalı istemci destek ekibine](https://support.paloaltonetworks.com/support)başvurun.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Palo Alto ağları açıklamalı portalı, bir Windows sanal makinesinde güvenlik duvarının arkasına yüklenir. Palo Alto ağlarında çoklu oturum açmayı test etmek için, Uzak Masaüstü Protokolü (RDP) kullanarak Windows VM 'de oturum açın. RDP oturumunda bir tarayıcı açın ve herhangi bir Web sitesine gidin. SSO URL 'SI açılır ve sizden kimlik doğrulamanız istenir. Kimlik doğrulaması tamamlandığında Web sitelerine erişebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

Daha fazla bilgi için şu makalelere bakın:

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory uygulamalarda çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory Koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

