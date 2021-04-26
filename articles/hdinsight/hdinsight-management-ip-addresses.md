---
title: Azure HDInsight yönetim IP adresleri
description: Azure HDInsight ile sanal ağ için ağ güvenlik gruplarını ve Kullanıcı tanımlı yolları doğru bir şekilde yapılandırmak amacıyla gelen trafiğe izin vermeniz gereken IP adreslerini öğrenin.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 5f694dec6deffde9efb32fefbab91ae3b7a44a2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99490869"
---
# <a name="hdinsight-management-ip-addresses"></a>HDInsight yönetim IP adresleri

Bu makalede, Azure HDInsight sistem durumu ve Yönetim Hizmetleri tarafından kullanılan IP adresleri listelenir. Ağ güvenlik grupları (NSG 'ler) veya Kullanıcı tanımlı yollar (UDRs) kullanıyorsanız, bu IP adreslerinden bazılarını gelen ağ trafiği için izin verilenler listesine eklemeniz gerekebilir.

## <a name="introduction"></a>Giriş
 
> [!Important]
> Çoğu durumda, IP adreslerini el ile eklemek yerine ağ güvenlik grupları için artık [hizmet etiketleri](hdinsight-service-tags.md) kullanabilirsiniz. IP adresleri yeni Azure bölgeleri için yayımlanmaz ve yalnızca yayımlanmış hizmet etiketleri olur. Yönetim IP adresleri için statik IP adresleri sonunda kullanım dışı olacaktır.

HDInsight kümenize gelen trafiği denetlemek için ağ güvenlik grupları (NSG 'ler) veya Kullanıcı tanımlı yollar (UDRs) kullanıyorsanız, kümenizin kritik Azure sistem durumu ve yönetim hizmetleriyle iletişim kurabildiğinden emin olmanız gerekir.  Bu hizmetlere ait IP adreslerinden bazıları bölgeye özeldir ve bazıları tüm Azure bölgeleri için geçerlidir. Özel DNS kullanmıyorsanız Azure DNS hizmetinden gelen trafiğe de izin vermeniz gerekebilir.

Burada listelenmeyen bir bölge için IP adreslerine ihtiyacınız varsa, bölgenizin IP adreslerini bulmak için [hizmet etiketi bulma API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 'sini kullanabilirsiniz. API 'yi kullandıysanız, [hizmet etıketı json dosyasını](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) indirin ve istediğiniz bölgeyi arayın.

HDInsight, daha fazla hata oluşmasını engellemek için küme oluşturma ve ölçeklendirme ile bu kurallara yönelik doğrulama yapar. Doğrulama geçmezse, oluşturma ve ölçekleme başarısız olur.

Aşağıdaki bölümlerde, izin verilmesi gereken belirli IP adresleri ele alınmaktadır.

## <a name="azure-dns-service"></a>Azure DNS hizmeti

Azure tarafından belirtilen DNS hizmetini kullanıyorsanız, TCP ve UDP için 53 numaralı bağlantı noktasında __168.63.129.16__ erişimine izin verin. Daha fazla bilgi için bkz. [VM 'ler ve rol örnekleri Için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) belgesi. Özel DNS kullanıyorsanız, bu adımı atlayın.

## <a name="health-and-management-services-all-regions"></a>Sistem durumu ve Yönetim Hizmetleri: tüm bölgeler

Azure HDInsight sistem durumu ve Yönetim Hizmetleri için şu IP adreslerinden gelen trafiğe izin ver: tüm Azure bölgeleri için geçerlidir:

| Kaynak IP adresi | Hedef  | Yön |
| ---- | ----- | ----- |
| 168.61.49.99 | \*: 443 | Gelen |
| 23.99.5.239 | \*: 443 | Gelen |
| 168.61.48.131 | \*: 443 | Gelen |
| 138.91.141.162 | \*: 443 | Gelen |

## <a name="health-and-management-services-specific-regions"></a>Sistem durumu ve Yönetim Hizmetleri: belirli bölgeler

Kaynaklarınızın bulunduğu belirli bir Azure bölgesindeki Azure HDInsight sistem durumu ve Yönetim Hizmetleri için listelenen IP adreslerinden gelen trafiğe izin verin:

> [!IMPORTANT]  
> Kullandığınız Azure bölgesi listelenmiyorsa ağ güvenlik grupları için [hizmet etiketi](hdinsight-service-tags.md) özelliğini kullanın.

| Ülke | Bölge | İzin verilen kaynak IP adresleri | İzin verilen hedef | Yön |
| ---- | ---- | ---- | ---- | ----- |
| Asya | Doğu Asya | 23.102.235.122</br>52.175.38.134 | \*: 443 | Gelen |
| &nbsp; | Güneydoğu Asya | 13.76.245.160</br>13.76.136.249 | \*: 443 | Gelen |
| Avustralya | Doğu Avustralya | 104.210.84.115</br>13.75.152.195 | \*: 443 | Gelen |
| &nbsp; | Güneydoğu Avustralya | 13.77.2.56</br>13.77.2.94 | \*: 443 | Gelen |
| Brezilya | Güney Brezilya | 191.235.84.104</br>191.235.87.113 | \*: 443 | Gelen |
| Kanada | Doğu Kanada | 52.229.127.96</br>52.229.123.172 | \*: 443 | Gelen |
| &nbsp; | Orta Kanada | 52.228.37.66</br>52.228.45.222 |\*: 443 | Gelen |
| Çin | Kuzey Çin | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*: 443 | Gelen |
| &nbsp; | Doğu Çin | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*: 443 | Gelen |
| &nbsp; | Çin Kuzey 2 | 40.73.37.141</br>40.73.38.172 | \*: 443 | Gelen |
| &nbsp; | Çin Doğu 2 | 139.217.227.106</br>139.217.228.187 | \*: 443 | Gelen |
| Avrupa | Kuzey Avrupa | 52.164.210.96</br>13.74.153.132 | \*: 443 | Gelen |
| &nbsp; | West Europe| 52.166.243.90</br>52.174.36.244 | \*: 443 | Gelen |
| Fransa | Orta Fransa| 20.188.39.64</br>40.89.157.135 | \*: 443 | Gelen |
| Almanya | Orta Almanya | 51.4.146.68</br>51.4.146.80 | \*: 443 | Gelen |
| &nbsp; | Kuzeydoğu Almanya | 51.5.150.132</br>51.5.144.101 | \*: 443 | Gelen |
| Hindistan | Orta Hindistan | 52.172.153.209</br>52.172.152.49 | \*: 443 | Gelen |
| &nbsp; | Güney Hindistan | 104.211.223.67<br/>104.211.216.210 | \*: 443 | Gelen |
| Japonya | Doğu Japonya | 13.78.125.90</br>13.78.89.60 | \*: 443 | Gelen |
| &nbsp; | Batı Japonya | 40.74.125.69</br>138.91.29.150 | \*: 443 | Gelen |
| Güney Kore | Güney Kore - Orta | 52.231.39.142</br>52.231.36.209 | \*: 443 | Gelen |
| &nbsp; | Güney Kore - Güney | 52.231.203.16</br>52.231.205.214 | \*: 443 | Gelen
| Birleşik Krallık | Batı Birleşik Krallık | 51.141.13.110</br>51.141.7.20 | \*: 443 | Gelen |
| &nbsp; | Güney Birleşik Krallık | 51.140.47.39</br>51.140.52.16 | \*: 443 | Gelen |
| Birleşik Devletler | Central US | 13.89.171.122</br>13.89.171.124 | \*: 443 | Gelen |
| &nbsp; | Doğu ABD | 13.82.225.233</br>40.71.175.99 | \*: 443 | Gelen |
| &nbsp; | Doğu ABD 2 | 20.44.16.8/29</br>20.49.102.48/29 | \*: 443 | Gelen |
| &nbsp; | Orta Kuzey ABD | 157.56.8.38</br>157.55.213.99 | \*: 443 | Gelen |
| &nbsp; | Orta Batı ABD | 52.161.23.15</br>52.161.10.167 | \*: 443 | Gelen |
| &nbsp; | Batı ABD | 13.64.254.98</br>23.101.196.19 | \*: 443 | Gelen |
| &nbsp; | Batı ABD 2 | 52.175.211.210</br>52.175.222.222 | \*: 443 | Gelen |
| &nbsp; | BAE Kuzey | 65.52.252.96</br>65.52.252.97 | \*: 443 | Gelen |
| &nbsp; | BAE Orta | 20.37.76.96</br>20.37.76.99 | \*: 443 | Gelen |

Azure Kamu için kullanılacak IP adresleri hakkında daha fazla bilgi için bkz. [Azure Kamu Intelligence + Analytics](../azure-government/compare-azure-government-global-azure.md) belgesi.

Daha fazla bilgi için bkz. [ağ trafiğini denetleme](./control-network-traffic.md).

Kullanıcı tanımlı yollar (UDRs) kullanıyorsanız, bir yol belirtmeniz ve bir sonraki atlama "Internet" olarak ayarlandığında sanal ağdan yukarıdaki IP 'lere giden trafiğe izin vermeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HDInsight kümeleri için sanal ağlar oluşturma](hdinsight-create-virtual-network.md)
* [Azure HDInsight için ağ güvenlik grubu (NSG) hizmet etiketleri](hdinsight-service-tags.md)
