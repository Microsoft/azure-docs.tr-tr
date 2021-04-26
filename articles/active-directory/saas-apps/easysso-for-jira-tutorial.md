---
title: 'Öğretici: Jira için EasySSO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory Microsoft Docs'
description: Jira için Azure Active Directory ile Easysarasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: b813f1f77b7bda2627f5275e772a3ee7c9d12834
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454363"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>Öğretici: Jira için EasySSO ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile Jira için EasySSO 'yi nasıl tümleştirileceğini öğreneceksiniz. Azure AD ile Jira için EasySSO 'yi tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Jira erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Jira 'ya otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Jirso çoklu oturum açma (SSO) özellikli bir abonelik için EasySSO.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Jira için EasySSO **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Jira için EasySSO **, tam zamanında** Kullanıcı sağlamayı destekliyor
* Jira için EasySSO 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve geri alımını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletilir. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-jira-from-the-gallery"></a>Galeriden Jira için EasySSO ekleme

Jira 'nın Azure AD 'ye yönelik easysto tümleştirmesini yapılandırmak için, Galeri 'den yönetilen SaaS uygulamaları listenize Jira için EasySSO eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Jira Için easysso** yazın.
1. Sonuçlar panelinden **Jira Için Easysso** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-jira"></a>Jira için EasySSO için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Jira IÇIN Azure AD SSO 'Yu EasySSO ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ile ilgili Kullanıcı arasında Jira için bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu Jira için EasySSO ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Jira SSO Için EasySSO](#configure-easysso-for-jira-sso)** 'yi, uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için yapılandırın.
    1. **[Jira test kullanıcısı için easysso](#create-easysso-for-jira-test-user)** , kullanıcının Azure AD gösterimine bağlanmış Jira Için easysso 'da B. Simon 'un bir karşılığı olacak şekilde oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Jira uygulama tümleştirmesi Için Easysso** sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
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

1. Jira uygulaması için EasySSO, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdakine ek olarak, Jira uygulaması için EasySSO, aşağıda gösterilen SAML yanıtında birkaç özniteliğin daha fazla özniteliğe geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name | Kaynak özniteliği|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. UserPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | Kullanıcı. Mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | User. DisplayName |
    | urn: OID: 2.5.4.4 | User. soyadı |
    | urn: OID: 2.5.4.42 | Kullanıcı. |
    
    Azure AD kullanıcılarınızın **sAMAccountName** ile yapılandırılmış olması durumunda **urn: OID: 0.9.2342.19200300.100.1.1** ' i **sAMAccountName** özniteliğine eşlemeniz gerekir.
    
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** veya **Federasyon meta verileri XML** seçenekleri için bağlantıları **İndir** ' e tıklayın ve bilgisayarınıza ya da tümünü kaydedin. Daha sonra Jira EasySSO 'yi yapılandırmak için ihtiyacınız olacak.

    ![Sertifika indirme bağlantısı](media/easysso-for-jira-tutorial/azure-ad-SAML-certificate.png)
    
    Sertifika ile Jira yapılandırması için EasySSO yapmayı planlıyorsanız, aşağıdaki bölümden **oturum açma URL 'sini** ve **Azure ad tanımlayıcısını** kopyalamanız ve bilgisayarınıza kaydetmeniz gerekir.

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

Bu bölümde, Jira için Easysa erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Jira Için Easysso** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-easysso-for-jira-sso"></a>Jira SSO için EasySSO yapılandırma

1. Yönetici ayrıcalıklarına sahip Atlasıris Jira Örneğinizde oturum açın ve **Uygulamaları Yönet** bölümüne gidin. 

    ![Uygulamaları Yönetme](media/easysso-for-jira-tutorial/jira-admin-1.png)

2. Sol tarafta **Easysso** öğesini bulun ve tıklatın.

    ![Kolay SSO](media/easysso-for-jira-tutorial/jira-admin-2.png)

3. **SAML** seçeneğini belirleyin. Bu, sizi SAML yapılandırması bölümüne götürür.

    ![SAML](media/easysso-for-jira-tutorial/jira-admin-3.png)

4. Üstteki **Sertifikalar** sekmesini seçin ve aşağıdaki ekran görüntülenir:

    ![Meta veri URL 'SI](media/easysso-for-jira-tutorial/jira-admin-4.png)

5. Şimdi **Azure AD SSO** yapılandırmasının önceki adımlarında kaydettiğiniz **sertifika (base64)** veya **meta veri dosyasını** bulun. İlerlemeniz için aşağıdaki seçenekleri kullanabilirsiniz:

    a. Bilgisayarınızda yerel dosyaya indirdiğiniz uygulama Federasyon **meta veri dosyasını** kullanın. **Yükleme** radyo düğmesini seçin ve işletim sisteminize özel karşıya yükleme dosyası iletişim kutusunu izleyin.

    **OR**

    b. Dosyanın içeriğini (herhangi bir düz metin düzenleyicisinde) görmek ve panoya kopyalamak için uygulama Federasyon **meta verileri dosyasını** açın. **Giriş** seçeneğini belirleyin ve Pano içeriğini metin alanına yapıştırın.

     **OR**

    c. Tam el ile yapılandırma. Dosyanın içeriğini (herhangi bir düz metin düzenleyicisinde) görmek ve panoya kopyalamak için uygulama Federasyon **sertifikası 'nı (base64)** açın. **IDP belirteç Imzalama sertifikaları** metin alanına yapıştırın. Ardından **genel** sekmesine gidin ve bağlama URL **'SI** ve **varlık KIMLIĞI** alanlarını, **oturum açma URL** 'si ve daha önce kaydettiğiniz **Azure AD tanımlayıcısı** için ilgili değerlerle birlikte girin.
 
6. Sayfanın alt kısmındaki **Kaydet** düğmesine tıklayın. Meta verilerin içeriğini veya sertifika dosyalarını yapılandırma alanlarına ayrıştırmış olursunuz. Jira yapılandırması tamamlandıktan sonra EasySSO.

7. En iyi test deneyimi için, **&** Me sekmesine gidin ve üzerinde **SAML oturum açma düğmesi** seçeneğini işaretleyin. Bu, özellikle Azure AD SAML tümleştirme uçtan uca test etmek için Jira oturum açma ekranında ayrı bir düğmeye olanak sağlar. Bu düğmeyi açık bırakabilir ve üretim modu için yerleşimini, renk ve çevirisini de yapılandırabilirsiniz.

    ![& göz atın](media/easysso-for-jira-tutorial/jira-admin-5.png)

    > [!NOTE]
    > Herhangi bir sorununuz olması gerekir, lütfen [Easysso destek ekibine](mailto:support@techtime.co.nz)başvurun.

### <a name="create-easysso-for-jira-test-user"></a>Jira test kullanıcısı için EasySSO oluştur

Bu bölümde, Jira 'de Britta Simon adlı bir Kullanıcı oluşturulur. Jira için EasySSO, varsayılan olarak **devre dışı** olan tam zamanında Kullanıcı sağlamayı destekler. Kullanıcı sağlamayı etkinleştirmek için EasySSO eklentisi yapılandırmasının Genel bölümünde oturum **açma başarılı olduğunda Kullanıcı oluşturma** seçeneğini açıkça denetlemeniz gerekir. Bir Kullanıcı Jira 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

Bununla birlikte, Kullanıcı ilk oturum açmada otomatik Kullanıcı sağlamayı etkinleştirmek istemiyorsanız, kullanıcıların, LDAP veya Atlasırwd gibi, arka uç Kullanıcı dizinlerinde, Jira örneği tarafından kullanılması gerekir.

![Kullanıcı sağlama](media/easysso-for-jira-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Test SSO 'SU 

### <a name="idp-initiated-workflow"></a>IDP tarafından başlatılan iş akışı

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Access panel 'de Jira kutucuğunun her ne için Easysa 'ya tıkladığınızda, SSO 'yu ayarladığınız Jira örneğinde otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

### <a name="sp-initiated-workflow"></a>SP tarafından başlatılan iş akışı

Bu bölümde, Jira **SAML oturum açma** düğmesini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

![Kullanıcı SAML oturum açma](media/easysso-for-jira-tutorial/jira-admin-7.png)

Bu senaryo, Jira EasySSO yapılandırma sayfasında bulunan **&** Me sekmesinde **SAML oturum açma düğmesini** etkinleştirmiş olduğunu varsayar (yukarıya bakın). Mevcut oturumlarınızda herhangi bir girişimden kaçınmak için, JIRA oturum açma URL 'nizi tarayıcıda geçersiz modda açın. **SAML oturum açma** düğmesine TıKLADıĞıNıZDA Azure AD Kullanıcı kimlik doğrulama akışına yönlendirilirsiniz. Başarılı bir şekilde tamamlandıktan sonra, SAML aracılığıyla kimliği doğrulanmış kullanıcı olarak JIRA örneğinize yeniden yönlendirilirsiniz.

Azure AD 'den geri yönlendirildikten sonra aşağıdaki ekranla karşılaşacağınız bir olasılık vardır

![EasySSO hata ekranı](media/easysso-for-jira-tutorial/jira-admin-8.png)

Bu durumda, **Atlassian-Jira. log** dosyasına erişim sağlamak için [Bu sayfadaki yönergeleri]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) izlemeniz gerekir. Hatanın ayrıntıları EasySSO hata sayfasında bulunan başvuru Kımlığı tarafından kullanılabilir olacaktır.

Günlük iletilerini geri almaya yönelik herhangi bir sorununuz olması gerekir, lütfen [Easysso destek ekibine](mailto:support@techtime.co.nz)başvurun.

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile Jira için EasySSO 'yi deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security oturum denetimi nedir?](/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve denetimlerle Jira için EasySSO koruma](/cloud-app-security/proxy-intro-aad)