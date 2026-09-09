# Chapitre 6 -- Limites et perimetre de ce parcours

Ce parcours couvre la presentation generale et le statut de depreciation du
depot, le pattern des quatre decorateurs d extension viem, le flux complet
de depot L1 vers L2 (avec le detail du calcul de `sourceHash` et de
l encodage RLP de la transaction de depot), le flux complet de retrait L2
vers L1 en trois etapes (initier, prouver, finaliser), et les adresses de
contrats predeployees L2 face aux deploiements L1 specifiques a chaque
chaine.

Sont volontairement laisses hors champ : le detail exhaustif de chaque
fichier `actions/**/*.ts` (plus de quarante fichiers d action au total,
chacun avec ses propres types de parametres generiques sur `TChain` et
`TChainOverride`) au-dela des exemples cites aux chapitres 3 et 4 ; les
fichiers de benchmark (`*.bench.ts`) et de test (`*.test.ts`) qui
accompagnent chaque action ; le detail de l estimation des frais L1 propres
a la L2 (`estimateFees`, `estimateL1Fee`, `estimateL1GasUsed`, qui
s appuient sur l oracle de prix `GasPriceOracle` predeploye) au-dela de leur
mention au chapitre 5 ; et l outillage de build, de tests (Vitest, y compris
les tests executes contre un fork Ethereum reel via `_test/globalSetup.ts`)
et de publication npm du depot.

L objectif reste le meme que pour les parcours precedents de cette
bibliotheque : comprendre precisement comment ce depot modelise et
implemente le protocole de pont OP Stack au niveau du client, sans
pretendre couvrir l integralite d une bibliotheque aussi vaste -- d autant
que ce depot est desormais officiellement marque comme deprecie au profit
du support natif equivalent dans viem lui-meme.
