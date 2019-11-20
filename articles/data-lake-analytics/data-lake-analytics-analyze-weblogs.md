---
title: Azure Data Lake Analytics'i kullanarak Web sitesi günlüklerini çözümleme
description: U-SQL işlevleri ve sorguları çalıştırmak için Azure Data Lake Analytics kullanarak Web sitesi günlüklerini çözümlemeyi öğrenin.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 04c6d4c74a82ccfbcbb0faecb0dca5ec495f6663
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672873"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Azure Data Lake Analytics'i kullanarak Web sitesi günlüklerini çözümleme
Özellikle de Web sitesini ziyaret etmeye çalıştıklarında hangi başvuranları hatalara çalıştığını bulmada Data Lake Analytics kullanarak Web sitesi günlüklerini çözümlemeyi öğrenin.

## <a name="prerequisites"></a>Önkoşullar
* **Visual Studio 2015 veya Visual Studio 2013**.
* **[Visual Studio için Data Lake Araçları](https://aka.ms/adltoolsvs)** .

    Visual Studio için Data Lake Araçları yüklendikten sonra Visual Studio 'daki **Araçlar** menüsünde bir **Data Lake** öğesi görürsünüz:

    ![U-SQL Visual Studio menüsü](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Data Lake Analytics temel bilgileri ve Visual Studio için Data Lake araçları**. Başlamak için bkz.:

  * [Visual Studio için Data Lake araçları 'nı kullanarak U-SQL betiği geliştirin](data-lake-analytics-data-lake-tools-get-started.md).
* **Data Lake Analytics hesabı.**  Bkz. [Azure Data Lake Analytics hesap oluşturma](data-lake-analytics-get-started-portal.md).
* **Örnek verileri yükler.** Azure portalında Data Lake Analytics hesabı açın ve Sol menüdeki **örnek betikler** ' e tıklayın ve ardından **örnek verileri Kopyala**' ya tıklayın. 

## <a name="connect-to-azure"></a>Azure'a Bağlanma
Herhangi bir U-SQL betiğini oluşturup test etmeden önce, önce Azure 'a bağlanmanız gerekir.

**Data Lake Analytics'e bağlanmak için**

1. Visual Studio'yu açın.
2. **Data Lake > seçenekler ve ayarlar**' a tıklayın.
3. **Oturum aç**' a tıklayın veya biri oturum açmışsa **kullanıcıyı değiştirin** ve yönergeleri izleyin.
4. Seçenekler ve ayarlar iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

**Data Lake Analytics hesaplarınıza gözatmaya yönelik**

1. Visual Studio 'da, **CTRL + ALT + S**tuşlarına basarak **Sunucu Gezgini** açın.
2. **Sunucu Gezgini**'nden, **Azure** seçeneğini ve ardından **Data Lake Analytics** seçeneğini genişletin. Varsa Data Lake Analytics hesaplarınızın listesini görürsünüz. Studio 'dan Data Lake Analytics Hesapları oluşturamazsınız. Hesap oluşturmak için bkz. [Azure Portal'ı kullanarak Azure Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-portal.md) veya [Azure PowerShell'i kullanarak Azure Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>U-SQL uygulaması geliştirme
U-SQL uygulaması, çoğunlukla bir U-SQL betiğtir. U-SQL hakkında daha fazla bilgi edinmek için, bkz. [u-SQL kullanmaya başlama](data-lake-analytics-u-sql-get-started.md).

Uygulamaya Kullanıcı tanımlı operatörler ekleme ekleyebilirsiniz.  Daha fazla bilgi için bkz. [Data Lake Analytics işleri Için U-SQL Kullanıcı tanımlı Işleçler geliştirme](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Data Lake Analytics işi oluşturma ve gönderme**

1. **> yeni > projesi dosyasına**tıklayın.
2. U-SQL proje türünü seçin.

    ![yeni U-SQL Visual Studio projesi](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. **Tamam**'ı tıklatın. Visual Studio, Script. usql dosyası ile bir çözüm oluşturur.
4. Script. usql dosyasına aşağıdaki betiği girin:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            DISTRIBUTED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    U-SQL ' y i anlamak için bkz. [Data Lake Analytics U-SQL dili ile çalışmaya başlama](data-lake-analytics-u-sql-get-started.md).    
5. Projenize yeni bir U-SQL betiği ekleyin ve aşağıdakileri girin:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. İlk U-SQL betiğine geri dönün ve **Gönder** düğmesinin yanındaki analiz hesabınızı belirtin.
7. **Çözüm Gezgini**'nden, **Script.usql** öğesine sağ tıklayın ve ardından **Betik Oluştur**'a tıklayın. Çıkış bölmesinde sonuçları doğrulayın.
8. **Çözüm Gezgini**'nden, **Script.usql** öğesine sağ tıklayın ve ardından **Betiği Gönder**'e tıklayın.
9. **Analiz hesabının** işi çalıştırmak istediğiniz yer olduğundan emin olun ve ardından **Gönder**' e tıklayın. Gönderim tamamlandığında, gönderme işleminin sonuçları ve iş bağlantısı Visual Studio için Data Lake Araçları içinde sunulur.
10. İş başarılı bir şekilde tamamlanana kadar bekleyin.  İş başarısız olursa büyük olasılıkla kaynak dosya eksik olabilir.  Lütfen Bu öğreticinin önkoşul bölümüne bakın. Ek sorun giderme bilgileri için bkz. [Azure Data Lake Analytics Işleri izleme ve sorun giderme](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    İş tamamlandığında, aşağıdaki ekranı görürsünüz:

    ![Data Lake Analytics Web günlüklerini Web sitesi günlüklerini çözümleme](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Şimdi **Script1. usql**için 7-10 adımlarını yineleyin.

**İş çıkışını görmek için**

1. **Sunucu Gezgini**'nden, **Azure** seçeneğini, **Data Lake Analytics** seçeneğini, Data Lake Analytics hesabınızı ve **Depolama Hesapları** seçeneğini genişletin, varsayılan Data Lake Store hesabına sağ tıklayın ve ardından **Explorer**'a tıklayın.
2. Klasörü açmak için **örnekler** ' e çift tıklayın ve ardından **Çıkış**' a çift tıklayın.
3. **Başarılı olmayan yanıtlar. log dosyasına**çift tıklayın.
4. Ayrıca, doğrudan çıkışa gitmek için işin grafik görünümündeki çıkış dosyasına çift tıklayabilirsiniz.

## <a name="see-also"></a>Ayrıca bkz.
Farklı araçlar kullanarak Data Lake Analytics ile çalışmaya başlamak için bkz.

* [Azure Portal'ı kullanarak Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell'i kullanarak Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-powershell.md)
* [.NET SDK'yı kullanarak Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-net-sdk.md)
