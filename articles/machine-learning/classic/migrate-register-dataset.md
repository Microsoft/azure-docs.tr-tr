---
title: "ML Studio (klasik): Azure Machine Learning 'a geçir-veri kümesini yeniden oluşturma"
description: Azure Machine Learning tasarımcısında Studio (klasik) veri kümelerini yeniden oluşturma
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 9604fc9d862d94ba5e566753d2186d7d28aa37ee
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308855"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Azure Machine Learning bir Studio (klasik) veri kümesini geçirme

Bu makalede, bir Studio (klasik) veri kümesini Azure Machine Learning 'a geçirmeyi öğreneceksiniz. Studio 'dan (klasik) geçiş hakkında daha fazla bilgi için bkz. [geçişe genel bakış makalesi](migrate-overview.md).

Bir veri kümesini Azure Machine Learning geçirmek için kullanabileceğiniz üç seçenek vardır. Senaryonuza en uygun seçeneği öğrenmek için her bir bölümü okuyun.


|Veriler nerede? | Geçiş seçeneği  |
|---------|---------|
|Studio 'da (klasik)     |  1. seçenek: [veri kümesini Studio 'Dan indirin (klasik) ve Azure Machine Learning yükleyin](#download-the-dataset-from-studio-classic).      |
|Bulut depolama     | 2. seçenek: [bir veri kümesini bulut kaynağından kaydetme](#import-data-from-cloud-sources). <br><br>  Seçenek 3: [bulut kaynağından veri almak Için veri alma modülünü kullanın](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning Ayrıca, veri kümelerini oluşturmak ve yönetmek için [kod ilk iş akışlarını](../how-to-create-register-datasets.md) destekler. 

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning çalışma alanı. [Azure Machine Learning çalışma alanı oluşturun](../how-to-manage-workspace.md#create-a-workspace).
- Geçirilecek bir Studio (klasik) veri kümesi.


## <a name="download-the-dataset-from-studio-classic"></a>Veri kümesini Studio 'dan indir (klasik)

Azure Machine Learning ' a bir Studio (klasik) veri kümesini geçirmenin en kolay yolu, veri kümenizi indirmek ve Azure Machine Learning kaydettirmek. Bu, veri kümenizin yeni bir kopyasını oluşturur ve bir Azure Machine Learning veri deposuna yükler.

Aşağıdaki Studio (klasik) veri kümesi türlerini doğrudan indirebilirsiniz.

* Düz metin (. txt)
* Üst bilgi (. csv) veya olmadan (.nh.csv) virgülle ayrılmış değerler (CSV)
* Üst bilgi (. TSV) veya (. NH. TSV) içermeyen sekmeyle ayrılmış değerler (TSV)
* Excel dosyası
* ZIP dosyası (. zip)

Veri kümelerini doğrudan indirmek için:
1. Studio (klasik) çalışma alanınıza () gidin [https://studio.azureml.net](https://studio.azureml.net) .
1. Sol gezinti çubuğunda **veri kümeleri** sekmesini seçin.
1. İndirmek istediğiniz veri kümelerini seçin.
1. Alt eylem çubuğunda **İndir**' i seçin.

    ![Studio 'da veri kümesinin nasıl indirileceği gösteren ekran görüntüsü (klasik)](./media/migrate-register-dataset/download-dataset.png)

Aşağıdaki veri türleri için veri kümelerini indirmek üzere **CSV 'ye Dönüştür** modülünü kullanmanız gerekir.

* SVMLight verileri (. svmlight) 
* Öznitelik Ilişki dosyası biçimi (ARFF) verileri (. arff) 
* R nesnesi veya çalışma alanı dosyası (. RDATA
* Veri kümesi türü (. Data). Veri kümesi türü, modül çıkışı için Studio (klasik) iç veri türüdür.

Veri kümenizi bir CSV 'ye dönüştürmek ve sonuçları indirmek için:

1. Studio (klasik) çalışma alanınıza () gidin [https://studio.azureml.net](https://studio.azureml.net) .
1. Yeni bir deneme oluşturun.
1. Tuval üzerine indirmek istediğiniz veri kümesini sürükleyip bırakın.
1. CSV modülüne bir **Convert** ekleyin.
1. CSV giriş bağlantı noktasına **Dönüştür** veri kümenizin çıkış bağlantı noktasına bağlanın.
1. Denemeyi çalıştırın.
1. **CSV 'ye Dönüştür** modülüne sağ tıklayın.
1. **Sonuçlar veri kümesi**  >  **indirmeyi** seçin.

    ![CSV ardışık düzenine nasıl dönüştüreceğiniz gösteren ekran görüntüsü](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Veri kümenizi Azure Machine Learning 'ye yükleyin

Veri dosyasını indirdikten sonra, veri kümesini Azure Machine Learning kaydedebilirsiniz:

1. Azure Machine Learning Studio 'ya gidin ([ml.Azure.com](https://ml.azure.com)).
1. Sol gezinti bölmesinde **veri kümeleri** sekmesini seçin.
1. Yerel **dosyalardan veri kümesi oluştur**' u seçin  >  .
    ![Veri kümeleri sekmesini ve yerel dosya oluşturma düğmesini gösteren ekran görüntüsü](./media/migrate-register-dataset/register-dataset.png)
1. Bir ad ve açıklama girin.
1. **Veri kümesi türü** için **tablo**' yı seçin.

    > [!NOTE]
    > ZIP dosyalarını da veri kümeleri olarak karşıya yükleyebilirsiniz. ZIP dosyasını karşıya yüklemek için **veri kümesi türü** için **Dosya** ' yı seçin.

1. Veri **deposu ve dosya seçimi için** veri kümesi dosyanızı karşıya yüklemek istediğiniz veri deposunu seçin.

    Varsayılan olarak, Azure Machine Learning veri kümesini varsayılan çalışma alanı BlobStore ' a depolar. Veri depoları hakkında daha fazla bilgi için bkz. [Storage Services 'A bağlanma](../how-to-access-data.md).

1. Veri kümeniz için veri ayrıştırma ayarlarını ve şemayı ayarlayın. Ardından, ayarlarınızı doğrulayın.

## <a name="import-data-from-cloud-sources"></a>Bulut kaynaklarından veri içeri aktarma

Verileriniz zaten bir bulut depolama hizmeti içinde ise ve verilerinizi yerel konumunda tutmak istiyorsanız. Aşağıdaki seçeneklerden birini kullanabilirsiniz:

|Alma yöntemi|Açıklama|
|---| --- |
|Azure Machine Learning veri kümesini kaydetme|Yerel ve çevrimiçi veri kaynaklarından veri alma (blob, ADLS 1., ADLS 2., dosya paylaşma, SQL DB). <br><br>Çalışma zamanında geç değerlendirilen veri kaynağına bir başvuru oluşturur. Bu veri kümesine tekrar tekrar eriştiğinizde ve veri sürümü oluşturma ve izleme gibi gelişmiş veri özelliklerini etkinleştirmek istiyorsanız bu seçeneği kullanın.
|Veri modülünü içeri aktarma|Çevrimiçi veri kaynaklarından veri alma (blob, ADLS 1., ADLS 2., dosya paylaşma, SQL DB). <br><br> DataSet yalnızca geçerli Tasarımcı işlem hattı çalıştırmasına aktarılır.


>[!Note]
> Studio (klasik) kullanıcılar aşağıdaki bulut kaynaklarının Azure Machine Learning ' de yerel olarak desteklenmediğini unutmayın:
> - Hive sorgusu
> - Azure Tablosu
> - Azure Cosmos DB
> - Şirket içi SQL veritabanı
>
> Kullanıcıların Azure Data Factory kullanarak verilerini desteklenen bir depolama hizmetlerine geçirmesini öneririz.  

### <a name="register-an-azure-machine-learning-dataset"></a>Azure Machine Learning veri kümesini kaydetme

Bir veri kümesini bulut hizmetinden Azure Machine Learning kaydetmek için aşağıdaki adımları kullanın: 

1. Bulut depolama hizmetini Azure Machine Learning çalışma alanınıza bağlayan [bir veri deposu oluşturun](../how-to-connect-data-ui.md#create-datastores). 

1. [Bir veri kümesini kaydedin](../how-to-connect-data-ui.md#create-datasets). Bir Studio (klasik) veri kümesini geçiriyorsanız **tablo** veri kümesi ayarını seçin.

Azure Machine Learning bir veri kümesini kaydettikten sonra tasarımcı içinde kullanabilirsiniz:
 
1. Yeni bir tasarımcı işlem hattı taslağı oluşturun.
1. Soldaki modül paletinde, **veri kümeleri** bölümünü genişletin.
1. Kayıtlı veri kümenizi tuvale sürükleyin. 

### <a name="use-the-import-data-module"></a>Veri alma modülünü kullanma

Doğrudan tasarımcı işlem hattınızla veri aktarmak için aşağıdaki adımları kullanın:

1. Bulut depolama hizmetini Azure Machine Learning çalışma alanınıza bağlayan [bir veri deposu oluşturun](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores). 

Veri deposunu oluşturduktan sonra, verileri Içe aktarmak için tasarımcıda [**verileri Içeri aktarma**](../algorithm-module-reference/import-data.md) modülünü kullanabilirsiniz:

1. Yeni bir tasarımcı işlem hattı taslağı oluşturun.
1. Soldaki modül paletinde, **veri alma** modülünü bulun ve tuvale sürükleyin.
1. **Veri al** modülünü seçin ve veri kaynağınızı yapılandırmak için sağ paneldeki ayarları kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir Studio (klasik) veri kümesini Azure Machine Learning 'a geçirmeyi öğrendiniz. Bir sonraki adım, [bir Studio (klasik) eğitim işlem hattını yeniden derlemelisiniz](migrate-rebuild-experiment.md).


Studio (klasik) geçiş serisinde diğer makalelere bakın:

1. [Geçişe genel bakış](migrate-overview.md).
1. **Veri kümelerini geçirin**.
1. [Bir Studio (klasik) eğitim işlem hattını yeniden derleyin](migrate-rebuild-experiment.md).
1. [Bir Studio (klasik) Web hizmetini yeniden derleyin](migrate-rebuild-web-service.md).
1. [İstemci uygulamalarıyla bir Azure Machine Learning Web hizmetini tümleştirin](migrate-rebuild-integrate-with-client-app.md).
1. [Execute R betiğini geçirin](migrate-execute-r-script.md).
