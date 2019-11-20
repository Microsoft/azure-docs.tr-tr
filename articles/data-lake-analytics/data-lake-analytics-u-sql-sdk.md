---
title: U-SQL işlerini yerel olarak çalıştırma-Azure Data Lake U-SQL SDK
description: Yerel iş istasyonunuzda komut satırını ve programlama arabirimlerini kullanarak U-SQL işlerini yerel olarak çalıştırmayı ve test yapmayı öğrenin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 51d9060eaf4b30c696ef2a3b5f798a31e2f2a98a
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309690"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK ile U-SQL çalıştırma ve test etme

U-SQL betiği geliştirirken, buluta göndermeden önce U-SQL betiğini yerel olarak çalıştırmak ve test etmek yaygındır. Azure Data Lake, bu senaryo için u-SQL SDK Azure Data Lake adlı bir NuGet paketi sağlar ve bu sayede U-SQL çalıştırmayı ve testini kolayca ölçeklendirebilirsiniz. Derlemeyi ve testi otomatikleştirmek için bu U-SQL testini CI (sürekli tümleştirme) sistemiyle tümleştirmek da mümkündür.

GUI araçları ile el ile yerel çalıştırma ve hata ayıklama U-SQL betiği hakkında daha fazla bilgi için, Visual Studio için Azure Data Lake Araçları kullanabilirsiniz. [Buradan](data-lake-analytics-data-lake-tools-local-run.md)daha fazla bilgi edinebilirsiniz.

## <a name="install-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK 'sını yükler

