

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
