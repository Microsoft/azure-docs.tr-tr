---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 02/25/2021
ms.openlocfilehash: f8c15b1236d15c193638842ce4dab7e81cd70ace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102184244"
---
Bu kılavuz, bir nesne algılama modeli oluşturmak üzere Go için Özel Görüntü İşleme istemci kitaplığını kullanmaya başlamanıza yardımcı olacak yönergeler ve örnek kod sağlar. Bir proje oluşturacak, Etiketler ekleyecek, projeyi eğtireceksiniz ve projenin tahmin uç nokta URL 'sini programlı bir şekilde test etmek üzere kullanacaksınız. Bu örneği kendi görüntü tanıma uygulamanızı oluşturmak için bir şablon olarak kullanın.

> [!NOTE]
> Bir nesne _algılama modelini kod yazmadan derlemek_ ve eğitebilmek istiyorsanız, bunun yerine [tarayıcı tabanlı kılavuza](../../get-started-build-detector.md) bakın.

Git için Özel Görüntü İşleme istemci kitaplığını kullanın:

* Yeni bir Özel Görüntü İşleme projesi oluşturma
* Projeye Etiketler ekleyin
* Görüntüleri karşıya yükleme ve etiketleme
* Projeyi eğitme
* Geçerli yinelemeyi Yayımla
* Tahmin uç noktasını test etme

