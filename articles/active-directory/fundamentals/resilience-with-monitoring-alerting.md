---
title: Azure AD B2C kullanarak izleme ve analiz aracılığıyla esnekliği | Microsoft Docs
description: Azure AD B2C kullanarak izleme ve analiz aracılığıyla esnekliği
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
ms.openlocfilehash: a834d4d30c40b618b1601a7f8901c68143ef4912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648526"
---
# <a name="resilience-through-monitoring-and-analytics"></a>İzleme ve analiz aracılığıyla esnekliği

İzleme, uygulamalarınızın ve hizmetlerinizin kullanılabilirliğini ve performansını en üst düzeye çıkarır. Altyapı ve uygulamalarınızdan telemetri toplamak, analiz etmek ve üzerinde işlem yapmak için kapsamlı bir çözüm sunar. Hizmetiniz veya uygulamalarınızda sorunlar bulunduğunda uyarılar size önceden bildirilir. Bunlar, hizmetinizin son kullanıcıları tarafından bildirilmeksizin sorunları tanımlamanızı ve ele belirlemenizi sağlar. [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) , denetim günlüklerinde ve oturum açma günlüklerinde arama yapmanıza ve özel görünümler oluşturmanıza yardımcı olur.

## <a name="monitor-and-get-notified-through-alerts"></a>Uyarıları izleyin ve uyarılar aracılığıyla bildirim alın

Hizmetlerinizin genel sistem durumunu güvence altına almak için sisteminizin ve altyapınızın izlenmesi kritik öneme sahiptir. Yeni Kullanıcı varışı, son kullanıcının kimlik doğrulama oranları ve dönüştürme gibi iş ölçümlerinin tanımıyla başlar. Bu tür göstergeleri izlemek için yapılandırın. Promosyon veya tatil trafiği nedeniyle yaklaşan bir dalgalanma planlandıysanız, tahminlerinizi özellikle olaya ve iş ölçümlerine ilişkin kıyaslamaya göre gözden geçirin. Olaydan sonra, önceki kıyaslamaya geri dönün.

Benzer şekilde, hataları veya performans kesintilerini algılamak, iyi bir taban çizgisi ayarlamak ve sonra uyarı tanımlamak için bir olmazdır uygulaması, ortaya çıkan sorunlara hemen yanıt verir.

