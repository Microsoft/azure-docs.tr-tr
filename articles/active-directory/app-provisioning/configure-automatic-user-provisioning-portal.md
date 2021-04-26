---
title: Azure Active Directory 'de kurumsal uygulamalar için Kullanıcı sağlama yönetimi
description: Azure Active Directory kullanarak kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetmeyi öğrenin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5dceeb11ed9a4d6af88650a6146f58db412748d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579425"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Azure portal kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme

Bu makalede, destekleyen uygulamalar için otomatik Kullanıcı hesabı sağlamayı ve sağlamayı kaldırmayı yönetmeye yönelik genel adımlar açıklanmaktadır. *Kullanıcı hesabı sağlama* , bir uygulamanın yerel kullanıcı profili deposundaki kullanıcı hesabı kayıtlarını oluşturma, güncelleştirme ve/veya devre dışı bırakma işlemidir. Çoğu bulut ve SaaS uygulaması kullanıcı rolünü ve izinlerini kullanıcının kendi yerel kullanıcı profili deposunda depolar ve bu tür bir Kullanıcı kaydının, çoklu oturum açma ve çalışma için Kullanıcı yerel deposunda bulunması *gerekir* . Otomatik Kullanıcı hesabı sağlama hakkında daha fazla bilgi için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD), Azure AD ile otomatik sağlama için etkinleştirilen binlerce önceden tümleştirilmiş uygulamayı içeren bir galersahiptir. [SaaS uygulamalarını Azure Active Directory ile tümleştirme hakkında öğreticiler listesinde](../saas-apps/tutorial-list.md)uygulamanıza özgü sağlama kurulum öğreticisini bularak başlatmanız gerekir. Büyük olasılıkla, sağlama bağlantısı oluşturmak için hem uygulamayı hem de Azure AD 'yi yapılandırmaya yönelik adım adım yönergeler bulacaksınız.

## <a name="finding-your-apps-in-the-portal"></a>Portaldaki uygulamalarınızı bulma

Bir dizinde çoklu oturum açma için yapılandırılmış tüm uygulamaları görüntülemek ve yönetmek için Azure Active Directory portalını kullanın. Kurumsal uygulamalar, kuruluşunuz içinde dağıtılan ve kullanılan uygulamalardır. Kurumsal uygulamalarınızı görüntülemek ve yönetmek için aşağıdaki adımları izleyin:

