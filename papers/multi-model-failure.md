# Multi-Model Systems Are Failing Because We're Treating Untrusted Handoffs Like Function Calls

We're building multi-model systems as if each model call is a trusted function.

It's not. It's a handoff.

The receiving model can't check what it got.  
The originating model has no control over how its output will be interpreted.

So every step introduces ambiguity.  
And it compounds.

This looks like composition. It's actually accumulation — error accumulating across steps.

Multi-model failures aren't model failures. They're infrastructure failures we're misclassifying. We're building the wrong thing because we're misclassifying the problem.

---

## The Failure Sequence

Model A produces something valid — by its own definition.

That gets passed forward without being checked.

Model B picks it up and interprets it under a slightly different frame.

- Maybe it assumes a different schema  
- Maybe it strips context that was load-bearing  
- Maybe it treats a constraint as a suggestion

Nothing breaks. Nothing throws.

The break happens in the handoff — and you don't see it, because both models did their job.

- Model A produced valid output  
- Model B processed it as valid input

You only see it later.

Wrong action taken.  
Wrong data written.  
Wrong decision made.

At that point, you can't localize the failure. There's no error signal. Just a bad outcome and no clear path back.

> This looks like orchestration. It isn't.  
> Orchestration assumes the components are reliable. These aren't.

> This looks like model failure. It isn't.  
> The models worked. The infrastructure didn't.

> This looks like composition. It isn't.  
> Composition requires contracts. This is just data passing without guarantees.

*The pattern is the same as a hallucination inside a single conversation thread.*

One bad assumption gets introduced early. Everything after that looks consistent, but it's wrong. The model fails confidently, so you don't catch it until the reasoning chain has already compounded the error.

> Except now we've turned that into a system behavior.  
> We've taken the failure mode of a single model and made it structural across multiple models.

---

## What's Actually Missing

**This is not an orchestration problem. It's a missing layer problem.**

We don't have anything that defines:
- what a valid output is
- what has to be preserved across steps
- how interpretation is constrained
- where failure is detected

So we treat models like functions, but we don't provide the infrastructure that makes function composition reliable.

Right now the system is:

```
produce → pass → reinterpret → pass → reinterpret
```

There's no point where truth gets re-established. Every step assumes the last step was correct. And that assumption is never checked.

In software engineering, function composition works because you have typed interfaces, error handling, and observable side effects. You know what a function expects. You know what it returns. You know when it fails.

In LLM composition, none of that exists by default.

You don't have a contract for what Model A is supposed to produce. You don't have a schema for what Model B is allowed to assume. You don't have a mechanism to detect when the handoff introduced a semantic break.

So the system degrades silently. Each step looks fine in isolation. The failure is in how they connect.

**Multi-model systems are function chains without contracts.** That's the diagnosis.

---

## What Fixes It (And What Doesn't)

This does not get fixed by better prompts.

Prompts change what a model produces. They don't change what happens after the output leaves the model. They don't validate the handoff. They don't enforce a contract. They don't detect when the receiving model reinterpreted something in a way that breaks the original intent.

What actually changes the system is inserting control at the handoff.

That means putting control at the handoff:

- You check what's being passed  
- You validate it against an expectation  
- You decide explicitly whether it moves forward  
- You track what actually survives each step, not what you assume survives  
- You define who's right when interpretations diverge — because right now, that's implicit

With that in place, the system changes:

- Failures stop showing up at the end. They show up at the moment something goes wrong  
- You can see what each model received and what it produced. Debugging becomes tractable  
- Behavior becomes repeatable. Same input, same intermediate states, same outcome — and you can see why

The system stops being emergent and starts being composable.

That's the difference between a system that works in demos and a system that works in production.

---

## Where This Goes Wrong

If you add more models before fixing this, you just increase the number of untrusted handoffs.

That creates progress-shaped objects. More activity. Less reliability.

You end up building interfaces around an idea instead of making the core behavior real. The system looks more sophisticated — more models, more steps, more orchestration logic — but it's less reliable, because every new boundary is another place where truth can degrade silently.

The actual decision is:

Do we keep building orchestration on top of untrusted handoffs?

Or do we treat every handoff as a place where truth has to be re-established before anything moves forward?

That's the line between something that demos well and something that works in production.

---

## What This Costs

Boundary validation adds latency. Every handoff that gets checked is a handoff that takes longer.

Schema enforcement requires contract definition upfront. You have to decide what "valid" means before you can enforce it. That's design work. That's effort.

Open-ended tasks resist schema enforcement. If the output is supposed to be creative or exploratory, defining a rigid contract might constrain the system in ways you don't want.

Semantic validation is harder than structural validation. You can check whether the output matches a schema. It's harder to check whether it means what it's supposed to mean.

These are real costs. That's what reliability costs.

The alternative is accumulating invisible errors. The alternative is a system that looks like it's working until it produces a bad outcome, and you can't figure out why.

---

## What This Reveals

There's a real gap between what models can do and what systems can reliably produce.

Multi-model coordination is where it shows up first — because it breaks even in the simplest case. Just two models, one handoff, and it still breaks.

None of this is new. Contracts, validation, observability, authority — this is basic distributed systems work.

But it's not default in AI tooling yet.

Teams that solve this first will build systems that work. It stays the default for teams that don't.

The question isn't whether multi-model systems are useful. They are. The question is whether we're going to build them like production systems or like demos.

Right now, we're building demos — because we're still treating untrusted handoffs like function calls.
