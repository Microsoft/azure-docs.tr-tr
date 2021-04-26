---
title: Portalı kullanarak Azure Paylaşılan görüntü galerisi oluşturma
description: Azure portal kullanarak sanal makine görüntülerini oluşturma ve paylaşma hakkında bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: e28a1412fa417b9f15ab7a6f79305d07ca6d9fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556103"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Portalı kullanarak Azure Paylaşılan görüntü galerisi oluşturma

[Paylaşılan görüntü Galerisi](../shared-image-galleries.md) , kuruluşunuz genelinde özel görüntü paylaşımını basitleştirir. Özel görüntüler market görüntüleri gibidir, ancak bunları kendiniz oluşturursunuz. Özel görüntüler, uygulamalar, uygulama yapılandırması ve diğer işletim sistemi yapılandırmalarının dağıtım görevlerinin önyüklemesi için kullanılabilir. 

Paylaşılan görüntü Galerisi, özel VM görüntülerinizi kuruluşunuzdaki diğer kişilerle, bir AAD kiracısı içinde veya bölgeler arasında paylaşmanızı sağlar. Hangi görüntüleri paylaşmak istediğinizi, içinde hangi bölgelerin kullanılabilir olmasını istediğinizi ve bunları ile paylaşmak istediğinizi seçin. Paylaşılan görüntüleri mantıksal olarak gruplandırabilmeniz için birden çok Galeri oluşturabilirsiniz. 

Galeri, tam Azure rol tabanlı erişim denetimi (Azure RBAC) sağlayan en üst düzey bir kaynaktır. Görüntülerin sürümü oluşturulabilir ve her görüntü sürümünü farklı bir Azure bölgesi kümesine çoğaltmayı tercih edebilirsiniz. Galeri yalnızca yönetilen görüntülerle birlikte kullanılabilir.

Paylaşılan görüntü Galerisi özelliğinin birden çok kaynak türü vardır. Bunları bu makalede kullanacağız veya oluşturacağız:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


Bu makalede çalışırken, kaynak grubu ve VM adlarını gerektiği yerde değiştirin.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>VM oluşturma

Artık bir veya daha fazla yeni VM oluşturabilirsiniz. Bu örnek, *Doğu ABD* veri merkezinde *Myresourcegroup* içinde *myvm* adlı bir VM oluşturur.

1. Görüntü tanımınıza gidin. Kullanılabilir tüm görüntü tanımlarını göstermek için kaynak filtresini kullanabilirsiniz.
1. Görüntü tanımınızın sayfasında, sayfanın üst kısmındaki menüden **VM oluştur** ' u seçin.
1. **Kaynak grubu** Için **Yeni oluştur** ' u seçin ve ad için *myresourcegroup* yazın.
1. **Sanal makine adı** alanına *myvm* yazın.
1. **Bölge** için *Doğu ABD*' yi seçin.
1. **Kullanılabilirlik seçenekleri** için, varsayılan *altyapı yedekliliği gerekmez*' ı gerekli olarak bırakın.
1. Görüntü  `latest` tanımı için sayfadan başladıysanız görüntünün değeri otomatik olarak görüntü sürümü ile doldurulur.
1. **Boyut** için kullanılabilir boyutlar LISTESINDEN bir VM boyutu seçin ve ardından **Seç**' i seçin.
1. **Yönetici hesabı** altında, görüntü Genelleştirilmiş ise, *azureuser* ve parola gibi bir Kullanıcı adı sağlamanız gerekir. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](faq.md#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır. Görüntünüz özelleştirilise, kaynak VM 'nin Kullanıcı adı ve parolası kullanıldığı için Kullanıcı adı ve parola alanları gri kalır.
1. VM 'ye uzaktan erişime izin vermek istiyorsanız, **ortak gelen bağlantı noktaları** altında **Seçili bağlantı noktalarına izin ver** ' i seçin ve ardından açılır listeden **RDP (3389)** seçeneğini belirleyin. VM 'ye uzaktan erişime izin vermek istemiyorsanız, **Genel gelen bağlantı noktaları** için **hiçbirini** seçilmemiş bırakın.
1. İşiniz bittiğinde sayfanın altındaki **gözden geçir + oluştur** düğmesini seçin.
1. VM doğrulamayı geçtikten sonra, dağıtımı başlatmak için sayfanın alt kısmındaki **Oluştur** ' u seçin.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilişkili kaynakları silebilirsiniz. Bunu yapmak için, sanal makinenin kaynak grubunu ve **Sil**’i seçin, ardından silinecek kaynak grubunun adını onaylayın.

Tek tek kaynakları silmek istiyorsanız, bunları ters sırada silmeniz gerekir. Örneğin, bir görüntü tanımını silmek için, bu görüntüden oluşturulan tüm görüntü sürümlerini silmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, şablonlar kullanarak paylaşılan görüntü Galerisi kaynağı da oluşturabilirsiniz. Çeşitli Azure hızlı başlangıç şablonları mevcuttur: 

- [Paylaşılan Görüntü Galerisi Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Tanımı Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Paylaşılan Görüntü Galerisinde Görüntü Sürümü Oluşturma](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Görüntü Sürümünden Sanal Makine Oluşturma](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Paylaşılan görüntü galerileri hakkında daha fazla bilgi için bkz. [genel bakış](../shared-image-galleries.md). Sorunlarla karşılaşırsanız bkz. [paylaşılan görüntü galerilerine sorun giderme](../troubleshooting-shared-images.md).