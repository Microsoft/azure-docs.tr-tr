---
title: Kurumsal Güvenlik Paketi kümelerini yönetme-Azure HDInsight
description: Azure HDInsight kümelerini Kurumsal Güvenlik Paketi ile yönetmeyi öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: bc31c3d71590a6b8c0b324ffcb8c10129a9f8699
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863250"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>HDInsight kümelerini Kurumsal Güvenlik Paketi ile yönetme

HDInsight 'daki kullanıcıları ve rolleri Kurumsal Güvenlik Paketi (ESP) ve ESP kümelerinin nasıl yönetileceğini öğrenin.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Etki alanına katılmış kümeye bağlamak için VSCode kullanma

Apache ambarı yönetilen Kullanıcı adı ' nı kullanarak normal kümeyi bağlayabilir, ayrıca etki alanı Kullanıcı adı (örneğin:) kullanarak bir güvenlik Apache Hadoop kümesi de bağlayabilirsiniz `user1@contoso.com` .

1. [Visual Studio Code](https://code.visualstudio.com/)açın. [Spark & Hive araçları](../hdinsight-for-vscode.md) uzantısının yüklü olduğundan emin olun.

1. Visual Studio Code için [bir kümeyi bağlama](../hdinsight-for-vscode.md#link-a-cluster) bölümündeki adımları izleyin.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Etki alanına katılmış kümeye bağlamak için IntelliJ kullanma

Ambarı yönetilen Kullanıcı adı ' nı kullanarak normal kümeyi bağlayabilir, ayrıca etki alanı Kullanıcı adı (örneğin:) kullanarak bir güvenlik Hadoop kümesi de bağlayabilirsiniz `user1@contoso.com` .

1. IntelliJ IDEA’yı açın. Tüm [önkoşulların](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) karşılandığından emin olun.

1. IntelliJ için [bir kümeyi bağlama](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) bölümündeki adımları izleyin.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Etki alanına katılmış kümeye bağlamak için Eclipse kullanma

Ambarı yönetilen Kullanıcı adı ' nı kullanarak normal kümeyi bağlayabilir, ayrıca etki alanı Kullanıcı adı (örneğin:) kullanarak bir güvenlik Hadoop kümesi de bağlayabilirsiniz `user1@contoso.com` .

1. Eclipse’i açın. Tüm [önkoşulların](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) karşılandığından emin olun.

1. [Bir kümeyi](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) tutulma için bağlama bölümündeki adımları izleyin.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Kurumsal Güvenlik Paketi kümelere erişin

Kurumsal Güvenlik Paketi (önceki adı HDInsight Premium), kümeye çok kullanıcılı erişim sağlar ve bu kimlik doğrulaması, Apache Ranger ve depolama ACL 'Leri (ADLS ACL 'Ler) tarafından Active Directory ve yetkilendirmeyle yapılır. Yetkilendirme, birden çok kullanıcı arasında güvenli sınırlar sağlar ve yalnızca ayrıcalıklı kullanıcıların yetkilendirme ilkelerine göre verilere erişmesine izin verir.

Güvenlik ve Kullanıcı yalıtımı, Kurumsal Güvenlik Paketi bir HDInsight kümesi için önemlidir. Bu gereksinimleri karşılamak için, küme oluşturma sırasında seçilen yerel kullanıcının yanı sıra AAD-DS ' d e (Kerberos) bulunan kullanıcılar için Kurumsal Güvenlik Paketi ile SSH erişimi desteklenir. Aşağıdaki tabloda her küme türü için önerilen erişim yöntemleri gösterilmektedir:

|İş Yükü|Senaryo|Erişim yöntemi|
|--------|--------|-------------|
|Apache Hadoop|Hive – etkileşimli Işler/sorgular    |<ul><li>[Beeline](#beeline)</li><li>[Hive Görünümü](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Araçları](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Etkileşimli Işler/sorgular, PySpark etkileşimli|<ul><li>[Beeline](#beeline)</li><li>[Livy ile Zeppelin](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive Görünümü](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Araçları](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batch senaryoları – Spark gönderme, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Etkileşimli sorgu (LLAP)|Etkileşimli|<ul><li>[Beeline](#beeline)</li><li>[Hive Görünümü](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Araçları](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Herhangi biri|Özel uygulama yüklemesi|<ul><li>[Betik eylemleri](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyıter Kurumsal Güvenlik Paketi ' de yüklü değil/desteklenmiyor.

Standart API 'Lerin kullanılması güvenlik açısından yardımcı olur. Ayrıca aşağıdaki avantajları elde edersiniz:

- **Yönetim** : Standart API 'ler, HS2 vb. kullanarak kodunuzu yönetebilir ve işleri otomatikleştirebilirsiniz.
- **Denetim** – SSH ile, KULLANıCıLARıN kümeye SSH 'yi denetlemesi için bir yol yoktur. Bu durum, işler kullanıcı bağlamında yürütülene kadar standart uç noktalar aracılığıyla oluşturulduğunda bu durum değildir.

### <a name="use-beeline"></a><a name="beeline"></a>Beeline kullanma

Makinenize Beeline yükleyip genel İnternet üzerinden bağlanın ve aşağıdaki parametreleri kullanın:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Yerel olarak yüklü olan ve bir Azure sanal ağı üzerinden bağlantı varsa, aşağıdaki parametreleri kullanın:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Bir headnode 'un tam etki alanı adını bulmak için, ambarı REST API belgelerini kullanarak HDInsight 'ı yönetme bölümündeki bilgileri kullanın.

## <a name="users-of-hdinsight-clusters-with-esp"></a>ESP ile HDInsight kümelerinin kullanıcıları

ESP olmayan bir HDInsight kümesi, küme oluşturma sırasında oluşturulan iki kullanıcı hesabına sahiptir:

- **Ambarı Yöneticisi**: Bu hesap *Hadoop kullanıcısı* veya *http kullanıcısı* olarak da bilinir. Bu hesap, tarihinde ambarı 'nda oturum açmak için kullanılabilir `https://CLUSTERNAME.azurehdinsight.net` . Ayrıca, ambarı görünümlerinde sorguları çalıştırmak, dış araçlarla (örneğin, PowerShell, Temptaton, Visual Studio) işleri yürütmek ve Hive ODBC sürücüsü ve BI araçları (örneğin, Excel, Power BI veya Tableau) ile kimlik doğrulaması yapmak için de kullanılabilir.

ESP içeren bir HDInsight kümesinde, ambarı yöneticisine ek olarak üç yeni kullanıcı vardır.

- **Ranger Yöneticisi**: Bu hesap, yerel Apache Ranger yönetici hesabıdır. Active Directory etki alanı kullanıcısı değildir. Bu hesap, ilkeleri kurmak ve diğer kullanıcılar için yönetici veya yönetici temsilcisi (bu kullanıcıların ilkeleri yönetebilmeleri için) oluşturmak için kullanılabilir. Varsayılan olarak, Kullanıcı adı *admin* ' dir ve parola, ambarı yönetici parolasıyla aynıdır. Parola, Ranger içindeki ayarlar sayfasından güncelleştirilir.

- **Küme Yöneticisi etki alanı kullanıcısı**: Bu hesap, ambarı ve Ranger dahil olmak üzere Hadoop kümesi Yöneticisi olarak atanan bir Active Directory etki alanı kullanıcısı. Küme oluşturma sırasında bu kullanıcının kimlik bilgilerini sağlamanız gerekir. Bu Kullanıcı aşağıdaki ayrıcalıklara sahiptir:
    - Makineleri etki alanına ekleyin ve küme oluşturma sırasında belirttiğiniz OU içine yerleştirin.
    - Küme oluşturma sırasında belirttiğiniz OU içinde hizmet sorumluları oluşturun.
    - Ters DNS girişleri oluşturun.

    Diğer AD kullanıcılarının da bu ayrıcalıklara sahip olduğunu aklınızda bulabilirsiniz.

    Küme içindeki (örneğin, Temptaton), Ranger tarafından yönetilmeyen bazı uç noktaları vardır ve bu nedenle güvenli değildir. Bu uç noktaları, Küme Yöneticisi etki alanı kullanıcısı hariç tüm kullanıcılar için kilitlenir.

- **Normal**: küme oluşturma sırasında, birden çok Active Directory grubu sağlayabilirsiniz. Bu gruplardaki kullanıcılar Ranger ve ambarı ile eşitlenir. Bu kullanıcılar etki alanı kullanıcılardır ve yalnızca Ranger tarafından yönetilen uç noktalara erişime sahiptir (örneğin, Hiveserver2). Tüm RBAC ilkeleri ve denetimleri bu kullanıcılar için geçerli olacaktır.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>ESP ile HDInsight kümelerinin rolleri

HDInsight Kurumsal Güvenlik Paketi aşağıdaki rollere sahiptir:

- Küme Yöneticisi
- Küme Işleci
- Hizmet Yöneticisi
- Hizmet operatörü
- Küme kullanıcısı

**Bu rollerin izinlerini görmek için**

1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **Roller**' i seçin.
3. İzinleri görmek için mavi soru işaretini seçin:

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png" alt-text="ESP HDInsight rol izinleri" border="true":::

## <a name="open-the-ambari-management-ui"></a>Ambarı yönetimi Kullanıcı arabirimini açın

1. `https://CLUSTERNAME.azurehdinsight.net/`Clustername öğesinin Kümenizin adı olduğu yere gidin.
1. Küme Yöneticisi etki alanı Kullanıcı adı ve parolasını kullanarak, ambarı 'nda oturum açın.
1. Sağ üst köşedeki **yönetici** açılan menüsünü seçin ve ardından **ambarı Yönet**' i seçin.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png" alt-text="ESP HDInsight Apache ambarı 'nı yönetme" border="true":::

    Kullanıcı arabirimi şöyle görünür:

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png" alt-text="ESP HDInsight Apache ambarı yönetim kullanıcı arabirimi" border="true":::

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Active Directory eşitlenen etki alanı kullanıcılarını listeleyin

1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **Kullanıcılar**' ı seçin. Active Directory HDInsight kümesine eşitlenmiş tüm kullanıcıları görürsünüz.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png" alt-text="ESP HDInsight ambarı yönetimi Kullanıcı arabirimi listesi kullanıcıları" border="true":::

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Active Directory eşitlenen etki alanı gruplarını listeleyin

1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **gruplar**' ı seçin. Active Directory HDInsight kümesine eşitlenmiş tüm grupları görürsünüz.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png" alt-text="ESP HDInsight ambarı yönetimi Kullanıcı arabirimi liste grupları" border="true":::

## <a name="configure-hive-views-permissions"></a>Hive görünümlerini yapılandırma izinleri

1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **Görünümler**' i seçin.
3. Ayrıntıları göstermek için **HIVE** ' ı seçin.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png" alt-text="ESP HDInsight ambarı yönetimi UI Hive görünümleri" border="true":::

4. Hive görünümlerini yapılandırmak için **Hive görünümü** bağlantısını seçin.
5. Aşağı kaydırarak **izinler** bölümüne gidin.

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png" alt-text="ESP HDInsight ambarı yönetimi UI Hive görünümleri izinleri yapılandırma" border="true":::

6. **Kullanıcı Ekle** veya **Grup Ekle**' yi seçin ve Hive görünümlerini kullanabileceği kullanıcıları veya grupları belirtin.

## <a name="configure-users-for-the-roles"></a>Rolleri için kullanıcıları yapılandırma

 Rollerin ve izinlerinin listesini görmek için bkz. ESP ile HDInsight kümelerinin rolleri.

1. Ambarı yönetimi Kullanıcı arabirimini açın.  Bkz. [ambarı yönetimi Kullanıcı arabirimini açma](#open-the-ambari-management-ui).
2. Sol menüden **Roller**' i seçin.
3. Kullanıcıları ve grupları farklı rollere atamak için **Kullanıcı Ekle** veya **Grup Ekle** ' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

- HDInsight kümesini Kurumsal Güvenlik Paketi ile yapılandırmak için bkz. [HDInsight KÜMELERINI ESP Ile yapılandırma](./apache-domain-joined-configure-using-azure-adds.md).
- Hive ilkelerini yapılandırmak ve Hive sorgularını çalıştırmak için bkz. [HDInsight kümeleri için Apache Hive ILKELERINI ESP Ile yapılandırma](apache-domain-joined-run-hive.md).