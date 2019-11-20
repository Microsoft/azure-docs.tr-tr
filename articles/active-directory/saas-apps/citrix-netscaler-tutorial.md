---
title: 'Öğretici: Citrix NetScaler ile Azure Active Directory Tümleştirme | Microsoft Docs'
description: Azure Active Directory ve Citrix NetScaler arasında çoklu oturum açmayı yapılandırmayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 64dd67680626857db7f39fa7fd721b28a02d1561
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276896"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Öğretici: Citrix NetScaler ile Azure Active Directory Tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile Citrix NetScaler tümleştirme konusunda bilgi edinin.
Citrix NetScaler'ı Azure AD ile tümleştirme ile aşağıdaki avantajları sağlar:

* Citrix NetScaler erişimi, Azure AD'de kontrol edebilirsiniz.
* Azure AD hesaplarına otomatik olarak (çoklu oturum açma) için Citrix NetScaler oturum açmış, kullanıcıların etkinleştirebilirsiniz.
* Hesaplarınız bir merkezi konumda - Azure portalında yönetebilir.

Azure AD SaaS uygulama tümleştirmesi hakkında daha fazla ayrıntı bilmek istiyorsanız, bkz. [uygulama erişimi ve Azure Active Directory ile çoklu oturum açma nedir](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Azure AD Tümleştirmesi ile Citrix NetScaler yapılandırmak için aşağıdaki öğeler gerekir:

* Azure AD aboneliğiniz. Bir Azure AD ortamını yoksa, bir aylık deneme alabilirsiniz [burada](https://azure.microsoft.com/pricing/free-trial/)
* Citrix NetScaler çoklu oturum açma abonelik etkin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, yapılandırma ve Azure AD çoklu oturum açma bir test ortamında test edin.

* Citrix NetScaler destekler **SP** tarafından başlatılan

* Citrix NetScaler destekler **zamanında** kullanıcı sağlama

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler galeri ekleme

Azure AD'de Citrix NetScaler tümleştirmesini yapılandırmak için Citrix NetScaler Galeriden yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

**Citrix NetScaler Galeriden eklemek için aşağıdaki adımları gerçekleştirin:**

1. İçinde **[Azure portalında](https://portal.azure.com)** , sol gezinti panelinde tıklayın **Azure Active Directory** simgesi.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. Gidin **kurumsal uygulamalar** seçip **tüm uygulamaları** seçeneği.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için tıklatın **yeni uygulama** iletişim üst kısmındaki düğmesi.

    ![Yeni Uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **Citrix NetScaler**seçin **Citrix NetScaler** sonucu panelinden ardından **Ekle** uygulama eklemek için Ekle düğmesine.

     ![Sonuç listesinde Citrix NetScaler](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

Bu bölümde, yapılandırmanız ve Citrix NetScaler ile Azure AD çoklu oturum açmayı test adlı bir test kullanıcı tabanlı **Britta Simon**.
Tek iş için oturum açma için bir Azure AD kullanıcısı ve Citrix NetScaler ilgili kullanıcı arasında bir bağlantı ilişki kurulması gerekir.

Yapılandırma ve Azure AD çoklu oturum açma Citrix NetScaler ile'test etmek için aşağıdaki yapı taşlarını tamamlanması gerekir:

1. **[Azure AD çoklu oturum açmayı yapılandırmayı](#configure-azure-ad-single-sign-on)**  - bu özelliği kullanmak, kullanıcılarınızın etkinleştirmek için.
2. **[Citrix NetScaler çoklu oturum açmayı yapılandırma](#configure-citrix-netscaler-single-sign-on)**  - uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. **[Bir Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)**  - Azure AD çoklu oturum açma Britta Simon ile test etmek için.
4. **[Azure AD test kullanıcı atama](#assign-the-azure-ad-test-user)**  - Azure AD çoklu oturum açmayı kullanmak Britta Simon etkinleştirmek için.
5. **[Citrix NetScaler test kullanıcısı oluşturma](#create-citrix-netscaler-test-user)**  - kullanıcı Azure AD gösterimini bağlı Citrix NetScaler Britta simon'un bir karşılığı vardır.
6. **[Çoklu oturum açmayı test](#test-single-sign-on)**  - yapılandırma çalışıp çalışmadığını doğrulayın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırın

Bu bölümde, Azure AD çoklu oturum açma Azure portalında etkinleştirin.

Azure AD çoklu oturum açma ile Citrix NetScaler yapılandırmak için aşağıdaki adımları gerçekleştirin:

1. İçinde [Azure portalında](https://portal.azure.com/), **Citrix NetScaler** uygulama tümleştirme sayfasında **çoklu oturum açma**.

    ![Çoklu oturum açma bağlantısı yapılandırma](common/select-sso.png)

2. Üzerinde **tek bir oturum açma yönteminizi seçmeniz** iletişim kutusunda, **SAML/WS-Federasyon** modu, çoklu oturum açmayı etkinleştirmek için.

    ![Çoklu oturum açma seçim modu](common/select-saml-option.png)

3. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **Düzenle** açmak için simgeyi **temel SAML yapılandırma** iletişim.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. Üzerinde **temel SAML yapılandırma** bölümünde, aşağıdaki adımları gerçekleştirin:

    ![Citrix NetScaler etki alanı ve URL'ler tek oturum açma bilgileri](common/sp-identifier-reply.png)

    a. İçinde **oturum açma URL'si** metin kutusuna şu biçimi kullanarak bir URL yazın: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. İçinde **tanımlayıcı (varlık kimliği)** metin kutusuna şu biçimi kullanarak bir URL yazın: `https://<<Your FQDN>>`

    c. İçinde **yanıt URL'si (onay belgesi tüketici hizmeti URL'si)** metin kutusuna şu biçimi kullanarak bir URL yazın: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerler gerçek oturum açma URL ve tanımlayıcıdır ile güncelleştirin. İlgili kişi [Citrix NetScaler istemci Destek ekibine](https://www.citrix.com/contact/technical-support.html) bu değerleri almak için. Gösterilen desenleri de başvurabilirsiniz **temel SAML yapılandırma** bölümünde Azure portalında.

    > [!NOTE]
    > SSO çalışmaya başlamak için bu URL'ler genel sitelerden erişilebilir olması gerekir. Güvenlik Duvarı veya diğer güvenlik ayarları Netscaler tarafında enble belirtecin yapılandırılmış ACS URL'SİNDE göndermek için Azure AD için etkinleştirmeniz gerekir.

5. Üzerinde **yukarı çoklu oturum açma SAML ile ayarlanmış** sayfasında **SAML imzalama sertifikası** bölümünde **indirme** indirmek için **Federasyon meta veri XML**  bilgisayarınızdaki belirli seçenekler ihtiyacınıza göre ve kaydedin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

6. Üzerinde **Citrix NetScaler ' ayarlamak** bölümünde, ihtiyacınıza göre uygun URL'lerini kopyalayın.

    ![Yapılandırma URL'leri kopyalayın](common/copy-configuration-urls.png)

    a. Oturum Açma URL'si:

    b. Azure AD Tanımlayıcısı

    c. Oturum Kapatma URL'si

### <a name="configure-citrix-netscaler-single-sign-on"></a>Citrix NetScaler çoklu oturum açmayı yapılandırın

1. Farklı bir web tarayıcı penceresinde Citrix NetScaler kiracınıza yönetici olarak oturum.

2. Emin olun **NetScaler üretici yazılımı sürümü NS12.1 =: 48.13.NC yapı**.

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure01.png)

3. Üzerinde **VPN Virtual Server** sayfasında, aşağıdaki adımları gerçekleştirin:

     ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure02.png)

    a. Ağ Geçidi ayarlarını ayarlayın **yalnızca ICA** olarak **true**.
    
    b. Ayarlama **kimlik doğrulamasını etkinleştir** olarak **true**.
    
    c. **DTLS** isteğe bağlıdır.
    
    d. Emin **SSLv3** olarak **devre dışı bırakılmış**.

4. Özelleştirilmiş **SSL şifrelemeleri** Grup A + üzerinde elde etmek için oluşturulur https://www.ssllabs.com aşağıda gösterildiği gibi:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure03.png)

5. Üzerinde **kimlik doğrulaması SAML Server'ı Yapılandır** sayfasında, aşağıdaki adımları gerçekleştirin:

      ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure04.png)

    a. İçinde **adı** metin kutusuna sunucunuzun adını yazın.

    b. İçinde **tekrar yönlendirme URL'sini** metin değerini yapıştırın **oturum açma URL'si** hangi Azure portaldan kopyaladığınız.

    c. İçinde **çoklu oturum kapatma URL'si** metin değerini yapıştırın **oturum kapatma URL'si** hangi Azure portaldan kopyaladığınız.

    d. İçinde **IDP sertifika adı**, tıklayın **"+"** Azure portalından indirdiğiniz sertifika eklemek oturum açın. Lütfen karşıya yüklendikten sonra sertifika, açılan listeden seçin.

    e. Bu sayfada ayarlamak daha fazla alan aşağıdaki gerekir

      ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure24.png)

    f. Seçin **istenen kimlik doğrulaması bağlamı** olarak **tam**.

    g. Seçin **imza algoritması** olarak **RSA-SHA256**.

    h. Seçin **Özet yöntemi** olarak **SHA256**.

    i. Denetleme **kullanıcıadı zorunlu**.

    j.           **Tamam**’a tıklayın.

6. Yapılandırmak için **oturumu profili**, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure06.png)

    a. İçinde **adı** metin, oturum profil adı yazın.

    b. Üzerinde **istemci deneyimini** sekmesinde, aşağıdaki ekran görüntüsünde gösterildiği gibi değişiklikleri yapın.

    c. Üzerinde değişiklik yapmadan devam **Genel sekmesinde** aşağıda gösterildiği gibi tıklayın **Tamam**

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure07.png)

    d. Üzerinde **yayımlanan uygulamalar** sekmesinde, aşağıdaki ekran görüntüsünde gösterildiği gibi değişiklikleri yapın ve tıklayın **Tamam**.

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure08.png)

    e. Üzerinde **güvenlik** sekmesinde, aşağıdaki ekran görüntüsünde gösterildiği gibi değişiklikleri yapın ve tıklayın **Tamam**.

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure09.png)

7. Oturum güvenilirlik bağlantı noktasına bağlanan ICA bağlantı **2598** gösterildiği ekran görüntüsü aşağıda.

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure10.png)

8. Üzerinde **SAML** bölümünde, eklemek **sunucuları** aşağıdaki ekran görüntüsünde gösterildiği gibi.

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure11.png)

9. Üzerinde **SAML** bölümünde, eklemek **ilkeleri** aşağıdaki ekran görüntüsünde gösterildiği gibi.

     ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure12.png)

10. Üzerinde **genel ayarları** sayfasında, Git **istemcisiz erişim** bölümü.

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure13.png)

11. Üzerinde **yapılandırma** sekmesinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure14.png)

    a. Seçin **etki alanlarına izin**.

    b. İçinde **etki alanı adı** metin etki alanını seçin.

    c. **Tamam**'ı tıklatın.

