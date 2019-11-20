---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2bd40db51d82bd2278bd716615636968adf8277b
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391817"
---
Standartlaştırılmış sanal makine (VM) görüntüleri, kuruluşların buluta geçiş yapmasına ve dağıtımlarda tutarlılık sağlamanıza olanak tanır. Görüntüler genellikle önceden tanımlanmış güvenlik ve yapılandırma ayarlarını ve gerekli yazılımları içerir. Kendi görüntüleme işlem hattınızı ayarlamak için zaman, altyapı ve kurulum gerekir, ancak Azure VM Image Builder sayesinde görüntünüzü açıklayan basit bir yapılandırma sağlamanız, hizmete göndermeniz ve görüntünün oluşturulup dağıtılması sağlanır.
 
Azure VM görüntü Oluşturucu (Azure görüntü Oluşturucu), bir Windows veya Linux tabanlı Azure Marketi görüntüsü, mevcut özel görüntüler veya Red Hat Enterprise Linux (RHEL) ISO ile başlamanıza ve kendi özelleştirmelerinizi eklemeye başlamanızı sağlar. Görüntü Oluşturucu [HashiCorp Packer](https://packer.io/)üzerinde oluşturulduğundan, mevcut Packer kabuğu sağlayıcısı oluştur betiklerinizi de içeri aktarabilirsiniz. Ayrıca, [Azure Paylaşılan görüntü galerisinde](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)bir yönetilen görüntü veya VHD olarak, görüntülerinizi nerede barındırıyorsanız istediğinizi belirtebilirsiniz.

> [!IMPORTANT]
> Azure görüntü Oluşturucu Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Önizleme özellikleri

Önizleme için, bu özellikler desteklenir:

- En düşük güvenlik ve kurumsal yapılandırmalarınızı içeren altın taban çizgisi görüntülerinin oluşturulması ve departmanların ihtiyaçları için daha fazla özelleştirme yapmasına izin vermek.
- Var olan görüntülerin düzeltme eki uygulama, görüntü Oluşturucu var olan özel görüntüleri sürekli olarak yamanız için izin verir.
- Azure Paylaşılan görüntü Galerisi ile tümleştirme, görüntüleri küresel olarak dağıtmanıza, sürümüne ve ölçeklendirmenize olanak tanır ve size bir görüntü yönetim sistemi sağlar.
- Mevcut görüntü oluşturma işlem hatlarıyla tümleştirme, işlem hattınızdan görüntü Oluşturucu 'Yu çağırın veya basit önizleme resmi Oluşturucu Azure DevOps görevini kullanın.
- Mevcut bir görüntü özelleştirme işlem hattını Azure 'a geçirin. Görüntüleri özelleştirmek için mevcut betikleri, komutlarınızı ve işlemlerinizi kullanın.
- Red Hat kullanın kendi abonelik desteğini getirin. Uygun, kullanılmayan Red Hat aboneliklerinizle kullanılmak üzere Red Hat kurumsal görüntüleri oluşturun.
- VHD biçimindeki görüntülerin oluşturulması.
 

## <a name="regions"></a>Bölgeler
Azure görüntü Oluşturucu hizmeti bu bölgelerde önizleme için kullanılabilir olacak. Görüntüler, bu bölgelerin dışına dağıtılabilir.
- Doğu ABD
- Doğu ABD 2
- Orta Batı ABD
- Batı ABD
- Batı ABD 2

## <a name="os-support"></a>İşletim sistemi desteği
AıB, Azure Marketi temel işletim sistemi görüntülerini destekleyecektir:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6
- CentOS 7,6
- Sanal Masaüstü için Windows 10 RS5 Enterprise/Professional/Enterprise (EVD) 
- Windows 2016
- Windows 2019

AıB, için bir kaynak olarak RHEL ISO 'yu destekleyecektir:
- RHEL 7,3
- RHEL 7,4
- RHEL 7.5

RHEL 7,6 ISOs desteklenmez, ancak test ediliyor.

## <a name="how-it-works"></a>Nasıl çalışır


![Azure Image Builder kavramsal çizimi](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure görüntü Oluşturucu, bir Azure Kaynak sağlayıcısı tarafından erişilebilen, tam olarak yönetilen bir Azure hizmetidir. Azure görüntü Oluşturucu işleminin üç ana bölümü vardır: kaynak, özelleştirme ve dağıtma, bunlar bir şablonda temsil edilir. Aşağıdaki diyagramda, bazı özellikleriyle birlikte bileşenler gösterilmektedir. 
 


**Image Builder işlemi** 

![Azure Image Builder işleminin kavramsal çizimi](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Görüntü şablonunu bir. JSON dosyası olarak oluşturun. Bu. JSON dosyası, görüntü kaynağı, özelleştirmeler ve dağıtım hakkındaki bilgileri içerir. [Azure Image Builder GitHub deposunda](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)birden çok örnek vardır.
1. Bu hizmeti hizmetine gönderirseniz, belirttiğiniz kaynak grubunda bir görüntü şablonu yapıtı oluşturulur. Arka planda, görüntü Oluşturucu kaynak görüntüyü veya ISO ve komut dosyalarını gerektiği şekilde indirir. Bunlar, aboneliğinizde otomatik olarak oluşturulan ayrı bir kaynak grubunda depolanır: IT_ @ no__t-0DestinationResourceGroup > _ @ no__t-1TemplateName >. 
1. Görüntü şablonu oluşturulduktan sonra görüntüyü oluşturabilirsiniz. Arka plan resmi tasarımcısında, IT_ @ no__t-0DestinationResourceGroup > _ @ no__t-1TemplateName > kaynak grubunda bir VM (D1v2), ağ, genel IP ve depolama oluşturmak için şablon ve kaynak dosyalarını kullanır.
1. Görüntü oluşturma işleminin bir parçası olarak, görüntü Oluşturucu görüntüyü şablona göre dağıtır ve ardından işlem için oluşturulan IT_ @ no__t-0DestinationResourceGroup > _ @ no__t-1TemplateName > kaynak grubundaki ek kaynakları siler.


## <a name="permissions"></a>İzinler

Azure VM Image Builder 'ın görüntüleri yönetilen görüntülere veya paylaşılan bir görüntü galerisine dağıtmasını sağlamak için, "Azure sanal makine görüntüsü Oluşturucusu" hizmeti (uygulama KIMLIĞI: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) için ' katılımcı ' izinleri sağlamanız gerekir. ). 

Mevcut bir özel yönetilen görüntü veya görüntü sürümü kullanıyorsanız, Azure görüntü oluşturucunun bu kaynak gruplarına en az ' okuyucu ' erişimi olması gerekir.

Azure CLı kullanarak erişim atayabilirsiniz:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Hizmet hesabı bulunmazsa, rol atamasını eklediğiniz aboneliğin kaynak sağlayıcısı için henüz kaydolmadığından bu durum oluşabilir.


## <a name="costs"></a>Maliyetler
Azure Image Builder ile görüntü oluştururken, derlerken ve depolarken bazı işlem, ağ ve depolama maliyetlerine tabi olursunuz. Bu maliyetler, el ile özel görüntüler oluşturma bölümünde tahakkuk eden maliyetlere benzer. Kaynaklar için Azure ücretlerinizi ücretlendirilecektir. 

Görüntü oluşturma işlemi sırasında, dosyalar `IT_<DestinationResourceGroup>_<TemplateName>` kaynak grubunda indirilir ve depolanır ve bu, küçük bir depolama maliyetlerine neden olur. Bunları tutmak istemiyorsanız, görüntü oluşturulduktan sonra **görüntü şablonunu** silin.
 
Image Builder, VM için gereken bir D1v2 VM boyutu, depolama alanı ve ağ kullanarak bir VM oluşturur. Bu kaynaklar, derleme işleminin süresi boyunca son verilecek ve görüntü Oluşturucu görüntüyü oluşturmayı tamamladığında silinir. 
 
Azure Image Builder, görüntüyü seçtiğiniz bölgelere dağıtır ve bu da ağ çıkış ücretleri doğuracaktır.
 
## <a name="next-steps"></a>Sonraki adımlar 
 
Azure görüntü Oluşturucu 'Yu denemek için [Linux](../articles/virtual-machines/linux/image-builder.md) veya [Windows](../articles/virtual-machines/windows/image-builder.md) görüntülerini oluşturma makalelerine bakın.
 
 
