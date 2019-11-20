---
title: 'Öğretici: çözüm GmbH için SAML SSO ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAML SSO ile cira tarafından çözümleme GmbH arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957fee48397bc0b23737157dec0e74cf6505fab5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160137"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Öğretici: çözüm GmbH ile Jira için SAML SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile, cira tarafından çözüm GmbH için SAML SSO 'yu ayarlamayı öğreneceksiniz.
Azure AD ile bir çözüm GmbH ile Jira tarafından sağlanan SAML SSO 'SU tümleştirme, aşağıdaki avantajları sağlar:

* Azure AD 'de çözüm GmbH tarafından SAML SSO eklentisi ile Jira 'da oturum açabilen bir denetim yapabilirsiniz.
* Kullanıcılarınızın cira tarafından çözümleme GmbH (çoklu oturum açma) için SAML SSO 'yu kullanarak Azure AD hesaplarıyla otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Cira tarafından çözüm GmbH için Azure AD tümleştirmesini ve SAML SSO 'yu yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* Tek bir oturum açma özellikli abonelik ile Jira by çözünürlüklü GmbH için SAML SSO

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* **SP** ve **IDP** tarafından başlatılan SSO GmbH tarafından desteklenen JIRA için SAML SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Çoklu oturum açma için kurumsal uygulama ekleme

Azure AD 'de çoklu oturum açmayı ayarlamak için yeni bir kurumsal uygulama eklemeniz gerekir. Galeride, bu için önceden yapılandırılmış bir uygulama önayarı vardır, bu, **cira tarafından çözümleme GmbH Için SAML SSO 'su**.

