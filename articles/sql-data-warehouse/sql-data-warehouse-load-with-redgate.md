---
title: "Redgate kullanarak verileri Azure veri deposuna yükleme | Microsoft Docs"
description: "Redgate Data Platform Studio’nun veri ambarı senaryoları için nasıl kullanılacağı hakkında bilgi edinin."
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: cb0b5489ccfabb0e2a4ee412162a18930073b309
ms.lasthandoff: 01/24/2017



---
# <a name="load-data-with-redgate-data-platform-studio"></a>Redgate Data Platform Studio ile veri yükleme
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

Bu öğreticide, şirket içi SQL Server’dan Azure SQL Veri Ambarı’na veri taşımak için [Redgate Data Platform Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/)’yu (DPS) kullanma işlemi gösterilmektedir. Data Platform Studio en uygun uyumluluk düzeltmelerini ve iyileştirmelerini uyguladığı için, SQL Veri Ambarı’nı kullanmaya başlamanın en hızlı yoludur.

> [!NOTE]
> [Redgate](http://www.red-gate.com) çeşitli SQL Server araçları sunan uzun süreli bir Microsoft ortağıdır. Data Platform Studio’daki bu özellik hem ticari hem de ticari olmayan kullanımlar için ücretsiz olarak sunulmuştur.
> 
> 

## <a name="before-you-begin"></a>Başlamadan önce
### <a name="create-or-identify-resources"></a>Kaynak oluşturma veya tanımlama
Bu öğreticiye başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Şirket içi SQL Server Veritabanı**: SQL Veri Ambarı’na aktarmak istediğiniz verilerin şirket içi SQL Server’dan (sürüm 2008R2 veya üstü) gelmesi gerekir. Data Platform Studio, verileri bir Azure SQL Veritabanından veya metin dosyalarından doğrudan aktaramaz.
* **Azure Storage Hesabı**: Data Platform Studio, verileri SQL Veri Ambarı’na yüklemeden önce Azure Blob Depolama içinde hazırlar. Depolama hesabı “Klasik” dağıtım modeli yerine “Resource Manager” dağıtım modeli (varsayılan) kullanmalıdır. Bir depolama hesabınız Depolama hesabı oluşturma hakkında bilgi edinin. 
* **SQL Veri Ambarı**: Bu öğretici, verileri şirket içi SQL Server’dan SQL Veri Ambarı’na taşır, bu nedenle çevrimiçi bir veri ambarına sahip olmanız gerekir. Veri ambarınız yoksa Azure SQL Veri Ambarı oluşturma hakkında bilgi edinin.

> [!NOTE]
> Depolama hesabı ve veri ambarı aynı bölge oluşturulursa performans geliştirilir.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>1. Adım: Azure hesabınızla Data Platform Studio’da oturum açın
Web tarayıcınızı açın ve [Data Platform Studio](https://www.dataplatformstudio.com/) web sitesine gidin. Depolama hesabını ve veri ambarını oluşturmak için kullandığını Azure hesabıyla oturum açın. E-posta adresiniz bir iş veya okul hesabı ve bir Microsoft hesabıyla ilişkiliyse, kaynaklarınıza erişimi olan hesabı seçtiğinizden emin olun.

> [!NOTE]
> Data Platform Studio’yu ilk kez kullanıyorsanız, Azure kaynaklarınızı yönetmesi için uygulamaya izin vermeniz istenir.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>2. Adım: Alma Sihirbazı’nı başlatın
Alma sihirbazını başlatmak için DPS ana ekranında Azure SQL Veri Ambarı’na Al bağlantısını seçin.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>3. Adım: Data Platform Studio Ağ Geçidini yükleyin
Şirket içi SQL Server veritabanınıza bağlanmak için DPS Ağ Geçidi’ni yüklemeniz gerekir. Ağ geçidi, şirket içi ortamınıza erişim sağlayan, verileri ayıklayan ve depolama hesabınıza yükleyen bir istemci aracısıdır. Verileriniz hiçbir zaman Redgate sunucularından geçmez. Ağ Geçidi’ni yüklemek için:

1. **Ağ Geçidi Oluştur** bağlantısına tıklayın
2. Sağlanan yükleyiciyi kullanarak Ağ Geçidi’ni indirip yükleyin

![][2]

> [!NOTE]
> Ağ Geçidi, kaynak SQL Server veritabanına ağ erişimi olan herhangi bir makineye yüklenebilir. SQL Server veritabanına, geçerli kullanıcının kimlik bilgileriyle Windows kimlik doğrulaması kullanarak erişir.
> 
> 

Yüklendikten sonra, Ağ Geçidi durumu Bağlı olarak değişir ve İleri’yi seçebilirsiniz.

## <a name="step-4-identify-the-source-database"></a>4. Adım: Kaynak veritabanınızı tanımlayın
*Sunucu Adı Girin* metin kutusuna veritabanınızı barındıran sunucunun adını girin ve **İleri**’yi seçin. Ardından, açılır menüden verileri almak istediğiniz veritabanını seçin.

![][3]

DPS, seçili veritabanında içeri aktarılacak tabloları inceler. DPS varsayılan olarak veritabanındaki tüm tabloları içeri aktarır. Tüm Tablolar bağlantısını genişleterek tabloları seçebilir veya seçimini kaldırabilirsiniz. İlerlemek için İleri düğmesini seçin.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>5. Adım: Verileri hazırlamak için bir depolama hesabı seçin
DPS, verilerin hazırlanacağı bir konum seçmenizi ister. Aboneliğinizde var olan bir depolama hesabı seçin ve **İleri**’yi seçin.

> [!NOTE]
> DPS, seçilen depolama hesabında yeni bir blob kapsayıcısı oluşturur ve her içeri aktarma için ayrı bir klasör kullanır.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>6. Adım: Veri ambarı seçin
Ardından, verileri içine aktarmak için çevrimiçi bir [Azure SQL Veri Ambarı](http://aka.ms/sqldw) veritabanı seçin. Veritabanınızı seçtikten sonra veritabanına bağlanmak için kimlik bilgilerini girip **İleri**’yi seçmeniz gerekir.

![][5]

> [!NOTE]
> DPS, kaynak veri tablolarını veri ambarında birleştirir. Tablo adının veri ambarında var olan tabloların üzerine yazılması gerekirse DPS sizi uyarır. İsterseniz, İçeri aktarmadan önce var olan tüm nesneleri silin seçeneğini işaretleyerek veri ambarında var olan tüm nesneleri silebilirsiniz.
> 
> 

## <a name="step-7-import-the-data"></a>7. Adım: Verileri içeri aktarın
DPS, verileri içeri aktarmak istediğinizi onaylar. Verileri içeri aktarmaya başlamak için İçeri aktarmayı başlat düğmesine tıklamanız yeterlidir.

![][6]

DPS, verileri şirket içi SQL Server’dan ayıklayıp karşıya yükleme ve SQL Veri Ambarı’na aktarma işlemlerinin ilerleme durumunu gösteren bir görsel gösterir.

![][7]

İçeri aktarma tamamlandıktan sonra DPS, verileri içeri aktarma işleminin bir özetini ve gerçekleştirilen uyumluluk düzeltmelerini içeren bir değişim raporunu gösterir.

![][8]

## <a name="next-steps"></a>Sonraki adımlar
SQL Veri Ambarı’ndaki verilerinizi araştırmak için aşağıdakileri görüntüleyin:

* [Azure SQL Veri Ambarı'nı (Visual Studio) Sorgulama][Query Azure SQL Data Warehouse (Visual Studio)]
* [Power BI ile verileri görselleştirme][Visualize data with Power BI]

Redgate Data Platform Studio hakkında daha fazla bilgi almak için:

* [DPS giriş sayfasını ziyaret edin](http://www.dataplatformstudio.com/)
* [DPS Channel9 gösterimini izleyin](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Verilerinizi SQL Veri Ambarı’na geçirme ve yüklemenin diğer yollarına genel bakış için bkz.:

* [Çözümünüzü SQL Veri Ambarı'na taşıma][Migrate your solution to SQL Data Warehouse]
* [Azure SQL Veri Ambarı’na veri yükleme](sql-data-warehouse-overview-load.md)

Geliştirme ile ilgili daha fazla ipucu için bkz. [SQL Veri Ambarı geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

