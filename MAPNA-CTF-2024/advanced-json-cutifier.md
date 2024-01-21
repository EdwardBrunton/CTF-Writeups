# Advanced Json Cutifier

## Challenge Description

My homework was to write a JSON beautifier. Just Indenting JSON files was too boring that's why I decided to add some features to my project using a popular (More than 1k stars on GitHub!! ) library to make my project more exciting.

## Identifying the Hidden Library

While experimenting with the website, I discovered that the following JSON payload:

```json
{
   Error: error {}
}
```

Resulted in this error message:

```log
RUNTIME ERROR: { }
    ctf:2:11-19 object <anonymous>
    Field "Error"
    During manifestation
```

A Google search for "during manifestation" golang json led me to the go-jsonnet library. Its parsing function signature closely resembled that of the website:

```go
// Redacted library in code
jsonStr, err := REDACTED.REDACTED().REDACTED("ctf", string(buf[:]))

// go-jsonnet library
jsonStr, err := jsonnet.MakeVM().EvaluateAnonymousSnippet("ctf", string(buf[:]))
```

To confirm my hypothesis, I used this payload:

```json
{
   person1: {
    name: "Alice",
    welcome: "Hello " + self.name + "!",
   },
   person2: self.person1 { name: "Bob" },
}
```

Which returned:

```json
{
   "person1": {
      "name": "Alice",
      "welcome": "Hello Alice!"
   },
   "person2": {
      "name": "Bob",
      "welcome": "Hello Bob!"
   }
}
```

This confirmed the website's use of the go-jsonnet library.

## Retrieving the Flag

With this knowledge, I obtained the flag by importing it as a string:

```json
{
    file: importstr "./flag.txt"
}
```

The output was:

```text
MAPNA{5uch-4-u53ful-f347ur3-a23f98d}
```
