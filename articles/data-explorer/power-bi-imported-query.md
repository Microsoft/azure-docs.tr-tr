---
title: "İçeri aktarılan bir sorgu Power BI ile Azure veri Gezgini'nde verileri Görselleştirme "
description: "Bu makalede, Power bı'da verileri görselleştirmek için üç seçenekten birini kullanmayı öğrenin: Azure veri Gezgini'nde bir sorgu alma."
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 6c2c8457cee004bf1288656ec9746b703d41aee1
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806414"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Power BI'a aktarılan bir sorgu kullanarak verileri Görselleştir

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. Power BI, verilerinizi görselleştirmenizi ve sonuçları kuruluşunuzda paylaşmanızı sağlayan bir iş analizi çözümüdür.

Azure Veri Gezgini, Power bı'daki verilere bağlanmak için üç seçenek sunar: yerleşik Bağlayıcısı, Azure veri Gezgini'nde bir sorguyu içeri aktarmak veya bir SQL sorgusu kullanın. Bu makalede veri almak ve bir Power BI raporuna görselleştirme böylece bir sorguyu içeri aktarmak nasıl gösterir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede tamamlamak için şunlara ihtiyacınız vardır:

* Bağlanabilir, böylece Azure Active directory üyesi olan bir kuruluş e-posta hesabı [Azure Veri Gezgini Yardım kümesi](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (seçin **DOWNLOAD FREE**)

* [Azure Veri Gezgini masaüstü uygulaması](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Azure veri Gezgini'nde verileri alma

İlk olarak, Azure Veri Gezgini masaüstü uygulamasında bir sorgu oluşturun ve Power BI kullanmak için dışarı aktarın. Ardından, Azure Veri Gezgini Yardım kümeye bağlanın ve verilerin bir alt getirin *StormEvents* tablo. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Bir tarayıcıda Git [ https://help.kusto.windows.net/ ](https://help.kusto.windows.net/) Azure Veri Gezgini Masaüstü uygulamasını başlatmak için.

1. İçinde bir masaüstü uygulaması, aşağıdaki sorguyu çalıştırın sağ sorgu penceresine kopyalayın.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Sonuç kümesi ilk birkaç satırı aşağıdaki görüntüye benzer olmalıdır.

    ![Sorgu sonuçları](media/power-bi-imported-query/query-results.png)

1. Üzerinde **Araçları** sekmesinde **sorgulamak için Power BI** ardından **Tamam**.

    ![Sorgu dışarı aktarma](media/power-bi-imported-query/export-query.png)

1. Power BI Desktop'ta üzerinde **giriş** sekmesinde **Veri Al** ardından **boş sorgu**.

    ![Verileri alma](media/power-bi-imported-query/get-data.png)

1. Güç sorgu Düzenleyicisi'nde, üzerinde **giriş** sekmesinde **Gelişmiş Düzenleyici**.

1. İçinde **Gelişmiş Düzenleyici** penceresi, dışarı aktardığınız sorgu seçip yapıştırma **Bitti**.

    ![Yapıştırma sorgu](media/power-bi-imported-query/paste-query.png)

1. Ana Power Query Düzenleyicisi penceresinde, seçin **kimlik bilgilerini Düzenle**. Seçin **kuruluş hesabı**, oturum açın ve ardından seçin **Connect**.

    ![Kimlik bilgilerini Düzenle](media/power-bi-imported-query/edit-credentials.png)

1. Üzerinde **giriş** sekmesinde **Kapat & Uygula**.

    ![Kapat ve uygula](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Bir rapordaki verileri görselleştirin

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede, oluşturulan rapor artık ihtiyacınız kalmadığında Power BI Desktop (.pbix) dosyasını silin.

## <a name="next-steps"></a>Sonraki adımlar

[Power BI için Azure Veri Gezgini Bağlayıcısı'nı kullanarak verileri Görselleştir](power-bi-connector.md)