Azure Data Lake U- [SQL SDK 'sını](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet.org üzerinde edinebilirsiniz. Kullanmadan önce, aşağıdaki gibi bağımlılıklara sahip olduğunuzdan emin olmanız gerekir.

### <a name="dependencies"></a>Bağımlılıkları

Data Lake U-SQL SDK aşağıdaki bağımlılıkları gerektirir:

- [Microsoft .NET Framework 4,6 veya sonraki bir sürümü](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 ve Windows SDK 10.0.10240.0 veya üzeri (Bu makalede CppSDK adı verilir). CppSDK 'yı almanın iki yolu vardır:

  - [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)'ı yükleyin. Program Files klasörü altında bir \Windows Kits\10 klasörü (örneğin, C:\Program Files (x86) \Windows Kits\10\olacak. \Windows Kits\10\libaltında Windows 10 SDK sürümünü de bulacaksınız. Bu klasörleri görmüyorsanız, Visual Studio 'Yu yeniden yükleyin ve yükleme sırasında Windows 10 SDK 'sını seçtiğinizden emin olun. Bu Visual Studio ile birlikte yüklüyse, U-SQL yerel derleyicisi onu otomatik olarak bulur.

    ![Visual Studio yerel için Data Lake araçları-Windows 10 SDK çalıştırma](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - [Visual Studio için Data Lake araçları](https://aka.ms/adltoolsvs)'nı yükler. Önceden paketlenmiş görseli C++ ve Windows SDK dosyalarını C:\Program Files (x86) \Microsoft Visual Studio 14.0 \ Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.xxxx.X\CppSDK. adresinde bulabilirsiniz. Bu durumda, U-SQL yerel derleyicisi bağımlılıkları otomatik olarak bulamaz. CppSDK yolunu belirtmeniz gerekir. Dosyaları başka bir konuma kopyalayabilir ya da olduğu gibi kullanabilirsiniz.

## <a name="understand-basic-concepts"></a>Temel kavramları anlama

### <a name="data-root"></a>Veri kökü

Veri kök klasörü, yerel işlem hesabı için bir "yerel depodır". Bir Data Lake Analytics hesabının Azure Data Lake Store hesabı ile eşdeğerdir. Farklı bir veri kök klasörüne geçiş yapmak, farklı bir mağaza hesabına geçmek gibidir. Farklı veri kök klasörleriyle yaygın olarak paylaşılan verilere erişmek istiyorsanız betiklerinizde mutlak yollar kullanmanız gerekir. Ya da, paylaşılan verilere işaret etmek için veri kök klasörü altında dosya sistemi sembolik bağlantıları (örneğin, NTFS üzerinde **mklink** ) oluşturun.

Veri kökü klasörü şu şekilde kullanılır:

- Veritabanları, tablolar, tablo değerli işlevler (TVFs) ve derlemeler dahil olmak üzere yerel meta verileri depolayın.
- U-SQL içinde göreli yollar olarak tanımlanan giriş ve çıkış yollarını bulun. Göreli yolların kullanılması, U-SQL projelerinizi Azure 'a dağıtmayı kolaylaştırır.

### <a name="file-path-in-u-sql"></a>U-SQL içindeki dosya yolu

U-SQL betiklerine hem göreli bir yol hem de yerel mutlak yol kullanabilirsiniz. Göreli yol, belirtilen veri kök klasör yolu ile ilişkilidir. Komut dosyalarınızı sunucu tarafı ile uyumlu hale getirmek için yol ayırıcısı olarak "/" kullanmanızı öneririz. Göreli yolların ve bunlara eşdeğer mutlak yolların bazı örnekleri aşağıda verilmiştir. Bu örneklerde, C:\LocalRunDataRoot, Data-root klasörüdür.

|Göreli yol|Mutlak yol|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input. csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input. csv |D:\abc\def\ınput.exe|

### <a name="working-directory"></a>Çalışma dizini

U-SQL betiğini yerel olarak çalıştırırken, geçerli çalışan dizin altında derleme sırasında çalışma dizini oluşturulur. Derleme çıktılarına ek olarak, yerel yürütmeye yönelik gerekli çalışma zamanı dosyaları bu çalışma dizinine gölge olarak kopyalanacaktır. Çalışma dizini kök klasörüne "ScopeWorkDir" adı verilir ve çalışma dizini altındaki dosyalar aşağıdaki gibidir:

|Dizin/dosya|Dizin/dosya|Dizin/dosya|Tanım|Açıklama|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Çalışma zamanı sürümünün karma dizesi|Yerel yürütme için gereken çalışma zamanı dosyalarının gölge kopyası|
| |Script_66AE4909AA0ED06C| |Betik adı + betik yolunun karma dizesi|Derleme çıktıları ve yürütme adımı günlüğü|
| | |\_Script\_. ABR|Derleyici çıkışı|Algeköşeli dosyası|
| | |\_ScopeCodeGen\_.*|Derleyici çıkışı|Oluşturulan yönetilen kod|
| | |\_ScopeCodeGenEngine\_. *|Derleyici çıkışı|Oluşturulan yerel kod|
| | |başvurulan derlemeler|Bütünleştirilmiş kod başvurusu|Başvurulan derleme dosyaları|
| | |deployed_resources|Kaynak dağıtımı|Kaynak dağıtım dosyaları|
| | |xxxxxxxx. XXX [1.. n]\_\*. *|Yürütme günlüğü|Yürütme adımlarının günlüğü|


## <a name="use-the-sdk-from-the-command-line"></a>Komut satırından SDK 'Yı kullanma

### <a name="command-line-interface-of-the-helper-application"></a>Yardımcı uygulamanın komut satırı arabirimi

SDK directory\build\runtime altında, LocalRunHelper. exe, yaygın olarak kullanılan yerel çalıştırma işlevlerinin çoğuna arabirimler sağlayan komut satırı yardımcı uygulamasıdır. Hem komut hem de bağımsız değişken anahtarlarının büyük/küçük harfe duyarlı olduğunu unutmayın. Çağırmak için:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

LocalRunHelper. exe ' yi bağımsız değişkenler olmadan veya **Yardım anahtarı ile birlikte çalıştırarak yardım bilgilerini** görüntüleyin:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Yardım bilgileri:

-  **Komut** , komutun adını verir.  
-  **Gerekli bağımsız değişken** , sağlanması gereken bağımsız değişkenleri listeler.  
-  **Isteğe bağlı bağımsız değişken** , varsayılan değerler ile isteğe bağlı bağımsız değişkenleri listeler.  İsteğe bağlı Boole bağımsız değişkenlerinin parametreleri yoktur ve görünümleri varsayılan değerlerine olumsuz anlamına gelir.

### <a name="return-value-and-logging"></a>Dönüş değeri ve günlüğü

Yardımcı uygulama başarılı için **0** ve hata için **-1** döndürür. Varsayılan olarak, yardımcı tüm iletileri geçerli konsola gönderir. Ancak komutların çoğu, çıkışları bir günlük dosyasına yönlendiren **-messageout path_to_log_file** isteğe bağlı bağımsız değişkenini destekler.

### <a name="environment-variable-configuring"></a>Ortam değişkeni yapılandırılıyor

U-SQL yerel çalışmasının, yerel depolama hesabı olarak belirtilen bir veri köküne ve Bağımlılıklar için belirtilen CppSDK yoluna ihtiyacı vardır. Bağımsız değişkenini her ikisi de komut satırında ayarlayabilir veya ortam değişkenini bu şekilde ayarlayabilirsiniz.

- **SCOPE_CPP_SDK** ortam değişkenini ayarlayın.

    Visual Studio için Data Lake araçları C++ 'Nı yükleyerek Microsoft Visual ve Windows SDK alırsanız aşağıdaki klasöre sahip olduğunuzdan emin olun:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Bu dizine işaret etmek için **SCOPE_CPP_SDK** adlı yeni bir ortam değişkeni tanımlayın. Veya klasörü başka bir konuma kopyalayın ve **SCOPE_CPP_SDK** olarak belirtin.

    Ortam değişkenini ayarlamaya ek olarak, komut satırını kullanırken **-cppsdk** bağımsız değişkenini de belirtebilirsiniz. Bu bağımsız değişken, varsayılan CppSDK ortam değişkeninizin üzerine yazar.

- **LOCALRUN_DATAROOT** ortam değişkenini ayarlayın.

    Veri köküne işaret eden **LOCALRUN_DATAROOT** adlı yeni bir ortam değişkeni tanımlayın.

    Ortam değişkenini ayarlamaya ek olarak, bir komut satırı kullanırken, **-dataroot** bağımsız değişkenini veri kök yoluyla belirtebilirsiniz. Bu bağımsız değişken, varsayılan veri kök ortam değişkeninizin üzerine yazar. Bu bağımsız değişkeni çalıştırdığınız her komut satırına eklemeniz gerekir, böylece tüm işlemler için varsayılan veri kök ortam değişkenini geçersiz kılabilirsiniz.

### <a name="sdk-command-line-usage-samples"></a>SDK komut satırı kullanım örnekleri

#### <a name="compile-and-run"></a>Derleyin ve çalıştırın

**Çalıştır** komutu betiği derlemek ve sonra derlenen sonuçları yürütmek için kullanılır. Komut satırı bağımsız değişkenleri, **derleme** ve **yürütme**özelliklerinden bir birleşimidir.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

**Çalıştırma**için isteğe bağlı bağımsız değişkenler aşağıda verilmiştir:


|Bağımsız Değişken|Varsayılan değer|Açıklama|
|--------|-------------|-----------|
|-CodeBehind|False|Betiğin arkasında. cs kodu bulunur|
|-CppSDK| |CppSDK dizini|
|-DataRoot| DataRoot ortam değişkeni|Yerel çalıştırma için DataRoot, varsayılan ' LOCALRUN_DATAROOT ' ortam değişkeni|
|-MessageOut| |Konsolundaki iletileri bir dosyaya dök|
|-Paralel|1\.|Planı belirtilen paralellik ile Çalıştır|
|-Başvurular| |'; ' İle ayrılmış ek başvuru derlemelerine veya arka plan kod veri dosyalarına yönelik yolların listesi|
|-UdoRedirect|False|Uıdo derleme yeniden yönlendirme yapılandırması oluştur|
|-UseDatabase|ana|Geçici derleme kaydı arkasında kod için kullanılacak veritabanı|
|-Ayrıntılı|False|Çalışma zamanının ayrıntılı çıkışlarını göster|
|-WorkDir|Geçerli dizin|Derleyici kullanımı ve çıkışları için Dizin|
|-RunScopeCEP|0|Kullanılacak ScopeCEP modu|
|-ScopeCEPTempPath|kopyalar|Akış verileri için kullanılacak geçici yol|
|-OptFlags| |İyileştirici bayraklarının virgülle ayrılmış listesi|


Bir örneği aşağıda verilmiştir:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

**Derleme** ve **yürütmeyi**birleştirmenin yanı sıra, derlenmiş yürütülebilir dosyaları ayrı olarak derleyip çalıştırabilirsiniz.

#### <a name="compile-a-u-sql-script"></a>U-SQL betiği derleme

**Derle** komutu, yürütülebilir dosyalara bir U-SQL betiği derlemek için kullanılır.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

**Derleme**için isteğe bağlı bağımsız değişkenler aşağıda verilmiştir:


|Bağımsız Değişken|Açıklama|
|--------|-----------|
| -CodeBehind [varsayılan değer ' false ']|Betiğin arkasında. cs kodu bulunur|
| -CppSDK [varsayılan değer ' ']|CppSDK dizini|
| -DataRoot [varsayılan değer ' DataRoot ortam değişkeni ']|Yerel çalıştırma için DataRoot, varsayılan ' LOCALRUN_DATAROOT ' ortam değişkeni|
| -MessageOut [varsayılan değer ' ']|Konsolundaki iletileri bir dosyaya dök|
| -References [varsayılan değer ' ']|'; ' İle ayrılmış ek başvuru derlemelerine veya arka plan kod veri dosyalarına yönelik yolların listesi|
| -Yüzeysel [varsayılan değer ' false ']|Yüzeysel derleme|
| -UdoRedirect [varsayılan değer ' false ']|Uıdo derleme yeniden yönlendirme yapılandırması oluştur|
| -UseDatabase [varsayılan değer ' Ana ']|Geçici derleme kaydı arkasında kod için kullanılacak veritabanı|
| -WorkDir [varsayılan değer ' geçerli dizin ']|Derleyici kullanımı ve çıkışları için Dizin|
| -RunScopeCEP [varsayılan değer ' 0 ']|Kullanılacak ScopeCEP modu|
| -ScopeCEPTempPath [varsayılan değer ' Temp ']|Akış verileri için kullanılacak geçici yol|
| -OptFlags [varsayılan değer ' ']|İyileştirici bayraklarının virgülle ayrılmış listesi|


İşte bazı kullanım örnekleri.

U-SQL betiği derle:

    LocalRunHelper compile -Script d:\test\test1.usql

Bir U-SQL betiği derleyin ve veri kökü klasörünü ayarlayın. Bunun, set ortam değişkeninin üzerine yazyacağını unutmayın.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Bir U-SQL betiği derleyin ve çalışma dizinini, başvuru derlemesini ve veritabanını ayarlayın:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Derlenen sonuçları Yürüt

**Execute** komutu derlenmiş sonuçları yürütmek için kullanılır.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

**Yürütme**için isteğe bağlı bağımsız değişkenler aşağıda verilmiştir:

|Bağımsız Değişken|Varsayılan değer|Açıklama|
|--------|-------------|-----------|
|-DataRoot | '' |Meta veri yürütme için veri kökü. **LOCALRUN_DATAROOT** ortam değişkenini varsayılan olarak belirler.|
|-MessageOut | '' |Konsolundaki iletileri bir dosyaya döker.|
|-Paralel | 1 |Belirtilen paralellik düzeyiyle oluşturulan yerel çalıştırma adımlarını çalıştırmak için gösterge.|
|-Ayrıntılı | 'False' |Çalışma zamanından ayrıntılı çıkışları göstermek için gösterge.|

İşte kullanım örneği:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Programlama arabirimleriyle SDK 'Yı kullanma

Programlama arabirimlerinin hepsi LocalRunHelper. exe ' de bulunur. U-SQL SDK 'sını ve C# test çerçevesini kullanarak u-SQL komut dosyası yerel testinizi ölçeklendirmeniz için bu işlevleri kullanabilirsiniz. Bu makalede, bu arabirimlerin U-SQL betiğini C# test etmek için nasıl kullanılacağını göstermek için standart birim testi projesini kullanacaksınız.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>1\. adım: Birim C# testi projesi ve yapılandırması oluşturma

- Visual C# > C# test > Unit test projesi > Dosya > Yeni > projesi aracılığıyla bir birim test projesi oluşturun.
- LocalRunHelper. exe ' yi projeye başvuru olarak ekleyin. LocalRunHelper. exe, NuGet paketindeki \build\runtime\LocalRunHelper.exe konumunda bulunur.

    ![Azure Data Lake U-SQL SDK başvuru Ekle](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **yalnızca** x64 ortamını destekler, derleme platformu hedefini x64 olarak ayarladığınızdan emin olun. Bunu, proje özelliği > Yapı > Platform hedefi aracılığıyla ayarlayabilirsiniz.

    ![Azure Data Lake U-SQL SDK x64 projesini Yapılandır](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Test ortamınızı x64 olarak ayarladığınızdan emin olun. Visual Studio 'da, test > test ayarları > Varsayılan Işlemci mimarisi > x64 aracılığıyla ayarlayabilirsiniz.

    ![Azure Data Lake U-SQL SDK x64 test ortamını Yapılandır](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- NugetPackage\build\runtime\ altındaki tüm bağımlılık dosyalarını, genellikle Projectfolder\bin\x64\debugaltında bulunan proje çalışma dizinine kopyalamadığınızdan emin olun.

### <a name="step-2-create-u-sql-script-test-case"></a>2\. adım: U-SQL betiği test çalışması oluşturma

U-SQL betik testi için örnek kod aşağıda verilmiştir. Sınama için betikleri, giriş dosyalarını ve beklenen çıkış dosyalarını hazırlamanız gerekir.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>LocalRunHelper. exe ' de programlama arabirimleri

LocalRunHelper. exe, U-SQL yerel derleme, çalıştırma vb. için programlama arabirimleri sağlar. Arabirimler aşağıdaki şekilde listelenmiştir.

**Constructor**

Public LocalRunHelper ([System. ıO. TextWriter messageOutput = NULL])

|Parametre|Type|Açıklama|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|çıkış iletileri için, konsolu kullanmak üzere null olarak ayarlayın|

**Özelliklerinin**

|Özellik|Tür|Açıklama|
|--------|----|-----------|
|AlgebraPath|dize|Algedeniz dosyasının yolu (algeköşeli dosyası, derleme sonuçlarından biridir)|
|CodeBehindReferences|dize|Betik başvuruların arkasında ek kod içeriyorsa, '; ' ile ayrılmış yolları belirtin|
|CppSdkDir|dize|CppSDK dizini|
|CurrentDir|dize|Geçerli dizin|
|DataRoot|dize|Veri kök yolu|
|DebuggerMailPath|dize|Hata ayıklayıcı posta yuvasını yolu|
|GenerateUdoRedirect|bool|Derleme yükleme yeniden yönlendirmesi geçersiz kılma yapılandırması oluşturmak istiyoruz|
|HasCodeBehind|bool|Betiğin arkasında kod varsa|
|InputDir|dize|Giriş verileri için Dizin|
|MessagePath|dize|İleti dökümü dosya yolu|
|OutputDir|dize|Çıkış verileri için Dizin|
|Paralellik|int|Algeköşeli çalıştırmak için paralellik|
|ParentPid|int|Hizmetin çıkış için izlediği üst öğenin PID 'SI, 0 veya negatif olarak ayarla|
|ResultPath|dize|Sonuç dökümü dosya yolu|
|RuntimeDir|dize|Çalışma zamanı dizini|
|ScriptPath|dize|Betiğin nerede bulunacağı|
|Basit|bool|Basit derleme veya Not|
|TempDir|dize|Geçici dizin|
|UseDataBase|dize|Geçici derleme kaydı arka planda kullanılacak veritabanını belirtin, varsayılan olarak ana|
|WorkDir|dize|Tercih edilen çalışma dizini|


**Yöntemi**

|Yöntem|Açıklama|Dönüş|Parametre|
|------|-----------|------|---------|
|ortak bool DoCompile ()|U-SQL betiğini derle|Başarılı olduğunda doğru| |
|public bool DoExec ()|Derlenen sonucu yürütme|Başarılı olduğunda doğru| |
|public bool DoRun ()|U-SQL betiğini çalıştırma (Derle + Yürüt)|Başarılı olduğunda doğru| |
|Genel bool IsValidRuntimeDir (dize yolu)|Verilen yolun geçerli çalışma zamanı yolu olup olmadığını denetleyin|Geçerli için doğru|Çalışma zamanı dizininin yolu|


## <a name="faq-about-common-issue"></a>Yaygın sorun hakkında SSS

### <a name="error-1"></a>Hata 1:
E_CSC_SYSTEM_INTERNAL: İç hata! ' ScopeEngineManaged. dll ' dosyası veya bütünleştirilmiş kodu ya da bağımlılıklarından biri yüklenemedi. Belirtilen modül bulunamadı.

Lütfen aşağıdakileri denetleyin:

- X64 ortamınıza sahip olduğunuzdan emin olun. Yapı hedefi platformu ve test ortamı x64 olmalıdır, **1. Adım: Yukarıda C# birim testi projesi ve yapılandırması** oluşturun.
- NugetPackage\build\runtime\ altındaki tüm bağımlılık dosyalarını proje çalışma dizinine kopyaladığınızdan emin olun.


## <a name="next-steps"></a>Sonraki adımlar

* U-SQL öğrenmek için bkz. [Azure Data Lake Analytics U-SQL dili ile çalışmaya başlama](data-lake-analytics-u-sql-get-started.md).
* Tanılama bilgilerini günlüğe kaydetmek için bkz. [Azure Data Lake Analytics için tanılama günlüklerine erişme](data-lake-analytics-diagnostic-logs.md).
* Daha karmaşık bir sorgu görmek için bkz. [Azure Data Lake Analytics kullanarak Web sitesi günlüklerini çözümleme](data-lake-analytics-analyze-weblogs.md).
* İş ayrıntılarını görüntülemek için bkz. [Azure Data Lake Analytics işleri Için Iş tarayıcısını ve Iş görünümünü kullanma](data-lake-analytics-data-lake-tools-view-jobs.md).
* Köşe yürütme görünümünü kullanmak için bkz. [Visual Studio için Data Lake araçları 'Nda köşe yürütme görünümünü kullanma](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
