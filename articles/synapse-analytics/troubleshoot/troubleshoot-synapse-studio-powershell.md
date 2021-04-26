---
title: Synapse Studio bağlantı sorunlarını giderme
description: PowerShell kullanarak Azure SYNAPSE Studio bağlantısı sorunlarını giderme
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 2bbdaef9268239005cdf5ea7fbee6734dadc8113
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566264"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>PowerShell ile SYNAPSE Studio bağlantısı sorunlarını giderme

Azure SYNAPSE Studio, düzgün şekilde çalışması için bir dizi Web API uç noktasına bağımlıdır. Bu kılavuz, şu durumlarda bağlantı sorunlarının nedenlerini belirlemenize yardımcı olur:
- Azure SYNAPSE Studio 'Ya erişmek için yerel ağınızı (bir kurumsal güvenlik duvarının arkasındaki ağ gibi) yapılandırma.
- Azure SYNAPSE Studio 'Yu kullanarak bağlantı sorunları yaşıyor.

## <a name="prerequisite"></a>Önkoşul

* Windows üzerinde PowerShell 5,0 veya üzeri sürüm veya
* Windows veya Linux 'ta PowerShell Core 6,0 veya üzeri sürümü.

## <a name="troubleshooting-steps"></a>Sorun giderme adımları

Aşağıdaki bağlantıya sağ tıklayın ve "hedefi farklı kaydet" i seçin:

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternatif olarak, bağlantıyı doğrudan açabilir ve açılan betik dosyasını kaydedebilirsiniz. Daha sonra değiştireolabileceğinden, yukarıdaki bağlantının adresini kaydetmeyin.

Dosya Gezgini 'nde indirilen betik dosyasına sağ tıklayın ve "PowerShell ile Çalıştır" seçeneğini belirleyin.

![İndirilen betik dosyasını PowerShell ile Çalıştır](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

İstendiğinde, şu anda bir sorun yaşayan veya bağlantı için test etmek istediğiniz Azure SYNAPSE çalışma alanı adını girin ve ENTER tuşuna basın.

![Çalışma alanı adını girin](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Tanılama oturumu başlatılır. Tamamlanmasını bekleyin.

![Tanılamada işlemin tamamlanmasını bekleyin](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Sonda, bir tanılama özeti gösterilir. BILGISAYARıNıZ bir veya daha fazla uç noktaya bağlanamıyorsa, "Özet" bölümünde bazı öneriler gösterilir.

![Tanılama özetini gözden geçir](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Ayrıca, bu oturum için bir tanılama günlük dosyası, sorun giderme betiği ile aynı klasörde oluşturulur. Konumu "genel ipuçları" bölümünde ( `D:\TestAzureSynapse_2020....log` ) gösterilir. Gerekirse bu dosyayı teknik desteğe gönderebilirsiniz.

Bir ağ yöneticisiyseniz ve Azure SYNAPSE Studio için güvenlik duvarı yapılandırmanızı ayarlarsanız, "Özet" bölümünün üzerinde gösterilen teknik ayrıntılar size yardımcı olabilir.

* "Geçti" ile işaretlenen tüm test öğeleri (istekler), HTTP durum kodundan bağımsız olarak bağlantı testlerini geçti demektir.
 Başarısız istekler için, nedeni, veya gibi sarı renkle gösterilir `NamedResolutionFailure` `ConnectFailure` . Bu nedenler, ağ ortamınızda yanlış yapılandırma olup olmadığını belirlemenize yardımcı olabilir.


## <a name="next-steps"></a>Sonraki adımlar
Yukarıdaki adımlar sorununuzu çözmeye yardımcı değilse, [bir destek bileti oluşturun](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).