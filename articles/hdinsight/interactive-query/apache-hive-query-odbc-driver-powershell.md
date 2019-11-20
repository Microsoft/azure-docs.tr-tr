---
title: ODBC sürücüsü ile sorgu Apache Hive & PowerShell-Azure HDInsight
description: Azure HDInsight 'ta Apache Hive kümelerini sorgulamak için Microsoft Hive ODBC sürücüsünü ve PowerShell 'i kullanın.
keywords: Hive, Hive ODBC, PowerShell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: f6139bb98fa0272e43c8e180d4ec029f7a7538bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494327"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Öğretici: ODBC ve PowerShell ile Apache Hive sorgulama

Microsoft ODBC sürücüleri, Apache Hive dahil olmak üzere farklı türlerde veri kaynaklarıyla etkileşimde bulunmak için esnek bir yol sağlar. Hive kümenize bir bağlantı açmak, seçtiğiniz bir sorguyu geçirmek ve sonuçları göstermek için ODBC sürücülerini kullanan PowerShell gibi komut dosyası dillerinde kod yazabilirsiniz.

Bu öğreticide aşağıdaki görevleri gerçekleştirirsiniz:

> [!div class="checklist"]
> * Microsoft Hive ODBC sürücüsünü indirme ve yükleme
> * Kümenize bağlı Apache Hive ODBC veri kaynağı oluşturma
> * PowerShell kullanarak kümenizdeki örnek bilgileri sorgulama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce aşağıdaki öğelere sahip olmanız gerekir:

* HDInsight üzerinde etkileşimli bir sorgu kümesi. Bir tane oluşturmak için bkz. [Azure HDInsight kullanmaya başlama](../hdinsight-hadoop-provision-linux-clusters.md). Küme türü olarak **etkileşimli sorgu** ' yı seçin.

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC sürücüsünü yükler

[Microsoft Hive ODBC sürücüsü](https://www.microsoft.com/download/details.aspx?id=40886)indirin ve yükleyin.

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC veri kaynağı oluşturma

Aşağıdaki adımlarda Apache Hive ODBC veri kaynağı oluşturma adımları gösterilmektedir.

1. Windows 'da, Windows **Yönetim araçları** > **ODBC veri kaynakları (32 bit)/(64 bit)**  > **Başlat** ' a gidin.  **ODBC veri kaynağı Yöneticisi** penceresi açılır.

    ![OBDC veri kaynağı Yöneticisi](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "ODBC veri kaynağı Yöneticisi 'ni kullanarak bir DSN yapılandırma")

1. **Yeni veri kaynağı oluştur** penceresini açmak IÇIN **Kullanıcı DSN** sekmesinden **Ekle** ' yi seçin.

1. **Microsoft Hive ODBC sürücüsü**' yi seçin ve ardından **son** ' u seçerek **Microsoft Hive ODBC sürücüsü DSN kurulum** penceresini açın.

1. Aşağıdaki değerleri yazın veya seçin:

   | Özellik | Açıklama |
   | --- | --- |
   |  Data Source Name |Veri kaynağınız için bir ad verin |
   |  Ana bilgisayar (ler) |`CLUSTERNAME.azurehdinsight.net` yazın. Örneğin, `myHDICluster.azurehdinsight.net` |
   |  Bağlantı noktası |**443** yazın.|
   |  Database |**Varsayılanı**kullanın. |
   |  Mechanism |**Windows Azure HDInsight hizmetini** seçin |
   |  User Name |HDInsight kümesi HTTP Kullanıcı Kullanıcı adı girin. Varsayılan kullanıcı adı **admin** şeklindedir. |
   |  Parola |HDInsight kümesi Kullanıcı parolasını girin. **Parolayı Kaydet (şifreli)** onay kutusunu seçin.|

1. İsteğe bağlı: **Gelişmiş Seçenekler**' i seçin.  

   | Parametre | Açıklama |
   | --- | --- |
   |  Yerel sorgu kullan |Seçildiğinde, ODBC sürücüsü TSQL HiveQL 'e dönüştürmeyi denemez. Bu seçeneği yalnızca %100, saf HiveQL deyimlerini gönderdikten emin olduğunuzda kullanın. SQL Server veya Azure SQL veritabanı 'na bağlanırken, işareti kaldırılmış olarak bırakmalısınız. |
   |  Blok başına getirilen satırlar |Çok sayıda kayıt getirilirken, en iyi performansı sağlamak için bu parametreyi ayarlama gerekebilir. |
   |  Varsayılan dize sütunu uzunluğu, Ikili sütun uzunluğu, ondalık sütun ölçeği |Veri türü uzunlukları ve ön ekleri verilerin nasıl döndürüldüğünü etkileyebilir. Duyarlık ve kesilme kaybı nedeniyle yanlış bilgilerin döndürülmesine neden olur. |

    ![Gelişmiş DSN yapılandırma seçenekleri](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Gelişmiş DSN yapılandırma seçenekleri")

1. Veri kaynağını test etmek için **Test** ' i seçin. Veri kaynağı doğru şekilde yapılandırıldığında, test sonucu **başarıyı**gösterir.  

1. Test penceresini kapatmak için **Tamam ' ı** seçin.  

1. **MICROSOFT HIVE ODBC sürücüsü DSN kurulum** penceresini kapatmak için **Tamam ' ı** seçin.  

1. **ODBC veri kaynağı Yöneticisi** penceresini kapatmak için **Tamam ' ı** seçin.  

## <a name="query-data-with-powershell"></a>PowerShell ile verileri sorgulama

Aşağıdaki PowerShell betiği, bir Hive kümesini sorgulamak için ODBC 'nin bir işlevidir.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

Aşağıdaki kod parçacığı, öğreticinin başlangıcında oluşturduğunuz etkileşimli sorgu kümesinde bir sorgu yürütmek için yukarıdaki işlevi kullanır. `DATASOURCENAME`, **MICROSOFT HIVE ODBC sürücüsü DSN kurulum** ekranında belirttiğiniz **veri kaynağı adıyla** değiştirin. Kimlik bilgileri istendiğinde, kümeyi oluştururken **küme oturum açma Kullanıcı adı** ve **küme oturum açma parolası** altında girdiğiniz kullanıcı adını ve parolayı girin.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, HDInsight kümesini ve depolama hesabını silin. Bunu yapmak için kümenin oluşturulduğu kaynak grubunu seçin ve **Sil**' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure HDInsight etkileşimli sorgu kümenizdeki verileri almak için Microsoft Hive ODBC sürücüsü ve PowerShell 'in nasıl kullanılacağını öğrendiniz.

> [!div class="nextstepaction"]
> [ODBC kullanarak Excel 'i Apache Hive bağlama](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
