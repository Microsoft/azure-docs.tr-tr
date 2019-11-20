---
title: Azure HDInsight yönetim IP adresleri
description: Azure HDInsight ile sanal ağ için ağ güvenlik gruplarını ve Kullanıcı tanımlı yolları doğru bir şekilde yapılandırmak amacıyla gelen trafiğe izin vermeniz gereken IP adreslerini öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 3f0b31cd3d37c3040ff99a89c1a5201b413fd3fc
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076641"
---
# <a name="hdinsight-management-ip-addresses"></a>HDInsight yönetim IP adresleri

HDInsight kümenize gelen trafiği denetlemek için ağ güvenlik grupları (NSG 'ler) veya Kullanıcı tanımlı yollar (UDRs) kullanıyorsanız, kümenizin kritik Azure sistem durumu ve yönetim hizmetleriyle iletişim kurabildiğinden emin olmanız gerekir.  Bu hizmetlere ait IP adreslerinden bazıları bölgeye özeldir ve bazıları tüm Azure bölgeleri için geçerlidir. Özel DNS kullanmıyorsanız Azure DNS hizmetinden gelen trafiğe de izin vermeniz gerekebilir.

Aşağıdaki bölümlerde, izin verilmesi gereken belirli IP adresleri ele alınmaktadır.

## <a name="azure-dns-service"></a>Azure DNS hizmeti

Azure tarafından belirtilen DNS hizmetini kullanıyorsanız, 53 numaralı bağlantı noktasında __168.63.129.16__ adresinden erişime izin verin. Daha fazla bilgi için bkz. [VM 'ler ve rol örnekleri Için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) belgesi. Özel DNS kullanıyorsanız, bu adımı atlayın.

## <a name="health-and-management-services-all-regions"></a>Sistem durumu ve Yönetim Hizmetleri: Tüm bölgeler

Azure HDInsight sistem durumu ve Yönetim Hizmetleri için şu IP adreslerinden gelen trafiğe tüm Azure bölgelerine uygulanan trafik sağlar:

| Kaynak IP adresi | Destination  | Direction |
| ---- | ----- | ----- |
| 168.61.49.99 | \*: 443 | Gelen |
| 23.99.5.239 | \*: 443 | Gelen |
| 168.61.48.131 | \*: 443 | Gelen |
| 138.91.141.162 | \*: 443 | Gelen |

## <a name="health-and-management-services-specific-regions"></a>Sistem durumu ve Yönetim Hizmetleri: Belirli bölgeler

Kaynaklarınızın bulunduğu belirli bir Azure bölgesindeki Azure HDInsight sistem durumu ve Yönetim Hizmetleri için listelenen IP adreslerinden gelen trafiğe izin verin:

> [!IMPORTANT]  
> Kullandığınız Azure bölgesi listede yoksa, önceki bölümden yalnızca dört IP adresini kullanın.

| Country | Bölge | İzin verilen kaynak IP adresleri | İzin verilen hedef | Direction |
| ---- | ---- | ---- | ---- | ----- |
| Asya | Doğu Asya | 23.102.235.122</br>52.175.38.134 | \*: 443 | Gelen |
| &nbsp; | Güneydoğu Asya | 13.76.245.160</br>13.76.136.249 | \*: 443 | Gelen |
| Avustralya | Avustralya Doğu | 104.210.84.115</br>13.75.152.195 | \*: 443 | Gelen |
| &nbsp; | Avustralya Güneydoğu | 13.77.2.56</br>13.77.2.94 | \*: 443 | Gelen |
| Brezilya | Güney Brezilya | 191.235.84.104</br>191.235.87.113 | \*: 443 | Gelen |
| Kanada | Doğu Kanada | 52.229.127.96</br>52.229.123.172 | \*: 443 | Gelen |
| &nbsp; | Orta Kanada | 52.228.37.66</br>52.228.45.222 |\*: 443 | Gelen |
| Çin | Çin Kuzey | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*: 443 | Gelen |
| &nbsp; | Çin Doğu | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*: 443 | Gelen |
| &nbsp; | Çin Kuzey 2 | 40.73.37.141</br>40.73.38.172 | \*: 443 | Gelen |
| &nbsp; | Çin Doğu 2 | 139.217.227.106</br>139.217.228.187 | \*: 443 | Gelen |
| Avrupa | Kuzey Avrupa | 52.164.210.96</br>13.74.153.132 | \*: 443 | Gelen |
| &nbsp; | Batı Avrupa| 52.166.243.90</br>52.174.36.244 | \*: 443 | Gelen |
| Fransa | Fransa Orta| 20.188.39.64</br>40.89.157.135 | \*: 443 | Gelen |
| Almanya | Almanya Orta | 51.4.146.68</br>51.4.146.80 | \*: 443 | Gelen |
| &nbsp; | Almanya Kuzeydoğu | 51.5.150.132</br>51.5.144.101 | \*: 443 | Gelen |
| Hindistan | Orta Hindistan | 52.172.153.209</br>52.172.152.49 | \*: 443 | Gelen |
| &nbsp; | Güney Hindistan | 104.211.223.67<br/>104.211.216.210 | \*: 443 | Gelen |
| Japonya | Japonya Doğu | 13.78.125.90</br>13.78.89.60 | \*: 443 | Gelen |
| &nbsp; | Japonya Batı | 40.74.125.69</br>138.91.29.150 | \*: 443 | Gelen |
| Güney Kore | Kore Orta | 52.231.39.142</br>52.231.36.209 | \*: 443 | Gelen |
| &nbsp; | Kore Güney | 52.231.203.16</br>52.231.205.214 | \*: 443 | Gelen
| Birleşik Krallık | Birleşik Krallık Batı | 51.141.13.110</br>51.141.7.20 | \*: 443 | Gelen |
| &nbsp; | Birleşik Krallık Güney | 51.140.47.39</br>51.140.52.16 | \*: 443 | Gelen |
| Amerika Birleşik Devletleri | Orta ABD | 13.89.171.122</br>13.89.171.124 | \*: 443 | Gelen |
| &nbsp; | East US | 13.82.225.233</br>40.71.175.99 | \*: 443 | Gelen |
| &nbsp; | Orta Kuzey ABD | 157.56.8.38</br>157.55.213.99 | \*: 443 | Gelen |
| &nbsp; | Batı Orta ABD | 52.161.23.15</br>52.161.10.167 | \*: 443 | Gelen |
| &nbsp; | Batı ABD | 13.64.254.98</br>23.101.196.19 | \*: 443 | Gelen |
| &nbsp; | Batı ABD 2 | 52.175.211.210</br>52.175.222.222 | \*: 443 | Gelen |

Azure Kamu için kullanılacak IP adresleri hakkında daha fazla bilgi için bkz. [Azure Kamu Intelligence + Analytics](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) belgesi.

Daha fazla bilgi için [ağ trafiğini denetleme](hdinsight-plan-virtual-network-deployment.md#networktraffic) bölümüne bakın.

Kullanıcı tanımlı yollar (UDRs) kullanıyorsanız, bir sonraki atlamanın "Internet" olarak ayarlandığı bir yol belirtmeli ve VNET 'ten yukarıdaki IP 'lere giden trafiğe izin vermeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight kümeleri için sanal ağlar oluşturma](hdinsight-create-virtual-network.md)
