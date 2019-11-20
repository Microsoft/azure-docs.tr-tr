---
title: Atlaseli Jira/Confluence Yönetici Kılavuzu-Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) ile Atlaseli Jira ve Confluence kullanımı için Yönetici Kılavuzu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8679f9a03fded546db68f058bca716ba053aa0fe
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161202"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Azure Active Directory için atlasme Jira ve Confluence Yönetici Kılavuzu

## <a name="overview"></a>Genel Bakış

Azure Active Directory (Azure AD) çoklu oturum açma (SSO) eklentisi, Microsoft Azure AD müşterilerin Atlasme Jira ve Confsunucu tabanlı ürünlerde oturum açmasını sağlamak için iş veya okul hesaplarını kullanmasına olanak sağlar. SAML 2,0 tabanlı SSO 'yu uygular.

## <a name="how-it-works"></a>Nasıl çalışır

Kullanıcılar Atlaseli Jira veya Confluence uygulamasında oturum açmak istediklerinde, oturum açma sayfasında **Azure AD 'ye oturum aç** düğmesini görür. Bunu seçerken, Azure AD organizasyonu oturum açma sayfasını (yani, iş veya okul hesabı) kullanarak oturum açması gerekir.

Kullanıcıların kimliği doğrulandıktan sonra, uygulamada oturum açabilmeleri gerekir. İş veya okul hesabı için KIMLIK ve parolayla kimlik doğrulaması yapılmış olmaları durumunda, doğrudan uygulamada oturum açın. 

Oturum açma, Jira ve Confluence üzerinde çalışmaktadır. Kullanıcılar Jira uygulamasında oturum açtıysa ve Confluence aynı tarayıcı penceresinde açılırsa, diğer uygulama için kimlik bilgilerini sağlamak zorunda kalmaz. 

Kullanıcılar ayrıca iş veya okul hesabı kapsamındaki uygulamalarım aracılığıyla Atlasme ürününe de alabilirler. Kimlik bilgileri istenmeden oturum açmış olmaları gerekir.

> [!NOTE]
> Kullanıcı hazırlama, eklenti aracılığıyla yapılmaz.

## <a name="audience"></a>Hedef Kitle

Jira ve Confluence yöneticileri, Azure AD 'yi kullanarak SSO 'yu etkinleştirmek için eklentiyi kullanabilir.

## <a name="assumptions"></a>Varsayımlar

* Jira ve Confluence örnekleri HTTPS etkindir.
* Kullanıcılar zaten Jira veya Confluence içinde oluşturulmuş.
* Kullanıcıların Jira veya Confluence 'de atanan rolleri vardır.
* Yöneticilerin, eklentiyi yapılandırmak için gereken bilgilere erişimi vardır.
* Jira veya Confluence, şirket ağı dışında da kullanılabilir.
* Eklenti yalnızca Jira ve Confluence 'ın şirket içi sürümü ile birlikte çalışarak.

## <a name="prerequisites"></a>Önkoşullar

Eklentiyi yüklemeden önce aşağıdaki bilgileri aklınızda edin:

* Jira ve Confluence, Windows 64 bit sürümüne yüklenmiştir.
* Jira ve Confluence sürümleri HTTPS etkindir.
* Internet 'te Jira ve Confluence vardır.
* Jira ve Confluence için yönetici kimlik bilgileri yerinde.
* Azure AD için yönetici kimlik bilgileri yerinde.
* WebSudo, Jira 'da ve Confluence 'de devre dışı bırakıldı.

## <a name="supported-versions-of-jira-and-confluence"></a>Desteklenen Jira ve Confluence sürümleri

Eklenti, Jira ve Confluence 'ın aşağıdaki sürümlerini destekler:

* Jira Core ve Software: 6,0-7,12
* Jira Service Desk: 3.0.0 to 3.5.0
* JIRA Ayrıca 5,2 'yi destekler. Daha fazla ayrıntı için [Microsoft Azure Active Directory JIRA 5,2 için çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial) 'yı tıklatın
* Confluence: 5,0-5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Yükleme

Eklentiyi yüklemek için şu adımları izleyin:

1. Jira veya Confluence örneğiniz için yönetici olarak oturum açın.

2. Jira/Confluence yönetim konsoluna gidin **ve eklentiler ' i seçin.**

3. Microsoft Indirme Merkezi ' nden [Jira/ için MICROSOFT SAML SSO eklentisini](https://www.microsoft.com/download/details.aspx?id=56506) , [Confluence IÇIN Microsoft SAML SSO eklentisi](https://www.microsoft.com/download/details.aspx?id=56503)' ni indirin.

   Eklentinin uygun sürümü arama sonuçlarında görünür.

4. Eklentiyi seçin ve evrensel Eklenti Yöneticisi (UPı) onu yüklenir.

