---
title: Windows sanal masaüstünü Azure Sentinel 'e bağlama | Microsoft Docs
description: Windows sanal masaüstü verilerinizi Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380302"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Windows sanal masaüstü verilerini Azure Sentinel 'e bağlama

Bu makalede, Azure Sentinel kullanarak Windows sanal masaüstü (WVD) ortamlarınızı nasıl izleyebileceğinizi açıklanmaktadır.

Örneğin, Windows sanal masaüstü ortamlarınızı izlemek, kuruluşunuzun güvenlik duruşunu koruyarak sanallaştırılmış Masaüstleri kullanarak daha fazla uzak iş sağlamanıza olanak sağlayabilir.

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Azure Sentinel 'de Windows sanal masaüstü verileri

Azure Sentinel 'de Windows sanal masaüstü verileri aşağıdaki türleri içerir:


|Veriler  |Açıklama  |
|---------|---------|
|**Windows olay günlükleri**     |  WVD ortamından Windows olay günlükleri, WVD ortamının dışında, diğer Windows makinelerinden Windows olay günlükleriyle aynı şekilde Azure Sentinel özellikli bir Log Analytics çalışma alanına akışla kaydedilir. <br><br>Log Analytics aracısını Windows makinenize yükleyip Windows olay günlüklerini Log Analytics çalışma alanına gönderilecek şekilde yapılandırın.<br><br>Daha fazla bilgi için bkz.<br>- [Windows bilgisayarlarına Log Analytics Aracısı 'nı yükler](/azure/azure-monitor/agents/agent-windows)<br>- [Log Analytics aracısıyla Windows olay günlüğü veri kaynaklarını toplama](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [Windows Güvenlik olaylarını bağlama](connect-windows-security-events.md)       |
|**Endpoint (MDE) için Microsoft Defender uyarıları**     |  Windows sanal masaüstü için MDE 'yi yapılandırmak için, diğer Windows uç noktaları için kullandığınız yordamın aynısını kullanın. <br><br>Daha fazla bilgi için bkz. <br>- [Endpoint Deployment için Microsoft Defender 'ı ayarlama](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Microsoft 365 Defender 'dan Azure Sentinel 'e veri bağlama](connect-microsoft-365-defender.md)       |
|**Windows sanal masaüstü tanılaması**     | Windows sanal masaüstü Tanılaması, Windows sanal masaüstü PaaS hizmeti 'nin bir özelliğidir ve bu, Windows sanal masaüstü rolü atanan bir kullanıcı tarafından hizmeti kullandığında bilgileri günlüğe kaydeder. <br><br>Her günlük, etkinlikte hangi Windows sanal masaüstü rolünün söz konusu olduğunu, oturum sırasında görünen tüm hata iletilerini, kiracı bilgilerini ve Kullanıcı bilgilerini içerir. <br><br>Tanılama özelliği, hem Kullanıcı hem de yönetim eylemleri için etkinlik günlükleri oluşturur. <br><br>Daha fazla bilgi için bkz. [Windows sanal masaüstündeki tanılama özelliği için Log Analytics kullanma](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019).        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Windows sanal masaüstü verilerini bağlama

Windows sanal masaüstü verilerini Azure Sentinel 'e aktarmak için Windows sanal masaüstü belgelerindeki yönergeleri kullanın.

Daha fazla bilgi için bkz. [Windows sanal masaüstü verilerini Log Analytics çalışma alanınıza gönderme](/azure/virtual-desktop/diagnostics-log-analytics).

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, Log Analytics Verilerinize karşı Azure Sentinel 'de sorgular çalıştırın.

Örneğin, [Windows sanal masaüstü belgelerinden](/azure/virtual-desktop/diagnostics-log-analytics)örnek sorgular bölümüne bakın.


Azure Sentinel, **genel**  >  **Günlükler**  >  **WINDOWS sanal masaüstü** alanında yerleşik sorgular da sağlar:

[![Azure Sentinel 'de Windows sanal masaüstü yerleşik sorguları. ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar


Daha fazla bilgi için bkz. [Windows sanal masaüstü Için Azure İzleyicisi sözlük sözlüğü](/azure/virtual-desktop/azure-monitor-glossary).
