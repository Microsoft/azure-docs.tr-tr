---
title: 'Öğretici: Oracle Cloud Infrastructure konsolu ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Oracle bulut altyapısı konsolu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 579a553f151cf34215af3188cfddada6da42e691
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943623"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Öğretici: Oracle bulut altyapısı konsolunu Azure Active Directory ile tümleştirme

Bu öğreticide, Oracle bulut altyapısı konsolunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Oracle bulut altyapısı konsolunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Oracle bulut altyapısı konsoluna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Oracle bulut altyapısı konsoluna otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Oracle Cloud Infrastructure konsolu çoklu oturum açma (SSO) aboneliği etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Oracle bulut altyapısı Konsolu **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Galeriden Oracle bulut altyapısı konsolunu ekleme

Oracle bulut altyapısı konsolunun tümleştirmesini Azure AD ile yapılandırmak için, Galeriden Oracle bulut altyapısı konsolunu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Oracle Cloud Infrastructure konsolunu** yazın.
1. Sonuçlar panelinden **Oracle bulut altyapısı konsolunu** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Oracle bulut altyapısı konsolu Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, Oracle bulut altyapısı konsolundaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Oracle bulut altyapısı konsolu ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
1. Uygulama tarafında SSO ayarlarını yapılandırmak için **[Oracle bulut altyapı konsolu 'Nu yapılandırın](#configure-oracle-cloud-infrastructure-console)** .
1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Oracle bulut altyapısı konsolunda, kullanıcının Azure AD gösterimine bağlı olan B. Simon 'un bir karşılığı olacak şekilde **[Oracle Cloud Infrastructure konsol test kullanıcısı oluşturun](#create-oracle-cloud-infrastructure-console-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Oracle Cloud Infrastructure konsolu** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

   > [!NOTE]
   > Hizmet sağlayıcı meta veri dosyasını, öğreticinin **Oracle bulut altyapı konsolu çoklu oturum açma bölümünde yapılandırma** bölümünden alacaksınız.
    
   1. Tıklayın **meta veri dosyasını karşıya yükleme**.

   1. Tıklayarak **klasör logosu** meta veri dosyası seçin ve **karşıya**.

   1. Meta veri dosyası başarıyla karşıya yüklendikten sonra **tanımlayıcı** ve **yanıt URL** değerleri, **temel SAML yapılandırması** bölüm metin kutusunda otomatik olarak doldurulur.
    
      > [!NOTE]
      > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak alamazsanız, değerleri gereksinimlerinize göre el ile girin.

      **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Değer gerçek değil. Değeri, gerçek oturum açma URL 'SI ile güncelleştirin. Değeri almak için [Oracle Cloud Infrastructure Konsolu istemci destek ekibine](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. Oracle Cloud Infrastructure konsol uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

   ![image](common/edit-attribute.png)

1. Daha fazlasına ek olarak, Oracle bulut altyapısı konsol uygulaması, SAML yanıtına daha fazla öznitelik geçirilmesini bekler. **Grup talepleri (Önizleme)** Iletişim kutusundaki **Kullanıcı öznitelikleri & talepler** bölümünde aşağıdaki adımları uygulayın:

   1. **Ad tanımlayıcı değeri**' nin yanındaki **kaleme** tıklayın.

   1. **Ad tanımlayıcı biçimini seçin**olarak **persistent** ' i seçin.
 
   1. **Kaydet**’e tıklayın.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. **Talepte döndürülen gruplar ' ın**yanındaki **kaleme** tıklayın.

   1. Radyo listesinden **güvenlik grupları** ' nı seçin.

   1. **Grup Kimliği**'Nin **kaynak özniteliğini** seçin.

   1. **Grup talebinin adını özelleştirmeyi**denetleyin.

   1. **Ad** metin kutusuna **GroupName**yazın.

   1. **Ad alanı (isteğe bağlı)** metin kutusuna yazın `https://auth.oraclecloud.com/saml/claims`.

   1. **Kaydet**’e tıklayın.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. **Oracle bulut altyapısı konsolunu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Oracle bulut altyapısı konsolunu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, yönetici olarak Oracle bulut altyapısı konsolunda oturum açın.

1. Menünün sol tarafına tıkladıktan sonra **kimlik** ' e tıklayın ve ardından **Federasyon**' a gidin.

   ![Yapılandırma](./media/oracle-cloud-tutorial/config01.png)

1. **Bu belgeyi indir** bağlantısını tıklatıp Azure Portal **temel SAML yapılandırması** bölümüne yükleyin ve ardından **kimlik sağlayıcısı ekle**' ye tıklayın.

   ![Yapılandırma](./media/oracle-cloud-tutorial/config02.png)

1. **Kimlik sağlayıcı ekle** açılır penceresinde aşağıdaki adımları uygulayın:

   ![Yapılandırma](./media/oracle-cloud-tutorial/config03.png)

   1. **Ad** metin kutusuna adınızı girin.

   1. **Açıklama** metin kutusuna açıklamanızı girin.

   1. **MICROSOFT ACTIVE DIRECTORY Federasyon Hizmeti (ADFS) veya SAML 2,0 uyumlu KIMLIK sağlayıcısını** **tür**olarak seçin.

   1. Azure portal 'ten indirdiğiniz Federasyon meta veri XML 'sini karşıya yüklemek için, **Araştır** ' a tıklayın.

   1. **Devam** ' a tıklayın ve **kimlik sağlayıcısını Düzenle** bölümünde aşağıdaki adımları uygulayın:

      ![Yapılandırma](./media/oracle-cloud-tutorial/config09.png)

   1. **KIMLIK sağlayıcısı grubu** özel grup olarak seçilmelidir. Grup KIMLIĞI, Azure Active Directory grubun GUID 'SI olmalıdır. Grubun, **OCı grubu** alanında karşılık gelen grupla eşlenmesi gerekir.

   1. Azure portal ve kuruluşunuzun ihtiyaç duyduğu kuruluma göre birden çok grubu eşleyebilirsiniz. İhtiyaç duyduğunuz sayıda grup eklemek için **+ eşleme Ekle** ' ye tıklayın.

   1. **Gönder**'e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B. Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B. Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Oracle bulut altyapısı konsoluna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Oracle Cloud Infrastructure konsolu**' nu seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

   ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Oracle bulut altyapısı konsol test kullanıcısı oluşturma

 Oracle bulut altyapısı konsolu, varsayılan olarak tam zamanında sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Erişim denemesi sırasında yeni bir Kullanıcı oluşturulmaz ve ayrıca Kullanıcı oluşturma gereksinimi yoktur.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Oracle bulut altyapısı konsol kutucuğunu seçtiğinizde, Oracle bulut altyapısı Konsolu oturum açma sayfasına yönlendirilirsiniz. Açılan menüden **KIMLIK sağlayıcısını** seçin ve oturum açmak için aşağıda gösterildiği gibi **devam** ' a tıklayın. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Yapılandırma](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
