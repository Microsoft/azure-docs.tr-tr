---
title: Oracle için BareMetal sağlama
description: Oracle için BareMetal altyapınızı sağlama hakkında bilgi edinin.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559220"
---
# <a name="provision-baremetal-for-oracle"></a>Oracle için BareMetal sağlama

Bu makalede, BareMetal altyapı örneklerinizi Oracle iş yükleri için sağlama bölümüne bakacağız. 

BareMetal örneklerinizi sağlamanın ilk adımı, Microsoft CSA 'iyle birlikte çalışacaktır. Bu kişiler, tek örnek, tek bir RAC veya RAC gibi belirli iş yükü ihtiyaçlarınıza ve dağıttığınız mimariye dayalı olarak size yardımcı olur. Bu topolojilerle ilgili daha fazla bilgi için bkz. [Oracle Için BareMetal altyapısının mimarisi](oracle-baremetal-architecture.md).

## <a name="prerequisites"></a>Önkoşullar

> [!div class="checklist"]
> * Etkin bir Azure aboneliği
> * Microsoft Premier destek sözleşmesi
> * Red Hat Enterprise Linux 7,6 lisansları
> * Oracle Destek Sözleşmesi 
> * Oracle için lisanslar ve yazılım yükleme bileşenleri
> * **Şirket Içinden Azure 'a** bağlı ExpressRoute (**isteğe bağlı** olarak, expressroute Global Reach Şirket içinden Oracle Database doğrudan bağlantı için yapılandırın)   
> * Sanal ağ
> * Ağ geçidi oluşturma
> * Sanal ağdaki sanal makineler (VM 'Ler) (sıçrama kutuları)

## <a name="information-to-provide-microsoft-operations"></a>Microsoft Işlemleri sağlama bilgileri

CSA için aşağıdaki bilgileri sağlamanız gerekir:

1. Sanal ağ adres alanı. Bu Aralık/24 alt ağ olmalıdır; Örneğin, 10.11.0.0/24.
2. P2P aralığı. Bu Aralık bir/29 alt ağı olmalıdır; Örneğin, 10.12.0.0/29.
3. Sunucu IP adresi havuzu. Önerilen Aralık/24 ' dir; Örneğin, 10.13.0.0/24.
4. Sunucu IP adresi. Sunucu IP adresi havuzundan bir IP adresi seçin.

    > [!Note] 
    > İlk otuz IP adresleri, Microsoft altyapı yapılandırması için ayrılmıştır. Bu nedenle, bu örnekte, bir dikey pencere için ilk kullanılabilir IP adresiniz 10.13.0.30 olacaktır.

5. Gerekli Azure bölgesi; Örneğin, Batı ABD2.
6. BareMetal altyapı SKU 'SU gerekli; Örneğin, S192 (iki makine).

## <a name="storage-requirements"></a>Depolama gereksinimleri

Sağlama isteği sırasında, gelecekteki büyümeye dayalı olarak beklenen depolama ihtiyacı dahil olmak üzere, CSA temsilcinizle birlikte çalışın. Eklenen depolama alanı 1 TB 'lik artışlarla.

Birimler için, temel katman ve kurumsal yapılandırma ile Oracle 'ın [En Iyi esnek mimarisi (OFA) standardını](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)izliyoruz. Standart "T-gömme boyutu" dışında özel depolama gereksinimleriniz varsa, özel isteğinizi CSA üzerinden yapın.

| Temel yapılandırma (POC/test) | Açıklama | Küçük | Orta | Büyük |
| --- | --- | --- | --- | --- |
| /U01 | Oracle ikilileri | 500 GB | 500 GB | 500 GB |
| /U02 | Yoğun/yönetici okuma | 500 GB | 1 TB | 5 TB |
| /U03 | Yoğun/günlük yazma | 500 GB | 1 TB | 5 TB |
| /U09 | Backup | 5 TB | 10 TB | 15 TB |

| Kuruluş yapılandırması | Açıklama | Küçük | Orta | Büyük | Çok büyük |
| --- | --- | --- | --- | --- | --- |
| /U01 | Oracle ikilileri | 500 GB | 500 GB | 500 GB | 500 GB |
| /U02 | Yönetici | 100 GB | 100 GB | 100 GB | 100 GB |
| /U10 ila/U59 | Yoğun okuma | 500 GB | 5 TB | 10 TB | 20 TB |
| /U60 ila/u89 | Yoğun yazma | 500 GB | 5 TB | 10 TB | 20 TB |
| /U90 ila/U91 | Günlükleri Yinele | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | Arşiv | 10 TB | 10 TB | 20 TB | 20 TB |
| /U98 | Backup | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>Sonraki adım

Oracle için BareMetal altyapısı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure 'da BareMetal Altyapısı nedir?](../../concepts-baremetal-infrastructure-overview.md)
