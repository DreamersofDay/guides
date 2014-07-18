# SOA at AlphaSights

## What is the purpose of this document?

SOA is a loaded concept. There is a lot of history behind that little acronym
and I want us to be clear as a company how we define SOA, it's benefits,
potential pit falls.

## What we want SOA to accomplish for us

SOA, if we do it right, should provide us many benefits including but not
limited to:
  * Increased agility
  * Increased maintainability
  * Make it easier to see the big picture
  * Align business workflows with the system
  * Give us a ubiquitous language for business and technology

Later, I will go on to describe how I think these benefits can be accomplished.

## What SOA is not for AlphaSights

SOA, with it’s legacy, brings along with it a lot of baggage. We need to
explicitly say what we are not talking about.

A quick aside about vendors: I’ll also say that a vendor is trying to sell us as
“SOA” in a box is snake oil. It doesn’t exist and never will. SOA are specific
to businesses. There is no one size fits all. Vendors make money off of
middleware for middleware for middleware and they just want to sell you as much
as possible (and support contracts of course). _[Rant over]_

Many service oriented technologies have been tried over the years in SOA, most
of them have failed or been deprecated by something better. Many of them sounded
like good ideas at the time, but as time progressed, they were proven to be

The following technologies should not be part of our SOA. Not only that, but as
we are building, we should avoid making the same mistakes that they did. That
does not mean we will not potentially have to connect to these services, but it
does mean that we should not use them internally. I’ve also included reasons why
we shouldn’t be using them or why they were problems back in the day.

#### DCOM (Distributed Component Object Model)
* Microsoft
* From the 90’s. No one uses it. It’s only in the list for the sake of
  completeness.
* Shares objects across services.

#### CORBA (Common Object Request Broker Architecture)
* Again, from the 90’s. Old. No one uses it anymore. It’s only here from
  completeness.
* Creates tight coupling between systems due to the shared object space
  (lessons learned).

#### SOAP and WS-*
* XML based (overly verbose).
* The benefits that you used to get (enveloping) have been supplanted by REST. REST is
  simpler. Modern web development frameworks are capable of creating RESTful
  resources.
* Would scare potential job candidates away.

#### RPC (Remote Procedure Calls)
* Better than CORBA, but has similar issues. You’re not sharing the remote
  objects anymore, but you are sharing quite a bit of knowledge.
* Introduces tight coupling between systems. Disparate systems that don’t
  need to know about each other start to know a lot about each other
  including where they are, parameters they take, etc. Multiple types of
  connascence going on.
* Maintenance costs are high due to tight coupling. If you change one
  service, you end up having to go around changing the other services to
  conform.

#### ActiveResource
* Again, ActiveResource is a cool concept, but being able to talk to remote
  systems like they are local, at medium to large scales, leads to
  maintenance issues in the long run.
* The tight coupling with ActiveResource is a sign that you have drawn the
  lines between services incorrectly.

### Notes on services that touch each other too much
It’s worth noting that DCOM, CORBA, and RPC are three technologies that failed
because of type coupling of the object model between services. Specifically, I
think they failed because they did not take into account common patterns in
object design. Even though they are services, not objects, I think some of the
same basic principles apply. Most notably:

* Don’t Repeat Yourself (DRY)
  * These services were not drying because domain model definitions were
    repeated over systems. Component level concepts, especially workflow
    knowledge, were shared.
* Single Responsibility Principle
  * Again, this has to do with shared knowledge of workflows.
  * Services also know a lot about each other. They knew where the services live,
    what their local object space looked like, what arguments method took, etc. There was little encapsulation so separation between services was fuzzy at best.

I think ActiveResource follows down the same paths as these other technologies
and it should be avoided in certain cases. While it’s the best of the bunch, I
think it shares the same issues. While your not calling remote procedures
necessarily, it’s similar. In an ideal world, services shouldn’t necessarily
need to know how to CRUD each other’s resources. GETs can be fine but patching
remote resources can get gnarly if you have many services. We of course have to
be pragmatic, but, when services share that much, they can become extremely hard
to change independently. There are three things that I’ve seen happen in that
case. 1.) Every time you change a service that other services know a lot about,
you end up breaking all the other systems. 2.) Development slows to a crawl
because when you touch that service, you need to touch all kinds of other
services to make sure they don’t break. 3.) Developers become so scared, that
they basically stop working on that important part of the system because “here
be dragons”.

## What SOA looks like at AlphaSights?

This is a big topic and I don’t claim to have all the answers, but I do have a
couple of opinions that I think could help us drive towards an answer. These
opinions are unique to AlphaSights and I doubt they are applicable to every
business.

Firstly, I think our SOA should solve business problems first and foremost. It
will most likely solve technology problems or at least present a clear path to
solutions as well though. Saying that SOA should be made to solve business
issues is a bit counter intuitive, but I think as I go into more details, you
should see what I mean.

### SOA starts with the team

#### Team organization

I feel that SOA starts with the organization of the teams. If you don’t set the
teams up properly, you’re likely to fail. Specifically teams working in a SOA
should be structured around products, not project and not speciality (stack
based: DBAs on a team, ruby devs on a team etc.). Conway’s law states:

