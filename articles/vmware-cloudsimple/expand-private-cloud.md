---
title: CloudSimple özel bulutu tarafından Azure VMware çözümünü Genişlet
description: Mevcut bir CloudSimple özel bulutun, var olan veya yeni bir kümede kapasite eklemek için nasıl genişletilebileceğinizi açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816169"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>CloudSimple özel bulutu genişletme

CloudSimple, özel bir bulutu dinamik olarak genişletme esnekliği sağlar. Daha küçük bir yapılandırmayla başlayabilir ve daha yüksek kapasiteye ihtiyaç duyduğunuzda öğesini genişletebilirsiniz. Ya da geçerli ihtiyaçları temel alarak özel bir bulut oluşturabilir ve sonra da tüketim büyüdükçe öğesini genişletebilirsiniz.

Bir özel bulut, bir veya daha fazla vSphere kümesinden oluşur. Her küme 3 ile 16 arasında düğüme sahip olabilir.  Özel bir bulutu genişletirken, var olan kümeye düğümleri ekler veya yeni bir küme oluşturun. Var olan bir kümeyi genişletmek için, ek düğümlerin mevcut düğümlerle aynı türde (SKU) olması gerekir. Yeni bir küme oluşturmak için düğümler farklı türde olabilir. Özel bulut limitleri hakkında daha fazla bilgi için, [Cloudsimple özel bulutuna genel bakış](cloudsimple-private-cloud.md) makalesindeki sınırlar bölümüne bakın.

VCenter üzerinde varsayılan **veri merkezi** ile özel bir bulut oluşturulur.  Her veri merkezi en üst düzey yönetim varlığı görevi görür.  CloudSimple, yeni bir küme için var olan veri merkezine ekleme veya yeni bir veri merkezi oluşturma seçeneği sunar.

Yeni küme yapılandırmasının bir parçası olarak CloudSimple, VMware altyapısını yapılandırır.  Bu ayarlar, vSAN disk grupları, VMware yüksek kullanılabilirlik ve dağıtılmış kaynak Zamanlayıcı (DRS) için depolama ayarlarını içerir.

Özel bulut, birden çok kez genişletilebilir. Genişletme, yalnızca genel düğüm sınırları içinde kaldığınızda yapılabilir. Bir özel bulutu her genişletişinizde, var olan kümeye ekler veya yeni bir bulut oluşturabilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Özel bulutunuzu genişletebilmeniz için önce düğümlerin sağlanması gerekir.  Düğüm sağlama hakkında daha fazla bilgi için bkz. [CloudSimple-Azure Ile VMware çözümü için düğüm sağlama](create-nodes.md) .  Yeni bir küme oluşturmak için, aynı SKU 'nun en az üç tane kullanılabilir düğümü olmalıdır.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="expand-a-private-cloud"></a>Özel bulutu Genişlet

1. [CloudSimple portalına erişin](access-cloudsimple-portal.md).

2. **Kaynaklar** sayfasını açın ve genişletmek Istediğiniz özel bulutu seçin.

3. Özet bölümünde **Genişlet**' e tıklayın.

    ![Özel bulutu Genişlet](media/resources-expand-private-cloud.png)

4. Mevcut kümenizi genişletip genişletmeyeceğinizi veya yeni bir vSphere kümesi oluşturmayı seçin. Değişiklik yaparken sayfadaki Özet bilgileri güncelleştirilir.

    * Mevcut kümenizi genişletmek için, **var olan kümeyi Genişlet**' e tıklayın. Genişletmek istediğiniz kümeyi seçin ve eklenecek düğüm sayısını girin. Her küme en fazla 16 düğüm içerebilir.
    * Yeni küme eklemek için **Yeni küme oluştur**' a tıklayın. Küme için bir ad girin. Mevcut bir veri merkezini seçin veya yeni bir veri merkezi oluşturmak için bir ad girin. Düğüm türünü seçin. Yeni bir vSphere kümesi oluştururken farklı bir düğüm türü seçebilirsiniz, ancak var olan bir vSphere kümesini genişlettikten sonra bunu yapabilirsiniz. Düğüm sayısını seçin. Her yeni kümenin en az üç düğümü olmalıdır.

    ![Özel bulutu Genişlet-düğüm ekleme](media/resources-expand-private-cloud-add-nodes.png)

5. Özel bulutu genişletmek için **Gönder** ' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da VMware VM 'lerini kullanma](quickstart-create-vmware-virtual-machine.md)
* [Özel bulutlar](cloudsimple-private-cloud.md) hakkında daha fazla bilgi edinin