Eklenti yüklendikten sonra, eklentileri **Yönet**' in **Kullanıcı tarafından yüklenen eklentiler** bölümünde görüntülenir.

## <a name="plug-in-configuration"></a>Eklenti yapılandırması

Eklentiyi kullanmaya başlamadan önce, onu yapılandırmanız gerekir. Eklentiyi seçin, **Yapılandır** düğmesini seçin ve yapılandırma ayrıntılarını sağlayın.

Aşağıdaki görüntüde hem Jira hem de Confluence içindeki yapılandırma ekranı gösterilmektedir:

![Eklenti yapılandırma ekranı](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Meta veri URL 'si**: Azure AD 'den Federasyon meta verilerini almaya yönelik URL.

* **Tanımlayıcılar**: Azure AD 'nin isteğin kaynağını doğrulamak IÇIN kullandığı URL. Azure AD 'de **tanımlayıcı** öğeyle eşlenir. Eklenti bu URL 'YI otomatik olarak https:// *\<etki alanı: bağlantı noktası >* /olarak türetir.

* **Yanıt URL 'si**: SAML oturum açma işlemini başlatan kimlik sağlayıcınızdaki (IDP) yanıt URL 'si. Azure AD 'de **yanıt URL 'si** öğesine eşlenir. Eklenti bu URL 'YI otomatik olarak https:// *\<etki alanı: bağlantı noktası >* /plugins/servlet/SAML/autholarak türetir.

* **Oturum açma URL 'si**: SAML oturum açma Işlemini Başlatan IDP 'inizdeki oturum açma URL 'si. Azure AD 'de **oturum açma** öğesiyle eşlenir. Eklenti bu URL 'YI otomatik olarak https:// *\<etki alanı: bağlantı noktası >* /plugins/servlet/SAML/autholarak türetir.

* **IDP VARLıK kimliği**: IDP 'nizin KULLANDıĞı varlık kimliği. Meta veri URL 'SI çözümlendiğinde bu kutu doldurulur.

* **Oturum açma URL 'si**: IDP 'nizden oturum açma URL 'si. Bu kutu, meta veri URL 'SI çözümlendiğinde Azure AD 'den doldurulur.

* **Oturum kapatma URL 'si**: IDP 'nizden oturum kapatma URL 'si. Bu kutu, meta veri URL 'SI çözümlendiğinde Azure AD 'den doldurulur.

* **X. 509.440 sertifikası**: IDP 'nin X. 509.440 sertifikasıdır. Bu kutu, meta veri URL 'SI çözümlendiğinde Azure AD 'den doldurulur.

* **Oturum açma düğmesi adı**: kuruluşunuzun kullanıcıların oturum açma sayfasında görmesini istediğini oturum açma düğmesinin adı.

* **SAML Kullanıcı kimliği konumları**: SAML yanıtında Jira veya Confluence Kullanıcı kimliğinin beklenen konumu. Bu, **NameID** içinde veya özel bir öznitelik adında olabilir.

* **Öznitelik adı**: Kullanıcı kimliğinin beklenildiği özniteliğin adı.

* **Giriş bölgesi bulmayı etkinleştir**: Şirket Active Directory Federasyon Hizmetleri (AD FS) (AD FS) tabanlı oturum açma kullanıyorsa yapılacak seçim.

* **Etki alanı adı**: oturum açma AD FS tabanlı etki alanı adı.

* **Çoklu**oturum açmayı etkinleştir: Kullanıcı Jira veya confluya oturumunu KAPATTıĞıNDA Azure AD 'den oturumu kapatmak istiyorsanız yapılacak seçim.

## <a name="troubleshooting"></a>Sorun giderme

* **Birden çok sertifika hatası**alıyorsunuz: Azure AD 'de oturum açın ve uygulamada bulunan birden çok sertifikayı kaldırın. Yalnızca bir sertifikanın bulunduğundan emin olun.

* **Azure AD 'de bir sertifika sona ermek üzere**: Eklentiler, sertifikanın otomatik olarak geçişine karşı bir işlem gerçekleştirir. Bir sertifikanın kullanım süreleri dolduğunda, yeni bir sertifika etkin olarak işaretlenmelidir ve kullanılmayan sertifikalar silinmelidir. Bu senaryoda bir Kullanıcı Jira 'da oturum açmaya çalıştığında, eklenti yeni sertifikayı getirir ve kaydeder.

* **WebSudo 'ı devre dışı bırakmak istiyorsunuz (güvenli Yönetici oturumunu devre dışı bırak)** :

  * Jira için güvenli yönetici oturumları (diğer bir deyişle, yönetim işlevlerine erişmeden önce parola onayı) varsayılan olarak etkinleştirilir. Jira örneğinizdeki bu özelliği kaldırmak istiyorsanız, Jira-config. Properties dosyanızda aşağıdaki satırı belirtin: `ira.websudo.is.disabled = true`

  * Confluence için, [Confluence support sitesindeki](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)adımları izleyin.

* **Meta veri URL 'si tarafından doldurulması beklenen alanlar doldurulmuyor**:

  * URL 'nin doğru olup olmadığını denetleyin. Doğru kiracı ve uygulama KIMLIĞINI eşleştirdiyseniz denetleyin.

  * URL 'YI bir tarayıcıda girin ve Federasyon meta veri XML 'sini alıp almadıysanız bakın.

* **Bir iç sunucu hatası var**: yüklemenin günlük dizinindeki günlükleri gözden geçirin. Kullanıcı Azure AD SSO 'yu kullanarak oturum açmaya çalışırken hata alıyorsanız, günlükleri destek ekibi ile paylaşabilirsiniz.

* **Kullanıcı oturum açmayı denediğinde bir "Kullanıcı kimliği bulunamadı" hatası var**: Jira veya Confluence IÇINDE Kullanıcı kimliği oluşturma.

* **Azure AD 'de "uygulama bulunamadı" hatası**: ilgili URL 'nın Azure AD 'de uygulamayla eşlenip eşlenmediğinden bkz.

* **Desteğe ihtiyacınız vardır**: [Azure AD SSO tümleştirme ekibine](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)ulaşın. Takım, 24-48 iş saatlerinde yanıt verir.

  Ayrıca, Azure portal kanal aracılığıyla Microsoft ile destek bileti de oluşturabilirsiniz.

## <a name="plug-in-faq"></a>Eklenti SSS

Bu eklentiyle ilgili herhangi bir sorgunuz varsa lütfen SSS bölümüne bakın.

### <a name="what-does-the-plug-in-do"></a>Eklenti ne yapar?

Eklenti, Atlaseli Jira için çoklu oturum açma (SSO) özelliği sağlar (Jira Core, Jira Software, Jira Service Desk dahil) ve şirket içi yazılım yazılımı. Eklenti, kimlik sağlayıcısı (IDP) olarak Azure Active Directory (Azure AD) ile birlikte kullanılabilir.

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Eklentiye hangi Atlasme ürünleri çalışıyor?

Eklenti, Jira ve Confluence 'ın şirket içi sürümleriyle birlikte çalışarak.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Eklenti bulut sürümlerinde çalışıyor mu?

Hayır. Eklenti yalnızca Jira ve Confluence 'ın şirket içi sürümlerini destekler.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Jira ve Confluence 'ın hangi sürümleri eklentiyi destekler?

Eklenti bu sürümleri destekler:

* Jira Core ve Software: 6,0-7,12
* Jira Service Desk: 3.0.0 to 3.5.0
* JIRA Ayrıca 5,2 'yi destekler. Daha fazla ayrıntı için [Microsoft Azure Active Directory JIRA 5,2 için çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial) 'yı tıklatın
* Confluence: 5,0-5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Eklenti ücretsizdir veya ödensin mi?

Bu, ücretsiz bir eklentidir.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Eklentiyi dağıttıktan sonra Jira veya Confluence 'ı yeniden başlatmem gerekir mi?

Yeniden başlatma gerekli değildir. Eklentiyi hemen kullanmaya başlayabilirsiniz.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Eklentiye yönelik destek almak Nasıl yaparım? mı?

Bu eklenti için gereken her türlü destek için [Azure AD SSO tümleştirme ekibine](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) ulaşabilirsiniz. Takım, 24-48 iş saatlerinde yanıt verir.

Ayrıca, Azure portal kanal aracılığıyla Microsoft ile destek bileti de oluşturabilirsiniz.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Eklenti, Jira ve Confluence 'ın Mac veya Ubuntu yüklemesinde çalışıyor mu?

Eklentiyi yalnızca Jira ve Confluence 'ın 64 bitlik Windows Server yüklemelerinde test ediyoruz.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Eklenti, Azure AD 'den farklı IDPs ile mi çalışıyor?

Hayır. Yalnızca Azure AD ile birlikte kullanılabilir.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Eklenti hangi SAML sürümü ile çalışıyor?

SAML 2,0 ile birlikte çalışarak.

### <a name="does-the-plug-in-do-user-provisioning"></a>Eklenti Kullanıcı sağlamasını yapar mi?

Hayır. Eklenti yalnızca SAML 2,0 tabanlı SSO sağlar. Kullanıcının SSO oturum açmadan önce uygulamada sağlanması gerekmez.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Eklenti Jira ve Confluence 'ın küme sürümlerini destekliyor mu?

Hayır. Eklenti, Jira ve Confluence 'ın şirket içi sürümleriyle birlikte çalışarak.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Eklenti, Jira ve Confluence 'ın HTTP sürümleriyle birlikte çalışıyor mu?

Hayır. Eklenti yalnızca HTTPS etkin yüklemelerle birlikte kullanılabilir.
