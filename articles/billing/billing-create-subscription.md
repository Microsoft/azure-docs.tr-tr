---
title: Ek bir Azure aboneliği oluşturma | Microsoft Docs
description: Azure portalında nasıl yeni bir Azure aboneliği ekleneceğini öğrenin.
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: acb41de8344d1467e86c38c7c99e8ad4ab517ead
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375818"
---
# <a name="create-an-additional-azure-subscription"></a>Ek Azure aboneliği oluşturma

Azure portalında [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Müşteri Sözleşmesi](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) veya [Microsoft İş Ortağı Sözleşmesi](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) ödeme hesabınız için ek bir abonelik oluşturabilirsiniz. Abonelik limitlerine ulaşmaktan kaçınmak için veya güvenlik amacıyla ayrı ortamlar oluşturmak veya uyumluluk nedeniyle verileri yalıtmak için ek bir abonelik isteyebilirsiniz.

Bir Microsoft Çevrimiçi Hizmet Programı (MOSP) ödeme hesabınız varsa, [Azure kaydolma portalında](https://account.azure.com/signup?offer=ms-azr-0003p) ek abonelikler oluşturabilirsiniz.

Ödeme hesapları hakkında daha fazla bilgi edinmek ve ödeme hesabınızın türünü belirlemek için bkz. [Azure portalında ödeme hesaplarını görüntüleme](billing-view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Azure abonelikleri oluşturmak için gereken izin

Abonelik oluşturmak için aşağıdaki izinlere sahip olmanız gerekir:

|Fatura hesabı  |İzin  |
|---------|---------|
|Kurumsal Anlaşma (EA) |  Kurumsal Anlaşma kaydında Hesap Sahibi rolü. Daha fazla bilgi için bkz. [Azure’daki Azure Kurumsal Anlaşma yönetici rollerini anlama](billing-understand-ea-roles.md).    |
|Microsoft Müşteri Sözleşmesi (MCA) |  Fatura bölümünde, faturalama profilinde veya ödeme hesabında sahip veya katkıda bulunan rolü. Veya fatura bölümünde Azure aboneliği oluşturucu rolü.  Daha fazla bilgi için bkz. [Abonelik faturalama rolleri ve görevi](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Microsoft İş Ortağı Sözleşmesi (MPA) |   İş ortağı kuruluşunda Genel Yönetici ve Yönetici Aracısı rolü. Daha fazla bilgi için bkz. [İş Ortağı Merkezi - Kullanıcı rollerini ve izinleri atama](https://docs.microsoft.com/partner-center/permissions-overview)  |

## <a name="create-a-subscription-in-the-azure-portal"></a>Azure portalında abonelik oluşturma

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Abonelikler** araması yapın.

   ![Portalda abonelik aramasını gösteren ekran görüntüsü](./media/billing-create-subscription/billing-search-subscription-portal.png)

1. **Add (Ekle)** seçeneğini belirleyin.

   ![Abonelikler görünümünde Ekle düğmesini gösteren ekran görüntüsü](./media/billing-create-subscription/subscription-add.png)

1. Birden çok ödeme hesabına erişiminiz varsa, abonelik oluşturmak istediğiniz ödeme hesabını seçin.

1. Formu doldurup **Oluştur**'a tıklayın. Aşağıdaki tablolarda, her ödeme hesabı türü için formdaki alanlar listelenmektedir.

**Kurumsal Anlaşma**

|Alan  |Tanım  |
|---------|---------|
|Adı     | Azure portalında aboneliği kolayca belirlemenize yardımcı olan görünen ad.  |
|Sunduğu     | Geliştirme veya test iş yükleri için bu aboneliği kullanmayı planlıyorsanız EA Geliştirme ve Test, diğer durumlarda Microsoft Azure Kurumsal’ı seçin. EA Geliştirme ve Test abonelikleri oluşturmak için kayıt hesabınızda DevTest teklifinin etkinleştirilmesi gerekir.|

**Microsoft Müşteri Sözleşmesi**

|Alan  |Tanım  |
|---------|---------|
|Faturalama profili     | Aboneliğinizin ücretleri, seçtiğiniz faturalama profiline faturalanır. Yalnızca bir faturalama profiline erişiminiz varsa seçim gri olur.     |
|Fatura bölümü     | Aboneliğinizin ücretleri, faturalama profilinin faturasının bu bölümünde görünür. Yalnızca bir fatura bölümüne erişiminiz varsa seçim gri olur.  |
|Planlama     | Geliştirme veya test iş yükleri için bu aboneliği kullanmayı planlıyorsanız DevTest için Microsoft Azure Planı’nı seçin; aksi takdirde Microsoft Azure Planı‘nı kullanın. Faturalama profili için etkinleştirilmiş yalnızca bir plan varsa seçim gri olur.  |
|Adı     | Azure portalında aboneliği kolayca belirlemenize yardımcı olan görünen ad.  |

**Microsoft İş Ortağı Sözleşmesi**

|Alan  |Tanım  |
|---------|---------|
|Müşteri    | Abonelik, seçtiğiniz müşteri için oluşturulur. Yalnızca bir müşteriniz varsa seçim gri olur.  |
|Kurumsal bayi    | Müşteriye hizmet sağlayan kurumsal bayi. Burası isteğe bağlı bir alandır ve yalnızca CSP iki katmanlı modelindeki Dolaylı sağlayıcılar için geçerlidir. |
|Adı     | Azure portalında aboneliği kolayca belirlemenize yardımcı olan görünen ad.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Program aracılığıyla ek Azure aboneliği oluşturma

Ayrıca program aracılığıyla ek abonelikler de oluşturabilirsiniz. Daha fazla bilgi için bkz. [Program aracılığıyla Azure abonelikleri oluşturma](../azure-resource-manager/programmatically-create-subscription.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure aboneliği yöneticileri ekleme veya değiştirme](billing-add-change-azure-subscription-administrator.md)
- [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md?toc=/azure/billing/TOC.json)
- [Kaynak organizasyonu ve yönetimi için yönetim grupları oluşturma](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
- [Azure için aboneliğinizi iptal etme](billing-how-to-cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
