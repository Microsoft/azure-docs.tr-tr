---
title: Azure 'da IBM DB2 pureScale dağıtma
description: Kısa bir süre önce, bir kuruluşu z/OS üzerinde çalışan IBM DB2 ortamından Azure 'da IBM DB2 pureScale 'e geçirmeye yönelik örnek bir mimari dağıtmayı öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 8eb8075454dc3a49e9525d566c34c64bab8be5a0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083439"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Azure 'da IBM DB2 pureScale dağıtma

Bu makalede, bir kurumsal müşterinin son zamanlarda z/OS üzerinde çalışan IBM DB2 ortamından Azure 'da IBM DB2 pureScale 'e geçiş yapmak için kullandığı [örnek bir mimarinin](ibm-db2-purescale-azure.md) nasıl dağıtılacağı açıklanır.

Geçiş için kullanılan adımları izlemek için GitHub 'daki [DB2onAzure](https://aka.ms/db2onazure) deposundaki yükleme betiklerine bakın. Bu betikler tipik, orta ölçekli çevrimiçi işlem işleme (OLTP) iş yükünün mimarisini temel alır.

## <a name="get-started"></a>başlarken

Bu mimariyi dağıtmak için GitHub 'daki [DB2onAzure](https://aka.ms/db2onazure) deposunda bulunan Deploy.sh betiğini indirip çalıştırın.

Depoda Ayrıca bir Grafana panosu ayarlamak için betikler de bulunur. Eklentiyi DB2 ile birlikte bulunan açık kaynaklı izleme ve uyarı sistemini sorgulamak için kullanabilirsiniz.

> [!NOTE]
> İstemcideki deploy.sh betiği özel SSH anahtarları oluşturur ve bunları HTTPS üzerinden Dağıtım şablonuna geçirir. Daha fazla güvenlik için gizli dizileri, anahtarları ve parolaları depolamak üzere [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) kullanmanızı öneririz.

## <a name="how-the-deployment-script-works"></a>Dağıtım betiği nasıl kullanılır?

Deploy.sh betiği, Bu mimaride Azure kaynaklarını oluşturur ve yapılandırır. Betik, hedef ortamda kullanılan Azure aboneliğini ve sanal makineleri ister ve sonra aşağıdaki işlemleri gerçekleştirir:

-   Yükleme için Azure 'da kaynak grubu, sanal ağ ve alt ağları ayarlar

-   Ortam için ağ güvenlik gruplarını ve SSH 'yi ayarlar

-   Hem GlusterFS hem de DB2 pureScale sanal makinelerinde NIC 'Leri ayarlar

-   GlusterFS depolama sanal makinelerini oluşturur

-   Sıçrama kutusu sanal makinesini oluşturur

-   DB2 pureScale sanal makineleri oluşturur

-   DB2 pureScale ping işlemleri yapan tanık sanal makinesini oluşturur

-   Test için kullanılacak bir Windows sanal makinesi oluşturur, ancak buna hiçbir şey yüklemez

Ardından, dağıtım betikleri Azure 'da paylaşılan depolama için bir Iscsı sanal depolama alanı ağı (vSAN) ayarlar. Bu örnekte, Iscsı GlusterFS 'e bağlanır. Bu çözüm, Iscsı hedeflerini tek bir Windows düğümü olarak yüklemek için de seçenek sağlar. Iscsı, DB2 pureScale Kurulum yordamının paylaşılan depolamaya bağlanmak için bir cihaz arabirimi kullanmasına izin veren TCP/IP üzerinden paylaşılan bir blok depolama arabirimi sağlar. GlusterFS temelleri için bkz [. mimari: Gluster docs](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) içindeki birim türleri konusu.

Dağıtım betikleri şu genel adımları çalıştırır:

1.  Azure 'da paylaşılan bir depolama kümesi ayarlamak için GlusterFS kullanın. Bu adım en az iki Linux düğümü içerir. Kurulum ayrıntıları için, Red Hat Gluster belgelerindeki [Microsoft Azure ' de Red Hat Gluster depolamayı ayarlama](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) bölümüne bakın.

2.  GlusterFS için hedef Linux sunucularında bir Iscsı doğrudan arabirimi ayarlayın. Kurulum ayrıntıları için GlusterFS yönetim kılavuzunda [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) ' ye bakın.

3.  Linux sanal makinelerinde Iscsı Başlatıcısı 'nı ayarlayın. Başlatıcı, bir Iscsı hedefi kullanarak GlusterFS kümesine erişir. Kurulum ayrıntıları için bkz. RootUsers belgelerindeki [Linux 'Ta bir Iscsı hedefi ve başlatıcısı yapılandırma](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) .

4.  GlusterFS 'yi Iscsı arabiriminin depolama katmanı olarak yükler.

Betikler Iscsı cihazını oluşturduktan sonra son adım DB2 pureScale 'yi yüklemektir. DB2 pureScale kurulumunun bir parçası olarak, [IBM SPI ölçeği](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (eskıden GPFS olarak bilinirdi), GlusterFS kümesine derlenir ve yüklenir. Bu kümelenmiş dosya sistemi DB2 purescale altyapısını çalıştıran sanal makineler arasında veri paylaşmasını sağlar. Daha fazla bilgi için bkz. IBM Web sitesindeki [IBM SPI ölçek](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) belgeleri.

