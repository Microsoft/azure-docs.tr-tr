---
title: 'Öğretici: Amazon Business ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Amazon Business arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496913"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Öğretici: Amazon Business ile Azure Active Directory tümleştirme

Bu öğreticide, Amazon Business 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Amazon Business erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Iş Amazon 'e otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/)bir aylık ücretsiz deneme sürümü edinebilirsiniz.
* Amazon Iş çoklu oturum açma (SSO) aboneliği etkin. Amazon Business hesabı oluşturmak için [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) sayfasına gidin.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, mevcut bir Amazon Business hesabındaki Azure AD SSO 'yu yapılandırıp test edersiniz.

* Amazon Business **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler
* Amazon Business **, tam zamanında** Kullanıcı sağlamayı destekliyor

## <a name="adding-amazon-business-from-the-gallery"></a>Galeriden Amazon Business ekleme

Amazon Business 'ın Azure AD ile tümleştirilmesini yapılandırmak için, galerideki Amazon Business 'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Amazon Business** yazın.
1. Sonuçlar panelinden **Amazon Business** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Yapılandırma ve Azure AD çoklu oturum açmayı test etme

**B. Simon**adlı bir test kullanıcısı kullanarak Amazon Business Ile Azure AD SSO 'yu yapılandırın ve test edin.

