---
title: Azure HDInsight KIMLIK Aracısı (HIB)
description: Etki alanına katılmış Apache Hadoop kümeleri için kimlik doğrulamasını basitleştirmek üzere Azure HDInsight KIMLIK Aracısı hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: c5bc5bc702dbd54bbad43aa4e1c6c8650c18e088
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863199"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight KIMLIK Aracısı (HIB)

Bu makalede, Azure HDInsight ID Broker özelliğinin nasıl ayarlanacağı ve kullanılacağı açıklanır. Bu özelliği kullanarak Apache ambarı 'na modern OAuth kimlik doğrulaması, Azure Active Directory Domain Services (Azure AD DS) üzerinde eski parola karmaları gerekmeden çok faktörlü kimlik doğrulaması zorlaması sağlayabilirsiniz.

## <a name="overview"></a>Genel Bakış

HDInsight ID Broker, aşağıdaki senaryolarda karmaşık kimlik doğrulama kurulumlarını basitleştirir:

* Kuruluşunuz, bulut kaynaklarına erişmek için kullanıcıların kimliğini doğrulamak üzere federasyon kullanır. Daha önce, HDInsight Kurumsal Güvenlik Paketi kümelerini kullanmak için şirket içi ortamınızdan Parola karması eşitlemesini Azure Active Directory (Azure AD) olarak etkinleştirmeniz gerekiyordu. Bu gereksinim bazı kuruluşlar için zor veya istenmeyen bir işlem olabilir.
* Kuruluşunuz, Apache ambarı ve diğer küme kaynaklarına Web tabanlı veya HTTP tabanlı erişim için çok faktörlü kimlik doğrulamasını zorlamak istiyor.

HDInsight ID Broker, parola karmalarını Azure AD DS eşitlemeye gerek kalmadan OAuth (modern) ile Kerberos (eski) arasında protokol geçişi sağlayan kimlik doğrulama altyapısını sağlar. Bu altyapı, HDInsight ID Broker node etkin olan bir Windows Server sanal makinesi (VM) üzerinde çalışan bileşenlerden ve küme ağ geçidi düğümleriyle birlikte oluşur.

Kuruluşunuzun ihtiyaçlarına bağlı olarak en iyi kimlik doğrulama seçeneğini öğrenmek için aşağıdaki tabloyu kullanın.

|Kimlik doğrulaması seçenekleri |HDInsight yapılandırması | Göz önünde bulundurulması gereken etkenler |
|---|---|---|
| Tam OAuth | Kurumsal Güvenlik Paketi + HDInsight KIMLIK Aracısı | En güvenli seçenek. (Çok faktörlü kimlik doğrulaması desteklenir.) Geçiş karması eşitleme gerekli *değildir* . Azure AD DS 'de Parola karması olmayan şirket içi hesaplara yönelik SSH/kinıt/keytab erişimi yok. Yalnızca bulutta bulunan hesaplar hala SSH/INIT/keytab olabilir. OAuth aracılığıyla ambarı 'na Web tabanlı erişim. , OAuth 'ı desteklemek için eski uygulamaların (örneğin, JDBC/ODBC) güncelleştirilmesini gerektirir.|
| OAuth + temel kimlik doğrulaması | Kurumsal Güvenlik Paketi + HDInsight KIMLIK Aracısı | OAuth aracılığıyla ambarı 'na Web tabanlı erişim. Eski uygulamalar temel kimlik doğrulamasını kullanmaya devam eder. Temel kimlik doğrulama erişimi için çok faktörlü kimlik doğrulaması devre dışı bırakılmalıdır. Geçiş karması eşitleme gerekli *değildir* . Azure AD DS 'de Parola karması olmayan şirket içi hesaplara yönelik SSH/kinıt/keytab erişimi yok. Yalnızca bulutta bulunan hesaplar hala SSH/INIT olabilir. |
| Tam temel kimlik doğrulama | Kurumsal Güvenlik Paketi | Şirket içi kurulumların çoğuna benzer. Azure AD DS Parola karması eşitlemesi gerekiyor. Şirket içi hesaplar SSH/kinıt yapabilir veya keytab kullanabilir. Çok faktörlü kimlik doğrulaması, yedekleme depolaması Azure Data Lake Storage 2. devre dışı bırakılmalıdır. |

