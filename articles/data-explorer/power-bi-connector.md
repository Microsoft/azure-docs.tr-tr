---
title: Power BI için Azure Veri Gezgini bağlayıcısını kullanarak verileri görselleştirin
description: 'Bu makalede, Power BI verileri görselleştirmeye yönelik üç seçenekten birini nasıl kullanacağınızı öğrenirsiniz: Azure Veri Gezgini için Power BI Bağlayıcısı.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 361ea6ed76207e8e9721f64df61738b6cd9631dc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024223"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Power BI için Azure Veri Gezgini bağlayıcısını kullanarak verileri görselleştirin

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. Power BI, verilerinizi görselleştirmenizi ve sonuçları kuruluşunuzda paylaşmanızı sağlayan bir iş analizi çözümüdür. Azure Veri Gezgini, Power BI verilere bağlanmak için üç seçenek sunar: yerleşik bağlayıcıyı kullanın, Azure Veri Gezgini bir sorgu içeri aktarın veya bir SQL sorgusu kullanın. Bu makalede, yerleşik bağlayıcının verileri almak ve Power BI bir raporda görselleştirmek için nasıl kullanılacağı gösterilmektedir. Power BI panoları oluşturmak için Azure Veri Gezgini Native bağlayıcısının kullanılması basittir. Power BI Bağlayıcısı, [içeri ve doğrudan sorgu bağlantı modlarını](https://docs.microsoft.com/power-bi/desktop-directquery-about)destekler. Senaryo, ölçek ve performans gereksinimlerine bağlı olarak, **içeri** veya **DirectQuery** modunu kullanarak panolar oluşturabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdakiler gerekir:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* Azure Active Directory 'nin üyesi olan bir kurumsal e-posta hesabı, [azure Veri Gezgini yardım kümesine](https://dataexplorer.azure.com/clusters/help/databases/samples)bağlanabilirsiniz.
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) ( **indirmeyi ücretsiz**Seç)

## <a name="get-data-from-azure-data-explorer"></a>Azure Veri Gezgini veri al

İlk olarak, Azure Veri Gezgini yardım kümesine bağlanırsınız, ardından *Stormevents* tablosundan verilerin bir alt kümesini getirin. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Power BI Desktop, **giriş** sekmesinde **veri al** ' ı **seçin.**

    ![Verileri alma](media/power-bi-connector/get-data-more.png)

1. *Azure Veri Gezgini*araması yapın, **Azure Veri Gezgini** ' i seçin ve sonra **bağlantısını**yapın.

    ![Arama ve veri alma](media/power-bi-connector/search-get-data.png)

1. **Azure Veri Gezgini (kusto)** ekranında, aşağıdaki bilgilerle formu doldurun.

    ![Küme, veritabanı, tablo seçenekleri](media/power-bi-connector/cluster-database-table.png)

    **Ayar** | **Değer** | **Alan açıklaması**
    |---|---|---|
    | Küme | *https://help.kusto.windows.net* | Yardım kümesinin URL 'SI. Diğer kümeler için URL, *https://\<ClusterName\>.\<bölgesi\>. kusto.Windows.net*biçimindedir. |
    | Veritabanı | Boş bırakın | Bağlanmakta olduğunuz kümede barındırılan bir veritabanı. Daha sonraki bir adımda bunu seçeceğiz. |
    | Tablo adı | Boş bırakın | Veritabanındaki tablolardan biri veya <code>StormEvents \| take 1000</code>gibi bir sorgu. Daha sonraki bir adımda bunu seçeceğiz. |
    | Gelişmiş Seçenekler | Boş bırakın | Sorgunuz için sonuç kümesi boyutu gibi seçenekler. |
    | Veri bağlantısı modu | *DirectQuery* | Power BI verileri içeri aktarmalarını veya doğrudan veri kaynağına bağlanıp bağlanmadığını belirler. Bu bağlayıcı ile her iki seçeneği de kullanabilirsiniz. |
    | | | |
    
    > [!NOTE]
    > **Içeri aktarma** modunda, veriler Power BI taşınır. **DirectQuery** modunda, veriler doğrudan Azure Veri Gezgini kümenizdeki sorgulanır.
    >
    > **Içeri aktarma** modunu şu durumlarda kullanın:
    > * Veri kümesi küçüktür.
    > * Neredeyse gerçek zamanlı verilere ihtiyacınız yoktur. 
    > * Verileriniz zaten toplanmış veya [kusto içinde toplamayı](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions) gerçekleştirirsiniz    
    >
    > Şu durumlarda **DirectQuery** modunu kullan:
    > * Veri kümesi çok büyük. 
    > * Neredeyse gerçek zamanlı verilere ihtiyacınız vardır.   

1. Zaten yardım kümesiyle bağlantınız yoksa oturum açın. Bir kuruluş hesabıyla oturum açın ve sonra **Bağlan**' ı seçin.

    ![Oturum aç](media/power-bi-connector/sign-in.png)

1. **Gezgin** ekranında, **örnekler** veritabanını genişletin, **stormevents** ' i seçin ve **Düzenle**' yi seçin.

    ![Tablo Seç](media/power-bi-connector/select-table.png)

    Tablo, Power Query Düzenleyicisi'nde açılır ve burada verileri içeri aktarmadan önce satırları ve sütunları düzenleyebilirsiniz.

1. Power Query düzenleyicide, **DamageCrops** sütununun yanındaki oku seçin ve ardından **azalan şekilde sıralayın**.

    ![Azalan sıralama DamageCrops](media/power-bi-connector/sort-descending.png)

1. **Giriş** sekmesinde, **satırları tut** ' u ve **en üstteki satırları sakla**' yı seçin. Sıralanan tablonun ilk 1000 satırını getirmek için *1000* değerini girin.

    ![En üstteki satırları tut](media/power-bi-connector/keep-top-rows.png)

1. **Giriş** sekmesinde **Kapat & Uygula**' yı seçin.

    ![Kapat ve uygula](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Rapordaki verileri görselleştirme

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makale için oluşturduğunuz rapora artık ihtiyacınız yoksa Power BI Desktop (. pbix) dosyasını silin.

## <a name="next-steps"></a>Sonraki adımlar

[Verileri sorgulamak için Power BI için Azure Veri Gezgini bağlayıcısını kullanmaya yönelik ipuçları](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
