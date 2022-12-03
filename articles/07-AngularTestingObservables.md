# Tips and tricks for Angular Testing

When I write Unit Test one of the things I stumble upon the most is observables. This has nothing to do with the fact that observables might be hard to grasp, rather it has to do with the way I think about problem and that is, the program flows sequentially. The only issue with this is, that Front End programming has more to do with asynchronous function than Back End development. 

With this being said, when dealing with observables not only it is necessary to test the positive case, but the error case as well. Most of the resources I found online explain in great details how to test observables with the `HttpClient`, but almost no one explains how to test simple error cases for observables that are not retrieving data over the wire.

For example if this is the piece of code I want to test, which is, let us say, part of the `BazService`:

```typescript

constructor(private service: BazMockService, private logger: LoggerService) {
}

getSomeData() {
    return this.service.getMockedData()
      .pipe(
        tap(value => this.logger.info('received: ' + value)),
        catchError(() => {
          debugger
          this.logger.error('something went wrong');
          return EMPTY;
        })
      );
}
```
and I want to make sure that in case of errors, the `LoggerService::error` method is called, I can write the following test:

```
let testSubject: BazService;
  
let loggerStub: LoggerService;
  
let mockServiceStub: BazMockService;

beforeEach(() => {
    
    mockServiceStub = new BazMockService();

    loggerStub = {
      info: (_) => {
      },
      warn: (_) => {
      },
      error: (_) => {
      }
    } as LoggerService;

    testSubject = new BazService(mockServiceStub, loggerStub);
});

it('test error case', () => {
    // This way we activate the error channel in the baz.service.ts
    jest.spyOn(mockServiceStub, 'getMockedData').mockReturnValue(
      throwError(() => new Error('I am a bad error'))
    );

    const logErrorSpy = jest.spyOn(loggerStub, 'error');

    testSubject.getSomeData().subscribe();

    expect(logErrorSpy).toHaveBeenCalled();
});
```

here the trick is made by the `throwError(...)` function in the `mockReturnValue` that is basically activating the `catchError` function within the `getSomeData()` from the `BazService`
