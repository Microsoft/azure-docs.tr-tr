---
title: Desteklenen diller
titleSuffix: Azure Data Science Virtual Machine
description: Desteklenen program dilleri ve ilgili Araçlar Veri Bilimi Sanal Makinesi önceden yüklenir.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 586cdd6dc06a7685f17c78fa4c4ea2f2ebf52f3d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802396"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Veri Bilimi Sanal Makinesi desteklenen diller 

Veri Bilimi Sanal Makinesi (DSVM), yapay zeka (AI) uygulamalarınızı oluşturmak için önceden oluşturulmuş birkaç dil ve geliştirme araçlarıyla birlikte gelir. Bazı önemli tabloları aşağıda verilmiştir.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

|    |           |
| ------------- | ------------- |
| Desteklenen dil sürümleri | Python 2,7 ve 3,6 |
| Desteklenen DSVM sürümleri      | Windows Server 2016     |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | İki genel `conda` ortamı oluşturulur: <br /> * @No__t-1 ' de bulunan `root` ortamı Python 3,6 ' dir. <br/> * @No__t-1 ' de bulunan `python2` ortamı Python 2,7 ' dir.       |
| Örneklere bağlantılar      | Python için örnek Jupyıter Not defterleri dahil edilmiştir.     |
| DSVM 'deki ilgili araçlar      | PySpark, R, Julia.      |

> [!NOTE]
> 2018 Mart ' den önce oluşturulan Windows Server 2016 ' in derlemeleri Python 3,5 ve Python 2,7 ' i içerir. Python 2,7, Conda **kök** ortamıdır ve **py35** , Python 3,5 ortamıdır.

### <a name="how-to-use-and-run-it"></a>Kullanma ve çalıştırma    

