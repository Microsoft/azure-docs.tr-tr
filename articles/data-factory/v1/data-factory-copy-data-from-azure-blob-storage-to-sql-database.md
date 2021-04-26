---
title: Blob depolamadan SQL veritabanına veri kopyalama-Azure
description: Bu öğreticide, blob depolamadan SQL veritabanına veri kopyalamak için bir Azure Data Factory işlem hattındaki kopyalama etkinliğinin nasıl kullanılacağı gösterilmektedir.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 24cedc6a1e0be66e9a924a50e25257f18b7f96a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376896"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Öğretici: Data Factory kullanarak blob depolamadan SQL veritabanına veri kopyalama
> [!div class="op_single_selector"]
> * [Genel bakış ve ön koşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
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
* **Azure depolama hesabı**. Bu öğreticide BLOB depolama alanını **kaynak** veri deposu olarak kullanırsınız. bir Azure depolama hesabınız yoksa, oluşturma adımları için [depolama hesabı oluşturma](../../storage/common/storage-account-create.md) makalesine bakın.
* **Azure SQL veritabanı**. Bu öğreticide Azure SQL veritabanını **hedef** veri deposu olarak kullanırsınız. Azure SQL veritabanı 'nda öğreticide kullanabileceğiniz bir veritabanınız yoksa bir veritabanı oluşturmak için bkz. [Azure SQL veritabanı 'nda veritabanı oluşturma ve yapılandırma](../../azure-sql/database/single-database-create-quickstart.md) .
* **SQL Server 2012/2014 veya Visual Studio 2013**. Örnek veritabanı oluşturmak ve sonuç verilerini veritabanında görüntülemek için SQL Server Management Studio veya Visual Studio 'Yu kullanırsınız.  

## <a name="collect-blob-storage-account-name-and-key"></a>BLOB depolama hesabı adını ve anahtarını topla
Bu öğreticiyi yapmak için Azure depolama hesabınızın hesap adı ve hesap anahtarı gereklidir. Azure depolama hesabınız için **Hesap adı** ve **hesap anahtarı** ' nı aklınızda edin.

1. [Azure Portal](https://portal.azure.com/)oturum açın.
2. Sol menüdeki **tüm hizmetler** ' e tıklayın ve **depolama hesapları**' nı seçin.

    ![Depolama hesaplarına gözatamazsınız](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. **Depolama hesapları** dikey penceresinde, bu öğreticide kullanmak istediğiniz **Azure Depolama hesabını** seçin.
4. **Ayarlar** altında **erişim tuşları** bağlantısı ' nı seçin.
5. **Depolama hesabı adı** metin kutusu ' nun yanındaki **Kopyala** (görüntü) düğmesine tıklayın ve dosyayı bir yere kaydedin/yapıştırın (örneğin: bir metin dosyası).
6. **KEY1**' i kopyalamak veya bir yere dönmek için önceki adımı tekrarlayın.

    ![Depolama erişim anahtarı](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. **X** simgesini tıklayarak tüm dikey pencereleri kapatın.

## <a name="collect-sql-server-database-user-names"></a>SQL Server, veritabanı, Kullanıcı adlarını toplayın
Bu öğreticiyi yapmak için mantıksal SQL Server, veritabanı ve kullanıcı adlarına ihtiyacınız vardır. **Sunucu**, **VERITABANı** ve Azure SQL veritabanı için **Kullanıcı** adlarını aklınızda edin.

1. **Azure Portal**, sol taraftaki **tüm hizmetler** ' e tıklayın ve **SQL veritabanları**' nı seçin.
2. **SQL veritabanları dikey** penceresinde, bu öğreticide kullanmak istediğiniz **veritabanını** seçin. **Veritabanı adını** aklınızda edin.  
3. **SQL veritabanı** dikey penceresinde **Ayarlar**' ın altında **Özellikler** ' e tıklayın.
4. **Sunucu adı** ve **Sunucu Yöneticisi oturum açma** değerlerini aklınızda edin.
5. **X** simgesini tıklayarak tüm dikey pencereleri kapatın.

## <a name="allow-azure-services-to-access-sql-server"></a>Azure hizmetlerinin SQL Server 'a erişmesine izin ver
Data Factory hizmetinin sunucunuza erişebilmesi için sunucunuz için **Azure hizmetlerine erişime Izin ver** **ayarının açık olduğundan** emin olun. Bu ayarı doğrulamak ve etkinleştirmek için aşağıdaki adımları uygulayın:

1. Sol taraftaki **tüm hizmetler** hub 'ına tıklayın ve **SQL sunucuları**' na tıklayın.
2. Sunucunuzu seçin ve **AYARLAR** altındaki **Güvenlik Duvarı**’na tıklayın.
3. **Güvenlik Duvarı ayarları** dikey penceresinde, **Azure hizmetlerine erişime izin ver** için **AÇIK**’a tıklayın.
4. **X** simgesini tıklayarak tüm dikey pencereleri kapatın.

## <a name="prepare-blob-storage-and-sql-database"></a>Blob depolamayı ve SQL veritabanını hazırlama
Şimdi aşağıdaki adımları gerçekleştirerek Azure Blob depolama alanınızı ve Azure SQL veritabanınızı öğreticiye hazırlayın:  

1. Not Defteri'ni başlatın. Aşağıdaki metni kopyalayın ve sabit sürücünüzdeki **C:\adfgetstarted** klasörüne **emp.txt** olarak kaydedin.

    ```
    John, Doe
    Jane, Doe
    ```
2. **Adföğreticisi** kapsayıcısını oluşturmak ve **emp.txt** dosyasını kapsayıcıya yüklemek için [Azure Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

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

    **Bilgisayarınızda SQL Server 2012/2014 yüklüyse:** sunucunuza bağlanmak ve SQL betiğini çalıştırmak için [SQL Server Management STUDIO kullanarak Azure SQL veritabanı 'nı yönetme](../../azure-sql/database/single-database-manage.md) yönergelerini izleyin.

    İstemcinizin mantıksal SQL Server 'a erişmesine izin verilmiyorsa, makinenizde (IP adresi) erişime izin vermek için sunucunuzun güvenlik duvarını yapılandırmanız gerekir. Sunucunuza yönelik güvenlik duvarını yapılandırma adımları için [Bu makaleye](../../azure-sql/database/firewall-configure.md) bakın.

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