---
title: Azure AD 'de uygulama sağlama için bilinen sorunlar
description: Azure AD 'de otomatik uygulama sağlamalarıyla çalışırken bilinen sorunlar hakkında bilgi edinin.
author: kenwith
ms.author: kenwith
manager: daveba
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: 9eba671f6c824c8c88388f2b9d61512dfb1d122f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256652"
---
# <a name="known-issues-application-provisioning"></a>Bilinen sorunlar: uygulama sağlama
Uygulama sağlama üzerinde çalışırken dikkat etmeniz gereken bilinen sorunlar. UserVoice üzerinde uygulama sağlama hizmeti hakkında geri bildirimde bulunmak için bkz. [Azure AD uygulama sağlama UserVoice](https://aka.ms/appprovisioningfeaturerequest). Hizmeti iyileştirebilmemiz için UserVoice 'ı yakından izliyoruz. 

> [!NOTE]
> Bu, bilinen sorunların kapsamlı bir listesi değildir. Listelenmeyen bir sorunu biliyorsanız, sayfanın en altında geri bildirim sağlayın.

## <a name="authorization"></a>Yetkilendirme 

**Başarılı bağlantı testinin ardından kaydedilemiyor**

Bir bağlantıyı başarıyla test edebilir, ancak kaydedebiliyorsanız kimlik bilgileri için izin verilen depolama sınırını aştınız. Daha fazla bilgi edinmek için bkz. [yönetici kimlik bilgilerini kaydetme sorunu](./user-provisioning.md).

**Kaydedilemiyor**

Kaydetmek için kiracı URL 'SI, gizli belirteç ve bildirim e-postası doldurulmalıdır. Bunlardan yalnızca birini sağlayamıyoruz. 

**Sağlama modu geri el ile değiştirilemiyor**

Hazırlama işlemini ilk kez yapılandırdıktan sonra sağlama modunun el ile otomatik 'e geçtiğine dikkat edin. El ile geri değiştiremezsiniz. Ancak, Kullanıcı arabirimi aracılığıyla sağlamayı kapatabilirsiniz. Kullanıcı arabiriminde sağlamayı devre dışı bırakmak, açılan menüyü el ile ayarlama ile aynı şekilde devre dışı bırakır.  


## <a name="attribute-mappings"></a>Öznitelik eşlemeleri 

**Öznitelik SamAccountName veya userType, kaynak özniteliği olarak kullanılamaz**

SamAccountName ve userType öznitelikleri varsayılan olarak bir kaynak öznitelik olarak kullanılamaz. Özniteliği eklemek için şemanızı genişletin. Şemayı genişleterek, kullanılabilir kaynak öznitelikleri listesine öznitelikleri ekleyebilirsiniz. Daha fazla bilgi için bkz. [eksik kaynak özniteliği](user-provisioning-sync-attributes-for-mapping.md). 

**Şema uzantısı için kaynak öznitelik açılan kutusu eksik**

Şemanıza yönelik uzantılar bazen Kullanıcı arabirimindeki kaynak özniteliği açılan listesinde eksik olabilir. Öznitelik eşlemelerinizin gelişmiş ayarlarına gidin ve öznitelikleri el ile ekleyin. Daha fazla bilgi için bkz. [öznitelik eşlemelerini özelleştirme](customize-application-attributes.md).

**Null öznitelik sağlanamıyor**

Azure AD Şu anda null öznitelikler sağlayamaz. Kullanıcı nesnesinde bir öznitelik null ise atlanacak. 

**Öznitelik eşleme ifadeleri için en fazla karakter**

Öznitelik eşleme ifadelerinde en fazla 10.000 karakter olabilir. 

**Desteklenmeyen kapsam filtreleri**

Dizin genişletmeleri, Approtaatamalar, userType ve accountExpires kapsam filtreleri olarak desteklenmez.


## <a name="service-issues"></a>Hizmet sorunları 

**Desteklenmeyen senaryolar**

- Sağlama parolaları desteklenmez. 
- İç içe grupların sağlanması desteklenmez. 
- Bu kiracılar için sağlama, kiracının boyutu nedeniyle desteklenmiyor.
- Tüm bulutlarda sağlama uygulamaları kullanılabilir değildir. Örneğin, Atlasme henüz kamu bulutu 'nda mevcut değildir. Uygulama geliştiricileriyle uygulamalarını tüm bulutlara eklemek için çalışıyoruz.

**OıDC tabanlı uygulamamda otomatik sağlama kullanılamıyor**

Bir uygulama kaydı oluşturursanız, kurumsal uygulamalarda karşılık gelen hizmet sorumlusu otomatik Kullanıcı sağlaması için etkinleştirilmeyecektir. Uygulamanın, birden çok kuruluş tarafından kullanılması amaçlanıyorsa veya sağlama için ikinci Galeri olmayan bir uygulama oluşturmak üzere galeriye eklenmesini istemeniz gerekir. 

**Sağlama aralığı düzeltildi**

Sağlama döngüleri arasındaki [süre](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) Şu anda yapılandırılamaz. 

**Değişiklikler hedef uygulamadan Azure AD 'ye taşınmıyor**

Uygulama sağlama hizmeti dış uygulamalarda yapılan değişikliklerden haberdar değildir. Bu nedenle, geri alınacak bir eylem yapılmaz. Uygulama sağlama hizmeti, Azure AD 'de yapılan değişiklikleri temel alır. 

**Eşitlemeden eşitleme atanmayan eşitleme için geçiş çalışmıyor**

Kapsam ' Tümünü Eşitle ' iken ' Sync atandı ' olarak değiştirildikten sonra, değişikliğin geçerli olduğundan emin olmak için yeniden başlatma işlemi de gerçekleştirdiğinizden emin olun. Yeniden başlatma işlemini kullanıcı arabiriminden yapabilirsiniz.

**Sağlama çevrimi tamamlanana kadar devam ediyor**

Sağlama `enabled = off` veya geçiş durdurma işlemleri yapıldığında, geçerli sağlama çevrimi tamamlanana kadar çalışmaya devam edecektir. Hizmeti, sağlama işlemini yeniden yapana kadar gelecekteki döngülerin yürütülmesini durdurur.

**Grubun üyesi sağlanmadı**

Bir grup kapsamdadır ve üye kapsam dışı olduğunda, Grup sağlanır. Kapsam dışı Kullanıcı sağlanmayacaktır. Üye kapsama geri dönerse, hizmet değişikliği hemen algılamaz. Sağlama yeniden başlatıldığında sorun ele alınacaktır. Tüm kullanıcıların doğru şekilde sağlandığından emin olmak için hizmeti düzenli aralıklarla yeniden başlatmanızı öneririz.  

**Yönetici sağlanmadı**

Bir Kullanıcı ve Yöneticisi her ikisi de sağlama kapsamıdır, hizmet kullanıcıyı hazırlar ve sonra Yöneticiyi güncelleştirir. Ancak, bir gün Kullanıcı kapsamdadır ve yönetici kapsam dışında olduğunda, yönetici başvurusu olmadan kullanıcıyı sağlayacağız. Yönetici kapsama geldiğinde, sağlama yeniden başlatılana ve hizmetin tüm kullanıcıları yeniden değerlendirmesine neden olana kadar yönetici başvurusu güncellenmez. 

## <a name="next-steps"></a>Sonraki adımlar
- [Sağlama nasıl çalışır?](how-provisioning-works.md)
