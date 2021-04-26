---
title: 'Öğretici: Eğitim: SAP NetWeaver ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAP NetWeaver arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: b6b8dab3472473082562f1e4c0216886191e4a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97962814"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Öğretici: SAP NetWeaver ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide SAP NetWeaver 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SAP NetWeaver 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SAP NetWeaver 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP NetWeaver 'ta otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SAP NetWeaver çoklu oturum açma (SSO) etkin aboneliği.
* SAP NetWeaver V 7.20 en az gereklidir

## <a name="scenario-description"></a>Senaryo açıklaması

* SAP NetWeaver, **SAML** (**SP tarafından başlatılan SSO**) ve **OAuth**'ı destekler. Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

> [!NOTE]
> Uygulamayı, kurumsal gereksiniminize göre SAML ya da OAuth içinde yapılandırın. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Galeriden SAP NetWeaver ekleme

SAP NetWeaver 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Gallery 'den yönetilen SaaS uygulamaları listenize SAP NetWeaver eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SAP NetWeaver** yazın.
1. Sonuçlar panelinden **SAP NetWeaver** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-sap-netweaver"></a>SAP NetWeaver için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test KULLANıCıSı kullanarak SAP NetWeaver Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, SAP NetWeaver 'deki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SAP NetWeaver ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
1. Uygulama tarafında SSO ayarlarını yapılandırmak için **[SAML kullanarak SAP NetWeaver 'ı yapılandırın](#configure-sap-netweaver-using-saml)** .
    1. SAP NetWeaver 'ın Azure AD gösterimine bağlı olan SAP NetWeaver 'de buna karşılık gelen B. Simon 'a sahip olması için **[SAP test kullanıcısı oluşturun](#create-sap-netweaver-test-user)** .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .
1. Uygulama tarafında OAuth ayarlarını yapılandırmak için, **[OAuth IÇIN SAP NetWeaver 'ı yapılandırın](#configure-sap-netweaver-for-oauth)** .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

SAP NetWeaver ile Azure AD çoklu oturum açmayı yapılandırmak için aşağıdaki adımları uygulayın:

1. Yeni bir Web tarayıcı penceresi açın ve SAP NetWeaver şirket sitenizde yönetici olarak oturum açın

1. **Http** ve **https** hizmetlerinin etkin olduğundan ve uygun bağlantı noktalarının **Smick** T-Code ' a atandığından emin olun.

1. SSO 'nun gerekli olduğu ve HTTP güvenlik oturumu yönetimini etkinleştirmesi gereken SAP System (T01) iş istemcisinde oturum açın.

    a. Işlem kodu **SICF_SESSIONS** git. Geçerli değerlerle ilgili tüm profil parametrelerini görüntüler. Şu şekilde görünür:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Kuruluş gereksinimlerinize göre yukarıdaki parametreleri ayarlayın, yukarıdaki parametrelere yalnızca gösterge olarak verilirler.

    b. Gerekirse, parametreleri ayarla SAP sisteminin örnek/varsayılan profilinde ve SAP sistemini yeniden başlatın.

    c. HTTP güvenlik oturumunu etkinleştirmek için ilgili istemciye çift tıklayın.

    ![HTTP güvenlik oturumu ](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. SıCF hizmetlerini aşağıdan etkinleştirin:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. SAP System [T01/122.368] iş istemcisinde Transaction Code **SAML2** 'a gidin. Bir tarayıcıda Kullanıcı arabirimini açar. Bu örnekte, SAP Business Client 122 olarak kabul ediyoruz.

    ![İşlem kodu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Kullanıcı arabirimine girmek için Kullanıcı adınızı ve parolanızı girip **Düzenle**' ye tıklayın.

    ![Kullanıcı adı ve parola](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. **Sağlayıcı adını** T01122 olarak değiştirin `http://T01122` ve **Kaydet**' e tıklayın.

    > [!NOTE]
    > Varsayılan olarak, sağlayıcı adı biçim olarak gelir `<sid><client>` , ancak Azure AD, `<protocol>://<name>` `https://<sid><client>` Azure AD 'de birden çok SAP NetWeaver ABAP altyapısına izin verecek şekilde, sağlayıcı adının korunmasını öneren, biçiminde ad bekler.

    ![Çoklu SAP NetWeaver ABAP motorları](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Hizmet sağlayıcı meta verileri oluşturuluyor**:-SAML 2,0 Kullanıcı arabiriminde **yerel sağlayıcı** ve **Güvenilen sağlayıcılar** ayarlarını yapılandırma ile işiniz bittiğinde, sonraki adım hizmet sağlayıcısının meta veri dosyasını (tüm ayarları, kimlik doğrulama bağlamlarını ve SAP 'deki diğer yapılandırmalarını içerir) oluşturmak olacaktır. Bu dosya oluşturulduktan sonra Azure AD 'ye yüklemesi gerekir.

    ![Hizmet sağlayıcı meta verileri oluşturuluyor](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. **Yerel sağlayıcı sekmesine** gidin.

    b. **Meta veriler**' e tıklayın.

    c. Oluşturulan **meta VERI XML dosyasını** bilgisayarınıza kaydedin ve Azure Portal **tanımlayıcı** ve **yanıt URL 'si** DEğERLERINI otomatik olarak doldurmak için **temel SAML yapılandırması** bölümüne yükleyin.

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **SAP NetWeaver** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımı uygulayın:

    a. Daha önce edindiğiniz **Service Provider meta veri dosyasını** karşıya yüklemek için **meta veri dosyasını karşıya yükle** ' ye tıklayın.

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra **tanımlayıcı** ve **yanıt URL** değerleri, **temel SAML yapılandırması** bölüm metin kutusunda aşağıda gösterildiği gibi otomatik olarak doldurulur:

    d. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Örnekleri için yapılandırılmış yanlış yanıt URL 'SI hatası bildiren birkaç müşteriyi gördük. Bu tür bir hata alırsanız, örneğiniz için doğru yanıt URL 'sini ayarlamak için aşağıdaki PowerShell betiğini geçici bir çözüm olarak kullanabilirsiniz.:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal nesne KIMLIĞI ilk olarak sizin tarafınızdan ayarlanacak ya da bunu da buraya geçirebilmeniz gerekir.

1. SAP NetWeaver uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![öznitelik 'yi Düzenle](common/edit-attribute.png)

1. **Kullanıcı öznitelikleri** Iletişim kutusundaki **Kullanıcı talepleri** bölümünde, YUKARıDAKI görüntüde gösterildiği gibi SAML belirteci özniteliğini yapılandırın ve aşağıdaki adımları gerçekleştirin:

    a. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **Düzenle simgesine** tıklayın.

    ![Düzenle simgesi](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. **Dönüştürme** listesinden **Extractmailprefix ()** öğesini seçin.

    c. **Parametre 1** listesinden **User. UserPrincipalName** öğesini seçin.

    d. **Kaydet**’e tıklayın.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **SAP NetWeaver ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

   ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

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

Bu bölümde, SAP NetWeaver 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **SAP NetWeaver**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sap-netweaver-using-saml"></a>SAML kullanarak SAP NetWeaver yapılandırma

1. SAP sisteminde oturum açın ve işlem kodu SAML2 ' e gidin. SAML Yapılandırması ekranı ile yeni tarayıcı penceresi açar.

2. Güvenilen kimlik sağlayıcısı (Azure AD) için uç noktaları yapılandırmak için **güvenilir sağlayıcılar** sekmesine gidin.

    ![Tek Sign-On güvenilir sağlayıcıları yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. **Ekle** ' ye basın ve bağlam menüsünden **meta veri dosyasını karşıya yükle** ' yi seçin.

    ![Tek Sign-On Yapılandır 2](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Azure portal indirdiğiniz meta veri dosyasını karşıya yükleyin.

    ![Tek Sign-On yapılandırma 3](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Sonraki ekranda, diğer adı yazın. Örneğin, aadsts ve devam etmek için **İleri** ' ye basın.

    ![Tek Sign-On 4 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. **Özet algoritmanız** için **SHA-256** olmalı ve herhangi bir değişiklik gerektirmediğinden emin olun ve **İleri**' ye basın.

    ![Tek Sign-On 5 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. **Tek Sign-On uç noktalarında** **http post** ' u kullanın ve devam etmek için **İleri** ' ye tıklayın.

    ![Tek Sign-On 6 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. **Çoklu oturum kapatma uç noktalarında** **httpredirect** seçeneğini belirleyin ve devam etmek için **İleri** 'yi tıklatın.

    ![Tek Sign-On 7 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. **Yapıt uç noktalarında** devam etmek için **İleri** ' ye basın.

    ![Tek Sign-On 8 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. **Kimlik doğrulama gereksinimleri**' nde **son**' a tıklayın.

    ![Tek Sign-On 9 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Sekmeye **Güvenilen sağlayıcı**  >  **kimliği Federasyonu** ' ne gidin (ekranın altından). **Düzenle**’ye tıklayın.

    ![Tek Sign-On 10 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. **Kimlik Federasyonu** sekmesi (alt pencere) altında **Ekle** ' ye tıklayın.

    ![Tek Sign-On 11 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Açılır pencerede, **desteklenen NameID biçimlerinden** **belirtilmemiş** ' i seçin ve Tamam ' a tıklayın.

    ![Tek Sign-On 12 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

1. Kullanıcı kimliği **kaynak** değerini **onaylama özniteliği** olarak, **Kullanıcı kimliği eşleme modu** değerini **e-posta** ve **onaylama özniteliği adı** olarak verin `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    ![Tek Sign-On yapılandırma ](./media/sapnetweaver-tutorial/nameid-format.png)

14. **Kullanıcı kimliği kaynağı** ve **Kullanıcı kimliği eşleme modu** değerlerinin SAP kullanıcısı ile Azure AD talebi arasındaki bağlantıyı belirleyeceğini unutmayın.

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Senaryo: SAP kullanıcısı için Azure AD kullanıcı eşlemesi.

    a. NameID ayrıntıları SAP ekranının ekran görüntüsü.

    ![Tek Sign-On 13 yapılandırma](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Azure AD 'den gerekli talepler ekran görüntüsü.

    ![Tek Sign-On 14 yapılandırma](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Senaryo: SU01 içinde yapılandırılmış e-posta adresine göre SAP Kullanıcı KIMLIĞI ' ni seçin. Bu durumda, SSO gerektiren her kullanıcı için e-posta KIMLIĞI su01 ' de yapılandırılmalıdır.

    a.  NameID ayrıntıları SAP ekranının ekran görüntüsü.

    ![Tek Sign-On 15 yapılandırma](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Azure AD 'den gerekli talepler ekran görüntüsü.

    ![Tek Sign-On yapılandırın 16](./media/sapnetweaver-tutorial/claimsaad2.png)

15. **Kaydet** ' e tıklayın ve kimlik sağlayıcısını etkinleştirmek için **Etkinleştir** ' e tıklayın.

    ![Tek Sign-On 17 yapılandırma](./media/sapnetweaver-tutorial/configuration1.png)

16. İstendiğinde **Tamam** ' a tıklayın.

    ![Tek Sign-On Yapılandır 18](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver test kullanıcısı oluşturma

    Bu bölümde, SAP NetWeaver 'de B. Simon adlı bir Kullanıcı oluşturacaksınız. SAP NetWeaver platformunda kullanıcıları eklemek için lütfen şirket içi SAP uzman takımınızdan çalışın veya kuruluşunuzun SAP iş ortağınızla birlikte çalışın.

## <a name="test-sso"></a>Test SSO 'SU

1. Kimlik sağlayıcısı Azure AD etkinleştirildikten sonra, SSO 'yu denetlemek için aşağıdaki URL 'ye erişmeyi deneyin (Kullanıcı adı & parolasını sorma)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (veya) aşağıdaki URL 'YI kullanın

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Sapurl 'yi gerçek SAP ana bilgisayar adıyla değiştirin.

2. Yukarıdaki URL, sizi belirtilen ekranın altına götürebilmelidir. Aşağıdaki sayfaya erişebilseniz, Azure AD SSO Kurulumu başarıyla tamamlandı.

    ![Tek Sign-On test et](./media/sapnetweaver-tutorial/testingsso.png)

3. Kullanıcı adı & parola istemi oluşursa, lütfen aşağıdaki URL 'YI kullanarak izlemeyi etkinleştirerek sorunu tanılayın

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>OAuth için SAP NetWeaver yapılandırma

1. SAP belgelenmiş işlem şu konumda bulunabilir: [NetWeaver ağ geçidi hizmeti etkinleştirme ve OAuth 2,0 kapsam oluşturma](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. SPRO ' ya gidin ve **hizmetleri etkinleştirme ve sürdürme**' yı bulun.

    ![Hizmetleri etkinleştirme ve sürdürme](./media/sapnetweaver-tutorial/oauth01.png)

3. Bu örnekte OData hizmetini `DAAG_MNGGRP` OAuth Ile Azure AD SSO 'ya bağlamak istiyoruz. Hizmet için teknik hizmet adı aramasını kullanın `DAAG_MNGGRP` ve henüz etkin değilse etkinleştirin ( `green` ICF düğümleri altında durum ' u arayın). Sistem diğer adının (hizmetin gerçekten çalıştığı bağlı arka uç sistemi) doğru olduğundan emin olun.

    ![OData hizmeti](./media/sapnetweaver-tutorial/oauth02.png)

    * Sonra üstteki düğme çubuğunda basma ve **OAuth** ' a tıklayın ve `scope` (varsayılan adı sunulsun) atayın.

4. Bizim örneğimiz için kapsam, `DAAG_MNGGRP_001` otomatik olarak bir sayı eklenerek hizmet adından oluşturulur. Rapor `/IWFND/R_OAUTH_SCOPES` , kapsam adını değiştirmek veya el ile oluşturmak için kullanılabilir.

    ![OAuth 'ı yapılandırma](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > İleti `soft state status is not supported` – sorun olmadığından yoksayılabilir. Daha ayrıntılı bilgi için [buraya](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)bakın.

### <a name="create-a-service-user-for-the-oauth-20-client"></a>OAuth 2,0 Istemcisi için bir hizmet kullanıcısı oluşturma

1. OAuth2, `service ID` Son Kullanıcı adına erişim belirtecini almak için bir kullanır. OAuth tasarımına göre önemli kısıtlama: `OAuth 2.0 Client ID` `username` bir erişim belirteci istenirken OAuth 2,0 istemcisinin oturum açması için kullanması ile aynı olmalıdır. Bu nedenle, bizim örneğimizde, ISTEMCI1 adına sahip bir OAuth 2,0 istemcisini kaydedebiliyoruz ve bir önkoşul olarak, SAP sisteminde aynı ada (ISTEMCI1) sahip olan bir kullanıcının ve başvurulan uygulama tarafından kullanılmak üzere yapılandıracağız bir kullanıcı var olmalıdır. 

2. Bir OAuth Istemcisi kaydedilirken, kullanılır `SAML Bearer Grant type` .

    >[!NOTE]
    >Daha fazla ayrıntı için, [burada](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)SAML taşıyıcı verme türü için OAuth 2,0 istemci kaydına bakın.

3. TCOD: SU01/Kullanıcı ISTEMCI1 'i oluşturma `System type` ve parola atama, kimlik bilgisini, çağıran koda Kullanıcı adı ile yazmak ısteyen API Programlayıcısının kimlik bilgilerini sağlamak için gereken şekilde kaydedin. Hiçbir profil veya rol atanmalıdır.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Yeni OAuth 2,0 Istemci KIMLIĞINI oluşturma Sihirbazı ile kaydetme

1. Yeni bir **OAuth 2,0 Client** start TRANSACTION **SOAUTH2** kaydetmek için. İşlem, zaten kayıtlı olan OAuth 2,0 istemcilerle ilgili bir genel bakış görüntüler. CLIENT1in Bu örnek olarak adlandırılan yeni OAuth istemcisinin sihirbazını başlatmak için **Oluştur** ' a tıklayın.

2. T-Code: **SOAUTH2** adresine gidin ve açıklamayı girip **İleri**' ye tıklayın.

    ![SOAUTH2](./media/sapnetweaver-tutorial/oauth04.png)

    ![OAuth 2,0 Istemci KIMLIĞI](./media/sapnetweaver-tutorial/oauth05.png)

3. Açılan listeden zaten eklenmiş olan **SAML2 IDP – Azure AD** ' ı seçin ve kaydedin.

    ![SAML2 IDP – Azure AD 1](./media/sapnetweaver-tutorial/oauth06.png)

    ![SAML2 IDP – Azure AD 2](./media/sapnetweaver-tutorial/oauth07.png)

    ![SAML2 IDP – Azure AD 3](./media/sapnetweaver-tutorial/oauth08.png)

4. Daha önce oluşturulan kapsamı eklemek için kapsam ataması altında **Ekle** ' ye tıklayın: `DAAG_MNGGRP_001`

    ![Kapsam](./media/sapnetweaver-tutorial/oauth09.png)

    ![kapsam ataması](./media/sapnetweaver-tutorial/oauth10.png)

5. **Son**' a tıklayın.

## <a name="next-steps"></a>Sonraki Adımlar

Azure AD SAP NetWeaver 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)
