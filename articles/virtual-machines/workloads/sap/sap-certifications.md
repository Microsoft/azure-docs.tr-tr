---
title: SAP için Microsoft Azure sertifikaları | Microsoft Docs
description: Azure platformunda geçerli yapılandırmaların ve SAP sertifikalarının güncelleştirilmiş listesi.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 76f67204b41a36d10541974e78a22f7d065e144b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669690"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure üzerinde çalışan SAP Sertifikaları ve yapılandırması

SAP ve Microsoft, müşterileri için karşılıklı avantajlara sahip güçlü bir iş ortaklığında birlikte çalışan uzun bir geçmişe sahiptir. Microsoft, SAP iş yüklerinizin çalıştırılacağı en iyi platform olduğundan emin olmak için, platformunu sürekli olarak güncelleştiriyor ve yeni sertifika ayrıntılarını SAP 'ye gönderen Microsoft Azure. Aşağıdaki tablolarda Azure desteklenen konfigürasyonlar ve büyüyen SAP sertifikalarının listesi ana hatlarıyla verilmiştir. Bu liste, burada ve resmi SAP listelerinden farklı olabilecek bir genel bakış listesidir. Ayrıntılı verilere nasıl ulaşırsanız, [Azure dağıtımları Için HANGI SAP yazılımlarının desteklendiği](./sap-supported-product-on-azure.md) makalesinde açıklanmaktadır.

## <a name="sap-hana-certifications"></a>SAP HANA sertifikaları
Başvur

- Yerel Azure VM 'Leri ve HANA büyük örnekleri için SAP HANA destek için [sertifikalı IaaS platformları SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) .

| SAP ürünü | Desteklenen İşletim Sistemi | Azure teklifleri |
| --- | --- | --- |
| SAP HANA geliştirici sürümü (SQLODBC, yalnızca ODBO-Windows, ODBC, JDBC sürücüleri, HANA Studio ve HANA veritabanı) oluşan HANA istemci yazılımı dahil olmak üzere | Red Hat Enterprise Linux, SUSE Linux Enterprise | D serisi VM ailesi |
| HANA 'da birinci departman | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Sertifikalı IaaS platformlarını SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 için denetlenen kullanılabilirlik. M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2 için tam destek <br /> Azure 'da SAP HANA (büyük örnekler) [sertifikalı IaaS platformları SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA, OLTP üzerinde Suite | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, Azure üzerinde SAP HANA (büyük örnekler) [sertifikalı IaaS platformları SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Siyah-beyaz, OLAP için HANA Enterprise | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, Azure üzerinde SAP HANA (büyük örnekler) [sertifikalı IaaS platformları SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> Azure 'da M416s_v2, M416ms_v2, SAP HANA (büyük örnekler) <br /> [Sertifikalı IaaS platformlarını SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

SAP 'ın, ' genişleme ' için [SAP HANA sertifikalı IaaS platformlarında](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) ' Kümeleme ' terimini ' genişleme ' için eş anlamlı olarak kullandığını ve yüksek kullanılabilirlik ' Kümelemesi ' için değil

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver sertifikaları
Microsoft Azure, Microsoft ve SAP 'nin tam desteğiyle aşağıdaki SAP ürünleri için sertifikalandırilmiştir.
Başvur

- [1928533-Azure 'DA SAP uygulamaları:](https://launchpad.support.sap.com/#/notes/1928533) SAP trex, SAP livecache ve SAP içerik sunucusu DAHIL tüm SAP NetWeaver tabanlı uygulamalar Için desteklenen ürünler ve Azure VM türleri. Ve SAP HANA hariç tüm veritabanları.


| SAP ürünü | Konuk işletim sistemi | RDBMS | Sanal makine türleri |
| --- | --- | --- | --- |
| SAP Business Suite yazılımı | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux Oracle Linux |SQL Server, Oracle (yalnızca Windows ve Oracle Linux), DB2, SAP ATıCı |A5 A11, D11 to D14, DS11 to DS14 DS15_v2 DS11_v2, GS1 to GS5 D2s_v3, D2as_v4 D64s_v3, D64as_v4 E2s_v3 E64s_v3, E2as_v4 E64as_v4 M208s_v2 M208ms_v2, M416s_v2 M416ms_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,,,, |
| SAP Business hepsi-tek | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux Oracle Linux |SQL Server, Oracle (yalnızca Windows ve Oracle Linux), DB2, SAP ATıCı |A5 A11, D11 to D14, DS11 to DS14 DS15_v2 DS11_v2, GS1 to GS5 D2s_v3, D2as_v4 D64s_v3, D64as_v4 E2s_v3 E64s_v3, E2as_v4 E64as_v4 M208s_v2 M208ms_v2, M416s_v2 M416ms_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,,,, |
| SAP BusinessObjects bı | Windows |Yok |A5 A11, D11 to D14, DS11 to DS14 DS15_v2 DS11_v2, GS1 to GS5 D2s_v3, D2as_v4 D64s_v3, D64as_v4 E2s_v3 E64s_v3, E2as_v4 E64as_v4 M208s_v2 M208ms_v2, M416s_v2 M416ms_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,,,, |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux Oracle Linux |SQL Server, Oracle (yalnızca Windows ve Oracle Linux), DB2, SAP ATıCı |A5 A11, D11 to D14, DS11 to DS14 DS15_v2 DS11_v2, GS1 to GS5 D2s_v3, D2as_v4 D64s_v3, D64as_v4 E2s_v3 E64s_v3, E2as_v4 E64as_v4 M208s_v2 M208ms_v2, M416s_v2 M416ms_v2, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,,,, |

## <a name="other-sap-workload-supported-on-azure"></a>Azure 'da desteklenen diğer SAP Iş yükü

| SAP ürünü | Konuk işletim sistemi | RDBMS | Sanal makine türleri |
| --- | --- | --- | --- |
| SAP Business One SQL Server | Windows  | SQL Server | Tüm NetWeaver sertifikalı VM türleri<br /> [SAP Note #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10,01 MS SP08 | Windows ve Linux | | Tüm NetWeaver sertifikalı VM türleri<br /> SAP Note #2451795 |
| SAP Business Objects bı platformu | Windows ve Linux | | SAP Note #2145537 |
| SAP Data Services 4,2 | | | SAP Note #2288344 |
| SAP Hybrsıs ticaret platformu  | Windows | SQL Server, Oracle | Tüm NetWeaver sertifikalı VM türleri <br /> [Hybrsıs belgeleri](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybrsıs ticaret platformu  | SLES 12 veya daha yeni | SAP HANA | Tüm NetWeaver sertifikalı VM türleri <br /> [Hybrsıs belgeleri](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybrsıs ticaret platformu  | RHEL 7 veya daha yeni | SAP HANA | Tüm NetWeaver sertifikalı VM türleri <br /> [Hybrsıs belgeleri]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybrsıs) ticaret platformu 1811 ve üzeri  | Windows, SLES veya RHEL | SQL Azure DB | Tüm NetWeaver sertifikalı VM türleri <br /> [Hybrsıs belgeleri](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
