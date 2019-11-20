---
title: Yeniden Dash ile Azure Veri Gezgini görselleştirme
description: Bu makalede, Azure Veri Gezgini 'daki verileri Redash yerel Bağlayıcısı ile görselleştirmeyi öğreneceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8bb8711bc4a6134ec740a55d9f5d5794b2de77ca
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588596"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Azure Veri Gezgini verileri yeniden tireyle görselleştirin

[Redash](https://www.redash.io/) , veri kaynaklarınızı bağlar ve sorgular, verileri görselleştirmek ve eşlerle paylaşmak için panolar oluşturur. Bu makalede, Azure Veri Gezgini 'i yeniden Dash için bir veri kaynağı olarak ayarlamayı ve sonra verileri görselleştirmeyi öğreneceksiniz.

## <a name="prerequisites"></a>Ön koşullar

1. [Küme ve veritabanı oluşturma](create-cluster-database-portal.md).
1. [Azure Veri Gezgini 'de örnek verileri içe](ingest-sample-data.md)alma bölümünde açıklandığı gibi verileri alma. Daha fazla alma seçeneği için bkz. alma [genel bakış](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Yeniden Dash 'de Azure Veri Gezgini Bağlayıcısı oluşturma 

1. [Yeniden Dash](https://www.redash.io/)için oturum açın. Hesap **oluşturmak için başlayın** ' ı seçin.
1. **Haydi başlayalım**' ın altında **bir veri kaynağı bağla**' yı seçin.

    ![Veri kaynağını bağlama](media/redash/connect-data-source.png)

1. **Yeni veri kaynağı oluştur** penceresinde **Azure Veri Gezgini (kusto)** öğesini seçin ve ardından **Oluştur**' u seçin. 

    ![Azure Veri Gezgini veri kaynağını seçin](media/redash/select-adx-data-source.png)

1. **Azure Veri Gezgini (kusto)** penceresinde, aşağıdaki formu doldurun ve **Oluştur**' u seçin.

    ![Azure Veri Gezgini (kusto) ayarları penceresi](media/redash/adx-settings-window.png)

1. **Ayarlar** penceresinde, **Azure Veri Gezgini (kusto)** veri kaynağı bağlantınızı test etmek Için **Kaydet** ve **Bağlantıyı Sına** ' yı seçin.

## <a name="create-queries-in-redash"></a>Redash 'te sorgu oluşturma

1. Redash 'in sol tarafında > **sorgu** **Oluştur** ' u seçin. **Yeni sorgu** ' ya tıklayın ve sorguyu yeniden adlandırın.

    ![Sorgu oluştur](media/redash/create-query.png)

1. Sorgunuzu üst düzen bölmesine yazın ve **Kaydet** ve **Yürüt**' ü seçin. Daha sonra kullanmak üzere sorguyu yayımlamak için **Yayımla** ' yı seçin.

    ![Sorguyu Kaydet ve Yürüt](media/redash/save-and-execute-query.png)

    Sol bölmede, açılan menüden veri kaynağı bağlantı adı 'nı (akışımızda**GitHub Bağlayıcısı** ) ve seçili veritabanındaki tabloları görebilirsiniz. 

1. Sorgu sonuçlarını alt Ortadaki bölmede görüntüleyin. **Yeni görselleştirme** düğmesini seçerek sorgu ile birlikte çalışmak için bir görselleştirme oluşturun.

    ![Yeni görselleştirme](media/redash/new-visualization.png)

1. Görselleştirme ekranında, **görselleştirme türünü** ve **X sütunu** ve **Y sütunu**gibi ilgili alanları seçin. Görselleştirmeyi **kaydedin** .

    ![Görselleştirme yapılandırma ve kaydetme](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Parametre kullanarak sorgu oluşturma

1. Yeni bir sorgu oluşturmak için > **sorgusu** **oluşturun** . {{}} küme ayraçları kullanarak buna bir parametre ekleyin. **Parametre Ekle** penceresini açmak için **{{}}** öğesini seçin. Ayrıca, mevcut bir parametrenin özniteliklerini değiştirmek ve **< parameter_name >** penceresini açmak için *Ayarlar simgesini* de seçebilirsiniz. 

    ![parametre Ekle](media/redash/insert-parameter.png)

1. Parametrinizi adlandırın. **Tür**seçin: açılan menüden **sorgu tabanlı açılan liste** . **Tamam**’ı seçin

    ![Sorgu tabanlı açılan liste](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > Sorgu birden çok değer kullanıyor, bu nedenle `| where Type in ((split('{{Type}}', ',')))`aşağıdaki sözdizimini eklemeniz gerekir. Daha fazla bilgi için bkz. [işleci](/azure/kusto/query/inoperator). Bu, [redash uygulamasında birden çok sorgu parametresi seçeneklerine](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io) neden olur

## <a name="create-a-dashboard-in-redash"></a>Yeniden Dash içinde pano oluşturma

1. Panonuzu oluşturmak için > **Pano** **oluşturun** . Alternatif olarak, var olan panoyu, **panoları** seçin > listeden bir pano seçin.

    ![Pano Oluştur](media/redash/create-dashboard.png)

1. **Yeni Pano** penceresinde panonuzu adlandırın ve **Kaydet**' i seçin. Yeni pencere öğesi oluşturmak için **< Dashboard_name >** penceresinde pencere **öğesi Ekle** ' yi seçin. 

1. **Pencere öğesi Ekle** penceresinde sorgu adı ' nı, **görselleştirme**ve **Parametreler**' i seçin. **Panoya Ekle** ' yi seçin

   ![Görselleştirmeler seçin ve panoya ekleyin](media/redash/add-widget-window.png)

1. Pano oluşturmayı tamamladıktan sonra **Düzenle** ' yi seçin.

1.  Pano düzenleme modunda, daha önce tanımlanan **tür** parametresini kullanmak Için **Pano düzeyi filtrelerini kullan** ' ı seçin.

    ![Pano oluşturmayı tamamen doldurun](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Gezgini için sorgu yazma](write-queries.md)