![Görüntü, izleme ve analiz bileşenlerini gösterir](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>İzleme ve uyarı uygulama

- **İzleme**: temel hizmet düzeyi HEDEFLERINE (SLO) göre sistem durumunu sürekli olarak Izlemek Için [Azure izleyici](../../active-directory-b2c/azure-monitor.md) 'yi kullanın ve kritik bir değişiklik olduğunda bildirim alın. Azure AD B2C ilkesi veya bir uygulamayı, sistem durumunun SLO 'yu sürdürmek üzere izlenmesi gereken kritik bir bileşen olarak tanımlayarak başlayın. Yuvalarınız ile hizalı anahtar göstergelerini belirler.
Örneğin, ' de ani bir bırakma bir iş kaybına neden olacağı için aşağıdaki ölçümleri izleyin.

  - **Toplam istek** sayısı: Azure AD B2C ilkeye gönderilen toplam "n" istek sayısı.

  - **Başarı oranı (%)**: başarılı Istekler/toplam istek sayısı.

  Azure AD B2C ilke tabanlı günlüklerin, [Denetim günlüklerinin](../../active-directory-b2c/analytics-with-application-insights.md)ve oturum açma günlüklerinin depolandığı [Application Insights](../../active-directory-b2c/analytics-with-application-insights.md) 'daki [anahtar göstergelerine](../../active-directory-b2c/view-audit-logs.md) erişin.  

   - **Görselleştirmeler**: anahtar göstergelerini görsel olarak Izlemek için Log Analytics derleme panoları kullanma.

   - **Geçerli dönem**: Toplam Istek ve başarı oranı (%) değişikliklerini göstermek için zamana bağlı grafikler oluşturma geçerli dönemde, örneğin, geçerli hafta.

   - **Önceki dönem**: Toplam Isteklerindeki ve başarı oranlarındaki değişiklikleri göstermek için zamana bağlı grafikler oluşturma (%) önceki bir dönem boyunca başvuru amaçlıdır. Örneğin, geçen hafta.

- **Uyarı**: Log Analytics 'in kullanılması, anahtar göstergelerinde ani değişiklikler olduğunda tetiklenen [uyarıları](../../azure-monitor/alerts/alerts-log.md) tanımlar. Bu değişiklikler, SLOs 'yi olumsuz etkileyebilir. Uyarılar, e-posta, SMS ve Web kancaları gibi çeşitli bildirim yöntemleri biçimlerini kullanır. Uyarı tetiklenecek eşik olarak davranan bir ölçüt tanımlayarak başlayın. Örnek:
  - Toplam isteklerin ani olarak düşürülme uyarısı: toplam istek sayısı aniden başlatıldığında bir uyarı tetikleyin. Örneğin, bir önceki döneme kıyasla toplam istek sayısı %25 ' i olduğunda bir uyarı yükseltir.  
  - Başarı oranına göre uyarı (%): seçili ilkenin başarı oranı önemli ölçüde düşerse bir uyarı tetikleyin.
  - Uyarı aldıktan sonra, Azure AD B2C için [Log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md)ve [vs Code uzantısını](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) kullanarak sorunu giderin. Sorunu çözdükten ve güncelleştirilmiş bir uygulamayı veya ilkeyi dağıttıktan sonra, normal aralığa geri dönene kadar anahtar göstergelerini izlemeye devam eder.

- **Hizmet uyarıları**: hizmet sorunları, planlı bakım, sağlık danışmanlığı ve güvenlik danışmanlığı hakkında bildirim almak için [Azure AD B2C hizmet düzeyi uyarılarını](../../service-health/service-health-overview.md) kullanın.

- **Raporlama**: [Log Analytics 'i kullanarak](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), Kullanıcı öngörüleri, teknik sorunlar ve büyüme fırsatları hakkında bilgi sahibi olmanıza yardımcı olan raporlar oluşturun.
  - **Sistem durumu panosu**: Log Analytics sorguları kullanarak grafik eklemeyi destekleyen [Azure Panosu özelliğini kullanarak özel panolar](../../azure-monitor/app/tutorial-app-dashboards.md) oluşturun. Örneğin, başarılı ve başarısız oturum açma düzenlerini, istekleri yapmak için kullanılan cihazlarla ilgili hata nedenlerini ve telemetrileri öğrenin.
  - **Abandon Azure AD B2C It neys**: kullanıcının oturum açma veya kaydolma yolculuğuna başladığı ancak hiç bitmediğini terk edilmiş Azure AD B2C It neys listesini izlemek için [çalışma kitabını](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) kullanın. Bu, bir yolculuğa geçmeden önce Kullanıcı tarafından gerçekleştirilen adımların ilke KIMLIĞI ve dökümü hakkındaki ayrıntıları sağlar.
  - **Azure AD B2C izleme çalışma kitapları**: Azure AD B2C ortamınızın sağlık durumu hakkında daha iyi öngörüler elde etmek için Azure AD B2C Pano, Multi-Factor AUTHENTICATION (MFA) Işlemleri, koşullu erişim raporu ve Bağıntıkimliği ile arama günlükleri içeren [izleme çalışma kitaplarını](https://github.com/azure-ad-b2c/siem)kullanın.
  
## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C geliştiricileri için esnekliği kaynakları](resilience-b2c.md)
  - [Dayanıklı son kullanıcı deneyimi](resilient-end-user-experience.md)
  - [Dış işlemlerle esnek arabirimler](resilient-external-processes.md)
  - [Geliştirici en iyi uygulamaları esnekliği](resilience-b2c-developer-best-practices.md)
- [Kimlik doğrulama altyapınızda esnekliği oluşturma](resilience-in-infrastructure.md)
- [Uygulamalarınızda kimlik doğrulama ve yetkilendirme esnekliği artırın](resilience-app-development-overview.md)