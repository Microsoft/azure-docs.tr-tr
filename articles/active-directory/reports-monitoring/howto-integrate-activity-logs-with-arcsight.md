---
title: Günlükleri Azure Izleyici kullanarak Arcizle tümleştirin | Microsoft Docs
description: Azure Izleyici 'yi kullanarak Azure Active Directory günlüklerini Arcgörüş ile tümleştirmeyi öğrenin
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7bcab04da005fd0d46d18e7b708dcb1c9d58e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89230526"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Azure Izleyici kullanarak Azure Active Directory günlüklerini Arcizle tümleştirin

[Micro Focus Arcgörüş](https://software.microfocus.com/products/siem-security-information-event-management/overview) , platformunuzun güvenlik tehditlerini algılamanıza ve bunlara yanıt vermenize yardımcı olan bir güvenlik bilgileri ve olay yönetimi (SIEM) çözümüdür. Artık Azure AD 'ye yönelik Arcizme bağlayıcısını kullanarak Azure Izleyici 'yi kullanarak Azure Active Directory (Azure AD) günlüklerini Arcgörüş alanına yönlendirebilirsiniz. Bu özellik, kiracınızı kullanarak kiracınızı güvenlik güvenliğinin aşılmasına karşı izlemenizi sağlar.  

Bu makalede, Azure Izleyici kullanarak Azure AD günlüklerini Arcize yönlendirmeyi öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar

Bu özelliği kullanmak için şunlara ihtiyacınız vardır:
* Azure AD etkinlik günlüklerini içeren bir Azure Olay Hub 'ı. [Etkinlik günlüklerinizi bir olay hub 'ına akışa](./tutorial-azure-monitor-stream-logs-to-event-hub.md)alma hakkında bilgi edinin. 
* Arcgözetimi syslog, Daemon SmartConnector (SmartConnector) veya Arcgörüş Load Balancer yapılandırılmış bir örneği. Olaylar Arcgörüş Load Balancer gönderilirse, bu, sonuçta Load Balancer tarafından SmartConnector 'a gönderilir.

[Azure Izleyici Olay Hub 'ı Için Arcgörüş SmartConnector yapılandırma kılavuzunu](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)indirip açın. Bu kılavuz, Azure Izleyici için Arcgörüş SmartConnector 'ı yüklemek ve yapılandırmak için gereken adımları içerir. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Azure AD günlüklerini Arcgörüş ile tümleştirme

1. İlk olarak, yapılandırma kılavuzunun **Önkoşullar** bölümündeki adımları uygulayın. Bu bölüm aşağıdaki adımları içerir:
    * Bağlayıcıyı dağıtmak ve yapılandırmak için **sahip** rolüne sahip bir kullanıcı olduğundan emin olmak için Azure 'da Kullanıcı izinlerini ayarlayın.
    * Sunucu üzerindeki bağlantı noktalarını Syslog NG Daemon SmartConnector ile açın, bu nedenle Azure 'dan erişilebilir. 
    * Dağıtım bir Windows PowerShell betiği çalıştırır, bu yüzden bağlayıcıyı dağıtmak istediğiniz makinede betikleri çalıştırmak için PowerShell 'i etkinleştirmeniz gerekir.

2. Bağlayıcıyı dağıtmak için yapılandırma kılavuzunun **bağlayıcı dağıtma** bölümündeki adımları izleyin. Bu bölümde, bağlayıcının nasıl indirileceği ve ayıklanacağı, uygulama özelliklerinin nasıl yapılandırılacağı ve dağıtım betiği ayıklanan klasörden nasıl çalıştırılacağı anlatılmaktadır. 

3. Bağlayıcının ayarlandığından ve düzgün çalıştığından emin olmak için **Azure 'Da dağıtımı doğrulama** bölümündeki adımları kullanın. Aşağıdakileri doğrulayın:
    * Önkoşul Azure işlevleri, Azure aboneliğinizde oluşturulur.
    * Azure AD günlükleri doğru hedefe akışla kaydedilir. 
    * Dağıtımınızdaki uygulama ayarları, Azure Işlev uygulamalarındaki uygulama ayarlarında kalıcı hale getirilir. 
    * Arcgörüş için yeni bir kaynak grubu Azure 'da, Arcgörüş bağlayıcı ve CEF biçimindeki eşlenmiş dosyaları içeren depolama hesapları için bir Azure AD uygulamasıyla oluşturulur.

4. Son olarak, yapılandırma kılavuzunun **dağıtım sonrası yapılandırmalarında** dağıtım sonrası adımlarını tamamlayabilirsiniz. Bu bölümde, işlev uygulamalarının bir zaman aşımı süresinden sonra boşta kalmasını engellemek, Olay Hub 'ından kaynak günlüklerinin akışını yapılandırmak ve yeni oluşturulan depolama hesabıyla ilişkilendirmek için Syslog ng Daemon akıllı bağlayıcı anahtar deposu sertifikasını güncelleştirmek üzere bir App Service planınız varsa ek yapılandırmanın nasıl gerçekleştirileceği açıklanmaktadır.

5. Yapılandırma Kılavuzu Ayrıca, Azure 'daki bağlayıcı özelliklerini özelleştirmeyi ve bağlayıcının nasıl yükseltileceğini ve kaldırılacağını açıklar. Ayrıca, olay yükü tek bir Syslog Daemon akıllı bağlayıcısının işleyebileceği değerden daha büyükse, performans geliştirmeleriyle ilgili bir [Azure tüketim planına](https://azure.microsoft.com/pricing/details/functions) yükseltme ve ArcLoad Balancer görüş yapılandırma dahil bir bölüm de vardır.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Izleyici Olay Hub 'ı için Arcgörüş SmartConnector yapılandırma kılavuzu](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)