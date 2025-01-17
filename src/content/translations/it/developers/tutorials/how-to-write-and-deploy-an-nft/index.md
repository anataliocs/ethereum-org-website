---
title: Come Scrivere e Distribuire un NFT (Parte 1/3 della Serie di tutorial sugli NFT)
description: Questo tutorial è la Parte 1 di una serie sugli NFT che ti guiderà passo dopo passo nella procedura per scrivere e distribuire lo smart contract di un Token Non Fungibile (token ERC-721) usando Etherfeum e l'Inter Planetary File System (IPFS).
author: "Sumi Mudgil"
tags:
  - "NFT"
  - "ERC-721"
  - "Alchemy"
  - "Solidity"
  - "smart contract"
skill: beginner
lang: it
published: 2021-04-22
---

Ora che gli NFT rendono nota la blockchain al grande pubblico, si presenta un'eccellente opportunità per comprendere tu stesso questo interesse, pubblicando il tuo NFT (Token ERC-721) sulla blockchain di Ethereum!

Alchemy è estremamente orgogliosa di supportare i più grandi nomi nello spazio degli NFT, tra cui Makersplace (ha recentemente toccato un record nella vendita di opere d'arte digitali a Christie, per $69 milioni), Dapper Labs (creatori di NBA Top Shot e Crypto Kitties), OpenSea (il più grande mercato di NFT al mondo), Zora, Super Rare, NFTfi, Foundation, Enjin, Origin Protocol, Immutable e altri.

In questo tutorial, ti guideremo verso la creazione e distribuzione di uno smart contract ERC-721 sulla rete di prova di Ropsten usando [MetaMask](https://metamask.io/), [Solidity](https://docs.soliditylang.org/en/v0.8.0/), [Hardhat](https://hardhat.org/), [Pinata](https://pinata.cloud/) e [Alchemy](https://alchemy.com/signup/eth) (non preoccuparti se ancora non capisci che significa; te lo spiegheremo!).

Nella Parte 2 di questo tutorial affronteremo come possiamo usare il nostro smart contract per coniare un NFT e nella Parte 3 spiegheremo come visualizzare il tuo NFT su MetaMask.

E, ovviamente, se in qualsiasi momento hai domande, non esitare a contattarci nel [Discord di Alchemy](https://discord.gg/gWuC7zB) o consulta [la documentazione sulle NFT API di Alchemy](https://docs.alchemy.com/alchemy/enhanced-apis/nft-api)!!

## Fase 1: connettersi alla rete di Ethereum {#connect-to-ethereum}

Ci sono molti modi per effettuare richieste alla Blockchain di Ethereum, ma per rendere le cose facili, useremo un profilo gratuito su [Alchemy](https://alchemy.com/signup/eth), una piattaforma per sviluppatori della blockchain e API che ci consente di comunicare con la chain di Ethereum senza dover eseguire dei nodi nostri.

In questo tutorial, approfitteremo anche degli strumenti per monitoraggio e analisi per sviluppatori messi a disposizione da Alchemy per comprendere cosa succede dietro le quinte quando distribuiamo il nostro smart contract. Se non hai già un profilo di Alchemy, puoi iscriverti gratuitamente [qui](https://alchemy.com/signup/eth).

## Fase 2: crea la tua app (e chiave API) {#make-api-key}

Una volta creato un profilo di Alchemy, puoi generare una chiave API creando un'app. Questo ci consentirà di effettuare richieste alla rete di prova di Ropsten. Dai un'occhiata a [questa guida](https://docs.alchemyapi.io/guides/choosing-a-network) se vuoi maggiori informazioni sulle reti di prova.

1. Vai alla pagina “Crea App” nella tua dashboard di Alchemy passando su “App” nella barra di navigazione e cliccando “Crea App”

![Crea la tua app](./create-your-app.png)

2. Dai un nome alla tua app (noi abbiamo scelto “Il mio primo NFT!"), aggiungi una breve descrizione, seleziona “Staging” come Ambiente (serve per la contabilità della tua app) e scegli "Ropsten" come rete.

![Configura e pubblica la tua app](./configure-and-publish-your-app.png)

3. Clicca “Create app” ed è tutto! La tua app dovrebbe apparire nella tabella seguente.

## Fase 3: crea un profilo di Ethereum (indirizzo) {#create-eth-address}

Per inviare e ricevere transazioni serve un profilo Ethereum. Per questo tutorial, useremo MetaMask, un portafoglio virtuale nel browser per gestire l'indirizzo del tuo profilo Ethereum. Se vuoi capire di più su come funzionano le transazioni su Ethereum, dai un'occhiata a [questa pagina](/developers/docs/transactions/) della Ethereum Foundation.

Puoi scaricare e creare gratuitamente un account MetaMask [qui](https://metamask.io/download.html). Quando crei un profilo, o se ne hai già uno, assicurati di passare alla "Rete di Prova Ropsten" in alto a destra (per non avere a che fare con soldi reali).

![Imposta Ropsten come rete](./metamask-ropsten.png)

## Fase 4: aggiungi ether da un Faucet {#step-4-add-ether-from-a-faucet}

Per poter distribuire il nostro smart contract alla rete di prova, avremo bisogno di di ETH finti. Per ottenere ETH, puoi andare all'indirizzo [FaucETH](https://fauceth.komputing.org) e inserire l'indirizzo del tuo account Ropsten, cliccare su "Richiedi fondi", poi selezionare "Ethereum Testnet Ropsten" nel menu a tendina e, infine, cliccare nuovamente il pulsante "Richiedi fondi". Subito dopo dovresti vedere gli ETH nel tuo account MetaMask!

## Fase 5: controlla il saldo {#check-balance}

Per ricontrollare che ci sia il saldo, facciamo una richiesta [eth_getBalance](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc#eth_getbalance) usando lo [strumento compositore di Alchemy](https://composer.alchemyapi.io?composer_state=%7B%22network%22%3A0%2C%22methodName%22%3A%22eth_getBalance%22%2C%22paramValues%22%3A%5B%22%22%2C%22latest%22%5D%7D). Questo restituirà l'importo di ETH nel nostro portafoglio. Dopo aver inserito l'indirizzo del tuo account di MetaMask e aver cliccato “Invia richiesta”, dovresti vedere una risposta come questa:

    `{"jsonrpc": "2.0", "id": 0, "result": "0xde0b6b3a7640000"}`

**NOTA:** Questo risultato è in wei non in ETH. Wei è usato come taglio più piccolo dell'ether. La conversione da wei a ETH è: 1 eth = 10<sup>18</sup> wei. Quindi se convertiamo 0xde0b6b3a7640000 in decimali, otteniamo 1\*10<sup>18</sup> wei, pari a 1 ETH.

Menomale! I nostri soldi finti ci sono tutti.

## Fase 6: inizializza il progetto {#initialize-project}

Prima, dovremo creare una cartella per il nostro progetto. Vai alla riga di comando e digita:

    mkdir my-nft
    cd my-nft

Ora che siamo nella cartella del nostro progetto, useremo npm init per inizializzare il progetto. Se non hai già installato npm, segui [queste istruzioni](https://docs.alchemyapi.io/alchemy/guides/alchemy-for-macs#1-install-nodejs-and-npm) (avremo bisogno anche di [Node.js](https://nodejs.org/en/download/), quindi scarica anche questo!).

    npm init

Non è rilevante come rispondi alle domande d'installazione; ecco le nostre risposte come esempio:

    package name: (my-nft)
    version: (1.0.0)
    description: My first NFT!
    entry point: (index.js)
    test command:
    git repository:
    keywords:
    author:
    license: (ISC)
    About to write to /Users/thesuperb1/Desktop/my-nft/package.json:

    {
      "name": "my-nft",
      "version": "1.0.0",
      "description": "My first NFT!",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "author": "",
      "license": "ISC"
    }

Approva il package.json, e siamo pronti!

## Fase 7: installa [Hardhat](https://hardhat.org/getting-started/#overview) {#install-hardhat}

Hardhat è un ambiente di sviluppo per compilare, distribuire, testare ed effettuare il debug del tuo software di Ethereum. Aiuta gli sviluppatori nella creazione di smart contract e di dApps localmente, prima di distribuirli alla catena dal vivo.

Nel nostro progetto my-nft esegui:

    npm install --save-dev hardhat

Dai un'occhiata a questa pagina per ulteriori dettagli sulle [istruzioni d'installazione](https://hardhat.org/getting-started/#overview).

## Fase 8: crea un progetto Hardhat {#create-hardhat-project}

Nella cartella del nostro progetto esegui:

    npx hardhat

Dovresti poi vedere un messaggio di benvenuto e l'opzione per selezionare cosa desideri fare. Seleziona “crea un hardhat.config.js vuoto”:

    888    888                      888 888               888
    888    888                      888 888               888
    888    888                      888 888               888
    8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
    888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
    888    888 .d888888 888    888  888 888  888 .d888888 888
    888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
    888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888
    👷 Welcome to Hardhat v2.0.11 👷‍
    ? Cosa vuoi fare? …
    Create a sample project
    ❯ Create an empty hardhat.config.js
    Quit

Questo genererà un file hardhat.config.js, in cui specificheremo tutta la configurazione per il nostro progetto (alla fase 13).

## Fase 9: aggiungi le cartelle del progetto {#add-project-folders}

Per mantenere organizzato il nostro progetto, creeremo due nuove cartelle. Vai alla cartella di root del tuo progetto nella tua riga di comando e digita:

    mkdir contracts
    mkdir scripts

- contracts/ è dove manterremo il codice dello smart contract del nostro NFT

- scripts/ è dove manterremo gli script per distribuire e interagire con il nostro smart contract

## Fase 10: compila il nostro contratto {#write-contract}

Ora che il nostro ambiente è configurato, passiamo alle cose più entusiasmanti: _scrivere il codice del nostro smart contract!_

Apri il progetto my-nft nel tuo editor preferito (a noi piace [VSCode](https://code.visualstudio.com/)). Gli smart contract sono scritti in un linguaggio detto Solidity, che useremo per scrivere il nostro smart contract MyNFT.sol.

1. Vai alla cartella `contracts` e crea un nuovo file chiamato MyNFT.sol

2. Segue il codice dello smart contract del nostro NFT, che abbiamo basato sull'implementazione ERC-721 della libreria di [OpenZeppelin](https://docs.openzeppelin.com/contracts/3.x/erc721). Copia e incolla i seguenti contenuti nel tuo file MyNFT.sol.

   ```solidity
   //Contratto basato su [https://docs.openzeppelin.com/contracts/3.x/erc721](https://docs.openzeppelin.com/contracts/3.x/erc721)
   // SPDX-License-Identifier: MIT
   pragma solidity ^0.8.0;

   import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
   import "@openzeppelin/contracts/utils/Counters.sol";
   import "@openzeppelin/contracts/access/Ownable.sol";
   import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";

   contract MyNFT is ERC721URIStorage, Ownable {
       using Counters for Counters.Counter;
       Counters.Counter private _tokenIds;

       constructor() ERC721("MyNFT", "NFT") {}

       function mintNFT(address recipient, string memory tokenURI)
           public onlyOwner
           returns (uint256)
       {
           _tokenIds.increment();

           uint256 newItemId = _tokenIds.current();
           _mint(recipient, newItemId);
           _setTokenURI(newItemId, tokenURI);

           return newItemId;
       }
   }
   ```

3. Poiché stiamo ereditando classi dalla libreria di contratti di OpenZeppelin, nella riga di comando esegui `npm install @openzeppelin/contracts` per installare la libreria nella nostra cartella.

Quindi, cosa _fa_ esattamente questo codice? Analizziamolo, riga dopo riga.

In cima al nostro smart contract, importiamo tre classi dello smart contract di [OpenZeppelin](https://openzeppelin.com/):

- @openzeppelin/contracts/token/ERC721/ERC721.sol contiene l'implementazione dello standard ERC-721, che lo smart contract del nostro NFT erediterà. (Per essere un NFT valido, il tuo smart contract deve implementare tutti i metodi dello standard ERC-721.) Per saperne di più sulle funzioni ERC-721 ereditate, dai un'occhiata alla definizione dell'interfaccia [qui](https://eips.ethereum.org/EIPS/eip-721).

- @openzeppelin/contracts/utils/Counters.sol fornisce contatori che possono esser solo incrementati o diminuiti di unità alla volta. Il nostro smart contract usa un contatore per tener traccia del numero totale di NFT coniati e impostare l'ID univoco sul nostro nuovo NFT. (A ogni NFT coniato usando uno smart contract, deve essere assegnato un ID univoco, qui il nostro ID univoco è determinato solo dal numero totale di NFT esistenti. Ad esempio, il primo NFT che coniamo con il nostro smart contract ha ID "1," il nostro secondo NFT ha ID "2," ecc.)

- @openzeppelin/contracts/access/Ownable.sol imposta [il controllo d'accesso](https://docs.openzeppelin.com/contracts/3.x/access-control) sul nostro smart contract, così solo il proprietario dello smart contract (tu) può coniare NFT. (Nota, prevedere il controllo dell'accesso è totalmente facoltativo. Se vuoi che tutti possano coniare un NFT usando il tuo smart contract, devi rimuovere la parola Ownable alla riga 10 e onlyOwner alla riga 17.)

Dopo le nostre dichiarazioni d'importazione, abbiamo il nostro smart contract NFT personalizzato, che è sorprendentemente breve: contiene solo un contatore, un costruttore e una sola funzione! Questo grazie ai nostri contratti ereditati da OpenZeppelin, che implementa gran parte dei metodi che necessitiamo per creare un NFT, come `ownerOf`, che restituisce il proprietario dell'NFT e `transferFrom`, che trasferisce la proprietà dell'NFT da un account a un altro.

Nel nostro costruttore ERC-721, noterai che passiamo 2 stringhe, “MyNFT” e “NFT.” La prima variabile è il nome dello smart contract mentre la seconda è il suo simbolo. Puoi assegnare a ciascuna di queste variabili il nome che desideri!

Infine, abbiamo la nostra funzione `mintNFT(address recipient, string tokenURI)` che ci consente di coniare un NFT! Noterai che questa funzione usa due variabili:

- `address recipient` specifica l'indirizzo che riceverà il tuo NFT appena coniato

- `string memory tokenURI` è una stringa che dovrebbe risolversi in un documento JSON che descrive i metadati dell'NFT. I metadati di un NFT sono davvero ciò che lo porta in vita, consentendogli di avere proprietà configurabili, quali nome, descrizione, immagine e altri attributi. Nella parte 2 di questo tutorial, descriveremo come configurare questi metadati.

`mintNFT` chiama alcuni metodi dalla libreria ERC-721 ereditata e, infine, restituisce un numero che rappresenta l'ID dell'NFT appena coniato.

## Fase 11: connetti MetaMask e Alchemy al tuo progetto {#connect-metamask-and-alchemy}

Ora che abbiamo creato un portafoglio MetaMask, un profilo di Alchemy e scritto il nostro smart contract, è il momento di collegare questi tre elementi.

Ogni transazione inviata dal tuo portafoglio virtuale richiede una firma tramite la tua chiave privata unica. Per fornire al nostro programma quest'autorizzazione, possiamo memorizzare in sicurezza la nostra chiave privata (e la chiave API di Alchemy) in un file ambientale.

Per saperne di più sull'invio delle transazioni, dai un'occhiata a [questo tutorial](/developers/tutorials/sending-transactions-using-web3-and-alchemy/) sull'invio di transazioni usando web3.

Prima, installa il pacchetto dotenv nella cartella del tuo progetto:

    npm install dotenv --save

Poi, crea un file `.env` nella cartella di root del nostro progetto e aggiungi la tua chiave privata di MetaMask e l'URL API di Alchemy HTTP.

- Segui [queste istruzioni](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key) per esportare la tua chiave privata da MetaMask

- Vedi di seguito come ottenere l'URL dell'API di Alchemy HTTP e copialo negli appunti

![Copia l'URL dell'API di Alchemy](./copy-alchemy-api-url.gif)

Il tuo `.env` dovrebbe somigliare a questo:

    API_URL="https://eth-ropsten.alchemyapi.io/v2/your-api-key"
    PRIVATE_KEY="your-metamask-private-key"

Per connetterli realmente al nostro codice, faremo riferimento a queste variabili nel nostro file hardhat.config.js nella fase 13.

<InfoBanner isWarning>
Non eseguire il commit di <code>.env</code>! Assicurati di non condividere o esporre mai il tuo file <code>.env</code> con nessuno, poiché così facendo comprometteresti i tuoi segreti. Se stai usando il controllo di versione, aggiungi il tuo <code>.env</code> a un file <a href="https://git-scm.com/docs/gitignore">gitignore</a>.
</InfoBanner>

## Fase 12: installa Ethers.js {#install-ethers}

Ethers.js è una libreria che rende più facile interagire ed effettuare richieste a Ethereum tramite wrapping dei [metodi JSON-RPC standard](/developers/docs/apis/json-rpc/) con altri metodi più facili da usare.

Hardhat rende davvero facile integrare [Plugin](https://hardhat.org/plugins/) per strumenti e funzionalità aggiuntive. Sfrutteremo il [plugin di Ethers](https://hardhat.org/plugins/nomiclabs-hardhat-ethers.html) per la distribuzione del contratto ([Ethers.js](https://github.com/ethers-io/ethers.js/) ha dei metodi di distribuzione del contratto molto puliti).

Nella cartella del tuo progetto digita:

    npm install --save-dev @nomiclabs/hardhat-ethers ethers@^5.0.0

Avremo bisogno di ethers anche nel nostro hardhat.config.js nella prossima fase.

## Fase 13: aggiorna hardhat.config.js {#update-hardhat-config}

Finora abbiamo aggiunto diverse dipendenze e plugin, ora dobbiamo aggiornare hardhat.config.js in modo che il nostro progetto li riconosca tutti.

Aggiorna il tuo hardhat.config.js affinché somigli a questo:

    /**
    * @type import('hardhat/config').HardhatUserConfig
    */
    require('dotenv').config();
    require("@nomiclabs/hardhat-ethers");
    const { API_URL, PRIVATE_KEY } = process.env;
    module.exports = {
       solidity: "0.8.1",
       defaultNetwork: "ropsten",
       networks: {
          hardhat: {},
          ropsten: {
             url: API_URL,
             accounts: [`0x${PRIVATE_KEY}`]
          }
       },
    }

## Fase 14: compila il contratto {#compile-contract}

Per assicurarti che tutto funzioni fino a questo punto, compila il contratto. L'attività di compilazione è una delle attività integrate di hardhat.

Dalla riga di comando esegui:

    npx hardhat compile

Potresti ottenere un avviso sull'assenza nel file sorgente dell'identificativo della licenza SPDX, ma non preoccupartene, si spera che tutto il resto funzioni! Altrimenti, puoi sempre inviare un messaggio nel [Discord di Alchemy](https://discord.gg/u72VCg3).

## Fase 15: scrivi lo script di distribuzione {#write-deploy}

Ora che il nostro contratto è scritto e il nostro file di configurazione è pronto, è il momento di scrivere lo script di distribuzione del contratto.

Vai alla cartella `script/` e crea un nuovo file chiamato `deploy.js`, aggiungendo i seguenti contenuti:

```js
async function main() {
  const MyNFT = await ethers.getContractFactory("MyNFT")

  // Start deployment, returning a promise that resolves to a contract object
  const myNFT = await MyNFT.deploy()
  await myNFT.deployed()
  console.log("Contract deployed to address:", myNFT.address)
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error)
    process.exit(1)
  })
```

Nel suo [tutorial sui Contratti](https://hardhat.org/tutorial/testing-contracts.html#writing-tests) hardhat spiega in modo eccellente cosa fa ognuna di queste righe di codice nel loro, quindi riportiamo qui le loro spiegazioni.

    const MyNFT = await ethers.getContractFactory("MyNFT");

Un ContractFactory su ethers.js è un'astrazione usata per distribuire nuovi smart contract, quindi MyNFT qui è una fabbrica di istanze del nostro contratto NFT. Usando il plugin hardhat-ethers, le istanze ContractFactory e Contract sono connesse di default al primo firmatario.

    const myNFT = await MyNFT.deploy();

Chiamare deploy() su un ContractFactory avvierà la distribuzione e restituirà un Promise che si risolve in un Contract. Questo è l'oggetto che ha un metodo per ciascuna delle funzioni del nostro smart contract.

## Fase 16: distribuisci il contratto {#deploy-contract}

Siamo finalmente pronti a distribuire il nostro smart contract! Torna alla root della cartella del tuo progetto e nella riga di comando esegui:

    npx hardhat --network ropsten run scripts/deploy.js

Vorrai poi vedere qualcosa del genere:

    Contract deployed to address: 0x81c587EB0fE773404c42c1d2666b5f557C470eED

Se andiamo a [Ropsten etherscan](https://ropsten.etherscan.io/) e cerchiamo l'indirizzo del nostro contratto, dovremmo poter vedere che è stato distribuito correttamente. Se non riesci a vederlo immediatamente, attendi un po', poiché potrebbe essere necessario un po' di tempo. La transazione somiglierà a questa:

![Visualizza l'indirizzo della tua transazione su Etherscan](./etherscan-transaction.png)

L'indirizzo "Da" dovrebbe corrispondere all'indirizzo del tuo account di MetaMask mentre l'indirizzo "A" sarà: “Creazione del Contratto.” Se clicchiamo nella transazione, vedremo l'indirizzo del nostro contratto nel campo "A":

![Visualizza l'indirizzo del tuo contratto su Etherscan](./etherscan-contract.png)

Sì! Hai appena distribuito il tuo smart contract dell'NFT alla chain di Ethereum!

Per capire cosa sta succedendo, andiamo alla scheda Explorer nella nostra [dashboard di Alchemy](https://dashboard.alchemyapi.io/explorer). Se hai diverse app di Alchemy, assicurati di filtrare per app e selezionare "MyNFT".

![Visualizza le chiamate effettuate "dietro le quinte" con la dashboard Explorer di Alchemy](./alchemy-explorer.png)

Qui vedrai numerose chiamate a JSON-RPC che Hardhat/Ethers ha effettuato per noi quando abbiamo chiamato la funzione .deploy(). Due chiamate importanti sono [eth_sendRawTransaction](/developers/docs/apis/json-rpc/#eth_sendrawtransaction), che è la richiesta per scrivere realmente il nostro smart contract sulla catena di Ropstein e [eth_getTransactionByHash](/developers/docs/apis/json-rpc/#eth_gettransactionbyhash), che è una richiesta a leggere le informazioni sulla nostra transazione dato l'hash (sistema tipico quando si inviano delle transazioni). Per saperne di più sull'invio delle transazioni, dai un'occhiata a questo tutorial [ sull'invio di transazioni usando web3](/developers/tutorials/sending-transactions-using-web3-and-alchemy/).

È tutto per la Parte 1 di questo tutorial. Nella [Parte 2, interagiremo realmente con il nostro smart contract, coniando un NFT](/developers/tutorials/how-to-mint-an-nft/), mentre nella [Parte 3 ti mostreremo come visualizzare il tuo NFT nel tuo portafoglio di Ethereum](/developers/tutorials/how-to-view-nft-in-metamask/)!
