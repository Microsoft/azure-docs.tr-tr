---
title: Desteklenen diller
titleSuffix: Azure Data Science Virtual Machine
description: Desteklenen program dilleri ve ilgili Araçlar Veri Bilimi Sanal Makinesi önceden yüklenir.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100516439"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Veri Bilimi Sanal Makinesi desteklenen diller 

Veri Bilimi Sanal Makinesi (DSVM), yapay zeka (AI) uygulamalarınızı oluşturmak için önceden oluşturulmuş birkaç dil ve geliştirme araçlarıyla birlikte gelir. Bazı önemli tabloları aşağıda verilmiştir.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen dil sürümleri | Python 2,7 ve 3,7 |
| Desteklenen DSVM sürümleri      | Windows Server 2016     |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | İki genel `conda` ortam oluşturulur: <br /> * `root` Konumunda bulunan ortam `/anaconda/` Python 3,7 ' dir. <br/> * `python2` Konumunda bulunan ortam `/anaconda/envs/python2` Python 2,7 ' dir.       |
| Örneklere bağlantılar      | Python için örnek Jupyıter Not defterleri dahil edilmiştir.     |
| DSVM 'deki ilgili araçlar      | PySpark, R, Julia.      |

> [!NOTE]
> 2018 Mart ' den önce oluşturulan Windows Server 2016 ' in derlemeleri Python 3,5 ve Python 2,7 ' i içerir. Python 2,7, Conda **kök** ortamıdır ve **py37** , Python 3,7 ortamıdır.

### <a name="how-to-use-and-run-it"></a>Kullanma ve çalıştırma    

* Komut isteminden çalıştırın:

  Bir komut istemi açın ve çalıştırmak istediğiniz Python sürümüne bağlı olarak aşağıdaki yöntemlerden birini kullanın:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* IDE içinde kullanın:

  Visual Studio Community Edition 'da yüklü olan Visual Studio için Python Araçları (PTV) kullanın. Varsayılan olarak, PTV 'lerde otomatik olarak ayarlanan tek ortam Python 3,6 ' dir. 

    > [!NOTE]
    > Python 2,7 ' de PTV 'leri göstermek için PTV 'lerde özel bir ortam oluşturmanız gerekir. Visual Studio Community sürümünde bu ortam yolunu ayarlamak için **Araçlar**  ->  **Python araçları**  ->  **Python ortamları** ' na gidin ve **+ özel**' i seçin. Sonra, konumu **c:\anaconda\envs\python2** olarak ayarlayın ve **Otomatik Algıla** seçeneğini belirleyin.

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Python 3,7 için bir Python _[Conda root]_ ve Python 2,7 için _Python [Conda env: python2]_ olarak çekirdek türünü ayarlayabilirsiniz.

* Python paketlerini yükler:

  DSVM 'deki varsayılan Python ortamları, tüm kullanıcılar tarafından okunabilen küresel ortamlardır. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri küresel ortama yüklemek için, komutunu yönetici olarak kullanarak kök veya python2 ortamına etkinleştirin `activate` . Ardından, `conda` `pip` paketleri yüklemek veya güncelleştirmek için veya gibi bir paket Yöneticisi kullanabilirsiniz.

## <a name="python-linux-edition"></a>Python (Linux sürümü)

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen dil sürümleri | Python 2,7 ve 3,5 |
| Desteklenen DSVM sürümleri      | Linux   |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | İki genel `conda` ortam oluşturulur: <br /> * `root` konumunda bulunan ortam `/anaconda/` Python 2,7 ' dir. <br/> * `py35` konumunda bulunan ortam `/anaconda/envs/py35` Python 3,5 ' dir.       |
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

  DSVM 'deki varsayılan Python ortamları, tüm kullanıcılar tarafından okunabilen küresel ortamlardır. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri küresel ortama yüklemek için, `source activate` komutunu yönetici olarak veya sudo izinleri olan bir kullanıcı olarak kullanarak kök veya py35 ortamına etkinleştirin. Ardından, `conda` `pip` paketleri yüklemek veya güncelleştirmek için veya gibi bir paket Yöneticisi kullanabilirsiniz.


## <a name="r"></a>R

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen dil sürümleri | Microsoft R Open 3. x (CRAN-R ile %100 uyumlu)<br /> Microsoft R Server 9. x geliştirici sürümü (bir kurumsal özellikli ölçeklenebilir R platformu)|
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

  R, tüm kullanıcılar tarafından okunabilen küresel bir ortamda DSVM 'ye yüklenir. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri genel ortama yüklemek için önceki yöntemlerden birini kullanarak R 'yi çalıştırın. Ardından, `install.packages()` paketleri yüklemek veya güncelleştirmek Için R paket yöneticisini çalıştırabilirsiniz.

