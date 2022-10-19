Go's `encoding/json` stdlib package with some minor enhancements that are very
hard achieve without modifying the json package.

This is a drop-in 100% compatible replacement.

Currently included patches:

[**proposal: encoding/json: add "readonly" tag**](https://github.com/golang/go/issues/28143)

Let's say you have a type like this:

    type Foo {
        ID     int    `json:"id"`
        UserID int    `json:"user_id"`
        Name   string `json:"name"`
    }

In a REST API, you will probably want to have:

    POST /foo      Create a new foo
    GET /foo/{id}  Get a foo by ID

The problem is that in the rest API you want to display all the fields in the
GET request, but *don't* want to allow people to set `ID` or `UserID` fields in
the POST request.

Turns out, this is kind of a tricky problem to solve. You can solve it by
creating a new type or a custom json.Unmarshaler, but there are various caveats
and it's tricky (see issue for details). With this change, you can add
`readonly` which tells `json.Unmarshal` to never set these fields:

    type Foo {
        ID     int    `json:"id,readonly"`
        UserID int    `json:"user_id,readonly"`
        Name   string `json:"name"`
    }
