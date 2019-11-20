---
title: Azure DevTest Labs SSS | Microsoft Docs
description: Azure DevTest Labs hakkında sık sorulan soruların yanıtlarını bulun.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2019
ms.author: spelluru
ms.openlocfilehash: fb66fa2d4a6a03841fa057c4d1982b7bf4c6976d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565325"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs SSS
Azure DevTest Labs hakkındaki en yaygın soruların bazılarına yanıt alın.

## <a name="blog-post"></a>Blog gönderisi
DevTest Labs ekip blogumuz, 20 Mart 2019 itibariyle kullanımdan kaldırılmıştır. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Özellik güncelleştirmelerini Şu anda nereden izleyebilirim?
Bu andan itibaren, Azure bloguna ve Azure güncelleştirmelerine yönelik özellik güncelleştirmelerini ve bilgilendirici blog yayınlarını naklettireceğiz. Bu blog gönderileri Ayrıca gerektiğinde belgelerimize bağlanır.

DevTest Labs 'de yeni özellikler hakkında bilgi sahibi olmak için DevTest [Labs Azure bloguna](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) ve [DevTest Labs Azure güncelleştirmelerine](https://azure.microsoft.com/updates/?product=devtest-lab) abone olun.

### <a name="what-happens-to-the-existing-blog-posts"></a>Var olan blog gönderilerine ne olur?
Şu anda var olan blog gönderilerini (kesinti güncelleştirmeleri hariç) [DevTest Labs belgelerimize](devtest-lab-overview.md)geçirmeye çalışıyoruz. MSDN blogu kullanım dışı olduğunda, DevTest Labs için belgelere genel bakış sayfasına yönlendirilir. Yeniden yönlendirildikten sonra ' filtre ölçütü ' başlığında Aradığınız makaleyi arayabilirsiniz. Tüm gönderilerini henüz geçirdik, ancak bu ayın sonuna kadar gerçekleştirilmelidir. 


### <a name="where-do-i-see-outage-updates"></a>Kesinti güncelleştirmelerini nereden görüyorum?
Artık sonraki sürümlerde Twitter işleyicimizi kullanarak kesinti güncellemeleri göndereceğiz. Kesintiler ve bilinen hatalar hakkında en son güncelleştirmeleri almak için bizi Twitter 'da izleyin.

### <a name="twitter"></a>Twitter
Twitter tanıtıcımız:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Genel
### <a name="what-if-my-question-isnt-answered-here"></a>Sorum burada yanıtlanmazsa ne olacak?
Sorunuz burada listelenmiyorsa bize bilgi verin. bu nedenle bir yanıt bulmanıza yardımcı olabiliriz.