> organizations which design systems ... are constrained to produce designs
> which are copies of the communication structures of these organizations
> - Melvin Conway

If teams are bound to produce copies of themselves, we might as well organize
teams around products. The team’s product is their service. Their consumers may
be internal or external, but the team owns the product. If it goes down, it’s
their problem. If it’s a great success, it’s their win.

Teams should be cross-functional. There should be business stakeholders,
designers, developers, and whomever is else may be to define that services part
of the workflow. Teams should also be small. I think Amazon has it right with
their two pizza rule. If the team can’t share two pizzas, the team is too big.
Communication ends up breaking down in one way or another.

Teams should also own their product for it’s entire life cycle. This includes
business stakeholders, not just the tech team. The business stakeholders should
be as familiar with the service as the rest of the team including metrics. It
may sound obvious, but the team should also have a common language when
referring to the system.

Teams should also be able to release their product independent of other teams.
Cross-team dependencies should be kept to a minimum to avoid blocking each
other. Part of accomplishing this is making sure services stay small, easy to
understand, and are self documenting when possible.

#### How teams create services

_Disclaimer: The following bit about workflows applies to many, but not all
services._

Business stakeholders bring the most important piece of the puzzle to service
creation. They bring an understanding of the business workflows. Collections of
services should parody steps in workflows. Another way to say it is that
services should be the result of decomposing the business domain. The seams
between business domain pieces should match the seams in the service
architecture. Again, the business stakeholder is extremely important. Without a
quality business stakeholder involved, the lines for the services are inevitably
drawn in the wrong place.

The services in these workflows should be autonomous, discrete and share as
little as possible. Ideally, within workflows, we can minimal concepts for
service that remains stable over time and allows for independent releases (like
an id).

Take for instance the following model of Netflix. There are bunch of different
components here. Most of them related to the “Arrested Development”, but not all
of them (for instance the single sign on and profile services). The services
related to the show are numerous, but the only thing they would really need to
share is the ID of the show. Then the view can be composed with all the
disparate pieces from the various services. Another concrete example of this is
Facebook.