Aşağıdaki diyagramda,, HDInsight ID Broker etkinleştirildikten sonra Federasyon kullanıcıları dahil tüm kullanıcılar için modern OAuth tabanlı kimlik doğrulama akışı gösterilmektedir:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="HDInsight ID broker ile kimlik doğrulama akışını gösteren diyagram." border="false":::

Bu diyagramda, istemci (yani, bir tarayıcı veya uygulama) önce OAuth belirtecini edinmesi gerekir. Ardından, bir HTTP isteğindeki ağ geçidine belirteç gösterir. Azure portal gibi diğer Azure hizmetlerinde zaten oturum açtıysanız, HDInsight kümenizde çoklu oturum açma deneyimiyle oturum açabilirsiniz.

Yalnızca temel kimlik doğrulamasını (yani, Kullanıcı adı ve parola) destekleyen birçok eski uygulama de olabilir. Bu senaryolarda, küme ağ geçitlerine bağlanmak için HTTP temel kimlik doğrulamasını kullanmaya devam edebilirsiniz. Bu kurulumda, ağ geçidi düğümlerinden doğrudan bir görüş satırı olmasını sağlamak için ağ geçidi düğümlerinden Active Directory Federasyon Hizmetleri (AD FS) (AD FS) uç noktasına ağ bağlantısı olduğundan emin olmanız gerekir.

Aşağıdaki diyagramda, Federasyon kullanıcıları için temel kimlik doğrulama akışı gösterilmektedir. İlk olarak, ağ geçidi, [Ropc akışını](../../active-directory/develop/v2-oauth-ropc.md)kullanarak kimlik doğrulamasını tamamlamaya çalışır. Azure AD ile eşitlenen Parola karması yoksa, AD FS uç noktasının keşfedilmesinden ve AD FS uç noktasına erişerek kimlik doğrulamasının tamamlanmasını geri döner.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Temel kimlik doğrulaması ile mimariyi gösteren diyagram." border="false":::

## <a name="enable-hdinsight-id-broker"></a>HDInsight KIMLIK Aracısı 'nı etkinleştir

