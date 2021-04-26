---
title: 'Öğretici: NetDocuments ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve NetDocuments arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 48ba2810c0aaf304042580cdf6579df54fd9ccd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Öğretici: NetDocuments ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, NetDocuments Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz. NetDocuments 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de NetDocuments erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla NetDocuments 'e otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* NetDocuments çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* NetDocuments **SP** tarafından başlatılan SSO 'yu destekler

## <a name="adding-netdocuments-from-the-gallery"></a>Galeriden NetDocuments ekleme

NetDocuments 'un Azure AD ile tümleştirilmesini yapılandırmak için, galerideki yönetim SaaS uygulamaları listenize NetDocuments eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, ara kutusuna **NetDocuments** yazın.
1. Sonuçlar panelinden **NetDocuments** ' i seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-netdocuments"></a>NetDocuments için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Azure AD SSO 'Yu NetDocuments ile yapılandırın ve test edin. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve NetDocuments içindeki ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu NetDocuments ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını doldurun:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[NetDocuments SSO 'Yu yapılandırma](#configure-netdocuments-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan NetDocuments 'ta B. Simon 'a sahip olmak için, **[NetDocuments test kullanıcısı oluşturun](#create-netdocuments-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **NetDocuments** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, aşağıdaki alanlar için değerleri girin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki URL desenlerinden birini yazın:

    |Oturum açma URL 'SI|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    b. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna URL 'lerden birini yazın:

    |Tanımlayıcı|
    |-----------|
    |`http://netdocuments.com/VAULT`|
    |`http://netdocuments.com/EU`|
    |`http://netdocuments.com/AU`|
    |`http://netdocuments.com/DE`|
    |

    c. **Yanıt URL 'si** metin kutusuna aşağıdaki URL desenlerinden birini yazın:

    |Yanıt URL'si|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri, gerçek oturum açma URL 'SI ve yanıt URL 'SI ile güncelleştirin. Depo KIMLIĞI, **CA 'dan** ve ardından NetDocuments deponuz ile ilişkili 8 karakter kodundan başlayan bir değerdir. Daha fazla bilgi için [NetDocuments federal kimlik desteği belgesine](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) bakabilirsiniz. Alternatif olarak, yukarıdaki bilgileri kullanarak yapılandırma zorluklarla karşılaşıyorsanız, bu değerleri almak için [NetDocuments istemci destek ekibine](https://support.netdocuments.com/hc/) başvurabilirsiniz. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. NetDocuments uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekler. Aşağıdaki ekran görüntüsünde, **NameIdentifier** 'ın **User. UserPrincipalName** ile eşlendiği varsayılan özniteliklerin listesi gösterilmektedir. NetDocuments uygulaması, **NameIdentifier** 'ın, **adıdentifier** olarak kuruluşunuz için geçerli olan **ObjectID** veya başka bir talep ile eşlenmesini bekliyor, bu nedenle, **Düzenle** simgesine tıklayarak ve öznitelik eşlemesini değiştirerek öznitelik eşlemesini düzenlemeniz gerekir.

    ![image](common/edit-attribute.png)

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **uygulama Federasyon meta verileri URL 'sini** bulun ve URL 'yi kopyalayın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

1. **NetDocuments ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD test kullanıcısını atama

Bu bölümde, NetDocuments 'e erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **NetDocuments**' i seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, &quot;varsayılan erişim&quot; rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name=&quot;configure-netdocuments-sso&quot;></a>NetDocuments SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, NetDocuments şirket sitenizde yönetici olarak oturum açın.

2. Sağ üst köşede, adınızı>**yöneticinizle** seçin.

3. **Güvenlik Merkezi**'ni seçin.
   
    ![Depo](./media/netdocuments-tutorial/security-center.png &quot;Güvenlik Merkezi")

4. **Gelişmiş kimlik doğrulaması**' nı seçin.
    
    ![Gelişmiş kimlik doğrulama seçeneklerini yapılandırma](./media/netdocuments-tutorial/advance-authentication.png "Gelişmiş kimlik doğrulama seçeneklerini yapılandırma")

5.  **Federal Kimlik** sekmesinde aşağıdaki adımları gerçekleştirin:   
   
    [![Federal Kimlik](./media/netdocuments-tutorial/federated-id.png "Federal Kimlik")](./media/netdocuments-tutorial/federated-id.png#lightbox)
   
    a. **Federal Kimlik sunucu türü** için **Windows Azure Active Directory** olarak öğesini seçin.
    
    b.  Azure portal ' den indirdiğiniz indirilen meta veri dosyasını karşıya yüklemek için **Dosya Seç**' i seçin.
    
    c.  **Kaydet**' i seçin.

### <a name="create-netdocuments-test-user"></a>NetDocuments test kullanıcısı oluştur

Azure AD kullanıcılarının NetDocuments 'ta oturum açmasını sağlamak için bunların NetDocuments içinde sağlanması gerekir. NetDocuments durumunda sağlama, el ile gerçekleştirilen bir görevdir.

**Bir kullanıcı hesabı sağlamak için aşağıdaki adımları gerçekleştirin:**

1. **NetDocuments** şirket sitenizde yönetici olarak oturum açın.

2. Sağ üst köşede, adınızı>**yöneticinizle** seçin.
   
    ![Yönetici](./media/netdocuments-tutorial/user-admin.png "Yönetici")

3. **Kullanıcılar ve gruplar ' ı** seçin.
   
    ![Kullanıcılar ve gruplar](./media/netdocuments-tutorial/users-groups.png "Depo")

4. **E-posta adresi** metin kutusuna, sağlamak istediğiniz geçerli bir Azure Active Directory hesabının e-posta adresini yazın ve ardından **Kullanıcı Ekle**' ye tıklayın.
   
    ![E-posta adresi](./media/netdocuments-tutorial/user-mail.png "E-posta Adresi")
   
    > [!NOTE]
    > Azure Active Directory hesap sahibi, hesabı etkin olmadan önce onaylamaya yönelik bir bağlantı içeren bir e-posta alır. Azure Active Directory Kullanıcı hesapları sağlamak için NetDocuments tarafından sunulan diğer tüm NetDocuments Kullanıcı hesabı oluşturma araçlarını veya API 'Leri kullanabilirsiniz.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz NetDocuments oturum açma URL 'sine yeniden yönlendirilir. 

* Doğrudan NetDocuments oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki NetDocuments kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız NetDocuments 'ta otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

NetDocuments yapılandırıldıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).