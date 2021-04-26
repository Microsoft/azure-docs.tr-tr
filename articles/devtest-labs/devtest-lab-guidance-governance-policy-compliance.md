---
title: Azure DevTest Labs Şirket ilkesi ve uyumluluğu
description: Bu makalede, Azure DevTest Labs altyapısı için Şirket ilkesi ve uyumluluğunu yöneten yönergeler sunulmaktadır.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 89fe380c3a8e0e0a82cc806bf19f9a3454041d94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592356"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Azure DevTest Labs altyapısını idare edin-Şirket ilkesi ve uyumluluğu
Bu makalede, Azure DevTest Labs altyapısı için Şirket ilkesi ve uyumluluğunu yöneten yönergeler sunulmaktadır. 

## <a name="public-vs-private-artifact-repository"></a>Ortak ve özel yapıt deposu

### <a name="question"></a>Soru
Kuruluşun, DevTest Labs 'de ortak yapıt deposu ve özel yapıt deposu kullanması gerekir mi?

### <a name="answer"></a>Yanıt
[Ortak yapıt deposu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , en yaygın olarak kullanılan bir ilk yazılım paketi kümesi sağlar. Ortak geliştirici araçları ve eklentilerini yeniden oluşturmak için zaman yatırmaya gerek kalmadan hızlı dağıtıma yardımcı olur. Kendi özel deposunu dağıtmayı seçebilirsiniz. Genel ve özel bir depoyu paralel olarak kullanabilirsiniz. Ortak depoyu devre dışı bırakmayı da tercih edebilirsiniz. Özel bir depoyu dağıtmaya yönelik ölçütler aşağıdaki soru ve noktalara göre yapılmalıdır:

- Kuruluşun, DevTest Labs sunumunun parçası olarak kurumsal lisanslı yazılıma sahip olması için bir gereksinimi var mı? Yanıt Evet ise, özel bir deponun oluşturulması gerekir.
- Kuruluş, genel sağlama sürecinin bir parçası olarak gerekli olan belirli bir işlem sağlayan özel yazılım geliştirebilir mi? Yanıt Evet ise, özel bir deponun dağıtılması gerekir.
- Kuruluşun idare ilkesi yalıtım gerektiriyorsa ve dış depolar kuruluş tarafından doğrudan yapılandırma yönetimi altında değilse, özel bir yapıt deposu dağıtılmalıdır. Bu işlemin bir parçası olarak, ortak deponun ilk kopyası özel depo ile kopyalanabilir ve tümleştirilebilir. Daha sonra, ortak depo devre dışı bırakılabilir, böylece kuruluş içinde hiç kimse artık erişemez. Bu yaklaşım, kuruluştaki tüm kullanıcıların kuruluş tarafından onaylanan tek bir depoya sahip olmasını ve yapılandırma drlarını en aza indirmenize olanak sağlar.

### <a name="single-repository-or-multiple-repositories"></a>Tek depo veya birden çok depo 

### <a name="question"></a>Soru
Bir kuruluş tek bir depoyu planlıyor veya birden çok depoya izin veriyor mu?

### <a name="answer"></a>Yanıt
Kuruluşunuzun genel idare ve yapılandırma yönetimi stratejisinin bir parçası olarak, merkezi bir depo kullanmanızı öneririz. Birden çok depo kullandığınızda, bu kişiler zaman içinde yönetilmeyen yazılımların siloları haline gelebilir. Merkezi bir depoyla, birden fazla ekip projeleri için bu depodan yapıları kullanabilir. Standartlaştırma, güvenlik ve yönetim kolaylığı sağlar ve çabalarınızın çoğaltılmasını ortadan kaldırır. Merkezileşmeyi bir parçası olarak, uzun süreli yönetim ve sürdürülebilirlik için önerilen uygulamalar aşağıdaki eylemlerdir:

- Azure Repos kimlik doğrulaması ve yetkilendirme için Azure aboneliğinin kullandığı Azure Active Directory kiracısıyla ilişkilendirin.
- Merkezi olarak yönetilen Azure Active Directory **Tüm DevTest Labs geliştiricileri** adlı bir grup oluşturun. Yapıt geliştirmeye katkıda bulunan tüm geliştiriciler bu gruba yerleştirilmelidir.
- Aynı Azure Active Directory grubu, Azure Repos deposuna ve laboratuvarına erişim sağlamak için kullanılabilir.
- Azure Repos, dallandırma veya dallama, birincil üretim deposundan ayrı bir geliştirme deposunda ayrı olarak kullanılmalıdır. İçerik yalnızca, doğru bir kod incelemesi sonrasında bir çekme isteğiyle Ana dala eklenir. Kod gözden geçireni değişikliği onayladığında, ana dalın bakımına sorumlu olan bir lider geliştiricisi, güncelleştirilmiş kodu birleştirir. 

## <a name="corporate-security-policies"></a>Kurumsal güvenlik ilkeleri

### <a name="question"></a>Soru
Kuruluş güvenlik ilkelerinin yerinde olması nasıl bir organizasyon olabilir?

### <a name="answer"></a>Yanıt
Bir kuruluş, aşağıdaki eylemleri gerçekleştirerek bunu elde edebilir:

1. Kapsamlı bir güvenlik ilkesi geliştirme ve yayımlama. İlke, using yazılımı, bulut varlıkları ile ilişkili kabul edilebilir kullanım kurallarını ifade ediler. Ayrıca ilkeyi açıkça ihlal ettiğini de tanımlar. 
2. Özel bir görüntü, özel yapıtlar ve Active Directory ile tanımlanmış güvenlik bölgesi içinde düzenlemeye izin veren bir dağıtım işlemi geliştirin. Bu yaklaşım, kurumsal sınır uygular ve ortak bir ortam denetimi kümesi ayarlar. Bu denetimler, bir geliştiricinin geliştirme sırasında göz önünde bulundurulması ve genel sürecinin bir parçası olarak güvenli bir geliştirme yaşam döngüsünü takip edebilir. Amaç Ayrıca geliştirmede çok kısıtlayıcı olmayan bir ortam sağlamaktır, ancak makul bir denetim kümesi de vardır. Laboratuvar sanal makineleri içeren kuruluş birimindeki (OU) Grup ilkeleri, üretimde bulunan toplam grup ilkelerinin bir alt kümesi olabilir. Alternatif olarak, belirlenen riskleri doğru bir şekilde azaltmak için ek bir küme olabilir.

## <a name="data-integrity"></a>Veri bütünlüğü

### <a name="question"></a>Soru
Bir kuruluş, uzaktan iletişim geliştiricilerin kod kaldıramayacağı veya kötü amaçlı yazılım ya da onaylanmamış yazılımlar sunabileceğine emin olmak için veri bütünlüğünü nasıl

### <a name="answer"></a>Yanıt
DevTest Labs 'de işbirliği yapmak için dış danışmanların, yüklenicilerin veya ' de uzaktan iletişim olan çalışanların tehdidi hafifletmek için birkaç denetim katmanı vardır. 

Daha önce belirtildiği gibi, ilk adımda, kabul edilebilir kullanım ilkesi drafted ve bir Kullanıcı ilkeyi ihlal ettiğinde sonuçları açıkça özetleyen bir bakış tanımlanmış olmalıdır. 

Uzaktan erişim için denetimlerin ilk katmanı, doğrudan laboratuvara bağlı olmayan bir VPN bağlantısı aracılığıyla uzaktan erişim ilkesi uygulamaktır. 

Denetimlerin ikinci katmanı, Uzak Masaüstü aracılığıyla kopyalamayı ve yapıştırmayı önleyen bir Grup İlkesi nesnesi kümesi uygulamaktır. Ağ ilkesi, ortamdan FTP ve RDP hizmetleri gibi giden hizmetlere izin vermek için uygulanabilir. Kullanıcı tanımlı yönlendirme, tüm Azure ağ trafiğini şirket içi olarak geri zorlayabilir, ancak yönlendirme, içerik ve oturumları taramak için bir proxy aracılığıyla denetlenmediği müddetçe verilerin karşıya yüklenmesine izin verebilecek tüm URL 'Leri hesaba işleyemedi. Ortak IP 'Ler, DevTest Labs 'i destekleyen sanal ağ dahilinde bir dış ağ kaynağının köprülemesini sağlamak için kısıtlanabilir.

Sonuç olarak, aynı tür kısıtlamaların kuruluş genelinde uygulanması gerekir, bu da bir içerik gönderisini kabul edebilen tüm olası çıkarılabilir medya veya dış URL yöntemlerinin de hesaba eklenmesi gerekir. Güvenlik ilkesini gözden geçirmek ve uygulamak için güvenlik uzmanınıza başvurun. Daha fazla öneri için bkz. [Microsoft Cyber güvenliği](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Sonraki adımlar
Bkz. [uygulama geçişi ve tümleştirmesi](devtest-lab-guidance-governance-application-migration-integration.md).
