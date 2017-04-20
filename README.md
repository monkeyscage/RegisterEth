# RedditRegister

## Building

1. First run `truffle compile`, then run `truffle migrate` to deploy the contracts onto your network of choice (default "development").
1. Then run `truffle test` to run basic tests

## Some notes

* The register function requires payment in order to fund the Oraclize queries. If the RedditRegister contract does not have sufficient funds it will return false. Excess or insufficient funds are not returned to the caller, but maintained by the contract to help with future calls. No funds can ever be directly withdrawn from the contract.

* The logic to check that sufficient funds are sent to the register function won't work for the first call to the function. This is because Oraclize doesn't charge for the first callback, so getPrice will return 0. However the contract actually makes 2 calls, so could still throw if there isn't sufficient funds for the second call. This is only an issue for the first time this function is called on the contract.

* Instead of making two Oraclize calls, we could pull a larger json blob from reddit via Oraclize and parse it in the smart contract (e.g. using jsmnsol-lib). This would mean a single Oraclize call which would be simpler, but more tricky parsing. In particular with jsmnsol-lib you would need to walk over each json token, checking for key / value pairs that match. On the plus side we could retrieve more information related to the account.

* Contract required posted address (on reddit.com/r/ethereumproofs) to be all lower-case. The address parsing logic in Oraclize library requires this. This function could be re-written to be more forgiving.

* Possibly the contract should be upgradable (or at least the logic used to validate users). Upgrade could be done based on a vote of existing registered users (although it is cheap to create reddit & ethereum accounts, there is a cost to register them with the contract, so some incentive not to register lots of accounts in order to vote maliciously).

* Writing tests is tricky with Oraclize as it is effectively "asynchronous". More effort required to write a full test suite.

* NB - I've found Oraclize on the test-net (I was using Ropsten) to be quite flacky - sometimes callbacks happen, sometimes they don't, and in addition they don't always return consistent data.
