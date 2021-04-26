---
title: 'Öğretici: Alibaba Cloud Service ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory (rol tabanlı SSO) | Microsoft Docs'
description: Azure Active Directory ve Alibaba bulut hizmeti (rol tabanlı SSO) arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 6e4cb3c372b696b203d2441c74a6cafff175af47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736163"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Öğretici: Alibaba Cloud Service ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory (rol tabanlı SSO)

Bu öğreticide, Alibaba bulut hizmeti 'ni (rol tabanlı SSO) Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Alibaba bulut hizmetini (rol tabanlı SSO) Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Alibaba bulut hizmeti 'ne (rol tabanlı SSO) erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Alibaba bulut hizmeti 'ne (rol tabanlı SSO) otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Alibaba bulut hizmeti (rol tabanlı SSO) çoklu oturum açma (SSO) etkin abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Alibaba bulut hizmeti (rol tabanlı SSO) **IDP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Galeriden Alibaba bulut hizmeti (rol tabanlı SSO) ekleme

Alibaba bulut hizmeti 'nin (rol tabanlı SSO) tümleştirmesini Azure AD 'ye yapılandırmak için, galerinizden yönetilen SaaS uygulamaları listenize Alibaba bulut hizmeti (rol tabanlı SSO) eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Alibaba Cloud Service (rol tabanlı SSO)** yazın.
1. Sonuçlar panelinden **Alibaba Cloud Service (rol tabanlı SSO)** öğesini seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.
5. **Alibaba bulut hizmeti (rol tabanlı SSO)** sayfasında, sol taraftaki Gezinti bölmesindeki **Özellikler** ' e tıklayın ve **nesne kimliğini** kopyalayın ve daha sonra kullanmak üzere bilgisayarınıza kaydedin.

    ![Özellikler yapılandırması](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>Alibaba bulut hizmeti için Azure AD SSO 'yu yapılandırma ve test etme (rol tabanlı SSO)

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu Alibaba Cloud Service (rol tabanlı SSO) ile yapılandırın ve test edin. SSO 'nun çalışması için, Alibaba bulut hizmetindeki (rol tabanlı SSO) bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Alibaba bulut hizmeti (rol tabanlı SSO) ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Alibaba bulut hizmetinde tek Sign-On Role-Based yapılandırın](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** .
    1. Uygulama tarafında tek Sign-On ayarlarını yapılandırmak için **[Alibaba Cloud Service (rol tabanlı SSO) SSO 'Yu yapılandırın](#configure-alibaba-cloud-service-role-based-sso-sso)** .
    1. **[Alibaba bulut hizmeti (rol tabanlı SSO)](#create-alibaba-cloud-service-role-based-sso-test-user)** , Kullanıcı Azure AD gösterimine bağlı Alibaba bulut hizmeti 'Nde (rol tabanlı SSO) Britta Simon 'a sahip olmak için bir test kullanıcısı oluşturun.
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Alibaba bulut hizmeti (rol tabanlı SSO)** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, **hizmet sağlayıcısı meta verileri dosyanız** varsa, aşağıdaki adımları uygulayın:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.


    >[!NOTE]
    >1. Alibaba Cloud International sitesi için lütfen [Bu](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) bağlantıdan hizmet sağlayıcısı meta verilerini indirin.
    > 1. Alibaba Cloud Service (CN) sitesi için lütfen [Bu](https://signin.aliyun.com/saml-role/sp-metadata.xml) bağlantıdan hizmet sağlayıcısı meta verilerini indirin.

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra, **tanımlayıcı** ve **yanıt URL** değerleri Alibaba bulut HIZMETI (rol tabanlı SSO) bölüm metin kutusunda otomatik olarak doldurulur.

    > [!Note]
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak doldurulmazsa, değerleri gereksinimlerinize göre el ile girin.

1. Alibaba bulut hizmeti (rol tabanlı SSO), Azure AD 'de rollerin yapılandırılmasını gerektirir. Rol talebi önceden yapılandırılmıştır, bu sayede onu yapılandırmanız gerekmez, ancak yine de bu [makaleyi](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)kullanarak Azure AD 'de oluşturmanız gerekir.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Alibaba bulut hizmetini ayarlama (rol tabanlı SSO)** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Alibaba bulut hizmeti 'ne (rol tabanlı SSO) erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Alibaba Cloud Service (rol tabanlı SSO)** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** sekmesinde, Kullanıcı listesinden U2 ' i seçin ve **Seç**' e tıklayın. Ardından **ata**' ya tıklayın.

    ![Azure AD testi Kullanıcı1 ' i atama](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Atanan rol ve test Alibaba bulut hizmetini (rol tabanlı SSO) görüntüleyin.

    ![Azure AD testi kullanıcı2 'yi atama](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Kullanıcıyı (U2) atadıktan sonra, oluşturulan rol kullanıcıya otomatik olarak eklenir. Birden çok rol oluşturduysanız, gerektiği gibi kullanıcıya uygun rolü eklemeniz gerekir. Azure AD 'den birden çok Alibaba bulut hesabına rol tabanlı SSO uygulamak istiyorsanız, önceki adımları yineleyin.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Alibaba bulut hizmetinde Role-Based tek Sign-On yapılandırma

1. Account1 kullanarak Alibaba Cloud [RAM konsolunda](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) oturum açın.

2. Sol taraftaki Gezinti bölmesinde **SSO**' yı seçin.

3. **Rol tabanlı SSO** sekmesinde **IDP oluştur ' a** tıklayın.

4. Görüntülenecek sayfada `AAD` IDP adı alanına bir açıklama girin, daha önce indirdiğiniz Federasyon meta veri dosyasını karşıya  yüklemek için **karşıya yükle** ' ye tıklayın ve **Tamam**' a tıklayın.

5. IDP başarıyla oluşturulduktan sonra, **RAM rolü oluştur**' a tıklayın.

6. **RAM rolü adı** alanına yazın `AADrole` , `AAD` **IDP Seç** açılan listesinden öğesini seçin ve Tamam ' a tıklayın.

    >[!NOTE]
    >Gerektiğinde role izin verebilirsiniz. IDP ve buna karşılık gelen rolü oluşturduktan sonra, IDP 'nin ve sonraki kullanım için rolün ARNs kaydedilmesini öneririz. IDP bilgileri sayfasında ve rol bilgileri sayfasında ARNs elde edebilirsiniz.

7. Alibaba Cloud RAM rolünü (AADrole) Azure AD kullanıcısı (U2) ile ilişkilendirin: RAM rolünü Azure AD kullanıcısı ile ilişkilendirmek Için şu adımları izleyerek Azure AD 'de bir rol oluşturmanız gerekir:

    a. [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer)' nde oturum açın.

    b. Rol oluşturmaya yönelik gerekli izinleri almak için **izinleri değiştir** ' e tıklayın.

    ![Graph config1](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Listeden aşağıdaki izinleri seçin ve aşağıdaki şekilde gösterildiği gibi **Izinleri Değiştir**' e tıklayın.

    ![Graph Config2](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >İzinler verildikten sonra, Graph Explorer 'da yeniden oturum açın.

    d. Graph Explorer sayfasında, ilk açılan listeden **Al** ' ı ve ikinci açılan listeden **Beta** ' yı seçin. Ardından `https://graph.microsoft.com/beta/servicePrincipals` aşağı açılan listelerin yanındaki alana girip **Sorguyu Çalıştır**' a tıklayın.

    ![Graph Config3](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Birden çok dizin kullanıyorsanız, `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` sorgunun alanına yazabilirsiniz.

    e. **Yanıt önizleme** bölümünde, sonraki kullanım Için ' hizmet sorumlusu ' öğesinden approles özelliğini ayıklayın.

    ![Graph Config4](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Sorgunun alanına girerek appRoles özelliğini bulabilirsiniz `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` . ' Nin, `objectID` Azure AD **özellikleri** SAYFASıNDAN kopyaladığınız nesne kimliği olduğunu unutmayın.

    f. Graph Explorer 'a geri dönün, yöntemi **Al** **'a git 'Ten değiştirin,** **İstek gövdesi** bölümüne aşağıdaki içeriği yapıştırın ve **Sorguyu Çalıştır**' a tıklayın:
    ```
    { 
      "appRoles": [
        { 
          "allowedMemberTypes":[
            "User"
          ],
          "description": "msiam_access",
          "displayName": "msiam_access",
          "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
          "isEnabled": true,
          "origin": "Application",
          "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
      ]
    }
    ```
    > [!NOTE]
    > , `value` IDP 'nin ve RAM konsolunda oluşturduğunuz rolün ARNs sayısıdır. Burada, gerektiğinde birden çok rol ekleyebilirsiniz. Azure AD, bu rollerin değerini SAML yanıtında talep değeri olarak gönderir. Ancak, `msiam_access` Düzeltme Eki işlemi için bölümden sonra yalnızca yeni roller ekleyebilirsiniz. Oluşturma işlemini düzgünleştirmek için, kimlikleri gerçek zamanlı olarak oluşturmak üzere GUID Oluşturucu gibi bir KIMLIK Oluşturucu kullanmanızı öneririz.

    örneğin: ' Hizmet sorumlusu ' gerekli rolle düzeltme eki uygulandıktan sonra, öğreticinin **Azure AD test kullanıcısı atama** adımlarını Izleyerek rolü Azure AD kullanıcısına (U2) ekleyin.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Alibaba bulut hizmetini yapılandırma (rol tabanlı SSO) SSO

**Alibaba Cloud Service (rol tabanlı SSO)** tarafında çoklu oturum açmayı yapılandırmak için, Indirilen **Federasyon meta veri XML** 'Sini ve uygun kopyalanmış URL 'leri Azure Portal [bulut HIZMETI 'ne (rol tabanlı SSO) destek ekibine](https://www.aliyun.com/service/)göndermeniz gerekir. Bu ayar, SAML SSO bağlantısının her iki tarafında da düzgün bir şekilde ayarlanmasını sağlamak üzere ayarlanmıştır.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Alibaba bulut hizmeti oluşturma (rol tabanlı SSO) test kullanıcısı

Bu bölümde, Alibaba bulut hizmetinde (rol tabanlı SSO) Britta Simon adlı bir Kullanıcı oluşturacaksınız. Alibaba Cloud Service (rol tabanlı SSO) platformunda kullanıcıları eklemek için [Alibaba Cloud Service (rol tabanlı SSO) destek ekibi](https://www.aliyun.com/service/) ile çalışın. Çoklu oturum açma kullanılmadan önce kullanıcıların oluşturulması ve etkinleştirilmesi gerekir.

## <a name="test-sso"></a>Test SSO 'SU 

Yukarıdaki yapılandırma tamamlandıktan sonra, aşağıdaki adımları izleyerek Alibaba bulut hizmetini (rol tabanlı SSO) test edin:

1. Azure portal, **Alibaba bulut hizmeti (rol tabanlı SSO)** sayfasına gidin, **Çoklu oturum açma**' yı seçin ve **Test**' e tıklayın.

    ![Test config1](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. **Geçerli kullanıcı olarak oturum aç**'a tıklayın.

    ![Test Config2](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Hesap seçimi sayfasında, U2 ' yi seçin.

    ![Test Config3](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Rol tabanlı SSO 'nun başarılı olduğunu gösteren aşağıdaki sayfa görüntülenir.

    ![Test Config4](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>Sonraki adımlar

Alibaba bulut hizmetini (rol tabanlı SSO) yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).
