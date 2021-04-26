---
title: 'Öğretici: SAP Cloud Platform ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve SAP bulut platformu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964057"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Öğretici: SAP Cloud Platform ile tümleştirme Azure Active Directory

Bu öğreticide SAP bulut platformunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. SAP Cloud Platform 'u Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de SAP bulut platformuna erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla SAP bulut platformunda otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini SAP Cloud Platform ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü edinebilirsiniz
* SAP Cloud Platform çoklu oturum açma etkin aboneliği

Bu Öğreticiyi tamamladıktan sonra, SAP bulut platformuna atadığınız Azure AD kullanıcıları, [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md)kullanarak uygulamada tek oturum açabilirler.

>[!IMPORTANT]
>Çoklu oturum açmayı test etmek için kendi uygulamanızı dağıtmanız veya SAP Cloud Platform hesabınızdaki bir uygulamaya abone olmanız gerekir. Bu öğreticide, hesapta bir uygulama dağıtılır.
> 

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SAP bulut platformu **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Galeriden SAP bulutu platformu ekleme

SAP Cloud Platform 'un Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden SAP bulut platformunu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SAP Cloud Platform** yazın.
1. Sonuçlar panelinden **SAP bulut platformu** ' nu seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>SAP bulut platformu için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test KULLANıCıSı kullanarak SAP Cloud Platform Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, SAP bulut platformunda bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu SAP bulut platformuyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. **[SAP Cloud Platform SSO 'Yu yapılandırma](#configure-sap-cloud-platform-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    1. SAP Cloud **[Platform test kullanıcısı oluşturma](#create-sap-cloud-platform-test-user)** -SAP bulut platformunda kullanıcının Azure AD gösterimine bağlanan Britta Simon 'a sahip olmak için.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **SAP Cloud Platform** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    ![SAP Cloud Platform etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/sp-identifier-reply.png)

    a. **Oturum açma URL 'si** metin kutusunda, kullanıcılarınız tarafından **SAP bulut platformu** uygulamanızda oturum açmak için kullanılan URL 'yi yazın. Bu, SAP bulut platformu uygulamanızda korunan bir kaynağın hesaba özgü URL 'sidir. URL aşağıdaki modele dayalıdır: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Bu, SAP bulut platformu uygulamanızda kullanıcının kimlik doğrulamasını gerektiren URL 'dir.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. **Tanımlayıcı** metin kutusunda, aşağıdaki desenlerden bırını kullanarak SAP bulut PLATFORMUNUZUN URL 'sini girin: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. **Yanıt URL** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI, tanımlayıcı ve yanıt URL 'siyle güncelleştirin. Sign-On URL ve tanımlayıcı almak için [SAP bulut platformu istemci destek ekibine](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) başvurun. Güven yönetimi bölümünden alabileceğiniz, öğreticide daha sonra açıklanan yanıt URL 'SI.
    > 
4. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imza sertifikası** bölümünde, **Federasyon meta veri XML** 'sini gereksiniminize göre belirtilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

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

Bu bölümde, SAP bulut platformu erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **SAP Cloud Platform**' u seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-sap-cloud-platform-sso"></a>SAP Cloud Platform SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, SAP Cloud Platform kokpiti üzerinde `https://account.<landscape host>.ondemand.com/cockpit` (örneğin:) oturum açın https://account.hanatrial.ondemand.com/cockpit) .

2. **Güven** sekmesine tıklayın.
   
    ![Güven](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Güven")

3. Güven Yönetimi bölümünde, **yerel hizmet sağlayıcısı** altında aşağıdaki adımları gerçekleştirin:

    !["Yerel hizmet sağlayıcısı" sekmesi seçili ve tüm metin kutularının vurgulandığı "güven yönetimi" bölümünü gösteren ekran görüntüsü.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Güven yönetimi")
   
    a. **Düzenle**’ye tıklayın.

    b. **Yapılandırma türü** olarak **özel**' i seçin.

    c. **Yerel sağlayıcı adı** olarak, varsayılan değeri bırakın. Bu değeri kopyalayın ve SAP bulut platformu için Azure AD yapılandırması içindeki **tanımlayıcı** alanına yapıştırın.

    d. **Imzalama anahtarı** ve **imza sertifikası** anahtar çifti oluşturmak için **anahtar çifti oluştur**' a tıklayın.

    e. **Asıl yayma** olarak **devre dışı** öğesini seçin.

    f. **Zorlamalı kimlik doğrulaması** olarak **devre dışı** seçeneğini belirleyin.

    örneğin: **Kaydet**’e tıklayın.

4. **Yerel hizmet sağlayıcısı** ayarlarını kaydettikten sonra, yanıt URL 'sini almak için aşağıdakileri gerçekleştirin:
   
    ![Meta verileri al](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Meta verileri al")

    a. **Meta veri al**' a tıklayarak SAP Cloud Platform meta veri dosyasını indirin.

    b. İndirilen SAP Cloud Platform meta veri XML dosyasını açın ve **NS3: AssertionConsumerService** etiketini bulun.
 
    c. **Location** özniteliğinin değerini kopyalayın ve ardından SAP bulut platformu IÇIN Azure AD yapılandırması ' nda **yanıt URL 'si** alanına yapıştırın.

5. **Güvenilen kimlik sağlayıcısı** sekmesine tıklayın ve ardından **güvenilen kimlik sağlayıcısı ekle**' ye tıklayın.
   
    !["Güvenilir kimlik sağlayıcısı" sekmesi seçili "güven yönetimi" sayfasını gösteren ekran görüntüsü.](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Güven yönetimi")
   
    >[!NOTE]
    >Güvenilen kimlik sağlayıcılarının listesini yönetmek için, yerel hizmet sağlayıcısı bölümünde özel yapılandırma türünü seçmiş olmanız gerekir. Varsayılan yapılandırma türü için, SAP KIMLIĞI hizmetine düzenlenemeyen ve örtük bir güveniniz vardır. Hiçbiri için herhangi bir güven ayarı yoktur.
    > 
    > 

6. **Genel** sekmesine tıklayın ve ardından indirilen meta veri dosyasını karşıya yüklemek için **Araştır** ' a tıklayın.
    
    ![Güven yönetimi](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Güven yönetimi")
    
    >[!NOTE]
    >Meta veri dosyasını karşıya yükledikten sonra, **Çoklu oturum açma URL 'si**, **Çoklu oturum kapatma URL 'Si** ve **imza sertifikası** değerleri otomatik olarak doldurulur.
    > 
     
7. **Öznitelikler** sekmesine tıklayın.

8. **Öznitelikler** sekmesinde aşağıdaki adımı gerçekleştirin:
    
    ![Öznitelikler](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Öznitelikler") 

    a. **Assertion-Based öznitelik Ekle**' ye tıklayın ve ardından aşağıdaki onaylama tabanlı öznitelikleri ekleyin:
       
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

### <a name="create-sap-cloud-platform-test-user"></a>SAP bulut platformu test kullanıcısı oluşturma

Azure AD kullanıcılarının SAP bulut platformunda oturum açmasını sağlamak için, SAP bulut platformunda roller atamanız gerekir.

**Bir kullanıcıya bir rol atamak için aşağıdaki adımları uygulayın:**

1. **SAP Cloud Platform** kokpiti ' nda oturum açın.

2. Şunları yapın:
   
    ![Yetkilendirmeler](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Yetkilendirmeler")
   
    a. **Yetkilendirme**' ye tıklayın.

    b. **Kullanıcılar** sekmesine tıklayın.

    c. **Kullanıcı** metin kutusuna kullanıcının e-posta adresini yazın.

    d. Kullanıcıyı bir role atamak için **ata** ' ya tıklayın.

    e. **Kaydet**’e tıklayın.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz SAP bulut platformu oturum açma URL 'sine yeniden yönlendirilir. 

* SAP Cloud Platform oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki SAP Cloud Platform kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız SAP bulut platformunda otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

SAP bulut platformunu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).