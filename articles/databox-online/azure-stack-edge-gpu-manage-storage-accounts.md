---
title: Azure Stack Edge Pro GPU depolama hesabı yönetimi | Microsoft Docs
description: Azure Stack Edge Pro 'unuzda depolama hesabını yönetmek için Azure portal nasıl kullanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 2d9520c8f97171dbf2f46aa2c94b9e1e280ed86c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201274"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>Azure portal kullanarak Azure Stack Edge Pro'nuzda Edge depolama hesaplarınızı yönetme 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede Azure Stack Edge Pro 'unuzda Edge depolama hesaplarının nasıl yönetileceği açıklanır. Azure Stack Edge Pro 'Yu Azure portal veya yerel Web Kullanıcı arabirimi aracılığıyla yönetebilirsiniz. Cihazınızda Edge depolama hesaplarını eklemek veya silmek için Azure portal kullanın.

## <a name="about-edge-storage-accounts"></a>Edge depolama hesapları hakkında

Azure Stack Edge Pro cihazınızdan SMB, NFS veya REST protokolleri aracılığıyla veri aktarabilirsiniz. REST API 'Lerini kullanarak BLOB depolama alanına veri aktarmak için Azure Stack Edge Pro 'unuzda Edge depolama hesapları oluşturmanız gerekir. 

Azure Stack Edge Pro cihazına eklediğiniz Edge depolama hesapları Azure depolama hesaplarına eşlenir. Edge depolama hesaplarına yazılan tüm veriler otomatik olarak buluta gönderilir.

İki hesap türünü ve bu hesaplardan her birinden Azure 'a veri akışını ayrıntılarıyla açıklayan bir diyagram aşağıda gösterilmektedir:

![BLOB depolama hesapları diyagramı](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Edge depolama hesabı ekleme
> * Edge depolama hesabını silme


## <a name="add-an-edge-storage-account"></a>Edge depolama hesabı ekleme

Bir Edge depolama hesabı oluşturmak için aşağıdaki yordamı uygulayın:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Edge depolama hesabını silme

Bir Edge depolama hesabını silmek için aşağıdaki adımları uygulayın.

1. Kaynağınızın **yapılandırma > depolama hesapları** bölümüne gidin. Depolama hesapları listesinden silmek istediğiniz depolama hesabını seçin. Üstteki komut çubuğundan **Depolama hesabını Sil**' i seçin.

    ![Depolama hesapları listesine git](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. **Depolama hesabını Sil** dikey penceresinde, silinecek depolama hesabını onaylayın ve **Sil**' i seçin.

    ![Depolama hesabını onaylayın ve silin](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

Depolama hesaplarının listesi, silme işlemini yansıtacak şekilde güncelleştirilir.


## <a name="add-delete-a-container"></a>Kapsayıcı ekleme, silme

Ayrıca, bu depolama hesaplarının kapsayıcılarını ekleyebilir veya silebilirsiniz.

Bir kapsayıcı eklemek için aşağıdaki adımları uygulayın:

1. Yönetmek istediğiniz depolama hesabını seçin. Üstteki komut çubuğundan **+ kapsayıcı Ekle**' yi seçin.

    ![Kapsayıcı eklemek için depolama hesabı seçin](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. Kapsayıcınız için bir ad girin. Bu kapsayıcı, Edge depolama hesabınızda ve bu hesapla eşlenmiş Azure Storage hesabı 'nda oluşturulur. 

    ![Kenar kapsayıcısı Ekle](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

Kapsayıcıların listesi, yeni eklenen kapsayıcıyı yansıtacak şekilde güncelleştirilir.

![Kapsayıcıların güncelleştirilmiş listesi](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

Artık bu listeden bir kapsayıcı seçip üstteki komut çubuğundan **+ kapsayıcıyı sil** ' i seçebilirsiniz. 

![Kapsayıcı silme](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Depolama anahtarlarını eşitleme

Cihazınızdaki kenar (yerel) depolama hesapları için erişim anahtarlarını eşzamanlı hale getirebilirsiniz. 

Depolama hesabı erişim anahtarını eşitlemek için aşağıdaki adımları uygulayın:

1. Kaynağında, yönetmek istediğiniz depolama hesabını seçin. Üstteki komut çubuğundan, **eşitleme depolama anahtarı**' nı seçin.

    ![Eşitleme depolama anahtarını seçin](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. Onay istendiğinde **Evet**' i seçin.

    ![Eşitleme depolama anahtarını seçin 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal aracılığıyla kullanıcıları yönetme](azure-stack-edge-gpu-manage-users.md) hakkında bilgi edinin.
