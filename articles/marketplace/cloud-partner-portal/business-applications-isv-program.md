---
title: Microsoft Business Applications bağımsız yazılım satıcısı (ISV) bağlama programı ekleme Kılavuzu
description: Business Applications ISV Connect programına nasıl ekleneceği aşağıda açıklanmaktadır.
services: Azure Marketplace, Cloud Partner Portal
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/12/2019
ms.author: evansma
ms.openlocfilehash: 4a47886176b704cc3bc36cc9fa85d1d3faee4c58
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819163"
---
# <a name="microsoft-business-applications-independent-software-vendor-isv-connect-program-onboarding-guide"></a>Microsoft Business Applications bağımsız yazılım satıcısı (ISV) bağlama programı ekleme Kılavuzu

[Business Applications ISV Connect program](https://partner.microsoft.com/solutions/business-applications/isv-overview) amaçlar, *Dynamics 365 müşteri katılımı ile modern, bulut tabanlı, iş kolu (LOB) çözümleri oluşturma ve bağımsız yazılım satıcılarının (ISV 'ler) genel başarısını hızlandırmaya ve PowerApps* ("DYNAMICS CE teklifleri") veya *Dynamics 365 finans ve işlemler* ("Dynamics Ops teklifleri"). Burada nasıl bir parçası olacak.

> [!IMPORTANT]
> *Dynamics 365 müşteri katılımı*olan mevcut ISV iş ortakları, *finans ve işlemler için Dynamics 365*ve *PowerApps* uygulamaları, bir önceki ekleme adımlarını **15 Ekim 2019** [Microsoft AppSource](https://appsource.microsoft.com)kaldırılan mevcut teklifler.

Business Applications ISV Connect programının [Teknik, pazarlama ve satış öncesi avantajlarından](https://partner.microsoft.com/solutions/business-applications/isv-overview) yararlanmak için, mevcut ISV iş ortaklarına ( *Dynamics CE* ve *Ops* teklifleriyle) Aşağıdaki adımlar:

- [Microsoft Iş Ortağı Merkezi hesabınızı ayarlayın](#set-up-your-microsoft-partner-center-account).
- [Business Applications eki kabul edin](#accept-the-business-applications-isv-program-addendum).
- [Faturalandırma bilgilerinizi ayarlayın](#set-up-your-billing-information).
- [Uygulamanızı yeniden toplayın](#recertify-your-application).

> [!IMPORTANT]
> Varolan bir teklifte değişiklikler yayımlamadan önce yukarıdaki adımların tümünü tamamlamalısınız.

## <a name="set-up-your-microsoft-partner-center-account"></a>Microsoft Iş Ortağı Merkezi hesabınızı ayarlama

[Iş Ortağı Merkezi](https://partner.microsoft.com) , ISV Connect programı için hesap ayarlarınızı yönetme yerdir.

> [!NOTE]
> Bu adım, Iş Ortağı Merkezi 'nde verilen hesabınız için Bulut İş Ortağı Portalı ve *sahip* ya da *yönetici* rolü için bir *sahip* rolü gerektirir.

Dynamics CE veya Dynamics Ops tekliflerini zaten yayımladıysanız, büyük olasılıkla Bulut İş Ortağı Portalı (CPP) hesabınız Iş Ortağı Merkezi 'nde zaten geçirilmiş ve etkinleştirilmiştir. Aksi takdirde, Iş Ortağı Merkezi hesabınızı CPP yayımcı profilinizden el ile etkinleştirebilirsiniz.

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)' de **Yayımcı profili** sayfanızda oturum açın.
2. Hesap ayarlarınızın Iş Ortağı Merkezi 'ne geçirildiğini gösteren bir başlık görürseniz bir sonraki bölüme geçin. Aksi takdirde, Iş Ortağı Merkezi hesabınızı etkinleştirmek isteyip istemediğinizi soran başlığı seçin.

Daha fazla bilgi için bkz. [bulut iş ortağı portalı 'Dan Iş ortağı merkezine hesap geçişi](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

## <a name="accept-the-business-applications-isv-program-addendum"></a>Business Applications ISV program eki 'ni kabul edin

Bu programa katılım, *Business Applications eki*incelemenizi ve kabul etmenizi gerektirir.

> [!NOTE]
> Bu adım, hesabınızın yasal sözleşmeleri imzalamasını sağlamak için Iş Ortağı Merkezi 'nde bir *sahip* veya *yönetici* rolü gerektirir. Önceki adımda [Iş Ortağı Merkezi hesabınızı etkinleştirerek](#set-up-your-microsoft-partner-center-account) bulut iş ortağı portalı hesap geçişinizi tamamladıysanız, Izinleriniz de Iş Ortağı Merkezi ' ne geçirilir.

1. [Iş Ortağı Merkezi panosuna](https://partner.microsoft.com/dashboard/account/agreements) gidin ve Istendiğinde Iş Ortağı Merkezi ' nde oturum açın.

2. **Ticari Market**başlığı altında **Microsoft Business Applications ISV eki**' ni bulun.

3. Sürüm bağlantısını seçin ve sözleşmeyi görüntüleyin.

4. Sözleşmeyi kabul etmek ve kabul etmek için **sözleşmeyi kabul et** ' i seçin.

## <a name="set-up-your-billing-information"></a>Faturalandırma bilgilerinizi ayarlama

Ardından, Business Applications ISV Connect programı için faturalandırma bilgilerinizi ayarlayın.

> [!NOTE]
> Bu adım, hesap için Iş Ortağı Merkezi 'nde faturalama bilgilerini güncelleştirmek üzere bir *sahip* veya *yönetici* rolü gerektirir.

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard)' nde oturum açın.

2. *Ayarlar* (dişli) simgesini seçin ve **Geliştirici ayarları**' nı seçin.

3. *Hesaplar*altında **Faturalandırma profili**' ni seçin.

4. Yasal varlığınızdan doldurulan birincil iletişim ve fatura bilgilerini gözden geçirin ve gerekli güncelleştirmeleri yapın.

5. Varsa vergi KIMLIĞI/KDV KIMLIĞI ayarlarını belirtin.

6. Değişikliklerinizi kaydetmek için **Kaydet** ' i seçin.

Faturalandırma profilinizi dilediğiniz zaman güncelleştirebilirsiniz, ancak *Şirket adı* ve fatura adresi *ülkesi* daha sonra değiştirilemez.

## <a name="recertify-your-application"></a>Uygulamanızı yeniden toplayın

Yukarıdaki adımları tamamladıktan sonra, mevcut Dynamics CE ve Dynamics Ops tekliflerinizin Iş Ortağı Merkezi 'nde düzenlenmesine uygun olması gerekir. Ayrıca, Iş Ortağı Merkezi aracılığıyla yeni teklifler de oluşturabilirsiniz.

Business Applications ISV Connect programında kayıt için son adım, mevcut uygulamalarınızın yeniden belirlenir.

1. Uygulamanızı AppSource 'a geri yüklemek için [Bu adımları izleyin](https://partner.microsoft.com/solutions/business-applications/isv-publish) .
2. [Yeni bir teklif oluşturun ve yayımlayın](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer). Bu teklif tamamen yeni bir teklif olacak ve mevcut bir tane yerine geçmez.

## <a name="further-info"></a>Daha fazla bilgi

Hesabınız hakkında yardım almak için hesap yöneticinize danışın veya [Microsoft Iş ortağı desteği](https://partner.microsoft.com/support) ile iletişime geçin. Business Applications ISV Connect programı ve Microsoft Iş Ortağı Merkezi hakkında genel bilgi için bkz.:

#### <a name="business-applications-for-isvs-online-article"></a>ISV 'Ler için Business Applications (çevrimiçi makale)
https://partner.microsoft.com/solutions/business-applications/isv-overview

#### <a name="overview-of-the-new-program-for-business-applications-isvs-pdf"></a>Business Applications ISV 'Ler için yeni programa genel bakış (PDF)
https://aka.ms/BizAppsISVProgram

#### <a name="business-applications-isv-connect-program-faq-pdf"></a>Business Applications ISV Connect program hakkında SSS (PDF)
https://assetsprod.microsoft.com/business-applications-partner-faq.pdf

#### <a name="upcoming-program-for-business-applications-isvs-blog-post"></a>Business Applications ISV 'Ler için yaklaşan program (blog gönderisi)
https://cloudblogs.microsoft.com/dynamics365/bdm/2019/04/17/upcoming-program-for-business-applications-isvs/
