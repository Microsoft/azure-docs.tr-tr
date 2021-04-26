---
title: Apache Ranger 'da ilke Apache Hive-Azure HDInsight
description: Azure HDInsight hizmetinde Hive için Kurumsal Güvenlik Paketi Apache Ranger ilkelerini yapılandırma hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 15c406576b373577a2a3a50108acad7ccbf36699
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863267"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Kurumsal Güvenlik Paketi ile HDInsight içinde Apache Hive ilkelerini yapılandırma

Apache Hive için Apache Ranger ilkelerini yapılandırma hakkında bilgi edinin. Bu makalede hivesampletable erişimini kısıtlamak için iki Ranger ilkesi oluşturacaksınız. hivesampletable, HDInsight kümelerine sahiptir. İlkeleri yapılandırdıktan sonra, HDInsight 'taki Hive tablolarına bağlanmak için Excel ve ODBC sürücüsünü kullanırsınız.

## <a name="prerequisites"></a>Önkoşullar

* Kurumsal Güvenlik Paketi olan bir HDInsight kümesi. Bkz. [HDInsight KÜMELERINI ESP Ile yapılandırma](./apache-domain-joined-configure-using-azure-adds.md).
* Enterprise, Office 2016, Office 2013 Professional Plus, Excel 2013 tek başına veya Office 2010 Professional Plus için Microsoft 365 uygulamalar içeren bir iş istasyonu.

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger Yönetici Arabirimine bağlanma
**Ranger Yönetici Arabirimine bağlanmak için**

1. Bir tarayıcıdan, `https://CLUSTERNAME.azurehdinsight.net/Ranger/` clustername 'in Kümenizin adı olduğu yerde, Ranger Yönetici Kullanıcı arabirimine gidin.

   > [!NOTE]  
   > Ranger Apache Hadoop kümeden farklı kimlik bilgileri kullanıyor. Önbelleğe alınmış Hadoop kimlik bilgilerini kullanan tarayıcıları engellemek için, Ranger Yönetici Kullanıcı arabirimine bağlanmak üzere yeni bir InPrivate tarayıcı penceresi kullanın.

2. Küme yöneticisi etki alanı kullanıcı adı ve parolasını kullanarak oturum açın:

    :::image type="content" source="./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png" alt-text="HDInsight ESP Ranger giriş sayfası" border="true":::

    Ranger şu an için yalnızca Yarn ve Hive ile birlikte çalışmaktadır.

## <a name="create-domain-users"></a>Etki alanı kullanıcılarını oluşturma

Hiveruser1 ve hiveuser2 oluşturma hakkında bilgi için bkz. [ESP Ile HDInsight kümesi oluşturma](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). Bu makalede iki kullanıcı hesabını kullanırsınız.

## <a name="create-ranger-policies"></a>Ranger ilkelerini oluşturma

Bu bölümde, hivesampletable 'a erişmek için iki Ranger ilkesi oluşturacaksınız. Farklı sütun kümelerine select izni vereceksiniz. Her iki kullanıcı da [, ESP ile bir HDInsight kümesi oluşturma](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)kullanılarak oluşturulmuştur. Sonraki bölümde, Excel 'de iki ilkeyi test edeceksiniz.

**Ranger ilkeleri oluşturmak için**

1. Ranger Yönetici Arabirimini açın. Bkz. Apache Ranger Yönetici Arabirimine bağlanma.
2. **Hive** altında **CLUSTERNAME_Hive**' yi seçin. Önceden yapılandırılmış iki ilke göreceksiniz.
3. **Yeni Ilke Ekle**' yi seçin ve ardından aşağıdaki değerleri girin:

    |Özellik |Değer |
    |---|---|
    |İlke Adı|Read-hivesampletable-tümü|
    |Hive veritabanı|default|
    |tablo|hivesampletable|
    |Hive sütunu|*|
    |Kullanıcı Seçin|hiveuser1 kullanıcısının|
    |İzinler|seçin|

    :::image type="content" source="./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png" alt-text="HDıNSIGHT ESP Ranger Hive ilkeleri yapılandırır" border="true":::.

    > [!NOTE]  
    > Select User alanında etki alanı kullanıcısı yoksa Ranger’ın AAD ile eşitlenmesi için birkaç dakika bekleyin.

4. İlkeyi kaydetmek için **Ekle** ' yi seçin.

5. Son iki adımı tekrarlayarak aşağıdaki özelliklere sahip yeni bir ilke oluşturun:

    |Özellik |Değer |
    |---|---|
    |İlke Adı|Read-hivesampletable-devicemake|
    |Hive veritabanı|default|
    |tablo|hivesampletable|
    |Hive sütunu|ClientID, devicemake|
    |Kullanıcı Seçin|hiveuser2|
    |İzinler|seçin|

## <a name="create-hive-odbc-data-source"></a>Hive ODBC veri kaynağı oluşturma

