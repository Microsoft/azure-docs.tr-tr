---
title: Şirket içi hizmet hesaplarını yöneten | Azure Active Directory
description: Hizmet hesapları için hesap yaşam döngüsü işlemi oluşturma ve çalıştırmaya yönelik kılavuz
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10ea524620f810e0bf1dddc230716031bbc10e69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642374"
---
# <a name="governing-on-premises-service-accounts"></a>Şirket içi hizmet hesaplarını yöneten

Windows Active Directory 'de dört tür şirket içi hizmet hesabı vardır:

* [Grup tarafından yönetilen hizmet hesapları](service-accounts-group-managed.md) (gMSAs)

* [tek başına yönetilen hizmet hesapları](service-accounts-standalone-managed.md) (SMSAs)

* [Bilgisayar hesapları](service-accounts-computer.md)

* [Hizmet hesabı olarak çalışan Kullanıcı hesapları](service-accounts-user-on-premises.md)


Hizmet hesaplarını yakından yönetmek önemlidir: 

* Hizmet hesaplarını, kullanım örneği gereksinimlerine ve amacına göre koruyun.

* Hizmet hesaplarının yaşam döngüsünü ve kimlik bilgilerini yönetin.

* Hizmet hesaplarını, sundukları riske ve bunların gerçekleştirdikleri izinlere göre değerlendirin, 

* Active Directory ve Azure Active Directory, büyük olasılıkla izin veren izinlere sahip eski hizmet hesaplarına sahip olmadığından emin olun.

## <a name="principles-for-creating-a-new-service-account"></a>Yeni hizmet hesabı oluşturma ilkeleri

Yeni bir hizmet hesabı oluştururken aşağıdaki ölçütleri kullanın.

| İlkeler| Dikkat edilmesi gerekenler | 
| - |- | 
| Hizmet hesabı eşleme| Hizmet hesabını tek bir hizmete, uygulamaya veya betiğe bağlayın. |
| Sahiplik| Hesabın sorumluluğunu talep eden ve kabul eden bir sahip olduğundan emin olun. |
| Kapsam| Kapsamı net bir şekilde tanımlayın ve hizmet hesabı için kullanım süresini tahmin edin. |
| Amaç| Tek belirli bir amaç için hizmet hesapları oluşturun. |
| Privilege| En az ayrıcalık ilkesini şu şekilde uygulayın: <br>Bunları, yöneticiler gibi yerleşik gruplara hiçbir şekilde atamakla.<br> Uygun yerlerde yerel makine ayrıcalıkları kaldırılıyor.<br>Erişim ve dizin erişimi için Active Directory temsili kullanma.<br>Ayrıntılı erişim izinleri kullanılıyor.<br>Kullanıcı tabanlı hizmet hesaplarında hesap süre sonlarını ve konum tabanlı kısıtlamaları ayarlama |
| İzleme ve denetim kullanımı| Oturum açma verilerini izleyin ve hedeflenen kullanımla eşleştiğinden emin olun. Anormal kullanım için Uyarılar ayarlayın. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Kullanıcı hesapları için en az ayrıcalığı zorla ve hesap aşırı kullanımını sınırla

Hizmet hesapları olarak kullanılan kullanıcı hesaplarıyla aşağıdaki ayarları kullanın:

* [**Hesap süre sonu**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true): hizmet hesabını, gözden geçirme süresi dolduktan sonra, devam etmesi gerektiğini belirlenmediği takdirde otomatik olarak süre sonu olarak ayarlayın

*  **Logonistasyonlarının**: hizmet hesabının oturum açabildiği konum için izinleri kısıtla. Bir makinede yerel olarak çalışır ve yalnızca o makinedeki kaynaklara erişirse, başka her yerde oturum açmasını kısıtlayın.

* [**Parola değiştirilemiyor**](/powershell/module/addsadministration/set-aduser): parametresini false olarak ayarlayarak hizmet hesabının kendi parolasını değiştirmesini engelleyin.

 
## <a name="build-a-lifecycle-management-process"></a>Yaşam döngüsü yönetimi işlemi oluşturma

Hizmet hesaplarınızın güvenliğini sağlamak için, bu hesapları, kullanımdan kaldırılana kadar gereken süreyi belirlemeniz gerekir. 

Hizmet hesaplarının yaşam döngüsü yönetimi için aşağıdaki işlemi kullanın:

1. Hesap için kullanım bilgilerini toplayın
1. Hizmet hesabını ve uygulamayı yapılandırma yönetimi veritabanına (CMDB) ekleme
1. Risk değerlendirmesi veya resmi İnceleme gerçekleştirme
1. Hizmet hesabı oluşturun ve kısıtlamaları uygulayın.
1. Yinelenen incelemeler zamanlayın ve gerçekleştirin. İzinleri ve kapsamları gereken şekilde ayarlayın.
1. Uygun olduğunda hesabı sağlamayı kaldırma.

