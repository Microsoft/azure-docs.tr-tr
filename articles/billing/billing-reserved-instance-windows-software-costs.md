---
title: Azure için rezervasyon yazılımı maliyetleri
description: Hangi yazılım ölçümlerinin Azure Ayrılmış Sanal Makine Örneği maliyetlerine dahil olmadığını öğrenin.
author: yashar
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2019
ms.author: banders
ms.openlocfilehash: 82a3dfa9534d58d425b15a433a69a224235e027f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111542"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Azure Ayrılmış Sanal Makine Örneklerine dahil olmayan yazılım maliyetleri

Sanal makine ayrılmış örneği ve SQL ayrılmış kapasite indirimleri, yazılım maliyetlerine değil, yalnızca altyapı maliyetlerine uygulanır. Windows sanal makinesi kullanıyorsanız ve ayrılmış sanal makine örneklerinizde Azure Hibrit Avantajı yoksa, aşağıdaki bölümde listelenen yazılım ölçümleri için ücretlendirilirsiniz. SQL PaaS dağıtımları için, Azure Hibrit Avantajı seçilmediyse IP maliyeti, ayrı ölçüm kullanılarak ücretlendirilmeye devam eder.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Rezervasyon maliyetine dahil olmayan Windows yazılım ölçümleri

| MeterId | Kullanım dosyasındaki Ölçüm Adı | Kullanan Sanal Makine |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Ayırma - Windows Svr Seri Aktarım (1 Çekirdek) | B Serisi |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Ayırma - Windows Svr Seri Aktarım (2 Çekirdek) | B Serisi |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Ayırma - Windows Svr Seri Aktarım (4 Çekirdek) | B Serisi |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Ayırma - Windows Svr Seri Aktarım (8 Çekirdek) | B Serisi |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Ayırma - Windows Svr (1 Çekirdek) | B Serisi dışında tümü |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Ayırma - Windows Svr (2 Çekirdek) | B Serisi dışında tümü |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Ayırma - Windows Svr (4 Çekirdek) | B Serisi dışında tümü |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Ayırma - Windows Svr (6 Çekirdek) | B Serisi dışında tümü |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Ayırma - Windows Svr (8 Çekirdek) | B Serisi dışında tümü |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Ayırma - Windows Svr (12 Çekirdek) | B Serisi dışında tümü |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Ayırma - Windows Svr (16 Çekirdek) | B Serisi dışında tümü |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Ayırma - Windows Svr (20 Çekirdek) | B Serisi dışında tümü |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Ayırma - Windows Svr (24 Çekirdek) | B Serisi dışında tümü |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Ayırma - Windows Svr (32 Çekirdek) | B Serisi dışında tümü |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Ayırma - Windows Svr (40 Çekirdek) | B Serisi dışında tümü |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Ayırma - Windows Svr (64 Çekirdek) | B Serisi dışında tümü |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Ayırma - Windows Svr (72 Çekirdek) | B Serisi dışında tümü |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Ayırma - Windows Svr (128 Çekirdek) | B Serisi dışında tümü |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Ayırma - Windows Svr (256 Çekirdek) | B Serisi dışında tümü |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Ayırma - Windows Svr (96 Çekirdek) | B Serisi dışında tümü |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Rezervasyon maliyetine dahil olmayan bulut hizmetleri yazılım ölçümleri

| MeterId | Kullanım dosyasındaki Ölçüm Adı |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Bulut Hizmetleri 1 vCPU Lisansı|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Bulut Hizmetleri 2 vCPU Lisansı|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Bulut Hizmetleri 4 vCPU Lisansı|
|13103090-ca72-4825-ab12-7f16c4931d95|Bulut Hizmetleri 8 vCPU Lisansı|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Bulut Hizmetleri 16 vCPU Lisansı|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Bulut Hizmetleri 20 vCPU Lisansı|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Bulut Hizmetleri 32 vCPU Lisansı|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Bulut Hizmetleri 64 vCPU Lisansı|
|7a803026-244c-4659-834c-11e6b2d6b76f|Bulut Hizmetleri 80 vCPU Lisansı|

## <a name="get-rates-for-azure-meters"></a>Azure ölçümleri için fiyatları alın

Bu ölçümlerin her birinin maliyetini Azure RateCard API aracılığıyla alabilirsiniz. Azure ölçümüne yönelik fiyatların nasıl alınacağı hakkında bilgi için bkz. [Azure aboneliğinde kullanılan kaynaklar için fiyat ve meta veri bilgilerini alma](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Sonraki adımlar
Azure rezervasyonları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure rezervasyonları nedir?](billing-save-compute-costs-reservations.md)
- [Azure Ayrılmış VM Örnekleri ile Sanal Makinelere ön ödeme yapma](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure rezervasyonlarını yönetme](billing-manage-reserved-vm-instance.md)
- [Rezervasyon indiriminin nasıl uygulandığını anlama](billing-understand-vm-reservation-charges.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
