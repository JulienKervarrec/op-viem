# Chapitre 4 -- Le flux de retrait : trois etapes obligatoires

Retirer des fonds de la chaine OP Stack vers L1 suit le protocole standard
Optimism en trois etapes distinctes, chacune modelisee par des actions
separees dans ce depot. Premiere etape (L2, wallet) : `writeWithdrawETH` ou
`writeWithdrawERC20` envoient une transaction au contrat predeploye
`L2ToL1MessagePasser` (adresse fixe `0x42...16` sur toute chaine OP Stack,
definie dans `opStackL2ChainContracts`), qui emet un evenement
`MessagePassed` et enregistre le hash du message dans son storage.

Deuxieme etape (preuve, cote L2 puis L1) : une fois le retrait inclus dans
un output L2 propose sur L1 (verifiable via `getOutputForL2Block` /
`getLatestProposedL2BlockNumber`, actions publiques L1), il faut prouver
que ce message a bien ete enregistre dans le storage du
`L2ToL1MessagePasser` a ce bloc precis. `getWithdrawalMessageStorageSlot`
calcule l emplacement de stockage exact : elle encode le
`messageHash` avec l entier `0` (car `L2ToL1MessagePasser` utilise le
premier mapping du contrat, a l index 0, pour stocker les messages envoyes)
via `encodeAbiParameters`, puis hache le resultat en keccak256 -- c est la
regle standard de calcul de slot pour un `mapping(bytes32 => bool)` en
Solidity. `getProveWithdrawalTransactionArgs` orchestre la suite : elle
recupere le bloc L2 concerne, appelle `getProof` (une preuve Merkle-Patricia
de type `eth_getProof`) sur ce slot precis a ce bloc, et assemble
l ensemble des arguments requis par la fonction `proveWithdrawalTransaction`
du contrat `OptimismPortal` sur L1 : la transaction de retrait elle-meme,
l index de l output L2 concerne, un `outputRootProof` (version, state root,
storage root du message passer, hash du dernier bloc) et le
`withdrawalProof` (le tableau de noeuds Merkle retourne par `eth_getProof`).
`writeProveWithdrawalTransaction` (action wallet L1) soumet ensuite ces
arguments on-chain.

Troisieme etape (finalisation, L1, apres le delai de contestation) :
`writeFinalizeWithdrawalTransaction` appelle la fonction correspondante du
`OptimismPortal`, qui verifie que le delai de challenge s est ecoule
(verifiable au prealable via `getSecondsToFinalizable`) puis libere
effectivement les fonds sur L1. `readProvenWithdrawals` et
`readFinalizedWithdrawals` permettent d interroger l etat de ces deux
etapes sans emettre de transaction.
