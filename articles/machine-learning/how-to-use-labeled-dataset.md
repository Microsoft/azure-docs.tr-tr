---
title: Etiketler ile veri kümeleri oluşturma ve araştırma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning etiketleme projelerinizden veri etiketlerini dışarı aktarmayı öğrenin ve makine öğrenimi görevleri için bunları kullanın.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: 97922cc8693cddcb13350117b6c17c4220f75337
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227985"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Etiketlerle Azure Machine Learning veri kümesi oluşturma ve araştırma

Bu makalede, veri etiketlerini bir Azure Machine Learning veri etiketleme projesinden dışarı aktarmayı ve bunları, veri araştırması için bir Pandas dataframe veya görüntü dönüştürmesi için Torchvision veri kümesi gibi popüler biçimlere yüklemeyi öğreneceksiniz. 

## <a name="what-are-datasets-with-labels"></a>Etiketleri olan veri kümeleri nelerdir 

Etiketli veri kümeleri olarak etiketlere sahip Azure Machine Learning veri kümelerine başvurduk. Etiketli veri kümelerinin bu özel veri kümesi türleri yalnızca Azure Machine Learning veri etiketleme projelerinin çıktısı olarak oluşturulur. [Bu adımlarla](how-to-create-labeling-projects.md)bir veri etiketleme projesi oluşturun. Machine Learning, çok etiketli veya çok sınıflı ve nesne tanımlamasının sınırlı kutular ile birlikte görüntü sınıflandırması için veri etiketleme projelerini destekler.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://aka.ms/AMLFree) oluşturun.
* [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)veya [Azure Machine Learning Studio](https://ml.azure.com/)'ya erişim.
    * [Azure-contrib-DataSet](/python/api/azureml-contrib-dataset/) paketini yükler
* Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).
* Azure Machine Learning veri etiketleme projesine erişim. Etiketleme projeniz yoksa, [Bu adımlarla](how-to-create-labeling-projects.md)bir tane oluşturun.

## <a name="export-data-labels"></a>Veri etiketlerini dışarı aktar 

Bir veri etiketleme projesini tamamladığınızda, etiket verilerini etiketleme projesinden dışarı aktarabilirsiniz. Bunu yaptığınızda, hem verilere hem de etiketlerine olan başvuruyu yakalayıp [Coco formatında](http://cocodataset.org/#format-data) veya bir Azure Machine Learning veri kümesi olarak dışarı aktarabilirsiniz. Etiketleme projenizin **proje ayrıntıları** sayfasında **dışarı aktar** düğmesini kullanın.

### <a name="coco"></a>COCO 

 COCO dosyası, Azure Machine Learning çalışma alanının varsayılan Blob deposunda *dışarı aktarma/Coco* içindeki bir klasörde oluşturulur. 
 
>[!NOTE]
>Nesne algılama projelerinde, COCO dosyasındaki "Bbox": [x, y, Width, Height] "değerleri normalleştirilmelidir. Bunlar 1 olarak ölçeklendirilir. Örnek: (10, 10) konumdaki bir sınırlayıcı kutu, yaklaşık 30 piksel genişlik, 60 piksel yükseklik, 640x480 piksel boyutunda, (0,015625) olarak açıklanacaktır. 0,02083, 0,046875, 0,125). Coordintes normalleştirildiğinden, tüm görüntüler için "width" ve "height" şeklinde ' 0,0 ' olarak gösterilecektir. Gerçek genişlik ve yükseklik, OpenCV veya Pillow (PIL) gibi Python kitaplığı kullanılarak elde edilebilir.

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning veri kümesi

Azure Machine Learning Studio 'nun **veri kümeleri** bölümünde, aktarılmış Azure Machine Learning veri kümesine erişebilirsiniz. Veri kümesi **ayrıntıları** sayfası, Python 'daki etiketlerinize erişmek için örnek kod de sağlar.

![Aktarılmış veri kümesi](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Etiketli veri kümelerini keşfet

Veri araştırması için popüler açık kaynaklı kitaplıkların yanı sıra görüntü dönüştürme ve eğitimle ilgili kitaplıklar için, etiketli veri kümelerinizi bir Pandas dataframe veya Torchvision veri kümesine yükleyin.

### <a name="pandas-dataframe"></a>Pandas dataframe

Etiketli veri kümelerini, sınıfındaki yöntemiyle bir Pandas dataframe 'e yükleyebilirsiniz [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) `azureml-contrib-dataset` . Aşağıdaki kabuk komutuyla sınıfı yüklersiniz: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Hizmetin geliştirilmesi için çalışdığımız için, azureml. contrib ad alanı sıklıkla değişir. Bu nedenle, bu ad alanındaki her şey Microsoft tarafından tam olarak desteklenmez ve önizleme olarak değerlendirilmelidir.

Azure Machine Learning, bir Pandas dataframe 'e dönüştürürken dosya akışları için aşağıdaki dosya işleme seçeneklerini sunar.
* İndir: veri dosyalarınızı yerel bir yola Indirin.
* Bağla: veri dosyalarınızı bağlama noktasına bağlayın. Bağlama yalnızca Azure Machine Learning Not defteri VM ve Azure Machine Learning Işlem gibi Linux tabanlı işlem için geçerlidir.

Aşağıdaki kodda `animal_labels` veri kümesi, daha önce çalışma alanına kaydedilen bir etiketleme projesinin çıktıdır.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision veri kümeleri

Etiketli veri kümelerini, sınıfından de [to_torchvision ()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset#to-torchvision--) yöntemiyle birlikte Torchvision veri kümesine yükleyebilirsiniz `azureml-contrib-dataset` . Bu yöntemi kullanmak için [Pytorch](https://pytorch.org/) yüklü olmalıdır. 

Aşağıdaki kodda `animal_labels` veri kümesi, daha önce çalışma alanına kaydedilen bir etiketleme projesinin çıktıdır.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Sonraki adımlar

* Tüm eğitim örneği için [Etiketler Not defteri ile veri kümesine](/azure/machine-learning/how-to-use-labeled-dataset) bakın.
