---
title: Geliştirme araçları
titleSuffix: Azure Data Science Virtual Machine
description: Veri Bilimi Sanal Makinesi bulunan araçlar ve tümleşik geliştirme ortamları hakkında bilgi edinin.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 76a550e95de24bf65b9b6097dd332e535da5b1c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330727"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Azure Veri Bilimi Sanal Makinesi geliştirme araçları

Veri Bilimi Sanal Makinesi (DSVM), yüksek düzeyde üretken bir tümleşik geliştirme ortamında (IDE) çok sayıda popüler araç sağlar. DSVM 'de sunulan bazı araçlar aşağıda verilmiştir.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

|    |           |
| ------------- | ------------- |
| Nedir?   | Genel amaçlı IDE      |
| Desteklenen DSVM sürümleri      | Windows      |
| Tipik kullanımlar      | Yazılım geliştirme    |
| DSVM 'de nasıl yapılandırılır ve yüklenir?      | Veri bilimi Iş yükü (Python ve R araçları), Azure iş yükü (Hadoop, Data Lake), Node. js, SQL Server araçları, [Visual Studio Code için Azure Machine Learning](https://github.com/Microsoft/vs-tools-for-ai)    |
| Kullanma ve çalıştırma      | Masaüstü kısayolu (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`). Grafiksel olarak, masaüstü simgesini veya **Başlat** menüsünü kullanarak Visual Studio 'yu açın. Programlar için arama yapın (Windows logo tuşu + S) ve ardından **Visual Studio**. Buradan, Python, R ve Node. js gibi C#dillerde projeler oluşturabilirsiniz.   |
| DSVM 'deki ilgili araçlar      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Değerlendirme döneminizin süresinin dolduğunu belirten bir ileti alabilirsiniz. Microsoft hesabı kimlik bilgilerinizi girin. Ya da Visual Studio Community erişimi almak için yeni bir ücretsiz hesap oluşturun.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Nedir?   | Genel amaçlı IDE      |
| Desteklenen DSVM sürümleri      | Windows, Linux     |
| Tipik kullanımlar      | Kod Düzenleyicisi ve git tümleştirmesi   |
| Kullanma ve çalıştırma      | Linux 'ta masaüstü kısayolu (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) Windows, masaüstü kısayolu veya terminali (`code`)    |
| DSVM 'deki ilgili araçlar      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Masaüstü 

|    |           |
| ------------- | ------------- |
| Nedir?   | R için istemci IDE dili   |
| Desteklenen DSVM sürümleri      | Windows, Linux      |
| Tipik kullanımlar      |  R geliştirme     |
| Kullanma ve çalıştırma      | Linux 'ta masaüstü kısayolu (`C:\Program Files\RStudio\bin\rstudio.exe`) Windows, masaüstü kısayolu (`/usr/bin/rstudio`)      |
| DSVM 'deki ilgili araçlar      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio sunucusu 

|    |           |
| ------------- | ------------- |
| Nedir?   | R için istemci IDE dili   |
| Nedir?   | R için Web tabanlı IDE    |
| Desteklenen DSVM sürümleri      | Linux      |
| Tipik kullanımlar      |  R geliştirme     |
| Kullanma ve çalıştırma      | Hizmeti _systemctl rstudio-Server_' ı etkinleştirerek etkinleştirin ve ardından hizmeti _systemctl start rstudio-Server_ile başlatın. Ardından, http: \//ağınız-VM-ip: 8787 konumundaki RStudio sunucusunda oturum açın.       |
| DSVM 'deki ilgili araçlar      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Nedir?   | Julia için istemci IDE dili   |
| Desteklenen DSVM sürümleri      | Windows, Linux      |
| Tipik kullanımlar      |  Julia geliştirme     |
| Kullanma ve çalıştırma      | Linux 'ta masaüstü kısayolu (`C:\JuliaPro-0.5.1.1\Juno.bat`) Windows, masaüstü kısayolu (`/opt/JuliaPro-VERSION/Juno`)      |
| DSVM 'deki ilgili araçlar      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>PyCharm

|    |           |
| ------------- | ------------- |
| Nedir?   | Python için istemci IDE dili    |
| Desteklenen DSVM sürümleri      | Linux      |
| Tipik kullanımlar      |  Python geliştirme     |
| Kullanma ve çalıştırma      | Linux üzerinde masaüstü kısayolu (`/usr/bin/pycharm`)      |
| DSVM 'deki ilgili araçlar      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Nedir?   | Etkileşimli veri görselleştirme ve BI aracı    |
| Desteklenen DSVM sürümleri      | Windows  |
| Tipik kullanımlar      |  Veri görselleştirme ve pano oluşturma   |
| Kullanma ve çalıştırma      | Masaüstü kısayolu (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| DSVM 'deki ilgili araçlar      |   Visual Studio 2017, Visual Studio Code, Juno      |

