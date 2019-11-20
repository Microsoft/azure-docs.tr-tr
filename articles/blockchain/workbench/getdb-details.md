---
title: Azure blok zincirini al çalışma ekranı önizleme veritabanı ayrıntıları
description: Azure blok zinciri çalışma ekranı önizleme veritabanı ve veritabanı sunucusu bilgilerini nasıl alabileceğinizi öğrenin.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f4a4eaab9a03aeed27e29eb645b6e22a028b243b
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845067"
---
# <a name="get-information-about-your-azure-blockchain-workbench-preview-database"></a>Azure blok zincirim çalışma ekranı önizleme veritabanınız hakkında bilgi alın

Bu makalede, Azure blok zinciri çalışma ekranı önizleme veritabanınız hakkında ayrıntılı bilgilerin nasıl alınacağı gösterilmektedir.

## <a name="overview"></a>Genel Bakış

Uygulamalar, iş akışları ve akıllı sözleşme yürütme ile ilgili bilgiler Blockchain Workbench SQL DB’deki veritabanı görünümleri kullanılarak sağlanır. Geliştiriciler Microsoft Excel, Power BI, Visual Studio ve SQL Server Management Studio gibi araçları kullanırken bu bilgileri kullanabilir.

Bir geliştiricinin veritabanına bağlanabilmesi için önce şunlar gerekir:

* Veritabanı güvenlik duvarında dış istemci erişimine izin verilmesi. Veritabanı güvenlik duvarı yapılandırmayla ilgili bu makalede erişime nasıl izin verileceği açıklanır.
* Veritabanı sunucu adı ve veritabanı adı.

## <a name="connect-to-the-blockchain-workbench-database"></a>Blockchain Workbench veritabanına bağlanma

Veritabanına bağlanmak için:

1. Azure blok zinciri çalışma ekranı kaynakları için **sahip** izinlerine sahip bir hesapla Azure Portal oturum açın.
2. Sol gezinti bölmesinden **Kaynak Grupları**'nı seçin.
3. Blockchain Workbench dağıtımınıza yönelik kaynak grubunun adını seçin.
4. Kaynak listesini sıralamak için **Tür**’ü, sonra da **SQL sunucunuzu** seçin. Bir sonraki ekran görüntüsünde yer alan sıralı listede "master" adlı bir veritabanının yanı sıra **Kaynak ön eki** olarak "lhgn" kullanan bir veritabanı şeklinde iki SQL veritabanı gösterilir.

   ![Sıralı Blockchain Workbench kaynak listesi](./media/getdb-details/sorted-workbench-resource-list.png)

5. Blockchain Workbench veritabanıyla ilgili ayrıntılı bilgileri görüntülemek üzere Blockchain Workbench’i dağıtmak için sağladığınız **Kaynak ön ekine** sahip veritabanının bağlantısını seçin.

   ![Veritabanı ayrıntıları](./media/getdb-details/workbench-db-details.png)

Veritabanı sunucu adı ve veritabanı adı, geliştirme ya da raporlama aracınızı kullanarak Blockchain Workbench veritabanına bağlanmanıza imkan tanır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench’te veritabanı görünümleri](database-views.md)