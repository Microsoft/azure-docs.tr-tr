---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99569484"
---
Varsayılan olarak, standart dosya paylaşımları yalnızca 5 TiB 'ye yayılabilir, ancak paylaşım limitini 100 TiB olarak artırabilirsiniz. Paylaşma sınırınızı artırmak için depolama hesabınızda **büyük dosya paylaşımının** etkinleştirin. Premium Depolama hesapları (*FileStorage* depolama hesapları), tüm Premium dosya paylaşımları tam 100-TİB kapasitesine sağlanması için zaten etkinleştirildiğinden büyük dosya paylaşım özelliği bayrağına sahip değildir.

Yalnızca yerel olarak yedekli veya bölge yedekli standart depolama hesaplarında büyük dosya paylaşımlarını etkinleştirebilirsiniz. Büyük dosya paylaşma özelliği bayrağını etkinleştirdikten sonra, artıklık düzeyini coğrafi olarak yedekli veya coğrafi bölgeye yedekli depolama olarak değiştiremezsiniz.

Var olan bir depolama hesabında büyük dosya paylaşımlarını etkinleştirmek için, depolama hesabının İçindekiler tablosundaki **dosya paylaşımları** ' na gidin.
Bu dikey pencerede **kapasiteyi paylaşma**' yı seçin, paylaşma kapasitesini **100 TİB** olarak değiştirin ve **Kaydet**' i seçin.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Azure portal büyük dosya paylaşımının kullanımını etkinleştir ayarının ekran görüntüsü." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Ayrıca, [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet 'i ve [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure CLI komutu aracılığıyla 100-tib dosya paylaşımlarını da etkinleştirebilirsiniz. Büyük dosya paylaşımlarının etkinleştirilmesi hakkında ayrıntılı yönergeler için bkz. [Enable ve, büyük dosya paylaşımlarını oluşturma](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Yeni depolama hesaplarında dosya paylaşımları oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure dosya paylaşımı oluşturma](../articles/storage/files/storage-how-to-create-file-share.md).