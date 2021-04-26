---
title: Python 'da ML modellerini yorumlama & açıklama (Önizleme)
titleSuffix: Azure Machine Learning
description: Machine Learning modelinizin Özellik önemini nasıl belirlediğini ve Azure Machine Learning SDK kullanırken tahmine dayalı hale getirmek için nasıl açıklamalar alabileceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: d79458cfc76adcfd35a6b8dee40c0c45786abc28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763298"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Python 'da (Önizleme), ML modellerini & öngörülerini açıklamak için yorumlamalar paketini kullanın

Bu nasıl yapılır kılavuzunda, aşağıdaki görevleri gerçekleştirmek için Azure Machine Learning Python SDK 'sının yorumlenebilirlik paketini kullanmayı öğreneceksiniz:


* Tüm model davranışının veya kişisel makinenizde tek tek tahminlerden yerel olarak açıklayın.

* Mühendislik uygulanan özellikler için yorumlenebilirlik tekniklerini etkinleştirin.

* Azure 'da tüm modelin ve bireysel tahminlerinin davranışını açıklayın.

* Model açıklamaları ile etkileşim kurmak için bir görselleştirme panosu kullanın.

* Inkoya sınırlama sırasında açıklamaları gözlemlemek için modelinize birlikte bir Puanlama açıklama dağıtın.


Desteklenen yorumlenebilirlik teknikleri ve makine öğrenimi modelleri hakkında daha fazla bilgi için, Azure Machine Learning ve [örnek not defterlerinde](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) [model yorumlenebilirliğini](how-to-machine-learning-interpretability.md) inceleyin.

Otomatik makine öğrenimi ile eğitilen modeller için yorumlenebilirliği etkinleştirme hakkında yönergeler için bkz. [otomatik makine öğrenimi modelleri için model açıklamaları (Önizleme)](how-to-machine-learning-interpretability-automl.md). 

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Kişisel makinenizde Özellik önemi değeri oluştur 
Aşağıdaki örnekte, Azure hizmetleriyle iletişim kurmadan kişisel makinenizde yorumlenebilirlik paketinin nasıl kullanılacağı gösterilmektedir.

1. `azureml-interpret` paketini yükleyin.
    ```bash
    pip install azureml-interpret
    ```

2. Yerel bir Jupyter Notebook örnek modeli eğitme.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. Açıklama yerel olarak çağırın.
   * Bir açıklama nesnesini başlatmak için modelinizi ve bazı eğitim verilerinizi açıklama oluşturucusuna geçirin.
   * Açıklamaları ve görselleştirmelerinizi daha bilgilendirici hale getirmek için sınıflandırma yaparsanız özellik adlarını ve çıkış sınıfı adlarını geçirmeye seçebilirsiniz.

   Aşağıdaki kod blokları `TabularExplainer` ,, `MimicExplainer` ve yerel olarak bir açıklama nesnesinin örneğini oluşturmayı gösterir `PFIExplainer` .
   * `TabularExplainer` Alttaki üç SHAP explainers birini ( `TreeExplainer` , `DeepExplainer` , veya `KernelExplainer` ) çağırır.
   * `TabularExplainer` kullanım durumu için en uygun olanı otomatik olarak seçer, ancak her üç temel explainers doğrudan çağırabilirsiniz.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    veya

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    veya

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Tüm model davranışını açıklayın (genel açıklama) 

Toplam (genel) özellik önem değerlerini almanıza yardımcı olması için aşağıdaki örneğe bakın.

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Tek bir tahmin açıklayın (yerel açıklama)
Tek bir örneğin veya örnek grubunun açıklamalarını çağırarak farklı veri noktalarının bireysel Özellik önem değerlerini alın.
> [!NOTE]
> `PFIExplainer` Yerel açıklamaları desteklemez.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Ham Özellik dönüştürmeleri

