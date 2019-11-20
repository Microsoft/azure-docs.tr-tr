---
title: 'Öğretici: Qlik Sense Enterprise ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Qlik Sense Enterprise arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7886c863dd50fd3f09d782f892bdebabceb73546
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160105"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Öğretici: Qlik Sense Enterprise 'ı Azure Active Directory tümleştirme

Bu öğreticide, Qlik Sense Enterprise 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Qlik Sense Enterprise 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Qlik Sense kuruluşa erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Qlik Sense Enterprise 'ta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Qlik Sense Kurumsal Çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. Qlik Sense Enterprise, **SP** tarafından başlatılan SSO 'yu destekler.

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Galeriden Qlik Sense kurumsal ekleme

Qlik Sense Enterprise 'ın Azure AD ile tümleştirilmesini yapılandırmak için, galerisindeki Qlik Sense Enterprise 'ı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Qlik Sense Enterprise** yazın.
1. Sonuçlar panelinden **Qlik Sense Enterprise** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**Britta Simon**adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Qlik Sense Enterprise ile yapılandırın ve test edin. SSO 'nun çalışması için, Qlik Sense kuruluşunda bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Qlik Sense Enterprise ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[Qlik Sense ENTERPRISE SSO 'Yu yapılandırın](#configure-qlik-sense-enterprise-sso)** .
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. **[Qlik Sense kurumsal test kullanıcısı oluşturun](#create-qlik-sense-enterprise-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Qlik Sense kuruluşunda Britta Simon 'ın bir karşılığı olacak şekilde.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Qlik Sense kurumsal** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, Bu öğreticinin ilerleyen kısımlarında açıklanan gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin veya bu değerleri almak için [Qlik Sense Kurumsal istemci destek ekibine](https://www.qlik.com/us/services/support) başvurun. URL 'Ler için varsayılan bağlantı noktası 443 ' dir ancak kuruluşunuzun ihtiyaç duyduğu şekilde özelleştirebilirsiniz.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, gereksiniminize göre verilen seçeneklerden **Federasyon meta veri XML 'sini** bulun ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-sso"></a>Qlik Sense Enterprise SSO 'yu yapılandırma

1. Federasyon meta veri XML dosyasını Qlik Sense sunucusuna yükleyebilmeniz için hazırlayın.

    > [!NOTE]
    > IDP meta verilerini Qlik Sense sunucusuna yüklemeden önce, Azure AD ve Qlik Sense sunucusu arasında düzgün işlem yapıldığından emin olmak için dosyanın düzenlenmesi gerekir.

    ![QlikSense][qs24]

    a. Bir metin düzenleyicisinde Azure portal indirmiş olduğunuz FederationMetaData. xml dosyasını açın.

    b. **RoleDescriptor**değerini arayın.  Dört giriş vardır (açma ve kapama öğesi etiketlerinin iki çifti).

    c. RoleDescriptor etiketlerini ve dosyadaki tüm bilgileri silin.

    d. Dosyayı kaydedin ve bu belgede daha sonra kullanmak üzere yakın tutun.

2. Qlik Sense Qlik yönetim konsoluna (QMC), sanal ara sunucu yapılandırması oluşturabileceğiniz bir kullanıcı olarak gidin.

3. QMC 'de, **sanal proxy 'ler** menü öğesine tıklayın.

    ![QlikSense][qs6]

4. Ekranın alt kısmındaki **Yeni oluştur** düğmesine tıklayın.

    ![QlikSense][qs7]

5. Sanal proxy düzenleme ekranı görüntülenir.  Ekranın sağ tarafında, yapılandırma seçeneklerinin görünür hale getirilmesi için bir menü bulunur.

    ![QlikSense][qs9]

6. Kimlik menü seçeneği işaretli olarak, Azure sanal proxy yapılandırması için tanımlama bilgilerini girin.

    ![QlikSense][qs8]  

    a. **Açıklama** alanı, sanal proxy yapılandırması için kolay bir addır.  Açıklama için bir değer girin.

    b. **Ön ek** alanı, Azure AD çoklu oturum açma Ile Qlik Sense 'e bağlanmak için sanal proxy uç noktasını tanımlar.  Bu sanal proxy için benzersiz bir ön ek adı girin.

    c. **Oturum eylemsizlik zaman aşımı (dakika)** , bu sanal ara sunucu üzerinden bağlantı zaman aşımına uğrar.

    d. **Oturum tanımlama bilgisi adı** , bir kullanıcının başarılı kimlik doğrulamasından sonra aldığı Qlik Sense oturumunda oturum tanımlayıcısını depolayan tanımlama bilgisi adıdır.  Bu ad benzersiz olmalıdır.

7. Görünür yapmak için kimlik doğrulama menü seçeneğine tıklayın.  Kimlik doğrulama ekranı görüntülenir.

    ![QlikSense][qs10]

    a. Anonim **erişim modu** açılan liste, anonim kullanıcıların sanal proxy aracılığıyla Qlik Sense 'a erişip erişemeyeceğini belirler.  Varsayılan seçenek anonim kullanıcı değildir.

    b. **Kimlik doğrulama yöntemi** açılır, sanal proxy 'nin kullanacağı kimlik doğrulama şemasını belirler.  Açılan listeden SAML ' yi seçin.  Sonuç olarak daha fazla seçenek görüntülenir.

    c. **SAML ana bilgisayar URI 'si alanında**, bu saml sanal proxy aracılığıyla Qlik Sense erişmek için Kullanıcı adı ' nı girin.  Ana bilgisayar adı, Qlik Sense sunucusunun URI 'sidir.

    d. **SAML VARLıK kimliğinde**, SAML ana bilgisayar URI 'si alanı için girilen değeri girin.

    e. **SAML IDP meta verileri** , daha önce **Azure AD yapılandırması 'Ndan Federasyon meta verilerini düzenleme** bölümünde düzenlenen dosyadır.  **IDP meta verilerini karşıya yüklemeden önce,** Azure AD Ile Qlik Sense sunucusu arasında düzgün işlem yapıldığından emin olmak için dosyanın düzenlenmesi gerekir.  **Lütfen dosya düzenlenmediyse yukarıdaki yönergelere bakın.**  Dosya düzenlendiyse, gözden geçirme düğmesine tıklayın ve sanal proxy yapılandırmasına yüklemek için düzenlenmiş meta veri dosyasını seçin.

    f. Azure AD 'nin Qlik Sense sunucusuna gönderdiği **Kullanıcı** kimliğini temsıl eden SAML özniteliği için öznitelik adı veya şema başvurusu girin.  Şema başvuru bilgileri, Azure Uygulama ekranları yapılandırması ' nda bulunabilir.  Ad özniteliğini kullanmak için `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`girin.

    g. Azure AD aracılığıyla Qlik Sense sunucusunda kimlik doğrulaması yaparken kullanıcılara eklenecek **Kullanıcı dizini** için değeri girin.  Sabit kodlanmış değerler **köşeli ayraç []** ile çevrelenmelidir.  Azure AD SAML onaylama 'da gönderilen bir özniteliği kullanmak için, bu metin kutusuna köşeli ayraçlar **olmadan** özniteliğin adını girin.

    h. **SAML imzalama algoritması** , sanal proxy yapılandırması için hizmet sağlayıcısını (Bu örnekte Qlik Sense Server) sertifika imzalama olarak ayarlar.  Qlik Sense sunucusu Microsoft Iyileştirilmiş RSA ve AES şifreleme sağlayıcısı kullanılarak oluşturulan bir güvenilen sertifika kullanıyorsa, SAML imzalama algoritmasını **SHA-256**olarak değiştirin.

    i. SAML öznitelik eşleme bölümü, grup gibi ek özniteliklerin güvenlik kurallarında kullanılmak üzere Qlik Sense 'e gönderilmesine izin verir.

8. **Yük Dengeleme** menü seçeneğine tıklayarak görünür hale getirin.  Yük Dengeleme ekranı görüntülenir.

    ![QlikSense][qs11]

9. **Yeni sunucu düğümü Ekle** düğmesine tıklayın, motor düğümünü veya düğümleri seçin Qlik Sense, Yük Dengeleme amaçlarıyla oturum gönderir ve **Ekle** düğmesine tıklayın.

    ![QlikSense][qs12]

10. Gelişmiş menü seçeneğine tıklayarak görünür hale getirin. Gelişmiş ekran görüntülenir.

    ![QlikSense][qs13]

    Konak izin listesi, Qlik Sense sunucusuna bağlanırken kabul edilen ana bilgisayar adlarını tanımlar.  **Qlik Sense sunucusuna bağlanırken kullanıcıların belirtmesi için ana bilgisayar adını girin.** Ana bilgisayar adı, https://olmadan SAML ana bilgisayar URI 'siyle aynı değerdir.

11. **Uygula** düğmesine tıklayın.

    ![QlikSense][qs14]

12. Sanal ara sunucu ile bağlantılı proxy 'lerin yeniden başlatılacağını bildiren uyarı iletisini kabul etmek için Tamam ' a tıklayın.

    ![QlikSense][qs15]

13. Ekranın sağ tarafında, Ilişkili öğeler menüsü görüntülenir.  **Proxy 'ler** menü seçeneğine tıklayın.

    ![QlikSense][qs16]

14. Proxy ekranı görünür.  Sanal proxy 'ye bir proxy bağlamak için alttaki **bağlantı** düğmesine tıklayın.

    ![QlikSense][qs17]

15. Bu sanal proxy bağlantısını destekleyecek proxy düğümünü seçin ve **bağlantı** düğmesine tıklayın.  Bağlandıktan sonra proxy, ilişkili proxy 'ler altında listelenecektir.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Yaklaşık beş ila on saniye sonra, QMC 'yi Yenile iletisi görüntülenir.  **QMC 'Yi Yenile** düğmesine tıklayın.

    ![QlikSense][qs20]

17. QMC yenilendiğinde, **sanal proxy** menü öğesine tıklayın. Yeni SAML sanal proxy girişi, ekrandaki tabloda listelenir.  Sanal proxy girdisinde tek tıklama.

    ![QlikSense][qs51]

18. Ekranın alt kısmındaki SP meta verilerini Indir düğmesi etkinleşir.  Meta verileri bir dosyaya kaydetmek için **SP meta verilerini indir** düğmesine tıklayın.

    ![QlikSense][qs52]

19. SP meta veri dosyasını açın.  **EntityId** girişini ve **assertionconsumerservice** girişini gözlemleyin.  Bu değerler **tanımlayıcı**, **URL 'Yi IMZALA** ve Azure AD uygulama yapılandırmasındaki **yanıt URL** 'si ile eşdeğerdir. Bu değerleri, eşleşmeseler Azure AD uygulama yapılandırmasındaki **Qlik Sense Enterprise etki alanı ve URL 'leri** bölümüne yapıştırın, ardından Azure AD uygulaması yapılandırma Sihirbazı 'nda değiştirmeniz gerekir.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına username@companydomain.extension girin. Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Qlik Sense kurumsal erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Qlik Sense Enterprise**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-qlik-sense-enterprise-test-user"></a>Qlik Sense kurumsal test kullanıcısı oluşturma

Bu bölümde, Qlik Sense Enterprise 'ta Britta Simon adlı bir Kullanıcı oluşturacaksınız. Qlik Sense Enterprise platformunda kullanıcıları eklemek için [Qlik Sense kurumsal destek ekibi](https://www.qlik.com/us/services/support) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

### <a name="test-sso"></a>Test SSO 'SU

Erişim panelinde Qlik Sense kurumsal kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız Qlik Sense kuruluşunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
