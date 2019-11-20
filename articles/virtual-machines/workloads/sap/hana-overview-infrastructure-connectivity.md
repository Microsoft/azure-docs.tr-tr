---
title: Azure 'da SAP HANA altyapı ve bağlantı (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA kullanmak için gerekli bağlantı altyapısını yapılandırın (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4fa0fe072fe98d565ad9d6f947540b7e1b039732
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101152"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (büyük örnekler) dağıtımı 

Bu makalede, Microsoft 'tan Azure 'da SAP HANA satın alma işlemi (büyük örnekler) tamamladığınız varsayılır. Bu makaleyi okumadan önce, genel arka plan için bkz. [Hana büyük örnekler Ortak terimleri](hana-know-terms.md) ve [Hana büyük örnekler SKU 'ları](hana-available-skus.md).


Microsoft, HANA büyük örnek birimlerini dağıtmak için aşağıdaki bilgileri gerektirir:

- Müşteri adı.
- İş iletişim bilgileri (e-posta adresi ve telefon numarası dahil).
- Teknik iletişim bilgileri (e-posta adresi ve telefon numarası dahil).
- Teknik ağ iletişim bilgileri (e-posta adresi ve telefon numarası dahil).
- Azure dağıtım bölgesi (örneğin, Batı ABD, Avustralya Doğu veya Kuzey Avrupa).
- Azure 'da SAP HANA (büyük örnekler) SKU (yapılandırma).
- Her Azure dağıtım bölgesi için:
    - Azure sanal ağlarını HANA büyük örneklerine bağlayan ER-P2P bağlantıları için bir/29 IP adresi aralığı.
    - HANA büyük örnekler sunucu IP havuzu için kullanılan A/24 CıDR bloğu.
    - [ExpressGlobal Reach Route](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) 'u kullanarak ŞIRKET Içinden Hana büyük örnek birimlerine veya farklı Azure bölgelerindeki Hana büyük örnek birimleri arasında doğrudan yönlendirmeyi etkinleştirmek için isteğe bağlı olarak, başka bir/29 IP adresi aralığı ayırmanız gerekir. Bu belirli Aralık, daha önce tanımladığınız diğer IP adresi aralıklarıyla çakışmayabilir.
- HANA büyük örneklerine bağlanan her Azure sanal ağının sanal ağ adres alanı özniteliğinde kullanılan IP adresi aralığı değerleri.
- Her HANA büyük örnek sistemi için veriler:
  - Tam etki alanı adıyla ideal olarak istenen konak adı.
  - Sunucu IP havuzu adres aralığının dışında, HANA büyük örnek birimi için istenen IP adresi. (Sunucu IP havuzu adres aralığındaki ilk 30 IP adresi, HANA büyük örnekleri içinde iç kullanım için ayrılmıştır.)
  - SAP HANA örneği için SAP HANA SID adı (gerekli SAP HANA ilgili disk birimlerini oluşturmak için gereklidir). Microsoft, NFS birimlerinde sıdaddm izinleri oluşturmak için HANA SID 'sine ihtiyaç duyuyor. Bu birimler, HANA büyük örnek birimine iliştirilemiyor. HANA SID, bağlanan disk birimlerinin ad bileşenlerinden biri olarak da kullanılır. Birim üzerinde birden fazla HANA örneği çalıştırmak istiyorsanız, birden çok HANA SID 'si listelemelidir. Her biri, atanan ayrı bir birim kümesini alır.
  - Linux IŞLETIM sisteminde sıdaddm kullanıcısının bir grup KIMLIĞI vardır. Bu KIMLIK, gerekli SAP HANA ilgili disk birimlerinin oluşturulması için gereklidir. SAP HANA yüklemesi, genellikle bir grup KIMLIĞI 1001 olan sapsys grubunu oluşturur. Sıdaddm kullanıcısı bu grubun bir parçasıdır.
  - Linux IŞLETIM sisteminde sıdaddm kullanıcısının bir kullanıcı KIMLIĞI vardır. Bu KIMLIK, gerekli SAP HANA ilgili disk birimlerinin oluşturulması için gereklidir. Birim üzerinde birden fazla HANA örneği çalıştırıyorsanız, tüm sıdavdm kullanıcılarını listeleyin. 
- Azure HANA büyük örneklerine SAP HANA Azure aboneliğinin Azure abonelik KIMLIĞI, doğrudan bağlanacak şekilde yapılır. Bu abonelik KIMLIĞI, HANA büyük örnek birimi veya birimleri ile ücretlendirilebilecek Azure aboneliğine başvurur.

Yukarıdaki bilgileri verdikten sonra Microsoft, Azure 'da SAP HANA (büyük örnekler) sağlar. Microsoft size Azure sanal ağlarınızı HANA büyük örneklerine bağlamak için size bilgi gönderir. Ayrıca, HANA büyük örnek birimlerine de erişebilirsiniz.

Microsoft tarafından dağıtıldıktan sonra HANA büyük örneklerine bağlanmak için aşağıdaki sırayı kullanın:

1. [Azure VM 'lerini HANA büyük örneklerine bağlama](hana-connect-azure-vm-large-instances.md)
2. [Sanal ağı HANA büyük örneklerine bağlama ExpressRoute](hana-connect-vnet-express-route.md)
3. [Ek ağ gereksinimleri (isteğe bağlı)](hana-additional-network-requirements.md)

