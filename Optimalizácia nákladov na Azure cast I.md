Optimalizácia nákladov na Azure: Komplexný sprievodca
Úvod
V snahe o efektívnosť cloudu je kľúčové efektívne spravovanie zdrojov. Tento článok poskytuje podrobný sprievodca odporúčaniami na šetrenie nákladov prostredníctvom identifikácie a vypnutia nepoužívaných zdrojov v Azure.

Krok 1: Identifikácia nepoužívaných zdrojov
Azure Advisor je vaším nástrojom pre identifikáciu nečinných virtuálnych strojov (VM) a ďalších nedostatočne využívaných zdrojov. Postupujte podľa týchto krokov, aby ste využili jeho možnosti:

Prístup k Azure Advisor
1. 
Prihlásenie: Prejdite naAzure Portala prihláste sa svojimi prihlasovacími údajmi.
2. 
Nájdenie Advisor: Pomocou vyhľadávacej funkcie portálu lokalizujte 'Advisor'.
3. 
Záložka Náklady: Na paneli Advisor kliknite na záložku 'Náklady', aby ste videli finančné rady.
4. 
Analýza odporúčaní: Azure Advisor využíva strojové učenie na navrhovanie optimalizácií pre vaše zdroje.
5. 
Konanie podľa návrhov: Identifikuje zdroje, ktoré boli neaktívne viac ako týždeň, a odporúča ich vypnutie na základe metrík CPU a sieťovej prevádzky.

Krok 2: Automatické vypnutie VM
Aby ste predišli plytvaniu, možno nakonfigurovať funkciu automatického vypnutia Azure, ktorá môže vypnúť VM počas nepracovných hodín. Pôvodne bola táto funkcia exkluzívna pre DevTest Labs, teraz je všeobecne dostupná pre všetky ARM-based Azure VM.

Konfigurácia automatického vypnutia s Azure CLI
Tu je príklad skriptu na nastavenie automatického vypnutia:

# Definícia názvu skupiny zdrojov a VM
RESOURCE_GROUP_NAME="myResourceGroup"
VM_NAME="myVM"

# Nastavenie požadovaného času vypnutia v UTC (HH:MM)
SHUTDOWN_TIME="20:00"

# Použitie konfigurácie automatického vypnutia
az vm auto-shutdown --resource-group $RESOURCE_GROUP_NAME --name $VM_NAME --time $SHUTDOWN_TIME

Prispôsobte SHUTDOWN_TIME tak, aby zodpovedal ekvivalentu UTC vo vašom miestnom časovom pásme.

Krok 3: Odstránenie osamotených zdrojov
Osamotené zdroje, ako sú nepripojené disky a nepoužité sieťové rozhrania, môžu ticho vyčerpávať váš rozpočet.

Identifikácia osamotených zdrojov
Využite Azure Resource Graph s Kusto Query Language (KQL) na lokalizáciu týchto zdrojov. Napríklad na nájdenie nepripojených diskov:

Resources
| where type has "microsoft.compute/disks"
| extend diskState = tostring(properties.diskState)
| where managedBy == "" or diskState == 'Unattached'
| project id, diskState, resourceGroup, location, subscriptionId

Odstránenie osamotených zdrojov
Pred pokračovaním v odstraňovaní ich cez Azure portal alebo pomocou príkazov Azure CLI/PowerShell potvrďte stav osamotenosti.

Automatizácia a údržba
Zvážte automatizáciu procesu čistenia s Azure Automation alebo Azure Logic Apps. Pravidelné monitorovanie je kľúčové na predchádzanie zbytočným výdavkom.

Záver
Vždy dôkladne skontrolujte potrebu zdrojov pred ich odstránením, aby ste predišli nezvratným akciám. Dodržiavaním týchto krokov môžete udržať nákladovo efektívne a upratané prostredie Azure.