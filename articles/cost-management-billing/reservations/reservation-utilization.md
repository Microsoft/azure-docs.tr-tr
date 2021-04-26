---
title: Azure ayırma kullanımını görüntüleme
description: Ayırma kullanımını ve ayrıntılarını nasıl alabileceğinizi öğrenin.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568955"
---
# <a name="view-reservation-utilization-after-purchase"></a>Satın alma işleminden sonra rezervasyon kullanımını görüntüle

Rezervasyon kullanım yüzdesini ve Azure portal ayırmayı kullanan kaynakları ve maliyet yönetimi Power BI uygulamasını görüntüleyebilirsiniz.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Azure RBAC erişimi ile Azure portal kullanımı görüntüleme

Ayırma kullanımını görüntülemek için, rezervasyona Azure RBAC erişiminin olması gerekir veya tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek için yükseltilmiş erişiminizin olması gerekir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. [Rezervasyonlar](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)’a gidin.
1. Listede, Sahip veya Okuyucu rolüne sahip olduğunuz tüm rezervasyonlar gösterilir. Her rezervasyon, bilinen son kullanım yüzdesini gösterir.
1. Kullanım geçmişini ve ayrıntılarını görmek için kullanım yüzdesini seçin. Aşağıdaki videoda bir örnek gösterilmektedir.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Kullanımı faturalandırma Yöneticisi olarak görüntüle

Bir Kurumsal Anlaşma (EA) Yöneticisi veya Microsoft Müşteri Sözleşmesi (MCA) Faturalandırma Yöneticisi, **maliyet yönetimi + Faturalandırmadan** kullanımı görüntüleyebilir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Maliyet yönetimi + faturalandırma**  >  **rezervasyonları**' na gidin.
1. Kullanım geçmişini ve ayrıntılarını görmek için kullanım yüzdesini seçin.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>API’ler, PowerShell ve CLI’yı kullanarak rezervasyonlar ve kullanım alın

Ayrılmış örnek kullanım API 'sini kullanarak [ayırma kullanımını](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) alabilirsiniz.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Power BI’da rezervasyonları ve kullanımı görme

Power BI kullanıcılara yönelik iki seçenek vardır:

- Power BI Desktop rezervasyon satın alma tarihi ve kullanım verileri için Azure maliyet yönetimi Bağlayıcısı, [Power BI Desktop Için Azure maliyet yönetimi Bağlayıcısı](/power-bi/desktop-connect-azure-cost-management)'nda bulunabilir. Bağlayıcıyı kullanarak istediğiniz raporları oluşturun.
- Azure maliyet yönetimi Power BI uygulaması-daha ayrıntılı bir şekilde özelleştirebileceğiniz önceden oluşturulmuş raporlar için [Azure maliyet yönetimi Power BI uygulamasını](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Rezervasyonları’nı yönetin](manage-reserved-vm-instance.md).
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](understand-reserved-instance-usage.md).
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](understand-reserved-instance-usage-ea.md).
- [CSP abonelikleri için rezervasyon kullanımını anlama](/partner-center/azure-reservations).
