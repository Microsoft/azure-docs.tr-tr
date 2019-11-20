---
title: Coğrafi AI kullanma
titleSuffix: Azure Data Science Virtual Machine
description: Coğrafi AI veri bilimi sanal makinesi verilerini çözümleme ve Jeo-uzamsal verileri temel alan modelleri oluşturmak için kullanmayı öğrenin.
keywords: derin öğrenme yapay ZEKA, veri bilimi araçları, veri bilimi sanal makinesi, Jeo-uzamsal analiz
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278427"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Coğrafi yapay zeka veri bilimi sanal makinesi kullanma

Coğrafi AI veri bilimi sanal makinesi, Jeo-uzamsal bilgi kullanan yapay ZEKA uygulamaları için model derleme analiz için veri alma ve veri denetimi gerçekleştirmek için kullanın. Coğrafi AI Veri Bilimi VM'si hazırladıktan ve argıs hesabınızda argıs 'ye oturum açmış olduktan sonra, ArcGIS Desktop ve ArcGIS Online ile etkileşime başlayabilirsiniz. Ayrıca, Python arabirimlerinden ve coğrafi Veri Bilimi VM'si önceden yapılandırılmış bir R dil köprüden argıs 'ye erişebilirsiniz. Zengin yapay zeka uygulamaları oluşturmak için, coğrafi Veri Bilimi VM'si Machine Learning ve derin öğrenme çerçeveleri ve üzerinde kullanılabilen diğer veri bilimi yazılımlarıyla birleştirin.  


## <a name="configuration-details"></a>Yapılandırma ayrıntıları

Argıs ile arabirim için kullanılan bir Python kitaplığı olan [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), ' de bulunan ```c:\anaconda```veri bilimi VM'si genel kök Conda ortamına yüklenir.

- Bir komut isteminde Python çalıştırıyorsanız, Conda kök Python ortamında ```activate``` etkinleştirmek için komutunu çalıştırın.
- IDE veya Jupyter Not Defteri kullanıyorsanız, doğru Conda ortamında olduğunuzdan emin olmak için ortamı veya çekirdeği seçebilirsiniz.

R Köprüsü, ' de bulunan ```C:\Program Files\Microsoft\ML Server\R_SERVER```ana Microsoft Machine Learning Server tek başına örneğinde [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) adlı bir R kitaplığı olarak yüklenir. Visual Studio, rstudio ve Jupiter, bu r ortamını kullanmak üzere önceden yapılandırılmıştır ve ```arcgisbinding``` r kitaplığına erişim sahibi olur. 


## <a name="geo-ai-data-science-vm-samples"></a>Coğrafi AI veri bilimi VM örnekleri

Temel Veri Bilimi VM'si makine öğrenimi ve derin öğrenme çerçevesi tabanlı örneklere ek olarak, coğrafi AI Veri Bilimi VM'si bir parçası olarak bir jeo-uzamsal örnek kümesi de sağlanır. Bu örnekler, Jeo-uzamsal verileri ve argıs yazılımlarını kullanarak AI uygulamalarının geliştirilmesine hızlı bir başlangıç yapmanıza yardımcı olabilir:


1. [Python ile Jeo-uzamsal analizler ile çalışmaya](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)başlama: ArcGIS için Python arabirimi aracılığıyla Jeo uzamsal verilerle çalışmayı gösteren bir giriş örneği, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) kitaplığı tarafından sağlanır. Ayrıca, geleneksel makine öğrenimini Jeo-uzamsal verilerle birleştirmeyi ve sonra da bu sonuçları ArcGIS 'deki bir haritada görselleştirmeyi gösterir.

2. [R ile Jeo-uzamsal analizler ile çalışmaya](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)başlama: [Arcgisbinding](https://github.com/R-ArcGIS/r-bridge) kitaplığı tarafından sağlanan ArcGIS için R arabirimini kullanarak Jeo-uzamsal verilerle çalışmayı gösteren bir giriş örneği. 

3. [Piksel düzeyinde Land kullanım sınıflandırması](https://github.com/Azure/pixel_level_land_classification): Giriş olarak havadan oluşan bir görüntüyü kabul eden ve bir Land Kapla etiketi döndüren derin bir sinir ağ modelinin nasıl oluşturulacağını gösteren bir öğretici. Kara-kapak etiketlerinin örnekleri *, ve* *su*. Model görüntüdeki gibi her piksel etiketini döndürür. 


## <a name="next-steps"></a>Sonraki adımlar

Veri bilimi sanal makinesi kullanan ek örnekleri şuradan ulaşılabilir:

* [Örnekler](dsvm-samples-and-walkthroughs.md)