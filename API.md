# Reform API docs

> Autogenerated from source code

# index <small>Module [src](./src/index.ts#L1)</small> <a id="#267"></a>

## Reform <small>Object literal [src](./src/index.ts#L29)</small> <a id="#268"></a>
`default` export for the entire library.

```javascript
// using the default export
import Reform from '@franleplant/reform'

// importing only the needed parts
import { reactMixins } from '@franleplant/reform'

```
<big><pre>
{
core: [./src/core](#user-content-#178)
reactHelpers: [./src/reactHelpers](#user-content-#201)
reactMixins: [./src/reactMixins](#user-content-#241)
types: [./src/types](#user-content-#1)
validators: [./src/validators](./src/validators)
}
</big></pre>
# core <small>Module [src](./src/core.ts#L1)</small> <a id="#178"></a>

## fieldIsValid <small>Function [src](./src/core.ts#L69)</small> <a id="#185"></a>

Evaluate whether a field is valid or not.

This function is a lightweight wrapper of `validateField`.
It has two variants, as listed below.
The first one is not so different from `validateField`, the only difference is that
it will return whether that field is valid (all values in `FieldErrors` are false)
or is invalid (at least one value in `FieldErrors` is true).

The second variant accepts an already calculated `FieldErrors` and just check
if all values are false (valid field) or some are true (invalid field).
This is useful if you already calculated `FieldErrors` in another event. In this
way you can avoid re calculating them again each time you want to know if the field is valid.

<big><pre>
fieldIsValid(value: string | number, rules: [Rules](#9)): boolean
</pre></big>




<big><pre>
fieldIsValid(fieldErrors: [FieldErrors](#15)): boolean
</pre></big>

## formIsValid <small>Function [src](./src/core.ts#L119)</small> <a id="#195"></a>

Analogous to `fieldIsValid` but operating for forms. There are also
two variants, the first one accepts values and rules and calculates the
`formErrors` and then check that all fields are valid, and the second
one accepts an already calculated `formErrors`.

<big><pre>
formIsValid(fieldsValues: [Fields](#21), rulesMap: [RulesMap](#12)): boolean
</pre></big>




<big><pre>
formIsValid(formErrors: [FormErrors](#18)): boolean
</pre></big>

## validateField <small>Function [src](./src/core.ts#L41)</small> <a id="#181"></a>

Validate `value` against `rules` and return which rules are valid with a value `false`
and which rules ar invalid with a value of `true`.

One of the central functions in `Reform.core`.
Accepts inline, ad-hoc, validators as well as available ones (`required`, `pattern`, etc).

Example

```javascript
// a required empty value should be invalid.
const fieldErrors = validateField('', {required: true});
assert(fieldErrors, {required: true});

// an invalid email value
const fieldErrors = validateField('not an email', {email: true});
assert(fieldErrors, {email: true});
// a valid email value
const fieldErrors = validateField('email@domain.com', {email: true});
assert(fieldErrors, {email: false});

// And of course you can combine them
const fieldErrors = validateField('email@domain.com', {required: true, email: true});
assert(fieldErrors, {required: false, email: false});
```

The most important part is that the result of this function, which is of the type `FieldErrors`
will be an object that has rule names as keys with boolean values. If the value is `true` it means
that there is an error, otherwise, it does not have an error, and that rule is passing.

<big><pre>
validateField(value: string | number, rules: [Rules](#9)): [FieldErrors](#15)
</pre></big>

## validateForm <small>Function [src](./src/core.ts#L100)</small> <a id="#191"></a>

A simple generalization of `validateField` but for an entire form.
It will basically run `validateField` on each `value` and each `rules`
indexed by `fieldName` and return `FormErrors` which is, an object that has
fieldNames as keys and `FieldErrors` as values.

<big><pre>
validateForm(fieldsValues: [Fields](#21), rulesMap: [RulesMap](#12)): [FormErrors](#18)
</pre></big>

# reactHelpers <small>Module [src](./src/reactHelpers.ts#L1)</small> <a id="#201"></a>

## fieldErrors <small>Function [src](./src/reactHelpers.ts#L213)</small> <a id="#233"></a>

Another error helper to make displaying field errors easier. Instead of `fieldIfError` which
is more procedural, use this helper if you have an structured way of displaying your errors.

It returns an Array of Failed Rules.
Each Failed Rule will be, in turn, an array of the form `['ruleName', 'ruleArgument']`.

Where `ruleName` will be the name of a failed rule, for example `required`, `minLenght, etc.
And `ruleArgument` will be the value of the rule, as defined by the user. For example `true`, '6', etc, respectively.

This will enable you to create functions, or components, that will render your errors _automatically_ for you.

Check mapFieldErrors for an even more easier way.

```javascript
const errors = this.fieldErrors('myField')
assert(errors, [['required', true], ['minLenght', 6], ['pattern', 'banana|apple'])
```


Tags

- unstable

<big><pre>
fieldErrors(this: [ValidationAbleInstance](#24), fieldName: string): Array\<Array\<any\>\>
</pre></big>

## fieldIfError <small>Function [src](./src/reactHelpers.ts#L174)</small> <a id="#228"></a>

Simple helper to make conditional displaying field errors more ergonomic.

Use it if to render field errors only if that field has a particular failed rule.

```javascript
{ this.fieldIfError('myField', 'required') && <span> myField is required! </span>
```

This function is purely for ergonomic purposes.

<big><pre>
fieldIfError(this: [ValidationAbleInstance](#24), fieldName: string, errorKey: string): boolean
</pre></big>

## fieldIsValid <small>Function [src](./src/reactHelpers.ts#L141)</small> <a id="#221"></a>

Calculate whether a field is valid or not depending on the already calculated
`fieldErrors` stored in the state.

Use it to render _invalid_ state in your inputs.

**Important** this function will **not re calculate your field's validity**

<big><pre>
fieldIsValid(this: [ValidationAbleInstance](#24), fieldName: string): boolean
</pre></big>

## formIsValid <small>Function [src](./src/reactHelpers.ts#L155)</small> <a id="#225"></a>

Calculate the form's validity from the `values` in `this.state.fields` and
the rules in `this.validationRules`.

This function, in contrast to `fieldIsValid`, **will effectively re-calculate your form's validity**

Use it to disable the submit button, or to prevent `onSubmit` callback from normal
processing of the form.

<big><pre>
formIsValid(this: [ValidationAbleInstance](#24)): boolean
</pre></big>

## mapFieldErrors <small>Function [src](./src/reactHelpers.ts#L262)</small> <a id="#237"></a>

An abstraction of `fieldErrors` for structured field error rendering.
Use it if you have a very standard way of displaying field errors.

This function will use the `MessageCreator` map defined in `this.validationMessages`.
It will map over the failed rules of a given field and create messages for you to display
how ever you want.

```javascript
//Define your validationMessages
this.validationMessages = {
// Define a per-rule message creator
minLenght: (ruleValue, ruleKey, fieldName) => `${fieldName} must have at least ${ruleValue} characters`
// Define a fall back for missing message creators
default: (ruleValue, ruleKey, fieldName) => `{fieldName} is invalid according to rule ${ruleKey}: ${ruleValue}`
}

// Example validationRules
this.validationRules = {
myField: {minLength: 6}
}

// Use inside your render function
{this.mapFieldErrors('myField')
.map(message => (
<span>{message}</span>
))
}

//In our example it will render something like this
<span>myField must have at least 6 characters</span>
```


Tags

- unstable

<big><pre>
mapFieldErrors(this: [ValidationAbleInstance](#24), fieldName: string): Array\<string\>
</pre></big>

## validateField <small>Function [src](./src/reactHelpers.ts#L65)</small> <a id="#205"></a>

Calculate the validity of a field, by `fieldName`, with a new value and store it in the
`this.state.errors[fieldName]`

Will return, to improve usability, whether that field is valid or not.

Use it if you are validating `onChange`, because `this.state.fields[fieldName]` (field's value)
wont be already set.


**Important**: This function needs to be executed in the context of a `ValidationAbleInstance`
and will **not** save the `value` of the field in the state, that's your job.

You can either use `Reform.reactMixins` to automatically bind this function to your instance (`this` inside your Component)
and use them as regular methods, or you can use `bind`, `apply` and `call` to run them as functions.

```javascript
// Create a new bounded function (very similar to what `reactMixins` do)
const boundedFunction = Reform.reactHelpers.validateField.bind(this)

// Run it in the proper context
const isValid = Reform.reactHelpers.validateField.call(this, 'myField', 'new value');
```

Ultimately, the way you use it does not matter as long as it fits your style.
However, if you are undecided, I suggest you start by using `React.reactMixins`

**Important** this function **will modify your component state**

This function will also set your form to `dirty` in `this.state.formIsDirty`

<big><pre>
validateField(this: [ValidationAbleInstance](#24), fieldName: string, value: any): boolean
</pre></big>

## validateFieldFromState <small>Function [src](./src/reactHelpers.ts#L89)</small> <a id="#210"></a>

A minimal variant of `validateField` but with the particularity of using the `value` already set in
your component's state.

Use it if you are validating **after** updating your state, i.e.: `onBlur`, `onSubmit` et al,

**Important** this function **will modify your component's state**

This function will also set your form to `dirty` in `this.state.formIsDirty`

<big><pre>
validateFieldFromState(this: [ValidationAbleInstance](#24), fieldName: string): boolean
</pre></big>

## validateForm <small>Function [src](./src/reactHelpers.ts#L104)</small> <a id="#214"></a>

Calculate the validity of your form from `fieldsValues` as parameters, update your state, and
return whether your form is valid or not.

Unless you have a very specific use case, most of the time you should be using `validateFormFromState`.

**Important** this function **will modify your component's state**

This function will also set your form to `dirty` in `this.state.formIsDirty`

<big><pre>
validateForm(this: [ValidationAbleInstance](#24), fieldsValues: [Fields](#21)): boolean
</pre></big>

## validateFormFromState <small>Function [src](./src/reactHelpers.ts#L127)</small> <a id="#218"></a>

Calculate the validity of your form from `fieldsValues` as parameters, update your state, and
return whether your form is valid or not.

Use it `onSubmit`.

**Important** this function **will modify your component's state**

This function will also set your form to `dirty` in `this.state.formIsDirty`

<big><pre>
validateFormFromState(this: [ValidationAbleInstance](#24)): boolean
</pre></big>

# reactMixins <small>Module [src](./src/reactMixins.ts#L1)</small> <a id="#241"></a>

## Base <small>Interface [src](./src/reactMixins.ts#L23)</small> <a id="#252"></a>

<big><pre>
{
}
</big></pre>
## GenericClass <small>Interface [src](./src/reactMixins.ts#L24)</small> <a id="#253"></a>

<big><pre>
{
;
displayName: string;
prototype: T
}
</big></pre>
## Reform <small>Interface [src](./src/reactMixins.ts#L10)</small> <a id="#242"></a>

<big><pre>
{
fieldErrors: [fieldErrors](#233);
fieldIfError: [fieldIfError](#228);
fieldIsValid: [fieldIsValid](#221);
formIsValid: [formIsValid](#225);
mapFieldErrors: [mapFieldErrors](#237);
validateField: [validateField](#205);
validateFieldFromState: [validateFieldFromState](#210);
validateForm: [validateForm](#214);
validateFormFromState: [validateFormFromState](#218)
}
</big></pre>
## classMixin <small>Function [src](./src/reactMixins.ts#L31)</small> <a id="#260"></a>



<big><pre>
classMixin(base: [GenericClass](#253)\<T\>): [GenericClass](#253)\<T & Reform\>
</pre></big>

## functionalMixin <small>Function [src](./src/reactMixins.ts#L55)</small> <a id="#264"></a>



<big><pre>
functionalMixin(instance: any): void
</pre></big>

# validators <small>Module [src](./src/validators.ts#L1)</small> <a id="#169"></a>

## validatorInterface <small>Object literal [src](./src/validators.ts#L4)</small> <a id="#170"></a>

<big><pre>
{
get: get(key: string): [Validator](#2)
set: set(key: string, value: [Validator](#2)): void
}
</big></pre>
# types <small>Module [src](./src/types.ts#L1)</small> <a id="#1"></a>

## FieldErrors <small>Interface [src](./src/types.ts#L17)</small> <a id="#15"></a>

<big><pre>
{
\[ruleKey: string\]: boolean
}
</big></pre>
## Fields <small>Interface [src](./src/types.ts#L25)</small> <a id="#21"></a>

<big><pre>
{
\[fieldName: string\]: any
}
</big></pre>
## FormErrors <small>Interface [src](./src/types.ts#L21)</small> <a id="#18"></a>

<big><pre>
{
\[fieldName: string\]: [FieldErrors](#15)
}
</big></pre>
## Rules <small>Interface [src](./src/types.ts#L9)</small> <a id="#9"></a>

<big><pre>
{
\[ruleKey: string\]: boolean | string | [Validator](#2) | any
}
</big></pre>
## RulesMap <small>Interface [src](./src/types.ts#L13)</small> <a id="#12"></a>

<big><pre>
{
\[fieldName: string\]: [Rules](#9)
}
</big></pre>
## ValidationAbleInstance <small>Interface [src](./src/types.ts#L29)</small> <a id="#24"></a>

<big><pre>
{
setState: any;
state: {
  errors: [FormErrors](#18);
  fields: [Fields](#21);
  formIsDirty: boolean
};
validationMessages: {
  \[ruleKey: string\]: [MessageCreator](#36)
};
validationRules: [RulesMap](#12)
}
</big></pre>
## Validator <small>Interface [src](./src/types.ts#L1)</small> <a id="#2"></a>

<big><pre>
{
}
</big></pre>
## ValidatorMap <small>Interface [src](./src/types.ts#L5)</small> <a id="#6"></a>

<big><pre>
{
\[ruleKey: string\]: [Validator](#2)
}
</big></pre>

# officialValidators/index <small>Module [src](./src/officialValidators/index.ts#L1)</small> <a id="#124"></a>

## isNumber <small>Function [src](./src/officialValidators/index.ts#L18)</small> <a id="#125"></a>



<big><pre>
isNumber(value: any): boolean
</pre></big>

## validatorMap <small>Object literal [src](./src/officialValidators/index.ts#L20)</small> <a id="#128"></a>

<big><pre>
{
maxDate: Function
maxMonth: Function
maxNumber: Function
maxTime: Function
maxWeek: Function
minDate: Function
minMonth: Function
minNumber: Function
minTime: Function
minWeek: Function
month: Function
number: [Validator](#2)
range: [Validator](#2)
time: Function
url: Function
week: Function
color: color(value: any): boolean
date: date(value: any): boolean
email: email(value: string): boolean
maxLength: maxLength(value: string, maxLength: number): boolean
minLength: minLength(value: string, minLength: number): boolean
pattern: pattern(value: string, re: RegExp): boolean
required: required(value: any): boolean
}
</big></pre>
# utils <small>Module [src](./src/utils.ts#L1)</small> <a id="#48"></a>

String
## parseMonth <small>Function [src](./src/utils.ts#L2)</small> <a id="#49"></a>



<big><pre>
parseMonth(value: string): number
</pre></big>

## parseTime <small>Function [src](./src/utils.ts#L46)</small> <a id="#59"></a>



<big><pre>
parseTime(time: string): number
</pre></big>

## parseWeek <small>Function [src](./src/utils.ts#L16)</small> <a id="#52"></a>



<big><pre>
parseWeek(value: string): number
</pre></big>

## weeksInYear <small>Function [src](./src/utils.ts#L35)</small> <a id="#55"></a>



<big><pre>
weeksInYear(year: number): number
</pre></big>
