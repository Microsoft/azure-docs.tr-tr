---
title: Azure HDInsight 'ta küme oluşturma, DomainNotFound hatasıyla başarısız oluyor
description: Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 54096e6d1741a7be9f50d4b4f7f0fcabbe880588
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943348"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta küme oluşturma, DomainNotFound hatasıyla başarısız oluyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

HDI Secure (Kurumsal Güvenlik Paketi) kümesi oluşturma işlemi `DomainNotFound` hata iletisiyle başarısız oluyor.

## <a name="cause"></a>Nedeni

Yanlış DNS ayarları.

## <a name="resolution"></a>Çözüm

Etki alanına katılmış kümeler dağıtıldığında HDI, AAD DS 'de (her küme için) bir iç Kullanıcı adı ve parola oluşturur ve tüm küme düğümlerini bu etki alanına birleştirir. Etki alanına ekleme işlemi Samba araçları kullanılarak gerçekleştirilir. Aşağıdaki önkoşulların karşılandığından emin olun:

* Etki alanı adı DNS aracılığıyla çözümlenmelidir.
* Etki alanı denetleyicilerinin IP adresi, kümenin dağıtıldığı sanal ağ için DNS ayarlarında ayarlanmalıdır.
* Sanal ağa AAD DS 'nin sanal ağı eşlenirse, el ile yapılması gerekir.
* DNS ileticileri kullanıyorsanız, etki alanı adı sanal ağ içinde doğru şekilde çözümlenmelidir.
* Güvenlik ilkeleri (NSG 'ler), etki alanına katılımı engellemez.

### <a name="additional-debugging-steps"></a>Ek hata ayıklama adımları

* Aynı alt ağda bir Windows sanal makinesi dağıtın, etki alanına bir Kullanıcı adı ve parola kullanarak makineye katılarak (Bu denetim masası kullanıcı arabiriminden yapılabilir) veya

* Aynı alt ağda bir Ubuntu VM dağıtma ve makineye katma etki alanı
  * Makineye SSH
  * sudo su
  * Betiği Kullanıcı adı ve parolayla Çalıştır
  * Betik ping yapar, gerekli yapılandırma dosyalarını ve ardından etki alanını oluşturun. Başarılı olursa DNS ayarlarınız iyidir.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]