# Chapitre 2 -- Les quatre decorateurs : etendre un client viem

Le depot expose quatre fonctions decoratrices, chacune concue
pour etre passee a la methode `.extend()` d un client viem existant :
`publicL1OpStackActions`, `publicL2OpStackActions`, `walletL1OpStackActions`
et `walletL2OpStackActions`. Le README illustre ce pattern explicitement :
un `PublicClient` configure sur `mainnet` est etendu avec
`walletL1OpStackActions` pour obtenir un client capable d appeler
`getOutputForL2Block`, tandis qu un client configure sur `base` est etendu
avec `publicL2OpStackActions` pour les actions cote L2.

Chaque decorateur suit exactement la meme forme : une fonction generique
sur le type de `Transport` et de `Chain` du client viem sous-jacent, qui
retourne un objet dont chaque propriete est une fonction qui ferme
(closure) sur le `client` recu en parametre et delegue a la fonction
d action correspondante importee depuis `actions/`. Par exemple
`publicL1OpStackActions` expose `simulateDepositETH: (args) =>
simulateDepositETH(client, args)`. Cette indirection permet a chaque
fonction d action de rester testable independamment (chaque fichier
`actions/**/*.ts` a son propre fichier `*.test.ts` associe, visible dans
l arborescence du depot), tout en offrant a l utilisateur final une API
groupee et chainee au style viem.

La separation public/wallet reflete la distinction viem standard entre
un `PublicClient` (lecture seule, simulation, pas de signature) et un
`WalletClient` (ecriture, necessite un compte signataire) : les actions
`simulateDeposit*` et `readProvenWithdrawals` sont exposees cote public,
tandis que les actions `writeDeposit*` et `writeProveWithdrawalTransaction`
qui emettent reellement une transaction signee sont exposees cote wallet.
