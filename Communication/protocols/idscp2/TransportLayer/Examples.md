# Examples

## RA Mechanism Calculation
An implementation for the calculation of the RA Mechanism could look like the following one:
```kotlin
fun calculateRaMechanism(verifier_suites: Array<String>, prover_suites: Array<String>): String? {
    if (verifier_suites.isNullOrEmpty() || prover_suites.isNullOrEmpty()) {
        return null;
    }

    for (v in verifier_suites) {
        for (p in prover_suites) {
            if (p == s) {
                // found a match
                return p
            }
        }
    }

    // no match
    return null
}

```
This is then called twice to calculate both, the local RaVerifier and the local RaProver mechanism:
```kotlin
private val localVerifierSuites: Array<String>
private val localProverSuites: Array<String>
private lateinit var proverMechanism: String?
private lateinit var verifierMechanism: String?

...

fun receivedIdscpHello(hello: IdscpHello) {

    ...

    // remote peer decides
    this.proverMechanism = calculateRaMechanism(hello.verifier_suites, this.localProverSuites)
    // decide which verifier mechanism to use
    this.verifierMechanism = calculateRaMechanism(this.localVerifierSuites, hello.prover_suites)

    ...

}
```