## <a name="db2-purescale-response-file"></a>DB2 pureScale yanıt dosyası

GitHub deposu, DB2 pureScale yüklemesi için otomatikleştirilmiş bir komut dosyası oluşturmanıza olanak sağlayan bir yanıt (. rsp) dosyası olan DB2server. rsp dosyasını içerir. Aşağıdaki tabloda, yanıt dosyasının kurulum için kullandığı DB2 pureScale seçenekleri listelenmektedir. Yanıt dosyasını ortamınız için gereken şekilde özelleştirebilirsiniz.

> [!NOTE]
> Örnek bir yanıt dosyası olan DB2server. rsp, GitHub 'daki [DB2onAzure](https://aka.ms/db2onazure) deposuna dahil edilmiştir. Bu dosyayı kullanırsanız, ortamınızda çalışmadan önce düzenlemeniz gerekir.

| Ekran adı               | Alan                                        | Value                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Hoş Geldiniz                   |                                              | Yeni yüklemesi                                                                                           |
| Ürün seçin          |                                              | DB2 sürüm 11.1.3.3. DB2 pureScale ile sunucu sürümleri                                              |
| Yapılandırma             | Dizin                                    | /Data1/seçenek/ibm/DB2/v11.1                                                                              |
|                           | Yükleme türünü seçin                 | Genelde                                                                                               |
|                           | IBM şartlarını kabul ediyorum                     | İşaretli                                                                                               |
| Örnek sahibi            | Örnek Için mevcut Kullanıcı, Kullanıcı adı        | DB2sdin1                                                                                              |
| Fbaşvurusu olan Kullanıcı               | Mevcut Kullanıcı, Kullanıcı adı                     | DB2sdfe1                                                                                              |
| Küme dosya sistemi       | Paylaşılan disk bölümü cihaz yolu            | /dev/DM-2                                                                                             |
|                           | Bağlama noktası                                  | /Db2sd\_1804a                                                                                         |
|                           | Veriler için paylaşılan disk                         | /dev/DM-1                                                                                             |
|                           | Bağlama noktası (veri)                           | /DB2fs/datafs1                                                                                        |
|                           | Günlük için paylaşılan disk                          | /dev/DM-0                                                                                             |
|                           | Bağlama noktası (günlük)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 küme hizmetleri Tiekesici. Cihaz yolu | /dev/DM-3                                                                                             |
| Ana bilgisayar listesi                 | D1 [eth1], D2 [eth1], CF1 [eth1], CF2 [eth1] |                                                                                                       |
|                           | Tercih edilen birincil CF                         | cf1                                                                                                   |
|                           | Tercih edilen ikincil CF                       | cf2                                                                                                   |
| Yanıt dosyası ve Özeti | İlk seçenek                                 | IBM DB2 pureScale özelliği ile DB2 Server Edition 'ı yükleyin ve ayarlarımı bir yanıt dosyasına kaydedin |
|                           | Yanıt dosyası adı                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Bu dağıtım ile ilgili notlar

- /Dev-dm0,/dev-DM1,/dev-DM2 ve/dev-dm3 değerleri, kurulumun gerçekleştiği sanal makinede yeniden başlatmanın ardından değişebilir (otomatik betikte D0). Doğru değerleri bulmak için, kurulumun çalışacağı sunucuda yanıt dosyasını tamamlamadan önce aşağıdaki komutu verebilirsiniz:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Kurulum betikleri, gerçek adların kolayca bulunabilmesi için Iscsı disklerinin diğer adlarını kullanır.

- D0 üzerinde kurulum betiği çalıştırıldığında, **\* /dev/DM-** Values, D1, cf0 ve CF1 üzerinde farklı olabilir. Değerlerin farkı, DB2 pureScale kurulumunu etkilemez.

## <a name="troubleshooting-and-known-issues"></a>Sorun çözümü ve bilinen sorunlar

GitHub deposu, yazarların koruabileceği bir Bilgi Bankası içerir. Karşılaşabileceğiniz olası sorunları ve deneyebileceğiniz çözümleri listeler. Örneğin, bilinen sorunlar şu durumlarda oluşabilir:

-   Ağ geçidi IP adresine ulaşmaya çalışıyorsunuz.

-   Genel genel lisansı (GPL) derliyoruz.

-   Konaklar arasındaki güvenlik anlaşması başarısız oluyor.

-   DB2 yükleyicisi var olan bir dosya sistemini algılar.

-   IBM SPFI ölçeğini el ile yüklüyorsunuz.

-   IBM SPFI ölçeği zaten oluşturulduğunda DB2 pureScale 'yi yüklüyorsunuz.

-   DB2 pureScale ve IBM SPFI ölçeğini kaldırıyorsunuz.

Bu ve diğer bilinen sorunlar hakkında daha fazla bilgi için [DB2onAzure](https://aka.ms/DB2onAzure) depodaki KB.MD dosyasına bakın.

## <a name="next-steps"></a>Sonraki adımlar

-   [GlusterFS Iscsı](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [DB2 pureScale özelliği yüklemesi için gerekli kullanıcıları oluşturma](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt-örnek Oluştur komutu](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale kümeleri veri çözümü](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform modernleştirme Alliance: Azure 'da IBM DB2](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure sanal veri merkezi yükselt ve kaydırma Kılavuzu](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
