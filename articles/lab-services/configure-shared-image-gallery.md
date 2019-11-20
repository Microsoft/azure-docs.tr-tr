---
title: Azure DevTest Labs paylaşılan görüntü galerisini yapılandırma | Microsoft Docs
description: Azure DevTest Labs paylaşılan görüntü galerisini yapılandırma hakkında bilgi edinin
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6e0c1419e5656f184d27dce8d185a86bea71d173
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389961"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Azure DevTest Labs’de paylaşılan resim galerisi yapılandırma
DevTest Labs artık [paylaşılan görüntü Galerisi](../virtual-machines/windows/shared-image-galleries.md) özelliğini desteklemektedir. Laboratuvar kullanıcılarının, laboratuvar kaynakları oluştururken paylaşılan bir konumdan görüntülere erişmesine imkan tanıyor. Ayrıca, özel olarak yönetilen sanal makine görüntülerinizle birlikte yapı ve organizasyon oluşturmanıza yardımcı oluyor. Paylaşılan görüntü Galerisi özelliği şunları destekler:

- Görüntülerin yönetilen bir şekilde global olarak yinelenmesi
- Daha kolay yönetim için görüntülerin sürümlerinin oluşturulması ve gruplanması
- Alanlar Arası Yedekli Depolama (ZRS) hesapları ile kullanılabilirlik alanlarını destekleyen bölgelerde görüntülerinizin yüksek oranda kullanılabilmesini sağlayın. ZRS, bölgesel arızalara karşı daha iyi koruma sağlar.
- Rol tabanlı erişim denetimini (RBAC) kullanarak abonelikler ve hatta kiracılar arasında paylaşım.

Daha fazla bilgi için bkz. [paylaşılan görüntü Galerisi belgeleri](../virtual-machines/windows/shared-image-galleries.md). 
 
Muhafaza etmek istediğiniz çok sayıda yönetilen görüntünüz varsa ve bunlara şirketiniz genelinde erişilebilmesini istiyorsanız paylaşılan görüntü galerisini, görüntülerinizi güncelleştirmeyi ve paylaşmayı kolaylaştıran bir depo olarak kullanabilirsiniz. Laboratuvar sahibi olarak var olan bir paylaşılan görüntü galerisini laboratuvarınıza ekleyebilirsiniz. Bu galeri eklendiğinde laboratuvar kullanıcıları yeni görüntülerden makine oluşturabilir. Bu özelliğin temel avantajlarından biri, artık DevTest Labs’in laboratuvarlar, abonelikler ve bölgeler genelinde görüntü paylaşma olanağından yararlanabilmesidir. 

> [!NOTE]
> Paylaşılan görüntü Galerisi hizmeti ile ilişkili maliyetler hakkında bilgi edinmek için bkz. [paylaşılan görüntü Galerisi Için faturalama](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Dikkat edilmesi gerekenler
- Tek seferde laboratuvara yalnızca bir paylaşılan görüntü Galerisi ekleyebilirsiniz. Başka bir galeri eklemek istiyorsanız, var olan birini ayırmanız ve bir tane eklemeniz gerekir. 
- DevTest Labs Şu anda laboratuvar aracılığıyla galeriye görüntü yüklemeyi desteklemiyor. 
- Paylaşılan görüntü Galerisi görüntüsünü kullanarak bir sanal makine oluştururken, DevTest Labs her zaman bu görüntünün yayımlanan en son sürümünü kullanır. Ancak görüntüde birden çok sürüm varsa, Kullanıcı sanal makine oluşturma sırasında Gelişmiş Ayarlar sekmesine giderek önceki bir sürümden makine oluşturmayı tercih edebilir.  
- DevTest Labs, paylaşılan görüntü galerisinin görüntüleri laboratuvarın bulunduğu bölgeye çoğaltmasına rağmen her zaman mümkün değildir. Kullanıcıların bu görüntülerden VM 'Ler oluştururken sorun yaşamamasını önlemek için görüntülerin zaten laboratuvarın bölgesine çoğaltıldığından emin olun. "

## <a name="use-azure-portal"></a>Azure portalı kullanma
1. [Azure Portal](https://portal.azure.com) oturum açın.
1. Sol gezinti menüsünde **tüm hizmetler** ' i seçin.
1. Listeden **DevTest Labs**'i seçin.
1. Laboratuvarlar listesinden **laboratuvarınızı**seçin.
1. Sol menüdeki **Ayarlar** bölümünde **yapılandırma ve ilkeler** ' i seçin.
1. Sol menüdeki **sanal makine tabanları** altında **paylaşılan görüntü galerileri** ' ni seçin.

    ![Paylaşılan görüntü galerileri menüsü](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. **Ekle** düğmesine tıklayıp açılan menüden galerinizi seçerek var olan bir paylaşılan görüntü galerisini laboratuvarınızda ekleyin.

    ![Ekle](./media/configure-shared-image-gallery/attach-options.png)
1. Ekli galeriye gidin ve sanal makine oluşturma için paylaşılan görüntüleri **etkinleştirmek veya devre dışı bırakmak** üzere galerinizi yapılandırın. Yapılandırmak için listeden bir görüntü Galerisi seçin. 

    Varsayılan olarak, **sanal makine tabanları için tüm görüntülerin kullanılmasına Izin ver** seçeneği **Evet**olarak ayarlanmıştır. Bu, ekli paylaşılan görüntü galerisinde mevcut olan tüm görüntülerin, yeni bir laboratuvar VM 'si oluştururken laboratuvar kullanıcısı tarafından kullanılabileceği anlamına gelir. Belirli görüntülere erişimin kısıtlanması gerekiyorsa, değişiklik, **tüm görüntülerin Hayır 'a sanal makine tabanı olarak kullanılmasına Izin verin** ve VMoluştururken izin vermek istediğiniz görüntüleri seçip **Kaydet** düğmesini seçin.

    ![Etkinleştir veya devre dışı bırak](./media/configure-shared-image-gallery/enable-disable.png)
1. Laboratuvar kullanıcıları daha sonra etkin görüntüleri kullanarak bir sanal makine oluşturabilir ve **taban sayfanızı seçin** sayfasında görüntüyü **ekleme** ve bulma ' yı tıklatın.

    ![Laboratuvar kullanıcıları](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Laboratuvarınızda paylaşılan bir görüntü Galerisi ekleyin
Laboratuvarınızda paylaşılan bir görüntü galerisi eklemek için bir Azure Resource Manager şablonu kullanıyorsanız, aşağıdaki örnekte gösterildiği gibi, bunu Kaynak Yöneticisi şablonunuzun kaynaklar bölümüne eklemeniz gerekir:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Kaynak Yöneticisi bir şablon örneği için, genel GitHub deponuzdaki şu Kaynak Yöneticisi şablonu örneklerine bakın: [Laboratuvar oluştururken paylaşılan görüntü Galerisi 'Ni yapılandırın](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>API kullanma

### <a name="shared-image-galleries---create-or-update"></a>Paylaşılan görüntü galerileri-oluşturma veya güncelleştirme

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Paylaşılan görüntü galerileri görüntüleri-liste 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Sonraki adımlar
Ekli paylaşılan görüntü galerisinden bir görüntü kullanarak VM oluşturma hakkında aşağıdaki makalelere bakın: [Galeriden paylaşılan bir görüntü kullanarak VM oluşturma](add-vm-use-shared-image.md)
