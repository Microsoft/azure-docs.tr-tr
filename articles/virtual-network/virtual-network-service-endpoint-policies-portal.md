---
title: Hizmet uç noktası ilkeleri oluşturma ve ilişkilendirme-Azure portal
titlesuffix: Azure Virtual Network
description: Bu makalede, Azure portal kullanarak hizmet uç noktası ilkelerini ayarlamayı ve kullanmayı öğrenin.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004968"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Azure portal kullanarak hizmet uç noktası ilkesi oluşturma, değiştirme veya silme

Hizmet uç noktası ilkeleri, sanal ağ trafiğini hizmet uç noktaları üzerinden belirli Azure kaynaklarına filtrelemenizi sağlar. Hizmet uç noktası ilkelerine alışkın değilseniz, daha fazla bilgi edinmek için [hizmet uç noktası ilkelerine genel bakış](virtual-network-service-endpoint-policies-overview.md) bölümüne bakın.

 Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Hizmet uç noktası ilkesi oluşturma
> * Hizmet uç noktası ilke tanımı oluşturma
> * Alt ağ ile sanal ağ oluşturma
> * Hizmet uç noktası ilkesini bir alt ağ ile ilişkilendirme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma 

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-service-endpoint-policy"></a>Hizmet uç noktası ilkesi oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. Arama bölmesinde "hizmet uç noktası ilkesi" yazın ve **hizmet uç noktası İlkesi** ' ni seçin ve ardından **Oluştur**' u seçin.

![Hizmet uç noktası İlkesi Oluştur](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. **Temel** bilgiler bölümünde aşağıdaki bilgileri girin veya seçin 

   - Abonelik: ilke için aboneliğinizi seçin
   - Kaynak grubu: **Yeni oluştur** ' u seçin ve *myresourcegroup* girin
   - Ad: myEndpointPolicy
   - Konum: Orta ABD
 
   ![Hizmet uç noktası İlkesi temelleri oluşturma](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. **Kaynaklar** altında **+ Ekle** ' yi seçin ve **Kaynak Ekle** bölmesinde aşağıdaki bilgileri girin veya seçin

   - Hizmet: hizmet uç noktası Ilkeleriyle yalnızca **Microsoft. Storage** kullanılabilir
   - Kapsam: **tek bir hesap** dışında, **abonelikteki tüm hesaplar** ve **kaynak grubundaki tüm hesaplar** ' ı seçin.
   - Abonelik: depolama hesabı aboneliğinizi seçin. İlke ve depolama hesapları farklı aboneliklerde olabilir.
   - Kaynak grubu: kaynak grubunuzu seçin. Kapsam, "kaynak grubundaki tüm hesaplar" veya "tek hesap" olarak ayarlandıysa gereklidir.  
   - Kaynak: seçili abonelik veya kaynak grubu altında Azure depolama kaynağınızı seçin
   - Kaynağı eklemeyi bitirmeden alt kısımdaki **Ekle** düğmesine tıklayın

   ![Hizmet uç noktası ilke tanımı-kaynak](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Yukarıdaki adımları gerektiği şekilde tekrarlayarak daha fazla kaynak ekleyin

5. İsteğe bağlı: **etiketlere** aşağıdaki bilgileri girin veya seçin:
   
   - Anahtar: ilke için anahtarınızı seçin. Ex: Bölüm     
   - Değer: anahtar için değer çifti girin. Ex: finans

6. **Gözden geçir + Oluştur**’u seçin. Bilgileri doğrulayıp **Oluştur**' a tıklayın. Daha fazla düzenleme yapmak için **önceki** seçeneğine tıklayın. 

   ![Hizmet uç noktası ilkesi son doğrulamaları oluştur](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Uç nokta ilkelerini görüntüleme 

1. Portaldaki *tüm hizmetler* kutusunda, *hizmet uç noktası ilkeleri* yazmaya başlayın. **Hizmet uç noktası ilkeleri**' ni seçin.
2. **Abonelikler**' in altında, aşağıdaki resimde gösterildiği gibi aboneliğinizi ve kaynak grubunuzu seçin

   ![İlkeyi göster](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. İlkeyi seçin ve ilke **tanımları** ' na tıklayarak daha fazla ilke tanımı görüntüleyin veya ekleyin.

   ![İlke tanımlarını göster](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. İlkenin ilişkilendirildiği alt ağları görüntülemek için **ilişkili alt ağları** seçin. Henüz bir alt ağ ilişkilendirilmediyse, sonraki adımdaki yönergeleri izleyin.

   ![İlişkili alt ağlar](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Bir ilkeyi bir alt ağla ilişkilendir

>[!WARNING] 
> İlkeyi verilen alt ağla ilişkilendirmeden önce alt ağdan erişilen tüm kaynakların ilke tanımına eklendiğinden emin olun. İlke ilişkilendirildikten sonra, hizmet uç noktaları üzerinden yalnızca *listelenen kaynaklara izin ver* erişimine izin verilir. 
>
> Ayrıca, hizmet uç noktası ilkesiyle ilişkili olan alt ağda yönetilen bir Azure hizmeti olmadığından emin olun

- Bir ilkeyi bir alt ağla ilişkilendirebilmeniz için önce bir sanal ağ ve alt ağ oluşturmanız gerekir. Bu konuda yardım almak için lütfen [sanal ağ oluşturma](./quick-create-portal.md) makalesine bakın.

- Sanal ağ ve alt ağ kurulduktan sonra, Azure depolama için sanal ağ hizmet uç noktalarını yapılandırmanız gerekir. Sanal ağ dikey penceresinde **hizmet uç noktaları**' nı seçin ve sonraki bölmede **Microsoft. Storage** ' ı ve **alt ağlar** altında, istediğiniz VNET veya alt ağı seçin

- Şimdi aşağıda gösterildiği gibi alt ağ için hizmet uç noktasını yapılandırmadan önce hizmet uç noktası ilkeleri oluşturduysanız, yukarıdaki bölmedeki açılan listeden hizmet uç noktası Ilkesini seçebilirsiniz

    ![Hizmet uç noktası oluştururken alt ağı ilişkilendir](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- Hizmet uç noktaları zaten yapılandırıldıktan sonra hizmet uç noktası ilkelerini ilişkilendirirseniz, aşağıda gösterildiği gibi **Ilişkili alt ağlar** bölmesine giderek alt ağı hizmet uç noktası ilke dikey penceresinden ilişkilendirmeyi seçebilirsiniz

    ![Alt ağı SEP ile ilişkilendir](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Tüm bölgelerdeki Azure depolama kaynaklarına erişim, bu alt ağdaki hizmet uç noktası Ilkesi olarak kısıtlanır.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide bir hizmet uç noktası İlkesi oluşturdunuz ve bir alt ağ ile ilişkilendirdiyseniz. Hizmet uç noktası ilkeleri hakkında daha fazla bilgi için bkz [. hizmet uç noktası ilkelerine genel bakış.](virtual-network-service-endpoint-policies-overview.md)
