---
title: Azure Data Factory şablonlarına genel bakış
description: Azure Data Factory ile hızlı bir şekilde başlamak için önceden tanımlanmış bir şablon kullanmayı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 7895fedd0f68ae1c8ea4808c54a18a4e73b57b2c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684051"
---
# <a name="templates"></a>Şablonlar

Şablonlar, Data Factory hızlı bir şekilde başlamanıza olanak sağlayan işlem hatları Azure Data Factory önceden tanımlanmıştır. Şablonlar, Data Factory yeni olduğunuzda ve hızlıca çalışmaya başlamak istediğinizde faydalıdır. Bu şablonlar, veri tümleştirme projelerini oluşturmaya yönelik geliştirme süresini azaltarak geliştirici üretkenliğini geliştirir.

## <a name="create-data-factory-pipelines-from-templates"></a>Şablonlardan Data Factory işlem hatları oluşturma

Aşağıdaki iki yolla bir şablondan Data Factory işlem hattı oluşturmaya başlamanızı sağlayabilirsiniz:

1.  Şablon galerisini açmak için genel bakış sayfasında **şablondan işlem hattı oluştur ' u** seçin.

    ![Genel Bakış sayfasından şablon galerisini açın](media/solution-templates-introduction/templates-intro-image1.png)

1.  Kaynak Gezgini 'daki yazar sekmesinde **+** , sonra şablondan işlem **hattı** ' nı seçerek şablon galerisini açın.

    ![Yazar sekmesinden şablon galerisini açın](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Şablon Galerisi

![Şablon Galerisi](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Kullanıma hazır Data Factory şablonları

Data Factory, Data Factory işlem hattı şablonlarını kaydetmek için Azure Resource Manager şablonları kullanır. Tüm Kaynak Yöneticisi şablonlarını, [resmi Azure Data Factory GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/templates)deposunda kullanıma hazır Data Factory şablonları için kullanılan bildirim dosyası ile birlikte görebilirsiniz. Microsoft tarafından sunulan önceden tanımlanmış şablonlar, ancak aşağıdaki öğelerle sınırlı değildir:

-   Şablonları Kopyala:

    -   [Veritabanından toplu kopyalama](solution-template-bulk-copy-with-control-table.md)
    
    -   [Yeni dosyaları LastModifiedDate göre Kopyala](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Dosya tabanlı mağazalar arasında birden çok dosya kapsayıcısı kopyalama](solution-template-copy-files-multiple-containers.md)

    -   [Dosyaları taşıma](solution-template-move-files.md)

    -   [Veritabanından Delta kopyası](solution-template-delta-copy-with-control-table.md)

    -   \<kaynak\> \<hedefe Kopyala\>

        -   [Amazon S3 'ten Azure Data Lake Store Gen 2 ' ye kadar](solution-template-migration-s3-azure.md)

        -   Google Big sorgusundan Azure Data Lake Store Gen 2 ' ye kadar

        -   HDF 'den Azure Data Lake Store Gen 2 ' ye kadar

        -   Netezza 'den Azure Data Lake Store Gen 1 ' e kadar

        -   Şirket içinden Azure SQL veritabanı 'na SQL Server

        -   Şirket içinden Azure SQL veri ambarı 'na SQL Server

        -   Şirket içi Oracle 'dan Azure SQL veri ambarı 'na

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
