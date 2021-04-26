---
title: Azure görüntü Oluşturucu (Önizleme) hakkında bilgi edinin
description: Azure 'da sanal makineler için Azure Image Builder hakkında daha fazla bilgi edinin.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 20bb6925f859d497046eb42bbafb5264826b77b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604075"
---
# <a name="preview-azure-image-builder-overview"></a>Önizleme: Azure Image Builder 'a genel bakış

Standartlaştırılmış sanal makine (VM) görüntüleri, kuruluşların buluta geçiş yapmasına ve dağıtımlarda tutarlılık sağlamanıza olanak tanır. Görüntüler genellikle önceden tanımlanmış güvenlik ve yapılandırma ayarlarını ve gerekli yazılımları içerir. Kendi görüntüleme işlem hattınızı ayarlamak için zaman, altyapı ve kurulum gerekir, ancak Azure VM Image Builder sayesinde görüntünüzü açıklayan bir yapılandırma sağlar, hizmete gönderebilir ve görüntü oluşturulup dağıtılır.
 
Azure VM görüntü Oluşturucu (Azure görüntü Oluşturucu), bir Windows veya Linux tabanlı Azure Marketi görüntüsü, mevcut özel görüntüler ile başlamanıza ve kendi özelleştirmelerinizi eklemeye başlamanızı sağlar. Görüntü Oluşturucu [HashiCorp Packer](https://packer.io/) üzerinde oluşturulduğundan, bazı benzerlikler görürsünüz ancak yönetilen bir hizmetin avantajına sahip olursunuz. Ayrıca, [Azure Paylaşılan görüntü galerisinde](shared-image-galleries.md)bir yönetilen görüntü veya VHD olarak, görüntülerinizi nerede barındırıyorsanız istediğinizi belirtebilirsiniz.

> [!IMPORTANT]
> Azure görüntü Oluşturucu Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Önizleme özellikleri

Önizleme için, bu özellikler desteklenir:

- En düşük güvenlik ve kurumsal yapılandırmalarınızı içeren temel görüntülerin oluşturulması ve bölümlerin daha fazla özelleştirmesini sağlar.
- Temel uygulamaların tümleştirilmesi, bu nedenle VM oluşturma sonrasında iş yüklerini alabilir veya Windows sanal masaüstü görüntülerini desteklemek için yapılandırma ekleyebilirsiniz.
- Var olan görüntülerin düzeltme eki uygulama, görüntü Oluşturucu var olan özel görüntüleri sürekli olarak yamanız için izin verir.
- Mevcut yapılandırma sunucularına (DSC, Chef, Pupevcil hayvan vb.), dosya paylaşımlarına veya diğer yönlendirilebilir sunuculara/hizmetlere bağlanabilmeniz için görüntü oluşturucuyu var olan sanal ağlarınıza bağlayın.
- Azure Paylaşılan görüntü Galerisi ile tümleştirme, görüntüleri küresel olarak dağıtmanıza, sürümüne ve ölçeklendirmenize olanak tanır ve size bir görüntü yönetim sistemi sağlar.
- Mevcut görüntü oluşturma işlem hatlarıyla tümleştirme, işlem hattınızdan görüntü Oluşturucu 'Yu çağırın veya basit önizleme resmi Oluşturucu Azure DevOps görevini kullanın.
- Mevcut bir görüntü özelleştirme işlem hattını Azure 'a geçirin. Görüntüleri özelleştirmek için mevcut betikleri, komutlarınızı ve işlemlerinizi kullanın.
- Azure Stack desteklemek için VHD biçimindeki görüntülerin oluşturulması.
 

## <a name="regions"></a>Bölgeler
Azure görüntü Oluşturucu hizmeti bu bölgelerde önizleme için kullanılabilir olacak. Görüntüler, bu bölgelerin dışına dağıtılabilir.
- Doğu ABD
- Doğu ABD 2
- Orta Batı ABD
- Batı ABD
- Batı ABD 2
- Kuzey Avrupa
- West Europe

## <a name="os-support"></a>İşletim sistemi desteği
AıB, Azure Marketi temel işletim sistemi görüntülerini destekleyecektir:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 kurumsal/kurumsal çoklu oturum/profesyonel
- Windows 2016
- Windows 2019

## <a name="how-it-works"></a>Nasıl çalışır?

Azure VM görüntü Oluşturucu, bir Azure Kaynak sağlayıcısı tarafından erişilebilen, tam olarak yönetilen bir Azure hizmetidir. Hizmet için kaynak görüntüyü, gerçekleştirilecek özelleştirmeyi ve yeni görüntünün dağıtılacağı yeri belirten bir yapılandırma sağlayın, aşağıdaki diyagramda yüksek düzey bir iş akışı gösterilmektedir:

![Kaynakları (Windows/Linux), özelleştirmeleri (kabuğu, PowerShell, Windows yeniden başlatma & güncelleştirme, dosya ekleme) ve Azure Paylaşılan görüntü Galerisi ile genel dağıtımı gösteren Azure Image Builder işleminin kavramsal çizimi](./media/image-builder-overview/image-builder-flow.png)

Şablon Yapılandırması PowerShell, az CLı, ARM şablonları kullanılarak geçirilebilir ve Azure VM Image Builder DevOps görevi kullanılarak, bir görüntü şablonu kaynağı oluşturacağız. Görüntü şablonu kaynağı oluşturulduğunda, aboneliğinizde oluşturduğunuz hazırlama kaynak grubunu şu biçimde görürsünüz: IT_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). Hazırlama kaynak grubu, ScriptURI özelliğinde, Shell, PowerShell özelleştirmesi dosyasında başvurulan dosya ve komut dosyalarını içerir.

