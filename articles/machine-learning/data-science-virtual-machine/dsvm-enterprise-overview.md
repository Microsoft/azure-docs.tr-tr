---
title: Team Analytics ve AI ortamı
titleSuffix: Azure Data Science Virtual Machine
description: Veri bilimi sanal Makinesi'ne kurumsal bir takım ortamı dağıtmak için desenler.
keywords: derin öğrenme yapay ZEKA, veri bilimi araçları, veri bilimi sanal makinesi, Jeo-uzamsal analiz, team data science Process'i
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 06d05d6d410af13bfbe85f3cb66523c1d48cb77c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195657"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Veri bilimi sanal makinesi tabanlı takım analizi ve yapay ZEKA ortamı 
[Veri bilimi sanal makinesi](overview.md) (dsvm), yapay zeka (AI) ve veri analizi için önceden oluşturulmuş yazılımlarla Azure platformunda zengin bir ortam sağlar.

Geleneksel olarak, bir tek analize yönelik Masaüstü DSVM kullanıldı. Bireysel veri bilimcileri, bu paylaşılan, önceden oluşturulmuş analiz ortamıyla üretkenlik elde edebilir. Büyük analiz takımları, veri bilimcilerinin ve AI geliştiricilerinin ortamlarını planlarken, yinelenen temalardan biri, geliştirme ve deneme için paylaşılan bir analiz altyapısıdır. Bu altyapı, kurumsal BT ilkeleriyle birlikte yönetilir ve bu da veri bilimi ve analiz ekipleri genelinde işbirliğini ve tutarlılığı kolaylaştırır.

Paylaşılan bir altyapı, analiz ortamının daha iyi BT kullanımını mümkün kılar. Bazı kuruluşlar ekip tabanlı veri bilimi/analiz altyapısını bir *analiz korumalı alanı*olarak çağırır. Veri bilimcilerinin verileri hızla anlamak için çeşitli veri varlıklarına erişmesine olanak sağlar. Bu korumalı alan ortamı Ayrıca, veri bilimcilerinin üretim ortamını etkilemeden denemeleri çalıştırmasına, hipotezleri doğrulamasına ve tahmine dayalı modeller oluşturmasına yardımcı olur.

DSVM Azure altyapı düzeyinde çalıştığından, BT yöneticileri kurumsal BT ilkelerle çalışmaya DSVM kolayca yapılandırabilirsiniz. DSVM, çeşitli paylaşım mimarları uygulamak için, aynı zamanda şirket veri varlıklarına denetimli bir şekilde erişim sağlarken tam esneklik sunar.

Bu bölümde, bazı desen ve bir ekip tabanlı veri bilimi altyapısı olarak DSVM dağıtmak için kullanabileceğiniz yönergeleri ele alınmaktadır. Bu desenlerin yapı taşları Azure hizmet olarak altyapı (IaaS) ' den geldiği için, tüm Azure sanal makineleri için geçerlidir. Bu makale serisi, bu standart Azure altyapı özelliklerini DSVM 'ye uygulamaya odaklanır.

Kurumsal ekip Analizi ortamının anahtar yapı taşları şunları içerir:

* [DSVMs tarafından boyutlandırılmış bir havuz](dsvm-pools.md)
* [Ortak kimlik ve havuzdaki Dsvm'leri birinden bir çalışma alanına erişim](dsvm-common-identity.md)
* [Veri kaynaklarına güvenli erişim](dsvm-secure-access-keys.md)


Bu seri, yukarıdaki konuların her biri için rehberlik ve işaretçiler sağlar. Büyük ölçekli kurumsal yapılandırmalarda DSVMs dağıtmaya yönelik tüm konuları ve gereksinimleri kapsamaz. Kuruluşunuzda DSVM örnekleri uygulanırken kullanabileceğiniz bazı diğer Azure kaynakları aşağıda verilmiştir:

* [Ağ güvenliği](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [İzleme](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) ve [Yönetimi](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Günlük kaydı ve denetim](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Rol tabanlı erişim denetimi](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [İlke ayarı ve zorlama](../../governance/policy/overview.md)
* [Kötü Amaçlı Yazılımdan Koruma](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Şifreleme](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Veri bulma ve idare](https://docs.microsoft.com/azure/data-catalog/)

Son olarak [Azure mimari merkezi](https://docs.microsoft.com/azure/architecture/) , bulut tabanlı analiz altyapınızı oluşturmak ve yönetmek için ayrıntılı bir uçtan uca mimari ve modeller sağlar.