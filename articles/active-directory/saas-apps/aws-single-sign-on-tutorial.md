---
title: 'Öğretici: AWS çoklu oturum açma ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory | Microsoft Docs'
description: Azure Active Directory ve AWS çoklu oturum açma arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 2d0b9e45dc5de0cd4550cf4b9f944fd33ebd7e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720737"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Öğretici: AWS çoklu oturum açma ile çoklu oturum açma (SSO) Tümleştirmesi Azure Active Directory

Bu öğreticide, AWS çoklu oturum açmayı Azure Active Directory (Azure AD) ile tümleştirmeyi öğreneceksiniz. AWS çoklu oturum açmayı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Azure AD 'de AWS çoklu oturum açma erişimine sahip olan denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla birlikte AWS çoklu oturum açma 'ya otomatik olarak oturum açmasını sağlayın.
* Hesaplarınızı tek bir merkezi konumda yönetin-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için aşağıdaki öğeler gereklidir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* AWS çoklu oturum açma (SSO) etkin aboneliği.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD SSO 'yu bir test ortamında yapılandırıp test edersiniz.

* AWS çoklu oturum açma **, SP ve ıDP** tarafından başlatılan SSO 'yu destekler.

* AWS çoklu oturum açma [**Otomatik Kullanıcı sağlamayı**](./aws-single-sign-on-provisioning-tutorial.md)destekler.

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Galeriden AWS çoklu oturum açma ekleme

AWS çoklu oturum açma 'nın tümleştirmesini Azure AD 'de yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize AWS çoklu oturum açma eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **AWS çoklu oturum açma** yazın.
1. Sonuçlar panelinden **AWS çoklu oturum açma** 'yı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>AWS çoklu oturum açma için Azure AD SSO 'yu yapılandırın ve test edin

**B. Simon** adlı bir test kullanıcısı kullanarak AWS çoklu oturum açma Ile Azure AD SSO 'yu yapılandırın ve test edin. SSO 'nun çalışması için, AWS çoklu oturum açma içindeki bir Azure AD kullanıcısı ve ilgili Kullanıcı arasında bir bağlantı ilişkisi oluşturmanız gerekir.

