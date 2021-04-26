---
title: Azure HDInsight ile küme oluşturma hatalarıyla ilgili sorunları giderme
description: Azure HDInsight için Apache kümesi oluşturma sorunlarını giderme hakkında bilgi edinin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: f909f5714a049f63032c8a23ca1731777a40d332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702870"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Azure HDInsight ile küme oluşturma hatalarıyla ilgili sorunları giderme

Aşağıdaki sorunlar, küme oluşturma hatalarının en yaygın temel nedenlerdir:

- İzin sorunları
- Kaynak ilkesi kısıtlamaları
- Güvenlik duvarları
- Kaynak kilitleri
- Desteklenmeyen bileşen sürümleri
- Depolama hesabı adı kısıtlamaları
- Hizmet kesintileri

## <a name="permissions-issues"></a>İzin sorunları

Azure Data Lake Storage 2. kullanıyorsanız ve şu hatayı alırsanız `AmbariClusterCreationFailedErrorCode` : " :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: ", Azure Portal açın, depolama hesabınıza gidin ve Access Control (IAM) altında, **Depolama Blobu veri katılımcısı** veya **Depolama Blobu veri sahibi** rolünün, abonelik için **Kullanıcı tarafından atanan yönetilen kimliğe** erişimi atandığından emin olun. Ayrıntılı yönergeler için [Data Lake Storage 2. yönetilen kimlik için Izinleri ayarlama](../hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2) bölümüne bakın.

Azure Data Lake Storage 1. kullanıyorsanız, bkz. Kurulum ve yapılandırma yönergeleri [Azure HDInsight kümeleri ile Azure Data Lake Storage 1. kullanma](../hdinsight-hadoop-use-data-lake-storage-gen1.md). Data Lake Storage 1., HBase kümelerinde desteklenmez ve HDInsight sürüm 4,0 ' de desteklenmez.

Azure Storage kullanıyorsanız, küme oluşturma sırasında depolama hesabı adının geçerli olduğundan emin olun.

## <a name="resource-policy-restrictions"></a>Kaynak ilkesi kısıtlamaları

Abonelik tabanlı Azure ilkeleri, genel IP adreslerinin oluşturulmasını reddedebilir. HDInsight kümesi oluşturmak için iki genel IP gerekir.  

Genel olarak, aşağıdaki ilkeler küme oluşturmayı etkileyebilir:

* Abonelik içindeki yük dengeleyiciler & IP adresi oluşturulmasını engellemeye yönelik ilkeler.
* Depolama hesabı oluşturulmasını önleyecek ilke.
* Ağ kaynaklarının silinmesini önleyecek ilke (IP adresi/yük dengeleyiciler).

## <a name="firewalls"></a>Güvenlik duvarları

Sanal ağ veya depolama hesabınızdaki güvenlik duvarları, HDInsight yönetim IP adresleriyle iletişimi reddedebilir.

Aşağıdaki tablodaki IP adreslerinden gelen trafiğe izin verin.

| Kaynak IP adresi | Hedef | Yön |
|---|---|---|
| 168.61.49.99 | *: 443 | Gelen |
| 23.99.5.239 | *: 443 | Gelen |
| 168.61.48.131 | *: 443 | Gelen |
| 138.91.141.162 | *: 443 | Gelen |

Ayrıca, kümenin oluşturulduğu bölgeye özgü IP adreslerini de ekleyin. Her bir Azure bölgesinin adres listesi için bkz. [HDInsight YÖNETIMI IP adresleri](../hdinsight-management-ip-addresses.md) .

Bir Express Route veya kendi özel DNS sunucunuz kullanıyorsanız bkz. [Azure HDInsight için bir sanal ağ planlayın-birden çok ağı bağlama](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Kaynak kilitleri  

[Sanal ağınızda ve kaynak grubunuzda kilit](../../azure-resource-manager/management/lock-resources.md)olmadığından emin olun. Kaynak grubu kilitliyse kümeler oluşturulamaz veya silinemez. 

## <a name="unsupported-component-versions"></a>Desteklenmeyen bileşen sürümleri

Çözümünüzde [Azure HDInsight ve Apache Hadoop bileşeni 'nin desteklenen bir sürümünü](../hdinsight-component-versioning.md) kullandığınızdan emin olun.  

## <a name="storage-account-name-restrictions"></a>Depolama hesabı adı kısıtlamaları

Depolama hesabı adları 24 karakterden uzun olamaz ve özel bir karakter içeremez. Bu kısıtlamalar depolama hesabındaki varsayılan kapsayıcı adı için de geçerlidir.

Diğer adlandırma kısıtlamaları da küme oluşturma için geçerlidir. Daha fazla bilgi için bkz. [küme adı kısıtlamaları](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Hizmet kesintileri

Olası kesintiler veya hizmet sorunları için [Azure durumunu](https://status.azure.com) denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Sanal Ağını kullanarak Azure HDInsight kapsamını genişletme](../hdinsight-plan-virtual-network-deployment.md)
* [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Azure HDInsight kümeleri ile Azure Depolama'yı kullanma](../hdinsight-hadoop-use-blob-storage.md)
* [Apache Hadoop, Apache Spark, Apache Kafka ve daha fazlasıyla HDInsight'ta küme oluşturma](../hdinsight-hadoop-provision-linux-clusters.md)
