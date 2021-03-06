---
title: Erőforrások rendszerezése felügyeleti csoportokkal – Azure-irányítás
description: Megismerheti a felügyeleti csoportokat és azok használatának módját, valamint a hozzájuk tartozó engedélyek működését.
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.date: 12/18/2019
ms.topic: overview
ms.openlocfilehash: 319f48d4d0f8ce8501fecb74282760340b597188
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240939"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Erőforrások rendszerezése az Azure Management Groups segítségével

Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási feltételeket alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket. A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától. A felügyeleti csoporton belüli összes előfizetésnek ugyanazzal az Azure Active Directory-bérlővel kell megbízhatósági kapcsolatban állnia.

Alkalmazhat például olyan szabályzatokat egy felügyeleti csoportra, amelyek korlátozzák a virtuális gépek (VM-ek) létrehozásához elérhető régiókat. A szabályzat minden felügyeleti csoportra, előfizetésre és erőforrásra érvényes lesz a felügyeleti csoporton belül, ha csak az adott régióban engedélyezi virtuális gépek létrehozását.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>A felügyeleti csoportok és előfizetések hierarchiája

A felügyeleti csoportok és előfizetések rugalmas szerkezetének létrehozásával hierarchiába rendezheti erőforrásait az egységes szabályzat- és hozzáféréskezeléshez. Az alábbi ábrán egy példa látható szabályozási hierarchia létrehozására felügyeleti csoportok használatával.

![Példa egy felügyeleti csoport hierarchiafájára](./media/tree.png)

Létrehozhat egy hierarchiát, amelyre szabályzatot alkalmazhat, például a virtuális gépek helyének az USA nyugati régiójára való korlátozását a „Production” csoporton. Ez a szabályzat minden olyan Nagyvállalati Szerződés (EA) előfizetésre vonatkozik, amely az adott felügyeleti csoport leszármazottait képezi, és az előfizetések alá tartozó összes virtuális gépre érvényes lesz. Ezt a biztonsági szabályzatot az erőforrás vagy az előfizetés tulajdonosa nem módosíthatja, ez pedig hatékonyabb kontrollt biztosít.

A felügyeleti csoportok használatának másik esete, amikor egyszerre több előfizetés számára szeretne felhasználói hozzáférést biztosítani. Ha több előfizetést helyez a felügyeleti csoport alá, mindössze egy [szerepköralapú hozzáférés-vezérlési](../../role-based-access-control/overview.md) (RBAC) hozzárendelést kell létrehoznia a felügyeleti csoporthoz, amelytől az összes előfizetés örökli a hozzáférést.
Ahelyett, hogy különböző előfizetésekre szkriptelne RBAC-hozzárendeléseket, a felügyeleti csoporton egyetlen hozzárendeléssel biztosíthatja a szükséges hozzáférést a felhasználóknak.

### <a name="important-facts-about-management-groups"></a>A felügyeleti csoportok fontosabb jellemzői

- A címtárak legfeljebb 10 000 felügyeleti csoportot támogatnak.
- A felügyeleticsoport-fák legfeljebb hatszintűek lehetnek.
  - A korlátozásba nem tartozik bele a gyökérszint és az előfizetés szintje.
