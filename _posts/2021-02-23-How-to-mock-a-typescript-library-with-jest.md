---
layout: post
---
I never thought that add unit tests were going to be that difficult. After one whole day I have got two tests implemented and passing.

Let's see what I figured out

# Basic tips to work with matchers

* To test if a constructor or a function call throws an error, add a function instantiating the class. In this way the error will be thrown just before the assertion. 

    ```typescript
    expect(() => { const newClass = NewClass()}).not.toThrowError();
    ```

* To test that any kind of object is equal to another, you need to execute a deep equal. Having object as an array, for example. 

    ```typescript
    expect(complexObject).toEqual({el1: '', el2: 0})
    ```

# Get coverage report displayed in your terminal

* To get coverage you need to add the following properties to the file jest.config.js:

    ```json
    "collectCoverage": true,
    "collectCoverageFrom": ["pathToYourFilesYouWantToCover/*.ts"]
    ```
    Make sure the path that you add in collectCoverageFrom is a correct one. Jest doesn't display any error, just returns an empty table
    instead of the coverage table with the code covered data. 

 # If you are using mocks in your tests, and one of the test only works when it's executed alone

    This can happen because you need to clear your mocks between tests. 

    ```typescript
    describe("Testing....", () => {
            const consoleLogSpy = jest.spyOn(console, 'log');

            beforeEach(() => {
                jest.clearAllMocks();
            })
    })
    ```

# If you need to mock a fs function, and force the result that is returning

    Before anything else, we need to clear all mocks before each tests

    ```typescript

    beforeEach(() => {
        jest.clearAllMocks();
    })
    
    ```

    We need to mock fs library and then use jest.spyOn to be able to force the function to return what we need for the test.
    So, at the top of the test, in the imports area:

    ```typescript

    jest.mock('fs');
    import * as fs from 'fs';

    ```

    And inside the test is where we force the function to return the required value

    ```typescript

    test("Should ...", () => {
        jest.spyOn(fs, 'existsSync').mockReturnValue(false);
    })

    ```

 # If you need to mock a function that you imported from another module you have in your code 

    Example of the module that you have been implemented, place in path './src/lib/util.ts'

    ```typescript
    
    export default async function getHello() {
        return Promise.resolve("Hello!")
    }
    
    ```

    Inside your test you will mock it like this:

    * In the imports zone you need add

    ```typescript
    import {jest} from '@jest/globals';
    jest.mock('./src/lib/util.ts');
    import getHello from './src/lib/util.ts'

    ```

    * Inside describe block

    ```typescript

    const mockGetHello = getHello as jest.MockedFunction<typeof getHello>;

    ```

    * Finally, inside the test

    ```typescript

    // Observe how we need to return as a Promise of string. We need to make sure it always returns
    // the same type than in the real function
    mockGetHello.mockImplementation(() => Promise.resolve('Hi!'))

    ```

# If you need to mock the same function, mocking to different results in the same test

    * First, lets mock the module imported in the imports area

    ```typescript
    impot {jest} from '@jest/globals'
    jest.mock('fs');
    import * as fs from 'fs';
    ```

    * Don't forget to clear all mocks before each test

    ```typescript
    beforeEach(() => {
        jest.clearAllMocks();
    })
    ```

    * We will use mockReturnValueOnce inside the test. The first time readFileSync is called, it will returned 'First reading', 
    and the second time it will returned 'Second reading'

    ```typescript
    jest.spyOn(fs, 'readFileSync')
        .mockReturnValueOnce('First reading')
        .mockReturnValueOnce('Second reading')
    ```

    * If you need to mock the same thing, but instead of mock a value, you need to mock a function, like throwing an error, you can use:

    ```typescript
    jest.spyOnd(fs, 'readFileSync') 
        mockImplementationOnce(() => 'First reading')
        mockImplmentationOnce(() => throw new Error('Error reading'))   
    ```

# If you want to mock the implementation of a class method, that it's instantiated inside the method that we are testing

    * First, as always, let's mock the class
    
    ```typescript

    import {mocked} from 'ts-jest';
    import OurClass from './OurClass'
    jest.mock('./OurClass');
    
    ```

    * Inside describe, we will called mocked from ts-jest to mock the class instance
    
    ```typescript

    const mockOurClass = mocked(OurClass);

    beforeEach(() => {
        mockOurClass.mockClear();    
    })

    ```

    * You can check inside the test, how the instantiation of the class is called

    ```typescript

    expect(OurClass).not.toHaveBeenCalled();

    ```

    * To mock the implementation of the class method

    ```typescript

    (mockOurClass as jest.MockInstance<any, any>).mockImplementation(() => {
        return {
            execute: () => Promise.resolve('Result you want to return has to be the same type thatn the one returned by execute method')
        }
    })

    ```
# If you want to check than an error has been thrown inside an async method

    ```typescript

    try {
        await methodThatThrowsError();
    }
    catch(e) {
        expect(e.message).toContain('Error message thrown');
    }

    ```
# If you want to check the content of a call to a function

    ```typescript

        // First, we spy the method whick call content we will check
        const consoleLogSpy = jest.spyOn('console', log);

        // Inside the test

        expect(consoleLogSpy).toHaveBeenCalledWith(expect.objectContaining(expectedContentValue));


    ```



               