---
title: Giden ağ trafiği kısıtlamasını Yapılandırma-Azure HDInsight
description: Azure HDInsight kümeleri için giden ağ trafiği kısıtlamasını yapılandırma hakkında bilgi edinin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 8f6959eb6f9d17a368e7df7b95ecc511d0396f87
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621448"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Güvenlik duvarını kullanarak Azure HDInsight kümeleri için giden ağ trafiği yapılandırma

Bu makalede, Azure Güvenlik Duvarı 'nı kullanarak HDInsight kümenizdeki giden trafiği güvenli hale getirmeye yönelik adımlar sağlanmaktadır. Aşağıdaki adımlarda, var olan bir küme için bir Azure Güvenlik Duvarı yapılandırdığınızı varsaymaktadır. Yeni bir küme dağıtıyorsanız ve güvenlik duvarının arkasındaysa, önce HDInsight kümenizi ve alt ağını oluşturun ve ardından bu kılavuzdaki adımları izleyin.

## <a name="background"></a>Arka plan

Azure HDInsight kümeleri normalde kendi sanal ağınıza dağıtılır. Kümede, ağ erişiminin düzgün çalışması için bu sanal ağın dışındaki hizmetlere bağımlılıkları vardır.

Gelen trafik gerektiren birkaç bağımlılık vardır. Gelen yönetim trafiği bir güvenlik duvarı cihazından gönderilemez. Bu trafiğin kaynak adresleri bilinmektedir ve [burada](hdinsight-management-ip-addresses.md)yayımlanır. Ayrıca, kümelerdeki gelen trafiğin güvenliğini sağlamak için bu bilgilerle ağ güvenlik grubu (NSG) kuralları da oluşturabilirsiniz.

HDInsight giden trafik bağımlılıkları, her geride statik IP adreslerine sahip olmayan FQDN 'Ler ile neredeyse tamamen tanımlanmıştır. Statik adreslerin olmaması, ağ güvenlik gruplarının (NSG 'ler) bir kümeden giden trafiği kilitlemek için kullanılamayacağı anlamına gelir. Adresler, geçerli ad çözümlemesine göre kuralları ayarlayamayacak ve NSG kurallarını ayarlamak için bu kuralın kullanılmasına yetecek kadar sık değişir.

