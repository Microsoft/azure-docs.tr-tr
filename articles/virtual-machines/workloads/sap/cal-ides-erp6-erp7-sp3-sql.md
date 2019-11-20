---
title: Azure 'da SAP ERP 6,0 için SAP ıDES EHP7 SP3 'Ü dağıtma | Microsoft Docs
description: Azure 'da SAP ERP 6,0 için SAP ıDES EHP7 SP3 'Ü dağıtma
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 66921280403027d1723b27f104b42d2c83271213
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100077"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Azure 'da SAP ERP 6,0 için SAP ıDES EHP7 SP3 'Ü dağıtma
Bu makalede, SAP Cloud gereç kitaplığı (SAP CAL) 3,0 aracılığıyla Azure 'da SQL Server ve Windows işletim sistemi çalıştıran bir SAP ıDES sisteminin nasıl dağıtılacağı açıklanır. Ekran görüntüleri adım adım işlemi gösterir. Farklı bir çözüm dağıtmak için, aynı adımları izleyin.

SAP CAL ile başlamak için [SAP Cloud gereç kitaplığı](https://cal.sap.com/) Web sitesine gidin. SAP Ayrıca yeni [SAP Cloud gereç kitaplığı 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)ile ilgili bir bloga sahiptir. 

> [!NOTE]
> 29 Mayıs 2017 itibariyle, SAP CAL dağıtımı için daha az tercih edilen klasik dağıtım modeline ek olarak Azure Resource Manager dağıtım modelini kullanabilirsiniz. Yeni Kaynak Yöneticisi dağıtım modelini kullanmanızı ve klasik dağıtım modelini göz ardı etmenizi öneririz.

Klasik modeli kullanan bir SAP CAL hesabı zaten oluşturduysanız, *başka BIR SAP Cal hesabı oluşturmanız gerekir*. Bu hesabın Kaynak Yöneticisi modelini kullanarak Azure 'a özel olarak dağıtmaları gerekir.

SAP CAL 'de oturum açtıktan sonra, ilk sayfa genellikle **çözümler** sayfasına yönlendirir. SAP CAL üzerinde sunulan çözümler artmasıyla artmakta olduğundan istediğiniz çözümü bulmak için tam olarak bir bit kaydırmanız gerekebilir. Yalnızca Azure üzerinde kullanılabilir olan vurgulanmış Windows tabanlı SAP ıDES çözümü, dağıtım işlemini gösterir:

![SAP CAL çözümleri](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>SAP CAL 'de hesap oluşturma
1. SAP CAL ' de ilk kez oturum açmak için SAP S-User veya SAP ile kayıtlı diğer Kullanıcı ' yı kullanın. Ardından, SAP CAL tarafından Azure 'da gereçlere dağıtım yapmak için kullanılan bir SAP CAL hesabı tanımlayın. Hesap tanımında şunları yapmanız gerekir:

    a. Azure 'da (Kaynak Yöneticisi veya klasik) dağıtım modelini seçin.

    b. Azure aboneliğinizi girin. SAP CAL hesabı yalnızca bir aboneliğe atanabilir. Birden fazla aboneliğe ihtiyacınız varsa, başka bir SAP CAL hesabı oluşturmanız gerekir.
    
    c. SAP CAL 'nin Azure aboneliğinize dağıtılması için izin verin.

   > [!NOTE]
   >  Sonraki adımlarda Kaynak Yöneticisi dağıtımları için SAP CAL hesabı oluşturma gösterilmektedir. Klasik dağıtım modeliyle bağlantılı bir SAP CAL hesabınız zaten varsa, yeni bir SAP CAL hesabı oluşturmak için bu adımları izlemeniz *gerekir* . Yeni SAP CAL hesabının Kaynak Yöneticisi modelinde dağıtılması gerekir.

1. Yeni bir SAP CAL hesabı oluşturmak için, **hesaplar** sayfasında Azure için iki seçenek gösterilir: 

    a. **Microsoft Azure (klasik)** , klasik dağıtım modelidir ve artık tercih edilmektedir.

    b. Yeni Kaynak Yöneticisi dağıtım modeli **Microsoft Azure** .

    ![SAP CAL hesapları](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Kaynak Yöneticisi modelinde dağıtmak için **Microsoft Azure**' ı seçin.

    ![SAP CAL hesapları](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Azure portal bulunan Azure **ABONELIK kimliğini** girin. 

    ![SAP CAL abonelik KIMLIĞI](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. SAP CAL 'sini tanımladığınız Azure aboneliğine dağıtmak üzere yetkilendirmek için **Yetkilendir**' e tıklayın. Tarayıcı sekmesinde aşağıdaki sayfa görüntülenir:

    ![Internet Explorer bulut Hizmetleri oturum açma](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Birden fazla Kullanıcı listeleniyorsa, seçtiğiniz Azure aboneliğinin ortak Yöneticisi olacak şekilde bağlantılı Microsoft hesabı seçin. Tarayıcı sekmesinde aşağıdaki sayfa görüntülenir:

    ![Internet Explorer bulut hizmetleri onayı](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Tıklayın **kabul**. Yetkilendirme başarılı olursa SAP CAL hesabı tanımı yeniden görüntülenir. Kısa bir süre sonra, bir ileti yetkilendirme işleminin başarılı olduğunu onaylar.

1. Yeni oluşturulan SAP CAL hesabını kullanıcıya atamak için, sağdaki metin kutusuna **Kullanıcı Kimliğinizi** girin ve **Ekle**' ye tıklayın. 

    ![Hesaptan Kullanıcı ilişkilendirmesi](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Hesabınızı SAP CAL 'sinde oturum açmak için kullandığınız kullanıcıyla ilişkilendirmek için **gözden geçir**' e tıklayın. 

1. Kullanıcı ve yeni oluşturulan SAP CAL hesabı arasındaki ilişkiyi oluşturmak için **Oluştur**' a tıklayın.

    ![Kullanıcıdan hesaba ilişkilendirme](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Şu şekilde kullanabileceğiniz bir SAP CAL hesabı başarıyla oluşturuldu:

- Kaynak Yöneticisi dağıtım modelini kullanın.
- SAP sistemlerini Azure aboneliğinize dağıtın.

> [!NOTE]
> SAP ıDES çözümünü Windows ve SQL Server dayalı olarak dağıtabilmeniz için önce SAP CAL aboneliğine kaydolmanız gerekebilir. Aksi takdirde, çözüm Genel Bakış sayfasında **kilitli** olarak görüntülenebilir.

### <a name="deploy-a-solution"></a>Çözüm dağıtma
1. SAP CAL hesabı ayarladıktan sonra **Windows ve SQL Server çözümünde SAP ıDES çözümünü** seçin. **Örnek oluştur**' a tıklayın ve kullanım ve hüküm koşullarını onaylayın. 

1. **Temel modda: Örnek** sayfası oluşturun, şunları yapmanız gerekir:

    a. Örnek **adı**girin.

    b. Bir Azure **bölgesi**seçin. Birden çok Azure bölgesini sunulan bir SAP CAL aboneliğine sahip olmanız gerekebilir.

    c.  Çözümün ana **parolasını** aşağıda gösterildiği gibi girin:

    ![SAP CAL temel modu: Örnek Oluştur](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. **Oluştur**'a tıklayın. Bir süre sonra çözümün boyutuna ve karmaşıklığına (SAP CAL bir tahmin sağlar) bağlı olarak, durum etkin ve kullanıma hazırlık olarak gösterilir: 

    ![SAP CAL örnekleri](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Kaynak grubunu ve SAP CAL tarafından oluşturulan tüm nesnelerini bulmak için Azure portal gidin. Sanal makine, SAP CAL 'sinde verilen aynı örnek adıyla başlayarak bulunabilir.

    ![Kaynak grubu nesneleri](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. SAP CAL portalında, dağıtılan örneklere gidin ve **Bağlan**' a tıklayın. Aşağıdaki açılır pencere görüntülenir: 

    ![Örneğe Bağlan](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Dağıtılan sistemlere bağlanmak için seçeneklerden birini kullanabilmeniz için **Başlangıç Kılavuzu**' na tıklayın. Belgeler, her bir bağlantı yöntemi için kullanıcıları adlandırır. Bu kullanıcıların parolaları, dağıtım işleminin başlangıcında tanımladığınız ana parolaya ayarlanır. Belgelerde, diğer işlevsel kullanıcılar parolalarla birlikte listelenir ve bu, dağıtılan sistemde oturum açmak için kullanabilirsiniz.

    ![SAP karşılama belgeleri](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Birkaç saat içinde, sağlıklı bir SAP ıDES sistemi Azure 'da dağıtılır.

SAP CAL aboneliği satın aldıysanız SAP, Azure üzerinde SAP CAL aracılığıyla dağıtımları tamamen destekler. Destek kuyruğu BC-VCM-CAL ' dir.

