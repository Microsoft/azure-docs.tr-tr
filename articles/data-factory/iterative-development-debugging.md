---
title: Azure Data Factory 'de yinelemeli geliştirme ve hata ayıklama
description: Azure portal Data Factory işlem hatlarını nasıl geliştirebileceğinizi ve hata ayıklacağınızı öğrenin.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 38e51e33ef487da8879132eb1ce9cf1e63a814e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672841"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Azure Data Factory ile yinelemeli geliştirme ve hata ayıklama

Azure Data Factory, Data Factory işlem hatlarını tekrarlayarak geliştirmenize ve hata ayıklamanıza olanak tanır.

Bu özelliğin sekiz dakikalık bir girişi ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Yinelemeli hata ayıklama özellikleri
Tek bir kod satırı yazmadan işlem hattı tuvalindeki **hata ayıklama** özelliğini kullanarak işlem hatları oluşturun ve test çalıştırmaları yapın.

![İşlem hattı tuvalindeki hata ayıklama özelliği](media/iterative-development-debugging/iterative-development-image1.png)

İşlem hattı tuvalinin **Çıkış** penceresinde, test çalıştırmalarının sonuçlarını görüntüleyin.

![İşlem hattı tuvalinin çıkış penceresi](media/iterative-development-debugging/iterative-development-image2.png)

Bir test çalıştırması başarılı olduktan sonra, işlem hattınızda daha fazla etkinlik ekleyin ve hata ayıklamaya yinelemeli bir şekilde devam edin. Ayrıca, devam ederken bir test çalıştırmasını **iptal** edebilirsiniz.

![Test çalıştırmasını iptal et](media/iterative-development-debugging/iterative-development-image3.png)

Test çalıştırmalarını gerçekleştirdiğinizde, **Hata Ayıkla**' yı seçmeden önce veri fabrikasında yaptığınız değişiklikleri yayımlamanız gerekmez. Bu özellik, Data Factory iş akışını güncelleştirmeden önce değişikliklerin beklendiği gibi çalıştığından emin olmak istediğiniz senaryolarda yararlıdır.

> [!IMPORTANT]
> **Hata ayıklamayı** seçtiğinizde işlem hattı çalıştırılır. Bu nedenle, örneğin, işlem hattı kopyalama etkinliği içeriyorsa, test çalıştırması verileri kaynaktan hedefe kopyalar. Sonuç olarak, hata ayıklama sırasında kopyalama etkinliklerinizde ve diğer etkinliklerinizde test klasörleri kullanmanızı öneririz. İşlem hattının hata ayıkladıktan sonra, normal işlemlerde kullanmak istediğiniz gerçek klasörlere geçin.

## <a name="visualizing-debug-runs"></a>Hata ayıklama çalıştırmaları görselleştiriliyor

Veri fabrikanızın sürmekte olan tüm hata ayıklama çalıştırmalarını tek bir yerde görselleştirebilirsiniz. Sayfanın sağ üst köşesindeki **hata ayıklama çalıştırmalarını görüntüle** ' yi seçin. Bu özellik, alt işlem hatları için hata ayıklama çalıştırmalarının ana işlem hatlarından oluşan senaryolarda ve tüm etkin hata ayıklama çalıştırmalarını görmek için tek bir görünüm istediğinizde yararlıdır.

![Etkin hata ayıklama çalıştırmalarını görüntüle simgesini seçin](media/iterative-development-debugging/view-debug-runs-image1.png)

![Etkin hata ayıklama çalıştırmalarının örnek listesi](media/iterative-development-debugging/view-debug-runs-image2.png)

Etkin veri akışı hata ayıklama oturumlarınız varsa, Bu oturumlar etkin hata ayıklama penceresinin alt kısmında görünür. Etkin bir veri akışı oturumu seçebilir ve ilgili kümeyi durdurabilirsiniz.

![Etkin veri akışı hata ayıklama çalıştırmalarının örnek listesi](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Hata ayıklama çalıştırmalarını izleme

**Hata ayıklama** özelliğiyle başlatılan test çalıştırmaları, **izleyici** sekmesindeki listede kullanılamaz. Yalnızca **tetikleyici sekmesinde Tetikle** **,** **zamanlamaya**veya atlayan **pencere** tetikleyicilerle tetiklenen çalıştırmaları görebilirsiniz. İşlem hattı tuvalinin **Çıkış** penceresinde **hata ayıklama** özelliğiyle başlatılan son test çalıştırmasını görebilirsiniz.

## <a name="setting-breakpoints-for-debugging"></a>Hata ayıklama için kesme noktaları ayarlanıyor

Data Factory, işlem hattı tuvalindeki belirli bir etkinliğe ulaşana kadar hata ayıklamanıza da olanak tanır. Test etmek istediğiniz kadar etkinliğe bir kesme noktası koymanız ve **Hata Ayıkla**' yı seçmeniz yeterlidir. Data Factory, testin yalnızca işlem hattı tuvalindeki kesme noktası etkinliğine kadar çalışmasını sağlar. İşlem hattının tamamını test etmek istemediğiniz durumlarda bu *hata ayıklama* işlemi, yalnızca işlem hattının içindeki etkinliklerin bir alt kümesi yararlı olur.

![İşlem hattı tuvalindeki kesme noktaları](media/iterative-development-debugging/iterative-development-image4.png)

Kesme noktası ayarlamak için işlem hattı tuvalinde bir öğe seçin. Öğenin sağ üst köşesinde boş bir kırmızı daire olarak bir *hata ayıklama* seçeneği belirir.

![Seçili öğede bir kesme noktası ayarlamadan önce](media/iterative-development-debugging/iterative-development-image5.png)

*Hata ayıklamayı Until* seçeneğini belirledikten sonra, kesme noktasının etkin olduğunu göstermek için dolgulu kırmızı bir daireye dönüşür.

![Seçili öğe üzerinde bir kesme noktası ayarladıktan sonra](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Sonraki adımlar
[Azure Data Factory 'de sürekli tümleştirme ve dağıtım](continuous-integration-deployment.md)
