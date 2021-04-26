---
title: CI/CD ardışık düzeninde U-SQL derlemelerini yönetme-Azure Data Lake
description: Azure DevOps ile bir CI/CD işlem hattındaki U-SQL C# derlemelerini yönetmeye yönelik en iyi yöntemleri öğrenin.
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015290"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>CI/CD ardışık düzeninde U-SQL derlemelerini yönetmeye yönelik en iyi yöntemler

Bu makalede, U-SQL derleme kaynak kodunu yeni tanıtılan U-SQL veritabanı projesiyle yönetmeyi öğreneceksiniz. Ayrıca, Azure DevOps kullanarak derleme kaydı için bir sürekli tümleştirme ve dağıtım (CI/CD) işlem hattı ayarlamayı öğreneceksiniz.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Derleme kaynak kodunu yönetmek için U-SQL veritabanı projesini kullanma

[U-SQL veritabanı projesi](data-lake-analytics-data-lake-tools-develop-usql-database.md) , Visual Studio 'da, geliştiricilerin U-SQL veritabanlarını hızlı ve kolay bir şekilde geliştirmesine, yönetmesine ve dağıtmasına yardımcı olan bir proje türüdür. U-SQL veritabanı projesi ile tüm U-SQL veritabanı nesnelerini (kimlik bilgileri hariç) yönetebilirsiniz.

C# derleme kaynak kodunu ve derleme kaydı DDL U-SQL betiklerini yönetmek için şunu kullanın:

- Derleme kaydı U-SQL betiklerini yönetmek için u-SQL veritabanı projesi.
- C# kaynak kodunu ve Kullanıcı tanımlı operatörler, işlevleri ve aggreg'leri (UDOs, UDF 'ler ve Udag 'ler) yönetmek için sınıf kitaplığı (U-SQL uygulaması Için).
- Sınıf kitaplığı projesine başvurmak için U-SQL veritabanı projesi.

Bir U-SQL veritabanı projesi, bir sınıf kitaplığına (U-SQL uygulaması Için) başvurabilir. Bu sınıf kitaplığı (U-SQL uygulaması Için) projesinde başvurulan C# kaynak kodunu kullanarak U-SQL veritabanında kayıtlı derlemeler oluşturabilirsiniz.

Projeler oluşturmak ve başvurular eklemek için bu adımları izleyin.