HDInsight ID Broker etkin bir Kurumsal Güvenlik Paketi kümesi oluşturmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Kurumsal Güvenlik Paketi kümesi için temel oluşturma adımlarını izleyin. Daha fazla bilgi için bkz. [Kurumsal güvenlik paketi HDInsight kümesi oluşturma](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. **HDıNSIGHT kimlik Broker 'ı etkinleştir**' i seçin.

HDInsight ID Broker özelliği kümeye bir ek VM ekler. Bu VM, HDInsight ID Broker düğümüdür ve kimlik doğrulamasını desteklemek için sunucu bileşenleri içerir. HDInsight ID Broker düğümü, Azure AD DS etki alanına katılmış etki alanıdır.

:::image type="content" source="./media/identity-broker/identity-broker-enable.png" alt-text="HDInsight ID Broker 'ı etkinleştirme seçeneğini gösteren diyagram." border="true":::

### <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarını kullanma

`idbrokernode`Şablonunuzun işlem profiline aşağıdaki özniteliklere sahip adlı yeni bir rol eklerseniz, küme HDıNSIGHT kimlik Aracısı düğümü etkinleştirilmiş olarak oluşturulur:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

ARM şablonunun tüm bir örneğini görmek için lütfen [burada](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)yayınlanan şablona bakın.


## <a name="tool-integration"></a>Araç tümleştirmesi

HDInsight araçları, OAuth ile yerel olarak destek için güncelleştirilir. Kümelere modern OAuth tabanlı erişim için bu araçları kullanın. HDInsight [IntelliJ eklentisi](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) Scala gibi Java tabanlı uygulamalarda kullanılabilir. [Visual Studio Code Için Spark ve Hive araçları](../hdinsight-for-vscode.md) , pyspark ve Hive işleri için kullanılabilir. Araçlar hem Batch hem de etkileşimli işleri destekler.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS Parola karması olmadan SSH erişimi

|SSH seçenekleri |Göz önünde bulundurulması gereken etkenler |
|---|---|
| Yerel VM hesabı (örneğin, sshuser) | Bu hesabı küme oluşturma zamanında sağladınız. Bu hesap için Kerberos kimlik doğrulaması yok. |
| Yalnızca bulut hesabı (örneğin, alice@contoso.onmicrosoft.com ) | Parola karması Azure AD DS kullanılabilir. Kerberos kimlik doğrulaması, SSH Kerberos aracılığıyla mümkündür. |
| Şirket içi hesap (örneğin, alice@contoso.com ) | SSH Kerberos kimlik doğrulaması yalnızca Azure AD DS 'de bir parola karması varsa mümkündür. Aksi takdirde, bu kullanıcı kümeye SSH ekleyemez. |

Etki alanına katılmış bir VM 'ye SSH eklemek veya komutu çalıştırmak için `kinit` bir parola sağlamanız gerekir. SSH Kerberos kimlik doğrulaması, karmasının Azure AD DS kullanılabilir olmasını gerektirir. SSH 'yi yalnızca yönetim senaryoları için kullanmak istiyorsanız yalnızca bir bulut hesabı oluşturabilir ve bunu kümeye SSH için kullanabilirsiniz. Diğer şirket içi kullanıcılar, Azure AD DS 'de Parola karması olmadan ambarı veya HDInsight araçlarını veya HTTP temel kimlik doğrulamasını kullanmaya devam edebilir.

Kuruluşunuz parola karmalarını Azure AD DS eşitlemiyor, en iyi uygulama olarak, Azure AD 'de yalnızca bulutta bulunan bir kullanıcı oluşturun. Ardından kümeyi oluştururken Küme Yöneticisi olarak atayın ve bunu yönetim amacıyla kullanın. VM 'ye SSH aracılığıyla kök erişimi almak için kullanabilirsiniz.

Kimlik doğrulama sorunlarını gidermek için [bu kılavuza](./domain-joined-authentication-issues.md)bakın.

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>HDInsight ID broker ile bir HDInsight ağ geçidine bağlanmak için OAuth kullanan istemciler

HDInsight ID Broker kurulumunda, ağ geçidine bağlanan özel uygulamalar ve istemciler, önce gerekli OAuth belirtecini almak üzere güncelleştirilebilen olabilir. Aşağıdaki bilgilerle belirteci almak için [Bu belgedeki](../../storage/common/storage-auth-aad-app.md) adımları izleyin:

*    OAuth Kaynak URI 'si: `https://hib.azurehdinsight.net` 
*   AppID: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*    İzin: (ad: Cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

OAuth belirtecini aldıktan sonra, bunu küme ağ geçidine yönelik HTTP isteğinin yetkilendirme üstbilgisinde kullanın (örneğin, https:// <clustername> -int.azurehdinsight.net). Apache Livy API 'sine örnek bir kıvrımlı komut aşağıdaki örnekteki gibi görünebilir:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Beeline ve Livy 'ı kullanmak için, istemcinizi OAuth kullanmak üzere ayarlamak ve kümeye bağlanmak için [burada](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) sağlanan örnek kodlarını da izleyebilirsiniz.

## <a name="faq"></a>SSS
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>AAD 'de HDInsight tarafından hangi uygulama oluşturuldu?
Her küme için, üçüncü taraf bir uygulama, ıdentifieruri (gibi) olarak küme URI 'si ile AAD 'ye kaydedilir `https://clustername.azurehdinsight.net` .

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>Kullanıcılardan, HIB etkin kümeleri kullanmadan önce neden onay istenir?
AAD 'de, kullanıcıların kimliğini doğrulayabilmesi veya verilere erişim izni vermeden önce tüm üçüncü taraf uygulamalar için izin gerekir.

### <a name="can-the-consent-be-approved-programatically"></a>Onay program aracılığıyla onaylansın mı?
Microsoft Graph API, onayı otomatik hale getirmenizi sağlar. [API belgelerine](/graph/api/resources/oauth2permissiongrant) , onayı otomatik hale getirmeye yönelik dizi:

* Bir uygulamayı kaydedin ve App. ReadWrite verin. Microsoft Graph erişmek için uygulamaya tüm izinler verin
* Bir küme oluşturulduktan sonra, küme uygulamasını tanımlayıcı URI 'sine göre sorgulama
* Uygulama için izin kaydetme

Küme silindiğinde, HDInsight uygulamayı siler ve herhangi bir onayı Temizleme gereksinimi yoktur.

 


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Active Directory Domain Services kullanarak Kurumsal Güvenlik Paketi bir HDInsight kümesi yapılandırma](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory kullanıcılarını HDInsight kümesine eşitleme](../hdinsight-sync-aad-users-to-cluster.md)
* [Küme performansını izleme](../hdinsight-key-scenarios-to-monitor.md)