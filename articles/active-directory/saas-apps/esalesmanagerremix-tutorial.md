---
title: 'Öğretici: E Sales Manager yeniden karması ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ile E Sales Manager yeniden karışımı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c06595b683092abf52300481068daab26394c4cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995650"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Azure Active Directory E Sales Manager yeniden karması ile tümleştirin

Bu öğreticide, Azure Active Directory (Azure AD) ile E Sales Manager yeniden karışımını tümleştirme hakkında bilgi edineceksiniz.

Azure AD 'yi E Sales Manager yeniden karması ile tümleştirerek aşağıdaki avantajları elde edersiniz:

- Azure AD 'de, E-Sales Manager yeniden karışımına erişimi olan bir denetim yapabilirsiniz.
- Kullanıcılarınızın, Azure AD hesaplarıyla otomatik olarak E-Satış Yöneticisi 'nin (çoklu oturum açma veya SSO) otomatik olarak oturum açmasını sağlayabilirsiniz.
- Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini E-Sales Manager yeniden karması ile yapılandırmak için aşağıdaki öğeler gereklidir:

- Azure AD aboneliği
- Bir E Sales Manager, SSO özellikli aboneliği yeniden karıştır

> [!NOTE]
> Bu öğreticideki adımları test ettiğinizde, bir üretim *ortamı kullanmanızı öneririz* .

Bu öğreticideki adımları test etmek için aşağıdaki önerileri izleyin:

- Gerekli olmadığı takdirde üretim ortamınızı kullanmayın.
- Bir Azure AD deneme ortamınız yoksa, bir [aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)alabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması
Bu öğreticide, Azure AD çoklu oturum açmayı test ortamında test edersiniz. 

Bu öğreticide özetlenen senaryo iki ana yapı blobundan oluşur:

* Galeriden E Sales Manager yeniden karışımı ekleme
* Azure AD çoklu oturum açmayı yapılandırma ve test etme

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Galeriden E Sales Manager yeniden karışımı ekleme
Azure AD 'nin tümleştirmesini E Sales Manager yeniden karışımına göre yapılandırmak için, aşağıdakileri yaparak Galeriden, yönetilen SaaS uygulamaları listenize E Sales Manager yeniden karışımı ekleyin:

