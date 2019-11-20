---
title: R ile denemenizi genişletme
titleSuffix: ML Studio (classic) - Azure
description: R dil aracılığıyla Azure Machine Learning Studio (klasik) işlevinin işlevselliğini genişletme komut dosyası modülünü kullanma.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 13d385c69151891030ea0ee8f87ea8b55197a73f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837598"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klasik): deneme 'nizi R ile genişletin 
Azure Machine Learning Studio (klasik) işlevlerini R dili ile, [Yürüt komut dosyası][execute-r-script] modülünü kullanarak genişletebilirsiniz.

Bu modül birden çok giriş veri kümesini kabul eder ve çıkış olarak tek bir veri kümesi verir. R betiği [Yürüt][execute-r-script] modülünün **r betiği** parametresine bir r betiği yazabilirsiniz.

Modülün her giriş bağlantı noktasına şuna benzer bir kod kullanarak erişirsiniz:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Şu anda yüklü olan tüm paketleri listeleme
Yüklü paketlerin listesi değişebilir. Şu anda yüklü olan paketlerin listesi, [Azure Machine Learning Studio (klasik) tarafından desteklenen R paketlerinde](https://msdn.microsoft.com/library/azure/mt741980.aspx)bulunabilir.

Ayrıca, aşağıdaki kodu [Execute R betik][execute-r-script] modülüne girerek yüklü paketlerin tüm ve geçerli listesini alabilirsiniz:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Bu, paket listesini [Execute R betik][execute-r-script] modülünün çıkış bağlantı noktasına gönderir.
Paket listesini görüntülemek için, [CSV 'ye Dönüştür][convert-to-csv] gibi bir dönüştürme modülünü [R betiği Yürüt][execute-r-script] modülünün sol çıktısına bağlayın, denemeyi çalıştırın, ardından dönüştürme modülünün çıktısına tıklayın ve **İndir**' i seçin. 

!["CSV 'ye Dönüştür" modülüne ait çıktıyı indirin](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Paketler içeri aktarılıyor
Aşağıdaki komutları kullanarak, zaten yüklü olmayan paketleri [R betiği Yürüt][execute-r-script] modülünde içeri aktarabilirsiniz:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

`my_favorite_package.zip` dosyası paketinizi içerir.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
