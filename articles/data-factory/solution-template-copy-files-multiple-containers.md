---
title: Birden çok kapsayıcıdan dosyaları kopyalama
description: Azure Data Factory kullanarak birden çok kapsayıcıdan dosya kopyalamak için bir çözüm şablonu kullanmayı öğrenin.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: ec7af1e81e0b295491420597636c8443f4d36512
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376097"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Birden çok klasörü Azure Data Factory kopyalama

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, her kopyalama etkinliğinin tek bir kapsayıcı veya klasörü kopyalaması beklenen dosya tabanlı mağazalar arasında kapsayıcılar veya klasörler kopyalamak için birden çok kopyalama etkinliği kullanabileceğiniz bir çözüm şablonu açıklanmaktadır. 

> [!NOTE]
> Tek bir kapsayıcıdan dosya kopyalamak istiyorsanız, tek bir kopyalama etkinliği ile bir işlem hattı oluşturmak için [veri kopyalama aracını](copy-data-tool.md) kullanmak daha etkilidir. Bu makaledeki şablon, bu basit senaryoya yönelik gereksiniminden daha fazla.

## <a name="about-this-solution-template"></a>Bu çözüm şablonu hakkında

Bu şablon, kaynak depolama deponuzda verilen bir üst klasörden klasörleri sıralar. Ardından, her bir klasörü hedef depoya kopyalar.

Şablon üç etkinlik içerir:
- **GetMetadata** , kaynak depolama deponuzi tarar ve belirtilen bir üst klasörden alt klasör listesini alır.
- **ForEach** , **GetMetadata** etkinliğinden alt klasör listesini alır ve sonra liste üzerinde dolaşır ve her bir klasörü kopyalama etkinliğine geçirir.
- **Kopya** , her bir klasörü kaynak depolama deposundan hedef depoya kopyalar.

Şablon aşağıdaki parametreleri tanımlar:
- *Sourcefilefolder* , veri kaynağı deponuzu ana klasör yolunun parçasıdır: *sourcefilefolder/sourcefiledirectory*, burada alt klasörlerin bir listesini alabilirsiniz. 
- *Sourcefiledirectory* , veri kaynağı deponuzu üst klasör yolundan bölümüdür: *sourcefilefolder/sourcefiledirectory*, alt klasörlerin bir listesini alabilir. 
- *Destinationfilefolder* , dosyaların hedef deponuza kopyalanacağı bir üst klasör yolu: *destinationfilefolder/destinationfiledirectory* bölümüdür. 
- *Destinationfiledirectory* , dosyaların hedef deponuza kopyalanacağı ana klasör yolunun bir parçasıdır: *destinationfilefolder/destinationfiledirectory* . 

Depolama depoları arasında kök klasörler altında birden çok kapsayıcıyı kopyalamak istiyorsanız, tüm dört parametreyi olarak girebilirsiniz */* . Bunu yaptığınızda, depolama depoları arasında her şeyi çoğaltacaksınız.

## <a name="how-to-use-this-solution-template"></a>Bu çözüm şablonunu kullanma

1. **Dosya depoları arasında birden çok dosya kapsayıcısı kopyalama** şablonu ' na gidin. Kaynak depolama deponuza **Yeni** bir bağlantı oluşturun. Kaynak depolama deposu, içindeki birden çok kapsayıcıdan dosyaları kopyalamak istediğiniz yerdir.

    ![Kaynağa yeni bir bağlantı oluşturun](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Hedef depolama deponuza **Yeni** bir bağlantı oluşturun.

    ![Hedefe yeni bir bağlantı oluşturun](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. **Bu şablonu kullan**' ı seçin.

    ![Bu şablonu kullan](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Aşağıdaki örnekte olduğu gibi ardışık düzeni görürsünüz:

    ![İşlem hattını gösterme](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. **Hata Ayıkla**' yı seçin, **parametreleri** girin ve ardından **son**' u seçin.

    ![İşlem hattını çalıştırma](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Sonucu gözden geçirin.

    ![Sonucu gözden geçirin](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory bir denetim tablosu kullanarak bir veritabanından toplu kopyalama](solution-template-bulk-copy-with-control-table.md)

- [Azure Data Factory ile birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md)
