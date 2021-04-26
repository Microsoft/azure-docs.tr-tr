---
title: 'Öğretici Azure Active Directory: F5 BIG-IP APM ile Oracle PeopleSoft ile korunan çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: F5 BIG-IP APM tarafından Azure Active Directory ve Oracle PeopleSoft ile korunan arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 3af149f0c1db7f354be6bd968bbd0cf858493d4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219306"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Öğretici: F5 BIG-IP APM ile Oracle PeopleSoft ile korunan çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Azure Active Directory (Azure AD) ile F5 BIG-IP APM ile Oracle PeopleSoft-Protected ile tümleştirmeyi öğreneceksiniz. Oracle PeopleSoft-Protected tarafından korunan büyük IP APM ile Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, F5 BIG-IP APM tarafından Oracle PeopleSoft-Protected 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla F5 BIG-IP APM tarafından Oracle PeopleSoft-protected ' e otomatik olarak oturum açmalarını sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.
* Bu öğretici, Oracle PeopleSoft ağaç yönergelerini içerir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

1. Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
1. F5 büyük-IP APM çoklu oturum açma (SSO) özellikli abonelik tarafından korunan Oracle PeopleSoft-Protected.

1. Ortak çözümü dağıtmak için aşağıdaki lisans gerekir:

    1. F5 BIG-IP® En Iyi demeti (veya) 
    2. F5 BIG-IP Access Policy Manager™ (APM) tek başına lisansı 
    3. F5 BIG-IP Access Policy Manager™ (APM) bir büyük IP F5 BIG-IP® yerel Traffic Manager™ (LTM) eklenti lisansı.
    4. Yukarıdaki lisansın yanı sıra, F5 sistemine de lisans verebilir: 
        * URL kategorisi veritabanını kullanmak için bir URL filtreleme aboneliği. 
        * Bilinen saldırganlar ve kötü amaçlı trafiği algılamak ve engellemek için bir F5 IP Intelligence aboneliği. 
        * Güçlü kimlik doğrulaması için dijital anahtarları korumak ve yönetmek için bir ağ donanımı güvenlik modülü (HSM).
1. F5 BIG-IP sistemi APM modülleriyle sağlanır (LTM isteğe bağlıdır).
1. İsteğe bağlı olsa da, F5 sistemlerini, yüksek kullanılabilirlik (HA) için kayan bir IP adresi ile birlikte etkin bekleme çifti içeren bir [eşitleme/yük devretme cihaz grubunda](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) dağıtmanız önemle önerilir. Bağlantı toplama Denetim Protokolü (LACP) kullanılarak daha fazla arabirim artıklığı elde edilebilir. LACP, bağlı fiziksel arabirimleri tek bir sanal arabirim (toplama grubu) olarak yönetir ve grup içindeki tüm arabirim başarısızlıklarını algılar.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* F5 büyük-IP APM tarafından PeopleSoft tarafından korunan Oracle **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="add-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Galeriden F5 BIG-IP APM 'ye Oracle PeopleSoft-Protected ile ekleme

F5 BIG-IP APM tarafından Azure AD 'ye Oracle PeopleSoft-Protected 'ın tümleştirilmesini yapılandırmak için, Gallery 'den yönetilen SaaS uygulamaları listenize F5 büyük-IP APM 'ye göre Oracle PeopleSoft korumalı olarak eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **F5 BIG-IP APM 'ye göre Oracle PeopleSoft-Protected** yazın.
1. Sonuçlar panelinden **F5 BIG-IP APM tarafından Oracle PeopleSoft-Protected** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>F5 BIG-IP APM ile Oracle PeopleSoft-Protected için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak F5 BIG-IP APM Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, F5 BIG-IP APM tarafından korunan bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