- Felügyeleti csoportonként vagy előfizetésenként egy szülő támogatott.
- Az egyes felügyeleti csoportok alá számos gyermek tartozhat.
- Az egyes címtárakban minden előfizetés és felügyeleti csoport egyetlen hierarchiában található. Tekintse meg [A gyökérszintű felügyeleti csoport fontosabb jellemzői](#important-facts-about-the-root-management-group) című szakaszt.

## <a name="root-management-group-for-each-directory"></a>Az egyes címtárak gyökérszintű felügyeleti csoportja

Minden címtárhoz tartozik egy legfelső szintű, vagy más néven gyökérszintű felügyeleti csoport.
Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a gyökérszintű felügyeleti csoport lehetővé teszi globális szabályzatok és RBAC-hozzárendelések címtárszintű alkalmazását. Az [Azure AD globális rendszergazdájának először emelnie kell a jogosultsági szintjét](../../role-based-access-control/elevate-access-global-admin.md), hogy a Felhasználói hozzáférés adminisztrátora szerepkörrel rendelkezzen a gyökérszintű csoport esetében. A jogosultsági szint emelését követően a rendszergazda bármilyen RBAC-szerepkört hozzárendelhet a címtár felhasználóihoz vagy csoportjaihoz a hierarchia kezelése érdekében. Rendszergazdaként hozzárendelheti saját fiókját a gyökérszintű felügyeleti csoport tulajdonosaként.

### <a name="important-facts-about-the-root-management-group"></a>A gyökérszintű felügyeleti csoport fontosabb jellemzői

- A gyökérszintű felügyeleti csoport megjelenített neve alapértelmezés szerint **Bérlői gyökércsoport** lesz. Az azonosító az Azure Active Directory-azonosító lesz.
- A megjelenített név módosításához a fiókjának a Tulajdonos vagy Közreműködő szerepkörrel kell rendelkeznie a gyökérszintű felügyeleti csoportra vonatkozóan. A felügyeleti csoport nevének frissítéséhez tekintse meg a [felügyeleti csoport nevének módosítása](manage.md#change-the-name-of-a-management-group) című témakört.
- A gyökérszintű felügyeleti csoportot a többi felügyeleti csoporttal szemben nem lehet törölni vagy áthelyezni.  
- A címtár összes előfizetése és felügyeleti csoportja a gyökérszintű felügyeleti csoport alá kerül.
  - A globális felügyelet érdekében a címtár erőforrásai is a gyökérszintű felügyeleti csoport alá kerülnek.
  - Az újonnan létrehozott előfizetések alapértelmezés szerint a gyökérszintű felügyeleti csoporthoz tartoznak.
- A gyökérszintű felügyeleti csoport az összes Azure-ügyfél számára látható, de nem mindegyikük rendelkezik hozzáféréssel a kezeléséhez.
  - Bárki, aki hozzáféréssel rendelkezik egy adott előfizetéshez, láthatja, hogy az hol helyezkedik el a hierarchiában.  
  - Senki nem kap alapértelmezés szerint hozzáférést a gyökérszintű felügyeleti csoporthoz. Kizárólag az Azure AD globális rendszergazdái emelhetik meg jogosultsági szintjüket, hogy hozzáférést kapjanak.  Ha már rendelkeznek hozzáféréssel a gyökérfelügyeleti csoporthoz, bármilyen RBAC-szerepkört hozzárendelhetnek a címtár felhasználóihoz.  

> [!IMPORTANT]
> A gyökérszintű felügyeleti csoporton végrehajtott felhasználóihozzáférés- és szabályzat-hozzárendelések **a címtárban lévő valamennyi erőforrásra érvényesek lesznek**.
> Ezért minden ügyfélnek fel kell mérnie, mire van szüksége ebben a hatókörben.
> A felhasználói hozzáférések és a szabályzatok hozzárendelései csak ebben a hatókörben lehetnek kötelezőek.  

## <a name="initial-setup-of-management-groups"></a>A felügyeleti csoportok kezdeti beállítása

A felügyeleti csoportok használatának megkezdésekor először egy beállítási folyamat történik. A folyamat első lépéseként létrejön a gyökérszintű felügyeleti csoport a címtárban. A csoport létrehozása után a címtárban található összes meglévő előfizetés a gyökérszintű felügyeleti csoport gyermekeként lesz beállítva. A folyamat célja, hogy egy adott címtáron belül csak egy felügyeleticsoport-hierarchia legyen. Az egyetlen hierarchia beállítása lehetővé teszi a rendszergazdai ügyfelek számára globális hozzáférések és szabályzatok alkalmazását, amelyeket a címtárat használó többi ügyfél nem tud megkerülni. Minden gyökérszintű hozzárendelés érvényes lesz a teljes hierarchiára, amely tartalmazza az összes felügyeleti csoportot, előfizetést, erőforráscsoportot és az Azure AD-bérlőn belüli erőforrásokat.

## <a name="trouble-seeing-all-subscriptions"></a>Nem látható az összes előfizetés

Néhány címtárban, amelyek az előzetes verzió korai szakaszában (2018. június 25. előtt) kezdtek felügyeleti csoportokat használni, egy probléma jelentkezhet, amelynek következtében a hierarchiában nem található meg minden előfizetés. Az előfizetéseket a hierarchiába helyező eljárás azután lett megvalósítva, hogy egy szerepkör- vagy szabályzat-hozzárendelés végre lett hajtva a címtár gyökérszintű felügyeleti csoportján. 

### <a name="how-to-resolve-the-issue"></a>A probléma elhárítása

A probléma megoldására két lehetősége van.

1. Az összes szerepkör- és szabályzat-hozzárendelés eltávolítása a gyökérszintű felügyeleti csoportról
   1. Ha az összes szerepkör- és szabályzat-hozzárendelést eltávolítja a gyökérszintű felügyeleti csoportról, a szolgáltatás a következő éjszakai ciklus során visszatölti az összes előfizetést a hierarchiába.  Ennek a folyamatnak az a célja, hogy a hozzáférések vagy szabályzat-hozzárendelések kiosztása nehogy véletlenül érvényes legyen az összes bérlői előfizetésre.
   1. A legjobb mód a folyamat a szolgáltatások működésének befolyásolása nélküli végrehajtására, ha a szerepkör- vagy szabályzat-hozzárendeléseket egy szinttel a gyökérszintű felügyeleti csoport alatt alkalmazza. Ezután eltávolíthatja az összes hozzárendelést a gyökérszintű hatókörből.
1. A visszatöltési folyamat elindítása az API közvetlen meghívásával
   1. A címtár bármelyik ügyfele meghívhatja a *TenantBackfillStatusRequest* vagy a *StartTenantBackfillRequest* API-t. A StartTenantBackfillRequest API a meghívásakor elindítja az összes előfizetés a hierarchiába való átvitelére vonatkozó kezdeti konfigurációs folyamatot. A folyamat azt az eljárást is elindítja, amely az összes új előfizetést a gyökérszintű felügyeleti csoport gyermekeként érvényesíti. Ennek az eljárásnak a végrehajtásakor nem szükséges a gyökérszinten módosítani a hozzárendeléseket. Az API meghívásával jóváhagyja, hogy a gyökérszinten jelen lévő szabályzatok és hozzáférési hozzárendelések az összes előfizetésre alkalmazhatók.

Ha kérdése van a visszatöltési folyamatot illetően, lépjen kapcsolatba velünk a következő e-mail-címen: managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Hozzáférés a felügyeleti csoportokhoz

Az Azure felügyeleti csoportjai támogatják az [Azure szerepkör-alapú hozzáférés-vezérlést (RBAC)](../../role-based-access-control/overview.md) minden erőforrás-hozzáféréshez és szerepkör-definícióhoz.
Ezeket az engedélyeket a hierarchiában található összes gyermek erőforrás örökli. Bármely RBAC-szerepkör hozzárendelhető egy olyan felügyeleti csoporthoz, amely örökölni fogja a hierarchiát az erőforrásokhoz.
A virtuálisgép-közreműködői RBAC-szerepkör például hozzárendelhető a felügyeleti csoporthoz. Ez a szerepkör nem végez műveletet a felügyeleti csoporton, de a csoport alá tartozó összes virtuális gép örökli.

Az alábbi ábrán a felügyeleti csoportokkal kapcsolatos szerepkörök és támogatott műveletek listája látható.

| RBAC-szerepkör neve             | Létrehozás | Átnevez | Áthelyezés** | Törlés | Hozzáférés hozzárendelése | Szabályzat hozzárendelése | Olvasás  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Tulajdonos                       | X      | X      | X      | X      | X             | X             | X     |
|Közreműködő                 | X      | X      | X      | X      |               |               | X     |
|Felügyeleti csoport közreműködője*             | X      | X      | X      | X      |               |               | X     |
|Olvasó                      |        |        |        |        |               |               | X     |
|Felügyeleti csoport olvasója*                  |        |        |        |        |               |               | X     |
|Erőforrás-szabályzat közreműködője |        |        |        |        |               | X             |       |
|Felhasználói hozzáférés rendszergazdája   |        |        |        |        | X             | X             |       |

*: a Felügyeleti csoport közreműködője és a Felügyeleti csoport olvasója szerepkör kizárólag a felügyeleti csoport hatókörén belül engedélyezi az adott művelet végrehajtását a felhasználók számára.  
\* *: A gyökérszintű felügyeleti csoportban lévő szerepkör-hozzárendelések nem szükségesek ahhoz, hogy egy előfizetést vagy felügyeleti csoportot át lehessen helyezni onnan.  A hierarchián belüli elemek áthelyezésének részleteiért tekintse meg az [Erőforrások kezelése felügyeleti csoportokkal](manage.md) című szakaszt.

## <a name="custom-rbac-role-definition-and-assignment"></a>Egyéni RBAC szerepkör-definíció és hozzárendelés

A felügyeleti csoportok egyéni RBAC szerepkör-támogatása jelenleg előzetes verzióban érhető el bizonyos [korlátozásokkal](#limitations).  A felügyeleti csoportok hatóköre a szerepkör-definíció hozzárendelhető hatókörében határozható meg.  Az egyéni RBAC-szerepkör hozzárendelhető lesz az adott felügyeleti csoporthoz és az alá tartozó összes felügyeleti csoporthoz, előfizetéshez, erőforráscsoporthoz vagy erőforráshoz. Az egyéni szerepkör ugyanúgy öröklődik lefelé a hierarchián belül, mint a beépített szerepkörök.    

### <a name="example-definition"></a>Példa definíció
Az [Egyéni szerepkör definiálása és létrehozása](../../role-based-access-control/custom-roles.md) nem változik a felügyeleti csoportok belefoglalásával. A felügyeleti csoport **/providers/Microsoft.Management/managementgroups/{GroupID}** megadásához használja a teljes elérési utat. 

Használja a felügyeleti csoport AZONOSÍTÓját, és ne a felügyeleti csoport megjelenítendő nevét. Ez a gyakori hiba azért fordul elő, mert mindkettő egyéni definiált mező a felügyeleti csoport létrehozásakor. 

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>A szerepkör-definíció és a hozzárendelési hierarchia elérési útjának megszakításával kapcsolatos problémák
A szerepkör-definíciók a felügyeleti csoport hierarchiáján belül bárhol hozzárendelhetők. A fölérendelt felügyeleti csoportban definiálható egy szerepkör-definíció, miközben a tényleges szerepkör-hozzárendelés létezik a gyermek előfizetésen. Mivel a két elem között fennáll a kapcsolat, hibaüzenet jelenik meg, amikor a hozzárendelést a definíciója alapján választja el. 

Példa: nézzük meg a hierarchia egy kis szakaszát a vizualizációhoz. 

![részfa](./media/subtree.png)

Tegyük fel, hogy egy egyéni szerepkör van definiálva a marketing felügyeleti csoportban. Ezt az egyéni szerepkört ezután a két ingyenes próbaverziós előfizetéshez rendeli hozzá.  

Ha az egyik előfizetést úgy próbálja áthelyezni, hogy az éles felügyeleti csoport gyermeke legyen, akkor ez az áthelyezés az előfizetés szerepkör-hozzárendelésének elérési útját megtöri a marketing-felügyeleti csoport szerepkör-definíciójában. Ebben az esetben hibaüzenetet kap, amely azt jelzi, hogy az áthelyezés nem engedélyezett, mert megszakítja ezt a kapcsolatot.  

A forgatókönyv kijavításához néhány különböző lehetőség áll rendelkezésre:
- Távolítsa el a szerepkör-hozzárendelést az előfizetésből, mielőtt áthelyezi az előfizetést egy új szülőre MG-ra.
- Adja hozzá az előfizetést a szerepkör-definícióhoz hozzárendelhető hatókörhöz.
- Módosítsa a hozzárendelhető hatókört a szerepkör-definíción belül. A fenti példában frissítheti a hozzárendelhető hatóköröket a marketingből a gyökérszintű felügyeleti csoportba, hogy a definíciót a hierarchia mindkét ága elérheti.   
- Hozzon létre egy további egyéni szerepkört, amely a másik ágban lesz meghatározva.  Ehhez az új szerepkörhöz szükség lesz a szerepkör-hozzárendelés módosítására az előfizetésben is.  

### <a name="limitations"></a>Korlátozások  
A felügyeleti csoportok egyéni szerepköreinek használatakor korlátozások vannak érvényben. 

 - Egy új szerepkör hozzárendelhető hatókörében csak egy felügyeleti csoportot lehet definiálni.  Ez a korlátozás azért van érvényben, hogy csökkentse a helyzetek számát, amikor a szerepkör-definíciók és a szerepkör-hozzárendelések le vannak választva.  Ez akkor fordulhat elő, ha egy szerepkör-hozzárendeléssel rendelkező előfizetést vagy felügyeleti csoportot egy másik, szerepkör-definícióval nem rendelkező szülőbe helyez át a rendszer.   
 - A RBAC adatsíkok műveletei nem definiálhatók a felügyeleti csoport egyéni szerepköreiben.  Ez a korlátozás azért van érvényben, mert késési probléma van az adatközpont-erőforrás-szolgáltatókat frissítő RBAC-műveletekkel. Ez a késési probléma jelenleg használatban van, és ezek a műveletek le lesznek tiltva a szerepkör-definícióból a kockázatok csökkentése érdekében.
 - A Azure Resource Manager nem ellenőrzi a felügyeleti csoport létezését a szerepkör-definíció hozzárendelhető hatókörében.  Ha a rendszer elküld egy elírást vagy helytelen felügyeleti csoport AZONOSÍTÓját, akkor a szerepkör-definíció továbbra is létrejön.   

## <a name="moving-management-groups-and-subscriptions"></a>Felügyeleti csoportok és előfizetések áthelyezése 

Egy felügyeleti csoportba vagy előfizetésbe egy másik felügyeleti csoport gyermekének kell lennie. a három szabályt igaz értékre kell kiértékelni.

Ha az áthelyezés műveletet végzi, a következőkre lesz szüksége: 

-  Felügyeleti csoport írási és szerepkör-hozzárendelési írási engedélyei az alárendelt előfizetéshez vagy a felügyeleti csoporthoz.
   - Beépített szerepkör – példa **tulajdonosa**
- Felügyeleti csoport írási hozzáférése a cél szülő felügyeleti csoportnál.
   - Beépített szerepkör – példa: **tulajdonos**, **közreműködő**, **felügyeleti csoport közreműködője**
- Felügyeleti csoport írási hozzáférése a meglévő szülő felügyeleti csoporton.
   - Beépített szerepkör – példa: **tulajdonos**, **közreműködő**, **felügyeleti csoport közreműködője**

**Kivétel**: Ha a cél vagy a meglévő szülő felügyeleti csoport a gyökérszintű felügyeleti csoport, az engedélyek követelményei nem érvényesek. Mivel a legfelső szintű felügyeleti csoport az összes új felügyeleti csoport és előfizetés alapértelmezett kihelyezett helye, nincs szükség arra, hogy az adott elem áthelyezéséhez szükséges engedélyekkel rendelkezik.

Ha az előfizetés tulajdonosi szerepköre az aktuális felügyeleti csoporttól örökölt, az áthelyezési célok korlátozottak. Az előfizetést csak egy másik felügyeleti csoportba helyezheti át, ahol a tulajdonosi szerepköre van. Nem helyezhető át olyan felügyeleti csoportba, ahol Ön közreműködő, mert elveszti az előfizetés tulajdonjogát. Ha közvetlenül az előfizetés tulajdonosi szerepköréhez van rendelve (nem a felügyeleti csoporttól örökölt), akkor áthelyezheti bármely olyan felügyeleti csoportba, ahol Ön közreműködő. 

## <a name="audit-management-groups-using-activity-logs"></a>Felügyeleti csoportok naplózása tevékenységnaplókkal

A felügyeleti csoportok támogatottak az [Azure-tevékenységnaplóban](../../azure-monitor/platform/platform-logs-overview.md). A felügyeleti csoportokkal kapcsolatos minden eseményre ugyanarról a központi helyről kereshet rá, mint más Azure-erőforrások esetében.  Például megtekintheti egy adott felügyeleti csoporthoz tartozó összes szerepkör-hozzárendelés vagy szabályzat-hozzárendelés módosításait.

![Tevékenységnaplók felügyeleti csoportokkal](media/al-mg.png)

Az Azure Portalon kívüli felügyeleti csoportok lekérdezésekor a felügyeleti csoportok célhatóköre a következőhöz hasonlóan néz ki: **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](/powershell/module/az.resources#resources)
- [Felügyeleti csoportok áttekintése a REST API-ban](/rest/api/resources/managementgroups)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](/cli/azure/account/management-group)
