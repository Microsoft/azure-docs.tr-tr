---
title: 'Öğretici: R ile ilk ML denemeniz'
titleSuffix: Azure Machine Learning
description: Bu öğreticide, Azure Machine Learning temel tasarım düzenlerini öğrenirsiniz ve bir lojistik regresyon modeli modelini, bir otomobil yanlışlıkla bir Fatality olasılığını tahmin etmek için R Packages azuremlsdk ve şapka işareti kullanarak eğitebilirsiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 11/04/2019
ms.openlocfilehash: 72ab2717cea479de6150f435398f164c7c9d5937
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092273"
---
# <a name="tutorial-train-and-deploy-your-first-model-in-r-with-azure-machine-learning"></a>Öğretici: Azure Machine Learning ile ilk modelinizi eğitim ve dağıtma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu öğreticide, Azure Machine Learning temel tasarım düzenlerini öğrenirsiniz.  Bir Fatality, yanlışlıkla bir otomobil olma olasılığını tahmin etmek için bir giriş **işareti** modeli eğtireceksiniz ve dağıtırsınız. Bu Öğreticiyi tamamladıktan sonra, daha karmaşık denemeleri ve iş akışları geliştirmek üzere ölçeğini ölçeklendirmek için R SDK 'sının pratik bilgisine sahip olacaksınız.

Bu öğreticide, aşağıdaki görevleri öğreneceksiniz:

> [!div class="checklist"]
> * Çalışma alanınızı bağlama
> * Veri yükleme ve eğitim için hazırlanma
> * Verileri veri deposuna yükleyin, böylece uzak eğitim için kullanılabilir hale gelir
> * İşlem kaynağı oluşturma
> * Fatality olasılığını tahmin etmek için bir giriş işareti modeli eğitme
> * Tahmin uç noktası dağıtma
> * Modeli R 'den test etme

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

## <a name="prerequisites"></a>Önkoşullar

1. [Yükleme yönergelerini](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) izleyerek şunları yapın:
    + Anaconda 'yı yükler
    + `azuremlsdk` yüklensin
    + Python için Azure Machine Learning SDK 'sını yükler