1. **Dosya**  >  **Yeni** proje ' ye tıklayarak bir sınıf kitaplığı (U-SQL uygulaması için) projesi oluşturun  >  . Proje **Azure Data Lake > U-SQL** düğümü altındadır.

   ![Visual Studio için Data Lake araçları--C# sınıf kitaplığı projesi oluşturma](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. Kullanıcı tanımlı C# kodunuzu sınıf kitaplığı (U-SQL uygulaması Için) projesine ekleyin.

1. **Dosya**  >  **Yeni** proje ' ye tıklayarak bir U-SQL projesi oluşturun  >  . Proje **Azure Data Lake**  >  **U-SQL** düğümü altındadır.

   ![Visual Studio için Data Lake araçları--U-SQL veritabanı projesi oluşturma](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. U-SQL veritabanı projesi için C# sınıf kitaplığı projesine bir başvuru ekleyin.

   ![Visual Studio için Data Lake araçları-başvuru Ekle](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Visual Studio için Data Lake araçları-U-SQL veritabanı proje başvurusu ekleme](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. Projeye sağ tıklayıp **Yeni öğe Ekle**' yi seçerek U-SQL veritabanı projesinde bir derleme betiği oluşturun.

   ![Visual Studio için Data Lake araçları--derleme betiği Ekle](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Derleme betiğini derleme Tasarım görünümünde açın. **Başvurudan derleme oluştur** açılan menüsünden başvurulan derlemeyi seçin.

   ![Visual Studio için Data Lake araçları--derlemeden derleme oluştur](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. Varsa, **yönetilen bağımlılıklar** ve **ek dosyalar** ekleyin. Ek dosyalar eklediğinizde, araç, derlemeleri yerel makinenizde ve derleme makinesinde daha sonra bulamediğinden emin olmak için göreli yolu kullanır.

En alttaki düzenleyici penceresinde **\@ _DeployTempDirectory** , Aracı yapı çıkış klasörüne işaret eden önceden tanımlanmış bir değişkendir. Yapı çıktı klasörü altında, her derlemenin derleme adıyla adlı bir alt klasörü vardır. Tüm dll 'Ler ve ek dosyalar bu alt klasörde bulunur.

## <a name="build-a-u-sql-database-project"></a>U-SQL veritabanı projesi oluşturma

U-SQL veritabanı projesi için derleme çıktısı bir U-SQL veritabanı dağıtım paketidir. Bu sonek ile adlandırılır `.usqldbpack` . `.usqldbpack`Paket, DDL klasöründeki tek bir U-SQL betiğinin tüm DDL deyimlerini içeren bir. zip dosyasıdır. Derleme için oluşturulan tüm. dll dosyaları ve ek dosyalar geçici klasöründedir.

## <a name="deploy-a-u-sql-database"></a>U-SQL veritabanı dağıtma

`.usqldbpack`Paket, bir yerel hesaba veya Azure Data Lake Analytics hesabına dağıtılabilir. Visual Studio 'Yu veya dağıtım SDK 'sını kullanın.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Visual Studio 'da bir U-SQL veritabanı dağıtma

Bir u-SQL veritabanı projesi ya da Visual Studio 'da bir paket kullanarak bir U-SQL veritabanı dağıtabilirsiniz `.usqldbpack` .

#### <a name="deploy-by-using-a-u-sql-database-project"></a>U-SQL veritabanı projesi kullanarak dağıtma

1. U-SQL veritabanı projesine sağ tıklayın ve ardından **Dağıt**' ı seçin.

1. **U-SQL veritabanı dağıtma** sihirbazında, veritabanını dağıtmak Istediğiniz **ADLA hesabını** seçin. Hem yerel hesaplar hem de ADLA hesapları desteklenir.

1. **Veritabanı kaynağı** otomatik olarak doldurulur. Projenin derleme çıkış klasöründeki. ustodbpack paketini işaret eder.

1. Veritabanı oluşturmak için **veritabanı adında** bir ad girin. Hedef Azure Data Lake Analytics hesabında aynı ada sahip bir veritabanı zaten varsa, veritabanı projesinde tanımlanan tüm nesneler veritabanı yeniden oluşturulmadan oluşturulur.

1. U-SQL veritabanını dağıtmak için **Gönder**' i seçin. Derlemeler ve ek dosyalar gibi tüm kaynaklar karşıya yüklenir. Tüm DDL deyimlerini içeren bir U-SQL işi gönderilir.

   ![Visual Studio için Data Lake araçları--U-SQL veritabanı projesini dağıtma](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![Visual Studio için Data Lake araçları--U-SQL veritabanı proje Sihirbazı 'nı dağıtma](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Azure DevOps 'da bir U-SQL veritabanı dağıtma

`PackageDeploymentTool.exe` U-SQL veritabanlarını dağıtmaya yardımcı olan programlama ve komut satırı arabirimlerini sağlar. SDK, konumunda bulunan [U-SQL SDK NuGet paketine](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)dahildir `build/runtime/PackageDeploymentTool.exe` .

Azure DevOps 'da, U-SQL veritabanı yenilemesi için bir Otomasyon işlem hattı ayarlamak üzere bir komut satırı görevi ve bu SDK kullanabilirsiniz. [SDK ve U-SQL veritabanı dağıtımı için BIR CI/CD işlem hattı ayarlama hakkında daha fazla bilgi edinin](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Lake Analytics için CI/CD işlem hattı ayarlama](data-lake-analytics-cicd-overview.md)
- [Azure Data Lake Analytics kodunuzu test etme](data-lake-analytics-cicd-test.md)
- [Yerel makinenizde U-SQL betiğini çalıştırma](data-lake-analytics-data-lake-tools-local-run.md)
