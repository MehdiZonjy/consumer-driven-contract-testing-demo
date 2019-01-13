# Consumer Driven Contract Testing

---
The most depressing thing about life as a programmer, I think, is if you’re faced with a chunk of code that either someone else wrote or, worse still, you wrote yourself but no longer dare to modify. That’s depressing.

*Simon Peyton Jones*

---
## Correctness Delima
- am I calling the correct endpoint?
- did i pass the correct parameters?
- is the api returning the response that i expect?
- is it safe to delete or change a given endpoint?
- did we accidently change the response schema?
---
## Unit Tests
They are easy to write
```javascript
const add = (a, b) => a + b
describe('add', ()=> {
  it('should add two numbers', ()=> {
    expect(add(!,2)).toEqual(3)
  })
})
```
+++
## Unit Tests
We can write many of them
```javascript
const nextContinueWatchingAssetItem = () => {/*.*/}

it('should skip shorts')
it('should return a movie when its progress is less than 80%')
it(`should skip show if there are less than 5 minutes remaining,
 and there is no next episode`)
```
+++
## Unit Tests
Can test happy and sad paths
```javascript

const becauseYouWatchedService = ()=>{/*..*/}
it('should send a notification when there is an item to recommend')
it('should not send any notification when it fails to fetch foggles')
```
+++
## Unit Tests
Aren't suitable for all cases as you can't test outside the boundries of the service
```javascript
const getUser = (conn,userId) => conn.queryOne(
  `SELECT * from users where id = $1`,
  [userId])
```
---
## Integration Tests
They can cross the boundries of a single service and test interactions with other services.
![Integration Test](./assets/imgs/integration-test1.png)
+++
## Integration Tests
Reality is more complicated
![Integration Test2](./assets/imgs/integration-test2.png)
+++
## Integration Tests
Reality is more complicated
![Integration Test3](./assets/imgs/integration-test3.png)
+++
## Integration Tests
 - can detect if services are broken and can't talk to each other
 - slow and expensive to write
 - Could be flaky
---
## E2E
 - slow
 - slow
 - slow
 - flaky++
---
## Consumer Contract Driven Testing
- Provider: an Application responsible for publishing an API
- Consumer: an Application who needs to call that api
+++
@title[Interaction]
@snap[west span-30]
![Interaction](./assets/imgs/interaction.png)
@snapend
@snap[east span-70]
![Interaction](./assets/imgs/interaction-json.png)
@snapend

+++
## Consumer Tests
![Consumer Tests](./assets/imgs/consumer-test.png)
+++
## Pacts (Contracts)
![Pact](assets/imgs/pact.png)
+++
## Provider Tests
![Provider Tests](assets/imgs/provider-tests.png)
+++
## Workflow
![workflow](assets/imgs/workflow.png)
+++
## Consumer driven contract testing
- It's two steps verification
- It fails if changes in the provider cause it to be unable to return the expected response in the contract
- It fails if the consumer is using invalid endpoints, or missing required parameters we would know
+++
## Async Messages
- Consumer: Reads messages from messages broker
- Provider: Send messages to messages broker
![async](assets/imgs/async-msg.png)
+++
## Async Consumer
 - should decouple message handling logic, from messages broker protocol
```javascript
const msgHandler = (msg) => {/*..*/}
SqsIngestor.start(
  {
    handler: services.inboxMessagesRouter,
    queueUrl: '....'
  }
)
```
+++
## Async Consumer Verification
```javascript
expectsToReceive('a valid create task message')
  .withContent({
    messageType: 'create-task',
     payload: {
      dueDate: 32112332,
      groupId: 'user.byw.123_321',
      message: 'hello world',
      topicArn: 'arn:aws:sns:ap-southeast-1:321123:snsTopic1'
    },
    timestamp:'2019-01-03T08:59:56.643Z',
    version: 1
}).verify(msg => {
  msgHandler(msg))
  expect(scheduledTasksRepo.create).toHaveBeenCalledTimes(1)
}
```
+++
### Async Provider Message Builder
- Should isolate message construction code (perhabs using Factory or Builders)
```javascript
const createTaskMessage = (task) => ({
  messageType: MESSAGE_TYPE_CREATE_TASK,
  payload: {
    message: task.message,
    groupId: task.groupId,
    dueDate: task.dueDate,
    topicArn: task.topicArn
  }
  timestamp: new Date().toISOString(),
  version: 1
})
```
---
### Pacts Broker
- Helps in moving pacts between consumers and provider
- Intergrates with deployment pipelines
+++
### Pacts Broker
![Broker](https://raw.githubusercontent.com/wiki/pact-foundation/pact_broker/images/webhook_end_to_end.png)
---
### Demo Time
---
### Thx (^_^)