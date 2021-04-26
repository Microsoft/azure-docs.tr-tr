---
title: Azure DNS Azure portal DNS bölgelerini yönetme | Microsoft Docs
description: Azure portal kullanarak DNS bölgelerini yönetebilirsiniz. Bu makalede, Azure DNS üzerinde DNS bölgelerini güncelleştirme, silme ve oluşturma işlemlerinin nasıl yapılacağı açıklanmaktadır
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 51e17812e1116f1e625685d5a818c18e25cdb2da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965808"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Azure portal DNS Bölgeleri Yönetme

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure klasik CLı](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Bu makalede, Azure portal kullanarak DNS bölgelerinizi nasıl yöneteceğiniz gösterilmektedir. DNS bölgelerinizi, platformlar arası [Azure CLI](dns-operations-dnszones-cli.md) veya Azure [PowerShell](dns-operations-dnszones.md)kullanarak da yönetebilirsiniz.

## <a name="create-a-dns-zone"></a>DNS bölgesi oluşturma

1. Azure portalında oturum açın
2. Hub menüsünde, **DNS bölgesi oluştur** dikey penceresini açmak Için **kaynak oluştur > ağ > DNS bölgesi** ' ne gidin.

    ![DNS bölgesi](./media/dns-operations-dnszones-portal/openzone650.png)

4. **DNS bölgesi oluştur** dikey penceresinde aşağıdaki değerleri girin ve **Oluştur**’a tıklayın:


   | **Ayar** | **Değer** | **Ayrıntılar** |
   |---|---|---|
   |**Ad**|contoso.com|DNS bölgesinin adı|
   |**Abonelik**|[Aboneliğiniz]|DNS bölgesini oluşturmak için bir abonelik seçin.|
   |**Kaynak grubu**|**Yeni oluştur:** contosoDNSRG|Bir kaynak grubu oluşturun. Kaynak grubu adı, seçili abonelik içinde benzersiz olmalıdır. Kaynak grupları hakkında daha fazla bilgi edinmek için [Kaynak Yöneticisi](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) genel bakış makalesini okuyun.|
   |**Konum**|Batı ABD||

> [!NOTE]
> Kaynak grubu, kaynak grubunun konumunu ifade eder ve DNS bölgesini etkilemez. DNS bölgesinin konumu her zaman "genel" şeklindedir ve gösterilmez.

## <a name="list-dns-zones"></a>DNS bölgelerini listeleme

Azure Portal **daha fazla hizmet**  >  **ağı**  >  **DNS bölgesi**' ne gidin. Her DNS bölgesi kendi kaynağıdır ve kayıt kümesi ve ad sunucularının sayısı gibi bilgiler bu görünümden görüntülenebilir. Sütun **adı sunucuları** varsayılan görünümde değil. Eklemek için, **sütunlar**' a tıklayın, **ad sunucuları**' nı seçin ve **bitti**' ye tıklayın.

![DNS bölgelerini listeleme](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS bölgesini silme

Portalda bir DNS bölgesine gidin. **DNS bölgesi** dikey penceresinde **bölgeyi Sil**' e tıklayın. Daha sonra, DNS bölgesini silmek istediğinizi onaylamanız istenir. DNS bölgesini silme, bölgede bulunan tüm kayıtları da siler.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Portal kullanarak Azure DNS kullanmaya başlama](dns-getstarted-portal.md)' yı zıyaret ederek DNS bölgeniz ve kayıtlarıyla nasıl çalışacağınızı öğrenin.