---
title: Şablonlara genel bakış
description: Azure Data Factory ile hızlı bir şekilde başlamak için önceden tanımlanmış bir şablon kullanmayı öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.author: weetok
author: dcstwh
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 511c8851a39d283bd69215216f5ec4a4e77653d1
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219816"
---
# <a name="templates"></a>Şablonlar

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Şablonlar, Data Factory hızlı bir şekilde başlamanıza olanak sağlayan işlem hatları Azure Data Factory önceden tanımlanmıştır. Şablonlar, Data Factory yeni olduğunuzda ve hızlıca çalışmaya başlamak istediğinizde faydalıdır. Bu şablonlar, veri tümleştirme projelerini oluşturmaya yönelik geliştirme süresini azaltarak geliştirici üretkenliğini geliştirir.

## <a name="create-data-factory-pipelines-from-templates"></a>Şablonlardan Data Factory işlem hatları oluşturma

Aşağıdaki iki yolla bir şablondan Data Factory işlem hattı oluşturmaya başlamanızı sağlayabilirsiniz:

1.  Şablon galerisini açmak için genel bakış sayfasında **şablondan işlem hattı oluştur ' u** seçin.

    ![Genel Bakış sayfasından şablon galerisini açın](media/solution-templates-introduction/templates-intro-image1.png)

1.  Kaynak Gezgini 'daki yazar sekmesinde, şablon **+** galerisini açmak için **şablondan işlem hattı** ' nı seçin.

    ![Yazar sekmesinden şablon galerisini açın](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Şablon Galerisi

![Şablon Galerisi](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Kullanıma hazır Data Factory şablonları

Data Factory, Data Factory işlem hattı şablonlarını kaydetmek için Azure Resource Manager şablonları kullanır. Tüm Kaynak Yöneticisi şablonlarını, [resmi Azure Data Factory GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/templates)deposunda kullanıma hazır Data Factory şablonları için kullanılan bildirim dosyası ile birlikte görebilirsiniz. Microsoft tarafından sunulan önceden tanımlanmış şablonlar, ancak aşağıdaki öğelerle sınırlı değildir:

-   Şablonları Kopyala:

    -   [Veritabanından toplu kopyalama](solution-template-bulk-copy-with-control-table.md)
    
    -   [LastModifiedDate parametresine göre yeni dosyaları kopyalama](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Dosya tabanlı mağazalar arasında birden çok dosya kapsayıcısı kopyalama](solution-template-copy-files-multiple-containers.md)

    -   [Dosyaları taşıma](solution-template-move-files.md)

    -   [Veritabanından Delta kopyası](solution-template-delta-copy-with-control-table.md)

    -   Öğesinden \<source\> öğesine Kopyala \<destination\>

        -   [Amazon S3 'ten Azure Data Lake Store Gen 2 ' ye kadar](solution-template-migration-s3-azure.md)

        -   Google Big sorgusundan Azure Data Lake Store Gen 2 ' ye kadar

        -   HDF 'den Azure Data Lake Store Gen 2 ' ye kadar

        -   Netezza 'den Azure Data Lake Store Gen 1 ' e kadar

        -   Şirket içinden Azure SQL veritabanı 'na SQL Server

        -   Şirket içinden Azure SYNAPSE Analytics 'e SQL Server

        -   Şirket içi Oracle 'dan Azure SYNAPSE Analytics 'e

-   SSIS şablonları

    -   SSIS paketlerini yürütmek için Azure-SSIS Integration Runtime zamanlama

-   Dönüştürme şablonları

    -   [Azure Databricks ile ETL](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Şablonlarım

İşlem hattını, işlem hattı sekmesinde **şablon olarak kaydet** ' i seçerek de bir şablon olarak kaydedebilirsiniz.

![İşlem hattını şablon olarak kaydetme](media/solution-templates-introduction/templates-intro-image4.png)

Şablon olarak kaydedilmiş işlem hatlarını şablon galerinin **Şablonlarım** bölümünde görüntüleyebilirsiniz. Ayrıca, Kaynak Gezgini **Şablonlar** bölümünde de görebilirsiniz.

![Şablonlarım](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Şablonlarım özelliğini kullanmak için GIT tümleştirmesini etkinleştirmeniz gerekir. Hem Azure DevOps GIT hem de GitHub desteklenir.