12. Olun **mağaza** ayarlarını **Web siteleri için alıcı** aşağıdaki ekran görüntüsünde gösterildiği gibi:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure15.png)

13. Üzerinde **kimlik doğrulama yöntemleri yönetme - Corp** açılır penceresinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure16.png)

    a. Seçin **kullanıcı adı ve parola**.

    b. Seçin **NetScaler gateway'den doğrudan**.

    c.           **Tamam**'ı tıklatın.

14. Üzerinde **güvenilen etki alanlarını yapılandırma** açılır penceresinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure17.png)

    a. Seçin **güvenilen etki alanı yalnızca**.

    b. Tıklayarak **Ekle** etki alanınızda eklemek için **güvenilen etki alanı** metin.

    c. Varsayılan etki alanından seçin, **varsayılan etki alanı** listesi.

    d. Seçin **oturum açma sayfasını göster etki alanları listesinde**.

    e.           **Tamam**'ı tıklatın.

15. Üzerinde **NetScaler ağ geçitlerini Yönet** açılır penceresinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure18.png)

    a. Tıklayarak **Ekle** NetScaler Geçitlerinizi eklemek için **NetScaler ağ geçitleri** metin.

    b. **Kapat**’a tıklayın.

16. Üzerinde **mağaza genel ayarları** sekmesinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure19.png)

    a. İçinde **görünen adı** metin NetScaler ağ geçidi adınızı yazın.

    b. İçinde **NetScaler ağ geçidi URL'si** metin NetScaler ağ geçidi URL'nizi yazın.

    c. Seçin **kullanımı veya rol** olarak **kimlik doğrulaması ve HDX yönlendirme**.

    d. **Tamam** düğmesine tıklayın.

