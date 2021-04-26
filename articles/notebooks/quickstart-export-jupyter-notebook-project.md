---
title: Microsoft teklifleriyle Jupyter Notebook kullanma
description: Jupi Not defterlerinin Microsoft teklifleriyle nasıl kullanılabileceği hakkında bilgi edinin.
ms.topic: quickstart
ms.date: 02/01/2021
ms.openlocfilehash: 5679c28d9cc8a4f1893ffad72002b66ad59861e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507386"
---
# <a name="use-a-jupyter-notebook-with-microsoft-offerings"></a>Microsoft teklifleriyle Jupyter Notebook kullanma

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Bu hızlı başlangıçta, Microsoft tekliflerde kullanılmak üzere bir Jupyter Notebook içeri aktarmayı öğreneceksiniz. 

Mevcut jupi Not defterleriniz varsa veya yeni bir proje başlatmak istiyorsanız, bunları Microsoft 'un birçok sunumundan kullanabilirsiniz. Aşağıdaki bölümlerde açıklanan bazı seçenekler şunlardır: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [GitHub Codespaces](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Not defterleri için ortam oluşturma

Kullanımdan kaldırılan Azure Notebooks önizlemenin ile eşleşen bir ortam oluşturmak istiyorsanız, GitHub 'da belirtilen betik dosyasını kullanabilirsiniz.

