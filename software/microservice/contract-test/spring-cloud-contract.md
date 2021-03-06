# Spring Cloud Contract

- JVM based microservices testing framework

- Consumer-driven contract testing framework

- Part of the Spring Cloud ecosystem

- Solvest the shortcomings of traditional testing strategies

## Overview

- Provides a contract definition language (groovy dsl)

- Generates and packages stubs from contracts (plugins) - contract in ~ stub out

- Downloads and executes stubs on the consumer side

- Generates and runs contract verification tests on the provider side

### Contract Definition Language

- The language used to build contracts

- Written in Groovy, statically typed and programmer friendly

- Supports HTTP and messaging by default

```groovy
```

### Contract Creation Workflow

1. Switch to provider project

2. Add Spring Cloud Contract Verifier dependency

3. Create a contract.groovy file

4. Declare an expected request

5. Declare an expected response

6. Run install task and skip tests (just generating stub)

* SCC looks for contract by default under test/resources/contracts


```groovy
Contract.make {

  request {
    method 'POST'
    url '/add'
    body """
    {
      v1: 1,
      v2: 2
    }
    """
    headers {
      contentType applicationJson()
    }
  }

  response {
    status 200
    body """
    {
      result: 3
    }
    """
    headers {
      contentType applicationJson()
    }
  }

}

```

### Stub Building Process

1. Generate stub

```shell
mvn clean install -DskipTests
```

2. Transform contract to stub mapping files(mocked api data generated from contract)

3. Package into JAR

## Running Stubs in WireMock

- the stubs are run in WireMock on consumer side

- a Java web server which simulates HTTP based APIs

- SCC generated stub mappings are actually WireMock stub mappings

- Works by returning the responses which correspond with requests declared in our stub mappings

- Provides useful debugging functionality when our stubs are failing

## StubRunner to Test Consumer

1. Downloads stubs from repository

2. Extracts sub mappings from JAR

3. Starts WireMock

4. Imports stub mappings into WireMock instance

5. Tests execute


```java
@AutoConfigureStubRunner(
  ids = {
    "com.xyz:" +       // groupId
    "abcservice:" +    // artifactId
    "1.0:" +           // version, "+" for the latest version
    "stubs:" +         // classifier
    "8080"             // WireMock port
  },
  workOffline = true     // use local repository
)
@SpringBootTest
public class MyTestClass {}

```

- Add stub-runner dependency to build file

## Contract Verification Tests

- Make the request in the contract and assert the response equals the response in the contract

- Automatically generated by SCC Verifier plugin

- Three test modes: MockMvc, explicit, or JaxRs

- Either Junit or Spock

### Contract Verification Workflow

1. Use SCC Verifier to generate contract verification tests

2. Execute contract verification tests

3. Analyse test failures

4. Make tests pass

**NOTE:** "We've deliberately not sliced through the whole stack in our contract
test and have instead mocked the service layer. That's because the contract
tests don't care about the internal behavior of the provider, as that's what the
provider's functional tests are for. The contract tests only care about
verifying the inter service communication will function as expected. "
