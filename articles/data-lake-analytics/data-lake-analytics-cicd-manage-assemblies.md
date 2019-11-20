---
title: CI/CD ardışık düzeninde U-SQL derlemelerini yönetme-Azure Data Lake
description: Azure DevOps ile bir CI/CD işlem C# hattındaki U-SQL derlemelerini yönetmeye yönelik en iyi uygulamaları öğrenin.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: e6de10ed712688e4ee9dccc22176e81ad5e574ca
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315842"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>CI/CD ardışık düzeninde U-SQL derlemelerini yönetmeye yönelik en iyi yöntemler

Bu makalede, U-SQL derleme kaynak kodunu yeni tanıtılan U-SQL veritabanı projesiyle yönetmeyi öğreneceksiniz. Ayrıca, Azure DevOps kullanarak derleme kaydı için bir sürekli tümleştirme ve dağıtım (CI/CD) işlem hattı ayarlamayı öğreneceksiniz.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Derleme kaynak kodunu yönetmek için U-SQL veritabanı projesini kullanma

[U-SQL veritabanı projesi](data-lake-analytics-data-lake-tools-develop-usql-database.md) , Visual Studio 'da, geliştiricilerin U-SQL veritabanlarını hızlı ve kolay bir şekilde geliştirmesine, yönetmesine ve dağıtmasına yardımcı olan bir proje türüdür. U-SQL veritabanı projesi ile tüm U-SQL veritabanı nesnelerini (kimlik bilgileri hariç) yönetebilirsiniz. 

C# Derleme kaynak kodunu ve derleme kaydı DDL U-SQL betiklerini yönetmek için şunu kullanın:

* Derleme kaydı U-SQL betiklerini yönetmek için u-SQL veritabanı projesi.
* Kullanıcı tanımlı işleçler, işlevler ve aggreg'lar (UDOs, C# UDF 'Ler ve Udag 'ler) için kaynak kodu ve bağımlılıkları yönetmek üzere sınıf kitaplığı (U-SQL uygulaması için).
* Sınıf kitaplığı projesine başvurmak için U-SQL veritabanı projesi. 

Bir U-SQL veritabanı projesi, bir sınıf kitaplığına (U-SQL uygulaması Için) başvurabilir. U-SQL veritabanında kayıtlı derlemeler, bu sınıf kitaplığı 'ndan başvurulan C# kaynak kodu kullanarak (u-SQL uygulaması için) proje oluşturabilirsiniz.

