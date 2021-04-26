---
title: Python 'da ML modellerini değerlendir ' eşitliği (Önizleme)
titleSuffix: Azure Machine Learning
description: Fairlearn ve Python SDK Azure Machine Learning kullanarak makine öğrenimi modellerinizin eşitliği nasıl değerlendirip azaltılacağını öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 70ad5d6d88dabb51e022a1fc5c011341b06f02fd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640681"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>ML modellerinin eşitliği 'i değerlendirmek için Fairlearn açık kaynak paketiyle birlikte Azure Machine Learning kullanın (Önizleme)

Bu nasıl yapılır kılavuzunda, aşağıdaki görevleri gerçekleştirmek için Azure Machine Learning ile [Fairlearn](https://fairlearn.github.io/) açık kaynaklı Python paketini kullanmayı öğreneceksiniz:

* Model tahminlerinizin eşitliği değerlendirin. Machine Learning 'de eşitliği hakkında daha fazla bilgi için bkz. [Machine Learning 'de eşitliği](concept-fairness-ml.md).
* Eşitliği Assessment Insights 'ı Azure Machine Learning Studio 'ya yükleyin, listeleyin ve indirin.
* Modelleriniz ' eşitliği öngörüleri ile etkileşim kurmak için Azure Machine Learning Studio 'da bir eşitliği değerlendirmesi panosu ' na bakın.

>[!NOTE]
> Eşitliği değerlendirmesi tamamen teknik bir alıştırma değildir. **Bu paket, bir makine öğrenimi modelinin eşitliği değerlendirmenize yardımcı olabilir, ancak yalnızca modelin nasıl gerçekleştirdiği hakkında kararlar verebilir ve yapabilirsiniz.**  Bu paket, eşitliği değerlendirmek için nicel ölçümlerini belirlemesine yardımcı olsa da, makine öğrenimi modellerinin geliştiricilerin, kendi modellerinin eşitliği değerlendirmek için bir nitel çözümlemesi gerçekleştirmesi gerekir.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning eşitliği SDK 

