---
title: Apache HBase & Kurumsal Güvenlik Paketi-Azure HDInsight
description: Öğretici-Kurumsal Güvenlik Paketi ile Azure HDInsight 'ta HBase için Apache Ranger ilkelerini yapılandırma hakkında bilgi edinin.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 89e9faeb3c67d0fd0c57adea3a3f69ec5438e3a0
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044654"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Öğretici: HDInsight 'ta Kurumsal Güvenlik Paketi ile Apache HBase ilkelerini yapılandırma

Kurumsal Güvenlik Paketi (ESP) Apache HBase kümeleri için Apache Ranger ilkelerini nasıl yapılandıracağınızı öğrenin. ESP kümeleri bir etki alanına bağlıdır ve kullanıcıların etki alanı kimlik bilgileriyle kimlik doğrulaması yapmasına olanak sağlar. Bu öğreticide, bir HBase tablosundaki farklı sütun ailelerine erişimi kısıtlamak için iki Ranger ilkesi oluşturacaksınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Etki alanı kullanıcılarını oluşturma
> * Ranger ilkelerini oluşturma
> * HBase kümesinde tablo oluşturma
> * Ranger ilkelerini test etme

## <a name="before-you-begin"></a>Başlamadan önce

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

* [Azure Portal](https://portal.azure.com/)’ında oturum açın.

* Kurumsal Güvenlik Paketi bir [HDInsight HBase kümesi](apache-domain-joined-configure-using-azure-adds.md)oluşturun.

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger Yönetici Arabirimine bağlanma

1. Bir tarayıcıdan, `https://<ClusterName>.azurehdinsight.net/Ranger/` URL’sini kullanarak Ranger Yönetici kullanıcı arabirimine bağlanın. `<ClusterName>` değerini HBase kümenizin adıyla değiştirmeyi unutmayın.

    > [!NOTE]  
    > Ranger kimlik bilgileri Hadoop kümesi kimlik bilgileriyle aynı değildir. Tarayıcıların ön belleğe alınmış Hadoop kimlik bilgilerini kullanmasını önlemek için Ranger Yönetici Arabirimine yeni bir InPrivate tarayıcı penceresinden bağlanın.

2. Azure Active Directory (AD) yönetici kimlik bilgilerinizi kullanarak oturum açın. Azure AD yönetici kimlik bilgileri HDInsight küme kimlik bilgileri veya Linux HDInsight düğümü SSH kimlik bilgileriyle aynı değildir.

## <a name="create-domain-users"></a>Etki alanı kullanıcılarını oluşturma

**Sales_user1** ve **marketing_user1** etki alanı kullanıcılarını oluşturmayı öğrenmek için [Kurumsal güvenlik paketi ile HDInsight kümesi oluşturma](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)sayfasını ziyaret edin. Bir üretim senaryosunda, etki alanı kullanıcıları Active Directory kiracınızdan gelir.

## <a name="create-hbase-tables-and-import-sample-data"></a>HBase tabloları oluşturma ve örnek verileri içeri aktarma

SSH kullanarak HBase kümelerine bağlanabilir ve ardından, HBase tabloları oluşturmak, veri eklemek ve verileri sorgulamak için [Apache HBase kabuğu](https://hbase.apache.org/0.94/book/shell.html) 'nu kullanabilirsiniz. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>HBase kabuğunu kullanmak için

1. SSH'den aşağıdaki HBase komutu çalıştırın:
   
    ```bash
    hbase shell
    ```

2. İki sütunlu ailelerle `Customers` bir HBase tablosu oluşturun: `Name` ve `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Bazı verileri ekleyin:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Tablonun içeriğini görüntüleyin:
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![HDInsight Hadoop HBase kabuğu çıkışı](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Ranger ilkelerini oluşturma

**Sales_user1** ve **Marketing_user1**için bir Ranger ilkesi oluşturun.

1. **Ranger Yönetici Arabirimini** açın. **HBase**altında **\<clustername > _hbase** öğesine tıklayın.

   ![HDInsight Apache Ranger yönetici kullanıcı arabirimi](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. **Ilke listesi** ekranı, bu küme için oluşturulan tüm Ranger ilkelerini görüntüler. Bir önceden yapılandırılmış ilke listelenebilir. **Yeni Ilke Ekle**' ye tıklayın.

    ![Apache Ranger HBase ilkeleri listesi](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. **Ilke oluştur** ekranında, aşağıdaki değerleri girin:

   |**Ayar**  |**Önerilen değer**  |
   |---------|---------|
   |İlke Adı  |  sales_customers_name_contact   |
   |HBase tablosu   |  Müşteriler |
   |HBase sütunu-aile   |  Ad, Iletişim |
   |HBase sütunu   |  * |
   |Grup Seç  | |
   |Kullanıcı Seçin  | sales_user1 |
   |İzinler  | Okuma |

   Konu adında şu joker karakterler bulunabilir:

   * `*` karakterlerin sıfır veya daha fazla örneğini belirtir.
   * `?` tek karakteri gösterir.

   ![Apache Ranger ilkesi satışları oluşturma](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >**Select User** için bir etki alanı kullanıcısı otomatik olarak doldurulmazsa, Ranger’ın Azure AD ile eşitlenmesi için birkaç dakika bekleyin.

4. **Add**’e tıklayarak ilkeyi kaydedin.

5. **Add New Policy**’ye tıklayıp aşağıdaki değerleri girin:

   |**Ayar**  |**Önerilen değer**  |
   |---------|---------|
   |İlke Adı  |  marketing_customers_contact   |
   |HBase tablosu   |  Müşteriler |
   |HBase sütunu-aile   |  İletişim |
   |HBase sütunu   |  * |
   |Grup Seç  | |
   |Kullanıcı Seçin  | marketing_user1 |
   |İzinler  | Okuma |

   ![Apache Ranger ilkesi pazarlama oluşturma](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. **Add**’e tıklayarak ilkeyi kaydedin.

## <a name="test-the-ranger-policies"></a>Ranger ilkelerini test etme

**Sales_user1** , yapılandırılan Ranger ilkelerine bağlı olarak, `Name` ve `Contact` sütun ailelerinde bulunan sütunlara ait tüm verileri görüntüleyebilir. **Marketing_user1** yalnızca `Contact` sütun ailesindeki verileri görüntüleyebilir.

### <a name="access-data-as-sales_user1"></a>Verilere sales_user1 olarak erişin

1. Kümeye yeni bir SSH bağlantısı açın. Kümede oturum açmak için aşağıdaki komutu kullanın:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. İstenen kullanıcılarımızın bağlamına geçmek için kinit komutunu kullanın.

   ```bash
   kinit sales_user1
   ```

2. HBase kabuğunu açın ve tablo `Customers` tarayın.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Satış kullanıcısının `Customers` tablonun tüm sütunlarını `Name` sütun ailedeki iki sütun ve `Contact` sütun-aile ' de bulunan beş sütun dahil) görüntüleyebileceğini unutmayın.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketing_user1"></a>Verilere marketing_user1 olarak erişin

1. Kümeye yeni bir SSH bağlantısı açın. **Marketing_user1**olarak oturum açmak için aşağıdaki komutu kullanın:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. İstenen kullanıcılarımızın bağlamına geçmek için kinit komutunu kullanın

   ```bash
   kinit marketing_user1
   ```

1. HBase kabuğunu açın ve tablo `Customers` tarayın:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Pazarlama kullanıcısının `Contact` sütun ailesinin yalnızca beş sütununu görüntüleyebildiğine dikkat edin.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

1. Ranger kullanıcı arabiriminden denetim erişimi olaylarını görüntüleyin.

   ![HDInsight Ranger Kullanıcı arabirimi Ilke denetimi](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, oluşturduğunuz HBase kümesini aşağıdaki adımlarla silin:

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.
2. Üstteki **arama** kutusuna **HDInsight**yazın. 
1. **Hizmetler**altında **HDInsight kümeleri** ' ni seçin.
1. Görüntülenen HDInsight kümeleri listesinde, bu öğretici için oluşturduğunuz kümenin yanındaki **...** öğesine tıklayın. 
1. **Sil**'e tıklayın. **Evet**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Apache HBase ile çalışmaya başlama](../hbase/apache-hbase-tutorial-get-started-linux.md)
