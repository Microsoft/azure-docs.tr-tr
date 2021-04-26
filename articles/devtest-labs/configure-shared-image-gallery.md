---
title: Azure DevTest Labs paylaşılan görüntü galerisini yapılandırma | Microsoft Docs
description: Azure DevTest Labs, kullanıcıların laboratuvar kaynaklarını oluştururken paylaşılan bir konumdan görüntülere erişmelerini sağlayan bir paylaşılan görüntü galerisini nasıl yapılandıracağınızı öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: febcff640efc29eb4916250366641635f9d8721e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788430"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Azure DevTest Labs’de paylaşılan resim galerisi yapılandırma
DevTest Labs artık [paylaşılan görüntü Galerisi](../virtual-machines/shared-image-galleries.md) özelliğini desteklemektedir. Laboratuvar kullanıcılarının laboratuvar kaynaklarını oluştururken paylaşılan bir konumdan görüntülere erişmelerini sağlar. Ayrıca, özel yönetilen VM görüntüleriniz etrafında yapı ve kuruluş oluşturmanıza de yardımcı olur. Paylaşılan görüntü Galerisi özelliği şunları destekler:

- Görüntülerin yönetilen genel çoğaltması
- Daha kolay yönetim için görüntülerin sürümü oluşturma ve gruplama
- Kullanılabilirlik alanlarını destekleyen bölgelerde, görüntülerinizi bölge yedekli depolama (ZRS) hesaplarıyla yüksek oranda kullanılabilir hale getirin. ZRS, ZGen hatalarıyla karşı daha iyi esnekliği sunar.
- Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak, abonelikler arasında ve hatta kiracılar arasında paylaşım.

Daha fazla bilgi için bkz. [paylaşılan görüntü Galerisi belgeleri](../virtual-machines/shared-image-galleries.md). 
 
Korumanız gereken çok sayıda yönetilen görüntünüz varsa ve bunları şirketiniz genelinde kullanılabilir hale getirmek istiyorsanız, paylaşılan bir görüntü galerisini bir depo olarak kullanarak görüntülerinizi güncelleştirmeyi ve paylaşmayı kolaylaştırır. Laboratuvar sahibi olarak, laboratuvarınızda var olan bir paylaşılan görüntü Galerisi ekleyebilirsiniz. Bu galeri eklendikten sonra, laboratuar kullanıcıları bu en son görüntülerden makineler oluşturabilir. Bu özelliğin önemli bir avantajı, DevTest Labs 'nin artık laboratularda, aboneliklerde ve bölgeler arasında görüntü paylaşımı avantajlarından yararlanabilleridir. 

> [!NOTE]
> Paylaşılan görüntü Galerisi hizmeti ile ilişkili maliyetler hakkında bilgi edinmek için bkz. [paylaşılan görüntü Galerisi Için faturalama](../virtual-machines/shared-image-galleries.md#billing).

## <a name="considerations"></a>Dikkat edilmesi gerekenler
- Tek seferde laboratuvara yalnızca bir paylaşılan görüntü Galerisi ekleyebilirsiniz. Başka bir galeri eklemek istiyorsanız, var olan birini ayırmanız ve bir tane eklemeniz gerekir. 
- DevTest Labs Şu anda laboratuvar aracılığıyla galeriye görüntü yüklemeyi desteklemiyor. 
- Paylaşılan görüntü Galerisi görüntüsünü kullanarak bir sanal makine oluştururken, DevTest Labs her zaman bu görüntünün yayımlanan en son sürümünü kullanır. Ancak görüntüde birden çok sürüm varsa, Kullanıcı sanal makine oluşturma sırasında Gelişmiş Ayarlar sekmesine giderek önceki bir sürümden makine oluşturmayı tercih edebilir.  
- DevTest Labs, paylaşılan görüntü galerisinin görüntüleri laboratuvarın bulunduğu bölgeye çoğaltmasına rağmen her zaman mümkün değildir. Kullanıcıların bu görüntülerden VM 'Ler oluştururken sorun yaşamamasını önlemek için görüntülerin zaten laboratuvarın bölgesine çoğaltıldığından emin olun. "

## <a name="use-azure-portal"></a>Azure portalı kullanma
1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol gezinti menüsünde **tüm hizmetler** ' i seçin.
1. Listeden **DevTest Labs**'i seçin.
1. Laboratuvarlar listesinden **laboratuvarınızı** seçin.
1. Sol menüdeki **Ayarlar** bölümünde **yapılandırma ve ilkeler** ' i seçin.
1. Sol menüdeki **sanal makine tabanları** altında **paylaşılan görüntü galerileri** ' ni seçin.

    ![Paylaşılan görüntü galerileri menüsü](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. **Ekle** düğmesine tıklayıp açılan menüden galerinizi seçerek var olan bir paylaşılan görüntü galerisini laboratuvarınızda ekleyin.

    ![İliştir](./media/configure-shared-image-gallery/attach-options.png)
1. Görüntü Galerisi eklendikten sonra, ekli galeriye gitmek için seçin. Sanal makine oluşturma için paylaşılan görüntüleri **etkinleştirmek veya devre dışı bırakmak** üzere galerinizi yapılandırın. Yapılandırmak için listeden bir görüntü Galerisi seçin. 

    Varsayılan olarak, **sanal makine tabanları için tüm görüntülerin kullanılmasına Izin ver** seçeneği **Evet** olarak ayarlanmıştır. Bu, ekli paylaşılan görüntü galerisinde mevcut olan tüm görüntülerin, yeni bir laboratuvar VM 'si oluştururken laboratuvar kullanıcısı tarafından kullanılabileceği anlamına gelir. Belirli görüntülere erişimin kısıtlanması gerekiyorsa, değişiklik, **tüm görüntülerin Hayır 'a sanal makine tabanı olarak kullanılmasına Izin verin** ve VM oluştururken izin vermek istediğiniz görüntüleri seçip **Kaydet** düğmesini seçin.

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="Görüntüleri etkinleştirme veya devre dışı bırakma":::

    > [!NOTE]
    > Paylaşılan görüntü galerisinde hem Genelleştirilmiş hem de özelleştirilmiş görüntüler desteklenir. 
1. Laboratuvar kullanıcıları daha sonra etkin görüntüleri kullanarak bir sanal makine oluşturabilir ve **taban sayfanızı seçin** sayfasında görüntüyü **ekleme** ve bulma ' yı tıklatın.

    ![Laboratuvar kullanıcıları](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullan

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

Kaynak Yöneticisi bir şablon örneği için, genel GitHub deponuzdaki şu Kaynak Yöneticisi Şablon örneklerine bakın: [Laboratuvar oluştururken paylaşılan bir görüntü Galerisi yapılandırma](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-rest-api"></a>REST API’yi kullanma

### <a name="get-a-list-of-labs"></a>Laboratuvarların listesini alın 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>Laboratuvara ilişkili paylaşılan görüntü galerilerinden oluşan listeyi alın

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>Paylaşılan görüntü Galerisi oluştur veya güncelleştir

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

### <a name="list-images-in-a-shared-image-gallery"></a>Paylaşılan görüntü galerisindeki görüntüleri listeleme

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>Sonraki adımlar
Ekli paylaşılan görüntü galerisinden bir görüntü kullanarak VM oluşturma hakkında aşağıdaki makalelere bakın: [Galeriden paylaşılan bir görüntü kullanarak VM oluşturma](add-vm-use-shared-image.md)