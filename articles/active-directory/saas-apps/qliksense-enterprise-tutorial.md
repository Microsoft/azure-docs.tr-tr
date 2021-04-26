---
title: 'Öğretici: Qlik Sense Enterprise ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Qlik Sense Enterprise arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 2d046f5f039555e58d9ce4c028e750ce083fd5f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733697"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Öğretici: Qlik Sense Enterprise 'ı Azure Active Directory tümleştirme

Bu öğreticide, Qlik Sense Enterprise 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Qlik Sense Enterprise 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Qlik Sense kuruluşa erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Qlik Sense Enterprise 'ta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Qlik Sense Kurumsal Çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 
* Qlik Sense Enterprise, **SP** tarafından başlatılan SSO 'yu destekler.
* Qlik Sense Enterprise **tam zamanında sağlamayı** destekler

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Galeriden Qlik Sense kurumsal ekleme

Qlik Sense Enterprise 'ın Azure AD ile tümleştirilmesini yapılandırmak için, galerisindeki Qlik Sense Enterprise 'ı yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Qlik Sense Enterprise** yazın.
1. Sonuçlar panelinden **Qlik Sense Enterprise** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-qlik-sense-enterprise"></a>Qlik Sense Enterprise için Azure AD SSO 'yu yapılandırma ve test etme

