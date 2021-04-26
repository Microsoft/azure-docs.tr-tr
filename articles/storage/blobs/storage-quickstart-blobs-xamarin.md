---
title: 'Hızlı başlangıç: Azure Blob depolama kitaplığı V12-Xamarin'
description: Bu hızlı başlangıçta, blob (nesne) deposunda bir kapsayıcı ve BLOB oluşturmak için Xamarin ile Azure Blob depolama istemci kitaplığı sürüm 12 ' yi nasıl kullanacağınızı öğreneceksiniz. Daha sonra, Blobun mobil cihazınıza nasıl indirileceği ve bir kapsayıcıdaki tüm Blobları nasıl listeleyeceğinizi öğrenirsiniz.
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c6b502bbc56d6c9521ec736150fbc0046ff78125
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642478"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Hızlı başlangıç: Xamarin ile Azure Blob depolama istemci kitaplığı V12

Xamarin ile Azure Blob depolama istemci kitaplığı V12 ile çalışmaya başlayın. Azure Blob depolama, Microsoft 'un bulut için nesne depolama çözümüdür. Paketi yüklemek ve temel görevler için örnek kodu denemek için adımları izleyin. Blob depolama, çok miktarda yapılandırılmamış veriyi depolamak için iyileştirilmiştir.

Xamarin ile Azure Blob depolama istemci kitaplığı V12 kullanın:

* Kapsayıcı oluşturma
* Azure Depolama'ya blob yükleme
* Bir kapsayıcıdaki tüm Blobları listeleme
* Blob 'u cihazınıza indirin
* Kapsayıcı silme

Başvuru bağlantıları:

