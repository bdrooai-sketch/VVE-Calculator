VvE Verduurzamings Calculator 2025 🏠♻️

Een interactieve online rekentool voor Verenigingen van Eigenaren (VvE's) om inzicht te krijgen in de kosten, subsidies en terugverdientijden van verduurzamingsmaatregelen.

🔗 Bekijk de tool hier live
(Vergeet niet deze link aan te passen nadat je GitHub Pages hebt geactiveerd!)

📋 Over dit project

Deze tool is ontwikkeld om VvE-besturen en leden te helpen bij de complexe financiële puzzel van verduurzaming. De calculator combineert landelijke subsidies (SVVE) met specifieke gemeentelijke voorwaarden en geeft direct inzicht in het effect op de maandelijkse servicekosten.

De tool is gebouwd als een Single File Application. Dit betekent dat alle logica, berekeningen en opmaak in één overzichtelijk bestand (index.html) zitten, wat het makkelijk maakt om te delen en te beheren zonder complexe server-installaties.

✨ Functionaliteiten

Automatische Schattingen: Berekent automatisch glas-, dak- en geveloppervlaktes op basis van het aantal appartementen.

Kostenoverzicht: Inclusief bouwkosten, onvoorzien (5%), procesbegeleiding en DMJOP-kosten.

Subsidieberekening:

🏛️ Landelijke SVVE: Automatische berekening per maatregel.

🏙️ Gemeentelijke Subsidie: Checkt voorwaarden (bouwjaar <1995, WOZ-waarde, minstens 1 maatregel & 2 slechte bouwdelen).

Woonlasten: Berekent het netto effect per maand (Leninglasten minus Energiebesparing).

Export: Mogelijkheid om alle scenario's direct naar Excel (.csv) te downloaden.

⚙️ Aanpassingen maken (Voor Beheerders)

Wil je de gasprijs, rente of subsidiebedragen aanpassen voor 2026? Dat kan eenvoudig in het bestand index.html.

Open index.html in een teksteditor (zoals Kladblok of VS Code).

Zoek naar het gedeelte // --- Constants.

Hier kun je waarden aanpassen, bijvoorbeeld:

GAS_PRICE: Huidige gasprijs.

INTEREST_RATE: Actuele rente van het Warmtefonds.

DATA: De kosten per m² en besparingen per maatregel.

🚀 Installatie

Er is geen installatie nodig. Omdat het een statisch HTML-bestand is met ingebouwde React (via CDN), werkt het direct in elke moderne browser.

Download index.html.

Open het bestand in Chrome, Edge of Safari.

Of host het gratis via GitHub Pages (instellingen > Pages > Source: main).

⚠️ Disclaimer

Deze rekentool is uitsluitend bedoeld ter indicatie en inspiratie. Aan de uitkomsten kunnen geen rechten worden ontleend. Prijzen zijn gebaseerd op gemiddelde markttarieven en subsidies op de regelingen geldig in 2025. Raadpleeg voor investeringsbeslissingen altijd een financieel adviseur en de officiële voorwaarden van het Warmtefonds en RVO.

📚 Bronnen

De berekeningen zijn gebaseerd op data van:

DVvE - Financiele Middelen

NPLW - Speciale Doelgroepen

Milieu Centraal
