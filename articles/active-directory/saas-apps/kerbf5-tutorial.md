---
title: 'Öğretici: F5 ile Azure AD çoklu oturum açma tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve F5 arasında çoklu oturum açma (SSO) yapılandırma hakkında bilgi edinin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 2fd96c47e98a6f8a66a5fda555304c4448d772e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646125"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Öğretici: F5 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, F5 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. F5 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de F5 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla F5 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

* F5 çoklu oturum açma (SSO) etkin abonelik.

* Ortak çözümü dağıtmak için aşağıdaki lisans gerekir:
    * F5 büyük IP &reg; en iyi demeti (veya)

    * F5 BIG-IP erişim Ilkesi Yöneticisi &trade; (APM) tek başına lisans

    * F5 Big-IP Access Policy Manager &trade; (APM) eklenti lisansı var olan BIR büyük IP F5 Big-IP &reg; Yerel Traffic Manager &trade; (LTM).

    * Yukarıdaki lisansın yanı sıra, F5 sistemine de lisans verebilir:

        * URL kategorisi veritabanını kullanmak için bir URL filtreleme aboneliği

        * Bilinen saldırganlar ve kötü amaçlı trafiği algılamak ve engellemek için F5 IP Intelligence aboneliği

        * Güçlü kimlik doğrulaması için dijital anahtarları korumak ve yönetmek için bir ağ donanımı güvenlik modülü (HSM)

* F5 BIG-IP sistemi APM modülleriyle sağlanır (LTM isteğe bağlıdır)

* İsteğe bağlı olsa da, F5 sistemlerini, yüksek kullanılabilirlik (HA) için kayan bir IP adresi ile birlikte etkin bekleme çifti içeren bir [eşitleme/yük devretme cihaz grubunda](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG) dağıtmanız önemle önerilir. Bağlantı toplama Denetim Protokolü (LACP) kullanılarak daha fazla arabirim artıklığı elde edilebilir. LACP, bağlı fiziksel arabirimleri tek bir sanal arabirim (toplama grubu) olarak yönetir ve grup içindeki tüm arabirim başarısızlıklarını algılar.

