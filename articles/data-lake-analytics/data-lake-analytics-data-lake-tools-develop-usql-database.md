---
title: U-SQL veritabanı projesi geliştirme-Azure Data Lake
description: Visual Studio için Azure Data Lake Araçları kullanarak U-SQL veritabanı geliştirmeyi öğrenin.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: a9b271b5f7d4e53dbf871d03dd43b62b9299aa53
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309919"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>U-SQL veritabanı projesi kullanarak Azure Data Lake için bir U-SQL veritabanı geliştirin

U-SQL veritabanı, tablolarda yapılandırılmamış veriler ve yönetilen yapılandırılmış veriler üzerinde yapılandırılmış görünümler sağlar. Ayrıca, yapılandırılmış verilerinizi ve özel kodunuzu düzenlemek için genel bir meta veri kataloğu sistemi sağlar. Veritabanı, bu ilgili nesneleri birlikte gruplandıran kavramdır.

[U-SQL veritabanı ve veri tanımlama dili (ddl)](/u-sql/data-definition-language-ddl-statements)hakkında daha fazla bilgi edinin. 

U-SQL veritabanı projesi, Visual Studio 'da, geliştiricilerin U-SQL veritabanlarını hızlı ve kolay bir şekilde geliştirmesine, yönetmesine ve dağıtmasına yardımcı olan bir proje türüdür.

## <a name="create-a-u-sql-database-project"></a>U-SQL veritabanı projesi oluşturma

Visual Studio için Azure Data Lake Araçları sürüm 2.3.3000.0 sonra U-SQL veritabanı projesi adlı yeni bir proje şablonu ekledi. U-SQL projesi oluşturmak için **dosya > yeni > proje**' yi seçin. U-SQL veritabanı projesi **Azure Data Lake > u-SQL düğümü**altında bulunabilir.

