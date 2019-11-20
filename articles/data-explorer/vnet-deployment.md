---
title: Sanal ağınıza Azure Veri Gezgini dağıtma (Önizleme)
description: Azure Veri Gezgini sanal ağınıza dağıtmayı öğrenin
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: a7a9efbf6fd9c3dbe6b16d12a54f743d5b0820ba
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838206"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>Sanal ağınıza Azure Veri Gezgini dağıtma (Önizleme)

Bu makalede, bir Azure Veri Gezgini kümesini özel bir Azure sanal ağına dağıtırken mevcut olan kaynaklar açıklanmaktadır. Bu bilgiler, sanal ağınızdaki (VNet) bir alt ağa bir küme dağıtmanıza yardımcı olur. Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağ nedir?](/azure/virtual-network/virtual-networks-overview)

   ![VNET diyagramı](media/vnet-deployment/vnet-diagram.png)

Azure Veri Gezgini, bir kümenin sanal ağınızdaki (VNet) bir alt ağa dağıtılmasını destekler. Bu özellik şunları yapmanızı sağlar:

* Azure Veri Gezgini kümesi trafiğinden [ağ güvenlik grubu](/azure/virtual-network/security-overview) (NSG) kurallarını zorunlu tutun.
* Şirket içi ağınızı Azure Veri Gezgini kümesinin alt ağına bağlayın.
* [Hizmet uç noktaları](/azure/virtual-network/virtual-network-service-endpoints-overview)ile veri bağlantısı kaynaklarınızı ([Olay Hub](/azure/event-hubs/event-hubs-about) 'ı ve [Event Grid](/azure/event-grid/overview)) güvenli hale getirin.

> [!NOTE]
> Sanal Ağ tümleştirmesi ve dağıtımı önizleme modundadır. Bu özelliği etkinleştirmek için bir [destek bileti](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)açın.

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>VNet 'iniz içindeki Azure Veri Gezgini kümenize erişin

Azure Veri Gezgini kümenize, her hizmet için aşağıdaki IP adreslerini kullanarak erişebilirsiniz (motor ve veri yönetimi hizmetleri):

* **Özel IP**: VNET 'in içindeki kümeye erişmek için kullanılır.
* **Genel IP**: yönetim ve Izleme için sanal ağ dışından kümeye erişmek için kullanılır ve kümeden başlayan giden bağlantıların kaynak adresi olarak.

Hizmete erişmek için aşağıdaki DNS kayıtları oluşturulmuştur: 

* `[clustername].[geo-region].kusto.windows.net` (motor) `ingest-[clustername].[geo-region].kusto.windows.net` (veri yönetimi) her hizmet için genel IP ile eşleştirilir. 

* `private-[clustername].[geo-region].kusto.windows.net` (motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (veri yönetimi) her hizmet için özel IP ile eşleştirilir.

## <a name="plan-subnet-size-in-your-vnet"></a>VNet 'iniz içindeki alt ağ boyutunu planlayın

Azure Veri Gezgini kümesini barındırmak için kullanılan alt ağın boyutu alt ağ dağıtıldıktan sonra değiştirilemez. Sanal ortamınızda Azure Veri Gezgini, iç yük dengeleyiciler (motor ve veri yönetimi) için her bir sanal makine için bir özel IP adresi ve iki özel IP adresi kullanır. Azure ağ, her alt ağ için beş IP adresi de kullanır. Azure Veri Gezgini, veri yönetimi hizmeti için iki sanal makine sağlar. Altyapı hizmeti VM 'Leri, Kullanıcı Yapılandırması ölçek kapasitesi başına sağlanır.

Toplam IP adresi sayısı:

| Kullanım | Adres sayısı |
| --- | --- |
| Altyapı hizmeti | örnek başına 1 |
| Veri yönetimi hizmeti | 2 |
| İç yük dengeleyiciler | 2 |
| Azure ayrılmış adresler | 5 |
| **Toplamda** | **#engine_instances + 9** |

> [!IMPORTANT]
> Azure Veri Gezgini dağıtıldıktan sonra değiştirilemediğinden alt ağ boyutu önceden planlanmalıdır. Bu nedenle gereken alt ağ boyutunu buna göre ayırın.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Azure Veri Gezgini bağlanmak için hizmet uç noktaları

[Azure hizmet uç noktaları](/azure/virtual-network/virtual-network-service-endpoints-overview) , Azure çok kiracılı kaynaklarınızı sanal ağınıza güvenli hale getirmenizi sağlar.
Azure Veri Gezgini kümesini alt ağa dağıtmak, Azure Veri Gezgini alt ağı için temel alınan kaynakları kısıtlayarak [Olay Hub 'ı](/azure/event-hubs/event-hubs-about) veya [Event Grid](/azure/event-grid/overview) veri bağlantıları ayarlamanıza olanak sağlar.

## <a name="dependencies-for-vnet-deployment"></a>VNet dağıtımı için bağımlılıklar

### <a name="network-security-groups-configuration"></a>Ağ güvenlik grupları yapılandırması

[Ağ güvenlik grupları (NSG)](/azure/virtual-network/security-overview) , bir sanal ağ içindeki ağ erişimini denetleme olanağı sağlar. Azure Veri Gezgini iki uç nokta kullanılarak erişilebilir: HTTPs (443) ve TDS (1433). Aşağıdaki NSG kurallarının, kümenizin yönetimi, izlenmesi ve düzgün çalışması için bu uç noktalara erişime izin verecek şekilde yapılandırılması gerekir.

#### <a name="inbound-nsg-configuration"></a>Gelen NSG yapılandırması

| **Kullanma**   | **Kaynak**   | **Alıcı**   | **Protokol**   |
| --- | --- | --- | --- |
| Yönetim  |[ADX yönetim adresleri](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (servicetag) | ADX alt ağı: 443  | TCP  |
| Sistem durumunu izleme  | [ADX sistem durumu izleme adresleri](#health-monitoring-addresses)  | ADX alt ağı: 443  | TCP  |
| ADX iç iletişimi  | ADX alt ağı: tüm bağlantı noktaları  | ADX alt ağı: tüm bağlantı noktaları  | Tümü  |
| Azure yük dengeleyiciye izin ver (sistem durumu araştırması)  | AzureLoadBalancer  | ADX alt ağı: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Giden NSG yapılandırması

| **Kullanma**   | **Kaynak**   | **Alıcı**   | **Protokol**   |
| --- | --- | --- | --- |
| Azure Storage bağımlılığı  | ADX alt ağı  | Depolama alanı: 443  | TCP  |
| Azure Data Lake bağımlılığı  | ADX alt ağı  | AzureDataLake: 443  | TCP  |
| EventHub alma ve hizmet izleme  | ADX alt ağı  | EventHub: 443, 5671  | TCP  |
| Ölçümleri yayımlama  | ADX alt ağı  | AzureMonitor: 443 | TCP  |
| Azure Izleyici yapılandırması indirmesi  | ADX alt ağı  | [Azure izleyici yapılandırma uç noktası adresleri](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (varsa) | ADX alt ağı | AzureActiveDirectory: 443 | TCP |
| Sertifika yetkilisi | ADX alt ağı | Internet: 80 | TCP |
| İç iletişim  | ADX alt ağı  | ADX alt ağı: tüm bağlantı noktaları  | Tümü  |
| `sql\_request` ve `http\_request` eklentileri için kullanılan bağlantı noktaları  | ADX alt ağı  | Internet: özel  | TCP  |

### <a name="relevant-ip-addresses"></a>İlgili IP adresleri

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure Veri Gezgini yönetimi IP adresleri

| Bölge | Adresler |
| --- | --- |
| Avustralya Orta | 20.37.26.134 |
| Avustralya Central2 | 20.39.99.177 |
| Avustralya Doğu | 40.82.217.84 |
| Avustralya Güneydoğu | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| Orta Kanada | 40.82.188.208 |
| Doğu Kanada | 40.80.255.12 |
| Orta Hindistan | 40.81.249.251 |
| Orta ABD | 40.67.188.68 |
| EUAP Orta ABD | 40.89.56.69 |
| Doğu Asya | 20.189.74.103 |
| Doğu ABD | 52.224.146.56 |
| Doğu ABD 2 | 52.232.230.201 |
| Doğu ABD2 EUAP | 52.253.226.110 |
| Fransa Orta | 40.66.57.91 |
| Fransa Güney | 40.82.236.24 |
| Japonya Doğu | 20.43.89.90 |
| Japonya Batı | 40.81.184.86 |
| Kore Orta | 40.82.156.149 |
| Kore Güney | 40.80.234.9 |
| Orta Kuzey ABD | 40.81.45.254 |
| Kuzey Avrupa | 52.142.91.221 |
| Güney Afrika Kuzey | 102.133.129.138 |
| Güney Afrika Batı | 102.133.0.97 |
| Orta Güney ABD | 20.45.3.60 |
| Güneydoğu Asya | 40.119.203.252 |
| Güney Hindistan | 40.81.72.110 |
| Birleşik Krallık Güney | 40.81.154.254 |
| Birleşik Krallık Batı | 40.81.122.39 |
| Batı Orta ABD | 52.159.55.120 |
| Batı Avrupa | 51.145.176.215 |
| Batı Hindistan | 40.81.88.112 |
| Batı ABD | 13.64.38.225 |
| Batı ABD 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Sistem durumu izleme adresleri

| Bölge | Adresler |
| --- | --- |
| Avustralya Orta | 191.239.64.128 |
| Avustralya Orta 2 | 191.239.64.128 |
| Avustralya Doğu | 191.239.64.128 |
| Avustralya Güneydoğu | 191.239.160.47 |
| Güney Brezilya | 23.98.145.105 |
| Orta Kanada | 168.61.212.201 |
| Doğu Kanada | 168.61.212.201 |
| Orta Hindistan | 23.99.5.162 |
| Orta ABD | 168.61.212.201 |
| EUAP Orta ABD | 168.61.212.201 |
| Doğu Asya | 168.63.212.33 |
| Doğu ABD | 137.116.81.189 |
| Doğu ABD 2 | 137.116.81.189 |
| EUAP Doğu ABD 2 | 137.116.81.189 |
| Fransa Orta | 23.97.212.5 |
| Fransa Güney | 23.97.212.5 |
| Japonya Doğu | 138.91.19.129 |
| Japonya Batı | 138.91.19.129 |
| Kore Orta | 138.91.19.129 |
| Kore Güney | 138.91.19.129 |
| Orta Kuzey ABD | 23.96.212.108 |
| Kuzey Avrupa | 191.235.212.69 
| Güney Afrika Kuzey | 104.211.224.189 |
| Güney Afrika Batı | 104.211.224.189 |
| Orta Güney ABD | 23.98.145.105 |
| Güney Hindistan | 23.99.5.162 |
| Güneydoğu Asya | 168.63.173.234 |
| Birleşik Krallık Güney | 23.97.212.5 |
| Birleşik Krallık Batı | 23.97.212.5 |
| Batı Orta ABD | 168.61.212.201 |
| Batı Avrupa | 23.97.212.5 |
| Batı Hindistan | 23.99.5.162 |
| Batı ABD | 23.99.5.162 |
| Batı ABD 2 | 23.99.5.162 | 

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure Izleyici yapılandırma uç noktası adresleri

| Bölge | Adresler |
| --- | --- |
| Avustralya Orta | 52.148.86.165 |
| Avustralya Orta 2 | 52.148.86.165 |
| Avustralya Doğu | 52.148.86.165 |
| Avustralya Güneydoğu | 52.148.86.165 |
| Brezilya Güney | 13.68.89.19 |
| Kanada Orta | 13.90.43.231 |
| Kanada Doğu | 13.90.43.231 |
| Orta Hindistan | 13.71.25.187 |
| Orta ABD | 52.173.95.68 |
| EUAP Orta ABD | 13.90.43.231 |
| Doğu Asya | 13.75.117.221 |
| Doğu ABD | 13.90.43.231 |
| Doğu ABD 2 | 13.68.89.19 | 
| EUAP Doğu ABD 2 | 13.68.89.19 |
| Fransa Orta | 52.174.4.112 |
| Fransa Güney | 52.174.4.112 |
| Japonya Doğu | 13.75.117.221 |
| Japonya Batı | 13.75.117.221 |
| Kore Orta | 13.75.117.221 |
| Kore Güney | 13.75.117.221 |
| Orta Kuzey ABD | 52.162.240.236 |
| Kuzey Avrupa | 52.169.237.246 |
| Güney Afrika Kuzey | 13.71.25.187 |
| Güney Afrika Batı | 13.71.25.187 |
| Orta Güney ABD | 13.84.173.99 |
| Güney Hindistan | 13.71.25.187 |
| Güneydoğu Asya | 52.148.86.165 |
| UK Güney | 52.174.4.112 |
| UK Batı | 52.169.237.246 |
| Batı Orta ABD | 52.161.31.69 |
| Batı Avrupa | 52.174.4.112 |
| Batı Hindistan | 13.71.25.187 |
| Batı ABD | 40.78.70.148 |
| Batı ABD 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute kurulumu

Şirket içi ağı Azure sanal ağı 'na bağlamak için ExpressRoute 'u kullanın. Ortak bir kurulum, Sınır Ağ Geçidi Protokolü (BGP) oturumu aracılığıyla varsayılan yolun (0.0.0.0/0) tanıtılmalıdır. Bu, sanal ağdan gelen trafiği, trafiği bırakan müşterinin Şirket içi ağına iletilecek şekilde zorlar ve giden akışların kesilmesine neden olur. Bu varsayılanı aşmak için [Kullanıcı tanımlı yol (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) yapılandırılabilir ve sonraki atlama *Internet*olur. UDR, BGP üzerinden öncelikli olduğundan trafik Internet 'e gidecek.

## <a name="securing-outbound-traffic-with-firewall"></a>Giden trafiğin güvenliğini güvenlik duvarıyla sağlama

[Azure Güvenlik Duvarı](/azure/firewall/overview) veya etki alanı adlarını sınırlamak için herhangi bir Sanal Gereç kullanarak giden trafiği güvenli hale getirmek istiyorsanız güvenlik duvarında aşağıdaki tam etki alanı ADLARıNA (FQDN) izin verilmelidir.

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
production.diagnostics.monitoring.core.windows.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

Ayrıca, asimetrik yolların sorunlarını engellemek için [Yönetim adresleri](#azure-data-explorer-management-ip-addresses) ve [sistem durumu izleme adresleriyle](#health-monitoring-addresses) bir sonraki atlama *Internet* ile alt ağda [yol tablosu](/azure/virtual-network/virtual-networks-udr-overview) tanımlamanız gerekir.

Örneğin, **Batı ABD** bölge Için aşağıdaki udrs tanımlanmalıdır:

| Ad | Adres Ön Eki | Sonraki atlama |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak sanal ağınıza Azure Veri Gezgini kümesi dağıtma

Azure Veri Gezgini kümesini sanal ağınıza dağıtmak için, VNet Azure Resource Manager şablonunuzda [azure Veri Gezgini kümesini dağıtma](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) ' yı kullanın.

Bu şablon küme, sanal ağ, alt ağ, ağ güvenlik grubu ve genel IP adreslerini oluşturur.
