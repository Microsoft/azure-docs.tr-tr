---
title: 'Öğretici: BitBucket için EasySSO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: BitBucket için Azure Active Directory ile Easysarasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 56f2eea9dc485c69b6070fda6e9519887f7b30cb
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519665"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Öğretici: BitBucket için EasySSO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile BitBucket için EasySSO 'ı nasıl tümleştirileceğini öğreneceksiniz. EasySSO 'yi, Azure AD ile BitBucket için tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de BitBucket için Easysa erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla BitBucket için otomatik olarak oturum açabilmesi için bu ayarı etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Çoklu oturum açma (SSO) için etkinleştirilen BitBucket için EasySSO aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* BitBucket için EasySSO, SP tarafından başlatılan ve IDP tarafından başlatılan SSO 'yu destekler.
* BitBucket için EasySSO, "Just-In-Time" Kullanıcı sağlamasını destekler.

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Galeriden BitBucket için EasySSO ekleyin

BitBucket için easysto 'un Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden BitBucket için EasySSO 'yı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak Azure portal oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar**' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni bir uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Bitbucket Için easysso** yazın.
1. Sonuçlardan **BitBucket Için Easysso** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>BitBucket için Easysiçin Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Bitbucket Için EasySSO Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında BitBucket için bağlantılı bir ilişki kurmanız gerekir.