- Bu SSS sonunda bir soru gönderin. 
- Daha geniş bir hedef kitleye ulaşmak için [Azure DEVTEST Labs MSDN forumuna](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)bir soru gönderin. Azure DevTest Labs ekiple ve topluluktaki diğer üyelerle birlikte katılın.
- Özellik istekleri için isteklerinizi ve fikirlerinizi [Azure DevTest Labs Kullanıcı seslerine](https://feedback.azure.com/forums/320373-azure-devtest-labs)gönderebilirsiniz.

### <a name="what-is-a-microsoft-account"></a>Microsoft hesabı nedir?
Microsoft hesabı, Microsoft cihazları ve hizmetleriyle yaptığınız neredeyse her şey için kullandığınız bir hesaptır. Skype, Outlook.com, OneDrive, Windows Phone, Azure ve Xbox Live 'da oturum açmak için kullandığınız bir e-posta adresi ve parolasıdır. Tek bir hesap, dosyalarınıza, fotoğraflarınızın, kişilerinizin ve ayarlarınızın size herhangi bir cihazda izleyebileceği anlamına gelir.
 
> [!NOTE]
> Bir Windows Live ID olarak adlandırılabilmesi için kullanılan bir Microsoft hesabı.

### <a name="why-should-i-use-azure-devtest-labs"></a>Neden Azure DevTest Labs kullanmalıyım?
Azure DevTest Labs takım zaman ve paradan tasarruf edebilir. Geliştiriciler, çeşitli farklı tabanlar kullanarak kendi ortamlarını oluşturabilir. Ayrıca, uygulamaları hızla dağıtmak ve yapılandırmak için yapıtlar da kullanabilirler. Özel resimleri ve formülleri kullanarak, sanal makineleri (VM 'Ler) şablon olarak kaydedebilir ve ekip genelinde kolayca yeniden oluşturabilirsiniz. DevTest Labs Ayrıca laboratuvar yöneticilerinin, bir ekibin ortamlarını azaltmak ve yönetmek için kullanabileceği birkaç yapılandırılabilir ilke sunar. Bu ilkeler otomatik kapatmalar, maliyet eşiği, Kullanıcı başına en fazla VM ve maksimum VM boyutu içerir. DevTest Labs hakkında daha ayrıntılı bir açıklama için bkz. [genel bakış](devtest-lab-overview.md) veya [tanıtım videosu](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>"Sorunsuz self servis" ne anlama geliyor?
Sorunsuz Self servis, geliştiricilerin ve Test edicilerin gerektiğinde kendi ortamlarını oluşturmasıdır. Yöneticiler, DevTest Labs 'in uygun erişimi ayarlamanıza, atık ve denetim maliyetlerini en aza indirmenize yardımcı olduğunu bilmenin güvenliğine sahiptir. Yöneticiler hangi VM boyutlarına izin verileceğini, en fazla VM sayısını ve VM 'Lerin başlatılıp kapanmakta olduğunu belirtebilir. DevTest Labs Ayrıca, laboratuvar kaynaklarının nasıl kullanıldığının farkında olmanıza yardımcı olmak için maliyetleri izlemeyi ve uyarıları ayarlamayı kolaylaştırır.

### <a name="how-can-i-use-devtest-labs"></a>DevTest Labs 'i nasıl kullanabilirim?
DevTest Labs, geliştirme veya test ortamları istediğinizde yararlıdır ve bunları hızlı bir şekilde yeniden oluşturmak veya maliyet tasarrufu ilkeleri kullanarak bunları yönetmek ister.
Müşterilerimizin DevTest Labs 'i şu şekilde kullanmasının bazı senaryoları aşağıda verilmiştir:

- Geliştirme ve test ortamlarını tek bir yerde yönetin. Ekip genelinde yapıları paylaşmak için maliyetleri azaltmak ve özel görüntüler oluşturmak için ilkeleri kullanın.
- Geliştirme aşamaları boyunca disk durumunu kaydetmek için özel görüntüler kullanarak bir uygulama geliştirin.
- İlerlemeye ilişkin maliyeti izleyin.
- Kalite güvencesi testi için toplu test ortamları oluşturun.
- Yapıları ve formülleri kullanarak bir uygulamayı çeşitli ortamlarda kolayca yapılandırabilir ve yeniden oluşturun.
- VM 'Leri hack maratonları (işbirliğine dayalı geliştirme veya test çalışması) için dağıtın ve ardından olay sona erdiğinde onları kolayca sağlayın.

### <a name="how-am-i-billed-for-devtest-labs"></a>DevTest Labs için nasıl faturalandırılırım?
DevTest Labs ücretsiz bir hizmettir. Laboratuvar oluşturma ve DevTest Labs 'de ilkeleri, şablonları ve yapıtları yapılandırma ücretsizdir. Yalnızca Laboratuvarlarınızda kullanılan VM 'Ler, depolama hesapları ve sanal ağlar gibi Azure kaynakları için ödeme yaparsınız. Laboratuvar kaynaklarının maliyeti hakkında daha fazla bilgi için bkz. [Azure DevTest Labs fiyatlandırması](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Güvenlik

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>DevTest Labs 'deki farklı güvenlik düzeyleri nelerdir?
Güvenlik erişimi rol tabanlı Access Control (RBAC) tarafından belirlenir. Erişimin nasıl çalıştığını öğrenmek için, RBAC tarafından tanımlanan bir izin, rol ve kapsam arasındaki farkları öğrenmenize yardımcı olur.

- **İzin**: İzin, belirli bir eyleme tanımlı bir erişimdir. Örneğin, bir izin tüm sanal makinelere okunabilir.
- **Rol**: Rol, gruplandırılabilen ve bir kullanıcıya atanabilecek bir izinler kümesidir. Örneğin, abonelik sahibi rolüne sahip bir kullanıcının abonelik içindeki tüm kaynaklara erişimi vardır.
- **Kapsam**: Kapsam, bir Azure kaynağı hiyerarşisinde yer alan bir düzeydir. Örneğin, bir kapsam bir kaynak grubu, tek bir laboratuvar veya tüm abonelik olabilir.

DevTest Labs kapsamı içinde, Kullanıcı izinlerini tanımlayan iki tür rol vardır:

- **Laboratuvar sahibi**: Laboratuvar sahibinin laboratuvardaki tüm kaynaklara erişimi vardır. Laboratuvar sahibi ilkeleri değiştirebilir, herhangi bir VM 'ye okuyup yazabilir, sanal ağı değiştirebilir ve benzeri devam edebilir.
- **Laboratuvar kullanıcısı**: Laboratuvar kullanıcısı VM 'Ler, ilkeler ve sanal ağlar gibi tüm laboratuvar kaynaklarını görüntüleyebilir. Ancak, bir laboratuvar kullanıcısı ilkeleri veya diğer kullanıcılar tarafından oluşturulan VM 'Leri değiştiremez.

Ayrıca, DevTest Labs 'de özel roller de oluşturabilirsiniz. DevTest Labs 'de özel roller oluşturmayı öğrenmek için bkz. [belirli laboratuvar ilkelerine Kullanıcı Izinleri verme](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Kapsamlar hiyerarşik olduğundan, bir kullanıcının belirli bir kapsamda izinleri olduğunda, kullanıcıya kapsamdaki her alt düzey kapsamda bu izinler otomatik olarak verilir. Örneğin, bir kullanıcıya abonelik sahibi rolü atanırsa, kullanıcının bir abonelikteki tüm kaynaklara erişimi vardır. Bu kaynaklar VM 'Leri, sanal ağları ve laboratuvarları içerir. Abonelik sahibi, laboratuvar sahibinin rolünü otomatik olarak devralır. Ancak, tersi doğru değildir. Laboratuvar sahibinin, abonelik düzeyinden daha düşük bir kapsam olan bir laboratuvara erişimi vardır. Bu nedenle, bir laboratuvar sahibi VM 'Leri, sanal ağları veya laboratuvar dışındaki diğer kaynakları göremez.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Geliştirici/test işlerini yapabilirken yönetebilmemesini sağlamak için DevTest Labs Ortamlarım için rol tabanlı erişim denetimi tanımlama Nasıl yaparım?.
Geniş bir düzen mevcuttur, ancak ayrıntı kuruluşunuza bağlıdır.

Merkezi olarak yalnızca gerekli olan nedir ve proje ve uygulama ekiplerinin gerekli denetim düzeyine sahip olmasını sağlar. Genellikle, bu, aboneliğin sahibi olduğu ve ağ yapılandırması gibi çekirdek BT işlevlerini işleyeceği anlamına gelir. Bir abonelik için **sahip** kümesi küçük olmalıdır. Bu sahipler, ihtiyacınız olduğunda ek sahipleri aday yapabilir veya "genel IP yok" gibi abonelik düzeyinde ilkeler uygulayabilir.

Katman1 veya katman 2 desteği gibi bir abonelik genelinde erişim gerektiren kullanıcıların bir alt kümesi olabilir. Bu durumda, bu kullanıcılara, kaynakları yönetebilmeleri, ancak kullanıcı erişimi sağlamamasını veya ilke ayarlamanıza olanak tanımak için **katkıda** bulunan erişimini sağlamanızı öneririz.

DevTest Labs kaynağı, proje/uygulama takımına yakın olan sahiplere ait olmalıdır. Bunun nedeni, makineler ve gerekli yazılımlar için gereksinimlerini anladıkları içindir. Çoğu kuruluşta, bu DevTest Labs kaynağının sahibi genellikle proje/geliştirme lideridir. Bu sahip, Laboratuvar ortamındaki kullanıcıları ve ilkeleri yönetebilir ve DevTest Labs ortamındaki tüm VM 'Leri yönetebilir.

Proje/uygulama ekibi üyeleri **DevTest Labs kullanıcıları** rolüne eklenmelidir. Bu kullanıcılar sanal makineler oluşturabilir (Laboratuvar ve abonelik düzeyindeki ilkelerle birlikte). Bunlar, kendi sanal makinelerini de yönetebilir. Diğer kullanıcılara ait sanal makineleri yönetemez.

Daha fazla bilgi için bkz. [Azure Kurumsal yapı iskelesi – seçkin abonelik idare belgeleri](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Kullanıcıların belirli bir görevi yapmasına izin vermek için Nasıl yaparım? bir rol oluşturmak mı istiyorsunuz?
Özel roller oluşturma ve bir role izin atama hakkında kapsamlı bir makale için bkz. [belirli laboratuvar ilkelerine Kullanıcı Izinleri verme](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Laboratuvardaki tüm VM 'Leri başlatma ve durdurma iznine sahip olan **DevTest Labs Ileri düzey Kullanıcı**rolünü oluşturan bir betik örneği aşağıda verilmiştir:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Kuruluş güvenlik ilkelerinin yerinde olması nasıl bir organizasyon olabilir?
Bir kuruluş, aşağıdaki eylemleri gerçekleştirerek bunu elde edebilir:

- Kapsamlı bir güvenlik ilkesi geliştirme ve yayımlama. İlke, using yazılımı, bulut varlıkları ile ilişkili kabul edilebilir kullanım kurallarını ifade ediler. Ayrıca ilkeyi açıkça ihlal ettiğini de tanımlar.
- Özel bir görüntü, özel yapıtlar ve Active Directory ile tanımlanmış güvenlik bölgesi içinde düzenlemeye izin veren bir dağıtım işlemi geliştirin. Bu yaklaşım, kurumsal sınır uygular ve ortak bir ortam denetimi kümesi ayarlar. Bu denetimler, bir geliştiricinin geliştirme sırasında göz önünde bulundurulması ve genel sürecinin bir parçası olarak güvenli bir geliştirme yaşam döngüsünü takip edebilir. Amaç Ayrıca geliştirmede daha fazla kısıtlayıcı olmayan bir ortam sağlamaktır, ancak makul bir denetim kümesi de vardır. Laboratuvar sanal makineleri içeren kuruluş birimindeki (OU) Grup ilkeleri, üretimde bulunan toplam grup ilkelerinin bir alt kümesi olabilir. Alternatif olarak, belirlenen riskleri doğru bir şekilde azaltmak için ek bir küme olabilir.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Bir kuruluş, uzaktan iletişim geliştiricilerin kod kaldıramayacağı veya kötü amaçlı yazılım ya da onaylanmamış yazılımlar sunabileceğine emin olmak için veri bütünlüğünü nasıl
DevTest Labs 'de işbirliği yapmak için dış danışmanların, yüklenicilerin veya ' de uzaktan iletişim olan çalışanların tehdidi hafifletmek için birkaç denetim katmanı vardır.

Daha önce belirtildiği gibi, ilk adımda, kabul edilebilir kullanım ilkesi drafted ve bir Kullanıcı ilkeyi ihlal ettiğinde sonuçları açıkça özetleyen bir bakış tanımlanmış olmalıdır.

Uzaktan erişim için denetimlerin ilk katmanı, bir uzaktan erişim ilkesini doğrudan laboratuvara bağlı olmayan bir VPN bağlantısı aracılığıyla uygulamaktır.

Denetimlerin ikinci katmanı, Uzak Masaüstü aracılığıyla kopyalamayı ve yapıştırmayı önleyen bir Grup İlkesi nesnesi kümesi uygulamaktır. Ağ ilkesi, ortamdan FTP ve RDP hizmetleri gibi giden hizmetlere izin vermek için uygulanabilir. Kullanıcı tanımlı yönlendirme, tüm Azure ağ trafiğini şirket içi 'e geri zorlayabilir, ancak yönlendirme, içerik ve oturumları taramak için bir proxy üzerinden denetlenmediği müddetçe verilerin karşıya yüklenmesine izin verebilecek tüm URL 'Leri hesaba işleyemedi. Ortak IP 'Ler, DevTest Labs 'i destekleyen sanal ağ dahilinde bir dış ağ kaynağının köprülemesini sağlamak için kısıtlanabilir.

Sonuç olarak, aynı tür kısıtlamaların kuruluş genelinde uygulanması gerekir, bu, tüm olası çıkarılabilir medya veya dış URL 'lerin bir içerik gönderisini kabul edebilecek şekilde hesaba neden olur. Güvenlik ilkesini gözden geçirmek ve uygulamak için güvenlik uzmanınıza başvurun. Daha fazla öneri için bkz. [Microsoft Cyber güvenliği](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Laboratuvar yapılandırması

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Kaynak Yöneticisi şablonundan laboratuvar oluşturmak Nasıl yaparım??
Farklı olarak dağıtabileceğiniz veya laboratuvarınız için özel şablonlar oluşturmak üzere değişiklik yaptığınız bir [laboratuvar Azure Resource Manager şablonlarının GitHub deposunu](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) sunuyoruz. Her şablonda, Laboratuvarı kendi Azure aboneliğinizde olduğu gibi dağıtmaya yönelik bir bağlantı bulunur. Ya da, [PowerShell veya Azure CLI kullanarak](../azure-resource-manager/resource-group-template-deploy.md)şablonu özelleştirebilir ve dağıtabilirsiniz.


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Her makinenin kendi kaynak grubunda olması yerine ortak bir kaynak grubunda oluşturulacak tüm sanal makinelerime sahip olabilir miyim? 
Evet, bir laboratuar sahibi olarak, laboratuvarın kaynak grubu ayırmasını işlemesini ya da belirttiğiniz ortak bir kaynak grubunda tüm sanal makinelerin oluşturulmasını sağlayabilirsiniz. 

Ayrı kaynak grubu senaryosu:
-   DevTest Labs, seçtiğiniz her ortak/özel IP sanal makinesi için yeni bir kaynak grubu oluşturur
-   DevTest Labs, aynı boyuta ait olan paylaşılan IP makineleri için bir kaynak grubu oluşturur.

Ortak kaynak grubu senaryosu:
-   Tüm sanal makineler, belirttiğiniz ortak kaynak grubunda sona erdirir. [Laboratuvar için daha fazla kaynak grubu ayırması](https://aka.ms/RGControl)öğrenin. 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Nasıl yaparım? DevTest Labs ortamımı genelinde bir adlandırma kuralına sahip mi?
Geçerli kurumsal adlandırma kurallarını Azure işlemlerine genişletmek ve bunları DevTest Labs ortamında tutarlı hale getirmek isteyebilirsiniz. DevTest Labs dağıtımı yaparken, belirli başlangıç ilkelerine sahip olmanız önerilir. Bu ilkeleri, tutarlılığı zorlamak için bir merkezi betik ve JSON şablonları ile dağıtırsınız. Adlandırma ilkeleri, abonelik düzeyinde uygulanan Azure ilkeleri aracılığıyla uygulanabilir. Azure Ilkesi için JSON örnekleri için bkz. [Azure ilke örnekleri](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Aynı abonelikte kaç tane laboratuvar oluşturabilirim?
Abonelik başına oluşturulabilecek laboratuvarların sayısında belirli bir sınır yoktur. Ancak, abonelik başına kullanılan kaynak miktarı sınırlıdır. [Azure aboneliklerine yönelik sınırlar ve Kotalar](../azure-subscription-service-limits.md) hakkında bilgi edinmek için [Bu sınırları nasıl artırabileceğiniz](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)hakkında bilgi edinebilirsiniz.


### <a name="how-many-vms-can-i-create-per-lab"></a>Laboratuvar başına kaç VM oluşturabilirim?
Laboratuvar başına oluşturulabilecek VM sayısında belirli bir sınır yoktur. Ancak, kullanılan kaynaklar (VM çekirdekleri, genel IP adresleri vb.) abonelik başına sınırlıdır. [Azure aboneliklerine yönelik sınırlar ve Kotalar](../azure-subscription-service-limits.md) hakkında bilgi edinmek için [Bu sınırları nasıl artırabileceğiniz](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)hakkında bilgi edinebilirsiniz.

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Nasıl yaparım?, laboratuvar başına Kullanıcı oranını ve bir kuruluşta gerekli olan toplam laboratuvarların sayısını belirlemektir mi?
Aynı geliştirme projesiyle ilişkili iş birimlerinin ve geliştirme gruplarının aynı laboratuvarda ilişkili olmasını öneririz. Her iki gruba da aynı ilke, görüntü ve kapalı ilke türlerinin uygulanmasını sağlar.

Coğrafi sınırları da dikkate almanız gerekebilir. Örneğin, Kuzey Doğu Birleşik Devletler (US) geliştiriciler Doğu ABD2 sağlanan bir laboratuvarı kullanabilir. Ve Dallas, Texas ve Denver 'daki geliştiriciler ABD Orta Güney bir kaynağı kullanmak için yönlendirilebilir. Dış üçüncü taraflarla işbirliğine dayalı bir çaba varsa, bunlar dahili geliştiriciler tarafından kullanılmayan bir laboratuvara atanabilir.

Ayrıca, Azure DevOps Projeleri içindeki belirli bir proje için de laboratuvar kullanabilirsiniz. Daha sonra, her iki kaynak kümesine erişime izin veren belirtilen bir Azure Active Directory grubu aracılığıyla güvenliği uygularsınız. Laboratuvara atanan sanal ağ, kullanıcıları birleştirmek için başka bir sınır olabilir.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Laboratuvardaki kaynakların silinmesini nasıl önleyebiliriz?
Yalnızca yetkili kullanıcıların kaynakları silmesi veya laboratuvar ilkelerini değiştirmesi için uygun izinleri laboratuvar düzeyinde ayarlamanızı öneririz. Geliştiricilerin **DevTest Labs Kullanıcı** grubu içine yerleştirilmesi gerekir. Lider geliştiricisi veya altyapı lideri, **DevTest Labs sahibi**olmalıdır. Yalnızca iki laboratuar sahibi olmasını öneririz. Bu ilke, bozulmayı önlemek için kod deposuna doğru genişletilir. Laboratuvar kullanıcılarının kaynakları kullanma hakları vardır ancak laboratuvar ilkelerini güncelleştiremez. Her bir yerleşik grubun bir laboratuvarda sahip olduğu rolleri ve hakları listeleyen aşağıdaki makaleye bakın: [Azure DevTest Labs ' de sahip ve kullanıcı ekleyin](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Nasıl yaparım? laboratuvara doğrudan bir bağlantı paylaşsın mı?

1. [Azure Portal](https://portal.azure.com)laboratuvara gidin.
2. Tarayıcınızdan **Laboratuvar URL 'sini** kopyalayın ve ardından Laboratuvar kullanıcılarınızla paylaşabilirsiniz.

> [!NOTE]
> Laboratuvar kullanıcısı Microsoft hesabı olan ve kuruluşunuzun Active Directory örneğinin üyesi olmayan bir dış Kullanıcı ise, paylaşılan bağlantıya erişmeye çalıştıklarında Kullanıcı bir hata mesajı görebilir. Bir dış Kullanıcı bir hata mesajı görürse, kullanıcıdan Azure portal önce sağ üst köşesinde adını seçmesini isteyin. Ardından, menünün dizin bölümünde, Kullanıcı laboratuvarın bulunduğu dizini seçebilir.

## <a name="virtual-machines"></a>Sanal makineler

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>DevTest Labs 'de görmem gereken sanal makineler sayfasında VM 'Leri neden göremiyorum?
DevTest Labs 'de bir VM oluşturduğunuzda, bu VM 'ye erişim izni verilir. VM 'yi her ikisi de Labs sayfasında ve **sanal makineler** sayfasında görüntüleyebilirsiniz. **DevTest Labs Owner** rolüne atanan kullanıcılar, laboratuvarda **tüm sanal makineler** sayfasında oluşturulan tüm VM 'leri görebilir. Ancak, **DevTest Labs Kullanıcı** rolüne sahip kullanıcılar, diğer KULLANıCıLARıN oluşturduğu VM kaynaklarına otomatik olarak okuma erişimi verilmez. Bu nedenle, **sanal makineler** sayfasında bu VM 'ler gösterilmez.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Aynı şablondan birden çok VM oluşturmak Nasıl yaparım?.
Aynı şablondan aynı anda birden çok VM oluşturmak için iki seçeneğiniz vardır:

- [Azure DevOps görevleri uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)kullanabilirsiniz.
- Bir VM oluştururken [Kaynak Yöneticisi şablonu](devtest-lab-add-vm.md#save-azure-resource-manager-template) oluşturabilir ve [Kaynak Yöneticisi şablonunu Windows PowerShell 'den dağıtabilirsiniz](../azure-resource-manager/resource-group-template-deploy.md).
- Ayrıca, sanal makine oluşturma sırasında oluşturulacak bir makinenin birden fazla örneğini belirtebilirsiniz. Birden çok sanal makine örneği oluşturma hakkında daha fazla bilgi edinmek için bkz. [Laboratuvar sanal makinesi oluşturma](devtest-lab-add-vm.md)hakkındaki belge.

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Mevcut Azure VM 'lerimi DevTest Labs laboratuvarma taşımak Nasıl yaparım? mı?
Mevcut sanal makinelerinizi DevTest Labs 'e kopyalamak için:

1.  Bir [Windows PowerShell betiği](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1)kullanarak var olan sanal makinenizin VHD dosyasını kopyalayın.
2.  [Özel görüntüyü](devtest-lab-create-template.md) DevTest Labs laboratuvarınızın içinde oluşturun.
3.  Özel görüntünüzün laboratuvarında bir VM oluşturun.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>VM 'larıma birden çok disk ekleyebilir miyim?

Evet, sanal makinelerinize birden çok disk ekleyebilirsiniz.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Sınamam için bir Windows işletim sistemi görüntüsü kullanmak istersem bir MSDN aboneliği satın almam gerekiyor mu?
Azure 'da geliştirme veya test etme için Windows istemci işletim sistemi görüntülerini (Windows 7 veya sonraki bir sürümü) kullanmak için aşağıdaki adımlardan birini uygulayın:

- [BIR MSDN aboneliği satın alın](https://www.visualstudio.com/products/how-to-buy-vs).
- Bir Kurumsal Anlaşma varsa, [Kurumsal Geliştirme ve test teklifiyle](https://azure.microsoft.com/offers/ms-azr-0148p)bir Azure aboneliği oluşturun.

Her bir MSDN teklifiyle ilgili Azure kredileri hakkında daha fazla bilgi için bkz. [Visual Studio aboneleri Için aylık Azure kredisi](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Laboratuvardaki tüm VM 'Leri silme işlemini otomatikleştirin Nasıl yaparım??
Laboratuvar sahibi olarak, Azure portal sanal makinelerinizden VM 'Leri silebilirsiniz. Ayrıca, bir PowerShell betiği kullanarak laboratuvarınızda bulunan tüm VM 'Leri silebilirsiniz. Aşağıdaki örnekte, açıklama **değiştirmek için değerler** altında parametre değerlerini değiştirin. SubscriptionID, labResourceGroup ve labName değerlerini Azure portal laboratuvar bölmesinden alabilirsiniz.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Ortamlar 

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>DevTest Labs Ortamumdaki Kaynak Yöneticisi şablonlarını nasıl kullanabilirim?
Kaynak Yöneticisi şablonlarınızı [DevTest Labs makalesindeki ortamlar özelliğinde](devtest-lab-test-env.md) bahsedilen adımları kullanarak bir DevTest Labs ortamına dağıtırsınız. Temel olarak, Kaynak Yöneticisi şablonlarınızı bir git deposuna (Azure Repos veya GitHub) kontrol edersiniz ve [şablonlarınıza yönelik özel bir depoyu](devtest-lab-test-env.md) laboratuvara eklersiniz. Bu senaryo, geliştirme makinelerini barındırmak için DevTest Labs kullanıyorsanız ancak üretim temsilcisi olan bir hazırlama ortamı oluşturuyorsanız faydalı olabilir.

Ayrıca, laboratuvar başına veya Kullanıcı başına sanal makine sayısının yalnızca laboratuvarda yerel olarak oluşturulan ve herhangi bir ortamda (Kaynak Yöneticisi şablonları) değil, yalnızca laboratuvar üzerinde oluşturulmuş makine sayısını sınırlayan bir değer de vardır.

## <a name="custom-images"></a>Özel Görüntüler

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Özel kurumsal görüntülerimi DevTest Labs ortamına getirmek için kolayca tekrarlanabilir bir işlem ayarlayabilirim?
Bu [videoya görüntü fabrikası düzenine](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4)bakın. Bu senaryo gelişmiş bir senaryodur ve sunulan betikler yalnızca örnek betiklerdir. Herhangi bir değişiklik gerekliyse, ortamınızda kullanılan betikleri yönetmeniz ve korumanız gerekir.

Görüntü fabrikası oluşturma hakkında ayrıntılı bilgi için, bkz. [Azure DevTest Labs özel görüntü fabrikası oluşturma](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Özel görüntü ve formül arasındaki fark nedir?
Özel görüntü, yönetilen bir görüntüdür. Formül, ek ayarlarla yapılandırabileceğiniz bir görüntüdür ve sonra kaydedebilir ve yeniden üretebilir. Aynı temel, sabit görüntüyü kullanarak hızlı bir şekilde birkaç ortam oluşturmak istiyorsanız özel bir görüntü tercih edilebilir. Sanal ağ veya alt ağın parçası olarak veya belirli boyuttaki bir VM olarak VM 'nizin yapılandırmasını en son bitlerle yeniden oluşturmak istiyorsanız formül daha iyi olabilir. Daha ayrıntılı bir açıklama için bkz. [DevTest Labs 'de özel resimleri ve formülleri karşılaştırma](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Ne zaman özel görüntüden bir formül kullanmalıyım?
Genellikle, Bu senaryodaki karar verme faktörü maliyetlidir ve yeniden kullanılır. Birçok kullanıcının/laboratuvarın temel görüntünün en üstünde çok fazla yazılım gerektiren bir senaryonuz varsa, özel bir görüntü oluşturarak maliyetleri azaltabilirsiniz. Bu, görüntünün bir kez oluşturulduğu anlamına gelir. Bu, sanal makinenin kurulum süresini ve kurulum gerçekleştiğinde çalışan sanal makine nedeniyle oluşan maliyeti azaltır.

Ancak, ek bir faktör yazılım paketinizdeki değişikliklerin sıklığıdır. Günlük derlemeler çalıştırır ve bu yazılımın kullanıcılarınızın sanal makinelerinde olması gerekiyorsa, özel bir görüntü yerine bir formül kullanmayı düşünün.

Daha ayrıntılı bir açıklama için bkz. DevTest Labs 'de [özel resimleri ve formülleri karşılaştırma](devtest-lab-comparing-vm-base-image-types.md) .

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Özel görüntüler oluşturmak için VHD dosyalarını karşıya yükleme işlemini Nasıl yaparım? otomatikleştirin.

Özel görüntüler oluşturmak için VHD dosyalarını karşıya yüklemeyi otomatik hale getirmek için iki seçeneğiniz vardır:

- SSD dosyalarını, laboratuvarda ilişkili depolama hesabına kopyalamak veya karşıya yüklemek için [AzCopy](../storage/common/storage-use-azcopy-v10.md) kullanın.
- [Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md)kullanın. Depolama Gezgini, Windows, OS X ve Linux 'ta çalışan tek başına bir uygulamadır.

Laboratuvarınız ile ilişkili hedef depolama hesabını bulmak için:

1.  [Azure Portal](https://portal.azure.com) oturum açın.
2.  Sol taraftaki menüden **kaynak grupları**' nı seçin.
3.  Laboratuvarınız ile ilişkili kaynak grubunu bulun ve seçin.
4.  **Genel bakış**' ın altında, depolama hesaplarından birini seçin.
5.  **Bloblar**'ı seçin.
6.  Listedeki karşıya yüklemeleri bulun. Hiçbiri yoksa, 4. adıma dönün ve başka bir depolama hesabı deneyin.
7.  AzCopy Komutunuz için hedef olarak **URL 'yi** kullanın.

Ne zaman bir Azure Market görüntüsü kullanmalıyım? kendi özel kurumsal görüntümi?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Ne zaman bir Azure Market görüntüsü kullanmalıyım? kendi özel kurumsal görüntümi?
Belirli endişeleriniz veya kuruluş gereksinimleriniz yoksa, Azure Marketi varsayılan olarak kullanılmalıdır. Bazı yaygın örnekler şunlardır;

- Temel görüntünün bir parçası olarak bir uygulamanın eklenmesini gerektiren karmaşık yazılım kurulumu.
- Bir uygulamanın yüklenmesi ve kurulumu, bir Azure Marketi görüntüsüne eklenmek üzere işlem zamanının etkili bir şekilde kullanılması için birkaç saat sürebilir.
- Geliştiriciler ve test ediciler, bir sanal makineye hızlı bir şekilde erişim gerektirir ve yeni bir sanal makinenin kurulum süresini en aza indirmek ister.
- Tüm makineler için yerinde olması gereken uyumluluk veya yasal koşullar (örneğin, güvenlik ilkeleri).
- Özel görüntülerin kullanılması, hafif olarak düşünülmemelidir. Bu temel görüntüler için VHD dosyalarını yönetmeniz gerektiği için, daha fazla karmaşıklık sağlar. Ayrıca, bu temel görüntülerin yazılım güncelleştirmeleriyle düzenli olarak yayama yapmanız gerekir. Bu güncelleştirmeler yeni işletim sistemi (OS) güncelleştirmelerini ve yazılım paketinin kendisi için gereken güncelleştirme veya yapılandırma değişikliklerini içerir.

## <a name="artifacts"></a>Yapıtlar

### <a name="what-are-artifacts"></a>Yapıtlar nelerdir?
Yapıtlar, en son bitleri dağıtmak veya geliştirme araçlarınızı bir VM 'ye dağıtmak için kullanabileceğiniz özelleştirilebilir öğelerdir. VM 'yi oluştururken sanal makinenize yapıt ekleyin. VM sağlandıktan sonra yapıtlar VM 'nizi dağıtıp yapılandırır. [Genel GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)depolarımızda, önceden var olan çeşitli yapıtlar mevcuttur. [Kendi yapıtlarınızı](devtest-lab-artifact-author.md)da yazabilirsiniz.

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>VM oluşturma sırasında yapım başarısız oldu. Nasıl yaparım? sorun gidermi?
Başarısız yapıtlarınız için günlük alma hakkında bilgi edinmek için bkz. [DevTest Labs 'de yapıt hatalarının nasıl tanılanacağı](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Kuruluşun, DevTest Labs 'de ortak yapıt deposu ve özel yapıt deposu kullanması gerekir mi?
[Ortak yapıt deposu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , en yaygın olarak kullanılan bir ilk yazılım paketi kümesi sağlar. Ortak geliştirici araçları ve eklentilerini yeniden oluşturmak için zaman yatırmaya gerek kalmadan hızlı dağıtıma yardımcı olur. Kendi özel deposunu dağıtmayı seçebilirsiniz. Genel ve özel bir depoyu paralel olarak kullanabilirsiniz. Ortak depoyu devre dışı bırakmayı da tercih edebilirsiniz. Özel bir depoyu dağıtmaya yönelik ölçütler aşağıdaki soru ve noktalara göre yapılmalıdır:

- Kuruluşun, DevTest Labs sunumunun parçası olarak kurumsal lisanslı yazılıma sahip olması için bir gereksinimi var mı? Yanıt Evet ise, özel bir deponun oluşturulması gerekir.
- Kuruluş, genel sağlama sürecinin bir parçası olarak gerekli olan belirli bir işlem sağlayan özel yazılım geliştirebilir mi? Yanıt Evet ise, özel bir deponun dağıtılması gerekir.
- Kuruluşun idare ilkesi yalıtım gerektiriyorsa ve dış depolar kuruluş tarafından doğrudan yapılandırma yönetimi altında değilse, özel bir yapıt deposu dağıtılmalıdır. Bu işlemin bir parçası olarak, ortak deponun ilk kopyası özel depo ile kopyalanabilir ve tümleştirilebilir. Daha sonra, ortak depo devre dışı bırakılabilir, böylece kuruluş içinde hiç kimse artık erişemez. Bu yaklaşım, kuruluştaki tüm kullanıcıların kuruluş tarafından onaylanan tek bir depoya sahip olmasını ve yapılandırma drlarını en aza indirmenize olanak sağlar.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Bir kuruluş tek bir depoyu planlıyor veya birden çok depoya izin veriyor mu?
Kuruluşunuzun genel idare ve yapılandırma yönetimi stratejisinin bir parçası olarak, merkezi bir depo kullanmanızı öneririz. Birden çok depo kullandığınızda, bu kişiler zaman içinde yönetilmeyen yazılımların siloları haline gelebilir. Merkezi bir depoyla, birden fazla ekip projeleri için bu depodan yapıları kullanabilir. Standartlaştırma, güvenlik ve yönetim kolaylığı sağlar ve çabalarınızın çoğaltılmasını ortadan kaldırır. Merkezileşmeyi bir parçası olarak, uzun süreli yönetim ve sürdürülebilirlik için önerilen uygulamalar aşağıdaki eylemlerdir:

- Azure Repos kimlik doğrulaması ve yetkilendirme için Azure aboneliğinin kullandığı Azure Active Directory kiracısıyla ilişkilendirin.
- Merkezi olarak yönetilen Azure Active Directory `All DevTest Labs Developers` adında bir grup oluşturun. Yapıt geliştirmeye katkıda bulunan tüm geliştiriciler bu gruba yerleştirilmelidir.
- Aynı Azure Active Directory grubu, Azure Repos deposuna ve laboratuvarına erişim sağlamak için kullanılabilir.
- Azure Repos, dallandırma veya dallama, birincil üretim deposundan ayrı bir geliştirme deposunda ayrı olarak kullanılmalıdır. İçerik yalnızca, doğru bir kod incelemesi sonrasında bir çekme isteğiyle birlikte Ana dala eklenir. Kod gözden geçiren değişikliği onayladığında, ana dalın bakımında sorumlu olan bir lider geliştiricisi, güncelleştirilmiş kodu birleştirir.

## <a name="cicd-integration"></a>CI/CD tümleştirmesi

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs CI/CD araç zincirimde tümleştirsin mi?
Azure DevOps kullanıyorsanız, DevTest Labs 'de yayın işlem hattınızı otomatikleştirmek için bir [DevTest Labs görev uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) kullanabilirsiniz. Bu uzantıyla gerçekleştirebileceğiniz görevlerden bazıları şunlardır:

- VM 'yi otomatik olarak oluşturun ve dağıtın. Ayrıca, Azure dosya kopyası veya PowerShell Azure DevOps Services görevlerini kullanarak VM 'yi en son derleme ile yapılandırabilirsiniz.
- Daha fazla araştırma için aynı VM 'de bir hatayı yeniden oluşturmak üzere test ettikten sonra sanal makinenin durumunu otomatik olarak yakala.
- Artık gerekli olmadığında, yayın işlem hattının sonundaki VM 'yi silin.

Aşağıdaki blog gönderileri Azure DevOps Services uzantısını kullanma hakkında rehberlik ve bilgiler sunar:

- [DevTest Labs ve Azure DevOps uzantısı](integrate-environments-devops-pipeline.md)
- [Azure DevOps Services mevcut bir DevTest Labs laboratuvarında yeni bir sanal makine dağıtın](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [DevTest Labs 'e yönelik sürekli dağıtımlar için Azure DevOps Services Release Management 'ı kullanma](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Diğer sürekli tümleştirme (CI)/sürekli teslim (CD) araç zincirleri için, [Azure PowerShell cmdlet 'lerini](../azure-resource-manager/resource-group-template-deploy.md) ve [.net sdk](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/)'larını kullanarak [Azure Resource Manager şablonlarını](https://azure.microsoft.com/resources/templates/) dağıtarak aynı senaryolara ulaşabilirsiniz. Ayrıca, toolzinciriniz ile tümleştirilecek [DevTest Labs Için REST API 'lerini](https://aka.ms/dtlrestapis) de kullanabilirsiniz.

## <a name="networking"></a>Ağ

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>DevTest Labs ortamım için yeni bir sanal ağ oluşturmalı ve var olan bir sanal ağı kullanıyor mıyım?
Sanal makinelerinizin mevcut altyapıyla etkileşime ihtiyacı varsa, DevTest Labs ortamınızda var olan bir sanal ağı kullanmayı göz önünde bulundurun. ExpressRoute kullanırsanız, aboneliklerde kullanılmak üzere atanan IP adresi alanınızı parçalara atabilmeniz için sanal ağlar/alt ağların miktarını en aza indirmek isteyebilirsiniz. 

Burada VNet eşleme[modelini (hub-bağlı bileşen modeli](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) de kullanmayı göz önünde bulundurun. Bu yaklaşım, abonelikler arasında VNET/subnet iletişimini mümkün bir şekilde sunar. Aksi halde, her DevTest Labs ortamının kendi sanal ağı olabilir. 

Abonelik başına sanal ağ sayısında [sınırlar](../azure-subscription-service-limits.md) vardır. Varsayılan miktar 50 ' dir, ancak bu sınır 100 olarak yükseltilebilir.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Paylaşılan IP ile genel IP ve özel IP 'yi ne zaman kullanmalıyım?
 
Siteden siteye VPN veya Express Route kullanıyorsanız, makinelerinizin iç ağınız aracılığıyla erişilebilir olması ve genel İnternet üzerinden erişilemez olması için özel IP 'Ler kullanmayı düşünün.

> [!NOTE]
> Laboratuvar sahipleri bu alt ağ ilkesini, yanlışlıkla VM 'Ler için genel IP adresleri oluşturmadığından emin olmak için değiştirebilir. Abonelik sahibinin ortak IP 'Lerin oluşturulmasını önleyecek bir abonelik ilkesi oluşturması gerekir.

Paylaşılan genel IP 'Leri kullanırken bir laboratuvardaki sanal makineler ortak bir IP adresi paylaşır. Bu yaklaşım, belirli bir abonelik için genel IP adresleri sınırlarına ulaşmaktan kaçınmak gerektiğinde yararlı olabilir.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Geliştirme ve test sanal makinelerinin herkese açık internet 'e ulaşamıyor Nasıl yaparım? emin misiniz? Ağ yapılandırmasını kurmak için önerilen desenler var mı?

Evet. Dikkate alınması gereken iki nokta vardır: gelen ve giden trafik.

- **Gelen trafik** : sanal makınenın genel IP adresi yoksa, İnternet tarafından erişilemez. Yaygın bir yaklaşım, bir kullanıcının genel IP adresi oluşturgerektirmediğinden, abonelik düzeyinde bir ilkenin ayarlanmış olmasını sağlamaktır.
- **Giden trafik** : sanal makinelerin genel İnternet 'e doğrudan erişmesini engellemek ve trafiği bir kurumsal güvenlik duvarı aracılığıyla zorlamak istiyorsanız, zorlamalı yönlendirmeyi kullanarak trafiği Express Route veya VPN aracılığıyla yönlendirebilirsiniz.

> [!NOTE]
> Proxy ayarları olmayan trafiği engelleyen bir ara sunucunuz varsa, laboratuvarın yapıt depolama hesabına özel durumlar eklemeyi unutmayın.

Sanal makineler veya alt ağlar için ağ güvenlik grupları da kullanabilirsiniz. Bu adım trafiğe izin vermek/engellemek için ek bir koruma katmanı ekler.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Var olan sanal ağ neden düzgün şekilde kaydediliyor?
Bunun bir nedeni, sanal ağ adınızın nokta içermeme olasılığı vardır. Bu durumda, dönemleri kaldırmayı veya kısa çizgilerden değiştirmeyi deneyin. Sonra, sanal ağı kaydetmeyi yeniden deneyin.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>PowerShell 'den bir VM sağlamadığımda neden "üst kaynak bulunamadı" hatası alıyorum?
Bir kaynak başka bir kaynağın üst öğesi olduğunda, alt kaynağı oluşturmadan önce üst kaynağın mevcut olması gerekir. Üst kaynak yoksa, bir **Parentresourcenotfound** iletisi görürsünüz. Üst kaynakta bir bağımlılık belirtmezseniz, alt kaynak üst öğeden önce dağıtılabilir.

VM 'Ler bir kaynak grubundaki laboratuvar altında alt kaynaklardır. PowerShell kullanarak VM 'Leri dağıtmak için Kaynak Yöneticisi Şablonlar kullandığınızda, PowerShell betiğine girilen kaynak grubu adı laboratuvarın kaynak grubu adı olmalıdır. Daha fazla bilgi için bkz. [Genel Azure dağıtım hatalarıyla Ilgili sorunları giderme](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Bir VM dağıtımı başarısız olursa nereden daha fazla hata bilgisi bulabilirim?
VM dağıtım hataları, etkinlik günlüklerinde yakalanır. Laboratuvar VM 'si etkinlik günlüklerini, laboratuvar VM 'si sayfasındaki kaynak menüsündeki **Denetim günlükleri** veya **sanal makine tanılama** altında bulabilirsiniz (sanal MAKINELERIM listesinden VM 'yi seçtikten sonra sayfa görünür).

Bazen, dağıtım hatası VM dağıtımı başlamadan önce oluşur. VM ile oluşturulan bir kaynak için abonelik sınırının aşıldığı bir örnektir. Bu durumda, hata ayrıntıları laboratuvar düzeyi etkinlik günlüklerinde yakalanır. Etkinlik günlükleri **yapılandırma ve ilke** ayarlarının altında bulunur. Azure 'da etkinlik günlüklerini kullanma hakkında daha fazla bilgi için bkz. [kaynaklardaki eylemleri denetlemek için etkinlik günlüklerini görüntüleme](../azure-resource-manager/resource-group-audit.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Laboratuvar oluşturmaya çalışırken neden "kaynak türü için konum kullanılamıyor" hatası alıyorum?
Laboratuvar oluşturmaya çalıştığınızda aşağıdakine benzer bir hata iletisi görebilirsiniz: 

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Aşağıdaki adımlardan birini gerçekleştirerek, bu hatayı çözebilirsiniz:

#### <a name="option-1"></a>Seçenek 1
[Bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/) sayfasında, Azure bölgelerindeki kaynak türünün kullanılabilirliğini kontrol edin. Kaynak türü belirli bir bölgede kullanılabilir değilse, DevTest Labs bu bölgede bir laboratuvar oluşturmayı desteklemez. Laboratuvarınızı oluştururken başka bir bölge seçin. 

#### <a name="option-2"></a>Seçenek 2
Bölgenizde kaynak türü kullanılabiliyorsa, aboneliğiniz ile kayıtlı olup olmadığını denetleyin. [Bu makalede](../azure-resource-manager/resource-manager-supported-services.md)gösterildiği gibi, abonelik sahibi düzeyinde yapılabilir. 


