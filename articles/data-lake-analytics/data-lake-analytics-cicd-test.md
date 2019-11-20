---
title: Azure Data Lake Analytics kodunuzu test etme
description: U-SQL için test çalışmalarının ve Azure Data Lake Analytics için genişletilmiş C# kodun nasıl ekleneceğini öğrenin.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913955"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Azure Data Lake Analytics kodunuzu test etme

Azure Data Lake, [U-SQL](data-lake-analytics-u-sql-get-started.md) dilini sağlar. U-SQL, herhangi bir ölçekte verileri C# işlemek için BILDIRIM temelli SQL 'i kesinlik ile birleştirir. Bu belgede, U-SQL ve genişletilmiş C# Kullanıcı tanımlı operatör (Udo) kodu için test çalışmaları oluşturmayı öğreneceksiniz.

## <a name="test-u-sql-scripts"></a>U-SQL betiklerini test etme

U-SQL betiği derlenir ve çalıştırılabilir kodun Azure 'da veya yerel bilgisayarınızda çalışması için iyileştirilmiştir. Derleme ve iyileştirme işlemi, tüm U-SQL betiğini bir bütün olarak değerlendirir. Her bildiri için geleneksel birim testi yapamazsınız. Ancak, U-SQL test SDK 'sını ve yerel çalışma SDK 'sını kullanarak betik düzeyinde testler yapabilirsiniz.

### <a name="create-test-cases-for-u-sql-script"></a>U-SQL betiği için test çalışmaları oluşturma

Visual Studio için Azure Data Lake Araçları, U-SQL betik test çalışmaları oluşturmanızı sağlar.

1. Çözüm Gezgini ' de bir U-SQL komut dosyasına sağ tıklayın ve ardından **birim testi oluştur**' u seçin.

