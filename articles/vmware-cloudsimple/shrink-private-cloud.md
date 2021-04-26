---
title: Azure VMware çözümünü CloudSimple özel bulutu ile küçültme
description: Mevcut bir vSphere kümesinden düğüm kaldırarak veya tüm kümeyi kaldırarak CloudSimple 'taki özel bulutu dinamik olarak daraltma hakkında bilgi edinin.
author: Ajayan1008
ms.author: v-hborys
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a99b9b56f17b78a98f37d47dcefab26dd9c859de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899142"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>CloudSimple özel bulutunu daraltma

CloudSimple, özel bir bulutu dinamik olarak küçültme esnekliği sağlar.  Bir özel bulut, bir veya daha fazla vSphere kümesinden oluşur. Her küme 3 ile 16 arasında düğüme sahip olabilir. Özel bir bulutu daraldığınızda, var olan kümeden bir düğümü kaldırır veya tüm kümeyi silersiniz. 

## <a name="before-you-begin"></a>Başlamadan önce

Özel bulutun küçültülmesi için aşağıdaki koşulların karşılanması gerekir.  Özel bir bulut oluşturulduğunda oluşturulan yönetim kümesi (ilk küme) silinemez.

* VSphere kümesi üç düğüme sahip olmalıdır.  Yalnızca üç düğümü olan bir küme daraltılamaz.
* Tüketilen toplam depolama alanı, kümenin küçültülmeden sonra toplam kapasiteyi aşmamalıdır.
* Dağıtılmış kaynak Zamanlayıcı (DRS) kurallarının sanal bir makinenin vMotion 'ı önlediği olup olmadığını denetleyin.  Kurallar varsa, kuralları devre dışı bırakın veya silin.  DRS kuralları konak benzeşim kurallarını barındırmak için sanal makineyi içerir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="shrink-a-private-cloud"></a>Özel Bulutu küçültme

1. [CloudSimple portalına erişin](access-cloudsimple-portal.md).

2. **Kaynaklar** sayfasını açın.

3. Küçültmek istediğiniz özel buluta tıklayın

4. Özet sayfasında **Küçült**' e tıklayın.

    ![Özel bulutu Küçült](media/shrink-private-cloud.png)

5. Küçültmek veya silmek istediğiniz kümeyi seçin. 

    ![Özel bulutu Küçült-küme Seç](media/shrink-private-cloud-select-cluster.png)

6. **Bir düğüm kaldır** ' ı veya **tüm kümeyi Sil**' i seçin. 

7. Küme kapasitesini doğrulama

8. Özel bulutu daraltmak için **Gönder** ' e tıklayın.

Özel bulutun küçültülmesi başlar.  Görevlerin ilerlemesini izleyebilirsiniz.  Daraltma işlemi, sanal San 'da yeniden eşitlenmesi gereken verilere bağlı olarak birkaç saat sürebilir.

> [!NOTE]
> 1. Veri merkezinde son veya tek kümeyi silerek özel bir bulutu daraldıysanız, veri merkezi silinmez.
> 2. Herhangi bir DRS kuralı ihlali oluşursa, düğüm kümeden kaldırılmaz ve görev açıklaması, düğüm kaldırma işleminin kümedeki DRS kurallarını ihlal edeceğini gösterir.    


## <a name="next-steps"></a>Sonraki adımlar

* [Azure'da VMware sanal makinelerini kullanma](quickstart-create-vmware-virtual-machine.md)
* [Özel bulutlar](cloudsimple-private-cloud.md) hakkında daha fazla bilgi edinin
