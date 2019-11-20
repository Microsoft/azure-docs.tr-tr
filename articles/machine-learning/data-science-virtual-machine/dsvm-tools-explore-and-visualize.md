---
title: Veri keşfi ve görselleştirme araçları
titleSuffix: Azure Data Science Virtual Machine
description: Veri Bilimi Sanal Makinesi için veri keşif ve görselleştirme araçları.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330694"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Azure Veri Bilimi Sanal Makinesi veri keşif ve görselleştirme araçları

Veri bilimi 'nde, anahtar verileri anlamaktır. Görselleştirme ve veri araştırma araçları, verilerin öğrenilmesini hızlandırmaya yardımcı olur. Veri Bilimi Sanal Makinesi (DSVM) üzerinde sunulan aşağıdaki araçlar, bu anahtar adımını daha kolay hale getirir.

## <a name="apache-drill"></a>Apache detaya gitme
|    |           |
| ------------- | ------------- |
| Nedir?   | Büyük verilerde açık kaynaklı SQL sorgu altyapısı    |
| Desteklenen DSVM sürümleri      | Windows, Linux  |
| DSVM 'de nasıl yapılandırılır ve yüklenir?      |  Yalnızca katıştırılmış modda `/dsvm/tools/drill*` ' a yüklendi   |
| Tipik kullanımlar      |  Ayıklama, dönüştürme, yükleme (ETL) gerekmeden yerinde veri araştırması için. CSV, JSON, ilişkisel tablolar ve Hadoop gibi farklı veri kaynaklarını ve biçimleri sorgulayın.     |
| Kullanma ve çalıştırma      | Masaüstü kısayolu  <br/> [10 dakika içinde detaya gitmeyi kullanmaya başlayın](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 'deki ilgili araçlar      |   Rattle, WEKA, SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Nedir?   | Etkileşimli veri görselleştirme ve BI aracı    |
| Desteklenen DSVM sürümleri      | Windows  |
| Tipik kullanımlar      |  Veri görselleştirme ve pano oluşturma   |
| Kullanma ve çalıştırma      | Masaüstü kısayolu (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`) veya yalnızca **Başlangıç** menüsünden çalıştırın.      |
| DSVM 'deki ilgili araçlar      |   Visual Studio 2019, Visual Studio Code, Juno      |

> [!NOTE]
> Power BI erişmek için bir Microsoft Office 365 hesabınızın olması gerekir.


## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Nedir?   |   R kullanarak veri madenciliği için grafik kullanıcı arabirimi (GUI)   |
| Desteklenen DSVM sürümleri     | Windows, Linux     |
| Tipik kullanımlar      | R için genel UI veri araştırma aracı    |
| Kullanma ve çalıştırma      | UI aracı. Windows üzerinde, bir komut istemi açın, R çalıştırın ve ardından R içinde `rattle()` ' ı çalıştırın. Linux 'ta X2Go ile bağlanın, bir Terminal başlatın, R çalıştırın ve ardından R içinde `rattle()` ' ı çalıştırın. |
| Örneklere bağlantılar      | [Rattle](https://togaware.com/onepager/) |
| DSVM 'deki ilgili araçlar      |LightGBM, WEKA, Xgboost   |


## <a name="weka"></a>WEKA
|    |           |
| ------------- | ------------- |
| Nedir?   |  Veri araştırma görevleri için makine öğrenimi algoritmalarının koleksiyonu. Bu algoritmalar doğrudan bir veri kümesine uygulanabilir veya kendi Java kodınızdan çağrılabilir. WEKA, veri ön işleme, sınıflandırma, gerileme, kümeleme, ilişkilendirme kuralları ve görselleştirme için araçlar içerir. |
| Desteklenen DSVM sürümleri     | Windows, Linux     |
| Tipik kullanımlar      | Genel makine öğrenme aracı     |
| Kullanma ve çalıştırma      | Windows 'ta, Başlat menüsünde WEKA araması yapın. Linux 'ta X2Go ile oturum açın ve ardından WEKA > Uygulama > Geliştirme ' ye gidin. |
| Örneklere bağlantılar      | [WEKA örnekleri](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| DSVM 'deki ilgili araçlar      |LightGBM, Rattle, Xgboost   |




