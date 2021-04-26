---
title: 'Hızlı başlangıç: SYNAPSE SQL havuzu için ölçek işlem (Azure portal)'
description: Azure portal kullanarak SYNAPSE SQL havuzunun (veri ambarı) işlem ölçeğini ölçeklendirebilirsiniz.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: jrasnick
ms.date: 04/28/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: aa339274a1ff68764fa5573d9c031e84f290e57c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534221"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Hızlı başlangıç: Azure portal ile SYNAPSE SQL havuzu için işlem ölçekleme

Azure portal kullanarak SYNAPSE SQL havuzunun (veri ambarı) işlem ölçeğini ölçeklendirebilirsiniz. Daha iyi performans için [işlemin ölçeğini genişletin](sql-data-warehouse-manage-compute-overview.md) veya maliyet tasarrufu sağlamak için işlemin ölçeğini geri daraltın. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="before-you-begin"></a>Başlamadan önce

Zaten sahip olduğunuz bir SQL havuzunu ölçeklendirebilir veya [hızlı başlangıç: oluşturma ve bağlanma-Portal](create-data-warehouse-portal.md) ' a, **mysampledatawarehouse** adlı bir SQL havuzu oluşturabilirsiniz. Bu hızlı başlangıç, **mySampleDataWarehouse** öğesini ölçeklendirir.

>[!IMPORTANT] 
>Ölçeklendirilmesi için SQL havuzunuzun çevrimiçi olması gerekir. 

## <a name="scale-compute"></a>Hesaplamayı ölçeklendirme

SQL havuzu işlem kaynakları, veri ambarı birimleri arttırılarak veya azaltılarak ölçeklendirilebilir. [Hızlı başlangıç: oluşturma ve bağlanma-Portal](create-data-warehouse-portal.md) **mysampledatawarehouse** oluşturdu ve 400 dwus ile başlatıldı. Aşağıdaki adımlar, **mySampleDataWarehouse** için DWU’ları ayarlar.

Veri ambarı birimlerini değiştirmek için:

1. Azure portal sol sayfasında **Azure SYNAPSE Analytics (eski ADıYLA SQL DW)** seçeneğine tıklayın.
2. **Azure SYNAPSE Analytics (eski ADıYLA SQL DW)** sayfasından **mysampledatawarehouse** öğesini seçin. SQL Havuzu açılır.
3. **Ölçek** seçeneğine tıklayın.

    ![Ölçek seçeneğine tıklayın](./media/quickstart-scale-compute-portal/click-scale.png)

2. Ölçek panelinde kaydırıcıyı sola veya sağa hareket ettirerek DWU ayarını değiştirin. Ardından ölçek ' i seçin.

    ![Kaydırıcıyı Taşıyın](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Sonraki adımlar
SQL havuzu hakkında daha fazla bilgi edinmek için [VERILERI SQL havuzu](./load-data-from-azure-blob-storage-using-copy.md) öğreticisine yüklemeye devam edin.