**Linux**:

* Terminalde Çalıştır:

  Bir Terminal açın ve çalıştırın `R` .  

* IDE içinde kullanın:

  Linux DSVM 'de yüklü olan RStudio 'yu kullanın.  

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Jupyıter R çekirdeğini (ırkernel) kullanmak için çekirdek türünü **R** olarak ayarlayabilirsiniz. 

* R paketlerini yükler:

  R, tüm kullanıcılar tarafından okunabilen küresel bir ortamda DSVM 'ye yüklenir. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri genel ortama yüklemek için önceki yöntemlerden birini kullanarak R 'yi çalıştırın. Ardından, `install.packages()` paketleri yüklemek veya güncelleştirmek Için R paket yöneticisini çalıştırabilirsiniz.


## <a name="julia"></a>Julia

| Kategori | Değer |
| ------------- | ------------- |
| Desteklenen dil sürümleri | 0.6 |
| Desteklenen DSVM sürümleri      | Linux, Windows     |
| DSVM 'de nasıl yapılandırılır/yüklenir?  | Windows: yüklendi `C:\JuliaPro-VERSION`<br /> Linux: yüklendi `/opt/JuliaPro-VERSION`    |
| Örneklere bağlantılar      | Julia için örnek Jupyıter Not defterleri dahil edilmiştir.     |
| DSVM 'deki ilgili araçlar      | Python, R      |

### <a name="how-to-use-and-run-it"></a>Kullanma ve çalıştırma    

**Windows**:

* Komut isteminden Çalıştır

  Bir komut istemi açın ve çalıştırın `julia` .
* IDE içinde kullanın:

  `Juno`DSVM 'de yüklü olan ve masaüstü kısayolu olarak kullanılabilen JULIA IDE ile birlikte kullanın.

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Çekirdek türünü **JULIA sürümü** olarak ayarlayabilirsiniz.

* Julia paketlerini yükler:

  Varsayılan Julia konumu, tüm kullanıcılar tarafından okunabilen küresel bir ortamdır. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri küresel ortama yüklemek için yukarıdaki yöntemlerden birini kullanarak Julia 'yı çalıştırın. Ardından, `Pkg.add()` paketleri yüklemek veya güncelleştirmek Için Julia Paket Yöneticisi komutlarını çalıştırabilirsiniz.


**Linux**:
* Bir terminalde Çalıştır:

  Bir Terminal açın ve çalıştırın `julia` .
* IDE içinde kullanın:

  `Juno`JULIA IDE 'YI DSVM 'de yüklü ve **uygulama** menüsü kısayolu olarak kullanılabilir şekilde kullanın.

* Jupyter içinde kullanın:

  Jupyter ' ı açın **ve yeni ' yi seçerek yeni** bir not defteri oluşturun. Çekirdek türünü **JULIA sürümü** olarak ayarlayabilirsiniz.

* Julia paketlerini yükler:

  Varsayılan Julia konumu, tüm kullanıcılar tarafından okunabilen küresel bir ortamdır. Ancak, genel paketleri yalnızca Yöneticiler yazabilir ve yükleyebilir. Paketleri küresel ortama yüklemek için yukarıdaki yöntemlerden birini kullanarak Julia 'yı çalıştırın. Ardından, `Pkg.add()` paketleri yüklemek veya güncelleştirmek Için Julia Paket Yöneticisi komutlarını çalıştırabilirsiniz.

## <a name="other-languages"></a>Diğer diller

**C#**: Windows 'da kullanılabilir ve Visual Studio Community Edition veya üzerinde erişilebilir `Developer Command Prompt for Visual Studio` , burada `csc` komutu çalıştırabilirsiniz.

**Java**: OpenJDK, dsvm 'Nin hem Linux hem de Windows sürümlerinde bulunur ve yolunda ayarlanır. Java kullanmak için `javac` `java` Windows veya Linux 'taki bash kabuğu 'ndaki bir komut isteminde veya komutunu yazın.

**Node.js**: Node.js Dsvm 'nin Linux ve Windows sürümlerinde bulunur ve yolda ayarlanır. Node.js erişmek için `node` `npm` Windows veya Linux 'taki bash kabuğu 'ndaki bir komut isteminde veya komutunu yazın. Windows 'da, Node.js uygulamanızı geliştirmek için bir grafik IDE sağlamak üzere Node.js araçları için Visual Studio uzantısı yüklenmiştir.

**F #**: Windows 'da kullanılabilir ve Visual Studio Community Edition aracılığıyla veya ' de erişilebilir `Developer Command Prompt for Visual Studio` , burada `fsc` komutu çalıştırabilirsiniz.