### <a name="collect-usage-information-for-the-service-account"></a>Hizmet hesabı için kullanım bilgilerini toplayın

Her hizmet hesabı için ilgili iş bilgilerini toplayın. Aşağıdaki tabloda toplanacak minimum bilgiler gösterilmektedir, ancak iş örneğini hesapların varlığı için sağlamak üzere gereken her şeyi toplamanız gerekir.

| Veriler| Ayrıntılar |
| - | - |
| Sahip| Hizmet hesabı için sorumlu olan kullanıcı veya Grup |
| Amaç| Hizmet hesabının amacı |
| İzinler (kapsamlar)| Beklenen izin kümesi |
| Yapılandırma yönetimi veritabanı (CMDB) bağlantıları| Hedef betiği/uygulama ve sahiplerle çapraz bağlantı hizmet hesabı |
| Risk| Güvenlik riski değerlendirmesi temelinde risk ve iş etkisi Puanlama |
| Ömür| Hesap süre sonu veya yeniden sertifika planlamasını etkinleştirmek için beklenen maksimum ömür süresi |


 

İdeal olarak, bir hesap self servis isteği yapın ve ilgili bilgileri gerekli kılın. Sahip, bir uygulama veya iş sahibi, bir BT üyesi veya altyapı sahibi olabilir. Bu istek için Microsoft Forms gibi bir araç kullanmak ve ilgili bilgiler, hesap onaylanırsa bunu CMDB envanter araclarınıza bağlantı noktasını kolaylaştırır.

### <a name="onboard-service-account-to-cmdb"></a>Hizmet hesabını CMDB 'a ekleme

Toplanan bilgileri bir CMDB türü uygulamada depolayın. İş bilgilerine ek olarak, diğer altyapı, uygulama ve işlemlere tüm bağımlılıkları dahil edin.  Bu merkezi depoyu daha kolay hale getirir:

* Riski değerlendirin.

* Hizmet hesabını gerekli kısıtlamalarla yapılandırın.

* İlgili işlevsel ve güvenlik bağımlılıklarını anlayın.

* Güvenlik ve devam eden gereksinimlerde düzenli incelemeler gerçekleştirin.

* Hizmet hesabını gözden geçirmek, devre dışı bırakma ve değiştirme için sahipler ile iletişime geçin.

Bir Web sitesi çalıştırmak için kullanılan ve bir veya daha fazla SQL veritabanına bağlanma ayrıcalıklarına sahip bir hizmet hesabı düşünün. Bu hizmet hesabı için CMDB 'niz içinde depolanan bilgiler şu olabilir:

|Veriler | Ayrıntılar|
| - | - |
| Sahip, Deputy| John Bloom, Anna Mayers |
| Amaç| HR Web sayfasını çalıştırın ve HR-veritabanlarına bağlanın. Veritabanlarına erişirken son kullanıcının kimliğine bürünebilir. |
| İzinler, kapsamlar| HR-WEBServer: yerel olarak oturum aç, Web sayfasını Çalıştır<br>HR-SQL1: yerel olarak oturum aç, tüm HR * veritabanı üzerinde oku<br>HR-SQL2: yerel olarak oturum açma, maaş üzerinde okuma * veritabanı |
| Maliyet Merkezi| 883944 |
| Risk değerlendirdiğini| Uzunlukta İş etkisi: Orta; özel bilgiler; Uzunlukta |
| Hesap kısıtlamaları| Oturum aç: yalnızca asıdan bahsedilen sunucular; Parola değiştirilemiyor; MBI-Password Ilkesi; |
| Ömür| edin |
| İnceleme çevrimi| İki aylık (Sahibe göre, güvenlik ekibine göre, gizlilik) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Hizmet hesabı kullanımının risk değerlendirmesi veya resmi incelemesi gerçekleştirme

İzinleri ve amacı verildiğinde, hesabın ilişkili uygulama veya hizmetine ve tehlikeye atılırsa altyapınıza ait olabilecek riski değerlendirin. Hem doğrudan hem de dolaylı riski göz önünde bulundurun. 

* Bir saldırgan ne kadar doğrudan erişim elde edebilir?

* Hizmet hesabına ne diğer bilgiler veya sistemler erişebilir?

* Hesap ek izinler vermek için kullanılabilir mi?

* İzinlerin ne zaman değişiklik yapacağını nasıl anlarsınız?

Ve belgelendikten sonra risk değerlendirmesi şu şekilde etkiye sahip olabilir:

