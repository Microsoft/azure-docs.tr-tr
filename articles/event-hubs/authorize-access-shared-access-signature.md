---
title: Azure Event Hubs paylaşılan erişim imzasıyla erişim yetkisi verme
description: Bu makalede, paylaşılan erişim Imzaları (SAS) kullanarak Azure Event Hubs kaynaklarına erişim yetkisi verme hakkında bilgi sağlanır.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992802"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Paylaşılan erişim Imzalarını kullanarak Event Hubs kaynaklarına erişimi yetkilendirme
Paylaşılan erişim imzası (SAS), Event Hubs ad alanındaki kaynaklara sınırlı erişim vermek için bir yol sağlar. SAS koruyucuları, yetkilendirme kurallarına göre Event Hubs kaynaklara erişim sağlar. Bu kurallar bir ad alanında ya da bir varlıkta (Olay Hub 'ı veya konu başlığı) yapılandırılır. Bu makalede SAS modeline genel bir bakış sağlanır ve SAS en iyi uygulamaları incelenir.

## <a name="what-are-shared-access-signatures"></a>Paylaşılan erişim imzaları nelerdir?
Paylaşılan erişim imzası (SAS), yetkilendirme kurallarına göre Event Hubs kaynaklarına temsilci erişimi sağlar. Yetkilendirme kuralı bir ada sahiptir, belirli haklara göre ilişkilendirilir ve bir çift şifreleme anahtarı taşır. SAS belirteçleri oluşturmak için Event Hubs istemcilerle veya kendi kodunuzda kural adını ve anahtarını kullanırsınız. İstemci daha sonra istenen işlem için kanıtlamak üzere Event Hubs belirtecini geçirebilir.

SAS, basit belirteçler kullanan talep tabanlı bir yetkilendirme mekanizmasıdır. SAS kullanarak, anahtarlar hiçbir şekilde hatta hiçbir şekilde geçirilmez. Anahtarlar, daha sonra hizmet tarafından doğrulanabileolabilecek bilgileri şifreli olarak imzalamak için kullanılır. SAS, istemcinin bir yetkilendirme kuralı adının ve eşleşen bir anahtarın hemen elinde bulunduğu bir Kullanıcı adı ve parola şemasına benzer şekilde kullanılabilir. SAS, bir güvenlik belirteci hizmetinden hiçbir zaman sınırlı ve imzalı bir erişim belirteci aldığı bir Federasyon güvenlik modeliyle benzer bir şekilde kullanılabilir.

> [!NOTE]
> Azure Event Hubs, Azure Active Directory (Azure AD) kullanarak Event Hubs kaynaklara yetki verme desteği sağlar. Azure AD tarafından döndürülen OAuth 2,0 belirtecini kullanarak kullanıcıları veya uygulamaları yetkilendirmek, paylaşılan erişim imzaları (SAS) üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Azure AD ile, belirteçlerin kodunuzda depolanması ve olası güvenlik açıklarına karşı risk altında olması gerekmez.
>
> Microsoft, mümkünse Azure Event Hubs uygulamalarınızın Azure AD ile kullanılmasını önerir. Daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure Event Hubs kaynağına erişimi yetkilendirme](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> SAS (paylaşılan erişim Imzaları) belirteçleri kaynaklarınızın korunması için önemlidir. Ayrıntı düzeyi sağlarken, SAS istemcilere Event Hubs kaynaklarınıza erişim izni verir. Ortak olarak paylaşılmamalıdır. Paylaşım yaparken, sorun giderme nedenleri için gerekliyse, herhangi bir günlük dosyasının azaltılmış bir sürümünü kullanmayı veya günlük dosyalarından SAS belirteçlerini (varsa) silmeyi düşünün ve ekran görüntülerinin SAS bilgilerini içermediğinden emin olun.

## <a name="shared-access-authorization-policies"></a>Paylaşılan erişim yetkilendirme ilkeleri
Her bir Event Hubs ad alanı ve Event Hubs varlığı (bir olay hub 'ı örneği veya bir Kafka konusu), kurallardan oluşan bir paylaşılan erişim yetkilendirme ilkesine sahiptir. Ad alanı düzeyindeki ilke, bireysel ilke yapılandırmasından bağımsız olarak ad alanındaki tüm varlıklar için geçerlidir.
Her yetkilendirme ilkesi kuralı için üç bilgi parçasına karar verirsiniz: ad, kapsam ve haklar. Ad, bu kapsamdaki benzersiz bir addır. Kapsam, söz konusu kaynağın URI 'sidir. Event Hubs ad alanı için kapsam, gibi tam etki alanı adıdır (FQDN) `https://<yournamespace>.servicebus.windows.net/`.

İlke kuralı tarafından belirtilen haklar şu bir bileşim olabilir:
- **Gönder** : varlığa ileti gönderme hakkını verir
- **Dinle** : varlığa dinleme veya alma hakkını verir
- **Yönet** : varlıkların oluşturulması ve silinmesi dahil olmak üzere ad alanının topolojisini yönetme hakkını verir

> [!NOTE]
> **Yönet** hakkı, **gönderme** ve **dinleme** haklarını içerir.

Ad alanı veya varlık ilkesi 12 ' ye kadar paylaşılan erişim yetkilendirme kuralı tutabilir, her biri temel hakları ve gönderme ve dinleme birleşimini kapsayan üç kural kümesi için yer sağlar. Bu sınır, SAS ilke deposunun bir kullanıcı veya hizmet hesabı deposu olması amaçlanmayan alt çizgileri çizer. Uygulamanızın Kullanıcı veya hizmet kimliklerine göre Event Hubs kaynaklarına erişim vermesi gerekiyorsa, bir kimlik doğrulama ve erişim denetiminden sonra SAS belirteçleri veren bir güvenlik belirteci hizmeti uygulamalıdır.

Bir yetkilendirme kuralına **birincil anahtar** ve **İkincil anahtar**atanır. Bu anahtarlar, şifreli olarak tanımlayıcı anahtarlardır. Onları kaybetmeyin veya sızmayın. Azure portal her zaman kullanılabilir. Oluşturulan anahtarlardan birini kullanabilir ve istediğiniz zaman yeniden oluşturabilirsiniz. İlkede bir anahtarı yeniden oluşturursanız veya değiştirirseniz, o anahtara göre daha önce verilen tüm belirteçler anında geçersiz olur. Ancak, bu tür belirteçlere göre oluşturulan devam eden bağlantılar, belirtecin süresi dolana kadar çalışmaya devam edecektir.

Bir Event Hubs ad alanı oluşturduğunuzda, ad alanı için **RootManageSharedAccessKey** adlı bir ilke kuralı otomatik olarak oluşturulur. Bu ilke, tüm ad alanı için **yönetme** izinlerine sahiptir. Bu kuralı bir yönetim kök hesabı gibi işlemeniz önerilir ve uygulamanızda kullanmayın. Portalda ad alanı için **yapılandırma** sekmesinde PowerShell veya Azure CLI aracılığıyla ek ilke kuralları oluşturabilirsiniz.

## <a name="best-practices-when-using-sas"></a>SAS kullanırken en iyi uygulamalar
Uygulamalarınızda paylaşılan erişim imzaları kullandığınızda, iki olası riski bilmeniz gerekir:

- SAS sızsa, onu alan herkes tarafından kullanılabilir ve bu da Event Hubs kaynaklarınızın güvenliğini sağlayabilir.
- Bir istemci uygulaması için belirtilen bir SAS dolarsa ve uygulama hizmetinizden yeni bir SAS alamadıysa, uygulamanın işlevselliği ipuçda olabilir.

Paylaşılan erişim imzalarını kullanmaya yönelik aşağıdaki öneriler, bu riskleri azaltmaya yardımcı olabilir:

- **Gerektiğinde ISTEMCILERIN SAS 'yi otomatik olarak yenilemesini sağlayabilirsiniz**: SAS sağlayan hizmetin kullanılamaması durumunda istemciler, yeniden denemeler için zaman aşımı süresine izin vermek üzere, sa 'yı süre sonundan önce yenilemelidir. SAS 'nizin, sona erme döneminde tamamlanması beklenen kısa süreli, kısa süreli işlemler için kullanılması gerekiyorsa, SAS 'nin yenilenmesi beklenmediğinden bu işlem gereksiz olabilir. Ancak, SAS üzerinden düzenli olarak istek yapan istemciniz varsa, süre sonu olma olasılığı oynatma olur. Temel olarak, istemcinin yenilemeyi yeterince erken istediğini güvence altına almak için (daha önce belirtildiği gibi), istemcinin yenileme gereksinimini yeterince erken (daha önce belirtildiği gibi) dengelenmesi gerekir (başarılı bir yenilemeden önce SAS süresi dolduğunda kesintiye uğramamak için).
- **SAS başlangıç zamanına dikkat edin**: SAS için başlangıç saatini **Şimdi**olarak ayarlarsanız, saat farkı (farklı makinelere göre geçerli zaman farkları) nedeniyle, hatalardan bazıları ilk birkaç dakika boyunca zaman zaman gözlemlenebilir. Genel olarak, başlangıç saatini geçmişte en az 15 dakika olacak şekilde ayarlayın. Ya da, hiç ayarlama yapmayın, bu, tüm durumlarda hemen geçerli hale gelir. Aynı genellikle süre sonu zamanı için de geçerlidir. Her türlü isteğe bağlı olarak 15 dakikalık saatin ölçeğini çarpıtmanız gerektiğini unutmayın. 
- **Erişilecek kaynağa özel olun**: En iyi güvenlik uygulaması, kullanıcıya gereken en düşük ayrıcalıkları sağlamaktır. Bir kullanıcının yalnızca tek bir varlığa okuma erişimi olması gerekiyorsa, tüm varlıklara okuma/yazma/silme erişimi değil, onlara bu tek varlığa yönelik okuma erişimi izni verin. Bu, SAS 'nin bir saldırgan içinde daha az gücü olduğundan, SAS tehlikeye atılırsa hasar azaltır.
- **Her zaman SAS kullanma**: Bazen Event Hubs karşı belirli bir işlemle ilişkili riskler SAS avantajlarından yararlanır. Bu gibi işlemler için, iş kuralı doğrulama, kimlik doğrulama ve denetim sonrasında Event Hubs yazan bir orta katman hizmeti oluşturun.
- **Her zaman https kullan**: Her zaman bir SAS oluşturmak veya dağıtmak için HTTPS kullanın. Bir SAS HTTP üzerinden geçirilirse ve bu bağlantıyı ele alırsanız bir saldırgan, SAS 'yi okuyabilir ve bunu amaçlanan kullanıcının sahip olduğu gibi kullanabilir, önemli verilerin güvenliğini sağlayabilir veya kötü amaçlı kullanıcı tarafından verilerin bozulmasına izin verebilir.

## <a name="conclusion"></a>Sonuç
Paylaşılan erişim imzaları, istemcilerinize Event Hubs kaynaklara sınırlı izinler sağlamak için faydalıdır. Azure Event Hubs kullanan tüm uygulamalar için güvenlik modelinin önemli bir parçasıdır. Bu makalede listelenen en iyi yöntemleri izlerseniz, uygulamanızın güvenliğini tehlikeye atmadan kaynaklarınıza erişim esnekliği sağlamak için SAS kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki ilgili makalelere bakın: 

- [Azure Active Directory kullanarak bir uygulamadan Azure Event Hubs istek kimliklerini doğrulama](authenticate-application.md)
- [Event Hubs kaynaklara erişmek için Azure Active Directory ile yönetilen bir kimliğin kimliğini doğrulama](authenticate-managed-identity.md)
- [Paylaşılan erişim Imzalarını kullanarak Azure Event Hubs istek kimliklerini doğrulama](authenticate-shared-access-signature.md)
- [Azure Active Directory kullanarak Event Hubs kaynaklarına erişim yetkisi verme](authorize-access-azure-active-directory.md)


