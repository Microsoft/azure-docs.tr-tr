---
title: "Excel, Python veya R 'tan Azure Databricks bağlanma "
description: Excel, Python veya R 'ye Azure Databricks bağlanmak için Simba sürücüsünü nasıl kullanacağınızı öğrenin.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601939"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Excel, Python veya R 'tan Azure Databricks bağlanma

Bu makalede, Microsoft Excel, Python veya R diliyle Azure Databricks bağlanmak için Databricks ODBC sürücüsünü nasıl kullanacağınızı öğreneceksiniz. Bağlantıyı oluşturduktan sonra Excel, Python veya R istemcilerinden Azure Databricks verilere erişebilirsiniz. Ayrıca, verileri daha fazla analiz etmek için istemcileri de kullanabilirsiniz. 

## <a name="prerequisites"></a>Ön koşullar

* Kümenizle ilişkili bir Azure Databricks çalışma alanınız, bir Spark kümeniz ve örnek verileriniz olmalıdır. Bu önkoşullara zaten sahip değilseniz, [Azure Portal kullanarak Azure Databricks Spark Işi çalıştırma](quickstart-create-databricks-workspace-portal.md)' da hızlı başlangıcı doldurun.

* Databricks [sürücü indirme sayfasından](https://databricks.com/spark/odbc-driver-download)DATABRICKS ODBC sürücüsünü indirin. Azure Databricks bağlanmak istediğiniz uygulamaya bağlı olarak 32-bit veya 64-bit sürümünü yükleyebilirsiniz. Örneğin, Excel 'den bağlanmak için sürücünün 32 bit sürümünü yüklemelisiniz. R ve Python 'dan bağlanmak için sürücünün 64 bit sürümünü yüklemelisiniz.

* Databricks 'te kişisel erişim belirteci ayarlayın. Yönergeler için bkz. [belirteç yönetimi](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>DSN ayarlama

Veri kaynağı adı (DSN), belirli bir veri kaynağıyla ilgili bilgileri içerir. ODBC sürücüsü, bir veri kaynağına bağlanmak için bu DSN 'ye ihtiyaç duyuyor. Bu bölümde, Microsoft Excel, Python veya R gibi istemcilerden Azure Databricks bağlanmak için Databricks ODBC sürücüsüyle birlikte kullanılabilecek bir DSN ayarlarsınız.

1. Azure Databricks çalışma alanından Databricks kümesine gidin.

    ![Databricks kümesini aç](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Databricks kümesini aç")

2. **Yapılandırma** sekmesinde, **JDBC/ODBC** sekmesine tıklayın ve **sunucu konak adı** ve **http yolu**değerlerini kopyalayın. Bu makaledeki adımları tamamlayabilmeniz için bu değerlere ihtiyacınız vardır.

    ![Databricks yapılandırmasını al](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks yapılandırmasını al")

3. Bilgisayarınızda, uygulamaya bağlı olarak **ODBC veri kaynakları** uygulamasını (32-bit veya 64 bit) başlatın. Excel 'den bağlanmak için 32 bit sürümünü kullanın. R ve Python 'dan bağlanmak için 64 bit sürümünü kullanın.

    ![ODBC 'yi Başlat](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC uygulamasını Başlat")

4. **Kullanıcı DSN 'si** sekmesinde **Ekle**' ye tıklayın. **Yeni veri kaynağı oluştur** iletişim kutusunda, **SIMBA Spark ODBC sürücüsünü**seçin ve ardından **son**' a tıklayın.

    ![ODBC 'yi Başlat](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC uygulamasını Başlat")

5. **Simba Spark ODBC sürücüsü** iletişim kutusunda, aşağıdaki değerleri sağlayın:

    ![DSN 'yi yapılandırma](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "DSN 'yi yapılandırma")

    Aşağıdaki tabloda, iletişim kutusunda sağlanacak değerler hakkında bilgi verilmektedir.
    
    |Alan  | Değer  |
    |---------|---------|
    |**Veri kaynağı adı**     | Veri kaynağı için bir ad sağlayın.        |
    |**Ana bilgisayar (ler)**     | *Sunucu konak adı*Için Databricks çalışma alanından kopyaladığınız değeri belirtin.        |
    |**Bağlantı Noktası**     | *443*girin.        |
    |**Kimlik doğrulama** > **mekanizması**     | *Kullanıcı adı ve parola '* yı seçin.        |
    |**Kullanıcı adı**     | *Belirteç*girin.        |
    |**Parola**     | Databricks çalışma alanından kopyaladığınız belirteç değerini girin. |
    
    DSN Kurulumu iletişim kutusunda aşağıdaki ek adımları gerçekleştirin.
    
    * **Http seçenekleri**' ne tıklayın. Açılan iletişim kutusunda, Databricks çalışma alanından kopyaladığınız *http yolunun* değerini yapıştırın. **Tamam** düğmesine tıklayın.
    * **SSL seçenekleri**' ne tıklayın. Açılan iletişim kutusunda **SSL 'Yi etkinleştir** onay kutusunu işaretleyin. **Tamam** düğmesine tıklayın.
    * Azure Databricks bağlantısını test etmek için **Test** ' e tıklayın. Yapılandırmayı kaydetmek için **Tamam** ' ı tıklatın.
    * **ODBC veri kaynağı Yöneticisi** Iletişim kutusunda **Tamam**' a tıklayın.

Artık DSN 'nizin kurulumunu tamamladınız. Sonraki bölümlerde, bu DSN 'yi Excel, Python veya R 'den Azure Databricks bağlanmak için kullanırsınız.

## <a name="connect-from-microsoft-excel"></a>Microsoft Excel 'den Bağlan

Bu bölümde, daha önce oluşturduğunuz DSN 'yi kullanarak Azure Databricks verileri Microsoft Excel 'e çekebilirsiniz. Başlamadan önce, bilgisayarınızda Microsoft Excel yüklü olduğundan emin olun. Excel 'in deneme sürümünü, [Microsoft Excel deneme bağlantısı](https://products.office.com/excel)'ndan kullanabilirsiniz.

1. Microsoft Excel 'de boş bir çalışma kitabı açın. **Veri** şeridinde **veri al**' a tıklayın. **Diğer kaynaklardan** ve ardından **ODBC**' ye tıklayın.

    ![Excel 'den ODBC 'yi Başlat](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Excel 'den ODBC 'yi Başlat")

2. **ODBC 'den** iletişim kutusunda, daha önce oluşturduğunuz DSN 'yi seçin ve ardından **Tamam**' a tıklayın.

    ![DSN Seç](./media/connect-databricks-excel-python-r/excel-select-dsn.png "DSN Seç")

3. Kimlik bilgileri istenirse, Kullanıcı adı için **belirteç**girin. Parola için, Databricks çalışma alanından aldığınız belirteç değerini sağlayın.

    ![Databricks için kimlik bilgilerini sağlayın](./media/connect-databricks-excel-python-r/excel-databricks-token.png "DSN Seç")

4. Gezgin penceresinden, Databricks içinde Excel 'e yüklemek istediğiniz tabloyu seçin ve ardından **Yükle**' ye tıklayın. 

    ![DTA 'i Excel 'e yükle](./media/connect-databricks-excel-python-r/excel-load-data.png "DTA 'i Excel 'e yükle")

Excel çalışma kitabınızda verileri aldıktan sonra, üzerinde analitik işlemler yapabilirsiniz.

## <a name="connect-from-r"></a>R 'den Bağlan

> [!NOTE]
> Bu bölümde, masaüstünüzde çalışan bir R Studio istemcisinin Azure Databricks ile nasıl tümleştirileceği hakkında bilgi verilmektedir. Azure Databricks kümesi üzerinde R Studio 'Nun nasıl kullanılacağına ilişkin yönergeler için, bkz. [Azure Databricks üzerinde r Studio](/azure/databricks/spark/latest/sparkr/rstudio).

Bu bölümde, Azure Databricks bulunan verilere başvurmak için R Language IDE kullanırsınız. Başlamadan önce, bilgisayarda aşağıdakilerin yüklü olması gerekir.

* R dili için IDE. Bu makale, masaüstü için RStudio 'Yu kullanır. Bunu, [R Studio](https://www.rstudio.com/products/rstudio/download/)'dan yükleyebilirsiniz.
* IDE olarak masaüstü için RStudio kullanıyorsanız, [https://aka.ms/rclient/](https://aka.ms/rclient/)Microsoft R Client de yükleyebilirsiniz. 

RStudio 'Yu açın ve aşağıdaki adımları uygulayın:

- `RODBC` paketine başvurun. Bu, daha önce oluşturduğunuz DSN 'yi kullanarak Azure Databricks bağlanmanızı sağlar.
- DSN kullanarak bağlantı kurun.
- Azure Databricks verilerde bir SQL sorgusu çalıştırın. Aşağıdaki kod parçacığında, *radio_sample_data* zaten var olan bir tablodur Azure Databricks.
- Çıktıyı doğrulamak için sorgu üzerinde bazı işlemler gerçekleştirin. 

Aşağıdaki kod parçacığı şu görevleri gerçekleştirir:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Python 'dan Bağlan

Bu bölümde, Azure Databricks bulunan verilere başvurmak için bir Python IDE (boş gibi) kullanırsınız. Başlamadan önce, aşağıdaki önkoşulları doldurun:

* Python uygulamasını [buradan](https://www.python.org/downloads/)ekleyin. Python 'un bu bağlantıdan yüklenmesi de boşta olarak yüklenir.

* Bilgisayardaki bir komut isteminden `pyodbc` paketini yükledikten sonra. Şu komutu çalıştırın:

      pip install pyodbc

BOŞTA ' i açın ve aşağıdaki adımları uygulayın:

- `pyodbc` paketini içeri aktarın. Bu, daha önce oluşturduğunuz DSN 'yi kullanarak Azure Databricks bağlanmanızı sağlar.
- Daha önce oluşturduğunuz DSN 'yi kullanarak bağlantı kurun.
-  Oluşturduğunuz bağlantıyı kullanarak bir SQL sorgusu çalıştırın. Aşağıdaki kod parçacığında, *radio_sample_data* zaten var olan bir tablodur Azure Databricks.
- Çıktıyı doğrulamak için sorgu üzerinde işlemler gerçekleştirin.

Aşağıdaki kod parçacığı şu görevleri gerçekleştirir:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Databricks verileri içeri aktarabileceğiniz kaynaklar hakkında bilgi edinmek için bkz. [Azure Databricks Için veri kaynakları](/azure/databricks/data/data-sources/index)