1. Azure Notebooks [GitHub deposuna](https://github.com/microsoft/AzureNotebooks) gidin veya [ortam klasörüne doğrudan erişin](https://aka.ms/aznbrequirementstxt).
1. Bir komut isteminden, projeleriniz için kullanmak istediğiniz dizine gidin.
1. Ortam klasörü içeriğini indirin ve BENIOKU yönergelerini izleyerek Azure Notebooks paketi bağımlılıklarını yükleyin.


## <a name="use-notebooks-in-visual-studio-code"></a>Visual Studio Code içindeki not defterlerini kullanma

[Vs Code](https://code.visualstudio.com/) , yerel olarak kullanabileceğiniz veya uzaktan işlem ile bağlantılı kullanabileceğiniz ücretsiz bir kod düzenleyicisidir. Python uzantısıyla birlikte kullanıldığında, jupi Not defterleri ile çalışmaya yönelik zengin bir yerel deneyim dahil olmak üzere Python geliştirmesi için tam bir ortam sunar. 

![VS Code Jupyter Notebook desteği](media/vs-code-jupyter-notebook.png)

Proje dosyalarınız varsa veya yeni bir not defteri oluşturmak istiyorsanız VS Code kullanabilirsiniz! Jupi Not defterleri ile VS Code kullanma hakkında rehberlik için, Visual Studio Code öğreticilerde bulunan [Visual Studio Code jupi Notebook](https://code.visualstudio.com/docs/python/jupyter-support) ve [veri bilimi](https://code.visualstudio.com/docs/python/data-science-tutorial) ile çalışma bölümüne bakın.

Azure Notebooks önizlemesiyle eşleşen bir ortam oluşturmak için Visual Studio Code ile [Azure Notebooks ortam betiğini](#create-an-environment-for-notebooks) de kullanabilirsiniz.

## <a name="use-notebooks-in-github-codespaces"></a>GitHub Codespaces içindeki not defterlerini kullanma

GitHub Codespaces, Visual Studio Code veya Web tarayıcınızda not defterlerinizi düzenleyebileceğiniz bulutta barındırılan ortamlar sağlar. Aynı harika Jupde deneyimi VS Code, ancak cihazınıza her şeyi yüklemeye gerek kalmadan sunar. Yerel bir ortam ayarlamak ve bulutta desteklenen bir çözümü tercih etmek istemiyorsanız, codespace oluşturmak harika bir seçenektir. Başlamak için:
1. Seçim GitHub Codespaces ile kullanmak istediğiniz proje dosyalarını toplayın.
1. Not defterlerinizi depolamak için [bir GitHub deposu oluşturun](https://help.github.com/github/getting-started-with-github/create-a-repo) .   
1. [Dosyalarınızı depoya ekleyin](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) .
1. [GitHub Codespaces önizlemesine erişim isteme](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Azure Machine Learning ile not defterlerini kullanma

Azure Machine Learning, kullanıcıların Azure 'da modelleri daha hızlı oluşturup dağıtmasını sağlayan uçtan uca bir makine öğrenimi platformu sağlar. Azure ML, Jupyıter not defterlerini bir VM 'de veya paylaşılan küme bilgi işlem ortamında çalıştırmanıza olanak sağlar. Deneme izleme, veri kümesi Yönetimi ve daha fazlasını içeren ML iş yükünüz için bulut tabanlı bir çözüme ihtiyacınız varsa Azure Machine Learning önerilir. Azure ML 'yi kullanmaya başlamak için:

1. Seçim Azure ML ile kullanmak istediğiniz proje dosyalarını toplayın.
1. Azure portal [bir çalışma alanı oluşturun](../machine-learning/how-to-manage-workspace.md) .

   ![Çalışma Alanı oluşturma](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. [Azure Studio 'yu (Önizleme)](https://ml.azure.com/)açın.
1. Sol taraftaki Gezinti çubuğunu kullanarak **Not defterleri**' ni seçin.
1. **Dosyaları karşıya yükle** düğmesine tıklayın ve proje dosyalarını karşıya yükleyin.

Azure ML ve Jupimter not defterlerini çalıştırma hakkında ek bilgi için [belgeleri](../machine-learning/how-to-run-jupyter-notebooks.md) gözden geçirebilir veya Microsoft Learn [Machine Learning modüle yönelik girişi](/learn/modules/intro-to-azure-machine-learning-service/) deneyebilirsiniz.


## <a name="use-azure-lab-services"></a>Azure Lab Services kullan

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) , eğitimciler 'in tamamında kolayca kurulum ve önceden yapılandırılmış VM 'lere isteğe bağlı erişim sağlamasına izin verir. Özel bir sınıf ortamında Jupyıter Not defterleri ve bulut işlemi ile çalışmanın bir yolunu arıyorsanız, Laboratuvar Hizmetleri harika bir seçenektir.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

Mevcut proje dosyalarınız varsa veya yeni bir not defteri oluşturmak istiyorsanız Azure Lab Services kullanabilirsiniz. Laboratuvar ayarlamayla ilgili rehberlik için bkz. [Python ve jupi Not defterleri ile veri bilimi öğretmek için laboratuvar ayarlama](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>GitHub 'ı kullanma

GitHub, not defterlerini (ve diğer dosyaları) depolamak, not defterlerinizi başkalarıyla paylaşmak ve işbirliği yapmak için ücretsiz, kaynak denetimi ile desteklenen bir yol sağlar. Projelerinizi paylaşmanın ve başkalarıyla işbirliği yapmaya yönelik bir yol arıyorsanız, GitHub harika bir seçenektir ve harika bir geliştirme deneyimi için [GitHub Codespaces](#use-notebooks-in-github-codespaces) ile birleştirilebilir. GitHub 'ı kullanmaya başlamak için

1. Seçim GitHub ile kullanmak istediğiniz proje dosyalarını toplayın.
1. Not defterlerinizi depolamak için [bir GitHub deposu oluşturun](https://help.github.com/github/getting-started-with-github/create-a-repo) . 
1. [Dosyalarınızı depoya ekleyin](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) .

## <a name="next-steps"></a>Sonraki adımlar

- [Visual Studio Code 'de Python hakkında bilgi edinin](https://code.visualstudio.com/docs/python/python-tutorial)
- [Azure Machine Learning ve Jupyıter not defterlerini öğrenin](../machine-learning/how-to-run-jupyter-notebooks.md)
- [GitHub Codespaces hakkında bilgi edinin](https://github.com/features/codespaces)
- [Azure Lab Services hakkında bilgi edinin](https://azure.microsoft.com/services/lab-services/)
- [GitHub hakkında bilgi edinin](https://help.github.com/github/getting-started-with-github/)
