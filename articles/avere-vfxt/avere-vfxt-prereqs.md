---
title: Avere vFXT önkoşulları-Azure
description: Azure için avere vFXT önkoşulları
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: dce359d1567ee763cd988e778b1e0e44475388cc
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255358"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Avere vFXT oluşturmayı hazırlama

Bu makalede, avere vFXT kümesi oluşturmak için önkoşul görevleri açıklanmaktadır.

## <a name="create-a-new-subscription"></a>Yeni abonelik oluştur

Yeni bir Azure aboneliği oluşturarak başlayın. Tüm proje kaynaklarını ve giderlerini kolayca izlemenize, diğer projeleri sağlama sırasında olası kaynak azaltmasından korumaya ve temizlemeyi basitleştirmeye olanak sağlamak için her bir avere vFXT projesi için ayrı bir abonelik kullanın.  

Azure portal yeni bir Azure aboneliği oluşturmak için:

* [Abonelikler dikey penceresine](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) gidin
* Üstteki **+ Ekle** düğmesine tıklayın
* İstenirse oturum aç
* Bir teklif seçin ve yeni bir abonelik oluşturma adımlarında ilerleyin

## <a name="configure-subscription-owner-permissions"></a>Abonelik sahibi izinlerini yapılandırma

Abonelik için sahip izinlerine sahip bir kullanıcının vFXT kümesini oluşturması gerekir. Yazılım hizmet koşullarını kabul etmek ve diğer işlemleri gerçekleştirmek için abonelik sahibi izinleri gereklidir. 

Sahip olmayan bir Azure kümesi için avere vFTX oluşturmasına izin veren bazı geçici çözüm senaryoları vardır. Bu senaryolar, kaynakların kısıtlanması ve oluşturucuya ek roller atanması ile ilgilidir. Her iki durumda da bir abonelik sahibi, [avere vFXT yazılım koşullarını](#accept-software-terms) zaman önüne kabul etmelidir. 

| Senaryo | Kısıtlamalar | Avere vFXT kümesini oluşturmak için gereken erişim rolleri | 
|----------|--------|-------|
| Kaynak Grubu Yöneticisi | Sanal ağ, küme denetleyicisi ve küme düğümlerinin kaynak grubu içinde oluşturulması gerekir | Hedef kaynak grubu kapsamındaki [Kullanıcı erişimi Yöneticisi](../role-based-access-control/built-in-roles.md#user-access-administrator) ve [katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor) rolleri | 
| Dış sanal ağ | Küme denetleyicisi ve küme düğümleri kaynak grubu içinde oluşturulur, ancak farklı bir kaynak grubundaki mevcut bir sanal ağ kullanılır | (1) vFXT kaynak grubu kapsamındaki [Kullanıcı erişimi Yöneticisi](../role-based-access-control/built-in-roles.md#user-access-administrator) ve [katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor) rolleri; ve (2) [sanal makine katılımcısı](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [Kullanıcı erişimi Yöneticisi](../role-based-access-control/built-in-roles.md#user-access-administrator)ve [avere katkıda](../role-based-access-control/built-in-roles.md#avere-contributor) bulunan rolleri VNET kaynak grubunun kapsamı. |
 
Diğer bir seçenek de, [Bu makalede](avere-vfxt-non-owner.md)açıklandığı gibi zaman içinde özel bir rol tabanlı erişim denetımı (RBAC) rolü oluşturmak ve kullanıcıya ayrıcalık atamanız. Bu yöntem, bu kullanıcılara önemli izinler verir. 

## <a name="quota-for-the-vfxt-cluster"></a>VFXT kümesi için kota

Aşağıdaki Azure bileşenleri için yeterli kotanın olması gerekir. Gerekirse, [bir kota artışı isteyin](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Burada listelenen sanal makineler ve SSD bileşenleri, vFXT kümesinin kendisi içindir. İşlem grubunuz için kullanmayı düşündüğünüz VM 'Ler ve SSD için ek kota gerekir.  Kotayı, iş akışını çalıştırmayı planladığınız bölge için etkinleştirildiğinden emin olun.

|Azure bileşeni|Kotasının|
|----------|-----------|
|Sanal makineler|3 veya daha fazla E32s_v3|
|Premium SSD depolama|200 GB işletim sistemi alanı ve düğüm başına 1 TB-4 TB önbellek alanı |
|Depolama hesabı (isteğe bağlı) |v2|
|Veri arka ucu depolaması (isteğe bağlı) |Bir yeni LRS blob kapsayıcısı |

## <a name="accept-software-terms"></a>Yazılım koşullarını kabul et

> [!NOTE] 
> Bir abonelik sahibi avere vFXT kümesini oluşturursa Bu adım gerekli değildir.

Küme oluşturma sırasında, avere vFXT yazılımının hizmet koşullarını kabul etmelisiniz. Abonelik sahibi değilseniz, abonelik sahibi bir süre önce kabul edin. Bu adımın her abonelik için yalnızca bir kez yapılması gerekir.

Yazılım koşullarını önceden kabul etmek için: 

1. Azure portal bir bulut kabuğu açın veya <https://shell.azure.com> ' a göz atarak. Abonelik KIMLIĞINIZLE oturum açın.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Hizmet koşullarını kabul etmek ve Azure yazılım görüntüsü için avere vFXT için programlı erişimi etkinleştirmek üzere bu komutu verin: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Sanal ağınızda (gerekirse) bir depolama hizmeti uç noktası oluşturun

[Hizmet uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md) , Azure Blob trafiğini sanal ağ dışında yönlendirme yerine yerel olarak tutar. Arka uç veri depolama için Azure Blob kullanan Azure kümesi için avere vFXT 'nin kullanılması önerilir. 

Küme oluşturmanın bir parçası olarak mevcut bir VNET sağlıyorsanız ve arka uç depolama alanı için yeni bir Azure Blob kapsayıcısı oluşturuyorsanız, Microsoft Storage için VNET 'te bir hizmet uç noktası olması gerekir. Kümeyi oluşturmadan önce bu uç noktanın mevcut olması gerekir, ya da oluşturma başarısız olur. 

Depolama hizmeti uç noktası, depolamayı daha sonra eklemeseniz bile Azure Blob depolama kullanan Azure kümesi için avere vFXT için önerilir. 

> [!TIP] 
> * Küme oluşturmanın bir parçası olarak yeni bir sanal ağ oluşturuyorsanız bu adımı atlayın. 
> * Küme oluşturma sırasında blob depolaması oluşturmadıysanız bu adım isteğe bağlıdır. Bu durumda, Azure Blob kullanmaya karar verirseniz hizmet uç noktasını daha sonra oluşturabilirsiniz.

Azure portal depolama hizmeti uç noktası oluşturun. 

1. Portalda, sol taraftaki **sanal ağlar** ' a tıklayın.
1. Kümeniz için VNET 'i seçin. 
1. Sol taraftaki **hizmet uç noktaları** ' na tıklayın.
1. Üstteki **Ekle** ' ye tıklayın.
1. Hizmeti ``Microsoft.Storage`` olarak bırakın ve kümenin alt ağını seçin.
1. Alt kısımdaki **Ekle**' ye tıklayın.

   ![Hizmet uç noktası oluşturma adımları için ek açıklamaların bulunduğu ekran görüntüsünü Azure portal](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Sonraki adım: vFXT kümesini oluşturma

Bu önkoşulları tamamladıktan sonra kümenin kendisini oluşturmaya geçebilirsiniz. Yönergeler için [vFXT kümesini dağıtma](avere-vfxt-deploy.md) makalesini okuyun.