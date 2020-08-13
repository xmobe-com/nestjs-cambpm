<p align="center">
  <a href="http://nestjs.com"><img src="https://nestjs.com/img/logo_text.svg" alt="Nest Logo" width="320" /></a>
</p>

# NestJS Connector: Camunda External Task Client

## Example

1. Clone the repository
2. Run `npm i && npm run build`
3. Download [workflow.bpmn](example/workflow.bpmn)
4. Open `workflow.bpmn` in Camunda Modeler and start a process instance
5. Go to `example/project`
6. Run `npm i` and `npm start`

```typescript
// src/app.controller.ts
@Controller()
export class AppController {
  @Subscription('my-external-task', {
    lockDuration: 500,
  })
  async myExternalTask(@Payload() task: Task, @Ctx() taskService: TaskService) {
    const businessKey = task.businessKey;
    const isBusinessKeyMissing = !businessKey;

    const processVariables = new Variables();
    processVariables.set('isBusinessKeyMissing', isBusinessKeyMissing);

    if (!isBusinessKeyMissing) {
      await taskService.complete(task, processVariables);
      Logger.log('External task successfully completed!');
    } else {
      const errorMessage = 'No business key given!';
      const options: HandleFailureOptions = {
        errorMessage: errorMessage,
      };
      await taskService.handleFailure(task, options);
      Logger.error(errorMessage);
    }
  }
}
```