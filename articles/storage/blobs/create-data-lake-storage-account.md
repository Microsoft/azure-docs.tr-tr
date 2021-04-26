---
title: Azure Data Lake Storage 2. için bir depolama hesabı oluşturun
description: Azure Data Lake Storage 2. ile kullanmak üzere bir depolama hesabı oluşturmayı öğrenin.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624346"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ile kullanmak için bir depolama hesabı oluşturma

Data Lake Storage 2. yeteneklerini kullanmak için hiyerarşik bir ad alanına sahip bir depolama hesabı oluşturun.

## <a name="choose-a-storage-account-type"></a>Depolama hesabı türü seçin

Data Lake Storage özellikleri aşağıdaki depolama hesabı türlerinde desteklenir:

- Genel amaçlı v2
- BlockBlobStorage

Aralarında nasıl seçim yapılacağı hakkında daha fazla bilgi için bkz. [depolama hesabına genel bakış](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Hiyerarşik ad alanı olan bir depolama hesabı oluşturma

**Hiyerarşik ad alanı** ayarı etkin olan bir [genel amaçlı v2 hesabı](../common/storage-account-create.md) ya da [blockblobstorage](storage-blob-create-account-block-blob.md) hesabı oluşturun.

**Depolama hesabı oluştur** sayfasının **Gelişmiş** sekmesinde **hiyerarşik ad alanı** ayarını etkinleştirerek, hesabı oluştururken Data Lake Storage yeteneklerini açın. Hesabı oluştururken bu ayarı etkinleştirmeniz gerekir. Daha sonra etkinleştiremezsiniz.

Aşağıdaki görüntüde, bu ayar **depolama hesabı oluştur** sayfasında gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![Hiyerarşik ad alanı ayarı](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Data Lake Storage ile kullanmak istediğiniz mevcut bir depolama hesabınız varsa ve hiyerarşik ad alanı ayarı devre dışı bırakılmışsa, verileri ayarı etkinleştirilmiş yeni bir depolama hesabına geçirmeniz gerekir.

> [!NOTE]
> **Veri koruma** ve **hiyerarşik ad alanı** eşzamanlı olarak etkinleştirilemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesabına genel bakış](../common/storage-account-overview.md)
- [Büyük veri gereksinimleri için Azure Data Lake Storage 2. kullanma](data-lake-storage-data-scenarios.md)
- [Azure Data Lake Storage 2. Nesil'de Erişim Denetimi](data-lake-storage-access-control.md)
