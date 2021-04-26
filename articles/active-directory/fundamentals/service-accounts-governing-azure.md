---
title: Azure Active Directory hizmet hesaplarını yöneten
description: Azure Active Directory 'de hizmet hesaplarının yaşam döngüsünü yönetmeye yönelik ilkeler ve yordamlar.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f540ab40a14af09aa8667860286021f572eb6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587908"
---
# <a name="governing-azure-ad-service-accounts"></a>Azure AD hizmet hesaplarını yöneten

Azure Active Directory (Azure AD) içinde üç tür hizmet hesabı vardır: [Yönetilen kimlikler](service-accounts-managed-identities.md), [hizmet sorumluları](service-accounts-principal.md)ve hizmet hesapları olarak çalışan Kullanıcı hesapları. Bu hizmet hesaplarını otomatik kullanım için oluştururken, bunlara Azure ve Azure AD 'de kaynaklara erişme izinleri verilir. Kaynaklar Microsoft 365 Hizmetleri, hizmet olarak yazılım (SaaS) uygulamaları, özel uygulamalar, veritabanları, IK sistemleri vb. içerebilir. Azure AD hizmet hesaplarını yönetirken, güvenlik ve süreklilik sağlamak için oluşturma, izinleri ve yaşam döngüsünü yönettiğiniz anlamına gelir.

> [!IMPORTANT] 
> Kullanıcı hesaplarının, doğal olarak daha az güvenli olduklarından hizmet hesapları olarak kullanılması önerilmez. Bu, hizmet sorumlularına dönüştürülmediğinden Azure AD ile eşitlenen şirket içi hizmet hesaplarını içerir. Bunun yerine, yönetilen kimliklerin veya hizmet sorumlularının kullanılmasını öneririz. Bu noktada, koşullu erişim ilkelerinin kullanılması hizmet sorumluları ile mümkün değildir, ancak işlevsellik geliyor demektir.


## <a name="plan-your-service-account"></a>Hizmet hesabınızı planlayın

Bir hizmet hesabı oluşturmadan veya bir uygulamayı kaydetmeye başlamadan önce, hizmet hesabının anahtar bilgilerini belgeleyin. Belgelenmiş bilgilerin olması, hesabı etkin bir şekilde izlemeyi ve yönetimini kolaylaştırır. Aşağıdaki verileri toplamayı ve merkezi yapılandırma yönetimi veritabanınızda (CMDB) izlemeyi öneririz.

| Veriler| Description| Ayrıntılar |
| - | - | - |
| Sahip| Hizmet hesabını yönetmek ve izlemek için sorumlu olan kullanıcı veya grup.| Hesabı izlemek ve sorunları azaltmak için bir yol uygulamak için gerekli izinlere sahip sahibi sağlayın. Sorun azaltma, sahip tarafından veya bir istek aracılığıyla yapılabilir. |
| Amaç| Hesap nasıl kullanılacaktır.| Hizmet hesabını belirli bir hizmet, uygulama veya betiğe eşleyin. Çoklu kullanım hizmet hesapları oluşturmaktan kaçının. |
| İzinler (kapsamlar)| Beklenen izin kümesi.| Erişebileceği kaynakları ve bu kaynaklara yönelik izinleri belgeleyin. |
| CMDB bağlantısı| Erişilecek kaynaklara ve hizmet hesabının kullanıldığı betiklerin bağlantısını yapın.| Değişikliklerin gerekli yukarı akış ve aşağı akış efektlerini iletişim kurabilmeleri için kaynak ve betik sahiplerini belgediğinizden emin olun. |
| Risk değerlendirmesi| Hesap tehlikeye atılırsa risk ve iş etkisi.| Bu bilgileri, izin kapsamını daraltmak ve hesap bilgilerine kimlerin erişiminin olması gerektiğini tespit etmek için kullanın. |
| İnceleme dönemi| Hizmet hesabının sahip tarafından İncelenme zamanlaması.| İnceleme iletişimini ve incelemeleri zamanlamak için bunu kullanın. Zamanlanan İnceleme döneminden sonra bir gözden geçirme belirli bir süre için gerçekleştirilmeyen belge ne olur? |
| Ömür| Hesabın en fazla kullanım ömrü tahmin edilen.| Sahibe yönelik iletişimleri zamanlamak ve sonunda hesapları silmek için bunu kullanın. Mümkün olduğunda, kimlik bilgilerinin otomatik olarak Devralındığı kimlik bilgileri için bir sona erme tarihi ayarlayın. |
| Name| Hesap standartlaştırılmış adı| Hizmet hesaplarında kolayca arama, sıralama ve filtreleme yapabilmeniz için tüm hizmet hesapları için bir adlandırma şeması oluşturun. |


## <a name="use-the-principle-of-least-privileges"></a>En az ayrıcalık ilkesini kullanın
Hizmet hesabına yalnızca görevlerini gerçekleştirmek için gereken izinleri verin ve daha fazlasını yapın. Bir hizmet hesabının, genel yönetici ayrıcalık düzeyi gibi üst düzey izinlere ihtiyacı varsa, neden ve gerekli izinleri azaltmayı deneyin.