Azure AD SSO 'yu Amazon Business ile yapılandırmak ve test etmek için aşağıdaki oluşturma adımlarını izleyin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. **[Amazon BUSINESS SSO 'Yu yapılandırma](#configure-amazon-business-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. Amazon Business **[test kullanıcısı oluşturma](#create-amazon-business-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Amazon Business 'ta B. Simon 'a karşılık gelen bir.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Amazon Business** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    1. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > Yanıt URL 'SI değeri gerçek değil. Bu değeri gerçek yanıt URL 'siyle güncelleştirin. Bu `<idpid>` değeri, Öğreticinin ilerleyen kısımlarında açıklanan Amazon Business SSO yapılandırma bölümünden alırsınız. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusuna bir URL yazın:`https://www.amazon.com/`

1. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Öznitelikleri, **Kullanıcı öznitelikleri & talepler** bölümündeki **Düzenle** simgesine tıklayarak düzenleyin.

    ![Öznitelikler](media/amazon-business-tutorial/map-attribute3.png)

1. Öznitelikleri düzenleyin ve bu özniteliklerin **ad alanı** değerini not defteri 'ne kopyalayın.

    ![Öznitelikler](media/amazon-business-tutorial/map-attribute4.png)

1. Amazon Business Application, yukarıdakine ek olarak SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. **Grup talepleri** Iletişim kutusunda **Kullanıcı öznitelikleri & talepler** bölümünde aşağıdaki adımları uygulayın:

    a. **Talepte döndürülen gruplar ' ın**yanındaki **kaleme** tıklayın.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Radyo listesinden **tüm gruplar** ' ı seçin.

    c. **Kaynak özniteliği**olarak **Grup Kimliği** ' ni seçin.

    d. **Grup Talebi onay kutusunun adını özelleştirin** ' i Işaretleyin ve kuruluşunuzun gereksinimlerine göre grup adını girin.

    e. **Kaydet**’e tıklayın.

1. **SAML Ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **meta veri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Amazon Business ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Amazon Business SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Amazon Business şirketinizin sitesinde yönetici olarak oturum açın.

1. **Kullanıcı profiline** tıklayın ve **iş ayarları**' nı seçin.

    ![Kullanıcı Profili](media/amazon-business-tutorial/user-profile.png)

1. **Sistem tümleştirmeler** sihirbazında, **Çoklu oturum açma (SSO)** seçeneğini belirleyin.

    ![Çoklu Oturum Açma (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. **SSO ayarlama** sihirbazında, Kuruluş gereksinimlerinize göre sağlayıcıyı seçin ve **İleri**' ye tıklayın.

    ![Varsayılan Grup](media/amazon-business-tutorial/default-group1.png)

1. **Yeni Kullanıcı hesabı Varsayılanları** sihirbazında, **varsayılan grubu** seçin ve ardından kuruluşunuzda kullanıcı rolüne göre **varsayılan satın alma rolü** ' nü seçin ve **İleri**' ye tıklayın.

    ![Varsayılan Grup](media/amazon-business-tutorial/dafault-group2.png)

1. **Meta veri dosyanızı karşıya yükleyin** sihirbazında, Azure Portal Indirdiğiniz **meta veri XML** dosyasını karşıya yüklemek için, **Araştır** ' a tıklayın ve **karşıya yükle**' ye tıklayın.

    ![Bağlantı verileri](media/amazon-business-tutorial/connection-data1.png)

1. İndirilen meta veri dosyasını karşıya yükledikten sonra, **bağlantı verileri** bölümündeki alanlar otomatik olarak doldurulur. Sonra **İleri**' ye tıklayın.

    ![Bağlantı verileri](media/amazon-business-tutorial/connection-data2.png)

1. **Öznitelik deyiminizi karşıya yükleyin** sihirbazında **Atla**' ya tıklayın.

    ![Öznitelikler](media/amazon-business-tutorial/map-attribute1.png)

1. **Öznitelik eşleme** sihirbazında **+ alan Ekle** seçeneğine tıklayarak gereksinim alanlarını ekleyin. Azure portal **Kullanıcı öznitelikleri & talepler** bölümünden, **SAML AttributeName** alanına kopyaladığınız ad alanı dahil öznitelik değerlerini ekleyin ve **İleri**' ye tıklayın.

    ![Öznitelikler](media/amazon-business-tutorial/map-attribute2.png)

1. **Amazon bağlantı verileri** sihirbazında, **İleri**' ye tıklayın.

    ![Bağlantı](media/amazon-business-tutorial/amazon-connect.png)

1. Lütfen yapılandırılan adımların **durumunu** kontrol edin ve **teste başla**' ya tıklayın.

    ![Bağlantı](media/amazon-business-tutorial/sso-connection1.png)

1. **Test SSO bağlantı** sihirbazında **Test**' e tıklayın.

    ![Bağlantı](media/amazon-business-tutorial/sso-connection2.png)

1. **IDP tarafından BAŞLATıLAN URL** sihirbazında, **Etkinleştir**' e tıklamadan önce **ıdpid** 'ye atanan DEĞERI kopyalayın ve Azure 'daki **temel SAML yapılandırması** bölümündeki **yanıt URL 'sindeki** **ıdpid** parametresine yapıştırın. Portal.

    ![Bağlantı](media/amazon-business-tutorial/sso-connection3.png)

1. **ETKIN SSO 'ya geçmeye çalışıyor musunuz?** sihirbazına izin vermek IÇIN, **SSO 'Yu tamamen test ediyorum ve canlı duruma** geç onay kutusunu işaretleyip **etkin ' e**tıklayın.

    ![Bağlantı](media/amazon-business-tutorial/sso-connection4.png)

1. Son olarak, **SSO bağlantı ayrıntıları** bölümünde **durum** **etkin**olarak gösterilir.

    ![Bağlantı](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Bir Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

> [!NOTE]
> Yöneticiler gerekirse, yönetim kullanıcılarının kiracısında test kullanıcılarını oluşturması gerekir. Aşağıdaki adımlarda bir test kullanıcısının nasıl oluşturulacağı gösterilmektedir.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Seçin **yeni kullanıcı** ekranın üstünde.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, username@companydomain.extensiongirin. Örneğin: `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1.           **Oluştur**'a tıklayın.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure portal bir Azure AD güvenlik grubu oluşturun

1. **Tüm grupları > Azure Active Directory**' ye tıklayın.

    ![Azure AD güvenlik grubu oluşturma](./media/amazon-business-tutorial/all-groups-tab.png)

1. **Yeni Grup**' a tıklayın:

    ![Azure AD güvenlik grubu oluşturma](./media/amazon-business-tutorial/new-group-tab.png)

1. **Grup türü**, **Grup adı**, **Grup açıklaması**, **üyelik türü**' nde Fill yazın. Üyeler ' i seçmek için oka tıklayın ve ardından gruba eklemek istediğiniz üyeyi arayın veya tıklayın. Seçili üyeleri eklemek için **Seç** ' e tıklayın, ardından **Oluştur**' a tıklayın.

    ![Azure AD güvenlik grubu oluşturma](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısı atayın

Bu bölümde, Amazon Business 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Amazon Business**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

   !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

    >[!NOTE]
    > Kullanıcıları Azure AD 'de atamadıysanız aşağıdaki hatayı alırsınız.

    ![Kullanıcı Ekle bağlantısı](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Azure portal Azure AD güvenlik grubunu atama

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Amazon Business**' u seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Amazon Business**' ı yazın ve seçin.

    ![Uygulamalar listesindeki Amazon Business bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve Gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' ye tıklayın.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. Kullanmak istediğiniz güvenlik grubunu arayın, ardından üyeleri seçin bölümüne eklemek için gruba tıklayın. **Seç**' e ve ardından **ata**' ya tıklayın.

    ![Güvenlik grubu ara](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Grubun Azure portal kurumsal uygulamaya başarıyla atandığını uyarılmak için menü çubuğundaki bildirimleri kontrol edin.

### <a name="create-amazon-business-test-user"></a>Amazon Iş testi kullanıcısı oluşturma

Bu bölümde, Amazon Business 'da B. Simon adlı bir Kullanıcı oluşturulur. Amazon Business, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı Amazon Business 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edin.

Erişim panelinde Amazon Business kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Amazon Business 'ta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory Koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