1. Yeni bir test projesi oluşturun veya var olan bir test projesine test durumunu ekleyin.

   ![Visual Studio için Data Lake araçları--U-SQL test projesi yapılandırması oluşturma](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Test veri kaynağını yönetme

U-SQL betiklerini test ettiğinizde, test giriş dosyaları gerekir. Test verilerini yönetmek için, **Çözüm Gezgini**' de, U-SQL projesine sağ tıklayın ve **Özellikler**' i seçin. **Test veri kaynağına**bir kaynak girebilirsiniz.

![Visual Studio için Data Lake araçları--proje testi veri kaynağını yapılandırma](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

U-SQL test `Initialize()` SDK 'sında arabirimini çağırdığınızda, test projesinin çalışma dizini altında geçici bir yerel veri kök klasörü oluşturulur. U-SQL betik test çalışmalarını çalıştırmadan önce, test veri kaynağı klasöründeki tüm dosyalar ve klasörler geçici yerel veri kök klasörüne kopyalanır. Test veri klasörü yolunu noktalı virgülle ayırarak daha fazla test veri kaynağı klasörü ekleyebilirsiniz.

### <a name="manage-the-database-environment-for-testing"></a>Test için veritabanı ortamını yönetme

U-SQL komut dosyalarınız U-SQL veritabanı nesneleriyle kullanıyorsa veya sorgulardaysa, U-SQL test çalışmalarını çalıştırmadan önce veritabanı ortamını başlatmalısınız. Saklı yordamlar çağrılırken bu yaklaşım gerekli olabilir. U-SQL test SDK 'sindeki arabirim,u-SQLprojesitarafındanbaşvurulantümveritabanlarını,testprojesininçalışmadizinindekigeçiciyerelverikökklasörünedağıtmanızayardımcıolur.`Initialize()`

U-SQL projesi için U-SQL veritabanı proje başvurularını yönetme hakkında daha fazla bilgi için, bkz. [u-SQL veritabanı projesine başvuru](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Test sonuçlarını doğrula

`Run()` Arabirim bir iş yürütme sonucu döndürür. *0* başarılı anlamına gelir ve *1* hata anlamına gelir. Çıkışları doğrulamak için onay C# işlevlerini de kullanabilirsiniz.

### <a name="run-test-cases-in-visual-studio"></a>Visual Studio 'da test çalışmalarını çalıştırma

Bir U-SQL betiği test projesi, C# birim test çerçevesinin üzerine kurulmuştur. Projeyi derledikten sonra, **Test** > **Windows** > **Test Gezgini**' ni seçin. Test **Gezgini**'nden test çalışmalarını çalıştırabilirsiniz. Alternatif olarak, birim testinizde. cs dosyasına sağ tıklayın ve **Testleri Çalıştır**' ı seçin.

## <a name="test-c-udos"></a>Test C# uıliği

### <a name="create-test-cases-for-c-udos"></a>Uıdos için C# test çalışmaları oluşturma

Kullanıcı tanımlı işleçlerinizi C# C# (UDOs) test etmek için bir birim test çerçevesi kullanabilirsiniz. Uıdos test edilirken, karşılık gelen **IRowset** nesnelerini giriş olarak hazırlamanız gerekir.

**IRowset** nesnesi oluşturmanın iki yolu vardır:

- **IRowset**oluşturmak için bir dosyadaki verileri yükleme:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- **IRowset**oluşturmak için bir veri koleksiyonundaki verileri kullanın:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Test sonuçlarını doğrula

UıDO işlevlerini çağırdığınızda, onaylama işlevlerini kullanarak C# şema ve satır kümesi değer doğrulama aracılığıyla sonuçları doğrulayabilirsiniz. Çözümünüze bir **U-SQL C# Udo birim testi projesi** ekleyebilirsiniz. Bunu yapmak için, Visual Studio 'da **dosya > yeni > proje** ' yi seçin.

### <a name="run-test-cases-in-visual-studio"></a>Visual Studio 'da test çalışmalarını çalıştırma

Projeyi derledikten sonra, **Test** > **Windows** > **Test Gezgini**' ni seçin. Test **Gezgini**'nden test çalışmalarını çalıştırabilirsiniz. Alternatif olarak, birim testinizde. cs dosyasına sağ tıklayın ve **Testleri Çalıştır**' ı seçin.

## Azure Pipelines 'de test çalışmalarını çalıştırma<a name="run-test-cases-in-azure-devops"></a>

Hem **U-SQL betik testi projeleri** hem  **C# de Udo test projeleri** birim testi projelerini miras C# alır. Azure Pipelines içindeki [Visual Studio test görevi](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) bu test çalışmalarını çalıştırabilir.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Azure Pipelines 'de U-SQL test çalışmalarını çalıştırma

U-SQL testi için, yapı bilgisayarınıza yüklediğinizden emin `CPPSDK` olun ve `CPPSDK` yolu ' ye `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`geçirin.

#### <a name="what-is-cppsdk"></a>CPPSDK nedir?

CPPSDK, Microsoft Visual C++ 14 ve Windows SDK 10.0.10240.0 içeren bir pakettir. Bu paket, U-SQL çalışma zamanı için gereken ortamı içerir. Bu paketi Visual Studio için Azure Data Lake Araçları yükleme klasörü altında edinebilirsiniz:

- Visual Studio 2015 için`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Visual Studio 2017 için`C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- Visual Studio 2019 için`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Azure Pipelines yapı aracısında CPPSDK hazırlama

Azure Pipelines CPPSDK bağımlılığını hazırlamanın en yaygın yolu aşağıdaki gibidir:

1. CPPSDK kitaplıklarını içeren klasörü ZIP.

1. Kaynak denetim sisteminize. zip dosyasını iade edin. . Zip dosyası, bir `.gitignore` dosya nedeniyle dosyaların yoksayılmaması için cppsdk klasörü altındaki tüm kitaplıkları iade almanızı sağlar.

1. Derleme ardışık düzeninde. zip dosyasını açın.

1. Yapı `USqlScriptTestRunner` bilgisayarında bu zip 'lenmiş bir klasöre işaret edin.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Azure Pipelines C# 'de Udo test çalışmalarını çalıştırma

Bir C# Udo testi Için, UDOs için gereken aşağıdaki derlemelere başvurduğunuzdan emin olun.

- Microsoft. Analytics. Interfaces
- Microsoft. Analytics. Types
- Microsoft. Analytics. UnitTest

Bunlara [Microsoft. Azure. DataLake. USQL. Interfaces NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)aracılığıyla başvurdıysanız, yapı ardışık düzenine bir NuGet geri yükleme görevi eklediğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Lake Analytics için CI/CD işlem hattı ayarlama](data-lake-analytics-cicd-overview.md)
- [Yerel makinenizde U-SQL betiğini çalıştırma](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL veritabanı geliştirmek için u-SQL veritabanı projesi kullanma](data-lake-analytics-data-lake-tools-develop-usql-database.md)
