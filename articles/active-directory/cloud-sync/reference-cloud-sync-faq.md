---
title: Azure AD Connect bulut eşitleme hakkında SSS
description: Bu belgede, bulut eşitlemesi hakkında sık sorulan sorular açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614020"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Azure Active Directory Connect bulut eşitleme hakkında SSS

Azure Active Directory (Azure AD) için bulut eşitlemesini bağlama hakkında sık sorulan sorular hakkında bilgi edinin.

## <a name="general-installation"></a>Genel yükleme

**S: bulut eşitlemesi ne sıklıkla çalıştırılır?**

Bulut sağlama, her 2 dakikada bir çalışacak şekilde zamanlanır. Her 2 dakikada bir Kullanıcı, Grup ve parola karma değişikliği Azure AD 'ye sağlanacak.

**S: ilk çalıştırmada Parola karması eşitleme başarısızlıklarını görme. Kaydol?**

Bu beklenen bir durumdur. Hataların nedeni, Kullanıcı nesnesi tarafından Azure AD 'de yok. Kullanıcı Azure AD 'ye sağlandıktan sonra, parola karmalarının sonraki çalıştırmada sağlanması gerekir. Birkaç çalıştırma bekleyin ve parola karması eşitleme 'nin artık hatalara sahip olmadığını onaylayın.

**S: Active Directory örneği, bulut eşitlemesi tarafından desteklenmeyen öznitelikler içeriyorsa (örneğin, Dizin uzantıları) ne olur?**

Bulut sağlama işlemi çalışır ve desteklenen öznitelikleri hazırlar. Desteklenmeyen öznitelikler Azure AD 'ye Hazırlanmayacak. Active Directory 'de Dizin uzantılarını inceleyin ve Azure AD 'ye Flow için bu özniteliklere ihtiyacınız olmadığından emin olun. Bir veya daha fazla öznitelik gerekliyse, Azure AD Connect eşitleme ' yi kullanmayı veya gerekli bilgileri desteklenen özniteliklerden birine taşımayı düşünün (örneğin, uzantı öznitelikleri 1-15).

**S: Azure AD Connect eşitleme ve bulut eşitlemesi arasındaki fark nedir?**

Azure AD Connect eşitleme ile, sağlama, şirket içi eşitleme sunucusunda çalıştırılır. Yapılandırma, şirket içi eşitleme sunucusunda depolanır. Azure AD Connect bulut eşitlemesi sayesinde, sağlama yapılandırması bulutta depolanır ve Azure AD sağlama hizmeti 'nin bir parçası olarak bulutta çalışır. 

**S: birden çok Active Directory ormanınızdan eşitlemek için bulut eşitlemesini kullanabilir miyim?**

Evet. Bulut sağlama, birden çok Active Directory ormanınızdan eşitleme yapmak için kullanılabilir. Çok ormanlı ortamda, tüm başvuruların (örneğin, yöneticinin) etki alanı içinde olması gerekir.  

**S: aracı nasıl güncelleştirilir?**

Aracılar Microsoft tarafından otomatik olarak yükseltilir. BT ekibi için bu, yeni aracı sürümlerini test etmek ve doğrulamak zorunda olma yükünü azaltır. 

**S: otomatik yükseltmeyi devre dışı bırakabilir miyim?**

Otomatik yükseltmeyi devre dışı bırakmak için desteklenen bir yol yoktur.

**S: bulut eşitlemesi için kaynak bağlayıcısını değiştirebilir miyim?**

Varsayılan olarak, bulut eşitleme, kaynak bağlantısı olarak Objectguıd 'e geri dönüş ile ms-DS-tutarlılık-GUID kullanır. Kaynak bağlayıcısını değiştirmek için desteklenen bir yol yoktur.

**S: bulut eşitlemesi kullanılırken AD etki alanı adları ile yeni hizmet sorumluları görüyorum. Beklensin mi?**

Evet, bulut eşitleme, hizmet sorumlusu adı olarak etki alanı adı ile sağlama yapılandırması için bir hizmet sorumlusu oluşturur. Hizmet sorumlusu yapılandırmasında herhangi bir değişiklik yapmayın.