Görüntü şablonu kaynağında çağırabileceğiniz derlemeyi çalıştırmak için `Run` , hizmet daha sonra yapı IÇIN VM, ağ, disk, ağ bağdaştırıcısı vb. gibi ek kaynaklar dağıtır. Var olan bir sanal ağ görüntüsü Oluşturucu kullanmadan bir görüntü oluşturursanız, hizmet ortak IP ve NSG 'yi de dağıtır. bu hizmet, SSH veya WinRM kullanarak yapı VM 'sine bağlanır. Mevcut bir sanal ağı seçerseniz, hizmet Azure özel bağlantısı kullanılarak dağıtılır ve genel IP adresi gerekli değildir, görüntü Oluşturucu ağı hakkında daha fazla ayrıntı için [ayrıntıları](./linux/image-builder-networking.md)gözden geçirin.

Oluşturma tamamlandığında tüm kaynaklar silinecek, hazırlama kaynak grubu ve depolama hesabı dışında, bunları kaldırmak için görüntü şablonu kaynağını silecektir veya derlemeyi yeniden çalıştırmak için oradan bırakabilirsiniz.

Bu belgede, [Azure görüntü Oluşturucu GitHub deposundaki](https://github.com/azure/azvmimagebuilder)yapılandırma şablonlarına ve çözümlerine başvuran birden çok örnek ve adım adım kılavuz vardır.

### <a name="move-support"></a>Desteği taşı
Görüntü şablonu kaynağı sabittir ve kaynakların ve hazırlama kaynak grubunun bağlantıları içerir, bu nedenle kaynak türü taşınmasını desteklemez. Görüntü şablonu kaynağını taşımak isterseniz, yapılandırma şablonunun bir kopyasına sahip olduğunuzdan emin olun (Bu durumda, mevcut yapılandırmayı kaynakta ayıklayın), yeni kaynak grubunda yeni bir adla yeni bir görüntü şablonu kaynağı oluşturun ve önceki resim şablonu kaynağını silin. 

## <a name="permissions"></a>İzinler
(AıB) için kaydolmanız durumunda bu, AıB hizmetine bir hazırlama kaynak grubu (IT_ *) oluşturma, yönetme ve silme izni verir ve bu, görüntü derlemesi için gerekli olan kaynak ekleme haklarına sahip olur. Bu işlem, başarılı bir kayıt sırasında aboneliğinizde kullanılabilir hale getirilen bir AıB hizmet sorumlusu adı (SPN) tarafından yapılır.

Azure VM Image Builder 'ın görüntüleri yönetilen görüntülere veya paylaşılan bir görüntü galerisine dağıtmasını sağlamak için, görüntüleri okuma ve yazma izinlerine sahip Azure Kullanıcı tarafından atanan bir kimlik oluşturmanız gerekir. Azure depolama 'ya erişiyorsanız, bu durumda özel ve ortak kapsayıcıları okumak için izinler gerekir.

İzinler [PowerShell](./linux/image-builder-permissions-powershell.md)ve [az CLI](./linux/image-builder-permissions-cli.md)için daha ayrıntılı olarak açıklanmıştır.

## <a name="costs"></a>Maliyetler
Azure Image Builder ile görüntü oluştururken, derlerken ve depolarken bazı işlem, ağ ve depolama maliyetlerine tabi olursunuz. Bu maliyetler, el ile özel görüntüler oluşturma bölümünde tahakkuk eden maliyetlere benzer. Kaynaklar için Azure ücretlerinizi ücretlendirilecektir. 

Görüntü oluşturma işlemi sırasında dosyalar indirilir ve kaynak grubunda depolanır ve bu, `IT_<DestinationResourceGroup>_<TemplateName>` küçük bir depolama maliyetlerine neden olur. Bunları tutmak istemiyorsanız, görüntü oluşturulduktan sonra **görüntü şablonunu** silin.
 
Image Builder, VM için gereken bir D1v2 VM boyutu, depolama alanı ve ağ kullanarak bir VM oluşturur. Bu kaynaklar, derleme işleminin süresi boyunca son verilecek ve görüntü Oluşturucu görüntüyü oluşturmayı tamamladığında silinir. 
 
Azure Image Builder, görüntüyü seçtiğiniz bölgelere dağıtır ve bu da ağ çıkış ücretleri doğuracaktır.

## <a name="hyper-v-generation"></a>Hyper-V oluşturma
Image Builder Şu anda yalnızca yerel olarak Hyper-V oluşturma (Gen1) 1 görüntülerini Azure Paylaşılan görüntü Galerisi 'ne (SıG) veya yönetilen görüntüye oluşturmayı destekler. 
 
## <a name="next-steps"></a>Sonraki adımlar 
 
Azure görüntü Oluşturucu 'Yu denemek için [Linux](./linux/image-builder.md) veya [Windows](./windows/image-builder.md) görüntülerini oluşturma makalelerine bakın.