17. Üzerinde **mağaza güvenli bilet yetkilisi** sekmesinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure20.png)

    a. Tıklayarak **Ekle** eklemek için düğmeyi, **güvenli bilet yetkilisi URL'SİNİN** metin kutusuna.

    b. Seçin **etkinleştirme oturumu güvenilirlik**.

    c. **Tamam**'ı tıklatın.

18. Üzerinde **mağaza kimlik doğrulama ayarları** sekmesinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure21.png)

    a. Seçin, **sürüm**.

    b. Seçin **oturum açma türü** olarak **etki alanı**.

    c. Girin, **geri çağırma URL'si**.

    d.           **Tamam**'ı tıklatın.

19. Üzerinde **mağaza dağıtma Citrix alıcı** sekmesinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure22.png)

    a. Seçin **dağıtım seçeneği** olarak **yerel alıcı kullanılamıyorsa, HTML5 için kullanım alıcı**.

    b.           **Tamam**'ı tıklatın.

20. Üzerinde **yönetme işaretleri** açılır penceresinde, aşağıdaki adımları gerçekleştirin:

    ![Çoklu oturum açmayı yapılandırın](./media/citrix-netscaler-tutorial/configure23.png)

    a. Seçin **iç işaret** olarak **hizmet URL'sini kullanmak**.

    b. Tıklayın **Ekle** , URL'nin olarak eklemek için **dış işaretleri** metin.

    c.           **Tamam**'ı tıklatın.

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Britta Simon adlı Azure portalında bir test kullanıcısı oluşturmaktır.

