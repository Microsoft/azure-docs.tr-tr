---
title: 'Öğretici: Palo Alto ağları ile tümleştirme portalı ile Azure Active Directory tümleştirme | Microsoft Docs'
description: Azure Active Directory ile Palo Alto ağları arasında çoklu oturum açmayı nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 1b9c9182b8ffc6e9ea2e05fd4863783f111f81dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727157"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Öğretici: Palo Alto Networks ile tümleştirme portalı Azure Active Directory tümleştirme

Bu öğreticide, Palo Alto ağlarını Azure Active Directory (Azure AD) ile nasıl tümleştirileceğini öğreneceksiniz.
Azure AD ile Palo Alto Networks ile ağları tümleştirme portalı, aşağıdaki avantajları sağlar:

* Azure AD 'de, Palo Alto Networks ile ilgilenen portala erişimi olan bir denetim yapabilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla ağlar arası bir portala (çoklu oturum açma), avuçiçi bilgisayarlara otomatik olarak oturum açmasını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz-Azure portal.

## <a name="prerequisites"></a>Önkoşullar

Azure AD 'yi Palo Alto ağları açıklamalı portalı ile tümleştirmek için aşağıdaki öğeler gereklidir:

* Azure Active Directory aboneliği. Azure AD yoksa, bir [aylık deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)alabilirsiniz.
* Bir Palo Alto Networks-yerleşik Portal çoklu oturum açma (SSO) özellikli abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD çoklu oturum açmayı bir test ortamında yapılandırıp test edersiniz.

* Palo Alto ağları açıklamalı portalı **IDP** tarafından başlatılan SSO 'yu destekler
* Palo Alto ağları açıklamalı portalı **, tam zamanında** Kullanıcı sağlamasını destekler

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Galerideki Palo Alto ağlarını ekleme portalı

Palo Alto ağlarını Azure AD 'ye yönelik olarak yapılandırmak için, Galeriden yönetilen SaaS uygulamaları listenize Palo Alto ağları başlığı altındaki portalı eklemeniz gerekir.

1. Azure portal iş veya okul hesabı ya da kişisel Microsoft hesabı kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Active Directory** hizmeti ' ni seçin.
1. **Kurumsal uygulamalar** ' a gidin ve **tüm uygulamalar**' ı seçin.
1. Yeni uygulama eklemek için **Yeni uygulama**' yı seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **Palo Alto Networks başlıklı portalı** yazın.
1. Sonuçlar panelinden **Palo Alto ağları giriş portalı** ' nı seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 'yu yapılandırma ve test etme

Bu bölümde, **B. Simon** adlı bir test kullanıcısına bağlı olarak Palo Alto Networks Ile Azure AD çoklu oturum açmayı yapılandırıp test edersiniz.
Çoklu oturum açma için, bir Azure AD kullanıcısı ve Palo Alto Networks ile ilgili Kullanıcı arasındaki bağlantı ilişkisinin kurulması gerekir.

Azure AD çoklu oturum açmayı, Palo Alto Networks ile birlikte yapılandırmak ve test etmek için aşağıdaki adımları uygulayın:

