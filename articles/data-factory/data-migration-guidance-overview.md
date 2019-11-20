---
title: Data Lake ve veri ambarınızdan verileri Azure 'a geçirmek için Azure Data Factory kullanın
description: Data Lake ve veri ambarınızdan verileri Azure 'a geçirmek için Azure Data Factory kullanın.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 0be9cbc9c5af2e0778654ef70c5350b48f10c35d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675770"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Data Lake veya veri ambarınızdan verileri Azure 'a geçirmek için Azure Data Factory kullanın

Data Lake veya kurumsal veri Ambarınızı (EDW) Microsoft Azure geçirmek istiyorsanız Azure Data Factory kullanmayı düşünün. Azure Data Factory, aşağıdaki senaryolara uygundur:

- Amazon Simple Storage Service (Amazon S3) veya şirket içi Hadoop Dağıtılmış Dosya Sistemi (bir) ile Azure 'a büyük veri iş yükü geçişi
- Oracle sınavlarından, Netezza, Teradata 'dan veya Amazon Redshift 'ten Azure 'a geçiş EDW

Azure Data Factory Data Lake geçişi için petabaytlarca (PB) verilerin yanı sıra veri ambarı geçişi için on terabaytlık (TB) veri taşıyabilir.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Veri geçişi için neden Azure Data Factory kullanılabilir?

- Azure Data Factory, yüksek performanslı, esnekliği ve ölçeklenebilirlik sayesinde verileri sunucusuz bir şekilde taşımak için işlem gücü miktarını kolayca ölçeklendirebilir. Ve yalnızca kullandığınız kadar ödersiniz. Ayrıca aşağıdakilere de göz önünde bırakabilirsiniz: 
  - Azure Data Factory veri hacminde veya dosya sayısında hiçbir sınırlama yoktur.
  - Azure Data Factory, ortamınızda en yüksek miktarda veri hareketi elde etmenizi sağlamak için ağınızı ve depolama bant genişliğinizi tam olarak kullanabilir.
  - Azure Data Factory Kullandıkça Öde yöntemini kullanır, böylece yalnızca gerçekten Azure 'a veri geçişini çalıştırmak için kullandığınız süre için ödeme yaparsınız.  
- Azure Data Factory, hem tek seferlik geçmiş yükleme hem de zamanlanmış artımlı yüklemeler gerçekleştirebilir.
- Azure Data Factory, verileri herkese açık veri Gölü ve ambar uç noktaları arasında taşımak için Azure Integration Runtime (IR) kullanır. Ayrıca, Azure sanal ağ (VNet) içinde veya bir güvenlik duvarının arkasındaki Data Lake ve ambar uç noktaları için verileri taşımak amacıyla kendinden konak IR kullanabilir.
- Azure Data Factory kurumsal düzeyde güvenliğe sahiptir: güvenli hizmetten hizmete tümleştirme için Windows Installer (MSI) veya hizmet kimliğini kullanabilir veya kimlik bilgisi yönetimi için Azure Key Vault kullanabilirsiniz.
- Azure Data Factory, kod ücretsiz yazma deneyimi ve zengin, yerleşik bir izleme panosu sağlar.  

## <a name="online-vs-offline-data-migration"></a>Çevrimiçi ve çevrimdışı veri geçişi

Azure Data Factory, verileri bir ağ üzerinden (Internet, ER veya VPN) aktarmak için standart bir çevrimiçi veri geçiş aracıdır. Çevrimdışı veri geçişi sayesinde kullanıcılar, kuruluşlarından veri aktarımı cihazlarını Azure veri merkezine fiziksel olarak teslim alırlar.  

Çevrimiçi ve çevrimdışı geçiş yaklaşımı arasından seçim yaparken dikkate alınacak üç önemli noktalar vardır:  

- Geçirilecek verilerin boyutu
- Ağ bant genişliği
- Geçiş penceresi

Örneğin, veri geçişinizi iki hafta içinde ( *geçiş pencereniz*) tamamlamaya yönelik Azure Data Factory kullanacağınızı varsayalım. Aşağıdaki tabloda pembe/mavi kesme hattına dikkat edin. Verilen herhangi bir sütun için en düşük pembe hücre, geçiş penceresi iki haftadan en yakın olan veri boyutu/ağ bant genişliği eşleştirmesini gösterir. (Mavi hücredeki herhangi bir boyut/bant genişliği eşleştirmesi iki haftadan uzun bir çevrimiçi geçiş penceresine sahiptir.) 

![çevrimiçi ve çevrimdışı](media/data-migration-guidance-overview/online-offline.png), bu tablo, verilerinizin boyutuna ve kullanılabilir ağ bant genişliğine göre çevrimiçi geçiş (Azure Data Factory) aracılığıyla amaçlanan geçiş pencerenizi karşılayıp karşılamadığını belirlemenize yardımcı olur. Çevrimiçi geçiş penceresi iki haftadan uzun olursa çevrimdışı geçiş kullanmak isteyeceksiniz.

> [!NOTE]
> Çevrimiçi geçiş kullanarak, hem geçmiş veri yükleme hem de artımlı akışların tek bir araç aracılığıyla uçtan uca elde edebilirsiniz.  Bu yaklaşım sayesinde verileriniz, tüm geçiş penceresi sırasında mevcut mağaza ve yeni mağaza arasında eşitlenebilir. Bu, ETL mantığınızı yenilenen verilerle yeni depoda yeniden oluşturabileceğiniz anlamına gelir.


## <a name="next-steps"></a>Sonraki adımlar

- [AWS S3 'ten Azure 'a veri geçirme](data-migration-guidance-s3-azure-storage.md)
- [Şirket içi Hadoop kümesinden Azure 'a veri geçirme](data-migration-guidance-hdfs-azure-storage.md)
- [Şirket içi Netezza sunucusundan Azure 'a veri geçirme](data-migration-guidance-netezza-azure-sqldw.md)
