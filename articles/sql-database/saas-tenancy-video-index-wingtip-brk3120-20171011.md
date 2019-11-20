---
title: SaaS SQL uygulaması videosu
description: Bu makalede, 11 Ekim 2017 olan Ignite konferansında SaaS DB kiracı uygulaması tasarımı hakkında 81 dakikalık videolarımızda çeşitli zaman noktaları dizine yer vardır. Sizi ilgilendiren bölüme atlayabilirsiniz. En az 3 desen açıklanmaktadır. Geliştirme ve yönetimi kolaylaştıran Azure özellikleri açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 0194d99a10eb34ecd8ad10fa0a1509baf8436381
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818472"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Azure SQL veritabanı kullanılarak çok kiracılı SaaS uygulaması için dizinli ve açıklama eklenmiş video

Bu makale, SaaS kiracı modelleri veya desenleri hakkında 81 dakikalık bir videonun zaman konumlarında açıklamalı bir dizindir. Bu makale, video ile ilgili olarak sizi ilgilendiren kısmı veya ileri atmanızı sağlar. Videoda, Azure SQL veritabanı 'nda çok kiracılı bir veritabanı uygulaması için ana tasarım seçenekleri açıklanmaktadır. Videoda tanıtımlar, yönetim kodu talimatları, yazılı belgelerimizde olduğu gibi deneyimle daha ayrıntılı bilgi verilmektedir.

Video, yazılı belgelerimizde bulunan bilgileri şu adreste artırır: 
- *Kavramsal:* [çok kiracılı SaaS veritabanı Kiracı desenleri][saas-concept-design-patterns-563e]
- *Öğreticiler:* [Wingtip bilet SaaS uygulaması][saas-how-welcome-wingtip-app-679t]

Video ve makaleler, Bulutta Azure SQL veritabanı 'nda çok kiracılı bir uygulama oluşturmanın birçok aşamasını anlatmaktadır. Azure SQL veritabanı 'nın özel özellikleri, yönetimi ve güvenilir bir performans sağlamak için daha kolay olan çok kiracılı uygulamalar geliştirmeyi ve uygulamayı kolaylaştırır.

Yazılı belgelerimizi düzenli olarak güncelleştirdik. Video düzenlenmez veya güncelleştirilmemiş, sonuçta daha fazla ayrıntı güncel hale gelebilir.



## <a name="sequence-of-38-time-indexed-screenshots"></a>38 sırasında zaman dizinli ekran görüntüleri dizisi

Bu bölüm, 81 dakikalık videonun tamamında 38 tartışmasında saat konumunu dizinliyor. Her dizin, videodan bir ekran görüntüsü ile ve bazen ek bilgilerle açıklanmalıdır.

Her seferinde Dizin *s:DD: ss*biçimindedir. Örneğin, **oturum hedefleri**etiketli ikinci dizinli saat konumu **0:03:11**' nin yaklaşık saat konumunda başlar.


### <a name="compact-links-to-video-indexed-time-locations"></a>Video dizinli zaman konumlarına yönelik Compact bağlantıları

Aşağıdaki başlıklar, bu makalenin ilerleyen kısımlarında karşılık gelen ek açıklamalı bölümlerinin bağlantılardır:

