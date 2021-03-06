---
title: Az adatgyűjtést az Azure Security Centerben |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan telepíthet egy Log Analytics-ügynököt, és hogyan állíthat be egy Log Analytics-munkaterületet, amelyben tárolni kívánja az összegyűjtött adatokat.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 5236fd89ed99ca14bb7fc24e40ef79e12e5177d9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245498"
---
# <a name="data-collection-in-azure-security-center"></a>Az adatgyűjtést az Azure Security Centerben
A Security Center adatokat gyűjt az Azure-beli virtuális gépekről (VM), a virtuálisgép-méretezési csoportokról, a IaaS-tárolók és a nem Azure-beli (beleértve a helyszíni) számítógépekről a biztonsági rések és fenyegetések figyeléséhez. Az adatok gyűjtése a Log Analytics ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a gépről, és az adatokat a munkaterületre másolja az elemzéshez. Az ilyen adatok többek között: operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép nevét, az IP-címeket, és bejelentkezett felhasználó. A Log Analytics ügynök az összeomlási memóriaképek fájljait is másolja a munkaterületre.

Az adatgyűjtés szükséges a hiányzó frissítések láthatóságának biztosításához, a hibásan konfigurált operációs rendszer biztonsági beállításai, az Endpoint Protection állapota, valamint az állapot és a veszélyforrások elleni védelem. 

Ez a cikk azt ismerteti, hogyan telepíthet egy Log Analytics-ügynököt, és hogyan állíthat be egy Log Analytics-munkaterületet, amelyben tárolni kívánja az összegyűjtött adatokat. Mindkét műveletet az adatgyűjtés engedélyezéséhez szükségesek. 

> [!NOTE]
> - Az adatgyűjtés csak számítási erőforrások (virtuális gépek, virtuálisgép-méretezési csoportok, IaaS-tárolók és nem Azure-beli számítógépek) esetében szükséges. Élvezheti az Azure Security Center akkor is, ha nem üzembe helyezi az ügynökök; azonban Ön csak korlátozott biztonsági, és a fent felsorolt funkciók nem támogatottak.  
> - A támogatott platformok listáját lásd: [Azure Security Center támogatott platformok](security-center-os-coverage.md).
> - Az adatok tárolása Log Analyticsban, akár új, akár meglévő munkaterületet használ, az adattárolásra további díjak merülhetnek fel. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

## Az log Analytics-ügynök automatikus kiépítés engedélyezése<a name="auto-provision-mma"></a>

Az adatok a gépekről való összegyűjtéséhez telepítenie kell a Log Analytics-ügynököt. Az ügynök telepítése automatikusan végezhető el (ajánlott), vagy manuálisan is telepítheti az ügynököt.  

>[!NOTE]
> Alapértelmezés szerint az Automatikus kiépítés le van. Ha úgy szeretné beállítani a Security Centert, hogy alapértelmezés szerint telepítse az automatikus kiépítés beállítást, állítsa be **a**következőre:.
>