* Hesap kısıtlamaları

* Hesap ömrü

* Hesap İnceleme gereksinimleri (temposunda ve gözden geçirenler)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Hizmet hesabı oluşturma ve hesap kısıtlamalarını uygulama

Yalnızca ilgili bilgiler CMDB 'de belgelendikten ve risk değerlendirmesi gerçekleştirdikten sonra hizmet hesabı oluşturun. Hesap kısıtlamaları risk değerlendirmesine hizalanmalıdır. Değerlendirmeyle ilgili olarak aşağıdaki kısıtlamaları göz önünde bulundurun.:

* [Hesap süre sonu](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true)

   * Hizmet hesabı olarak kullanılan tüm Kullanıcı hesapları için, kullanım için gerçekçi ve kesin bir bitiş tarihi tanımlayın. Bunu "Account Expires" bayrağını kullanarak ayarlayın. Daha fazla ayrıntı için bkz.[ set-Adaccountexpiasyon](/powershell/module/addsadministration/set-adaccountexpiration). 

* Oturum açma ([Logonworkstation](/powershell/module/addsadministration/set-aduser))

* [Parola ilkesi](../../active-directory-domain-services/password-policy.md) gereksinimleri

* Yalnızca ayrıcalıklı kullanıcılar için yönetimi sağlayan bir [OU konumunda](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) oluşturma

* Hizmet hesabında ve [hizmet hesabı kullanımıyla](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html) [ilgili değişiklikleri algılayan](/windows/security/threat-protection/auditing/audit-directory-service-changes) denetimi ayarlayın ve toplayın.

Üretime geçmeye hazırsanız, hizmet hesabına güvenli bir şekilde erişim izni verin. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Hizmet hesaplarının düzenli incelemelerini zamanlayın

Orta ve yüksek riskli olarak sınıflandırılan hizmet hesaplarının düzenli incelemelerini ayarlayın. Gözden geçirmeler şunları içermelidir: 

* Hesap için devam eden gereksinim kanıtlaması ve ayrıcalıkların ve kapsamların gerekçe.

* Yukarı akış ve aşağı akış bağlantıları değerlendirmesi dahil gizlilik ve güvenlik ekiplerine göre gözden geçirin.

* Denetimlerin yalnızca amaçlanan amaçlar için kullanılmasını sağlamaya yönelik veriler

### <a name="deprovision-service-accounts"></a>Hizmet hesaplarını sağlamayı kaldırma

Hazırlama işleminizde, önce izinleri ve izleyiciyi kaldırın, ardından uygunsa hesabı kaldırın.

Hizmet hesaplarının sağlamasını kaldırma:

* Hizmet hesabının oluşturulduğu betik veya uygulama kullanımdan kaldırıldı.

* Hizmet hesabının kullanıldığı komut dosyası veya uygulama içindeki işlev (örneğin, belirli bir kaynağa erişim) devre dışı bırakıldı.

* Hizmet hesabı farklı bir hizmet hesabıyla değiştirilmiştir.

Tüm izinleri kaldırdıktan sonra, hesabı kaldırmak için bu işlemi kullanın.

1. İlişkili uygulama veya betiğin sağlaması geri alındıktan sonra, ilişkili hizmet hesapları için oturum açma işlemlerini ve kaynak erişimini, başka bir işlemde kullanılmadığından emin olmak için izleyin. Artık gerekli olmadığından eminseniz bir sonraki adıma gidin.

2. Hizmet hesabının oturum açmasını devre dışı bırakın ve artık gerekli olmadığından emin olun. Zaman hesaplarının devre dışı kalması için bir iş ilkesi oluşturun.

3. Devre dışı bırakılan ilke karşılandıktan sonra hizmet hesabını silin. 

   * MSAs için, PowerShell 'i kullanarak [kaldırabilir](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps&preserve-view=true) veya yönetilen hizmet hesabı kapsayıcısından el ile silebilirsiniz.

   * Bilgisayar veya Kullanıcı hesapları için Active Directory ' den hesabı el ile silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Hizmet hesaplarını güvenli hale getirmek için aşağıdaki makalelere bakın

* [Şirket içi hizmet hesaplarına giriş](service-accounts-on-premises.md)

* [Güvenli grup yönetilen hizmet hesapları](service-accounts-group-managed.md)

* [Güvenli tek başına yönetilen hizmet hesapları](service-accounts-standalone-managed.md)

* [Güvenli bilgisayar hesapları](service-accounts-computer.md)

* [Güvenli Kullanıcı hesapları](service-accounts-user-on-premises.md)

* [Şirket içi hizmet hesaplarını yönetir](service-accounts-govern-on-premises.md)