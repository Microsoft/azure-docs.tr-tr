---
title: 'Öğretici: çözüm GmbH için SAML SSO ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAML SSO ile cira tarafından çözümleme GmbH arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 827a05a8dfbf05b0dacb0bd812fb964567f39b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954216"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Öğretici: çözüm GmbH ile Jira için SAML SSO ile tümleştirme Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile bir çözüm GmbH için SAML SSO 'yu nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile cira tarafından çözüm GmbH için SAML SSO 'yu tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, cira tarafından çözümleme GmbH için SAML SSO 'ya erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla çözüm GmbH için SAML SSO 'yu otomatik olarak oturum açarak etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Cira tarafından çözümleme GmbH çoklu oturum açma (SSO) özellikli abonelik için SAML SSO 'SU.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Cira by Resolution GmbH için SAML SSO, **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler.

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Galeriden cira tarafından çözüm GmbH için SAML SSO 'SU ekleme

Cira tarafından çözüm GmbH tarafından Azure AD 'ye yönelik SAML SSO tümleştirmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize bir çözüm GmbH için SAML SSO 'SU eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Jira by Resolution GmbH için SAML SSO** yazın.
1. Sonuçlar panelinden **cira tarafından çözüm GmbH Için SAML SSO** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Cira tarafından çözümleme GmbH için SAML SSO 'SU için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Jira tarafından çözüm GmbH için SAML SSO 'Su Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve SAML SSO 'SU ile ilgili Kullanıcı arasında, çözümleme GmbH ile ilgili bir bağlantı oluşturmanız gerekir.