![Visual Studio için Data Lake araçları--U-SQL veritabanı projesi oluşturma](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Veritabanı projesi kullanarak U-SQL veritabanı nesneleri geliştirme

U-SQL veritabanı projesine sağ tıklayın. **> Ekle yeni öğe**' yi seçin. Yeni **öğe ekleme** Sihirbazı ' nda desteklenen tüm yeni nesne türlerini bulabilirsiniz. 

Derleme olmayan bir nesne (örneğin, tablo değerli bir işlev) için yeni bir öğe ekledikten sonra yeni bir U-SQL betiği oluşturulur. Düzenleyicide bu nesne için DDL bildirisini geliştirmeye başlayabilirsiniz.

Bir derleme nesnesi için araç, derlemeyi kaydetmenize ve DLL dosyalarını ve diğer ek dosyaları dağıtmanıza yardımcı olan Kullanıcı dostu bir UI Düzenleyicisi sağlar. Aşağıdaki adımlarda U-SQL veritabanı projesine bir derleme nesnesi tanımının nasıl ekleneceği gösterilmektedir:

1.  U-SQL veritabanı C# projesi için uıdo/UDAG/UDF 'yi içeren projeye başvurular ekleyin.

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı proje başvurusu ekleme](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı proje başvurusu ekleme](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  Derleme tasarımı görünümünde, **başvuruya ait derleme oluştur** açılan menüsünden başvurulan derlemeyi seçin.

    ![Visual Studio için Data Lake araçları--derlemeden derleme oluştur](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  **Yönetilen bağımlılıklar** ve varsa **ek dosyalar** ekleyin. Ek dosyalar eklediğinizde araç, derlemeleri hem yerel makinenizde hem de derleme makinesinde daha sonra bulamediğinden emin olmak için göreli yolu kullanır. 

@_DeployTempDirectory, Aracı yapı çıkış klasörüne işaret eden önceden tanımlanmış bir değişkendir. Yapı çıktı klasörü altında, her derlemenin derleme adıyla adlı bir alt klasörü vardır. Tüm dll 'Ler ve ek dosyalar bu alt klasörde bulunur. 
 
## <a name="build-a-u-sql-database-project"></a>U-SQL veritabanı projesi oluşturma

U-SQL veritabanı projesi için derleme çıktısı, son ek `.usqldbpack`Ile adlandırılmış BIR u-SQL veritabanı dağıtım paketidir. Paket, DDL klasöründeki tek bir U-SQL betiğinin yanı sıra tüm dll 'leri ve **geçici** klasördeki derlemeler için ek dosyaları içeren bir. zip dosyasıdır. `.usqldbpack`

[MSBuild komut satırı ve bir Azure DevOps Services derleme göreviyle bir U-SQL veritabanı projesi oluşturma](data-lake-analytics-cicd-overview.md)hakkında daha fazla bilgi edinin.

## <a name="deploy-a-u-sql-database"></a>U-SQL veritabanı dağıtma

. Ustodbpack paketi, Visual Studio veya Deployment SDK kullanarak bir yerel hesaba veya bir Azure Data Lake Analytics hesabına dağıtılabilir. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Visual Studio 'da bir U-SQL veritabanı dağıtma

Visual Studio 'da bir U-SQL veritabanı projesi veya. ustodbpack paketi aracılığıyla bir U-SQL veritabanı dağıtabilirsiniz.

#### <a name="deploy-through-a-u-sql-database-project"></a>U-SQL veritabanı projesi aracılığıyla dağıtma

1.  U-SQL veritabanı projesine sağ tıklayın ve ardından **Dağıt**' ı seçin.
2.  **U-SQL veritabanı dağıtma sihirbazında**, veritabanını dağıtmak istediğiniz **ADLA hesabını** seçin. Hem yerel hesaplar hem de ADLA hesapları desteklenir.
3.  **Veritabanı kaynağı** otomatik olarak doldurulur ve projenin derleme çıkış klasöründeki. ustodbpack paketine işaret eder.
4.  Veritabanı oluşturmak için **veritabanı adında** bir ad girin. Hedef Azure Data Lake Analytics hesabında aynı ada sahip bir veritabanı zaten varsa, veritabanı projesinde tanımlanan tüm nesneler veritabanını yeniden oluşturmadan oluşturulur.
5.  U-SQL veritabanını dağıtmak için **Gönder**' i seçin. Tüm kaynaklar (derlemeler ve ek dosyalar) karşıya yüklenir ve tüm DDL deyimlerini içeren bir U-SQL işi gönderilir.

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı projesini dağıtma](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı proje Sihirbazı 'nı dağıtma](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>U-SQL veritabanı dağıtım paketi aracılığıyla dağıtma

1.  **Sunucu Gezgini**açın. Sonra veritabanını dağıtmak istediğiniz **Azure Data Lake Analytics hesabını** genişletin.
2.  **U-SQL veritabanları**' na sağ tıklayın ve ardından **veritabanını dağıt**' ı seçin.
3.  **Veritabanı kaynağını** U-SQL veritabanı dağıtım paketi (. ustodbpack dosyası) yoluna ayarlayın.
4.  Veritabanı oluşturmak için **veritabanı adını** girin. Hedef Azure Data Lake Analytics hesabında zaten aynı ada sahip bir veritabanı varsa, veritabanı projesinde tanımlanan tüm nesneler veritabanını yeniden oluşturmadan oluşturulur.

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı paketini dağıtma](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı paketi dağıtma Sihirbazı](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>SDK kullanarak U-SQL veritabanı dağıtma

`PackageDeploymentTool.exe`U-SQL veritabanlarını dağıtmaya yardımcı olan programlama ve komut satırı arabirimlerini sağlar. SDK, konumunda `build/runtime/PackageDeploymentTool.exe`bulunan [U-SQL SDK NuGet paketine](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)dahildir.

[SDK ve U-SQL veritabanı dağıtımı IÇIN CI/CD işlem hattı ayarlama hakkında daha fazla bilgi edinin](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Bir U-SQL veritabanı projesine başvurma

U-SQL projesi bir U-SQL veritabanı projesine başvurabilir. Başvuru iki iş yükünü etkiler:

- *Proje derlemesi*: U-SQL betiklerini oluşturmadan önce başvurulan veritabanı ortamlarını ayarlayın. 
- *Yerel çalıştırma (yerel-proje) hesabı*: Başvurulan veritabanı ortamları, U-SQL betiği yürütmeden önce (bir yerel-proje) hesabına dağıtılır. [Burada yerel çalıştırmalar ve (yerel makine) ve (yerel-proje) hesabı arasındaki fark hakkında daha fazla bilgi edinin](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>U-SQL veritabanı başvurusu ekleme

1. **Çözüm Gezgini**' de u-SQL projesine sağ tıklayın ve ardından **u-SQL veritabanı başvurusu Ekle...** ' yi seçin.

    ![Visual Studio için Data Lake araçları--veritabanı proje başvurusu ekleme](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Geçerli çözümde veya U-SQL veritabanı paket dosyasında bir U-SQL veritabanı projesinden bir veritabanı başvurusu yapılandırın.
3. Veritabanının adını belirtin.

    ![Visual Studio için Data Lake araçları proje başvurusu ekleme Sihirbazı](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Lake Analytics için CI/CD işlem hattı ayarlama](data-lake-analytics-cicd-overview.md)
- [Azure Data Lake Analytics kodunuzu test etme](data-lake-analytics-cicd-test.md)
- [Yerel makinenizde U-SQL betiğini çalıştırma](data-lake-analytics-data-lake-tools-local-run.md)
