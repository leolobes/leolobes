npm install stellar-sdk
const StellarSdk = require('stellar-sdk');

// Initialiser le réseau Stellar (testnet pour cet exemple)
StellarSdk.Networks.useTestNetwork();

// Clé publique et clé secrète de votre compte Stellar (remplacez-les par vos propres clés)
const sourceSecretKey = 'votre_clé_secrète';
const sourceKeypair = StellarSdk.Keypair.fromSecret(sourceSecretKey);
const sourcePublicKey = sourceKeypair.publicKey();

// Créer un client Stellar
const server = new StellarSdk.Server('https://horizon-testnet.stellar.org');

// Créer une transaction
const transaction = new StellarSdk.TransactionBuilder(
  StellarSdk.Keypair.fromSecret(sourceSecretKey).publicKey(),
  {
    fee: await server.fetchBaseFee(),
    networkPassphrase: StellarSdk.Networks.TESTNET
  }
)
  .addOperation(
    StellarSdk.Operation.changeTrust({
      asset: new StellarSdk.Asset('TRENDA', sourcePublicKey),
    })
  )
  .setTimeout(30)
  .build();

// Signer et envoyer la transaction
transaction.sign(sourceKeypair);
server.submitTransaction(transaction)
  .then(result => {
    console.log('Transaction réussie :', result);
  })
  .catch(err => {
    console.error('Erreur lors de l\'envoi de la transaction :', err);
  });
pip install xrpl-py
import xrpl
from xrpl.models.transactions import Payment, TrustSet
from xrpl.wallet import Wallet

# Créer un portefeuille (générer des clés secrètes)
wallet = Wallet.create()

# Adresse de l'émetteur du token
issuer_address = wallet.classic_address

# Créer une transaction TrustSet pour définir le trustline du token
def create_trustline(account, token):
    trustline_tx = TrustSet(
        account=account,
        limit_amount={
            "currency": "TRENDA",
            "issuer": issuer_address,
            "value": "1000000",  # Le montant que vous autorisez
        }
    )
    return trustline_tx

# Créer un TrustSet pour le token TRENDA
trustline_tx = create_trustline(wallet.classic_address, "TRENDA")

# Signer la transaction avec la clé secrète du portefeuille
signed_tx = wallet.sign(trustline_tx)

# Envoyer la transaction au Ledger
client = xrpl.clients.JsonRpcClient("https://s.altnet.rippletest.net:51234/")
response = client.submit(signed_tx)
print("Transaction soumise :", response)