Mühendislik uygulanmış özellikler yerine ham, dönüştürülmemiş özellikler açısından açıklamaları almayı tercih edebilirsiniz. Bu seçenek için, özellik dönüştürme işlem hattınızı içindeki açıklama geçitirsiniz `train_explain.py` . Aksi takdirde açıklama, mühendislik uygulanmış özellikler açısından açıklamaları sağlar.

Desteklenen dönüşümlerin biçimi, [sköğren-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)bölümünde açıklananla aynıdır. Genel olarak, Tüm dönüştürmeler tek bir sütunda çalıştıkları sürece desteklenir, böylece bir çok-çok olacak şekilde net bir şekilde çalışır.

Bir `sklearn.compose.ColumnTransformer` veya bir birlikte bulunan transformatör tanımlama gruplarının listesi ile ham özellikler için bir açıklama alın. Aşağıdaki örnek kullanılmıştır `sklearn.compose.ColumnTransformer` .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Örneği, birlikte bulunan transformatör tanımlama grupları listesiyle çalıştırmak istiyorsanız aşağıdaki kodu kullanın:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>Uzak çalıştırmalar aracılığıyla Özellik önem değerleri oluşturma

Aşağıdaki örnek, `ExplanationClient` uzak çalıştırmalar için model yorumlenebilirliğini etkinleştirmek üzere sınıfını nasıl kullanabileceğinizi gösterir. Yerel işlemle kavramsal olarak benzerdir, ancak şunları yapabilirsiniz:

* `ExplanationClient`Yorumlenebilirlik bağlamını karşıya yüklemek için uzak çalıştırmada öğesini kullanın.
* Bağlamı daha sonra yerel bir ortamda indirin.

1. `azureml-interpret` paketini yükleyin.
    ```bash
    pip install azureml-interpret
    ```
1. Yerel bir Jupyter Notebook eğitim betiği oluşturun. Örneğin, `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. İşlem hedefi olarak bir Azure Machine Learning Işlem kurun ve eğitim çalıştırmanıza iletin. Yönergeler için [Azure Machine Learning işlem kümeleri oluşturma ve yönetme](how-to-create-attach-compute-cluster.md) konusuna bakın. [Örnek Not defterlerinin](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) yararlı olduğunu da görebilirsiniz.

1. Açıklamayı yerel Jupyter Notebook indirin.

    ```python
    from azureml.interpret import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>Görsel öğeler

Açıklamaları yerel Jupyter Notebook indirdikten sonra, modellerinizi anlamak ve yorumlamak için açıklamalar panosundaki görselleştirmeleri kullanabilirsiniz. Jupyter Notebook açıklamalar panosu pencere öğesini yüklemek için aşağıdaki kodu kullanın:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

Görselleştirmeler hem mühendislik uygulanan hem de ham özelliklerde açıklamaları destekler. Ham açıklamalar orijinal veri kümesindeki özellikleri temel alır ve uygulanan açıklamalar, özellik Mühendisliği uygulanmış olan veri kümesindeki özelliklere dayanır.

Özgün veri kümesiyle ilgili bir modeli yorumlamaya çalışırken, her bir özellik önem derecesi özgün veri kümesindeki bir sütuna karşılık geldiği için ham açıklamaları kullanmanız önerilir. Bir kategorik özellikten tek tek kategorilerin etkisini incelerken, uygulanan açıklamaları yararlı olabilecek bir senaryo. Kategorik bir özelliğe tek yönlü bir kodlama uygulanmışsa, sonuçta elde edilen mühendislik uygulanan açıklamalar kategori başına farklı bir önem derecesi, tek başına mühendislik uygulanan bir özellik için de içerecektir. Bu, veri kümesinin hangi bölümünün modele en çok bilgilendirici olduğunu daraltdığınızda yararlı olabilir.

