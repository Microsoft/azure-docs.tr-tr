---
title: Tasarımcı 'da eğitilen modelleri dağıtmak için Studio 'yu kullanma
titleSuffix: Azure Machine Learning
description: Tek bir kod satırı yazmadan makine öğrenimi modellerini dağıtmak için Azure Machine Learning Studio kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio, designer
ms.openlocfilehash: 35acfc51ae76fdacef11f03b1fbd91ad58650ae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97722632"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Tasarımcı 'da eğitilen modelleri dağıtmak için Studio 'yu kullanma

Bu makalede, tasarımcı modelini Azure Machine Learning Studio 'da gerçek zamanlı uç nokta olarak dağıtmayı öğreneceksiniz.

Kaydolduktan veya indirildikten sonra, tasarımcı eğitilen modellerini diğer modeller gibi kullanabilirsiniz. Dışarıya yönelik modeller, Nesnelerin İnterneti (IoT) ve yerel dağıtımlar gibi kullanım durumlarında dağıtılabilir.

Studio 'da dağıtım aşağıdaki adımlardan oluşur:

1. Eğitilen modeli kaydedin.
1. Model için giriş betiği ve Conda Dependencies dosyalarını indirin.
1. Seçim Giriş betiğini yapılandırın.
1. Modeli bir işlem hedefine dağıtın.

Model kaydı ve dosya indirme adımlarını atlamak için modelleri doğrudan tasarımcıda da dağıtabilirsiniz. Bu, hızlı dağıtım için yararlı olabilir. Daha fazla bilgi için bkz. [Tasarımcı ile model dağıtma](tutorial-designer-automobile-price-deploy.md).

Tasarımcıda eğitilen modeller ayrıca SDK veya komut satırı arabirimi (CLı) aracılığıyla da dağıtılabilir. Daha fazla bilgi için bkz. [mevcut modelinizi Azure Machine Learning dağıtma](how-to-deploy-existing-model.md).

## <a name="prerequisites"></a>Önkoşullar

* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md)

