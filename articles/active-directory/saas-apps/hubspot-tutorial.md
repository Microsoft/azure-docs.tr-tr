---
title: 'Öğretici: HubSpot ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ile HubSpot arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 91dfdcef01a121c8282b8fad2e75f67989b75dc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733275"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Öğretici: HubSpot ile tümleştirme Azure Active Directory

Bu öğreticide, Hubu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Hubleke 'yi Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de, Hubsize erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla hub 'a otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini HubSpot ile yapılandırmak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Azure AD aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Çoklu oturum açma özelliği etkinleştirilmiş bir HubSpot aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edin ve Hubu Azure AD ile tümleştirin.

HubSpot aşağıdaki özellikleri destekler:

* **SP tarafından başlatılan çoklu oturum açma**
* **IDP-başlatılan çoklu oturum açma**

## <a name="adding-hubspot-from-the-gallery"></a>Galeriden HubSpot ekleme

HubSpot 'un Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize HubSpot eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **HubSpot** yazın.
1. Sonuçlar panelinden **HubSpot** ' yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>HubSpot için Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, Azure AD çoklu oturum açmayı, **Britta Simon** adlı bir test kullanıcısına göre HubSpot ile yapılandırıp test edersiniz. Çoklu oturum açma için, hub 'da bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açmayı hub spot ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını tamamlamalısınız:

| Görev | Açıklama |
| --- | --- |
| **[Azure AD çoklu oturum açmayı yapılandırma](#configure-azure-ad-single-sign-on)** | Kullanıcılarınızın bu özelliği kullanmasına olanak sağlar. |
| **[HubSpot çoklu oturum açmayı yapılandırma](#configure-hubspot-single-sign-on)** | Uygulamadaki çoklu oturum açma ayarlarını yapılandırır. |
| **[Azure AD test kullanıcısı oluşturma](#create-an-azure-ad-test-user)** | Britta Simon adlı Kullanıcı için Azure AD çoklu oturum açmayı sınar. |
| **[Azure AD test kullanıcısını atama](#assign-the-azure-ad-test-user)** | Azure AD çoklu oturum açma kullanımı için Britta Simon 'U sağlar. |
| **[HubSpot test kullanıcısı oluşturma](#create-a-hubspot-test-user)** | Kullanıcının Azure AD gösterimine bağlı olan hubo 'da Britta Simon 'ın bir karşılığı oluşturur. |
| **[Çoklu oturum açma testi](#test-single-sign-on)** | Yapılandırmanın çalıştığını doğrular. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

1. Azure portal, **HubSpot** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölmesinde, *IDP tarafından başlatılan modu* yapılandırmak için aşağıdaki adımları izleyin:

    1. **Tanımlayıcı** kutusuna şu düzene sahıp bir URL girin: https: \/ /api.HubSpot.com/login-api/v1/SAML/Login?portalId = \<CUSTOMER ID\> .

    1. **Yanıt URL 'si** kutusuna şu düzene sahıp bir URL girin: https: \/ /api.HubSpot.com/login-api/v1/SAML/ACS?portalId = \<CUSTOMER ID\> .

    ![HubSpot etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/idp-intiated.png)

    > [!NOTE]
    > URL 'Leri biçimlendirmek için, Azure portal **temel SAML yapılandırması** bölmesinde gösterilen desenlere de başvurabilirsiniz.

1. Uygulamayı *SP tarafından başlatılan* modda yapılandırmak için:

    1. **Ek URL 'Ler ayarla**' yı seçin.

    1. **Oturum açma URL 'si** kutusuna **https: \/ /app.HubSpot.com/login** girin.

    ![Ek URL ayarla seçeneği](common/metadata-upload-additional-signon.png)

1. **SAML Ile tek Sign-On ayarla** bölmesinde, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** yanındaki **İndir** ' i seçin. Gereksinimlerinize göre bir indirme seçeneği belirleyin. Sertifikayı bilgisayarınıza kaydedin.

    ![Sertifika (base64) indirme seçeneği](common/certificatebase64.png)

1. **Hub kümesi ayarla** bölümünde, gereksinimlerinize göre aşağıdaki URL 'leri kopyalayın:

    * Oturum Açma URL’si
    * Azure AD tanımlayıcısı
    * Oturum kapatma URL 'SI

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot çoklu oturum açmayı yapılandırma

1. Tarayıcınızda yeni bir sekme açın ve HubSpot yönetici hesabınızda oturum açın.

1. Sayfanın sağ üst köşesindeki **Ayarlar** simgesini seçin.

    ![Hubworkıdaki ayarlar simgesi](./media/hubspot-tutorial/config1.png)

1. **Hesap Varsayılanları**' nı seçin.

    ![HubSpot 'daki hesap Varsayılanları seçeneği](./media/hubspot-tutorial/config2.png)

1. **Güvenlik** bölümüne ilerleyin ve sonra **Ayarla**' yı seçin.

    ![HubSpot 'da ayarla seçeneği](./media/hubspot-tutorial/config3.png)

1. **Çoklu oturum açma ayarla** bölümünde aşağıdaki adımları uygulayın:

    1. **Hedef kitle URl 'si (hizmet sağlayıcısı VARLıK kimliği)** kutusunda, değeri kopyalamak için **Kopyala** ' yı seçin. Azure portal, **temel SAML yapılandırması** bölmesinde, değeri **tanımlayıcı** kutusuna yapıştırın.

    1. **Oturum açma URl 'si, ACS, alıcı veya yeniden yönlendirme** kutusunda, değeri kopyalamak için **Kopyala** ' yı seçin. Azure portal, **temel SAML yapılandırması** bölmesinde, **yanıt URL 'si** kutusuna değeri yapıştırın.

    1. Hubworkıda, **kimlik sağlayıcısı tanımlayıcısı veya veren URL** kutusunda, Azure Portal KOPYALADıĞıNıZ **Azure AD tanımlayıcısı** için değeri yapıştırın.

    1. Hubworkıda, **kimlik sağlayıcısı tek Sign-On URL** kutusunda, Azure Portal kopyaladığınız **oturum açma URL 'si** için değeri yapıştırın.

    1. Windows Not defteri 'nde indirdiğiniz sertifika (base64) dosyasını açın. Dosyanın içeriğini seçin ve kopyalayın. Ardından, Hubin ' de **X. 509.440 sertifika** kutusuna yapıştırın.

    1. **Doğrula** seçeneğini belirleyin.

        ![Hubın içinde çoklu oturum açma ayarlama bölümü](./media/hubspot-tutorial/config4.png)

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

Bu bölümde, hub 'a erişim izni vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **HubSpot**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="create-a-hubspot-test-user"></a>HubSpot test kullanıcısı oluşturma

Azure AD 'nin bir kullanıcının HubG 'de oturum açmasını sağlamak için, kullanıcının hubın içinde sağlanması gerekir. HubSpot 'da, sağlama el ile gerçekleştirilen bir görevdir.

Hubın içinde bir kullanıcı hesabı sağlamak için:

1. HubSpot şirket sitenizde yönetici olarak oturum açın.

1. Sayfanın sağ üst köşesindeki **Ayarlar** simgesini seçin.

    ![Hubworkıdaki ayarlar simgesi](./media/hubspot-tutorial/config1.png)

1. **Takımlar & kullanıcılar**' ı seçin.

    ![Hubın içindeki Kullanıcı & takımları seçeneği](./media/hubspot-tutorial/user1.png)

1. **Kullanıcı oluştur**' u seçin.

    ![HubSpot 'da kullanıcı oluştur seçeneği](./media/hubspot-tutorial/user2.png)

1. **E-posta addess 'Leri Ekle** kutusunda, kullanıcının e-posta adresini brittasıon contoso.com biçiminde girin \@ ve ardından **İleri**' yi seçin.

    ![Hubın kullanıcıları Oluştur bölümünde e-posta adresi ekle (es) kutusu](./media/hubspot-tutorial/user3.png)

1. **Kullanıcıları oluştur** bölümünde her bir sekmeyi seçin. Her sekmede, Kullanıcı için ilgili seçenekleri ve izinleri ayarlayın. Ardından **İleri**' yi seçin.

    ![Hubın kullanıcı oluştur bölümündeki sekmeler](./media/hubspot-tutorial/user4.png)

1. Daveti kullanıcıya göndermek için **Gönder**' i seçin.

    ![HubSpot 'daki gönder seçeneği](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Kullanıcı daveti kabul ettikten sonra Kullanıcı etkinleştirilir.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz HubG oturum açma URL 'sine yeniden yönlendirilir.  

* Hub noktası oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Hubu noktasında otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içinde HubSpot kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Hubz noktasında otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Hubma 'yı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad).