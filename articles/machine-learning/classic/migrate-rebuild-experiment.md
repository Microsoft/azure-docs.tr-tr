---
title: 'ML Studio (klasik): Azure Machine Learning geçir-yeniden oluşturma denemesi'
description: Azure Machine Learning tasarımcısında Studio (klasik) denemeleri yeniden derleyin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 35a68d1a2883d0631f90cd8009e272bca0c66278
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311847"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Azure Machine Learning bir Studio (klasik) denemesini yeniden oluşturma

Bu makalede, Azure Machine Learning ' de bir Studio (klasik) denemesini yeniden oluşturmayı öğreneceksiniz. Studio 'dan (klasik) geçiş hakkında daha fazla bilgi için bkz. [geçişe genel bakış makalesi](migrate-overview.md).

Studio (klasik) **denemeleri** **, Azure Machine Learning** işlem hatlarına benzerdir. Ancak Azure Machine Learning işlem hatları, SDK 'Yı destekleyen arka uca oluşturulmuştur. Bu, Machine Learning geliştirmesi için iki seçeneğe sahip olduğunuz anlamına gelir: sürükle ve bırak Tasarımcısı veya Code-First SDK 'Ları.

SDK ile işlem hatları oluşturma hakkında daha fazla bilgi için bkz. [Azure Machine Learning işlem hatları](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning çalışma alanı. [Azure Machine Learning çalışma alanı oluşturun](../how-to-manage-workspace.md#create-a-workspace).
- Bir Studio (klasik) geçirmeye geçmeyi deneyin.
- [Veri kümenizi](migrate-register-dataset.md) Azure Machine Learning 'ye yükleyin.

## <a name="rebuild-the-pipeline"></a>İşlem hattını yeniden derleme

[Veri kümenizi Azure Machine Learning 'e geçirdikten](migrate-register-dataset.md)sonra, denemenizin yeniden hazırlanmaya hazırsınız demektir.

Azure Machine Learning, görsel grafiğe **ardışık düzen taslağı** denir. Bu bölümde, klasik denemenizin bir işlem hattı taslağı olarak yeniden oluşturulması gerekir.

1. Azure Machine Learning Studio 'ya git ([ml.Azure.com](https://ml.azure.com))
1. Sol gezinti bölmesinde, **Tasarımcı** kullanımı > **kolay önceden oluşturulmuş modüller** ![ ekran görüntüsünü seçerek yeni bir ardışık düzen taslağı oluşturmayı gösterir.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Tasarımcı modülleriyle denemeniz el ile yeniden derleyin.
    
    Değiştirme modüllerini bulmak için [Modül eşleme tablosuna](migrate-overview.md#studio-classic-and-designer-module-mapping) başvurun. Birçok Studio (klasik) en popüler modül, tasarımcıda aynı sürüme sahip.

    > [!Important]
    > Denemeniz R betiği Yürüt modülünü kullanıyorsa, denemenizin geçirilmesi için ek adımlar gerçekleştirmeniz gerekir. Daha fazla bilgi için bkz. [R betik modüllerini geçirme](migrate-execute-r-script.md).

1. Parametreleri ayarlayın.
    
    Her modülü seçin ve modül Ayarları panelinde parametreleri sağa ayarlayın. Studio (klasik) denemenizin işlevlerini yeniden oluşturmak için parametreleri kullanın. Her modül hakkında daha fazla bilgi için bkz. [modül başvurusu](../algorithm-module-reference/module-reference.md).

## <a name="submit-a-run-and-check-results"></a>Çalıştırma ve denetim sonuçlarını gönder

Studio (klasik) denemesini yeniden oluşturduktan sonra bir işlem **hattı çalıştırması** gönderme zamanı.

İşlem hattı çalıştırması çalışma alanınıza bağlı bir **işlem hedefi** üzerinde yürütülür. Tüm işlem hattı için varsayılan bir işlem hedefi ayarlayabilir veya işlem hedeflerini modül başına temelinde belirtebilirsiniz.

Bir işlem hattı taslağından bir çalıştırma gönderdiğinizde, işlem **hattı çalıştırmasına** dönüşür. Her işlem hattı çalıştırması kaydedilir ve Azure Machine Learning kaydedilir.

Tüm işlem hattı için varsayılan bir işlem hedefi ayarlamak için:
1. İşlem  ![ hattı adının yanındaki tasarımcıda dişli simgesi dişli simgesini seçin ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) .
1. **İşlem hedefini seç**' i seçin.
1. Mevcut bir işlem seçin veya ekrandaki yönergeleri izleyerek yeni bir işlem oluşturun.

İşlem hedefi ayarlanmış olduğuna göre, bir işlem hattı çalıştırması gönderebilirsiniz:

1. Tuvalin üst kısmında **Gönder**' i seçin.
1. Yeni bir deneme oluşturmak için **Yeni oluştur** ' u seçin.
    
    Denemeleri benzer işlem hattını Düzenle birlikte çalışır. Birden çok kez işlem hattı çalıştırırsanız, art arda çalıştırmalar için aynı denemeyi seçebilirsiniz. Bu, günlüğe kaydetme ve izleme için kullanışlıdır.
1. Bir deneme adı girin. Sonra **Gönder**' i seçin.

    İlk çalıştırma 20 dakikaya kadar sürebilir. Varsayılan işlem ayarları en az 0 düğüm boyutuna sahip olduğundan, tasarımcı boşta kaldıktan sonra kaynakları ayırmalıdır. Düğümler zaten ayrıldığından, art arda yapılan çalıştırmalar daha az zaman alır. Çalışma süresini hızlandırmak için, en az 1 veya daha büyük düğüm boyutuyla bir işlem kaynakları oluşturabilirsiniz.

Çalıştırma bittikten sonra, her modülün sonucunu kontrol edebilirsiniz:

1. Çıktısını görmek istediğiniz modüle sağ tıklayın.
1. **Görselleştir**, **çıktıyı görüntüle** veya **günlüğü görüntüle** seçeneklerinden birini belirleyin.

    - **Görselleştirin**: sonuçlar veri kümesini önizleyin.
    - **Çıktıyı görüntüle**: çıkış depolama konumunun bağlantısını açın. Çıktıyı araştırmak veya indirmek için bunu kullanın. 
    - **Günlüğü görüntüle**: sürücü ve sistem günlüklerini görüntüleyin. Hatalar ve özel durumlar gibi kullanıcı tarafından gönderilen betikle ilgili bilgileri görmek için **70_driver_log** kullanın.

> [!IMPORTANT]
> Tasarımcı modülleri, Studio 'daki C# paketlerine kıyasla açık kaynaklı Python paketleri kullanır (klasik). Sonuç olarak, modül çıktısı tasarımcı ve stüdyo (klasik) arasında biraz farklılık gösterebilir. 


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Machine Learning ' de bir Studio (klasik) denemesini yeniden oluşturmayı öğrendiniz. Sonraki adım [Azure Machine Learning Web hizmetlerini yeniden derlemedir](migrate-rebuild-web-service.md).


Studio (klasik) geçiş serisinde diğer makalelere bakın:

1. [Geçişe genel bakış](migrate-overview.md).
1. [Veri kümesini geçirin](migrate-register-dataset.md).
1. **Bir Studio (klasik) eğitim işlem hattını yeniden derleyin**.
1. [Bir Studio (klasik) Web hizmetini yeniden derleyin](migrate-rebuild-web-service.md).
1. [İstemci uygulamalarıyla bir Azure Machine Learning Web hizmetini tümleştirin](migrate-rebuild-integrate-with-client-app.md).
1. [Execute R betiğini geçirin](migrate-execute-r-script.md).