Hizmet hesabı ayrıcalıkları için aşağıdaki yöntemleri öneririz.

**İzinler**

* Hizmet hesaplarına yerleşik roller atamayın. Bunun yerine, [Microsoft Graph Için OAuth2 izin verme modelini](/graph/api/resources/oauth2permissiongrant)kullanın,

* Hizmet sorumlusuna ayrıcalıklı bir rol atanması gerekiyorsa, belirli, gerekli ayrıcalıklı olan [özel bir rolü](../roles/custom-create.md) zamana bağlıysa bir biçimde atamayı düşünün.

* Hizmet hesaplarını yükseltilmiş izinlere sahip herhangi bir grubun üyeleri olarak eklemeyin. 

* Gibi [ayrıcalıklı rollerin üyelerini numaralandırmak Için PowerShell kullanın](/powershell/module/azuread/get-azureaddirectoryrolemember)   
`Get-AzureADDirectoryRoleMember`ve ObjectType "hizmet sorumlusu" için filtre uygulayın.

   veya kullanın  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* Bir hizmet hesabının bir kaynakta erişebileceği işlevselliği sınırlandırmak için [OAuth 2,0 kapsamlarını kullanın](../develop/v2-permissions-and-consent.md) .
* Hizmet sorumluları ve yönetilen kimlikler, OAuth 2,0 kapsamlarını, oturum açmış bir kullanıcının kimliğine bürünmekte olan temsilci bir bağlamda veya uygulama bağlamında hizmet hesabı olarak kullanabilir. Uygulama bağlamı No öğesinde oturum açık.

* Uygun olduklarından emin olmak için kaynaklar için kapsam hizmeti hesapları isteğini denetleyin. Örneğin, bir hesap dosyaları istiyorsa. ReadWrite. All, gerçekten yalnızca File. Read. All ile mı ihtiyacı olduğunu değerlendirin. İzinler hakkında daha fazla bilgi için bkz. [Microsoft Graph izin başvurusu](/graph/permissions-reference).

* Kaynaklarınız için istenen erişimle uygulama veya API 'nin geliştiricisine güvendiğinizden emin olun.

**Süre**

* Hizmet hesabı kimlik bilgilerini (istemci gizli dizisi, sertifika) beklenen kullanım süresine göre sınırlandırın.

* Zamanlama, hizmet hesaplarının kullanımını ve amacını düzenli olarak gözden geçirir. Hesap kullanım süresi dolmadan önce gözden geçirmeler yapıldığından emin olun.

Amaç, kapsam ve gerekli izinlerin net bir şekilde anlaşıldıktan sonra hizmet hesabınızı oluşturun. 

[Yönetilen kimlikler oluşturma ve kullanma](../../app-service/overview-managed-identity.md?tabs=dotnet)

[Hizmet sorumlularını oluşturma ve kullanma](../develop/howto-create-service-principal-portal.md)

Mümkün olduğunda yönetilen bir kimlik kullanın. Yönetilen kimlik kullanmıyorsanız, hizmet sorumlusu kullanın. Bir hizmet sorumlusu kullanamaz ve ardından yalnızca bir Azure AD Kullanıcı hesabı kullanın.

 

## <a name="build-a-lifecycle-process"></a>Yaşam döngüsü işlemi oluşturma

Bir hizmet hesabının yaşam döngüsünü yönetmek, planlamayla başlar ve kalıcı silme işlemini sonlandırır. 

Bu makale daha önce planlama ve oluşturma bölümünü kapsamıştır. Ayrıca, izinleri izlemenizi, gözden geçirmeyi, hesabın devam eden kullanımını belirlemenizi ve sonunda hesabın sağlanması gerekir.

### <a name="monitor-service-accounts"></a>Hizmet hesaplarını izleme

Hizmet hesaplarınızın kullanım desenlerinin amaçlanan desenleri yansıttığından ve hizmet hesabının hala etkin bir şekilde kullanıldığından emin olmak için hizmet hesaplarınızı proaktif olarak izleyin.

**Aşağıdaki yöntemlerden birini kullanarak hizmet hesabı oturum açma işlemlerini toplayın ve izleyin:**

* Azure AD Sign-In günlüklerini Azure AD portalında kullanma.

* Azure AD Sign-In günlüklerini [Azure depolama](../../storage/index.yml), [Azure Event Hubs](../../event-hubs/index.yml)veya [Azure izleyici](../../azure-monitor/logs/data-platform-logs.md)'ye aktarma.


