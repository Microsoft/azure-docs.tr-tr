---
title: Öğretici-Azure 'da Jupyter Not defteri oluşturma ve çalıştırma
description: Veri bilimi 'nde doğrusal gerileme sürecini gösteren Azure Notebooks bir Jupyter Not defteri çalıştırma.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: 42072894b99ebf1cfcbb5e622c2a9c3a4321a944
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496655"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Öğretici: Python ile Jupyter Not defteri oluşturma ve çalıştırma

Bu öğreticide, basit doğrusal regresyon gösteren bir bütün Jupyter Not defteri oluşturmak için Azure Notebooks kullanma işleminde size kılavuzluk eder. Bu öğreticide, farklı hücreler oluşturmayı, hücreleri çalıştırmayı ve Not defterini bir slayt gösterisi olarak sunmayı içeren Jupyter Not defteri Kullanıcı arabirimi hakkında bilgi edinin.

Tamamlanan Not defteri [GitHub-Azure Notebooks örnekleri](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)üzerinde bulunabilir. Bununla birlikte, bu öğretici yeni bir proje ve boş bir not defteriyle başlar, böylece adım adım oluşturmaya deneyim sağlayabilirsiniz.

## <a name="create-the-project"></a>Proje oluşturma

1. [Azure Notebooks](https://notebooks.azure.com) gidin ve oturum açın. (Ayrıntılar için bkz. [hızlı başlangıç-Azure Notebooks Için oturum açma](quickstart-sign-in-azure-notebooks.md)).

1. Ortak profil sayfanızda sayfanın en üstündeki **Projelerim** ' nı seçin:

    ![Projelerim tarayıcı penceresinin üst kısmında bağlantı](media/quickstarts/my-projects-link.png)

1. **Projelerim** sayfasında **+ Yeni proje** ' yi seçin (klavye kısayolu: n); düğme yalnızca tarayıcı penceresi dar ise **+** olarak görünebilir:

    ![Projelerim sayfasında yeni proje komutu](media/quickstarts/new-project-command.png)

1. Görüntülenen **Yeni proje oluştur** açılır penceresinde aşağıdaki ayrıntıları girin veya ayarlayın, sonra **Oluştur**' u seçin.

    - **Proje adı**: doğrusal regresyon örneği-Cricket CHIRP 'leri
    - **Proje kimliği**: doğrusal-regresyon-örnek
    - **Ortak proje**: (işaretsiz)
    - **README.MD oluşturma**: (işaretsiz)

1. Birkaç dakika sonra Azure Notebooks yeni projeye götürür.

## <a name="create-the-data-file"></a>Veri dosyası oluşturma

Not defterinde oluşturduğunuz doğrusal regresyon modeli, projenizdeki *cricket_chirps. csv*adlı bir dosyadan veri çizer. Bu dosyayı [GitHub-Azure Notebooks örneklerinden](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)kopyalayarak veya doğrudan verileri girerek oluşturabilirsiniz. Aşağıdaki bölümlerde her iki yaklaşım da açıklanır.

### <a name="upload-the-data-file"></a>Veri dosyasını karşıya yükle

1. Azure Notebooks içindeki proje panonuzda, **URL 'den** > **karşıya yükle** ' yi seçin.
1. Açılan pencerede, dosya **URL 'sine** ve *cricket_chirps. csv* dosyasına **dosya adı**alanına aşağıdaki URL 'yi girin ve **bitti**' yi seçin.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. *Cricket_chirps. csv* dosyası artık projenizin dosya listesinde görünmelidir:

    ![Yeni oluşturulan CSV dosyası proje dosyası listesinde gösteriliyor](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Sıfırdan dosya oluşturma

1. Azure Notebooks içindeki proje panonuzda **+ yeni** > **boş dosya** ' yı seçin
1. Projenin dosya listesinde bir alan görüntülenir. *Cricket_chirps. csv* yazın ve ENTER tuşuna basın.
1. *Cricket_chirps. csv* dosyasına sağ tıklayın ve **dosyayı Düzenle**' yi seçin.
1. Görüntülenen düzenleyicide aşağıdaki verileri girin:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Dosyayı kaydetmek ve Proje panosuna dönmek için **dosyayı kaydet** ' i seçin.

## <a name="install-project-level-packages"></a>Proje düzeyi paketleri 'ni yükler

Bir not defteri içinde, gerekli paketleri yüklemek için bir kod hücresinde `!pip install` gibi komutları dilediğiniz zaman kullanabilirsiniz. Ancak, bu tür komutlar, Not defterinin kod hücrelerini her çalıştırdığınızda çalıştırılır ve önemli ölçüde zaman alabilir. Bu nedenle, `requirements.txt` bir dosya kullanarak paketleri proje düzeyine yükleyebilirsiniz.

1. Aşağıdaki içeriklerle `requirements.txt` adlı bir dosya oluşturmak için [sıfırdan bir dosya oluşturma](#create-a-file-from-scratch) bölümünde açıklanan süreci kullanın:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Ayrıca, [veri dosyasını karşıya yükleme](#upload-the-data-file)konusunda açıklandığı gibi, dilerseniz yerel bilgisayarınızdan bir `requirements.txt` dosyasını karşıya yükleyebilirsiniz.

1. Proje panosunda **proje ayarları**' nı seçin.
1. Görüntülenen açılan pencerede **ortam** sekmesini seçin ve **+ Ekle**' yi seçin.
1. **Ortam kurulum adımları**altındaki ilk açılan denetimde (işlem), **requirements. txt**' yi seçin.
1. İkinci açılan denetimde (dosya adı), *requirements. txt* (oluşturduğunuz dosya) öğesini seçin.
1. Üçüncü açılan denetimde (Python sürümü), **Python sürüm 3,6**' yı seçin.
1. **Kaydet**’i seçin.

![Requirements. txt dosyasını belirten proje ayarları ortamı sekmesi](media/tutorial/tutorial-requirements-txt.png)

Bu kurulum adımla birlikte, projede çalıştırdığınız herhangi bir not defteri, bu paketlerin yüklendiği bir ortamda çalışır.

## <a name="create-and-run-a-notebook"></a>Not defteri oluşturma ve çalıştırma

Veri dosyası hazır ve proje ortamı ayarlanmış olarak, artık Not defterini oluşturup açabilirsiniz.

1. Proje panosunda **+ yeni** > **Not defteri**' ni seçin.
1. Açılan pencerede, *Doğrusal regresyon örneği-Cricket CHIRP 'leri. ipynb* **öğesini**girin, dil için **Python 3,6** ' i seçin ve ardından **Yeni**' yi seçin.
1. Yeni Not defteri dosya listesinde görüntülendikten sonra, Not defterini başlatmak için seçin. Yeni bir tarayıcı sekmesi otomatik olarak açılır.
1. Ortam ayarlarında bir *requirements. txt* dosyanız olduğundan, "kapsayıcının hazırlanmasını tamamlaması bekleniyor" iletisini görürsünüz. İletiyi kapatmak için **Tamam** ' ı seçebilirsiniz ve not defterinde çalışmaya devam edebilirsiniz; Ancak, ortam tamamen ayarlanana kadar kod hücrelerini çalıştıramazsınız.
1. Not defteri, varsayılan olarak tek bir boş kod hücresi olan Jupyter arabiriminde açılır.

    [Azure Notebooks yeni bir not defterinin Ilk görünümünü ![](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Not defteri arabirimine tura katılın

Not defteri çalışırken, kod ve Markaşağı hücreleri ekleyebilir, bu hücreleri çalıştırabilir ve Not defteri işlemini yönetebilirsiniz. Ancak ilk olarak, arabirimi öğrenmek için birkaç dakika harcamanız gerekir. Tam belgeler için **yardım** > **Not defteri yardım** menüsü komutunu seçin.

Pencerenin üst kısmında aşağıdaki öğeleri görürsünüz:

(A) not defterinizin adı, tıklayarak düzenleyebileceğiniz.
(B), tarayıcınızda yeni sekmeler açan içeren projeye ve proje panonuza gitmek için düğmeler.
(C) not defteriyle çalışmaya yönelik komutları içeren bir menü.
(D) ortak işlemlere yönelik kısayollarla bir araç çubuğu.
(E) hücreleri içeren düzen tuvali.
(F) Not defterinin güvenilir olup olmadığı göstergesi (varsayılan **güvenilir değil**).
(G) bir etkinlik göstergesi ile birlikte Not defterini çalıştırmak için kullanılan çekirdek.

[Jupyıter arabiriminin birincil kullanıcı arabirimi alanı ![](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyıter, birincil UI öğelerine yerleşik bir tur sağlar. **Yardım** > **Kullanıcı arabirimi turu** komutunu seçerek ve açılır pencerelere tıklayarak Turu başlatın.

Menü komutlarının grupları aşağıdaki gibidir:

| Menü | Açıklama |
| --- | --- |
| Dosya | Not defteri dosyasını yönetmek için komutlar, not defterlerini oluşturma ve kopyalama, baskı önizleme gösterme ve Not defterini çeşitli biçimlerde indirme komutları dahil. |
| Düzenle | Hücreleri kesme, kopyalama ve yapıştırma, değerleri bulma ve değiştirme, hücre eklerini yönetme ve görüntü ekleme için tipik komutlar.  |
| Görünüm | Jupyıter Kullanıcı arabiriminin farklı bölümlerinin görünürlüğünü denetlemek için komutlar. |
| Ekle | Geçerli hücrenin üstüne veya altına yeni bir hücre ekleme komutları. Bu komutları sıklıkla bir not defteri oluştururken kullanırsınız. |
| Hücresi | Çeşitli **çalıştırma** komutları farklı birleşimlerde bir veya daha fazla hücre çalıştırır. **Hücre türü** komutları, **kod**, **markın**ve **Ham nbconvert** (düz metin) arasındaki bir hücrenin türünü değiştirir. **Geçerli çıkışlar** ve **tüm çıktılar** komutları çalıştırma kodu çıkışının nasıl gösterileceğini denetler ve tüm çıktıyı temizlemek için bir komut içerir. |
| İne | Kodun çekirdekte nasıl çalıştırıldığını yönetmek için komutlar ve Not defterini çalıştırmak için kullanılan dili veya Python sürümünü değiştirmek için **çekirdeği değiştirme** . |
| Veriler | Proje veya oturumdan dosya yükleme ve indirme komutları. Bkz. [proje veri dosyalarıyla çalışma](work-with-project-data-files.md) |
| Pencere | Görselleştirme, eşleme ve çizim için ek yetenekler sağlayan [Jupyter pencere](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)öğelerini yönetmeye yönelik komutlar.|
| Yardım | Jupyıter arabirimi için yardım ve belge sağlayan komutlar. |

Araç çubuğundaki komutların çoğu eşdeğer menü komutlarına sahiptir. Tek bir istisna, [paylaşma ve sunma ile](present-jupyter-notebooks-slideshow.md)ilgili olarak açıklanan **Slayt gösterisini yazın/düzenleyin**.

Not defterini izleyen bölümlerde doldurmada Bu komutların bir sayısını kullanırsınız.

## <a name="create-a-markdown-cell"></a>Markaşağı hücresi oluşturma

1. Not defteri tuvalinde gösterilen ilk boş hücreye tıklayın. Varsayılan olarak, bir hücre bir **kod** türüdür, yani seçili çekirdek için çalıştırılabilir kod içerecek şekilde tasarlanmıştır (Python, R veya F#). Geçerli tür, araç çubuğundaki tür açılan kutusunda gösterilir:

    ![Hücre türü araç çubuğu açılır menüsü](media/tutorial/tutorial-cell-type-drop-down.png)

1. Araç çubuğu açılır menüsü kullanılarak hücre türünü **Markaşağı** olarak değiştirin; Alternatif olarak **, > ** **hücre türünü** > **markaşağı** menü komutunu kullanın:

    ![Hücre türü menü komutu](media/tutorial/tutorial-cell-type-menu.png)

1. Düzenle 'yi başlatmak için hücreye tıklayın, ardından aşağıdaki Marku girin:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Özet 'i tarayıcı için HTML olarak göstermek için, araç çubuğunda **Çalıştır** komutunu seçin ya da **hücreleri Çalıştır** komutunu > **hücresini** kullanın. Biçimlendirme ve bağlantıların Markaşağı kodu, bir tarayıcıda beklediği gibi görünür.

1. Not defterinde son hücreyi çalıştırdığınızda Jupyter, çalıştırdığınız birinin altında otomatik olarak yeni bir hücre oluşturur. Bu bölümdeki adımları aşağıdaki marksection ile yineleyerek bu hücreye daha fazla markın koyun:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Markazaltma 'yı yeniden düzenlemek için, işlenen hücreye çift tıklayın. Değişiklikleri yaptıktan sonra HTML 'yi tekrar işlemek için hücreyi çalıştırın.

## <a name="create-a-code-cell-with-commands"></a>Komutlarla bir kod hücresi oluşturma

Önceki Markaşağı hücresinde açıklanan komutları doğrudan not defterine ekleyebilirsiniz. Paketleri yüklemek, verileri almak için kıvrımlı veya wget çalıştırmak ya da başka bir şey yapmak için komutları kullanabilirsiniz. Jupi Not defterleri bir Linux sanal makinesi içinde etkin bir şekilde çalışır, bu nedenle, tam Linux komutunun birlikte çalışmak üzere ayarlanmış olması gerekir.

1. Önceki Markaşağı hücresinde **Çalıştır** kullanıldıktan sonra görünen kod hücresinde aşağıdaki komutları girin. Yeni bir hücre görmüyorsanız, **ekleme** ** > Ekle** ' yi kullanarak bir tane oluşturun veya araç çubuğundaki **+** düğmesini kullanın.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Hücreyi çalıştırmadan önce, araç çubuğunda **+** düğmesiyle yeni bir hücre oluşturun, markaşağı olarak ayarlayın ve aşağıdaki açıklamayı girin:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Not defterindeki tüm hücreleri çalıştırmak için **Tümünü Çalıştır** komutunu > **hücreyi** seçin. Markaşağı hücreleri HTML olarak işlendiğine ve komutun çekirdekte çalıştırıldığına ve çekirdek göstergesinin Marku içinde açıklandığı gibi gözlemleytiğine dikkat edin:

    ![Not defteri çekirdeği için meşgul göstergesi](media/tutorial/tutorial-kernel-busy.png)

1. Ayrıca, tüm `pip install` komutlarının çalışması için çok zaman alır ve bu paketleri proje ortamına zaten yüklediklerinden (ve varsayılan olarak Azure Notebooks de dahil olduklarından), "gereksinim zaten memnun. " Bu çıkışın hepsi görsel açıdan dikkat çekici olabilir, bu nedenle satışı (tek bir tıklama kullanarak) **seçin ve sonra** çıktıyı **gizlemek Için** > **hücre çıktılarının** > hücresini kullanın. Çıktıyı tamamen kaldırmak için aynı alt menüdeki **clear** komutunu da kullanabilirsiniz.

    **Iki durumlu** komut yalnızca hücreden en son çıktıyı gizler; hücreyi yeniden çalıştırırsanız, çıkış yeniden görüntülenir.

1. Paketler proje ortamında yüklendiği için, `#`kullanarak `! pip install` komutları not edin; Bu şekilde, bu şekilde, eğitim malzemeleri olarak not defterinde kalabilirler, ancak çalıştırmak için zaman kalmaz ve gereksiz çıkış oluşturmaz. Bu durumda, not defterinde açıklamalı komutların tutulması Not defterinin bağımlılıklarını da gösterir.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Kalan hücreleri oluşturma

Not defterinin geri kalanını doldurmak için bir sonraki bir dizi markı ve kod hücresi oluşturun. Aşağıda listelenen her bir hücre için ilk olarak yeni hücreyi oluşturun, ardından türü ayarlayın ve ardından içeriği yapıştırın.

Her hücreyi oluşturduktan sonra Not defterini çalıştırmayı bekleyebilir, ancak her hücreyi oluştururken çalıştırmak ilginç olur. Tüm hücreler çıktıyı göstermez; herhangi bir hata görmüyorsanız, hücrenin normal şekilde çalıştığını varsayın.

Her kod hücresi, önceki hücrelerde çalıştırılan koda bağlıdır ve hücrelerden birini çalıştırmayı düşünüyorsanız, daha sonraki hücreler hatalara neden olabilir. Bir hücreyi çalıştırmayı unutmuş olduğunu fark ederseniz, geçerli hücreyi çalıştırmadan önce **Yukarıdaki tümünü çalıştır** > **hücresini** kullanmayı deneyin.

Beklenmedik sonuçlar (muhtemelen!!) görürseniz, her hücrenin gerektiği şekilde "Code" veya "Markaşağı" olarak ayarlandığından emin olun. Örneğin, bir "geçersiz sözdizimi" hatası genellikle kod hücresine markı girdiğinizde oluşur.

1. Markın hücresi:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kod hücresi; çalıştırıldığında, tablo içeriğini çıkış olarak gösterir. `print` bildirimine açıklama ekleyerek çıktıyı gizleyebilirsiniz.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Bu koddan "BID y. dtype boyutu değişti" ile ilgili uyarılar görebilirsiniz; uyarılar güvenle yoksayılabilir.

1. Markın hücresi:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Kod hücresi; çalıştırıldığında, bu hücrede çıkış yok.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markın hücresi:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Kod hücresi; çalıştırıldığında, bu hücrede çıktıyı gösterir `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markın hücresi:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Kod hücresi; çalıştırıldığında, bu hücrede `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`gibi sonuçlar gösterilmektedir.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markın hücresi:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markın hücresi:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Kod hücresi; çalıştırıldığında, bu hücre bir grafik çizimi üretir. Çizimi ilk kez görmüyorsanız (ve bunun yerine "şekil boyutu 640x480, 1 ekseniyle"), hücreyi yeniden çalıştırın.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Matplotlib kodundan çıkış çizme](media/tutorial/tutorial-plot-output.png)

1. Markın hücresi:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Çıkışları temizle ve tüm hücreleri yeniden çalıştır

Önceki bölümde yer alan tüm not defterini doldurmak için yukarıdaki adımları uyguladıktan sonra, doğrusal regresyon üzerinde tam öğretici bağlamında çalışan bir kod parçası oluşturdunuz. Kodun ve metinlerin bu doğrudan birleşimi, Not defterlerinin harika avantajlarından biridir!

Tüm Not defterini şimdi yeniden çalıştırmayı deneyin:

1. **Çekirdek** > **yeniden başlatma & çıktıyı temizle**' ye tıklayarak tüm çekirdeğin oturum verilerini ve tüm hücre çıkışlarını temizleyin. Bu komut, bir not defteri tamamlandığında her zaman iyi bir seçimdir. yalnızca kod hücreleri arasında garip bağımlılıklar oluşturmadığınızdan emin olun.

1. **Tümünü çalıştırmak** > **hücreyi** kullanarak not defterini yeniden çalıştırın. Kod çalışırken çekirdek göstergesinin doldurulduğuna dikkat edin.

    Çok uzun süre çalışan veya başka bir şekilde takılmış bir kodunuz varsa, çekirdek > **kesme** komutunu **kullanarak çekirdeği durdurabilirsiniz** .

1. Sonuçları incelemek için not defterinde ilerleyin. (Çizim görünmezse, bu hücreyi yeniden çalıştırın.)

## <a name="save-halt-and-close-the-notebook"></a>Not defterini Kaydet, durdur ve Kapat

Bir not defterini düzenlemekte olduğunuz sırada, bu **dosyanın** geçerli durumunu > **Kaydet ve denetim noktası** komutuyla veya araç çubuğundaki Kaydet düğmesiyle kaydedebilirsiniz. "Checkpoint", oturum sırasında dilediğiniz zaman öğesine döndürülebileceği bir anlık görüntü oluşturur. Kontrol noktaları, bir dizi deneysel değişiklik yapmanıza izin verir ve bu değişiklikler işe alınmazda, **dosyayı** kullanarak bir kontrol noktasına dönebilir > , **denetim noktası komutuna dönün** . Alternatif bir yaklaşım, ek hücreler oluşturmak ve çalıştırmak istemediğiniz herhangi bir kodu açıklama eklemek olur; Her iki yöntem de geçerlidir.

Ayrıca, Not defteri 'nin geçerli durumunun **bir kopyasını projenizde** yeni bir dosyaya oluşturmak için Istediğiniz zaman **dosyayı** da > de kullanabilirsiniz. Bu kopya, otomatik olarak yeni bir tarayıcı sekmesinde açılır.

Bir not defteri ile işiniz bittiğinde, > **Kapat ve Durdur** komutunu kullanın ve **Bu, Not** defterini kapatır ve çalıştıran çekirdeği kapatır. Azure Notebooks ardından tarayıcı sekmesini otomatik olarak kapatır.

## <a name="debug-notebooks-using-visual-studio-code"></a>Visual Studio Code kullanarak not defterlerinde hata ayıklama

Not defterinizdeki kod hücreleri, istediğiniz şekilde davranmıyorsa, kod hataları veya diğer hatalar olabilir. Ancak, değişkenlerin değerini göstermek için `print` deyimlerini kullanarak, tipik bir jupi ortamı hata ayıklama olanakları sunmaz.

Neyse ki not defteri 'nin *. ipynb* dosyasını indirebilir, sonra Python uzantısını kullanarak Visual Studio Code açabilirsiniz. Uzantı doğrudan bir not defterini tek bir kod dosyası olarak içeri aktarır ve açıklamalarda Markaşağı hücreleri korur. Not defteri 'ni içeri aktardıktan sonra, Visual Studio Code hata ayıklayıcıyı kullanarak kodunuzda adım adım ilerledikten, kesme noktaları ayarlamanıza, durumu incelemenize ve benzeri bir durumla devam edebilirsiniz. Kodunuzda düzeltmeler yaptıktan sonra, *. ipynb* dosyasını Visual Studio Code dışarı aktarıp Azure Notebooks geri yükleyin.

Daha fazla bilgi için Visual Studio Code belgelerinde [Jupyter Notebook hata ayıklama](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) bölümüne bakın.

Ayrıca bkz. jupi Not defterleri için Visual Studio Code ek özellikler için [Visual Studio Code-jupde desteği](https://code.visualstudio.com/docs/python/jupyter-support) .

## <a name="next-steps"></a>Sonraki adımlar

- [Örnek not defterlerini keşfet](azure-notebooks-samples.md)

Nasıl yapılır makaleleri:

- [Projeleri oluşturma ve kopyalama](create-clone-jupyter-notebooks.md)
- [Projeleri yapılandırma ve yönetme](configure-manage-azure-notebooks-projects.md)
- [Bir not defteri içinden paket yükler](install-packages-jupyter-notebook.md)
- [Bir slayt gösterisi sunun](present-jupyter-notebooks-slideshow.md)
- [Veri dosyalarıyla çalışma](work-with-project-data-files.md)
- [Veri kaynaklarına erişin](access-data-resources-jupyter-notebooks.md)
- [Azure Machine Learning kullan](use-machine-learning-services-jupyter-notebooks.md)
