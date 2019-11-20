---
title: 'Öğretici: SAP Cloud Platform ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAP bulut platformu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89ea2c45e16dfeb63801f70fa4480c0d865a890f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160077"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Öğretici: SAP Cloud Platform ile tümleştirme Azure Active Directory

Bu öğreticide SAP bulut platformunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.
SAP Cloud Platform ile Azure AD arasında tümleştirme aşağıdaki avantajları sağlar:

* SAP bulut platformuna erişimi olan Azure AD 'de denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP bulut platformunda (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini SAP Cloud Platform ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* SAP Cloud Platform çoklu oturum açma etkin aboneliği

Bu Öğreticiyi tamamladıktan sonra, SAP bulut platformuna atadığınız Azure AD kullanıcıları, [erişim paneline giriş](../user-help/active-directory-saas-access-panel-introduction.md)kullanarak uygulamada tek oturum açabilirler.

>[!IMPORTANT]
>Çoklu oturum açmayı test etmek için kendi uygulamanızı dağıtmanız veya SAP Cloud Platform hesabınızdaki bir uygulamaya abone olmanız gerekir. Bu öğreticide, hesapta bir uygulama dağıtılır.
> 

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SAP bulut platformu **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Galeriden SAP bulutu platformu ekleme

SAP Cloud Platform 'un Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden SAP bulut platformunu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

**Galeriden SAP bulut platformu eklemek için aşağıdaki adımları uygulayın:**

