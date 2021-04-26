---
title: VS Code, kaynak uzantısı oluşturma ve yönetme (Önizleme)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Visual Studio Code uzantısını kullanarak Azure Machine Learning kaynaklarını oluşturma ve yönetme hakkında bilgi edinin.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010682"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>VS Code Uzantısı ile Azure Machine Learning kaynaklarını yönetme (Önizleme)

Azure Machine Learning kaynaklarını VS Code uzantısıyla yönetmeyi öğrenin.

![Azure Machine Learning VS Code uzantısı](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bir hesabınız yoksa, [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)denemek için kaydolun.
- Visual Studio Code. Bunu yapmazsanız, [yükleyebilirsiniz](https://code.visualstudio.com/docs/setup/setup-overview).
- VS Code Azure Machine Learning uzantısı. Uzantıyı yüklemek için [Azure Machine Learning vs Code uzantısı yükleme kılavuzunu](tutorial-setup-vscode-extension.md#install-the-extension) izleyin.

Aşağıdaki işlemlerin hepsi, Visual Studio Code Azure Machine Learning görünümünde olduğunu varsayar. Uzantıyı başlatmak için VS Code etkinlik çubuğunda **Azure** simgesini seçin.

## <a name="workspaces"></a>Çalışma Alanları

Daha fazla bilgi için bkz. [çalışma alanları](concept-workspace.md).

### <a name="create-a-workspace"></a>Çalışma alanı oluşturma

1. Azure Machine Learning görünümünde, abonelik düğümünüz ' a sağ tıklayın ve **çalışma alanı oluştur**' u seçin.
1. İsteminde:
    1. Çalışma alanınız için bir ad sağlayın
    1. Azure aboneliğinizi seçme
    1. Çalışma alanını sağlamak için yeni bir kaynak grubu seçin veya oluşturun
    1. Çalışma alanının sağlanacağı konumu seçin.

Çalışma alanı oluşturmak için alternatif yöntemler şunlardır:

- Komut paleti > komut **paleti** ' ni açın ve metin Istemine gırın **Azure ml: çalışma alanı oluştur**.
- `+`Azure Machine Learning görünümünün en üstündeki simgeye tıklayın.
- Diğer kaynakların sağlanması sırasında bir çalışma alanı seçmeniz istendiğinde yeni bir çalışma alanı oluşturun.

### <a name="remove-a-workspace"></a>Çalışma alanını kaldır

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Kaldırmak istediğiniz çalışma alanına sağ tıklayın.
1. Kaldırmak isteyip istemediğinizi seçin:
    - *Yalnızca çalışma alanı*: Bu seçenek **yalnızca** çalışma alanı Azure kaynağını siler. Kaynak grubu, depolama hesapları ve çalışma alanının iliştirildiği diğer kaynaklar hala Azure 'da.
    - *İlişkili kaynaklarla*: Bu seçenek çalışma alanını **ve** onunla ilişkili tüm kaynakları siler.

## <a name="datastores"></a>Veri depoları

VS Code uzantısı Şu anda aşağıdaki türlerin veri depolarını desteklemektedir:

- Azure Dosya Paylaşımı
- Azure Blob Depolama

Bir çalışma alanı oluşturduğunuzda, bu türlerin her biri için bir veri deposu oluşturulur.

Daha fazla bilgi için bkz. [veri depoları](concept-data.md#datastores).

### <a name="create-a-datastore"></a>Veri deposu oluşturma

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Veri deposunu oluşturmak istediğiniz çalışma alanı düğümünü genişletin.
1. **Veri depoları** düğümüne sağ tıklayın ve **veri deposunu kaydet**' i seçin.
1. İsteminde:
    1. Veri deposu için bir ad sağlayın.
    1. Veri deposu türünü seçin.
    1. Depolama kaynağınızı seçin. Çalışma alanım ile ilişkili bir depolama kaynağı seçebilir veya Azure aboneliklerinizde geçerli bir depolama kaynağından seçim yapabilirsiniz.
    1. Verilerinizin daha önce seçilen depolama kaynağının içinde olduğu kapsayıcıyı seçin.
1. Bir yapılandırma dosyası VS Code görüntülenir. Yapılandırma dosyanız karşılandıysanız **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.

### <a name="manage-a-datastore"></a>Veri deposunu yönetme

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanı düğümünü genişletin.
1. Çalışma alanınızın içindeki **Datamağazaların** düğümünü genişletin.
1. İstediğiniz veri deposunu seçin:
    - *Varsayılan olarak ayarlayın*. Denemeleri her çalıştırdığınızda, bu veri deposu kullanılacaktır.
    - *Salt okuma ayarlarını inceleyin*.
    - *Değiştir*. Kimlik doğrulama türünü ve kimlik bilgilerini değiştirin. Desteklenen kimlik doğrulama türleri, hesap anahtarını ve SAS belirtecini içerir.

## <a name="datasets"></a>Veri kümeleri

Uzantı şu anda aşağıdaki veri kümesi türlerini desteklemektedir:

- *Tablo*: verileri bir veri çerçevesine (Pandas veya pyspark) sunmanızı sağlar.
- *Dosya*: bir dosya veya dosya koleksiyonu. Dosyaları, işlem için yüklemenize veya oluşturmanıza olanak sağlar.

Daha fazla bilgi için bkz. [veri kümeleri](concept-data.md#datasets)

### <a name="create-dataset"></a>Veri kümesi oluşturma

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Veri deposunu oluşturmak istediğiniz çalışma alanı düğümünü genişletin.
1. **Veri kümeleri** düğümüne sağ tıklayın ve **veri kümesi oluştur**' u seçin.
1. İsteminde:
    1. Veri kümesi türünü seçin
    1. Verilerin bilgisayarınızda mi yoksa Web 'de mi olduğunu tanımlayın
    1. Verilerinizin konumunu belirtin. Bu, tek bir dosya ya da veri dosyalarınızı içeren bir dizin olabilir.
    1. Verilerinizi karşıya yüklemek istediğiniz veri deposunu seçin.
    1. Veri deposundaki veri kümenizin tanımlanmasına yardımcı olan bir ön ek sağlayın.

### <a name="version-datasets"></a>Sürüm veri kümeleri

Makine öğrenimi modellerini oluştururken, veriler değiştikçe veri kümenizin sürümünü kullanmak isteyebilirsiniz. Bunu VS Code uzantısında yapmak için:

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanı düğümünü genişletin.
1. **Veri kümeleri** düğümünü genişletin.
1. Sürüm eklemek istediğiniz veri kümesine sağ tıklayın ve **Yeni sürüm oluştur**' u seçin.
1. İsteminde:
    1. Veri kümesi türünü seçin
    1. Verilerin bilgisayarınızda mi yoksa Web 'de mi olduğunu tanımlayın.
    1. Verilerinizin konumunu belirtin. Bu, tek bir dosya ya da veri dosyalarınızı içeren bir dizin olabilir.
    1. Verilerinizi karşıya yüklemek istediğiniz veri deposunu seçin.
    1. Veri deposundaki veri kümenizin tanımlanmasına yardımcı olan bir ön ek sağlayın.

### <a name="view-dataset-properties"></a>Veri kümesi özelliklerini görüntüle

Bu seçenek, belirli bir veri kümesiyle ilişkili meta verileri görmenizi sağlar. Bunu VS Code uzantısında yapmak için:

1. Çalışma alanı düğümünü genişletin.
1. **Veri kümeleri** düğümünü genişletin.
1. İncelemek istediğiniz veri kümesine sağ tıklayın ve **veri kümesi özelliklerini görüntüle**' yi seçin. Bu, en son veri kümesi sürümünün özelliklerine sahip bir yapılandırma dosyası görüntüler.

> [!NOTE]
> Veri kümenizin birden çok sürümüne sahipseniz, yalnızca veri kümesi düğümünü genişleterek ve ilgilendiğiniz sürümde bu bölümde açıklanan adımların aynısını gerçekleştirerek belirli bir sürümün veri kümesi özelliklerini görüntülemeyi tercih edebilirsiniz.

### <a name="unregister-datasets"></a>Veri kümelerinin kaydını sil

Bir veri kümesini ve tüm sürümünü kaldırmak için kaydını silin. Bunu VS Code uzantısında yapmak için:

1. Çalışma alanı düğümünü genişletin.
1. **Veri kümeleri** düğümünü genişletin.
1. Kaydını silmek istediğiniz veri kümesine sağ tıklayın ve **veri kümesinin kaydını sil**' i seçin.

## <a name="environments"></a>Ortamlar

Daha fazla bilgi için bkz. [ortamlar](concept-environments.md).

### <a name="create-environment"></a>Ortam Oluştur

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Veri deposunu oluşturmak istediğiniz çalışma alanı düğümünü genişletin.
1. **Ortamlar** düğümüne sağ tıklayın ve **Ortam Oluştur**' u seçin.
1. İsteminde:
    1. Ortamınız için bir ad sağlayın
    1. Ortam yapılandırmanızı tanımlayın:
        - *Seçkin ortamlar*: Azure Machine Learning önceden yapılandırılmış ortamlar. JSON dosyasındaki özelliğini değiştirerek ortamı daha ayrıntılı bir şekilde özelleştirebilirsiniz `dependencies` . [Seçkin ortamlar](resource-curated-environments.md)hakkında daha fazla bilgi edinin.
        - *Conda bağımlılıklar dosyası*: Anaconda ortamları için ortam tanımınızı içeren dosya belirtilebilir.
        - *PIP gereksinimleri dosyası*: PIP ortamları için ortam tanımınızı içeren dosya sağlanmış olabilir.
        - *Mevcut Conda ortamı*: Bu seçenek, yerel bilgisayarınızdaki Conda ortamlarını arar ve seçili ortamdan bir ortam oluşturmaya çalışır.
        - *Özel*: kendi kanallarınızı ve bağımlılıklarınızı tanımlayın
    1. Düzenleyicide bir yapılandırma dosyası açılır. Yapılandırmanızı karşılıyoruz, **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.

### <a name="view-environment-configurations"></a>Ortam yapılandırmasını görüntüleme

Uzantıdaki belirli bir ortam için bağımlılıkları ve konfigürasyonları görüntülemek için:

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanı düğümünü genişletin.
1. **Ortamlar** düğümünü genişletin.
1. Görüntülemek istediğiniz ortama sağ tıklayın ve **ortamı görüntüle**' yi seçin.

### <a name="edit-environment-configurations"></a>Ortam yapılandırmasını düzenle

Uzantıdaki belirli bir ortam için bağımlılıkları ve konfigürasyonları düzenlemek için:

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **ortamlar** düğümünü genişletin.
1. Görüntülemek istediğiniz ortama sağ tıklayın ve **ortamı Düzenle**' yi seçin.
1. Değişiklikleri yaptıktan sonra, yapılandırmanızla memnun kaldıysanız **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.

## <a name="experiments"></a>Denemeler

Daha fazla bilgi için bkz. [denemeleri](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-experiment"></a>Deneme oluşturma

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanı düğümünü genişletin.
1. Çalışma alanınızdaki **denemeleri** düğümüne sağ tıklayın ve **deneme oluştur**' u seçin.
1. Komut isteminde, denemeniz için bir ad girin.

### <a name="run-experiment"></a>Deneme Çalıştır

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **denemeleri** düğümünü genişletin.
1. Çalıştırmak istediğiniz deneye sağ tıklayın.
1. Etkinlik çubuğunda **denemeler Çalıştır** simgesini seçin.
1. Denemenizi yerel olarak mı yoksa uzaktan mi çalıştırmak istediğinizi seçin. Denemeleri ile yerel olarak çalışma ve hata ayıklama hakkında daha fazla bilgi için [hata ayıklama kılavuzuna](how-to-debug-visual-studio-code.md) bakın.
1. Aboneliğinizi seçin.
1. Denemeyi çalıştırmak için Azure ML Çalışma Alanı seçin.
1. Denemenizi seçin.
1. Denemeyi çalıştırmak için bir işlem seçin veya oluşturun.
1. Denemeniz için bir çalıştırma yapılandırması seçin veya oluşturun.

Alternatif olarak, uzantının üst kısmında denemeyi **Çalıştır** düğmesini seçebilir ve komut isteminde denemenizin çalışmasını yapılandırabilirsiniz.

### <a name="view-experiment"></a>Denemeyi görüntüle

Azure Machine Learning Studio deneme hesabınızı görüntülemek için:

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **denemeleri** düğümünü genişletin.
1. Görüntülemek istediğiniz denemeyi sağ tıklatın ve denemeyi **görüntüle**' yi seçin. 
1. Deneme URL 'sini Azure Machine Learning Studio 'da açmanızı isteyen bir istem görüntülenir. **Aç**’ı seçin.

### <a name="track-run-progress"></a>Çalıştırma ilerlemesini izleme

Deneme hesabınızı çalıştırdığınız için ilerleme durumunu görmek isteyebilirsiniz. Azure Machine Learning Studio 'da bir çalıştırmanın ilerlemesini, uzantısından izlemek için:

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **denemeleri** düğümünü genişletin.
1. İlerleme durumunu izlemek istediğiniz deneme düğümünü genişletin.
1. Çalıştır ' a sağ tıklayın ve **Azure Portal görünüm Çalıştır**' ı seçin.
1. Azure Machine Learning Studio 'da çalıştırma URL 'sini açmanızı isteyen bir istem belirir. **Aç**’ı seçin.

### <a name="download-run-logs--outputs"></a>Çalıştırma günlüklerini & çıktıları indirin

Çalıştırma tamamlandıktan sonra, bir deneme çalıştırmasının parçası olarak oluşturulan model gibi günlükleri ve varlıkları indirmek isteyebilirsiniz.

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **denemeleri** düğümünü genişletin.
1. İlerleme durumunu izlemek istediğiniz deneme düğümünü genişletin.
1. Çalıştırmaya sağ tıklayın:
    - Çıkışları indirmek için **çıktıları indir**' i seçin.
    - Günlükleri indirmek için **günlükleri indir**' i seçin.

### <a name="view-run-metadata"></a>Çalışma meta verilerini görüntüle

Uzantısında, çalıştırma için kullanılan çalıştırma yapılandırması gibi meta verileri ve çalıştırma ayrıntılarını inceleyebilirsiniz.

## <a name="compute-instances"></a>İşlem örnekleri

Daha fazla bilgi için bkz. [işlem örnekleri](concept-compute-instance.md).

### <a name="create-compute-instance"></a>İşlem örneği oluştur

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. İşlem örneğini oluşturmak istediğiniz çalışma alanı düğümünü genişletin.
1. **İşlem örnekleri** düğümüne sağ tıklayın ve **işlem örneği oluştur**' u seçin.
1. İsteminde:
    1. İşlem örneğiniz için bir ad sağlayın.
    1. Listeden bir VM boyutu seçin.
    1. SSH erişimini etkinleştirmek isteyip istemediğinizi seçin.
        1. SSH erişimini etkinleştirirseniz, ortak SSH anahtarını veya anahtarı içeren dosyayı da sağlamanız gerekir. Daha fazla bilgi için bkz. [Azure 'DA SSH anahtarları oluşturma ve kullanma kılavuzu](../virtual-machines/linux/mac-create-ssh-keys.md).

### <a name="stop-or-restart-compute-instance"></a>İşlem örneğini Durdur veya yeniden Başlat

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **işlem örneği** düğümünü genişletin.
1. Durdurmak veya yeniden başlatmak istediğiniz işlem örneğine sağ tıklayın ve sırasıyla **işlem örneğini durdur** veya **Işlem örneğini yeniden Başlat** ' ı seçin.

### <a name="view-compute-instance-configuration"></a>İşlem örneği yapılandırmasını görüntüle

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **işlem örneği** düğümünü genişletin.
1. İncelemek istediğiniz işlem örneğine sağ tıklayın ve **işlem örneği özelliklerini görüntüle**' yi seçin.

### <a name="delete-compute-instance"></a>İşlem örneğini Sil

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **işlem örneği** düğümünü genişletin.
1. Silmek istediğiniz işlem örneğine sağ tıklayın ve **işlem örneğini Sil**' i seçin.

## <a name="compute-clusters"></a>İşlem kümeleri

Uzantı aşağıdaki işlem türlerini destekler:

- Azure Machine Learning işlem kümesi
- Azure Kubernetes Service

Daha fazla bilgi için bkz. [işlem hedefleri](concept-compute-target.md#train).

### <a name="create-compute"></a>İşlem oluştur

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Altında işlem kümesi oluşturmak istediğiniz çalışma alanı düğümünü genişletin.
1. **İşlem kümeleri** düğümüne sağ tıklayın ve **işlem oluştur**' u seçin.
1. İsteminde:
    1. İşlem türü seçin
    1. Bir VM boyutu seçin. [VM boyutları](../virtual-machines/sizes.md)hakkında daha fazla bilgi edinin.
    1. İşlem için bir ad sağlayın.

### <a name="view-compute-configuration"></a>İşlem yapılandırmasını görüntüle

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **işlem kümeleri** düğümünü genişletin.
1. Görüntülemek istediğiniz işlem için sağ tıklayın ve **Işlem özelliklerini görüntüle**' yi seçin.

### <a name="edit-compute-scale-settings"></a>İşlem ölçek ayarlarını Düzenle

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **işlem kümeleri** düğümünü genişletin.
1. Düzenlemek istediğiniz işlem için sağ tıklayın ve **Işlem Düzenle**' yi seçin.
1. İşlem için bir yapılandırma dosyası düzenleyicide açılır. Yapılandırmanızı karşılıyoruz, **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.

### <a name="delete-compute"></a>İşlem silme

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **işlem kümeleri** düğümünü genişletin.
1. Silmek istediğiniz işlem için sağ tıklayın ve işlemi **Sil**' i seçin.

### <a name="create-run-configuration"></a>Çalıştırma yapılandırması oluştur

Uzantısında bir çalıştırma yapılandırması oluşturmak için:

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **işlem kümeleri** düğümünü genişletin.
1. Çalıştırma yapılandırmasını oluşturmak istediğiniz işlem hedefine sağ tıklayın ve **çalıştırma yapılandırması oluştur**' u seçin.
1. İsteminde:
    1. İşlem hedefi için bir ad sağlayın
    1. Yeni bir ortam seçin veya oluşturun.
    1. Çalıştırmak istediğiniz betiğin adını yazın veya yerel bilgisayarınızda betik için tarayıcıda **ENTER** tuşuna basın.
    1. Seçim Eğitim çalıştıralım için bir veri başvurusu oluşturmak isteyip istemediğinizi seçin. Bunun yapılması, çalışma yapılandırmanızda bir veri kümesi tanımlamanızı ister.
        1. Kayıt kümenizde bir yapılandırma dosyası olan çalışma kümenize bağlanmak için kayıt kümelerinden birini seçin düzenleyicide açılır. Yapılandırmanızı karşılıyoruz, **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.
    1. Yapılandırmanızı karşılıyoruz, **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.

### <a name="edit-run-configuration"></a>Çalıştırma yapılandırmasını düzenle

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın **işlem kümeleri** düğümündeki işlem kümesi düğümünü genişletin.
1. Düzenlemek istediğiniz çalışma yapılandırmasına sağ tıklayın ve **çalıştırma yapılandırmasını düzenle**' yi seçin.
1. Çalışma yapılandırmanız için bir yapılandırma dosyası düzenleyicide açılır. Yapılandırmanızı karşılıyoruz, **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.

### <a name="delete-run-configuration"></a>Çalıştırma yapılandırmasını sil

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanı düğümünü genişletin.
1. **İşlem kümeleri** düğümü içinde ilgilendiğiniz işlem kümesi düğümünü genişletin.
1. Düzenlemek istediğiniz çalışma yapılandırmasına sağ tıklayın ve **çalıştırma yapılandırmasını sil**' i seçin.

## <a name="models"></a>Modeller

Daha fazla bilgi için bkz. [modeller](concept-azure-machine-learning-architecture.md#models)

### <a name="register-model"></a>Modeli kaydetme

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanı düğümünü genişletin.
1. **Modeller** düğümüne sağ tıklayın ve **modeli Kaydet**' i seçin.
1. İsteminde:
    1. Modelinize bir ad verin
    1. Modelinizin bir dosya veya klasör olup olmadığını seçin.
    1. Yerel BILGISAYARıNıZDA modeli bulun.
    1. Düzenleyicide modelinize yönelik bir yapılandırma dosyası. Yapılandırmanızı karşılıyoruz, **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.

### <a name="view-model-properties"></a>Model özelliklerini görüntüle

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **modeller** düğümünü genişletin.
1. Özelliklerini görmek istediğiniz modele sağ tıklayın ve **model özelliklerini görüntüle**' yi seçin. Model özelliklerinizi içeren düzenleyicide bir dosya açılır.

### <a name="download-model"></a>Modeli indir

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **modeller** düğümünü genişletin.
1. İndirmek istediğiniz modele sağ tıklayın ve **model dosyasını indir**' i seçin.

### <a name="delete-a-model"></a>Modeli silme

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **modeller** düğümünü genişletin.
1. Silmek istediğiniz modele sağ tıklayın ve **modeli kaldır**' ı seçin.

## <a name="endpoints"></a>Uç Noktalar

VS Code uzantısı aşağıdaki dağıtım hedeflerini destekler:

- Azure Container Instances
- Azure Kubernetes Service

Daha fazla bilgi için bkz. [Web hizmeti uç noktaları](concept-azure-machine-learning-architecture.md#web-service-endpoint).

### <a name="create-deployments"></a>Dağıtım oluşturma

> [!NOTE]
> Dağıtım oluşturma şu anda yalnızca Conda ortamlarıyla birlikte çalışıyor.

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanı düğümünü genişletin.
1. **Uç noktalar** düğümüne sağ tıklayın ve **Hizmeti Dağıt**' ı seçin.
1. İsteminde:
    1. Zaten kayıtlı bir model veya yerel model dosyası kullanmak isteyip istemediğinizi seçin.
    1. Modelinizi seçin
    1. Modelinizi dağıtmak istediğiniz dağıtım hedefini seçin.
    1. Modelinize bir ad verin.
    1. Model Puanlama yaparken çalıştırılacak betiği sağlayın.
    1. Bir Conda Dependencies dosyası sağlayın.
    1. Dağıtımınız için bir yapılandırma dosyası düzenleyicide görüntülenir. Yapılandırmanızı karşılıyoruz, **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.

> [!NOTE]
> Alternatif olarak, *modeller* düğümünde kayıtlı bir modele sağ tıklayıp **hizmet dağıtımı kayıtlı modelden**' yi seçebilirsiniz.

### <a name="delete-deployments"></a>Dağıtımları silme

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **uç noktalar** düğümünü genişletin.
1. Kaldırmak istediğiniz dağıtıma sağ tıklayın ve **hizmeti Kaldır**' ı seçin.
1. Hizmeti kaldırmak istediğinizi onaylayan bir istem belirir. **Tamam**'ı seçin.

### <a name="manage-deployments"></a>Dağıtımları yönetme

Dağıtımları oluşturmaya ve silmeye ek olarak, dağıtımla ilişkili ayarları görüntüleyebilir ve düzenleyebilirsiniz.

1. Çalışma alanınızı içeren abonelik düğümünü genişletin.
1. Çalışma alanınızın içindeki **uç noktalar** düğümünü genişletin.
1. Yönetmek istediğiniz dağıtıma sağ tıklayın:
    - Ayarları düzenlemek için **hizmeti Düzenle**' yi seçin.
        - Dağıtımınız için bir yapılandırma dosyası düzenleyicide görüntülenir. Yapılandırmanızı karşılıyoruz, **Kaydet** ' i seçin veya vs Code komut paleti ' ni açın (**> komut paletini görüntüleyin**) ve **Azure ml: Kaydet ve devam et** yazın.
    - Dağıtım yapılandırma ayarlarını görüntülemek için **hizmet özelliklerini görüntüle**' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

VS Code uzantılı [bir görüntü sınıflandırma modeli eğitme](tutorial-train-deploy-image-classification-model-vscode.md) .