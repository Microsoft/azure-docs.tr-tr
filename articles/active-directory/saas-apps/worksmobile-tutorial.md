---
title: 'Öğretici: satır ÇALıŞMALARLA Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ve satır ÇALıŞMASı arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: a8b344cd9e4428c3508534edaec4fd98fb67d832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182058"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Öğretici: satır ÇALıŞMALARLA Azure Active Directory tümleştirme

Bu öğreticide, Azure Active Directory (Azure AD) ile HATTıN nasıl tümleştirileceğini öğreneceksiniz.
SATıR tümleştirmesi Azure AD ile birlikte ÇALıŞARAK aşağıdaki avantajları sağlar:

* Azure AD 'de satır ÇALıŞMASıNA erişimi olan denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla ÇALıŞMASı için (çoklu oturum açma) otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD tümleştirmesini satır ÇALıŞMASı ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Bir Azure AD aboneliği. Bir Azure AD ortamınız yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) alabilirsiniz
* SATıR, çoklu oturum açma özellikli abonelik ile ÇALıŞMAKTADıR

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* SATıR ÇALıŞMASı **SP** tarafından başlatılan SSO 'yu destekler

> [!NOTE]
> Bu uygulamanın tanımlayıcısı, tek bir kiracıda yalnızca bir örneğin yapılandırılabilmesini sağlamak için sabit bir dize değeridir.

## <a name="adding-line-works-from-the-gallery"></a>Galeriden satır ekleme

SATıR çalışmasının tümleştirmesini Azure AD 'ye göre yapılandırmak için, galerideki satır çalışmalarından yönetilen SaaS uygulamaları listesine eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **satır çalışması** yazın.
1. Sonuçlar panelinden **satır çalışması** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

Azure AD SSO 'yu bir satır ile yapılandırın ve test edin, **B. Simon** adlı bir test kullanıcısı kullanılarak. SSO 'nun çalışması için, bir Azure AD kullanıcısı ve SATıRDAKI ilgili Kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO 'yu satır ÇALıŞMASı ile yapılandırmak ve test etmek için aşağıdaki adımları gerçekleştirin:

1. **[Azure AD SSO 'Yu yapılandırın](#configure-azure-ad-sso)** -kullanıcılarınızın bu özelliği kullanmasını sağlamak için.
    * Azure AD **[test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** -Britta Simon Ile Azure AD çoklu oturum açma sınamasını test edin.
    * Azure AD **[Test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma özelliğini kullanarak Britta Simon 'u etkinleştirin.
2. **[Satır çalışma SSO 'Su yapılandırma](#configure-line-works-sso)** -uygulama tarafında tek Sign-On ayarlarını yapılandırmak için.
    * **[Satır oluştur test kullanıcısı](#create-line-works-test-user)** -Kullanıcı IÇIN Azure AD gösterimine bağlı olan, satır Içinde Britta Simon 'un bir karşılığı olmalıdır.
3. **[Test SSO](#test-sso)** -yapılandırmanın çalışıp çalışmadığını doğrulamak için.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **SATıRDAKI çalışmalar** uygulama tümleştirmesi sayfasında **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** için Düzenle/kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Oturum açma URL 'si** metin kutusunda, aşağıdaki kalıbı kullanarak bir URL yazın:`https://auth.worksmobile.com/d/login/<domain>/`

    b. **Yanıt URL** 'si metin kutusuna aşağıdaki kalıbı kullanarak bir URL yazın:`https://auth.worksmobile.com/acs/ <domain>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Sign-On URL 'SI ve yanıt URL 'SI ile güncelleştirin. İlgili kişi satırı, değerleri almak için [Destek ekibine](https://line.worksmobile.com/jp/en/contactus/) başvurun. Ayrıca, Azure portal **temel SAML yapılandırması** bölümünde gösterilen desenlere de başvurabilirsiniz.

5. **SAML Ile tek Sign-On ayarlama** sayfasında, **SAML imzalama sertifikası** bölümünde, **sertifika (base64)** ' i gereksiniminize göre ve bilgisayarınıza kaydetmek için **İndir** ' e tıklayın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **SATıRı çalışır duruma ayarla** bölümünde uygun URL 'leri gereksiniminize göre kopyalayın.

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

Bu bölümde, satır ÇALıŞMALARıNıZıN erişimine izin vererek Azure çoklu oturum açma özelliğini kullanmak için B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **satır çalışması**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-line-works-sso"></a>SATıR çalışma SSO 'SU yapılandırma

**Satır ıçı çalışma** tarafında çoklu oturum açmayı yapılandırmak Için lütfen [satırı, SSO BELGELERINI](https://developers.worksmobile.com/jp/document/1001080101) ve bir hat çalışma ayarını yapılandırın.

> [!NOTE]
> İndirilen sertifika dosyasını. CERT 'den. ped 'ye dönüştürmeniz gerekir


### <a name="create-line-works-test-user"></a>SATıR çalışma testi Kullanıcı Oluştur

Bu bölümde, satır içinde Britta Simon adlı bir Kullanıcı oluşturacaksınız. Access [Line, yönetici sayfasına](https://admin.worksmobile.com) sahıptır ve hat çalışma platformunda kullanıcıları ekleyin.

### <a name="test-sso"></a>Test SSO 'SU

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz. 

1. Azure portal içinde **Bu uygulamayı test et** ' e tıklayın. Bu işlem, oturum açma akışını başlatabileceğiniz satır için çalışır oturum açma URL 'sine yeniden yönlendirilir. 

2. SATıRLA doğrudan oturum açma URL 'sine gidin ve oturum açma akışını buradan başlatın.

3. Microsoft Access panel ' i kullanabilirsiniz. Erişim panelinde hat çalışma kutucuğuna tıkladığınızda, bu işlem LINE, oturum açma URL 'sine yeniden yönlendirilir. Erişim paneli hakkında daha fazla bilgi için bkz. [erişim paneline giriş](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Sonraki adımlar

HAT çalışma düzeyini yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).