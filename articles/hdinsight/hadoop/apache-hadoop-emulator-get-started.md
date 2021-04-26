---
title: Apache Hadoop korumalı alan kullanmayı öğrenin-Azure HDInsight
description: 'Apache Hadoop ekosistemini kullanma hakkında bilgi edinmeye başlamak için bir Azure sanal makinesinde Hortonçalışmalardan bir Hadoop korumalı alanı ayarlayabilirsiniz. '
keywords: Hadoop öykünücüsü, Hadoop korumalı alanı
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.openlocfilehash: 60f4e4998e1077ca373f47a0cdf35c9d7a064605
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866684"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Sanal makinede öykünücü olan Apache Hadoop korumalı alan ile çalışmaya başlama

Hadoop ekosistemi hakkında bilgi edinmek için bir sanal makinede Hortonçalışmalardan Apache Hadoop korumalı alanı yüklemeyi öğrenin. Korumalı alan, Hadoop, Hadoop Dağıtılmış Dosya Sistemi (bir) ve iş gönderimi hakkında bilgi edinmek için yerel bir geliştirme ortamı sağlar. Hadoop 'u tanıdıktan sonra HDInsight kümesi oluşturarak Azure 'da Hadoop kullanmaya başlayabilirsiniz. Kullanmaya başlama hakkında daha fazla bilgi için bkz. [HDInsight 'Ta Hadoop kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Önkoşullar

* [Oracle VirtualBox](https://www.virtualbox.org/). [Buradan](https://www.virtualbox.org/wiki/Downloads)indirin ve yükleyin.

## <a name="download-and-install-the-virtual-machine"></a>Sanal makineyi indir ve yükle

1. [Cloudera indirmelerine](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)gidin.

1. Bir VM 'ye en son Hortonçalışmalar korumalı alanını indirmek için **yükleme türünü seçin** altında **VirtualBox** ' a tıklayın. Oturum açın veya ürün ilgi formunu doldurun.

1. İndirmeye başlamak için **HDP KORUMALı alan (en son)** düğmesine tıklayın.

Korumalı alanı ayarlama hakkında yönergeler için bkz. [korumalı alan dağıtımı ve Install Guide](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Eski bir HDP sürüm korumalı alanını indirmek için **eski sürümlerin** altındaki bağlantılara bakın.

## <a name="start-the-virtual-machine"></a>Sanal makineyi Başlat

1. Oracle VM VirtualBox öğesini açın.
1. **Dosya** menüsünde gereci **içeri aktar**' a tıklayın ve ardından Hortonçalışmalar korumalı alanı görüntüsünü belirtin.
1. Hortonçalışmalar korumalı alanını seçin, **Başlat**' a ve ardından **normal Başlat**' a tıklayın. Sanal makine önyükleme işlemini tamamladıktan sonra oturum açma yönergelerini görüntüler.

    :::image type="content" source="./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png" alt-text="VirtualBox Manager normal başlangıç" border="true":::

1. Bir Web tarayıcısı açın ve görüntülenecek URL 'ye gidin (genellikle `http://127.0.0.1:8888` ).

## <a name="set-sandbox-passwords"></a>Korumalı alan parolalarını ayarla

1. Hortonçalışmalar korumalı alanı sayfasının **Başlarken** adımından **Gelişmiş seçenekleri görüntüle**' yi seçin. SSH kullanarak korumalı alanda oturum açmak için bu sayfadaki bilgileri kullanın. Belirtilen adı ve parolayı kullanın.

   > [!NOTE]
   > Yüklü bir SSH istemciniz yoksa, üzerinde sanal makine tarafından belirtilen Web tabanlı SSH 'yi kullanabilirsiniz **http://localhost:4200/** .

    SSH kullanarak ilk kez bağlandığınızda, kök hesabın parolasını değiştirmeniz istenir. SSH kullanarak oturum açtığınızda kullanabileceğiniz yeni bir parola girin.

2. Oturum açtıktan sonra aşağıdaki komutu girin:

    ```bash
    ambari-admin-password-reset
    ```

    İstendiğinde, ambarı yönetici hesabı için bir parola sağlayın. Bu, ambarı Web Kullanıcı arabirimine eriştiğinizde kullanılır.

## <a name="use-hive-commands"></a>Hive komutlarını kullanma

1. Bir SSH bağlantısından korumalı alana, Hive kabuğu 'nu başlatmak için aşağıdaki komutu kullanın:

    ```bash
    hive
    ```

2. Kabuk başladıktan sonra, korumalı alan ile birlikte sunulan tabloları görüntülemek için aşağıdakileri kullanın:

    ```hiveql
    show tables;
    ```

3. Tablodan 10 satır almak için aşağıdakileri kullanın `sample_07` :

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio 'Yu Hortonçalışmalar korumalı alanı ile nasıl kullanacağınızı öğrenin](./apache-hadoop-visual-studio-tools-get-started.md)

* [Hortonçalışmalar korumalı alanının servis 'i öğrenme](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop öğreticisi-HDP ile çalışmaya başlama](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)