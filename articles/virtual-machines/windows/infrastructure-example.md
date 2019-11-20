---
title: Örnek Azure altyapısına ilişkin Izlenecek yol
description: Azure 'da örnek bir altyapı dağıtmaya yönelik anahtar tasarımı ve uygulama yönergeleri hakkında bilgi edinin.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab6f304d78357e261c68ebbcfcb3746844edce8a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038558"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Windows VM 'Leri için örnek Azure altyapı Kılavuzu
Bu makalede, örnek bir uygulama altyapısı oluşturma işlemi adım adım açıklanmaktadır. Adlandırma kuralları, kullanılabilirlik kümeleri, sanal ağlar ve yük dengeleyiciler ve aslında sanal makinelerinizi (VM 'Ler) dağıtmak için tüm kılavuzları ve kararları bir araya getiren basit bir çevrimiçi mağaza için bir altyapı tasarlama ayrıntımız yaptık.

## <a name="example-workload"></a>Örnek iş yükü
Adventure Works döngüleri, Azure 'da şunları içeren bir çevrimiçi mağaza uygulaması oluşturmak istiyor:

* Web katmanında istemci ön ucu çalıştıran iki IIS sunucusu
* Uygulama katmanındaki verileri ve siparişleri işleyen iki IIS sunucusu
* Ürün verilerini ve siparişlerin bir veritabanı katmanında depolanması için AlwaysOn kullanılabilirlik grupları (iki SQL sunucusu ve çoğunluk düğüm tanığı) içeren iki Microsoft SQL Server örneği
* Bir kimlik doğrulama katmanındaki müşteri hesapları ve tedarikçiler için iki Active Directory etki alanı denetleyicisi
* Tüm sunucular iki alt ağda bulunur:
  * Web sunucuları için bir ön uç alt ağı 
  * uygulama sunucuları, SQL kümesi ve etki alanı denetleyicileri için arka uç alt ağı

![Uygulama altyapısı için farklı katmanların diyagramı](./media/infrastructure-example/example-tiers.png)

Müşterilerin çevrimiçi mağazaya gözatmasına yönelik olarak, gelen güvenli Web trafiği Web sunucuları arasında yük dengelenmesi gerekir. Web sunucularından gelen HTTP istekleri biçiminde işleme trafiğinin işlenmesi, uygulama sunucuları arasında dengelenmesi gerekir. Ayrıca, altyapı yüksek kullanılabilirlik için tasarlanmalıdır.

Elde edilen tasarımın şunu içermesi gerekir:

* Bir Azure aboneliği ve hesabı
* Tek bir kaynak grubu
* Azure Yönetilen Diskleri
* İki alt ağa sahip bir sanal ağ
* Benzer role sahip VM 'Ler için kullanılabilirlik kümeleri
* Sanal makineler

Yukarıdaki tüm bunlar şu adlandırma kurallarını izler:

* Adventure Works döngüleri ön ek olarak **[IT iş yükü]-[konum]-[Azure Kaynak]** kullanır
  * Bu örnekte, "**azos**" (Azure Online Mağazası), BT iş yükü adıdır ve "**kullanım**" (Doğu ABD 2) konumudur
* Sanal ağlar AZOS kullanır-USE-VN **[sayı]**
* Kullanılabilirlik kümeleri azos kullanma-as- **[rol]**
* Sanal makine adları azos kullanma-VM- **[VMName]**

## <a name="azure-subscriptions-and-accounts"></a>Azure abonelikleri ve hesapları
Adventure Works döngüleri, bu BT iş yükü için faturalandırma sağlamak üzere Adventure Works kurumsal abonelik adlı kurumsal aboneliklerini kullanıyor.

## <a name="storage"></a>Depolama
Adventure Works döngüleri, Azure yönetilen diskleri kullanması gerektiğini tespit ederler. VM 'Ler oluştururken, kullanılabilir depolama katmanları her ikisi de kullanılır:

* Web sunucuları, uygulama sunucuları ve etki alanı denetleyicileri ve bunların veri diskleri için **Standart depolama** .
* SQL Server VM 'Ler ve bunların veri diskleri için **Premium Depolama** .

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
Çevrimiçi mağazalarından oluşan dört katmanın tümüyle kullanılabilirliğini sürdürmek için Adventure Works döngülerinin dört kullanılabilirlik kümesine karar vermiş olması gerekir:

* **azos-** Web sunucuları için Web 'i kullanma
* **azos-** uygulama sunucuları için uygulama olarak kullanma
* **azos-** SQL sunucuları için-SQL kullanma
* **azos-** etki alanı denetleyicileri için DC kullanımı

## <a name="virtual-machines"></a>Sanal makineler
Adventure Works döngüleri, Azure VM 'Leri için aşağıdaki adlara karar verdi:

* **azos-Use-VM-web01** First Web Server
* **azos-VM-web02** ikinci Web sunucusu için kullanılır
* **azos-Use-VM-app01** ilk uygulama sunucusu için
* **azos-VM-app02** ikinci uygulama sunucusu için kullanılır
* azos-küme içindeki ilk SQL Server sunucusu için **-VM-sql01 kullanın**
* **azos-VM-sql02** , kümedeki ikinci SQL Server sunucusu için kullanılır
* azos-ilk etki alanı denetleyicisi için **-VM-DC01 kullanın**
* **azos-VM-DC02** ikinci etki alanı denetleyicisi için kullanılır

Elde edilen yapılandırma aşağıda verilmiştir.

![Azure 'da dağıtılan son uygulama altyapısı](./media/infrastructure-example/example-config.png)

Bu yapılandırma şunları içerir:

* İki alt ağa sahip bir yalnızca bulut sanal ağı (ön uç ve arka uç)
* Standart ve Premium disklerle Azure yönetilen diskler
* Her bir çevrimiçi mağaza katmanı için bir tane olmak üzere dört kullanılabilirlik kümesi
* Dört katmanda sanal makineler
* Internet 'ten Web sunucularına HTTPS tabanlı Web trafiği için dış yük dengeli bir küme
* Web sunucularından uygulama sunucularına şifrelenmemiş web trafiği için iç yük dengeli bir küme
* Tek bir kaynak grubu
