# Chapitre 3 -- Le flux de depot : d Ethereum vers la chaine OP Stack

Deposer des fonds consiste a appeler le contrat `OptimismPortal` sur L1.
`simulateDepositETH` (action publique L1) illustre le mecanisme le plus
simple : elle appelle `simulateContract` avec l adresse du `portal` (resolue
via `resolveAddress`, qui accepte soit une adresse brute soit un objet
`ChainContract` avec `chainId`), l ABI et le nom de fonction du depot
(constantes `ABI`/`FUNCTION` importees de `types/depositETH.ts`), et
transmet `amount` a la fois comme argument de fonction et comme `value` de
la transaction (l ETH envoye). Les variantes `simulateDepositERC20` et le
plus generique `simulateDepositTransaction` suivent la meme structure pour
les depots de tokens ERC20 (via le bridge standard) et les depots de
transaction arbitraire.

Le type `DepositTransaction` (dans `types/depositTransaction.ts`) modelise
la transaction telle qu elle apparait *sur L2* une fois le depot traite :
`sourceHash`, `from`, `to`, `mint`, `value`, `gas`,
`isSystemTransaction`, `data`. Chaque champ est commente avec un lien direct
vers le fichier Go source d op-geth qui definit ce type cote client
d execution L2, ancrage documentaire systematique dans ce depot. La
constante `DEPOSIT_TX_PREFIX = '0x7E'` est l octet de type de transaction
EIP-2718 special reserve aux transactions de depot sur OP Stack.

`getSourceHash` calcule le `sourceHash` requis par ce type : elle concatene
le hash du bloc L1 avec l index du log (le `TransactionDeposited` event
concerne, encode sur 32 octets), hache le tout en keccak256 pour obtenir un
`depositIDHash`, puis concatene ce hash avec un `domain` (encode sur 32
octets, `SourceHashDomain.UserDeposit = 0` pour un depot utilisateur normal)
et hache a nouveau -- une derivation en deux etapes, explicitement
recopiee et adaptee depuis le SDK Optimism officiel (le commentaire cite le
fichier source exact). `rlpEncodeDepositTransaction` serialise ensuite la
`DepositTransaction` en RLP (avec des ajustements manuels pour que les
valeurs a zero soient encodees en chaine vide `'0x'`, correspondant au
comportement d op-geth plutot qu au comportement par defaut de `trim` de
viem) et prefixe le resultat par `DEPOSIT_TX_PREFIX`, produisant l objet
qui, une fois hache, donne le hash de la transaction L2 correspondante --
c est ce que `getL2HashFromL1DepositInfo` expose comme fonction de haut
niveau pour retrouver, depuis une transaction de depot L1, le hash de la
transaction L2 qu elle a genere.
