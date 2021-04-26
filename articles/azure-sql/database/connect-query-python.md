---
title: Bir veritabanını sorgulamak için Python kullanma
description: Bu konu başlığı altında, Python kullanarak Azure SQL veritabanı 'nda bir veritabanına bağlanan ve Transact-SQL deyimlerini kullanarak sorgulayan bir program oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491971"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Hızlı başlangıç: bir veritabanını sorgulamak için Python kullanma

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Bu hızlı başlangıçta Python kullanarak Azure SQL veritabanı, Azure SQL yönetilen örneği veya SYNAPSE SQL veritabanı 'na bağlanın ve verileri sorgulamak için T-SQL deyimlerini kullanın.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakilere ihtiyacınız vardır:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Sorgu çalıştıracağınız bir veritabanı.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 ve ilgili yazılımlar
    

    |**Eylem**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |SQL Server için ODBC sürücüsünü, SQLCMD ve Python sürücüsünü yükler|[MacOS üzerinde SQL Server kullanarak Python uygulamaları oluşturma konusunda](https://www.microsoft.com/sql-server/developer-get-started/python/mac/) **1,2**, **1,3** ve **2,1** adımlarını kullanın. Bu ayrıca Install homebrew ve Python yükler.       |[Pyodbc Python geliştirmesi için bir ortam yapılandırma](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Pyodbc Python geliştirmesi için bir ortam yapılandırın](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Python ve diğer gerekli paketleri yükler|    |`sudo apt-get install python python-pip gcc g++ build-essential` komutunu kullanın.|    |
    |Daha fazla bilgi|[MacOS 'ta Microsoft ODBC sürücüsü](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Linux 'ta Microsoft ODBC sürücüsü](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Linux 'ta Microsoft ODBC sürücüsü](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



Python ve Azure SQL veritabanı 'nda veritabanını daha fazla incelemek için bkz. [Python Için Azure SQL veritabanı kitaplıkları](/python/api/overview/azure/sql), [pyodbc deposu](https://github.com/mkleehammer/pyodbc/wiki/)ve bir [pyodbc örneği](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Veritabanınızı sorgulamak için kod oluşturma 

1. Bir metin düzenleyicisinde, *SQLtest.py* adlı yeni bir dosya oluşturun.  
   
1. Aşağıdaki kodu ekleyin. Önkoşullar bölümünden bağlantı bilgilerini alın ve,, ve için kendi değerlerinizi yerine koyun \<server> \<database> \<username> \<password> .
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Kodu çalıştırma

1. Komut isteminde aşağıdaki komutu çalıştırın:

   ```cmd
   python sqltest.py
   ```

1. Veritabanlarının ve harmanlamaları döndürüldüğünden emin olun ve ardından komut penceresini kapatın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL veritabanı 'nda ilk veritabanınızı tasarlama](design-first-database-tutorial.md)
- [SQL Server için Microsoft Python sürücüleri](/sql/connect/python/python-driver-for-sql-server/)
- [Python Geliştirici Merkezi](https://azure.microsoft.com/develop/python/?v=17.23h)
