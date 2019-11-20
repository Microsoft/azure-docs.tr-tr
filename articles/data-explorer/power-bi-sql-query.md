---
title: Power BI bir SQL sorgusu kullanarak Azure Veri Gezgini verileri görselleştirme
description: 'Bu makalede, Power BI verileri görselleştirmeye yönelik üç seçenekten birini nasıl kullanacağınızı öğrenirsiniz: bir Azure Veri Gezgini kümesine yönelik bir SQL sorgusu.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: e4e7858a54f3002a511269a2519135d5ac24ed68
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024077"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Power BI bir SQL sorgusu kullanarak Azure Veri Gezgini verileri görselleştirme

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. Power BI, verilerinizi görselleştirmenizi ve sonuçları kuruluşunuzda paylaşmanızı sağlayan bir iş analizi çözümüdür.

Azure Veri Gezgini, Power BI verilere bağlanmak için üç seçenek sunar: yerleşik bağlayıcıyı kullanın, Azure Veri Gezgini bir sorgu içeri aktarın veya bir SQL sorgusu kullanın. Bu makalede, bir SQL sorgusunun verileri almak ve bir Power BI raporunda görselleştirmek için nasıl kullanılacağı gösterilmektedir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdakiler gerekir:

* Azure Active Directory 'nin üyesi olan bir kurumsal e-posta hesabı, [azure Veri Gezgini yardım kümesine](https://dataexplorer.azure.com/clusters/help/databases/samples)bağlanabilirsiniz.

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) ( **indirmeyi ücretsiz**Seç)

## <a name="get-data-from-azure-data-explorer"></a>Azure Veri Gezgini veri al

İlk olarak, Azure Veri Gezgini yardım kümesine bağlanırsınız, ardından *Stormevents* tablosundan verilerin bir alt kümesini getirin. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Genellikle yerel sorgu dilini Azure Veri Gezgini ile kullanırsınız, ancak burada kullanacağınız SQL sorgularını de destekler. Azure Veri Gezgini, SQL sorgusunu sizin için yerel bir sorguya çevirir.

1. Power BI Desktop, **giriş** sekmesinde **veri al** ' ı **seçin.**

    ![Verileri alma](media/power-bi-sql-query/get-data-more.png)

1. *Azure SQL veritabanı*'nda arama yapın, **Azure SQL veritabanı** ' nı seçin ve ardından **bağlanın**.

    ![Arama ve veri alma](media/power-bi-sql-query/search-get-data.png)

1. **SQL Server veritabanı** ekranında, aşağıdaki bilgilerle formu doldurun.

    ![Veritabanı, tablo, sorgu seçenekleri](media/power-bi-sql-query/database-table-query.png)

    **Ayar** | **Değer** | **Alan açıklaması**
    |---|---|---|
    | Sunucu | *help.kusto.windows.net* | Yardım kümesinin URL 'SI ( *https://* olmadan). Diğer kümeler için, URL *\<ClusterName\>.\<Region\>. kusto.Windows.net*biçiminde olur. |
    | Veritabanı | *Örnekler* | Bağlanmakta olduğunuz kümede barındırılan örnek veritabanı. |
    | Veri bağlantısı modu | *İçeri Aktarma* | Power BI verileri içeri aktarmalarını veya doğrudan veri kaynağına bağlanıp bağlanmadığını belirler. Bu bağlayıcı ile her iki seçeneği de kullanabilirsiniz. |
    | Komut zaman aşımı | Boş bırakın | Sorgunun zaman aşımı hatası vermeden önce ne kadar süre çalıştığı. |
    | SQL ekstresi | Sorguyu bu tablonun altına Kopyala | Azure Veri Gezgini 'in yerel bir sorguya çeviren SQL deyimidir. |
    | Diğer seçenekler | Varsayılan değerler olarak bırak | Seçenekler Azure Veri Gezgini kümelerine uygulanmaz. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Zaten yardım kümesiyle bağlantınız yoksa oturum açın. Bir Microsoft hesabı oturum açın ve sonra **Bağlan**' ı seçin.

    ![Oturum aç](media/power-bi-sql-query/sign-in.png)

1. **Help.kusto.Windows.net: Samples** ekranında **Yükle**' yi seçin.

    ![Veri yükleme](media/power-bi-sql-query/load-data.png)

    Tablo, örnek verileri temel alan raporlar oluşturabileceğiniz rapor görünümündeki ana Power BI penceresinde açılır.

## <a name="visualize-data-in-a-report"></a>Rapordaki verileri görselleştirme

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makale için oluşturduğunuz rapora artık ihtiyacınız yoksa Power BI Desktop (. pbix) dosyasını silin.

## <a name="next-steps"></a>Sonraki adımlar

[Power BI için Azure Veri Gezgini bağlayıcısını kullanarak verileri görselleştirin](power-bi-connector.md)