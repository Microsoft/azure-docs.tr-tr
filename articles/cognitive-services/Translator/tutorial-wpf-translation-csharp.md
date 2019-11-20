---
title: "Öğretici: WPF ile bir çeviri uygulaması oluşturma, C# -Translator metin çevirisi API'si"
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, tek bir abonelik anahtarıyla metin çevirisi, dil algılama ve yazım denetimi yapmak için bir WPF uygulaması oluşturacaksınız.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 6f8196c276b4f8ef5c8a49f6f83f59f9f505a6be
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647698"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Öğretici: WPF ile bir çeviri uygulaması oluşturma

Bu öğreticide, tek bir abonelik anahtarıyla metin çevirisi, dil algılama ve yazım denetimi için Azure bilişsel hizmetler 'i kullanan bir [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2019) uygulaması oluşturacaksınız. Özellikle, uygulamanız Translator Metin Çevirisi ve [Bing yazım denetimi](https://azure.microsoft.com/services/cognitive-services/spell-check/)API 'leri çağıracaktır.

WPF nedir? Masaüstü istemci uygulamaları oluşturan bir UI çerçevesidir. WPF geliştirme platformu, uygulama modeli, kaynaklar, denetimler, grafikler, düzen, veri bağlama, belgeler ve güvenlik dahil olmak üzere çok sayıda uygulama geliştirme özelliği destekler. .NET Framework bir alt kümesidir, bu nedenle daha önce ASP.NET veya Windows Forms kullanarak .NET Framework ile uygulamalar oluşturduysanız, programlama deneyiminin tanıdık olması gerekir. WPF, uygulama programlamaya yönelik bildirim temelli bir model sağlamak için Genişletilebilir uygulama biçimlendirme dili 'ni (XAML) kullanır. Bu, gelecek bölümlerde gözden geçireceğiz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Visual Studio 'da WPF projesi oluşturma
> * Projenize derlemeler ve NuGet paketleri ekleyin
> * XAML ile uygulamanızın kullanıcı arabirimini oluşturma
> * Dilleri almak, metin çevirmek ve kaynak dilini algılamak için Translator Metin Çevirisi API'si kullanın
> * Girişinizi doğrulamak ve çeviri doğruluğunu artırmak için Bing Yazım Denetimi API'si kullanın
> * WPF uygulamanızı çalıştırma

### <a name="cognitive-services-used-in-this-tutorial"></a>Bu öğreticide kullanılan bilişsel hizmetler

Bu liste, bu öğreticide kullanılan bilişsel hizmetleri içerir. Her bir özelliğin API başvurusuna gözatabilmek için bağlantıyı izleyin.