**Galeriden, cira tarafından çözüm GmbH için SAML SSO 'SU eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar**' a gidin ve ardından **tüm uygulamalar**' a tıklayın.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Jira by Resolution GmbH Için SAML SSO**yazın, sonuç panelinden **cira tarafından Resolution GmbH için SAML SSO** 'yu seçin ve sonra uygulamayı eklemek için **Ekle** düğmesine tıklayın. Ayrıca kurumsal uygulamanın adını da değiştirebilirsiniz.

     ![Sonuç listesinde Jira tarafından çözüm GmbH için SAML SSO 'SU](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>SAML SSO eklentisi ve Azure AD ile çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, bir Azure AD kullanıcısı için Jira 'da çoklu oturum açmayı test edecek ve yapılandıracaksınız. Bu, **Britta Simon**adlı bir test kullanıcısı için yapılır.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve çözümleme GmbH ile ilgili SAML SSO 'SU ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirmeniz gerekir:

1. **[Azure AD kurumsal uygulamasını Çoklu oturum açma Için yapılandırma](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** -Azure AD kurumsal uygulamasını Çoklu oturum açma için yapılandırma
2. **[JIRA ÖRNEĞINIZIN SAML SSO eklentisini yapılandırın](#configure-the-saml-sso-plugin-of-your-jira-instance)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırın.
3. Azure **[ad test kullanıcısı oluşturma](#create-an-azure-ad-test-user)** -Azure AD 'de bir test kullanıcısı oluşturun.
1. **[Azure AD test kullanıcısına atama](#assign-the-azure-ad-test-user)** -test kullanıcısını Azure tarafında çoklu oturum açmayı kullanmak üzere etkinleştirir.
1. **[JIRA 'da test kullanıcısı oluşturun](#create-the-test-user-also-in-jira)** -Azure AD test kullanıcısı Için Jira 'da bir karşılık gelen test kullanıcısı oluşturun.
1. **[Çoklu oturum açmayı sına](#test-single-sign-on)** -yapılandırmanın çalışıp çalışmadığını doğrulayın.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Azure AD kurumsal uygulamasını Çoklu oturum açma için yapılandırma

Bu bölümde, Azure portal çoklu oturum açmayı ayarlarsınız.

Jira tarafından çözümleme GmbH için SAML SSO ile çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), yalnızca **Jira tarafından çözüm GmbH kurumsal uygulama için oluşturulan SAML SSO 'su** ' nde, sol panelde **Çoklu oturum açma** ' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. **Çoklu oturum açma yöntemi seç**için, çoklu oturum açmayı etkinleştirmek üzere **SAML** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. Ardından, **temel SAML yapılandırması** iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    ![Cira tarafından çözüm GmbH etki alanı ve URL 'Ler çoklu oturum açma bilgileri için SAML SSO](common/idp-intiated.png)

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<server-base-url>/plugins/servlet/samlsso`

    b. **Yanıt URL 'si** metin kutusuna şu kalıbı kullanarak bir URL yazın: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    ![Cira tarafından çözüm GmbH etki alanı ve URL 'Ler çoklu oturum açma bilgileri için SAML SSO](common/metadata-upload-additional-signon.png)

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tanımlayıcı, yanıt URL 'si ve oturum açma URL 'SI için, **\<Server-Base-url >** JIRA ÖRNEĞINIZIN temel URL 'siyle değiştirin. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz. Bir sorununuz varsa, [cira tarafından çözümleme GmbH istemci desteği ekibi Için SAML SSO 'su](https://www.resolution.de/go/support)' nde bizimle iletişime geçin.

4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i indirin ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>JIRA örneğinizin SAML SSO eklentisini yapılandırın 

1. Farklı bir Web tarayıcısı penceresinde, Jira örneğiniz için yönetici olarak oturum açın.

2. Sağ tarafta dişli üzerine gelin ve **Uygulamaları Yönet**' e tıklayın.
    
    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon1.png)

3. Yönetici erişimi sayfasına yönlendiriliyorsunuz, **parolayı** girin ve **Onayla** düğmesine tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon2.png)

4. Jira genellikle sizi Atlasısize Market 'e yönlendirir. Aksi takdirde, sol panelde **yeni uygulamalar bul** ' a tıklayın. **JIRA Için SAML çoklu oturum açma (SSO)** araması YAPıN ve SAML eklentisini yüklemek için **Install** düğmesine tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/store.png)

5. Eklenti yüklemesi başlar. İşlem tamamlandığında **Kapat** düğmesine tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/store-2.png)

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/store-3.png)

6. Ardından **Yönet**' e tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/store-4.png)
    
8. Daha sonra, yeni yüklenen eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/store-5.png)

9. Azure AD 'yi yeni bir kimlik sağlayıcısı olarak yapılandırmak için **SAML SingleSignOn eklenti yapılandırma** sihirbazında **Yeni IDP Ekle** ' ye tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon4.png) 

10. **SAML kimlik sağlayıcınızı seçin** sayfasında aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon5a.png)
 
    a. **Azure AD** 'Yi IDP türü olarak ayarlayın.
    
    b. Kimlik sağlayıcısının **adını** ekleyin (ör. Azure AD).
    
    c. Kimlik sağlayıcısı (ör. Azure AD) için (isteğe bağlı) bir **Açıklama** ekleyin.
    
    d. **İleri**’ye tıklayın.
    
11. **Kimlik sağlayıcısı yapılandırma** sayfasında **İleri**' ye tıklayın.
 
    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon5b.png)

12. **SAML IDP meta verilerini Içeri aktar** sayfasında, aşağıdaki adımları uygulayın:

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon5c.png)

    a. **Meta VERI XML dosyası seç** düğmesine tıklayın ve daha önce Indirdiğiniz **Federasyon meta veri XML** dosyasını seçin.

    b. **Al** düğmesine tıklayın.
     
    c. İçeri aktarma işlemi başarılı olana kadar kısa bir süre bekleyin.  
     
    d. **İleri** düğmesine tıklayın.
    
13. **Kullanıcı kimliği özniteliği ve dönüştürme** sayfasında, **İleri** düğmesine tıklayın.

    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon5d.png)
    
14. **Kullanıcı oluşturma ve güncelleştirme** sayfasında, Ayarları Kaydet ' in **yanındaki & Kaydet** ' e tıklayın.
    
    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon6a.png)
    
15. **Ayarlarınızı test** etme sayfasında, şimdi için Kullanıcı testini atlamak üzere **testi atla & el ile yapılandır** ' ı tıklatın. Bu, sonraki bölümde gerçekleştirilecek ve Azure portal bazı ayarlar gerektirir.
    
    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon6b.png)
    
16. Uyarıyı atlamak için **Tamam** ' ı tıklatın.
    
    ![Çoklu oturum açmayı yapılandırma](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır. Kullanıcıyla, çoklu oturum açmayı test edersiniz.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. **Kullanıcı özellikleri**' nde aşağıdaki adımları gerçekleştirin:

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Britta Simon**girin.
  
    b. **Kullanıcı adı** alanına <b>BrittaSimon@contoso.com</b>girin.

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, kurumsal uygulamaya Britta Simon ekleyerek çoklu oturum açmayı kullanmasına izin verir.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin. 

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, Bu öğreticinin başlangıcında oluşturduğunuz kurumsal uygulamayı arayın. Öğreticinin adımlarını izlediyseniz, bu, **cira tarafından çözüm GmbH Için SAML SSO**adı verilir. Başka bir ad verildiyse, bu adı arayın.

    ![Uygulamalar listesindeki Jira by Resolution GmbH bağlantısı için SAML SSO](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' a tıklayın.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın altındaki **Seç** düğmesine tıklayın.

6. SAML onaylama 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-the-test-user-also-in-jira"></a>Jira 'da test kullanıcısını da oluşturun

Azure AD kullanıcılarının, cira tarafından çözüm GmbH için SAML SSO 'ya oturum açmasını sağlamak için, bu kişiler, cira tarafından çözüm GmbH tarafından sağlanan SAML SSO 'ya sağlanmalıdır. Bu öğreticide, sağlamayı el ile yapmanız gerekir. Ancak, aynı zamanda SAML SSO eklentisi için çözüm tarafından sağlanan diğer sağlama modelleri de vardır. Örneğin, **tam zamanında** sağlama. [Çözüm GmbH göre SAML SSO 'su](https://wiki.resolution.de/doc/saml-sso/latest/all)hakkındaki belgelerine bakın. Onunla ilgili sorularınız varsa, [çözüm desteğiyle](https://www.resolution.de/go/support)desteğe başvurun.

**Bir kullanıcı hesabını el ile sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Jira örneğinde yönetici olarak oturum açın.

2. Dişli 'nin üzerine gelin ve **Kullanıcı yönetimi**' ni seçin.

   ![Çalışan Ekle](./media/samlssojira-tutorial/user1.png)

3. Yönetici erişimi sayfasına yönlendiriliyorsunuz, **parolayı** girin ve **Onayla** düğmesine tıklayın.

    ![Çalışan Ekle](./media/samlssojira-tutorial/user2.png) 

4. **Kullanıcı yönetimi** sekmesi bölümünde **Kullanıcı oluştur**' a tıklayın.

    ![Çalışan Ekle](./media/samlssojira-tutorial/user3-new.png) 

5. **"Yeni Kullanıcı Oluştur"** iletişim sayfasında aşağıdaki adımları gerçekleştirin. Kullanıcıyı Azure AD 'de tam olarak şöyle oluşturmanız gerekir:

    ![Çalışan Ekle](./media/samlssojira-tutorial/user4-new.png) 

    a. **E-posta adresi** metin kutusuna kullanıcının e-posta adresini yazın: <b>BrittaSimon@contoso.com</b>.

    b. **Tam ad** metin kutusuna kullanıcının tam adını yazın: **Britta Simon**.

    c. Kullanıcı **adı** metin kutusuna kullanıcının e-posta adresini yazın: <b>BrittaSimon@contoso.com</b>. 

    d. **Parola** metin kutusuna kullanıcının parolasını girin.

    e. Kullanıcı oluşturmayı sona erdirmesi için **Kullanıcı oluştur** ' a tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Jira tarafından çözüm GmbH için SAML SSO 'SU ' ne tıkladığınızda, SSO 'yu ayarladığınız bir çözüm GmbH tarafından Jira tarafından otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Ayrıca, [https://\<Server-Base-url >/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso)' ya gitmeniz halinde çoklu oturum açmayı test edebilirsiniz. **\<Server-Base-url >** JIRA ÖRNEĞINIZIN temel URL 'siyle değiştirin.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Jira için çoklu oturum açma yeniden yönlendirmeyi etkinleştirme

Daha önce bölümünde belirtildiği gibi, çoklu oturum açmayı tetiklemenin Şu anda iki yolu vardır. **Azure Portal** kullanarak ya da **Jira örneğiniz için özel bir bağlantı**kullanarak. Çözüme göre SAML SSO eklentisi GmbH, yalnızca **Jira örneğinizi gösteren herhangi BIR URL 'ye erişerek**çoklu oturum açmayı tetiklemeniz de sağlar.

Temelde, Jira 'ya erişen tüm kullanıcılar, eklentisindeki bir seçenek etkinleştirildikten sonra çoklu oturum açma 'ya yönlendirilir.

SSO yeniden yönlendirmeyi etkinleştirmek için, **Jira örneğiniz**içinde aşağıdakileri yapın:

1. Jira içindeki SAML SSO eklentisinin yapılandırma sayfasına erişin.
1. Sol panelde **yeniden yönlendirme** ' ye tıklayın.
![](./media/samlssojira-tutorial/ssore1.png)

1. Değer **SSO yeniden yönlendirmeyi etkinleştirin**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Sağ üst köşedeki **Ayarları Kaydet** düğmesine basın.

Seçeneği etkinleştirdikten sonra, [https://\<Server-Base-url >/login.exe \ SP? Nosso](https://\<server-base-url>/login.jsp?nosso)' ya gidildiğinde **Nosso 'yu etkinleştir** seçeneği ele alındıktan sonra Kullanıcı adı/parola istemine erişmeye devam edebilirsiniz. Her zaman olduğu gibi, **\<Server-Base-url >** temel URL 'niz ile değiştirin.


## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

