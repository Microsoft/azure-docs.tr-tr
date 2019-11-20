---
title: 'Öğretici: pega sistemleriyle tümleştirme Azure Active Directory | Microsoft Docs'
description: Bu öğreticide, Azure Active Directory ve pega sistemleri arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026813"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Öğretici: pega sistemleriyle tümleştirme Azure Active Directory

Bu öğreticide, pega sistemlerini Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz.

Bu tümleştirme bu avantajları sağlar:

* Pega sistemlerine kimlerin erişebileceğini denetlemek için Azure AD kullanabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla pega sistemlerine (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz: Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini pega sistemleriyle yapılandırmak için şunları yapmanız gerekir:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa, bir [aylık deneme](https://azure.microsoft.com/pricing/free-trial/)için kaydolabilirsiniz.
* Çoklu oturum açma özelliği etkinleştirilmiş bir pega sistemleri aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edeceksiniz.

* Pega sistemleri SP tarafından başlatılan ve IDP tarafından başlatılan SSO 'yu destekler.

## <a name="add-pega-systems-from-the-gallery"></a>Galeriden pega sistemleri ekleme

Pega sistemlerinin Azure AD 'ye tümleştirmesini ayarlamak için galerideki pega sistemlerini yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. [Azure Portal](https://portal.azure.com)sol bölmede **Azure Active Directory**' i seçin:

    ![Azure Active Directory'yi seçin](common/select-azuread.png)

2. **Kurumsal uygulamalar** > **tüm uygulamalar**' a gidin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

3. Bir uygulama eklemek için pencerenin üst kısmındaki **Yeni uygulama** ' yı seçin:

    ![Yeni uygulama seçin](common/add-new-app.png)

4. Arama kutusuna **pega Systems**yazın. Arama sonuçlarında **pega sistemleri** ' ni seçin ve ardından **Ekle**' yi seçin.

     ![Arama sonuçları](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

Bu bölümde, Britta Simon adlı bir test kullanıcısı kullanarak pega sistemleriyle Azure AD çoklu oturum açmayı yapılandırıp test edeceksiniz.
Çoklu oturum açmayı etkinleştirmek için, bir Azure AD kullanıcısı ve pega sistemlerinde buna karşılık gelen kullanıcı arasında bir ilişki kurmanız gerekir.

Azure AD çoklu oturum açma 'yı pega sistemleriyle yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirmeniz gerekir:

1. Kullanıcılarınıza yönelik özelliği etkinleştirmek için **[Azure AD çoklu oturum açmayı yapılandırın](#configure-azure-ad-single-sign-on)** .
2. **[Pega sistemlerini uygulama tarafında çoklu oturum açmayı yapılandırın](#configure-pega-systems-single-sign-on)** .
3. Azure AD çoklu oturum açma sınamasını test etmek için **[bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** .
4. Kullanıcı için Azure AD çoklu oturum açma özelliğini etkinleştirmek için **[Azure AD test kullanıcısını atayın](#assign-the-azure-ad-test-user)** .
5. Kullanıcının Azure AD gösterimine bağlı **[bir pega sistemleri test kullanıcısı oluşturun](#create-a-pega-systems-test-user)** .
6. Yapılandırmanın çalıştığını doğrulamak için **[Çoklu oturum açmayı test](#test-single-sign-on)** edin.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma

Bu bölümde, Azure portal Azure AD çoklu oturum açmayı etkinleştireceksiniz.

Azure AD çoklu oturum açmayı pega sistemleriyle birlikte yapılandırmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com/), **pega Systems** uygulama tümleştirmesi sayfasında, **Çoklu oturum açma**' yı seçin:

    ![Çoklu oturum açma seçin](common/select-sso.png)

2. Çoklu oturum **açma yöntemi seç** iletişim kutusunda, çoklu oturum açmayı etkinleştirmek için **SAML/WS-Besme** modunu seçin:

    ![Çoklu oturum açma yöntemi seçin](common/select-saml-option.png)

3. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **temel SAML yapılandırması** Iletişim kutusunu açmak için **Düzenle** simgesini seçin:

    ![Düzenle simgesi](common/edit-urls.png)

4. **Temel SAML yapılandırması** iletişim kutusunda, uygulamayı IDP tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları izleyin.

    ![Temel SAML yapılandırması iletişim kutusu](common/idp-intiated.png)

    1. **Tanımlayıcı** kutusunda, bu modele bir URL girin:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. **Yanıt URL 'si** kutusuna, bu modele bir URL girin:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Uygulamayı SP tarafından başlatılan modda yapılandırmak istiyorsanız **ek URL 'Ler ayarla** ' yı seçin ve aşağıdaki adımları izleyin.

    ![Pega sistemleri etki alanı ve URL 'Ler çoklu oturum açma bilgileri](common/both-advanced-urls.png)

    1. **Oturum açma URL 'si** kutusuna oturum açma URL 'si değerini girin.

    1. **Geçiş durumu** kutusunda şu düzende bir URL girin: `https://<customername>.pegacloud.io/prweb/sso`

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

    1. **Kaynak**için **öznitelik**' i seçin.

    1. **Kaynak özniteliği** listesinde, bu satır için gösterilen öznitelik değerini seçin.

    1. **Tamam**’ı seçin.

    1. **Kaydet**’i seçin.

8. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**' nun yanında, gereksinimlerinize göre **yükleme** bağlantısını seçin ve sertifikayı bilgisayarınıza kaydedin:

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

9. **Pega sistemlerini ayarla** bölümünde, gereksinimlerinize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

    1. **Oturum açma URL 'si**.

    1. **Azure AD tanımlayıcısı**.

    1. **Oturum kapatma URL 'si**.

### <a name="configure-pega-systems-single-sign-on"></a>Pega sistemlerini çoklu oturum açmayı yapılandırma

1. **Pega sistemleri** tarafında çoklu oturum açmayı yapılandırmak için, başka bir tarayıcı penceresinde yönetici hesabıyla pega portalında oturum açın.

2. @No__t **Oluştur**-1**sysadmin** > **kimlik doğrulama hizmeti**seçin:

    ![Kimlik doğrulama hizmeti seçin](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. **Kimlik doğrulama hizmeti oluştur** ekranında aşağıdaki adımları izleyin.

    ![Kimlik doğrulama hizmeti ekranı oluştur](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. **Tür** listesinde **SAML 2,0**' yi seçin.

    1. **Ad** kutusuna herhangi bir ad (örneğin, **Azure AD SSO**) girin.

    1. **Kısa açıklama** kutusuna bir açıklama girin.  

    1. **Oluştur ve Aç ' ı**seçin.
    
4. **Kimlik sağlayıcısı (IDP) bilgileri** bölümünde **IDP meta verilerini içeri aktar** ' ı seçin ve Azure Portal indirdiğiniz meta veri dosyasına gidin. Meta verileri yüklemek için **Gönder** ' e tıklayın:

    ![Kimlik sağlayıcısı (IDP) bilgi bölümü](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    İçeri aktarma, IDP verilerini burada gösterildiği gibi dolduracaktır:

    ![IOP verileri içeri aktarıldı](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. **Hizmet sağlayıcısı (SP) ayarları** bölümünde aşağıdaki adımları uygulayın.

    ![Hizmet sağlayıcı ayarları](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. **Varlık tanımlama** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **tanımlayıcı** kutusuna yapıştırın.

    1. **Onaylama tüketici hizmeti (ACS) konum** değerini kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'si** kutusuna yapıştırın.

    1. **İstek IMZALAMAYı devre dışı bırak**' ı seçin.

7. **Kaydet**’i seçin.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portal Britta Simon adlı bir test kullanıcısı oluşturacaksınız.

1. Azure portal sol bölmedeki **Azure Active Directory** ' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin:

    ![Tüm kullanıcıları seç](common/users.png)

2. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin:

    ![Yeni Kullanıcı Seç](common/new-user.png)

3. **Kullanıcı** iletişim kutusunda aşağıdaki adımları izleyin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** kutusuna **Brittasıon**yazın.
  
    b. **Kullanıcı adı** kutusuna **brittasıon @ \<yourcompanydomain >. \<extension >** girin. (Örneğin, BrittaSimon@contoso.com.)

    c. **Parolayı göster**' i seçin ve ardından **parola** kutusunda değer ' i yazın.

    d. **Oluştur**'u seçin.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, pega sistemlerine erişimi vererek Azure çoklu oturum açma özelliğini kullanmak için Britta Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **pega sistemleri**' ni seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde **pega Systems**' ı seçin.

    ![Uygulama listesi](common/all-applications.png)

3. Sol bölmede **Kullanıcılar ve gruplar**' ı seçin:

    ![Kullanıcıları ve grupları seçin](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' yi seçin ve sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle ' yi seçin](common/add-assign-user.png)

5. **Kullanıcılar ve gruplar** iletişim kutusunda kullanıcılar listesinden **Britta Simon** ' ı seçin ve ardından ekranın altındaki **Seç** düğmesine tıklayın.

6. SAML assertion 'da bir rol değeri bekleliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin. Ekranın alt kısmındaki **Seç** düğmesine tıklayın.

7. **Atama Ekle** Iletişim kutusunda **ata**' yı seçin.

### <a name="create-a-pega-systems-test-user"></a>Pega sistemleri test kullanıcısı oluşturma

Ardından, pega sistemlerinde Britta Simon adlı bir kullanıcı oluşturmanız gerekir. Kullanıcı oluşturmak için [pega Systems destek](https://www.pega.com/contact-us) ekibiyle çalışın.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi

Şimdi, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test etmeniz gerekir.

Erişim panelinde pega sistemleri kutucuğunu seçtiğinizde, SSO 'yu ayarladığınız pega sistemleri örneğine otomatik olarak oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [uygulamalarım portalındaki uygulamalara erişme ve bunları kullanma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme öğreticileri](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)