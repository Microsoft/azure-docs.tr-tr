---
title: Azure SQL Edge 'i bağlama ve sorgulama
description: Azure SQL Edge 'e bağlanma ve sorgulama hakkında bilgi edinin.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395215"
---
# <a name="connect-and-query-azure-sql-edge"></a>Azure SQL Edge 'i bağlama ve sorgulama

Azure SQL Edge 'de, bir kapsayıcıyı dağıttıktan sonra aşağıdaki konumlardan herhangi birinden veritabanı altyapısına bağlanabilirsiniz:

- Kapsayıcının içinde
- Aynı konakta çalışan başka bir Docker kapsayıcısından
- Ana makineden
- Ağdaki herhangi bir diğer istemci makineden

## <a name="tools-to-connect-to-azure-sql-edge"></a>Azure SQL Edge 'e bağlanmak için Araçlar

Azure SQL Edge örneği örneğine, bu ortak araçlardan herhangi birinden bağlanabilirsiniz:

* [sqlcmd](/sql/linux/sql-server-linux-setup-tools): sqlcmd istemci araçları, Azure SQL Edge 'in kapsayıcı görüntüsüne zaten dahil edilmiştir. Etkileşimli bash kabuğu ile çalışan bir kapsayıcıya eklerseniz, araçları yerel olarak çalıştırabilirsiniz. SQL istemci araçları, SQL Edge kapsayıcılarının ARM64 sürümüne dahil olmadıklarından, ARM64 platformunda kullanılabilir DEĞILDIR. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Bir ağ makinesinden Azure SQL Edge veritabanı altyapısına bağlanmak için şunlar gerekir:

- **Konak makinenin IP adresi veya ağ adı**: Bu, Azure SQL Edge kapsayıcısının çalıştığı ana makinedir.
- **Azure SQL Edge kapsayıcı ana bilgisayar bağlantı noktası eşlemesi**: Bu, Docker kapsayıcı bağlantı noktasının konaktaki bir bağlantı noktasına yönelik eşlemedir. Kapsayıcı içinde Azure SQL Edge her zaman bağlantı noktası 1433 ' e eşlenir. İsterseniz bunu değiştirebilirsiniz. Bağlantı noktası numarasını değiştirmek için Azure IoT Edge Azure SQL Edge modülü için **kapsayıcı oluşturma seçeneklerini** güncelleştirin. Aşağıdaki örnekte, kapsayıcıda 1433 numaralı bağlantı noktası konaktaki bağlantı noktası 1600 ile eşleştirilir.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Azure SQL Edge örneği Için sa parolası**: Bu, `SA_PASSWORD` Azure SQL Edge dağıtımı sırasında ortam değişkeni için belirtilen değerdir.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Kapsayıcı içinden veritabanı altyapısına bağlanma

[SQL Server komut satırı araçları](/sql/linux/sql-server-linux-setup-tools) , Azure SQL Edge 'in kapsayıcı görüntüsüne dahildir. Kapsayıcıyı etkileşimli bir komut istemiyle birlikte eklerseniz, araçları yerel olarak çalıştırabilirsiniz. SQL istemci araçları, SQL Edge kapsayıcılarının ARM64 sürümüne dahil olmadıklarından, ARM64 platformunda kullanılabilir DEĞILDIR. 

1. `docker exec -it`Çalışan kapsayıcının içinde etkileşimli bir bash kabuğu başlatmak için komutunu kullanın. Aşağıdaki örnekte, `e69e056c702d` KAPSAYıCı kimliğidir.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Her zaman kapsayıcı KIMLIĞININ tamamını belirtmeniz gerekmez. Benzersiz bir şekilde tanımlamak için yeterli karakter belirtmeniz yeterlidir. Bu nedenle, bu örnekte, `e6` tam kimlik yerine veya kullanmak yeterli olabilir `e69` .

2. Kapsayıcının içindeyken sqlcmd ile yerel olarak bağlanın. Sqlcmd, varsayılan olarak yolda değildir, bu nedenle tam yolu belirtmeniz gerekir.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Sqlcmd ile işiniz bittiğinde, yazın `exit` .

4. Etkileşimli komut istemiyle işiniz bittiğinde yazın `exit` . Etkileşimli bash kabuğu 'ndan çıktıktan sonra Kapsayıcınız çalışmaya devam eder.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Aynı konaktaki başka bir kapsayıcıdan Azure SQL Edge 'e bağlanma

Aynı konakta çalışan iki kapsayıcı aynı Docker ağı üzerinde olduğundan, hizmete ait kapsayıcı adını ve bağlantı noktası adresini kullanarak kolayca erişebilirsiniz. Örneğin, aynı konaktaki başka bir Python modülünden (container) Azure SQL Edge örneğine bağlanıyorsanız aşağıdakine benzer bir bağlantı dizesi kullanabilirsiniz. (Bu örnekte, Azure SQL Edge 'in varsayılan bağlantı noktasında dinlemek üzere yapılandırıldığı varsayılır.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Başka bir ağ makinesinden Azure SQL Edge 'e bağlanma

Ağ üzerindeki başka bir makineden Azure SQL Edge örneğine bağlanmak isteyebilirsiniz. Bunu yapmak için, Docker konağının IP adresini ve Azure SQL Edge kapsayıcısının eşlendiği ana bilgisayar bağlantı noktasını kullanın. Örneğin, Docker konağının IP adresi *xxx.xxx.xxx.xxx* Ise ve Azure SQL Edge kapsayıcısı, ana bilgisayar bağlantı noktası *1600*' e eşlenmişse, Azure SQL Edge örneği için sunucu adresi xxx. xxx *. xxx. xxx, 1600* olur. Güncelleştirilmiş Python betiği:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Bir Windows makinesinde çalışan SQL Server Management Studio kullanarak Azure SQL Edge örneğine bağlanmak için bkz. [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Bir Windows, Mac veya Linux makinesinde Visual Studio Code kullanarak Azure SQL Edge örneğine bağlanmak için bkz. [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode).

Bir Windows, Mac veya Linux makinesinde Azure Data Studio kullanarak Azure SQL Edge örneğine bağlanmak için bkz. [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Sonraki adımlar

[Bağlanma ve sorgulama](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Linux 'ta SQL Server araçları 'nı yükler](/sql/linux/sql-server-linux-setup-tools)