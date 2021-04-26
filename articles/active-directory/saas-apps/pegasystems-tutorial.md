---
title: 'Öğretici: pega sistemleriyle tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ve pega sistemleri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 802bd61d499f64a128a4d1c0585363cb1962f8a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650048"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Öğretici: pega sistemleriyle tümleştirme Azure Active Directory

Bu öğreticide, pega sistemlerini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. Pega sistemlerini Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de pega sistemlerine erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla pega sistemlerine otomatik olarak oturum açmalarına olanak sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Pega Systems çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* Pega sistemleri SP tarafından başlatılan ve IDP tarafından başlatılan SSO 'yu destekler.

## <a name="add-pega-systems-from-the-gallery"></a>Galeriden pega sistemleri ekleme

Pega sistemlerinin Azure AD ile tümleştirilmesini yapılandırmak için galerideki pega sistemlerini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **pega Systems** yazın.
1. Sonuçlar panelinden **pega sistemleri** ' ni seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Pega sistemleri için Azure AD SSO 'yu yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak, pega SISTEMLERIYLE Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, pega sistemlerinde bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

Azure AD SSO 'yu pega sistemleriyle yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[Pega Systems SSO 'Yu yapılandırma](#configure-pega-systems-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. Kullanıcının Azure AD gösterimine bağlı olan pega sistemlerinde B. Simon 'un bir karşılığı olacak şekilde **[pega sistemleri test kullanıcısı oluşturun](#create-pega-systems-test-user)** .
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **pega Systems** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, uygulamayı IDP tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları izleyin.

    ![Temel SAML yapılandırması iletişim kutusu](common/idp-intiated.png)

    1. **Tanımlayıcı** kutusunda, bu modele bir URL girin:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. **Yanıt URL 'si** kutusuna, bu modele bir URL girin:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Uygulamayı SP tarafından başlatılan modda yapılandırmak istiyorsanız **ek URL 'Ler ayarla** ' yı seçin ve aşağıdaki adımları izleyin.

    ![Pega sistemleri etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/both-advanced-urls.png)

    1. **Oturum açma URL 'si** kutusuna oturum açma URL 'si değerini girin.

    1. **Geçiş durumu** kutusunda, bu modele bir URL girin:`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Burada belirtilen değerler yer tutuculardır. Gerçek tanımlayıcıyı, yanıt URL 'sini, oturum açma URL 'sini ve geçiş durumu URL 'sini kullanmanız gerekir. Bu öğreticinin ilerleyen kısımlarında açıklandığı gibi bir pega uygulamasından tanımlayıcıyı ve yanıt URL 'SI değerlerini alabilirsiniz. Geçiş durumu değerini almak için, [pega Systems destek ekibine](https://www.pega.com/contact-us)başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

6. Pega Systems uygulamasının, SAML onaylamaları için belirli bir biçimde olması gerekir. Bunları doğru biçimde almak için SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemeniz gerekir. Aşağıdaki ekran görüntüsünde varsayılan öznitelikler gösterilmektedir. **Kullanıcı öznitelikleri** iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Kullanıcı öznitelikleri](common/edit-attribute.png)

7. Önceki ekran görüntüsünde gösterilen özniteliklerin yanı sıra, pega sistemleri uygulamasının SAML yanıtına daha fazla sayıda özniteliğin geri geçirilmesi gerekir. **Kullanıcı öznitelikleri** Iletişim kutusunun **Kullanıcı talepleri** bölümünde, bu SAML belirteci özniteliklerini eklemek için aşağıdaki adımları izleyin:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Bu değerler kuruluşunuza özgüdür. Uygun değerleri girin.

    1. **Kullanıcı taleplerini Yönet** iletişim kutusunu açmak için **yeni talep Ekle** ' yi seçin:

    ![Yeni talep Ekle ' yi seçin](common/new-save-attribute.png)

    ![Kullanıcı taleplerini Yönet iletişim kutusu](common/new-attribute-details.png)

    1. **Ad** kutusuna, bu satır için gösterilen öznitelik adını girin.

    1. **Ad alanı** kutusunu boş bırakın.

    1. **Kaynak** için **öznitelik**' i seçin.

    1. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini seçin.

    1. **Tamam**'ı seçin.

    1. **Kaydet**’i seçin.

8. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**' nun yanında, gereksinimlerinize göre **yükleme** bağlantısını seçin ve sertifikayı bilgisayarınıza kaydedin:

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

9. **Pega sistemlerini ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, pega sistemlerine erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde **pega Systems**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

### <a name="configure-pega-systems-sso"></a>Pega Systems SSO 'yu yapılandırma

1. **Pega sistemleri** tarafında çoklu oturum açmayı yapılandırmak için, başka bir tarayıcı penceresinde yönetici hesabıyla pega portalında oturum açın.

2.   >  **Sysadmin**  >  **kimlik doğrulama hizmeti** oluştur ' u seçin:

    ![Kimlik doğrulama hizmeti seçin](./media/pegasystems-tutorial/admin.png)
    
3. **Kimlik doğrulama hizmeti oluştur** ekranında aşağıdaki adımları izleyin.

    ![Kimlik doğrulama hizmeti ekranı oluştur](./media/pegasystems-tutorial/admin1.png)

    1. **Tür** listesinde **SAML 2,0**' yi seçin.

    1. **Ad** kutusuna herhangi bir ad (örneğin, **Azure AD SSO**) girin.

    1. **Kısa açıklama** kutusuna bir açıklama girin.  

    1. **Oluştur ve Aç ' ı** seçin.
    
4. **Kimlik sağlayıcısı (IDP) bilgileri** bölümünde **IDP meta verilerini içeri aktar** ' ı seçin ve Azure Portal indirdiğiniz meta veri dosyasına gidin. Meta verileri yüklemek için **Gönder** ' e tıklayın:

    ![Kimlik sağlayıcısı (IDP) bilgi bölümü](./media/pegasystems-tutorial/admin2.png)
    
    İçeri aktarma, IDP verilerini burada gösterildiği gibi dolduracaktır:

    ![IOP verileri içeri aktarıldı](./media/pegasystems-tutorial/idp.png)
    
6. **Hizmet sağlayıcısı (SP) ayarları** bölümünde aşağıdaki adımları uygulayın.

    ![Hizmet sağlayıcı ayarları](./media/pegasystems-tutorial/sp.png)

    1. **Varlık tanımlama** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** kutusuna yapıştırın.

    1. **Onaylama tüketici hizmeti (ACS) konum** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'si** kutusuna yapıştırın.

    1. **İstek IMZALAMAYı devre dışı bırak**' ı seçin.

7. **Kaydet**’i seçin.

### <a name="create-pega-systems-test-user"></a>Pega sistemleri test kullanıcısı oluşturma

Ardından, pega sistemlerinde Britta Simon adlı bir kullanıcı oluşturmanız gerekir. Kullanıcı oluşturmak için [pega Systems destek](https://www.pega.com/contact-us) ekibiyle çalışın.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz pega Systems oturum açma URL 'sine yeniden yönlendirilir.  

* Pega Systems oturum açma URL 'sine doğrudan gidin ve oturum akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'Yu ayarladığınız pega sistemlerinde otomatik olarak oturum açmış olmanız gerekir. 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamalarım içindeki pega sistemleri kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız pega sistemlerinde otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

Pega sistemlerini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).