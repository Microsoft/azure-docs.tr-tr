---
title: Kurumsal Güvenlik Paketi kümelerini yönetme-Azure HDInsight
description: Azure HDInsight kümelerini Kurumsal Güvenlik Paketi ile yönetmeyi öğrenin.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 35accc587fe197da751a8695e3ec0b21ea9fbbd4
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044912"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>HDInsight kümelerini Kurumsal Güvenlik Paketi ile yönetme
HDInsight 'daki kullanıcıları ve rolleri Kurumsal Güvenlik Paketi (ESP) ve ESP kümelerinin nasıl yönetileceğini öğrenin.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Etki alanına katılmış kümeye bağlamak için VSCode kullanma

Apache ambarı yönetilen Kullanıcı adı ' nı kullanarak normal kümeyi bağlayabilir, ayrıca etki alanı Kullanıcı adı (örneğin: `user1@contoso.com`) kullanarak bir güvenlik Apache Hadoop kümesi de bağlayabilirsiniz.

1. **CTRL + SHIFT + P**tuşlarına basarak komut paletini açın ve ardından **HDInsight: bir kümeyi bağla**' yı girin.

   ![komut paleti, bir kümeyi bağlama](./media/apache-domain-joined-manage/link-cluster-command.png)

2. HDInsight kümesi URL 'sini girin-> girişi Kullanıcı adı-> giriş parolası-> küme türünü seçin-> doğrulama başarılı olursa başarı bilgilerini gösterir.

   ![bağlama kümesi işlem adımları iletişim kutusu](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Küme hem Azure abonelikte hem de bir kümeye bağlandığında, bağlantılı Kullanıcı adı ve parola kullanılır.

3. Komut **listesi kümesi**kullanarak bağlantılı bir küme görebilirsiniz. Artık bu bağlantılı kümeye bir komut dosyası gönderebilirsiniz.

   ![küme listeleme komut çıktısı doğrulaması](./media/apache-domain-joined-manage/hdinsight-linked-cluster.png "bağlı küme")

4. Ayrıca HDInsight 'a giriş yaparak bir kümenin bağlantısını kaldırabilirsiniz **: bir kümenin bağlantısını** komut paleti.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Etki alanına katılmış kümeye bağlamak için IntelliJ kullanma

Ambarı yönetilen Kullanıcı adı ' nı kullanarak normal kümeyi bağlayabilir, ayrıca etki alanı Kullanıcı adı ' nı kullanarak bir güvenlik Hadoop kümesini bağlayabilirsiniz (örneğin: `user1@contoso.com`).

1. **Azure Explorer**'dan **bir kümeyi bağla** ' yı tıklatın.

   ![bağlama kümesi bağlam menüsü IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. **Küme adı**, **Kullanıcı adı** ve **parola**girin. Kimlik doğrulama hatası varsa Kullanıcı adını ve parolayı denetlemeniz gerekir. İsteğe bağlı olarak, depolama hesabı, depolama anahtarı ekleyin ve depolama kapsayıcısından bir kapsayıcı seçin. Depolama bilgileri, sol ağaçta Depolama Gezgini içindir

   ![Azure Explorer bağlantı kümesi iletişim kutusu IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Küme hem Azure abonelikte hem de bir kümeye bağlandığında bağlı depolama anahtarını, Kullanıcı adını ve parolayı kullanırız.
   > 
   > ![IntelliJ 'de Azure Gezgini depolama hesabı](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

3. Giriş bilgileri doğru ise, **HDInsight** düğümünde bağlantılı bir küme görebilirsiniz. Artık, bu bağlantılı kümeye bir uygulama gönderebilirsiniz.

   ![Azure Explorer bağlantılı küme IntelliJ](./media/apache-domain-joined-manage/linked-cluster-intellij.png "bağlı küme IntelliJ]")

4. Ayrıca bir kümenin bağlantısını **Azure Explorer**'da kaldırabilirsiniz.

   ![Azure Explorer bağlantısı olmayan küme IntelliJ](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Etki alanına katılmış kümeye bağlamak için Eclipse kullanma

Ambarı yönetilen Kullanıcı adı ' nı kullanarak normal kümeyi bağlayabilir, ayrıca etki alanı Kullanıcı adı ' nı kullanarak bir güvenlik Hadoop kümesini bağlayabilirsiniz (örneğin: `user1@contoso.com`).

1. **Azure Explorer**'dan **bir kümeyi bağla** ' yı tıklatın.

   ![bağlama kümesi bağlam menüsü tutulma](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Küme **adı**, **Kullanıcı adı** ve **parola**girin, ardından kümeyi bağlamak için Tamam düğmesine tıklayın. İsteğe bağlı olarak, depolama hesabı, depolama anahtarı girin ve ardından Depolama Gezgini 'nin sol ağaç görünümünde çalışması için depolama kapsayıcısını seçin

   ![Azure Gezgini bağlantı kümesi iletişim kutusu tutulma](./media/apache-domain-joined-manage/link-cluster-dialog1.png)

   > [!NOTE]  
   > Küme hem Azure abonelikte hem de bir kümeye bağlandığında bağlı depolama anahtarını, Kullanıcı adını ve parolayı kullanırız.
   > 
   > ![Azure Explorer depolama hesabı, tutulma 'da](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Giriş bilgileri doğru ise, Tamam düğmesine tıkladıktan sonra **HDInsight** düğümünde bağlantılı bir küme görebilirsiniz. Artık, bu bağlantılı kümeye bir uygulama gönderebilirsiniz.

   ![Azure Explorer bağlantılı küme tutulma](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Ayrıca bir kümenin bağlantısını **Azure Explorer**'da kaldırabilirsiniz.
   
   ![Azure Explorer bağlantısı olmayan küme tutulma](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Kümelere Kurumsal Güvenlik Paketi erişin.

Kurumsal Güvenlik Paketi (önceki adı HDInsight Premium), kümeye çok kullanıcılı erişim sağlar ve bu kimlik doğrulaması, Apache Ranger ve depolama ACL 'Leri (ADLS ACL 'Ler) tarafından Active Directory ve yetkilendirmeyle yapılır. Yetkilendirme, birden çok kullanıcı arasında güvenli sınırlar sağlar ve yalnızca ayrıcalıklı kullanıcıların yetkilendirme ilkelerine göre verilere erişmesine izin verir.

Güvenlik ve Kullanıcı yalıtımı, Kurumsal Güvenlik Paketi bir HDInsight kümesi için önemlidir. Bu gereksinimleri karşılamak için Kurumsal Güvenlik Paketi ile kümeye SSH erişimi engellenir. Aşağıdaki tabloda her küme türü için önerilen erişim yöntemleri gösterilmektedir:

|İş yükü|Senaryo|Erişim yöntemi|
|--------|--------|-------------|
|Apache Hadoop|Hive – etkileşimli Işler/sorgular  |<ul><li>[Beeline](#beeline)</li><li>[Hive görünümü](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Araçları](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Etkileşimli Işler/sorgular, PySpark etkileşimli|<ul><li>[Beeline](#beeline)</li><li>[Livy ile Zeppelin](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive görünümü](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Araçları](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batch senaryoları – Spark gönderme, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Etkileşimli sorgu (LLAP)|Etkileşimli|<ul><li>[Beeline](#beeline)</li><li>[Hive görünümü](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Araçları](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Herhangi biri|Özel uygulama yüklemesi|<ul><li>[Betik eylemleri](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyıter Kurumsal Güvenlik Paketi ' de yüklü değil/desteklenmiyor.

Standart API 'Lerin kullanılması güvenlik açısından yardımcı olur. Ayrıca, aşağıdaki avantajları elde edersiniz:

- **Yönetim** : Standart API 'ler, HS2 vb. kullanarak kodunuzu yönetebilir ve işleri otomatikleştirebilirsiniz.
- **Denetim** – SSH ile, KULLANıCıLARıN kümeye SSH 'yi denetlemesi için bir yol yoktur. Bu durum, işler kullanıcı bağlamında yürütülene kadar standart uç noktalar aracılığıyla oluşturulduğunda bu durum değildir. 



### <a name="beeline"></a>Beeline kullanma 
Makinenize Beeline yükleyip genel İnternet üzerinden bağlanın ve aşağıdaki parametreleri kullanın: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Yerel olarak yüklü olan ve bir Azure sanal ağı üzerinden bağlantı varsa, aşağıdaki parametreleri kullanın: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Bir headnode 'un tam etki alanı adını bulmak için, ambarı REST API belgelerini kullanarak HDInsight 'ı yönetme bölümündeki bilgileri kullanın.














## <a name="users-of-hdinsight-clusters-with-esp"></a>ESP ile HDInsight kümelerinin kullanıcıları
ESP olmayan bir HDInsight kümesi, küme oluşturma sırasında oluşturulan iki kullanıcı hesabına sahiptir:

* **Ambarı Yöneticisi**: Bu hesap *Hadoop kullanıcısı* veya *http kullanıcısı*olarak da bilinir. Bu hesap, https://&lt;clustername >. azurehdinsight. net konumundaki ambarı 'nda oturum açmak için kullanılabilir. Ayrıca, ambarı görünümlerinde sorguları çalıştırmak, dış araçlarla (örneğin, PowerShell, Temptaton, Visual Studio) işleri yürütmek ve Hive ODBC sürücüsü ve BI araçları (örneğin, Excel, Power BI veya Tableau) ile kimlik doğrulaması yapmak için de kullanılabilir.

ESP içeren bir HDInsight kümesinde, ambarı yöneticisine ek olarak üç yeni kullanıcı vardır.

* **Ranger Yöneticisi**: Bu hesap, yerel Apache Ranger yönetici hesabıdır. Active Directory etki alanı kullanıcısı değil. Bu hesap, ilkeleri kurmak ve diğer kullanıcılar için yönetici veya yönetici temsilcisi (bu kullanıcıların ilkeleri yönetebilmeleri için) oluşturmak için kullanılabilir. Varsayılan olarak, Kullanıcı adı *admin* ' dir ve parola, ambarı yönetici parolasıyla aynıdır. Parola, Ranger içindeki ayarlar sayfasından güncelleştirilir.
* **Küme Yöneticisi etki alanı kullanıcısı**: Bu hesap, ambarı ve Ranger dahil olmak üzere Hadoop kümesi Yöneticisi olarak atanan bir Active Directory etki alanı kullanıcısı. Küme oluşturma sırasında bu kullanıcının kimlik bilgilerini sağlamanız gerekir. Bu Kullanıcı aşağıdaki ayrıcalıklara sahiptir:

  * Makineleri etki alanına ekleyin ve küme oluşturma sırasında belirttiğiniz OU içine yerleştirin.
  * Küme oluşturma sırasında belirttiğiniz OU içinde hizmet sorumluları oluşturun.
  * Ters DNS girişleri oluşturun.

    Diğer AD kullanıcılarının da bu ayrıcalıklara sahip olduğunu aklınızda bulabilirsiniz.

    Küme içinde, Ranger tarafından yönetilmeyen bazı uç noktaları vardır ve bu nedenle güvenli değildir. Bu uç noktaları, Küme Yöneticisi etki alanı kullanıcısı hariç tüm kullanıcılar için kilitlenir.
* **Normal**: küme oluşturma sırasında, birden çok Active Directory grubu sağlayabilirsiniz. Bu gruplardaki kullanıcılar Ranger ve ambarı ile eşitlenir. Bu kullanıcılar etki alanı kullanıcılardır ve yalnızca Ranger tarafından yönetilen uç noktalara erişime sahiptir (örneğin, Hiveserver2). Tüm RBAC ilkeleri ve denetimleri bu kullanıcılar için geçerli olacaktır.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>ESP ile HDInsight kümelerinin rolleri
HDInsight Kurumsal Güvenlik Paketi aşağıdaki rollere sahiptir:

* Küme Yöneticisi
* Küme Işleci
* Hizmet Yöneticisi
* Hizmet operatörü
* Küme kullanıcısı

**Bu rollerin izinlerini görmek için**

1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **Roller**' e tıklayın.
3. İzinleri görmek için mavi soru işaretine tıklayın:

    ![ESP HDInsight rol izinleri](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Ambarı yönetimi Kullanıcı arabirimini açın

1. [Azure portalı](https://portal.azure.com) üzerinde oturum açın.
2. HDInsight kümenizi açın.
3. Ambarı açmak için üstteki menüden **Pano** ' ya tıklayın.
4. Küme Yöneticisi etki alanı Kullanıcı adı ve parolasını kullanarak, ambarı 'nda oturum açın.
5. Sağ üst köşedeki **yönetici** açılan menüsüne tıklayın ve ardından **ambarı Yönet**' e tıklayın.

    ![ESP HDInsight Apache ambarı 'nı yönetme](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Kullanıcı arabirimi şöyle görünür:

    ![ESP HDInsight Apache ambarı yönetim kullanıcı arabirimi](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Active Directory eşitlenen etki alanı kullanıcılarını listeleyin
1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **Kullanıcılar**' a tıklayın. Active Directory HDInsight kümesine eşitlenmiş tüm kullanıcıları görürsünüz.

    ![ESP HDInsight ambarı yönetimi Kullanıcı arabirimi listesi kullanıcıları](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Active Directory eşitlenen etki alanı gruplarını listeleyin
1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **gruplar**' a tıklayın. Active Directory HDInsight kümesine eşitlenmiş tüm grupları görürsünüz.

    ![ESP HDInsight ambarı yönetimi Kullanıcı arabirimi liste grupları](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Hive görünümlerini yapılandırma izinleri
1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden, **Görünümler**' e tıklayın.
3. Ayrıntıları göstermek için **HIVE** ' e tıklayın.

    ![ESP HDInsight ambarı yönetimi UI Hive görünümleri](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Hive görünümlerini yapılandırmak için **Hive görünümü** bağlantısına tıklayın.
5. Aşağı kaydırarak **izinler** bölümüne gidin.

    ![ESP HDInsight ambarı yönetimi UI Hive görünümleri izinleri yapılandırma](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. **Kullanıcı Ekle** veya **Grup Ekle**' ye tıklayın ve ardından Hive görünümlerini kullanabileceği kullanıcıları veya grupları belirtin.

## <a name="configure-users-for-the-roles"></a>Rolleri için kullanıcıları yapılandırma
 Rollerin ve izinlerinin listesini görmek için bkz. ESP ile HDInsight kümelerinin rolleri.

1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **Roller**' e tıklayın.
3. Kullanıcıları ve grupları farklı rollere atamak için **Kullanıcı Ekle** veya **Grup Ekle** ' ye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
* HDInsight kümesini Kurumsal Güvenlik Paketi ile yapılandırmak için bkz. [HDInsight KÜMELERINI ESP Ile yapılandırma](apache-domain-joined-configure.md).
* Hive ilkelerini yapılandırmak ve Hive sorgularını çalıştırmak için bkz. [HDInsight kümeleri için Apache Hive ILKELERINI ESP Ile yapılandırma](apache-domain-joined-run-hive.md).
