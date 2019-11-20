---
title: Visual Studio Code Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Visual Studio Code için Azure Machine Learning yüklemeyi ve Azure Machine Learning bir deneme oluşturma hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: dd85f3a495b90b3a1dc9d3f021d3600496792759
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824365"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Visual Studio Code için Azure Machine Learning kullanmaya başlama

Bu makalede, makine öğrenimi modellerini eğitmek ve dağıtmak için **Visual Studio Code uzantısının Azure Machine Learning** nasıl kullanacağınızı öğreneceksiniz.

[Azure Machine Learning](overview-what-is-azure-ml.md) , makine öğrenimi modellerinin oluşturulmasını, eğitimini ve dağıtımını kolaylaştırır.
+ Eğitim için, denemeleri yerel olarak veya uzaktan çalıştırılmasına yönelik destek sağlar. Her deneme için, hiper parametrelere ince ayar yapmak üzere birden çok çalıştırmanın özel ölçümlerini günlüğe kaydedebilirsiniz
+ Test ve üretim gereksinimleriniz için makine öğrenimi modellerini kolayca dağıtmak üzere Azure Machine Learning de kullanabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

+ Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

+ Windows, Mac ve Linux 'ta çalışan hafif bir kod Düzenleyicisi [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)yükler.

+ [Python 3,5 veya üstünü yükler](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>Uzantıyı yükleme

Azure Machine Learning uzantısını yüklediğinizde, iki uzantı otomatik olarak yüklenir. Bunlar [Azure Hesap uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) ve [Microsoft Python uzantısıdır](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). Python kodunu düzenlemede, çalıştırmaya ve hata ayıklamaya yönelik Python uzantısını kullanma hakkında daha fazla bilgi için bkz. [Python Hello-World öğreticisi](https://code.visualstudio.com/docs/Python/Python-tutorial).

Azure Machine Learning uzantısını yüklemek için:

1. Visual Studio Code'u açın.

1. Uzantılar sekmesine geçin ve "Azure Machine Learning" araması yapın.

1. Uzantı sekmesinde, **yükler**' i seçin.

1. Uzantı için bir hoş geldiniz sekmesi Visual Studio Code açılır ve Azure sembolü (aşağıdaki ekran görüntüsünde kırmızı renkle vurgulanır) etkinlik çubuğuna eklenir.

   ![Visual Studio Code etkinlik çubuğundaki Azure simgesi](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. İletişim kutusunda **oturum aç** ' ı seçin ve Azure ile kimlik doğrulaması yapmak için istemleri izleyin.

   Visual Studio Code uzantısı için Azure Machine Learning birlikte yüklenen Azure Hesap uzantısı, Azure hesabınızda kimlik doğrulaması yapmanıza yardımcı olur. Komutların listesi için bkz. [Azure hesap uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)sayfası.

> [!TIP]
> Uzantı yükleyicisini doğrudan [Azure Machine Learning Visual Studio Code uzantısı (Önizleme) için](https://aka.ms/vscodetoolsforai)de indirebilirsiniz.

## <a name="quickstart-with-azure-machine-learning"></a>Azure Machine Learning ile hızlı başlangıç
Azure Machine Learning kullanarak eğitim betikleri çalıştırmanın birden çok yolu vardır. Yeni başladıysanız, ilk olarak Azure 'da çalıştırmak üzere bir eğitim betiğini hızlı bir şekilde nasıl gönderebileceğinizi inceleyelim.

Azure Machine Learning kavramlarıyla ilgili zaten bilgi sahibiyseniz ve bu uzantıları yönetme ve kullanma konusunda daha fazla ayrıntı istiyorsanız, aşağıda [VS Code Azure Machine Learning derinlemesine](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code) bakın.

## <a name="run-an-existing-python-training-script-in-azure"></a>Azure 'da mevcut bir Python eğitim betiği çalıştırın
Mevcut bir eğitim betiğinize sahipseniz, VS Code için Azure Machine Learning uzantısı yalnızca mükemmel bir düzen, hata ayıklama ve kaynak yönetim deneyimi sağlar, ancak Azure 'da betiğiniz için ölçümleri çalıştırmayı ve depolamayı da kolaylaştırır.

Başlayalım. Zaten varsa kendi eğitim betiğinizi kullanabilir veya örnek [vscode-Tools-for-AI deposunun](https://github.com/microsoft/vscode-tools-for-ai)kopyasını oluşturabilirsiniz. Bu uzantıyla ilgili sorunları çözmek için genel depodır. Ayrıca, bu örnek için kullanacağımız küçük bir **mnıst veritabanını kullanacaksınız** örnek klasörü içerir.

1. VS Code içindeki **mnıst veritabanını kullanacaksınız** klasörünü açın.

1. En sevdiğiniz sanal ortam paketinizi veya Anaconda kullanarak yeni bir Python ortamı oluşturun ve TensorFlow ve sayısal tuş takımı paketlerini kurun.

1. VS Code durum çubuğunun sol alt köşesinde, Python yorumlayıcı olarak oluşturduğunuz yeni ortamı seçin.

1. **Train.py** açın ve hata ayıklayıcıyı açıp Çalıştır düğmesine basarak çalıştırın (veya yalnızca F5 tuşuna basın).

   [![, MNIST eğitimi çalıştırın](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

Her şey doğru şekilde yüklenirse, betik çalışır ve çıktılar klasöründe bir TensorFlow modeli oluşturur.

[TensorFlow modelini göster ![](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

Betiğinizin doğru çalıştığını bildiğinize göre şimdi Azure 'da çalıştıralim!

Bu, ek bir **train.py**değişikliği olmadan kolayca yapılabilir. Ancak, yalnızca birkaç basit değişiklikle, her eğitim çalışması hakkında tercih ettiğiniz önemli ölçümleri otomatik olarak izlemek için Azure Machine Learning kullanabilirsiniz.

### <a name="make-azure-aware-of-your-run-metrics"></a>Çalıştırma ölçümlerinizi Azure 'a duyarlı hale getirme
Azure 'un çalışmalarınızın önemli bilgilerden haberdar olması için projenizi değiştirmek üzere:

1. **Train.py** ile aynı klasörde **amlrun.py** adlı bir dosya oluşturun

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. Amlrun dosyasını **train.py** içinde içeri aktarma

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. **Train.py** 'de Run nesnesini başlatma

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Run. log () işleviyle ölçümleri Azure 'da günlüğe kaydedin:

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Betiği Azure 'da çalıştırma
İşte bu kadar! Şimdi, kodunuzu bulutta çalıştırmak için yalnızca uzantıyı kullanın! Aşağıdaki izlenecek yol videosunun, yeni bir Azure ML çalışma alanı ve işlem oluşturmak için gereken süre miktarını ve eğitim betiğini çalıştırmak için gereken süreyi sıkıştırıp aldığını unutmayın.

   [Azure ML denemesi ![başlatın](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

Deneme Çalıştır düğmesine tıkladıktan sonra, istemleri aşağıdaki gibi yanıtlayın:

1. Azure aboneliğinizi seçme
1. *Yeni* BIR Azure ML çalışma alanı oluşturmayı seçin
1. Çalışma için Python ortamını başlatmak üzere önceden yapılandırılmış bir şablon kümesinden seçim yapın. Şablonlar, şunlar için bir başlangıç noktası ve ekleme ayarları sağlar:
    1. **Pytorch**, **TensorFlow**veya **scikit-öğrenme**
    1. **Tek** veya **Dağıtılmış** işlem eğitimi
    1. Özel ortamlar için **genel**
1. Şablona dahil olmayan herhangi bir paket ekleyerek betiğe yönelik PIP ve Conda paketlerinin listesinin tamamlandığından emin olun.
1. Deneme çalıştırmasının varsayılan adlarını ve özelliklerini gözden geçirin ve JSON dosyasındaki deneme deneyimini **Gönder** bağlantısına tıklayın. JSON dosyası, göndermeden önce deneme ayarlarını gözden geçirmeniz veya değiştirmeniz için yeterli olmadığı için kaydedilmez.
1. Uzantı, sizin için her şeyi ayarlarken ve komut dosyanızı yürüttüğünde, arka ve rahat oturdum!

    [bulutta eğitme ![](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

Birkaç saniye içinde, denemenin Azure 'a gönderildiği, VS Code bildiriminde **deneme çalıştırmasını görüntüle** bağlantısına tıklayarak veya VS Code tarafından Azure Machine Learning Studio 'da ilerleme durumunu görüntüleyebilirsiniz. Azure sekmesinde Yenile düğmesine vurun.

Şu anda, çalışma ölçümlerini görüntüleme yalnızca Studio 'da desteklenir. Yukarıda bahsedilen **Görünüm deneme çalıştırması** bağlantısı sizi, günlüğe kaydettiğiniz ölçümleri görebileceğiniz çalıştırmaya götürür.
[Portalda ![deneme çalıştırması](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>VS Code ayrıntılı Azure Machine Learning

Yukarıdaki izlenecek yolda, en kolay yolu izleyerek bir deneme yaptık. Uzantının, bir deneme çalıştırmak için yönetmeniz gereken adımları en aza indirecek gibi. Bu bölümde, en fazla denetim sağlayarak tüm Azure Machine Learning kavramlarını tek tek nasıl yönetebileceğinizi ele alacağız.

Visual Studio Code ' de eğitim ve makine öğrenimi modellerini dağıtmaya başlamadan önce, bulutta bir [Azure Machine Learning çalışma alanı](concept-workspace.md) oluşturmanız gerekir. Bu çalışma alanı, modellerinizi ve kaynaklarınızı içerecektir.

### <a name="create-a-workspace"></a>Çalışma alanı oluşturma

1. Visual Studio Code etkinlik çubuğunda Azure simgesini seçin. Azure Machine Learning kenar çubuğu görüntülenir.

    [![çalışma alanı oluşturma](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. Azure aboneliğinize sağ tıklayın ve **çalışma alanı oluştur**' u seçin. Varsayılan olarak, oluşturma tarihi ve saati içeren bir ad oluşturulur. Adı **Teamworkspace** olarak değiştirin ve ENTER 'a basın.

1. Ne seçeceğiniz veya yeni bir tane oluşturacağınız hakkında bilgi edinmek istiyorsanız listeden bir kaynak grubu seçin. Yeni bir tane oluşturuyorsanız, modelinizi dağıtmayı planladığınız konuma en yakın konumu seçin. Bu durumda **Batı ABD 2**seçtik.

1. ENTER tuşuna bastıktan sonra, Azure Machine Learning çalışma alanınızı oluşturma isteği alınır. Visual Studio Code bildirim alanındaki işlem hakkında bildirim alırsınız.

1. Yeni oluşturduğunuz çalışma alanınızı bulmak için abonelik düğümünü genişletin.

### <a name="create-an-experiment"></a>Deneme oluşturma
Tek tek model eğitimi çalıştırmalarını izlemek ve analiz etmek için, çalışma alanınızda bir veya daha fazla denemeleri oluşturulabilir. Çalıştırmalar, Azure bulutu 'nda veya yerel makinenizde yapılabilir.

1. **Teamworkspace** çalışma alanını genişletin. **Denemeleri** düğümüne sağ tıklayın ve bağlam menüsünden **deneme oluştur** ' u seçin.

1. Sorulduğunda, denemeniz için bir ad girin. Örnek ekran görüntülerinde, **deneme adı adlandırılmış**olur.

1. Yeni deneme oluşturmak için ENTER ' u seçin. Yeni deneme ağaçta **denemeleri** düğümünün bir alt öğesi olarak görünür.

1. Bir çalışma alanında, **etkin** deneme olarak ayarlamak için bir denemeye sağ tıklayabilirsiniz. **Etkin** deneme, bulutta o anda açık olan ve Visual Studio Code açık olan klasöre bağlantı sağlar. Bu klasör yerel Python betiklerinizi içermelidir. Etkin bir deneme ayarlayarak, tüm eğitim çalıştırmaları için anahtar ölçümler, nerede çalıştırıldıklarından bağımsız olarak deneme içinde depolanır.

    [![deneme oluşturma](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>İşlem hedefleri oluşturma ve yönetme

Visual Studio Code için Azure Machine Learning sayesinde verilerinizi hazırlayabilir, modellerle eğitebilir ve bunları yerel olarak ve Uzaktan işlem hedeflerine dağıtabilirsiniz.

Uzantı Azure Machine Learning için çeşitli uzak işlem hedeflerini destekler. Daha fazla bilgi için, [Azure Machine Learning için desteklenen işlem hedeflerinin](how-to-set-up-training-targets.md)tam listesine bakın.

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Visual Studio Code Azure Machine Learning için işlem hedefleri oluşturma

İşlem hedefi oluşturmak için:

1. Visual Studio Code etkinlik çubuğunda Azure simgesini seçin. Azure Machine Learning kenar çubuğu görüntülenir.

1. Ağaç görünümünde, Azure aboneliğinizi ve Azure Machine Learning çalışma alanını genişletin.

1. Çalışma alanı düğümü altında, **işlem** düğümüne sağ tıklayıp **işlem oluştur**' u seçin.

1. Listeden işlem hedefi türünü seçin.

1. Komut paleti isteminde bir sanal makine boyutu seçin. "GPU" gibi metin ile hesaplamayı filtreleyebilirsiniz.

1. Komut paleti isteminde, işlem hedefi için bir ad girin.

1. Adı girdikten sonra, işlem varsayılan parametreler kullanılarak oluşturulur. Parametreleri değiştirmek için, yeni işlem ' a sağ tıklayın ve **Işlem Düzenle**' yi seçin.

1. Görüntülenen JSON 'da, istediğiniz değişiklikleri yapın ve "Kaydet ve devam et" CodeLens (klavyeyi kullanarak, komut paletini çağırmak ve **Azure ml: Kaydet ve devam et** komutunu çalıştırmak için **Ctrl-Shift-p** tuşlarına basın)

İşte Azure Machine Learning işlem oluşturma ve düzenleme örneği (AMLCompute):

[Visual Studio Code ' de AML işlem oluşturma ![](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>Çalışma yapılandırma dosyası

Bir işlem üzerinde Azure Machine Learning bir deneme çalıştırmak için, bu işlem için uygun şekilde yapılandırılması gerekir. Çalışma yapılandırma dosyası, bu ortamın belirtildiği mekanizmadır.

Yukarıda oluşturulan AmlCompute için bir çalıştırma yapılandırması oluşturma örneği aşağıda verilmiştir.

[işlem için çalıştırma yapılandırması oluşturma ![](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

Azure ML denemeleri 'yi yerel makinenizde çalıştırmak için bir çalıştırma yapılandırma dosyası hala gereklidir. Yerel çalışma yapılandırması oluştururken, kullanılan Python ortamı varsayılan olarak VS Code içinde ayarladığınız yorumlayıcı yoludur.

### <a name="train-and-tune-models"></a>Modelleri eğitme ve ayarlama

VS Code için Azure ML uzantısını kullanarak bir deneme sürümünde eğitim betiği çalıştırmanın birden çok yolu vardır.

1. Eğitim betiğine sağ tıklayın ve Azure **ml: Azure 'da farklı çalıştır deneyi** seçin
1. Denemeler Çalıştır araç çubuğu simgesine tıklayın.
1. Bir çalıştırma yapılandırması düğümüne sağ tıklayın.
1. Azure ML yürütmek için VS Code komut paletini kullanın **: deneme çalıştırması**

Azure Machine Learning deneme çalıştırmak için:

1. Visual Studio Code etkinlik çubuğunda Azure simgesini seçin.

1. Ağaç görünümünde, Azure aboneliğinizi ve Azure Machine Learning çalışma alanını genişletin.

1. Çalışma alanı düğümü altında, **denemeleri** düğümünü genişletin ve çalıştırmak istediğiniz denemeyi sağ tıklatın.

1. **Deneme Çalıştır**' ı seçin.

1. Modelinize eğitebilmeniz için çalıştırmak istediğiniz Python dosyasının adını seçin ve çalıştırmayı göndermek için ENTER tuşuna basın. Note: seçilen dosya, şu anda VS Code açık olan klasörde bulunmalıdır.

1. Çalıştırma gönderildikten sonra, seçtiğiniz deneyin altında bir **çalıştırma düğümü** görüntülenir. Çalıştırmaların durumunu izlemek için bu düğümü kullanın. Note: en son durumu görmek için pencereyi düzenli olarak yenilemek gerekebilir.

İşte daha önce oluşturulan işlem üzerinde bir deneme çalıştırmaya ilişkin bir örnek:

[deneme ![yerel olarak çalıştırın](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Modelleri dağıtma ve yönetme
Azure Machine Learning, makine öğrenimi modellerinizi bulutta ve kenarda dağıtabilir ve yönetebilirsiniz.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Modelinizi Visual Studio Code Azure Machine Learning için kaydedin

Modelinize eğitim edindiniz. bu aşamada, çalışma alanınıza kaydedebilirsiniz. Kayıtlı modelleri izleyebilir ve dağıtabilirsiniz.

Modelinizi kaydetmek için:

1. Visual Studio Code etkinlik çubuğunda Azure simgesini seçin. Azure Machine Learning kenar çubuğu görüntülenir.

1. Ağaç görünümünde, Azure aboneliğinizi ve Azure Machine Learning çalışma alanını genişletin.

1. Çalışma alanı düğümü altında **modeller** ' e sağ tıklayın ve **modeli Kaydet**' i seçin.

1. Komut paletinde, alanına bir model adı girin.

1. Listeden bir **model dosyası** (tek modeller için) veya **model klasörünü** (örneğin, TensorFlow gibi birden çok dosya içeren modeller için) karşıya yüklemeyi seçin.

1. Klasörünüzü veya dosyanızı seçin.

1. Model özelliklerinizi yapılandırmayı bitirdiğinizde pencerenin sağ alt köşesinde **Gönder**' i seçin.

Modelinizi Azure Machine Learning için nasıl kaydedeceğinizi gösteren bir örnek aşağıda verilmiştir:

[bir modeli AML 'ye kaydetme ![](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Hizmetinizi Visual Studio Code dağıtma

Visual Studio Code, Web hizmetinizi şu şekilde dağıtabilirsiniz:
+ Test için Azure Container Instances (acı).
+ Üretim için Azure Kubernetes hizmeti (AKS).

ACI kapsayıcıları gerektiği şekilde oluşturulduğundan, önceden test etmek için bir ACI kapsayıcısı oluşturmanız gerekmez. Ancak, AKS kümelerini önceden yapılandırmanız gerekir. Daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

Bir Web hizmeti dağıtmak için:

1. Visual Studio Code etkinlik çubuğunda Azure simgesini seçin. Azure Machine Learning kenar çubuğu görüntülenir.

1. Ağaç görünümünde, Azure aboneliğinizi ve Azure Machine Learning çalışma alanınızı genişletin.

1. Çalışma alanı düğümü altında **modeller** düğümünü genişletin.

1. Dağıtmak istediğiniz modele sağ tıklayın ve bağlam menüsünden **hizmeti kayıtlı modelden dağıt** ' ı seçin.

1. Komut paletinde, dağıtmak istediğiniz işlem hedefini seçin.

1. Komut paletinde, alanına bu hizmet için bir ad girin.

1. Komut paletinde, komut dosyasını bulmak ve seçmek için klavyenizde Enter tuşunu seçin.

1. Komut paletinde klavyenizdeki Enter tuşunu seçerek Conda bağımlılık dosyasına gidip seçin.

1. Hizmet özelliklerinizi yapılandırmayı bitirdiğinizde pencerenin sağ alt köşesinde dağıtılacak **Gönder** ' i seçin. Hizmet özellikleri dosyasında, yerel bir Docker dosyası veya bir Schema. JSON dosyası belirtebilirsiniz.

Web hizmeti artık dağıtıldı.

Bir Web hizmetini dağıtmaya ilişkin bir örnek aşağıda verilmiştir:

[Web hizmeti dağıtma ![](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Ek özelliklerle deneyin

Visual Studio Code pek çok Azure Machine Learning özelliğine erişmek için komut paleti kullanabilirsiniz. CTRL + SHIFT + P komut paleti türünü çağırmak için. Buradan, uzantının ek Azure ML özelliklerini arayabilirsiniz.

[Visual Studio Code için Azure Machine Learning klavye kısayollarını ![](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

* Visual Studio Code dışında Azure Machine Learning eğitme hakkında yönergeler için bkz. [öğretici: modelleri Azure Machine Learning Ile eğitme](tutorial-train-models-with-aml.md).
* Kodu yerel olarak düzenleme, çalıştırma ve hata ayıklama hakkında yönergeler için bkz. [Python Hello-World öğreticisi](https://code.visualstudio.com/docs/Python/Python-tutorial).