AWS çoklu oturum açma ile Azure AD SSO 'yu yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    1. Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -B. Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    1. Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştirmek için.
1. **[AWS çoklu oturum açma SSO 'Yu yapılandırma](#configure-aws-single-sign-on-sso)** -uygulama tarafında çoklu oturum açma ayarlarını yapılandırmak için.
    1. AWS çoklu **[oturum açma testi kullanıcısını](#create-aws-single-sign-on-test-user)** , kullanıcının Azure AD gösterimine bağlı olan AWS çoklu oturum açma bölümünde B. Simon 'a sahip olmak için oluşturun.
1. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **AWS çoklu oturum açma** uygulaması tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

1. **Hizmet sağlayıcısı meta verileri dosyanız** varsa, **temel SAML yapılandırması** bölümünde aşağıdaki adımları uygulayın:

    a. **Meta veri dosyasını karşıya yükle**' ye tıklayın.

    b. Dosya **logosu** ' na tıklayarak **AWS çoklu oturum açma SSO** (nokta 8) yapılandırma bölümünde indirdiğiniz meta veri dosyasını seçin ve **Ekle**' ye tıklayın.

    ![image2](common/browse-upload-metadata.png)

    c. Meta veri dosyası başarıyla karşıya yüklendikten sonra **tanımlayıcı** ve **yanıt URL** değerleri temel SAML yapılandırması bölümünde otomatik olarak doldurulur:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > **Tanımlayıcı** ve **yanıt URL 'si** değerleri otomatik olarak almıyorsanız, değerleri gereksinimlerinize göre el ile girin.

1. **Hizmet sağlayıcısı meta verileri dosyanız** yoksa, **temel SAML yapılandırması** bölümünde aşağıdaki adımları uygulayın, uygulamayı **IDP** başlatılmış modda yapılandırmak istiyorsanız aşağıdaki alanlar için değerleri girin:

    a. **Tanımlayıcı** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. **Yanıt URL 'si** metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Uygulamayı **SP** tarafından başlatılan modda yapılandırmak Istiyorsanız **ek URL 'ler ayarla** ' ya tıklayın ve aşağıdaki adımı gerçekleştirin:

    **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek tanımlayıcı, yanıt URL 'SI ve oturum açma URL 'SI ile güncelleştirin. Bu değerleri almak için [AWS çoklu oturum açma istemci desteği ekibine](mailto:aws-sso-partners@amazon.com) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

1. AWS çoklu oturum açma uygulaması, SAML belirteci öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML onayları bekliyor. Aşağıdaki ekran görüntüsünde varsayılan özniteliklerin listesi gösterilmektedir.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > AWS SSO 'da ATZı etkinse, ek öznitelikler doğrudan AWS hesaplarına oturum etiketleri olarak geçirilebilir.

1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, **SAML Imzalama sertifikası** bölümünde **sertifika bulun (base64)** ve sertifikayı indirip bilgisayarınıza kaydetmek için **İndir** ' i seçin.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **AWS çoklu oturum açma ayarla** bölümünde, gereksiniminize göre uygun URL 'leri kopyalayın.

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

Bu bölümde, AWS çoklu oturum açma erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **AWS çoklu oturum açma**' yı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-aws-single-sign-on-sso"></a>AWS çoklu oturum açma SSO 'yu yapılandırma

1. AWS çoklu oturum açma içindeki yapılandırmayı otomatik hale getirmek için, **uzantıyı yüklemek** üzere **uygulamalar güvenli oturum açma tarayıcı uzantısı** ' nı yüklemeniz gerekir.

    ![Uygulamalarım uzantısı](common/install-myappssecure-extension.png)

2. Tarayıcıya uzantı ekledikten sonra, **AWS çoklu oturum açmayı ayarla** ' ya tıklayarak sizi AWS çoklu oturum açma uygulamasına yönlendirebilirsiniz. Buradan, AWS çoklu oturum açma 'da oturum açmak için yönetici kimlik bilgilerini sağlayın. Tarayıcı uzantısı, uygulamayı sizin için otomatik olarak yapılandırır ve 3-10 adımlarını otomatikleştirecektir.

    ![Kurulum yapılandırması](common/setup-sso.png)

3. AWS çoklu oturum açmayı el ile ayarlamak istiyorsanız, farklı bir Web tarayıcısı penceresinde, AWS çoklu oturum açma şirket sitenizde yönetici olarak oturum açın.

1. **Hizmetler-> güvenlik, kimlik & uyumluluğu-> AWS çoklu oturum açma**'ya gidin.
2. Sol gezinti bölmesinde **Ayarlar**' ı seçin.
3. **Ayarlar** sayfasında **kimlik kaynağını** bulun ve **Değiştir**' e tıklayın.

    ![Kimlik kaynağı değişiklik hizmeti ekran görüntüsü](./media/aws-single-sign-on-tutorial/settings.png)

4. Kimlik kaynağını Değiştir ' de, **dış kimlik sağlayıcısı**' nı seçin.

    
    ![Dış kimlik sağlayıcısı bölümü seçme ekran görüntüsü](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. **Dış kimlik sağlayıcısını Yapılandır** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Meta verileri indir ve karşıya yükle bölümünün ekran görüntüsü](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. **Hizmet sağlayıcısı meta verileri** bölümünde **AWS SSO SAML meta verilerini** bulun ve meta veri dosyasını indirmek Için **meta veri dosyasını indir** ' i seçin ve bu meta veri dosyasını bilgisayarınıza kaydedin ve Azure Portal karşıya yüklemek için kullanın.

    b. **AWS SSO oturum açma URL 'si** değerini kopyalayın, bu değeri Azure Portal **temel SAML yapılandırması bölümünde** yer **alan URL 'yi aç** metin kutusuna yapıştırın.

    c. **Kimlik sağlayıcısı meta verileri** bölümünde, Azure Portal indirdiğiniz meta veri dosyasını karşıya yüklemek için **Araştır** ' ı seçin.

    d. Ileri 'yi seçin **: gözden geçir**.

8. Metin kutusuna, kimlik kaynağını değiştirmek için **kabul et** yazın.

    ![Yapılandırmayı onaylama ekran görüntüsü](./media/aws-single-sign-on-tutorial/accept.png)

9. **Kimlik kaynağını Değiştir**' e tıklayın.

### <a name="create-aws-single-sign-on-test-user"></a>AWS çoklu oturum açma testi Kullanıcı Oluştur

1. **AWS SSO konsolunu** açın.

2. Sol gezinti bölmesinde, **Kullanıcılar**' ı seçin.

3. Kullanıcılar sayfasında, **Kullanıcı Ekle**' yi seçin.

4. Kullanıcı Ekle sayfasında, şu adımları izleyin:

    a. **Kullanıcı adı** alanına B. Simon girin.

    b. **E-posta adresi** alanına, girin `username@companydomain.extension` . Örneğin, `B.Simon@contoso.com`.

    c. **E-posta adresini Onayla** alanına, önceki adımdan e-posta adresini yeniden girin.

    d. Ad alanına yazın `Jane` .

    e. Soyadı alanına, girin `Doe` .

    f. Görünen ad alanına, girin `Jane Doe` .

    örneğin: **İleri: gruplar**' ı seçin.

    > [!NOTE]
    > AWS SSO 'ya girilen kullanıcı adının kullanıcının Azure AD oturum açma adıyla eşleştiğinden emin olun. Bu, herhangi bir kimlik doğrulama sorununu önlemenize yardımcı olur.

5. **Kullanıcı ekle**’yi seçin.
6. Ardından, kullanıcıyı AWS hesabınıza atayacaksınız. Bunu yapmak için AWS SSO konsolunun sol gezinti bölmesinde **AWS hesapları**' nı seçin.
7. AWS hesapları sayfasında, AWS organizasyonu sekmesini seçin, kullanıcıya atamak istediğiniz AWS hesabının yanındaki kutuyu işaretleyin. Ardından **Kullanıcı ata**' yı seçin.
8. Kullanıcıları ata sayfasında, B. Simon kullanıcısının yanındaki kutuyu bulun ve işaretleyin. Ardından **İleri: izin kümeleri**' ni seçin.
9. İzin kümelerini Seç bölümünde, B. Simon kullanıcısına atamak istediğiniz izin kümesinin yanındaki kutuyu işaretleyin. Mevcut bir izin kümesi yoksa, **yeni izin kümesi oluştur**' u seçin.

    > [!NOTE]
    > İzin kümeleri, kullanıcıların ve grupların bir AWS hesabına sahip olduğu erişim düzeyini tanımlar. İzin kümeleri hakkında daha fazla bilgi için bkz. AWS SSO **Izin kümeleri** sayfası.
10. **Son**’u seçin.

> [!NOTE]
> AWS çoklu oturum açma işlemi otomatik Kullanıcı sağlamayı da destekler, Ayrıca, otomatik Kullanıcı sağlamayı yapılandırma hakkında daha [fazla ayrıntı bulabilirsiniz](./aws-single-sign-on-provisioning-tutorial.md) .

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

#### <a name="sp-initiated"></a>SP başlatıldı:

* Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu, oturum açma akışını başlatabileceğiniz AWS çoklu oturum açma URL 'sine yeniden yönlendirilir.  

* AWS çoklu oturum açma URL 'sine doğrudan gidin ve oturum açma akışını buradan başlatın.

#### <a name="idp-initiated"></a>IDP başlatıldı:

* Azure portal **Bu uygulamayı test et** ' e tıklayın ve SSO 'yu ayarladığınız AWS çoklu oturum açma tarafında otomatik olarak oturum açmış olmanız gerekir 

Uygulamayı dilediğiniz modda test etmek için Microsoft My Apps ' i de kullanabilirsiniz. Uygulamamda AWS çoklu oturum açma kutucuğuna tıkladığınızda, SP modunda yapılandırıldıysa oturum açma akışını başlatmak için uygulama oturum açma sayfasına yönlendirilirsiniz ve ıDP modunda yapılandırıldıysa, SSO 'yu ayarladığınız AWS çoklu oturum açma için otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

AWS çoklu oturum açmayı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).