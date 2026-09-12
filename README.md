# Timelock

A compiler for multi-step Raiku execution workflows. You declare a workflow as a sequence of steps with dependencies; Timelock classifies each step against Raiku's real, documented primitives, AOT reservation or JIT auction, lets you mark adjacent steps atomic where Raiku's documented same-block JIT bundling actually applies, and then computes the one number nobody has published: how many slots of unguaranteed exposure survive in the plan even when every individual leg is scheduled correctly.

## Why this exists

Raiku's own "Engineering Contract" material names the requirement directly: multi-leg institutional trades must execute whole, because partial execution leaves an exposure that was never part of the mandate. What's actually published, though, is narrower than that requirement: true atomicity is documented in exactly one place, JIT can treat a same-block group as a single unit. Anything spanning separate slots, sell now, buy later if a price condition holds, hedge after that, has no documented cross-slot guarantee. The only real path to full workflow atomicity is standing up a custom Extension, Raiku's off-chain execution environment that settles back to Solana via a chunked cryptographic proof. That's a legitimate answer, but it's a much heavier build than declaring a workflow.

Timelock doesn't invent a guarantee that doesn't exist. It builds the best schedule achievable with the primitives that are real, and then proves, with a number, exactly how far short of full atomicity that plan falls.

## What's real versus modeled

**Documented and used as-is:** AOT reservation (a future slot or window, English auction, predictable pricing), JIT auction (sealed-bid, immediate, minimum bid is the live priority fee plus 5 percent), same-block atomic grouping for JIT, pre-confirmation (a cryptographic guarantee issued ahead of the slot stating a transaction will execute in slot N), and Solana's real 400ms slot time.

**Modeled and disclosed:** the one-slot floor per non-atomic dependency edge is structural, two separate transactions cannot land in the same instant unless explicitly bundled, so it holds regardless of network conditions. The contention slider is a disclosed illustrative assumption for how many extra slots a JIT leg might need under load, not a measured figure.

Sources: [Raiku, the Engineering Contract, what the rewritten contract makes possible](https://raiku.com/blog/the-engineering-contract-what-the-rewritten-contract-makes-possible), [Raiku, the deterministic execution layer for perp DEXs](https://raiku.com/blog/raiku-the-deterministic-execution-layer-for-perp-dexs), [raikuhandbook.org, JIT vs AOT](https://raikuhandbook.org/jit-vs-aot.html).

## Stack

Plain HTML, CSS, and JavaScript, no build step, no backend, no dependency on raiku-sdk or raiku-agave, both still gated at the time of writing. Everything runs client-side in the browser.

## Running it locally

Open `index.html` directly, or serve the folder with any static file server.

## Deploying

This repository is set up for a static deploy on Netlify: publish directory is the repository root, no build command needed.

## Status

Independent build, not affiliated with Raiku. Mechanics are documented by Raiku; the implementation, the atomicity gap calculation, and this repository are not.
