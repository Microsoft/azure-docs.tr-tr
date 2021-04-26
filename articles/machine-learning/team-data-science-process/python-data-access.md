---
title: Python istemci kitaplığı ile veri kümelerine erişme-takım veri bilimi Işlemi
description: Yerel bir Python ortamından güvenli bir şekilde Azure Machine Learning verilerine erişmek ve bunları yönetmek için Python istemci kitaplığını yükleyip kullanın.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b773241fdff41323272422f99e88f0d01e884055
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889561"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Azure Machine Learning Python istemci kitaplığını kullanarak Python ile veri kümelerine erişim
Microsoft Azure Machine Learning Python istemci kitaplığının önizlemesi, yerel bir Python ortamından Azure Machine Learning veri kümelerine güvenli erişim sağlayabilir ve bir çalışma alanında veri kümelerinin oluşturulmasını ve yönetilmesini sağlar.

Bu konuda aşağıdakiler hakkında yönergeler sağlanmaktadır:

* Machine Learning Python istemci kitaplığı 'nı yükler
* Yerel Python ortamınızdan Azure Machine Learning veri kümelerine erişim yetkisi alma yönergeleri dahil olmak üzere veri kümelerine erişin ve karşıya yükleyin
* denemeleri 'deki ara veri kümelerine erişme
* veri kümelerini numaralandırmak, meta verilere erişmek, bir veri kümesinin içeriğini okumak, yeni veri kümeleri oluşturmak ve mevcut veri kümelerini güncelleştirmek için Python istemci kitaplığını kullanın

## <a name="prerequisites"></a><a name="prerequisites"></a>Önkoşullar
Python istemci kitaplığı, aşağıdaki ortamlar altında test edilmiştir:

* Windows, Mac ve Linux
* Python 2,7 ve 3.6 +

Aşağıdaki paketlere bir bağımlılığı vardır:

* istekleri
* Python-dateutil
* pandas

