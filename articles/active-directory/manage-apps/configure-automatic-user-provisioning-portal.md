---
title: Azure Active Directory 'de kurumsal uygulamalar için Kullanıcı sağlama yönetimi | Microsoft Docs
description: Azure Active Directory kullanarak kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetmeyi öğrenin
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26b00670ad93cceab8f570d3a5f56bd095fa80b5
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315265"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Azure portal kurumsal uygulamalar için Kullanıcı hesabı sağlamayı yönetme

Bu makalede, tarafından desteklenen uygulamalar için otomatik Kullanıcı hesabı sağlamayı ve sağlamayı kaldırmayı yönetmek üzere [Azure Portal](https://portal.azure.com) nasıl kullanılacağı açıklanır. Otomatik Kullanıcı hesabı sağlama ve nasıl çalıştığı hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](user-provisioning.md).

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

### <a name="admin-credentials"></a>Yönetici Kimlik Bilgileri

Azure AD 'nin uygulamanın kullanıcı yönetim API 'sine bağlanması için gereken kimlik bilgilerini girmek üzere **yönetici kimlik bilgileri** ' ni genişletin. Gerekli giriş uygulamaya bağlı olarak değişir. Belirli uygulamalar için kimlik bilgisi türleri ve gereksinimleri hakkında bilgi edinmek için ilgili [uygulamanın yapılandırma öğreticisine](user-provisioning.md)bakın.

Sağlanan kimlik bilgilerini kullanarak Azure AD 'nin uygulamanın sağlama uygulamasına bağlanmasını sağlayarak kimlik bilgilerini test etmek için **Bağlantıyı Sına** ' yı seçin.

### <a name="mappings"></a>Eşlemeler

Kullanıcı hesapları sağlandığında veya güncelleştirilirken Azure AD ile hedef uygulama arasında akan Kullanıcı özniteliklerini görüntülemek ve düzenlemek için **eşlemeler** ' i genişletin.

Azure AD Kullanıcı nesneleri ile her bir SaaS uygulamasının Kullanıcı nesneleri arasında önceden yapılandırılmış bir eşlemeler kümesi vardır. Bazı uygulamalar, gruplar veya kişiler gibi diğer nesne türlerini yönetir. Eşleme düzenleyicisini sağ tarafta açmak için tabloda bir eşleme seçin; burada bunları görüntüleyebilir ve özelleştirebilirsiniz.

![Öznitelik eşleme ekranını gösterir](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Desteklenen özelleştirmeler şunlardır:

* SaaS uygulamasının kullanıcı nesnesine Azure AD Kullanıcı nesnesi gibi belirli nesneler için eşlemeleri etkinleştirme ve devre dışı bırakma.
* Azure AD kullanıcı nesnesinden uygulamanın kullanıcı nesnesine akan öznitelikleri düzenleyebilirsiniz. Öznitelik eşlemesi hakkında daha fazla bilgi için bkz. [öznitelik eşleme türlerini anlama](customize-application-attributes.md#understanding-attribute-mapping-types).
* Azure AD 'nin hedeflenen uygulama üzerinde çalıştığı sağlama eylemlerini filtreleme. Azure AD 'nin nesneleri tamamen eşitlemesini yapmak yerine, çalıştırılan eylemleri sınırlayabilirsiniz.

  Örneğin yalnızca **güncelleştirme** ' yi seçin ve Azure AD yalnızca bir uygulamadaki mevcut kullanıcı hesaplarını güncelleştirir ancak yenilerini oluşturmaz. Yalnızca **Oluştur** ' u seçin ve Azure yalnızca yeni kullanıcı hesapları oluşturur, ancak varolanları güncelleştirmez. Bu özellik, yöneticilerin hesap oluşturma ve güncelleştirme iş akışları için farklı eşlemeler oluşturmalarına olanak tanır.

* Yeni bir öznitelik eşlemesi ekleniyor. **Öznitelik eşleme** bölmesinin alt kısmındaki **Yeni eşleme Ekle** ' yi seçin. Yeni eşlemeyi listeye eklemek için **özniteliği Düzenle** formunu doldurun ve **Tamam** ' ı seçin.

### <a name="settings"></a>Ayarlar

**Hazırlama** ekranının **Ayarlar** alanında SEÇILI uygulama için Azure AD sağlama hizmetini başlatabilir ve durdurabilirsiniz. Sağlama önbelleğini kaldırmayı ve hizmeti yeniden başlatmayı da tercih edebilirsiniz.

Hazırlama işlemi bir uygulama için ilk kez etkinleştiriliyorsa, **sağlama durumunu** **Açık**olarak değiştirerek hizmeti açın. Bu değişiklik, Azure AD sağlama hizmeti 'nin bir başlangıç döngüsünü çalıştırmasına neden olur. **Kullanıcılar ve gruplar** bölümünde atanan kullanıcıları okur, hedef uygulamayı kendileri için sorgular ve ardından Azure AD **eşlemeleri** bölümünde tanımlanan sağlama eylemlerini çalıştırır. Bu işlem sırasında, sağlama hizmeti, hangi kullanıcı hesaplarının yönettiği hakkında önbelleğe alınmış verileri depolar, bu nedenle atama için kapsamda olmayan hedef uygulamalar içinde yönetilmeyen hesaplar, sağlama işlemlerinden etkilenmez. İlk döngüden sonra sağlama hizmeti, Kullanıcı ve Grup nesnelerini 40 dakikalık bir aralıkta otomatik olarak eşitler.

Sağlama hizmetini duraklatmak için **sağlama durumunu** **kapalı** olarak değiştirin. Bu durumda, Azure uygulamadaki hiçbir Kullanıcı veya Grup nesnesini oluşturmaz, güncelleştirmez veya kaldırmaz. Durumu yeniden **Açık** olarak değiştirin ve hizmet kaldığınız yerden gelir.

**Geçerli durumu temizle ve eşitlemeyi yeniden Başlat** onay kutusunu seçin ve **Kaydet** ' i seçin:

* Sağlama hizmetini durdur
* Hizmetleri yeniden başlatın ve ilk döngüyü yeniden çalıştırın

Bu seçenek, yöneticilerin sağlama dağıtım işlemini yeniden başlatmasını sağlar.
