# Chapitre 1 -- Presentation de op-viem

Ce depot etend viem (la bibliotheque client Ethereum TypeScript la plus
utilisee) avec des actions specifiques aux chaines OP Stack (Optimism, Base,
Zora, et leurs equivalents testnet). Le README affiche un avertissement de
depreciation explicite : la plupart des fonctionnalites de op-viem ont ete
integrees directement dans viem (module `viem/op-stack`), et le projet
recommande desormais d utiliser le support natif de viem plutot que cette
extension. Ce depot reste neanmoins une reference utile pour comprendre,
de maniere isolee et commentee, le detail des mecanismes de pont (bridge)
entre une chaine L1 (Ethereum mainnet) et une chaine L2 OP Stack.

Le package s appelle `op-viem` (confirme par le `name` dans `package.json`)
et s organise en quatre dossiers principaux sous `src/` : `actions/`
(fonctions bas niveau, une par operation, separees en `public/L1`,
`public/L2` et `wallet/L1`, `wallet/L2`), `decorators/` (quatre fonctions
qui regroupent des actions en une extension de client viem, au sens de
`client.extend(...)`), `types/` (les types des parametres et structures de
donnees du protocole), et `utils/` (fonctions de calcul cryptographique
reutilisees par plusieurs actions : hachage de source, encodage RLP,
calcul de slot de stockage).

La fracture L1/L2 structure tout le depot : les actions L1 concernent les
depots (deposer des fonds depuis Ethereum vers la chaine OP Stack) et la
verification/finalisation des retraits ; les actions L2 concernent
l initiation des retraits et l estimation des frais specifiques a la couche
L2 (notamment le cout de publication des donnees sur L1).