**Britta Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Qlik Sense Enterprise ile yapılandırın ve test edin. SSO 'nun çalışması için, Qlik Sense kuruluşunda bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Qlik Sense Enterprise ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Qlik Sense ENTERPRISE SSO 'Yu yapılandırın](#configure-qlik-sense-enterprise-sso)** .
    1. **[Qlik Sense kurumsal test kullanıcısı oluşturun](#create-qlik-sense-enterprise-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Qlik Sense kuruluşunda Britta Simon 'ın bir karşılığı olacak şekilde.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Qlik Sense kurumsal** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** sayfasında, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. **Tanımlayıcı** metin kutusunda, aşağıdaki düzenin birini kullanarak bir URL yazın:

    | Tanımlayıcı |
    |-------------|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com` |
    | `https://<Fully Qualified Domain Name>.qliksense.com` |
    |
   

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, Bu öğreticinin ilerleyen kısımlarında açıklanan gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin veya bu değerleri almak için [Qlik Sense Kurumsal istemci destek ekibine](https://www.qlik.com/us/services/support) başvurun. URL 'Ler için varsayılan bağlantı noktası 443 ' dir ancak kuruluşunuzun ihtiyaç duyduğu şekilde özelleştirebilirsiniz.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, gereksiniminize göre verilen seçeneklerden **Federasyon meta veri XML 'sini** bulun ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `Britta Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `BrittaSimon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Qlik Sense kurumsal erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Qlik Sense Enterprise**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-qlik-sense-enterprise-sso"></a>Qlik Sense Enterprise SSO 'yu yapılandırma

1. Federasyon meta veri XML dosyasını Qlik Sense sunucusuna yükleyebilmeniz için hazırlayın.

    > [!NOTE]
    > IDP meta verilerini Qlik Sense sunucusuna yüklemeden önce, Azure AD ve Qlik Sense sunucusu arasında düzgün işlem yapıldığından emin olmak için dosyanın düzenlenmesi gerekir.

    ![Ekran görüntüsünde, Federasyon meta verileri X M L dosyası olan bir Visual Studio Code penceresi gösterilmektedir.][qs24]

    a. Bir metin düzenleyicisinde Azure portal indirdiğiniz FederationMetaData.xml dosyasını açın.

    b. **RoleDescriptor** değerini arayın.  Dört giriş vardır (açma ve kapama öğesi etiketlerinin iki çifti).

    c. RoleDescriptor etiketlerini ve dosyadaki tüm bilgileri silin.

    d. Dosyayı kaydedin ve bu belgede daha sonra kullanmak üzere yakın tutun.

2. Qlik Sense Qlik yönetim konsoluna (QMC), sanal ara sunucu yapılandırması oluşturabileceğiniz bir kullanıcı olarak gidin.

3. QMC 'de, **sanal proxy 'ler** menü öğesine tıklayın.

    ![Ekran görüntüsü, yapılandırma SISTEMI ' nden seçilen sanal proxy 'leri gösterir.][qs6]

4. Ekranın alt kısmındaki **Yeni oluştur** düğmesine tıklayın.

    ![Ekran görüntüsü yeni oluştur seçeneğini gösterir.][qs7]

5. Sanal proxy düzenleme ekranı görüntülenir.  Ekranın sağ tarafında, yapılandırma seçeneklerinin görünür hale getirilmesi için bir menü bulunur.

    ![Ekran görüntüsü, özelliklerden seçilen tanımlamayı gösterir.][qs9]

6. Kimlik menü seçeneği işaretli olarak, Azure sanal proxy yapılandırması için tanımlama bilgilerini girin.

    ![Ekran görüntüsü, tanımlanan değerleri girebileceğiniz sanal ara sunucu kimliğini düzenleme bölümünü gösterir.][qs8]  

    a. **Açıklama** alanı, sanal proxy yapılandırması için kolay bir addır.  Açıklama için bir değer girin.

    b. **Ön ek** alanı, Azure AD çoklu oturum açma Ile Qlik Sense 'e bağlanmak için sanal proxy uç noktasını tanımlar.  Bu sanal proxy için benzersiz bir ön ek adı girin.

    c. **Oturum eylemsizlik zaman aşımı (dakika)** , bu sanal ara sunucu üzerinden bağlantı zaman aşımına uğrar.

    d. **Oturum tanımlama bilgisi adı** , bir kullanıcının başarılı kimlik doğrulamasından sonra aldığı Qlik Sense oturumunda oturum tanımlayıcısını depolayan tanımlama bilgisi adıdır.  Bu ad benzersiz olmalıdır.

7. Görünür yapmak için kimlik doğrulama menü seçeneğine tıklayın.  Kimlik doğrulama ekranı görüntülenir.

    ![Ekran görüntüsü, tanımlanan değerleri girebileceğiniz sanal proxy kimlik doğrulaması bölümünü gösterir.][qs10]

    a. Anonim **erişim modu** açılan liste, anonim kullanıcıların sanal proxy aracılığıyla Qlik Sense 'a erişip erişemeyeceğini belirler.  Varsayılan seçenek anonim kullanıcı değildir.

    b. **Kimlik doğrulama yöntemi** açılır, sanal proxy 'nin kullanacağı kimlik doğrulama şemasını belirler.  Açılan listeden SAML ' yi seçin.  Sonuç olarak daha fazla seçenek görüntülenir.

    c. **SAML ana bilgisayar URI 'si alanında**, bu saml sanal proxy aracılığıyla Qlik Sense erişmek için Kullanıcı adı ' nı girin.  Ana bilgisayar adı, Qlik Sense sunucusunun URI 'sidir.

    d. **SAML VARLıK kimliğinde**, SAML ana bilgisayar URI 'si alanı için girilen değeri girin.

    e. **SAML IDP meta verileri** , daha önce **Azure AD yapılandırması 'Ndan Federasyon meta verilerini düzenleme** bölümünde düzenlenen dosyadır.  **IDP meta verilerini karşıya yüklemeden önce,** Azure AD Ile Qlik Sense sunucusu arasında düzgün işlem yapıldığından emin olmak için dosyanın düzenlenmesi gerekir.  **Lütfen dosya düzenlenmediyse yukarıdaki yönergelere bakın.**  Dosya düzenlendiyse, gözden geçirme düğmesine tıklayın ve sanal proxy yapılandırmasına yüklemek için düzenlenmiş meta veri dosyasını seçin.

    f. Azure AD 'nin Qlik Sense sunucusuna gönderdiği **Kullanıcı** kimliğini temsıl eden SAML özniteliği için öznitelik adı veya şema başvurusu girin.  Şema başvuru bilgileri, Azure Uygulama ekranları yapılandırması ' nda bulunabilir.  Name özniteliğini kullanmak için, girin `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    örneğin: Azure AD aracılığıyla Qlik Sense sunucusunda kimlik doğrulaması yaparken kullanıcılara eklenecek **Kullanıcı dizini** için değeri girin.  Sabit kodlanmış değerler **köşeli ayraç []** ile çevrelenmelidir.  Azure AD SAML onaylama 'da gönderilen bir özniteliği kullanmak için, bu metin kutusuna köşeli ayraçlar **olmadan** özniteliğin adını girin.

    h. **SAML imzalama algoritması** , sanal proxy yapılandırması için hizmet sağlayıcısını (Bu örnekte Qlik Sense Server) sertifika imzalama olarak ayarlar.  Qlik Sense sunucusu Microsoft Iyileştirilmiş RSA ve AES şifreleme sağlayıcısı kullanılarak oluşturulan bir güvenilen sertifika kullanıyorsa, SAML imzalama algoritmasını **SHA-256** olarak değiştirin.

    i. SAML öznitelik eşleme bölümü, grup gibi ek özniteliklerin güvenlik kurallarında kullanılmak üzere Qlik Sense 'e gönderilmesine izin verir.

8. **Yük Dengeleme** menü seçeneğine tıklayarak görünür hale getirin.  Yük Dengeleme ekranı görüntülenir.

    ![Ekran görüntüsü, yük dengeleme için sanal proxy düzenleme ekranını gösterir ve yeni sunucu düğümü Ekle seçeneğini belirleyebilirsiniz.][qs11]

9. **Yeni sunucu düğümü Ekle** düğmesine tıklayın, motor düğümünü veya düğümleri seçin Qlik Sense, Yük Dengeleme amaçlarıyla oturum gönderir ve **Ekle** düğmesine tıklayın.

    ![Ekran görüntüsü, sunucu ekleyebileceğiniz iletişim kutusunda yük dengelemeye sunucu düğümleri Ekle düğmesini gösterir.][qs12]

10. Gelişmiş menü seçeneğine tıklayarak görünür hale getirin. Gelişmiş ekran görüntülenir.

    ![Ekran görüntüsü, sanal proxy gelişmiş ekranını Düzenle ekranını gösterir.][qs13]

    Konak izin listesi, Qlik Sense sunucusuna bağlanırken kabul edilen ana bilgisayar adlarını tanımlar.  **Qlik Sense sunucusuna bağlanırken kullanıcıların belirtmesi için ana bilgisayar adını girin.** Ana bilgisayar adı, https://olmadan SAML ana bilgisayar URI 'siyle aynı değerdir.

11. **Uygula** düğmesine tıklayın.

    ![Ekran görüntüsü Uygula düğmesini gösterir.][qs14]

12. Sanal ara sunucu ile bağlantılı proxy 'lerin yeniden başlatılacağını bildiren uyarı iletisini kabul etmek için Tamam ' a tıklayın.

    ![Ekran görüntüsü, sanal proxy 'ye değişiklikleri Uygula onay iletisini gösterir.][qs15]

13. Ekranın sağ tarafında, Ilişkili öğeler menüsü görüntülenir.  **Proxy 'ler** menü seçeneğine tıklayın.

    ![Ekran görüntüsü Ilişkili öğelerden seçilen proxy 'Leri gösterir.][qs16]

14. Proxy ekranı görünür.  Sanal proxy 'ye bir proxy bağlamak için alttaki **bağlantı** düğmesine tıklayın.

    ![Ekran görüntüsünde bağlantı düğmesi gösterilir.][qs17]

15. Bu sanal proxy bağlantısını destekleyecek proxy düğümünü seçin ve **bağlantı** düğmesine tıklayın.  Bağlandıktan sonra proxy, ilişkili proxy 'ler altında listelenecektir.

    ![Ekran görüntüsü, select proxy Services 'ı gösterir.][qs18]
  
    ![Ekran görüntüsü, sanal ara sunucu ilişkili öğeler iletişim kutusunda Ilişkili proxy 'leri gösterir.][qs19]

16. Yaklaşık beş ila on saniye sonra, QMC 'yi Yenile iletisi görüntülenir.  **QMC 'Yi Yenile** düğmesine tıklayın.

    ![Ekran görüntüsü, oturumunuzun sona erdiği iletiyi gösterir.][qs20]

17. QMC yenilendiğinde, **sanal proxy** menü öğesine tıklayın. Yeni SAML sanal proxy girişi, ekrandaki tabloda listelenir.  Sanal proxy girdisinde tek tıklama.

    ![Ekran görüntüsü, tek bir girişi olan sanal proxy 'leri gösterir.][qs51]

18. Ekranın alt kısmındaki SP meta verilerini Indir düğmesi etkinleşir.  Meta verileri bir dosyaya kaydetmek için **SP meta verilerini indir** düğmesine tıklayın.

    ![Ekran görüntüsünde, S P meta verilerini Indir düğmesi gösterilir.][qs52]

19. SP meta veri dosyasını açın.  **EntityId** girişini ve **assertionconsumerservice** girişini gözlemleyin.  Bu değerler **tanımlayıcı**, **URL 'Yi IMZALA** ve Azure AD uygulama yapılandırmasındaki **yanıt URL** 'si ile eşdeğerdir. Bu değerleri, eşleşmeseler Azure AD uygulama yapılandırmasındaki **Qlik Sense Enterprise etki alanı ve URL 'leri** bölümüne yapıştırın, ardından Azure AD uygulaması yapılandırma Sihirbazı 'nda değiştirmeniz gerekir.

    ![Ekran görüntüsünde EntityId ve AssertionConsumerService adlı bir EntityDescriptor içeren bir düz metin düzenleyici gösterilmektedir.][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Qlik Sense kurumsal test kullanıcısı oluşturma

Qlik Sense Enterprise, **tam zamanında sağlamayı** destekler, kullanıcılar SSO özelliğini kullandıkları için otomatik olarak Qlik Sense ENTERPRISE ' kullanıcıları ' deposuna eklenir. Ayrıca, istemciler QMC 'yi kullanabilir ve Active Directory ve diğerleri gibi Qlik Sense kuruluşunda kullanıcıları önceden doldurmak için bir UDC (Kullanıcı dizin Bağlayıcısı) oluşturabilir.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Qlik Sense kurumsal oturum açma URL 'sine yönlendirecektir. 

* Doğrudan Qlik Sense kurumsal oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Qlik Sense kurumsal kutucuğuna tıkladığınızda bu, Qlik Sense kurumsal oturum açma URL 'sine yeniden yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Qlik Sense Enterprise 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)

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