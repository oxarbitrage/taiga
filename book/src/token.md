# Token

`Token` define the type of note (e.g. XAN, ETH, BTC). Each token is identified by an address `tokenAddress` (the same way as user address identifies a user) and has its own VP `TokVP`.

### Token VP
Each token has its own [validity predicate](./validity-predicates.md) `TokVP` that defines the conditions on which the token can be sent/received/etc (e.g. whitelist VP that only allows using the token a specified set of users). As with other VPs, `TokVP` checks that input and output notes of the tx satisfy certain constraints.
It is required that the `TokVP` of the tokens involved in a tx evaluated to `true`.

In Taiga, VPs are shielded, so instead of showing that `TokVP` evaluates to `true` publicly, a ZK proof is created. To make sure that `TokVP`  evaluates to `true`, an observer can verify the proof (using the verifier key):

```verify(TokVP_proof, token_VK) = True```

### Token Address
Each token is identified by an address that is derived from its verifier key `token_VK`:
`tokenAddress = Com(token_VK)`


### Example
##### Create a token
In order to create a token, we need `TokVP`. Let's use the `TrivialValidityPredicate` (see [more](./validity-predicates.md)):
```rust
let mut token_vp = TrivialValidityPredicate::<CP> {
	input_notes,
	output_notes,
};

// transform the VP into a short form 
let desc_vp = ValidityPredicateDescription::from_vp(&mut token_vp, &vp_setup).unwrap();

let token = Token::<CP>::new(desc_vp);

//token address can be used to create notes of that token;
let token_address = token.address().unwrap();
```
This example is reproducible with [this file](https://github.com/anoma/taiga/blob/main/src/doc_examples/token.rs) or with the command
```
cargo test doc_examples::token::test_token_creation
```

#### Dummy token

It is also possible to create a dummy token without VP:

```rust
let token = Token::<CP>::dummy(&mut rng)
```

Using this token, we can create a [dummy note](./notes.md) of a specific token (all other fields are random):

```rust
let note = Note::<CP>::dummy_from_token(token, rng)
```

Next: [User](./users.md)