| Hizmet | Özellik | Açıklama |
|---------|---------|-------------|
| Translator Metni | [Dilleri al](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Metin çevirisi için desteklenen dillerin tüm listesini alın. |
| Translator Metni | [Çevir](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Metni 60 ' den fazla dile çevirin. |
| Translator Metni | [Acağı](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Giriş metninin dilini tespit edin. Algılama için güvenirlik puanı içerir. |
| Bing Yazım Denetimi | [Yazım Denetimi](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Çeviri doğruluğunu artırmak için yazım hatalarını düzeltin. |

## <a name="prerequisites"></a>Ön koşullar

Devam etmeden önce şunlar gerekir:

* Azure bilişsel hizmetler aboneliği. [Bilişsel hizmetler anahtarı alın](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#create-a-new-azure-cognitive-services-resource).
* Bir Windows makinesi
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) -Community veya Enterprise

> [!NOTE]
> Bu öğretici için Batı ABD bölgede aboneliğin oluşturulmasını öneririz. Aksi takdirde, Bu alıştırmada çalışırken koddaki uç noktaları ve bölgeleri değiştirmeniz gerekir.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Visual Studio 'da WPF uygulaması oluşturma

Yapmanız gereken ilk şey, Visual Studio 'da projemizi ayarladık.

1. Visual Studio’yu açın. **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da WPF uygulaması ' nı bulun ve seçin **(.NET Framework)** . Seçenekleri daraltmak için C# **dilden** seçim yapabilirsiniz.
1. **İleri**' yi seçin ve ardından projenizin `MSTranslatorTextDemo`adını belirleyin.
1. Framework sürümünü **.NET Framework 4.7.2** veya üzeri olarak ayarlayın ve **Oluştur**' u seçin.
   ![Visual Studio 'da adı ve Framework sürümünü girin](media/name-wpf-project-visual-studio.png)

Projeniz oluşturuldu. İki sekmeden açık olduğunu fark edeceksiniz: `MainWindow.xaml` ve `MainWindow.xaml.cs`. Bu öğreticide, bu iki dosyaya kod ekliyoruz. Uygulamanın kullanıcı arabirimi için `MainWindow.xaml` değiştireceksiniz. Translator Metin Çevirisi ve Bing Yazım Denetimi çağrılarımız için `MainWindow.xaml.cs` değiştireceksiniz.
   ![ortamınızı gözden geçirin](media/blank-wpf-project.png)

Sonraki bölümde, JSON ayrıştırma gibi ek işlevler için projenize derlemeler ve bir NuGet paketi ekleyeceğiz.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Projenize başvurular ve NuGet paketleri ekleyin

Projemiz .NET Framework derlemeleri ve NuGet Paket Yöneticisi 'ni kullanarak yükleytiğimiz NewtonSoft. json ' ı gerektirir.

### <a name="add-net-framework-assemblies"></a>.NET Framework derlemeleri Ekle

Nesneleri seri hale getirmek ve seri durumdan çıkarmak için ve HTTP isteklerini ve yanıtlarını yönetmek için projenize derleme ekleyelim.

1. Projenizi Visual Studio 'nun Çözüm Gezgini bulun. Projenize sağ tıklayın ve ardından **başvuru Yöneticisi**'ni açan **> Başvuru Ekle**' yi seçin.
1. **Derlemeler** sekmesi, başvuru için kullanılabilen tüm .NET Framework derlemeleri listeler. Başvuruları aramak için sağ üst köşedeki arama çubuğunu kullanın.
   ![derleme başvuruları Ekle](media/add-assemblies-2019.png)
1. Projeniz için aşağıdaki başvuruları seçin:
   * [System. Runtime. Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System. Web](https://docs.microsoft.com/dotnet/api/system.web)
   * System.Web.Extensions
   * [System. Windows](https://docs.microsoft.com/dotnet/api/system.windows)
1. Bu başvuruları projenize ekledikten sonra, **başvuru Yöneticisi**'ni kapatmak için **Tamam** ' a tıklayabilirsiniz.

> [!NOTE]
> Derleme başvuruları hakkında daha fazla bilgi edinmek istiyorsanız bkz. [nasıl yapılır: başvuru Yöneticisi 'ni kullanarak başvuru ekleme veya kaldırma](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019).

### <a name="install-newtonsoftjson"></a>NewtonSoft. JSON 'ı yükler

Uygulamamız JSON nesnelerinin serisini kaldırmak için NewtonSoft. JSON kullanacaktır. Paketi yüklemek için bu yönergeleri izleyin.

1. Projenizi Visual Studio 'nun Çözüm Gezgini bulun ve projenize sağ tıklayın. **NuGet Paketlerini Yönet**' i seçin.
1. **Araştır** sekmesini bulun ve seçin.
1. Arama çubuğuna [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) girin.

    ![NewtonSoft. JSON öğesini bulun ve yükler](media/nuget-package-manager.png)

1. Paketi seçin ve ardından **yüklensin**' e tıklayın.
1. Yükleme tamamlandığında sekmesini kapatın.

## <a name="create-a-wpf-form-using-xaml"></a>XAML kullanarak WPF formu oluşturma

Uygulamanızı kullanmak için bir kullanıcı arabirimine ihtiyacınız olacak. XAML kullanarak, kullanıcıların giriş ve çeviri dillerini seçmesine, çevrilecek metni girmesine ve çeviri çıktısını görüntülemesine olanak tanıyan bir form oluşturacağız.

Biz oluşturduğumuzun bir bakalım.

![WPF XAML kullanıcı arabirimi](media/translator-text-csharp-xaml.png)

Kullanıcı arabirimi şu bileşenleri içerir:

| Ad | Tür | Açıklama |
|------|------|-------------|
| `FromLanguageComboBox` | Öğesi | Metin çevirisi için Microsoft Translator tarafından desteklenen dillerin listesini görüntüler. Kullanıcı çeviri yaptığı kaynak dili seçer. |
| `ToLanguageComboBox` | Öğesi | `FromComboBox`dil listesini görüntüler, ancak kullanıcının çevirdiğini dili seçmek için kullanılır. |
| `TextToTranslate` | TextBox | Kullanıcının çevrilecek metin girmesine izin verir. |
| `TranslateButton` | Bu | Metni çevirmek için bu düğmeyi kullanın. |
| `TranslatedTextLabel` | Etiket | Çeviriyi görüntüler. |
| `DetectedLanguageLabel` | Etiket | Çevrilecek metnin algılanan dilini görüntüler (`TextToTranslate`). |

> [!NOTE]
> Bu formu XAML kaynak kodunu kullanarak oluşturuyoruz, ancak formu Visual Studio 'daki düzenleyiciyle oluşturabilirsiniz.

Kodu projenize ekleyelim.

1. Visual Studio 'da `MainWindow.xaml`sekmesini seçin.
1. Bu kodu projenize kopyalayın ve sonra değişikliklerinizi kaydetmek için **dosya > MainWindow. xaml dosyasını Kaydet** ' i seçin.
   ```xaml
   <Window x:Class="MSTranslatorTextDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorTextDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
Artık Visual Studio 'da uygulamanın kullanıcı arabiriminin önizlemesini görmeniz gerekir. Yukarıdaki görüntüye benzer görünmelidir.

Bu, formunuz için hazırlayın. Şimdi metin çevirisini ve Bing Yazım Denetimi kullanmak için kod yazalım.

> [!NOTE]
> Bu formu ince ayar veya kendinizinkini oluşturabilirsiniz.

## <a name="create-your-app"></a>Uygulamanızı oluşturun

`MainWindow.xaml.cs` uygulamamızı denetleyen kodu içerir. Sonraki birkaç bölümde, açılan menülerinizi doldurmak ve Translator Metin Çevirisi ve Bing Yazım Denetimi tarafından sunulan el ile API 'yi çağırmak için kod ekleyeceğiz.

* Program başlatıldığında ve `MainWindow` örneği oluşturulduğunda, dil seçim açılır listelerimizi almak ve doldurmak için Translator Metin Çevirisi API'si `Languages` yöntemi çağırılır. Bu, her oturumun başlangıcında bir kez gerçekleşir.
* **Çevir** düğmesine tıklandığında, kullanıcının dil seçimi ve metni alınır, girişte yazım denetimi yapılır ve çeviri ve algılanan dil kullanıcı için görüntülenir.
  * Translator Metin Çevirisi API'si `Translate` yöntemi `TextToTranslate`metin çevirmek için çağrılır. Bu çağrıda Ayrıca açılır menüler kullanılarak seçilen `to` ve `from` dilleri de bulunur.
  * Translator Metin Çevirisi API'si `Detect` yöntemi `TextToTranslate`metin dilini belirlemede çağrılır.
  * Bing Yazım Denetimi, `TextToTranslate` doğrulamak ve hatalı yazımları ayarlamak için kullanılır.

Tüm projemiz `MainWindow : Window` sınıfında kapsüllenir. Abonelik anahtarınızı ayarlamak, Translator Metin Çevirisi ve Bing Yazım Denetimi için uç noktaları bildirmek ve uygulamayı başlatmak için kod ekleyerek başlayalım.

1. Visual Studio 'da `MainWindow.xaml.cs`sekmesini seçin.
1. Önceden doldurulmuş `using` deyimlerini aşağıdaki kodla değiştirin.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. `MainWindow : Window` sınıfını bulun ve bu kodla değiştirin:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. Bilişsel hizmetler abonelik anahtarınızı ekleyin ve kaydedin.

Bu kod bloğunda, çeviri için kullanılabilir diller hakkında bilgi içeren iki üye değişkeni bildirdik:

| Değişken | Tür | Açıklama |
|----------|------|-------------|
|`languageCodes` | Dizeler dizisi |Dil kodlarını önbelleğe alır. Translator hizmeti dilleri belirlemek için kısa kodlar kullanır (örneğin İngilizce için `en`). |
|`languageCodesAndTitles` | Sıralanmış sözlük | Kullanıcı arabirimindeki "kolay anlaşılır" adları, API’de kullanılan kısa kodlarla eşleştirir. Büyük küçük harf kullanımından bağımsız olarak alfabetik sırayla tutulur. |

Sonra, `MainWindow` Oluşturucusu içinde `HandleExceptions`ile hata işleme ekledik. Bu hata işleme, bir özel durum işlenmediğinde bir uyarının sağlandığından emin olmanızı sağlar. Daha sonra, belirtilen abonelik anahtarını doğrulamak için bir denetim çalıştırılır ve 32 karakter uzunluğunda olur. Anahtar 32 karakterden fazlaysa bir hata oluşur.

En azından doğru uzunlukta olan anahtarlar varsa, `InitializeComponent()` çağrısı, ana uygulama penceresinin XAML açıklamasını bularak, yükleyerek ve örnekleyerek Kullanıcı arabirimini alır.

Son olarak, çeviri dillerini almak ve uygulamanın kullanıcı arabirimimizin açılan menülerini doldurmak için yöntemleri çağırmak için bir kod ekledik. Endişelenmeyin, bu çağrıların arkasındaki koda yakında başlayacağız.

## <a name="get-supported-languages"></a>Desteklenen dilleri alma

Translator Metin Çevirisi API'si şu anda 60 'den fazla dili destekliyor. Yeni dil desteği zamana göre eklenebileceğinden, uygulamanızdaki dil listesini sabit bir şekilde kodlamak yerine Translator Metin Çevirisi tarafından açığa çıkarılan diller kaynağını çağırmayı öneririz.

Bu bölümde, çeviri için kullanılabilen dillerin bir listesini istediğimizden önce, diller kaynağına bir `GET` isteği oluşturacağız.

> [!NOTE]
> Diller kaynağı, dil desteğini Şu sorgu parametreleriyle filtrelemenizi sağlar: alfabe, sözlük ve çeviri. Daha fazla bilgi için bkz. [API başvurusu](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Daha fazla ilerleyebilmemiz için, diller kaynağı çağrısı için örnek çıktıya göz atalım:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

Bu çıktıdan, dil kodunu ve belirli bir dilin `name` ayıklayabiliriz. Uygulamamız JSON nesnesinin serisini kaldırmak için NewtonSoft. JSON kullanıyor ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Son bölümde kaldığınız yerden başladığımızda, uygulamamıza desteklenen diller için bir yöntem ekleyelim.

1. Visual Studio 'da `MainWindow.xaml.cs`sekmesini açın.
2. Bu kodu projenize ekleyin:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

`GetLanguagesForTranslate()` yöntemi bir HTTP GET isteği oluşturur ve isteğin kapsamını çeviri için desteklenen dillere sınırlamak için `scope=translation` sorgu dizesi parametresini kullanır. Desteklenen dillerin İngilizce olarak döndürülmesi için `Accept-Language` değerine sahip `en` üst bilgisi eklenir.

JSON yanıtı ayrıştırılır ve bir sözlüğe dönüştürülür. Ardından dil kodları `languageCodes` üye değişkenine eklenir. Dil kodlarını ve kolay dil adlarını içeren anahtar/değer çiftleri döndürülür ve `languageCodesAndTitles` üye değişkenine eklenir. Formdaki açılan menülerde kolay adlar görüntülenir, ancak bu kodlar çeviriyi istemek için gereklidir.

## <a name="populate-language-drop-down-menus"></a>Dil açılır menülerini doldur

Kullanıcı arabirimi XAML kullanılarak tanımlanmıştır, bu yüzden çağrı `InitializeComponent()`ek olarak ayarlamanız gerekmez. Yapmanız gereken tek şey, kolay dil adlarını, **çeviri** **ve açılan menülere çevirecek şekilde** bir adım adım ekler. `PopulateLanguageMenus()` yöntemi adlarını ekler.

1. Visual Studio 'da `MainWindow.xaml.cs`sekmesini açın.
2. Bu kodu projenize `GetLanguagesForTranslate()` yönteminin altına ekleyin:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Bu yöntem `languageCodesAndTitles` sözlüğü üzerinde yinelenir ve her anahtarı her iki menüye ekler. Menüler doldurulduktan sonra varsayılan ve sonraki diller sırasıyla **Algıla** ve **İngilizce** olarak ayarlanır.

> [!TIP]
> Menüler için varsayılan bir seçim olmadan, kullanıcı önce bir "hedef" veya "kaynak" dil seçmeden **Çevir**’e tıklayabilir. Varsayılan değerler bu sorunla başa çıkma gereksinimini ortadan kaldırır.

Artık `MainWindow` başlatıldığından ve Kullanıcı arabirimi oluşturulduktan sonra, **çeviri** düğmesine tıklanana kadar bu kod çalışmaz.

## <a name="detect-language-of-source-text"></a>Kaynak metnin dilini Algıla

Artık Translator Metin Çevirisi API'si kullanarak kaynak metnin (metin alanına girilen metin) dilini algılamak için yöntem oluşturacağız. Bu istek tarafından döndürülen değer, daha sonra çeviri isteğinde kullanılacaktır.

1. Visual Studio 'da `MainWindow.xaml.cs`sekmesini açın.
2. Bu kodu projenize `PopulateLanguageMenus()` yönteminin altına ekleyin:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Bu yöntem, Algıla kaynağına bir HTTP `POST` isteği oluşturur. İsteğin gövdesi olarak iletilen `text`tek bir bağımsız değişken alır. Daha sonra, çeviri isteimizi oluşturduğumuzda, Kullanıcı arabirimimize girilen metin, dil algılama için bu yönteme geçirilir.

Ayrıca, bu yöntem yanıtın güvenilirlik Puanını değerlendirir. Puan `0.5`değerinden büyükse, algılanan Dil Kullanıcı arabirimimizde görüntülenir.

## <a name="spell-check-the-source-text"></a>Kaynak metnini yazım denetimi

Artık Bing Yazım Denetimi API'si kullanarak kaynak metnimizi yazım denetimi yapmak için bir yöntem oluşturacağız. Yazım denetimi, Translator Metin Çevirisi API'si ' den doğru çevirileri geri almanızı sağlar. **Çeviri düğmesine tıklandığında** , kaynak metinde yapılan tüm düzeltmeler çeviri talebimize geçirilir.

1. Visual Studio 'da `MainWindow.xaml.cs`sekmesini açın.
2. Bu kodu projenize `DetectLanguage()` yönteminin altına ekleyin:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Tıklama üzerinde metin çevir

Yapmanız gereken son şey, Kullanıcı arabirimimizin **çevir** düğmesine tıklandığında çağrılan bir yöntem oluşturmaktır.

1. Visual Studio 'da `MainWindow.xaml.cs`sekmesini açın.
1. Bu kodu projenize `CorrectSpelling()` yönteminin altına ekleyin ve kaydedin:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

İlk adım, "Kimden" ve "to" dillerini ve kullanıcının formumuza girdiği metni almak için kullanılır. Kaynak dil **Algıla**olarak ayarlandıysa, kaynak metnin dilini belirlemek için `DetectLanguage()` çağırılır. Metin, çevirmen API 'sinin desteklemediği bir dilde olabilir. Bu durumda, kullanıcıyı bilgilendirmek için bir ileti görüntüler ve metni çevirmeden geri döndürün.

Kaynak dil İngilizce ise (belirtilerek veya algılanarak), metnin yazımını `CorrectSpelling()` ile denetleyin ve düzeltmeleri uygulayın. Düzeltilen metin, kullanıcının bir düzeltmenin yapıldığını görmesi için metin alanına geri eklenir.

Metni çevirecek kod tanıdık gelmelidir: URI oluşturma, istek oluşturma, gönderme ve yanıtı ayrıştırma. JSON dizisi çeviri için birden fazla nesne içerebilir, ancak uygulamamız yalnızca bir tane gerektirir.

Başarılı bir istekten sonra, `TranslatedTextLabel.Content`, çevrilmiş metni göstermek için Kullanıcı arabirimini güncelleştiren `translation`ile değiştirilmiştir.

## <a name="run-your-wpf-app"></a>WPF uygulamanızı çalıştırma

Bu, WPF kullanılarak oluşturulan bir çalışan çeviri uygulamasına sahipsiniz. Uygulamanızı çalıştırmak için, Visual Studio 'daki **Başlat** düğmesine tıklayın.

## <a name="source-code"></a>Kaynak kod

Bu proje için kaynak kodu GitHub ' da kullanılabilir.

* [Kaynak kodunu keşfet](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Microsoft Translator Metin Çevirisi API’si başvurusu](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
