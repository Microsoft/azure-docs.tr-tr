---
title: Değişiklik gizliliği nasıl-akıllı gürültü (Önizleme)
titleSuffix: Azure Machine Learning
description: Akıllı gürültü açık kaynak kitaplıklarını kullanarak Azure Machine Learning modellere yönelik fark gizliliği en iyi yöntemlerini nasıl uygulayacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 01/21/2020
ms.openlocfilehash: 62a002569696da4ef18e7bd967f027eb8247ef65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98681413"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Azure Machine Learning 'de değişiklik gizliliğini kullanın (Önizleme)

Akıllı gürültü Python açık kaynak kitaplıklarını kullanarak Azure Machine Learning modellere yönelik fark gizliliği en iyi yöntemlerini nasıl uygulayacağınızı öğrenin.

Değişiklik gizliliği, altın standart gizlilik tanımıdır. Bu Gizlilik tanımına bağlı olan sistemler, daha fazla veri yeniden oluşturma ve yeniden tanımlama saldırılarına karşı, yardımcı bilgilere sahip olan saldırgan saldırıları da dahil olmak üzere güçlü bir iş yelpazesi sunar. [Değişiklik gizliliği 'nin nasıl çalıştığı](./concept-differential-privacy.md)hakkında daha fazla bilgi edinin.


## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.
- [Python 3 karşılaştırması](https://www.python.org/downloads/)

## <a name="install-smartnoise-python-libraries"></a>SmartNoise Python kitaplıklarını yükler

### <a name="standalone-installation"></a>Tek başına yükleme

Kitaplıklar, dağıtılmış Spark kümelerinden çalışacak şekilde tasarlanmıştır ve tıpkı diğer tüm paketler gibi yüklenebilir.

Aşağıdaki yönergelerde `python` ve `pip` komutlarınızın ve ' a eşlendiği varsayılır `python3` `pip3` .

[Akıllı gürültü Python paketlerini](https://pypi.org/project/opendp-smartnoise/)yüklemek için PIP kullanın.

`pip install opendp-smartnoise`

Paketlerin yüklendiğini doğrulamak için bir Python istemi başlatın ve şunu yazın:

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

İçeri aktarmalar başarılı olursa kitaplıklar yüklenir ve kullanıma hazırlıolur.

### <a name="docker-image-installation"></a>Docker görüntü yüklemesi

Ayrıca, Docker ile Smartgürültü paketleri de kullanabilirsiniz.

`opendp/smartnoise`Spark, Jupyıter ve örnek kod içeren bir Docker kapsayıcısı içindeki kitaplıkları kullanmak için görüntüyü çekin.

```sh
docker pull opendp/smartnoise:privacy
```

Görüntüyü çektikten sonra Jupyter sunucusunu başlatın:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

Bu `8989` `localhost` , parolanızla birlikte bağlantı noktasında bir jupi sunucusu başlatır `pass@word99` . Kapsayıcıyı adı ile başlatmak için yukarıdaki komut satırını kullandığınız varsayılarak `smartnoise-privacy` , şunu çalıştırarak Jupyter sunucusunda bir bash terminali açabilirsiniz:

```sh
docker exec -it smartnoise-run bash
```

Docker örneği, kapatmadan tüm durumu temizler, bu nedenle çalışan örnekte oluşturduğunuz tüm not defterlerini kaybedersiniz. Bunu çözmek için, bir yerel klasörü başlattığınızda kapsayıcıya bağlayabilirsiniz:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

*My-not defterim* klasörü altında oluşturduğunuz tüm not defterleri yerel dosya sisteminize depolanır.

## <a name="perform-data-analysis"></a>Veri analizini gerçekleştirme

Farklı bir özel yayın hazırlamak için bir veri kaynağı, bir istatistik ve gizlilik koruması düzeyini belirten bazı gizlilik parametrelerini seçmeniz gerekir.  

Bu örnek, vatandaşlık Demographics 'in anonim kayıtlarını temsil eden California genel kullanım mikro verileri (PUMS) öğesine başvurur:

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

Bu örnekte, ortalama ve yaş farkını hesapladık.  Toplam `epsilon` 1,0 (Epsilon, gizlilik parametremiz) kullanarak, işlem yapmak istediğimiz iki miktar boyunca gizlilik bütçemizi yayıyoruz. [Gizlilik ölçümleri](concept-differential-privacy.md#differential-privacy-metrics)hakkında daha fazla bilgi edinin.

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Sonuçlar aşağıdakine benzer şekilde görünür:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Bu örnek hakkında dikkat etmeniz gereken bazı önemli noktalar vardır.  İlk olarak, `Analysis` nesnesi bir veri işleme grafiğini temsil eder.  Bu örnekte, ortalama ve varyans aynı kaynak düğümden hesaplanır.  Ancak, girişleri zaman çıktıları ile Birleşik olarak birleştiren daha karmaşık ifadeler ekleyebilirsiniz.

Analiz grafı, `data_upper` `data_lower` yaş için alt ve üst sınırları belirterek ve meta verileri içerir.  Bu değerler, değişiklik gizliliğini sağlamak üzere paraziti tam olarak ayarlamak için kullanılır.  Bu değerler, bazı aykırı değerleri veya eksik değerleri işlemede de kullanılır.

Son olarak, analiz grafiği harcanan toplam gizlilik bütçesini izler.

Birden fazla mekanizma, istatistikler ve yardımcı program işlevleriyle daha karmaşık analiz grafikleri oluşturmak için kitaplığı kullanabilirsiniz:

| İstatistikler    | Düzen | Yardımcı Programlar  |
| ------------- |------------|------------|
| Count         | Gauss   | Cast       |
| Histogram     | Geometrik  | Clamping   |
| Ortalama          | Laplace    | Dijitalleştirerek   |
| Dağılım Dilimleri     |            | Filtre     |
| Sum           |            | Dayanak |
| Varyans/Kovaryans |      | Dönüşüm  |

Daha fazla bilgi için bkz. [veri analizi Not defteri](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb) .

## <a name="approximate-utility-of-differentially-private-releases"></a>Farklı özel sürümlerin yaklaşık bir yardımcı programı

Değişiklik gizliliği gürültü ayarlayarak çalıştığından, sürümlerin yardımcı programı gizlilik riskine bağlı olarak farklılık gösterebilir.  Genellikle, her birinin korunması için gereken gürültü, örnek boyutları büyük büyüyerek, ancak tek bir bireyi hedefleyen yayınlar için sonucu ortaya çıkmaktadır.  Analistleri, yayının ne kadar yararlı olduğunu anlamak için bir sürümün doğruluk bilgilerini gözden geçirebilir:

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

Bu işlemin sonucu aşağıdakine benzer görünmelidir:

```text
Age accuracy is: 0.2995732273553991
```

Bu örnek, yukarıdaki gibi ortalaması hesaplar ve `get_accuracy` 0,05 ' de doğruluk istemek için işlevini kullanır `alpha` . Bir `alpha` 0,05, %95 aralığını temsil eder, bu değer, bu değerin, bildirilen doğruluk sınırları dahilinde %95 ' de düşecek.  Bu örnekte, bildirilen doğruluk 0,3 ' dir; bu da yayınlanan değerin 0,6 Genişlik (yaklaşık %95) aralığında olacağı anlamına gelir.  Bu değeri hata çubuğu olarak düşünmek doğru değildir, çünkü yayınlanan değer tarafından belirtilen orandaki bildirilen doğruluk aralığının dışına düşecek `alpha` ve Aralık dışındaki değerler her iki yönde de dışında olabilir.

Analistleri `get_accuracy` `alpha` , ek gizlilik maliyeti ödemeden daha dar veya daha fazla güven aralığı almak için farklı değerleri sorgulayabilir.

## <a name="generate-a-histogram"></a>Histogram oluşturma

Yerleşik `dp_histogram` işlev, aşağıdaki veri türlerinden herhangi birinde farklı şekilde özel histogramlar oluşturur:

- Numara kümesinin depo gözlerine bölünmesi gereken bir sürekli değişken
- Yalnızca iki değer üzerinde olabilen bir Boole veya dichotom değişkeni
- Dizeler olarak numaralandırılan ayrı kategorilerin bulunduğu kategorik bir değişken

İşte `Analysis` bir sürekli değişken histogramını belirten sepetler için bir örnek:

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Bireyler histogram bölmeleri arasında tutarlı bir şekilde bölüntiğinden, histogram çok sayıda bölme içerse bile, gizlilik maliyeti, histogram başına yalnızca bir kez uygulanır.

Histogramlar hakkında daha fazla bilgi için bkz. histogram [Not defteri](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb).

## <a name="generate-a-covariance-matrix"></a>Kovaryans matrisi oluşturma

SmartNoise, işlevi ile üç farklı `dp_covariance` işlev sunar:

- İki vektör arasındaki Kovaryans
- Bir matrisin Kovaryans matrisi
- Matris çiftinin çapraz Kovaryans matrisi

Bir skaler Kovaryans Hesaplama örneği aşağıda verilmiştir:

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Daha fazla bilgi için bkz. [Kovaryans Not defteri](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>Sonraki Adımlar

- [Smartnoise örnek not defterlerini](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis)araştırma.