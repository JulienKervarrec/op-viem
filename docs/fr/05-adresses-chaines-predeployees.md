# Chapitre 5 -- Adresses de contrats : predeployes L2 et deploiements L1

Ce depot distingue deux categories d adresses de contrats. Cote L2, les
contrats systeme OP Stack sont predeployes a des adresses fixes et
identiques sur toute chaine OP Stack (Base, Optimism, Zora, etc.) :
`opStackL2ChainContracts` (dans `types/opStackContracts.ts`) liste sept
contrats -- `l2CrossDomainMessenger` (`0x...07`), `l2StandardBridge`
(`0x...10`), `gasPriceOracle` (`0x...0F`), `l1Block` (`0x...15`),
`l2ToL1MessagePasser` (`0x...16`), `l2Erc721Bridge` (`0x...14`),
`optimismMintableErc721Factory` (`0x...17`) -- toutes dans l espace
d adresses reserve `0x4200000000000000000000000000000000...`, une
convention OP Stack qui rend ces contrats reconnaissables au premier coup
d oeil sur un explorateur de blocs.

Cote L1, chaque chaine OP Stack a ses propres contrats de pont deployes a
des adresses specifiques a cette chaine (puisqu ils vivent sur Ethereum
mainnet, pas sur la L2). `src/chains/base.ts` exporte `baseAddresses`,
qui associe a Base ses cinq contrats L1 : `portal` (le contrat central pour
depots et retraits), `l2OutputOracle` (propositions d etat L2), et les
bridges/messengers standard (`l1StandardBridge`, `l1CrossDomainMessenger`,
`l1Erc721Bridge`), chacun accompagne de son `blockCreated` (utile pour
borner les recherches de logs). Des fichiers equivalents existent pour
`optimism.ts`, `zora.ts`, et leurs variantes testnet Goerli, refletant le
fait qu au moment de l ecriture de ce depot, plusieurs chaines OP Stack
distinctes de Base partageaient la meme base de code de pont.

Le type generique `Addresses<TChainId>` (utilise par ces fichiers) associe
a chaque champ un `RawOrContractAddress`, une union qui accepte soit une
adresse brute (`0x...`), soit un objet `ChainContract` complet avec
`chainId` -- resolue par la fonction utilitaire `resolveAddress` au moment
de l appel, pour que les fonctions d action restent agnostiques du format
choisi par l appelant.