- [1. **(başlangıç)** hoş geldiniz slaydı, 0:00:03](#anchor-image-wtip-min00001)
- [2. oturum hedefleri, 0:03:11](#anchor-image-wtip-min00311)
- [3. gündem, 0:04:17](#anchor-image-wtip-min00417)
- [4. çok kiracılı web uygulaması, 0:05:05](#anchor-image-wtip-min00505)
- [5. uygulama Web formu, işlem aşamasında, 0:05:55](#anchor-image-wtip-min00555)
- [6. kiracı başına maliyet (ölçek, yalıtım, kurtarma), 0:09:31](#anchor-image-wtip-min00931)
- [7. çok kiracılı veritabanı modelleri: profesyonelleri ve dezavantajları, 0:11:59](#anchor-image-wtip-min01159)
- [8. Karma model, MT/ST, 0:13:01 avantajlarından yararlanır](#anchor-image-wtip-min01301)
- [9. tek kiracılı ve çok kiracılı: profesyonelleri ve dezavantajları, 0:16:44](#anchor-image-wtip-min01644)
- [10. havuzlar öngörülemeyen iş yükleri için düşük maliyetli, 0:19:36](#anchor-image-wtip-min01936)
- [11. kiracı başına veritabanı ve karma ST/MT, 0:20:08 gösterimi](#anchor-image-wtip-min02008)
- [12. dojo, 0:20:29 gösteren canlı uygulama formu](#anchor-image-wtip-min02029)
- [13. MYOB ve bir DBA, 0:28:54](#anchor-image-wtip-min02854)
- [14. MYOB elastik havuz kullanım örneği, 0:29:40](#anchor-image-wtip-min02940)
- [15. MYOB ve diğer ISV 'leri öğrenme, 0:31:36](#anchor-image-wtip-min03136)
- [16. desenler E2E SaaS senaryosunda, 0:43:15](#anchor-image-wtip-min04315)
- [17. kurallı karma çok kiracılı SaaS uygulaması, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS örnek uygulaması, 0:48:10](#anchor-image-wtip-min04810)
- [19. öğreticilerde araştırılan senaryolar ve desenler 0:49:10](#anchor-image-wtip-min04910)
- [20. öğreticiler ve GitHub deposunun tanıtımı, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub deposu Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. desenleri keşfetme, 0:56:20](#anchor-image-wtip-min05620)
- [23. kiracılar sağlama ve ekleme, 0:57:44](#anchor-image-wtip-min05744)
- [24. kiracılar ve uygulama bağlantısı sağlama, 0:58:58](#anchor-image-wtip-min05858)
- [25. tek bir kiracı sağlayan yönetim betikleri gösterimi, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell için sağlama ve Katalog, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. öngörülemeyen kiracı iş yüklerini yönetme, 1:04:36](#anchor-image-wtip-min10436)
- [29. elastik havuz izleme, 1:06:39](#anchor-image-wtip-min10639)
- [30. yük oluşturma ve performans izleme, 1:09:42](#anchor-image-wtip-min10942)
- [31. şema yönetimi, 1:10:33](#anchor-image-wtip-min11033)
- [32. kiracı veritabanları arasında dağıtılmış sorgu, 1:12:21](#anchor-image-wtip-min11221)
- [33. Bilet oluşturma tanıtımı, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS geçici analiz, 1:12:46](#anchor-image-wtip-min11246)
- [35. kiracı verilerini SQL DW 'ye ayıklayın, 1:16:32](#anchor-image-wtip-min11632)
- [36. günlük satış dağıtımının grafiği, 1:16:48](#anchor-image-wtip-min11648)
- [37. kaydırın ve eylemi çağırın, 1:19:52](#anchor-image-wtip-min11952)
- [38. daha fazla bilgi için kaynaklar, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Videoda açıklamalı Dizin zaman konumları

Herhangi bir ekran görüntüsü görüntüsüne tıkladığınızda videoda tam zaman konumu yer alır.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(başlangıç)* hoş geldiniz slaydı, 0:00:01

*MYOB 'den öğrenme: Azure SQL veritabanı 'nda SaaS uygulamaları için tasarım desenleri-BRK3120*

[![Hoş geldiniz slaydı][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Başlık: MYOB 'den öğrenme: Azure SQL veritabanı 'nda SaaS uygulamaları için tasarım desenleri
- Bill.Gibson@microsoft.com
- Sorumlu Program Yöneticisi, Azure SQL veritabanı
- Microsoft Ignite oturumu BRK3120, Orlando, FL ABD, Ekim/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. oturum hedefleri, 0:01:53
[![Oturum hedefleri][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Çok kiracılı uygulamalar için, uzmanlardan ve dezavantajlarla alternatif modeller.
- Geliştirme, yönetim ve kaynak maliyetlerini azaltmak için SaaS desenleri.
- Örnek bir uygulama ve betikler.
- PaaS özellikleri + SaaS desenleri SQL veritabanını çok kiracılı SaaS için yüksek düzeyde ölçeklenebilir, uygun maliyetli bir veri platformu yapar.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. gündem, 0:04:09
[![Gündem][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. çok kiracılı web uygulaması, 0:05:00
[![Wingtip SaaS uygulaması: çok kiracılı web uygulaması][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. uygulama Web formu, işlem aşamasında, 0:05:39
[![Uygulama Web formu eylemde][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. kiracı başına maliyet (ölçek, yalıtım, kurtarma), 0:06:58
[![Kiracı başına maliyet, ölçek, yalıtım, kurtarma][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. çok kiracılı veritabanı modelleri: profesyonelleri ve dezavantajları, 0:09:52
[![Çoklu kiracı için veritabanı modelleri: profesyonelleri ve dezavantajları][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Karma model, MT/ST, 0:12:29 avantajlarından yararlanır
[![Karma model, MT/ST 'nin avantajlarını karıştırır][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. tek kiracılı ve çok kiracılı: profesyonelleri ve dezavantajları, 0:13:11
[![Tek kiracılı ve çok kiracılı-kiracı karşılaştırması: profesyonelleri ve dezavantajları][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. havuzlar öngörülemeyen iş yükleri için düşük maliyetli, 0:17:49
[![Havuzlar öngörülemeyen iş yükleri için uygun maliyetli bir hesaplıdır][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. kiracı başına veritabanı ve karma ST/MT, 0:19:59 gösterimi
[![Kiracı başına veritabanı ve karma ST/MT tanıtımı][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. dojo, 0:20:10 gösteren canlı uygulama formu
[![Dojo gösteren canlı uygulama formu][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB ve bir DBA, 0:25:06
[![Bir DBA değil MYOB][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB elastik havuz kullanım örneği, 0:29:30
[![MYOB elastik havuz kullanım örneği][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. MYOB ve diğer ISV 'leri öğrenme, 0:31:25
[![MYOB ve diğer ISV 'lerden öğrenme][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. desenler E2E SaaS senaryosunda, 0:31:42
[![E2E SaaS senaryosuna desenler oluşturma][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. kurallı karma çok kiracılı SaaS uygulaması, 0:46:04
[![Kurallı karma çok kiracılı SaaS uygulaması][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS örnek uygulaması, 0:48:01
[![Wingtip SaaS örnek uygulaması][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. öğreticilerde araştırılan senaryolar ve desenler 0:49:00
[![Öğreticilerde araştırılan senaryolar ve desenler][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. öğreticiler ve GitHub deposunun tanıtımı, 0:50:12
[![Tanıtım öğreticileri ve GitHub deposu][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub deposu Microsoft/WingtipSaaS, 0:50:32
[![GitHub deposu Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. desenleri keşfetme, 0:56:15
[![Desenleri keşfetme][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. kiracılar sağlama ve ekleme, 0:56:19
[![Kiracılar ve ekleme sağlama][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. kiracılar ve uygulama bağlantısı sağlama, 0:57:52
[![Kiracılar ve uygulama bağlantısı sağlanıyor][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. tek bir kiracı sağlayan yönetim betikleri gösterimi, 0:59:36
[![Tek bir kiracıyı sağlayan yönetim betikleri tanıtımı][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell için sağlama ve Katalog, 0:59:56
[![Sağlama ve kataloğa yönelik PowerShell][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. öngörülemeyen kiracı iş yüklerini yönetme, 1:03:34
[![Öngörülemeyen kiracı iş yüklerini yönetme][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. elastik havuz izleme, 1:06:32
[![Elastik havuz izleme][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. yük oluşturma ve performans izleme, 1:09:37
[![Yük oluşturma ve performans izleme][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. şema yönetimi, 1:09:40
[![Ölçekte şema yönetimi][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. kiracı veritabanları arasında dağıtılmış sorgu, 1:11:18
[![Kiracı veritabanları arasında dağıtılmış sorgu][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Bilet oluşturma tanıtımı, 1:12:28
[![Bilet oluşturma tanıtımı][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS geçici analiz, 1:12:35
[![SSMS geçici analiz][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. kiracı verilerini SQL DW 'ye ayıklayın, 1:15:46
[![Kiracı verilerini SQL DW 'ye Ayıkla][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. günlük satış dağıtımının grafiği, 1:16:38
[![Günlük satış dağılımının grafiği][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. kaydırın ve eylemi çağırın, 1:17:43
[![Yukarı kaydırın ve eylemi çağırın][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. daha fazla bilgi için kaynaklar, 1:20:35
[![Daha fazla bilgi için kaynaklar][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blog gönderisi, 22 Mayıs 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Kavramsal:* [çok kiracılı SaaS veritabanı Kiracı desenleri][saas-concept-design-patterns-563e]

- *Öğreticiler:* [Wingtip bilet SaaS uygulaması][saas-how-welcome-wingtip-app-679t]

- Wingtip bilet SaaS kiracı uygulamasının türleri için GitHub depoları:
    - [GitHub deposu Için tek başına uygulama modeli][github-wingtip-standaloneapp].
    - [Kiracı modeli başına-DB Için GitHub deposu][github-wingtip-dbpertenant].
    - [-Multi-tenant DB modeli Için GitHub deposu][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Sonraki adımlar

- [İlk öğretici makalesi][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Hoş geldiniz slaydı"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Oturum hedefleri."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Gündem."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS uygulaması: çok kiracılı web uygulaması"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Uygulama Web formu eylemde"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Kiracı başına maliyet, ölçek, yalıtım, kurtarma"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Çoklu kiracı için veritabanı modelleri: profesyonelleri ve dezavantajları"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Karma model, MT/ST 'nin avantajlarını karıştırır"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Tek kiracılı ve çok kiracılı-kiracı karşılaştırması: profesyonelleri ve dezavantajları"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Havuzlar öngörülemeyen iş yükleri için uygun maliyetli bir hesaplıdır"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Kiracı başına veritabanı ve karma ST/MT tanıtımı"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Dojo gösteren canlı uygulama formu"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "Bir DBA değil MYOB"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "MYOB elastik havuz kullanım örneği"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "MYOB ve diğer ISV 'lerden öğrenme"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "E2E SaaS senaryosuna desenler oluşturma"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Kurallı karma çok kiracılı SaaS uygulaması"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS örnek uygulaması"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Öğreticilerde araştırılan senaryolar ve desenler"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Öğreticiler ve GitHub deposunun tanıtımı"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub deposu Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Desenleri keşfetme"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Kiracılar ve ekleme sağlama"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Kiracılar ve uygulama bağlantısı sağlanıyor"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Tek bir kiracıyı sağlayan yönetim betikleri tanıtımı"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "Sağlama ve kataloğa yönelik PowerShell"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Öngörülemeyen kiracı iş yüklerini yönetme"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Elastik havuz izleme"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Yük oluşturma ve performans izleme"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Ölçekte şema yönetimi"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Kiracı veritabanları arasında dağıtılmış sorgu"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Bilet oluşturma tanıtımı"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS geçici analiz"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Kiracı verilerini SQL DW 'ye Ayıkla"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Günlük satış dağılımının grafiği"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Yukarı kaydırın ve eylemi çağırın"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Daha fazla bilgi için kaynaklar"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