Talimatlara [Hive ODBC veri kaynağı oluşturma](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) sayfasından erişebilirsiniz.  

 | Özellik  |Açıklama |
 | --- | --- |
 | Data Source Name | Veri kaynağınız için bir ad verin |
 | Ana bilgisayar | CLUSTERNAME.azurehdinsight.net girin. Örnek: HDIKumesi.azurehdinsight.net |
 | Bağlantı noktası | **443** yazın. (Önceden 563 olan bu bağlantı noktası 443 olarak değiştirilmiştir.) |
 | Veritabanı | **Varsayılanı** kullanın. |
 | Hive Server Type | **Hive Server 2**’yi seçin |
 | Mechanism | **Azure HDInsight Service**’i seçin |
 | HTTP Path | Boş bırakın. |
 | User Name | hiveuser1@contoso158.onmicrosoft.com yazın. Farklı olduğunda, etki alanı adını güncelleştirin. |
 | Parola | hiveuser1 kullanıcısının parolasını girin. |

Veri kaynağını kaydetmeden önce **Test**’e tıklayın.

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight’tan Excel’e veri aktarma

Son bölümde iki ilke yapılandırdınız.  hiveuser1 tüm sütunlarda select iznine, hiveuser2 ise iki sütunda select iznine sahiptir. Bu bölümde verileri Excel'e aktarmak için iki kullanıcının kimliğine bürüneceksiniz.

1. Excel’de yeni veya mevcut bir çalışma kitabını açın.

1. ODBC 'den başlatmak için **veri** sekmesinden   >    >  **ODBC 'den**  diğer kaynaklardan veri al ' a gidin.

    :::image type="content" source="./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png" alt-text="Veri bağlantısı açma Sihirbazı" border="true":::

1. Aşağı açılan listeden, son bölümde oluşturduğunuz veri kaynağı adını seçin ve ardından **Tamam**' ı seçin.

1. İlk kullanım için bir **ODBC sürücüsü** iletişim kutusu açılır. Sol menüden **Windows** ' u seçin. Ardından, **Gezgin** penceresini açmak için **Bağlan** ' ı seçin.

1. **Veritabanı ve Tablo Seç** iletişim kutusunun açılmasını bekleyin. Bu işlem birkaç saniye sürebilir.

1. **Hivesampletable**' ı seçin ve ardından **İleri**' yi seçin.

1. **Son**'u seçin.

1. **Verileri İçeri Aktar** iletişim kutusunda sorguyu değiştirebilir veya belirtebilirsiniz. Bunu yapmak için **Özellikler**' i seçin. Bu işlem birkaç saniye sürebilir.

1. **Tanım** sekmesini seçin. Komut metni:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   Tanımladığınız Ranger ilkelerine göre hiveuser1 kullanıcısı tüm sütunlarda select iznine sahiptir.  Bu sorgu, hiveuser1's kimlik bilgileriyle çalışır, ancak bu sorgu hiveuser2's kimlik bilgileriyle çalışmaz.

1. **Tamam ' ı** seçerek bağlantı özellikleri iletişim kutusunu kapatın.

1. **Verileri Içeri aktar** iletişim kutusunu kapatmak için **Tamam ' ı** seçin.  

1. hiveuser1 kullanıcısının parolasını tekrar girin ve **Tamam**’a tıklayın. Verilerin Excel’e aktarılması birkaç saniye sürer. İşlem tamamlandığında 11 ' den fazla veri sütunu görürsünüz.

İkinci ilkeyi test etmek için (Read-hivesampletable-devicemake), son bölümde oluşturduğunuz

1. Excel'de yeni bir sayfa ekleyin.
2. Verileri içeri aktarmak için son yordamı uygulayın.  Yaptığınız tek değişiklik, hiveuser1's yerine hiveuser2's kimlik bilgilerini kullanmaktır. Bu, hiveuser2 yalnızca iki sütunu görme iznine sahip olduğu için başarısız olur. Şu hatayı alacaksınız:

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. Verileri içe aktarmak için aynı yordamı uygulayın. Bu sefer hiveuser2 kullanıcısının kimlik bilgilerini kullanın ve select deyimi için:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    Yeni değer:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    İşlem tamamlandığında, verilerin içeri aktarıldığı iki sütununu görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

* HDInsight kümesini Kurumsal Güvenlik Paketi ile yapılandırmak için bkz. [HDInsight KÜMELERINI ESP Ile yapılandırma](./apache-domain-joined-configure-using-azure-adds.md).
* HDInsight kümesini ESP ile yönetmek için bkz. [HDInsight KÜMELERINI ESP Ile yönetme](apache-domain-joined-manage.md).
* HDInsight kümelerinde ESP kullanarak Hive sorguları çalıştırmak için bkz. [HDInsight Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Hive JDBC kullanarak Hive bağlamak için bkz [. HIVE JDBC sürücüsünü kullanarak Azure HDInsight 'ta Apache Hive bağlama](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Excel 'i Hive ODBC kullanarak Hadoop 'a bağlamak için bkz [. Microsoft HIVE ODBC sürücüsü ile Apache Hadoop Excel 'e bağlanma](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Excel 'i Power Query kullanarak Hadoop 'a bağlamak için bkz. [Power Query kullanarak Excel 'i Apache Hadoop bağlama](../hadoop/apache-hadoop-connect-excel-power-query.md)