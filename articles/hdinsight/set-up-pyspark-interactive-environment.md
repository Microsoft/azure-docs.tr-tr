---
title: Azure HDInsight araçları ile PySpark etkileşimli ortamı
description: Sorgular ve betikler oluşturmak ve göndermek için Visual Studio Code için Azure HDInsight araçlarını kullanmayı öğrenin.
keywords: VScode, Azure HDInsight araçları, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, etkileşimli sorgu
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: db2336fb79207ada24b71e0e64f0aaaab543e4da
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241546"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code için PySpark etkileşimli ortamını ayarlama

Aşağıdaki adımlarda VS Code 'da PySpark etkileşimli ortamının nasıl ayarlanacağı gösterilmektedir.

Ana yolunuzda sanal ortam oluşturmak için **Python/PIP** komutunu kullanıyoruz. Başka bir sürüm kullanmak istiyorsanız, **Python/PIP** komutunun varsayılan sürümünü el ile değiştirmeniz gerekir. Daha fazla ayrıntı için bkz. [Update-alternatifler](https://linux.die.net/man/8/update-alternatives).

1. [Python](https://www.python.org/downloads/) ve [PIP](https://pip.pypa.io/en/stable/installing/)'yi yükler.

   + [https://www.python.org/downloads/](https://www.python.org/downloads/)'tan Python 'ı yükler.
   + [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (Python yüklemesinden yüklenmediyse) PIP 'yi yükleme.
   + Aşağıdaki komutları kullanarak Python ve PIP 'nin başarıyla yüklendiğini doğrulayın. Seçim

        ![Python pversıon sürümü komutunu denetle](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > MacOS varsayılan sürümünü kullanmak yerine Python 'un el ile yüklenmesi önerilir.

2. Aşağıdaki komutu çalıştırarak **virtualenv** 'yi çalıştırın.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Diğer paketler

Bir hata iletisiyle karşılaşırsanız, aşağıdaki komutları çalıştırarak gerekli paketleri yükleyebilirsiniz:

   ![Python için libkrb5 paketini yükler](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

VS Code yeniden başlatın ve ardından HDInsight çalıştıran betik düzenleyicisine geri dönün **: PySpark Interactive**.

## <a name="next-steps"></a>Sonraki adımlar

### <a name="demo"></a>Tanıtım
* VS Code için HDInsight: [video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar
* [Visual Studio Code için Azure HDInsight aracını kullanma](hdinsight-for-vscode.md)
* [Apache Spark Scala uygulamaları oluşturmak ve göndermek için Azure Toolkit for IntelliJ kullanma](spark/apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ kullanarak Apache Spark uygulamalarda SSH aracılığıyla uzaktan hata ayıklayın](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [VPN aracılığıyla Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark uygulamalar oluşturmak için Azure Toolkit for Eclipse HDInsight araçlarını kullanma](spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesinde Jupyter Not defteri için kullanılabilir çekirdekler](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight 'ta Microsoft Power BI ile Apache Hive verileri görselleştirme](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight'da Apache Hive sorgularını çalıştırmak için Apache Zeppelin'i kullanma](./interactive-query/hdinsight-connect-hive-zeppelin.md)
