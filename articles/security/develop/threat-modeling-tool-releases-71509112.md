---
title: Threat Modeling Tool yayınları-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Tehdit modelleme aracı için sürüm notlarını belgeleme
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: ce5f2b5541da3b3a3db046ba0dc768e3fbe2431e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68928111"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA sürüm 7.1.50911.2-9/12/2018

Microsoft Threat Modeling Tool artık desteklenen genel kullanıma açık (GA) bir yayın olarak indirileceği hakkında duyurmaktan mutluluk duyuyoruz. Bu sürüm, önemli gizlilik ve güvenlik güncelleştirmelerinin yanı sıra hata düzeltmeleri, özellik güncelleştirmeleri ve kararlılık geliştirmeleri içerir. 2017 Preview sürümünün mevcut kullanıcılarının, istemcisini açmadan önce ClickOnce teknolojisi aracılığıyla en son sürüme güncelleştirmesi istenir. Aracın yeni kullanıcıları için, [istemciyi indirmek üzere buraya tıklayın](https://aka.ms/threatmodelingtool).

Bu sürümle, 2017 önizlemesi için desteği sonlandırıyoruz ve önizleme güncelleştirmesinin tüm kullanıcılarını GA sürümüne öneririz. 15 2018 Ekim 'de veya sonrasında, Threat Modeling Tool için gereken en düşük ClickOnce sürümünü ayarlayacağız ve tüm önizleme istemcilerinin yükseltilmesi gerekecektir.

[Microsoft Indirme merkezi](https://www.microsoft.com/en-us/download/details.aspx?id=49168)'nden erişilebilen Microsoft Threat Modeling Tool 2016, yalnızca kritik güvenlik düzeltmeleri Için 1 2019 Ekim 'e kadar desteklenmeye devam eder.

## <a name="feature-changes"></a>Özellik değişiklikleri

### <a name="azure-stencil-updates"></a>Azure kalıbı güncelleştirmeleri

Ek Azure kalıpları ve bunlarla ilişkili tehditler ve azaltıcı etkenler, bu sürümle birlikte, şablon kümesi aktarımına eklenmiştir. "Azure Uygulama Hizmetleri", "Azure veritabanı teklifleri" ve "Azure Storage" odaklanma alanlarında önemli değişiklikler yapılmıştır.

![Azure kalıbı güncelleştirmeleri](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>OneDrive tümleştirme özelliği kaldırıldı

Önizlemenin "OneDrive 'A Kaydet", "OneDrive 'Dan aç" ve "bağlantı paylaşma" özellikleri kaldırılmıştır. OneDrive kullanıcılarının, OneDrive 'da depolanan dosyalarına standart dosya Kaydet ve Aç iletişim kutuları aracılığıyla erişmesi için Microsoft 'un [OneDrive Windows](https://onedrive.live.com/about/en-us/download/) istemcisini kullanması önerilir.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Müşteriler tarafından bildirilen Notable düzeltilen hatalar

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>TMT önizlemesinde, standart şablon kullanılırken araç çöküyor

- Bir genel şablon (örneğin, "genel veri akışı") çizim yüzeyine eklendiğinde ve tehditler oluşturduğunda araç kilitlenebilir. Bu sorun düzeltilmiştir.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>TMT önizlemesinde, bir raporu kaydettiğimde veya tehditleri kopyaladığınızda risk düzeyleri yanlış

- Bir Kullanıcı belirli tehditlerin risk düzeyini değiştirirse ve bir raporu kaydederse veya riskleri kopyayorsa, risk düzeyi "yüksek" olarak dönebilir. Bu sorun düzeltilmiştir.

## <a name="known-issues-and-faq"></a>Bilinen sorunlar ve SSS

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Yüksek çözünürlüklü ekranların kullanıcıları tehdit özelliklerinde küçük metin ile karşılaşabilir

#### <a name="issue"></a>Sorun

Aracın analiz görünümünde, kullanıcının Windows 'da okunabilirlik için varsayılan olarak ayarlanmış yüksek çözünürlüklü bir ekranı varsa, bir tehdidin "olası risk azaltma" bölümü küçük bir metinle görünebilir.

![Yüksek çözünürlüklü ekranlarda bilinen sorun](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Geçici Çözüm

Kullanıcı azaltma metnine tıklayabilir ve standart Windows Yakınlaştırma denetimini (Crtl-fare tekerleği yukarı) kullanarak bu bölümün büyütme oranını artırabilir.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Ana pencerenin "son açılan modeller" bölümündeki dosyalar açılamayabilir

#### <a name="issue"></a>Sorun

Önizleme sürümünün "OneDrive 'Dan aç" özelliği kaldırılmıştır. OneDrive 'a kaydedilen "son açılan modeller" olan kullanıcılar aşağıdaki hatayı alır.

![OneDrive özelliği kaldırıldı](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Geçici Çözüm

OneDrive kullanıcıları, OneDrive 'da depolanan dosyalarına standart ve "bir model aç" iletişim kutusu aracılığıyla erişmek için Microsoft 'un [OneDrive Windows](https://onedrive.live.com/about/en-us/download/) istemcisini kullanmasını teşvik edilir.

![OneDrive özelliği kaldırıldı](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Kuruluşum aracın 2016 sürümünü kullanıyor, Azure kalıbı kümesini kullanabilir miyim?

Evet, şunları yapabilirsiniz! [Azure kalıbı kümesi GitHub 'da kullanılabilir](https://github.com/Microsoft/threat-modeling-templates/)ve aracın 2016 sürümüne yüklenebilir. Azure kalıbı kümesiyle yeni bir model oluşturmak için Ana Menü ekranındaki "yeni modeller Için şablon" iletişim kutusunu kullanın. TMT 2016, Azure kalıbı kümesinin "olası azaltmalar" alanlarında bulunan bağlantıları işleyebilir, bu nedenle HTML etiketleri olarak görüntülenmiş bağlantıları görebilirsiniz.

![2016 Istemcisinde Azure kalıbı güncelleştirmeleri](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Sistem gereksinimleri

- Desteklenen İşletim Sistemleri
  - Microsoft Windows 10
- .NET sürümü gerekli
  - .NET 3.5.2
- Ek Gereksinimler
  - Araç ve şablonların güncelleştirmelerini almak için bir Internet bağlantısı gerekir.

## <a name="documentation-and-feedback"></a>Belgeler ve geri bildirim

- Threat Modeling Tool belgeleri [docs.Microsoft.com](threat-modeling-tool.md)konumunda bulunur ve [Aracı kullanma hakkında](threat-modeling-tool-getting-started.md)bilgiler içerir.

## <a name="next-steps"></a>Sonraki adımlar

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)en son sürümünü indirin.