1. [Azure Active Directory portalını](https://aad.portal.azure.com)açın.
1. Sol bölmeden **Kurumsal uygulamalar** ' ı seçin. Galeriden eklenen uygulamalar dahil olmak üzere, yapılandırılan tüm uygulamaların bir listesi gösterilir.
1. Kaynak bölmesini yüklemek için herhangi bir uygulamayı seçin, burada raporları görüntüleyebilir ve uygulama ayarlarını yönetebilirsiniz.
1. Seçili uygulama için Kullanıcı hesabı sağlama ayarlarını yönetmek üzere **sağlamayı** seçin.

   ![Kullanıcı hesabı sağlama ayarlarını yönetmek için sağlama ekranı](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Sağlama modları

**Sağlama** bölmesi, bir kurumsal uygulama için desteklenen sağlama modlarını gösteren bir **mod** menüsüyle başlar ve bunları yapılandırmanıza olanak tanır. Mevcut seçenekler şunlardır:

* **Otomatik** -Bu seçenek, Azure AD bu uygulamaya yönelik Kullanıcı HESAPLARıNıN otomatik API tabanlı sağlamasını veya sağlamasını destekliyorsa görüntülenir. Yöneticilere yardımcı olacak bir arabirim göstermek için bu modu seçin:

  * Azure AD 'yi uygulamanın kullanıcı yönetimi API 'sine bağlanacak şekilde yapılandırma
  * Kullanıcı hesabı verilerinin Azure AD ile uygulama arasında nasıl akacağınızı tanımlayan hesap eşlemeleri ve iş akışları oluşturun
  * Azure AD sağlama hizmetini yönetme

* **El ile** -Bu seçenek, Azure AD tarafından bu uygulamaya otomatik olarak Kullanıcı hesaplarının sağlanması desteği yoksa gösterilir. Bu durumda, uygulamada depolanan kullanıcı hesabı kayıtları, bu uygulama tarafından sunulan Kullanıcı yönetimi ve sağlama özelliklerine (SAML Just-In-Time sağlamayı içerebilir) bağlı olarak bir dış işlem kullanılarak yönetilmelidir.

## <a name="configuring-automatic-user-account-provisioning"></a>Otomatik Kullanıcı hesabı sağlamayı yapılandırma

Yönetici kimlik bilgileri, eşlemeler, başlatma ve durdurma ve eşitleme için ayarları belirtmek üzere **Otomatik** seçeneğini belirleyin.

### <a name="admin-credentials"></a>Yönetici kimlik bilgileri

Azure AD 'nin uygulamanın kullanıcı yönetim API 'sine bağlanması için gereken kimlik bilgilerini girmek üzere **yönetici kimlik bilgileri** ' ni genişletin. Gerekli giriş uygulamaya bağlı olarak değişir. Belirli uygulamalar için kimlik bilgisi türleri ve gereksinimleri hakkında bilgi edinmek için ilgili [uygulamanın yapılandırma öğreticisine](user-provisioning.md)bakın.

Sağlanan kimlik bilgilerini kullanarak Azure AD 'nin uygulamanın sağlama uygulamasına bağlanmasını sağlayarak kimlik bilgilerini test etmek için **Bağlantıyı Sına** ' yı seçin.

### <a name="mappings"></a>Eşlemeler

Kullanıcı hesapları sağlandığında veya güncelleştirilirken Azure AD ile hedef uygulama arasında akan Kullanıcı özniteliklerini görüntülemek ve düzenlemek için **eşlemeler** ' i genişletin.

Azure AD Kullanıcı nesneleri ile her bir SaaS uygulamasının Kullanıcı nesneleri arasında önceden yapılandırılmış bir eşlemeler kümesi vardır. Bazı uygulamalar Grup nesnelerini de yönetebilir. Tabloda bir eşleme seçerek eşleme düzenleyicisini açın, burada bunları görüntüleyebilir ve özelleştirebilirsiniz.

Desteklenen özelleştirmeler şunlardır:

* SaaS uygulamasının kullanıcı nesnesine Azure AD Kullanıcı nesnesi gibi belirli nesneler için eşlemeleri etkinleştirme ve devre dışı bırakma.
* Azure AD kullanıcı nesnesinden uygulamanın kullanıcı nesnesine akan öznitelikleri düzenleyebilirsiniz. Öznitelik eşlemesi hakkında daha fazla bilgi için bkz. [öznitelik eşleme türlerini anlama](customize-application-attributes.md#understanding-attribute-mapping-types).
* Azure AD 'nin hedeflenen uygulama üzerinde çalıştığı sağlama eylemlerini filtreleme. Azure AD 'nin nesneleri tamamen eşitlemesini yapmak yerine, çalıştırılan eylemleri sınırlayabilirsiniz.

  Örneğin yalnızca **güncelleştirme** ' yi seçin ve Azure AD yalnızca bir uygulamadaki mevcut kullanıcı hesaplarını güncelleştirir ancak yenilerini oluşturmaz. Yalnızca **Oluştur** ' u seçin ve Azure yalnızca yeni kullanıcı hesapları oluşturur, ancak varolanları güncelleştirmez. Bu özellik, yöneticilerin hesap oluşturma ve güncelleştirme iş akışları için farklı eşlemeler oluşturmalarına olanak tanır.

* Yeni bir öznitelik eşlemesi ekleniyor. **Öznitelik eşleme** bölmesinin alt kısmındaki **Yeni eşleme Ekle** ' yi seçin. Yeni eşlemeyi listeye eklemek için **özniteliği Düzenle** formunu doldurun ve **Tamam** ' ı seçin.

### <a name="settings"></a>Ayarlar

Bildirimleri alacak bir e-posta adresi ayarlamak ve hatalarda uyarı alıp almamanız için **Ayarlar** ' ı genişletin. Ayrıca, eşitlenecek kullanıcıların kapsamını da seçebilirsiniz. Tüm kullanıcıları ve grupları ya da yalnızca atanmış olanları eşitlemeyi seçebilirsiniz.

### <a name="provisioning-status"></a>Sağlama durumu 

Hazırlama işlemi bir uygulama için ilk kez etkinleştiriliyorsa, **sağlama durumunu** **Açık** olarak değiştirerek hizmeti açın. Bu değişiklik, Azure AD sağlama hizmeti 'nin bir başlangıç döngüsünü çalıştırmasına neden olur. **Kullanıcılar ve gruplar** bölümünde atanan kullanıcıları okur, hedef uygulamayı kendileri için sorgular ve ardından Azure AD **eşlemeleri** bölümünde tanımlanan sağlama eylemlerini çalıştırır. Bu işlem sırasında, sağlama hizmeti, hangi kullanıcı hesaplarının yönettiği hakkında önbelleğe alınmış verileri depolar, bu nedenle atama için kapsamda olmayan hedef uygulamalar içinde yönetilmeyen hesaplar, sağlama işlemlerinden etkilenmez. İlk döngüden sonra sağlama hizmeti, Kullanıcı ve Grup nesnelerini 40 dakikalık bir aralıkta otomatik olarak eşitler.

Sağlama hizmetini duraklatmak için **sağlama durumunu** **kapalı**  olarak değiştirin. Bu durumda, Azure uygulamadaki hiçbir Kullanıcı veya Grup nesnesini oluşturmaz, güncelleştirmez veya kaldırmaz. Durumu yeniden **Açık** olarak değiştirin ve hizmet kaldığınız yerden gelir.
