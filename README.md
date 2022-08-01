# Controller Class

## Learning Goals

- Create a controller class.
- Configure it to return JSON formatted response.
- Accept different client parameters.

## Introduction

The controller is where we will define which methods a client request is routed
to and what should be sent back as a response. Spring provides several
annotations to make it easy to manage requests, responses, and client
parameters.

## Add the Controller Class

Create a class called `MemberController` in the `controller` package and add the
following code:

```java
package org.example.springwebdemo.controller;

import org.example.springwebdemo.model.Member;
import org.example.springwebdemo.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api")
public class MemberController {
    @Autowired
    MemberService memberService;

    @PostMapping("/members")
    public Member createMember(@RequestBody Member member) {
        return memberService.createMember(member);
    }

    @GetMapping("/members")
    public List<Member> readMembers() {
        return memberService.getMembers();
    }

    @GetMapping("/members/{memberId}")
    public Member readMember(@PathVariable(value = "memberId") Integer id) {
        return memberService.getMember(id);
    }

    @PutMapping("/members/{memberId}")
    public Member updateMember(@PathVariable(value = "memberId") Integer id, @RequestBody Member memberData) {
        return memberService.updateMember(id, memberData);
    }

    @DeleteMapping("/members/{memberId}")
    public void deleteMember(@PathVariable(value = "memberId") Integer id) {
        memberService.deleteMember(id);
    }
}
```

### @RestController

The `@RestController` combines the following annotations:

- `@Controller`: Indicates to Spring that the class represents a controller.
- `@ResponseBody`: Configures Spring to return JSON response from controller
  methods instead of view templates which is the default.

### @RequestMapping

This annotation defines the base URL for the API. If our base URL is
`http://www.example.org` and we use `/api` as the value for `@RequestMapping`,
it would create the base API as `http://www.example.org/api`.

### MemberService Injection

The `MemberService` object is injected automatically by Spring into the
controller so we can use it in the class.

Now that we have talked about the class level annotations and the dependency, we
will look at the individual details and the annotations used.

## Running the Application

We are using Maven as the build tool and can run the following command to start
our application:

```
./mvnw spring-boot:run
```

We can also use the globally installed `mvn` tool to run the application:

```
mvn spring-boot:run
```

Both of these commands will start the web application on the localhost at port
`8080`. We can append the API endpoints defined in our controller to the default
`http://localhost:8080/` address to get the desired response from the
application. For example, we can make a `GET` request to
`http://localhost:8080/api/members` to retrieve a list of all the members in the
database.

## Post Method

```java
@PostMapping("/members")
public Member createMember(@RequestBody Member member) {
    return memberService.createMember(member);
}
```

This method is creating the `/api/members` endpoint for the `POST` HTTP method
and persisting the data sent from the client. The data from the client is a JSON
object like the following:

```json
{
	"name": "Jack"
	"email": "jack@example.com"
}
```

### @PostMapping

The `@PostMapping` annotation is a shorthand for the
`@RequestMapping(value="/members", method=RequestMethod.POST)` annotation. Both
of these annotations define the endpoint path as `/api/members`

### @RequestBody

This annotation allows the method to accept JSON formatted client data and
convert it to a Java object. For example, the JSON object mentioned above would
be mapped to a `Member` object with the `name` and `email` attributes set to the
values in the JSON.

## Get Methods

We will look at both of the `GET` methods in this section. Let’s start with the
first one.

### readMembers

```java
@GetMapping("/members")
public List<Member> readMembers() {
    return memberService.getMembers();
}
```

The `@GetMapping` is a shorthand for the
`@RequestMapping(value="/members", method=RequestMethod.GET)` annotation. Both
of these annotations define the endpoint path as `/api/members`.

The `readMembers` method calls the `memberService` to retrieve all the member
records from the database and returns a JSON like this:

```json
[
  {
    "id": 1,
    "name": "john",
    "email": "john@example.com"
  },
  {
    "id": 2,
    "name": "Liam",
    "email": "liam@example.com"
  }
]
```

### readMember

```java
@GetMapping("/members/{memberId}")
public Member readMember(@PathVariable(value = "memberId") Integer id) {
    return memberService.getMember(id);
}
```

The `{memberId}` in the `@GetMapping` value is a dynamic value which takes on
the value of the client’s request path parameter. For example, the following URL
would assign the value of `1` to `memberId` and `id`:

```json
http://localhost:8080/api/members/1
```

The `@PathVariable` annotation maps the dynamic path value to the method
parameter `id`.

## Update Method

```java
@PutMapping("/members/{memberId}")
public Member updateMember(@PathVariable(value = "memberId") Integer id, @RequestBody Member memberData) {
    return memberService.updateMember(id, memberData);
}
```

The `@PutMapping` is a shorthand for the
`@RequestMapping(value="/members/{memberId}", method=RequestMethod.PUT)`. This
method is using `@PathVariable` to get the ID of the record that needs to be
updated and `@RequestBody` to get the data sent by the client.

## Delete Method

```java
@DeleteMapping("/members/{memberId}")
public void deleteMember(@PathVariable(value = "memberId") Integer id) {
    memberService.deleteMember(id);
}
```

The `@DeleteMapping` is a shorthand for the
`@RequestMapping(value="/members/{memberId}", method=RequestMethod.DELETE)`
annotation. This method calls the `deleteMember` method on the `memberService`
class which removes the record with the given ID from the database.

## Conclusion

We have learned how to create the controller class, map routes to methods, and
get information from client requests. In the next lesson, we will learn how to
run the application and test the API endpoints.
