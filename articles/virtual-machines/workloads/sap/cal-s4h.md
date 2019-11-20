---
title: Azure VM 'de SAP S/4HANA veya siyah beyaz/4HANA dağıtma | Microsoft Docs
description: Azure VM 'de SAP S/4HANA veya siyah beyaz/4HANA dağıtma
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 2fa68d9dc3052263b5354086ee802cc31fa35ace
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101454"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Azure 'da SAP S/4HANA veya siyah beyaz/4HANA dağıtma
Bu makalede SAP Cloud gereç kitaplığı (SAP CAL) 3,0 kullanılarak Azure 'da S/4HANA 'nın nasıl dağıtılacağı açıklanır. Siyah beyaz/4HANA gibi diğer SAP HANA tabanlı çözümleri dağıtmak için aynı adımları izleyin.

> [!NOTE]
> SAP CAL hakkında daha fazla bilgi için [SAP Cloud gereç kitaplığı](https://cal.sap.com/) Web sitesine gidin. SAP Ayrıca [SAP Cloud gereç kitaplığı 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)ile ilgili bir bloga sahiptir.
> 
> [!NOTE]
> 29 Mayıs 2017 itibariyle, SAP CAL dağıtımı için daha az tercih edilen klasik dağıtım modeline ek olarak Azure Resource Manager dağıtım modelini kullanabilirsiniz. Yeni Kaynak Yöneticisi dağıtım modelini kullanmanızı ve klasik dağıtım modelini göz ardı etmenizi öneririz.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Çözümü dağıtmak için adım adım işlem

Aşağıdaki ekran görüntüleri dizisi, SAP CAL kullanarak Azure 'da S/4HANA 'yı nasıl dağıtacağınızı gösterir. İşlem, siyah beyaz/4HANA gibi diğer çözümlerle aynı şekilde işler.

**Çözümler** sayfasında, Azure 'DA KULLANıLABILEN SAP Cal Hana tabanlı çözümlerin bazıları gösterilir. **SAP S/4HANA 1610 FPS01, tam özellikli gereç** orta satırdaysa:

![SAP CAL çözümleri](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>SAP CAL 'de hesap oluşturma
1. SAP CAL ' de ilk kez oturum açmak için SAP S-User veya SAP ile kayıtlı diğer Kullanıcı ' yı kullanın. Ardından, SAP CAL tarafından Azure 'da gereçlere dağıtım yapmak için kullanılan bir SAP CAL hesabı tanımlayın. Hesap tanımında şunları yapmanız gerekir:

    a. Azure 'da (Kaynak Yöneticisi veya klasik) dağıtım modelini seçin.

    b. Azure aboneliğinizi girin. SAP CAL hesabı yalnızca bir aboneliğe atanabilir. Birden fazla aboneliğe ihtiyacınız varsa, başka bir SAP CAL hesabı oluşturmanız gerekir.

    c. SAP CAL 'nin Azure aboneliğinize dağıtılması için izin verin.

   > [!NOTE]
   >  Sonraki adımlarda Kaynak Yöneticisi dağıtımları için SAP CAL hesabı oluşturma gösterilmektedir. Klasik dağıtım modeliyle bağlantılı bir SAP CAL hesabınız zaten varsa, yeni bir SAP CAL hesabı oluşturmak için bu adımları izlemeniz *gerekir* . Yeni SAP CAL hesabının Kaynak Yöneticisi modelinde dağıtılması gerekir.

1. Yeni bir SAP CAL hesabı oluşturun. **Hesaplar** sayfasında Azure için üç seçenek gösterilmektedir: 

    a. **Microsoft Azure (klasik)** , klasik dağıtım modelidir ve artık tercih edilmektedir.

    b. Yeni Kaynak Yöneticisi dağıtım modeli **Microsoft Azure** .

    c. **21Vianet tarafından çalıştırılan Windows Azure** , klasik dağıtım modelini kullanan Çin 'deki bir seçenektir.

    Kaynak Yöneticisi modelinde dağıtmak için **Microsoft Azure**' ı seçin.

    ![SAP CAL hesabı ayrıntıları](./media/cal-s4h/s4h-pic-2a.png)

1. Azure portal bulunan Azure **ABONELIK kimliğini** girin.

   ![SAP CAL hesapları](./media/cal-s4h/s4h-pic3c.png)

1. SAP CAL 'sini tanımladığınız Azure aboneliğine dağıtmak üzere yetkilendirmek için **Yetkilendir**' e tıklayın. Tarayıcı sekmesinde aşağıdaki sayfa görüntülenir:

   ![Internet Explorer bulut Hizmetleri oturum açma](./media/cal-s4h/s4h-pic4c.png)

1. Birden fazla Kullanıcı listeleniyorsa, seçtiğiniz Azure aboneliğinin ortak Yöneticisi olacak şekilde bağlantılı Microsoft hesabı seçin. Tarayıcı sekmesinde aşağıdaki sayfa görüntülenir:

   ![Internet Explorer bulut hizmetleri onayı](./media/cal-s4h/s4h-pic5a.png)

1. Tıklayın **kabul**. Yetkilendirme başarılı olursa SAP CAL hesabı tanımı yeniden görüntülenir. Kısa bir süre sonra, bir ileti yetkilendirme işleminin başarılı olduğunu onaylar.

1. Yeni oluşturulan SAP CAL hesabını kullanıcıya atamak için, sağdaki metin kutusuna **Kullanıcı Kimliğinizi** girin ve **Ekle**' ye tıklayın.

   ![Hesaptan Kullanıcı ilişkilendirmesi](./media/cal-s4h/s4h-pic8a.png)

1. Hesabınızı SAP CAL 'sinde oturum açmak için kullandığınız kullanıcıyla ilişkilendirmek için **gözden geçir**' e tıklayın. 
 
1. Kullanıcı ve yeni oluşturulan SAP CAL hesabı arasındaki ilişkiyi oluşturmak için **Oluştur**' a tıklayın.

   ![Kullanıcıdan SAP CAL hesabı ilişkilendirmesi](./media/cal-s4h/s4h-pic9b.png)

Şu şekilde kullanabileceğiniz bir SAP CAL hesabı başarıyla oluşturuldu:

- Kaynak Yöneticisi dağıtım modelini kullanın.
- SAP sistemlerini Azure aboneliğinize dağıtın.

Artık, Azure 'daki Kullanıcı aboneliğinize S/4HANA dağıtmaya başlayabilirsiniz.

> [!NOTE]
> Devam etmeden önce Azure H serisi VM 'Ler için Azure vCPU kotaları olup olmadığını saptayın. Şu anda SAP CAL, bazı SAP HANA tabanlı çözümleri dağıtmak için Azure 'un H serisi VM 'lerini kullanır. Azure aboneliğiniz H serisi için H serisi vCPU kotalarına sahip olmayabilir. Bu durumda, en az 16 H serisi vCPU kotası almak için Azure desteği ile iletişim kurmanız gerekebilir.
> 
> [!NOTE]
> Azure 'da SAP CAL 'sinde bir çözüm dağıttığınızda yalnızca bir Azure bölgesi seçebileceğinizi fark edebilirsiniz. SAP CAL tarafından önerilenden farklı Azure bölgelerine dağıtmak için SAP 'den bir CAL aboneliği satın almanız gerekir. Ayrıca, CAL hesabınızın başlangıçta önerilenden farklı Azure bölgelerine teslim edilmesi için SAP ile bir ileti açmanız gerekebilir.

### <a name="deploy-a-solution"></a>Çözüm dağıtma

SAP CAL 'nin **çözümler** sayfasından bir çözüm dağıtalım. SAP CAL 'nin dağıtılması için iki sırası vardır:

- Dağıtılacak sistemi tanımlamak için bir sayfa kullanan temel bir sıra
- VM boyutlarında belirli seçimler sağlayan gelişmiş bir sıra 

Dağıtımın temel yolunu burada gösteririz.

1. **Hesap ayrıntıları** sayfasında şunları yapmanız gerekir:

    a. SAP CAL hesabı seçin. (Kaynak Yöneticisi dağıtım modeliyle dağıtım ile ilişkili bir hesabı kullanın.)

    b. Örnek **adı**girin.

    c. Bir Azure **bölgesi**seçin. SAP CAL 'si bir bölge önerir. Başka bir Azure bölgesine ihtiyacınız varsa ve SAP CAL aboneliğiniz yoksa SAP ile CAL aboneliği sipariş etmeniz gerekir.

    d. Sekiz veya dokuz karakterlik çözüm için bir ana **parola** girin. Parola, farklı bileşenlerin yöneticileri için kullanılır.

   ![SAP CAL temel modu: Örnek Oluştur](./media/cal-s4h/s4h-pic10a.png)

1. **Oluştur**' a tıklayın ve görüntülenen Ileti kutusunda **Tamam**' a tıklayın.

   ![SAP CAL desteklenen VM boyutları](./media/cal-s4h/s4h-pic10b.png)

1. Özel **anahtar** iletişim kutusunda, özel anahtarı SAP Cal 'de depolamak için **Mağaza** ' ya tıklayın. Özel anahtar için parola korumasını kullanmak için **İndir**' e tıklayın. 

   ![SAP CAL özel anahtarı](./media/cal-s4h/s4h-pic10c.png)

1. SAP CAL **Uyarı** iletisini okuyun ve **Tamam**' a tıklayın.

   ![SAP CAL uyarısı](./media/cal-s4h/s4h-pic10d.png)

    Artık dağıtım gerçekleşir. Bir süre sonra çözümün boyutuna ve karmaşıklığına (SAP CAL bir tahmin sağlar) bağlı olarak, durum etkin ve kullanıma hazırlık olarak gösterilir.

1. Tek bir kaynak grubundaki diğer ilişkili kaynaklarla toplanan sanal makineleri bulmak için Azure portal gidin: 

   ![Yeni portalda dağıtılan SAP CAL nesneleri](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. SAP CAL portalında durum **etkin**olarak görünür. Çözüme bağlanmak için **Bağlan**' a tıklayın. Farklı bileşenlere bağlanmak için farklı seçenekler bu çözüm içinde dağıtılır.

   ![SAP CAL örnekleri](./media/cal-s4h/active_solution.png)

1. Dağıtılan sistemlere bağlanmak için seçeneklerden birini kullanabilmeniz için **Başlangıç Kılavuzu**' na tıklayın. 

   ![Örneğe Bağlan](./media/cal-s4h/connect_to_solution.png)

    Belgeler, her bir bağlantı yöntemi için kullanıcıları adlandırır. Bu kullanıcıların parolaları, dağıtım işleminin başlangıcında tanımladığınız ana parolaya ayarlanır. Belgelerde, diğer işlevsel kullanıcılar parolalarla birlikte listelenir ve bu, dağıtılan sistemde oturum açmak için kullanabilirsiniz. 

    Örneğin, Windows Uzak Masaüstü makinesinde önceden yüklenmiş SAP GUI kullanıyorsanız, S/4 sistemi şöyle görünebilir:

   ![Önceden yüklenmiş SAP GUI SM50](./media/cal-s4h/gui_sm50.png)

    Ya da Dbakokpit kullanıyorsanız, örnek şöyle görünebilir:

   ![Dbakokpit SAP GUI 'de SM50](./media/cal-s4h/dbacockpit.png)

Birkaç saat içinde, Azure 'da sağlıklı bir SAP S/4 gereci dağıtılır.

SAP CAL aboneliği satın aldıysanız SAP, Azure üzerinde SAP CAL aracılığıyla dağıtımları tamamen destekler. Destek kuyruğu BC-VCM-CAL ' dir.







