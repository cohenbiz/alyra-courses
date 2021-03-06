# **Solidity**

Documentation officielle: https://solidity.readthedocs.io/en/v0.7.3/

Lorsque vous consultez une documentation, vérifiez bien le numéro de version auquel s'applique cette documentation.

## **Introduction**

Solidity est un langage orienté objet de haut niveau à typage fort.
Typage fort signifie que le type de toutes nos variables, ainsi que les paramètres de nos fonctions et leur valeur de retour doivent avoir un type définit dés la compilation (typage fort statique), ou pour certains avant leur exécution (typage for dynamique).

> Solidity is statically typed, supports inheritance, libraries and complex user-defined types among other features.

Solidty est un langage dont la syntaxe se rapproche fortement du C++, java ou encore Javascript. On peut le décrire comme un langage C-Like.

> Solidity was influenced by C++, Python and JavaScript and is designed to target the Ethereum Virtual Machine (EVM).

Solidity est l'un des langages qui permet de developper et d'implémenter des smart contracts.
Les fichiers code source en Solidity doivent être compilés en `bytecode` puis ce `bytecode` sera ensuite déployé sur l'`EVM` puis exécuté lors d'une interaction avec ce smart contracts.
Solidity est un petit langage, qui n'a été prévu que pour écrire des smart contracts, il a été spécifiquement désigné pour cela, ce n'est pas un **General-purpose programming languages**

## **Structure d'un fichier Solidity**

Par convention, l'extension d'un fichier Solidity est `.sol`.  
Un fichier Solidity se compose d'un certain nombre de définitions de `contract`s, `library`, d'`import`, de directives `pragma` de `struct`, d'`enum`, de `function` et de variables `constant`.

_SolidityCourse.sol_:

```solidity
// SPDX-License-Identifier: MIT                 // SPDX Licence Identifier
pragma solidity ^0.6.0;                         // Version pragma
pragma experimental ABIEncoderV2;               // Experimental pragma

import './Logger.sol' as logger;                // import de ./Logger.sol

contract SolidityCourse is logger.Logger {      // Declaration du contract
    address private owner;

    constructor() public {
        owner = msg.sender;
    }

    function whoIsOwner() public returns(address) {
        log();
        return owner;
    }

    function getLastVisitor() public returns(address) {
        address lastVisitor = getLast();
        log();
        return lastVisitor;
    }

    function getNthVisitor(uint _pos) public returns(address) {
        address nthVisitor = getVisitorByPosition(_pos);
        log();
        return nthVisitor;
    }

    function getAllVisitors() public view returns(address[] memory) {
        require(owner == msg.sender, 'Only owner can use this function.');

        return getAll();
    }
}
```

_Logger.sol_:

```solidity
// SPDX-License-Identifier: MIT                 // SPDX Licence Identifier
pragma solidity ^0.6.0;                         // Version pragma
pragma experimental ABIEncoderV2;               // Experimental pragma
contract Logger {                               // Declaration du contract
    address[] private  logs;
    function log() internal {
        logs.push(msg.sender);
    }
    function getLast() internal view returns(address) {
        return logs[logs.length - 1];
    }

    function getVisitorByPosition(uint _pos) internal view returns(address) {
        return logs[_pos - 1];
    }

    function getAll() internal view returns(address[] memory) {
        return logs;
    }
}
```

### **`SPDX Licence Identifier`**