> [!NOTE]
> Uygulanan ve ham açıklamalar sırayla hesaplanır. İlk olarak, uygulanan bir açıklama model ve korleştirme işlem hattı temel alınarak oluşturulur. Daha sonra ham açıklama, aynı RAW özelliğinden gelen mühendislik uygulanmış özelliklerin önemini toplayarak, bu uygulanan açıklamaya göre oluşturulur.

### <a name="create-edit-and-view-dataset-cohorts"></a>Veri kümesi katmanları oluşturma, düzenleme ve görüntüleme

En üstteki şeritte, modelinizdeki ve verilerinizde genel istatistikler gösterilmektedir. Bu tanımlı alt gruplar genelinde modelinizin performansını ve açıklamalarını araştırmak veya karşılaştırmak için verilerinizi veri kümesi işbirliği veya alt gruplar halinde dilimleyebilir ve bu verilere aktarabilirsiniz. Veri kümesi istatistiklerinizi ve açıklamalarını bu alt gruplar genelinde karşılaştırarak, bir grupta neden olası hataların meydana gelmediğini bir fikir edinebilirsiniz.

[![Veri kümesi bozar oluşturma, düzenlememe ve görüntüleme](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Tüm model davranışını anlama (genel açıklama) 

Açıklama panosunun ilk üç sekmesi, öngörülerinin ve açıklamalarıyla birlikte eğitilen modelin genel analizini sağlar.

#### <a name="model-performance"></a>Model performansı
Tahmin değerlerinizin dağıtımını ve model performans ölçümlerinizin değerlerini inceleyerek modelinizin performansını değerlendirin. Ayrıca, veri kümenizin farklı alt grupları veya alt gruplarının bir karşılaştırılma analizine bakarak modelinize daha fazla araştırma yapabilirsiniz. Farklı boyutlarda kesmek için y değeri ve x değeri boyunca filtreler seçin. Doğruluk, duyarlık, geri çekme, yanlış pozitif ücret (FPR) ve yanlış negatif oran (FNR) gibi ölçümleri görüntüleyin.

[![Açıklama görselleştirmesinde model performansı sekmesi](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Veri kümesi Gezgini
Verilerinizi farklı boyutlarda dilimlemek için X, Y ve renk eksenleri üzerinde farklı filtreler seçerek veri kümesi istatistiklerinizi keşfedebilirsiniz. Tahmin edilen sonuç, veri kümesi özellikleri ve hata grupları gibi filtrelerle veri kümesi istatistiklerini çözümlemek için yukarıdaki veri kümesi katmanları oluşturun. Grafik türlerini değiştirmek için grafiğin sağ üst köşesindeki dişli simgesini kullanın.

[![Açıklama görselleştirmesinde veri kümesi Gezgini sekmesi](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Toplu özellik önemi
Genel model tahminlerinizi etkileyen (genel açıklama olarak da bilinir) en önemli özellikleri bulun. Azalan Özellik önem değerlerini göstermek için kaydırıcıyı kullanın. Özellik önem değerlerini yan yana görmek için en fazla üç Kohortlar seçin. Seçili özelliğin değerlerinin aşağıdaki bağımlılık çiziminde nasıl çalıştığını görmek için grafikteki Özellik çubuklarının herhangi birine tıklayın.

[![Açıklama görselleştirmesinde Birleşik Özellik önemi sekmesi](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Ayrı tahminleri anlama (yerel açıklama) 

Açıklama sekmesinin dördüncü sekmesi, bireysel bir DataPoint ve bireysel özellikler için detaya gitmenizi sağlar. Ana dağılım çiziminde her bir veri noktasına tıklayarak veya sağdaki panel sihirbazında belirli bir DataPoint seçerek herhangi bir veri noktası için bireysel Özellik önem çizimi ' ni yükleyebilirsiniz.

|ZF|Description|
|----|-----------|
|Bireysel Özellik önemi|Tek bir tahmine yönelik üst k önemli özellikleri gösterir. Belirli bir veri noktasındaki temeldeki modelin yerel davranışını göstermeye yardımcı olur.|
|What-If Analizi|Seçili gerçek veri noktasının özellik değerlerinde değişikliklere izin verir ve yeni özellik değerleriyle kuramsal bir DataPoint oluşturarak tahmin değerindeki sonuç değişikliklerini gözlemleyin.|
|Bireysel koşullu beklenti (buz)|Özellik değeri değişikliklerinin en küçük değerden en büyük değere değiştirilmesine izin verir. Bir özellik değiştiğinde veri noktasının öngörme şeklini göstermeye yardımcı olur.|

[![Açıklama panosundaki bireysel Özellik önem ve durum sekmesi](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Bunlar, birçok yaklaştırma göre açıklamaları sağlar ve tahmine dayalı olarak "Neden" değildir. Causal çıkarımı için kesin matematik sağlamlığı olmadan, kullanıcıların What-If aracının Özellik perturuna göre gerçek yaşam kararları almasını tavsiye ettik. Bu araç öncelikle modelinizi ve hata ayıklamayı anlamak içindir.

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio 'da görselleştirme

[Uzaktan yorumlanabilecek](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) adımları tamamlarınız (Azure Machine Learning çalıştırma geçmişine oluşturulan açıklamaları karşıya yüklerken), görselleştirmeleri [Azure Machine Learning Studio](https://ml.azure.com)'daki açıklamalar panosunda görüntüleyebilirsiniz. Bu Pano, Jupyter Not defteriniz içinde oluşturulan pano pencere öğesinin daha basit bir sürümüdür. Azure Machine Learning Studio 'da gerçek zamanlı hesaplamalar gerçekleştirebilen etkin bir işlem olmadığından What-If DataPoint oluşturma ve buz çizimleri devre dışı bırakılmıştır.

Veri kümesi, genel ve yerel açıklamalar varsa, veriler sekmelerin tümünü doldurur. Yalnızca genel bir açıklama varsa, bireysel Özellik önem düzeyi sekmesi devre dışı bırakılır.

Azure Machine Learning Studio 'daki açıklamalar panosuna erişmek için bu yollardan birini izleyin:

* **Denemeleri** bölmesi (Önizleme)
  1. Azure Machine Learning üzerinde çalıştırdığınız denemeleri listesini görmek için sol bölmedeki **denemeleri** öğesini seçin.
  1. Bu deneyteki tüm çalıştırmaları görüntülemek için belirli bir deneme seçin.
  1. Bir çalıştırma seçin ve ardından **açıklamalar** sekmesini açıklama görselleştirme panosu ' na tıklayın.

   [![Denemeleri içinde AzureML Studio 'daki toplu özellik önem derecesine sahip görselleştirme panosu](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **Modeller** bölmesi
  1. [Azure Machine Learning ile modelleri dağıtma](./how-to-deploy-and-where.md)bölümündeki adımları izleyerek orijinal modelinizi kaydettiniz, görüntülemek için sol bölmedeki **modeller** ' ı seçebilirsiniz.
  1. Açıklamalar panosunu görüntülemek için bir model ve ardından **açıklamalar** sekmesini seçin.

## <a name="interpretability-at-inference-time"></a>Çıkarımı zamanında yorumlenebilirlik

Açıklama 'yi özgün modelle birlikte dağıtabilir ve tüm yeni DataPoint için bireysel Özellik önem değerlerini (yerel açıklama) sağlamak üzere çıkarımı zaman içinde kullanabilirsiniz. Ayrıca, şu anda yalnızca Azure Machine Learning SDK sürümünde desteklenen, çıkarım performansını artırmak için daha fazla ağırlık Puanlama explainers sunuyoruz. Daha hafif bir Puanlama açıklama dağıtma işlemi, model dağıtmaya benzer ve aşağıdaki adımları içerir:

1. Bir açıklama nesnesi oluşturun. Örneğin, şunu kullanabilirsiniz `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Açıklama nesnesiyle bir Puanlama açıklama oluşturun.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Puanlama açıklama modelini kullanan bir görüntüyü yapılandırın ve kaydedin.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. İsteğe bağlı bir adım olarak, Puanlama açıklama buluttan alabilir ve açıklamaları test edebilirsiniz.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Aşağıdaki adımları izleyerek görüntüyü bir işlem hedefine dağıtın:

   1. Gerekirse, [modelleri Azure Machine Learning Ile dağıtma](./how-to-deploy-and-where.md)' daki adımları izleyerek orijinal tahmin modelinizi kaydedin.

   1. Puanlama dosyası oluşturma.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Dağıtım yapılandırmasını tanımlayın.

         Bu yapılandırma, modelinizin gereksinimlerine bağlıdır. Aşağıdaki örnekte, bir CPU çekirdeği ve bir GB bellek kullanan bir yapılandırma tanımlanmaktadır.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Ortam bağımlılıklarıyla bir dosya oluşturun.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. G + + yüklenmiş özel bir dockerfile oluşturun.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Oluşturulan görüntüyü dağıtın.
   
         Bu işlem yaklaşık beş dakika sürer.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Dağıtımı test edin.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Temizleyin.

   Dağıtılmış bir Web hizmetini silmek için kullanın `service.delete()` .

## <a name="troubleshooting"></a>Sorun giderme

* **Seyrek veriler desteklenmez**: model açıklama panosu çok sayıda özellik ile önemli ölçüde yavaşlar/yavaşlar, bu nedenle şu anda seyrek veri biçimini desteklemiyoruz. Ayrıca, genel bellek sorunları büyük veri kümeleri ve çok sayıda özellik ile ortaya çıkar. 

* **Model açıklamaları ile desteklenmeyen tahmin modelleri**: Bcnforecaster, oto ARIMA, Prophet, üs Alyumuşatma, ortalama, Naive, mevsimlik ve mevsimlik, en iyi model olarak aşağıdaki algoritmaları önermek için, denemeleri, en iyi model açıklaması. Oto ve tahmin için açıklamaları destekleyen regresyon modelleri vardır. Ancak, açıklama panosunda "tekil Özellik önemi" sekmesi, veri işlem hatlarında karmaşıklık nedeniyle tahmin için desteklenmez.

* **Veri dizini Için yerel açıklama**: Açıklama panosu, Pano verileri rasgele şekilde örnekleyecek şekilde, bu veri kümesi 5000 veri noktasından büyükse, yerel önem değerlerinin orijinal doğrulama veri kümesinden bir satır tanımlayıcısıyla ilişkilendirilmesi desteklenmez. Ancak, panoda her bir DataPoint için ham veri kümesi özellik değerleri, her bir özellik önem derecesi sekmesinde görüntülenir. Kullanıcılar, ham veri kümesi özellik değerleriyle eşleşen yerel kaynakları özgün veri kümesine geri eşleyebilir. Doğrulama veri kümesi boyutu 5000 örnek ise, `index` AzureML Studio 'daki Özellik doğrulama veri kümesindeki dizine karşılık gelir.

* **Studio 'Da ne durum/buz çizimleri desteklenmez**: What-If ve tek koşullu beklentiler (buz) çizimleri, karşıya yüklenen açıklamanın tahmine dayalı özelliklerin ve olasılıkların yeniden hesaplanması için etkin bir işlem gerektirdiğinden, açıklamalar sekmesinin altında Azure Machine Learning Studio 'da desteklenmez. SDK kullanılarak pencere öğesi olarak çalıştırıldığında jupi not defterlerinde Şu anda desteklenmektedir.


## <a name="next-steps"></a>Sonraki adımlar

[Model yorumlenebilirliği hakkında daha fazla bilgi edinin](how-to-machine-learning-interpretability.md)

[Gözden geçirin Azure Machine Learning Yorumlenebilirliği örnek Not defterleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
