---
title: 'Öğretici: Bir varlık bağlama uygulaması oluşturma-C#'
titlesuffix: Azure Cognitive Services
description: Varlık Bağlama API’sini kullanarak bir bilgi bankasındaki ilgili girişlere yönelik metin ve bağlantı adlandırılmış varlıklarını analiz edin.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 4b283103920230a0d2aae98c83f75fb03679a675
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706802"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Öğretici: İle bir varlık bağlama uygulaması derlemeC#

Microsoft’un Varlık Bağlama aracı, bir bilgi bankasındaki ilgili girişlere yönelik metin ve bağlantı adlandırılmış varlıklarını analiz etmek için kullanılan bir doğal dil işleme aracıdır. 

Bu öğretici, Varlık Bağlama İstemci Kitaplığını bir NuGet paketi olarak kullanarak varlık bağlamayı keşfeder. 

### <a name="Prerequisites">Önkoşullar</a>

- Visual Studio 2015
- Bir Microsoft Bilişsel Hizmetler API Anahtarı
- İstemci kitaplığını ve örneği alma
- Microsoft Varlık Bağlama NuGet Paketi

[SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows) aracılığıyla Varlık Bağlama Akıllı Hizmeti API’si İstemci Kitaplığı’nı indirebilirsiniz. İndirilen zip dosyasının istediğiniz bir klasöre ayıklanması gerekir, çoğu kullanıcı, Visual Studio 2015 klasörünü seçer.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">1. Adım: Varlık Bağlama Akıllı Hizmeti abone olun ve anahtarınızı alın</a>
Varlık Bağlama Akıllı Hizmeti’ni kullanmadan önce bir API anahtarına kaydolmanız gerekir. Bkz. [Abonelikler](https://www.microsoft.com/cognitive-services/en-us/sign-up). Bu öğreticide, hem birincil hem de ikincil anahtar kullanılabilir.

### <a name="step-2-create-a-new-project-in-visual-studio">2. Adım: Visual Studio 'da yeni proje oluşturma</a>

Visual Studio’da yeni bir proje oluşturarak başlayalım. İlk olarak, Başlat menüsünden Visual Studio 2015’i başlatın. Daha sonra proje şablonunuz için **Yüklü → Şablonlar → Visual C# → Windows Evrensel → Boş Uygulama** seçeneklerini belirleyerek yeni bir proje oluşturun:

 ![Evrensel uygulama oluşturma](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Adım 3: Projenize NuGet paketini bağlama varlığı ekleyin</a>

Bilişsel Hizmetler Varlık Bağlaması bir NuGet.org paketi olarak yayınlanır ve kullanılmadan önce yüklenmesi gerekir.
Bunu projenize eklemek için **Çözüm Gezgini** sekmesine gidin, projenize sağ tıklayın ve **Nuget Paketlerini Yönet**’i seçin.

İlk olarak **NuGet Paket Yöneticisi** penceresinde sağ üst köşede **Paket Kaynağı** olarak NuGet.org seçeneğini belirleyin. Sol üst köşedeki **Gözat** seçeneğini belirleyin ve arama kutusuna “ProjectOxford.EntityLinking” yazın. **Microsoft.ProjectOxford.EntityLinking** NuGet paketini seçin ve **Yükle**’ye tıklayın.

Daha sonra Newtonsoft.Json araması yapıp bunu yükleyin. Değişiklikleri gözden geçirmeniz istenirse **Tamam**’a tıklayın. Size EntityLinking lisans koşulları sunulursa **Kabul Ediyorum**’u seçin.

Varlık Bağlama şimdi uygulamanızın parçası olarak yüklenir. Çözüm Gezgini’nde projenizin parçası olarak ** Microsoft.ProjectOxford.EntityLinking** başvurusunun mevcut olup olmadığını denetleyerek bunu onaylayabilirsiniz.

 ![Projeye dahil edilen nuget kitaplığı](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">4. Adım: Uygulamanızın XAML 'e giriş ve çıkış metin bloğu ekleme</a>
**Çözüm Gezgini**bölümünde **MainPage. xaml** dosyasına gidin ve dosyayı çift tıklatarak yeni bir pencerede açın. Kolaylık olması için, **Tasarımcı** sekmesinde **XAML** düğmesine çift tıklayabilirsiniz; böylece **Görsel Tasarımcı** gizlenir ve kod görünümü için tüm alan ayrılır.

 ![Projeye dahil edilen nuget kitaplığı](./Images/UWPMainPage.png)
 
Metin hizmeti olarak, işlevselliği görselleştirmenin en iyi yolu bir giriş ve çıkış metin bloğu oluşturmaktır. Bunu yapmak için **Kılavuz**’a şu XAML’yi ekleyin. Bu kod, üç bileşen ekler: bir giriş metin kutusu, bir çıkış metin bloğu ve başlat düğmesi.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">5. Adım: Varlık Bağlama Akıllı Hizmeti ekleme işlemine devam edin</a>
 
Şimdi kullanıcı arabirimi oluşturulur. Varlık Bağlama hizmetini kullanmadan önce, button-Click işleyicisini eklemeniz gerekir. **Çözüm Gezgini**’nden **MainPage.xaml** dosyasını açın. Düğmenin sonuna bir button_Click işleyicisi ekleyin.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Kodda bir button-Click işleyicisi uygulanması gerekir. button-Click uygulamak için **Çözüm Gezgini**’nden **MainPage.xaml.cs** dosyasını açın. EntityLinkingServiceClient, Varlık Bağlama yanıtlarını almak için kullanılan bir sarmalayıcıdır. EntityLinkingServiceClient oluşturucu bağımsız değişkeni, Bilişsel Hizmetler abonelik anahtarıdır. Varlık Bağlama hizmetini çağırmak için **1. Adım**’da aldığınız abonelik anahtarını yapıştırın. 

Aşağıda, Varlık Bağlama Hizmeti kullanarak yanıta "wikipediaId" öğesini ekleyen örnek kod yer almaktadır. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Şimdi ilk doğal dil işleme Varlık Bağlama Uygulamanızı çalıştırmaya hazırsınız demektir. Uygulamayı derlemek ve başlatmak için **F5 tuşuna** basın. Metin parçacıklarını veya paragrafları giriş kutusuna yapıştırın. "Sonuç Al" düğmesine basın ve çıkış bloğunda tanımlanan varlıkları gözlemleyin.
 
 ![UWP Örnek sonucu](./Images/DemoCodeResult.png)
 
### <a name="summary">Özet</a>
 
Bu öğreticide, birkaç C# ve XAML kod satırıyla Varlık Bağlama Akıllı Hizmeti İstemci Kitaplığı’ndan yararlanmak için nasıl bir uygulama oluşturulacağını öğrendiniz. 