Azure Machine Learning eşitliği SDK, `azureml-contrib-fairness` Azure Machine Learning içindeki açık kaynaklı Python paketini [Fairlearn](http://fairlearn.github.io), tümleştirir. Fairlearn 'in Azure Machine Learning dahilinde tümleştirmesi hakkında daha fazla bilgi edinmek için bu [örnek not defterlerine](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)göz atın. Fairlearn hakkında daha fazla bilgi için bkz. [örnek kılavuz](https://fairlearn.org/v0.6.0/auto_examples/) ve [örnek Not defterleri](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Ve paketlerini yüklemek için aşağıdaki komutları kullanın `azureml-contrib-fairness` `fairlearn` :
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
Sonraki Fairlearn sürümleri de aşağıdaki örnek kodda çalışmalıdır.



## <a name="upload-fairness-insights-for-a-single-model"></a>Tek bir model için eşitliği öngörülerini karşıya yükleyin

Aşağıdaki örnek, eşitliği paketinin nasıl kullanılacağını göstermektedir. Model eşitliği öngörülerini Azure Machine Learning yükleyecek ve Azure Machine Learning Studio 'daki eşitliği değerlendirme panosunu görmeniz gerekir.

1. Jupyter Notebook bir örnek modeli eğitme. 

    Veri kümesi için, OpenML 'den aldığımız iyi bilinen yetişkinlere yönelik veri kümesini kullanırız. Bu, bir tek bir repaıp 'nin önceki kredisinin bulunduğu etiketle ilgili bir kredi kararı sorunuyla karşılaştık. Daha önce görmeyen bireyler bir kredisi yeniden boyar. Bu tür bir model, kredi kararları vermek için kullanılabilir.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Azure Machine Learning açın ve modelinizi kaydedin.
   
    Eşitliği panosu kayıtlı veya kayıtsız modeller ile tümleştirilebilir. Modelinizi aşağıdaki adımlarla Azure Machine Learning kaydedin:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Ön işlem eşitliği ölçümleri.

    Fairlearn 'in paketini kullanarak bir pano sözlüğü oluşturun `metrics` . `_create_group_metric_set`Yöntemi, pano oluşturucusuna benzer bağımsız değişkenlere sahiptir, ancak gizli özellikler sözlük olarak geçirilir (adların kullanılabilir olmasını sağlamak için). Bu yöntemi çağırırken tahmin türünü (Bu durumda ikili sınıflandırma) de belirtmemiz gerekir.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Önceden hesaplanan eşitliği ölçümlerini karşıya yükleyin.
    
    Şimdi `azureml.contrib.fairness` karşıya yüklemeyi gerçekleştirmek için paketi içeri aktarın:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Bir deneme ve bir çalıştırma oluşturun ve panoyu buna yükleyin:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Azure Machine Learning Studio 'dan eşitliği panosunu denetleyin

    Önceki adımları (generated eşitliği Insights 'ı Azure Machine Learning karşıya yükleyerek) tamamladıktan sonra, eşitliği panosunu [Azure Machine Learning Studio](https://ml.azure.com)'da görüntüleyebilirsiniz. Bu Pano, Fairlearn ' de sunulan görselleştirme panosu, hassas özelliğin alt gruplarının (örn. erkek vs. Female) arasındaki ayırt etmeyi incelemenize olanak sağlar.
    Azure Machine Learning Studio 'daki görselleştirme panosuna erişmek için bu yollardan birini izleyin:

    * **Denemeleri bölmesi (Önizleme)**
    1. Azure Machine Learning üzerinde çalıştırdığınız denemeleri listesini görmek için sol bölmedeki **denemeleri** öğesini seçin.
    1. Bu deneyteki tüm çalıştırmaları görüntülemek için belirli bir deneme seçin.
    1. Bir çalıştır ve sonra **eşitliği** sekmesini açıklama görselleştirme panosu olarak seçin.
    1. **Eşitliği** sekmesine giriş olduktan sonra sağdaki menüden bir **eşitliği ID** 'ye tıklayın.
    1. Hassas öznitelerinizi, performans ölçümünü ve eşitliği değerlendirme sayfasında yer alan ilgi çekici eşitliği ölçüsünü seçerek panonuzu yapılandırın.
    1. Hem **ayırma** , hem de **hizmet kalitesi kalitesini** gözlemlemek için grafik türünü bir diğerine değiştirin.



    [![Eşitliği Pano ayırması](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![Eşitliği panosu hizmet kalitesi](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **Modeller bölmesi**
    1. Önceki adımları izleyerek özgün modelinize kaydolduysanız, görüntülemek için sol bölmede **modeller** seçebilirsiniz.
    1. Açıklama görselleştirme panosunu görüntülemek için bir model ve sonra **eşitliği** sekmesini seçin.

    Görselleştirme panosu ve içerdiği öğeler hakkında daha fazla bilgi edinmek için Fairlearn 'in [Kullanıcı kılavuzuna](https://fairlearn.org/v0.6.0/user_guide/assessment.html#fairlearn-dashboard)göz atın.

## <a name="upload-fairness-insights-for-multiple-models"></a>Birden çok model için eşitliği öngörülerini karşıya yükleyin

Birden çok modeli karşılaştırmak ve eşitliği değerlendirmelerinin nasıl farklı olduğunu görmek için görselleştirme panosuna birden fazla model geçirebilir ve bunların performans-eşitliği ticaretlerini karşılaştırabilirsiniz.

1. Modellerinizi eğitme:
    
    Şimdi, bir destek vektör makinesi Estimator 'ı temel alan ikinci bir sınıflandırıcı oluşturuyoruz ve Fairlearn 'in paketini kullanarak bir eşitliği Pano sözlüğünü karşıya yükleyeceğiz `metrics` . Daha önce eğitilen modelin hala kullanılabilir olduğunu varsayıyoruz.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Modellerinizi kaydetme

    Ardından Azure Machine Learning içinde her iki modeli de kaydedin. Kolaylık sağlamak için, sonuçları `id` kayıtlı modelin (biçimdeki bir dize) tahmine ici ile eşleyen bir sözlükte depolayın `name:version` :

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Fairlearn panosunu yerel olarak yükleme

    Eşitliği öngörülerini Azure Machine Learning karşıya yüklemeden önce, bu tahminleri yerel olarak çağrılan Fairlearn panosunda inceleyebilirsiniz. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Ön işlem eşitliği ölçümleri.

    Fairlearn 'in paketini kullanarak bir pano sözlüğü oluşturun `metrics` .

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Önceden hesaplanan eşitliği ölçümlerini karşıya yükleyin.
    
    Şimdi `azureml.contrib.fairness` karşıya yüklemeyi gerçekleştirmek için paketi içeri aktarın:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Bir deneme ve bir çalıştırma oluşturun ve panoyu buna yükleyin:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Önceki bölüme benzer şekilde, görselleştirme panosuna erişmek ve eşitliği ve performans açısından iki modeli karşılaştırmak için, yukarıda açıklanan yollardan birini ( **denemeleri** veya **modeller** aracılığıyla Azure Machine Learning) takip edebilirsiniz.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Azaltılan ve hafiftılan eşitliği öngörülerini karşıya yükleme

Fairlearn 'in [risk azaltma algoritmalarını](https://fairlearn.org/v0.6.0/user_guide/mitigation.html)kullanabilir, üretilen hafifletme modellerini orijinal olmayan modele karşılaştırabilir ve karşılaştırılan modeller arasında performans/eşitliği ticaretlerini izleyebilirsiniz.

[Izgara arama](https://fairlearn.org/v0.6.0/user_guide/mitigation.html#grid-search) azaltma algoritması kullanımını gösteren bir örnek görmek için (farklı eşitliği ve performans ticaretine sahip bir azaltılan modeller koleksiyonu oluşturur) bu [örnek Not defterini](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)inceleyin. 

Birden çok model ' eşitliği Insights 'ı tek bir çalıştırmada karşıya yüklemek, modellerin eşitliği ve performansına göre karşılaştırılmasını sağlar. Belirli bir modelin ayrıntılı eşitliği içgörülerini görmek için model karşılaştırma grafiğinde görüntülenecek modellerin herhangi birine tıklayabilirsiniz.


[![Model karşılaştırma Fairlearn panosu](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Sonraki adımlar

[Model eşitliği hakkında daha fazla bilgi edinin](concept-fairness-ml.md)

[Azure Machine Learning eşitliği örnek not defterlerine göz atın](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