1. [Azure Portal](https://portal.azure.com)sol bölmedeki **Azure Active Directory**' ı seçin. 

    ![Azure Active Directory düğmesi][1]

1. **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.

    !["Kurumsal uygulamalar" penceresi][2]
    
1. Yeni bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin.

    ![Yeni uygulama düğmesi][3]

1. Arama kutusuna **e Sales Manager yeniden karışımı** yazın, sonuçlar listesinde **e Sales Manager yeniden karışımı** ' ı seçin ve ardından **Ekle**' yi seçin.

    ![Sonuç listesinde E Sales Manager yeniden karışımı oluştur](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, "Britta Simon" adlı bir test kullanıcısına bağlı olarak, E Sales Manager yeniden karması ile Azure AD çoklu oturum açma 'yı yapılandırıp test edersiniz.

Çoklu oturum açma 'nın çalışması için, Azure AD 'nin, Azure AD 'de Kullanıcı ve karşılık gelen E-Sales Manager 'ı yeniden karışımı tanımlaması gerekir. Diğer bir deyişle, bir Azure AD kullanıcısı ve E Sales Manager yeniden karışımında aynı kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için E Sales Manager yeniden karışımı ile, sonraki beş bölümde yer alan yapı taşlarını doldurun:

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Azure portal Azure AD 'de çoklu oturum açmayı etkinleştirin ve aşağıdakileri yaparak E-Sales Manager uygulamanızda çoklu oturum açmayı yapılandırın:

1. Azure portal, **E Sales Manager Uygulama tümleştirmesini yeniden karıştır** sayfasında, **Çoklu oturum açma**' yı seçin.

    !["Çoklu oturum açma" bağlantısı][4]

1. **Çoklu oturum** açma penceresinde, **Çoklu oturum açma modu** kutusunda **SAML tabanlı oturum açma**' yı seçin.
 
    !["Çoklu oturum açma" penceresi](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. **E Sales Manager etki alanı ve URL 'Leri yeniden karıştır** altında aşağıdakileri yapın:

    ![E Sales Manager etki alanı ve URL 'Leri yeniden karıştır çoklu oturum açma bilgileri](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. **Oturum açma URL 'si** kutusuna URL 'yi şu biçimde yazın: *https:// \<Server-Based-URL> / \<sub-domain> /eSales-PC*.

    b. **Tanımlayıcı** kutusuna URL 'yi şu biçimde yazın: *https:// \<Server-Based-URL> / \<sub-domain> /*.

    c. Bu öğreticide daha sonra kullanmak için **tanımlayıcı** değerini aklınızda yapın.
    
    > [!NOTE] 
    > Yukarıdaki değerler gerçek değildir. Bunları gerçek oturum açma URL 'SI ve tanımlayıcısıyla güncelleştirin. Değerleri almak için, [E Sales Manager istemci desteği ekibine](mailto:esupport@softbrain.co.jp)başvurun.

1. **SAML Imzalama sertifikası** altında **sertifika (base64)** öğesini seçin ve ardından sertifika dosyasını bilgisayarınıza kaydedin.

    ![Sertifika (base64) indirme bağlantısı](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. **Diğer tüm Kullanıcı özniteliklerini görüntüle ve Düzenle** onay kutusunu seçin ve ardından **emapostaadresi** özniteliğini seçin.
    
    ![Kullanıcı öznitelikleri penceresi](./media/esalesmanagerremix-tutorial/configure1.png)

    **Özniteliği Düzenle** penceresi açılır.

1. **Ad alanı** ve **ad** değerlerini kopyalayın. Değeri, düzende oluşturun *\<Namespace>/\<Name>* ve daha sonra bu öğreticide kullanmak üzere kaydedin.

    ![Öznitelik düzenleme penceresi](./media/esalesmanagerremix-tutorial/configure2.png)

1. **E Sales Manager yapılandırmayı yeniden karıştır** altında, **e-Sales Manager yeniden karışımını Yapılandır**' ı seçin.

    !["E Sales Manager yeniden karışım yapılandırması" bölümünün "E-Sales Manager yeniden karışımı yapılandırma" seçiliyken gösterdiği ekran görüntüsü.](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    **Oturum açma yapılandırma** penceresi açılır.

1. **Hızlı başvuru** bölümünde, oturum kapatma URL 'SINI ve SAML çoklu oturum açma hizmeti URL 'sini kopyalayın.

1. **Kaydet**’i seçin.

    ![Kaydet düğmesi](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. E-Sales Manager 'da uygulamayı yönetici olarak yeniden Karıştır ' da oturum açın.

1. Sağ üst köşedeki **Yönetici Menüsü**' nü seçin.

    !["Yönetici Menüsü" komutu](./media/esalesmanagerremix-tutorial/configure4.png)

1. Sol bölmede, dış sistemle **sistem ayarları**  >  **ortak işlemi**' ni seçin.

    !["Sistem ayarları" ve "dış sistemle birlikte Işlem" bağlantıları](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. **Dış Sistem penceresinde birlikte işlem** ' de **SAML**' yi seçin.

    !["Dış sistemle birlikte Işlem" penceresi](./media/esalesmanagerremix-tutorial/configure6.png)

1. **SAML kimlik doğrulaması ayarı** altında aşağıdakileri yapın:

    !["SAML kimlik doğrulama ayarı" bölümü](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. **Bilgisayar sürümü** onay kutusunu seçin.
    
    b. **İşbirliği öğesi** bölümünde, açılan listede, **e-posta**' yı seçin.

    c. **İşbirliği öğesi** kutusunda, daha önce kopyaladığınız talep değerini Azure Portal yapıştırın (yani, **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** ).

    d. **Veren (VARLıK kimliği)** kutusunda, daha önce Azure Portal **E-Sales Manager 'ın etki alanı ve URL 'leri yeniden karması** bölümünden kopyaladığınız tanımlayıcı değeri yapıştırın.

    e. Azure portal indirilen sertifikanızı karşıya yüklemek için **dosya seçimi**' ni seçin.

    f. **Kimlik sağlayıcısı oturum açma URL** 'si kutusunda, daha önce Azure Portal kopyaladığınız SAML çoklu oturum açma hizmeti URL 'sini yapıştırın.

    örneğin: **Kimlik sağlayıcısı oturum kapatma URL 'si** kutusunda, daha önce Azure Portal kopyaladığınız oturum kapatma URL 'si değerini yapıştırın.

    h. **Ayar Tamam**' ı seçin.

> [!TIP]
> Uygulamayı ayarlarken, [Azure Portal](https://portal.azure.com)önceki yönergelerin kısa bir sürümünü okuyabilirsiniz. Uygulamayı **Active Directory**  >  **Kurumsal uygulamalar** bölümüne ekledikten sonra, **Çoklu oturum açma** sekmesini seçin ve ardından alttaki **yapılandırma** bölümünde yer alan ekli belgelere erişin. Katıştırılmış belge özelliği hakkında daha fazla bilgi için bkz. [Azure AD Embedded belgeleri]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, aşağıdakileri yaparak Azure portal test kullanıcısı Britta Simon ' u oluşturursunuz:

![Azure AD test kullanıcısı oluşturma][100]

1. Azure portal sol bölmedeki **Azure Active Directory**' ı seçin.

    ![Azure Active Directory bağlantısı](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Geçerli kullanıcıların listesini göstermek için **Kullanıcılar ve gruplar**  >  **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. **Tüm kullanıcılar** penceresinin en üstündeki **Ekle**' yi seçin.

    ![Ekle düğmesi](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Kullanıcı** penceresi açılır.

1. **Kullanıcı** penceresinde şunları yapın:

    ![Kullanıcı penceresi](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. **Ad** kutusunda **Brittasıon** yazın.

    b. **Kullanıcı adı** kutusuna kullanıcının e-posta adresini yazın Britta Simon.

    c. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri unutmayın.

    d. **Oluştur**’u seçin.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>E-Satış Yöneticisi oluşturma test kullanıcısını yeniden karıştır

1. E-posta Sales Manager 'da uygulamayı yönetici olarak yeniden Karıştır ' da oturum açın.

1. Sağ üst taraftaki menüden **Yönetici Menüsü** ' nü seçin.

    ![E Sales Manager yeniden karma Yapılandırma](./media/esalesmanagerremix-tutorial/configure4.png)

1. **Şirket**  >  **bölümlerinin ve çalışanların ayarlarının bakımını** yapın ve ardından **kayıtlı çalışanlar**' ı seçin.

    !["Kayıtlı çalışanlar" sekmesi](./media/esalesmanagerremix-tutorial/user1.png)

1. **Yeni çalışan kaydı** bölümünde aşağıdakileri yapın:
    
    !["Yeni çalışan kaydı" bölümü](./media/esalesmanagerremix-tutorial/user2.png)

    a. **Çalışan adı** kutusuna kullanıcının adını (örneğin, **Britta**) yazın.

    b. Kalan gerekli alanları doldurun.
    
    c. SAML 'yi etkinleştirirseniz, yönetici oturum açma sayfasından oturum alamaz. Yönetici **oturum açma** onay kutusunu seçerek kullanıcıya yönetici oturum açma ayrıcalıkları verin.

    d. **Kayıt**' ı seçin.

1. Gelecekte yönetici olarak oturum açmak için yönetici izinlerine sahip kullanıcı olarak oturum açın ve sağ üst köşedeki **Yönetici Menüsü**' nü seçin.

    !["Yönetici Menüsü" komutu](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, E-Sales Manager yeniden karışımına erişim vererek Kullanıcı Britta Simon 'u Azure çoklu oturum açma özelliğini kullanacak şekilde etkinleştirirsiniz. Bunu yapmak için aşağıdakileri yapın: 

![Kullanıcı rolünü atama][200] 

1. Azure Portal, **uygulamalar** görünümünü açın, **Dizin** görünümüne gidin ve ardından **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.

    !["Kurumsal uygulamalar" ve "tüm uygulamalar" bağlantıları][201] 

1. **Uygulamalar** listesinde **E Sales Manager yeniden karışımı**' nı seçin.

    ![E Sales Manager yeniden karışımı bağlantısı](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı][202]

1. **Ekle** ' yi seçin ve sonra **atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.

    ![Atama Ekle bölmesi][203]

1. **Kullanıcılar ve gruplar** penceresinde, **Kullanıcılar** listesinde **Britta Simon**' u seçin.

1. **Seç** düğmesini belirleyin.

1. **Atama Ekle** penceresinde **ata**' yı seçin.
    
### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde E Sales Manager yeniden karışımı kutucuğunu seçtiğinizde, E-Satış Yöneticisi 'nin uygulamanızı yeniden karıştırmak için otomatik olarak oturum açmış olmanız gerekir.

Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md). 

## <a name="additional-resources"></a>Ek kaynaklar

* [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png