**S: bir sonraki oturum açışında parolayı değiştirmek için eşitlenen bir kullanıcı gerektiğinde ne olur?**

Parola karması eşitleme, bulut eşitleme 'de etkinse ve şirket içi AD 'de bir sonraki oturum açma sırasında parola değiştirmek gerekirse, bulut eşitleme, Azure AD 'de "yapılacak-değişiklik" parola karmasını sağlamaz. Kullanıcı parolayı değiştirdiğinde, Kullanıcı parolası karması AD 'den Azure AD 'ye sağlanır.

**S: bulut eşitlemesi herhangi bir nesne için ms-DS-ımnguıd 'in geri yazmayı destekliyor mu?**

Hayır, bulut eşitlemesi herhangi bir nesne (Kullanıcı nesneleri dahil) için ms-DS-ımfd GUID 'inin geri yazmayı desteklemez. 

**S: bulut eşitlemesini kullanarak kullanıcıları sağlamadım. Yapılandırmayı sildim. Azure AD 'de hala eşitlenmiş olan eski nesneleri görüyorum mi?** 

Yapılandırmayı sildiğinizde, bulut eşitlemesi Azure AD 'de eşitlenmiş nesneleri otomatik olarak kaldırmaz. Eski nesnelerinizin olmadığından emin olmak için, yapılandırmanın kapsamını boş bir grup veya kuruluş birimleriyle değiştirin. Sağlama çalıştıktan ve nesneleri temizledikten sonra yapılandırmayı devre dışı bırakın ve silin. 

**S: Exchange hibrit 'in desteklenmediği ne anlama geliyor?**

Exchange karma dağıtımı özelliği, Exchange posta kutularının hem şirket içinde hem de Microsoft 365 birlikte bulunması için izin verir. Azure AD Connect, Azure AD'den belirli bir öznitelikler kümesini şirket içi dizininize geri eşitler.  Bulut sağlama Aracısı Şu anda bu öznitelikleri şirket içi dizininizle eşitlemez ve bu nedenle Azure AD Connect bir değiştirme olarak desteklenmez.

**S: bulut sağlama aracısını Windows Server Core 'a yükleyebilir miyim?**

Hayır, aracıyı sunucu çekirdeği üzerine yükleme desteklenmiyor.

**S: bulut sağlama aracısına sahip bir hazırlama sunucusu kullanabilir miyim?**

Hayır, hazırlama sunucuları desteklenmez.

**S: Konuk Kullanıcı hesaplarını eşitlenebilir miyim?**

Hayır, Konuk Kullanıcı hesaplarının eşitlenmesi desteklenmez.

**S: bir kullanıcıyı bulut eşitlemesi kapsamındaki bir OU 'dan Azure AD Connect kapsamında olan bir OU 'ya taşıdım, ne olur?**

Kullanıcı silinir ve yeniden oluşturulur.  Bir kullanıcıyı bulut eşitlemesi kapsamındaki bir OU 'dan taşımak, silme işlemi olarak görüntülenir.  Kullanıcı Azure AD Connect tarafından yönetilen bir OU 'ya taşınırsa, Azure AD 'ye yeniden hazırlanacaktır ve yeni bir Kullanıcı oluşturulur.

**S: bulut eşitleme filtresi kapsamında olan OU 'yu yeniden adlandırıp veya taşıdım, Azure AD 'de oluşturulan kullanıcıya ne olur?**

Hiçbir şey.  OU yeniden adlandırılırsa veya taşınırsa kullanıcılar silinmez.

**S: Azure AD Connect bulut eşitlemesi büyük grupları destekliyor mu?**

Evet. Bugün, OU kapsam filtrelemesi kullanılarak eşitlenmiş en fazla 50 k grup üyesini destekliyoruz. Aynı zamanda, Grup kapsamı filtrelemesini kullandığınızda, Grup boyutunuzu 1500 Üyeden az tutmanız önerilir. Bunun nedeni, büyük bir grubu Grup kapsamı filtresinin bir parçası olarak eşitleyebilseniz de, bu gruba Üyeler 1500 ' den büyük toplu işler ekleyerek Delta eşitlemesi başarısız olur. 

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)
