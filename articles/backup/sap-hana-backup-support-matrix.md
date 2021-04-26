---
title: SAP HANA Backup destek matrisi
description: Bu makalede, Azure VM 'lerinde SAP HANA veritabanlarını yedeklemek için Azure Backup kullandığınızda desteklenen senaryolar ve sınırlamalar hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: cbf910a0291e90965c9698a8b2a43c587cbfe0b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101707243"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM’leri üzerinde SAP HANA veritabanlarının yedeklerine yönelik destek matrisi

Azure Backup, SAP HANA veritabanlarının Azure 'a yedeklenmesini destekler. Bu makalede, Azure VM 'lerinde SAP HANA veritabanlarını yedeklemek için Azure Backup kullandığınızda desteklenen senaryolar ve sınırlamalar özetlenmektedir.

> [!NOTE]
> Günlük yedekleme sıklığı artık en az 15 dakika olarak ayarlanabilir. Günlük yedeklemeleri, veritabanı için başarılı bir tam yedekleme işlemi tamamlandıktan sonra yalnızca Flow 'a başlar.

## <a name="scenario-support"></a>Senaryo desteği

| **Senaryo**               | **Desteklenen konfigürasyonlar**                                | **Desteklenmeyen yapılandırmalar**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topoloji**               | Yalnızca Azure Linux VM 'lerinde çalışan SAP HANA                    | HANA büyük örnekler (HLI)                                   |
| **Bölgeler**                   | **GA**<br> **Kuzey** – Orta ABD, Doğu ABD 2, Doğu ABD, Orta Kuzey ABD, Orta Güney ABD, Batı ABD 2, Orta Batı ABD, Batı ABD, Kanada Orta, Kanada Doğu, Brezilya Güney <br> **Asya Pasifik** – Avustralya Orta, Avustralya Orta 2, Avustralya Doğu, Avustralya Güneydoğu, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Doğu Asya, Güneydoğu Asya, Orta Hindistan, Güney Hindistan, Batı Hindistan, Çin Doğu, Çin Kuzey, Çin doğu2, Çin Kuzey 2 <br> **Avrupa** – Batı Avrupa, Kuzey Avrupa, Fransa Orta, UK Güney, UK Batı, Almanya Kuzey, Almanya Orta Batı, İsviçre Kuzey, İsviçre Batı, Orta İsviçre Kuzey, Norveç Doğu, Norveç Batı <br> **Afrika/ben** -Güney Afrika Kuzey, Güney Afrika Batı, BAE Kuzey, BAE Orta  <BR>  **Azure Kamu bölgeleri** | Fransa Güney, Almanya Orta, Almanya Kuzeydoğu, US Gov IOWA |
| **İşletim sistemi sürümleri**            | SLES 12 with SP2, SP3, SP4 ve SP5; SLES 15 with SP0, SP1, SP2 <br><br>  RHEL 7,4, 7,6, 7,7, 8,1 & 8,2                |                                             |
| **HANA sürümleri**          | Hana 1. x üzerinde SDC, HANA 2. x SPS04, SPS05 Rev <= 53 (şifreleme etkin senaryolar için de onaylanır)      |                                                            |
| **HANA dağıtımları**       | Tek bir Azure VM üzerinde SAP HANA-yalnızca ölçeği artırma. <br><br> Yüksek kullanılabilirlik dağıtımlarında iki farklı makinede bulunan düğümler ayrı veri zincirleriyle tek tek düğümler olarak değerlendirilir.               | Ölçeği genişletme <br><br> Yüksek kullanılabilirlik dağıtımlarında yedekleme, ikincil düğüme otomatik olarak yük devretmez. Yedeklemenin yapılandırılması her düğüm için ayrı olarak yapılmalıdır.                                           |
| **HANA örnekleri**         | Tek bir Azure VM üzerinde tek bir SAP HANA örneği – yalnızca ölçeği büyütme | Tek bir VM 'de birden çok SAP HANA örneği. Tek seferde bu birden çok örneği koruyabilirsiniz.                  |
| **HANA veritabanı türleri**    | 2. x üzerinde 1. x, çok veritabanı kapsayıcısı (MDC) ÜZERINDE Tek Veritabanı kapsayıcı (SDC) | HANA 1. x içinde MDC                                              |
| **HANA veritabanı boyutu**     | <= 8 TB boyutundaki HANA veritabanları (Bu, HANA sisteminin bellek boyutu değildir)               |                                                              |
| **Yedekleme türleri**           | Tam, fark, artımlı ve günlük yedeklemeleri                          |  Anlık Görüntüler                                       |
| **Geri yükleme türleri**          | Desteklenen geri yükleme türleri hakkında bilgi edinmek için SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) notuna bakın |                                                              |
| **Yedekleme limitleri**          | SAP HANA örneği başına 8 TB 'a kadar tam yedekleme boyutu (geçici sınır)         |                                                              |
| **Özel yapılandırma** |                                                              | SAP HANA + dinamik katmanlama <br>  , Geçişli aracılığıyla kopyalama        |

------

>[!NOTE]
>Azure Backup, bir Azure VM 'de çalışan bir SAP HANA Veritabanının yedeklenmesinde gün ışığından yararlanma saati değişikliklerini otomatik olarak ayarlamaz.
>
>İlkeyi gerektiği şekilde el ile değiştirin.

> [!NOTE]
> Artık Azure portal HANA yerel istemcilerinden (SAP HANA Studio/kokpit/DBA kokpiti) tetiklenen [yedekleme ve geri yükleme](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) işlerini (aynı makineye) izleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure VM 'lerinde çalışan SAP HANA veritabanlarını nasıl yedekleyeceğinizi](./backup-azure-sap-hana-database.md) öğrenin
* [Azure VM 'lerinde çalışan SAP HANA veritabanlarını geri yüklemeyi](./sap-hana-db-restore.md) öğrenin
* [Kullanarak yedeklenen SAP HANA veritabanlarını yönetmeyi öğrenin Azure Backup](sap-hana-db-manage.md)
* [SAP HANA veritabanlarını yedeklerken sık karşılaşılan sorunları gidermeye nasıl sorun](./backup-azure-sap-hana-database-troubleshoot.md) alabileceğinizi öğrenin
