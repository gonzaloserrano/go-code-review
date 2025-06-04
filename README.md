# Go Code Review

An informal [Go programming language](https://go.dev/) code review guidelines index.

## General References

#### Official
- [Style Guide](https://google.github.io/styleguide/go/)
- [Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
- [Effective Go](https://golang.org/doc/effective_go.html)
- Go Proverbs: [Web](https://go-proverbs.github.io/) - [Video](https://www.youtube.com/watch?v=PAAkCSZUG1c)

#### 3rd Party

- [Go Best Practices](https://peter.bourgon.org/go-best-practices-2016/), [Go for Industrial Programming](https://peter.bourgon.org/go-for-industrial-programming/) - [Video](https://www.youtube.com/watch?v=PTE4VJIdHPg) by Peter Bourgon
- [Ultimate Go Design Guidelines](https://github.com/ardanlabs/gotraining/blob/master/topics/go/README.md) by Ardan Labs
- [Idiomatic Go](https://about.sourcegraph.com/go/idiomatic-go) by Sourcegraph
- [The Zen of Go](https://dave.cheney.net/2020/02/23/the-zen-of-go) by Dave Cheney
- [Practical Go: Real World Advice for Writing Maintainable Go Programs](https://dave.cheney.net/practical-go/presentations/qcon-china.html) by Dave Cheney
- [Uber's Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md)
- [Awesome Go Style](https://github.com/dgryski/awesome-go-style)

## About Code Reviewing, Git & GitHub

- The [Guidelines for Faster PR Reviews](https://github.com/kubernetes/community/blob/master/contributors/guide/pull-requests.md#best-practices-for-faster-reviews) from the Kubernetes project are a must. A quick summary:
    - Do small commits and, even better, small PRs.
    - Use separate PRs for fixes not related to your feature.
    - Add a different commit for review changes so it's easy to review them instead of the whole patch.
    - Test and document your code.
    - Don't add features you don't need.
- Other guidelines:
    - [How to Write a Git Commit Message](https://cbea.ms/git-commit)
    - [Use a Global .gitignore](https://gist.github.com/subfuzion/db7f57fff2fb6998a16c) to avoid coupling personal environment's files. Use a [.gitignore Generator](https://github.com/toptal/gitignore) when possible.
    - Prefer documentation as code (example test files) over READMEs.
    - Choose a good GitHub merge strategy:
      - Choose merge when:
          - Multiple commits.
          - Deps in different commit.
      - Choose squash if you just have a single commit to avoid the extra merge commit.
    - Have a proper Continuous Integration (CI) to ensure code quality:
        - Tests are in green (don't forget the `-race` flag).
        - New code or changes in functionality have the corresponding tests (e.g., `gocovmerge` + codecov.io or coveralls.io).

## Linting

Just use [golangci-lint](https://golangci-lint.run/). The defaults are fine. The more linters you enable, the more you can avoid nitpicks in PR reviews.

As a formatter, I personally prefer [gofumpt: a stricter gofmt](https://github.com/mvdan/gofumpt).

## Design

In companies I worked with that had rich domains, such as video games and logistics, I've seen really good results applying techniques such as Test-Driven Development, Domain-Driven Design, and Command-Query Separation. You can read about it in [this presentation](https://present.gon.cat/uservices.slide#1).

Other stuff:

- Middleware Pattern:
  - See [this go-kit presentation example](https://youtu.be/NX0sHF8ZZgw?t=1344) by Peter Bourgon.
  - And [Embrace the Interface](https://www.youtube.com/watch?v=xyDkyFjzFVc) video by Tomas Senart ([@tsenart](https://twitter.com/tsenart)).
  - [Another good blog post with examples](https://medium.com/@matryer/writing-middleware-in-golang-and-how-go-makes-it-so-much-fun-4375c1246e81).
- Example: [Coupling with Logger, Metrics, etc](https://peter.bourgon.org/go-best-practices-2016/#logging-and-instrumentation):
  - Don't log everything, maybe using metrics is better ([blog post by @copyconstruct](

https://medium.com/@copyconstruct/logs-and-metrics-6d34d3026e38)).
  - [The RED Method](https://www.weave.works/blog/the-red-method-key-metrics-for-microservices-architecture/) for choosing metrics.
- [Code is Communication](https://talks.golang.org/2014/readability.slide#44).
- [API Design](https://talks.golang.org/2014/readability.slide#42)
    - [Make Your APIs Sync](https://about.sourcegraph.com/go/idiomatic-go/#asynchronous-apis).
- [Always Write the Simplest Code You Can](http://go-talks.appspot.com/github.com/davecheney/presentations/writing-high-performance-go.slide?utm_source=statuscode&utm_medium=medium#43).
- Structs with just one field: `has-a` -> `is-a`.
- Avoid [Primitive Obsession - C#](http://enterprisecraftsmanship.com/2015/03/07/functional-c-primitive-obsession/).
- [Accept Interfaces, Return Concrete Types](http://idiomaticgo.com/post/best-practice/accept-interfaces-return-structs/).
- [DDD in Go](https://gist.github.com/abdullin/3e3fd199674255e4d206).
- [Think About Using Functional Options in Your Constructors](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis).

## Package Design

- Learn and follow the stdlib design; read the official doc about [Organizing a Go Module](https://go.dev/doc/modules/layout).
- Think twice whether your exported types really need to be exported.
- Simplify by reducing as much the number of package dependencies: sometimes [duplication is better than the wrong abstraction](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction?duplication).

Other references:

- Ben Johnson's article on [Standard Package Layout](https://medium.com/@benbjohnson/standard-package-layout-7cdbc8391fc1).
- Bill Kennedy's resources:
  - [Blog Article](https://www.goinggo.net/2017/02/design-philosophy-on-packaging.html).
  - [GopherCon India '17 Video](https://www.youtube.com/watch?v=spKM5CyBwJA).
- [Style Guides for Go Pkgs - @rakyll](https://rakyll.org/style-packages):
    - Use multiple files.
    - Keep types closer to where they are used.
    - Organize by responsibility (e.g., avoid package `model`).

## Error Design

- [Design for Errors - Railway Oriented Programming](https://hackmd.io/JwDgbGDMBmAMBGBaArMA7GRAWATARgFNFgdoRFYwATdXEAQwGNGqg===).
  - https://dave.cheney.net/2015/01/26/errors-and-exceptions-redux.
- [Indent Error Flow: Early Returns](https://github.com/golang/go/wiki/CodeReviewComments#indent-error-flow).
  - [Talk: Keep the normal code path at a minimal indentation](https://talks.golang.org/2014/readability.slide#27).
- [Errors shouldn't be capitalized](https://github.com/golang/go/wiki/CodeReviewComments#error-strings).
- Prefer testing the public API of your package with the test code in a different package (`package whatever_test`).
- Modeling:
  - For composition, don't use 3rd party libs since Go 1.13 has wrapping built-in.
    - For multierror support, use [Hashi's lib](https://github.com/hashicorp/go-multierror) or build it yourself. A stdlib implementation [has been implemented](https://github.com/golang/go/issues/53435#issuecomment-1254255280) but not published yet (as of 1.19.1).
  - Prefer modeling your own errors as values or types; avoid coupling tests to error messages, use `errors.Is/As` instead.
  - Prefer wrapping for returning errors you don't own (check [wrapcheck](https://github.com/tomarrell/wrapcheck) linter).
- [Error Handling Guidelines](https://www.goinggo.net/2017/05/design-philosophy-on-logging.html) by Bill K.
  - Handling an error means:
    - The error has been logged.
    - The application is back to 100% integrity.
    - The current error is not reported any longer.
- An `http.Handler` shouldn't repeat sending an errored response more than once; separate the domain logic from the HTTP layer.
- Don't panic, return errors - especially in libs. For tests or main functions, you can panic, but use function names with `Must` prefix.

## Concurrency

- Coverage: at least happy path. Think about important error paths too.
- Read [Golang Concurrency Tricks](https://udhos.github.io/golang-concurrency-tricks/).
- Go makes concurrency easy enough to be dangerous [source](https://www.youtube.com/watch?v=DJ4d_PZ6Gns&t=1270s).
- Shared mutable state is the root of all evil [source](http://henrikeichenhardt.blogspot.com.es/2013/06/why-shared-mutable-state-is-root-of-all.html).
- How to protect shared mutable data:
  - With Mutex/RWMutex:
    - Theory in Spanish: [El Problema de los Lectores/Escritores](https://github.com/gallir/libro_concurrencia/blob/master/chapters/06-semaforos.adoc#lectores-escritores).
    - `mu sync.Mutex` over the fields it protects.
    - Use `RWMutex` if you have (significantly?) more reads than writes.
  - Or avoid sharing state and use message passing via channels:
    - [Channel Design](https://github.com/ardanlabs/gotraining/tree/master/topics/go#channel-design).
      - Suspect of buffered channels.
    - [Principles of Designing APIs with Channels](https://www.youtube.com/watch?v=hFqXgmor74k).
  - Or use the `atomic` pkg if your state is a primitive (`float64` for e.g.):
    - That's a nice thing for testing using spies in concurrent tests for e.g.
  - Or use things like `sync.Once` or `sync.Map` (>= Go 1.9).
- Watch out when:
  - [Using Goroutines on Loop Iteration Variables](https://go.dev/wiki/CommonMistakes#using-goroutines-on-loop-iterator-variables).
  - Lack of concurrent test logic for concurrent Subject Under Tests.
  - You see async fire-and-forget logic that does not handle errors.
  - There isn't a clear explanation on why the concurrent code is faster than serial.
- Goroutine Lifetime:
  - Watch out for leaks: lifetime must be always clear, e.g., terminate a `for-select` subscribing to context.Done() to finish return.
  - [More from official core review](https://github.com/golang/go/wiki/CodeReviewComments#goroutine-lifetimes).
  - [More from Dave Cheney](http://go-talks.appspot.com/github.com/davecheney/presentations/writing-high-performance-go.slide?utm_source=statuscode&utm_medium=medium#35).
- If you write libs, leave concurrency to the caller when possible. See [Zen Of Go](https://the-zen-of-go.netlify.app/).
    - Your code will be simpler, and the clients will choose the kind of concurrency they want.
- Refs:
  - [Channel Axioms](https://dave.cheney.net/2014/03/19/channel-axioms)
  - [The Channel Closing Principle](http://www.tapirgames.com/blog/golang-channel-closing): don't close a channel from the receiver side and don't close a channel if the channel has multiple concurrent senders.
  - [Pipelines and Cancellation](https://blog.golang.org/pipelines).
  - [Advanced Concurrency Patterns](https://blog.golang.org/advanced-go-concurrency-patterns).
  - Mutex contention [profiling](https://talks.golang.org/2017/state-of-go.slide#23) [tips](http://golang.rakyll.org/mutexprofile/) from Jaana B Dogan.

## HTTP

- Don't use `http.ListenAndServe`. Use `http.Server{}` with good defaults as explained in [How to Handle HTTP Timeouts in Go](https://blog.cloudflare.com/the-complete-guide-to-golang-net-http-timeouts/).
- An HTTP client [must close the response body](https://golang.org/pkg/net/http/#pkg-overview) when finished reading with it.
  - [Closing the body must be done after error checking](https://stackoverflow.com/a/42525774/547956).
- When creating HTTP-based libs, allow injecting an `http.Client`.

## Naming

Be careful when using short names. Usually, there is no problem with the receivers' names or temporary variables like indexes in loops, etc. But if your funcs violate the SRP or you have many args, then you can end up with many short names and it can do more harm than good. If understanding a var name is not straightforward, use longer names.

Other refs:
  - [Avoid GetSomething Getter Name](https://golang.org/doc/effective_go.html#Getters).
  - [Named Result Parameters](https://github.com/golang/go/wiki/CodeReviewComments#named-result-parameters).
  - [Initialisms - ID, HTTP, etc](https://github.com/golang/go/wiki/CodeReviewComments#initialisms).
  - [Receiver Name](https://github.com/golang/go/wiki/CodeReviewComments#receiver-names).
    > Don't name result parameters just to avoid declaring a var inside the function.
    
## Testing

[Testing on the Toilet](https://gerlacdt.github.io/posts/google-testing-toilet/) short articles by Google have tons of wisdom.

[Go Testing By Example](https://youtu.be/1-o-iJlL4ak?si=v4C6DFLlrYBLB1nt) presentation from Russ Cox (Go technical lead) is also great. Its best tips in my opinion are:
- Make it easy to add new test cases, by using table-driven tests.
- If you didn't add a test, you didn't fix the bug.
- Code quality is limited by test quality.

Other guidelines:

- Use coverage to find gaps or edge cases.
- Any change in behavior ideally should have a test that covers it; if you do TDD, the test implementation comes first.
- Scope: if tests are complicated think about
  - Refactoring the code.
  - Refactoring the tests.
  - Adding arrange / act / assert comments, if needed.
- Types of tests:
  - Unit.
  - Internal.
  - Integration.
  - Example files.
  - Benchmarks.
    - [The Right Way](https://docs.google.com/presentation/d/e/2PACX-1vQ9aFgICdqCz5pjrVJ4zFZrWtTKbfGYFPCOKsScomkLoE1Kzk3DVd9-u4k_XgZekqJ7nl-YTy4lD8Uq/pub?slide=id.g550f852d27_228_0) by [Daniel Martí](https://twitter.com/mvdan_).
- Subtests: see concurrency.
- Tags: if you have tagged some tests (e.g., _// +build integration_) then you need to run your tests with `-tags`.
- Use `t.Run` and `t.Parallel`.
- De-flaking unit tests, by the [Kubernetes project](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-testing/flaky-tests.md#deflaking-unit-tests).

Test doubles naming (from [The Little Mocker](https://8thlight.com/blog/uncle-bob/2014/05/14/TheLittleMocker.html) by Uncle Bob):
  - Dummy: objects are passed around but never actually used.
  - Fake: objects actually have working implementations, but usually take some shortcut that makes them not suitable for production (an InMemoryTestDatabase is a good example).
  - Stubs: provide canned answers to calls made during the test, usually not responding at all to anything outside what's programmed in for the test.
  - Spies: stubs that also record some information based on how they were called. One form of this might be an email service that records how many messages it was sent.
  - Mocks: pre-programmed with expectations that form a specification of the calls they are expected to receive. They can throw an exception if they receive a call they don't expect and are checked during verification to ensure they got all the calls they were expecting.

## Performance

- [Receiver Type: Value or Pointer?](https://github.com/golang/go/wiki/CodeReviewComments#receiver-type).
- [Writing High-Performance Go Talk](http://go-talks.appspot.com/github.com/davecheney/presentations/writing-high-performance-go).
- [Escape Analysis and Memory Profiling - Bill Kennedy @ GopherCon SG 2017](https://www.youtube.com/watch?v=2557w0qsDV0).
- From [So You Wanna Go Fast?](https://youtu.be/DJ4d_PZ6Gns?t=2193) talk by Tyler Treat:
  - The stdlib provides general solutions, and you should generally use them.
  - Small, idiomatic changes can have a profound performance impact.
  - Learn and use tools from the Go toolchain to analyze the performance.
  - The performance can change a lot between Go versions, review your optimizations.
  - Code is marginal, architecture is material: the big wins come from architecture, do it right first.
  - Mechanical sympathy: know how your abstractions work in your hardware; Go makes this possible.
  - Optimize for the right trade-off: optimizing for performance means trading something else.

## Gotchas

- [50 Shades of Go](http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang/).
- A `time.Ticker` must be stopped, otherwise, a goroutine is leaked.
- Slices hold a reference to the underlying array as explained here and here. Slices are passed by reference; maybe you think you are returning small data but its underlying array can be huge.
- Interfaces and nil gotcha, see the [Understanding Nil](https://www.youtube.com/watch?v=ynoY2xz-F8s) talk by Francesc Campoy.

## Nitpicks
- How to add EOL at EOF. [See why](https://stackoverflow.com/questions/729692/why-should-text-files-end-with-a-newline/729795#729795).
  - [VSCode](https://stackoverflow.com/questions/44704968/visual-studio-code-insert-newline-at-the-end-of-files).
  - [JetBrains' IDEs](https://www.jetbrains.com/help/idea/configuring-line-endings-and-line-separators.html) such as GoLand:
    - `Preferences > Editor > General > Ensure every saved file ends with a line break`.
  - [Sublime Text](https://www.shellhacks.com/sublime-text-add-newline-at-eof-on-save/).
  - Vim: by default, it's on. [Read this](http://ftp.vim.org/vim/patches/7.4/7.4.785) to disable.