F5 BIG-IP APM tarafından Oracle PeopleSoft-Protected ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[F5 BIG-IP APM SSO Ile Oracle PeopleSoft-Protected yapılandırın](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** .
    1. **[F5 Big-IP APM test kullanıcısına göre oracle PeopleSoft-Protected oluşturun](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** , bu, kullanıcının Azure AD gösterimine bağlı olan F5 BIG-IP APM 'de B. Simon 'a sahip olacak şekilde
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Oracle PeopleSoft-Protected by F5 BIG-IP APM** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<FQDN>.peoplesoft.f5.com`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. **Oturum kapatma URL** 'si metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı, yanıt URL 'si ve oturum kapatma URL 'SI ile güncelleştirin. Değeri almak için [F5 büyük-IP APM istemci destek ekibi tarafından Oracle PeopleSoft Ile korunan ile](https://support.f5.com) iletişim kurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. F5 BIG-IP APM uygulaması, Oracle PeopleSoft tarafından korunan belirli bir biçimde SAML onayları bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Yukarıdaki ' a ek olarak, F5 büyük-IP APM uygulaması tarafından korunan Oracle PeopleSoft, daha az sayıda özniteliğin aşağıda gösterilen SAML yanıtına geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
    
    | Name |  Kaynak özniteliği|
    | ------------------ | --------- |
    | EMPLıD | User. EmployeeID |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'Sini ve **sertifikayı (base64)** indirin ve bilgisayarınıza kaydedin.

    ![Sertifika indirme bağlantısı](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

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

Bu bölümde, F5 BIG-IP APM tarafından Oracle PeopleSoft ile korunan erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **F5 BIG-IP APM tarafından Oracle PeopleSoft-Protected**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>F5 büyük-IP APM SSO ile Oracle PeopleSoft-Protected yapılandırma

### <a name="f5-saml-sp-configuration"></a>F5 SAML SP yapılandırması

Meta veri sertifikasını, daha sonra kurulum sürecinde kullanılacak F5 'e aktarın. **System > sertifika yönetimi > trafik sertifikası yönetimi > SSL sertifikası listesi**' ne gidin. Sağ köşeden **Içeri aktar** ' ı seçin.

![F5 SAML SP yapılandırması](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>SAML ıDP bağlayıcısını ayarlayın 

1. **> federasyon > saml: Service Provider > dış IDP bağlayıcılarına** gidin ve **meta verilerden > oluştur**' a tıklayın.

    ![F5 SAML ıDP Bağlayıcısı](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Aşağıdaki sayfada, XML dosyasını karşıya yüklemek için **Araştır** ' a tıklayın.

1. **Kimlik sağlayıcısı adı** metin kutusuna geçerli bir ad verin ve ardından **Tamam**' a tıklayın.

    ![Yeni SAML ıDP Bağlayıcısı](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. **Güvenlik ayarları** sekmesinde gerekli adımları gerçekleştirin ve ardından **Tamam**' a tıklayın.

    ![SAML ıDP bağlayıcısını Düzenle](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>SAML SP 'yi ayarlama

1. **> federasyon > SAML hizmeti sağlayıcısı > yerel SP Hizmetleri** ' ne gidin ve **Oluştur**' a tıklayın. Aşağıdaki bilgileri tamamlayıp **Tamam**' a tıklayın.

    * Ad: `<Name>`
    * Varlık KIMLIĞI: `https://<FQDN>.peoplesoft.f5.com`
    * SP adı ayarları
        * Şemadaki `https`
        * Konağının `<FQDN>.peoplesoft.f5.com`
        * Açıklaması `<Description>`

    ![Yeni SAML SP Hizmetleri](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. SP yapılandırması ' nı seçin, PeopleSoftAppSSO ve **IDP bağlayıcıları bağlama/kaldırma ' ya tıklayın**.
**Yeni satır ekle** ' ye tıklayın ve önceki adımda oluşturulan **dış IDP bağlayıcısını** seçin, **Güncelleştir**' e tıklayın ve ardından **Tamam**' a tıklayın.

    ![SAML SP hizmetleri oluşturma](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Uygulama yapılandırılıyor

### <a name="create-a-new-pool"></a>Yeni Havuz oluştur
1. **Yerel trafik > havuzlar > havuz listesi**' ne gidin, **Oluştur**' a tıklayın, aşağıdaki bilgileri doldurun ve **bitti**' ye tıklayın.

    * Ad: `<Name>`
    * Açıklaması `<Description>`
    * Sistem durumu Izleyicileri: `http`
    * Adrestir `<Address>`
    * Hizmet bağlantı noktası: `<Service Port>`

    ![yeni havuz oluşturma](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Yeni bir Istemci SSL profili oluşturun

**SSL > istemci > + > yerel trafik > profillerine** gidin, aşağıdaki bilgileri tamamlayıp **bitti**' ye tıklayın.

* Ad: `<Name>`
* Sertifika `<Certificate>`
* Anahtar: `<Key>`

![Yeni Istemci SSL profili](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Yeni bir sanal sunucu oluştur

1. Sanal **sunucular > sanal sunucu listesi > + ' > yerel trafiğe** gidin, aşağıdaki bilgileri tamamlayıp **bitti**' ye tıklayın.
    * Ad: `<Name>`
    * Hedef adres/maske: `<Address>`
    * Hizmet bağlantı noktası: bağlantı noktası 443 HTTPS
    * HTTP profili (Istemci): http

    ![Yeni bir sanal sunucu oluştur](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Aşağıdaki değerleri aşağıdaki sayfada doldurabilirsiniz:

    * SSL profili (Istemci): `<SSL Profile>`
    * Kaynak adres çevirisi: otomatik eşleme
    * Erişim profili: `<Access Profile>`
    * Varsayılan havuz: `<Pool>`


    ![Yeni bir sanal sunucu PeopleSoft havuzu oluşturma](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>Çoklu oturum açma çözümü olarak F5 Big-IP APM 'yi desteklemek için PeopleSoft uygulamasını ayarlama

>[!Note]
> Başvurunun https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start`Yönetici kimlik bilgilerini kullanarak PeopleSoft konsolunda oturum açın (örnek: PS/PS).

    ![Yönetici Self Servis](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. PeopleSoft uygulamasında, yeni bir kullanıcı profili olarak **OAMPSFT** oluşturun ve **PeopleSoft Kullanıcı** gibi düşük bir güvenlik rolünü ilişkilendirin.
Yeni bir kullanıcı profili oluşturmak için Kullanıcı profillerine **> Peopletools > güvenlik > Kullanıcı** profilleri ' ne gidin, örneğin: **OAMPSFT** ve **PeopleSoft Kullanıcı** ekleme.

    ![PeopleSoft kullanıcısı](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Web profiline erişin ve genel erişim **Kullanıcı kimliği** olarak **OAMPSFT** girin.

    ![Kullanıcı profilleri](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. **PeopleTools Uygulama Tasarımcısı** **FUNCLIB_LDAP** kaydını açın.

    ![Web profili yapılandırması](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. **OAMSSO_AUTHENTICATION** işlevi Için kullanıcı üstbilgisini **PS_SSO_UID** güncelleştirin.
**Getwwwauthconfig ()** işlevinde, **&defaultuserıd** öğesine atanmış değeri Web profilinde tanımladığımız **OAMPSFT** ile değiştirin. Kayıt tanımını kaydedin.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Oturum açma **PeopleCode** sayfasına (PeopleTools, güvenlik, güvenlik nesneleri, oturum açma PeopleCode) erişin ve **OAMSSO_AUTHENTICATION** Işlevini etkinleştirin — Oracle Access Manager tek oturum açma için oturum açma PeopleCode.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>F5 Big-IP APM 'yi ayarlama, "PS_SSO_UID" HTTP üst bilgisini PeopleSoft Kullanıcı KIMLIĞIYLE doldurmak için

### <a name="configuring-per-request-policy"></a>Per-Request Ilkesini yapılandırma
1. **> profile/ilkelerine > erişim Per-Request ilkeleri**' ne gidin, **Oluştur**' a tıklayın, aşağıdaki bilgileri doldurun ve **bitti**' ye tıklayın.

    * Ad: `<Name>`
    * Profil türü: tümü
    * Diller: `<Language>`

    ![Per-Request Ilkesini yapılandırma ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Per-Request Ilkeyi **Düzenle** ' ye tıklayın `<Name>` ![ Per-Request ilkesini Düzenle PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Sanal sunucuya Per-Request Ilkesi atama

Sanal **sunucular > sanal sunucu listesini > yerel trafiğe gidin >** `<Name>` Per-Request ilkesi olarak belirtin

![Per-Request Ilke olarak PeopleSoftSSO ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>PeopleSoft uygulamasından çoklu oturum kapatma desteği için F5 Big-IP APM ayarlanıyor

Tüm PeopleSoft kullanıcıları için çoklu oturum kapatma desteği eklemek için lütfen şu adımları izleyin:

1. PeopleSoft portalı için doğru oturum kapatma URL 'sini belirleme
    * PeopleSoft uygulamasının bir kullanıcı oturumunu sonlandırmak için kullandığı adresi öğrenmek için, aşağıdaki örnekte gösterildiği gibi, herhangi bir Web tarayıcısını kullanarak portalı açmanız ve tarayıcı hata ayıklama araçlarını etkinleştirmeniz gerekir:

        ![PeopleSoft portalı için oturum kapatma URL 'SI ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Kimliği olan öğesini bulun `PT_LOGOUT_MENU` ve sorgu PARAMETRELERIYLE URL yolunu kaydedin. Örneğimizde aşağıdaki değeri aldık: `/psp/ps/?cmd=logout`

1. Kullanıcıyı APM oturum kapatma URL 'sine yönlendiren LTM ırule oluşturun: `/my.logout.php3`

    * **Yerel trafiğe > iRule**' a gidin, **Oluştur**' a tıklayın, aşağıdaki bilgileri doldurun ve **bitti**' ye tıklayın.

        Ad: `<Name>`  
        Tanım:  
                    _HTTP_REQUEST {anahtar-glob--[HTTP:: Uri] { `/psp/ps/?cmd=logout` {http:: Redirect `/my.logout.php3` }}}_

1. Oluşturulan ırule 'ü sanal sunucuya atayın

    * Sanal **sunucular > sanal sunucu listesi > PeopleSoftApp > kaynakları > yerel trafiğe** gidin. **Yönet...** öğesine tıklayın Bu   

    * `<Name>`Etkin ırule olarak belirtip **tamamlandı**' ya tıklayın.

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * **Ad** metin kutusu değerini şöyle verin`<Name>` 

        ![_iRule_PeopleSoftApp bitti](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>F5 BIG-IP APM test kullanıcısı tarafından Oracle PeopleSoft-Protected oluşturma

Bu bölümde, F5 büyük-IP APM tarafından PeopleSoft-Protected Oracle 'da B. Simon adlı bir Kullanıcı oluşturacaksınız. F5 Big-IP APM platformu aracılığıyla Oracle PeopleSoft-Protected kullanıcıları eklemek için [F5 büyük-IP APM destek ekibi tarafından oracle PeopleSoft-Protected](https://support.f5.com) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum akışını başlatabileceğiniz F5 BIG-IP APM oturum açma URL 'sine göre Oracle PeopleSoft-Protected yönlendirir.  

* F5 BIG-IP APM oturum açma URL 'sine doğrudan Oracle PeopleSoft-Protected gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal ' de **Bu uygulamayı test et** ' e tıklayın ve F5 ' i AYARLADıĞıNıZ F5 BIG-IP APM tarafından Oracle PeopleSoft-Protected otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda F5 BIG-IP APM kutucuğuna göre Oracle PeopleSoft-Protected tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız F5 BIG-IP APM tarafından Oracle PeopleSoft-Protected otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

F5 BIG-IP APM ile Oracle PeopleSoft-Protected yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve infilğini koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
