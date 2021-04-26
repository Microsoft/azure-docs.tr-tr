---
title: DevTest Labs kavramları | Microsoft Docs
description: DevTest Labs 'in temel kavramlarını ve Azure sanal makinelerini oluşturmayı, yönetmeyi ve izlemeyi nasıl kolaylaştırabileceğinizi öğrenin
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b184f835ee99d47ddb843e5b02136299c26bfcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327867"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs kavramları
## <a name="overview"></a>Genel Bakış
Aşağıdaki liste, anahtar DevTest Labs kavramlarını ve tanımlarını içerir:

## <a name="labs"></a>Labs
Laboratuvar, sınırları ve kotaları belirterek bu kaynakları daha iyi yönetmenize olanak tanıyan sanal makineler (VM 'Ler) gibi bir kaynak grubunu kapsayan altyapısıdır.

## <a name="virtual-machine"></a>Sanal makine
Azure VM, Azure 'un sunduğu [isteğe bağlı, ölçeklenebilir bilgi işlem kaynakları](/azure/architecture/guide/technology-choices/compute-decision-tree) türlerinden biridir. Azure VM 'Leri, kendisini çalıştıran fiziksel donanımı satın alıp sürdürmenize gerek kalmadan sanallaştırma esnekliği sağlar, ancak yine de üzerinde çalışan yazılımı yapılandırma, düzeltme eki uygulama ve yükleme gibi belirli görevleri gerçekleştirerek VM 'yi korumanız gerekir.

[Azure 'Daki Windows sanal makinelerine genel bakış](../virtual-machines/windows/overview.md) , bir VM oluşturmadan önce göz önünde bulundurmanız gerekenler, nasıl oluşturacağınız ve nasıl yöneteceğiniz hakkında bilgi verir.

## <a name="claimable-vm"></a>Çakışan VM
Azure çakışan bir VM, izinleri olan herhangi bir laboratuvar kullanıcısı tarafından kullanılabilecek bir sanal makinedir. Laboratuvar Yöneticisi, belirli temel görüntüler ve yapıtlar ile VM 'Ler hazırlayıp paylaşılan bir havuza kaydedebilir. Daha sonra bir laboratuvar kullanıcısı, belirli bir yapılandırmaya sahip olmaları gerektiğinde havuzdan çalışan bir VM 'yi talep edebilir.

İlk olarak belirli bir kullanıcıya atanmayan bir VM, ancak "çakışan sanal makineler" altındaki her kullanıcının listesinde görünür. Bir VM bir kullanıcı tarafından talep edildikten sonra, "sanal makinelerim" alanına taşınır ve başka hiçbir Kullanıcı tarafından artık çakışmaz.

## <a name="environment"></a>Ortam
DevTest Labs 'de bir ortam, bir laboratuvardaki Azure kaynakları koleksiyonuna başvurur. [Bu blog gönderisi](./devtest-lab-faq.md#blog-post) , Azure Resource Manager şablonlarınızdaki çoklu VM ortamları oluşturmayı açıklar.

## <a name="base-images"></a>Temel görüntüler
Temel görüntüler, tüm araçları ve ayarları önceden yüklenmiş ve hızlı bir şekilde VM oluşturmak için yapılandırılmış olan VM görüntüleridir. Mevcut bir temeli seçerek ve Test Aracınızı yüklemek için yapıt ekleyerek bir VM sağlayabilirsiniz. Daha sonra, sağlanan VM 'yi bir temel olarak kaydedebilirsiniz, böylece taban, her VM sağlama için test aracısını yeniden yüklemek zorunda kalmadan kullanılabilir.

## <a name="artifacts"></a>Artifacts
Yapıtlar, bir VM sağlandıktan sonra uygulamanızı dağıtmak ve yapılandırmak için kullanılır. Yapıtlar şu olabilir:

* SANAL makineye yüklemek istediğiniz aracılar, Fiddler ve Visual Studio gibi araçlar.
* VM 'de çalıştırmak istediğiniz eylemler (örneğin, bir depoyu kopyalama).
* Test etmek istediğiniz uygulamalar.

Yapıtlar, dağıtım gerçekleştirme ve yapılandırma uygulama yönergelerini içeren JSON dosyalarıdır [Azure Resource Manager](../azure-resource-manager/management/overview.md) .

## <a name="artifact-repositories"></a>Yapıt depoları
Yapıt depoları, yapıtların iade edildiği git depolarıdır. Yapıt depoları, kuruluşunuzdaki birden çok laboratuvarda eklenebilir ve yeniden kullanılabilir ve paylaşımı etkinleştirir.

## <a name="formulas"></a>Formüller
Formüller, temel görüntülere ek olarak hızlı VM sağlaması için bir mekanizma sağlar. DevTest Labs içindeki bir formül, bir laboratuar sanal makinesi oluşturmak için kullanılan varsayılan özellik değerlerinin bir listesidir.
Formüller sayesinde, temel görüntü, VM boyutu, sanal ağ ve yapıtlar gibi aynı özellik kümesine sahip VM 'Ler her seferinde bu özellikleri belirtmeye gerek kalmadan oluşturulabilir. Bir formülden bir VM oluştururken, varsayılan değerler olduğu gibi kullanılabilir veya değiştirilmiş olabilir.

## <a name="policies"></a>İlkeler
İlkeler, laboratuvarınızda maliyeti denetlemeye yardımcı olur. Örneğin, VM 'Leri tanımlı bir zamanlamaya göre otomatik olarak kapatmak için bir ilke oluşturabilirsiniz.

## <a name="caps"></a>Şapkalar
CAPS, laboratuvarınızda çöp durumunu en aza indirecek bir mekanizmadır. Örneğin, Kullanıcı başına veya bir laboratuvarda oluşturulabilecek VM sayısını kısıtlamak için bir sınır belirleyebilirsiniz.

## <a name="security-levels"></a>Güvenlik düzeyleri
Güvenlik erişimi, Azure rol tabanlı erişim denetimi (Azure RBAC) tarafından belirlenir. Erişimin nasıl çalıştığını anlamak için, bir izin, rol ve Azure RBAC tarafından tanımlanan bir kapsam arasındaki farkları anlamanıza yardımcı olur.

* İzin-bir izin, belirli bir eyleme (örneğin, tüm sanal makinelere yönelik okuma erişimi) tanımlı bir erişimdir.
* Rol-rol, gruplandırılabilen ve bir kullanıcıya atanabilecek bir izinler kümesidir. Örneğin, *abonelik sahibi* rolü bir abonelik içindeki tüm kaynaklara erişebilir.
* Kapsam-bir kapsam, bir kaynak grubu, tek bir laboratuar veya tüm abonelik gibi bir Azure kaynağı hiyerarşisinde yer alan bir düzeydir.

DevTest Labs kapsamındaki Kullanıcı izinlerini tanımlamak için iki tür rol vardır: Laboratuvar sahibi ve laboratuvar kullanıcısı.

* Laboratuvar sahibi-laboratuvar sahibi, laboratuvardaki kaynaklara erişebilir. Bu nedenle, bir laboratuvar sahibi ilkeleri değiştirebilir, tüm VM 'Leri okuyup yazabilir, sanal ağı değiştirebilir ve benzeri devam edebilir.
* Laboratuvar kullanıcısı-laboratuvar kullanıcısı VM 'Ler, ilkeler ve sanal ağlar gibi tüm laboratuvar kaynaklarını görüntüleyebilir, ancak ilkeleri veya diğer kullanıcılar tarafından oluşturulan VM 'Leri değiştiremez.

DevTest Labs 'de özel roller oluşturmayı öğrenmek için, [belirli laboratuvar ilkelerine Kullanıcı Izinleri verme](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)makalesine bakın.

Kapsamlar hiyerarşik olduğundan, bir kullanıcının belirli bir kapsamda izinleri olduğunda, bu izinler otomatik olarak bu izinlere dahil edilen her alt düzey kapsamda atanır. Örneğin, bir kullanıcı abonelik sahibi rolüne atanırsa, tüm sanal makineleri, tüm sanal ağları ve tüm laboratuvarları içeren bir abonelikteki tüm kaynaklara erişebilirler. Bu nedenle, bir abonelik sahibi laboratuvar sahibinin rolünü otomatik olarak devralır. Ancak, tersi doğru değildir. Laboratuvar sahibinin, abonelik düzeyinden daha düşük bir kapsam olan bir laboratuvara erişimi vardır. Bu nedenle, bir laboratuar sahibi sanal makineleri veya sanal ağları ya da laboratuvarın dışındaki kaynakları göremez.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları
Bu makalede ele alınan kavramların tümü, Azure çözümünüzün altyapısını/yapılandırmasını tanımlamanızı ve tutarlı bir durumda tekrar tekrar dağıtılmasını sağlayan Azure Resource Manager şablonları kullanılarak yapılandırılabilir.

[Azure Resource Manager şablonlarının yapısını ve sözdizimini anlamak](../azure-resource-manager/templates/template-syntax.md#template-format) bir Azure Resource Manager şablonunun yapısını ve bir şablonun farklı bölümlerinde kullanılabilen özellikleri açıklar.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Sonraki adımlar
[DevTest Labs 'de Laboratuvar oluşturma](devtest-lab-create-lab.md)
