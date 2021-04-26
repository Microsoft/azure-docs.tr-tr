---
title: 'Öğretici: Bamboo için EasySSO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Bamboo için Azure Active Directory ile Easysarasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 3d784979876857e94c1e09ffc5b7bef11c0a0d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bamboo"></a>Öğretici: Bamboo için Easyswith ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Bamboo için EasySSO Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. EasySSO 'yi Azure AD ile Bamboo için tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Bamboo 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Bamboo otomatik olarak oturum açmalarına olanak tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Bamboo çoklu oturum açma (SSO) özellikli abonelik için EasySSO.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Bamboo için EasySSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Bamboo için EasySSO **, yalnızca zaman** Kullanıcı sağlamasını destekler

## <a name="adding-easysso-for-bamboo-from-the-gallery"></a>Galeriden Bamboo için EasySSO ekleniyor

EasysBamboo 'in Azure AD 'ye tümleştirilmesine yönelik olarak yapılandırmak için, Galeriden Bamboo için EasySSO 'yı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Bamboo Için easysso** yazın.
1. Sonuçlar panelinden **Bamboo Için Easysso** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bamboo"></a>Bamboo için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak Bamboo IÇIN Azure AD SSO 'yu yapılandırıp test edin. SSO 'nun çalışması için, Bamboo için bir Azure AD kullanıcısı ve Easysile ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Bamboo için Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Bamboo SSO Için EasySSO](#configure-easysso-for-bamboo-sso)** 'yi, uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için yapılandırın.
    1. **[Bamboo test kullanıcısı için EasySSO](#create-easysso-for-bamboo-test-user)** , kullanıcının Azure AD gösterimine bağlı Bamboo Için easysso 'da buna karşılık gelen B. Simon 'a sahip olması için oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, Bamboo uygulama tümleştirmesi **Için Easysso** sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/plugins/servlet/easysso/saml`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri şüpheli olarak almak için [Easysso destek ekibine](mailto:support@techtime.co.nz) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Bamboo uygulaması için EasySSO, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki Bamboo uygulaması için EasySSO, daha fazla özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name | Kaynak özniteliği |
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. UserPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | Kullanıcı. Mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | User. DisplayName |
    | urn: OID: 2.5.4.4 | User. soyadı |
    | urn: OID: 2.5.4.42 | Kullanıcı. |
    
    Azure AD kullanıcılarınızın **sAMAccountName** ile yapılandırılmış olması durumunda **urn: OID: 0.9.2342.19200300.100.1.1** ' i **sAMAccountName** özniteliğine eşlemeniz gerekir.
    
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** veya **Federasyon meta verileri XML** seçenekleri için bağlantıları **İndir** ' e tıklayın ve bilgisayarınıza ya da tümünü kaydedin. Daha sonra Bamboo Easysconfigure 'ı yapılandırmak için buna ihtiyacınız olacaktır.

    ![Sertifika indirme bağlantısı](./media/easysso-for-bamboo-tutorial/certificate.png)
    
    Bamboo yapılandırması için EasySSO 'yi sertifikayla el ile yapmayı planlıyorsanız, **oturum açma URL 'sini** ve **Azure ad tanımlayıcısını** aşağıdaki bölümden kopyalamanız ve bilgisayarınıza kaydetmeniz gerekir.

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

Bu bölümde, Bamboo için Easys'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Bamboo Için Easysso**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-easysso-for-bamboo-sso"></a>Bamboo SSO için EasySSO yapılandırma

1. Yakınlaştırma sırasında yapılandırmayı otomatikleştirmek için, **uzantıyı yüklemeniz**' ne tıklayarak **uygulamalarımın güvenli oturum açma tarayıcı uzantısını** yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **ayarı** Yakınlaştır ' a tıklayarak sizi yakınlaştırma uygulamasına yönlendirebilirsiniz. Buradan, yakınlaştırma sırasında oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-10 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. Farklı bir Web tarayıcısı penceresinde yakınlaştırmayı el ile ayarlamak istiyorsanız, yakınlaştırma şirket sitenizde yönetici olarak oturum açın.

1. **Uygulamaları Yönet** bölümüne gidin. 

    ![Uygulamaları Yönetme](./media/easysso-for-bamboo-tutorial/bamboo-admin-1.png)

2. Sol tarafta **Easysso** öğesini bulun ve tıklatın.

    ![Kolay SSO](./media/easysso-for-bamboo-tutorial/bamboo-admin-2.png)

3. **SAML** seçeneğini belirleyin. Bu, sizi SAML yapılandırması bölümüne götürür.

    ![SAML](./media/easysso-for-bamboo-tutorial/bamboo-admin-3.png)

4. Üstteki **Sertifikalar** sekmesini seçin ve aşağıdaki ekran görüntülenir:

    ![Meta veri URL 'SI](./media/easysso-for-bamboo-tutorial/bamboo-admin-4.png)

5. Şimdi **Azure AD SSO** yapılandırmasının önceki adımlarında kaydettiğiniz **sertifika (base64)** veya **meta veri dosyasını** bulun. İlerlemeniz için aşağıdaki seçenekleri kullanabilirsiniz:

    a. Bilgisayarınızda yerel dosyaya indirdiğiniz uygulama Federasyon **meta veri dosyasını** kullanın. **Yükleme** radyo düğmesini seçin ve işletim sisteminize özel karşıya yükleme dosyası iletişim kutusunu izleyin

    **OR**

    b. Dosyanın içeriğini (herhangi bir düz metin düzenleyicisinde) görmek ve panoya kopyalamak için uygulama Federasyon **meta verileri dosyasını** açın. **Giriş** seçeneğini belirleyin ve Pano içeriğini metin alanına yapıştırın.
 
    **OR**

    c.  Tam el ile yapılandırma. Dosyanın içeriğini (herhangi bir düz metin düzenleyicisinde) görmek ve panoya kopyalamak için uygulama Federasyon **sertifikası 'nı (base64)** açın. **IDP belirteç Imzalama sertifikaları** metin alanına yapıştırın. Ardından **genel** sekmesine gidin ve bağlama URL **'SI** ve **varlık KIMLIĞI** alanlarını, **oturum açma URL** 'si ve daha önce kaydettiğiniz **Azure AD tanımlayıcısı** için ilgili değerlerle birlikte girin.
 
6. Sayfanın alt kısmındaki **Kaydet** düğmesine tıklayın. Meta verilerin içeriğini veya sertifika dosyalarını yapılandırma alanlarına ayrıştırmış olursunuz. Bamboo yapılandırması için EasySSO tamamlanmıştır.

7. En iyi test deneyimi için, **&** Me sekmesine gidin ve üzerinde **SAML oturum açma düğmesi** seçeneğini işaretleyin. Bu, özellikle Azure AD SAML tümleştirme uçtan uca test etmek için Bamboo oturum açma ekranında ayrı bir düğmeyi etkinleştirir. Bu düğmeyi açabilir ve üretim modu için yerleşimini, rengini ve çevirisini de yapılandırabilirsiniz.

    ![& göz atın](./media/easysso-for-bamboo-tutorial/bamboo-admin-5.png)

    > [!NOTE]
    > Herhangi bir sorununuz olması gerekir, lütfen [Easysso destek ekibine](mailto:support@techtime.co.nz)başvurun.

### <a name="create-easysso-for-bamboo-test-user"></a>Bamboo test kullanıcısı için EasySSO oluştur

Bu bölümde, Bamboo içinde Britta Simon adlı bir Kullanıcı oluşturulur. Bamboo için EasySSO, varsayılan olarak **devre dışı** olan tam zamanında Kullanıcı sağlamayı destekler. Kullanıcı sağlamayı etkinleştirmek için EasySSO eklentisi yapılandırmasının Genel bölümünde oturum **açma başarılı olduğunda Kullanıcı oluşturma** seçeneğini açıkça denetlemeniz gerekir. Bir Kullanıcı Bamboo içinde zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

Ancak, Kullanıcı ilk oturum açmada otomatik Kullanıcı sağlamayı etkinleştirmek istemiyorsanız, kullanıcıların arka uç Kullanıcı dizinlerinde olması gerekir; örneğin, Bamboo örneği, LDAP veya Atlasduyumu Crowd gibi.

![Kullanıcı sağlama](./media/easysso-for-bamboo-tutorial/bamboo-admin-6.png)

## <a name="test-sso"></a>Test SSO 'SU 

### <a name="idp-initiated-workflow"></a>IDP tarafından başlatılan iş akışı

Bu bölümde, uygulamalarımı kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Uygulamalarım için EasySSO for Bamboo kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Bamboo örneğinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

### <a name="sp-initiated-workflow"></a>SP tarafından başlatılan iş akışı

Bu bölümde, Bamboo **SAML Login** düğmesini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

![Kullanıcı SAML oturum açma](./media/easysso-for-bamboo-tutorial/bamboo-admin-7.png)

Bu senaryo, Bamboo EasySSO yapılandırma sayfanızdaki **&** Me sekmesinde **SAML oturum açma düğmesini** etkinleştirmiş olduğunu varsayar (yukarıya bakın). Mevcut oturumlarınız ile herhangi bir girişimden kaçınmak için, Bamboo oturum açma URL 'nizi tarayıcıda geçersiz modda açın. **SAML oturum açma** düğmesine TıKLADıĞıNıZDA Azure AD Kullanıcı kimlik doğrulama akışına yönlendirilirsiniz. Başarılı bir şekilde tamamlandıktan sonra, SAML aracılığıyla kimliği doğrulanmış kullanıcı olarak Bamboo örneğinize yeniden yönlendirilirsiniz.

Azure AD 'den geri yönlendirildikten sonra aşağıdaki ekranla karşılaşacağınız bir olasılık vardır

![EasySSO hata ekranı](./media/easysso-for-bamboo-tutorial/bamboo-admin-8.png)

Bu durumda, **Atlassian-Bamboo. log** dosyasına erişim sağlamak için [Bu sayfadaki yönergeleri]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) izlemeniz gerekir. Hatanın ayrıntıları EasySSO hata sayfasında bulunan başvuru Kımlığı tarafından kullanılabilir olacaktır.

Günlük iletilerini geri almaya yönelik herhangi bir sorununuz olması gerekir, lütfen [Easysso destek ekibine](mailto:support@techtime.co.nz)başvurun.

## <a name="next-steps"></a>Sonraki adımlar

Bamboo için EasySSO 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).