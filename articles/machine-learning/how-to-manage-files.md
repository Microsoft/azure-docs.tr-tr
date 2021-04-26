---
title: Çalışma alanınızdaki dosyaları oluşturun ve yönetin
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio 'da çalışma alanınızda dosya oluşturma ve yönetme hakkında bilgi edinin.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 5bfa7d83c00386ae922f0eba221ad9f2f74639ae
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066152"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Çalışma alanınızda dosya oluşturma ve yönetme

Azure Machine Learning çalışma alanınızda dosyaları oluşturmayı ve yönetmeyi öğrenin.  Bu dosyalar varsayılan çalışma alanı depolama alanında depolanır. Dosya ve klasörler, çalışma alanına okuma erişimi olan diğer kişilerle paylaşılabilir ve çalışma alanındaki herhangi bir işlem örneklerinden kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://aka.ms/AMLFree) oluşturun.
* Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Dosya oluştur

Varsayılan klasörünüzde () yeni bir dosya oluşturmak için `Users > yourname` :

1. Çalışma alanınızı [Azure Machine Learning Studio](https://ml.azure.com)'da açın.
1. Sol tarafta, **Not defterleri**' ni seçin.
1. Görüntüyü seçin **+** .
1. **Yeni dosya oluştur** görüntüsünü seçin.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Yeni dosya oluştur":::

1. Dosyayı adlandırın.
1. Bir dosya türü seçin.
1. **Oluştur**’u seçin.

Not defterleri ve çoğu metin dosya türleri Önizleme bölümünde görüntülenir.  Çoğu dosya türünün önizleme yoktur.

Farklı bir klasörde yeni bir dosya oluşturmak için:
1. "..." Öğesini seçin klasörün sonunda
1. **Yeni dosya oluştur**' u seçin.

> [!IMPORTANT]
> Not defterlerinde ve betiklerde bulunan içerikler, oturumlarınızdan verileri okuyabilir ve Azure 'da kuruluşunuz olmadan verilere erişebilir.  Yalnızca güvenilir kaynaklardan gelen dosyaları yükleyin. Daha fazla bilgi için bkz. [güvenli kod en iyi uygulamaları](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

## <a name="manage-files-with-git"></a>Git ile dosyaları yönetme

Git depoları kopyalamak ve yönetmek için [bir işlem örneği terminali kullanın](how-to-access-terminal.md#git) .

## <a name="clone-samples"></a>Örnek kopyalama

Çalışma alanınız, SDK 'Yı keşfetmenize ve kendi makine öğrenimi projeleriniz için örnek olarak size yardımcı olmak üzere tasarlanan Not defterleri içeren **örnek bir not defteri** klasörü içerir.   Bu not defterlerini çalıştırmak ve düzenlemek için kendi klasörünüze dönüştürün.  

Bir örnek için bkz. [öğretici: Ilk ml denemenize oluşturma](tutorial-1st-experiment-sdk-setup.md#azure).

## <a name="share-files"></a>Dosyaları paylaşma

Bir dosyayı paylaşmak için URL 'YI kopyalayıp yapıştırın.  Bu URL 'ye yalnızca çalışma alanının diğer kullanıcıları erişebilir.  [Çalışma alanınıza erişim verme](how-to-assign-roles.md)hakkında daha fazla bilgi edinin.

## <a name="delete-a-file"></a>Dosyayı silme

**Örnek Not defteri** *dosyalarını silemezsiniz.*  Bu dosyalar, Studio 'nun bir parçasıdır ve her yeni SDK yayımlandığında güncellenir.  

**Dosyalarınız** bölümünde bulunan dosyaları şu *yollarla silebilirsiniz:*

* Studio 'da, bir klasörün veya dosyanın sonundaki **...** öğesini seçin.  Desteklenen bir tarayıcı (Microsoft Edge, Chrome veya Firefox) kullandığınızdan emin olun.
* Çalışma alanınızdaki herhangi bir işlem örneğinden [bir Terminal kullanın](how-to-access-terminal.md) . **~/CloudFiles** klasörü, çalışma alanı Depolama hesabınızdaki depolama alanı ile eşlendi.
* Ya da bu araçların bulunduğu jupi veya Jupyıterlab.
