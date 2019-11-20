---
title: Blob depolamadan SQL veritabanına veri kopyalama-Azure
description: Bu öğreticide, blob depolamadan SQL veritabanına veri kopyalamak için bir Azure Data Factory işlem hattındaki kopyalama etkinliğinin nasıl kullanılacağı gösterilmektedir.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5a254979e345ae07bef5c8e79006bd0aaa0bf7df
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682809"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Öğretici: Data Factory kullanarak blob depolamadan SQL veritabanına veri kopyalama
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-dot-net.md). 

Bu öğreticide, blob depolamadan SQL veritabanına veri kopyalamak için bir işlem hattı ile veri fabrikası oluşturacaksınız.

Kopyalama Etkinliği, Azure Data Factory’de veri hareketini gerçekleştirir. Bu etkinlik, çeşitli veri depolama alanları arasında güvenli, güvenilir ve ölçeklenebilir bir yolla veri kopyalayabilen genel olarak kullanılabilir bir hizmet tarafından desteklenir. Kopyalama etkinliği hakkında ayrıntılı bilgi için [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md) makalesine bakın.  

> [!NOTE]
> Data Factory hizmetine ayrıntılı bir genel bakış için, bkz. [Azure Data Factory makalesine giriş](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Öğretici için Önkoşullar
Bu öğreticiye başlamadan önce aşağıdaki önkoşullara sahip olmanız gerekir:

* **Azure aboneliği**.  Bir aboneliğiniz yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılar için [ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/) makalesine bakın.
* **Azure depolama hesabı**. Bu öğreticide BLOB depolama alanını **kaynak** veri deposu olarak kullanırsınız. Azure depolama hesabınız yoksa, oluşturma adımları için [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md) makalesine bakın.
* **Azure SQL Veritabanı**. Bu öğreticide bir Azure SQL veritabanını **hedef** veri deposu olarak kullanırsınız. Öğreticide kullanabileceğiniz bir Azure SQL veritabanınız yoksa, bir [Azure SQL veritabanı oluşturma ve yapılandırma hakkında](../../sql-database/sql-database-get-started.md) bilgi almak için bkz.
* **SQL Server 2012/2014 veya Visual Studio 2013**. Örnek veritabanı oluşturmak ve sonuç verilerini veritabanında görüntülemek için SQL Server Management Studio veya Visual Studio 'Yu kullanırsınız.  

## <a name="collect-blob-storage-account-name-and-key"></a>BLOB depolama hesabı adını ve anahtarını topla
Bu öğreticiyi yapmak için Azure depolama hesabınızın hesap adı ve hesap anahtarı gereklidir. Azure depolama hesabınız için **Hesap adı** ve **hesap anahtarı** ' nı aklınızda edin.

1. [Azure Portal](https://portal.azure.com/)’da oturum açın.
2. Sol menüdeki **tüm hizmetler** ' e tıklayın ve **depolama hesapları**' nı seçin.

    ![Depolama hesaplarına gözatamazsınız](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. **Depolama hesapları** dikey penceresinde, bu öğreticide kullanmak istediğiniz **Azure Depolama hesabını** seçin.
4. **Ayarlar**altında **erişim tuşları** bağlantısı ' nı seçin.
5. **Depolama hesabı adı** metin kutusu ' nun yanındaki **Kopyala** (görüntü) düğmesine tıklayın ve dosyayı bir yere kaydedin/yapıştırın (örneğin: bir metin dosyası).
6. **KEY1**' i kopyalamak veya bir yere dönmek için önceki adımı tekrarlayın.

    ![Depolama erişim anahtarı](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. **X**simgesini tıklayarak tüm dikey pencereleri kapatın.

## <a name="collect-sql-server-database-user-names"></a>SQL Server, veritabanı, Kullanıcı adlarını toplayın
Bu öğreticiyi yapmak için Azure SQL Server, veritabanı ve kullanıcı adlarına ihtiyacınız vardır. Azure SQL veritabanınız için **sunucu**, **veritabanı**ve **kullanıcının** adlarını aklınızda edin.

1. **Azure Portal**, sol taraftaki **tüm hizmetler** ' e tıklayın ve **SQL veritabanları**' nı seçin.
2. **SQL veritabanları dikey**penceresinde, bu öğreticide kullanmak istediğiniz **veritabanını** seçin. **Veritabanı adını**aklınızda edin.  
3. **SQL veritabanı** dikey penceresinde **Ayarlar**' ın altında **Özellikler** ' e tıklayın.
4. **Sunucu adı** ve **Sunucu Yöneticisi oturum açma**değerlerini aklınızda edin.
5. **X**simgesini tıklayarak tüm dikey pencereleri kapatın.

## <a name="allow-azure-services-to-access-sql-server"></a>Azure hizmetlerinin SQL Server 'a erişmesine izin ver
Azure SQL Server için **Azure hizmetlerine erişime Izin ver** ayarının, Data Factory HIZMETININ Azure SQL sunucunuza **erişebilmesi için açık** olduğundan emin olun. Bu ayarı doğrulamak ve etkinleştirmek için aşağıdaki adımları uygulayın:

1. Sol taraftaki **tüm hizmetler** hub 'ına tıklayın ve **SQL sunucuları**' na tıklayın.
2. Sunucunuzu seçin ve **AYARLAR** altındaki **Güvenlik Duvarı**’na tıklayın.
3. **Güvenlik Duvarı ayarları** dikey penceresinde, **Azure hizmetlerine erişime izin ver** için **AÇIK**’a tıklayın.
4. **X**simgesini tıklayarak tüm dikey pencereleri kapatın.

## <a name="prepare-blob-storage-and-sql-database"></a>Blob depolamayı ve SQL veritabanını hazırlama
Şimdi aşağıdaki adımları gerçekleştirerek Azure Blob depolama alanınızı ve Azure SQL veritabanınızı öğreticiye hazırlayın:  

1. Not Defteri'ni başlatın. Aşağıdaki metni kopyalayın ve sabit sürücünüzdeki **C:\adfgetstarted** klasörüne **ılation. txt** olarak kaydedin.

    ```
    John, Doe
    Jane, Doe
    ```
2. [Azure Storage Gezgini](https://storageexplorer.com/) gibi araçları **adftutorial** kapsayıcısı oluşturmak ve **emp.txt** dosyasını kapsayıcıya yüklemek için kullanın.

3. Azure SQL Veritabanı’nınizde **emp** tablosu oluşturmak için aşağıdaki SQL betiğini kullanın.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Bilgisayarınızda SQL Server 2012/2014 yüklüyse:** Azure SQL sunucunuza bağlanmak ve SQL betiğini çalıştırmak için [SQL Server Management STUDIO kullanarak Azure SQL veritabanı 'nı yönetme](../../sql-database/sql-database-manage-azure-ssms.md) yönergelerini izleyin. 

    İstemcinizin Azure SQL sunucusuna erişim izni yoksa, makinenizden (IP adresi) erişim izni vermek için Azure SQL sunucunuzun güvenlik duvarını yapılandırmanız gerekir. Azure SQL sunucunuzun güvenlik duvarını yapılandırmaya yönelik adımlar için [bu makaleye](../../sql-database/sql-database-configure-firewall-settings.md) bakın.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
Önkoşulları tamamladınız. Aşağıdaki yollarla bir veri fabrikası oluşturabilirsiniz. Öğreticiyi uygulamak için üstteki aşağı açılan listedeki seçeneklerden birini veya aşağıdaki bağlantıları tıklatın.     

* [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Bu öğreticideki veri işlem hattı, bir kaynak veri deposundaki verileri hedef veri deposuna kopyalar. Çıkış verileri üretmek için giriş verilerini dönüştürmez. Azure Data Factory kullanarak verileri dönüştürme hakkında bir öğretici için bkz. [Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için ilk işlem hattınızı oluşturma](data-factory-build-your-first-pipeline.md).
> 
> Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliği diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Ayrıntılı bilgi için bkz. [Data Factory’de zamanlama ve yürütme](data-factory-scheduling-and-execution.md). 
