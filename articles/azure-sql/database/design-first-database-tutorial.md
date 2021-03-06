---
title: 'Öğretici: SSMS kullanarak ilk ilişkisel veritabanınızı tasarlama'
description: SQL Server Management Studio kullanarak Azure SQL veritabanı 'nda ilk ilişkisel veritabanınızı tasarlamayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: ae7baeac6cee2a692928642e3e38ce0adad17d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674886"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>Öğretici: SSMS kullanarak Azure SQL veritabanı 'nda ilişkisel veritabanı tasarlama
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL veritabanı, Microsoft Bulut (Azure) içindeki ilişkisel bir hizmet olarak veritabanı (DBaaS). Bu öğreticide, Azure portalını ve [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)'yu (SSMS) kullanarak şu işlemleri gerçekleştirmeyi öğreneceksiniz:

> [!div class="checklist"]
>
> - Azure portal * kullanarak veritabanı oluşturma
> - Azure portal kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlama
> - SSMS ile veritabanına bağlanma
> - SSMS ile tablo oluşturma
> - BCP ile toplu veri yükleme
> - SSMS ile veri sorgulama

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

> [!TIP]
> Aşağıdaki Microsoft Learn modülü, basit bir veritabanının oluşturulması dahil olmak üzere [Azure SQL veritabanını sorgulayan bir ASP.NET uygulamasının nasıl geliştirileceği ve yapılandırılacağı](/learn/modules/develop-app-that-queries-azure-sql/)hakkında bilgi edinmenize yardımcı olur.
> [!NOTE]
> Bu öğreticinin amacı doğrultusunda Azure SQL veritabanı 'nı kullanıyoruz. Ayrıca, bir elastik havuzda veya bir SQL yönetilen örneğinde havuza alınmış bir veritabanı da kullanabilirsiniz. SQL yönetilen örneği ile bağlantı için, şu SQL yönetilen örneği hızlı başlangıçlara bakın: [hızlı başlangıç: Azure SQL yönetilen örneğine bağlanmak Için Azure VM 'Yi yapılandırma](../managed-instance/connect-vm-instance-configure.md) ve [hızlı başlangıç: Şirket IÇINDEN bir Azure SQL yönetilen örneğine Noktadan siteye bağlantı yapılandırma](../managed-instance/point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için yüklediğinizden emin olun:

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (en son sürüm)
- [Bcp ve sqlcmd](https://www.microsoft.com/download/details.aspx?id=36433) (en son sürüm)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda boş bir veritabanı oluşturma

Azure SQL veritabanı 'ndaki bir veritabanı, tanımlı bir dizi işlem ve depolama kaynağı ile oluşturulur. Veritabanı bir [Azure Kaynak grubu](../../active-directory-b2c/overview.md) içinde oluşturulur ve [mantıksal bir SQL Server](logical-servers.md)kullanılarak yönetilir.

Boş bir veritabanı oluşturmak için bu adımları izleyin.

1. Azure portal menüsünde veya **Giriş** sayfasında, **Kaynak oluştur**’u seçin.
2. **Yeni** sayfasında, Azure Market bölümünde **Veritabanları**’nı seçin ve ardından **Öne Çıkan** bölümünde **SQL Veritabanı**’na tıklayın.

   ![create empty-database](./media/design-first-database-tutorial/create-empty-database.png)

3. **SQL veritabanı** formunu, önceki görüntüde gösterildiği gibi aşağıdaki bilgilerle doldurun:

    | Ayar       | Önerilen değer | Açıklama |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Veritabanı adı** | *yourDatabase* | Geçerli veritabanı adları için bkz. [veritabanı tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). |
    | **Abonelik** | *Aboneliğiniz*  | Abonelikleriniz hakkında daha ayrıntılı bilgi için bkz. [Abonelikler](https://account.windowsazure.com/Subscriptions). |
    | **Kaynak grubu** | *yourResourceGroup* | Geçerli kaynak grubu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Kaynak seçme** | Boş veritabanı | Boş bir veritabanı oluşturulması gerektiğini belirtir. |

4. Var olan bir sunucuyu kullanmak için **sunucu** ' ya tıklayın veya yeni bir sunucu oluşturun ve yapılandırın. Mevcut bir sunucuyu seçin ya da **Yeni sunucu oluştur ' a** tıklayın ve **Yeni sunucu** formunu aşağıdaki bilgilerle doldurun:

    | Ayar       | Önerilen değer | Açıklama |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Sunucu adı** | Genel olarak benzersiz bir ad | Geçerli sunucu adları için bkz. [Adlandırma kuralları ve kısıtlamalar](/azure/architecture/best-practices/resource-naming). |
    | **Sunucu yöneticisi oturum açma bilgileri** | Geçerli bir ad | Geçerli oturum açma adları için bkz. [veritabanı tanımlayıcıları](/sql/relational-databases/databases/database-identifiers). |
    | **Parola** | Geçerli bir parola | Parolanız en az sekiz karakter uzunluğunda olmalı ve şu kategorilerden üçünden karakter kullanmalıdır: büyük harf karakterler, küçük harf karakterler, sayılar ve alfasayısal olmayan karakterler. |
    | **Konum** | Geçerli bir konum | Bölgeler hakkında bilgi için bkz. [Azure Bölgeleri](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/design-first-database-tutorial/create-database-server.png)

5. **Seç**’e tıklayın.
6. Hizmet katmanını, DTU veya sanal çekirdek sayısını ve depolama alanı miktarını belirtmek için **Fiyatlandırma katmanı**’na tıklayın. Her hizmet katmanı için kullanılabilir DTU 'lar/sanal çekirdek sayısı ve depolama seçeneklerini keşfedebilirsiniz.

    Hizmet katmanını, DTU 'Lar veya sanal çekirdekler sayısını ve depolama alanı miktarını seçtikten sonra **Uygula**' ya tıklayın.

7. Boş veritabanı için bir **harmanlama** girin (Bu öğretici için varsayılan değeri kullanın). Harmanlamalar hakkında daha fazla bilgi için bkz. [Harmanlamalar](/sql/t-sql/statements/collations)

8. **SQL veritabanı** formunu tamamladığınıza göre, veritabanını sağlamak için **Oluştur** ' a tıklayın. Bu adım birkaç dakika sürebilir.

9. Araç çubuğunda **Bildirimler**’e tıklayarak dağıtım işlemini izleyin.

   ![Ekran görüntüsü, dağıtım devam etmekte olan bildirimler menüsünü gösterir.](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Sunucu düzeyinde IP güvenlik duvarı kuralı oluşturma

Azure SQL veritabanı, sunucu düzeyinde bir IP güvenlik duvarı oluşturur. Bu güvenlik duvarı, bir güvenlik duvarı kuralı tarafından IP 'nin güvenlik duvarından geçmesine izin verilmediği takdirde, dış uygulamaların ve araçların sunucuya ve sunucu üzerindeki veritabanlarına bağlanmasını engeller. Veritabanınıza dış bağlantıyı etkinleştirmek için, önce IP adresiniz (veya IP adres aralığı) için bir IP güvenlik duvarı kuralı eklemeniz gerekir. [Sunucu düzeyinde BIR IP güvenlik duvarı kuralı](firewall-configure.md)oluşturmak için bu adımları izleyin.

> [!IMPORTANT]
> Azure SQL Veritabanı, 1433 numaralı bağlantı noktası üzerinden iletişim kurar. Bu hizmete bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, yöneticiniz 1433 numaralı bağlantı noktasını açmadığı takdirde veritabanınıza bağlanamazsınız.

1. Dağıtım tamamlandıktan sonra, Azure portal menüsünden **SQL veritabanları** ' nı seçin veya herhangi bir sayfadan *SQL veritabanlarını* arayıp seçin.  

1. **SQL veritabanları** sayfasında *YourDatabase* ' i seçin. Veritabanınızın genel bakış sayfası açılır ve tam **sunucu adı** (örneğin, `contosodatabaseserver01.database.windows.net` ) görüntülenerek daha fazla yapılandırma seçeneği sunulur.

   ![sunucu adı](./media/design-first-database-tutorial/server-name.png)

1. Bu tam sunucu adını, SQL Server Management Studio sunucunuza ve veritabanlarına bağlanmak için kullanmak üzere kopyalayın.

1. Araç çubuğunda **Sunucu güvenlik duvarını ayarla**’ya tıklayın. Sunucu için **güvenlik duvarı ayarları** sayfası açılır.

   ![Sunucu düzeyi IP güvenlik duvarı kuralı](./media/design-first-database-tutorial/server-firewall-rule.png)

1. Geçerli IP adresinizi yeni bir IP güvenlik duvarı kuralına eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' ye tıklayın. Bir IP güvenlik duvarı kuralı, tek bir IP adresi veya IP adresi aralığı için 1433 bağlantı noktasını açabilir.

1. **Kaydet**’e tıklayın. Sunucuda 1433 numaralı bağlantı noktasını açan geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

1. **Tamam**’a tıklayın ve sonra **Güvenlik duvarı ayarları** sayfasını kapatın.

IP adresiniz artık IP güvenlik duvarından geçebilirler. Artık SQL Server Management Studio veya seçtiğiniz başka bir aracı kullanarak veritabanınıza bağlanabilirsiniz. Daha önce oluşturduğunuz sunucu yöneticisi hesabını kullandığınızdan emin olun.

> [!IMPORTANT]
> Varsayılan olarak, SQL veritabanı IP güvenlik duvarı üzerinden erişim tüm Azure hizmetleri için etkinleştirilir. Tüm Azure hizmetleri için devre dışı bırakmak isterseniz bu sayfadaki **KAPALI** öğesine tıklayın.

## <a name="connect-to-the-database"></a>Veritabanına bağlanın

Veritabanınıza bağlantı kurmak için [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) kullanın.

1. SQL Server Management Studio'yu açın.
2. **Sunucuya Bağlan** iletişim kutusuna şu bilgileri girin:

   | Ayar       | Önerilen değer | Açıklama |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Sunucu türü** | Veritabanı altyapısı | Bu değer gereklidir. |
   | **Sunucu adı** | Tam sunucu adı | Örneğin, *yourserver.Database.Windows.net*. |
   | **Kimlik Doğrulaması** | SQL Server Kimlik Doğrulaması | SQL kimlik doğrulaması, bu öğreticide yapılandırdığımız tek kimlik doğrulama türüdür. |
   | **Oturum aç** | Sunucu yöneticisi hesabı | Sunucuyu oluştururken belirttiğiniz hesap. |
   | **Parola** | Sunucu yöneticisi hesabınızın parolası | Sunucuyu oluştururken belirttiğiniz parola. |

   ![sunucuya bağlan](./media/design-first-database-tutorial/connect.png)

3. **Sunucuya bağlan** iletişim kutusunda **Seçenekler**’e tıklayın. **Veritabanına Bağlan** bölümünde, bu veritabanına bağlanmak Için *YourDatabase* yazın.

    ![sunucuda veritabanına bağlanma](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. **Bağlan**'a tıklayın. **Nesne Gezgini** penceresi SSMS 'de açılır.

5. **Nesne Gezgini**' de, **veritabanları** ' nı genişletin ve ardından örnek veritabanındaki nesneleri görüntülemek için *YourDatabase* ' i genişletin.

   ![veritabanı nesneleri](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>Veritabanınızda tablo oluşturma

[Transact-SQL](/sql/t-sql/language-reference) kullanarak üniversiteler için bir öğrenci yönetim sistemi modelleyen dört tablo ile bir veritabanı şeması oluşturun:

- Kişi
- Kurs
- Öğrenci
- Kredi

Aşağıdaki diyagramda bu tabloların birbirleriyle nasıl ilişkili olduğu gösterilmektedir. Bu tablolardan bazıları başka tablolardaki sütunlara başvurur. Örneğin, *öğrenci* tablosu *kişi* tablosunun *PersonID* sütununa başvurur. Bu öğreticideki tabloların birbirleriyle ilişkisini anlamak için diyagram üzerinde çalışın. Etkili veritabanı tabloları oluşturmaya ilişkin ayrıntılı bir bakış için bkz. [Etkili veritabanı tabloları oluşturma](/previous-versions/tn-archive/cc505842(v=technet.10)). Veri türleri seçme hakkında bilgi için bkz. [Veri türleri](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Tablolarınızı oluşturup tasarlamak için [SQL Server Management Studio’daki tablo tasarımcısını](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) da kullanabilirsiniz.

![Tablo ilişkileri](./media/design-first-database-tutorial/tutorial-database-tables.png)

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

   ![Tablo oluşturma](./media/design-first-database-tutorial/create-tables.png)

3. Oluşturduğunuz tabloları görmek için **Nesne Gezgini** içindeki *veritabanı* altındaki **Tablolar** düğümünü genişletin.

   ![ssms tables-created](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Tablolara veri yükleme

1. Veritabanınızın örnek verilerini depolamak için Indirmeler klasörünüzde *sampleData* adlı bir klasör oluşturun.

2. Aşağıdaki bağlantılara sağ tıklayıp *sampleData* klasörüne kaydedin.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Bir komut istemi penceresi açın ve *sampleData* klasörüne gidin.

4. *Sunucu*, *veritabanı*, *Kullanıcı* ve *parola* değerlerini ortamınızın değerleriyle değiştirerek tablolara örnek veri eklemek için aşağıdaki komutları yürütün.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Daha önce oluşturduğunuz tablolara örnek veriler yüklediniz.

## <a name="query-data"></a>Verileri sorgulama

Veritabanı tablolarından bilgi almak için aşağıdaki sorguları yürütün. SQL sorguları yazma hakkında daha fazla bilgi edinmek için bkz. [SQL sorguları yazma](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)) . İlk sorgu, ' Dominick Pope ' tarafından %75 ' den yüksek bir sınıfa sahip olan öğrencileri bulmak için tüm dört tabloyu birleştirir. İkinci sorgu tüm dört tabloyu birleştirir ve ' Noe Copaman ' öğesinin şimdiye kadar kaydolduğu kursları bulur.

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
>
> - Azure portal * kullanarak veritabanı oluşturma
> - Azure portal kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlama
> - SSMS ile veritabanına bağlanma
> - SSMS ile tablo oluşturma
> - BCP ile toplu veri yükleme
> - SSMS ile veri sorgulama

Visual Studio ve C# kullanarak veritabanı tasarlama hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure SQL veritabanı C# ve ADO.NET içinde ilişkisel veritabanı tasarlama](design-first-database-csharp-tutorial.md)