![Hizmet sorumlusu oturum açma ekranını gösteren ekran görüntüsü.](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**Sign-In günlüklerinde aramanız gereken zekası şunları içerir:**

* Kiracıda artık oturum açma izni olmayan hizmet hesapları var mı?

* Hizmet hesaplarının oturum açma desenleri değişiyor mu?

Azure AD oturum açma günlüklerini dışa aktarmanızı ve bunları Azure Sentinel gibi mevcut güvenlik bilgilerine ve olay yönetimi (SıEM) araçlarına aktarmanıza önerilir. Uyarı ve panolar oluşturmak için SıEM 'nizi kullanın.

### <a name="review-service-account-permissions"></a>Hizmet hesabı izinlerini gözden geçirme

, Kaldırılan izinleri ve hizmet hesapları tarafından erişilip düşürülemeyeceğini görmek için düzenli olarak gözden geçirin.

* Bir hizmet hesabına izin verilen kapsamları [denetlemek ve belgelemek üzere otomasyon oluşturmak](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) için [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) 'i kullanın.

* [Mevcut hizmet asıl kimlik bilgilerini gözden geçirmek](https://github.com/AzureAD/AzureADAssessment) ve bunların geçerliliğini denetlemek için PowerShell 'i kullanın.

* Hizmet sorumlusunun kimlik bilgilerini "hiç süre sonu" olarak ayarlamayın.

* Mümkün olduğunda Azure Keykasasında depolanan sertifikaları veya kimlik bilgilerini kullanın.

Microsoft 'un ücretsiz PowerShell örneği, hizmet sorumlusunun OAuth2 izin verir ve kimlik bilgileri bilgilerini toplayıp, verileri yorumlamak ve kullanmak için bir virgülle ayrılmış değerler dosyası (CSV) ve Power BI örnek panosu kaydeder. daha fazla bilgi için bkz [. Azuread/AzureADAssessment: bir Azure AD kiracı durumunu ve yapılandırmasını değerlendirmek Için araç (GitHub.com)](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>Hizmet hesabı kullanımını recertify

Hizmet hesaplarının sahipleri ve güvenlik ya da BT ekibi tarafından düzenli aralıklarla düzenli olarak incelendiğinden emin olmak için bir gözden geçirme işlemi oluşturun. 

**İşlem şunları içermelidir:**

* Her bir hizmet hesabının İnceleme döngüsünü belirleme (CMDB 'de belgelenmelidir).

* İnceleme başlamadan önce sahip ve güvenlik ya da BT ekiplerine yönelik iletişimler.

* İnceleme kaçırıldığında uyarı iletişiminin zamanlaması ve içeriği.

* Sahipler gözden geçirilmezse veya yanıt vermezse yapılacaklar hakkında yönergeler. Örneğin, gözden geçirme tamamlanana kadar hesabı devre dışı bırakmak (ancak silmeyebilirsiniz) isteyebilirsiniz.

* Yukarı akış ve aşağı akış bağımlılıklarını belirleme ve herhangi bir etkilerin diğer kaynak sahiplerine bildirme yönergeleri.

**İnceleme, sahibi ve BT iş ortağı sertifikalarını içermelidir:**

* Hesap hala gereklidir.

* Hesaba verilen izinler yeterlidir ve gereklidir ya da bir değişiklik istenir.

* Hesaba ve kimlik bilgilerine erişim denetlenir.

* Hesabın kullandığı kimlik bilgileri, hesabın değerlendirilme riskiyle ilgili olarak uygundur (kimlik bilgisi türü ve kimlik bilgisi ömrü)

* Hesabın risk Puanlama, son yeniden sertifika sonrasında değiştirilmedi

* Hesabın beklenen ömrü ve sonraki ön sertifika tarihi üzerinde bir güncelleştirme.

### <a name="deprovision-service-accounts"></a>Hizmet hesaplarını sağlamayı kaldırma

* * Şu koşullarda hizmet hesaplarının sağlamasını kaldırma: * * * *

* Hizmet hesabının oluşturulduğu betik veya uygulama kullanımdan kaldırıldı.

* Hizmet hesabı için kullanılan betik veya uygulama içindeki işlev (örneğin, belirli bir kaynağa erişim) kullanımdan kaldırıldı.


* Hizmet hesabı farklı bir hizmet hesabıyla değiştirilmiştir.

* Kimlik bilgilerinin zamanı dolmuştur veya hesap başka bir şekilde işlevsel değildir ve şikayetsiz değildir.

**Sağlama kaldırma işlemleri aşağıdaki görevleri içermelidir.**

1. İlişkili uygulama veya betiğin sağlaması geri alındıktan sonra, hizmet hesabı tarafından [oturum açma](../reports-monitoring/concept-sign-ins.md#sign-ins-report) işlemlerini ve kaynak erişimini izleyin.

   * Hesap hala etkinse, sonraki adımları uygulamadan önce nasıl kullanıldığını saptayın.
 
2. Bu bir yönetilen hizmet kimliği ise, hizmet hesabının oturum açmasını devre dışı bırakın, ancak dizinden kaldırmayın.

3. Hizmet hesabı için rol atamalarını ve OAuth2 onay onayları iptal edin.

4. Tanımlı bir süre ve Sahibe karşı çok uyarı ettikten sonra, hizmet hesabını dizinden silin.

## <a name="next-steps"></a>Sonraki adımlar
Azure hizmet hesaplarının güvenliğini sağlama hakkında daha fazla bilgi için bkz.

[Azure hizmet hesaplarına giriş](service-accounts-introduction-azure.md)

[Yönetilen kimliklerin güvenliğini sağlama](service-accounts-managed-identities.md)

[Hizmet ilkelerinin güvenliğini sağlama](service-accounts-principal.md)




 