1. [GitHub](https://github.com/Azure/azureml-sdk-for-r/tree/master/vignettes/train-and-deploy-with-caret)'dan üç öğretici dosyası alın.  Bunları bir **öğreticiler** dizinine kaydedin.

2. Bir Azure Machine Learning çalışma alanı oluşturun ve aşağıdaki adımları kullanarak yapılandırma dosyasını indirin.


### <a name="create-a-workspace"></a>Çalışma alanı oluşturma

Azure Machine Learning çalışma alanı, bulutta makine öğrenimi modellerini denemek, eğmek ve dağıtmak için kullandığınız temel bir kaynaktır. Azure aboneliğiniz ve kaynak grubunuz SDK 'daki kolayca tüketilen bir nesneye sahiptir. Zaten bir Azure Machine Learning çalışma alanınız varsa, [sonraki bölüme atlayın](#config). Aksi takdirde, şimdi bir tane oluşturun.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="config"></a>Config. json dosyasını indir

1. Çalışma alanı sayfasının en üstünde **config. json dosyasını indir**' i seçin.

    ![Config. json dosyasını indir](media/tutorial-1st-r-experiment/download-config.png)  

1. Bu dosyayı **öğreticiler** dizininize ekleyin.

Artık öğreticiyi çalıştırmaya hazırsınız.

Bu öğreticiyi çalıştırmak için RStudio kullanmanızı öneririz. RStudio 'da, mevcut dizini > Yeni Proje > Dosya ' yı seçin ve yukarıda oluşturduğunuz **öğreticiler** dizinini seçin.

> [!Important]
> Bu makalenin geri kalanında, **eğitme ve dağıtma-aci ile aynı içeriğe sahip olursunuz. RMD** dosyası.  
> Rmarkkiyle karşılaşırsanız, bu dosyadaki kodu kullanmayı ücretsiz olarak kullanabilirsiniz.  Ya da kod parçacıklarını buradan veya bu makaleden bir R betiğine veya komut satırına kopyalayabilir/yapıştırabilirsiniz.


## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma
Bu öğreticide geliştirme çalışmalarınız için kurulum aşağıdaki eylemleri içerir:

* Gerekli paketleri yükleme
* Yerel bilgisayarınızın uzak kaynaklarla iletişim kurabilmesi için bir çalışma alanına bağlanın
* Çalıştırmalarınızı izlemek için bir deneme oluşturun
* Eğitim için kullanmak üzere uzak bir işlem hedefi oluşturun

### <a name="install-required-packages"></a>Gerekli paketleri yükleme
Bu öğreticide, Azure ML SDK 'sının zaten yüklü olduğu varsayılır. Devam edin ve **azuremlsdk** paketini içeri aktarın.

```R
library(azuremlsdk)
```

Öğretici, [ **davag** paketindeki](https://cran.r-project.org/package=DAAG)verileri kullanır. Paketi yoksa, yükleyebilirsiniz.

```R
install.packages("DAAG")
```

Eğitim ve Puanlama betikleri (`accidents.R` ve `accident_predict.R`) bazı ek bağımlılıklara sahiptir. Bu betikleri yerel olarak çalıştırmayı planlıyorsanız, gerekli paketlerin de bulunduğundan emin olun.

### <a name="load-your-workspace"></a>Çalışma alanınızı yükleme
Mevcut çalışma alanınızdan bir çalışma alanı nesnesi örneği oluşturun. Aşağıdaki kod, **config. JSON** dosyasından çalışma alanı ayrıntılarını yükler. Ayrıca [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html)kullanarak bir çalışma alanı alabilirsiniz.

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Deneme oluşturma
Azure ML denemesi, genellikle aynı eğitim betiğiyle çalıştırılan bir gruplamayı izler. Kazalardan dolayı verilerinde giriş işareti modeline eğitim için çalıştırmaları izlemek üzere bir deneme oluşturun.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>İşlem hedefi oluşturmak
Yönetilen bir hizmet olan Azure Machine Learning Işlem (AmlCompute) kullanarak, veri bilimcileri, Azure sanal makinelerinin kümelerinde makine öğrenimi modellerini eğitebilir. Örnek olarak GPU desteği olan VM 'Ler sayılabilir. Bu öğreticide, eğitim ortamınız olarak tek düğümlü bir AmlCompute kümesi oluşturacaksınız. Aşağıdaki kod, çalışma alanınızda zaten mevcut değilse işlem kümesini sizin için oluşturur.

Zaten mevcut değilse, işlem kümenizin sağlanması için birkaç dakika beklemeniz gerekebilir.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Eğitim için verileri hazırlama
Bu öğretici, **Dadag** paketindeki verileri kullanır. Bu veri kümesi, Fatality olma olasılığını tahmin etmek için kullanabileceğiniz değişkenlerle birlikte ABD 'de 25.000 otomobil kilitlenmelerinden veri içerir. İlk olarak, verileri R 'ye aktarın ve analiz için `accidents` yeni bir veri çerçevesine dönüştürün ve bir `Rdata` dosyasına dışarı aktarın.

```R
library(DAAG)
data(nassCDS)

accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Veri deposuna veri yükleme
Uzaktan Eğitim ortamınıza erişebilmek için verileri buluta yükleyin. Her Azure ML çalışma alanı, bağlantı bilgilerini, çalışma alanına bağlı depolama hesabında sağlanan Azure Blob kapsayıcısına depolayan bir varsayılan veri deposu ile birlikte gelir. Aşağıdaki kod, yukarıda oluşturduğunuz kazalardan dolayı verilerini bu veri deposuna karşıya yükler.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Modeli eğitme

Bu öğreticide, uzaktan işlem kümenizi kullanarak karşıya yüklenen verilerinize bir lojistik regresyon modeli uydurun. Bir iş göndermek için şunları yapmanız gerekir:

* Eğitim betiğini hazırlama
* Tahmin aracı oluşturma
* İşi gönderme

### <a name="prepare-the-training-script"></a>Eğitim betiğini hazırlama
Bu öğreticiyle aynı dizinde sizin için `accidents.R` adlı bir eğitim betiği sunulmaktadır. Eğitim için Azure ML hizmetinden yararlanmak üzere yapılan **eğitim betiği içinde** aşağıdaki ayrıntılara dikkat edin:

* Eğitim betiği, eğitim verilerini içeren dizini bulmak için `-d` bir bağımsız değişken alır. İşinizi daha sonra tanımlayıp gönderdiğinizde, bu bağımsız değişken için veri deposuna işaret edersiniz. Azure ML, eğitim işi için depolama klasörünü uzak kümeye bağlayacaktır.
* Eğitim betiği, `log_metric_to_run()`kullanarak Azure ML 'deki çalıştırma kaydına bir ölçüm olarak son doğruluğu günlüğe kaydeder. Azure ML SDK, eğitim çalıştırmaları sırasında çeşitli ölçümleri günlüğe kaydetmek için bir günlük API kümesi sağlar. Bu ölçümler, deneme çalıştırması kaydında kaydedilir ve kalıcı hale getirilir. Ölçümlere daha sonra istediğiniz zaman erişilebilir veya [Azure Machine Learning Studio](https://ml.azure.com)'daki çalıştırma ayrıntıları sayfasında görüntüleyebilirsiniz. `log_*()`günlük yöntemlerinin tamamına yönelik [başvuruya](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) bakın.
* Eğitim betiği, modelinizi **çıktılar**adlı bir dizine kaydeder. `./outputs` klasörü, Azure ML tarafından özel bir işleme alır. Eğitim sırasında, `./outputs` yazılan dosyalar Azure ML tarafından çalıştırma kaydlarınıza otomatik olarak yüklenir ve yapıtlar olarak kalıcı hale getirilir. Eğitilen modeli `./outputs`kaydederek, çalışma bittikten sonra ve uzak eğitim ortamınıza artık erişiminiz olmadığında bile model dosyanıza erişebiliyor ve bu dosyayı alabilirsiniz.

### <a name="create-an-estimator"></a>Tahmin aracı oluşturma

Azure ML tahmin aracı, işlem hedefinde bir eğitim betiği yürütmek için gereken çalıştırma yapılandırma bilgilerini kapsüller. Azure ML çalıştırmaları, belirtilen işlem hedefi üzerinde Kapsayıcılı işler olarak çalıştırılır. Varsayılan olarak, eğitim işiniz için tasarlanan Docker görüntüsü R, Azure ML SDK ve yaygın olarak kullanılan bir R paketi kümesi içerir. Varsayılan paketlerin tam listesine buraya bakın.

Estimator oluşturmak için şunu tanımlayın:

* Eğitim için gereken betiklerinizi içeren dizin (`source_directory`). Bu dizindeki tüm dosyalar, yürütme için küme düğümüne yüklenir. Dizin, eğitim betiğinizi ve gereken ek betikleri içermelidir.
* Yürütülecek eğitim betiği (`entry_script`).
* Bu örnekte, daha önce oluşturduğunuz AmlCompute kümesi, bu durumda işlem hedefi (`compute_target`).
* Eğitim betiğiyle gerekli parametreler (`script_params`). Azure ML, eğitim betiğinizi `Rscript`bir komut satırı betiği olarak çalıştıracaktır. Bu öğreticide, `ds$path(target_path)`ile erişebileceğiniz veri dizini bağlama noktası olan betiğe bir bağımsız değişken belirtirsiniz.
* Eğitim için gereken tüm ortam bağımlılıkları. Eğitim için tasarlanan varsayılan Docker görüntüsü, eğitim betiğinde gerekli olan üç paketi (`caret`, `e1071`ve `optparse`) içerir.  Bu nedenle ek bilgi belirtmeniz gerekmez. Varsayılan olarak dahil olmayan R paketleri kullanıyorsanız, ek CRAN paketleri eklemek için Estimator 'ın `cran_packages` parametresini kullanın. Yapılandırılabilir seçeneklerin tam kümesi için [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) başvurusuna bakın.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>İşi uzak kümede gönder

Son olarak, işi kümenizde çalışacak şekilde gönderebilirsiniz. `submit_experiment()`, çalıştırmaya göre arabirim için kullandığınız bir Run nesnesi döndürür. Toplam olarak, ilk çalıştırma **yaklaşık 10 dakika**sürer. Ancak daha sonra çalıştırıldığında, komut dosyası bağımlılıkları değişmedikçe aynı Docker görüntüsü yeniden kullanılır.  Bu durumda, görüntü önbelleğe alınır ve kapsayıcı başlangıç zamanı çok daha hızlıdır.

```R
run <- submit_experiment(exp, est)
```

Çalıştırmanın ayrıntılarını RStudio görüntüleyicisinde görüntüleyebilirsiniz. Belirtilen "Web görünümü" bağlantısına tıkladığınızda, Kullanıcı arabiriminde çalışmayı izleyebileceğiniz Azure Machine Learning Studio 'ya gidecektir.

```R
view_run_details(run)
```

Model eğitimi arka planda gerçekleşir. Daha fazla kod çalıştırmadan önce model eğitimi bitirene kadar bekleyin.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Siz ve çalışma alanına erişimi olan iş arkadaşları--paralel olarak birden çok denemeleri gönderebilir ve Azure ML, işlem kümesindeki görevleri zamanlamaya göre planlamalarını sağlar. Kümeyi, birden fazla düğüme otomatik olarak ölçeklenebilen şekilde yapılandırabilir ve kuyrukta daha fazla işlem görevi olmadığında ölçeği geri ölçeklendirebilirsiniz. Bu yapılandırma ekiplerin işlem kaynaklarını paylaşması için ekonomik bir yoldur.

## <a name="retrieve-training-results"></a>Eğitim sonuçlarını alma
Modelinize yönelik eğitimi tamamladıktan sonra, kaydedilen tüm ölçümler ve son eğitilen model dahil olmak üzere, çalışma kaydında kalıcı olan iş yapıtlarına erişebilirsiniz.

### <a name="get-the-logged-metrics"></a>Günlüğe kaydedilen ölçümleri alın
Eğitim betiğindeki `accidents.R`, modelinizden bir ölçüm günlüğe kaydedilir: eğitim verilerinde tahminlerinin doğruluğu. Ölçümleri [Studio](https://ml.azure.com)'da görebilir veya bir R listesi olarak yerel oturuma aşağıdaki gibi ayıklayabilirsiniz:

```R
metrics <- get_run_metrics(run)
metrics
```

Birden çok denemeleri (örneğin, farklı değişkenler, algoritmalar veya hiper paramers kullanarak) çalıştırırsanız, üretimde kullanacağınız modeli karşılaştırmak ve seçmek için her çalıştıranların ölçümlerini kullanabilirsiniz.

### <a name="get-the-trained-model"></a>Eğitilen modeli al
Eğitilen modeli alabilir ve yerel R oturumunuzda sonuçlara bakabilirsiniz. Aşağıdaki kod, model dosyasını içeren `./outputs` dizininin içeriğini indirir.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Tahmini ölüm olasılığının artışına katkıda bulunan bazı faktörler görürsünüz:

* daha yüksek etki hızı 
* Erkek sürücü
* daha eski işant
* yolcular

Şu ile ölümün daha düşük olasılıkların olduğunu göreceksiniz:

* Uçak varlığı
* varlık Seatbelts
* en fazla çakışma 

Üretim araç yılının önemli bir etkisi yoktur.

Bu modeli yeni tahminler yapmak için kullanabilirsiniz:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Web hizmeti olarak dağıtma

Modelinize göre, çakışmadan ölüm tehlikini tahmin edebilirsiniz. Azure ML 'yi kullanarak modelinizi bir tahmin hizmeti olarak dağıtın. Bu öğreticide, Web hizmetini [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (aci) ' de dağıtacaksınız.

### <a name="register-the-model"></a>Modeli kaydedin

İlk olarak, [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html)çalışma alanınıza indirdiğiniz modeli kaydedin. Kayıtlı bir model herhangi bir dosya koleksiyonu olabilir, ancak bu durumda R model nesnesi yeterlidir. Azure ML, dağıtım için kayıtlı modeli kullanır.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Çıkarım bağımlılıklarını tanımlama
Modelinize yönelik bir Web hizmeti oluşturmak için önce bir Puanlama betiği (`entry_script`) oluşturmanız gerekir (JSON biçiminde) ve modelinizden bir tahmin çıktısını alacak bir R betiği. Bu öğretici için `accident_predict.R`sunulan Puanlama dosyasını kullanın. Puanlama betiği, modelinizi yükleyen ve giriş verilerine dayalı bir tahmin yapmak için modeli kullanan bir işlev döndüren bir `init()` yöntemi içermelidir. Daha fazla bilgi için [belgelere](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) bakın.

Sonra, betiğinizin paket bağımlılıkları için bir Azure ML **ortamı** tanımlayın. Bir ortamla, betiğinizin çalışması için gereken R paketlerini (CRAN veya başka bir yerde) belirtirsiniz. Ayrıca, betiğinizin davranışını değiştirmek için başvurmasına olanak sağlayan ortam değişkenlerinin değerlerini de sağlayabilirsiniz. Varsayılan olarak, Azure ml, eğitim için tahmin aracı ile kullanılan varsayılan Docker görüntüsünü oluşturur. Öğreticinin özel gereksinimleri olmadığından, özel öznitelikleri olmayan bir ortam oluşturun.

```R
r_env <- r_environment(name = "basic_env")
```

Bunun yerine dağıtım için kendi Docker görüntünüzü kullanmak istiyorsanız `custom_docker_image` parametresini belirtin. Bir ortam tanımlamak için yapılandırılabilir seçeneklerin tam kümesi için [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) başvurusuna bakın.

Artık Puanlama betiğinizi ve ortam bağımlılıklarınızı kapsüllemek için bir **çıkarım yapılandırması** oluşturmak için ihtiyacınız olan her şeye sahipsiniz.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>ACI'ye dağıtma
Bu öğreticide, hizmetinizi ACI 'ye dağıtacaksınız. Bu kod, test ve hafif yüklemeler için uygun olan gelen isteklere yanıt vermek için tek bir kapsayıcı sağlar. Ek yapılandırılabilir seçenekler için [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) bakın. (Üretim ölçeğinde dağıtımlar için [Azure Kubernetes hizmeti](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html)' ne de dağıtım yapabilirsiniz.)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Şimdi modelinizi bir Web hizmeti olarak dağıtırsınız. Dağıtım **birkaç dakika sürebilir**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Dağıtılan hizmeti test etme

Modelinize bir hizmet olarak dağıtıldığına göre, [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html)kullanarak hizmeti R 'den test edebilirsiniz.  Tahmin edilecek yeni bir veri kümesi sağlayın, bunu JSON 'a dönüştürün ve hizmete gönderin.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Ayrıca, REST istemci çağrılarını kabul eden Web hizmetinin HTTP uç noktasını da alabilirsiniz. Bu uç noktayı, Web hizmetini test etmek veya bir uygulamayla bütünleştirmek isteyen herkesle paylaşabilirsiniz.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç kalmadığında kaynakları silin. Hala kullanmayı planladığınız herhangi bir kaynağı silmeyin. 

Web hizmetini silin:
```R
delete_webservice(aci_service)
```

Kayıtlı modeli Sil:
```R
delete_model(model)
```

Hesaplama kümesini silme:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Her şeyi sil

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Ayrıca, kaynak grubunu koruyabilir ancak tek bir çalışma alanını silebilirsiniz. Çalışma alanı özelliklerini görüntüleyin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

R 'de ilk Azure Machine Learning denemenizi tamamladığınıza göre, [r için Azure MACHINE LEARNING SDK](https://azure.github.io/azureml-sdk-for-r/index.html)hakkında daha fazla bilgi edinin.

