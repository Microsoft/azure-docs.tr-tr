---
title: 'Öğretici: SSMS kullanarak ilk ilişkisel veritabanınızı tasarlama'
description: SQL Server Management Studio kullanarak Azure SQL veritabanı 'nda tek bir veritabanında ilk ilişkisel veritabanınızı tasarlamayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.openlocfilehash: d3fecd54e36c8a3dd43c88f5aa4e4233057c3f91
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838590"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Öğretici: SSMS kullanarak Azure SQL veritabanı 'nda tek bir veritabanında ilişkisel veritabanı tasarlama

Azure SQL veritabanı, Microsoft Bulut (Azure) içindeki ilişkisel bir hizmet olarak veritabanı (DBaaS). Bu öğreticide, Azure portalını ve [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)'yu (SSMS) kullanarak şu işlemleri gerçekleştirmeyi öğreneceksiniz:

> [!div class="checklist"]
> - Azure portal * kullanarak tek bir veritabanı oluşturun
> - Azure portal kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlama
> - SSMS ile veritabanına bağlanma
> - SSMS ile tablo oluşturma
> - BCP ile toplu veri yükleme
> - SSMS ile veri sorgulama

\* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

> [!TIP]
> Aşağıdaki Microsoft Learn modülü, basit bir veritabanının oluşturulması dahil olmak üzere [Azure SQL veritabanını sorgulayan bir ASP.NET uygulamasının nasıl geliştirileceği ve yapılandırılacağı](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)hakkında bilgi edinmenize yardımcı olur.
> [!NOTE]
> Bu öğreticinin amacı doğrultusunda tek bir veritabanı kullanıyoruz. Ayrıca, bir elastik havuzda veya yönetilen bir örnekteki örnek veritabanında havuza alınmış bir veritabanı da kullanabilirsiniz. Yönetilen bir örneğe bağlantı için bkz. bu yönetilen örneğe hızlı başlangıçlara bakın: [hızlı başlangıç: Azure SQL veritabanı yönetilen örneğine bağlanmak Için Azure VM 'Yi yapılandırma](sql-database-managed-instance-configure-vm.md) ve [hızlı başlangıç: Azure SQL veritabanı 'na Noktadan siteye bağlantı yapılandırma Şirket içinden yönetilen örnek](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için yüklediğinizden emin olun:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (en son sürüm)
- [Bcp ve sqlcmd](https://www.microsoft.com/download/details.aspx?id=36433) (en son sürüm)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-blank-single-database"></a>Boş bir tek veritabanı oluşturma

Azure SQL veritabanı 'nda tek bir veritabanı, tanımlı bir dizi işlem ve depolama kaynağı ile oluşturulur. Veritabanı bir [Azure Kaynak grubu](../azure-resource-manager/resource-group-overview.md) içinde oluşturulur ve [veritabanı sunucusu](sql-database-servers.md)kullanılarak yönetilir.

Boş tek veritabanı oluşturmak için bu adımları izleyin.

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.
2. **Yeni** sayfasında, Azure Market bölümünde **Veritabanları**’nı seçin ve ardından **Öne Çıkan** bölümünde **SQL Veritabanı**’na tıklayın.

   ![create empty-database](./media/sql-database-design-first-database/create-empty-database.png)

3. **SQL veritabanı** formunu, önceki görüntüde gösterildiği gibi aşağıdaki bilgilerle doldurun:

    | Ayar       | Önerilen değer | Açıklama |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Veritabanı adı** | *yourDatabase* | Geçerli veritabanı adları için bkz. [veritabanı tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). |
    | **Abonelik** | *Aboneliğiniz*  | Abonelikleriniz hakkında daha ayrıntılı bilgi için bkz. [Abonelikler](https://account.windowsazure.com/Subscriptions). |
    | **Kaynak grubu** | *yourResourceGroup* | Geçerli kaynak grubu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Kaynak seçme** | Boş veritabanı | Boş bir veritabanı oluşturulması gerektiğini belirtir. |

4. Mevcut bir veritabanı sunucusunu kullanmak için **sunucu** ' ya tıklayın veya yeni bir veritabanı sunucusu oluşturun ve yapılandırın. Mevcut bir sunucuyu seçin ya da **Yeni sunucu oluştur ' a** tıklayın ve **Yeni sunucu** formunu aşağıdaki bilgilerle doldurun:

    | Ayar       | Önerilen değer | Açıklama |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Sunucu adı** | Genel olarak benzersiz bir ad | Geçerli sunucu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Sunucu yöneticisi oturum açma bilgileri** | Geçerli bir ad | Geçerli oturum açma adları için bkz. [veritabanı tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). |
    | **Parola** | Geçerli bir parola | Parolanız en az sekiz karakter uzunluğunda olmalı ve şu kategorilerden üçünden karakter kullanmalıdır: büyük harf karakterler, küçük harf karakterler, sayılar ve alfasayısal olmayan karakterler. |
    | **Konum** | Geçerli bir konum | Bölgeler hakkında bilgi için bkz. [Azure Bölgeleri](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. **Seç**'e tıklayın.
6. Hizmet katmanını, DTU veya sanal çekirdek sayısını ve depolama alanı miktarını belirtmek için **Fiyatlandırma katmanı**’na tıklayın. Her hizmet katmanı için kullanılabilir DTU 'lar/sanal çekirdek sayısı ve depolama seçeneklerini keşfedebilirsiniz.

    Hizmet katmanını, DTU 'Lar veya sanal çekirdekler sayısını ve depolama alanı miktarını seçtikten sonra **Uygula**' ya tıklayın.

7. Boş veritabanı için bir **harmanlama** girin (Bu öğretici için varsayılan değeri kullanın). Harmanlamalar hakkında daha fazla bilgi için bkz. [Harmanlamalar](/sql/t-sql/statements/collations)

8. **SQL veritabanı** formunu tamamladığınıza göre, tek veritabanını sağlamak için **Oluştur** ' a tıklayın. Bu adım birkaç dakika sürebilir.

9. Araç çubuğunda **Bildirimler**’e tıklayarak dağıtım işlemini izleyin.

   ![bildirim](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Sunucu düzeyinde IP güvenlik duvarı kuralı oluşturma

SQL veritabanı hizmeti, sunucu düzeyinde bir IP güvenlik duvarı oluşturur. Bu güvenlik duvarı, bir güvenlik duvarı kuralı tarafından IP 'nin güvenlik duvarından geçmesine izin verilmediği takdirde, dış uygulamaların ve araçların sunucuya ve sunucu üzerindeki veritabanlarına bağlanmasını engeller. Tek veritabanınızın dış bağlantısını etkinleştirmek için, önce IP adresiniz (veya IP adres aralığı) için bir IP güvenlik duvarı kuralı eklemeniz gerekir. [SQL veritabanı sunucu düzeyinde BIR IP güvenlik duvarı kuralı](sql-database-firewall-configure.md)oluşturmak için bu adımları izleyin.

> [!IMPORTANT]
> SQL veritabanı hizmeti 1433 bağlantı noktası üzerinden iletişim kurar. Bu hizmete bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, yöneticiniz 1433 numaralı bağlantı noktasını açmadığı takdirde tek veritabanınıza bağlanamazsınız.

1. Dağıtım tamamlandıktan sonra, Azure portal menüsünden **SQL veritabanları** ' nı seçin veya herhangi bir sayfadan *SQL veritabanlarını* arayıp seçin.  

1. **SQL veritabanları** sayfasında *YourDatabase* ' i seçin. Veritabanınızın genel bakış sayfası açılır ve tam **sunucu adı** (örneğin, `contosodatabaseserver01.database.windows.net`) görüntülenerek daha fazla yapılandırma seçeneği sunulur.

   ![sunucu adı](./media/sql-database-design-first-database/server-name.png)

1. Bu tam sunucu adını, SQL Server Management Studio sunucunuza ve veritabanlarına bağlanmak için kullanmak üzere kopyalayın.

1. Araç çubuğunda **Sunucu güvenlik duvarını ayarla**’ya tıklayın. SQL Veritabanı sunucusu için **Güvenlik duvarı ayarları** sayfası açılır.

   ![Sunucu düzeyi IP güvenlik duvarı kuralı](./media/sql-database-design-first-database/server-firewall-rule.png)

1. Geçerli IP adresinizi yeni bir IP güvenlik duvarı kuralına eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' ye tıklayın. Bir IP güvenlik duvarı kuralı, tek bir IP adresi veya IP adresi aralığı için 1433 bağlantı noktasını açabilir.

1. **Kaydet** düğmesine tıklayın. SQL veritabanı sunucusunda 1433 numaralı bağlantı noktasını açan geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

1. **Tamam**’a tıklayın ve sonra **Güvenlik duvarı ayarları** sayfasını kapatın.

IP adresiniz artık IP güvenlik duvarından geçebilirler. Artık SQL Server Management Studio veya seçtiğiniz başka bir aracı kullanarak tek veritabanınıza bağlanabilirsiniz. Daha önce oluşturduğunuz sunucu yöneticisi hesabını kullandığınızdan emin olun.

> [!IMPORTANT]
> Varsayılan olarak, SQL veritabanı IP güvenlik duvarı üzerinden erişim tüm Azure hizmetleri için etkinleştirilir. Tüm Azure hizmetleri için devre dışı bırakmak isterseniz bu sayfadaki **KAPALI** öğesine tıklayın.

## <a name="connect-to-the-database"></a>Veritabanına bağlanın

Tek veritabanınıza bağlantı kurmak için [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) kullanın.

1. SQL Server Management Studio’yu açın.
2. **Sunucuya Bağlan** iletişim kutusuna şu bilgileri girin:

   | Ayar       | Önerilen değer | Açıklama |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Sunucu türü** | Veritabanı altyapısı | Bu değer gereklidir. |
   | **Sunucu adı** | Tam sunucu adı | Örneğin, *yourserver.Database.Windows.net*. |
   | **Kimlik Doğrulaması** | SQL Server Kimlik Doğrulaması | SQL kimlik doğrulaması, bu öğreticide yapılandırdığımız tek kimlik doğrulama türüdür. |
   | **Oturum açma** | Sunucu yöneticisi hesabı | Sunucuyu oluştururken belirttiğiniz hesap. |
   | **Parola** | Sunucu yöneticisi hesabınızın parolası | Sunucuyu oluştururken belirttiğiniz parola. |

   ![sunucuya bağlan](./media/sql-database-design-first-database/connect.png)

3. **Sunucuya bağlan** iletişim kutusunda **Seçenekler**’e tıklayın. **Veritabanına Bağlan** bölümünde, bu veritabanına bağlanmak Için *YourDatabase* yazın.

    ![sunucuda veritabanına bağlanma](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. **Bağlan**'a tıklayın. **Nesne Gezgini** penceresi SSMS 'de açılır.

5. **Nesne Gezgini**' de, **veritabanları** ' nı genişletin ve ardından örnek veritabanındaki nesneleri görüntülemek için *YourDatabase* ' i genişletin.

   ![veritabanı nesneleri](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Veritabanınızda tablo oluşturma

[Transact-SQL](/sql/t-sql/language-reference) kullanarak üniversiteler için bir öğrenci yönetim sistemi modelleyen dört tablo ile bir veritabanı şeması oluşturun:

- Kişi
- Ders
- Öğrenci
- Kredisi

Aşağıdaki diyagramda bu tabloların birbirleriyle nasıl ilişkili olduğu gösterilmektedir. Bu tablolardan bazıları başka tablolardaki sütunlara başvurur. Örneğin, *öğrenci* tablosu *kişi* tablosunun *PersonID* sütununa başvurur. Bu öğreticideki tabloların birbirleriyle ilişkisini anlamak için diyagram üzerinde çalışın. Etkili veritabanı tabloları oluşturmaya ilişkin ayrıntılı bir bakış için bkz. [Etkili veritabanı tabloları oluşturma](https://msdn.microsoft.com/library/cc505842.aspx). Veri türleri seçme hakkında bilgi için bkz. [Veri türleri](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Tablolarınızı oluşturup tasarlamak için [SQL Server Management Studio’daki tablo tasarımcısını](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) da kullanabilirsiniz.

![Tablo ilişkileri](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. **Nesne Gezgini**, *veritabanına* sağ tıklayın ve **Yeni sorgu**' yı seçin. Veritabanınıza bağlı boş bir sorgu penceresi açılır.

2. Sorgu penceresinde aşağıdaki sorguyu yürüterek veritabanınızda dört tablo oluşturun:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Tablo oluşturma](./media/sql-database-design-first-database/create-tables.png)

3. Oluşturduğunuz tabloları görmek için **Nesne Gezgini** içindeki *veritabanı* altındaki **Tablolar** düğümünü genişletin.

   ![ssms tables-created](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Tablolara veri yükleme

1. Veritabanınızın örnek verilerini depolamak için Indirmeler klasörünüzde *sampleData* adlı bir klasör oluşturun.

2. Aşağıdaki bağlantılara sağ tıklayıp *sampleData* klasörüne kaydedin.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Bir komut istemi penceresi açın ve *sampleData* klasörüne gidin.

4. *Sunucu*, *veritabanı*, *Kullanıcı*ve *parola* değerlerini ortamınızın değerleriyle değiştirerek tablolara örnek veri eklemek için aşağıdaki komutları yürütün.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Daha önce oluşturduğunuz tablolara örnek veriler yüklediniz.

## <a name="query-data"></a>Verileri sorgulama

Veritabanı tablolarından bilgi almak için aşağıdaki sorguları yürütün. SQL sorguları yazma hakkında daha fazla bilgi edinmek için bkz. [SQL sorguları yazma](https://technet.microsoft.com/library/bb264565.aspx) . İlk sorgu, ' Dominick Pope ' tarafından %75 ' den yüksek bir sınıfa sahip olan öğrencileri bulmak için tüm dört tabloyu birleştirir. İkinci sorgu tüm dört tabloyu birleştirir ve ' Noe Copaman ' öğesinin şimdiye kadar kaydolduğu kursları bulur.

1. SQL Server Management Studio sorgu penceresinde aşağıdaki sorguyu yürütün:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. Bir sorgu penceresinde aşağıdaki sorguyu yürütün:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, birçok temel veritabanı görevini öğrendiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> - Tek bir veritabanı oluşturma
> - Sunucu düzeyinde IP güvenlik duvarı kuralı ayarlama
> - [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) kullanarak veritabanına bağlanma
> - Tablo oluşturma
> - Toplu veri yükleme
> - Bu verileri sorgulama

Visual Studio ve C# kullanarak veritabanı tasarlama hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure SQL veritabanı C# ve ADO.NET içinde tek bir veritabanında ilişkisel veritabanı tasarlama](sql-database-design-first-database-csharp.md)
