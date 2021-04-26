---
title: 'Öğretici Azure Active Directory: MongoDB bulutu ile çoklu oturum açma (SSO) Tümleştirmesi | Microsoft Docs'
description: Azure Active Directory ile MongoDB bulutu arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 5904d3eeec3f5880213f8a8c6a41cefbe76801b3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520097"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Öğretici: MongoDB bulutu ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, MongoDB bulutunu Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. MongoDB bulutunu Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* MongoDB bulutu, MongoDB Atlas, MongoDB topluluğu, MongoDB Üniversitesi ve MongoDB desteğine erişimi olan Azure AD 'de denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla MongoDB bulutuna otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin: Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* MongoDB bulutu çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* MongoDB bulutu, **SP** ve **IDP** tarafından başlatılan SSO 'yu destekler.
* MongoDB bulutu **, tam zamanında** Kullanıcı sağlamayı destekler.

## <a name="add-mongodb-cloud-from-the-gallery"></a>Galeriden MongoDB bulutu ekleme

MongoDB bulutunun Azure AD ile tümleştirilmesini yapılandırmak için, Galeriden MongoDB bulutunu yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **MongoDB bulutu** yazın.
1. Sonuçlar panelinden **MongoDB bulutu** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-mongodb-cloud"></a>MongoDB bulutu için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak MongoDB bulutuyla Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, MongoDB bulutu 'ndaki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bağlı bir ilişki kurmanız gerekir.

Azure AD SSO 'yu MongoDB bulutu ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için [Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso) .
    1. B. Simon ile Azure AD çoklu oturum açma sınamasını test etmek için [bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user) .
    1. Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek üzere [Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user) .
1. Uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için [MongoDB Cloud SSO 'Yu yapılandırın](#configure-mongodb-cloud-sso) .
    1. MongoDB bulutu 'nda, kullanıcının Azure AD gösterimine bağlı olarak B. Simon 'a karşılık gelen bir [MongoDB bulut test kullanıcısı oluşturun](#create-a-mongodb-cloud-test-user) .
1. Yapılandırmanın çalışıp çalışmadığını doğrulamak için [test SSO 'su](#test-sso) .

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **MongoDB Cloud** Application Integration sayfasında **Yönet** bölümünü bulun. **Çoklu oturum açma** seçeneğini belirleyin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesini seçin.

   ![Tek Sign-On SAML sayfası ile ayarlama ekran görüntüsü, kurşun kalem simgesi vurgulanmış](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda uygulamayı yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanan bir URL yazın:`https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. **Ek URL 'Ler ayarla**' yı seçin ve uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanan bir URL yazın:`https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [MongoDB bulut istemci desteği ekibine](https://support.mongodb.com/)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. MongoDB bulut uygulaması SAML onayları 'nin belirli bir biçimde olmasını bekler, bu da SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektirir. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![Varsayılan özniteliklerin ekran görüntüsü](common/default-attributes.png)

1. Önceki özniteliklere ek olarak, MongoDB Cloud uygulaması, SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. Bu öznitelikler de önceden doldurulur, ancak gereksinimlerinize göre gözden geçirebilirsiniz.
    
    | Name | Kaynak özniteliği|
    | ---------------| --------- |
    | e-posta | User. UserPrincipalName |
    | firstName | Kullanıcı. |
    | lastName | User. soyadı |

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**'i bulun. Sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Indirme bağlantısı vurgulanmış şekilde SAML Imzalama sertifikası bölümünün ekran görüntüsü](common/metadataxml.png)

1. **MongoDB bulutu ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![URL vurgulanmış şekilde Mongo DB bulutu ayarlama bölümünün ekran görüntüsü](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure Portal sol bölmeden, kullanıcılar **Azure Active Directory**  >    >  **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu işaretleyin ve parolayı aşağı yazın.
   1. **Oluştur**’u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, MongoDB bulutuna erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **MongoDB bulutu**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-mongodb-cloud-sso"></a>MongoDB Cloud SSO 'yu yapılandırma

MongoDB bulut tarafında çoklu oturum açmayı yapılandırmak için, Azure portal kopyalanmış uygun URL 'Lerin olması gerekir. Ayrıca, MongoDB bulut kuruluşunuz için Federasyon uygulamasını yapılandırmanız gerekir. [MongoDB bulut belgelerindeki](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/)yönergeleri izleyin. Bir sorununuz varsa [MongoDB bulut desteği ekibine](https://support.mongodb.com/)başvurun.

### <a name="create-a-mongodb-cloud-test-user"></a>MongoDB bulut testi kullanıcısı oluşturma

MongoDB bulutu, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Yapmanız gereken başka bir eylem yoktur. Bir Kullanıcı MongoDB bulutu 'nda zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz MongoDB bulut oturum açma URL 'sine yeniden yönlendirilir.  

* Doğrudan MongoDB bulutu oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız MongoDB bulutunda otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki MongoDB bulut kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız MongoDB bulutunda otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Sonraki adımlar

MongoDB bulutunu yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
