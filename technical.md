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

class CaseEventsProcessor{
	ProcessCaseEventAsync(CaseEvent @event, CancellationToken cancellationToken = default) {
		var dueDiligenceCommandsResult = await _dueDiligenceCommandFactory.CreateCommandsAsync(@event);
		await _mediator.SendMany(commands, cancellationToken); // Sends the commands
	}
}

class DueDiligenceCommandFactory{
	CreateCommandsAsync(CaseEvent @event){ 
		// Handles CaseCreated events
		_caseFactory.CreateAsync(caseCreated.CaseId); // Actually pulls the case from dynamodb
		_mapper.MapAsync(@case); // Populates individuals and companies from @case.FormSections.KycKybReview.Data
		var createLrsCheckCommand = new CreateLrsCheck(individuals, data.Company, caseCreated.CaseId, caseCreated.CorrelationId) // Create a lrs check command
		new PublishCommand(createLrsCheckCommand, _settings.DueDiligenceQueue)
	}
}

class DueDiligenceCommandsHandler{
	Handle(CreateLrsCheck command, CancellationToken cancellationToken){
		_lrsIntegrationService.TryCreateLrsCheckAsync(command, cancellationToken)
	}
	Handle(GetLrsCheckResult command, CancellationToken cancellationToken){
		_lrsIntegrationService.TryGetLrsCheckResult
	}
}

class LrsIntegrationService{
	
	// Currently TryCreateLrsCheckAsync method only creates an lrs check for a company?
	TryCreateLrsCheckAsync(){
		// Create a LRS request using DD-SDK
		// Persist the reference from POST response and other details into dynamodb
		// Publish an event CreateCaseDueDiligenceEvent (not a message in the dd queue?) I am not sure who is listenning this event
		await _lrsService.PostCompanyCheckAsync();
		_dynamoDbRepository.CreateOrUpdateAsync(ddCheckDocument);
		var publishEventCommand = _commandFactory.CreateCaseDueDiligenceEvent(@case, command, checkId);
		_mediator.Send(publishEventCommand, cancellationToken)
	}
}

class PublishCaseDueDiligenceEventFactory{
	CreateCaseDueDiligenceEvent(){
		// Not sure what and why we are encrypting FormSections.MerchantScreening.Data.Lrs.Data
		var retrieveLrsCheckData = EncryptionHelper.EncryptObjectAES(@case.FormSections.MerchantScreening.Data.LocalRegistersScreening.Data, _caseEncryptionSecret);
		CreateLrsCheckCreated(@case, command.CorrelationId, retrieveLrsCheckData, checkId)
	}
}
```
