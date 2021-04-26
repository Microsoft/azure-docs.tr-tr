---
title: 'Öğretici: Costpoint ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile Costpoint arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 781d1509b69ecb09189772f4a31d4872201d0b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652938"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Öğretici: Costpoint 'i Azure Active Directory tümleştirin

Bu öğreticide, Costpoint 'i Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Costpoint 'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Costpoint 'e erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla maliyette otomatik olarak oturum açmalarına olanak sağlar.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Costpoint çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz. 

* Costpoint **SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

## <a name="generate-costpoint-metadata"></a>Costpoint meta verileri oluştur

Costpoint SAML SSO yapılandırması, **DeltekCostpoint711Security.pdf** kılavuzunda açıklanmıştır. Bu kılavuzu, deltek costpoint destek sitesinden indirin ve **SAML çoklu oturum açma kurulumu**'  >  **nu, maliyetli ve Microsoft Azure bölümünde SAML çoklu oturum açmayı yapılandırma** bölümüne başvurun. Yönergeleri izleyin ve bir **Costpoint SP Federasyon meta VERI XML** dosyası oluşturun. 

!["Web Logic-Security" sekmesi seçiliyken "ürün yapılandırma yardımcı programı" nı gösteren ekran görüntüsü.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Galeriden Costpoint ekleyin

Costpoint 'in Azure AD ile tümleştirilmesini yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Costpoint eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **costpoint** yazın.
1. Sonuçlar panelinden **Costpoint** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Costpoint için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, costpoint Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve Costpoint ile ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Costpoint ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Costpoint SSO 'Yu yapılandırma](#configure-costpoint-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcı Azure AD gösterimine bağlı olan Costpoint 'te B. Simon 'a karşılık gelen, costpoint **[test kullanıcısı oluşturun](#create-costpoint-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için şu adımları izleyin:

1. **Costpoint** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin.

1. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa, şu adımları izleyin:

   > [!NOTE]
   > Hizmet sağlayıcı meta veri dosyasını, [Costpoint meta verilerini oluşturma](#generate-costpoint-metadata)bölümünde alırsınız. Dosya kullanımı öğreticide daha sonra açıklanmaktadır.
 
   1. **Meta veri dosyasını karşıya yükle** düğmesini seçin, ardından daha önce costpoint tarafından oluşturulan **Costpoint SP Federasyon meta veri XML** dosyasını seçin ve ardından dosyayı karşıya yüklemek için **Ekle** düğmesini seçin.

      ![Meta veri dosyasını karşıya yükle](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Meta veri dosyası başarıyla karşıya yüklendiğinde, **tanımlayıcı** ve **yanıt URL 'Si** değerleri, costpoint bölümünde otomatik olarak doldurulur.

      > [!NOTE]
      > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak yoksa, gereksinimlerinize göre değerleri el ile girin. **Tanımlayıcının (VARLıK kimliği)** ve **yanıt URL 'Sinin (onaylama tüketici hizmeti URL 'si)** doğru ayarlandığını ve **ACS URL 'sinin** **/loginservlet.exe** ile biten geçerli bir costpoint URL olduğunu doğrulayın.

   1. **Ek URL 'Ler ayarla**' yı seçin. **Geçiş durumu** için aşağıdaki kalıbı kullanarak bir değer girin: `system=[your system]` (örneğin, **System = deltekcp**).

1. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL** 'sini kopyalamak ve Not defteri 'ne kaydetmek için **Kopyala** simgesini seçin.

   ![SAML Imzalama sertifikası](common/copy-metadataurl.png)

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

Bu bölümde, Costpoint 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Costpoint**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-costpoint-sso"></a>Costpoint SSO 'yu yapılandırma

1. Costpoint yapılandırma yardımcı programına dönün. **IDP Federasyon meta VERILERI XML** metin kutusunda, *uygulama Federasyon meta verileri URL 'si* dosyasının içeriğini yapıştırın. 

   ![Costpoint yapılandırma yardımcı programı](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. **DeltekCostpoint711Security.pdf** kılavuzundaki yönergeleri Izleyerek COSTPOINT SAML kurulumunu tamamlayın.

### <a name="create-costpoint-test-user"></a>Costpoint test kullanıcısı oluştur

Bu bölümde, Costpoint 'te bir Kullanıcı oluşturacaksınız. Kullanıcı KIMLIĞININ **b. Simon** ve Kullanıcı adının **b. Simon** olduğunu varsayalım. Kullanıcı costpoint platformunda eklemek için [Costpoint istemci destek ekibi](https://www.deltek.com/about/contact-us) ile çalışın. Kullanıcının çoklu oturum açmayı kullanabilmesi için önce oluşturulması ve etkinleştirilmesi gerekir.

Kullanıcı oluşturulduktan sonra, kullanıcının **kimlik doğrulama yöntemi** seçimi **Active Directory** olmalıdır; **SAML çoklu oturum açma** onay kutusu seçilmelidir ve Azure ACTIVE DIRECTORY Kullanıcı adının **Active Directory veya sertifika kimliği** olması gerekir (aşağıdaki ekran görüntüsünde gösterilen).

![Costpoint kullanıcısı](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Costpoint oturum açma URL 'sine yeniden yönlendirilir.  

* Costpoint oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız costpoint 'te otomatik olarak oturum açmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içinde Costpoint kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Costpoint 'te otomatik olarak oturum açmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Costpoint 'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).