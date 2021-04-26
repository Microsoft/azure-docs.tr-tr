---
title: Visual Studio Code (Önizleme) içinde işlem örneğine bağlanma
titleSuffix: Azure Machine Learning
description: Etkileşimli Jupyter Notebook ve Uzaktan geliştirme iş yüklerini çalıştırmak için Visual Studio Code Azure Machine Learning bir işlem örneğine nasıl bağlanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028599"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Visual Studio Code bir Azure Machine Learning işlem örneğine bağlanma (Önizleme)

Bu makalede, Visual Studio Code kullanarak Azure Machine Learning bir işlem örneğine nasıl bağlanacağınızı öğreneceksiniz.

[Azure Machine Learning Işlem örneği](concept-compute-instance.md) , veri bilimcileri için tam olarak yönetilen bulut tabanlı bir iş istasyonudur ve BT yöneticileri için yönetim ve kurumsal hazırlık özellikleri sağlar.

Visual Studio Code bir işlem örneğine bağlanmak için kullanabileceğiniz iki yol vardır:

* Uzaktan işlem örneği. Bu seçenek, Machine Learning projelerinizi oluşturmaya yönelik tam özellikli bir geliştirme ortamı sağlar.
* Uzak Jupyter Notebook sunucusu. Bu seçenek, bir işlem örneğini uzak Jupyter Notebook sunucusu olarak ayarlamanıza olanak tanır.

## <a name="configure-a-remote-compute-instance"></a>Uzak işlem örneği yapılandırma

Geliştirme için bir uzak işlem örneği yapılandırmak üzere birkaç önkoşul olması gerekir.