* Komut isteminden çalıştırın:

  Bir komut istemi açın ve çalıştırmak istediğiniz Python sürümüne bağlı olarak aşağıdaki yöntemlerden birini kullanın:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.6
    activate 
    python --version 
    ```
    
* IDE içinde kullanın:

  Visual Studio Community Edition 'da yüklü olan Visual Studio için Python Araçları (PTV) kullanın. Varsayılan olarak, PTV 'lerde otomatik olarak ayarlanan tek ortam Python 3,6 ' dir. 

    > [!NOTE]
    > Python 2,7 ' de PTV 'leri göstermek için PTV 'lerde özel bir ortam oluşturmanız gerekir. Visual Studio Community sürümünde bu ortam yolunu ayarlamak için **araçlar** -> **Python araçları** -> **Python ortamları** ' na gidin ve **+ özel**' i seçin. Sonra, konumu **c:\anaconda\envs\python2** olarak ayarlayın ve **Otomatik Algıla**seçeneğini belirleyin.

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Python 3,6 için bir Python _[Conda root]_ ve Python 2,7 için _Python [Conda env: python2]_ olarak çekirdek türünü ayarlayabilirsiniz.

* Python paketlerini yükler:

  DSVM 'deki varsayılan Python ortamları, tüm kullanıcılar tarafından okunabilen küresel ortamlardır. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri genel ortama yüklemek için `activate` komutunu yönetici olarak kullanarak kök veya python2 ortamına etkinleştirin. Ardından, paketleri yüklemek veya güncelleştirmek için `conda` veya `pip` gibi bir paket Yöneticisi kullanabilirsiniz.

## <a name="python-linux-edition"></a>Python (Linux sürümü)

|    |           |
| ------------- | ------------- |
| Desteklenen dil sürümleri | Python 2,7 ve 3,5 |
| Desteklenen DSVM sürümleri      | Linux   |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | İki genel `conda` ortamı oluşturulur: <br /> *  @ no__t-1 `/anaconda/` ' de bulunan bir ortam Python 2,7 ' dir. <br/> *  @ no__t-1 `/anaconda/envs/py35`, Python 3,5 ' dir.       |
| Örneklere bağlantılar      | Python için örnek Jupyıter Not defterleri dahil edilmiştir.     |
| DSVM 'deki ilgili araçlar      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Kullanma ve çalıştırma    

* Bir terminalde Çalıştır:

  Terminal ' i açın ve çalıştırmak istediğiniz Python sürümüne bağlı olarak aşağıdakilerden birini yapın:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* IDE içinde kullanın:

  Visual Studio Community Edition 'da yüklü olan Pyı düğmesini kullanın. 

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Python 3,5 ortamı için Python 2,7 ve **Python [Conda env: py35]** için çekirdek türünü **Python [Conda root]** olarak ayarlayabilirsiniz. 

* Python paketlerini yükler:

  DSVM 'deki varsayılan Python ortamları, tüm kullanıcılar tarafından okunabilen küresel ortamlardır. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri küresel ortama yüklemek için, `source activate` komutunu yönetici olarak veya sudo izinleri olan bir kullanıcı olarak kullanarak kök veya py35 ortamına etkinleştirin. Ardından, paketleri yüklemek veya güncelleştirmek için `conda` veya `pip` gibi bir paket Yöneticisi kullanabilirsiniz.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Desteklenen dil sürümleri | Microsoft R Open 3. x (CRAN-R ile% 100 uyumlu)<br /> Microsoft R Server 9. x geliştirici sürümü (bir kurumsal özellikli ölçeklenebilir R platformu)|
| Desteklenen DSVM sürümleri      | Linux, Windows     |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Örneklere bağlantılar      | R için örnek Jupyıter Not defterleri dahil edilmiştir.     |
| DSVM 'deki ilgili araçlar      | Parlak r, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Kullanma ve çalıştırma    

**Windows**:

* Komut isteminden çalıştırın:

  Bir komut istemi açın ve `R` yazın.

* IDE içinde kullanın:

  Visual Studio Community Edition veya RStudio 'da yüklü olan RTools for Visual Studio (RTVS) kullanın. Bunlar, başlangıç menüsünde veya masaüstü simgesi olarak kullanılabilir. 

* Jupyıter 'da kullanma

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Jupyıter R çekirdeğini (ırkernel) kullanmak için çekirdek türünü **R** olarak ayarlayabilirsiniz.

* R paketlerini yükler:

  R, tüm kullanıcılar tarafından okunabilen küresel bir ortamda DSVM 'ye yüklenir. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri genel ortama yüklemek için önceki yöntemlerden birini kullanarak R 'yi çalıştırın. Ardından, paketleri yüklemek veya güncelleştirmek için R Package Manager `install.packages()` ' ı çalıştırabilirsiniz.

**Linux**:

* Terminalde Çalıştır:

  Bir Terminal açın ve @no__t çalıştırın-0.  

* IDE içinde kullanın:

  Linux DSVM 'de yüklü olan RStudio 'yu kullanın.  

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Jupyıter R çekirdeğini (ırkernel) kullanmak için çekirdek türünü **R** olarak ayarlayabilirsiniz. 

* R paketlerini yükler:

  R, tüm kullanıcılar tarafından okunabilen küresel bir ortamda DSVM 'ye yüklenir. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri genel ortama yüklemek için önceki yöntemlerden birini kullanarak R 'yi çalıştırın. Ardından, paketleri yüklemek veya güncelleştirmek için R Package Manager `install.packages()` ' ı çalıştırabilirsiniz.


## <a name="julia"></a>J

|    |           |
| ------------- | ------------- |
| Desteklenen dil sürümleri | 0,6 |
| Desteklenen DSVM sürümleri      | Linux, Windows     |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Windows: @no__t yüklendi-0<br /> Linux: @no__t ' de yüklendi-0    |
| Örneklere bağlantılar      | Julia için örnek Jupyıter Not defterleri dahil edilmiştir.     |
| DSVM 'deki ilgili araçlar      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Kullanma ve çalıştırma    

**Windows**:

* Komut isteminden Çalıştır

  Bir komut istemi açın ve `julia` ' i çalıştırın.
* IDE içinde kullanın:

  DSVM 'de yüklü olan ve masaüstü kısayolu olarak kullanılabilen `Juno` kullanın.

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Çekirdek türünü **JULIA sürümü**olarak ayarlayabilirsiniz.

* Julia paketlerini yükler:

  Varsayılan Julia konumu, tüm kullanıcılar tarafından okunabilen küresel bir ortamdır. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri küresel ortama yüklemek için yukarıdaki yöntemlerden birini kullanarak Julia 'yı çalıştırın. Ardından, paketleri yüklemek veya güncelleştirmek için `Pkg.add()` gibi Julia Package Manager komutlarını çalıştırabilirsiniz.


**Linux**:
* Bir terminalde Çalıştır:

  Bir Terminal açın ve @no__t çalıştırın-0.
* IDE içinde kullanın:

  DSVM 'de yüklü olan ve **uygulama** menüsü kısayolu olarak kullanılabilir olan `Juno` ' ı kullanın.

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Çekirdek türünü **JULIA sürümü**olarak ayarlayabilirsiniz.

* Julia paketlerini yükler:

  Varsayılan Julia konumu, tüm kullanıcılar tarafından okunabilen küresel bir ortamdır. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri küresel ortama yüklemek için yukarıdaki yöntemlerden birini kullanarak Julia 'yı çalıştırın. Ardından, paketleri yüklemek veya güncelleştirmek için `Pkg.add()` gibi Julia Package Manager komutlarını çalıştırabilirsiniz.

## <a name="other-languages"></a>Diğer diller

**C#** : Windows 'da kullanılabilir ve Visual Studio Community Edition aracılığıyla veya `Developer Command Prompt for Visual Studio` ' de erişilebilir, burada `csc` komutunu çalıştırabilirsiniz.

**Java**: OpenJDK, dsvm 'Nin hem Linux hem de Windows sürümlerinde bulunur ve yolunda ayarlanır. Java kullanmak için, Windows 'da veya Linux 'taki bash kabuğu 'ndaki bir komut isteminde `javac` veya `java` komutunu yazın.

**Node. js**: node. js, dsvm 'Nin hem Linux hem de Windows sürümlerinde bulunur ve yolunda ayarlanır. Node. js ' ye erişmek için Windows 'daki veya Linux 'taki bash kabuğu 'ndaki bir komut isteminde `node` veya `npm` komutunu yazın. Windows 'da Node. js Araçları için Visual Studio uzantısı, Node. js uygulamanızı geliştirmek için bir grafik IDE sağlamak üzere yüklenmiştir.

**F#** : Windows 'da kullanılabilir ve Visual Studio Community Edition aracılığıyla veya `Developer Command Prompt for Visual Studio` ' de erişilebilir, burada `fsc` komutunu çalıştırabilirsiniz.
