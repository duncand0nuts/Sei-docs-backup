# Integration and Debugging

## Parallelization Dependency Mapping Integration

After you create a dependency mapping json for your contract, you will want to verify its correctness by testing it on a local Sei network.

You can follow the steps below to register the dependency mapping:

```
// register dependency using CLI
seid tx accesscontrol register-wasm-dependency-mapping dependency_mapping.json --from $KEY --fees 2000usei --chain-id $CHAIN_ID

// verify the dependency is correctly registered 
seid q accesscontrol wasm-dependency-mapping $CONTRACT_ADDRESS
```

{% hint style="danger" %}
Remember to instantiate your contract with an **admin** account, otherwise you won't be authenticated to register dependencies for that contract
{% endhint %}

## Debugging Errors

It's possible that your contract may have errors with dependencies after initially defining them, and this can be identified via robust testing with a local sei environment. While testing contract using local sei, devs can search for the keyword **`invalid concurrent`** in their sei chain output in order to find errors relating to parallelization. Below is an example of what the output may look like:\


<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

We can interpret the errors in the above INFO logging in the **`Missing Access Operation:`**  in the following way:

* **AccessType**: represents READ or WRITE access of the operation
* **StoreKey**: represents which Sei Cosmos module's KV store had a usage that wasn't declared in the contract dependencies. You can use the [Resource Types](terminology-and-appendix/resource-types-and-identifiers.md) appendix to find the corresponding resource type.
* **Identifier**: a hex string used to identify the key that was being accessed in the KV store. Using this identifier, one can infer the specific resource type by the key prefix used (the first segment of the hex value) and this can be used for identifying what kind of resourcetype needs to be declared.

**For example:**

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

In the above image, we see that there is a missing dependency with the following elements:

* AccessType - `WRITE`
* StoreKey -`wasm`
* Identifier - `03b8789fc7e7456bde8a20801c73ea12ecef7e7a3cfe97ff9b8e8841fb98402e6b756e736574746c65645f706f736974696f6e73`

By referring to [store-key.md](terminology-and-appendix/store-key.md "mention") we can figure out the `wasm` StoreKey corresponds to the `x/wasm` module and `wasmtypes.StoreKey` is the key to find the right list of ResourceTypes in the [#resourcetypeprefix](terminology-and-appendix/resource-types-and-identifiers.md#resourcetypeprefix "mention").&#x20;

Here the identifier begins with `03` as the hex value of the key. By referring to the wasm keys in the [ResourceTypePrefix](terminology-and-appendix/resource-types-and-identifiers.md#resourcetypeprefix), we can figure out that the prefex `0x03` refers to the `ResourceType_KV_WASM_CONTRACT_STORE` and the corresponding resource type in the [Resource Types](terminology-and-appendix/resource-types-and-identifiers.md#resource-types) is `KV_WASM_CONTRACT_STORE`.&#x20;

Using the information above, we can create a dependency that should cover the requirement flagged in the logs as such:

```json
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_WASM_CONTRACT_STORE",
        "identifier_template": "*"
    },
    "selector_type": "NONE"
},
```

This should be sufficient to resolve the dependency error, but for better dependency specification, we would like to as closely replicate the identifier as possible. We can identify that the next segment of the identifier matches a parsed contract address, which can be identified using `seid keys parse identifer[2:34]` (aka the 32 characters after the first two which were the store prefix). For the above example, the characters `b8789fc7e7456bde8a20801c73ea12ecef7e7a3cfe97ff9b8e8841fb98402e6b` are parsed to be `sei1hpufl3l8g44aaz3qsqw886sjanhhu73ul6tllxuw3pqlhxzq9e4sn6dgnt` as the contract address.

Using this, we can specify the dependency to be generated using the contract address:

```json
{

    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_WASM_CONTRACT_STORE",
        "identifier_template": "03%s"
    },
    "selector_type": "CONTRACT_ADDRESS",
    "selector": "sei1hpufl3l8g44aaz3qsqw886sjanhhu73ul6tllxuw3pqlhxzq9e4sn6dgnt"
},
```

Now, we have specified the contract store dependency specific to our contract so that it can operate concurrently to other wasm contracts. By repeating this investigative process, we can build the dependencies for all missing dependencies and have a functioning concurrent wasm contract.

More details about selectors can be found in the [Selectors](terminology-and-appendix/selectors.md) appendix.

After we fix the dependency mapping json, we can follow the [Parallelization Dependency Mapping Integration](integration-and-debugging.md#parallelization-dependency-mapping-integration) guide to register the dependency and verify the correctness again.

{% hint style="warning" %}
:construction: **This section is still under work** :construction:: There may be scripts to further automate the debugging process and interpreting errors in parallel dependencies in the future, however do not currently exist.
{% endhint %}