* Azure Machine Learning Visual Studio Code uzantısı. Daha fazla bilgi için [Azure Machine Learning Visual Studio Code uzantısı kurulum kılavuzuna](tutorial-setup-vscode-extension.md)bakın.
* Azure Machine Learning çalışma alanı. Henüz bir tane yoksa [Yeni bir çalışma alanı oluşturmak için Azure Machine Learning Visual Studio Code uzantısını kullanın](how-to-manage-resources-vscode.md#create-a-workspace) .
* İşlem örneğini Azure Machine Learning. [Azure Machine Learning Visual Studio Code uzantısını kullanarak, yoksa yeni bir işlem örneği oluşturun](how-to-manage-resources-vscode.md#create-compute-instance) .

Uzaktan işlem örneğinize bağlanmak için:

# <a name="vs-code"></a>[VS Code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure Machine Learning uzantısı

1. VS Code, Azure Machine Learning uzantısını başlatın.
1. Uzantınızın **işlem örnekleri** düğümünü genişletin.
1. Bağlanmak istediğiniz işlem örneğine sağ tıklayın ve **Işlem örneğine Bağlan**' ı seçin.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Azure ML uzantısı Visual Studio Code işlem örneğine bağlanma" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Komut paleti

1. VS Code ' de, komut paleti ' ni **görüntüle > komut paleti**' ni seçerek açın.
1. **Azure ml: Işlem örneğine Bağlan** metin kutusuna girin.
1. Aboneliğinizi seçin.
1. Çalışma alanınızı seçin.
1. İşlem örneğinizi seçin veya yeni bir tane oluşturun.

# <a name="studio"></a>[Studio](#tab/studio)

[Ml.Azure.com](https://ml.azure.com) adresine gidin

> [!IMPORTANT]
> Visual Studio Code uzak işlem örneğinize bağlanmak için, Azure Machine Learning Studio 'da oturum açtığınız hesabın Visual Studio Code kullandığınız bir aynı olduğundan emin olun.

### <a name="compute"></a>İşlem

1. **İşlem** sekmesini seçin
1. *Uygulama URI 'si* sütununda, bağlanmak istediğiniz işlem örneği için **vs Code** seçin.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Azure ML Studio VS Code Işlem örneğine bağlanma" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Not Defteri

1. **Not defteri** sekmesini seçin
1. *Not defteri* sekmesinde, düzenlemek istediğiniz dosyayı seçin.
1. **Düzenleyicilerde düzenle > vs Code (Önizleme)** seçeneğini belirleyin.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Azure ML Not defteri VS Code Işlem örneğine bağlanma" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

Uzaktan işlem örneğiniz için yeni bir pencere başlatılır. Uzak bir işlem örneğiyle bağlantı kurmaya çalışırken, aşağıdaki görevler gerçekleşirken:

1. Yetkilendirme. Bir bağlantı kurmaya çalışan kullanıcının işlem örneğini kullanma yetkisine sahip olduğundan emin olmak için bazı denetimler gerçekleştirilir.
1. VS Code uzak sunucu, işlem örneği üzerine yüklenir.
1. Gerçek zamanlı etkileşim için bir WebSocket bağlantısı oluşturulur.

Bağlantı kurulduktan sonra kalıcı hale gelir. İşlem örneğinizle bağlantıyı sürdürmek üzere otomatik olarak yenilenen oturum başlangıcında bir belirteç verilir.

Uzaktan işlem örneğinize bağlandıktan sonra düzenleyiciyi şu şekilde kullanın:

* [Uzaktan işlem örneğiniz veya dosya paylaşımınızda dosyaları yazın ve yönetin](https://code.visualstudio.com/docs/editor/codebasics).
* [Uzaktan işlem örneğiniz üzerinde komut ve uygulama çalıştırmak](how-to-access-terminal.md)için [vs Code tümleşik Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) ' i kullanın.
* [Betiklerinizde ve uygulamalarınızda hata ayıklayın](https://code.visualstudio.com/Docs/editor/debugging)
* [Git depolarınızı yönetmek için VS Code kullanın](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>İşlem örneğini uzak Not defteri sunucusu olarak yapılandırma

Bir işlem örneğini uzak bir Jupyter Notebook sunucusu olarak yapılandırmak için birkaç önkoşul olması gerekir:

* Azure Machine Learning Visual Studio Code uzantısı. Daha fazla bilgi için [Azure Machine Learning Visual Studio Code uzantısı kurulum kılavuzuna](tutorial-setup-vscode-extension.md)bakın.
* Azure Machine Learning çalışma alanı. Henüz bir tane yoksa [Yeni bir çalışma alanı oluşturmak için Azure Machine Learning Visual Studio Code uzantısını kullanın](how-to-manage-resources-vscode.md#create-a-workspace) .

Bir işlem örneğine bağlanmak için:

1. Visual Studio Code bir Jupyter Notebook açın.
1. Tümleşik Not Defteri Deneyimi yüklendiğinde **Jupyter sunucusu**' nu seçin.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning uzak Jupyter Notebook sunucusu açılır menüsünü Başlat](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Alternatif olarak, komut paletini de kullanabilirsiniz:

    1. Menü çubuğundan **görüntüle > komut paleti** ' ni seçerek komut paleti ' ni açın.
    1. Metin kutusuna girin `Azure ML: Connect to Compute instance Jupyter server` .

1. `Azure ML Compute Instances`Jupyıter sunucu seçenekleri listesinden seçin.
1. Abonelikler listesinden aboneliğinizi seçin. Varsayılan Azure Machine Learning çalışma alanınızı daha önce yapılandırdıysanız, bu adım atlanır.
1. Çalışma alanınızı seçin.
1. Listeden işlem örneğinizi seçin. Bir tane yoksa, **yeni Azure ML işlem örneği oluştur** ' u seçin ve komut istemlerini izleyerek bir tane oluşturun.
1. Değişikliklerin etkili olabilmesi için Visual Studio Code yeniden yüklemeniz gerekir.
1. Bir Jupyter Notebook açın ve bir hücre çalıştırın.

> [!IMPORTANT]
> Bağlantıyı kurmak için bir hücre çalıştırmanız **gerekir** .

Bu noktada, Jupyter Notebook hücreleri çalıştırmaya devam edebilirsiniz.

> [!TIP]
> Ayrıca, Jupyıter benzeri kod hücrelerini içeren Python betik dosyalarıyla (. Kopyala) çalışabilirsiniz. Daha fazla bilgi için bkz. [Python etkileşimli belgelerine Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="next-steps"></a>Sonraki adımlar

Visual Studio Code uzak olarak ayarladığınıza göre, [kodunuzda etkileşimli olarak hata ayıklamak](how-to-debug-visual-studio-code.md)için Visual Studio Code uzaktan işlem olarak bir işlem örneği kullanabilirsiniz.

[Öğretici: Ilk ml modelinize eğitme](tutorial-1st-experiment-sdk-train.md) bir işlem örneğinin tümleşik bir not defteriyle nasıl kullanılacağını gösterir.