Projeler oluşturmak ve başvurular eklemek için bu adımları izleyin.
1.  >  **Dosya** > yeni**Proje**'**ye**tıklayarak bir sınıf kitaplığı (U-SQL uygulaması için) projesi oluşturun. Proje **Azure Data Lake > U-SQL** düğümü altındadır.

   ![Visual Studio için Data Lake araçları--sınıf C# kitaplığı projesi oluşturma](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Kullanıcı tanımlı C# kodunuzu sınıf kitaplığı (U-SQL uygulaması için) projesine ekleyin.

1.  >  **Dosya** > yeni**Proje**'**ye**tıklayarak bir U-SQL projesi oluşturun. Proje **Azure Data Lake** > **U-SQL** düğümü altındadır.

   ![Visual Studio için Data Lake araçları--U-SQL veritabanı projesi oluşturma](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. U-SQL veritabanı projesi C# için sınıf kitaplığı projesine bir başvuru ekleyin.

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı proje başvurusu ekleme](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı proje başvurusu ekleme](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Projeye sağ tıklayıp **Yeni öğe Ekle**' yi seçerek U-SQL veritabanı projesinde bir derleme betiği oluşturun.

   ![Visual Studio için Data Lake araçları--derleme betiği Ekle](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Derleme betiğini derleme Tasarım görünümünde açın. **Başvurudan derleme oluştur** açılan menüsünden başvurulan derlemeyi seçin.

    ![Visual Studio için Data Lake araçları--derlemeden derleme oluştur](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Varsa, **yönetilen bağımlılıklar** ve **ek dosyalar**ekleyin. Ek dosyalar eklediğinizde, araç, derlemeleri yerel makinenizde ve derleme makinesinde daha sonra bulamediğinden emin olmak için göreli yolu kullanır.

En alttaki düzenleyici penceresinde bulunan _deploytempdirectory, aracı derleme çıktı klasörüne işaret eden önceden tanımlanmış bir değişkendir.  **\@** Yapı çıktı klasörü altında, her derlemenin derleme adıyla adlı bir alt klasörü vardır. Tüm dll 'Ler ve ek dosyalar bu alt klasörde bulunur.

## <a name="build-a-u-sql-database-project"></a>U-SQL veritabanı projesi oluşturma

U-SQL veritabanı projesi için derleme çıktısı bir U-SQL veritabanı dağıtım paketidir. Bu sonek `.usqldbpack`ile adlandırılır. `.usqldbpack` Paket, DDL klasöründeki tek bir U-SQL betiğinin tüm DDL deyimlerini içeren bir. zip dosyasıdır. Derleme için oluşturulan tüm. dll dosyaları ve ek dosyalar geçici klasöründedir.

## <a name="deploy-a-u-sql-database"></a>U-SQL veritabanı dağıtma

Paket `.usqldbpack` , bir yerel hesaba veya Azure Data Lake Analytics hesabına dağıtılabilir. Visual Studio 'Yu veya dağıtım SDK 'sını kullanın. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Visual Studio 'da bir U-SQL veritabanı dağıtma

Bir u-SQL veritabanı projesi ya da `.usqldbpack` Visual Studio 'da bir paket kullanarak bir u-SQL veritabanı dağıtabilirsiniz.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>U-SQL veritabanı projesi kullanarak dağıtma

1.  U-SQL veritabanı projesine sağ tıklayın ve ardından **Dağıt**' ı seçin.
2.  **U-SQL veritabanı dağıtma** sihirbazında, veritabanını dağıtmak Istediğiniz **ADLA hesabını** seçin. Hem yerel hesaplar hem de ADLA hesapları desteklenir.
3.  **Veritabanı kaynağı** otomatik olarak doldurulur. Projenin derleme çıkış klasöründeki. ustodbpack paketini işaret eder.
4.  Veritabanı oluşturmak için **veritabanı adında** bir ad girin. Hedef Azure Data Lake Analytics hesabında aynı ada sahip bir veritabanı zaten varsa, veritabanı projesinde tanımlanan tüm nesneler veritabanı yeniden oluşturulmadan oluşturulur.
5.  U-SQL veritabanını dağıtmak için **Gönder**' i seçin. Derlemeler ve ek dosyalar gibi tüm kaynaklar karşıya yüklenir. Tüm DDL deyimlerini içeren bir U-SQL işi gönderilir.

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı projesini dağıtma](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Visual Studio için Data Lake araçları--U-SQL veritabanı proje Sihirbazı 'nı dağıtma](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Azure DevOps 'da bir U-SQL veritabanı dağıtma

`PackageDeploymentTool.exe`U-SQL veritabanlarını dağıtmaya yardımcı olan programlama ve komut satırı arabirimlerini sağlar. SDK, konumunda `build/runtime/PackageDeploymentTool.exe`bulunan [U-SQL SDK NuGet paketine](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)dahildir.

Azure DevOps 'da, U-SQL veritabanı yenilemesi için bir Otomasyon işlem hattı ayarlamak üzere bir komut satırı görevi ve bu SDK kullanabilirsiniz. [SDK ve U-SQL veritabanı dağıtımı için BIR CI/CD işlem hattı ayarlama hakkında daha fazla bilgi edinin](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Analytics için CI/CD işlem hattı ayarlama](data-lake-analytics-cicd-overview.md)
* [Azure Data Lake Analytics kodunuzu test etme](data-lake-analytics-cicd-test.md)
* [Yerel makinenizde U-SQL betiğini çalıştırma](data-lake-analytics-data-lake-tools-local-run.md)
