---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391819"
---
Azure portal veya SMB kullanarak Azure dosya paylaşımında yapılan değişiklikler anında algılanır ve sunucu uç noktasındaki değişiklikler gibi çoğaltılmaz. Azure dosyalarında değişiklik bildirimleri veya günlük kaydı yoktur, bu nedenle dosyalar değiştirildiğinde bir eşitleme oturumu otomatik olarak başlatmak için bir yol yoktur. Windows Server 'da, Azure Dosya Eşitleme dosyalar değiştiğinde eşitleme oturumunu otomatik olarak başlatmak için [WINDOWS USN günlük kaydı](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) kullanır.

Azure dosya paylaşımında yapılan değişiklikleri algılamak için Azure Dosya Eşitleme, *değişiklik algılama işi*adlı Zamanlanmış bir iş vardır. Değişiklik algılama işi dosya paylaşımındaki her dosyayı numaralandırır ve ardından bu dosyanın eşitleme sürümüyle karşılaştırır. Değişiklik algılama işi dosyaların değiştiğini belirlediğinde Azure Dosya Eşitleme bir eşitleme oturumu başlatır. Değişiklik algılama işi her 24 saatte bir başlatılır. Değişiklik algılama işi Azure dosya paylaşımındaki her dosyayı numaralandırarak çalıştığından, değişiklik algılama daha büyük ad uzaylarında daha küçük ad uzaylarından daha uzun sürer. Büyük ad alanları için, hangi dosyaların değiştirildiğini belirleyebilmek her 24 saatte bir daha uzun sürebilir.

Azure dosya paylaşımında değiştirilen dosyaları hemen eşitlemek için **Invoke-AzStorageSyncChangeDetection** PowerShell cmdlet 'i Azure dosya paylaşımındaki değişikliklerin algılanmasını el ile başlatmak için kullanılabilir. Bu cmdlet, bazı otomatik işlem türlerinin Azure dosya paylaşımında değişiklik yapmakta olduğu veya değişikliklerin bir yönetici tarafından yapıldığı (dosya ve dizinleri paylaşıma taşıma gibi) olduğu senaryolar için tasarlanmıştır. Son Kullanıcı değişiklikleri için, Azure Dosya Eşitleme aracısını IaaS sanal makinesine yüklemek ve son kullanıcıların IaaS VM 'si aracılığıyla dosya paylaşımında erişimi olması önerilir. Bu şekilde, tüm değişiklikler Invoke-AzStorageSyncChangeDetection cmdlet 'ini kullanmaya gerek kalmadan diğer aracılara hızla eşitlenir. Daha fazla bilgi için bkz. [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) belgeleri.

>[!NOTE]
>REST kullanılarak Azure dosya paylaşımında yapılan değişiklikler, SMB son değiştirilme süresini güncelleştirmez ve eşitleme tarafından değişiklik olarak görünmez.

Windows Server 'daki birimler için USN 'ye benzer bir Azure dosya paylaşımında değişiklik algılama ekleme işlemini araştırıyoruz. Bu özelliğin [Azure dosyaları UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)'ta Oylama yaparak gelecekte geliştirme için önceliklendirmesine yardımcı olun.