1. **[Azure AD SSO 'Yu yapılandırma](#configure-azure-ad-sso)** -kullanıcının bu özelliği kullanmasını etkinleştirin.
    * **[Bir Azure AD test kullanıcısı oluşturun](#create-an-azure-ad-test-user)** ve B. Simon kullanıcısı Ile Azure AD çoklu oturum açma sınamasını yapın.
    * **[Azure AD test kullanıcısına atama](#assign-the-azure-ad-test-user)** -Azure AD çoklu oturum açma kullanmak için B. Simon ayarlayın.
2. **[Palo Alto ağlarını yapılandırma açıklamalı PORTALı SSO](#configure-palo-alto-networks-captive-portal-sso)** -uygulamadaki çoklu oturum açma ayarlarını yapılandırın.
    * Palo Alto Networks Şirket portalı test kullanıcısına, kullanıcının Azure AD gösterimine bağlı olan Palo Alto Networks ile ilgili bir B. Simon 'ın bir karşılığı olacak şekilde **[bir Palo Alto ağı oluşturun](#create-a-palo-alto-networks-captive-portal-test-user)** .
3. **[Test SSO](#test-sso)** -yapılandırmanın çalıştığını doğrulayın.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portal Azure AD SSO 'yu etkinleştirmek için bu adımları izleyin.

1. Azure portal, **Palo Alto ağları başlıklı Portal** uygulama tümleştirmesi sayfasında, **Yönet** bölümünü bulun ve **Çoklu oturum açma**' yı seçin.
1. **Çoklu oturum açma yöntemi seçin** sayfasında **SAML**' yi seçin.
1. **SAML ile çoklu oturum açmayı ayarlama** sayfasında, ayarları düzenlemek IÇIN **temel SAML yapılandırması** kalem simgesine tıklayın.

   ![Temel SAML yapılandırmasını düzenle](common/edit-urls.png)

4. **Temel SAML yapılandırması** bölmesinde aşağıdaki adımları gerçekleştirin:

   1. **Tanımlayıcı** için, düzenine sahıp bir URL girin `https://<customer_firewall_host_name>/SAML20/SP` .

   2. **Yanıt URL 'si** için, düzenine sahıp bir URL girin `https://<customer_firewall_host_name>/SAML20/SP/ACS` .

      > [!NOTE]
      > Bu adımdaki yer tutucu değerlerini gerçek tanımlayıcı ve yanıt URL 'Leriyle güncelleştirin. Gerçek değerleri almak için, [Palo Alto ağları captive portalı istemci destek ekibine](https://support.paloaltonetworks.com/support)başvurun.

5. **SAML Imzalama sertifikası** bölümünde, **Federasyon meta verileri XML**' nun yanındaki **İndir**' i seçin. İndirilen dosyayı bilgisayarınıza kaydedin.

    ![Federasyon meta verileri XML indirme bağlantısı](common/metadataxml.png)

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

Bu bölümde, Azure çoklu oturum açma özelliğini kullanarak Palo Alto Networks şirket portalına erişim izni vererek B. Simon 'u etkinleştireceksiniz.

1. Azure portal **Kurumsal uygulamalar**' ı seçin ve ardından **tüm uygulamalar**' ı seçin.
1. Uygulamalar listesinde, **Palo Alto ağları açıklamalı portalı**' nı seçin.
1. Uygulamanın genel bakış sayfasında **Yönet** bölümünü bulun ve **Kullanıcılar ve gruplar**' ı seçin.
1. **Kullanıcı Ekle**' yi seçin, sonra **atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar** ' ı seçin.
1. **Kullanıcılar ve gruplar** iletişim kutusunda, kullanıcılar listesinden **B. Simon** ' ı seçin ve ardından ekranın alt kısmındaki **Seç** düğmesine tıklayın.
1. Kullanıcılara bir rolün atanmasını bekliyorsanız, **Rol Seç** açılır listesinden bunu seçebilirsiniz. Bu uygulama için ayarlanmış bir rol yoksa, "varsayılan erişim" rolü seçili olduğunu görürsünüz.
1. **Atama Ekle** Iletişim kutusunda **ata** düğmesine tıklayın.

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Palo Alto ağları açıklamalı portalı SSO 'yu yapılandırma

Ardından, Palo Alto Networks Şirket portalı 'nda çoklu oturum açma ayarlayın:

1. Farklı bir tarayıcı penceresinde, bir yönetici olarak Palo Alto Networks Web sitesinde oturum açın.

2. **Cihaz** sekmesini seçin.

    ![Palo Alto Networks Web sitesi cihaz sekmesi](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Menüsünde **SAML kimlik sağlayıcısı**' nı seçin ve ardından **içeri aktar**' ı seçin.

    ![Içeri aktar düğmesi](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. **SAML kimlik sağlayıcısı sunucu profili Içeri aktarma** iletişim kutusunda aşağıdaki adımları izleyin:

    ![Palo Alto ağlarını çoklu oturum açma yapılandırma](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. **Profil adı** Için **Azuread-captiveportal** gibi bir ad girin.
    
    2. **Kimlik sağlayıcısı meta verileri**' nin yanında, **Araştır**' ı seçin. Azure portal indirdiğiniz metadata.xml dosyasını seçin.
    
    3. **Tamam**’ı seçin.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Bir Palo Alto ağı oluşturma yönetim portalı test kullanıcısı

Ardından, Palo Alto Networks Portal 'da *Britta Simon* adlı bir kullanıcı oluşturun. Palo Alto ağları captive portalı, varsayılan olarak etkinleştirilen tam zamanında Kullanıcı sağlamayı destekler. Bu bölümdeki herhangi bir görevi gerçekleştirmeniz gerekmez. Bir Kullanıcı Palo Alto ağları giriş portalı 'nda zaten mevcut değilse, kimlik doğrulamasından sonra yeni bir tane oluşturulur.

> [!NOTE]
> Bir kullanıcıyı el ile oluşturmak istiyorsanız, [Palo Alto Networks ana portalı istemci destek ekibine](https://support.paloaltonetworks.com/support)başvurun.

## <a name="test-sso"></a>Test SSO 'SU 

Bu bölümde, Azure AD çoklu oturum açma yapılandırmanızı aşağıdaki seçeneklerle test edersiniz.

* Azure portal ' de bu uygulamayı test et ' e tıklayın ve SSO 'yu ayarladığınız Palo Alto ağları ana portalında otomatik olarak oturum açmış olmanız gerekir

* Microsoft My Apps ' i kullanabilirsiniz. Uygulamalarım içindeki Palo Alto ağları 'nın giriş portalı kutucuğuna tıkladığınızda, SSO 'yu ayarladığınız Palo Alto ağları giriş portalı 'nda otomatik olarak oturum açmış olmanız gerekir. Uygulamalarım hakkında daha fazla bilgi için bkz. [uygulamalarıma giriş](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Sonraki adımlar

Palo Alto Networks Şirket portalı 'Nı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak ayıklanmasını ve zaman korumasını koruyan oturum denetimini zorunlu kılabilirsiniz. Oturum denetimi koşullu erişimden genişletiliyor. [Microsoft Cloud App Security ile oturum denetimini nasıl zorlayacağınızı öğrenin](/cloud-app-security/proxy-deployment-any-app).