Başvuru belgeleri [(eğitim)](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training) [(tahmin)](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)| Kitaplık kaynak kodu [(eğitim)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/customvision/training) [(tahmin)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.1/customvision/prediction) 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Go 1.8 +](https://golang.org/doc/install)
* Azure aboneliğiniz olduktan sonra bir Özel Görüntü İşleme kaynak oluşturun ve bir <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> eğitim ve tahmin kaynağı oluşturmak ve anahtarlarınızı ve uç noktanızı almak için Azure Portal özel görüntü işleme bir kaynak oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesine tıklayın.
    * Uygulamanızı Özel Görüntü İşleme bağlamak için oluşturduğunuz kaynaklarda anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="install-the-custom-vision-client-library"></a>Özel Görüntü İşleme istemci kitaplığını yükler

Go için Özel Görüntü İşleme bir görüntü analizi uygulaması yazmak için Özel Görüntü İşleme hizmeti istemci kitaplığı gerekir. PowerShell'de aşağıdaki komutu çalıştırın:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

veya kullanıyorsanız `dep` , deponuzda çalıştırın:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```


[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="create-the-custom-vision-project"></a>Özel Görüntü İşleme projesi oluşturma

Örnek adlı yeni bir dosya oluşturun. tercih ettiğiniz proje dizinine *gidin* ve tercih ettiğiniz kod düzenleyicisinde açın.

Yeni bir Özel Görüntü İşleme hizmeti projesi oluşturmak için betiğinize aşağıdaki kodu ekleyin. Abonelik anahtarlarınızı uygun tanımlara ekleyin. Ayrıca, Özel Görüntü İşleme Web sitesinin ayarlar sayfasından uç nokta URL 'nizi alın.

Projenizi oluştururken diğer seçenekleri belirtmek için bkz. [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) yöntemi ( [bir algılayıcı derleme](../../get-started-build-detector.md) Web portalı kılavuzunda açıklanmıştır).

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample OD Project"
    iteration_publish_name = "detectModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    var objectDetectDomain training.Domain
    domains, _ := trainer.GetDomains(ctx)

    for _, domain := range *domains.Value {
        fmt.Println(domain, domain.Type)
        if domain.Type == "ObjectDetection" && *domain.Name == "General" {
            objectDetectDomain = domain
            break
        }
    }
    fmt.Println("Creating project...")
    project, _ := trainer.CreateProject(ctx, project_name, "", objectDetectDomain.ID, "")
```

## <a name="create-tags-in-the-project"></a>Projede etiketler oluşturma

Projenize sınıflandırma etiketleri oluşturmak için aşağıdaki kodu örnek sonuna ekleyin *. git*:

```Go
# Make two tags in the new project
forkTag, _ := trainer.CreateTag(ctx, *project.ID, "fork", "A fork", string(training.Regular))
scissorsTag, _ := trainer.CreateTag(ctx, *project.ID, "scissors", "Pair of scissors", string(training.Regular))
```

## <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

Nesne algılama projelerinde resimleri etiketlediğinizde, her etiketlenmiş nesnenin bölgesini normalleştirilmiş koordinatları kullanarak belirtmeniz gerekir.

> [!NOTE]
> Bölgelerin koordinatlarını işaretlemek için bir tıklama ve sürükleme yardımcı programına sahip değilseniz, [Customvision.ai](https://www.customvision.ai/)adresindeki Web Kullanıcı arabirimini kullanabilirsiniz. Bu örnekte, koordinatlar zaten sağlanmış.

Projeye görüntüler, etiket ve bölgeler eklemek için etiket oluşturduktan sonra aşağıdaki kodu ekleyin. Bu öğreticide bölgelerin satır içi sabit kodlanmış olduğunu unutmayın. Bölgeler, sınırlayıcı kutuyu normalleştirilmiş koordinatlarıyla belirtir ve koordinatlar şu sırayla verilir: sol, üst, genişlik, yükseklik.

```Go
forkImageRegions := map[string][4]float64{
    "fork_1.jpg": [4]float64{ 0.145833328, 0.3509314, 0.5894608, 0.238562092 },
    "fork_2.jpg": [4]float64{ 0.294117659, 0.216944471, 0.534313738, 0.5980392 },
    "fork_3.jpg": [4]float64{ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 },
    "fork_4.jpg": [4]float64{ 0.254901975, 0.185898721, 0.5232843, 0.594771266 },
    "fork_5.jpg": [4]float64{ 0.2365196, 0.128709182, 0.5845588, 0.71405226 },
    "fork_6.jpg": [4]float64{ 0.115196079, 0.133611143, 0.676470637, 0.6993464 },
    "fork_7.jpg": [4]float64{ 0.164215669, 0.31008172, 0.767156839, 0.410130739 },
    "fork_8.jpg": [4]float64{ 0.118872553, 0.318251669, 0.817401946, 0.225490168 },
    "fork_9.jpg": [4]float64{ 0.18259804, 0.2136765, 0.6335784, 0.643790841 },
    "fork_10.jpg": [4]float64{ 0.05269608, 0.282303959, 0.8088235, 0.452614367 },
    "fork_11.jpg": [4]float64{ 0.05759804, 0.0894935, 0.9007353, 0.3251634 },
    "fork_12.jpg": [4]float64{ 0.3345588, 0.07315363, 0.375, 0.9150327 },
    "fork_13.jpg": [4]float64{ 0.269607842, 0.194068655, 0.4093137, 0.6732026 },
    "fork_14.jpg": [4]float64{ 0.143382356, 0.218578458, 0.7977941, 0.295751631 },
    "fork_15.jpg": [4]float64{ 0.19240196, 0.0633497, 0.5710784, 0.8398692 },
    "fork_16.jpg": [4]float64{ 0.140931368, 0.480016381, 0.6838235, 0.240196079 },
    "fork_17.jpg": [4]float64{ 0.305147052, 0.2512582, 0.4791667, 0.5408496 },
    "fork_18.jpg": [4]float64{ 0.234068632, 0.445702642, 0.6127451, 0.344771236 },
    "fork_19.jpg": [4]float64{ 0.219362751, 0.141781077, 0.5919118, 0.6683006 },
    "fork_20.jpg": [4]float64{ 0.180147052, 0.239820287, 0.6887255, 0.235294119 },
}

scissorsImageRegions := map[string][4]float64{
    "scissors_1.jpg": [4]float64{ 0.4007353, 0.194068655, 0.259803921, 0.6617647 },
    "scissors_2.jpg": [4]float64{ 0.426470578, 0.185898721, 0.172794119, 0.5539216 },
    "scissors_3.jpg": [4]float64{ 0.289215684, 0.259428144, 0.403186262, 0.421568632 },
    "scissors_4.jpg": [4]float64{ 0.343137264, 0.105833367, 0.332107842, 0.8055556 },
    "scissors_5.jpg": [4]float64{ 0.3125, 0.09766343, 0.435049027, 0.71405226 },
    "scissors_6.jpg": [4]float64{ 0.379901975, 0.24308826, 0.32107842, 0.5718954 },
    "scissors_7.jpg": [4]float64{ 0.341911763, 0.20714055, 0.3137255, 0.6356209 },
    "scissors_8.jpg": [4]float64{ 0.231617644, 0.08459154, 0.504901946, 0.8480392 },
    "scissors_9.jpg": [4]float64{ 0.170343131, 0.332957536, 0.767156839, 0.403594762 },
    "scissors_10.jpg": [4]float64{ 0.204656869, 0.120539248, 0.5245098, 0.743464053 },
    "scissors_11.jpg": [4]float64{ 0.05514706, 0.159754932, 0.799019635, 0.730392158 },
    "scissors_12.jpg": [4]float64{ 0.265931368, 0.169558853, 0.5061275, 0.606209159 },
    "scissors_13.jpg": [4]float64{ 0.241421565, 0.184264734, 0.448529422, 0.6830065 },
    "scissors_14.jpg": [4]float64{ 0.05759804, 0.05027781, 0.75, 0.882352948 },
    "scissors_15.jpg": [4]float64{ 0.191176474, 0.169558853, 0.6936275, 0.6748366 },
    "scissors_16.jpg": [4]float64{ 0.1004902, 0.279036, 0.6911765, 0.477124184 },
    "scissors_17.jpg": [4]float64{ 0.2720588, 0.131977156, 0.4987745, 0.6911765 },
    "scissors_18.jpg": [4]float64{ 0.180147052, 0.112369314, 0.6262255, 0.6666667 },
    "scissors_19.jpg": [4]float64{ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 },
    "scissors_20.jpg": [4]float64{ 0.158088237, 0.04047389, 0.6691176, 0.843137264 },
}
```
Ardından, her bir örnek görüntüsünü bölge koordinatları ile karşıya yüklemek için bu ilişkilerin haritasını kullanın (tek bir toplu işte en fazla 64 görüntü yükleyebilirsiniz). Aşağıdaki kodu ekleyin.

> [!NOTE]
> Bilişsel hizmetler git SDK örnekleri projesini daha önce indirdiğiniz yere göre görüntülerin yolunu değiştirmeniz gerekir.

```Go
// Go through the data table above and create the images
fmt.Println("Adding images...")
var fork_images []training.ImageFileCreateEntry
for file, region := range forkImageRegions {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "fork", file))

    imageRegion := training.Region { 
        TagID:forkTag.ID,
        Left:&region[0],
        Top:&region[1],
        Width:&region[2],
        Height:&region[3],
    }

    fork_images = append(fork_images, training.ImageFileCreateEntry {
        Name: &file,
        Contents: &imageFile,
        Regions: &[]training.Region{ imageRegion },
    })
}
    
fork_batch, _ := trainer.CreateImagesFromFiles(ctx, *project.ID, training.ImageFileCreateBatch{ 
    Images: &fork_images,
})

if (!*fork_batch.IsBatchSuccessful) {
    fmt.Println("Batch upload failed.")
}

var scissor_images []training.ImageFileCreateEntry
for file, region := range scissorsImageRegions {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "scissors", file))

    imageRegion := training.Region { 
        TagID:scissorsTag.ID,
        Left:&region[0],
        Top:&region[1],
        Width:&region[2],
        Height:&region[3],
    }

    scissor_images = append(scissor_images, training.ImageFileCreateEntry {
        Name: &file,
        Contents: &imageFile,
        Regions: &[]training.Region{ imageRegion },
    })
}
    
scissor_batch, _ := trainer.CreateImagesFromFiles(ctx, *project.ID, training.ImageFileCreateBatch{ 
    Images: &scissor_images,
})
    
if (!*scissor_batch.IsBatchSuccessful) {
    fmt.Println("Batch upload failed.")
}     
```

## <a name="train-and-publish-the-project"></a>Projeyi eğitme ve yayımlama

Bu kod, tahmin modelinin ilk yinelemesini oluşturur ve ardından bu yinelemeyi tahmin uç noktasına yayınlar. Yayımlanan yinelemeye verilen ad, tahmin istekleri göndermek için kullanılabilir. Bir yineleme, yayınlanana kadar tahmin uç noktasında kullanılamaz.

```go
iteration, _ := trainer.TrainProject(ctx, *project.ID)
fmt.Println("Training status:", *iteration.Status)
for {
    if *iteration.Status != "Training" {
        break
    }
    time.Sleep(5 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
    fmt.Println("Training status:", *iteration.Status)
}

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

## <a name="use-the-prediction-endpoint"></a>Tahmin uç noktasını kullanma

Tahmin uç noktasına bir görüntü göndermek ve tahmini almak için dosyanın sonuna aşağıdaki kodu ekleyin:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_od_image.jpg"))
    results, _ := predictor.DetectImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        boundingBox := *prediction.BoundingBox

        fmt.Printf("\t%s: %.2f%% (%.2f, %.2f, %.2f, %.2f)", 
            *prediction.TagName,
            *prediction.Probability * 100,
            *boundingBox.Left,
            *boundingBox.Top,
            *boundingBox.Width,
            *boundingBox.Height)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

*Örnek. go* çalıştırın.

```shell
go run sample.go
```

Uygulamanın çıkışı konsolda görüntülenmelidir. Ardından test görüntüsünün (**samples/vision/images/Test** yolunda bulunur) uygun etiketlendiğini ve algılama bölgesinin doğru olduğunu onaylayabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Sonraki adımlar

Artık koddaki nesne algılama işleminin her adımını tamamladınız. Bu örnek tek bir eğitim yinelemesi yürütür, ancak genellikle modelinizi daha doğru hale getirmek için birden çok kez eğitmeniz ve test etmeniz gerekir. Sonraki kılavuzda görüntü sınıflandırma konusu üstünde durulur ancak temel ilkeleri nesne algılamaya benzer.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](../../test-your-model.md)

* [Özel Görüntü İşleme nedir?](../../overview.md)
* [SDK başvuru belgeleri (eğitim)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training)
* [SDK başvuru belgeleri (tahmin)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)