Ha az automatikus kiosztás be van kapcsolva, Security Center kiépíti a Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépen és a létrehozott újakon. Az Automatikus kiépítés használata erősen ajánlott, de a manuális ügynöktelepítések is rendelkezésre áll. [Ismerje meg, hogyan telepítheti a log Analytics Agent bővítményt](#manual-agent).



Az log Analytics-ügynök automatikus kiépítés engedélyezése:
1. A Security Center Főmenüben válassza a **díjszabás & beállítások**lehetőséget.
2. Kattintson a megfelelő előfizetésre

   ![Előfizetés kiválasztása][7]

3. Válassza **az adatgyűjtés**lehetőséget.
4. Az automatikus **kiépítés**területen válassza **a** be lehetőséget az automatikus kiépítés engedélyezéséhez.
5. Kattintson a **Mentés** gombra.

   ![Automatikus kiépítés engedélyezése][1]

>[!NOTE]
> - A már meglévő telepítések kiépítésével kapcsolatos utasításokért lásd: az [automatikus kiépítés egy előre létező ügynök telepítése esetén](#preexisting).
> - A manuális üzembe helyezéssel kapcsolatos utasításokért lásd: [a log Analytics Agent bővítmény telepítése manuálisan](#manual-agent).
> - Az automatikus kiépítés kikapcsolásával kapcsolatos utasításokért lásd: az [automatikus kiépítés](#offprovisioning)kikapcsolása.
> - A Security Center PowerShell használatával történő előkészítésével kapcsolatos útmutatásért lásd: [a Azure Security Center bevezetésének automatizálása a PowerShell használatával](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Munkaterület-konfiguráció
A Security Center által gyűjtött adatokat a rendszer Log Analytics-munkaterület(ek)en tárolja Kiválaszthatja, hogy az adatok tárolása a Security Center által létrehozott munkaterületeken vagy egy meglévő munkaterületet hozott létre az Azure virtuális gépekről gyűjtött. 

Munkaterület-konfiguráció beállítása előfizetésenként, de sok előfizetést használhatja ugyanazon a munkaterületen.

### <a name="using-a-workspace-created-by-security-center"></a>A Security Center által létrehozott munkaterület használatával

A Security center automatikusan létrehozhat egy alapértelmezett munkaterületet, amely tárolja az adatokat. 

A Security Center által létrehozott munkaterület kiválasztása:

1. Az **alapértelmezett munkaterület konfigurálása**területen jelölje be a Security Center által létrehozott munkaterület (ok) használata jelölőnégyzetet.
   ![válassza az árképzési szintet][10] 

1. Kattintson a **Save** (Mentés) gombra.<br>
    A Security Center egy új erőforrás és egy alapértelmezett munkaterületet hoz létre, hogy földrajzi hely, és csatlakoztatja az ügynököt a munkaterülethez. A munkaterület és erőforrás-csoport elnevezési van:<br>
   **Munkaterület: Alapértelmezettmunkaterület-[előfizetés-azonosító]-[geo]<br> erőforráscsoport: DefaultResourceGroup-[geo]**

   Ha egy előfizetés több geolocations a virtuális gépeket tartalmaz, a Security Center több munkaterületet hoz létre. Több munkaterülettel jönnek létre az adatok adatvédelmi szabályok kezelése.
1. A Security Center automatikusan engedélyezi a a tarifacsomagot állítsa be az előfizetés-munkaterülethez a Security Center megoldást. 

> [!NOTE]
> A Security Center által létrehozott munkaterület tarifacsomagját a Log Analytics nincs hatással a Security Center a számlázás. A Security Center árazása minden esetben a Security Center biztonsági szabályzat és az egyes munkaterületekre telepített megoldások alapján történik. Az ingyenes szinten a Security Center a *SecurityCenterFree* megoldást teszi elérhetővé az alapértelmezett munkaterületen. A Standard szinten a Security Center a *Security* megoldást teszi elérhetővé az alapértelmezett munkaterületen.
> Az adatok Log Analyticsban való tárolása további díjakat eredményezhet az adattároláshoz. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

A meglévő log Analytics-fiókokkal kapcsolatos további információkért lásd: [meglévő log Analytics-ügyfelek](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Egy meglévő munkaterület használata

Ha már rendelkezik meglévő Log Analytics munkaterülettel, érdemes lehet ugyanazt a munkaterületet használni.

A meglévő Log Analytics-munkaterület használatához rendelkeznie kell olvasási és írási engedélyekkel a munkaterületen.

> [!NOTE]
> Azure virtuális gépekre, amelyek csatlakoznak a meglévő munkaterületen engedélyezett megoldások lépnek érvénybe. Fizetett megoldások esetén ez további díjakat eredményezhet. Az adatok adatvédelmi megfontolások ellenőrizze, hogy a kijelölt munkaterület a megfelelő földrajzi régióban.
> Az adatok log Analyticsben való tárolása további díjakat eredményezhet az adattároláshoz. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

Egy meglévő Log Analytics-munkaterület kiválasztása:

1. Az **alapértelmezett munkaterület konfigurálása**területen válassza a **másik munkaterület használata**lehetőséget.

   ![Meglévő munkaterület kiválasztása][2]

2. A legördülő menüből válassza ki a munkaterületet a gyűjtött adatok tárolásához.

   > [!NOTE]
   > A lekéréses menüre, az összes előfizetés között a munkaterületek érhetők el. További információért tekintse meg a több [előfizetés munkaterületének kiválasztása](security-center-enable-data-collection.md#cross-subscription-workspace-selection) című témakört. A munkaterület hozzáférési engedéllyel kell rendelkeznie.
   >
   >

3. Kattintson a **Mentés** gombra.
4. A **Mentés**gombra kattintva a rendszer megkérdezi, hogy szeretné-e újrakonfigurálni azokat a figyelt virtuális gépeket, amelyek korábban egy alapértelmezett munkaterülethez voltak csatlakoztatva.

   - Válassza a **nem** lehetőséget, ha azt szeretné, hogy az új munkaterület-beállítások csak az új virtuális gépeken legyenek érvényesek. Az új munkaterület-beállítások csak az új ügynök telepítésére érvényesek; újonnan felderített virtuális gépek, amelyeken nincs telepítve a Log Analytics ügynök.
   - Válassza az **Igen** lehetőséget, ha azt szeretné, hogy az új munkaterület-beállítások minden virtuális gépen érvényesek legyenek. Emellett egy munkaterület létrehozása a Security Center minden virtuális Gépről az új cél munkaterületet újra csatlakoztatni.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, nem kell törölnie a mindaddig, amíg az összes virtuális gép újra lett csatlakozott az új cél munkaterületet, a Security Center által létrehozott munkaterület(ek). Ez a művelet sikertelen lesz, ha a munkaterület túl korán törlődik.
   >
   >

   - A művelet megszakításához kattintson a **Mégse** gombra.

     ![Meglévő munkaterület kiválasztása][3]

5. Válassza ki a kívánt munkaterülethez tartozó árképzési szintet, amelyet a Log Analytics-ügynököt kíván beállítani. <br>Egy meglévő munkaterületet használja, állítsa be a munkaterület tarifacsomagja. Ez telepíti a security Center megoldás a munkaterület Ha egyik még nem létezik.

    a.  A Security Center főmenüjében válassza a **díjszabás & beállítások**elemet.
     
    b.  Válassza ki azt a kívánt munkaterületet, amelyben csatlakozni kíván az ügynökhöz.
        ![válassza ki a munkaterületet][7] c. Állítsa a tarifacsomagot.
        ![válassza az árképzési szintet][9]
   
   >[!NOTE]
   >Ha a munkaterülethez már engedélyezve van egy **biztonsági** vagy **SecurityCenterFree** megoldás, a díjszabás automatikusan be lesz állítva. 

## <a name="cross-subscription-workspace-selection"></a>Az előfizetések közötti munkaterület kiválasztása
Amikor kiválaszt egy munkaterületet, amely tárolja az adatokat, az összes előfizetés a munkaterületek érhetők el. A munkaterület előfizetések közötti kiválasztása lehetővé teszi, hogy különböző előfizetésekben található virtuális gépekről gyűjtsön adatokat, és az Ön által választott munkaterületen tárolja őket. Ez a beállítás akkor hasznos, ha egy központosított munkaterületet használ a szervezetben, és ezt szeretné használni a biztonsági adatok gyűjtéséhez is. A munkaterületek kezelésével kapcsolatos további információkért lásd: [munkaterület-hozzáférés kezelése](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Gyűjtemény adatszint
Amikor az Azure Security Centerben kiválaszt egy adatgyűjtési szintet, az csak a biztonsági események Log Analytics-munkaterületen való tárolására lesz hatással. A Log Analytics ügynök továbbra is gyűjti és elemzi a Azure Security Center veszélyforrások elleni védelemhez szükséges biztonsági eseményeket, függetlenül attól, hogy az Log Analytics munkaterületen milyen biztonsági eseményeket kíván tárolni (ha van ilyen). Ha úgy dönt, hogy eltárolja a biztonsági eseményeket a munkaterületen, ezáltal lehetővé teszi a vizsgálatukat, keresésüket és naplózásukat. 
> [!NOTE]
> Az adatok log Analyticsben való tárolása további díjakat eredményezhet az adattároláshoz. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.
> 
> A jobb házirend az előfizetések és munkaterületek négyféle események szűrése a munkaterület-ben való tárolásának közül választhat: 

- **Nincs** – a biztonsági események tárolójának letiltása. Ez az alapértelmezett beállítás.
- **Minimális** – az esemény kötetét minimalizálni kívánó ügyfelek kisebb csoportja.
- **Common (gyakori** ) – Ez egy olyan esemény, amely megfelel a legtöbb ügyfélnek, és lehetővé teszi, hogy teljes körű naplózási nyomvonalat hozzon létre.
- **Minden esemény** – azon ügyfelek számára, akik az összes eseményt tárolni szeretnék.


> [!NOTE]
> Ezen biztonsági események készletek csak a Security Center Standard szinten érhetők el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
Ezen készletek tervezték, hogy a tipikus forgatókönyvek. Ellenőrizze, hogy melyik illik jobban az igényeinek, még a megvalósítás előtt kiértékeléséhez.
>
>

A **gyakori** és a **minimális** eseményekhez tartozó események meghatározása érdekében ügyfeleinkkel és iparági szabványokkal együttműködve megismerheti az egyes események és azok használatának szűretlen gyakoriságát. Ez a folyamat használjuk a következő irányelveket:

- **Minimális** – győződjön meg arról, hogy ez a készlet csak olyan eseményekre vonatkozik, amelyek egy nagyon alacsony kötettel rendelkező sikeres szabálysértést és fontos eseményeket jelezhetnek. Ez a készlet például a felhasználó sikeres és sikertelen bejelentkezését (4624, 4625) tartalmazza, de nem tartalmazza a kijelentkezést, amely fontos a naplózáshoz, de az észleléshez nem releváns, és viszonylag nagy mennyiségű. Ezen adatok mennyisége a legtöbb, a bejelentkezési események és folyamat létrehozása event (esemény azonosítója 4688).
- **Common** – teljes felhasználói naplózási nyomvonalat adhat meg ebben a készletben. Ez a készlet például felhasználói bejelentkezéseket és felhasználói bejelentkezéseket is tartalmaz (4634-es azonosítójú esemény). Például a biztonsági csoportok változásait, legfontosabb tartomány tartományvezérlő Kerberos műveleti és az eseményeket, amelyek a szervezetek által ajánlott műveletek naplózási tartalmazza.

Eseményeket, amelyek nagyon kevés a Rendszeríró a közös állítja be a fő motiváció kiválasztása az összes esemény keresztül csökkentése és a meghatározott események kiszűrésére.

Íme a biztonsági és AppLocker esemény azonosítók az egyes teljes áttekintését:

| Adatszint | Összegyűjtött események mutatók |
| --- | --- |
| Minimális | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Közös | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Ha Csoportházirend objektumot (GPO-t) használ, javasoljuk, hogy engedélyezze a naplózási házirendek folyamatát a 4688-as és a 4688-es eseményen belüli *parancssori* mezőben. További információ a 4688-es folyamat-létrehozási eseményről: Security Center [Gyakori kérdések](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled). További információ ezekről a naplózási házirendekről: a [naplórend ajánlásai](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Ha engedélyezni szeretné az adatgyűjtést az [adaptív alkalmazások vezérlőinek](security-center-adaptive-application.md)számára, Security Center egy helyi AppLocker-házirendet konfigurál a naplózási módban az összes alkalmazás engedélyezéséhez. Ennek hatására az AppLocker események, amelyeket gyűjteni, és a Security Center által használható létrehozása. Fontos megjegyezni, hogy ez a szabályzat minden olyan gépeken, amelyeken már van egy konfigurált AppLocker-házirendek nem lesznek konfigurálva. 
> - A Windows Filtering platform [5156-es azonosítójú eseményének](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)összegyűjtéséhez engedélyeznie kell a [naplózási szűrési platform kapcsolatát](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol készlet/Subcategory: "a platform kapcsolatának szűrése"/Success: Engedélyezés)
>

A szűrési házirend kiválasztásához:
1. Az **adatgyűjtés** lapon válassza ki a szűrési házirendet a **biztonsági események**alatt.
2. Kattintson a **Mentés** gombra.

   ![Válassza ki a házirend szűrése][5]

### Automatikus kiépítés egy korábban létező ügynök telepítése esetén<a name="preexisting"></a> 

A következő használati esetek adja meg, hogy működik az esetre, ha már van egy ügynök vagy a bővítmény telepítése automatikus kiépítése. 

- Log Analytics ügynök telepítve van a gépen, de nem bővítményként (közvetlen ügynök)<br>
Ha a Log Analytics ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center telepíti a Log Analytics-ügynök bővítményt, és a Log Analytics-ügynököt a legújabb verzióra frissíti.
A telepített ügynök továbbra is jelentést készít a már konfigurált munkaterület (ek) ről, és a Security Center-ben konfigurált munkaterületnek is jelentést küld (a Windows rendszerű gépeken támogatott a többszörös vezérlési funkció).
Ha a konfigurált munkaterület egy felhasználói munkaterület (nem Security Center alapértelmezett munkaterülete), akkor telepítenie kell a "Security/" securityFree "megoldást arra a Security Centerra, hogy elindítsa a munkaterületre jelentett virtuális gépekről és számítógépekről származó események feldolgozását.<br>
<br>
Linux rendszerű gépek esetén az ügynök többsoros vezérlése még nem támogatott – ezért ha egy meglévő ügynök telepítését észleli, az automatikus kiépítés nem történik meg, és a gép konfigurációja nem módosul.
<br>
A 2019-03-17 előtti Security Center előfizetésekben lévő meglévő gépek esetében a rendszer nem telepíti a Log Analytics ügynök bővítményt, és a gép nem lesz hatással. Ezen gépek esetében tekintse meg a "figyelési ügynök állapotával kapcsolatos problémák megoldása a gépeken" című javaslatot az ügynök telepítési problémáinak megoldásához ezeken a gépeken.

  
- System Center Operations Manager ügynök telepítve van a gépen<br>
A Security Center a Log Analytics Agent bővítményt a meglévő Operations Manager telepíti. A meglévő Operations Manager ügynöke a szokásos módon folytatja a Operations Manager-kiszolgáló jelentését. Vegye figyelembe, hogy a Operations Manager ügynök és Log Analytics ügynök közös futásidejű kódtárakat használ, amelyeket a rendszer a folyamat során a legújabb verzióra frissít.
Megjegyzés: Ha a Operations Manager ügynök 2012-es verziója telepítve van **, ne kapcsolja** be az automatikus kiépítés beállítást.<br>

- Jelen egy már meglévő Virtuálisgép-bővítmény<br>
    - Ha a figyelési ügynök bővítményként van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy a jelentéskészítés csak egyetlen munkaterületre legyen elérhető. A Security Center nem bírálja felül a meglévő kapcsolatok felhasználó munkaterületeket. Security Center a virtuális gépről a már csatlakoztatott munkaterületen tárolja a biztonsági adatait, ha a "biztonság" vagy a "securityFree" megoldás telepítve van rajta. Security Center a folyamat legújabb verziójára frissítheti a bővítmény verzióját.  
    - Ha szeretné látni, hogy a meglévő bővítmény mely munkaterületre küld adatokat, futtassa a tesztet a [Azure Security Center kapcsolatának ellenőrzéséhez](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Másik lehetőségként megnyithatja Log Analytics munkaterületeket, kiválaszthat egy munkaterületet, kiválaszthatja a virtuális gépet, és megtekintheti a Log Analytics-ügynök közötti kapcsolatokat. 
    - Ha olyan környezettel rendelkezik, amelyben a Log Analytics ügynök telepítve van az ügyfél-munkaállomásokon, és jelentéskészítés egy meglévő Log Analytics munkaterületre, tekintse át az [Azure Security Center által támogatott operációs rendszerek](security-center-os-coverage.md) listáját, és győződjön meg arról, hogy az operációs rendszer támogatott. További információ: [meglévő log Analytics-ügyfelek](./faq-azure-monitor-logs.md).
 
### Automatikus kiépítés kikapcsolása<a name="offprovisioning"></a>
Kikapcsolhatja az Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával. 


1. Térjen vissza a Security Center főmenüjébe, és válassza ki a biztonsági szabályzatot.
2. Kattintson a **beállítások szerkesztése** elemre azon előfizetés sorában, amely esetében le szeretné tiltani az automatikus kiépítés beállítást.
3. A **biztonsági szabályzat – adatgyűjtési** panel **automatikus kiépítés** területén válassza ki a **ki**lehetőséget.
4. Kattintson a **Mentés** gombra.

   ![Automatikus kiépítés letiltása][6]

Automatikus kiépítés letiltása (kikapcsolva) esetén az alapértelmezett munkaterület-konfigurációs szakasz nem jelenik meg.

Ha kikapcsolhatja az automatikus üzembe helyezése után nem található:
-   Ügynökök nem építi ki a új virtuális gépeket.
-   A Security Center leállítja az adatok gyűjtését az alapértelmezett munkaterületre.
 
> [!NOTE]
>  Az automatikus kiépítés letiltása nem távolítja el a Log Analytics ügynököt azon Azure-beli virtuális gépekről, amelyeken az ügynök ki lett építve. További információ a OMS-bővítmény eltávolításáról: [Hogyan Security Center által telepített OMS-bővítmények eltávolítása](faq-data-collection-agents.md#remove-oms).
>
    
## Manuális ügynök kiépítés<a name="manual-agent"></a>
 
Több módon is telepítheti a Log Analytics-ügynököt manuálisan. Ha manuálisan telepíti, győződjön meg róla, az Automatikus kiépítés letiltása.

### <a name="operations-management-suite-vm-extension-deployment"></a>Az Operations Management Suite virtuális Géphez konzolbővítmény üzembe helyezése 

Manuálisan is telepítheti a Log Analytics-ügynököt, így Security Center a virtuális gépekről gyűjthet biztonsági adatokat, és javaslatokat és riasztásokat is biztosíthat.
1. Válassza ki a automatikus üzembe helyezése – kikapcsolva.
2. Hozzon létre egy munkaterületet, és állítsa be azt a munkaterülethez tartozó árképzési szintet, amelyet a Log Analytics-ügynököt kíván beállítani:

   a.  A Security Center főmenüjében válassza a **biztonsági szabályzat**elemet.
     
   b.  Válassza ki a munkaterületet, ahol csatlakoztassa az ügynököt kíván. Ellenőrizze, hogy a munkaterület ugyanabban az előfizetésben, használhatja a Security Centerben, és hogy van-e olvasási/írási engedéllyel a munkaterületen.
       ![munkaterület kiválasztása][8]
3. Állítsa a tarifacsomagot.
   ![válassza az árképzési szintet][9] 
   >[!NOTE]
   >Ha a munkaterülethez már engedélyezve van egy **biztonsági** vagy **SecurityCenterFree** megoldás, a díjszabás automatikusan be lesz állítva. 
   > 

4. Ha szeretné az ügynökök a Resource Manager-sablon használatával új virtuális gépek üzembe helyezése, telepítse az OMS virtuális gépi bővítmény:

   a.  [Telepítse a Windowshoz készült OMS virtuálisgép-bővítményt](../virtual-machines/extensions/oms-windows.md)
    
   b.  [A OMS virtuálisgép-bővítmény telepítése Linuxra](../virtual-machines/extensions/oms-linux.md)
5. A bővítmények meglévő virtuális gépeken való üzembe helyezéséhez kövesse az [adatok gyűjtése az Azure Virtual Machinesról](../azure-monitor/learn/quick-collect-azurevm.md)című témakör utasításait.

   > [!NOTE]
   > Az **esemény-és teljesítményadatokat gyűjtő** szakasz nem kötelező.
   >
6. A bővítmény telepítése a PowerShell használatával, használja a következő PowerShell-példa:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Lépjen a **log Analyticsra** , és kattintson a **Speciális beállítások**elemre.
    
      ![A log analytics beállítása][11]

   2. Másolja ki a **munkaterület azonosítója** és az **elsődleges kulcs**értékét.
  
      ![Értékek másolása][12]

   3. Töltse fel a nyilvános config és a privát config ezekkel az értékekkel:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Amikor telepíti egy Windows virtuális gépen:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Amikor telepíti egy Linux rendszerű virtuális gépen:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> A Security Center PowerShell használatával történő előkészítésével kapcsolatos útmutatásért lásd: [a Azure Security Center bevezetésének automatizálása a PowerShell használatával](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Hibakeresés

-   Az automatikus telepítési problémák azonosításához lásd: az [ügynök állapotával kapcsolatos problémák elhárítása](security-center-troubleshooting-guide.md#mon-agent).

-  A figyelési ügynök hálózati követelményeinek azonosításához lásd: a [figyelési ügynök hálózati követelményeinek hibaelhárítása](security-center-troubleshooting-guide.md#mon-network-req).
-   A manuális előkészítési problémák azonosításához lásd: [az Operations Management Suite bevezetési problémáinak elhárítása](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- A nem figyelt virtuális gépek és számítógépek problémáinak azonosítása:

    A virtuális gép vagy a számítógép nem figyelt a Security Center által, ha a gép nem fut a Microsoft Monitoring Agent bővítményt. Előfordulhat, hogy egy gépen már telepítve van egy helyi ügynök, például a OMS Direct ügynök vagy a System Center Operations Manager ügynök. Ilyen ügynökökkel rendelkező gépek eszközként van azonosítva nem figyelt, mert ezek az ügynökök teljes mértékben nem támogatottak a Security Centerben. A Security Center összes funkciójának legteljesebb kihasználása érdekében szükség van a Microsoft Monitoring Agent bővítményre.

    Ha további információra van szükség arról, hogy Security Center miért nem tudja sikeresen figyelni a virtuális gépeket és a számítógépeket az automatikus kiépítés során, tekintse meg a [figyelési ügynök állapotával kapcsolatos problémákat](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Következő lépések
Ez a cikk bemutatta, hogyan gyűjti az adatokat, és a Security Center működik az automatikus kiépítést. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center: GYIK](faq-general.md) – Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
