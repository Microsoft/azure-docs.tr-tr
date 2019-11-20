---
title: Örnek Azure altyapısına ilişkin Izlenecek yol
description: Azure 'da örnek bir altyapı dağıtmaya yönelik anahtar tasarımı ve uygulama yönergeleri hakkında bilgi edinin.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf63db18dac5fa202bbf339723c6555c4acb2ca2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036026"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Linux VM 'Leri için örnek Azure altyapı Kılavuzu
Bu makalede, örnek bir uygulama altyapısı oluşturma işlemi adım adım açıklanmaktadır. Adlandırma kuralları, kullanılabilirlik kümeleri, sanal ağlar ve yük dengeleyiciler ve aslında sanal makinelerinizi (VM 'Ler) dağıtmak için tüm kılavuzları ve kararları bir araya getiren basit bir çevrimiçi mağaza için bir altyapı tasarlama ayrıntımız yaptık.

## <a name="example-workload"></a>Örnek iş yükü
Adventure Works döngüleri, Azure 'da şunları içeren çevrimiçi bir mağaza uygulaması oluşturmak istiyor:

* Web katmanında istemci ön ucu çalıştıran iki NGINX sunucusu
* Bir uygulama katmanındaki verileri ve siparişleri işleyen iki NGINX sunucusu
* Bir veritabanı katmanında ürün verilerini ve siparişleri depolamak için parçalı bir kümenin parçası olan iki MongoDB sunucusu
* Bir kimlik doğrulama katmanındaki müşteri hesapları ve tedarikçiler için iki Active Directory etki alanı denetleyicisi
* Tüm sunucular iki alt ağda bulunur:
  * Web sunucuları için bir ön uç alt ağı 
  * uygulama sunucuları, MongoDB kümesi ve etki alanı denetleyicileri için arka uç alt ağı

![Uygulama altyapısı için farklı katmanların diyagramı](./media/infrastructure-example/example-tiers.png)

Müşteriler çevrimiçi depoya gözatabileceği için, gelen güvenli Web trafiği Web sunucuları arasında yük dengelenmesi gerekir. Web sunucularından gelen HTTP istekleri biçiminde işleme trafiğinin işlenmesi, uygulama sunucuları arasında yük dengelenmesi gerekir. Ayrıca, altyapı yüksek kullanılabilirlik için tasarlanmalıdır.

Elde edilen tasarımın şunu içermesi gerekir:

* Bir Azure aboneliği ve hesabı
* Tek bir kaynak grubu
* Azure Yönetilen Diskleri
* İki alt ağa sahip bir sanal ağ
* Benzer role sahip VM 'Ler için kullanılabilirlik kümeleri
* Sanal makineler

Yukarıdaki tüm bunlar şu adlandırma kurallarını izler:

* Adventure Works döngüleri ön ek olarak **[IT iş yükü]-[konum]-[Azure Kaynak]** kullanır
  * Bu örnekte, "**azos**" (Azure çevrimiçi depo), BT iş yükü adıdır ve "**kullanım**" (Doğu ABD 2) konumudur
* Sanal ağlar AZOS kullanır-USE-VN<strong>[sayı]</strong>
* Kullanılabilirlik kümeleri azos kullanma-as- **[rol]**
* Sanal makine adları azos kullanma-VM- **[VMName]**

## <a name="azure-subscriptions-and-accounts"></a>Azure abonelikleri ve hesapları
Adventure Works döngüleri, bu BT iş yükü için faturalandırma sağlamak üzere Adventure Works kurumsal abonelik adlı kurumsal aboneliklerini kullanıyor.

## <a name="storage"></a>Depolama
Adventure Works döngüleri, Azure yönetilen diskleri kullanması gerektiğini tespit ederler. VM 'Ler oluştururken, her iki depolama alanı kullanılabilir depolama katmanı da kullanılır:

* Web sunucuları, uygulama sunucuları ve etki alanı denetleyicileri ve bunların veri diskleri için **Standart depolama** .
* MongoDB parçalı küme sunucuları ve bunların veri diskleri için **Premium Depolama** .

## <a name="virtual-network-and-subnets"></a>Sanal ağ ve alt ağlar
Sanal ağın Adventure Iş döngüleri şirket içi ağı üzerinde devam eden bağlantılara ihtiyacı olmadığından, yalnızca bulut sanal ağı üzerinde karar vermiştir.

Azure portal kullanarak aşağıdaki ayarlarla yalnızca bir bulut sanal ağı oluşturmuşlar:

* Ad: AZOS-USE-VN01
* Konum: Doğu ABD 2
* Sanal ağ adres alanı: 10.0.0.0/8
* İlk alt ağ:
  * Ad: ön uç
  * Adres alanı: 10.0.1.0/24
* İkinci alt ağ:
  * Ad: arka uç
  * Adres alanı: 10.0.2.0/24

## <a name="availability-sets"></a>Kullanılabilirlik kümeleri
Satır içi mağazalarından oluşan dört katmanın yüksek oranda kullanılabilirliğini sürdürmek için Adventure Works döngülerinin dört kullanılabilirlik kümesine karar vermiş olması gerekir:

* **azos-** Web sunucuları için Web 'i kullanma
* **azos-** uygulama sunucuları için uygulama olarak kullanma
* **azos-** MongoDB parçalı kümesindeki sunucular için DB 'yi kullanma
* **azos-** etki alanı denetleyicileri için DC kullanımı

## <a name="virtual-machines"></a>Sanal makineler
Adventure Works döngüleri, Azure VM 'Leri için aşağıdaki adlara karar verdi:

* **azos-Use-VM-web01** First Web Server
* **azos-VM-web02** ikinci Web sunucusu için kullanılır
* **azos-Use-VM-app01** ilk uygulama sunucusu için
* **azos-VM-app02** ikinci uygulama sunucusu için kullanılır
* **azos-Use-VM-db01** -kümedeki Ilk MongoDB sunucusu için
* **azos-Use-VM-db02** -kümedeki Ikinci MongoDB sunucusu için
* azos-ilk etki alanı denetleyicisi için **-VM-DC01 kullanın**
* **azos-VM-DC02** ikinci etki alanı denetleyicisi için kullanılır

Elde edilen yapılandırma aşağıda verilmiştir.

![Azure 'da dağıtılan son uygulama altyapısı](./media/infrastructure-example/example-config.png)

Bu yapılandırma şunları içerir:

* İki alt ağa sahip bir yalnızca bulut sanal ağı (ön uç ve arka uç)
* Standart ve Premium diskleri kullanan Azure yönetilen diskler
* Satır içi mağazanın her katmanı için bir adet olmak üzere dört kullanılabilirlik kümesi
* Dört katmanda sanal makineler
* Internet 'ten Web sunucularına HTTPS tabanlı Web trafiği için dış yük dengeli bir küme
* Web sunucularından uygulama sunucularına şifrelenmemiş web trafiği için iç yük dengeli bir küme
* Tek bir kaynak grubu
