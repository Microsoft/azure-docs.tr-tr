---
title: Azure CDN Faturalandırmayı anlama | Microsoft Docs
description: Bu SSS Azure CDN faturalandırmasının nasıl çalıştığını açıklar.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: magattus
ms.openlocfilehash: e2827a11f4ec2a5c0467c3699cd9990aaf7ae97a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495479"
---
# <a name="understanding-azure-cdn-billing"></a>Azure CDN faturalamasını anlama

Bu SSS, Azure Content Delivery Network (CDN) tarafından barındırılan içeriklere yönelik faturalandırma yapısını açıklamaktadır.

## <a name="what-is-a-billing-region"></a>Faturalandırma bölgesi nedir?
Faturalandırma bölgesi, Azure CDN nesnelerin tesliminin ne kadar ücretlendirilildiği belirlemek için kullanılan coğrafi bir alandır. Geçerli faturalandırma bölgeleri ve bölgeleri aşağıdaki gibidir:

- Bölge 1: Kuzey Amerika, Avrupa, Orta Doğu ve Afrika

- Bölge 2: Asya Pasifik (Japonya dahil)

- Bölge 3: Güney Amerika

- Bölge 4: Avustralya ve Yeni Zelanda

- Bölge 5: Hindistan

Varlık noktası (POP) bölgeleri hakkında daha fazla bilgi için bkz. [bölgeye göre Azure CDN pop konumları](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Örneğin, Meksika 'da bulunan bir POP Kuzey Amerika bölgedeyse ve bu nedenle bölge 1 ' de yer alır. 

Azure CDN fiyatlandırması hakkında daha fazla bilgi için bkz. [Content Delivery Network fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Teslimat ücretleri bölge tarafından nasıl hesaplanır?
Azure CDN faturalandırma bölgesi, son kullanıcıya içerik teslim eden kaynak sunucunun konumunu temel alır. İstemcinin hedefi (fiziksel konumu) Faturalandırma bölgesi olarak kabul edilmez.

Örneğin, Meksika 'da bulunan bir Kullanıcı bir istek yayınlar ve bu istek eşleme veya trafik koşullarına bağlı olarak Birleşik Devletler POP 'ta bulunan bir sunucu tarafından hizmet verilirken, faturalandırma Bölgesi Birleşik Devletler olur.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Faturalandırılabilir Azure CDN işlem nedir?
CDN 'de sonlanan herhangi bir HTTP (S) isteği, tüm yanıt türlerini içeren faturalandırılabilir bir olaydır: başarılı, hata veya diğer. Ancak farklı yanıtlar farklı trafik miktarları oluşturabilir. Örneğin, *304 değiştirilmedi* ve diğer üst bilgi yanıtları küçük bir başlık yanıtı olduklarından az trafik oluşturur; benzer şekilde, hata yanıtları (örneğin, *404 bulunamadı*) faturalanabilir ancak küçük bir yanıt yükü nedeniyle küçük bir ücret doğurur.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Azure CDN kullanımı ile ilgili diğer Azure maliyetleri nelerdir?
Azure CDN kullanmak, nesneleriniz için kaynak olarak kullanılan hizmetler üzerinde bazı kullanım ücretleri de doğurur. Bu maliyetler genellikle genel CDN kullanım maliyetinin küçük bir bölümünü alır.

İçeriğiniz için kaynak olarak Azure Blob depolamayı kullanıyorsanız, önbellek dolguları için aşağıdaki depolama ücretlerine de tabi olursunuz:

- Kullanılan gerçek GB: kaynak nesnelerinizin gerçek depolama alanı.

- İşlemler: önbelleğin doldurulması için gereken şekilde.

- GB cinsinden aktarımlar: CDN önbelleklerini dolduracak şekilde aktarılan veri miktarı.

> [!NOTE]
> 2019 Ekim 'den itibaren, Microsoft 'tan Azure CDN kullanıyorsanız, Azure 'da barındırılan kaynaklardan gelen veri aktarımının maliyeti CDN pop 'Lara ücretsizdir. Verizon Azure CDN ve Akamai 'tan gelen Azure CDN, aşağıda açıklanan hızlara tabidir.

Azure depolama faturalandırma hakkında daha fazla bilgi için bkz. [Azure depolama faturalandırmasını anlama – bant genişliği, işlemler ve kapasite](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

*Barındırılan hizmet teslimi*kullanıyorsanız, ücretlendirilecektir:

- Azure İşlem Zamanı: kaynak olarak davranan işlem örnekleri.

- Azure işlem aktarımı: verileri Azure CDN önbelleklerini dolduracak şekilde hesaplama örneklerinden aktarır.

İstemciniz, bayt aralığı isteklerini kullanıyorsa (kaynak hizmetten bağımsız olarak) aşağıdaki noktalar geçerlidir:

- Bir *bayt aralığı ISTEğI* CDN 'de faturalandırılabilir bir işlemdir. Bir istemci, bir bayt aralığı isteği sorun verdiği zaman, bu istek nesnenin bir alt kümesi (aralığı) içindir. CDN, yalnızca istenen içeriğin kısmi bir bölümüyle yanıt verir. Bu kısmi yanıt faturalandırılabilir bir işlemdir ve aktarım miktarı, Aralık yanıtının (artı üstbilgileri) boyutuyla sınırlıdır.

- Bir istek yalnızca bir nesnenin kısmına ulaştığında (bir bayt aralığı üst bilgisi belirterek), CDN tüm nesneyi önbelleğine alabilir. Sonuç olarak, CDN 'den faturalandırılabilir işlem kısmi bir yanıt için olsa bile, kaynağın faturalandırılabilir hareketi nesnenin tam boyutunu içerebilir.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Önbelleği desteklemek için ne kadar aktarım etkinliği meydana geldi?
Bir CDN POP 'un önbelleğini doldurması gereken her seferinde, önbelleğe alınan nesnenin kaynağına bir istek sağlar. Sonuç olarak, kaynak her önbellek isabetsiz bir faturalanabilir işlem doğurur. Önbellek isabetsizlik sayısı bir dizi etkene bağlıdır:

- İçeriğin önbelleklenebilir olması: içerik yüksek TTL (yaşam süresi)/sona erme değerleri Içeriyorsa ve sık sık erişiliyorsa, yükün büyük çoğunluğu CDN tarafından işlenir. Tipik bir iyi önbellek isabet oranı %90 ' dan fazla olduğundan, istemci isteklerinin %10 ' dan küçük bir önbellek isabetsizliği veya nesne yenilemesi için kaynağa dönmesi gereken anlamına gelir.

- Nesne yüklemesi gereken kaç düğüm vardır: bir düğüm kaynaktan bir nesne yüklediğinde, faturalandırılabilir bir işlem doğurur. Sonuç olarak, daha fazla genel içerik (daha fazla düğümden erişilir), faturalandırılabilir işlemlerin oluşmasına neden olur.

- TTL etkisi: bir nesne için daha yüksek bir TTL, kaynağın kaynaktan daha az sıklıkta getirilmesi gerektiği anlamına gelir. Ayrıca, tarayıcılar gibi istemcilerin nesneyi daha uzun sürede önbelleğe alabileceği anlamına gelir ve bu da işlemleri CDN 'ye düşürebilir.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Microsoft 'tan gelen Azure CDN ücretsiz veri aktarımı için hangi kaynak hizmetleri uygun? 
CDN başlangıç noktası olarak aşağıdaki Azure hizmetlerinden birini kullanırsanız, kaynaktan CDN pop 'Lara veri aktarımından ücretlendirilmeyecektir. 

- Azure Depolama
- Azure Media Services
- Azure Sanal Makineler
- Sanal Ağ
- Yük Dengeleyici
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Ağ İzleyicisi
- Azure Güvenlik Duvarı
- Azure Front Door Hizmeti
- Azure Bastion
- Azure Uygulama Hizmeti
- Azure İşlevleri
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Azure Veri Gezgini
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL veritabanı
- Redis için Azure Önbelleği

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Nasıl yaparım? maliyetlerimi en etkili şekilde yönetin mi?
İçeriğiniz için mümkün olan en uzun TTL 'yi ayarlayın. 