* Kerberos uygulamaları için, kısıtlı temsilciye yönelik bir şirket içi AD hizmet hesabı.  AD temsili hesabı oluşturmak için [F5 belgelerine](https://support.f5.com/csp/article/K43063049) bakın.

## <a name="access-guided-configuration"></a>Kılavuzlu yapılandırmaya erişin

* Access Kılavuzlu yapılandırma ', F5 TMOS Version 13.1.0.8 ve üzeri sürümlerde desteklenir. BÜYÜK IP sisteminiz 13.1.0.8 altında bir sürüm çalıştırıyorsa, lütfen **Gelişmiş yapılandırma** bölümüne bakın.

* Erişim destekli yapılandırma, tamamen yeni ve kolaylaştırılmış bir kullanıcı deneyimi sunar. Bu iş akışı tabanlı mimari, seçilen topolojiye uyarlanmış, sezgisel, yeniden entrant yapılandırma adımları sağlar.

* Yapılandırmaya devam etmeden önce, [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748)adresinden en son kullanım örneği paketini indirerek Kılavuzlu yapılandırmayı yükseltin. Yükseltmek için aşağıdaki yordamı izleyin.

    >[!NOTE]
    >Aşağıdaki ekran görüntüleri, en son yayınlanan sürüme yöneliktir (AGC sürüm 5,0 ile büyük IP 15,0). Aşağıdaki yapılandırma adımları, 13.1.0.8 ile en son büyük IP sürümüne kadar bu kullanım durumu için geçerlidir.

1. F5 BIG-IP Web Kullanıcı arabiriminde, **erişim >> Kılavuzu yapılandırması**' na tıklayın.

2. **Kılavuzlu yapılandırma** sayfasında, sol üst köşedeki **Kılavuzlu yapılandırmayı Yükselt** ' e tıklayın.

    !["Kılavuzlu yapılandırmayı yükselt" eylemi seçiliyken "Kılavuzlu yapılandırma" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure14.png) 

3. Yükseltme Kılavuzu yapılandırma açılan ekranında, indirilen kullanım örneği paketini karşıya yüklemek için **Dosya Seç** ' i seçin ve karşıya yükle **ve yükle** düğmesine tıklayın.

    !["Dosya Seç" ve "karşıya yükle ve Yükle" seçiliyken "Kılavuzlu yapılandırmayı yükselt" açılan ekranını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure15.png) 

4. Yükseltme tamamlandığında **devam** düğmesine tıklayın.

    !["Kılavuzlu yapılandırma güncelleştirmesi Tamam" iletişim kutusunun ve "devam" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* F5 **SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* F5 SSO üç farklı şekilde yapılandırılabilir.

- [Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](#configure-f5-single-sign-on-for-kerberos-application)

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Anahtar kimlik doğrulama senaryoları

Açık KIMLIK Connect, SAML ve WS-Bessel kimlik doğrulama protokolleri için Azure Active Directory yerel tümleştirme desteğiyle, F5, Azure AD ile hem iç hem de dış erişim için, eski tabanlı kimlik doğrulama uygulamalarına yönelik güvenli erişimi genişleterek bu uygulamalara modern senaryolar (ör. parola daha az erişim) sağlar. Bu şunlar dahildir:

* Üst bilgi tabanlı kimlik doğrulama uygulamaları

* Kerberos kimlik doğrulama uygulamaları

* Anonim kimlik doğrulaması veya yerleşik kimlik doğrulama uygulamaları

* NTLM kimlik doğrulama uygulamaları (Kullanıcı için çift istemlerle koruma)

* Form tabanlı uygulama (Kullanıcı için çift istemlerle koruma)

## <a name="adding-f5-from-the-gallery"></a>Galeriden F5 ekleme

F5 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize F5 ' i eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **F5** yazın.
1. Sonuçlar panelinden **F5** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>F5 için Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu F5 ile yapılandırın ve test edin. SSO 'nun çalışması için, F5 'teki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu F5 ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[F5 SSO 'Yu yapılandırın](#configure-f5-sso)** .
    1. F5 'te kullanıcının Azure AD gösterimine bağlı olan B. Simon 'un bir karşılığı olacak şekilde **[F5 test kullanıcısı oluşturun](#create-f5-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **F5** uygulaması tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YourCustomFQDN>.f5.com/`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YourCustomFQDN>.f5.com/`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [F5 istemci destek ekibine](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** ve **sertifika (base64)** bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **F5 ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, F5 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **F5**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.
1. **Koşullu erişim** ' e tıklayın.
1. **Yeni ilke**' ye tıklayın.
1. Şimdi, koşullu erişim ilkesi için bir kaynak olarak F5 uygulamanızı görebilir ve çok faktörlü auth, cihaz tabanlı erişim denetimi veya kimlik koruma Ilkesi dahil herhangi bir koşullu erişim uygulayabilirsiniz.

## <a name="configure-f5-sso"></a>F5 SSO 'yu Yapılandır

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma

### <a name="guided-configuration"></a>Kılavuzlu yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve F5 (Kerberos) Şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. Meta veri sertifikasını, kurulum işleminin ilerleyen kısımlarında kullanılacak olan F5 'e aktarmanız gerekir.

1. **System > sertifika yönetimi > trafik sertifikası yönetimi > SSL sertifikası listesi**' ne gidin. Sağ köşeden **Içeri aktar** ' ı seçin. Bir **sertifika adı** belirtin (daha sonra yapılandırmadan başvurulacak). **Sertifika kaynağında** karşıya yükle ' yı seçin SAML çoklu oturum açmayı yapılandırırken Azure 'dan indirilen sertifikayı belirtin. **İçeri Aktar**’a tıklayın.

    !["Sertifika adı" vurgulanmış, "karşıya dosya yükleme" ve "Içeri aktar" düğmesi seçili olan "S S L sertifikası/anahtar kaynağı" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure01.png) 

1. Ayrıca, **Uygulama ana bilgisayar adı Için SSL sertifikası gerekir. System > sertifika yönetimi > trafik sertifikası yönetimi > SSL sertifikası listesi**' ne gidin. Sağ köşeden **Içeri aktar** ' ı seçin. **Içeri aktarma türü** **PKCS 12 (IIS)** olacaktır. **Anahtar adı** belirtin (daha sonra yapılandırmadan başvurulacak) ve pfx dosyasını belirtmeniz gerekir. PFX için **parola** belirtin. **İçeri Aktar**’a tıklayın.

    >[!NOTE]
    >Uygulama adı örneğinde, `Kerbapp.superdemo.live` bir joker karakter sertifikası kullanıyoruz KeyName `WildCard-SuperDemo.live`

    !["S S L sertifikası/anahtar kaynağı" sayfasını, girilen değerleri ve "Içeri aktar" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure02.png) 
 
1. Azure AD Federasyonu ve uygulama erişimini ayarlamak için destekli deneyim kullanacağız. – F5 BIG-IP **Main** ' e gidin ve **erişim > Kılavuzlu yapılandırma > Federasyon > SAML hizmeti sağlayıcısı**' nı seçin. **İleri** ' ye ve ardından yapılandırmaya başlamak için **İleri** ' ye tıklayın.

    !["Federasyon" simgesi vurgulanmış ve "S A M L Service Provider" seçiliyken "Kılavuzlu yapılandırma" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure03.png) 

    !["Ileri" düğmesi seçiliyken "Kılavuzlu yapılandırma-S A M L hizmet sağlayıcısı" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure04.png)

1. Bir **yapılandırma adı** girin. **VARLıK kimliğini** (Azure AD uygulama yapılandırmasında yapılandırdığınız gibi) belirtin. **Ana bilgisayar adını** belirtin. Başvuru için bir **Açıklama** ekleyin. Kalan varsayılan girişleri kabul edin ve ardından **& kaydet**' e tıklayın.

    !["Ana bilgisayar adı" ve "Açıklama" metin kutuları vurgulanmış ve "& sonrakini Kaydet" düğmesi seçili olan "hizmet sağlayıcı özellikleri" ni gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure05.png) 

1. Bu örnekte, 443 numaralı bağlantı noktası ile 192.168.30.200 olarak yeni bir sanal sunucu oluşturacağız. **Hedef adreste** sanal sunucu IP adresini belirtin. Istemci **SSL profilini** seçin, yeni oluştur ' u seçin. Daha önce karşıya yüklenen uygulama sertifikasını (Bu örnekteki joker karakter sertifikası) ve ilişkili anahtarı belirtin ve ardından **& Ileri kaydet**' e tıklayın.

    >[!NOTE]
    >Bu örnekte, Iç Web sunucusu 80 numaralı bağlantı noktasında çalışıyor ve bunu 443 ile yayımlamak istiyoruz.

    !["Hedef adres" metin kutusu vurgulanmış ve "& Ileri Kaydet" düğmesi seçili olan "sanal sunucu özellikleri" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure06.png)

1. **IDP bağlayıcınızı yapılandırmak için yöntem seçin** altında, meta veri ' yi belirtin, Dosya Seç ' e tıklayın ve daha önce Azure AD 'Den Indirilen meta veri xml dosyasını yükleyin SAML ıDP Bağlayıcısı için benzersiz bir **ad** belirtin. Daha önce karşıya yüklenen **meta veri Imzalama sertifikasını** seçin. **İleri & kaydet**' e tıklayın.

    !["Ad" metin kutusu vurgulanmış ve "& Ileri Kaydet" düğmesi seçili olan "dış kimlik sağlayıcısı bağlayıcı ayarları" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure07.png)  

1. **Havuz Seç** altında **Yeni oluştur** ' u (alternatif olarak zaten var olan bir havuz seçin) belirtin. Diğer değerin varsayılan olmasına izin verin.    Havuz sunucuları ' nın altında IP **adresi/düğüm adı** altında IP adresini yazın. **Bağlantı noktasını** belirtin. **İleri & kaydet**' e tıklayın.
 
    !["IP adresi/düğüm adı" ve "bağlantı noktası" metin kutuları vurgulanmış ve "& sonrakini Kaydet" düğmesi seçili olan "havuz özellikleri" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure08.png)

1. Tek Sign-On ayarları ekranında **Çoklu oturum açmayı etkinleştir**' i seçin. **Seçili tek Sign-On türü** altında **Kerberos**' u seçin. Username. **SAML. Last. Identity** ' i  (Azure AD 'de talep eşlemesi kullanarak ayarlanan bu değişken) **Kullanıcı adı kaynak** (Bu değişken). **Gelişmiş ayarı göster**' i seçin. **Kerberos bölgesi** altında etki alanı adını yazın. **Hesap adı/hesap parolası** altında APM temsili hesabı ve parolasını belirtin. **KDC** alanında etki alanı denetleyicisi IP 'sini belirtin. **İleri & kaydet**' e tıklayın.

    ![Metin kutuları vurgulanmış ve "& Ileri Kaydet" düğmesinin seçili olduğu "tek Sign-On ayarları" gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure09.png)   

1. Bu kılavuzun amaçları doğrultusunda Endpoint denetimlerini atlayacağız.  Ayrıntılar için F5 belgelerine bakın.  Ekranda **kaydet & ileri ' yi** seçin.

    !["Endpoint denetimleri özellikleri" sayfasını ve "& Ileri Kaydet" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure10.png) 

1. Varsayılanları kabul edin ve **& Ileri kaydet**' e tıklayın. SAML oturum yönetimi ayarları ile ilgili ayrıntılar için F5 belgelerine başvurun.


    !["& sonrakini Kaydet" düğmesinin seçili olduğu "zaman aşımı ayarları" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure11.png) 
 
1. BÜYÜK IP 'yi yapılandırmak için özet ekranını gözden geçirin ve **Dağıt** ' ı seçin.
 
    !["Özet" bölümü vurgulanmış ve "Dağıt" düğmesi seçili olan "uygulamanızın dağıtılmaya hazırlandığını" gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure12.png)

1. Uygulama yapılandırıldıktan sonra **son**' a tıklayın.

    !["Son" düğmesinin seçili olduğu "uygulamanızın dağıtıldığı" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Gelişmiş Yapılandırma

>[!NOTE]
>Başvuru için [buraya](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) tıklayın

### <a name="configuring-an-active-directory-aaa-server"></a>Active Directory AAA sunucusunu yapılandırma

Access Policy Manager 'da (APM), kullanıcıların kimliğini doğrulamak için kullanılacak etki alanı denetleyicilerini ve kimlik bilgilerini belirtmek üzere bir Active Directory AAA sunucusu yapılandırırsınız.

1. Ana sekmede, **> aaa sunucuları > Active Directory erişim ilkesi**' ne tıklayın. Active Directory sunucuları listesi ekranı açılır.

2. **Oluştur**’a tıklayın. Yeni sunucu özellikleri ekranı açılır.

3. **Ad** alanına, kimlik doğrulama sunucusu için benzersiz bir ad yazın.

4. **Etki alanı adı** alanına Windows etki alanının adını yazın.

5. **Sunucu bağlantısı** ayarı için şu seçeneklerden birini seçin:

   * AAA sunucusu için yüksek kullanılabilirlik ayarlamak üzere **havuzu kullan** ' ı seçin.

   * Tek başına işlevselliği için AAA sunucusu kurmak üzere **doğrudan** ' yi seçin.

6. **Doğrudan** öğesini seçtiyseniz, **etki alanı denetleyicisi** alanına bir ad yazın.

7. **Havuz** kullan ' ı seçtiyseniz havuzu yapılandırın:

   * **Etki alanı denetleyicisi havuzu adı** alanına bir ad yazın.

   * Havuzdaki **etki alanı denetleyicilerini** , her bırı için IP adresini ve ana bilgisayar adını yazıp **Ekle** düğmesine tıklayarak belirtin.

   * AAA sunucusunun sistem durumunu izlemek için, bir sistem durumu İzleyicisi seçme seçeneğiniz vardır: Bu durumda yalnızca **gateway_icmp** İzleyicisi uygundur; Bunu, **sunucu havuzu İzleyicisi** listesinden seçebilirsiniz.

8. Yönetici **adı** alanında Active Directory yönetim izinlerine sahip bir yönetici için büyük/küçük harfe duyarlı bir ad yazın. APM, AD sorgusu için **yönetici adı** ve **yönetici parolası** alanlarındaki bilgileri kullanır. Anonim sorgular için Active Directory yapılandırıldıysa, yönetici adı sağlamanız gerekmez. Aksi takdirde APM, bir Active Directory sunucusuna bağlamak, Kullanıcı grubu bilgilerini getirmek ve parolayla ilgili işlevselliği desteklemek için Active Directory parola ilkeleri getirmek için yeterli ayrıcalığa sahip bir hesaba ihtiyaç duyuyor. (APM, bir AD sorgu eyleminde kullanıcıdan süre sonundan önce parolayı değiştirmeyi sor seçeneğini belirlerseniz parola ilkelerini almalıdır.) Bu yapılandırmada yönetici hesabı bilgileri sağlamazsanız APM, bilgileri getirmek için Kullanıcı hesabını kullanır. Bu, Kullanıcı hesabının yeterli ayrıcalığı varsa işe yarar.

9. **Yönetici parolası** alanına, etki alanı adıyla ilişkili yönetici parolasını yazın.

10. **Yönetici parolasını doğrula** alanına, **etki alanı adı** ayarıyla ilişkili yönetici parolasını yeniden yazın.

11. **Grup önbelleği ömrü** alanına gün sayısını yazın. Varsayılan yaşam süresi 30 gündür.

12. **Parola güvenlik nesnesi önbelleği ömrü** alanına gün sayısını yazın. Varsayılan yaşam süresi 30 gündür.

13. **Kerberos ön kimlik doğrulaması şifreleme türü** listesinden bir şifreleme türü seçin. Varsayılan değer **none**' dır. Bir şifreleme türü belirtirseniz, büyük IP sistemi, ilk kimlik doğrulama hizmeti isteği (AS-REQ) paketi içinde Kerberos ön kimlik doğrulaması verileri içerir.

14. **Zaman aşımı** alanına aaa sunucusu için bir zaman aşımı aralığı (saniye cinsinden) yazın. (Bu ayar isteğe bağlıdır.)

15. **Bitti**' ye tıklayın. Yeni sunucu listede görüntülenir. Bu, yeni Active Directory sunucusunu Active Directory sunucuları listesine ekler.

    !["Genel Özellikler" ve "yapılandırma" bölümlerini gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML yapılandırması

1. Meta veri sertifikasını, kurulum işleminin ilerleyen kısımlarında kullanılacak olan F5 'e aktarmanız gerekir. **System > sertifika yönetimi > trafik sertifikası yönetimi > SSL sertifikası listesi**' ne gidin. Sağ köşeden **Içeri aktar** ' ı seçin.

    !["Içeri aktar" düğmesinin seçili olduğu "S S L sertifikası/anahtar kaynağını Içeri aktar" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure18.png)

2. SAML ıDP 'yi ayarlamak için, **Access > federasyon > saml: Service Provider > dış IDP bağlayıcıları**' na gidin ve meta verilerden **> oluştur**' a tıklayın.

    !["Oluştur" açılır listesinden "from meta verileri" içeren "S A M L Service Provider" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure19.png)

    !["Yeni S A M L P bağlayıcısını oluştur" iletişim kutusunun gösterildiği ekran görüntüsü.](./media/kerbf5-tutorial/configure20.png)

    !["Genel ayarlar" seçiliyken "S A M L L L P bağlayıcısını Düzenle" penceresinin gösterildiği ekran görüntüsü.](./media/kerbf5-tutorial/configure21.png)

    !["Tek oturum açma hizmeti ayarları" seçiliyken "S A M L L t I r P bağlayıcısını Düzenle" penceresinin gösterildiği ekran görüntüsü.](./media/kerbf5-tutorial/configure22.png)

    !["Güvenlik ayarları" seçiliyken "S A M L L L P bağlayıcısını Düzenle" penceresinin gösterildiği ekran görüntüsü.](./media/kerbf5-tutorial/configure23.png)

    !["S A m L L P bağlayıcısını Düzenle" penceresinin "S L O hizmet ayarları" seçili olduğunu gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure24.png)

1. SAML SP 'yi ayarlamak için, **Yerel SP hizmetleri > > federasyon > SAML hizmet sağlayıcısına erişim** ' e gidin ve **Oluştur**' a tıklayın. Aşağıdaki bilgileri tamamlayıp **Tamam**' a tıklayın.

    * Tür adı: KerbApp200SAML
    * Varlık KIMLIĞI *: https://kerb-app.com.cutestat.com
    * SP adı ayarları
    * Düzen: https
    * Ana bilgisayar: kerbapp200.superdemo.live
    * Açıklama: kerbapp200.superdemo.live

     !["Genel ayarlar" seçiliyken "S A M L S P Service düzenleme" penceresini gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure25.png)

     b. SP yapılandırması ' nı seçin, KerbApp200SAML ve **IDP bağlayıcıları bağlama/** kaldırma ' ya tıklayın.

     !["S a M L Service Provider-Local S P Services" sayfasını "KerbAPP200 S A M L" seçiliyken gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure26.png)

     !["Bağlama/ciltten t P bağlayıcıları bağla" düğmesinin seçili olduğu ekran görüntüsü.](./media/kerbf5-tutorial/configure27.png)

     c. **Yeni satır ekle** ' ye tıklayın ve önceki adımda oluşturulan **dış IDP bağlayıcısını** seçin, **Güncelleştir**' e tıklayın ve ardından **Tamam**' a tıklayın.

     !["Yeni satır ekle" düğmesinin seçili olduğu "Bu S P 'yi kullanan bir M L t S ı d PS 'yi Düzenle" penceresini gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure28.png)

1. Kerberos SSO 'yu yapılandırmak için **erişim > çoklu oturum açma > Kerberos**, bilgileri tam olarak Tamam ' a gidin ve **bitti**' ye tıklayın.

    >[!Note]
    > Oluşturulacak ve belirtilecektir Kerberos temsili hesabı gerekir. KCD bölümüne başvurun (değişken başvuruları için eki Inceleyin)

    * **Kullanıcı adı kaynağı**: oturum. SAML. Last. attr. Name. http: \/ /schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **Kullanıcı bölgesi kaynağı**: Session. Logon. Last. Domain

        !["Kullanıcı adı kaynağı" ve "Kullanıcı bölgesi kaynağı" metin kutuları vurgulanmış "tek Sign-On-Özellikler" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure29.png)

1. Erişim profilini yapılandırmak için, erişim **profili > erişim profiline (oturum ilkeleri başına) >** gidin, **Oluştur**' a tıklayın, aşağıdaki bilgileri doldurun ve **bitti**' ye tıklayın.

    * Ad: KerbApp200
    * Profil türü: tümü
    * Profil kapsamı: profil
    * Diller: Ingilizce

        !["Ad", "profil türü" ve "dil" metin kutuları vurgulanmış "profiller/Ilkeler-Özellikler" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure30.png)

1. KerbApp200 adına tıklayın, aşağıdaki bilgileri doldurun ve **Güncelleştir**' e tıklayın.

    * Etki alanı tanımlama bilgisi: superdemo.live
    * SSO yapılandırması: KerAppSSO_sso

        !["Etki alanı tanımlama bilgisi" metin kutusu ve "S S O yapılandırması" açılır ve "Güncelleştir" düğmesi seçili olan "S S D/auth Domains" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure31.png)

1. **Erişim ilkesi** ' ne tıklayın ve ardından "KerbApp200" profili Için **erişim ilkesini Düzenle** ' ye tıklayın.

    !["Profil KerbApp200 için erişim Ilkesini Düzenle" eyleminin seçildiği "erişim Ilkesi" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure32.png)

    !["Erişim Ilkesi" sayfasını ve "S A M L kimlik doğrulama S P" iletişim kutusunu gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure33.png)

    !["Erişim Ilkesi" sayfasını ve "atama" metin kutuları vurgulanmış "değişken atama" iletişim kutusunu gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure34.png)

    * **Session. Logon. son. usernameUPN Expr {[mcget {Session. SAML. Last. Identity}]}**

    * **Session. ad. lastactualdomain metın superdemo.live**

        !["Erişim Ilkesi" sayfasını ve "arama filtresi" metin kutusu vurgulanmış "Active Directory" iletişim kutusunu gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName =% {Session. Logon. Last. usernameUPN})**

        !["A D sorgu-dal kuralları" iletişim kutusu ile "erişim Ilkesi" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure36.png)

        !["Özel değişken" ve "özel Ifade" metin kutularının vurgulandığını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure37.png)

    * **Session. Logon. Last. UserName expr {"[mcget {Session. ad. Last. attr. sAMAccountName}]"}**

        !["Oturum açma sayfasından Kullanıcı adı" metin kutusunun vurgulandığını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure38.png)

    * **mcget {Session. Logon. Last. UserName}**
    * **mcget {Session. Logon. Last. Password**

1. Yeni düğüm eklemek için, **Yerel trafik > düğümler > düğüm listesi ' ne gidin, Oluştur ' a tıklayın**, aşağıdaki bilgileri doldurun ve ardından **bitti**' ye tıklayın.

    * Ad: KerbApp200
    * Açıklama: KerbApp200
    * Adres: 192.168.20.200

        !["Ad", "Açıklama" ve "adres" metin kutularının vurgulandığına ve "tamamlandı" düğmesinin seçili olduğu "yeni düğüm" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure39.png)

1. Yeni bir havuz oluşturmak için **Yerel trafik > havuzlar > havuz listesi**' ne gidin, Oluştur ' a tıklayın, aşağıdaki bilgileri doldurun ve **bitti**' ye tıklayın.

    * Ad: KerbApp200-Pool
    * Açıklama: KerbApp200-Pool
    * Sistem durumu Izleyicileri: http
    * Adres: 192.168.20.200
    * Hizmet bağlantı noktası: 81

        ![Girilen değerlerin ve "tamamlandı" düğmesinin seçili olduğu "yeni havuz" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure40.png)

1. Sanal sunucu oluşturmak için, sanal sunucu **listesi > sanal sunucular > yerel trafiğe gidin > +**, aşağıdaki bilgileri tamamlayıp **bitti**' ye tıklayın.

    * Ad: KerbApp200
    * Hedef adres/maske: Ana bilgisayar 192.168.30.200
    * Hizmet bağlantı noktası: bağlantı noktası 443 HTTPS
    * Erişim profili: KerbApp200
    * Önceki adımda oluşturulan erişim profilini belirtin

        !["Ad", "hedef adres/maske" ve "hizmet bağlantı noktası" metin kutularının vurgulandığı "sanal sunucu listesi" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure41.png)

        !["Erişim profili" açılan listesi vurgulanmış "sanal sunucu listesi" sayfasını gösteren ekran görüntüsü.](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Kerberos temsilcisini ayarlama 

>[!NOTE]
>Başvuru için [buraya](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) tıklayın

*  **1. Adım:** Bir temsili hesabı oluşturun

    **Örnek:**
    * Etki alanı adı: **superdemo.Live**

    * Sam hesap adı: **büyük-ipuser**

    * New-ADUser-Name "APM temsili hesabı"-UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -sAMAccountName "Big-ıpuser"-PasswordNeverExpires $true etkin $true-AccountPassword (Read-Host-AsSecureString "Password! 1234")

* **2. Adım:** SPN 'YI ayarla (APM temsili hesabında)

    **Örnek:**
    * Setspn – A **Host/Big-ipuser. superdemo. canlı** büyük-ipuser

* **Adım 3:** SPN temsili (App Service hesabı için) F5 temsili hesabı için uygun temsilciyi ayarlayın.
    Aşağıdaki örnekte, APM temsili hesabı FRP-app1. superdemo için KCD için Yapılandırılıyor. canlı uygulama.

    ![F5 (Kerberos) yapılandırması](./media/kerbf5-tutorial/configure43.png)

* Yukarıdaki başvuru belgesinde bahsedilen ayrıntıları burada [belirtin.](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="create-f5-test-user"></a>F5 test kullanıcısı oluştur

Bu bölümde, F5 'te B. Simon adlı bir Kullanıcı oluşturacaksınız. F5 platformunda kullanıcıları eklemek için [F5 istemci destek ekibi](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde F5 kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız F5 'te otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek kaynaklar

- [ SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi ](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir? ](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)

- [Azure AD ile F5 'i deneyin](https://aad.portal.azure.com/)

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](headerf5-tutorial.md)

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](advance-kerbf5-tutorial.md)

- [F5 BIG-IP APM ve güvenli karma erişim için Azure AD tümleştirmesi](../manage-apps/f5-aad-integration.md)

- [Güvenli karma erişim için Azure IaaS 'de F5 BIG-IP Virtual Edition VM dağıtma öğreticisi](../manage-apps/f5-bigip-deployment-guide.md)

- [Parola açısından daha az VPN için F5 BIG-IP ile çoklu oturum açma tümleştirmesi Azure Active Directory öğreticisi](../manage-apps/f5-aad-password-less-vpn.md)