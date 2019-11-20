---
title: Azure altyapı bütünlüğü
description: Bu makalede, Azure altyapısının bütünlüğü ele alınmaktadır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: ef81e74b07a351139aa8feefbdf1b89ea7e4994f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727181"
---
# <a name="azure-infrastructure-integrity"></a>Azure altyapı bütünlüğü

## <a name="software-installation"></a>Yazılım yükleme
Azure ortamında yüklü olan yazılım yığınındaki tüm bileşenler, Microsoft güvenlik geliştirme yaşam döngüsü (SDL) işleminden sonra özel olarak oluşturulmuştur. İşletim sistemi (OS) görüntüleri ve SQL veritabanı da dahil olmak üzere tüm yazılım bileşenleri değişiklik yönetimi ve sürüm yönetimi sürecinin bir parçası olarak dağıtılır. Tüm düğümlerde çalışan işletim sistemi, Windows Server 2008 veya Windows Server 2012 ' nin özelleştirilmiş bir sürümüdür. Tam sürüm, yapı denetleyicisi (FC) tarafından, işletim sisteminin oynamasını amaçladığı role göre seçilir. Ayrıca, ana bilgisayar işletim sistemi yetkisiz yazılım bileşenlerinin yüklenmesine izin vermez.

Bazı Azure bileşenleri, Konuk işletim sistemi üzerinde çalışan bir konuk VM 'de Azure müşterileri olarak dağıtılır.

## <a name="virus-scans-on-builds"></a>Derlemelerde virüs taramaları
Azure yazılım bileşeni (işletim sistemi dahil) derlemeleri, Endpoint Protection Anti-Virus aracını kullanan bir virüs taramasını az bir şekilde gitmenize sahip olmalıdır. Her bir virüs taraması, nelerin tarandığı ve taramanın sonuçlarının ayrıntılarını alan ilişkili derleme dizininde bir günlük oluşturur. Virüs taraması, Azure 'daki her bileşene yönelik derleme kaynak kodunun bir parçasıdır. Kod, temiz ve başarılı bir virüs taraması olmadan üretime taşınmadı. Herhangi bir sorun belirtilmişse, derleme dondurulur ve ardından Microsoft Güvenlik içindeki güvenlik ekiplerine geçerek "standart dışı" kodun derlemeyi girdiği yeri belirler.

## <a name="closed-and-locked-environment"></a>Kapalı ve kilitli ortam
Varsayılan olarak, Azure altyapı düğümleri ve konuk VM 'Ler üzerinde Kullanıcı hesapları oluşturulmaz. Ayrıca, varsayılan Windows yönetici hesapları da devre dışı bırakılır. Azure Live support 'tan Yöneticiler, doğru kimlik doğrulama sayesinde bu makinelerde oturum açıp Acil onarım işlemleri için Azure üretim ağını yönetebilir.

## <a name="azure-sql-database-authentication"></a>Azure SQL veritabanı kimlik doğrulaması
Tüm SQL Server uygulamalarında olduğu gibi, Kullanıcı hesabı yönetimi sıkı bir şekilde denetlenmelidir. Azure SQL veritabanı yalnızca SQL Server kimlik doğrulamasını destekler. Bir müşterinin veri güvenliği modelini tamamlamak için güçlü parolalara sahip olan ve belirli haklara sahip kullanıcı hesaplarının de kullanılması gerekir.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Microsoft kurumsal ağı ile bir Azure kümesi arasındaki ACL 'Ler ve güvenlik duvarları
Hizmet platformu ve Microsoft kurumsal ağı arasındaki erişim denetimi listeleri (ACL 'Ler) ve güvenlik duvarları, SQL veritabanı örneklerini yetkisiz Insider erişimine karşı korur. Ayrıca, yalnızca Microsoft Kurumsal ağından gelen IP adresi aralıklarından gelen kullanıcılar Windows Fabric platform yönetimi uç noktasına erişebilir.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Bir SQL veritabanı kümesindeki düğümler arasındaki ACL 'Ler ve güvenlik duvarları
Ek bir koruma olarak, derinlemesine savunma stratejisi, ACL 'Ler ve bir güvenlik duvarının bir parçası olarak bir SQL veritabanı kümesindeki düğümler arasında uygulanmıştır. Windows Fabric platform kümesi içindeki tüm iletişimler ve çalışan tüm kodlar güvenilir.

## <a name="custom-monitoring-agents"></a>Özel izleme aracıları
SQL veritabanı, SQL veritabanı kümesinin sistem durumunu izlemek için, watchdogs olarak da bilinen özel izleme aracılarını (MAs) kullanır.

## <a name="web-protocols"></a>Web protokolleri

### <a name="role-instance-monitoring-and-restart"></a>Rol örneği izleme ve yeniden başlatma
Azure, tüm dağıtılan, çalışan rollerin (internet 'e yönelik Web veya arka uç işleme çalışan rollerinin), sağlandıkları hizmetleri verimli ve verimli bir şekilde teslim etmesini sağlamak üzere sürekli sistem durumu izlemesine tabi olmasını sağlar. Bir rol, barındırılan uygulamada kritik bir hata veya rol örneğinin içindeki temel bir yapılandırma sorunu tarafından sağlıksız hale gelirse, FC rol örneğindeki sorunu algılar ve bir düzeltici durum başlatır.

### <a name="compute-connectivity"></a>İşlem bağlantısı
Azure, dağıtılan uygulamanın veya hizmetin standart Web tabanlı protokoller aracılığıyla erişilebilir olmasını sağlar. İnternet 'e yönelik Web rollerinin sanal örneklerine dış internet bağlantısı vardır ve doğrudan Web kullanıcıları tarafından erişilebilir. Çalışan rollerinin genel olarak erişilebilen web rolü sanal örnekleri adına gerçekleştirdiği işlemlerin duyarlılığını ve bütünlüğünü korumak için, arka uç işleme çalışan rollerinin sanal örneklerinin dış internet bağlantısı vardır ancak doğrudan dış Web kullanıcıları tarafından erişilir.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yönelik daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure üretim ağı](production-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)
