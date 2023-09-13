# Basics of typescript


## Data Model

The samples are based on [JavaScriptSphere: How to model your crazy data in TypeScript by Michał Michalczuk](https://www.youtube.com/watch?v=HQ-7ldlWXmY) this example shows

### What means `structural types`
This [samples](https://www.typescriptlang.org/play?ssl=19&ssc=12&pln=1&pc=1#code/C4TwDgpgBAChBOBnA9gOygXigbwFBQKgDMBLJYVAQwFtoAuKRYeE1AcylwF9ddRIoAUWpgANshARoWPIWJkmVWlAZMW7TnLDxk1ZMAgMAFAEpMAPigA3ZCQAmAbm69REYFAgiVQkeMnScfEJSciV6KAByABVdCIAaIIJtXX1DKFMLdOwuE24nXAB6AqgAd2R4AGtEWAQUdBJqxABXACNENyhkIh8xCSlcAGM0JihIJAY4JDRMDxF8otK0CPdmSlREMBJXUZ09EgMoBqhqBsRWNlxPMBmxxHygA) demonstrates what `structural types` allow you to do
```ts
type Person = {
    firstname : string 
}

type Employee = {
    firstname : string 
    promote: () => void;
}

let emp : Employee = {
    firstname : 'Tom',
    promote: () => ({})
};

// works Person is subset of Employee
const pers: Person = emp;

// won't transpile promoite is missing
emp = pers;
```
