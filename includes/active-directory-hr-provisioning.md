---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93135396"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Kullanıcı sağlamayı Azure Active Directory için bulut HR uygulaması

Geçmişte, BT personeli çalışanları oluşturmak, güncelleştirmek ve silmek için el ile gerçekleştirilen yöntemlere güvendi. Çalışan verilerini eşitlemek için CSV dosyalarını veya özel betikleri karşıya yükleme gibi yöntemleri kullandık. Bu sağlama işlemlerinde hata açık, güvensiz ve yönetimi zor.

Çalışanların, satıcıların veya contentik çalışanların kimlik yaşam döngülerini yönetmek için [Azure Active Directory (Azure AD) Kullanıcı sağlama hizmeti](../articles/active-directory/app-provisioning/user-provisioning.md) , bulut tabanlı insan kaynakları (hr) uygulamalarıyla tümleştirme sunar. Uygulama örnekleri, Workday veya başarılı faktörleri içerir.

Azure AD, aşağıdaki bulut HR uygulaması (uygulama) iş akışlarını etkinleştirmek için bu tümleştirmeyi kullanır:

- **Kullanıcıları Active Directory sağla:** Bir bulut HR uygulamasındaki seçili kullanıcı kümelerini bir veya daha fazla Active Directory etki alanına sağlayın.
- **Azure AD 'ye yalnızca bulutta Kullanıcı sağlama:** Active Directory kullanılmayan senaryolarda, kullanıcıları doğrudan Cloud HR uygulamasından Azure AD 'ye sağlayın.
- **Cloud HR uygulamasına geri yaz:** Azure AD 'den e-posta adreslerini ve Kullanıcı adı özniteliklerini Cloud HR uygulamasına geri yazın.


## <a name="enabled-hr-scenarios"></a>Etkin HR senaryoları

Azure AD Kullanıcı sağlama hizmeti, aşağıdaki HR tabanlı kimlik yaşam döngüsü yönetimi senaryolarına yönelik Otomasyonu sunar:

- **Yeni çalışan işe alım:** Bulut HR uygulamasına yeni bir çalışan eklendiğinde, Active Directory ve Azure AD 'de otomatik olarak bir kullanıcı hesabı oluşturulur. Bu seçenek, e-posta adresi ve Kullanıcı adı özniteliklerini bulut HR uygulamasına geri yazma seçeneğiyle birlikte otomatik olarak oluşturulur.
- **Çalışan özniteliği ve profil güncelleştirmeleri:** Bulut HR uygulamasında ad, başlık veya yönetici gibi bir çalışan kaydı güncelleştirilirse, Kullanıcı hesapları Active Directory ve Azure AD 'de otomatik olarak güncelleştirilir.
- **Çalışan sonlandırmaları:** Bulut HR uygulamasında bir çalışan sonlandırıldığında, Kullanıcı hesapları Active Directory ve Azure AD 'de otomatik olarak devre dışı bırakılır.
- **Çalışan rehires:** Bulut HR uygulamasında bir çalışan yeniden işe alındığında, eski hesapları otomatik olarak yeniden etkinleştirilebilir veya Active Directory ve Azure AD 'ye yeniden sağlanabilir.

## <a name="who-is-this-integration-best-suited-for"></a>Bu tümleştirme kim için en uygun?

Azure AD Kullanıcı sağlama ile bulut HR uygulaması tümleştirmesi ideal olan kuruluşlar için idealdir:

- Bulut HR Kullanıcı sağlama için önceden oluşturulmuş, bulut tabanlı bir çözüm ister.
- Bulut HR uygulamasından Active Directory veya Azure AD 'ye doğrudan kullanıcı sağlamayı gerektir.
- Kullanıcılardan bulut HR uygulamasından elde edilen verileri kullanarak sağlanması gerekir.
- Yalnızca bulut HR uygulamasında algılanan değişiklik bilgilerini temel alan bir veya daha fazla Active Directory ormanlara, etki alanına ve OU 'Lara katılmayı zorunlu kılın.
- E-posta için Office 365 kullanın.


### <a name="key-benefits"></a>Önemli avantajlar

Bu HR-odaklı BT sağlama özelliği, aşağıdaki önemli iş avantajlarını sunmaktadır:

- **Üretkenliği artırın:** Artık Kullanıcı hesaplarının ve Office 365 lisanslarının atamasını otomatikleştirebilir ve anahtar gruplarına erişim sağlayabilirsiniz. Atamaları otomatik hale getirmek, iş araçlarına yeni bir anında erişim sağlar ve üretkenliği artırır.
- **Riski yönetin:** Bulut HR uygulamasından gelen verilerle akan çalışanların durum veya grup üyeliklerine göre değişiklikleri otomatikleştirerek güvenliği artırabilirsiniz. Değişikliklerin otomatikleştirilmesi, kullanıcılar kuruluştan geçiş yaparken veya kuruluştan ayrıldığınızda, anahtar uygulamalarına yönelik kullanıcı kimliklerinin ve erişimin otomatik olarak güncelleştirilmesini sağlar.
- **Adres uyumluluğu ve idare:** Azure AD, hem kaynak hem de hedef sistemlerin uygulamaları tarafından gerçekleştirilen Kullanıcı hazırlama istekleri için yerel denetim günlüklerini destekler. Denetimle, tek bir ekrandan uygulamalara kimlerin erişebileceğini izleyebilirsiniz.
- **Maliyeti yönetme:** Otomatik sağlama, el ile sağlama ile ilişkili verimsizlikleri ve insan hatasından kaçınarak maliyetleri azaltır. Eski ve süresi geçmiş platformlar kullanılarak zaman içinde oluşturulmuş özel geliştirmiş Kullanıcı sağlama çözümleri gereksinimini azaltır.