* [API başvuru belgeleri](/dotnet/api/azure.storage.blobs)
* [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Örnek](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* Azure depolama hesabı- [depolama hesabı oluşturma](../common/storage-account-create.md)
* Visual Studio, [.NET iş yüküne yönelik mobil geliştirme](/xamarin/get-started/installation/?pivots=windows) veya [Mac için Visual Studio](/visualstudio/mac/installation?view=vsmac-2019&preserve-view=true)

## <a name="setting-up"></a>Ayarlanıyor
    
Bu bölümde, bir projeyi Xamarin ile Azure Blob depolama istemci kitaplığı V12 ile çalışacak şekilde hazırlama adımları gösterilmektedir.
    
### <a name="create-the-project"></a>Proje oluşturma

1. Visual Studio 'Yu açın ve boş bir form uygulaması oluşturun.
1. Adlandırın: BlobQuickstartV12

### <a name="install-the-package"></a>Paketi yükler

1. Çözüm Gezgini bölmesinde çözümünüze sağ tıklayın ve **çözüm Için NuGet Paketlerini Yönet**' i seçin.
1. **Azure. Storage. bloblarını** arayın ve en son kararlı sürümü çözümünüzdeki tüm projelere yükler.

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

**BlobQuickstartV12** dizininden:

1. Düzenleyicinizde *MainPage. xaml* dosyasını açın
1. Öğeler arasındaki her şeyi kaldırın `<ContentPage></ContentPage>` ve aşağıdaki kodla değiştirin:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Nesne modeli

Azure Blob depolama, büyük miktarlarda yapılandırılmamış verileri depolamak için iyileştirilmiştir. Yapılandırılmamış veriler, metin veya ikili veriler gibi belirli bir veri modeline veya tanıma bağlı olmayan verilerdir. BLOB depolama üç tür kaynak sunar:

* Depolama hesabı
* Depolama hesabındaki bir kapsayıcı
* Kapsayıcıda bir blob

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![BLOB depolama mimarisi diyagramı](./media/storage-blobs-introduction/blob1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki .NET sınıflarını kullanın:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` sınıfı, Azure depolama kaynaklarını ve BLOB kapsayıcılarını değiştirmenize olanak sağlar.
* [Blobcontainerclient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` sınıfı, Azure depolama kapsayıcılarını ve bloblarını değiştirmenize olanak sağlar.
* [Blobclient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` sınıfı, Azure Storage bloblarını değiştirmenize izin verir.
* [Blobdownloadınfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` sınıfı, blob indirilmeden döndürülen özellikleri ve içeriği temsil eder.

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, Xamarin. Forms uygulamasında .NET için Azure Blob depolama istemci kitaplığı ile aşağıdaki görevlerin nasıl gerçekleştirileceğini gösterir:

* [Sınıf düzeyi değişkenleri oluştur](#create-class-level-variables)
* [Kapsayıcı oluşturma](#create-a-container)
* [Blobları bir kapsayıcıya yükleme](#upload-blobs-to-a-container)
* [Kapsayıcıdaki blobları listeleme](#list-the-blobs-in-a-container)
* [Blob’ları indirme](#download-blobs)
* [Kapsayıcı silme](#delete-a-container)

### <a name="create-class-level-variables"></a>Sınıf düzeyi değişkenleri oluştur

Aşağıdaki kod birkaç sınıf düzeyi değişkenini bildirir. Bu örneğin geri kalanı boyunca Azure Blob depolama ile iletişim kurması gerekir.

Bunlar, [depolama bağlantı dizesini yapılandırma](#configure-your-storage-connection-string) bölümünde ayarlanan depolama hesabı için bağlantı dizesine ek niteliğindedir.

Bu kodu *MainPage. xaml. cs* dosyasında sınıf düzeyi değişkenleri olarak ekleyin:

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Kapsayıcı oluşturma

Yeni kapsayıcı için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kapsayıcı adına bir GUID değeri ekler.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcıları ve blobları adlandırma hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) sınıfının bir örneğini oluşturun. Ardından, depolama hesabınızda kapsayıcıyı oluşturmak için [Createblobcontainsısync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) yöntemini çağırın.

Bu kodu *MainPage. xaml. cs* dosyasına ekleyin:

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Blobları bir kapsayıcıya yükleme

Aşağıdaki kod parçacığı:

1. Bir `MemoryStream` metin oluşturur.
1. [Blobcontainerclient](/dotnet/api/azure.storage.blobs.blobcontainerclient) sınıfının [uploadasync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) işlevini çağırarak metin dosyası adına ve metin Içine geçirerek metni bir bloba yükler `MemoryStream` . Bu yöntem, daha önce oluşturulmadıysa bir blob oluşturur, aksi takdirde üzerine yazar.

Bu kodu *MainPage. xaml. cs* dosyasına ekleyin:

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

[Getblobsasync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) yöntemini çağırarak kapsayıcıdaki Blobları listeleyin. Bu durumda, kapsayıcıya yalnızca bir blob eklenmiş, bu nedenle listeleme işlemi yalnızca bir BLOB döndürüyor.

Bu kodu *MainPage. xaml. cs* dosyasına ekleyin:

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Blob’ları indirme

[Downloadtoasync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) yöntemini çağırarak önceden oluşturulmuş blobu indirin. Örnek kod, bir `Stream` Blobun ilk önce bir ' a, `MemoryStream` sonra da metnin görüntülenebilmesi için bir öğesine kopyalar `StreamReader` .

Bu kodu *MainPage. xaml. cs* dosyasına ekleyin:

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Kapsayıcı silme

Aşağıdaki kod, [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)kullanarak tüm kapsayıcıyı silerek uygulamanın oluşturduğu kaynakları temizler.

Uygulama ilk olarak blobu ve kapsayıcıyı silmeden önce onaylamanızı ister. Bu, kaynakların silinmeden önce doğru bir şekilde oluşturulduğunu doğrulamak iyi bir şansınız olur.

Bu kodu *MainPage. xaml. cs* dosyasına ekleyin:

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulama başlatıldığında, önce kapsayıcıyı göründüğü şekilde oluşturur. Daha sonra, Blobları karşıya yüklemek, listelemek, indirmek ve kapsayıcıyı silmek için düğmelere tıklacaksınız.

Uygulamayı Windows üzerinde çalıştırmak için F5 tuşuna basın. Uygulamayı Mac üzerinde çalıştırmak için cmd + ENTER tuşlarına basın.

Uygulama her işlemden sonra ekrana yazar. Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Temizleme işlemine başlamadan önce, Blob içeriğinin ekrandaki çıkışının karşıya yüklenen değerle eşleştiğini doğrulayın.

Değerleri doğruladıktan sonra, kapsayıcıyı silme ve tanıtımı bitiş uyarısını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Xamarin ile Azure Blob depolama istemci kitaplığı V12 kullanarak Blobları karşıya yükleme, indirme ve listeleme hakkında daha fazla öğrendiniz.

BLOB depolama örnek uygulamalarını görmek için devam edin:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Xamarin örneği](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Öğreticiler, örnekler, hızlı ve diğer belgelerde, [mobil geliştiriciler Için Azure](/azure/mobile-apps)' u ziyaret edin.
* Xamarin hakkında daha fazla bilgi edinmek için bkz. [Xamarin ile çalışmaya](/xamarin/get-started/)başlama.
