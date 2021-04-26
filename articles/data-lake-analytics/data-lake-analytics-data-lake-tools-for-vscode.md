---
title: Visual Studio Code için Azure Data Lake Araçları’nı kullanma
description: U-SQL betiklerini oluşturmak, test etmek ve çalıştırmak için Visual Studio için Azure Data Lake Araçları kodu kullanmayı öğrenin.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e1d74795fd25019e205f4b7b1d2bac1b67107e2d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878587"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Visual Studio Code için Azure Data Lake Araçları’nı kullanma

Bu makalede, U-SQL betiklerini oluşturmak, test etmek ve çalıştırmak için Visual Studio için Azure Data Lake Araçları kodu (VS Code) nasıl kullanabileceğinizi öğrenin. Bilgiler aşağıdaki videoda da ele alınmıştır:

[![Video oynatıcı: VS Code için Azure Data Lake araçları](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Önkoşullar

VS Code için Azure Data Lake araçları, Windows, Linux ve macOS 'u destekler. U-SQL yerel çalıştırma ve yerel hata ayıklama yalnızca Windows 'da çalışır.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS ve Linux için:

- [.NET 5,0 SDK](https://dotnet.microsoft.com/download)
- [Mono 5.2. x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake araçları 'nı yükler

Önkoşulları yükledikten sonra, VS Code için Azure Data Lake Araçları yükleyebilirsiniz.

### <a name="to-install-azure-data-lake-tools"></a>Azure Data Lake araçları 'nı yüklemek için

1. Visual Studio Code’u açın.
2. Sol bölmedeki **Uzantılar** ' ı seçin. Arama kutusuna **Azure Data Lake araçları** girin.
3. **Azure Data Lake araçları**' nın yanındaki **yüklemeyi** seçin.

   ![Data Lake araçlarını yüklemeye yönelik seçimler](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Birkaç saniye sonra Yükle düğmesi yeniden **yüklenmek** üzere değişir. 
4. **Azure Data Lake araçları** uzantısını etkinleştirmek Için **yeniden yükle** ' yi seçin.
5. Onaylamak için **pencereyi yeniden yükle** ' yi seçin. **Uzantılar** bölmesinde **Azure Data Lake araçları** ' na bakabilirsiniz.

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake araçlarını etkinleştir

Uzantıyı etkinleştirmek için. usql dosyası oluşturun veya var olan bir. usql dosyasını açın.

## <a name="work-with-u-sql"></a>U-SQL ile çalışma

U-SQL ile çalışmak için, bir U-SQL dosyası ya da bir klasör açmanız gerekir.

### <a name="to-open-the-sample-script"></a>Örnek betiği açmak için

Komut paletini açın (Ctrl + Shift + P) ve **adl: açık örnek betiği** girin. Bu örnek, bu örneğin başka bir örneğini açar. Ayrıca, bu örnekte bir betiği düzenleyebilir, yapılandırabilir ve gönderebilirsiniz.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>U-SQL projenizin klasörünü açmak için

1. Visual Studio Code, **Dosya** menüsünü seçin ve sonra **klasörü aç**' ı seçin.
2. Bir klasör belirtin ve ardından **Klasör Seç**' i seçin.
3. **Dosya** menüsünü seçin ve ardından **Yeni**' yi seçin. Bir adsız-1 dosyası projeye eklenir.
4. Aşağıdaki kodu başlıksız-1 dosyasına girin:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   @departmentsOutputters.Csv () kullanarak "/output/departments.csv" ÖĞESINE çıkış yapın

    Betik,/Output klasörüne eklenen bazı verilerle bir departments.csv dosyası oluşturur.

5. Dosyayı açılan klasöre **Myusql. usql** olarak kaydedin.

### <a name="to-compile-a-u-sql-script"></a>U-SQL betiğini derlemek için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Adl: derleme betiği** girin. Derleme sonuçları **Çıkış** penceresinde görüntülenir. Ayrıca bir betik dosyasına sağ tıklayıp **adl: betiği derle** ' yi seçerek U-SQL işini derleyebilirsiniz. Derleme sonucu **çıktı** bölmesinde görünür.

### <a name="to-submit-a-u-sql-script"></a>U-SQL betiği göndermek için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Adl: gönderme işini** girin. Ayrıca bir betik dosyasına sağ tıklayıp **adl: Işi gönder**' i seçebilirsiniz.

Bir U-SQL işi gönderdikten sonra, gönderim günlükleri VS Code **Çıkış** penceresinde görüntülenir. İş görünümü sağ bölmede görüntülenir. Gönderim başarılı olursa, iş URL 'SI de görüntülenir. Gerçek zamanlı iş durumunu izlemek için iş URL 'sini bir Web tarayıcısında açabilirsiniz.

İş görünümünün **Özet** sekmesinde iş ayrıntılarını görebilirsiniz. Ana işlevler, bir betiği yeniden gönder, bir betiği Yinele ve portalda aç ' ı içerir. İş görünümünün **veri** sekmesinde giriş dosyalarına, çıkış dosyalarına ve kaynak dosyalarına başvurabilirsiniz. Dosyalar yerel bilgisayara indirilebilir.

![İş görünümündeki Özet sekmesi](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![İş görünümündeki veri sekmesi](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Varsayılan bağlamı ayarlamak için

Dosya parametrelerini ayrı ayrı ayarlamadıysanız, bu ayarı tüm betik dosyalarına uygulamak için varsayılan bağlamı ayarlayabilirsiniz.

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Adl: varsayılan bağlamı ayarla**' yı girin. Veya betik düzenleyicisine sağ tıklayıp **adl: varsayılan bağlamı ayarla**' yı seçin.
3. İstediğiniz hesabı, veritabanını ve şemayı seçin. Ayar, yapılandırma dosyasında xxx_settings.jskaydedilir.

   ![Hesap, veritabanı ve şema varsayılan bağlam olarak ayarlandı](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Betik parametrelerini ayarlamak için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Adl: betik parametrelerini ayarla**' yı girin.
3. Dosyadaki xxx_settings.jsaşağıdaki özelliklerle açılır:

   - **Hesap**: U-SQL işlerini derlemek ve çalıştırmak Için gereken Azure aboneliğiniz kapsamındaki bir Azure Data Lake Analytics hesabı. U-SQL işlerini derleyip çalıştırmadan önce bilgisayar hesabını yapılandırmanız gerekir.
   - **veritabanı**: hesabınızdaki bir veritabanı. Varsayılan değer **yöneticisidir**.
   - **şema**: veritabanınız altındaki bir şema. Varsayılan değer **dbo**' dır.
   - **Optionalsettings**:
        - **Öncelik**: öncelik aralığı 1 ile 1000 arasındadır ve en yüksek öncelik 1 ' dir. Varsayılan değer **1000**'dir.
        - **Degreeofparalellik**: paralellik aralığı 1 ile 150 arasındadır. Varsayılan değer Azure Data Lake Analytics hesabınızda izin verilen en büyük paralellik değeridir.

   ![JSON dosyasının içeriği](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Yapılandırmayı kaydettikten sonra, varsayılan bir bağlam ayarlanmamışsa, ilgili. usql dosyasının sol alt köşesindeki hesap, veritabanı ve şema bilgileri görüntülenir.

### <a name="to-set-git-ignore"></a>Git Ignore 'yi ayarlamak için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Adl: git Ignore ayarla**' yı girin.

   - VS Code çalışma klasörünüzde bir **. gitIgnore** dosyanız yoksa, klasörünüzde **. gitIgnore** adlı bir dosya oluşturulur. Dört öğe (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. Cache**, **obj**) varsayılan olarak dosyaya eklenir. Gerekirse daha fazla güncelleştirme yapabilirsiniz.
   - VS Code çalışma klasörünüzde zaten bir **. gitIgnore** dosyanız varsa, bu araç, dosyada dört öğe dahil edilmediğinden **. gitIgnore** dosyanıza dört öğe (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. Cache**, **obj**) ekler.

   ![. GitIgnore dosyasındaki öğeler](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Arka plan kod dosyalarıyla çalışma: C diyez, Python ve R

Azure Data Lake araçları birden çok özel kodu destekler. Yönergeler için, bkz. [VS Code Azure Data Lake Analytics Için Python, R ve C Sharp Ile U-SQL geliştirme](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Derlemelerle çalışma

Derleme geliştirme hakkında bilgi için bkz. [Azure Data Lake Analytics işleri Için U-SQL derlemelerini geliştirme]().

Data Lake Analytics kataloğuna özel kod derlemelerini kaydetmek için Data Lake araçlarını kullanabilirsiniz.

### <a name="to-register-an-assembly"></a>Bir derlemeyi kaydetmek için

Derlemeyi **adl: Register Assembly** veya **adl: Register Assembly (Gelişmiş)** komutu aracılığıyla kaydedebilirsiniz.

### <a name="to-register-through-the-adl-register-assembly-command"></a>ADL: Register Assembly komutunu kullanarak kaydolmak için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Adl: Register derlemesini** girin.
3. Yerel derleme yolunu belirtin.
4. Bir Data Lake Analytics hesabı seçin.
5. Bir veritabanı seçin.

Portal bir tarayıcıda açılır ve derleme kayıt işlemini görüntüler.  

**Adl: Register Assembly** komutunu tetiklemenin daha kolay bir yolu, dosya Gezgini 'nde. dll dosyasına sağ tıklamanız.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>ADL: yazmaç derlemesi (Gelişmiş) komutu üzerinden kaydolmak için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.
2. **Adl: yazmaç derlemesini (Gelişmiş)** girin.
3. Yerel derleme yolunu belirtin.
4. JSON dosyası görüntülenir. Gerekirse, derleme bağımlılıklarını ve kaynak parametrelerini gözden geçirin ve düzenleyin. Yönergeler **Çıkış** penceresinde görüntülenir. Derleme kaydına devam etmek için JSON dosyasını kaydedin (CTRL + S).

   ![Bütünleştirilmiş kod bağımlılıkları ve kaynak parametreleri içeren JSON dosyası](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake araçları, DLL 'nin herhangi bir derleme bağımlılığı olup olmadığını algılar. Bağımlılıklar, algılandıktan sonra JSON dosyasında görüntülenir.
>- DLL kaynaklarınızı (örneğin,. txt,. png ve. csv) derleme kaydının bir parçası olarak yükleyebilirsiniz.

**Adl: yazmaç derlemesi (Gelişmiş)** komutunu tetiklemenin bir diğer yolu, dosya Gezgini 'nde. dll dosyasına sağ tıklamanız.

Aşağıdaki U-SQL kodu, bir derlemenin nasıl çağrılacağını gösterir. Örnekte, derleme adı *Test* olur.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Windows kullanıcıları için U-SQL yerel çalıştırma ve yerel hata ayıklama kullanın

U-SQL yerel çalıştırma, kodunuzun Data Lake Analytics yayınlanmasından önce yerel verilerinizi sınar ve betiğinizi yerel olarak doğrular. Kodunuzun Data Lake Analytics gönderilmeden önce aşağıdaki görevleri gerçekleştirmek için yerel hata ayıklama özelliğini kullanabilirsiniz:

- C# arka plan kodunuzda hata ayıklayın.
- Kodda adım adım ilerleyin.
- Betiğinizi yerel olarak doğrulayın.

Yerel çalıştırma ve yerel hata ayıklama özelliği yalnızca Windows ortamlarında çalışır ve macOS ve Linux tabanlı işletim sistemlerinde desteklenmez.

Yerel çalıştırma ve yerel hata ayıklama yönergeleri için, bkz. [U-SQL yerel çalıştırma ve Visual Studio Code ile yerel hata ayıklama](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Azure'a Bağlanma

Data Lake Analytics 'de U-SQL betiklerini derlemek ve çalıştırmak için önce Azure hesabınıza bağlanmanız gerekir.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Bir komut kullanarak Azure 'a bağlanmak için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin.

2. **Adl: Login** girin. Oturum açma bilgileri sağ alt tarafta görüntülenir.

   ![Login komutunu girme](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Oturum açma ve kimlik doğrulama hakkında bildirim](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. [Oturum açma Web sayfasını](https://aka.ms/devicelogin)açmak için **Kopyala & aç** ' ı seçin. Kodu kutuya yapıştırın ve sonra **devam**' ı seçin.

    ![Oturum açma Web sayfası](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Web sayfasından oturum açmak için yönergeleri izleyin. Bağlandığınızda, Azure hesabınızın adı VS Code penceresinin sol alt köşesindeki durum çubuğunda görüntülenir.

> [!NOTE]
>
> - Data Lake araçlar, oturumunuzu otomatik olarak bir sonraki sefer kapatır.
> - Hesabınızda iki faktör etkinse, PIN kullanmak yerine telefon kimlik doğrulaması kullanmanızı öneririz.

Oturumu kapatmak için **adl: logout** komutunu girin.

### <a name="to-connect-to-azure-from-the-explorer"></a>Azure 'a gezgin 'e bağlanmak için

Azure **Data Lake**' i genişletin, **Azure 'da oturum aç**' ı seçin ve ardından [bir komut kullanarak Azure 'a bağlanmak için](#sign-in-by-command)adım 3 ve adım 4 ' ü izleyin.

![Gezgin 'de "Azure 'da oturum aç" seçimi](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Gezgin oturumunuzu açamazsınız. Oturumu kapatmak için, bkz. [bir komut kullanarak Azure 'a bağlanmak için](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Ayıklama betiği oluşturma

**Adl: Create Ayıkla betiğini** veya Azure Data Lake Gezgini ' ni kullanarak. csv,. tsv ve. txt dosyaları için bir ayıklama betiği oluşturabilirsiniz.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Bir komut kullanarak ayıklama betiği oluşturmak için

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin ve **adl: ayıklama betiği oluştur** yazın.
2. Bir Azure depolama dosyası için tam yolu belirtin ve Enter tuşunu seçin.
3. Bir hesap seçin.
4. Bir. txt dosyası için, dosyayı ayıklamak üzere bir sınırlayıcı seçin.

![Ayıklama betiği oluşturma işlemi](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Ayıklama betiği, girdlarınız temel alınarak oluşturulur. Sütunları algılayamadığı bir betik için, iki seçenekten birini seçin. Aksi takdirde, yalnızca bir betik oluşturulur.

![Ayıklama betiği oluşturma sonucu](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Gezgin 'den bir ayıklama betiği oluşturmak için

Ayıklama betiğini oluşturmanın bir başka yolu da Azure Data Lake Store veya Azure Blob depolama alanındaki. csv,. tsv veya. txt dosyasındaki sağ tıklama (kısayol) menüsünü kullanmaktır.

![Kısayol menüsünden "betiği AYıKLA" komutu](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Sonraki adımlar

- [VS Code Azure Data Lake Analytics için Python, R ve C Sharp ile U-SQL geliştirme](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL yerel çalıştırma ve Visual Studio Code ile yerel hata ayıklama](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Öğretici: Azure Data Lake Analytics kullanmaya başlayın](data-lake-analytics-get-started-portal.md)
- [Öğretici: Visual Studio için Data Lake araçları 'nı kullanarak U-SQL betikleri geliştirme](data-lake-analytics-data-lake-tools-get-started.md)
