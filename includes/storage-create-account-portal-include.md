---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718285"
---
Azure portalında genel amaçlı v2 bir depolama hesabı oluşturmak için aşağıdaki adımları izleyin:

1. Azure portalı menüsünde **Tüm hizmetler**’i seçin. Kaynak listesinde **Depolama Hesapları** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Depolama hesapları**' nı seçin.
1. Görüntülenen **depolama hesapları** penceresinde **+ Yeni**' yi seçin.
1. **Temel bilgiler** dikey penceresinde, depolama hesabının oluşturulacağı aboneliği seçin.
1. **Kaynak grubu** alanında, istediğiniz kaynak grubunu seçin veya yeni bir kaynak grubu oluşturun.  Azure Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../articles/azure-resource-manager/management/overview.md).
1. Ardından, depolama hesabınız için bir ad girin. Seçtiğiniz ad Azure genelinde benzersiz olmalıdır. Ad ayrıca 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir.
1. Depolama hesabınız için bir bölge seçin veya varsayılan bölgeyi kullanın.
1. Bir performans katmanı seçin. Varsayılan katman *standarttır*.
1. Depolama hesabının nasıl çoğaltılacağı belirtin. Varsayılan artıklık seçeneği, *coğrafi olarak yedekli depolama (GRS)*' dir. Kullanılabilir çoğaltma seçenekleri hakkında daha fazla bilgi için bkz. [Azure depolama artıklığı](../articles/storage/common/storage-redundancy.md).
1. **Gelişmiş**, **ağ**, **veri koruma** ve **Etiketler** dikey pencerelerinde ek seçenekler mevcuttur. Azure Data Lake Storage kullanmak için, **Gelişmiş** dikey pencereyi seçin ve ardından **hiyerarşik ad alanını** **etkin** olarak ayarlayın. Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. giriş](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Depolama hesabı ayarlarınızı gözden geçirmek ve hesabı oluşturmak için **Gözden Geçir + Oluştur**’u seçin.
1. **Oluştur**’u seçin.

Aşağıdaki görüntüde yeni bir depolama hesabı için **temel bilgiler** dikey penceresindeki ayarlar gösterilmektedir:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Azure portal depolama hesabı oluşturmayı gösteren ekran görüntüsü." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
