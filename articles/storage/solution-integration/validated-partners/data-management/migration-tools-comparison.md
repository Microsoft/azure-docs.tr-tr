---
title: Azure depolama geçiş araçları karşılaştırması-yapılandırılmamış veriler
description: Yapılandırılmamış verilerin geçirilmesi için kullanılan araçlar arasındaki temel işlevler ve karşılaştırma
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 862feace6aab4f49ad3482c4ccd6510669c876a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576542"
---
# <a name="comparison-matrix"></a>Karşılaştırma matrisi

Aşağıdaki karşılaştırma matrisi, yapılandırılmamış verilerin geçirilmesi için kullanılabilecek farklı araçların temel işlevlerini gösterir. 

## <a name="supported-azure-services"></a>Desteklenen Azure hizmetleri

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Veri dinamikleri](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Çözüm adı**  | [Azure Dosya Eşitleme](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Veri taşınabilirliği ve geçiş](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Akıllı Veri Yönetimi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure dosyaları desteği (tüm katmanlar)** | Yes                          | Yes                      | Yes            | Yes                            |
| **Azure NetApp Files desteği**      | Hayır                           | Yes                      | Yes            | Yes                            |
| **Azure Blob sık/seyrek destek**   | No                           | Evet (NFS önizlemesi aracılığıyla)    | Yes            | Yes                            |
| **Azure Blob arşiv katmanı desteği** | Hayır                           | Hayır                       | Hayır             | Evet (geçiş hedefi olarak) |
| **Azure Data Lake Storage desteği** | Hayır                           | Hayır                       | Hayır             | Hayır                             |
| **Desteklenen Kaynaklar**      | Windows Server 2012 R2 ve yukarı | Bulut dosya sistemleri & NAS | Herhangi bir NAS ve S3 | NAS, blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Desteklenen protokoller (kaynak/hedef)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Veri dinamikleri](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Çözüm adı**   | [Azure Dosya Eşitleme](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Veri taşınabilirliği ve geçiş](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Akıllı Veri Yönetimi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Yes | Yes | Yes | Yes |
| **SMB 3.0**       | Yes | Yes | Yes | Yes |
| **SMB 3,1**       | Yes | Yes | Yes | Yes |
| **NFS v3**        | Hayır  | Yes | Yes | Yes |
| **NFS v 4.1**      | Hayır  | Yes | Hayır  | Yes |
| **Blob REST API** | Hayır  | Hayır  | Yes | Yes |
| **S3**            | Hayır  | Yes | Yes | Yes |

## <a name="extended-features"></a>Genişletilmiş özellikler

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Veri dinamikleri](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Çözüm adı**  | [Azure Dosya Eşitleme](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Veri taşınabilirliği ve geçiş](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Akıllı Veri Yönetimi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID/SID yeniden eşleme**                   | Hayır  | Yes                        | Yes | Hayır                             |
| **Protokol ACL yeniden eşlemesi**                | Hayır  | Hayır                         | Hayır  | Hayır                             |
| **DFS desteği**                           | Yes | Yes                        | Yes | Yes                            |
| **Daraltma desteği**                    | Yes | Yes                        | Yes | Yes                            |
| **Dosya deseninin dışlamaları**               | Hayır  | Yes                        | Yes | Evet (kopyalama işlevini kullanma) |
| **Seçmeli dosya öznitelikleri için destek** | Yes | Yes                        | Yes | Evet (genişletilmiş öznitelikler için)  |
| **Yayılmaları Sil**                   | Yes | Yes                        | Yes | Yes                            |
| **NTFS merkezleriyle 'ı izleyin**                 | Hayır  | Yes                        | Hayır  | Yes                            |
| **SMB sahibini ve Grup sahibini geçersiz kıl**    | Yes | Yes                        | Yes | Hayır                             |
| **Gözetim raporlama Zinciri**            | Hayır  | Yes                        | Hayır  | Yes                            |
| **Alternatif veri akışları için destek**    | Hayır  | Yes                        | Yes | Hayır                             |
| **Geçiş için zamanlama**              | Hayır  | Yes                        | Yes | Yes                            |
| **ACL 'yi koruma**                        | Hayır  | Yes                        | Yes | Yes                            |
| **DACL desteği**                          | Yes | Yes                        | Yes | Yes                            |
| **SACL desteği**                          | Yes | Yes                        | Yes | Hayır                             |
| **Erişim süresini koruma**                | Yes | Yes                        | Yes | Yes                            |
| **Değiştirilme süresini koruma**              | Yes | Yes                        | Yes | Yes                            |
| **Oluşturma süresini koruma**              | Hayır  | Yes                        | Yes | Yes                            |
| **Azure Data Box desteği**       | Yes | Yes                        | Hayır  | Hayır                             |
| **Anlık görüntülerin geçirilmesi**                | No  | El ile                     | Yes | Hayır                             |
| **Sembolik bağlantı desteği**                 | Hayır  | Yes                        | Hayır  | Yes                            |
| **Sabit bağlantı desteği**                     | No  | Ayrı dosyalar olarak geçirildi | Yes | Yes                            |
| **Açık/kilitli dosyalar için destek**       | Yes | Yes                        | Yes | Yes                            |
| **Artımlı geçiş**                 | Yes | Yes                        | Yes | Yes                            |
| **Geçiş desteği**                    | Hayır  | Yes                        | Yes | Hayır (yalnızca el ile)               |
| **[Diğer özellikler](#other-features)**         | [Bağlantısının](#azure-file-sync)| [Bağlantısının](#dobimigrate) | [Bağlantısının](#data-mobility-and-migration) | [Bağlantısının](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Değerlendirme ve raporlama

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Veri dinamikleri](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Çözüm adı**   | [Azure Dosya Eşitleme](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Veri taşınabilirliği ve geçiş](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Akıllı Veri Yönetimi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Kapasite**                        | Hayır      | Yes | Yes | Yes            |
| **dosya/klasör sayısı**            | Hayır      | Yes | Yes | Yes            |
| **Zaman içinde yaş dağılımı**      | Hayır      | Yes | Yes | Yes            |
| **Erişim zamanı**                     | Hayır      | Yes | Yes | Yes            |
| **Değiştirilme zamanı**                   | Hayır      | Yes | Yes | Yes            |
| **Oluşturulma Zamanı**                   | Hayır      | Yes | Yes | Yes            |
| **Dosya/nesne rapor durumu başına** | Kısmi | Yes | Yes | Yes            |

## <a name="licensing"></a>Lisanslama

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Veri dinamikleri](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Çözüm adı**   | [Azure Dosya Eşitleme](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Veri taşınabilirliği ve geçiş](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Akıllı Veri Yönetimi](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **KLG**             | yok | Yes | Yes | Yes |
| **Azure taahhüt** | Yes   | Yes | Yes | Yes |

## <a name="other-features"></a>Diğer özellikler

### <a name="azure-file-sync"></a>Azure Dosya Eşitleme

- İç karma doğrulaması

### <a name="dobimigrate"></a>DobiMigrate

- Geçiş öncesi denetimler
- Geçiş planlaması
- Kesme testi için kuru çalıştırma
- Kullanımdan kesilmesinden önce hedef tarafı Kullanıcı etkinliğini Algıla ve uyar
- İlke temelli geçişler
- Zamanlanmış kopyalama yinelemeleri
- Kök dizin güvenliğini işlemeye yönelik yapılandırılabilir seçenekler
- İsteğe bağlı doğrulama çalıştırmaları
- Kaynak ve hedefte veri geri okuma doğrulaması
- Grafik, etkileşimli hata işleme iş akışı
- Bazı işlemleri, silme ve güncelleştirme gibi yaymadan kısıtlama yeteneği
- Kaynakta erişim süresini koruma yeteneği (hedefe ek olarak)
- Geçiş geçişi sırasında kaynağa geri alma yürütme özelliği
- Seçili SMB dosyası özniteliklerini geçirebilme
- NTFS güvenlik tanımlayıcılarını temizleme özelliği
- NFSv3 izinlerini geçersiz kılma ve hedefe yeni mod bitleri yazma olanağı
- NFSv3 POSIX taslak ACL 'lerini NFSv4 ACL 'lerine dönüştürebilme
- SMB 1 (CıFS)

### <a name="data-mobility-and-migration"></a>Veri taşınabilirliği ve geçiş

- Karma doğrulama

### <a name="intelligent-data-management"></a>Akıllı Veri Yönetimi

- Proje/dizin tabanlı geçişler
- Hataların otomatik yeniden denenmesi
- Değerlendirme/raporlama: dosya türleri, dosya boyutu, proje tabanlı
- Değerlendirme/raporlama: özel meta veri tabanlı aramalar
- Arşivleme, çoğaltma, analiz için tam veri yaşam döngüsü yönetimi çözümü
- Blob, S3 verileri üzerinde zaman tabanlı Analize erişim
- Etiketleme
- Destek 24 x 7 x 365
- Karma doğrulama

*Listenin son tarihi Mart, 31 2021 ' de doğrulandı.*

## <a name="see-also"></a>Ayrıca bkz.

- [Depolama geçişine genel bakış](../../../common/storage-migration-overview.md)
- [Veri aktarımı için bir Azure çözümü seçme](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Azure dosya paylaşımlarına geçirme](/azure/storage/files/storage-files-migration-overview)
- [Azure için WANdisco LiveData platform ile Data Lake Storage geçirin](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [AzCopy ile verileri Azure depolama 'ya kopyalama veya taşıma](https://aka.ms/azcopy)
- [AzReplicate ile büyük veri kümelerini Azure Blob depolamaya geçirme (örnek uygulama)](https://github.com/Azure/AzReplicate/tree/master/)
