Technische Verantwoording VvE Verduurzamings Calculator (Versie 2025.1)
Datum: 26 november 2025
Status: Definitief Rekenmodel
Doel: Transparantie bieden in de gehanteerde formules, kengetallen en aannames van de VvE rekentool.
1. Inleiding
Deze rekentool is ontwikkeld om VvE-leden en besturen een eerste indicatie te geven van de financiële haalbaarheid van verduurzaming. Het model combineert bouwkundige kosten, subsidies, financieringslasten en energiebesparingen tot één netto maandlast per appartementsrecht.
Dit document beschrijft exact hoe de berekeningen tot stand komen.
2. Invoer en Aannames Oppervlaktes
Indien de gebruiker zelf geen vierkante meters invoert, maakt de tool een automatische schatting op basis van het aantal appartementen. Deze kengetallen zijn gebaseerd op een gemiddeld portiek- of galerijflat-appartement.
Bouwdeel
Automatische Schatting (per woning)
Toelichting
Glas
12 $m^2$
Gemiddeld totaal raamoppervlak (voor- en achtergevel).
Dak
25 $m^2$
Aandeel in het gemeenschappelijke dak (bij hoogbouw is dit relatief klein per woning).
Gevel
35 $m^2$
Gesloten geveldelen (excl. ramen).

Let op: De gebruiker kan en moet deze waarden overschrijven voor een nauwkeurige berekening.
3. Kostenopbouw (Bruto Investering)
De totale bruto investering wordt als volgt opgebouwd:
$$\text{Totaal} = (\text{Bouwkosten} \times 1,05) + \text{Advieskosten}$$
A. Bouwkosten (Materialen & Arbeid)
De tool hanteert vaste vierkantemeterprijzen (prijspeil schatting 2025). Deze prijzen zijn exclusief BTW in de database opgeslagen, maar worden in de berekening direct verhoogd met 21% BTW.
Maatregel
Prijsindicatie (excl. BTW)
Prijs incl. 21% BTW
Bron/Schatting
HR++ Glas
€ 140 / $m^2$
€ 169,40 / $m^2$
Vervanging glas in bestaand kozijn.
Triple Glas
€ 850 / $m^2$
€ 1.028,50 / $m^2$
Inclusief vervanging kozijnen + ventilatieroosters.
Dakisolatie
€ 65 / $m^2$
€ 78,65 / $m^2$
Isolatie op bestaande dakbedekking (omgekeerd dak) of binnenzijde.
Spouwmuur
€ 23 / $m^2$
€ 27,83 / $m^2$
Na-isolatie gevulde spouw.
Buitengevel
€ 175 / $m^2$
€ 211,75 / $m^2$
Isolatie aan buitenzijde met afwerking (stuc/strip).

B. Onvoorzien & Opslag
Over de som van de bouwkosten wordt een opslag van 5% berekend voor onvoorziene kosten (meerwerk). In eerdere versies was dit hoger, maar omdat advieskosten nu apart worden berekend, is dit percentage verlaagd naar een standaard risico-opslag.
C. Advies & Organisatie
De kosten voor het DMJOP en de procesbegeleiding worden door de gebruiker ingevoerd (exclusief BTW). De tool telt hier 21% BTW bij op.
4. Subsidiesystematiek
Het model berekent twee stromen subsidies die bij elkaar worden opgeteld.
A. Landelijke Subsidie (SVVE)
De Subsidieregeling Verduurzaming voor Verenigingen van Eigenaars (SVVE) wordt berekend per vierkante meter. Er wordt aangenomen dat aan de isolatiewaarden (Rd-waardes) wordt voldaan.
HR++ Glas: € 50,00 per $m^2$
Triple Glas: € 222,00 per $m^2$ (incl. kozijn)
Dakisolatie: € 32,50 per $m^2$
Spouwmuur: € 5,85 per $m^2$
Buitengevel: € 40,50 per $m^2$
Noot: Het rekenmodel past geen "stapelbonus" correctie toe; de genoemde bedragen zijn de basistarieven zoals gehanteerd voor VvE's die integraal verduurzamen.
B. Gemeentelijke Subsidie (Lokale regeling)
Deze module simuleert een specifieke lokale subsidie (bijv. gemeente Haarlem) gericht op woningen met een lagere WOZ-waarde.
Bedrag: € 1.000 (vast bedrag per in aanmerking komend appartement).
Voorwaarden in model:
Bouwjaar pand < 1995 (Input check).
Er zijn minstens 2 'slechte' bouwdelen aanwezig in het pand (Input check).
Er wordt minimaal 1 maatregel daadwerkelijk uitgevoerd (Check op selectie).
Geldt alleen voor het aantal appartementen met WOZ < € 486.000 (peil 2022).
5. Financiering (Nationaal Warmtefonds)
De "Netto Financieringsbehoefte" wordt als volgt bepaald:

$$\text{Lening} = \text{Bruto Investering} - \text{Totaal Subsidies} - \text{Eigen Geld (MJOP)}$$
Indien het resultaat positief is, wordt een VvE Energiebespaarlening gesimuleerd op basis van een annuïteitenhypotheek.
Rente: 3,69% (Peildatum november 2025, indicatief voor looptijd 20 jaar).
Looptijd: 20 jaar (240 maanden).
Formule:
$$\text{Maandlast} = P \times \frac{r(1+r)^n}{(1+r)^n - 1}$$

Waarbij $P$ = hoofdsom, $r$ = maandrente (3,69%/12), $n$ = aantal maanden (240).
6. Energiebesparing (Indicatief)
De besparing wordt berekend via een theoretische benadering van bespaarde kuubs aardgas per vierkante meter oppervlakte.
Formule:

$$\text{Besparing (€)} = \text{Oppervlakte ($m^2$)} \times \text{Besparingskengetal} \times \text{Gasprijs}$$
Gehanteerde parameters:
Gasprijs: € 1,35 per $m^3$ (Conservatief gemiddelde inclusief belastingen).
Besparingskengetallen ($m^3$ gas per $m^2$ oppervlak):
HR++ Glas: 15 $m^3/m^2$ (Verschil enkel glas -> HR++)
Triple Glas: 35 $m^3/m^2$ (Verschil enkel glas -> Triple + kierdichting)
Dakisolatie: 10 $m^3/m^2$ (Van ongeïsoleerd naar Rc 3.5)
Spouwmuur: 8 $m^3/m^2$
Buitengevel: 25 $m^3/m^2$
Disclaimer Energie:
Dit is de meest onzekere factor in het model. De werkelijke besparing is sterk afhankelijk van het stookgedrag van de bewoners ("rebound effect": mensen gaan comfortabeler stoken als het huis warmer blijft) en de ligging van het appartement (hoekwoning vs. tussenwoning).
7. Samenvatting Netto Effect
Het eindcijfer "Netto effect servicekosten" is een simpele aftreksom:
$$\text{Extra bijdrage lening} - \text{Geschatte energiebesparing} = \text{Netto Effect}$$
Positief getal (+): De kosten zijn hoger dan de baten; de woonlasten stijgen.
Negatief getal (-): De besparing is hoger dan de kosten; de woonlasten dalen.