Une véritable confiance en votre smart contract peut s'établir avec vos utilisateurs que si vous fournissez le code source de votre smart contract.
(Il faut notez que le `bytecode` de votre smart contract sera toujours visible dans l'`EVM`, et pas nécessairement le code source Solidity de votre smart contract).
Publier son code Solidity peut entraîner des problèmes de copyright, il faut donc spécifier une licence qui sera incluses dans les meta-données de votre `bytecode`.  
Liste des licences disponibles: https://spdx.org/licenses/
Si vous ne souhaitez pas inclure une licence il faudra tout de même spécifier l'identifier `SPDX-License-Identifier: MIT `

### **Généralités sur la directive `pragma`**

La directive `pragma` est ce qu'on appelle une directive du préprocesseur.
Cette directive assure une phase préliminaire de la compilation.  
C'est un procédé qui est inspiré des langages `C` et `C++`.  
Son objectif est de renseigner le compilateur avant la phase de compilation sur son comportement, des vérifications à effectuer ou des fonctionnalités à activer.
Actuellement pour Solidity les directives `pragma` servent à spécifier une ou des versions de compilateurs acceptables pour notre code source et d'activer des fonctionnalités expérimentales.

### **Version `pragma`**

Cette directive `pragma` spécifie la ou les versions acceptables pour notre code source Solidity.

```solidity
// Ne compilera pas avec une version de compilateur antérieure à 0.6.0
// et ne compilera pas à partir de 0.7.0
pragma solidity ^0.6.0;
```

```solidity
// Compilera seulement avec une version de compilateur entre 0.6.0
// et 0.8.0 (non incluse)
pragma solidity >= 0.6.0 < 0.8.0;
```

### **Experimental `pragma`**

Cette directive `pragma` spécifie les features expérimentales à activer lors de la compilation qui ne sont pas encore activées par défaut.  
Il existe actuellement 2 features expérimentales sur Solidity:

```solidity
// Offre la possibilité d'encoder et decoder des structures de données complexes.
// Cette fonctionnalité est très utile, et vous devrez probablement l'activer.
// Depuis 0.6.0 cette feature n'est plus considérée comme experimentale en réalité.
pragma experimental ABIEncoderV2;
```

et

```solidity
// Permet d'obtenir des informations complémentaires sur des bugs, erreurs et
// problèmes liées à la sécurité pendant la compilation.
// Cette feature est vraiment expérimentale, il est préférable de la désactiver.
pragma experimental SMTChecker;
```

### **import**

Solidity utilise la même syntaxe d'`import` que l'on a utilisé en JavaScript ES6.
Le chemin d'accès du fichier à importer est important. Tous les noms de fichiers importés sont considérés comme des chemins absolus. Si nous souhaitons importer un fichier qui est dans le répertoire courant nous devons ajouter `./` devant le nom du fichier.  
Il existe des options que nous pouvons passer au compilateur afin de déterminer le répertoire de base des `import`.

```solidity
// Importe tout le contenu de ./DummyLib.sol dans le fichier
// Peu recommandé car cela pollue l'espace de noms dans notre fichier actuel
import "./Logger.sol";
```

L'`import` précédent peut créer une collision de noms, cad que des noms importés existe déjà dans le fichier courant.
Pour éviter cela on peut créer des alias:

```solidity
// Importe symbol1 en tant que symbol2 depuis ./DummyLib.sol
import {Logger as logger} from "./Logger.sol";
```

Une autre façon de faire:

```solidity
// Importe tout le contenu de ./DummyLib.sol et le rend accessible
// dans le symbole dummyLib
import * as logger from "./Logger.sol";
```

Une alternative à la syntaxe précédente est:

```solidity
// Importe tout le contenu de ./DummyLib.sol et le rend accessible
// dans le symbole dummyLib
import "./DummyLib.sol" as logger;
```

### **Les commentaires**

Comme en JavaScript:

```solidity
// Commentaire sur une ligne.

/*
    Commentaire sur
    plusieures lignes.
*/
```

Il existe un autre type de commentaire qui en plus de servir à documenter le code,
sert également à générer la documentation technique du code automatiquement.
Ce sont les commentaires `natspec`:

```solidity
pragma solidity >=0.4.0 <0.6.0;

/** @title Shape calculator. */
contract ShapeCalculator {
    /** @dev Calculates a rectangle's surface and perimeter.
      * @param w Width of the rectangle.
      * @param h Height of the rectangle.
      * @return s The calculated surface.
      * @return p The calculated perimeter.
      */
    function rectangle(uint w, uint h) public pure returns (uint s, uint p) {
        s = w * h;
        p = 2 * (w + h);
    }
}
```

## **Déclaration d'un contrat**

Un contract est l'équivalent d'une classe d'un langage orienté objet.
Un contract se déclare de la manière suivante:

```solidity
contract MyContract {
    // Déclarations goes here...
}
```

le mot clé `contract` suivit du nom du contract et entre les 2 accolades le contenu du contract.
Un contract peut contenir les déclarations suivantes:

- un `constructor`
- variables d'état
- `function`s
- `modifier`s
- `event`s
- types `struct`
- types `enum`

### **constructor**

Le `constructor` est la première fonction apppelée lors du déploiment d'un contract.  
Il est optionnel mais il est très utile lorsqu'il faut initialiser les variables d'état, et particluièrement lorsque cette initialisation dépend de `global variables`.
Un contrat sans `constructor` possédera le `constructor` par défaut: `constructor() {}`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;
pragma experimental ABIEncoderV2;

contract MyContract {
    address private owner;
    uint public  max;

    constructor(uint _max) public  {
        owner = msg.sender;
        max = _max;
    }
}
```

Les variables d'états peuvent aussi être initialisées hors du `constructor`, si elle ne sont pas initialisées elles prenderont une valeur par défaut qui correspondera à `0` selon leur type.

### **State variables**

Par défaut ces variables sont initialisées

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.0 <0.8.0;

contract SimpleStorage {
    uint storedData; // State variable
    // ...
}
```

Ces variables sont stockées de manière permanente dans le smart contract.  
Sa `data location` est le `storage`.

### **function**

Tout code exécutable se trouve dans une fonction.

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >0.7.0 <0.8.0;

contract SimpleAuction {
    function bid() public payable { // Function
        // ...
    }
}

// Helper function defined outside of a contract
function helper(uint x) pure returns (uint) {
    return x * 2;
}
```

Les fonctions peuvent également être définie à l'extérieur d'un smart contract.

### **modifier**

Les `modifier`s ajoutent un contrôle sur l'exécution d'une fonction. Ils vérifient la condition d'exécution ou non de la fonction à laquelle est appliqué le `modifier`s.

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.22 <0.8.0;

contract Purchase {
    address public seller;

    modifier onlySeller() { // Modifier
        require(
            msg.sender == seller,
            "Only seller can call this."
        );
        _;
    }

    function abort() public view onlySeller { // Modifier usage
        // ...
    }
}
```

### **event**

Les events servent à écrire dans le journal de l'`EVM`.
C'est un moyen efficace de vérifier si un évenement s'est produit depuis notre Dapp.

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.21 <0.8.0;

contract SimpleAuction {
    event HighestBidIncreased(address bidder, uint amount); // Event

    function bid() public payable {
        // ...
        emit HighestBidIncreased(msg.sender, msg.value); // Triggering event
    }
}
```

### **struct**

Les `struct` sont l'équivalent des objets en Javascript.
Nous pouvons définir une `struct` qui contient plusieurs variables.

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.0 <0.8.0;

contract Ballot {
    struct Voter { // Struct
        uint weight;
        bool voted;
        address delegate;
        uint vote;
    }
}
```

### **enum**

Les `enum`est un type de données qui consiste en un ensemble de valeurs constantes. Ces différentes valeurs représentent différents cas.  
Lorsqu'une variable est de type énuméré, elle peut avoir comme valeur n'importe quel cas de ce type énuméré.

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.0 <0.8.0;

contract Purchase {
    enum State { Created, Locked, Inactive } // Enum
}
```

## **Types de données**

## **Structures de contrôle**

Solidity supporte les structures de contrôle suivantes:
`if`, `else if`, `else`, `while`, `do`, `for`, `break`, `continue`, `return`.
La syntaxe est là même que pour JavaScript à la différence de certaines execptions:

- Les points virgules sont obligatoires.
- Il n'y a pas de conversions entre un non-booléen vers un booléen, le code suivant est invalide en Solidity `if (1) { ... }`.

## **Precedence of operators**

## **Ether and time units**

### **Ether units**

A literal number can take a suffix of wei, gwei or ether to specify a subdenomination of Ether, where Ether numbers without a postfix are assumed to be Wei.

```solidity
assert(1 wei == 1);
assert(1 gwei == 1e9);
assert(1 ether == 1e18);
```

The only effect of the subdenomination suffix is a multiplication by a power of ten.

### **time units**

Suffixes like `seconds`, `minutes`, `hours`, `days` and `weeks` after literal numbers can be used to specify units of time where seconds are the base unit and units are considered naively in the following way:

        1 == 1 seconds
        1 minutes == 60 seconds
        1 hours == 60 minutes
        1 days == 24 hours
        1 weeks == 7 days

Take care if you perform calendar calculations using these units, because not every year equals 365 days and not even every day has 24 hours because of leap seconds. Due to the fact that leap seconds cannot be predicted, an exact calendar library has to be updated by an external oracle.

These suffixes cannot be applied to variables. For example, if you want to interpret a function parameter in days, you can in the following way:

```solidity
function f(uint start, uint daysAfter) public {
    if (block.timestamp >= start + daysAfter * 1 days) {
      // ...
    }
}
```

## **Variables globales**

- `abi.decode(bytes memory encodedData, (...)) returns (...)`: ABI-decodes the provided data. The types are given in parentheses as second argument. Example: `(uint a, uint[2] memory b, bytes memory c) = abi.decode(data, (uint, uint[2], bytes))
- `abi.encode(...) returns (bytes memory)`: ABI-encodes the given arguments
- `abi.encodePacked(...) returns (bytes memory)`: Performs packed encoding of the given arguments. Note that this encoding can be ambiguous!
- `abi.encodeWithSelector(bytes4 selector, ...) returns (bytes memory)`: ABI-encodes the given arguments starting from the second and prepends the given four-byte selector -`abi.encodeWithSignature(string memory signature, ...) returns (bytes memory)`: Equivalent to abi.encodeWithSelector(bytes4(keccak256(bytes(signature)), ...)
- `block.coinbase (address payable)`: current block miner’s address block.
- ` difficulty (uint)`: current block difficulty `block.gaslimit (uint): current block gaslimit
- `block.number (uint)`: current block number
- `block.timestamp (uint)`: current block timestamp
- `gasleft() returns (uint256)`: remaining gas
- `msg.data (bytes)`: complete calldata
- `msg.sender (address payable)`: sender of the message (current call) msg.value (uint): number of wei sent with the message
- `tx.gasprice (uint): gas price of the transaction `tx.origin (address payable): sender of the transaction (full call chain)
- `assert(bool condition): abort execution and revert state changes if condition is `false (use for internal error)
- `require(bool condition): abort execution and revert state changes if condition is false (use for malformed input or error in external component) `require(bool condition, string memory message): abort execution and revert state changes if condition is false (use for malformed input or error in external component). Also provide error message.
- `revert()`: abort execution and revert state changes
- `revert(string memory message)`: abort execution and revert state changes providing an explanatory string
- `blockhash(uint blockNumber) returns (bytes32): hash of the given block - only works for 256 most recent blocks `
- `keccak256(bytes memory) returns (bytes32)`: compute the Keccak-256 hash of the input
- `sha256(bytes memory) returns (bytes32)`: compute the SHA-256 hash of the input
- `ripemd160(bytes memory) returns (bytes20)`: compute the RIPEMD-160 hash of the input
- `ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)`: recover address associated with the public key from elliptic curve signature, return zero on error
- `addmod(uint x, uint y, uint k) returns (uint)`: compute (x + y) % k where the addition is performed with arbitrary precision and does not wrap around at 2\*\*256. Assert that k != 0 starting from version 0.5.0.
- `mulmod(uint x, uint y, uint k) returns (uint)`: compute (x \* y) % k where the multiplication is performed with arbitrary precision and does not wrap around at 2\*\*256. Assert that k != 0 starting from version 0.5.0.
- `this (current contract’s type)`: the current contract, explicitly convertible to address or address payable
- `super`: the contract one level higher in the inheritance hierarchy
- `selfdestruct(address payable recipient)`: destroy the current contract, sending its funds to the given address
- `<address>.balance (uint256)`: balance of the Address in Wei
- `<address payable>.send(uint256 amount) returns (bool`): send given amount of Wei to Address, returns false on failure
- `<address payable>.transfer(uint256 amount)`: send given amount of Wei to Address, throws on failure
- `type(C).name (string)`: the name of the contract
- `type(C).creationCode (bytes memory)`: creation bytecode of the given contract, see Type Information.
- `type(C).runtimeCode (bytes memory)`: runtime bytecode of the given contract, see Type Information.
- `type(I).interfaceId (bytes4)`: value containing the EIP-165 interface identifier of the given interface, see Type Information.
- `type(T).min (T)`: the minimum value representable by the integer type T, see Type Information.
- `type(T).max (T)`: the maximum value representable by the integer type T, see Type Information.

## **cheatsheet**

https://solidity.readthedocs.io/en/latest/cheatsheet.html
