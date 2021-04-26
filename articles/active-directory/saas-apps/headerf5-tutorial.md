---
title: 'Öğretici: F5 ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve F5 arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 28040a92d713707204f0651773f7edce9f4daf6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651782"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Öğretici: Azure Active Directory ile F5 arasında çoklu oturum açma (SSO) yapılandırma

Bu öğreticide, F5 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. F5 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de F5 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla F5 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

> [!NOTE]
> F5 BIG-IP APM [Şimdi satın al](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

* F5 çoklu oturum açma (SSO) etkin abonelik.

* Ortak çözümü dağıtmak için aşağıdaki lisans gerekir:

    * F5 BIG-IP® En Iyi demeti (veya) 

    * F5 BIG-IP Access Policy Manager™ (APM) tek başına lisansı 

    * F5 BIG-IP Access Policy Manager™ (APM) bir büyük IP F5 BIG-IP® yerel Traffic Manager™ (LTM) eklenti lisansı.

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

1. **Kılavuzlu yapılandırma** sayfasında, sol üst köşedeki **Kılavuzlu yapılandırmayı Yükselt** ' e tıklayın.

    ![Ekran görüntüsü, Kılavuzlu yapılandırmayı Güncelleştir bağlantısını içeren Kılavuzlu yapılandırma sayfasını gösterir.](./media/headerf5-tutorial/configure14.png) 

1. Yükseltme Kılavuzu yapılandırma açılan ekranında, indirilen kullanım örneği paketini karşıya yüklemek için **Dosya Seç** ' i seçin ve karşıya yükle **ve yükle** düğmesine tıklayın.

    ![Ekran görüntüsü, seçili dosya Seç ' in bulunduğu Kılavuzlu yapılandırmayı Yükselt iletişim kutusunu gösterir.](./media/headerf5-tutorial/configure15.png) 

1. Yükseltme tamamlandığında **devam** düğmesine tıklayın.

    ![Ekran görüntüsü, bir tamamlanma iletisi ile yükseltme destekli yapılandırma iletişim kutusunu gösterir.](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* F5 SSO üç farklı şekilde yapılandırılabilir.

- [Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma](#configure-f5-single-sign-on-for-header-based-application)

- [Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](kerbf5-tutorial.md)

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Anahtar kimlik doğrulama senaryoları

* Açık KIMLIK Connect, SAML ve WS-Bessel kimlik doğrulama protokolleri için Azure Active Directory yerel tümleştirme desteğiyle, F5, Azure AD ile hem iç hem de dış erişim için, eski tabanlı kimlik doğrulama uygulamalarına yönelik güvenli erişimi genişleterek bu uygulamalara modern senaryolar (ör. parola daha az erişim) sağlar. Bu şunlar dahildir:

* Üst bilgi tabanlı kimlik doğrulama uygulamaları

* Kerberos kimlik doğrulama uygulamaları

* Anonim kimlik doğrulaması veya yerleşik kimlik doğrulama uygulamaları

* NTLM kimlik doğrulama uygulamaları (Kullanıcı için çift istemlerle koruma)

* Form tabanlı uygulama (Kullanıcı için çift istemlerle koruma)

## <a name="adding-f5-from-the-gallery"></a>Galeriden F5 ekleme

F5 'in tümleştirmesini Azure AD 'ye göre yapılandırmak için Galeri 'den yönetilen SaaS uygulamaları listenize F5 ' i eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **F5** yazın.
1. Sonuçlar panelinden **F5** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-f5"></a>F5 için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'yu F5 ile yapılandırın ve test edin. SSO 'nun çalışması için, F5 'teki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu F5 ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için **[F5 SSO 'Yu yapılandırın](#configure-f5-sso)** .
    1. F5 'te kullanıcının Azure AD gösterimine bağlı olan B. Simon 'un bir karşılığı olacak şekilde **[F5 test kullanıcısı oluşturun](#create-f5-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal **F5** uygulaması tümleştirme sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
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
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-f5-sso"></a>F5 SSO 'yu Yapılandır

- [Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](kerbf5-tutorial.md)

- [Gelişmiş Kerberos uygulaması için F5 çoklu oturum açmayı yapılandırma](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Üst bilgi tabanlı uygulama için F5 çoklu oturum açmayı yapılandırma

### <a name="guided-configuration"></a>Kılavuzlu yapılandırma

1. Yeni bir Web tarayıcı penceresi açın ve F5 (üst bilgi tabanlı) Şirket sitenizde yönetici olarak oturum açın ve aşağıdaki adımları gerçekleştirin:

1. **System > sertifika yönetimi > trafik sertifikası yönetimi > SSL sertifikası listesi**' ne gidin. Sağ köşeden **Içeri aktar** ' ı seçin. Bir **sertifika adı** belirtin (daha sonra yapılandırmadan başvurulacak). **Sertifika kaynağında** karşıya yükle ' yı seçin SAML çoklu oturum açmayı yapılandırırken Azure 'dan indirilen sertifikayı belirtin. **İçeri Aktar**’a tıklayın.

    ![Ekran görüntüsü, sertifika adı ve sertifika kaynağını seçtiğiniz S S L sertifika listesini gösterir.](./media/headerf5-tutorial/configure12.png)
 
1. Ayrıca, **Uygulama ana bilgisayar adı Için SSL sertifikası gerekir. System > sertifika yönetimi > trafik sertifikası yönetimi > SSL sertifikası listesi**' ne gidin. Sağ köşeden **Içeri aktar** ' ı seçin. **Içeri aktarma türü** **PKCS 12 (IIS)** olacaktır. **Anahtar adı** belirtin (daha sonra yapılandırmadan başvurulacak) ve pfx dosyasını belirtmeniz gerekir. PFX için **parola** belirtin. **İçeri Aktar**’a tıklayın.

    >[!NOTE]
    >Uygulama adı örneğinde, `Headerapp.superdemo.live` anahtar adının bir joker karakter sertifikası kullandık `WildCard-SuperDemo.live` .

    ![Ekran görüntüsü, S S L Sertifika/anahtar kaynağı sayfasını gösterir.](./media/headerf5-tutorial/configure13.png)

1. Azure AD Federasyonu ve uygulama erişimini ayarlamak için destekli deneyim kullanacağız. – F5 BIG-IP **Main** ' e gidin ve **erişim > Kılavuzlu yapılandırma > Federasyon > SAML hizmeti sağlayıcısı**' nı seçin. **İleri** ' ye ve ardından yapılandırmaya başlamak için **İleri** ' ye tıklayın.

    ![Ekran görüntüsü, Federasyon seçiliyken Kılavuzlu yapılandırma sayfasını gösterir.](./media/headerf5-tutorial/configure01.png)

    ![Ekran görüntüsü SAML hizmet sağlayıcısı sayfasını gösterir.](./media/headerf5-tutorial/configure02.png)
 
1. Bir **yapılandırma adı** girin. **VARLıK kimliğini** (Azure AD uygulama yapılandırmasında yapılandırdığınız gibi) belirtin. **Ana bilgisayar adını** belirtin. Başvuru için bir **Açıklama** ekleyin. Kalan varsayılan girişleri kabul edin ve ardından **& kaydet**' e tıklayın.

    ![Ekran görüntüsü, hizmet sağlayıcısı özellikleri sayfasını gösterir.](./media/headerf5-tutorial/configure03.png) 

1. Bu örnekte, 443 numaralı bağlantı noktası ile 192.168.30.20 olarak yeni bir sanal sunucu oluşturacağız. **Hedef adreste** sanal sunucu IP adresini belirtin. Istemci **SSL profilini** seçin, yeni oluştur ' u seçin. Daha önce karşıya yüklenen uygulama sertifikasını (Bu örnekteki joker karakter sertifikası) ve ilişkili anahtarı belirtin ve ardından **& Ileri kaydet**' e tıklayın.

    >[!NOTE]
    >Bu örnekte, Iç Web sunucusu 888 numaralı bağlantı noktasında çalışıyor ve bunu 443 ile yayımlamak istiyoruz.

    ![Ekran görüntüsü, sanal sunucu özellikleri sayfasını gösterir.](./media/headerf5-tutorial/configure04.png) 

1. **IDP bağlayıcınızı yapılandırmak için yöntem seçin** altında, meta veri ' yi belirtin, Dosya Seç ' e tıklayın ve daha önce Azure AD 'Den Indirilen meta veri xml dosyasını yükleyin SAML ıDP Bağlayıcısı için benzersiz bir **ad** belirtin. Daha önce karşıya yüklenen **meta veri Imzalama sertifikasını** seçin. **İleri & kaydet**' e tıklayın.

    ![Ekran görüntüsü dış kimlik sağlayıcısı bağlayıcı ayarları sayfasını gösterir.](./media/headerf5-tutorial/configure05.png)
 
1. **Havuz Seç** altında **Yeni oluştur** ' u (alternatif olarak zaten var olan bir havuz seçin) belirtin. Diğer değerin varsayılan olmasına izin verin. Havuz sunucuları ' nın altında IP **adresi/düğüm adı** altında IP adresini yazın. **Bağlantı noktasını** belirtin. **İleri & kaydet**' e tıklayın.

    ![Ekran görüntüsü havuz özellikleri sayfasını gösterir.](./media/headerf5-tutorial/configure06.png)

1. Tek Sign-On ayarları ekranında **Çoklu oturum açmayı etkinleştir**' i seçin. Seçili tek Sign-On türü altında **http üst bilgi tabanlı**' yı seçin. Username. **SAML. Last. Identity** ' i  (Azure AD 'de talep eşlemesi kullanarak ayarlanan bu değişken) Kullanıcı adı kaynak (Bu değişken). SSO üstbilgileri altında.

    * **HeaderName: Myauthorleştirme**

    * **Üst bilgi değeri:% {Session. SAML. Last. attr. Name. Identity}**

    * **Sonra & kaydet** ' e tıklayın

    Değişkenlerin ve değerlerin tamamı listesi için ek 'e bakın. Gerektiğinde daha fazla üst bilgi ekleyebilirsiniz.

    >[!NOTE]
    >Hesap adı F5 temsilcisini oluşturma hesabıdır (F5 belgelerini kontrol edin).

    ![Ekran görüntüsü tek Sign-On ayarları sayfasını gösterir.](./media/headerf5-tutorial/configure07.png) 

1. Bu kılavuzun amaçları doğrultusunda Endpoint denetimlerini atlayacağız.  Ayrıntılar için F5 belgelerine bakın. **& Kaydet İleri ' yi** seçin.

    ![Ekran görüntüsü, uç nokta denetimleri Özellikler sayfasını gösterir.](./media/headerf5-tutorial/configure08.png)

1. Varsayılanları kabul edin ve **& Ileri kaydet**' e tıklayın. SAML oturum yönetimi ayarları hakkında ayrıntılı bilgi için F5 belgelerine bakın.

    ![Ekran görüntüsü zaman aşımı ayarları sayfasını gösterir.](./media/headerf5-tutorial/configure09.png)

1. BÜYÜK IP 'yi yapılandırmak için özet ekranını gözden geçirin ve **Dağıt** ' ı seçin. **son**' a tıklayın.

    ![Ekran görüntüsü, uygulamanızın dağıtılmaya hazırlanma sayfasını gösterir.](./media/headerf5-tutorial/configure10.png)

    ![Ekran görüntüsünde uygulamanızın dağıtıldığı sayfa görüntülenir.](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Gelişmiş Yapılandırma

Bu bölüm, Kılavuzlu yapılandırmayı kullanmezseniz veya ek parametreler eklemek/değiştirmek istiyorsanız kullanılmak üzere tasarlanmıştır. Uygulama ana bilgisayar adı için bir TLS/SSL sertifikasına ihtiyacınız olacak.

1. **System > sertifika yönetimi > trafik sertifikası yönetimi > SSL sertifikası listesi**' ne gidin. Sağ köşeden **Içeri aktar** ' ı seçin. **Içeri aktarma türü** **PKCS 12 (IIS)** olacaktır. **Anahtar adı** belirtin (daha sonra yapılandırmadan başvurulacak) ve pfx dosyasını belirtmeniz gerekir. PFX için **parola** belirtin. **İçeri Aktar**’a tıklayın.

    >[!NOTE]
    >Uygulama adı örneğinde, `Headerapp.superdemo.live` anahtar adının bir joker karakter sertifikası kullandık `WildCard-SuperDemo.live` .
  
    ![Ekran görüntüsü, gelişmiş yapılandırma için S S L Sertifika/anahtar kaynağı sayfasını gösterir.](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Bıgıp 'ye yeni bir Web sunucusu ekleme-F5

1. **Ana > IApps > Uygulama Hizmetleri > uygulama > oluştur**' a tıklayın.

1. **Adı** ve **şablonu** altına girin **F5. http**' yi seçin.
 
    ![Ekran görüntüsü, şablon seçimine sahip Uygulama Hizmetleri sayfasını gösterir.](./media/headerf5-tutorial/configure18.png)

1. Bu durumda HeaderApp2 dışarıdan HTTPS olarak yayımlanacak, **büyük IP SISTEM SSL trafiğini nasıl işleyeceğiz**? Istemciden bir **düz metin (SSL yük boşaltma) Için sonlandırma SSL**'yi belirttik. **Kullanmak ISTEDIĞINIZ SSL sertifikası** ile sertifikanızı ve anahtarınızı belirtin ve **hangi SSL özel anahtarını kullanmak istiyorsunuz?**. Sanal sunucu **için kullanmak ISTEDIĞINIZ IP adresi** altında sanal sunucu IP 'sini belirtin. 

    * **Diğer ayrıntıları belirtin**

        * FQDN  

        * Uygulama havuzundan çıkılıyor veya yeni bir tane oluşturun.

        * Yeni bir uygulama sunucusu oluşturuyorsanız, **Iç IP adresini** ve **bağlantı noktası numarasını** belirtin.

        ![Ekran görüntüsü bu ayrıntıları belirtebileceğiniz bölmeyi gösterir.](./media/headerf5-tutorial/configure19.png) 

1. **Bitti**' ye tıklayın.

    ![Ekran görüntüsü tamamlandığında sayfayı gösterir.](./media/headerf5-tutorial/configure20.png) 

1. Uygulama özelliklerinin değiştirilebilmesi için emin olun. **Ana > IApps > uygulama hizmetleri: uygulamalar >> HeaderApp2**' e tıklayın. **Katı güncelleştirmelerin** işaretini KALDıRıN (GUI dışında bazı ayarları değiştirecağız). **Güncelleştir** düğmesine tıklayın.

    ![Ekran görüntüsünde Özellikler sekmesi seçili olan Uygulama Hizmetleri sayfası gösterilir.](./media/headerf5-tutorial/configure21.png) 

1. Bu noktada, sanal sunucuya gözatabilmeniz gerekir.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>IOP olarak F5 ve Azure olarak yapılandırma

1.  **> federasyon> SAML hizmeti sağlayıcısı > yerel SP hizmeti ' ne tıklayın > oluştur veya + imzala ' ya tıklayın**.

    ![Ekran görüntüsü bu büyük ı P hakkında sayfasını gösterir. ](./media/headerf5-tutorial/configure22.png)

1. Hizmet sağlayıcı hizmeti için ayrıntıları belirtin. F5 SP yapılandırmasını temsil eden **adı** belirtin. **VARLıK kimliğini** belirtin (genellikle uygulama URL 'si ile aynı).

    ![Ekran görüntüsü, yeni SAML S P hizmeti oluştur iletişim kutusu ile SAML hizmeti sağlayıcısı sayfasını gösterir.](./media/headerf5-tutorial/configure23.png)

    ![Ekran görüntüsü Endpoint Settings seçiliyken yeni SAML S P hizmeti oluştur iletişim kutusunu gösterir.](./media/headerf5-tutorial/configure24.png)

    ![Ekran görüntüsü yeni SAML S P hizmeti oluştur iletişim kutusunun Güvenlik ayarları seçili olduğunu gösterir.](./media/headerf5-tutorial/configure25.png)

    ![Ekran görüntüsü, kimlik doğrulama bağlamı seçiliyken yeni SAML S P hizmeti oluştur iletişim kutusunu gösterir.](./media/headerf5-tutorial/configure26.png)

    ![Ekran görüntüsü, Istenen özniteliklerin seçili olduğu yeni SAML S P hizmeti oluştur iletişim kutusunu gösterir.](./media/headerf5-tutorial/configure27.png)

    ![Ekran görüntüsü, Gelişmiş ayarlar seçiliyken SAML S P hizmetini Düzenle iletişim kutusunu gösterir.](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>IDP Bağlayıcısı oluştur

1. **Bağlama/kaldırma IDP bağlayıcıları** düğmesine tıklayın, **Yeni IDP Bağlayıcısı oluştur** ' u seçin ve **meta veriler** seçeneğini belirleyin ve ardından aşağıdaki adımları gerçekleştirin:
 
    ![Ekran görüntüsünde, yeni ı d P Bağlayıcısı Oluştur seçiliyken Bu S P kullanan bu S P 'yi Düzenle adlı SAML ı 'Leri Düzenle iletişim kutusu gösterilir.](./media/headerf5-tutorial/configure29.png)

    a. Azure AD 'den indirilen metadata.xml dosyasına gidin ve bir **kimlik sağlayıcısı adı** belirtin.

    b. **Tamam**' a tıklayın.

    c. Bağlayıcı oluşturulur ve sertifika, meta veri XML dosyasından otomatik olarak hazırdır.
    
    ![Ekran görüntüsü yeni SAML ı d Bağlayıcısı oluştur iletişim kutusunu gösterir.](./media/headerf5-tutorial/configure30.png)

    d. F5BIG-IP ' i tüm istekleri Azure AD 'ye gönderecek şekilde yapılandırın.

    e. **Yeni satır ekle**' ye tıklayın, **AzureIDP** öğesini seçin (önceki adımlarda oluşturulan gibi), şunu belirtin 

    f. **Eşleşen kaynak =% {Session. Server. landinguri}** 

    örneğin: **Eşleşen değer =/***

    h. **Güncelleştir** 'e tıklayın

    i. **Tamam 'a** tıklayın

    j. **SAML ıDP kurulumu tamamlandı**
    
    ![Ekran görüntüsünde, bu S P 'ye Kullanıcı tarafından kullanılan SAML ı 'Leri Düzenle iletişim kutusu gösterilir.](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Kullanıcıları Azure SAML ıDP 'ye yönlendirmek için F5 Ilkesini yapılandırma

1. F5 Ilkesini kullanıcıları Azure SAML ıDP 'ye yönlendirmek üzere yapılandırmak için aşağıdaki adımları uygulayın:

    a. **Erişim profillerine > > ana > profil/ilkeler**' e tıklayın.

    b. **Oluştur** düğmesine tıklayın.

    ![Ekran görüntüsü, erişim profilleri sayfasını gösterir.](./media/headerf5-tutorial/configure32.png)
 
    c. **Ad** belirtin (örnekte HeaderAppAzureSAMLPolicy).

    d. Diğer ayarları özelleştirebilirsiniz, lütfen F5 belgelerine başvurun.

    ![Ekran görüntüsü Genel Özellikler sayfasını gösterir.](./media/headerf5-tutorial/configure33.png)

    ![Ekran görüntüsü, devam eden genel özellikler sayfasını gösterir.](./media/headerf5-tutorial/configure34.png) 

    e. **Bitti**' ye tıklayın.

    f. Ilke oluşturma işlemi tamamlandıktan sonra, Ilkeye tıklayın ve **erişim ilkesi** sekmesine gidin.

    ![Ekran görüntüsü genel özelliklerle birlikte erişim Ilkesi sekmesini gösterir.](./media/headerf5-tutorial/configure35.png)
 
    örneğin: **Görsel ilke Düzenleyicisi**' ne tıklayın, **profil için erişim ilkesini** düzenleyin.

    h. Görsel Ilke düzenleyicisinde + Işaretine tıklayın ve **SAML kimlik doğrulaması**' nı seçin.

    ![Ekran görüntüsünde bir erişim Ilkesi gösterilir.](./media/headerf5-tutorial/configure36.png)

    ![Ekran görüntüsü, SAML auth seçiliyken bir arama iletişim kutusu gösterir.](./media/headerf5-tutorial/configure37.png)
 
    i. **Öğe Ekle**' ye tıklayın.

    j. **Özellikler** **adı** belirtin ve **aaa sunucusu** altında, önceden yapılandırılmış SP 'yi seçin, **Kaydet**' e tıklayın.
 
    ![Ekran görüntüsü, bir sunucusu da dahil olmak üzere öğenin özelliklerini gösterir.](./media/headerf5-tutorial/configure38.png)

    k. Temel Ilke kullanılabilir, ilkeyi ek kaynaklar/öznitelik depoları içerecek şekilde özelleştirebilirsiniz.

    ![Ekran görüntüsü özelleştirilmiş ilkeyi gösterir.](./media/headerf5-tutorial/configure39.png)
 
    l. Üstteki **erişim Ilkesini Uygula** bağlantısına tıklatığınızdan emin olun.

### <a name="apply-access-profile-to-the-virtual-server"></a>Erişim profilini sanal sunucuya Uygula

1. F5 BIG-IP APM 'nin profil ayarlarını gelen trafiğe uygulaması ve daha önce tanımlanan erişim ilkesini çalıştırması için, erişim profilini sanal sunucuya atayın.

    a. **Ana**  >  **Yerel trafik**  >  **sanal sunucuları**' na tıklayın.

    ![Ekran görüntüsü, sanal sunucular listesi sayfasını gösterir.](./media/headerf5-tutorial/configure40.png)
 
    b. Sanal sunucu ' ya tıklayın, **erişim ilkesi** ' ne gidin, **erişim profili** açılır penceresinde, oluşturulan SAML Ilkesini seçin (örnekte HeaderAppAzureSAMLPolicy)

    c. **Güncelleştir** 'e tıklayın
 
    ![Ekran görüntüsü, erişim Ilkesi bölmesini gösterir.](./media/headerf5-tutorial/configure41.png)

    d. gelen onaylamanın özel SAML özniteliklerini ayıklamak ve bunları arka uç test uygulamasına HTTP üstbilgileri olarak geçirmek için bir F5 BIG-IP ırule® oluşturun. **Ana > yerel trafik > ırules > ırule listesi ' ne tıklayın > oluştur ' a tıklayın** .

    ![Ekran görüntüsü yerel trafik ırule listesini gösterir.](./media/headerf5-tutorial/configure42.png)
 
    e. Aşağıdaki F5 büyük IP ırule metnini tanım penceresine yapıştırın.

    ![Ekran görüntüsü yeni ırule sayfasını gösterir.](./media/headerf5-tutorial/configure43.png)
 
    RULE_INIT {set static::d ebug 0} ACCESS_ACL_ALLOWED {

    http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name{$static::d ebug} {log local0 ise [erişim:: oturum verileri al "Session.SAML.Last.attr.Name."] olarak ayarlayın AZUREAD_USERNAME. "AZUREAD_USERNAME = $AZUREAD _USERNAME"} if {! ( [HTTP:: header var "AZUREAD_USERNAME"]) } {HTTP:: header INSERT "AZUREAD_USERNAME" $AZUREAD _USERNAME}

    http://schemas.microsoft.com/identity/claims/displayname{$static::d ebug} {log local0 ise [erişim:: oturum verileri al "Session.SAML.Last.attr.Name."] olarak ayarlayın AZUREAD_DISPLAYNAME. "AZUREAD_DISPLAYNAME = $AZUREAD _DISPLAYNAME"} if {! ( [HTTP:: header var "AZUREAD_DISPLAYNAME"]) } {HTTP:: header INSERT "AZUREAD_DISPLAYNAME" $AZUREAD _DISPLAYNAME}

    http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress{$static::d ebug} {log local0 ise [erişim:: oturum verileri al "Session.SAML.Last.attr.Name."] olarak ayarlayın AZUREAD_EMAILADDRESS. "AZUREAD_EMAILADDRESS = $AZUREAD _EMAILADDRESS"} if {! ( [HTTP:: header var "AZUREAD_EMAILADDRESS"]) } {HTTP:: header INSERT "AZUREAD_EMAILADDRESS" $AZUREAD _EMAILADDRESS}}

    **Aşağıdaki örnek çıktı**

    ![Ekran görüntüsü örnek çıktıyı gösterir.](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>F5 test kullanıcısı oluştur

Bu bölümde, F5 'te B. Simon adlı bir Kullanıcı oluşturacaksınız. F5 platformunda kullanıcıları eklemek için [F5 istemci destek ekibi](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir. 

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz F5 oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan F5 oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız F5 'te otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içinde F5 kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız F5 'te otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

> [!NOTE]
> F5 BIG-IP APM [Şimdi satın al](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="next-steps"></a>Sonraki adımlar

F5 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).