1. **[Azure Portal](https://portal.azure.com)** sol gezinti panelinde **Azure Active Directory** simgesine tıklayın.

    ![Azure Active Directory düğmesi](common/select-azuread.png)

2. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar** seçeneğini belirleyin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Yeni uygulama eklemek için, iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesine tıklayın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusuna **SAP Cloud Platform**yazın, sonuç panelinden **SAP bulut platformu** ' nu seçin ve ardından **Ekle** düğmesine tıklayarak uygulamayı ekleyin.

     ![SAP bulut platformu sonuçlar listesinde](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon**adlı bir test kullanıcısına bağlı olarak SAP bulut platformuyla yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve SAP bulut platformunda ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

SAP Cloud Platform ile Azure AD çoklu oturum açmayı yapılandırmak ve test etmek için aşağıdaki yapı taşlarını gerçekleştirmeniz gerekir:

1. **[Azure AD çoklu oturum açma özelliğini yapılandırarak](#configure-azure-ad-single-sign-on)** kullanıcılarınızın bu özelliği kullanmasına olanak sağlayın.
2. **[SAP Cloud Platform çoklu oturum açmayı yapılandırma](#configure-sap-cloud-platform-single-sign-on)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
5. SAP Cloud **[Platform test kullanıcısı oluşturma](#create-sap-cloud-platform-test-user)** -SAP bulut platformunda kullanıcının Azure AD gösterimine bağlanan Britta Simon 'a sahip olmak için.
6. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[Çoklu oturum açmayı sınayın](#test-single-sign-on)** .

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açma özelliğini etkinleştirirsiniz.

Azure AD çoklu oturum açmayı SAP Cloud Platform ile yapılandırmak için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **SAP Cloud Platform** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

    ![Çoklu oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin.

    ![Çoklu oturum açma seçme modu](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![SAP Cloud Platform etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, kullanıcılarınız tarafından **SAP bulut platformu** uygulamanızda oturum açmak için kullanılan URL 'yi yazın. Bu, SAP bulut platformu uygulamanızda korunan bir kaynağın hesaba özgü URL 'sidir. URL Şu modele dayalıdır: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Bu, SAP bulut platformu uygulamanızda kullanıcının kimlik doğrulamasını gerektiren URL 'dir.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. **Tanımlayıcı** metin kutusunda, aşağıdaki desenlerden bırını kullanarak SAP bulut PLATFORMUNUZUN URL 'sini girin: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Bu değerler gerçek değildir. Bu değerleri gerçek oturum açma URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Oturum açma URL 'sini ve tanımlayıcıyı almak için [SAP Cloud Platform istemci destek ekibine](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) başvurun. Güven yönetimi bölümünden alabileceğiniz, öğreticide daha sonra açıklanan yanıt URL 'SI.
    > 
4. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>SAP Cloud Platform çoklu oturum açmayı yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, `https://account.<landscape host>.ondemand.com/cockpit`(örneğin: https://account.hanatrial.ondemand.com/cockpit) SAP Cloud Platform kokpiti üzerinde oturum açın.

2. **Güven** sekmesine tıklayın.
   
    ![Güvenmeyin](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Güven")

3. Güven Yönetimi bölümünde, **yerel hizmet sağlayıcısı**altında aşağıdaki adımları gerçekleştirin:

    ![Güven yönetimi](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Güven yönetimi")
   
    a. **Düzenle**’ye tıklayın.

    b. **Yapılandırma türü**olarak **özel**' i seçin.

    c. **Yerel sağlayıcı adı**olarak, varsayılan değeri bırakın. Bu değeri kopyalayın ve SAP bulut platformu için Azure AD yapılandırması içindeki **tanımlayıcı** alanına yapıştırın.

    d. **Imzalama anahtarı** ve **imza sertifikası** anahtar çifti oluşturmak için **anahtar çifti oluştur**' a tıklayın.

    e. **Asıl yayma**olarak **devre dışı**öğesini seçin.

    f. **Zorlamalı kimlik doğrulaması**olarak **devre dışı**seçeneğini belirleyin.

    g. **Kaydet** düğmesine tıklayın.

4. **Yerel hizmet sağlayıcısı** ayarlarını kaydettikten sonra, yanıt URL 'sini almak için aşağıdakileri gerçekleştirin:
   
    ![Meta verileri al](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Meta verileri al")

    a. **Meta veri al**' a tıklayarak SAP Cloud Platform meta veri dosyasını indirin.

    b. İndirilen SAP Cloud Platform meta veri XML dosyasını açın ve **NS3: AssertionConsumerService** etiketini bulun.
 
    c. **Location** özniteliğinin değerini kopyalayın ve ardından SAP bulut platformu IÇIN Azure AD yapılandırması ' nda **yanıt URL 'si** alanına yapıştırın.

5. **Güvenilen kimlik sağlayıcısı** sekmesine tıklayın ve ardından **güvenilen kimlik sağlayıcısı ekle**' ye tıklayın.
   
    ![Güven yönetimi](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Güven yönetimi")
   
    >[!NOTE]
    >Güvenilen kimlik sağlayıcılarının listesini yönetmek için, yerel hizmet sağlayıcısı bölümünde özel yapılandırma türünü seçmiş olmanız gerekir. Varsayılan yapılandırma türü için, SAP KIMLIĞI hizmetine düzenlenemeyen ve örtük bir güveniniz vardır. Hiçbiri için herhangi bir güven ayarı yoktur.
    > 
    > 

6. **Genel** sekmesine tıklayın ve ardından indirilen meta veri dosyasını karşıya yüklemek için **Araştır** ' a tıklayın.
    
    ![Güven yönetimi](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Güven yönetimi")
    
    >[!NOTE]
    >Meta veri dosyasını karşıya yükledikten sonra, **Çoklu oturum açma URL 'si**, **Çoklu oturum kapatma URL 'Si**ve **imza sertifikası** değerleri otomatik olarak doldurulur.
    > 
     
7. **Öznitelikler** sekmesine tıklayın.

8. **Öznitelikler** sekmesinde aşağıdaki adımı gerçekleştirin:
    
    ![Özelliklerine](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Öznitelikler") 

    a. **Onaylama tabanlı öznitelik Ekle**' ye tıklayın ve ardından aşağıdaki onaylama tabanlı öznitelikleri ekleyin:
       
    | Assertion özniteliği | Principal özniteliği |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |FirstName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Soyadı |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-posta |
   
     >[!NOTE]
     >Özniteliklerin yapılandırması, SCP 'deki uygulamaların nasıl geliştirildiğini, diğer bir deyişle, SAML yanıtında beklediği öznitelikleri ve bu özniteliğe hangi adın (Principal özniteliği) erişebileceğini bağlıdır.
     > 
    
    b. Ekran görüntüsündeki **Varsayılan özniteliği** yalnızca çizim amaçlıdır. Senaryonun çalışması için gerekli değildir.  
 
    c. Ekran görüntüsünde gösterilen **Principal özniteliği** için adlar ve değerler uygulamanın nasıl geliştirildiğine bağlıdır. Uygulamanız farklı eşlemeler gerektiriyor olabilir.

### <a name="assertion-based-groups"></a>Onaylama tabanlı gruplar

İsteğe bağlı bir adım olarak, Azure Active Directory kimlik sağlayıcınız için onaylama tabanlı grupları yapılandırabilirsiniz.

SAP bulut platformunda grupların kullanılması, bir veya daha fazla kullanıcıyı SAP bulut platformu uygulamalarınızdaki bir veya daha fazla rol için dinamik olarak atamanızı, SAML 2,0 onaylama içindeki özniteliklerin değerleriyle tespit etmenizi sağlar. 

Örneğin, onaylama "*sözleşme = geçici*" özniteliğini içeriyorsa, etkilenen tüm kullanıcıların "*geçici*" grubuna eklenmesini isteyebilirsiniz. "*Geçici*" Grup, SAP bulut platformu hesabınızda dağıtılan bir veya daha fazla uygulamadan bir veya daha fazla rol içerebilir.
 
SAP bulut platformu hesabınızdaki bir veya daha fazla uygulama rolüne aynı anda birçok kullanıcı atamak istediğinizde onaylama tabanlı grupları kullanın. Belirli rollere yalnızca tek veya az sayıda kullanıcı atamak istiyorsanız, bunları SAP bulut platformu kokpitinin "**yetkilendirmeler**" sekmesinde doğrudan atamanız önerilir.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portal Britta Simon adlı bir test kullanıcısı oluşturmaktır.

1. Azure portal, sol bölmedeki **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.

    !["Kullanıcılar ve gruplar" ve "tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.

    ![Yeni Kullanıcı düğmesi](common/new-user.png)

3. Kullanıcı Özellikleri ' nde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanına **Brittasıon**girin.
  
    b. **Kullanıcı adı** alanında **brittasıon\@yourşirketnotlarıetki alanı. Extension** yazın  
    Örneğin, BrittaSimon@contoso.com

    c. **Parolayı göster** onay kutusunu seçin ve ardından parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur**’a tıklayın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, SAP bulut platformuna erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştirin.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **SAP bulut platformu**' nu seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **SAP Cloud Platform**yazın ve seçin.

    ![Uygulamalar listesindeki SAP Cloud Platform bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle** düğmesine tıklayın, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** Iletişim kutusunda kullanıcılar listesinde **Britta Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

6. SAML onaylama işlemi içinde herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, listeden Kullanıcı için uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-sap-cloud-platform-test-user"></a>SAP bulut platformu test kullanıcısı oluşturma

Azure AD kullanıcılarının SAP bulut platformunda oturum açmasını sağlamak için, SAP bulut platformunda roller atamanız gerekir.

**Bir kullanıcıya bir rol atamak için aşağıdaki adımları uygulayın:**

1. **SAP Cloud Platform** kokpiti ' nda oturum açın.

2. Şunları yapın:
   
    ![Yetkilendirmeleri](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Yetkilendirmeler")
   
    a. **Yetkilendirme**' ye tıklayın.

    b. **Kullanıcılar** sekmesine tıklayın.

    c. **Kullanıcı** metin kutusuna kullanıcının e-posta adresini yazın.

    d. Kullanıcıyı bir role atamak için **ata** ' ya tıklayın.

    e. **Kaydet** düğmesine tıklayın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde SAP Cloud Platform kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız SAP bulut platformunda otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