* Aşağıdaki modüllerden birini içeren tamamlanmış bir eğitim işlem hattı:
    - [Model modülünü eğitme](./algorithm-module-reference/train-model.md)
    - [Anomali algılama modeli modülünü eğitme](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Kümeleme modeli modülünü eğitme](./algorithm-module-reference/train-clustering-model.md)
    - [Pytorch model modülünü eğitme](./algorithm-module-reference/train-pytorch-model.md)
    - [SVD öneren modülünü eğitme](./algorithm-module-reference/train-svd-recommender.md)
    - [Vowpal Wabbit model modülünü eğitme](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [Geniş kapsamlı & modeli modülünü eğitme](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Modeli Kaydet

Eğitim işlem hattı tamamlandıktan sonra, diğer projelerdeki modele erişmek için eğitilen modeli Azure Machine Learning çalışma alanınıza kaydedin.

1. [Model eğitme modülünü](./algorithm-module-reference/train-model.md)seçin.
1. Sağ bölmedeki **çıktılar + Günlükler** sekmesini seçin.
1. Dişli simgesine ait **modeli Kaydet** simgesi ![ ekran görüntüsünü seçin ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![Model modülü eğitimi sağ bölmesinin ekran görüntüsü](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Modeliniz için bir ad girin ve ardından **Kaydet**' i seçin.

Modelinize kaydolduktan sonra, bunu Studio 'daki **modeller** varlık sayfasında bulabilirsiniz.
    
![Modeller varlık sayfasında kayıtlı modelin ekran görüntüsü](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Giriş betiği dosyası ve Conda Dependencies dosyalarını indirin

Azure Machine Learning Studio 'da bir modeli dağıtmak için aşağıdaki dosyalar gereklidir:

- **Giriş betiği dosyası** -eğitilen modeli yükler, isteklerin giriş verilerini işler, gerçek zamanlı Inse ve sonucu döndürür. `score.py` **Eğitim modeli** modülü tamamlandığında Tasarımcı otomatik olarak bir giriş betik dosyası oluşturur.

- **Conda Dependencies dosyası** -Web sitenizin hangi PIP ve Conda paketlerinin bağımlı olduğunu belirtir. `conda_env.yaml` **Model eğitme** modülü tamamlandığında Tasarımcı otomatik olarak bir dosya oluşturur.

Bu iki dosyayı **eğitme modeli** modülünün sağ bölmesinde indirebilirsiniz:

1. **Model eğitme** modülünü seçin.
1. **Çıktılar + Günlükler** sekmesinde klasörü seçin `trained_model_outputs` .
1. `conda_env.yaml`Dosyayı ve dosyayı indirin `score.py` .

    ![Sağ bölmede dağıtım için dosyaları indirme ekran görüntüsü](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

Alternatif olarak, modelinize kaydolduktan sonra **modeller** varlık sayfasından dosyaları indirebilirsiniz:

1. **Modeller** varlık sayfasına gidin.
1. Dağıtmak istediğiniz modeli seçin.
1. **Yapıtlar** sekmesini seçin.
1. `trained_model_outputs` klasörünü seçin.
1. `conda_env.yaml`Dosyayı ve dosyayı indirin `score.py` .  

    ![Model ayrıntısı sayfasında dağıtım için dosyaları indirme ekran görüntüsü](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> `score.py`Dosya, **puan modeli** modülleriyle neredeyse aynı işlevleri sağlar. Ancak, [SVD öneren Puanlama](./algorithm-module-reference/score-svd-recommender.md), [geniş kapsamlı ve derin öneren](./algorithm-module-reference/score-wide-and-deep-recommender.md)ve [Vowpal Wabbit modelinin](./algorithm-module-reference/score-vowpal-wabbit-model.md) puan gibi bazı modüller farklı Puanlama modları için parametrelere sahiptir. Giriş betiğindeki bu parametreleri de değiştirebilirsiniz.
>
>Dosyadaki parametreleri ayarlama hakkında daha fazla bilgi için, `score.py` [giriş betiğini yapılandırma](#configure-the-entry-script)bölümüne bakın.

## <a name="deploy-the-model"></a>Modeli dağıtma

Gerekli dosyaları indirdikten sonra modeli dağıtmaya hazırsınız demektir.

1. **Modeller** varlık sayfasında, kayıtlı modeli seçin.
1. **Dağıt** düğmesini seçin.
1. Yapılandırma menüsünde, aşağıdaki bilgileri girin:

    - Uç nokta için bir ad girin.
    - Modeli [Azure Kubernetes hizmetine](how-to-deploy-azure-kubernetes-service.md) veya [Azure Container Instance](how-to-deploy-azure-container-instance.md)'a dağıtmayı seçin.
    - `score.py` **Giriş betik dosyası** için öğesini yükleyin.
    - `conda_env.yml` **Conda Dependencies dosyası** için öğesini karşıya yükleyin. 

    >[!TIP]
    > **Gelişmiş** ayarında, DAĞıTıM için CPU/bellek kapasitesi ve diğer parametreleri ayarlayabilirsiniz. Bu ayarlar, önemli miktarda memery (yaklaşık 4 GB) kullanan PyTorch modelleri gibi bazı modeller için önemlidir.

1. Modelinizi gerçek zamanlı bir uç nokta olarak dağıtmak için **Dağıt** ' ı seçin.

    ![Model varlığı sayfasında modeli dağıtma ekran görüntüsü](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Gerçek zamanlı uç noktayı tüketme

Dağıtım başarılı olduktan sonra, **uç** nokta varlığı sayfasında gerçek zamanlı uç noktayı bulabilirsiniz. Bu işlem tamamlandıktan sonra, istemcilerin gerçek zamanlı uç noktaya istek göndermek için kullanabileceği bir REST uç noktası bulacaksınız. 

> [!NOTE]
> Tasarımcı Ayrıca test için bir örnek veri JSON dosyası oluşturur, `_samples.json` **trained_model_outputs** klasörüne indirebilirsiniz.

Gerçek zamanlı bir uç nokta kullanmak için aşağıdaki kod örneğini kullanın.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Bilgisayar görme ilgili gerçek zamanlı uç noktaları kullanın

Bilgisayar görme ilgili gerçek zamanlı uç noktaları kullanırken, Web hizmeti yalnızca giriş olarak dizeyi kabul ettiğinden görüntüleri bayta dönüştürmeniz gerekir. Örnek kod aşağıda verilmiştir:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Giriş betiğini yapılandırma

Tasarımcıda [SVD öneren puanı](./algorithm-module-reference/score-svd-recommender.md), [geniş kapsamlı ve derin öneren puan](./algorithm-module-reference/score-wide-and-deep-recommender.md)ve [Vowpal Wabbit modelinin](./algorithm-module-reference/score-vowpal-wabbit-model.md) Puanlama gibi bazı modüller, farklı Puanlama modları için parametrelere sahiptir. 

Bu bölümde, giriş betiği dosyasında bu parametrelerin nasıl güncelleştireceğinizi öğreneceksiniz.

Aşağıdaki örnek, eğitilen **geniş bir & derin öneren** modeli için varsayılan davranışı günceller. Varsayılan olarak, `score.py` Dosya Web hizmetine kullanıcılar ve öğeler arasında derecelendirmeleri tahmin etmek üzere bildirir. 

Öğe önerileri yapmak için giriş komut dosyasını değiştirebilir ve parametresini değiştirerek önerilen öğeleri döndürebilirsiniz `recommender_prediction_kind` .


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

**Geniş & derin öneren** ve **Vowpal Wabbit** modelleri için aşağıdaki yöntemleri kullanarak Puanlama modu parametresini yapılandırabilirsiniz:

- Parametre adları, [Vowpal Wabbit modeli puanı](./algorithm-module-reference/score-vowpal-wabbit-model.md) ve [Score Wide ve derin öneren](./algorithm-module-reference/score-wide-and-deep-recommender.md); için parametre adlarının küçük harfli ve alt çizgi birleşimleridir.
- Mod türü parametre değerleri, ilgili seçenek adlarının dizeleridir. **Öneren tahmin türünü** yukarıdaki kodlardan örnek olarak al, değer `'Rating Prediction'` veya olabilir `'Item Recommendation'` . Diğer değerlere izin verilmez.

**SVD öneren** eğitilen modeli için parametre adları ve değerleri daha az olabilir ve parametrelerin nasıl ayarlanacağına karar vermek için aşağıdaki tablolara bakabilirsiniz.

| [Score SVD öneren](./algorithm-module-reference/score-svd-recommender.md) içinde parametre adı                           | Giriş betik dosyasındaki parametre adı |
| ------------------------------------------------------------ | --------------------------------------- |
| Öneren tahmin türü                                  | prediction_kind                         |
| Önerilen öğe seçimi                                   | recommended_item_selection              |
| Tek bir kullanıcı için öneri havuzunun minimum boyutu    | min_recommendation_pool_size            |
| Bir kullanıcı için önerilen en fazla öğe sayısı               | max_recommended_item_count              |
| Öğelerin öngörülen derecelendirmelerinin, etiketlerle birlikte döndürülüp döndürülmeyeceğini belirtir | return_ratings                          |

Aşağıdaki kod, bir SVD öneren için parametrelerin nasıl ayarlanacağını gösterir. Bu, tüm altı parametreyi kullanarak, öngörülen derecelendirmelerin ekli olduğu dereceli öğeleri önerir.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Sonraki adımlar

* [Tasarımcıda bir modeli eğitme](tutorial-designer-automobile-price-train-score.md)
* [Azure Machine Learning SDK ile modelleri dağıtma](how-to-deploy-and-where.md)
* [Başarısız bir dağıtımın sorunlarını giderme](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service’e dağıtma](how-to-deploy-azure-kubernetes-service.md)
* [Web hizmetlerini kullanmak için istemci uygulamaları oluşturma](how-to-consume-web-service.md)
* [Web hizmetini güncelleştirme](how-to-deploy-update-web-service.md)
