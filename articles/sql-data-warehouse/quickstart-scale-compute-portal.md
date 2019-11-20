---
title: 'Hızlı başlangıç: ölçek işlem-Azure portal '
description: Azure portalından Azure SQL Veri Ambarı’nda işlemi ölçeklendirin. Daha iyi performans için işlem ölçeğini genişletin veya maliyet tasarrufu için işlem ölçeğini daraltın.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 59d929165ac9618d68707e2f13741e7bbba7b37f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685968"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Hızlı Başlangıç: Azure portalından Azure SQL Veri Ambarı’nda işlemi ölçeklendirme

Azure portalından Azure SQL Veri Ambarı’nda işlemi ölçeklendirin. Daha iyi performans için [işlem ölçeğini genişletin](sql-data-warehouse-manage-compute-overview.md) veya maliyet tasarrufu için işlem ölçeğini daraltın. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="before-you-begin"></a>Başlamadan önce

Zaten sahip olduğunuz bir veri ambarını ölçeklendirebilir veya [hızlı başlangıç: oluşturma ve bağlanma-Portal](create-data-warehouse-portal.md) ' ı kullanarak **mysampledatawarehouse**adlı bir veri ambarı oluşturabilirsiniz.  Bu hızlı başlangıç, **mySampleDataWarehouse** öğesini ölçeklendirir.

>[!Note]
>Ölçeklendirilmesi için veri ambarınızın çevrimiçi olması gerekir. 

## <a name="scale-compute"></a>Hesaplamayı ölçeklendirme

SQL veri ambarı işlem kaynakları, veri ambarı birimleri arttırılarak veya azaltılarak ölçeklendirilebilir. [Oluşturma ve bağlanma-Portal] hızlı başlangıç (Create-Data-Warehouse-portal.md), **Mysampledatawarehouse** oluşturdu ve 400 dwus ile başlatıldı. Aşağıdaki adımlar, **mySampleDataWarehouse** için DWU’ları ayarlar.

Veri ambarı birimlerini değiştirmek için:

1. Azure portalının sol taraftaki sayfasında **SQL veri ambarları**’na tıklayın.
2. **SQL veri ambarları** sayfasından **mySampleDataWarehouse** seçeneğini belirleyin. Veri ambarı açılır.
3. **Ölçek** seçeneğine tıklayın.

    ![Ölçek seçeneğine tıklayın](media/quickstart-scale-compute-portal/click-scale.png)

2. Ölçek panelinde kaydırıcıyı sola veya sağa hareket ettirerek DWU ayarını değiştirin.

    ![Kaydırıcıyı Taşıyın](media/quickstart-scale-compute-portal/scale-dwu.png)

3. **Kaydet** düğmesine tıklayın. Bir onay iletisi görüntülenir. Onaylamak için **evet**’e, iptal etmek için **hayır**’a tıklayın.

    ![Kaydet’e tıklayın.](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Sonraki adımlar
Artık veri ambarınızın işlem ölçeğini ölçeklendirdiniz. Azure SQL Veri Ambarı hakkında daha fazla bilgi edinmek için, veri yükleme öğreticisiyle devam edin.

> [!div class="nextstepaction"]
>[Verileri bir SQL veri ambarına yükleme](load-data-from-azure-blob-storage-using-polybase.md)
