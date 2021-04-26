---
title: Öğretici-Azure VMware çözümü özel bulutu dağıtma
description: Azure VMware çözümü özel bulutu oluşturmayı ve dağıtmayı öğrenin
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: ea4bf27a1ff14e4872bc2a0e19daa032dd4ba66d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870922"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Öğretici: Azure VMware çözümü özel bulutu dağıtma

Azure VMware çözümü, Azure 'da bir vSphere kümesi dağıtmanıza olanak tanır. En düşük ilk dağıtım üç ana bilgisayar. Küme başına en fazla 16 ana bilgisayar için, ek konaklar tek seferde eklenebilir.

Azure VMware çözümü, başlatma sırasında özel bulutunuzu şirket içi vCenter 'ünüzle yönetmenize izin vermediğinden ek yapılandırma gerekir. Bu yordamlar ve ilgili Önkoşullar Bu öğreticide ele alınmıştır.

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Azure VMware çözümü özel bulutu oluşturma
> * Özel bulutun dağıtıldığını doğrulayın

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Özel bir bulut oluşturmak için uygun yönetici hakları ve izinleri. Abonelikte en az katkıda bulunan düzeyinde olmanız gerekir.
- Azure VMware çözümünü dağıtmak için [planlama](production-ready-deployment-steps.md) makalesinde topladığınız bilgileri izleyin.
- [Öğretici: ağ denetim listesi](tutorial-network-checklist.md)' nde açıklandığı şekilde yapılandırılmış uygun ağa sahip olduğunuzdan emin olun.
- Konaklar sağlandı ve Microsoft. AVS kaynak sağlayıcısı, Istek içinde açıklandığı şekilde kaydedilir [ve Microsoft. AVS kaynak sağlayıcısı 'nı etkinleştirir](enable-azure-vmware-solution.md).

## <a name="create-a-private-cloud"></a>Özel bulut oluşturma

[Azure Portal](#azure-portal) kullanarak veya [Azure CLI](#azure-cli)kullanarak bir Azure VMware çözümü özel bulutu oluşturabilirsiniz.

### <a name="azure-portal"></a>Azure portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Azure VMware çözümü özel bulutu oluşturmak için Azure portal yerine Azure Cloud Shell kullanarak Azure CLı 'yi kullanabilirsiniz.  Azure VMware çözümü ile kullanabileceğiniz komutların listesi için bkz. [Azure VMware komutları](/cli/azure/vmware).

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell’i açma

Bir kod bloğunun sağ üst köşesinden **deneyin** öğesini seçin. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve **ENTER** tuşuna basarak çalıştırın.

#### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[' Az Group Create '](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Özel bulut oluşturma

Kaynak grubu ve özel bulut, bir konum ve kümenin boyutu için bir ad sağlayın.

| Özellik  | Açıklama  |
| --------- | ------------ |
| **-g** (kaynak grubu adı)     | Özel bulut kaynaklarınız için kaynak grubunun adı.        |
| **-n** (özel bulut adı)     | Azure VMware çözümünüz özel bulutunuzun adı.        |
| **--konum**     | Özel bulutunuz için kullanılan konum.         |
| **--küme-boyut**     | Kümenin boyutu. En küçük değer 3 ' dir.         |
| **--Ağ-bloğu**     | Özel bulutunuz için kullanılacak CıDR IP adresi ağ bloğu. Adres bloğu, aboneliğinizdeki ve şirket içi ağlardaki diğer sanal ağlarda kullanılan adres bloklarıyla çakışmamalıdır.        |
| **--SKU** | SKU değeri: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Azure VMware komutları

Azure VMware çözümü ile kullanabileceğiniz komutların listesi için bkz. [Azure VMware komutları](/cli/azure/vmware).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure VMware çözümü özel bulutu oluşturma
> * Özel bulutun dağıtıldığını doğrulayın
> * Azure VMware çözümünün özel bulutunu silme

Bir sıçrama kutusu oluşturmayı öğrenmek için bir sonraki öğreticiye geçin. Özel bulutunuzu yerel olarak yönetebilmeniz için ortamınıza bağlanmak üzere bağlantı kutusunu kullanın.


> [!div class="nextstepaction"]
> [Azure VMware çözümüne özel buluta erişme](tutorial-access-private-cloud.md)