Azure AD SSO 'yu, cira tarafından çözümleme GmbH için SAML SSO 'SU ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için, **[çözüm GmbH SSO 'su IÇIN SAML SSO 'Yu yapılandırın](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** .
    1. User 'ın Azure AD gösterimine bağlı olan, çözümleme GmbH için SAML SSO 'da B. Simon 'a sahip olacak şekilde, **[cira tarafından çözümleme GmbH test kullanıcısı IÇIN SAML SSO 'Su oluşturun](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **cira tarafından çözümleme GmbH uygulama tümleştirmesi Için SAML SSO** sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tanımlayıcı, yanıt URL 'si ve oturum açma URL 'SI için, **\<server-base-url>** Jira örneğinizin temel URL 'si ile değiştirin. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz. Bir sorununuz varsa, [cira tarafından çözümleme GmbH istemci desteği ekibi Için SAML SSO 'su](https://www.resolution.de/go/support)' nde bizimle iletişime geçin.

4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i indirin ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, cira tarafından çözüm GmbH göre SAML SSO 'SU erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **cira by Resolution GmbH Için SAML SSO**'yu seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Cira tarafından çözüm GmbH SSO 'SU için SAML SSO 'yu yapılandırma 

1. Farklı bir Web tarayıcısı penceresinde, Jira örneğiniz için yönetici olarak oturum açın.

2. Sağ tarafta dişli üzerine gelin ve **Uygulamaları Yönet**' e tıklayın.
    
    !["COG" simgesine işaret eden oku ve açılan listeden "Uygulamaları Yönet" i gösteren ekran görüntüsü.](./media/samlssojira-tutorial/add-on-1.png)

3. Yönetici erişimi sayfasına yönlendiriliyorsunuz, **parolayı** girin ve **Onayla** düğmesine tıklayın.

    !["Yönetici erişimi" sayfasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/add-on-2.png)

4. Jira genellikle sizi Atlasısize Market 'e yönlendirir. Aksi takdirde, sol panelde **yeni uygulamalar bul** ' a tıklayın. **JIRA Için SAML çoklu oturum açma (SSO)** araması YAPıN ve SAML eklentisini yüklemek için **Install** düğmesine tıklayın.

    !["A m L tek oturum açma (S S O) Jira, a m L/S S O" uygulaması için "Install" düğmesine işaret eden bir oka sahip "JIRA için Atlasıra marketi" sayfasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/store.png)

5. Eklenti yüklemesi başlar. İşlem tamamlandığında **Kapat** düğmesine tıklayın.

    !["Yükleme" iletişim kutusunu gösteren ekran görüntüsü.](./media/samlssojira-tutorial/store-2.png)

    !["Yüklü ve hazırlanmaya başlamaya!" gösteren ekran görüntüsü "Kapat" düğmesi seçili iletişim kutusu.](./media/samlssojira-tutorial/store-3.png)

6. Ardından **Yönet**' e tıklayın.

    !["Yönet" düğmesinin seçili olduğu "S a M L Single Sign on (S O) Jira, S A m L/S S O" uygulamasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/store-4.png)
    
7. Daha sonra, yeni yüklenen eklentiyi yapılandırmak için **Yapılandır** ' a tıklayın.

    !["Uygulamaları Yönet" sayfasını gösteren ekran görüntüsü "Configure a m L Teksignon for Jira" uygulaması için "Yapılandır" düğmesi seçilidir.](./media/samlssojira-tutorial/store-5.png)

8. Azure AD 'yi yeni bir kimlik sağlayıcısı olarak yapılandırmak için **SAML SingleSignOn eklenti yapılandırma** sihirbazında **Yeni IDP Ekle** ' ye tıklayın.

    ![Ekran görüntüsünde, "yeni ı d P ekle" düğmesi seçiliyken "hoş geldiniz" sayfası görüntülenir.](./media/samlssojira-tutorial/add-on-4.png) 

9. **SAML kimlik sağlayıcınızı seçin** sayfasında aşağıdaki adımları uygulayın:

    !["I d P Type" ve "Name" metin kutuları vurgulanmış ve "Ileri" düğmesi seçili olan "S A M L-m kimlik sağlayıcısını seçin" sayfasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. **Azure AD** 'Yi IDP türü olarak ayarlayın.
    
    b. Kimlik sağlayıcısının **adını** ekleyin (ör. Azure AD).
    
    c. Kimlik sağlayıcısı (ör. Azure AD) için (isteğe bağlı) bir **Açıklama** ekleyin.
    
    d. **İleri**’ye tıklayın.
    
10. **Kimlik sağlayıcısı yapılandırma** sayfasında **İleri**' ye tıklayın.
 
    !["Kimlik sağlayıcısı yapılandırması" sayfasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/configuration.png)

11. **SAML IDP meta verilerini Içeri aktar** sayfasında, aşağıdaki adımları uygulayın:

    !["Meta veri X M L dosyası seçin" eyleminin seçili olduğu "a M L L L P meta verilerini Içeri aktar" sayfasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/metadata.png)

    a. **Meta VERI XML dosyası seç** düğmesine tıklayın ve daha önce Indirdiğiniz **Federasyon meta veri XML** dosyasını seçin.

    b. **Al** düğmesine tıklayın.
     
    c. İçeri aktarma işlemi başarılı olana kadar kısa bir süre bekleyin.  
     
    d. **İleri** düğmesine tıklayın.
    
12. **Kullanıcı kimliği özniteliği ve dönüştürme** sayfasında, **İleri** düğmesine tıklayın.

    !["Ileri" düğmesi seçili "Kullanıcı I D özniteliği ve dönüşümü" sayfasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/transformation.png)
    
13. **Kullanıcı oluşturma ve güncelleştirme** sayfasında, Ayarları Kaydet ' in **yanındaki & kaydet** ' e tıklayın.
    
    !["Kullanıcı oluşturma ve güncelleştirme" sayfasını "& sonrakini Kaydet" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/samlssojira-tutorial/update.png)
    
14. **Ayarlarınızı test** etme sayfasında, şimdi için Kullanıcı testini atlamak üzere **testi atla & el ile yapılandır** ' ı tıklatın. Bu, sonraki bölümde gerçekleştirilecek ve Azure portal bazı ayarlar gerektirir.
    
    !["Test & el ile yapılandırmayı atla" düğmesinin seçili olduğu "ayarlarınızı test etme" sayfasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/test.png)
    
15. Uyarıyı atlamak için **Tamam** ' ı tıklatın.
    
    !["O K" düğmesinin seçili olduğu uyarı iletişim kutusunu gösteren ekran görüntüsü.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Çözüm GmbH test kullanıcısı tarafından Jira tarafından SAML SSO oluşturma

Azure AD kullanıcılarının, cira tarafından çözüm GmbH için SAML SSO 'ya oturum açmasını sağlamak için, bu kişiler, cira tarafından çözüm GmbH tarafından sağlanan SAML SSO 'ya sağlanmalıdır. Bu öğreticide, sağlamayı el ile yapmanız gerekir. Ancak, aynı zamanda SAML SSO eklentisi için çözüm tarafından sağlanan diğer sağlama modelleri de vardır. Örneğin, **tam zamanında** sağlama. [Çözüm GmbH göre SAML SSO 'su](https://wiki.resolution.de/doc/saml-sso/latest/all)hakkındaki belgelerine bakın. Onunla ilgili sorularınız varsa, [çözüm desteğiyle](https://www.resolution.de/go/support)desteğe başvurun.

**Bir kullanıcı hesabını el ile sağlamak için aşağıdaki adımları gerçekleştirin:**

1. Jira örneğinde yönetici olarak oturum açın.

2. Dişli 'nin üzerine gelin ve **Kullanıcı yönetimi**' ni seçin.

   ![Açılan ekran görüntüsü, açılan listeden "Kullanıcı Yönetimi" simgesine işaret eden "COG" simgesine işaret eden bir görüntüler.](./media/samlssojira-tutorial/user-1.png)

3. Yönetici erişimi sayfasına yönlendiriliyorsunuz, **parolayı** girin ve **Onayla** düğmesine tıklayın.

    !["Parola" metin kutusu vurgulanmış "yönetici erişimi" sayfasını gösteren ekran görüntüsü.](./media/samlssojira-tutorial/user-2.png) 

4. **Kullanıcı yönetimi** sekmesi bölümünde **Kullanıcı oluştur**' a tıklayın.

    !["Kullanıcı oluşturma" düğmesinin seçili olduğu "Kullanıcı Yönetimi" sekmesini gösteren ekran görüntüsü.](./media/samlssojira-tutorial/user-3-new.png) 

5. **"Yeni Kullanıcı Oluştur"** iletişim sayfasında aşağıdaki adımları gerçekleştirin. Kullanıcıyı Azure AD 'de tam olarak şöyle oluşturmanız gerekir:

    ![Çalışan Ekle](./media/samlssojira-tutorial/user-4-new.png) 

    a. **E-posta adresi** metin kutusuna kullanıcının e-posta adresini yazın: <b>BrittaSimon@contoso.com</b> .

    b. **Tam ad** metin kutusuna kullanıcının tam adını yazın: **Britta Simon**.

    c. Kullanıcı **adı** metin kutusuna kullanıcının e-posta adresini yazın: <b>BrittaSimon@contoso.com</b> . 

    d. **Parola** metin kutusuna kullanıcının parolasını girin.

    e. Kullanıcı oluşturmayı sona erdirmesi için **Kullanıcı oluştur** ' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz bir çözüm GmbH oturum açma URL 'SI için SAML SSO 'ya yeniden yönlendirilir.  

* Doğrudan çözümleme GmbH oturum açma URL 'SI için SAML SSO 'ya gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal ' de **Bu uygulamayı test et** ' e tıklayın ve SSO 'yu ayarladığınız cira tarafından GmbH için SAML SSO 'su için otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki Jira by Resolution GmbH kutucuğunun SAML SSO 'SU ' ne tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız cira tarafından çözümleme GmbH için SAML SSO 'ya otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="enable-sso-redirection-for-jira"></a>Jira için SSO yeniden yönlendirmeyi etkinleştirme

Daha önce bölümünde belirtildiği gibi, çoklu oturum açmayı tetiklemenin Şu anda iki yolu vardır. **Azure Portal** kullanarak ya da **Jira örneğiniz için özel bir bağlantı** kullanarak. Çözüme göre SAML SSO eklentisi GmbH, yalnızca **Jira örneğinizi gösteren herhangi BIR URL 'ye erişerek** çoklu oturum açmayı tetiklemeniz de sağlar.

Temelde, Jira 'ya erişen tüm kullanıcılar, eklentisindeki bir seçenek etkinleştirildikten sonra çoklu oturum açma 'ya yönlendirilir.

SSO yeniden yönlendirmeyi etkinleştirmek için, **Jira örneğiniz** içinde aşağıdakileri yapın:

1. Jira içindeki SAML SSO eklentisinin yapılandırma sayfasına erişin.
1. Sol panelde **yeniden yönlendirme** ' ye tıklayın.

   ![Sol gezinmede yeniden yönlendirme bağlantısını vurgulayan Jira SAML SingleSignOn Plugin yapılandırma sayfasının kısmi ekran görüntüsü.](./media/samlssojira-tutorial/configure-1.png)

1. Değer **SSO yeniden yönlendirmeyi etkinleştirin**.

   ![Jira SAML SingleSignOn Plugin yapılandırma sayfasının, seçili "SSO yeniden yönlendirmeyi etkinleştir" onay kutusunu vurgulayan kısmi ekran görüntüsü.](./media/samlssojira-tutorial/configure-2.png) 

1. Sağ üst köşedeki **Ayarları Kaydet** düğmesine basın.

Seçeneği etkinleştirdikten sonra, ' ye gidildiğinde **Nosso etkinleştir** seçeneği ele alındıktan sonra Kullanıcı adı/parola istemine erişmeye devam edebilirsiniz `https://<server-base-url>/login.jsp?nosso` . Her zaman olduğu gibi, **\<server-base-url>** temel URL 'niz ile değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Cira tarafından çözüm GmbH için SAML SSO 'yu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve geri alımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).