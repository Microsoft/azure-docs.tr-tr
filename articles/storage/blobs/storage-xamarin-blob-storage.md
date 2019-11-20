---
title: Xamarin 'ten nesne (blob) depolaması kullanma | Microsoft Docs
description: Xamarin için Azure Storage istemci kitaplığı, geliştiricilerin Yerel Kullanıcı arabirimleriyle iOS, Android ve Windows Mağazası uygulamaları oluşturmalarına olanak sağlar. Bu öğreticide, Azure Blob depolama kullanan bir uygulama oluşturmak için Xamarin 'in nasıl kullanılacağı gösterilmektedir.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726332"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Xamarin 'ten blob depolamayı kullanma

Xamarin, geliştiricilerin Yerel Kullanıcı arabirimleriyle C# IOS, Android ve Windows Mağazası uygulamaları oluşturmak için paylaşılan bir kod temeli kullanmasını sağlar. Bu öğretici, Azure Blob depolamayı bir Xamarin uygulamasıyla nasıl kullanacağınızı gösterir. Azure depolama hakkında daha fazla bilgi edinmek için, koda girmeden önce bkz. [Microsoft Azure depolama giriş](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Yeni bir Xamarin uygulaması oluşturma

Bu öğreticide, Android, iOS ve Windows 'u hedefleyen bir uygulama oluşturacağız. Bu uygulama yalnızca bir kapsayıcı oluşturacak ve bu kapsayıcıya bir blob yükleyecek. Windows üzerinde Visual Studio 'yu kullanacağız, ancak macOS üzerinde Xamarin Studio kullanarak bir uygulama oluştururken aynı dersleri uygulanabilir.

Uygulamanızı oluşturmak için aşağıdaki adımları izleyin:

1. Henüz yapmadıysanız, [Visual Studio Için Xamarin](https://www.xamarin.com/download)'i indirip yükleyin.
2. Visual Studio 'yu açın ve boş bir uygulama (yerel taşınabilir) oluşturun: **Dosya > yeni > projesi > platformlar arası > boş uygulama (yerel taşınabilir)** .
3. Çözüm Gezgini bölmesinde çözümünüze sağ tıklayın ve **çözüm Için NuGet Paketlerini Yönet**' i seçin. **Windowsazure. Storage** araması yapın ve en son kararlı sürümü çözümünüzdeki tüm projelere yükler.
4. Projenizi derleyin ve çalıştırın.

Artık bir sayacı artıran bir düğmeye tıklaetmenize olanak tanıyan bir uygulamanız olmalıdır.

## <a name="create-container-and-upload-blob"></a>Kapsayıcı oluştur ve blobu yükle

Ardından, `(Portable)` projeniz altında, ' ye `MyClass.cs`bir kod ekleyeceksiniz. Bu kod bir kapsayıcı oluşturur ve bu kapsayıcıya bir BLOB yükler. `MyClass.cs`aşağıdaki gibi görünmelidir:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

"Your_account_name_here" ve "your_account_key_here" değerini gerçek hesap adı ve hesap anahtarınızla değiştirdiğinizden emin olun.

İOS, Android ve Windows Phone projeleriniz, taşınabilir projenize başvuru yapmış olabilir. tüm paylaşılan kodunuzu tek bir yerde yazabilir ve tüm projelerinizde kullanabilirsiniz. Bundan sonra yararlanmaya başlamak için her bir projeye aşağıdaki kod satırını ekleyebilirsiniz:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp. DROID > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp. iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp. WinPhone > MainPage. xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Artık bu uygulamayı bir Android veya Windows Phone öykünücüsünde çalıştırabilirsiniz. Bu uygulamayı bir iOS öykünücüsünde de çalıştırabilirsiniz, ancak bunun için bir Mac gerekir. Bunun nasıl yapılacağı hakkında ayrıntılı yönergeler için lütfen [Visual Studio 'yu bir Mac 'e bağlama](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/) belgelerini okuyun

Uygulamanızı çalıştırdığınızda, kapsayıcıyı `mycontainer` depolama hesabınızda oluşturacaktır. Metin içeren blobu `myblob`içermelidir,. `Hello, world!` [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/)kullanarak bunu doğrulayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, özellikle blob depolamada bir senaryoya odaklanarak Azure Storage kullanan Xamarin 'te platformlar arası uygulama oluşturmayı öğrendiniz. Ancak, yalnızca blob depolamayla değil, tablo, dosya ve kuyruk depolama ile de çok daha fazlasını yapabilirsiniz. Daha fazla bilgi edinmek için lütfen aşağıdaki makalelere göz atın:

* [.NET kullanarak Azure Blob Depolama ile çalışmaya başlama](storage-dotnet-how-to-use-blobs.md)
* [Azure dosyaları 'na giriş](../files/storage-files-introduction.md)
* [.NET ile Azure Dosyaları için geliştirme](../files/storage-dotnet-how-to-use-files.md)
* [.NET kullanarak Azure Tablo Depolama ile çalışmaya başlama](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.NET kullanarak Azure Kuyruk Depolama ile çalışmaya başlama](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