BitBucket için Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. [Bit demeti SSO 'su](#configure-easysso-for-bitbucket-sso) uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak Için EasySSO 'ı yapılandırın.
    1. [Bitbucket test kullanıcısının](#create-an-easysso-for-bitbucket-test-user) , Kullanıcı Azure AD gösterimine bağlı olan Bitbucket Için Easysso 'da B. Simon 'a karşılık gelen bir Easysso oluşturun.
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **BitBucket Için Easysso** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun. **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Tek Sign-On SAML sayfası ile ayarlama ekran görüntüsü, kurşun kalem simgesi vurgulanmış](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`https://<server-base-url>/plugins/servlet/easysso/saml`

1. **Ek URL 'Ler ayarla**' yı seçin ve uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları izleyin:

    - **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanan bir URL yazın:`https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Şüpheli olursa bu değerleri almak için [Easysso destek ekibine](mailto:support@techtime.co.nz) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. BitBucket uygulaması için EasySSO, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Varsayılan özniteliklerin ekran görüntüsü](common/default-attributes.png)

1. BitBucket uygulaması için EasySSO, SAML yanıtına daha fazla özniteliğin geri geçirilmesini de bekler. Aşağıdaki tabloda bunlar gösterilmektedir. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre gözden geçirebilirsiniz.
    
    | Name | Kaynak özniteliği|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. UserPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | Kullanıcı. Mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | User. DisplayName |
    | urn: OID: 2.5.4.4 | User. soyadı |
    | urn: OID: 2.5.4.42 | Kullanıcı. |
    
    Azure AD kullanıcılarınız için **sAMAccountName** yapılandırıldıysa, **urn: OID: 0.9.2342.19200300.100.1.1** öğesini **sAMAccountName** özniteliğine eşlemeniz gerekir.
    
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** veya **Federasyon meta verileri XML** seçenekleri için indirme bağlantıları ' nı seçin. Ya da her ikisini de bilgisayarınıza kaydedin. Daha sonra BitBucket Easysi 'yi yapılandırmak için buna ihtiyacınız olacak.

    ![Yükleme bağlantıları vurgulanmış şekilde SAML Imzalama sertifikası bölümünün ekran görüntüsü](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Bir sertifikayla BitBucket için EasySSO yapılandırmayı planlıyorsanız, **oturum açma URL 'sini** ve **Azure ad tanımlayıcısını** kopyalamanız ve bilgisayarınıza kaydetmeniz gerekir.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal bir test kullanıcısı olan B. Simon oluşturacaksınız.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** için girin `B.Simon` .
   1. **Kullanıcı adı** için öğesini girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından parolayı yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, BitBucket için Easysa erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure Portal **Kurumsal uygulamalar**  >  **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **BitBucket Için Easysso** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı ekle**'yi seçin. **Atama Ekle** Iletişim kutusunda **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, **Kullanıcılar** listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmında **Seç** ' i seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

## <a name="configure-easysso-for-bitbucket-sso"></a>BitBucket SSO için EasySSO yapılandırma

1. Yakınlaştırma sırasında yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımın güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **ayarı** Yakınlaştır ' a tıklayarak sizi yakınlaştırma uygulamasına yönlendirebilirsiniz. Buradan, yakınlaştırma sırasında oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-10 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Farklı bir Web tarayıcısı penceresinde yakınlaştırmayı el ile ayarlamak istiyorsanız, yakınlaştırma şirket sitenizde yönetici olarak oturum açın.

1. **Yönetim** bölümüne gidin.

    ![Dişli simgesi vurgulanmış şekilde BitBucket örneğinin ekran görüntüsü](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. **Easysso** öğesini bulun ve seçin.

    ![Kolay SSO seçeneğinin ekran görüntüsü](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. **SAML**'yi seçin. Bu sizi SAML yapılandırması bölümüne götürür.

    ![SAML vurgulanmış olarak EasySSO Yönetim sayfasının ekran görüntüsü](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. **Sertifikalar** sekmesini seçin ve aşağıdaki ekranla karşılaşırsınız:

    ![Çeşitli seçenekler vurgulanmış şekilde sertifikalar sekmesinin ekran görüntüsü](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Bu öğreticinin önceki bölümünde kaydettiğiniz **sertifika (base64)** veya **meta veri dosyasını** bulun. Aşağıdaki yollarla devam edebilirsiniz:

    - Bilgisayarınızda yerel bir dosyaya indirdiğiniz uygulama Federasyon **meta veri dosyasını** kullanın. Radyoyu **karşıya yükle** düğmesini seçin ve işletim sisteminize özgü yolu izleyin.

    - Herhangi bir düz metin düzenleyicisinde dosyanın içeriğini görmek için uygulama Federasyon **meta verileri dosyasını** açın. Panoyu Pano 'ya kopyalayın. **Giriş**' i seçin ve Pano içeriğini metin alanına yapıştırın.
 
    - Tam el ile yapılandırma yapın. Herhangi bir düz metin düzenleyicisinde dosyanın içeriğini görmek için uygulama Federasyon **sertifikası 'nı (base64)** açın. Pano üzerine kopyalayın ve **IDP belirteç Imzalama sertifikaları** metin alanına yapıştırın. Daha sonra **genel** sekmesine gidin ve **bağlama URL 'SI** ve **varlık KIMLIĞI** alanlarını, **oturum açma URL** 'si ve daha önce kaydettiğiniz **Azure AD tanımlayıcısı** için ilgili değerlerle girin.
 
1. Sayfanın alt kısmındaki **Kaydet** ' i seçin. Meta veri veya sertifika dosyalarının içeriğinin yapılandırma alanlarıyla ayrıştırıldığına bakabilirsiniz. BitBucket yapılandırması için EasySSO işlemi tamamlanmıştır.

1. Yapılandırmayı test etmek için **& göz atın** sekmesine gidin ve **SAML oturum açma düğmesini** seçin. Bu, özellikle Azure AD SAML tümleştirmenizi uçtan uca test etmek için BitBucket oturum açma ekranında ayrı bir düğme sağlar. Bu düğmeyi açık bırakabilir ve üretim modu için yerleşimini, rengini ve çevirisini de yapılandırabilirsiniz.

    ![SAML oturum açma düğmesi vurgulanmış şekilde SAML sayfasının ekran görüntüsü & Me sekmesi](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Herhangi bir sorununuz varsa, [Easysso destek ekibine](mailto:support@techtime.co.nz)başvurun.

### <a name="create-an-easysso-for-bitbucket-test-user"></a>BitBucket test kullanıcısı için bir EasySSO oluşturun

Bu bölümde, BitBucket 'da Britta Simon adlı bir Kullanıcı oluşturacaksınız. BitBucket için EasySSO, varsayılan olarak devre dışı olan tam zamanında Kullanıcı sağlamayı destekler. Bunu etkinleştirmek için EasySSO eklentisinin **genel** bölümünde, **başarılı oturum açma durumunda Kullanıcı oluşturmayı** açıkça denetlemeniz gerekir. Bir Kullanıcı BitBucket 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

Ancak, Kullanıcı ilk kez oturum açtığında otomatik Kullanıcı sağlamayı etkinleştirmek istemiyorsanız, kullanıcıların BitBucket örneğinin kullandığı kullanıcı dizinlerinde mevcut olmaları gerekir. Örneğin, bu dizin LDAP veya Atlasbir Crowd olabilir.

![Başarılı oturum açma üzerinde kullanıcı oluştur vurgulanarak EasySSO eklenti yapılandırmasının genel bölümünün ekran görüntüsü](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum akışını başlatabileceğiniz BitBucket oturum açma URL 'SI için EasySSO 'a yönlendirilir.

* BitBucket oturum açma URL 'SI için doğrudan Easysna gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Bitbucket Için EasySSO 'da otomatik olarak oturum açmış olmanız gerekir

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım için EasySSO kutucuğunda tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız BitBucket için EasySSO 'da otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

BitBucket için EasySSO 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve bu verilerin bir listesini koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).