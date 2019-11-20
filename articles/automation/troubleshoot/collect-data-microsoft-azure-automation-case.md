---
title: Microsoft Azure Otomasyonu için bir durum açtığınızda toplanacak veriler | Microsoft Docs
description: Bu makalede, Azure Otomasyonu için Microsoft Azure desteğiyle bir servis talebi açmadan önce toplamanız gereken bazı temel bilgiler açıklanmaktadır.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301756"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Microsoft Azure Otomasyonu için bir servis talebi açtığınızda toplanan veriler

Bu makalede, Azure Otomasyonu için Microsoft Azure desteğiyle bir servis talebi açmadan önce toplamanız gereken bazı temel bilgiler açıklanmaktadır. Bu bilgiler, durumu açmak için gerekli değildir. Ancak, Microsoft 'un sorununuzu daha hızlı çözümlemesine yardımcı olabilir. Ayrıca, büyük/küçük harf 'i açtıktan sonra bu veriler destek mühendisi tarafından istenebilir.

## <a name="collect-more-information"></a>Daha fazla bilgi toplayın

Microsoft Azure Otomasyonu desteği için bir servis talebi açmadan önce, aşağıdaki bilgileri toplamanızı öneririz.

### <a name="basic-data-collection"></a>Temel veri toplama

Aşağıdaki Bilgi Bankası makalesinde açıklanan verileri toplayın:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Azure Otomasyonu ile komut dosyalı tanılamayı yakalama

## <a name="collect-data-depending-on-issue"></a>Soruna bağlı olarak veri topla
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Linux üzerinde Güncelleştirme Yönetimi etkileyen sorunlar için

1. KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)' de listelenen öğelere ek olarak, aşağıdaki günlük toplama aracını çalıştırın:

   [OMS Linux Aracısı günlük toplayıcısı](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Aşağıdaki klasörün içeriğini sıkıştırın ve ardından sıkıştırılmış dosyayı Azure desteği 'ne gönderin:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. OMS Linux aracısının bildirdiği çalışma alanı KIMLIĞI ' nin, güncelleştirmeler için izlenmekte olan çalışma alanıyla aynı olduğunu doğrulayın.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Windows üzerinde Güncelleştirme Yönetimi etkileyen sorunlar için

[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)' de listelenen öğelere ek olarak şunları yapın:

1. Aşağıdaki olay günlüklerini EVTX biçiminde dışarı aktarın:

   * Sistem
   * Uygulama
   * Güvenlik
   * Operations Manager
   * Microsoft-SMA/operasyonel

2. Aracının bildirdiği çalışma alanı KIMLIĞININ, Windows güncelleştirmeleri tarafından izlenmekte olan çalışma alanıyla aynı olduğunu doğrulayın.

### <a name="for-issues-that-affect-a-job"></a>Bir işi etkileyen sorunlar için

[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)' de listelenen öğelere ek olarak, aşağıdaki bilgileri toplayın:

1. **JobId** numarasını toplayın (bir sorun yaşayan iş için):

   1. Azure portal **Otomasyon hesapları** dikey penceresine gidin.
   2. Sorun gidermekte olduğunuz **Otomasyon hesabını** seçin.
   3. **İşleri**seçin.
   4. Sorun gidermekte olduğunuz işi seçin.
   5. **Iş Özeti**altında BIR **iş kimliği** (GUID) arayın.

   ![Iş Özeti bölmesi içindeki iş KIMLIĞI](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Hesap adını toplayın:

   1. Azure portal **Otomasyon hesapları** dikey penceresine gidin.
   2. Sorun gidermeye çalıştığınız **Otomasyon hesabının** adını alın.

3. Çalıştırdığınız betiğin örneğini toplayın.

4. Günlük dosyalarını toplayın:

   1. Azure portal **Otomasyon hesapları** dikey penceresine gidin.
   2. Sorun gidermekte olduğunuz **Otomasyon hesabını** seçin.
   3. **İşleri**seçin.
   4. Sorun gidermekte olduğunuz işi seçin.
   5. **Tüm Günlükler**' i seçin.
   6. Elde edilen dikey pencerede, verileri toplayın.

   ![Tüm günlüklerde listelenen veriler](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Modülleri etkileyen sorunlar için

"Temel veri toplama" altındaki öğelere ek olarak, aşağıdaki bilgileri toplayın:

* Sorunun yeniden üretibilmesini sağlamak için izlediğiniz adımlar.
* Herhangi bir hata iletisinin ekran görüntüsü.
* Geçerli modüllerin ve bunların sürüm numaralarının ekran görüntüsü.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun.

Alternatif olarak, bir Azure destek olayı dosyası. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.