Python, IPython ve yukarıda listelenen üç paket ile birlikte gelen [Anaconda](https://www.anaconda.com/) veya [Canopy](https://store.enthought.com/downloads/)gibi bir Python dağıtımı kullanmanızı öneririz. IPython kesinlikle gerekli olmasa da, verileri etkileşimli bir şekilde işlemek ve görselleştirmek için harika bir ortamdır.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Azure Machine Learning Python istemci kitaplığı 'nı yüklemek
Bu konuda özetlenen görevleri gerçekleştirmek için Python istemci kitaplığı Azure Machine Learning ' nı yükler. Bu kitaplık, [Python paket dizininden](https://pypi.python.org/pypi/azureml)kullanılabilir. Bunu Python ortamınıza yüklemek için, yerel Python ortamınızdan aşağıdaki komutu çalıştırın:

```console
pip install azureml
```

Alternatif olarak, [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)'daki kaynaklardan indirebilir ve yükleyebilirsiniz.

```console
python setup.py install
```

Makinenizde git yüklüyse, doğrudan git deposundan yüklemek için PIP 'yi kullanabilirsiniz:

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Veri kümelerine erişmek için kod parçacıklarını kullanma
Python istemci kitaplığı, çalışan denemeleri adresinden mevcut veri kümelerinize programlı erişim sağlar.

Azure Machine Learning Studio (klasik) web arabiriminden, yerel makinenizde Pandas DataFrame nesneleri olarak veri kümelerini indirmek ve seri durumdan çıkarmak için gerekli tüm bilgileri içeren kod parçacıkları oluşturabilirsiniz.

### <a name="security-for-data-access"></a><a name="security"></a>Veri erişimi için güvenlik
Azure Machine Learning Studio (klasik) tarafından Python istemci kitaplığı ile kullanılmak üzere sunulan kod parçacıkları, çalışma alanı KIMLIĞINIZI ve yetkilendirme belirtecini içerir. Bunlar, çalışma alanınıza tam erişim sağlar ve parola gibi korunması gerekir.

Güvenlik nedenleriyle, kod parçacığı işlevselliği yalnızca rolü çalışma alanı için **sahip** olarak ayarlanmış kullanıcılar tarafından kullanılabilir. Rolünüzde, **Ayarlar** altındaki **Kullanıcılar** sayfasında Azure Machine Learning Studio (klasik) görüntülenir.

![Ekran görüntüsü Azure Machine Learning Studio KULLANıCıLARı sayfasındaki ayarları gösterir.][security]

Rolünüz **sahip** olarak ayarlanmamışsa, bir sahip olarak yeniden davet edebilir veya çalışma alanının sahibinden kod parçacığını size vermesini isteyebilirsiniz.

Yetkilendirme belirtecini almak için şu seçeneklerden birini seçebilirsiniz:

* Sahibinden belirteç isteyin. Sahipler, Azure Machine Learning Studio (klasik) içindeki çalışma alanının ayarlar sayfasından yetkilendirme belirteçlerine erişebilirler. Sol bölmeden **Ayarlar** ' ı seçin ve birincil ve ikincil belirteçleri görmek IÇIN **Yetkilendirme belirteçleri** ' ne tıklayın. Birincil veya ikincil yetkilendirme belirteçleri kod parçacığında kullanılabilir olsa da, sahiplerin yalnızca ikincil yetkilendirme belirteçlerini paylaşması önerilir.

   ![Yetkilendirme belirteçleri](./media/python-data-access/ml-python-access-settings-tokens.png)

* Sahip rolüne yükseltilmesini sorma: çalışma alanının geçerli bir sahibinin öncelikle çalışma alanından birini kaldırması ve bunu bir sahip olarak yeniden davet etmesi gerekir.

Geliştiriciler çalışma alanı KIMLIĞINI ve yetkilendirme belirtecini aldıktan sonra, rolünden bağımsız olarak kod parçacığını kullanarak çalışma alanına erişebilirler.

Yetkilendirme belirteçleri, **Ayarlar** altındakı **Yetkilendirme belirteçleri** sayfasında yönetilir. Bunları yeniden oluşturabilirsiniz, ancak bu yordam önceki belirtece erişimi iptal eder.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Yerel Python uygulamasından veri kümelerine erişme
1. Machine Learning Studio (klasik) içinde, sol taraftaki Gezinti çubuğunda **veri kümeleri** ' ne tıklayın.
2. Erişmek istediğiniz veri kümesini seçin. **Veri kümeleri listesinden veya** **örnekler** listesinden herhangi bir veri kümesinden birini seçebilirsiniz.
3. Alt araç çubuğundan **veri erişim kodu oluştur**' a tıklayın. Veriler Python istemci kitaplığıyla uyumsuz bir biçimde ise, bu düğme devre dışı bırakılır.
   
    ![Ekran görüntüsünde VERI kümesi oluşturma kodu olan VERI kümeleri gösterilmektedir.][datasets]
4. Görüntülenen pencereden kod parçacığını seçin ve panonuza kopyalayın.
   
    ![Veri erişim kodu oluştur düğmesi][dataset-access-code]
5. Kodu yerel Python uygulamanızın not defterine yapıştırın.
   
    ![Kodu Not defterine yapıştırın][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Machine Learning denemeleri 'deki ara veri kümelerine erişin
Machine Learning Studio (klasik) ' de bir deneme çalıştıktan sonra, modül çıkış düğümlerinden ara veri kümelerine erişmek mümkündür. Ara veri kümeleri, bir model aracı çalıştırıldığında oluşturulan ve ara adımlarda kullanılan veri veri kümeleridir.

Veri biçimi Python istemci kitaplığıyla uyumlu olduğu sürece ara veri kümelerine erişilebilir.

Aşağıdaki biçimler desteklenir (Bu biçimler için sabitler `azureml.DataTypeIds` sınıfında bulunur):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Bir modül çıkış düğümünün üzerine gelerek biçimi belirleyebilirsiniz. Bir araç ipucunda düğüm adıyla birlikte görüntülenir.

[Bölünmüş][split] modül gibi bazı modüller, `Dataset` Python istemci kitaplığı tarafından desteklenmeyen adlı bir biçime çıkış.

![Veri kümesi biçimi][dataset-format]

Desteklenen bir biçime çıkış almak için [CSV 'ye Dönüştür][convert-to-csv]gibi bir dönüştürme modülü kullanmanız gerekir.

![GenericCSV biçimi][csv-format]

Aşağıdaki adımlarda bir deneme oluşturan, çalıştıran ve ara veri kümesine erişen bir örnek gösterilmektedir.

1. Yeni bir deneme oluşturun.
2. **Yetişkin Census geliri Ikili sınıflandırma veri kümesi** modülünü ekleyin.
3. [Bölünmüş][split] bir modül ekleyin ve girişini veri kümesi modül çıktısına bağlayın.
4. CSV modülüne bir [dönüştürme][convert-to-csv] ekleyin ve girişini [bölünmüş][split] modül çıktılarından birine bağlayın.
5. Denemeyi kaydedin, çalıştırın ve işin bitmesini bekleyin.
6. [CSV 'ye Dönüştür][convert-to-csv] modülüne çıkış düğümüne tıklayın.
7. Bağlam menüsü göründüğünde, **veri erişim kodu oluştur**' u seçin.
   
    ![Bağlam Menüsü][experiment]
8. Kod parçacığını seçin ve görüntülenen pencereden panonuza kopyalayın.
   
    ![Bağlam menüsünden erişim kodu oluştur][intermediate-dataset-access-code]
9. Kodu Not defterinize yapıştırın.
   
    ![Kodu Not defterine Yapıştır][ipython-intermediate-dataset]
10. Matplotlib kullanarak verileri görselleştirebilirsiniz. Bu, Yaş sütunu için bir histogram içinde görüntülenir:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Veri kümelerine erişmek, okumak, oluşturmak ve yönetmek için Python istemci kitaplığı Machine Learning kullanın
### <a name="workspace"></a>Çalışma alanı
Çalışma alanı, Python istemci kitaplığının giriş noktasıdır. `Workspace`Bir örnek oluşturmak için çalışma alanı kimliğiniz ve yetkilendirme belirtecinizle birlikte sınıfı sağlayın:

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>Veri kümelerini listeleme
Belirli bir çalışma alanındaki tüm veri kümelerini numaralandırmak için:

```python
for ds in ws.datasets:
    print(ds.name)
```

Yalnızca Kullanıcı tarafından oluşturulan veri kümelerini numaralandırmak için:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

Yalnızca örnek veri kümelerini numaralandırmak için:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

Bir veri kümesine ad ile erişebilirsiniz (büyük/küçük harfe duyarlıdır):

```python
ds = ws.datasets['my dataset name']
```

Ya da dizine göre erişebilirsiniz:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>Meta veri
Veri kümelerinde içeriğe ek olarak meta veriler vardır. (Ara veri kümeleri, bu kural için bir özel durumdur ve hiç meta veri içermez.)

Bazı meta veri değerleri Kullanıcı tarafından oluşturma sırasında atanır:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Diğer kullanıcılar Azure ML tarafından atanan değerlerdir:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

`SourceDataset`Kullanılabilir meta veriler hakkında daha fazla bilgi için sınıfına bakın.

### <a name="read-contents"></a>İçeriği oku
Machine Learning Studio (klasik) tarafından sunulan kod parçacıkları, veri kümesini bir Pandas DataFrame nesnesine otomatik olarak indirip serisini kaldıramıyor. Bu yöntem ile yapılır `to_dataframe` :

```python
frame = ds.to_dataframe()
```

Ham verileri karşıdan yüklemeyi tercih ediyorsanız ve serisini kendi kendinize gerçekleştirmek istiyorsanız, bu bir seçenektir. Bu anda, Python istemci kitaplığının serisini kaldıramıyor ' ARFF ' gibi biçimler için tek seçenektir.

İçeriği metin olarak okumak için:

```python
text_data = ds.read_as_text()
```

İçeriği ikili olarak okumak için:

```python
binary_data = ds.read_as_binary()
```

Ayrıca, içeriğe yalnızca bir akış açabilirsiniz:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>Yeni bir veri kümesi oluştur
Python istemci kitaplığı, Python programınızdaki veri kümelerini karşıya yüklemenize olanak sağlar. Bu veri kümeleri daha sonra çalışma alanınızda kullanıma sunulmuştur.

Verilerinize bir Pandas veri çerçevesinde sahipseniz, aşağıdaki kodu kullanın:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Verileriniz zaten serileştirilmiş ise şunları kullanabilirsiniz:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Python istemci kitaplığı, bir Pandas DataFrame 'i aşağıdaki biçimlere seri hale getirmek için kullanılabilir (bunlar `azureml.DataTypeIds` sınıfta sabitler):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Mevcut bir veri kümesini güncelleştirme
Mevcut bir veri kümesiyle eşleşen bir ada sahip yeni bir veri kümesini karşıya yüklemeye çalışırsanız, bir çakışma hatası almalısınız.

Mevcut bir veri kümesini güncelleştirmek için önce var olan veri kümesine bir başvuru almanız gerekir:

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Ardından `update_from_dataframe` , Azure 'da veri kümesinin içeriğini seri hale getirmek ve değiştirmek için kullanın:

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Verileri farklı bir biçimde seri hale getirmek istiyorsanız, isteğe bağlı parametre için bir değer belirtin `data_type_id` .

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

İsteğe bağlı olarak, parametresi için bir değer belirterek yeni bir açıklama ayarlayabilirsiniz `description` .

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

İsteğe bağlı olarak, parametresi için bir değer belirterek yeni bir ad belirleyebilirsiniz `name` . Şu andan itibaren veri kümesini yalnızca yeni adı kullanarak alacaksınız. Aşağıdaki kod, verileri, adı ve açıklamayı günceller.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

`data_type_id` `name` Ve parametreleri, `description` isteğe bağlıdır ve varsayılan olarak önceki değerlerine ayarlanır. `dataframe`Parametresi her zaman gereklidir.

Verileriniz zaten serileştirilmiş ise yerine kullanın `update_from_raw_data` `update_from_dataframe` . Yerine yalnızca ' i geçirirseniz `raw_data`  `dataframe` , benzer bir şekilde işe yarar.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: /azure/machine-learning/studio-module-reference/convert-to-csv
[split]: /azure/machine-learning/studio-module-reference/split-data