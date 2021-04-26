---
title: Kullanıcıları, ambarı görünümleri için yetkilendirme-Azure HDInsight
description: ESP özellikli HDInsight kümeleri için ambarı Kullanıcı ve grup izinlerini yönetme.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 44f98692131070940514498b6bf648936bea0806
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872243"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Kullanıcıları Apache Ambari Görünümleri için yetkilendirme

[Kurumsal güvenlik paketi (ESP) etkin HDInsight kümeleri](./domain-joined/hdinsight-security-overview.md) , Azure Active Directory tabanlı kimlik doğrulaması da dahil olmak üzere kurumsal düzeyde yetenekler sağlar. Kümeye erişim sağlanmış olan Azure AD gruplarına eklenen [Yeni kullanıcıları eşitleyebilir](hdinsight-sync-aad-users-to-cluster.md) ve bu kullanıcıların belirli eylemleri gerçekleştirmesine izin verebilirsiniz. [Apache ambarı](https://ambari.apache.org/) 'nda kullanıcılar, gruplar ve izinlerle çalışma, hem ESP HDInsight kümeleri hem de standart HDInsight kümeleri için desteklenir.

Active Directory Kullanıcılar, etki alanı kimlik bilgilerini kullanarak küme düğümlerinde oturum açabilirler. Ayrıca, [ton](https://gethue.com/), ambarı GÖRÜNÜMLERI, ODBC, JDBC, POWERSHELL ve REST API 'ler gibi onaylanan diğer uç noktalarla küme etkileşimlerinin kimliğini doğrulamak için etki alanı kimlik bilgilerini de kullanabilirler.

> [!WARNING]  
> Linux tabanlı HDInsight kümenizdeki ambarı izleme (hdinsightwatchdog) parolasını değiştirmeyin. Parola değiştirme, betik eylemlerini kullanma veya kümeniz ile ölçeklendirme işlemleri gerçekleştirme yeteneğini keser.

Daha önce yapmadıysanız, yeni bir ESP kümesi sağlamak için [Bu yönergeleri](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) izleyin.

## <a name="access-the-ambari-management-page"></a>Ambarı yönetim sayfasına erişin

[Apache ambarı Web Kullanıcı arabirimindeki](hdinsight-hadoop-manage-ambari.md) **ambarı yönetim sayfasına** ulaşmak için, sayfasına gidin `https://CLUSTERNAME.azurehdinsight.net` . Kümeyi oluştururken tanımladığınız küme yönetici kullanıcı adını ve parolasını girin. Ardından, ambarı panosundan **Yönetim** menüsünün altındaki **ambarı Yönet** ' i seçin:

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png" alt-text="Apache ambarı Pano Yönetimi":::

## <a name="add-users"></a>Kullanıcı ekle

### <a name="add-users-through-the-portal"></a>Portalı kullanarak Kullanıcı ekleme

1. Yönetim sayfasından **Kullanıcılar**' ı seçin.

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png" alt-text="Apache ambarı yönetim sayfası kullanıcıları":::

1. **+ Yerel kullanıcı oluştur**' u seçin.

1. **Kullanıcı adı** ve **parola** belirtin. **Kaydet**' i seçin.

### <a name="add-users-through-powershell"></a>PowerShell aracılığıyla Kullanıcı ekleme

Aşağıdaki değişkenleri, `CLUSTERNAME` `NEWUSER` ve `PASSWORD` değerlerini uygun değerlerle değiştirerek düzenleyin.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Kıvrımlı aracılığıyla Kullanıcı ekleme

,, `CLUSTERNAME` `ADMINPASSWORD` `NEWUSER` Ve `USERPASSWORD` değerlerini uygun değerlerle değiştirerek aşağıdaki değişkenleri düzenleyin. Betik, Bash ile yürütülecek şekilde tasarlanmıştır. Bir Windows komut istemi için küçük değişiklikler yapmanız gerekir.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Apache Hive görünümlerine izin verme

Ambarı, diğerleri arasında [Apache Hive](https://hive.apache.org/) ve [Apache tez](https://tez.apache.org/)için görünüm örnekleriyle gelir. Bir veya daha fazla Hive görünümü örneğine erişim vermek için, **ambarı yönetim sayfasına** gidin.

1. Yönetim sayfasından, sol taraftaki **Görünümler** menü başlığı altında bulunan **Görünümler** bağlantısını seçin.

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png" alt-text="Apache ambarı görünümleri görünüm bağlantıları":::

2. Görünümler sayfasında **HIVE** satırını genişletin. Hive hizmeti kümeye eklendiğinde oluşturulan bir varsayılan Hive görünümü vardır. Ayrıca, gerektiğinde daha fazla Hive görünüm örneği de oluşturabilirsiniz. Hive görünümü seçin:

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png" alt-text="HDInsight görünümleri-Apache Hive görünümü":::

3. Görünüm sayfasının alt kısmına doğru ilerleyin. *İzinler* bölümünde, etki alanı kullanıcılarının görünüme izinleri vermek için iki seçeneğiniz vardır:

Bu :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png" alt-text="kullanıcılara bu kullanıcılara"::: izin verme izni **ver**

**Bu gruplara Izin ver** :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png" alt-text="Bu gruplara izin ver":::

1. Kullanıcı eklemek için **Kullanıcı Ekle** düğmesini seçin.

   * Kullanıcı adını yazmaya başlayın ve önceden tanımlanmış adların açılan listesini görürsünüz.

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png" alt-text="Apache ambarı Kullanıcı otomatik tamamlanır":::

   * Kullanıcı adını seçin veya yazın. Bu Kullanıcı adını yeni bir kullanıcı olarak eklemek için **Yeni** düğmesini seçin.

   * Değişikliklerinizi kaydetmek için **mavi onay kutusunu** seçin.

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png" alt-text="Apache ambarı Kullanıcı izinleri verme":::

1. Bir grup eklemek için **Grup Ekle** düğmesini seçin.

   * Grup adını yazmaya başlayın. Var olan bir grup adını seçme veya yeni bir grup ekleme işlemi, Kullanıcı ekleme ile aynıdır.
   * Değişikliklerinizi kaydetmek için **mavi onay kutusunu** seçin.

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png" alt-text="Apache ambarı izin verme izinleri":::

Kullanıcıları doğrudan bir görünüme eklemek, bu görünümü kullanmak için bir kullanıcıya izin atamak istediğinizde, ancak ek izinlere sahip bir grubun üyesi olmasını istemediğiniz durumlarda faydalıdır. Yönetim yükü miktarını azaltmak için gruplara izin atama daha kolay olabilir.

## <a name="grant-permissions-to-apache-tez-views"></a>Apache TEZ görünümlerine izin verme

[Apache tez](https://tez.apache.org/) görünüm örnekleri, kullanıcıların [Apache Hive](https://hive.apache.org/) sorguları ve [Apache Pig](https://pig.apache.org/) betikleri tarafından gönderilen tüm tez işlerini izlemelerine ve hatalarını ayıklamasına olanak tanır. Küme sağlandığında oluşturulan bir varsayılan tez görünüm örneği vardır.

Bir tez görünümü örneğine kullanıcılar ve gruplar atamak için, görünümler sayfasındaki **tez** satırını daha önce açıklandığı gibi genişletin.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png" alt-text="HDInsight görünümleri-Apache Tez görünümü":::

Kullanıcı veya grup eklemek için önceki bölümde 3-5 arasındaki adımları yineleyin.

## <a name="assign-users-to-roles"></a>Rollere kullanıcı atama

Kullanıcılar ve gruplar için, erişim izinleri azaltma sırasıyla listelenen beş güvenlik rolü vardır:

* Küme Yöneticisi
* Küme Işleci
* Hizmet Yöneticisi
* Hizmet operatörü
* Küme kullanıcısı

Rolleri yönetmek için, **ambarı yönetimi sayfasına** gidin, ardından sol taraftaki *kümeler* menü grubunda bulunan **Roller** bağlantısını seçin.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png" alt-text="Apache ambarı roller menü bağlantıları":::

Her role verilen izinlerin listesini görmek için Roller sayfasındaki **Roller** tablosu üst bilgisinin yanındaki mavi soru işaretine tıklayın.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png" alt-text="Apache ambarı roller menü bağlantısı izinleri":::

Bu sayfada, kullanıcılar ve gruplar için rolleri yönetmek için kullanabileceğiniz iki farklı görünüm vardır: blok ve liste.

### <a name="block-view"></a>Görünümü engelle

Blok görünümü her bir rolü kendi satırında görüntüler ve daha önce açıklandığı gibi bu **kullanıcılara roller ata** ve **Bu gruplara roller ata** seçeneklerini sunar.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png" alt-text="Apache ambarı rolleri blok görünümü":::

### <a name="list-view"></a>Liste görünümü

Liste görünümü iki kategoride Hızlı Düzenle özellikleri sağlar: kullanıcılar ve gruplar.

* Liste görünümünün kullanıcılar kategorisi, tüm kullanıcıların bir listesini görüntüleyerek, açılan listeden her bir kullanıcı için bir rol seçmenize olanak sağlar.

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png" alt-text="Apache ambarı rol listesi görünümü-kullanıcılar":::

* Liste görünümünün gruplar kategorisi, tüm grupları ve her gruba atanan rolü görüntüler. Örneğimizde, grupların listesi, kümenin etki alanı ayarlarının **Erişim Kullanıcı grubu** özelliğinde BELIRTILEN Azure AD gruplarından eşitlenir. Bkz. [ESP özellikli HDInsight kümesi oluşturma](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png" alt-text="Apache ambarı rol listesi görünümü-gruplar":::

    Yukarıdaki görüntüde, "hiveusers" grubuna *küme Kullanıcı* rolü atanır. Bu, bu grubun kullanıcılarının hizmet yapılandırmasını ve küme ölçümlerini görüntülemesine izin veren, ancak değiştirmediğinden yalnızca bir salt okuma rolüdür.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Yalnızca bir görüntüleme kullanıcısı olarak ambarı 'nda oturum açın

"Hiveuser1 kullanıcısının" Azure AD etki alanı kullanıcımızı Hive ve tez görünümlerine atamamız gerekir. Ambarı Web Kullanıcı arabirimini başlatırken ve bu kullanıcının etki alanı kimlik bilgilerini (e-posta biçiminde ve parola olarak Azure AD Kullanıcı adı) girerken, Kullanıcı, ambarı görünümleri sayfasına yönlendirilir. Kullanıcı, buradan erişilebilir bir görünüm seçebilir. Kullanıcı Pano, hizmetler, konaklar, uyarılar veya yönetici sayfaları dahil olmak üzere sitenin başka bir bölümünü ziyaret edemez.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png" alt-text="Yalnızca görünümleri olan Apache ambarı kullanıcısı":::

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Bir küme kullanıcısı olarak ambarı 'nda oturum açın

"Hiveuser2" Azure AD etki alanı kullanıcımızı *küme Kullanıcı* rolüne atadık. Bu rol panoya ve tüm menü öğelerine erişebiliyor. Bir küme kullanıcısı bir yöneticiden daha az izin verilen seçeneklere sahiptir. Örneğin, hiveuser2 her bir hizmetin yapılandırmasını görüntüleyebilir, ancak düzenleyemezsiniz.

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png" alt-text="Apache ambarı Pano görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta ESP ile Apache Hive ilkelerini yapılandırma](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight kümelerini yönetme](./domain-joined/apache-domain-joined-manage.md)
* [HDInsight 'ta Apache Hadoop ile Apache Hive görünümünü kullanma](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Azure AD kullanıcılarını kümeyle eşitler](hdinsight-sync-aad-users-to-cluster.md)
* [Apache ambarı 'nı kullanarak HDInsight kümelerini yönetme REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)