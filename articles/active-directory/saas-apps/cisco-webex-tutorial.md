---
title: 'Öğretici Azure Active Directory: Cisco WebEx toplantılarıyla çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ve Cisco WebEx Toplantıları arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: bb8ea637d0353e4efa0cb946f486d68639fc699d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592500"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Öğretici: Cisco WebEx toplantılarıyla çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, Cisco WebEx toplantılarının Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. Cisco WebEx toplantılarını Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Cisco WebEx toplantılarına erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Cisco WebEx toplantılarına otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.


## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Cisco WebEx Toplantıları çoklu oturum açma (SSO) etkin aboneliği.

> [!NOTE]
> Bu tümleştirme Ayrıca Azure AD ABD kamu bulut ortamından kullanılabilir. Bu uygulamayı Azure AD ABD kamu bulutu uygulama galerisinde bulabilir ve bunu ortak buluttan yaptığınız şekilde yapılandırabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* Cisco WebEx Toplantıları **SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

* Cisco WebEx Toplantıları **, tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Galeriden Cisco WebEx Toplantıları ekleme

Cisco WebEx toplantılarının Azure AD 'de tümleştirilmesini yapılandırmak için, Galeriden, yönetilen SaaS uygulamaları listenize Cisco WebEx Toplantıları eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Cisco WebEx Toplantıları** yazın.
1. Sonuçlar panelinden **Cisco WebEx Toplantıları** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Cisco WebEx toplantıları için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Cisco WebEx toplantılarıyla Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için Cisco WebEx toplantılarında bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu Cisco WebEx toplantılarıyla yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
   1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
   1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
   
1. **[Cisco WebEx Toplantıları SSO 'Su yapılandırma](#configure-cisco-webex-meetings-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
   * Kullanıcının Azure AD gösterimine bağlı olan Cisco WebEx toplantılarında B. Simon 'a sahip olmak için **[Cisco WebEx Toplantıları test kullanıcısı oluşturun](#create-cisco-webex-meetings-test-user)** .
    
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Cisco WebEx Toplantıları** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, **Service Provider meta veri** dosyasını aşağıdaki gibi karşıya yükleyerek **IDP** tarafından başlatılan modda uygulamayı yapılandırabilirsiniz:
   1. **Meta veri dosyasını karşıya yükle**' ye tıklayın.
   1. Meta veri dosyasını seçmek için **klasör logosu** ' na tıklayın ve **karşıya yükle**' ye tıklayın.
   1. Hizmet sağlayıcı meta veri dosyasını karşıya yükleme işleminin başarıyla tamamlanmasından sonra, **tanımlayıcı** ve **yanıt URL** değerleri **temel SAML yapılandırması** bölümünde otomatik olarak doldurulur.
   
      > [!Note]
      > Service Provider meta verileri dosyasını, Öğreticinin ilerleyen kısımlarında açıklanan **Cisco WebEx Toplantıları SSO bölümünü yapılandırma** bölümünden alacaksınız. 

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:  
   1. **Temel SAML yapılandırması** bölümünde Düzenle/kalem simgesine tıklayın.

      ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

   1. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak URL 'yi yazın:`https://<customername>.my.webex.com`

1. Cisco WebEx Toplantıları uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Kullanıcı öznitelikleri iletişim kutusunu açmak için **Düzenle** simgesine tıklayın.

   ![image](common/edit-attribute.png)

1. Daha fazlasına ek olarak, Cisco WebEx Toplantıları uygulaması SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. Kullanıcı öznitelikleri iletişim kutusundaki Kullanıcı talepleri bölümünde aşağıdaki tabloda gösterildiği gibi SAML belirteci özniteliği eklemek için aşağıdaki adımları gerçekleştirin: 

   | Name | Kaynak özniteliği|
   | ---------------|  --------- |
   |   FirstName    | Kullanıcı. |
   |   Soyadı    | User. soyadı |
   |   e-posta       | Kullanıcı. Mail |
   |   'sini    | Kullanıcı. Mail |

   1. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' ye tıklayın.
   1. **Ad** metin kutusuna, bu satır için gösterilen öznitelik adını yazın.
   1. **Ad alanını** boş bırakın.
   1. **Öznitelik** olarak kaynak seçin.
   1. **Kaynak özniteliği** listesinden, açılan listeden o satır için gösterilen öznitelik değerini seçin.
   1. **Kaydet**’e tıklayın.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

   ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Cisco WebEx toplantılarını ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, Cisco WebEx toplantılarına erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **Cisco WebEx Toplantıları**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-cisco-webex-meetings-sso"></a>Cisco WebEx Toplantıları SSO 'SU yapılandırma

1. Yönetici kimlik bilgilerinizle Cisco WebEx toplantılarında oturum açın.
1. **Ortak site ayarları** ' na gidin ve **SSO yapılandırması**' na gidin.

   ![Ekran görüntüsü ortak site ayarları ve S S O yapılandırması seçiliyken Cisco WebEx yönetimini gösterir.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. **WebEx yönetimi** sayfasında, aşağıdaki adımları uygulayın:

   ![Ekran görüntüsü WebEx yönetim sayfasını bu adımda açıklanan bilgilerle gösterir.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. **Federasyon Protokolü** olarak **SAML 2,0** ' ı seçin.
   1. Azure portal 'ten indirdiğiniz meta veri dosyasını karşıya yüklemek için **SAML meta verilerini Içeri aktar** bağlantısına tıklayın.
   1. **IDP tarafından başlatılan** **SSO profilini** seçin ve **dışarı aktar** düğmesine tıklayarak hizmet sağlayıcısı meta VERI dosyasını indirin ve Azure Portal **temel SAML yapılandırması** bölümüne yükleyin.
   1. **Authcontextclassref** metin kutusunda aşağıdaki değerlerden birini yazın:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Azure AD 'yi kullanarak MFA 'yı etkinleştirmek için aşağıdaki gibi iki değer girin: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. **Otomatik hesap oluşturmayı** seçin.
   
      > [!NOTE]
      > **Tam zamanında** Kullanıcı sağlamayı etkinleştirmek Için **Otomatik hesap oluşturmayı** denetlemeniz gerekir. Bu SAML belirteci özniteliklerinin yanı sıra SAML yanıtında geçirilmesi gerekir.

   1. **Kaydet**’e tıklayın.

      > [!NOTE]
      > Bu yapılandırma yalnızca e-posta biçiminde WebEx UserID kullanan müşteriler içindir.
      > 
      > Cisco WebEx toplantılarının nasıl yapılandırılacağı hakkında daha fazla bilgi edinmek için bkz. [WebEx belgeleri](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) sayfası.

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco WebEx Toplantıları test kullanıcısı oluşturma

Bu bölümün amacı, Cisco WebEx toplantılarında B. Simon adlı bir Kullanıcı oluşturmaktır. Cisco WebEx Toplantıları, varsayılan olarak etkinleştirilen **tam zamanında** sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir Kullanıcı Cisco WebEx toplantılarında zaten mevcut değilse, Cisco WebEx toplantılarına erişmeye çalıştığınızda yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz Cisco WebEx toplantılarının oturum açma URL 'sine yeniden yönlendirilir.  

* Cisco WebEx Toplantıları oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız Cisco WebEx toplantılarında otomatik olarak oturum açmış olmanız gerekir.

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki Cisco WebEx Toplantıları kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa, oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız Cisco WebEx toplantılarında otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Cisco WebEx toplantılarını yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-aad)