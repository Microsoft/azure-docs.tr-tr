---
author: rothja
ms.author: jroth
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.openlocfilehash: 5509c640fbf232cb6c613d96c30cb0e8d9d10ec5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551701"
---
## <a name="c-program-example"></a>C# program örneği

Bu makalenin sonraki bölümlerinde Transact-SQL (T-SQL) deyimlerini SQL veritabanına göndermek için ADO.NET kullanan bir C# programı bulunmaktadır. C# programı aşağıdaki eylemleri gösterir:

- [ADO.NET kullanarak SQL veritabanı 'na bağlanma](#cs_1_connect)
- [T-SQL deyimleri döndüren yöntemler](#cs_2_return)
    - Tablo oluşturma
    - Tabloları verilerle doldurma
    - Verileri güncelleştirme, silme ve seçme
- [T-SQL ' i veritabanına gönder](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>Varlık Ilişkisi diyagramı (ERD)

`CREATE TABLE`Deyimler iki tablo arasında *yabancı anahtar* (FK) Ilişkisi oluşturmak için **Başvurular** anahtar sözcüğünü içerir. *Tempdb* kullanıyorsanız, `--REFERENCES` bir dizi baştaki tire kullanarak anahtar sözcüğü not edin.

ERD iki tablo arasındaki ilişkiyi görüntüler. **TabEmployee. DepartmentCode** *alt* sütunundaki değerler, **tabdepartment. DepartmentCode** *üst* sütunundaki değerlerle sınırlıdır.

![Yabancı anahtarı gösteren ERD](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> T-SQL ' i, tablo adlarına bir lider eklemek için, `#` bunları *tempdb*'de geçici tablolar olarak oluşturan bir seçenek olacak şekilde düzenleyebilirsiniz. Bu, kullanılabilir test veritabanı olmadığında tanıtım amacıyla faydalıdır. Yabancı anahtarlara yapılan herhangi bir başvuru, kullanımları sırasında zorlanmaz ve geçici tablolar, programın çalışması bittikten sonra bağlantı kapandığında otomatik olarak silinir.

### <a name="to-compile-and-run"></a>Derlemek ve çalıştırmak için

C# programı mantıksal olarak bir. cs dosyasıdır ve her bir bloğun anlaşılması daha kolay anlamak için fiziksel olarak birçok kod bloğuna bölünmüştür. Programı derlemek ve çalıştırmak için aşağıdaki adımları uygulayın:

1. Visual Studio 'da bir C# projesi oluşturun. Proje türü  **Şablonlar**  >  **Visual C#**  >  **Windows Masaüstü**  >  **konsol uygulaması (.NET Framework)** altında bulunan bir konsol olmalıdır.

1. *Program. cs* dosyasında, kod başlangıç satırları ' nı aşağıdaki adımlarla değiştirin:

    1. Aşağıdaki kod bloklarını kopyalayıp, sunulan aynı sırada kopyalayıp yapıştırın, bkz. [veritabanına Bağlan](#cs_1_connect), [T-SQL oluştur](#cs_2_return)ve [veritabanına gönder](#cs_3_submit).

    1. Yönteminde aşağıdaki değerleri değiştirin `Main` :

        - *CB. DataSource*
        - *CB. UserID*
        - *CB. Parolayı*
        - *TialCatalogcb.Ini*

1. *System.Data.dll* bütünleştirilmiş koda başvurulduğunu doğrulayın. Doğrulamak için **Çözüm Gezgini** bölmesinde **Başvurular** düğümünü genişletin.

1. Visual Studio 'dan programı derlemek ve çalıştırmak için **Başlat** düğmesini seçin. Rapor çıktısı bir program penceresinde görüntülenir, ancak GUID değerleri test çalıştırmaları arasında farklılık gösterir.

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"></a>

### <a name="connect-to-sql-database-using-adonet"></a>ADO.NET kullanarak SQL veritabanı 'na bağlanma

```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                var cb = new SqlConnectionStringBuilder();
                cb.DataSource = "your_server.database.windows.net";
                cb.UserID = "your_user";
                cb.Password = "your_password";
                cb.InitialCatalog = "your_database";

                using (var connection = new SqlConnection(cb.ConnectionString))
                {
                    connection.Open();

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
                        "@csharpParmDepartmentName", "Legal");

                    Submit_6_Tsql_SelectEmployees(connection);
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }

            Console.WriteLine("View the report output here, then press any key to end the program...");
            Console.ReadKey();
        }
```

<a name="cs_2_return"></a>

### <a name="methods-that-return-t-sql-statements"></a>T-SQL deyimleri döndüren yöntemler

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

static string Build_4_Tsql_UpdateJoin()
{
    return @"
        DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';

        -- Promote everyone in one department (see @parm...).
        UPDATE empl
        SET
            empl.EmployeeLevel += 1
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName1;
    ";
}

static string Build_5_Tsql_DeleteJoin()
{
    return @"
        DECLARE @DName2  nvarchar(128);
        SET @DName2 = @csharpParmDepartmentName;  --'Legal';

        -- Right size the Legal department.
        DELETE empl
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName2

        -- Disband the Legal department.
        DELETE tabDepartment
            WHERE DepartmentName = @DName2;
    ";
}

static string Build_6_Tsql_SelectEmployees()
{
    return @"
        -- Look at all the final Employees.
        SELECT
            empl.EmployeeGuid,
            empl.EmployeeName,
            empl.EmployeeLevel,
            empl.DepartmentCode,
            dept.DepartmentName
        FROM
            tabEmployee   as empl
        LEFT OUTER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        ORDER BY
            EmployeeName;
    ";
}
```

<a name="cs_3_submit"></a>

### <a name="submit-t-sql-to-the-database"></a>T-SQL ' i veritabanına gönder

```csharp
static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("Now, SelectEmployees (6)...");

    string tsql = Build_6_Tsql_SelectEmployees();

    using (var command = new SqlCommand(tsql, connection))
    {
        using (SqlDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                    reader.GetGuid(0),
                    reader.GetString(1),
                    reader.GetInt32(2),
                    (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                    (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
            }
        }
    }
}

static void Submit_Tsql_NonQuery(
    SqlConnection connection,
    string tsqlPurpose,
    string tsqlSourceCode,
    string parameterName = null,
    string parameterValue = null
    )
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

    using (var command = new SqlCommand(tsqlSourceCode, connection))
    {
        if (parameterName != null)
        {
            command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                parameterName,
                parameterValue);
        }
        int rowsAffected = command.ExecuteNonQuery();
        Console.WriteLine(rowsAffected + " = rows affected.");
    }
}
} // EndOfClass
}
```