Giden adreslerin güvenliğini sağlamaya yönelik çözüm, etki alanı adlarına göre giden trafiği denetleyebilirler bir güvenlik duvarı cihazı kullanmaktır. Azure Güvenlik Duvarı, giden HTTP ve HTTPS trafiğini hedef veya [FQDN ETIKETLERININ](https://docs.microsoft.com/azure/firewall/fqdn-tags)FQDN 'sine göre kısıtlayabilir.

## <a name="configuring-azure-firewall-with-hdinsight"></a>HDInsight ile Azure Güvenlik duvarını yapılandırma

Azure Güvenlik Duvarı ile mevcut HDInsight 'ınızdan çıkış kilitleme adımlarının Özeti şunlardır:

1. Güvenlik duvarı oluşturun.
1. Güvenlik duvarına uygulama kuralları ekleme
1. Güvenlik duvarına ağ kuralları ekleyin.
1. Yönlendirme tablosu oluşturun.

### <a name="create-new-subnet"></a>Yeni alt ağ oluştur

Kümenizin bulunduğu sanal ağda **AzureFirewallSubnet** adlı bir alt ağ oluşturun.

### <a name="create-a-new-firewall-for-your-cluster"></a>Kümeniz için yeni bir güvenlik duvarı oluşturma

**Test-FW01** adlı bir güvenlik duvarı oluşturarak **güvenlik** duvarını [öğreticiden dağıtma: Azure Portal kullanarak Azure Güvenlik Duvarı dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)adımlarını uygulayın.

### <a name="configure-the-firewall-with-application-rules"></a>Uygulama kurallarıyla güvenlik duvarını yapılandırma

Kümenin önemli iletişimleri göndermesini ve almasını sağlayan bir uygulama kuralı koleksiyonu oluşturun.

1. Azure portal yeni güvenlik duvarını **Test-FW01** seçin.

1. **Ayarlar** > **kurallar** > **uygulama kuralı koleksiyonu** >  **+ uygulama kuralı koleksiyonu Ekle**' ye gidin.

    ![Başlık: uygulama kuralı koleksiyonu Ekle](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. **Uygulama kuralı koleksiyonu Ekle** ekranında, aşağıdaki bilgileri sağlayın:

    **Üst bölüm**

    | Özellik|  Değer|
    |---|---|
    |Ad| FwAppRule|
    |Öncelik|200|
    |Eylem|Allow|

    **FQDN etiketleri bölümü**

    | Ad | Kaynak adres | FQDN etiketi | Notlar |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate ve HDInsight | HDI Hizmetleri için gerekli |

    **Hedef FQDN bölümü**

    | Ad | Kaynak adresler | Protokol: bağlantı noktası | Hedef FQDN 'ler | Notlar |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https: 443 | login.windows.net | Windows oturum açma etkinliğine izin verir |
    | Rule_3 | * | https: 443 | login.microsoftonline.com | Windows oturum açma etkinliğine izin verir |
    | Rule_4 | * | https: 443, http: 80 | storage_account_name. blob. Core. Windows. net | `storage_account_name` değerini gerçek depolama hesabı adınızla değiştirin. Kümeniz, ile desteklenir ve ardından, için bir kural ekleyin. YALNıZCA HTTPS bağlantılarını kullanmak için depolama hesabında ["güvenli aktarım gerekli"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) özelliğinin etkinleştirildiğinden emin olun. |

   ![Başlık: uygulama kuralı koleksiyonu ayrıntılarını girin](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. **Add (Ekle)** seçeneğini belirleyin.

### <a name="configure-the-firewall-with-network-rules"></a>Ağ kurallarıyla güvenlik duvarını yapılandırma

HDInsight kümenizi doğru şekilde yapılandırmak için ağ kuralları oluşturun.

1. Önceki adımdan devam etmek için **ağ kuralı koleksiyonu** >  **+ ağ kuralı koleksiyonu Ekle**' ye gidin.

1. **Ağ kuralı koleksiyonu Ekle** ekranında, aşağıdaki bilgileri sağlayın:

    **Üst bölüm**

    | Özellik|  Değer|
    |---|---|
    |Ad| FwNetRule|
    |Öncelik|200|
    |Eylem|Allow|

    **IP adresleri bölümü**

    | Ad | Protokol | Kaynak adresler | Hedef adresler | Hedef bağlantı noktaları | Notlar |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | PROTOKOLLERINDEN | * | * | 123 | Zaman hizmeti |
    | Rule_2 | Herhangi biri | * | DC_IP_Address_1, DC_IP_Address_2 | * | Kurumsal Güvenlik Paketi (ESP) kullanıyorsanız, IP adresleri bölümüne, ESP kümeleri için AAD-DS ile iletişime izin veren bir ağ kuralı ekleyin. Etki alanı denetleyicilerinin IP adreslerini portaldaki AAD-DS bölümünde bulabilirsiniz |
    | Rule_3 | TCP | * | Data Lake Storage hesabınızın IP adresi | * | Azure Data Lake Storage kullanıyorsanız, ADLS 1. ve Gen2 ile ilgili bir SNı sorunu gidermek için IP adresleri bölümüne bir ağ kuralı ekleyebilirsiniz. Bu seçenek, trafiği, büyük veri yükleri için daha yüksek maliyetlere neden olabilecek güvenlik duvarı 'na yönlendirir, ancak trafik günlüğe kaydedilir ve güvenlik duvarı günlüklerinde denetlenebilir. Data Lake Storage hesabınızın IP adresini belirleme. FQDN 'yi bir IP adresine çözümlemek için `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` gibi bir PowerShell komutu kullanabilirsiniz.|
    | Rule_4 | TCP | * | * | 12000 | Seçim Log Analytics kullanıyorsanız, Log Analytics çalışma alanınız ile iletişimi etkinleştirmek için IP adresleri bölümünde bir ağ kuralı oluşturun. |

    **Hizmet etiketleri bölümü**

    | Ad | Protokol | Kaynak Adresler | Hizmet etiketleri | Hedef bağlantı noktaları | Notlar |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | HDInsight alt ağında SQL Server için hizmet uç noktaları yapılandırmadığınız ve güvenlik duvarını atlayacak olan SQL trafiğini, SQL trafiğini günlüğe kaydetme ve denetleme gibi bir ağ kuralını yapılandırın. |

   ![Başlık: uygulama kuralı koleksiyonu girin](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. **Add (Ekle)** seçeneğini belirleyin.

### <a name="create-and-configure-a-route-table"></a>Rota tablosu oluşturma ve yapılandırma

Aşağıdaki girişlerle bir yol tablosu oluşturun:

* [Sistem durumu ve yönetim hizmetlerinden](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) gelen tüm IP adresleri: bir sonraki atlama türüne sahip tüm bölgeler **.**

* Kümenin [sistem durumu ve yönetim hizmetlerinden](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) oluşturulduğu bölge IÇIN iki IP adresi: **Internet**'in bir sonraki atlama türüne sahip belirli bölgeler.

* 0\.0.0.0/0 IP adresi için bir Sanal Gereç yolu, sonraki atlama olan Azure Güvenlik Duvarı özel IP adresiniz.

Örneğin, "Doğu ABD" ABD bölgesinde oluşturulan bir kümenin yol tablosunu yapılandırmak için aşağıdaki adımları kullanın:

1. Azure Güvenlik Duvarı **testinizi seçin-FW01**. **Genel bakış** sayfasında LISTELENEN **özel IP adresini** kopyalayın. Bu örnekte, **10.0.2.4 örnek adresini**kullanacağız.

1. Ardından, **tüm hizmetler** > **ağ** > **yönlendirme tabloları** ve **yol tablosu oluştur**' a gidin.

1. Yeni rotabileceğiniz **ayarlar** > **rotalar** >  **+ Ekle**' ye gidin. Aşağıdaki yolları ekleyin:

| Yönlendirme adı | Adres ön eki | Sonraki atlama türü | Sonraki atlama adresi |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Sanal gereç | 10.0.2.4 |

Yol tablosu yapılandırmasını doldurun:

1. **Ayarlar**altında **alt ağlar** ' i seçerek HDInsight alt ağına oluşturduğunuz yol tablosunu atayın.

1. **+ İlişkilendir**' i seçin.

1. **Alt ağı ilişkilendir** ekranında, kümenizin oluşturulduğu sanal ağı ve HDInsight kümeniz Için kullandığınız **alt ağı** seçin.

1. **Tamam**’ı seçin.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-node veya özel uygulama trafiği

Yukarıdaki adımlar kümenin sorun olmadan çalışmasına izin verir. Yine de, varsa kenar düğümleri üzerinde çalışan özel uygulamalarınıza uyum sağlamak için bağımlılıkları yapılandırmanız gerekir.

Uygulama bağımlılıkları tanımlanmalıdır ve Azure Güvenlik duvarı ya da yol tablosuna eklenmelidir.

Asimetrik yönlendirme sorunlarından kaçınmak için uygulama trafiği için yolların oluşturulması gerekir.

Uygulamalarınızın başka bağımlılıkları varsa, bunların Azure güvenlik duvarınızdan eklenmesi gerekir. Diğer her şey için HTTP/HTTPS trafiğine ve ağ kurallarına izin vermek üzere uygulama kuralları oluşturun.

## <a name="logging-and-scale"></a>Günlüğe kaydetme ve ölçeklendirme

Azure Güvenlik Duvarı, günlükleri birkaç farklı depolama sistemine gönderebilir. Güvenlik duvarınız için günlüğe kaydetmeyi yapılandırma yönergeleri için [öğretici: Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izleme](../firewall/tutorial-diagnostics.md)bölümündeki adımları izleyin.

Günlüğe kaydetme kurulumunu tamamladıktan sonra, verileri Log Analytics olarak günlüğe aktarıyorsanız, engellenen trafiği aşağıdaki gibi bir sorgu ile görüntüleyebilirsiniz:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Azure Güvenlik Duvarı 'nı Azure Izleyici günlükleriyle tümleştirmek, uygulama bağımlılıklarının tümünün farkında olmadığınız durumlarda ilk olarak bir uygulama çalışırken yararlıdır. Azure izleyici günlükleri hakkında daha fazla bilgi edinmek için Azure izleyici ['de günlük verilerini çözümleme](../azure-monitor/log-query/log-query-overview.md)

Azure Güvenlik duvarının ölçek sınırları ve istek artışları hakkında bilgi edinmek için [Bu](../azure-subscription-service-limits.md#azure-firewall-limits) belgeye bakın veya [SSS](../firewall/firewall-faq.md)bölümüne bakın.

## <a name="access-to-the-cluster"></a>Kümeye erişim

Güvenlik duvarını başarıyla ayarladıktan sonra, VNET 'in içinden ambarı 'na erişmek için iç uç noktayı (`https://CLUSTERNAME-int.azurehdinsight.net`) kullanabilirsiniz.

Ortak uç nokta (`https://CLUSTERNAME.azurehdinsight.net`) veya SSH uç noktası (`CLUSTERNAME-ssh.azurehdinsight.net`) kullanmak için, [burada](../firewall/integrate-lb.md)açıklanan asimetrik yönlendirme sorununa engel olmak için yol tablosunda ve NSG kurallarında doğru yollara sahip olduğunuzdan emin olun. Özellikle bu durumda, gelen NSG kurallarında istemci IP adresine izin vermeniz ve ayrıca sonraki atlama kümesini `internet`olarak Kullanıcı tanımlı yol tablosuna eklemeniz gerekir. Bu doğru ayarlanmamışsa bir zaman aşımı hatası görürsünüz.

## <a name="configure-another-network-virtual-appliance"></a>Başka bir ağ sanal gereci yapılandırma

> [!Important]
> Aşağıdaki bilgiler **yalnızca** Azure Güvenlik Duvarı dışında bir ağ sanal gereci (NVA) yapılandırmak istiyorsanız gereklidir.

Önceki yönergeler, HDInsight kümenizdeki giden trafiği kısıtlamak için Azure Güvenlik duvarını yapılandırmanıza yardımcı olur. Azure Güvenlik Duvarı, yaygın olarak karşılaşılan birçok önemli senaryo için trafiğe izin verecek şekilde otomatik olarak yapılandırılır. Başka bir ağ sanal gereci kullanmak istiyorsanız, bir dizi ek özelliği el ile yapılandırmanız gerekir. Ağ sanal gerecinizi yapılandırırken şunları göz önünde bulundurun:

* Hizmet uç noktası özellikli Hizmetleri, hizmet uç noktaları ile yapılandırılmalıdır.
* IP adresi bağımlılıkları HTTP/sn olmayan trafiğe yöneliktir (TCP ve UDP trafiği).
* FQDN HTTP/HTTPS uç noktaları, NVA cihazınıza yerleştirilebilir.
* Joker karakter HTTP/HTTPS uç noktaları, bir dizi niteleyicilere göre değişebilen bağımlılıklardır.
* Oluşturduğunuz yol tablosunu HDInsight alt ağına atayın.

### <a name="service-endpoint-capable-dependencies"></a>Hizmet uç noktası özellikli bağımlılıklar

| **Bkz** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP adresi bağımlılıkları

| **Bkz** | **Ayrıntılar** |
|---|---|
| \*: 123 | NTP saat denetimi. Trafik, 123 numaralı bağlantı noktasında birden çok uç noktaya denetlenir |
| [Burada](hdinsight-management-ip-addresses.md) Yayınlanan IP 'ler | Bunlar HDInsight hizmetidir |
| ESP kümeleri için AAD-DS özel IP 'Leri |
| \*: KMS Windows etkinleştirmesi için 16800 |
| Log Analytics için \*12000 |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS bağımlılıkları

> [!Important]
> Aşağıdaki liste, en önemli FQDN 'lerin çoğunu sağlar. [Bu dosyada](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)NVA 'nizi yapılandırmak için FQDN 'lerin tam listesini alabilirsiniz.

| **Bkz**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight sanal ağ mimarisi](hdinsight-virtual-network-architecture.md)