![Netflix as an
SOA](https://www.evernote.com/shard/s297/sh/28a3c4b6-34bd-4274-9529-fac0034f5de6/cdbb547b9fba41fa4872b2ef4c58249a/res/8dd3b899-aaae-4fe1-b6e3-901d7af3b4db/skitch.png?resizeSmall&width=832)

Services here might actually just be services that are the first part of a
workflow as well. Take for example the “Ratings Service”. Me clicking the button
may just be the first part of a workflow. That click could trigger an event that
bounces around getting picked up by various services. First, hitting persisting
my rating in some way. Then maybe getting picked up by a User activity service.
Then perhaps going to a real time data aggregation pipeline. Then possibly
through an AB testing capture service. All of that could happen in parallel as
well. The workflow in this case really doesn’t care which happens first.

I think the _Software Fortress_ model is actually a pretty good fit for SOA.
From the book in reference to a single Software Fortress (service in this
case):

> “..a conglomerate of software systems serving a common purpose and typically
> owned by a cohesive group of individuals. These software systems work together
> in a tight trust relationship to provide consistent and meaningful
> functionality to a hostile outside world.”
> - Roger Sessions

And in reference to an architecture consisting of multiple fortresses
(services):

>“…an enterprise architecture consisting of a series of self-contained, mutually
>suspicious, marginally cooperating software fortresses interacting through
>carefully crafted and meticulously managed treaty relationships.”
> - Roger Sessions

Some of that may sound a bit extreme, but the point is that services shouldn’t
know a whole lot about each other. Otherwise the dependencies between the
systems will make development crawl to a halt.

Lastly, these services should be built over time, with agility. This is another
obvious point, but these services should be iterated over and provide business
value at every step. The goal is not to implement a service oriented
architecture, the goal is to provide technology to automate processes and in
turn provide busines value.


### Technology

Now that we’ve talked about the people part of SOA, we should talk tech. I’ll
start of by saying that over time, we will end up with a hybrid architecture.
It’s the only way to keep agility. We’ll end up having some
[http://en.wikipedia.org/wiki/Event-driven_SOA](event driven architecture),
[http://en.wikipedia.org/wiki/Resource-oriented_architecture](RESTful resouces),
[microservices](http://martinfowler.com/articles/microservices.html), and
[http://martinfowler.com/bliki/CQRS.html](CQRS). For me any of these are more
than welcome. They all serve specific needs well. Being dogmatic and sticking
with just one would be inflexible and ultimately a mistake. We should choose the
right tool for the right job.

The two main ones I want to focus on in this context are resource oriented
architecture and event drive soa.

#### Resource Oriented Architecture

We should all be pretty familiar with the idea of RESTful resources so I won’t
go into it. What I will say is that when we do REST, we should be treat
hypermedia as the engine of application state (HATEOAS). This keep services and
their consumers decoupled so that the teams working on them can work
independently of a each other as much as possible. This is in contrast
predefined fixed contracts between client and server. The best example is
probably WSDLs. Not only do these kind of predefined contracts impede the
agility of the teams and create unnecessary dependencies, I also think that it
is DRY violation.

##### Consumer Driven Contracts through Hypermedia

I think contracts are a good idea because if done right, they can decrease
cross-team dependencies. There are a couple key factors that makes me consider a
contract (or framework for creating contracts) good. Contracts:
  * Should be simple
  * Build with multiple consumers in mind
  * Express a core set of data/functionality, but allow for extension
    (Open/Close Principle)
  * Evolutionary (maintain forward compatibility)
  * Self describing

Another benefit is that if metrics related to the contract endpoints are
tracked, they should be able to help provide a couple of key insights to the
service team. Namely:
  * Increased visibility of coupling between services
  * Insight into what parts of their service are actually being used.

Consumer driven contracts (CDC) are basically an XML SOA idea and I’m basically just
applying it to HATEOAS. My hope is that this is a type of contract that can
exist between service and consumer that doesn’t slow down development and
instead speeds it up. We don’t want to put the HATE in HATEOAS.

The first fundamental concept is simple but key to implementing this properly.
It’s on the Must Ignore pattern. In in a nutshell, the pattern states that if
you don’t know what something is in the markup, you must ignore it. Think custom
HTTP headers or custom markup tags in HTML. The browser doesn’t error, it just
ignores them. With CDC through hypermedia, clients should ignore keys they don’t
know about.

From there, build a hypermedia endpoint with a specific consumer in mind. That
first iteration defines your core API. As new consumers come on, instead of just
putting it all in the same namespace, separate it out in some way. These
extensions to the core contract can be put as either “links” in the response or
their own namespaced section.

Obiously, following HATEOAS is more complicated, but I think the upfront cost
greatly outweighs the long term benefits. Also, I think that this type of
architecture for the API can limit the need of versioning internal APIs.

As far as standards for the Hypermedia API go, I’d say go with
[http://jsonapi.org/](JSON API). It seems to have gained plenty of steam and is
the top dog in this space at the momment.


#### Event Driven Architecture (Event Driven SOA)

I’m going to be a little fast and loose with definitions here. Certain people
use event driven architecture very broadly (myself include). Others have a
really narrow view. My definition is a system with a set of services that do not
directly interact, but instead write message to some kind of bus. While this is
happening, services are also listening for events and responding accordingly if
they want to. It’s really broard, but it’s good enough for this context.

Event driven architecture is possibly one of the most decoupled ways to handle
services. There is not direct communication between services. This doesn’t
easily lend itself to the standard producer/consumer model (that’s more a REST
thing). That said, it is a perfect fit for workflows.

When multiple events are combined you are essentially making Workflows (or
Sagas) which can be really powerful. There are so many examples of workflows
that it’s hard to give one that doesn’t make it sounds too narrowly focused.
Suffice it to say that sets of interactions can be easily modeled in this way.
With these kinds of workflows, we can also take certain steps (services) and
reorder them to meet new business needs as they arise. Think different workflows
for accepting new projects, but being able to keep the same services in place
save one.

##### What does an event look like?

I think events should be small. You shouldn’t need to be passing around a whole
bunch of data to make things happen. Events should also be immutable. If you
need to add data to an event, don’t. Fire off a new event with only the data
that makes sense. Events should be in the past tense. They are not remote
procedure calls to other systems. They are announcements of things that have
happened. Events should be modeled after business concepts. Again, we’re
parodying the business in the architecture of the technology. Business events
drive business processes.

##### Difficulties with EDA?

Monitoring can be difficult. Need to make sure events can easily be tracked
across the system. We should always include a unique event id. Also, sending
around events is inherently more complicated that letting services talk directly
to each other. What we don’t want to have happen though is having services
basically break the Law of Demeter. Call depth for services should be kept to an
absolute minimum. Events help this.


### Wrapping Up

What should SOA do for AlphaSights:
  * Increase agility
    * Small agile teams working on discrete, autonomous services.
    * Independent teams can evolve components independently.

  * Align business needs and technology
    * Teams are organized around products, not projects.
    * Business stakeholders have a large role to play and are considered members
      of the product team.

  * Give business and technology a ubiquitous language

  * Make it easier for everyone to keep the big picture of the system in their
    heads
    * By encapsulating services

  * Help business define workflows and automate them
    * Workflows should also be flexible and easy to create

  * Help us apply design best practices on a high level (these are pretty loose
    comparisons)
    * Single Responsibility
      * Services provide small discrete functionality
    * Open/Close
      * New service should be able to be made quick.
      * Workflows should be open for extension by swapping in/out components
    * Liskov Substition
      * Services should be easy to interchange if we want to swap it out for
        another services that speaks the same language. For instance, if we find
        value in swapping a Rails service out for an Elixir service, we should
        be able to with ease.

I didn’t cover web hooks, infrastructure, service orchestration and a bunch of
other stuff, but I wanted to get this out there and see what everyone thinks. If
this was a conference talk, I think I would have just gone on for about 2.5
hours :). I think this is the start of a good framework that can help us move forward and
start to deliver our SOA while simultaneously providing business value.

I think it would be really beneficial to talk about some of this stuff out loud
as well. Maybe developer university?
