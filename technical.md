## Sending notifications (email or slack) in Genesis 

```csharp
class NotificationCommandsHandler{
	async Task<Result> Handle(){TrySendEmailNotificationAsync()}
	async Task<Result> Handle(){TrySendEmailNotificationAsync()}
	async Task<Result> Handle(NotifyCaseCreated command){TrySendEmailNotificationAsync()}
	...
}


class NotificationService{
	TrySendEmailNotificationAsync(command){
		// Gets the case
		// Gets recipients
		// Get attachment | 4. Publish SQS command
		// Publish event
		CreateNotificationEvent(case)
	}
	TrySendSlackNotificationAsync(command){}
  
}

class PublishNotificationEventCommandFactory {
	CreateNotificationEvent(case){
		// Takes a command and creates an event 
		CreateNotificationSentForCaseCreated()
	}
}
class PublishNotificationEventCommandFactory {
	CreateNotificationSentForCaseCreated(){
		// Creates Event
		return new PublishEventCommand<CaseEvent>(@event, correlationId);
	}
}
```

## Due Diligence integration (creating check requests) in Genesis

```csharp

class LrsIntegrationService{
	
	TryCreateLrsCheckAsync(){
		// Create a LRS request using DD-SDK
		// Persist the reference from POST respons and other details in dynamodb
		// Publish an event CreateCaseDueDiligenceEvent (not a message in the dd queue?)
		await _lrsService.PostCompanyCheckAsync();
		_dynamoDbRepository.CreateOrUpdateAsync(ddCheckDocument);
		_mediator.Send(publishEventCommand, cancellationToken)
	}
}
```
