---
title: Azure Data Factory kullanarak birden çok kapsayıcıdan dosya kopyalama
description: Azure Data Factory kullanarak birden çok kapsayıcıdan dosya kopyalamak için bir çözüm şablonu kullanmayı öğrenin.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: 004a623f0dfe251da9d452b53c2541e53339d965
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684267"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Azure Data Factory ile birden çok kapsayıcıdan dosya kopyalama

Bu makalede, dosya depoları arasında birden çok kapsayıcıdan dosya kopyalamak için kullanabileceğiniz bir çözüm şablonu açıklanmaktadır. Örneğin, Data Lake ' ı AWS S3 'ten Azure Data Lake Store 'e geçirmek için kullanabilirsiniz. Veya, her şeyi bir Azure Blob depolama hesabından diğerine çoğaltmak için şablonu kullanabilirsiniz.

> [!NOTE]
> Tek bir kapsayıcıdan dosya kopyalamak istiyorsanız, tek bir kopyalama etkinliği ile bir işlem hattı oluşturmak için [veri kopyalama aracını](copy-data-tool.md) kullanmak daha etkilidir. Bu makaledeki şablon, bu basit senaryoya yönelik gereksiniminden daha fazla.

## <a name="about-this-solution-template"></a>Bu çözüm şablonu hakkında

Bu şablon, kaynak depolama deponuzdan kapsayıcıları sıralar. Daha sonra bu kapsayıcıları hedef depoya kopyalar.

Şablon üç etkinlik içerir:
- **GetMetadata** , kaynak depolama deponuzi tarar ve kapsayıcı listesini alır.
- **ForEach** , **GetMetadata** etkinliğinden kapsayıcı listesini alır ve sonra liste üzerinde dolaşır ve her kapsayıcıyı kopyalama etkinliğine geçirir.
- **Kopya** , her kapsayıcıyı kaynak depolama deposundan hedef depoya kopyalar.

Şablon iki parametre tanımlar:
- *SourceFilePath* , veri kaynağı deponuzın yoludur ve burada kapsayıcıların bir listesini alabilirsiniz. Çoğu durumda yol, birden çok kapsayıcı klasörü içeren kök dizindir. Bu parametrenin varsayılan değeri `/`.
- *Destinationfilepath* , dosyaların hedef deponuzda kopyalanacağı yoldur. Bu parametrenin varsayılan değeri `/`.

## <a name="how-to-use-this-solution-template"></a>Bu çözüm şablonunu kullanma

1. **Dosya depoları arasında birden çok dosya kapsayıcısı kopyalama** şablonu ' na gidin. Kaynak depolama deponuza **Yeni** bir bağlantı oluşturun. Kaynak depolama deposu, içindeki birden çok kapsayıcıdan dosyaları kopyalamak istediğiniz yerdir.

    ![Kaynağa yeni bir bağlantı oluşturun](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Hedef depolama deponuza **Yeni** bir bağlantı oluşturun.

    ![Hedefe yeni bir bağlantı oluşturun](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. **Bu şablonu kullan**' ı seçin.

    ![Bu şablonu kullan](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Aşağıdaki örnekte olduğu gibi ardışık düzeni görürsünüz:

    ![İşlem hattını gösterme](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. **Hata Ayıkla**' yı seçin, **parametreleri**girin ve ardından **son**' u seçin.

    ![İşlem hattını çalıştırma](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Sonucu gözden geçirin.

    ![Sonucu gözden geçirin](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory bir denetim tablosu kullanarak bir veritabanından toplu kopyalama](solution-template-bulk-copy-with-control-table.md)

- [Azure Data Factory ile birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md)