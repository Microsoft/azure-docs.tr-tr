---
title: 'Öğretici Azure Active Directory: Cisco WebEx ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Cisco WebEx arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592532"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Öğretici: Cisco WebEx ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide Cisco WebEx Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Cisco WebEx 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Cisco WebEx erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Cisco WebEx 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Cisco WebEx çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Cisco WebEx, **SP** tarafından başlatılan SSO 'yu destekler.
* Cisco WebEx [**Otomatik Kullanıcı sağlamayı**](./cisco-webex-provisioning-tutorial.md)destekler.

## <a name="adding-cisco-webex-from-the-gallery"></a>Galeriden Cisco WebEx ekleme

Cisco WebEx 'ın Azure AD 'ye tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Cisco WebEx eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Cisco WebEx** yazın.
1. Sonuçlar panelinden **Cisco WebEx** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Cisco WebEx için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Cisco WebEx Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için Cisco WebEx içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Cisco WebEx ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
2. Uygulama tarafında SSO ayarlarını yapılandırmak için **[Cisco WebEx 'ı yapılandırın](#configure-cisco-webex)** .
    1. Cisco WebEx **[Test kullanıcısını](#create-cisco-webex-test-user)** , kullanıcının Azure AD gösterimine bağlı olan Cisco WebEx 'de B. Simon 'a sahip olacak şekilde oluşturun.
3. Yapılandırmanın çalışıp çalışmadığını doğrulamak için **[test SSO 'su](#test-sso)** .

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Cisco WebEx** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde, Indirilen **hizmet sağlayıcısı meta veri** dosyasını karşıya yükleyin ve aşağıdaki adımları gerçekleştirerek uygulamayı yapılandırın:

    >[!Note]
    >Hizmet sağlayıcı meta veri dosyasını, Öğreticinin ilerleyen kısımlarında açıklanan **Cisco WebEx 'Yi yapılandırma** bölümünde bulabilirsiniz. 

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    b. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.

    c. Hizmet sağlayıcı meta veri dosyasını karşıya yükleme işleminin başarıyla tamamlanmasından sonra **tanımlayıcı** ve **yanıt URL** değerleri **temel SAML yapılandırması** bölümünde otomatik olarak doldurulur:

    **Oturum açma URL 'si** metin kutusunda, SP meta veri dosyasını karşıya yüklemeye göre otomatik doldurulmuş **yanıt URL 'si** değerini yapıştırın.

1. Cisco WebEx uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/default-attributes.png)

1. Cisco WebEx uygulaması, yukarıdakine ek olarak aşağıda gösterilen SAML yanıtına daha fazla öznitelik geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre bunları gözden geçirebilirsiniz.
  
    | Name |  Kaynak özniteliği|
    | ---------------|--------- |
    | 'sini | User. UserPrincipalName |

    > [!NOTE]
    >  Kaynak öznitelik değeri varsayılan olarak userpricipalname ile eşleştirilir. Bu, WebEx içindeki ayara göre User. Mail veya User. onpremiseuserprincipalname veya diğer herhangi bir değere değiştirilebilir.


1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

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

Bu bölümde, Cisco WebEx erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Cisco WebEx** öğesini seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-cisco-webex"></a>Cisco WebEx yapılandırma

1. Yönetici kimlik bilgilerinizle Cisco WebEx hizmetinde oturum açın.

1. **Kuruluş ayarları** ' nı seçin ve **kimlik doğrulaması** bölümünde **Değiştir**' i tıklatın.

    ![Ekran görüntüsü, Değiştir ' i seçebileceğiniz kimlik doğrulama ayarlarını gösterir.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. **3. taraf kimlik sağlayıcısını tümleştirin ' ı seçin. (Gelişmiş)** ve **İleri**' ye tıklayın.

    ![Ekran görüntüsünde, üçüncü taraf kimlik sağlayıcısını tümleştirme gösterilmektedir.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. **Hizmet sağlayıcısı meta veri dosyasını** Indirmek Için **meta veri dosyasını indir** ' e tıklayın ve bilgisayarınıza kaydedin, **İleri**' ye tıklayın.

    ![Ekran görüntüsü hizmet sağlayıcısı meta veri dosyasını gösterir.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Azure AD meta veri dosyasını bulup karşıya yüklemek için **dosya tarayıcısı** seçeneğine tıklayın. Ardından, **meta verilerde (daha güvenli) bir sertifika yetkilisi tarafından imzalanmış sertifika iste** ' yi seçin ve **İleri**' ye tıklayın.

    ![Ekran görüntüsü g/ç meta verilerini Içeri aktar sayfasını gösterir.](./media/cisco-spark-tutorial/idp-metadata.png)

1. **Test SSO bağlantısı**' nı seçin ve yeni bir tarayıcı sekmesi açıldığında, oturum açarak Azure AD ile kimlik doğrulaması yapın.

1. **Cisco Cloud Işbirliği yönetimi** tarayıcı sekmesine dönün. Test başarılı olduysa, **Bu test başarılı oldu öğesini seçin. Tek Sign-On seçeneğini etkinleştirip** **İleri**' ye tıklayın.

1. **Kaydet**’e tıklayın.

> [!NOTE]
> Cisco WebEx 'in nasıl yapılandırılacağı hakkında daha fazla bilgi edinmek için lütfen [Bu](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) sayfaya bakın.

### <a name="create-cisco-webex-test-user"></a>Cisco WebEx test kullanıcısı oluşturma

Bu bölümde, Cisco WebEx içinde B. Simon adlı bir Kullanıcı oluşturulur. Bu uygulama, iş kurallarınız temelinde otomatik sağlama ve sağlamayı kaldırma sağlayan otomatik Kullanıcı sağlamayı destekler.  Microsoft, mümkün olduğunda otomatik sağlama kullanılmasını önerir. Bkz. [Cisco WebEx](./cisco-webex-provisioning-tutorial.md)için otomatik sağlamayı etkinleştirme.

El ile bir kullanıcı oluşturmanız gerekiyorsa aşağıdaki adımları gerçekleştirin:

1. Yönetici kimlik bilgilerinizle Cisco WebEx hizmetinde oturum açın.

2. **Kullanıcılar** ' a ve ardından **Kullanıcıları Yönet**' e tıklayın.
   
    ![Ekran görüntüsü, kullanıcıları yönetebileceğiniz kullanıcılar sayfasını gösterir.](./media/cisco-spark-tutorial/user-1.png) 

3. **Kullanıcıları Yönet** penceresinde, **kullanıcıları el Ile Ekle veya Değiştir**' i seçin.

    ![Ekran görüntüsü, kullanıcıları yönetebileceğiniz ve kullanıcıları el Ile Ekle veya Değiştir ' i seçebileceğiniz kullanıcılar sayfasını gösterir.](./media/cisco-spark-tutorial/user-2.png)

4. **Ad ve e-posta adresi** seçin. Sonra, metin kutusunu aşağıdaki gibi doldurun:

    ![Ekran görüntüsü, kullanıcıları el ile ekleyebileceğiniz veya değiştirebileceğiniz Mange kullanıcıları iletişim kutusunu gösterir.](./media/cisco-spark-tutorial/user-3.png) 

    a. **Ilk ad** metin kutusuna **B** gibi kullanıcının adını yazın.

    b. **Soyadı** metin kutusunda, **Simon** gibi kullanıcı adının soyadını yazın.

    c. **E-posta adresi** metin kutusuna, gibi kullanıcının e-posta adresini yazın b.simon@contoso.com .

5. B. Simon eklemek için artı işaretine tıklayın. Ardından **İleri**' ye tıklayın.

6. **Kullanıcılar Için hizmet ekle** penceresinde, **Kullanıcı Ekle** ' ye ve ardından **son**' a tıklayın.

## <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Cisco WebEx oturum açma URL 'sine yeniden yönlendirilir. 

* Cisco WebEx oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Cisco WebEx kutucuğuna tıkladığınızda bu işlem Cisco WebEx oturum açma URL 'sine yönlendirilir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Cisco WebEx 'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)