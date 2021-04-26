---
title: 'Öğretici: Jobscience ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Jobscience arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 5a104dcd6ccf500c115359a1b72c67b85359a802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002196"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Öğretici: Jobscience ile Azure Active Directory tümleştirme

Bu öğreticide, Jobscience 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

Jobscience 'yi Azure AD ile tümleştirmek aşağıdaki avantajları sağlar:

- Azure AD 'de Jobscience erişimi olan denetimi yapabilirsiniz
- Kullanıcılarınızın Azure AD hesaplarıyla Jobscience (çoklu oturum açma) için otomatik olarak oturum açmasını sağlayabilirsiniz
- Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini Jobscience ile yapılandırmak için aşağıdaki öğeler gereklidir:

- Azure AD aboneliği
- Bir Jobscience çoklu oturum açma etkin aboneliği

> [!NOTE]
> Bu öğreticideki adımları test etmek için, üretim ortamının kullanılmasını önermiyoruz.

Bu öğreticideki adımları test etmek için aşağıdaki önerileri izlemeniz gerekir:

- Gerekli olmadığı takdirde üretim ortamınızı kullanmayın.
- Bir Azure AD deneme ortamınız yoksa, buradan bir aylık deneme sürümü edinebilirsiniz: [deneme teklifi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Senaryo açıklaması
Bu öğreticide, Azure AD çoklu oturum açmayı test ortamında test edersiniz. Bu öğreticide özetlenen senaryo iki ana yapı blobundan oluşur:

1. Galeriden Jobscience ekleme
1. Azure AD çoklu oturum açmayı yapılandırma ve test etme

## <a name="adding-jobscience-from-the-gallery"></a>Galeriden Jobscience ekleme
Jobscience 'ın Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Jobscience eklemeniz gerekir.

**Galeriden Jobscience eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın. 

    ![Active Directory][1]

1. **Kurumsal uygulamalar**'a gidin. Ardından **tüm uygulamalara** gidin.

    ![Ekran görüntüsü, tüm uygulamalar seçiliyken Yönet altında seçilen Azure portal kurumsal uygulamaları gösterir.][2]
    
1. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Ekran görüntüsü yeni seçili uygulamayı gösterir.][3]

1. Arama kutusuna **Jobscience** yazın.

    ![Ekran görüntüsünde, Jobscience 'a girilen Galeriden ekleme gösterilir.](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Sonuçlar panelinde **Jobscience**' ı seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

    ![Ekran görüntüsünde, Jobscience dahil edilen sonuçlar gösterilir.](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme
Bu bölümde, "Britta Simon" adlı bir test kullanıcısına göre Jobscience ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.

Çoklu oturum açma 'nın çalışması için Azure AD 'nin, Azure AD 'deki bir kullanıcıya, Jobscience 'daki karşılık gelen kullanıcının ne olduğunu bilmeleri gerekir. Diğer bir deyişle, Azure AD kullanıcısı ve Jobscience içindeki ilgili Kullanıcı arasındaki bağlantı ilişkisinin oluşturulması gerekir.

Jobscience **'da, bağlantı ilişkisini oluşturmak için** Kullanıcı adının DEĞERINI Azure AD ' de **Kullanıcı adı** olarak atayın.

Azure AD çoklu oturum açma 'yı Jobscience ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma yapılandırması](#configuring-azure-ad-single-sign-on)** , kullanıcılarınızın bu özelliği kullanmasına olanak tanımak için.
1. **[Azure AD test kullanıcısı oluşturma](#creating-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test etme.
1. **[Bir Jobscience test kullanıcısı oluşturma](#creating-a-jobscience-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Jobscience 'Da Britta Simon 'a sahip olacak.
1. Azure AD **[Test kullanıcısına atama](#assigning-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak Için Britta Simon 'u etkinleştirmek için.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı test etme](#testing-single-sign-on)** .

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure AD Azure portal 'de çoklu oturum açmayı etkinleştirin ve Jobscience uygulamanızda çoklu oturum açmayı yapılandırın.

**Azure AD çoklu oturum açmayı Jobscience ile yapılandırmak için aşağıdaki adımları uygulayın:**

1. Azure portal, **Jobscience** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' ya tıklayın.

    ![Ekran görüntüsünde, Azure portal Yönet altında çoklu oturum açma gösterilmektedir.][4]

1. Çoklu oturum **açma iletişim kutusunda** , çoklu oturum açmayı etkinleştirmek için **SAML tabanlı oturum açma** olarak **mod** ' u seçin.
 
    ![Ekran görüntüsü SAML tabanlı oturum açma modunun seçili olduğunu gösterir.](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. **Jobscience etki alanı ve URL 'ler** bölümünde aşağıdaki adımları uygulayın:

    ![Ekran görüntüsü, oturum açma U R L 'yi gösterir.](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Bu değer gerçek değil. Bu değeri gerçek Sign-On URL 'siyle güncelleştirin. Bu değeri [Jobscience istemci destek ekibine](http://www.jobscience.com/support) veya öğreticide daha sonra açıklanacak olan, oluşturacağınız SSO profilinden alın. 
 
1. **SAML Imzalama sertifikası** bölümünde **sertifika (base64)** öğesine tıklayın ve ardından sertifika dosyasını bilgisayarınıza kaydedin.

    ![Ekran görüntüsü, bir sertifikayı indirebileceğiniz SAML Imzalama sertifikası bölmesini gösterir.](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. **Kaydet** düğmesine tıklayın.

    ![Ekran görüntüsü Kaydet düğmesini gösterir.](./media/jobscience-tutorial/tutorial_general_400.png)

1. **Jobscience yapılandırma** bölümünde, **oturum açma penceresini yapılandırma** ' yı açmak Için **Jobscience 'ı Yapılandır** ' a tıklayın. **Hızlı başvuru bölümünde** **oturum kapatma URL 'Si, SAML VARLıK kimliği ve SAML tek Sign-On hizmeti URL 'sini** kopyalayın.

    ![Ekran görüntüsü Jobscience yapılandırma penceresini gösterir.](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Jobscience şirket sitenizde yönetici olarak oturum açın.

1. **Kurulum**'a gidin.
   
   ![Ekran görüntüsü şirketinizin kurulum öğesini gösterir.](./media/jobscience-tutorial/IC784358.png "Kurulum")

1. Sol gezinti bölmesindeki **Yönet** bölümünde, **etki alanı yönetimi** ' ne tıklayarak ilgili bölümü genişletin ve **sonra etki alanım ' a** tıklayarak etki **alanım** sayfasını açın. 
   
   ![Etki alanım](./media/jobscience-tutorial/ic767825.png "Etki alanım")

1. Etki alanınızı doğru şekilde ayarlandığını doğrulamak için, bu, "**kullanıcılara dağıtılan 4. adım**" içinde olduğundan emin olun ve "**etki alanı** ayarlarımı" gözden geçirin.

    ![Kullanıcıya dağıtılan etki alanı](./media/jobscience-tutorial/ic784377.png "Kullanıcıya dağıtılan etki alanı")

1. Jobscience şirket sitesinde **güvenlik denetimleri**' ne ve ardından **tek Sign-On ayarları**' na tıklayın.
    
    ![Ekran görüntüsü, güvenlik denetimlerinden seçilen tek Sign-On ayarlarını gösterir.](./media/jobscience-tutorial/ic784364.png "Güvenlik Denetimleri")

1. **Tek Sign-On ayarları** bölümünde aşağıdaki adımları gerçekleştirin:
    
    ![Tek Sign-On ayarları](./media/jobscience-tutorial/ic781026.png "Tek Sign-On ayarları")
    
    a. **SAML etkin**' i seçin.

    b. **Yeni**' ye tıklayın.

1. **SAML tek Sign-On ayarı düzenleme** iletişim kutusunda, aşağıdaki adımları uygulayın:
    
    ![SAML tek Sign-On ayarı](./media/jobscience-tutorial/ic784365.png "SAML tek Sign-On ayarı")
    
    a. **Ad** metin kutusuna yapılandırmanız için bir ad yazın.

    b. **Veren** metin kutusunda, Azure Portal kopyaladığınız **SAML varlık kimliği** değerini yapıştırın.

    c. **Varlık kimliği** metin kutusuna şunu yazın`https://salesforce-jobscience.com`

    d. Azure AD sertifikanızı karşıya yüklemek için **Araştır** ' a tıklayın.

    e. **SAML kimlik türü** olarak, onaylama ' yı seçtiğinizde **Kullanıcı nesnesinden federasyon kimliği bulunur**.

    f. **SAML kimlik konumu** olarak, Select **Identity öğesi Subject ifadesinin NameIdentfier öğesidir**.

    örneğin: **Kimlik sağlayıcısı oturum açma URL** 'si metin kutusunda, Azure Portal kopyaladığınız **SAML tek Sign-On hizmeti URL 'sinin** değerini yapıştırın.

    h. **Kimlik sağlayıcısı oturum kapatma URL 'si** metin kutusunda, Azure Portal kopyaladığınız **oturum kapatma URL 'si** değerini yapıştırın.

    i. **Kaydet**’e tıklayın.

1. Sol gezinti bölmesindeki **Yönet** bölümünde, **etki alanı yönetimi** ' ne tıklayarak ilgili bölümü genişletin ve **sonra etki alanım ' a** tıklayarak etki **alanım** sayfasını açın. 
    
    ![Etki alanım](./media/jobscience-tutorial/ic767825.png "Etki alanım")

1. **Etki alanım** sayfasında, **oturum açma sayfası markalama** bölümünde **Düzenle**' ye tıklayın.
    
    ![Ekran görüntüsü, düzenleme düğmesi ile oturum açma sayfası markalaması bölümünü gösterir.](./media/jobscience-tutorial/ic767826.png "Oturum açma sayfası markası")

1. **Oturum açma sayfası markalama** sayfasında, **kimlik doğrulama HIZMETI** bölümünde **SAML SSO ayarlarınızın** adı görüntülenir. Seçin ve ardından **Kaydet**' e tıklayın.
    
    ![Ekran görüntüsü, PPE ile oturum açma sayfası markalama bölümünü gösterir ve Seçileni kaydeder.](./media/jobscience-tutorial/ic784366.png "Oturum açma sayfası markası")

1. SP tarafından başlatılan çoklu oturum açma URL 'sini almak için **güvenlik denetimleri** menü bölümündeki **Çoklu oturum açma ayarları** ' na tıklayın.

    ![Ekran görüntüsü, tek Sign-On ayarları seçiliyken Yönetim güvenlik denetimlerini gösterir.](./media/jobscience-tutorial/ic784368.png "Güvenlik Denetimleri")
    
    Yukarıdaki adımda oluşturduğunuz SSO profilini tıklatın. Bu sayfada şirketiniz için çoklu oturum açma URL 'SI (örneğin,) gösterilir `https://companyname.my.salesforce.com?so=companyid` .    

> [!TIP]
> Artık, uygulamayı ayarlarken [Azure Portal](https://portal.azure.com)içinde bu yönergelerin kısa bir sürümünü okuyabilirsiniz!  Bu uygulamayı **Active Directory > kurumsal uygulamalar** bölümünden ekledikten sonra, yalnızca **Çoklu oturum açma** sekmesine tıklayın ve en alttaki **yapılandırma** bölümünde yer alan katıştırılmış belgeye erişin. Katıştırılmış belge özelliği hakkında daha fazla bilgi için şu adımları bulabilirsiniz: [Azure AD Embedded belgeleri]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma
Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

![Azure AD kullanıcısı oluşturma][100]

**Azure AD 'de bir test kullanıcısı oluşturmak için aşağıdaki adımları uygulayın:**

1. **Azure Portal**, sol gezinti bölmesinde **Azure Active Directory** simgesine tıklayın.

    ![Ekran görüntüsü Azure portal Azure A D simgesini gösterir.](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Kullanıcıların listesini göstermek için **Kullanıcılar ve gruplar** ' a gidin ve **tüm kullanıcılar**' a tıklayın.
    
    ![Ekran görüntüsü, tüm kullanıcılar seçiliyken Yönet menüsünden seçilen kullanıcıları ve grupları gösterir.](./media/jobscience-tutorial/create_aaduser_02.png) 

1. **Kullanıcı** iletişim kutusunu açmak için iletişim kutusunun üst kısmındaki **Ekle** ' ye tıklayın.
 
    ![Ekran görüntüsü Kullanıcı iletişim kutusunu açmak için Ekle düğmesini gösterir.](./media/jobscience-tutorial/create_aaduser_03.png) 

1. **Kullanıcı** iletişim sayfasında, aşağıdaki adımları uygulayın:
 
    ![Ekran görüntüsü, bu adımdaki değerleri girebileceğiniz Kullanıcı iletişim kutusunu gösterir.](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. **Ad** metin kutusuna **Brittasıon** yazın.

    b. **Kullanıcı adı** metin kutusuna Brittasıon **e-posta adresini** yazın.

    c. **Parolayı göster** ' i seçin ve **parola** değerini yazın.

    d. **Oluştur**’a tıklayın.
 
### <a name="creating-a-jobscience-test-user"></a>Jobscience test kullanıcısı oluşturma

Azure AD kullanıcılarının Jobscience 'de oturum açmasını sağlamak için bunların Jobscience 'a sağlanması gerekir. Jobscience söz konusu olduğunda, sağlama el ile gerçekleştirilen bir görevdir.

>[!NOTE]
>Azure Active Directory Kullanıcı hesapları sağlamak için Jobscience tarafından sunulan diğer Jobscience Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.
>  
        
**Kullanıcı sağlamayı yapılandırmak için aşağıdaki adımları uygulayın:**

1. **Jobscience** şirket sitenizde yönetici olarak oturum açın.

1. Kurulum 'a gidin.
   
   ![Ekran görüntüsü, kurulum öğesini gösterir.](./media/jobscience-tutorial/ic784358.png "Kurulum")
1. **Kullanıcıları Yönet \>**' e gidin.
   
   ![Kullanıcılar](./media/jobscience-tutorial/ic784369.png "Kullanıcılar")
1. **Yeni Kullanıcı**' ya tıklayın.
   
   ![Tüm Kullanıcılar](./media/jobscience-tutorial/ic784370.png "Tüm Kullanıcılar")
1. **Kullanıcı Düzenle** iletişim kutusunda aşağıdaki adımları gerçekleştirin:
   
   ![Kullanıcı düzenleme](./media/jobscience-tutorial/ic784371.png "Kullanıcı düzenleme")
   
   a. **Ad** metin kutusuna, kullanıcının adını, Britta gibi bir kez yazın.
   
   b. Soyadı metin kutusunda, Simon **adlı** kullanıcının soyadını yazın.
   
   c. **Diğer ad** metin kutusuna, Brittas gibi kullanıcının diğer adını yazın.

   d. **E-posta** metin kutusuna, gibi kullanıcının e-posta adresini yazın Brittasimon@contoso.com .

   e. **Kullanıcı adı** metin kutusuna, gibi bir Kullanıcı adı yazın Brittasimon@contoso.com .

   f. **Nick adı** metin kutusunda, Simon gibi bir Kullanıcı adı yazın.

   örneğin: **Kaydet**’e tıklayın.

    
> [!NOTE]
> Azure Active Directory hesap sahibi bir e-posta alır ve etkin hale gelmeden önce hesaplarını doğrulamak için bir bağlantıyı izler.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD test kullanıcısına atama

Bu bölümde, Jobscience 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

![Ekran görüntüsü, hesap görünen adını gösterir.][200] 

**Jobscience 'e Britta Simon atamak için aşağıdaki adımları uygulayın:**

1. Azure portal, uygulamalar görünümünü açın ve ardından dizin görünümüne gidip **Kurumsal uygulamalar** ' a gidin ve ardından **tüm uygulamalar**' a tıklayın.

    ![Ekran görüntüsü, Azure portal menüsündeki tüm uygulamalar seçili olan kurumsal uygulamaları gösterir.][201] 

1. Uygulamalar listesinde **Jobscience**' yı seçin.

    ![Ekran görüntüsü Jobscience seçili olduğunu gösterir.](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. Soldaki menüde **Kullanıcılar ve gruplar**' a tıklayın.

    ![Ekran görüntüsü, Azure portal menüsünden seçilen kullanıcıları ve grupları gösterir.][202] 

1. **Ekle** düğmesine tıklayın. Sonra **atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Ekran görüntüsü, atamalar eklemek için kullanılan Ekle düğmesini gösterir.][203]

1. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' u seçin.

1. **Kullanıcılar ve gruplar** Iletişim kutusunda **Seç** düğmesine tıklayın.

1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.
    
### <a name="testing-single-sign-on"></a>Çoklu oturum açmayı test etme

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Jobscience kutucuğuna tıkladığınızda, Jobscience uygulamanızda otomatik olarak oturum açmış olmanız gerekir.
Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

* [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](tutorial-list.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png