1. Azure portalında, sol bölmede seçin **Azure Active Directory**seçin **kullanıcılar**ve ardından **tüm kullanıcılar**.

    !["Kullanıcılar ve Gruplar" ve "Tüm kullanıcılar" bağlantıları](common/users.png)

2. Seçin **yeni kullanıcı** ekranın üstünde.

    ![Yeni kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı özellikleri, aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. İçinde **adı** alana **BrittaSimon**.
  
    b. İçinde **kullanıcı adı** alan türü **brittasimon@yourcompanydomain.extension**  
    Örneğin, BrittaSimon@contoso.com

    c. Seçin **Show parola** onay kutusunu işaretleyin ve ardından parola kutusunda görüntülenen değeri yazın.

    d.           **Oluştur**'a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Azure çoklu oturum açma kullanmak için Citrix NetScaler erişim vererek Britta Simon etkinleştirin.

1. Azure portalında **kurumsal uygulamalar**seçin **tüm uygulamaları**, ardından **Citrix NetScaler**.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **Citrix NetScaler**.

    ![Uygulamalar listesini Citrix NetScaler bağlantıdaki](common/all-applications.png)

3. Soldaki menüde **kullanıcılar ve gruplar**.

    !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

4. Tıklayın **Kullanıcı Ekle** düğmesine ve ardından **kullanıcılar ve gruplar** içinde **atama Ekle** iletişim.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. İçinde **kullanıcılar ve gruplar** iletişim kutusunda **Britta Simon** 'a tıklayın kullanıcı listesinde **seçin** ekranın alt kısmındaki düğmesi.

6. SAML onaylaması ardından içinde herhangi bir rolü değer bekleniyor durumunda **rolü Seç** 'a tıklayın listeden bir kullanıcı için uygun rolü Seç iletişim kutusu **seçin** ekranın alt kısmındaki düğmesi.

7. İçinde **atama Ekle** iletişim tıklatın **atama** düğmesi.

### <a name="create-citrix-netscaler-test-user"></a>Citrix NetScaler test kullanıcısı oluşturma

Bu bölümde, Britta Simon adlı bir kullanıcı Citrix NetScaler oluşturulur. Citrix NetScaler just-ın-time kullanıcı hazırlama, varsayılan olarak etkin olduğu destekler. Bu bölümde, hiçbir eylem öğesini yoktur. Citrix NetScaler bir kullanıcı zaten mevcut değilse yeni bir kimlik doğrulamasından sonra oluşturulur.

>[!NOTE]
>Bir kullanıcı el ile oluşturmanız gerekiyorsa, iletişime geçmeniz [Citrix NetScaler istemci Destek ekibine](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim paneli Citrix NetScaler kutucuğa tıkladığınızda, size otomatik olarak SSO'yu ayarlama Citrix NetScaler için'oturum açmanız. Erişim paneli hakkında daha fazla bilgi için bkz: [erişim Paneli'ne giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamaları Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

