---
title: Always Encrypted-Windows sertifika deposu
description: Bu makalede, SQL Server Management Studio (SSMS) ' de Always Encrypted Sihirbazı 'Nı kullanarak bir SQL veritabanında hassas verilerin nasıl güvenli hale kullanılacağı gösterilmektedir. Ayrıca, şifreleme anahtarlarınızı Windows sertifika depolama alanında nasıl depolayabileceği de gösterilmektedir.
keywords: verileri, SQL şifrelemesini, veritabanı şifrelemesini, hassas verileri, Always Encrypted şifreleyin
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 03/08/2019
ms.openlocfilehash: 82c3c3274a8a9d66019ce906ee7be47cedac7470
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822053"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Always Encrypted: hassas verileri koruma ve şifreleme anahtarlarını Windows sertifika deposunda depolama

Bu makalede, [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx)' de [Always Encrypted Sihirbazı 'Nı](https://msdn.microsoft.com/library/mt459280.aspx) kullanarak bir SQL veritabanında hassas verilerin nasıl güvenli hale kullanılacağı gösterilmektedir. Ayrıca, şifreleme anahtarlarınızı Windows sertifika depolama alanında nasıl depolayabileceği de gösterilmektedir.

Always Encrypted, Azure SQL veritabanı 'ndaki yeni bir veri şifreleme teknolojisidir ve SQL Server, istemci ve sunucu arasındaki taşıma sırasında ve veriler kullanımda olduğunda, önemli verilerin şu şekilde göründüğünden emin olmak için sunucuda bekleyen hassas verileri korumaya yardımcı olur. veritabanı sisteminin içinde düz metin. Verileri şifreledikten sonra, yalnızca anahtarlara erişimi olan istemci uygulamaları veya uygulama sunucuları düz metin verilerine erişebilir. Ayrıntılı bilgi için bkz. [Always Encrypted (veritabanı altyapısı)](https://msdn.microsoft.com/library/mt163865.aspx).

Veritabanını Always Encrypted kullanacak şekilde yapılandırdıktan sonra, şifrelenen verilerle çalışmak için Visual Studio C# ile birlikte bir istemci uygulaması oluşturacaksınız.

Azure SQL veritabanı için Always Encrypted ayarlamayı öğrenmek için bu makaledeki adımları izleyin. Bu makalede, aşağıdaki görevlerin nasıl gerçekleştirileceğini öğreneceksiniz:

* [Always Encrypted anahtarlar](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)oluşturmak için SSMS 'deki Always Encrypted Sihirbazı 'nı kullanın.
  * Bir [sütun ana anahtarı (CMK)](https://msdn.microsoft.com/library/mt146393.aspx)oluşturun.
  * Bir [sütun şifreleme anahtarı (cek)](https://msdn.microsoft.com/library/mt146372.aspx)oluşturun.
* Veritabanı tablosu oluşturun ve sütunları şifreleyin.
* Şifrelenmiş sütunlardan veri ekleyen, seçen ve görüntüleyen bir uygulama oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticide şunlar gerekir:

* Bir Azure hesabı ve aboneliği Bir hesabınız yoksa, [ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/)için kaydolun.
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) sürüm 13.0.700.242 veya üzeri.
* [.NET Framework 4,6](https://msdn.microsoft.com/library/w0x726c2.aspx) veya üzeri (istemci bilgisayarda).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Boş bir SQL veritabanı oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Kaynak oluştur** > **veri + depolama** > **SQL veritabanı**' na tıklayın.
3. Yeni veya var olan bir sunucuda **Clinic** adlı **boş** bir veritabanı oluşturun. Azure portal veritabanı oluşturma hakkında ayrıntılı yönergeler için bkz. [Ilk Azure SQL veritabanınız](sql-database-single-database-get-started.md).

    ![Boş veritabanı oluşturma](./media/sql-database-always-encrypted/create-database.png)

Öğreticide daha sonra bağlantı dizesine ihtiyacınız olacak. Veritabanı oluşturulduktan sonra, yeni Clinic veritabanına gidin ve bağlantı dizesini kopyalayın. Bağlantı dizesini istediğiniz zaman alabilirsiniz, ancak Azure portal olduğunuzda kopyalamak kolaydır.

1. **SQL veritabanları** > **Clinic** > **veritabanı bağlantı dizelerini göster**' e tıklayın.
2. **ADO.net**için bağlantı dizesini kopyalayın.

    ![Bağlantı dizesini kopyalayın](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>SSMS ile veritabanına bağlanma

SSMS 'yi açın ve Clinic veritabanıyla sunucuya bağlanın.

1. SSMS’i açın. (Açık değilse **sunucuya Bağlan** penceresini açmak Için > **veritabanı altyapısına** **Bağlan** ' a tıklayın).
2. Sunucu adınızı ve kimlik bilgilerinizi girin. Sunucu adı, SQL veritabanı dikey penceresinde ve daha önce kopyaladığınız bağlantı dizesinde bulunabilir. *Database.Windows.net*dahil olmak üzere tüm sunucu adını yazın.

    ![Bağlantı dizesini kopyalayın](./media/sql-database-always-encrypted/ssms-connect.png)

**Yeni güvenlik duvarı kuralı** penceresi açılırsa Azure 'da oturum açın ve SSMS 'nin sizin için yeni bir güvenlik duvarı kuralı oluşturmasına izin verin.

## <a name="create-a-table"></a>Bir tablo oluşturma

Bu bölümde, hasta verilerini tutacak bir tablo oluşturacaksınız. Bu, başlangıçta normal bir tablo olacaktır ve bir sonraki bölümde şifrelemeyi yapılandıracaksınız.

1. **Veritabanları**' nı genişletin.
2. **Clinic** veritabanına sağ tıklayın ve **Yeni sorgu**' ya tıklayın.
3. Aşağıdaki Transact-SQL (T-SQL) ' i yeni sorgu penceresine yapıştırın ve **yürütün** .

        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO

## <a name="encrypt-columns-configure-always-encrypted"></a>Sütunları şifreleyin (Always Encrypted Yapılandır)

SSMS, sizin için CMK, CEK ve şifrelenmiş sütunları ayarlayarak Always Encrypted kolayca yapılandırmak için bir sihirbaz sağlar.

1. **Veritabanları** > **Clinic** > **tabloları**' nı genişletin.
2. **Hastalar** tablosuna sağ tıklayın ve Always Encrypted Sihirbazı 'nı açmak Için **sütunları şifreleyin** ' ı seçin:

    ![Sütunları şifreleyin](./media/sql-database-always-encrypted/encrypt-columns.png)

Always Encrypted Sihirbazı aşağıdaki bölümleri içerir: **sütun seçimi**, **ana anahtar yapılandırması** (CMK), **doğrulama**ve **Özet**.

### <a name="column-selection"></a>Sütun seçimi

**Giriş** sayfasında, **sütun seçim** sayfasını açmak için **İleri** ' ye tıklayın. Bu sayfada, hangi sütunları şifrelemek istediğinizi, [şifreleme türünü ve kullanılacak sütun şifreleme anahtarını (cek)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) seçersiniz.

Her hasta için **SSK** ve **Doğum tarihi** bilgilerini şifreleyin. **SSK** sütunu, eşitlik aramalarını, birleştirmeleri ve gruplama tarafından desteklenen belirleyici şifrelemeyi kullanır. **Doğum tarihi** sütunu, işlemleri desteklemeyen rastgele şifrelemeyi kullanır.

**SSN** sütunu Için **şifreleme türünü** **belirleyici** ve **Doğum tarihi** sütununu **rastgele**olarak ayarlayın. **İleri**’ye tıklayın.

![Sütunları şifreleyin](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Ana anahtar yapılandırması

**Ana anahtar yapılandırma** sayfası, CMK 'nizi ayarladığınız ve CMK 'ın depolanacağı anahtar deposu sağlayıcısını seçtiğiniz yerdir. Şu anda, bir CMK 'yi Windows sertifika deposunda, Azure Key Vault veya bir donanım güvenlik modülünde (HSM) depolayabilmeniz gerekir. Bu öğreticide, anahtarlarınızı Windows sertifika depolama alanında nasıl depolayabileceği gösterilmektedir.

**Windows sertifika deposunun** seçili olduğunu doğrulayın ve **İleri**' ye tıklayın.

![Ana anahtar yapılandırması](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Doğrulama

Sütunları şimdi şifreleyebilir veya daha sonra çalıştırmak için bir PowerShell betiği kaydedebilirsiniz. Bu öğreticide **Şimdi sona ermesini sağlamak Için devam** 'ı seçin ve **İleri**' ye tıklayın.

### <a name="summary"></a>Özet

Ayarların doğru olduğundan emin olun ve Always Encrypted kurulumunu gerçekleştirmek için **son** ' a tıklayın.

![Özet](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Sihirbazın eylemlerini doğrulama

Sihirbaz tamamlandıktan sonra veritabanınız Always Encrypted ayarlanır. Sihirbaz aşağıdaki eylemleri gerçekleştirdi:

* Bir CMK oluşturuldu.
* Bir CEK oluşturuldu.
* Şifreleme için seçili sütunlar yapılandırıldı. **Hastalar** tablonuzda Şu anda hiç veri yok, ancak seçili sütunlardaki tüm mevcut veriler artık şifrelendi.

SSMS 'de anahtarların oluşturulmasını, **Clinic** > **güvenlik** > **Always Encrypted anahtarlarına**giderek doğrulayabilirsiniz. Artık sihirbazın sizin için oluşturduğu yeni anahtarları görebilirsiniz.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Şifrelenmiş verilerle birlikte çalışarak bir istemci uygulaması oluşturma

Always Encrypted ayarlandığına *göre,* şifrelenmiş sütunlarda *eklemeleri* ve seçimi gerçekleştiren bir uygulama oluşturabilirsiniz. Örnek uygulamayı başarılı bir şekilde çalıştırmak için, onu Always Encrypted Sihirbazı 'nı çalıştırdığınız bilgisayarda çalıştırmanız gerekir. Uygulamayı başka bir bilgisayarda çalıştırmak için, Always Encrypted sertifikalarınızı istemci uygulamasını çalıştıran bilgisayara dağıtmanız gerekir.  

> [!IMPORTANT]
> Uygulamanızın, Always Encrypted sütunları ile sunucuya düz metin verileri geçirirken [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) nesneleri kullanması gerekir. SqlParameter nesneleri kullanılmadan değişmez değerler geçirilmesi bir özel durumla sonuçlanır.

1. Visual Studio 'Yu açın ve yeni C# bir konsol uygulaması oluşturun. Projenizin **.NET Framework 4,6** veya üzeri bir sürüme ayarlandığından emin olun.
2. Projeyi **Alwaysencryptedconsoleapp** olarak adlandırın ve **Tamam**' a tıklayın.

![Yeni konsol uygulaması](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Always Encrypted etkinleştirmek için Bağlantı dizenizi değiştirin

Bu bölümde, veritabanı bağlantı dizeniz Always Encrypted nasıl etkinleştirileceği açıklanmaktadır. Yeni oluşturduğunuz konsol uygulamasını, "Always Encrypted örnek konsol uygulaması" bölümünde değiştirirsiniz.

Always Encrypted etkinleştirmek için, bağlantı dizeniz için **sütun şifreleme ayarı** anahtar sözcüğünü eklemeniz ve **etkin**olarak ayarlamanız gerekir.

Bunu doğrudan bağlantı dizesinde ayarlayabilir veya bir [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)kullanarak ayarlayabilirsiniz. Sonraki bölümde bulunan örnek uygulama, **SqlConnectionStringBuilder**'ın nasıl kullanılacağını gösterir.

> [!NOTE]
> Bu, Always Encrypted özgü bir istemci uygulamasında gerekli tek değişikdir. Bağlantı dizesini dışarıdan depolayan mevcut bir uygulamanız varsa (yani bir yapılandırma dosyasında), herhangi bir kodu değiştirmeden Always Encrypted etkinleştirebilirsiniz.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Bağlantı dizesinde Always Encrypted etkinleştir

Bağlantı dizeniz için aşağıdaki anahtar sözcüğü ekleyin:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>SqlConnectionStringBuilder ile Always Encrypted etkinleştirme

Aşağıdaki kod, [SqlConnectionStringBuilder. ColumnEncryptionSetting ayarını](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) [etkin](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx)olarak ayarlayarak always Encrypted nasıl etkinleştirileceğini gösterir.

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted örnek konsol uygulaması

Bu örnekte nasıl yapılacağı gösterilmektedir:

* Always Encrypted sağlamak için Bağlantı dizenizi değiştirin.
* Şifrelenmiş sütunlara veri ekleyin.
* Şifrelenmiş bir sütunda belirli bir değeri filtreleyerek bir kayıt seçin.

**Program.cs** içeriğini aşağıdaki kodla değiştirin. Ana yöntemin üzerindeki genel connectionString değişkeni için bağlantı dizesini, Azure portal geçerli bağlantı dizeniz ile değiştirin. Bu kodda yapmanız gereken tek değişiklik budur.

Always Encrypted eylemi görmek için uygulamayı çalıştırın.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Verilerin şifrelendiğini doğrulama

Sunucu üzerindeki gerçek verilerin, SSMS ile **hastalar** verileri sorgulanarak şifrelenip şifrelenmediğini kolayca kontrol edebilirsiniz. (Sütun şifreleme ayarının henüz etkinleştirilmediği geçerli bağlantınızı kullanın.)

Clinic veritabanında aşağıdaki sorguyu çalıştırın.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Şifrelenmiş sütunlarda herhangi bir düz metin verisi içermediğini görebilirsiniz.

   ![Yeni konsol uygulaması](./media/sql-database-always-encrypted/ssms-encrypted.png)

SSMS 'yi düz metin verilerine erişmek üzere kullanmak için, bağlantı **sütunu şifreleme ayarı = Enabled** parametresini bağlantıya ekleyebilirsiniz.

1. SSMS 'de sunucunuza **Nesne Gezgini**sağ tıklayın ve ardından **bağlantıyı kes**' e tıklayın.
2. **Sunucuya Bağlan** penceresini açmak Için > **veritabanı altyapısına** **Bağlan** ' a tıklayın ve ardından **Seçenekler**' e tıklayın.
3. **Ek bağlantı parametreleri** ve tür **sütunu şifreleme ayarı = etkin**öğesine tıklayın.

    ![Yeni konsol uygulaması](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. **Clinic** veritabanında aşağıdaki sorguyu çalıştırın.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Artık şifrelenmiş sütunlarda düz metin verileri görebilirsiniz.

    ![Yeni konsol uygulaması](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Farklı bir bilgisayardan SSMS (veya herhangi bir istemci) ile bağlanıyorsanız, şifreleme anahtarlarına erişemez ve verilerin şifresini çözemeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

Always Encrypted kullanan bir veritabanı oluşturduktan sonra şunları yapmak isteyebilirsiniz:

* Bu örneği farklı bir bilgisayardan çalıştırın. Şifreleme anahtarlarına erişemez, bu nedenle düz metin verilerine erişemez ve başarıyla çalıştırılmaz.
* [Anahtarlarınızı döndürün ve temizleyin](https://msdn.microsoft.com/library/mt607048.aspx).
* [Always encrypted ile zaten şifrelenmiş olan verileri geçirin](https://msdn.microsoft.com/library/mt621539.aspx).
* [Always Encrypted sertifikalarını diğer istemci makinelere dağıtın](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) ("sertifikaları uygulamalar ve kullanıcılar Için kullanılabilir hale getirme" bölümüne bakın).

## <a name="related-information"></a>İlgili bilgiler

* [Always Encrypted (istemci geliştirme)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Saydam Veri Şifrelemesi](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server şifreleme](https://msdn.microsoft.com/library/bb510663.aspx)
* [Always Encrypted Sihirbazı](https://msdn.microsoft.com/library/mt459280.aspx)
* [Always Encrypted blogu](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)