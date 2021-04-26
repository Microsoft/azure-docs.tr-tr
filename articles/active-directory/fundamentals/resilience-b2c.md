---
title: Azure AD B2C kullanarak müşteri kimliği ve erişim yönetimi 'nde esnekliği oluşturun | Microsoft Docs
description: Azure AD B2C kullanarak müşteri kimliği ve erişim yönetimi 'nde esnekliği oluşturma yöntemleri
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724900"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile Müşteri Kimliğiniz ve erişim yönetiminde esnekliği oluşturun

[Azure Active Directory (ad) B2C](../../active-directory-b2c/overview.md) , kritik müşterilere yönelik uygulamalarınızı başarıyla başlatmanıza yardımcı olmak üzere tasarlanan bir müşteri kimliği ve erişim yönetimi (cıhar) platformudur. Hizmetlerimizin gereksinimlerinize göre ölçeklendirilmesine yardımcı olmak ve olası kesinti durumlarında esnekliği 'i geliştirmek üzere tasarlanan [esnekliği](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) için birçok yerleşik özelliği sunuyoruz. Ayrıca, görev açısından kritik bir uygulama başlatırken, uygulamanızda çeşitli tasarım ve yapılandırma öğelerinin yanı sıra, kesinti veya başarısızlık senaryolarına yanıt olarak dayanıklı bir davranış almanızı sağlamak için uygulamanın Azure AD B2C içinde nasıl yapılandırıldığına göz önünde bulundurmanız önemlidir. Bu makalede, esnekliği artırmanıza yardımcı olacak en iyi uygulamalardan bazılarını ele alacağız.

Dayanıklı bir hizmet, kesintiler olmasına rağmen çalışmaya devam eden bir hizmettir. Şu şekilde hizmetinize esnekliği artırılmasına yardımcı olabilirsiniz:

- Tüm bileşenleri anlama

- tek başarısızlık noktalarını ortadan kaldırma

- Hatalı bileşenlerin etkilerini sınırlamak için yalıtma

- hızlı yük devretme mekanizmaları ve Kurtarma yolları ile artıklık sağlama

Uygulamanızı geliştirirken, çözümünüzün kimlik bileşenleriyle uygulamanızdaki [kimlik doğrulama ve yetkilendirme esnekliği nasıl artırabileceğinizi](resilience-app-development-overview.md) göz önünde bulundurmanızı öneririz. Bu makale, Azure AD B2C uygulamalarına özgü esnekliği yönelik geliştirmeleri ele kurmaya çalışır. Önerilerimiz, CıHAR işlevlerine göre gruplandırılır.

![Görüntü, ](media/resilience-b2c/high-level-components.png) sonraki bölümlerde CII bileşenlerini gösterir, esnekliği oluşturmak için aşağıdaki alanlarda size kılavuzluk ederiz:

- [Son Kullanıcı deneyimi](resilient-end-user-experience.md): kimlik doğrulama akışınız için bir geri dönüş planı etkinleştirin ve Azure AD B2C kimlik doğrulama hizmetinin kesintiye uğramasını azaltmaya yönelik olası etkiyi azaltın.

- [Dış süreçlerle arabirimler](resilient-external-processes.md): hatalardan kurtararak uygulamalarınızda ve arabirimlerde esnekliği oluşturun.  

- [Geliştirici en iyi uygulamaları](resilience-b2c-developer-best-practices.md): genel özel ilke sorunları nedeniyle kırılganlığına önleyin ve talep Doğrulayıcıları, üçüncü taraf UYGULAMALARı ve REST API 'leri ile etkileşimler gibi alanlarda hata işlemeyi geliştirir.

- [İzleme ve analiz](resilience-with-monitoring-alerting.md): anahtar göstergelerini izleyerek ve uyarı ile performans kesintilerini algılayarak hizmetinizin sistem durumunu değerlendirin.

- [Kimlik doğrulama altyapınızda esnekliği oluşturma](resilience-in-infrastructure.md)

- [Uygulamalarınızda kimlik doğrulama ve yetkilendirme esnekliği artırın](resilience-app-development-overview.md)

Azure AD B2C kullanarak esnek ve ölçeklenebilir akışlar oluşturmayı öğrenmek için bu videoyu izleyin.
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]
