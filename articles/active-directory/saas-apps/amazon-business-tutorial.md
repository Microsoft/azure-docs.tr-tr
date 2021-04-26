---
title: 'Öğretici: Amazon Business ile tümleştirme Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve Amazon Business arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 1424669af9fe28257e66c8c5438f6e1d171954e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672908"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Öğretici: Amazon Business ile Azure Active Directory tümleştirme

Bu öğreticide, Amazon Business 'ı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) 'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de Amazon Business erişimi olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla Iş Amazon 'e otomatik olarak kaydolmalarına imkan tanıyın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

Azure AD ile SaaS uygulaması tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma nedir?](../manage-apps/what-is-single-sign-on.md).

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

1. [Azure Portal](https://portal.azure.com) iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Amazon Business** yazın.
1. Sonuçlar panelinden **Amazon Business** ' ı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD çoklu oturum açmayı yapılandırma ve test etme

**B. Simon** adlı bir test kullanıcısı kullanarak Amazon Business Ile Azure AD SSO 'yu yapılandırın ve test edin.

Azure AD SSO 'yu Amazon Business ile yapılandırmak ve test etmek için aşağıdaki oluşturma adımlarını izleyin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
2. **[Amazon BUSINESS SSO 'Yu yapılandırma](#configure-amazon-business-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
3. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
4. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
5. Amazon Business **[test kullanıcısı oluşturma](#create-amazon-business-test-user)** -kullanıcının Azure AD gösterimine bağlı olan Amazon Business 'ta B. Simon 'a karşılık gelen bir.
6. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com/), **Amazon Business** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML Ile tek Sign-On ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

    ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Temel SAML yapılandırması** bölümünde, **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız aşağıdaki adımları uygulayın:

    1. **Tanımlayıcı (VARLıK kimliği)** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

       | URL | Region |
       |-|-|
       | `https://www.amazon.com`| Kuzey Amerika |
       | `https://www.amazon.co.jp`| Doğu Asya |
       | `https://www.amazon.de`| Avrupa |

    1. **Yanıt URL 'si** metin kutusuna aşağıdaki desenlerden birini kullanarak bir URL yazın:

       | URL | Region |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Kuzey Amerika |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Doğu Asya |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Avrupa |

       > [!NOTE]
       > Yanıt URL 'SI değeri gerçek değil. Bu değeri gerçek yanıt URL 'siyle güncelleştirin. `<idpid>`Bu değeri, Öğreticinin ilerleyen kısımlarında açıklanan Amazon BUSINESS SSO yapılandırma bölümünden alırsınız. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız, Amazon Business Configuration ' da sunulan tam URL 'Yi **ek URL 'Ler ayarla** bölümünde **oturum açma URL** 'sine eklemeniz gerekir.

1. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir. Öznitelikleri, **Kullanıcı öznitelikleri & talepler** bölümündeki **Düzenle** simgesine tıklayarak düzenleyin.

    ![Ekran görüntüsü, kullanıcı özniteliklerinin & taleplerini Kullanıcı. Ise ve Emaadresi Kullanıcı. Mail gibi varsayılan değerlerle gösterir.](media/amazon-business-tutorial/map-attribute3.png)

1. Öznitelikleri düzenleyin ve bu özniteliklerin **ad alanı** değerini not defteri 'ne kopyalayın.

    ![Ekran görüntüsü, talep adı ve değer için sütun içeren talepler & Kullanıcı özniteliklerini gösterir.](media/amazon-business-tutorial/map-attribute4.png)

1. Amazon Business Application, yukarıdakine ek olarak SAML yanıtına daha fazla özniteliğin geri geçirilmesini bekler. **Grup talepleri** Iletişim kutusunda **Kullanıcı öznitelikleri & talepler** bölümünde aşağıdaki adımları uygulayın:

    1. **Talepte döndürülen gruplar ' ın** yanındaki **kaleme** tıklayın.

        ![Ekran görüntüsü, seçili talepler ' de döndürülen gruplar için Kullanıcı özniteliklerini & taleplerini gösterir.](./media/amazon-business-tutorial/config04.png)

        ![Ekran görüntüsünde, bu yordamda açıklandığı gibi değerleri içeren Grup talepleri gösterilmektedir.](./media/amazon-business-tutorial/config05.png)

    1. Radyo listesinden **tüm gruplar** ' ı seçin.

    1. **Kaynak özniteliği** olarak **Grup Kimliği** ' ni seçin.

    1. **Grup Talebi onay kutusunun adını özelleştirin** ' i Işaretleyin ve kuruluşunuzun gereksinimlerine göre grup adını girin.

    1. **Kaydet**’e tıklayın.

1. **SAML Ile tekli Sign-On ayarlama** sayfasında, **SAML Imzalama Sertifikası** bölümünde **meta veri XML** 'i bulun ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Amazon Business ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

    ![Yapılandırma URL 'Lerini Kopyala](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Amazon Business SSO 'yu yapılandırma

1. Farklı bir Web tarayıcısı penceresinde, Amazon Business şirketinizin sitesinde yönetici olarak oturum açın.

1. **Kullanıcı profiline** tıklayın ve **iş ayarları**' nı seçin.

    ![Kullanıcı Profili](media/amazon-business-tutorial/user-profile.png)

1. **Sistem tümleştirmeler** sihirbazında, **tek Sign-On (SSO)** öğesini seçin.

    ![Çoklu Oturum Açma (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. **SSO ayarlama** sihirbazında, Kuruluş gereksinimlerinize göre sağlayıcıyı seçin ve **İleri**' ye tıklayın.

    ![Ekran görüntüsü, S s 'yi, Microsoft Azure bir D ve Ileri seçiliyken ayarlamayı gösterir.](media/amazon-business-tutorial/default-group1.png)

    > [!NOTE]
    > Microsoft ADFS listelenmiş bir seçenek olsa da Azure AD SSO ile çalışmaz.

1. **Yeni Kullanıcı hesabı Varsayılanları** sihirbazında, **varsayılan grubu** seçin ve ardından kuruluşunuzda kullanıcı rolüne göre **varsayılan satın alma rolü** ' nü seçin ve **İleri**' ye tıklayın.

    ![Ekran görüntüsü, Yeni Kullanıcı hesabı varsayılanlarını Microsoft S S, talep eden ve Next seçili olarak gösterir.](media/amazon-business-tutorial/dafault-group2.png)

1. **Meta veri dosyanızı karşıya yükleyin** sihirbazında, Azure Portal Indirdiğiniz **meta veri XML** dosyasını karşıya yüklemek için, **Araştır** ' a tıklayın ve **karşıya yükle**' ye tıklayın.

    ![Ekran görüntüsü, bir x m l dosyasına gözatmanıza ve karşıya yüklemenize olanak tanıyan meta veri dosyanızı karşıya yüklemeyi gösterir.](media/amazon-business-tutorial/connection-data1.png)

1. İndirilen meta veri dosyasını karşıya yükledikten sonra, **bağlantı verileri** bölümündeki alanlar otomatik olarak doldurulur. Sonra **İleri**' ye tıklayın.

    ![Ekran görüntüsü, Azure A D tanımlayıcı, oturum açma U R L ve SAML Imzalama sertifikası belirtebileceğiniz bağlantı verilerini gösterir.](media/amazon-business-tutorial/connection-data2.png)

1. **Öznitelik deyiminizi karşıya yükleyin** sihirbazında **Atla**' ya tıklayın.

    ![Ekran görüntüsü, öznitelik deyiminize gözatmanızı sağlayan Attribute deyiminizi karşıya yüklemeyi gösterir, ancak bu durumda atla ' yı seçin.](media/amazon-business-tutorial/map-attribute1.png)

1. **Öznitelik eşleme** sihirbazında **+ alan Ekle** seçeneğine tıklayarak gereksinim alanlarını ekleyin. Azure portal **Kullanıcı öznitelikleri & talepler** bölümünden,  **SAML AttributeName** alanına kopyaladığınız ad alanı dahil öznitelik değerlerini ekleyin ve **İleri**' ye tıklayın.

    ![Ekran görüntüsü, Amazon Data SAML öznitelik adlarınızı düzenleyebileceğiniz öznitelik eşlemesini gösterir.](media/amazon-business-tutorial/map-attribute2.png)

1. **Amazon bağlantı verileri** sihirbazında, **İleri**' ye tıklayın.

    ![Ekran görüntüsü Amazon bağlantı verilerini gösterir, burada devam etmek için ileri ' ye tıklayabilirsiniz.](media/amazon-business-tutorial/amazon-connect.png)

1. Lütfen yapılandırılan adımların **durumunu** kontrol edin ve **teste başla**' ya tıklayın.

    ![Ekran görüntüsü, testi başlatma seçeneğiyle birlikte S O bağlantı ayrıntılarını gösterir.](media/amazon-business-tutorial/sso-connection1.png)

1. **Test SSO bağlantı** sihirbazında **Test**' e tıklayın.

    ![Ekran görüntüsü test düğmesiyle test S O bağlantısını gösterir.](media/amazon-business-tutorial/sso-connection2.png)

1. **IDP tarafından BAŞLATıLAN URL** sihirbazında, **Etkinleştir**' e tıklamadan önce **ıdpid** 'ye atanan değeri kopyalayın ve Azure Portal **temel SAML yapılandırması** bölümündeki **yanıt URL 'sindeki** **ıdpid** parametresine yapıştırın.

    ![Ekran görüntüsü, test için gerekli bir U R L 'yi alabileceğiniz ve sonra Etkinleştir ' i seçebileceğiniz g/ç tarafından başlatılan U R L 'yi gösterir.](media/amazon-business-tutorial/sso-connection3.png)

1. **ETKIN SSO 'ya geçmeye çalışıyor musunuz?** sihirbazına izin vermek IÇIN, **SSO 'Yu tamamen test ediyorum ve canlı duruma** geç onay kutusunu işaretleyip **etkin ' e** tıklayın.

    ![Ekran görüntüsü, etkin ' e geç ' i seçebileceğiniz etkin S S öğesine geçiş yapmaya hazırız olduğunu gösterir.](media/amazon-business-tutorial/sso-connection4.png)

1. Son olarak, **SSO bağlantı ayrıntıları** bölümünde **durum** **etkin** olarak gösterilir.

    ![Ekran görüntüsü, etkin durumuyla birlikte S O bağlantı ayrıntılarını gösterir.](media/amazon-business-tutorial/sso-connection5.png)

    > [!NOTE]
    > Uygulamayı **SP** tarafından başlatılan modda yapılandırmak istiyorsanız, aşağıdaki adımı tamamlayarak, Azure Portal **ek URL 'Leri ayarla** bölümünün **oturum açma URL 'si** metin kutusunda yukarıdaki ekran görüntüsünden oturum açma URL 'sini yapıştırın. Şu biçimi kullanın:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, B. Simon adlı Azure portal bir test kullanıcısı oluşturacaksınız.

> [!NOTE]
> Yöneticiler gerekirse, yönetim kullanıcılarının kiracısında test kullanıcılarını oluşturması gerekir. Aşağıdaki adımlarda bir test kullanıcısının nasıl oluşturulacağı gösterilmektedir.

1. Azure portal sol bölmeden **Azure Active Directory**' i seçin, **Kullanıcılar**' ı seçin ve ardından **tüm kullanıcılar**' ı seçin.
1. Ekranın üst kısmındaki **Yeni Kullanıcı** ' yı seçin.
1. **Kullanıcı** özellikleri ' nde şu adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. **Kullanıcı adı** alanına, girin username@companydomain.extension . Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı göster** onay kutusunu seçin ve ardından **parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur**’a tıklayın.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure portal bir Azure AD güvenlik grubu oluşturun

1. **Tüm grupları > Azure Active Directory**' ye tıklayın.

    ![Ekran görüntüsü, Azure Active Directory seçili ve Gruplar bölmesinde seçilen tüm gruplar Azure portal menüsünü gösterir.](./media/amazon-business-tutorial/all-groups-tab.png)

1. **Yeni Grup**' a tıklayın:

    ![Ekran görüntüsü yeni Grup düğmesini gösterir.](./media/amazon-business-tutorial/new-group-tab.png)

1. **Grup türü**, **Grup adı**, **Grup açıklaması**, **üyelik türü**' nde Fill yazın. Üyeler ' i seçmek için oka tıklayın ve ardından gruba eklemek istediğiniz üyeyi arayın veya tıklayın. Seçili üyeleri eklemek için **Seç** ' e tıklayın, ardından **Oluştur**' a tıklayın.

    ![Ekran görüntüsü, üye seçme ve dış kullanıcıları davet etme dahil olmak üzere seçenekler içeren Grup bölmesini gösterir.](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Amazon Business 'a erişim vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Amazon Business**' ı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.

    ![Ekran görüntüsü Kullanıcı Ekle düğmesini gösterir.](common/add-assign-user.png)

1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. SAML assertion 'da herhangi bir rol değeri bekliyorsanız, **Rol Seç** iletişim kutusunda, Kullanıcı için listeden uygun rolü seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

    >[!NOTE]
    > Kullanıcıları Azure AD 'de atamadıysanız aşağıdaki hatayı alırsınız.

    ![Ekran görüntüsünde, oturum açmadığınız bir hata iletisi gösterilir.](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Azure portal Azure AD güvenlik grubunu atama

1. Azure portal **Kurumsal uygulamalar**' ı seçin, **tüm uygulamalar**' ı seçin ve ardından **Amazon Business**' u seçin.

    ![Kurumsal uygulamalar dikey penceresi](common/enterprise-applications.png)

2. Uygulamalar listesinde, **Amazon Business**' ı yazın ve seçin.

    ![Uygulamalar listesindeki Amazon Business bağlantısı](common/all-applications.png)

3. Soldaki menüde **Kullanıcılar ve gruplar**' ı seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. **Kullanıcı Ekle**' ye tıklayın.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. Kullanmak istediğiniz güvenlik grubunu arayın, ardından üyeleri seçin bölümüne eklemek için gruba tıklayın. **Seç**' e ve ardından **ata**' ya tıklayın.

    ![Güvenlik grubu ara](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Grubun Azure portal kurumsal uygulamaya başarıyla atandığını uyarılmak için menü çubuğundaki bildirimleri kontrol edin.

### <a name="create-amazon-business-test-user"></a>Amazon Iş testi kullanıcısı oluşturma

Bu bölümde, Amazon Business 'da B. Simon adlı bir Kullanıcı oluşturulur. Amazon Business, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümde sizin için herhangi bir eylem öğesi yok. Bir kullanıcı Amazon Business 'da zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, erişim panelini kullanarak Azure AD çoklu oturum açma yapılandırmanızı test edersiniz.

Erişim panelinde Amazon Business kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Amazon Business 'ta otomatik olarak oturum açmış olmanız gerekir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesi](./tutorial-list.md)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory'de koşullu erişim nedir?](../conditional-access/overview.md)