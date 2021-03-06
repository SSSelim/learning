# Consumer-driven Contract Testing

- Fills the microservices testing gap(prevents false positives by mocks, faster feedback loop)

- Where consumers and providers are continously tested against constracts

- Where consumers drive the implementation of providers

- Microservices become independently testable and releasable

- Implemented by Spring Cloud Contract

### Architecture

- Consumer
- Constract
- Provider

### Contract

- A set of agreed interactions between a provider and a consumer

- Something which is continuously tested

- Whilst commonly HTTP, can be any protocol

- Not the same as stubbing

- Not api documentation


### Consmer Side

- Tests
- Consumer
- Uses 'Stub Producer' generated by framework using the "contract"
- Stub producer returns agreed response when agreed request is made

### Provider Side

- Framework generates tests against provider using "contract"

- Tests will do the agreed request to the provider, asserts response matches agreed response

## Benefits

- Fast reliable feedback (faster than end-to-end tests)

- Save on infrastructre costs

- consumers drive the API

- Navigating dependency hell

- Develop in parallel reliably

- Contracts cannot be stale(opposite of stubbing)
